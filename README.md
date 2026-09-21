# Lost Cities Odds Calculator

**[Open the calculator](https://fresh-glitch.github.io/lost-cities-odds/)**

What one chest in RLCraft 2.10's Lost Cities dimension is actually holding, for every item it can
reach, worked out exactly rather than sampled. Add an item of your own and see where it would land
among them.

One page, no build step, no dependencies. Everything is in `index.html`.

## What it does

- **Every item, per context.** A Lost Cities chest draws one of seven loot tables, chosen by the
  floor it sits on and the building part it belongs to, so the same item has different odds in a
  high rise, on the ground, in a basement and in a rail dungeon. All five are shown side by side.
- **A rarity ladder** on a shared logarithmic axis, so "rarer than a diamond, commoner than a
  nether star" is something you can see rather than work out.
- **Add your own item**, three ways, and it appears on the ladder and in the table beside
  everything already there:
  - *I know the odds*: a flat one chest in N.
  - *A separate pool of my own*: an item weight against an empty weight, rolled once. This is how a
    mod adds something without disturbing the loot already in the chest, and the chance is exactly
    the item's share of the weight.
  - *An extra entry in the pack's pool*: one more entry in the table's own pool, which rolls one to
    three times over its existing weights. Those weights differ per table, so the answer changes
    with the context, and every point of weight takes a share away from what is already there.

Items you add stay in your own browser and are sent nowhere.

## Where the numbers come from

Every loot table the game would load, read in the game's own precedence order: the world save
first, then mod jars, then the vanilla client jar. 593 tables in total.

The maths is exact, not a simulation. Rolls within a pool are independent draws, so the chance a
pool yields nothing is the average of `(1 - q)^r` over its roll count, and pools multiply. An entry
that points at another table recurses.

The seven Lost Cities tables are the ones LootTweaker writes into the world save at
`<world>/data/loot_tables/loottweaker/`, which is where RLCraft's own scripts put them, so they are
read exactly as the game wrote them.

## What it does not cover

- **Luck is zero.** Bonus rolls and entry quality both scale with Luck, so a lucky player does a
  little better on the weighted tables.
- **Only what is in a file.** A mod that injects into a vanilla loot table from Java at runtime
  leaves nothing on disk to read, so those additions are not counted. That affects the shared
  vanilla sub-tables, not the seven Lost Cities tables.
- **One instance.** The figures come from one RLCraft 2.10 install. A pack with different scripts,
  or a different version, will differ.

## Three dead entries, found on the way

`iceandfire:ice_dragon_cave`, `iceandfire:fire_dragon_cave` and `loottweaker:dragonsteel_gear` are
referenced by the basement, subway and rare tables but exist in no jar and in no script. Ice and
Fire ships `ice_dragon_female_cave` and `ice_dragon_male_cave`, not the names used. Those entries
are drawn normally and yield nothing, which is why basement and subway chests come up empty more
often than their weights suggest.

## Credits

Item art belongs to the mods it came from and is included only so a row can be recognised at a
glance. Not affiliated with RLCraft, The Lost Cities, LootTweaker, or any of the mods named.
