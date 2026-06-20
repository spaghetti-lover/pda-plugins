# pda-plugins

A Claude Code plugin marketplace for personal development, by Phung Duc Anh. It bundles several
focused plugins — each a folder under this repo — that you can install individually.

## Install

Add this marketplace, then install the plugins you want:

```
/plugin marketplace add spaghetti-lover/pda-plugins
/plugin install pda-code@pda-plugins
/plugin install pda-learn@pda-plugins
/plugin install pda-teamwork@pda-plugins
/plugin install pda-social@pda-plugins
/plugin install pda-health@pda-plugins
```

## Plugins

| Plugin         | Folder                | Skills                                          |
| -------------- | --------------------- | ----------------------------------------------- |
| `pda-code`     | `pda-code-plugin/`    | `technical-writing`                             |
| `pda-learn`    | `pda-learn-plugin/`   | `fast-reading-coach`, `learning-misconceptions` |
| `pda-teamwork` | `pda-teamwork-plugin/`| `counter-argument`, `feedback`                  |
| `pda-social`   | `pda-social-plugin/`  | _(placeholder — no skills yet)_                 |
| `pda-health`   | `pda-health-plugin/`  | `hair-care`, `skin-care`                        |

### pda-code
- **technical-writing** — write clear technical docs following Google's developer documentation style guide.

### pda-learn
- **fast-reading-coach** — read papers and technical books faster with deep comprehension (PLAN / CHECK / DRILL modes).
- **learning-misconceptions** — evidence-based learning science to give better study advice and reframe flawed habits.

### pda-teamwork
- **counter-argument** — build calm, fact-based counter-arguments for workplace disputes.
- **feedback** — structure two-way feedback sessions based on the McKinsey bi-weekly feedback system.

### pda-social
Reserved for social-interaction skills. No skills yet.

### pda-health
- **hair-care** — a standard hair-care routine: washing/conditioning frequency, gentle drying with heat protection, nourishing oils, and maintenance habits.
- **skin-care** — foundational skincare (cleanse, moisturize, sunscreen) plus advanced active-ingredient treatments (BHA, AHA, retinol, niacinamide, vitamin C) for acne, dark spots, and aging.

## Structure

```
.claude-plugin/marketplace.json   # lists all plugins (source: local sub-folder)
.claude-plugin/plugin.json        # umbrella metadata
<plugin>-plugin/
  .claude-plugin/plugin.json      # the plugin's own manifest
  skills/<skill>/SKILL.md         # one folder per skill
```
