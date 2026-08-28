# Mace.VIP — custom font pack

A Minecraft resource pack providing custom bitmap glyphs (rank tags, logo, enchantment book icons) under the font id `mace_vip:default`.

## Usage

Each glyph is a single character. Copy the character from the **Char** column into your tab / chat / scoreboard text and render it with the font `mace_vip:default`.

MiniMessage example:

```
<font:mace_vip:default>X</font>
```

| Glyph | Codepoint | Escape | Char |
|-------|-----------|--------|------|
| Mace logo (tab/header) | U+E000 | `` |  |
| OWNER tag | U+E001 | `` |  |
| ADMIN tag | U+E002 | `` |  |
| CREATOR tag | U+E003 | `` |  |
| HOST tag | U+E004 | `` |  |
| CORE+ tag | U+E005 | `` |  |
| CORE tag | U+E006 | `` |  |
| MEDIA tag | U+E007 | `` |  |
| MOD tag | U+E008 | `` |  |
| Sharpness book icon | U+E009 | `` |  |
| Breach book icon | U+E00A | `` |  |
| Wind Burst book icon | U+E00B | `` |  |
| Density book icon | U+E00C | `` |  |
| Common prefix | U+E00D | `` |  |
| Uncommon prefix | U+E00E | `` |  |
| Rare prefix | U+E00F | `` |  |
| Epic prefix | U+E010 | `` |  |
| Legendary prefix | U+E011 | `` |  |
| Mythic prefix | U+E012 | `` |  |
| Golden Wind Charge icon | U+E013 | `` |  |

## Sizing knobs

Edit `assets/mace_vip/font/default.json`:

- **height** — on-screen pixel height of the glyph
- **ascent** — how high it sits on the line (higher value = higher up)

Rank tags are height 8 / ascent 7 (line up with chat text). Logo and book icons are height 16 / ascent 13 (bump height for a bigger logo).

## Custom items

Item models live under `assets/mace_vip/models/item/`, with 16x16 textures in `assets/mace_vip/textures/item/` and item definitions in `assets/mace_vip/items/`. Set an item's `minecraft:item_model` component to the id:

- `mace_vip:sharpness_book`
- `mace_vip:breach_book`
- `mace_vip:wind_burst_book`
- `mace_vip:density_book`
- `mace_vip:golden_wind_charge`

## Hats

24 cosmetic hat models live under `assets/mace_vip/models/item/hats/`, with textures in `assets/mace_vip/textures/item/hats/` and item definitions in `assets/mace_vip/items/` (one per hat, mirroring the book pattern).

Give an item a hat by setting its `minecraft:item_model` component to the hat id, e.g. `mace_vip:cow_boy_hat`. Each model keeps its Blockbench `head` display transform, so it sits correctly when worn / shown on a head.

Available hats:

- `mace_vip:bandana_1`
- `mace_vip:bandana_2`
- `mace_vip:bandana_3`
- `mace_vip:cap`
- `mace_vip:capitan_hat`
- `mace_vip:chef_hat`
- `mace_vip:cosmo_hat`
- `mace_vip:cow_boy_hat`
- `mace_vip:cow_boy_hat_broke`
- `mace_vip:death_hat`
- `mace_vip:demon_hat`
- `mace_vip:farm_hat`
- `mace_vip:fire_hat`
- `mace_vip:football_helmet`
- `mace_vip:gas_mask`
- `mace_vip:gray_hat`
- `mace_vip:king_hat`
- `mace_vip:knight_hat`
- `mace_vip:maniac_mask`
- `mace_vip:miner_hat`
- `mace_vip:pink_hat`
- `mace_vip:pirate_hat_1`
- `mace_vip:sherif_hat`
- `mace_vip:winter_hat`

> Note: these models were authored in Blockbench with free rotations. Vanilla item models only allow a single rotation axis at fixed angles (0, ±22.5, ±45°), so a few hats (bandana, capitan_hat, cow_boy_hat, cow_boy_hat_broke, demon_hat, football_helmet, gas_mask, pink_hat, pirate_hat_1, sherif_hat) had off-axis rotations snapped to the nearest legal angle. They load and render correctly but may differ very slightly from the original Blockbench preview.

## Worldborder shader / version support

The pack itself loads on 1.21.11 and everything newer (`min_format` 75, `max_format` 1000).
The lodestone worldborder shader is version-specific, because Minecraft keeps reshaping the
item render path:

| Version | Pack format | Border shader |
|---------|-------------|---------------|
| 1.21.11 | 75 | base pack, `core/rendertype_item_entity_translucent_cull` |
| 26.1 – 26.1.2 | 84–87 | `overlay_26_1/`, `core/item` |
| 26.2+ | 88+ | `overlay_26/`, `core/item` |

26.x collapsed item rendering into `core/item`, so the base override targets a pipeline that
no longer exists and is simply ignored there. 26.1 and 26.2 need separate overlays because
26.2 split the lightmap and overlay colour into their own varyings (`lightMapColor`,
`overlayColor`, `Sampler1`), where 26.1 folds the lightmap straight into `vertexColor`.

Neither version's item pipeline declares the `Globals` uniform block, but `GlProgram` treats
`Projection`, `Lighting`, `Fog` and `Globals` as built-ins and binds any of them a program
declares, so `GameTime` — which both border variants animate on — is reachable from
`core/item` on both.

A core shader that fails to compile makes the client throw away **every** selected resource
pack, not just the effect, so validate before pushing a shader change:

```bash
python3 tools/validate-shaders.py 26.2 overlay_26
python3 tools/validate-shaders.py 26.1 overlay_26_1
```

## Building

Pushing to the repo builds `mace-vip-pack.zip` via Forgejo Actions (see `.forgejo/workflows/zip.yml`), attached to the run as an artifact. To zip locally:

```
zip -r mace-vip-pack.zip pack.mcmeta pack.png assets overlay_26 overlay_26_1
```
