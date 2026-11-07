# Fur Renderer

Dalamud plugin that grows hair strands on gear and minions using Hikari's fur textures and draws them over the game's model. The fur follows your character's animations and uses the item's dye.

The mods aren't included, you'll need them loaded through Penumbra.

## Install

In `/xlsettings` > Experimental, add this under Custom Plugin Repositories and save:

```
https://raw.githubusercontent.com/Minescence/FfxivFurRenderer/main/repo.json
```

Then install Fur Renderer from `/xlplugins`. `/fur` opens the settings.

## How it works

The plugin checks your character's models and your minion for materials that reference `chara/common/texture/minamotohikari/fur_*`. When it finds one it reads the `.mdl` Penumbra loaded and generates the fur on a background thread, which takes about a second per item. Density is 152,525 strands/m² with lengths of 2.5 to 5 cm. Nothing from the mods ships with the plugin.

Every frame a compute shader skins the strands to the live skeleton, including the race deformer from `human.pbd`. The GPU draws them as camera-facing ribbons just before the UI, using the game's depth buffer so they don't show through walls or your body. Strands hidden that way or outside the view are skipped before drawing, and items far from the camera draw fewer strands.

Each strand takes its colour from the item's colour table and textures where it grows, so dye changes show right away. Brightness comes from the pixels the game rendered for that item, so fur gets darker in shadow.

## Limits

- Expect around 20 fps with 1.67M strands across 8 items and an RTX 3070 at 1440p. To lessen the impact, lower Density or Children per strand in `/fur` (GPose has its own sliders for both), but it might look a little less like fur.
- Items at 980k strands or more get skipped, except the Big Fluffy Coat. That means no TRPC blanket or Asylum wall/floor.
- Shape keys are ignored.
- Other human actors in GPose don't get fur.
- Only V6 `.mdl` files work.

I've only tested this on one female character, so male models and other races might have problems.
