# bb-themes-custom

Cloudroom — a custom theme for bb (the IDE). It is a pure CSS skin: it changes
colours, buttons, switches, tabs, cards, the composer, and the code theme. It
adds no behaviour and does not modify packaged app files.

## Files

| File | Purpose |
|------|---------|
| `theme.css` | The full skin (tokens + component rules), ~19.7 KB |
| `pierre-dark.json` | Code theme, dark (`cloudroom-dark`) |
| `pierre-light.json` | Code theme, light (`cloudroom-light`) |
| `DESIGN.md` | Visual design spec and selector reference |

## Install

Copy this folder into bb's custom theme directory and activate it:

```bash
bb theme dir                       # usually ~/.bb/theme
git clone https://github.com/vineetshaw2008-bot/bb-themes-custom.git ~/.bb/theme/cloudroom
bb theme set cloudroom
```

If the folder already exists, pull instead of cloning:

```bash
cd ~/.bb/theme/cloudroom && git pull
bb theme set cloudroom             # re-activate after every CSS edit
```

## Verify

```bash
bb theme list                      # shows cloudroom as active
bb settings show --json | python3 -c "import sys,json;d=json.load(sys.stdin);print(len(d['appearance']['customCss']), d['appearance']['resolvedCodeTheme'])"
```

Expected: ~19700 bytes injected, code theme `bb:cloudroom:dark` /
`bb:cloudroom:light`.

## Notes

- Tested against the packaged bb app 0.49.0. The CSS targets the compiled
  classes of that build (for example primary buttons use
  `bg-foreground text-background`). Re-verify selectors after a bb upgrade.
- `bb theme set cloudroom` does not hot-reload from disk on file edit; run it
  again after changing `theme.css` or the code theme JSON files.
- This repo holds only the visual theme. The composer behaviour (auto compact,
  hover to type) lives in a separate plugin repo:
  `bb-plugin-auto-compact`.
