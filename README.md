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

The plugin checks your character's models and your minion (and, with "Other players" on, everyone else in view) for materials that reference `chara/common/texture/minamotohikari/fur_*`. When it finds one it reads the `.mdl` Penumbra loaded and generates the fur on a background thread, which takes about a second per item. Density is 152,525 strands/m² with lengths of 2.5 to 5 cm. Nothing from the mods ships with the plugin.

Every frame a compute shader skins the strands to the live skeleton, including the race deformer from `human.pbd`. The GPU draws them as camera-facing ribbons just before the UI, using the game's depth buffer so they don't show through walls or your body. Strands outside the view, behind walls or behind other fur are skipped before drawing (the plugin remembers which strands actually showed last frame), and items far from the camera draw fewer strands. Each frame of fur is blended with the last ones, following it as it moves, so the thin tips don't twinkle.

Each strand takes its colour from the item's colour table and textures where it grows, so dye changes show right away. Brightness comes from the pixels the game rendered for that item, so fur gets darker in shadow.

## Limits

- Fur costs frame rate. On an RTX 3070 at 1440p, a full fur outfit (1.49M strands over 7 items) seen from 3 m runs at about 85 fps, against about 155 fps with the fur off. Every extra character in fur costs about as much again. To lessen the impact, lower Density or Strands per root in `/fur` (GPose has its own section with both), but it might look a little less like fur.
- Big fur props (blankets, padded walls) grow millions of strands and cost a lot of frame rate and video memory.
- Shape keys are ignored.
- Other players and GPose actors get fur only with "Other players" turned on in `/fur`, and only where their Hikari items show on your screen, which for other players takes a sync plugin such as Lightless, Mare or Snowcloak. Each extra fur coat costs about 230 MB of GPU memory.
- Only V6 `.mdl` files work.

I've only tested this on one female character, so male models and other races might have problems.
