# Lost Cities Odds Calculator

**[Open the calculator](https://fresh-glitch.github.io/lost-cities-odds/)**

What one chest in RLCraft 2.10's Lost Cities dimension is actually holding, for every item it can
reach, computed rather than sampled. Set your Luck, add an item of your own, and see where it lands
among them.

One page, no build step, no dependencies. Everything is in `index.html`.

## What it does

- **Every item, per context.** A Lost Cities chest draws one of seven loot tables, chosen by the
  floor it sits on and the building part it belongs to, so the same item has different odds in a
  high rise, on the ground, in a basement and in a rail dungeon. All five are shown side by side.
- **A Luck slider**, with its own configurable range, that recomputes the whole page from the loot
  tables as you move it.
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

  The last two take a `quality`, which is the field Luck multiplies, so your item responds to the
  slider the way a real entry would.

Items you add stay in your own browser and are sent nowhere.

## What Luck actually does

Read out of the mapped 1.12.2 sources rather than assumed. Vanilla spends Luck in exactly two
places:

```java
LootPool.generateLoot:   i = rolls.generateInt(rand)
                             + MathHelper.floor(bonusRolls.generateFloat(rand) * luck)
LootEntry.getEffectiveWeight(luck) = max(floor(weight + quality * luck), 0)
```

and `createLootRoll` drops any entry whose effective weight is not above zero. So Luck changes how
many times a pool rolls, and how the weights inside it compare. Nothing else.

Three consequences the page makes visible:

- **Luck does not lift everything.** An entry with no `quality` keeps the weight it had while the
  ones beside it grow, so its share falls. A master spell book is genuinely rarer at high Luck than
  at none.
- **Negative Luck is a cliff, not a slope.** Once `weight + quality * luck` reaches zero the entry
  leaves its pool altogether, so the race rings vanish at about -2 rather than fading out.
- **Bonus rolls cut both ways.** Where a pool has them, negative Luck subtracts rolls, which is why
  some columns empty out well before the weights would explain it.

Luck is treated as a whole number, which is what potions and attributes give in practice.

## Where the numbers come from

Every loot table the game would load, read in the game's own precedence order: the world save
first, then mod jars, then the vanilla client jar. The 33 tables the Lost Cities chests can reach
are what ships with the page.

The maths is exact, not a simulation. Rolls within a pool are independent draws, so the chance a
pool yields nothing is the mean of `(1 - q)^r` over its roll count, and pools multiply. An entry
that points at another table recurses. The bonus roll count is itself random, because
`generateFloat` is uniform over `[min, max)`, so its distribution is integrated rather than sampled.

The page's arithmetic is checked against an independent implementation in Python that uses exact
fractions and reads the original tables rather than the stripped model: 8820 values across every
item, every context and nine Luck levels, agreeing to within 5e-16.

The seven Lost Cities tables are the ones LootTweaker writes into the world save at
`<world>/data/loot_tables/loottweaker/`, which is where RLCraft's own scripts put them, so they are
read exactly as the game wrote them.

## What it does not cover

- **Only what is in a file.** A mod that injects into a vanilla loot table from Java at runtime
  leaves nothing on disk to read, so those additions are not counted. That affects the shared
  vanilla sub-tables, not the seven Lost Cities tables.
- **RLCraft Luckified is not modelled, because it does not apply here.** Its only loot-related
  mixin is on `EnchantWithLevelsMixin`, which uses Luck to add enchanting levels and recolour the
  result. It does not touch entry weights or roll counts, so it changes how good an enchanted item
  is, never which item you get.
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
