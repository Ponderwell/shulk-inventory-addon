# Shulk Inventory Addon

A Fabric mod for Minecraft **1.21.1** (Fabric Loader ≥ 0.16.0) that modifies
the [Origins](https://github.com/apace100/origins-fabric) **Shulk** origin so
that its 9 extra inventory slots appear **directly inside the player's normal
inventory screen** instead of a separate GUI opened with the `G` keybind.

---

## What it does

| Vanilla Origins Shulk | With this addon |
|---|---|
| Press `G` → opens a separate 9-slot window | 9 slots appear in a **"Shell" panel** to the right of the normal inventory screen |
| Must switch screens to access extra items | Extra items accessible any time the inventory is open |
| Separate key-binding required | No extra keybind needed |

The 9 items stored in the extra panel are **the same items** Origins persists —
this addon only changes *where* they are displayed and accessed. Items survive
death as usual (Origins handles persistence).

---

## Requirements

| Dependency | Version |
|---|---|
| Minecraft | 1.21.1 |
| Fabric Loader | ≥ 0.16.0 |
| Fabric API | Any compatible with 1.21.1 |
| Origins (Fabric) | 1.13.x for 1.21.1 |

---

## How it works (technical summary)

1. **`PlayerScreenHandlerMixin`** — injects 9 custom `ShulkSlot` instances into
   the `PlayerScreenHandler` when it is constructed for a player that has the
   `origins:shulker_inventory` power.  The slots are appended after all 46
   vanilla slots (indices 46–54).

2. **`ShulkSlot`** — a custom `Slot` that reads and writes directly to the
   `SimpleInventory` backing the Origins `InventoryPowerType` via reflection,
   so all items remain in Origins' own NBT persistence and survive death as
   normal.

3. **`InventoryScreenMixin`** (client-only) — expands the inventory screen
   background by 58 px and draws a 3×3 slot grid labelled **"Shell"** to the
   right of the normal panel.

4. **`ServerPlayerEntityMixin`** — cancels the `openHandledScreen` call that
   Origins would normally make when the player presses the primary-active power
   key, since those slots are now always visible in the regular inventory.

5. **`ShulkInventoryHelper`** — all Origins internals (Apoli's
   `PowerHolderComponent`, `InventoryPowerType`, `SimpleInventory`) are
   accessed via **reflection** so the mod remains compatible with multiple
   Origins versions without a hard class-level dependency.

---

## Building

```bash
git clone <this repo>
cd shulk-inventory-addon
./gradlew build
# Output jar: build/libs/shulk-inventory-addon-1.0.0.jar
```

Place the compiled jar alongside Origins in your `mods/` folder.

---

## Compatibility notes

- Only affects players with the `origins:shulker_inventory` power (i.e. the
  Shulk origin).  Human players and all other origins are completely unaffected.
- The mod does **not** modify any Origins classes — it only hooks the vanilla
  `PlayerScreenHandler` and `InventoryScreen`.
- Should be compatible with inventory-management mods (Inventory Profiles Next,
  etc.) since extra slots are registered in the standard slot list.

---

## License

MIT
