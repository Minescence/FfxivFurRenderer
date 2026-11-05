# Fur Renderer

Hikari's fur textures make a mesh look furry. This Dalamud plugin grows actual strands on items that use them, up to about a million each, and draws those over the game's model while you play. They bend with your skeleton and take whatever dye you've applied; walk into shadow and they darken with it.

## Install

1. In game, open `/xlsettings` and go to the **Experimental** tab.
2. Under **Custom Plugin Repositories**, add this URL and save:

   ```
   https://raw.githubusercontent.com/Minescence/FfxivFurRenderer/main/repo.json
   ```

3. Open `/xlplugins`, search for **Fur Renderer** and install it.

You also need a mod that uses the fur textures, loaded through Penumbra. None ship with the plugin, so get them from their author. Once one is on your character the fur appears by itself; `/fur` opens the settings.

## How it works

Each frame the plugin checks your character's loaded models and your minion (that's how the fur boas work) for materials pointing at `chara/common/texture/minamotohikari/fur_*`. When it finds a new one, it reads the `.mdl` file Penumbra loaded and grows strands across the fur meshes on a background thread, usually in under a second: 152,525 strands per square metre, 2.5 to 5 cm long, clumped and leaned to match the TheSleeveExperiment Blender scene. All of that happens on your machine from your own mod files, which is why this repo contains no mod data.

Drawing happens right before the game renders its UI. A compute shader skins every strand to your live skeleton, including the race deformer from `human.pbd`, and the GPU expands each one into a camera-facing ribbon in a multisampled layer. That layer respects the scene's depth, so walls and your own body still hide the fur behind them.

Colour comes straight from the game's colour table, which means a dye change shows up on the fur as soon as the game applies it. Brightness comes from how the game lit the item underneath each strand; sun, lamps and cast shadows carry over without the plugin computing any of them.

## Limits

- It's heavy. One rough measurement: 1.67M strands across 8 items ran at about 20 fps on an RTX 3070 at 2560x1440. Lower **Density** or **Children per strand** in `/fur` if you need frames back.
- Items needing 980,000 strands or more get skipped, apart from the Big Fluffy Coat (980k, or 1.2M on the male model). That rules out the TRPC blanket and the Asylum wall and floor.
- Shape keys are ignored, each model gets a single colour, and the second dye channel doesn't affect the fur.
- In GPose, other human actors get no fur. Minions do.
- Models have to be V6 `.mdl` files with the usual vertex layout; anything else logs a failure and goes without fur.

So far it has only run in game on one female character. Male models and other races should work, but nobody has checked them yet.
