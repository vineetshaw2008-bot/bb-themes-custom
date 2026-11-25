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

## Install from GitHub

The theme is published at
`https://github.com/vineetshaw2008-bot/bb-themes-custom` (private repo — use an
account with access).

```bash
# Fresh install
git clone https://github.com/vineetshaw2008-bot/bb-themes-custom.git ~/.bb/theme/cloudroom
bb theme set cloudroom

# Already cloned: update and re-activate
cd ~/.bb/theme/cloudroom && git pull
bb theme set cloudroom
```

The matching behaviour plugin is installed separately:

```bash
bb plugin install git:github.com/vineetshaw2008-bot/bb-plugin-auto-compact@v0.1.1 --yes
bb plugin enable auto-compact
```

Installing the plugin does not auto-switch the theme, so run
`bb theme set cloudroom` once after setup.

---

## Installed plugin stack and workflow snapshot (2026-11-25)

Recorded from the live instance: bb `0.49.0`, app `http://127.0.0.1:38886`,
host `host_4detrgykgv`. This section exists so the setup can be rebuilt after a
WSL/environment reset. The two GitHub repos hold **code and notes only** —
threads, projects, settings, and credentials live in the bb server data dir and
must be backed up separately (`bb server export`).

**Snapshot:** 47 plugins installed — 39 `running`, 7 `disabled`, 1 enabled but
`needs-configuration`. All 5 projects and all 37 visible threads were checked.

### Project / thread coverage

| Project (id) | Threads | Provider split |
|--------------|---------|----------------|
| `themes` (`proj_cp6ve23hip`) | 12 | mostly `acp-opencode`, one active thread (`thr_e2rvwamyzj`) |
| `datausagetoday.com` (`proj_yfw2f8zm95`) | 4 | `pi` / `acp-opencode` |
| `Research` (`proj_87rzmcvqw4`) | 1 | `acp-kilo` |
| `cloudroom-bb-custom` (`proj_et4pr6tvn8`) | 0 | — |
| `pdftomdconvert.com` (`proj_6tg8cny3yy`) | 20 | `acp-opencode`, `acp-antigravity`, `pi` |

Providers actually used by threads: `acp-opencode` 25, `acp-kilo` 5, `pi` 4,
`acp-antigravity` 3. Environments: `project-checkout` 36, `git-worktree` 1
(the Astro fix thread `thr_f9xuxkzd85`).

### Plugin inventory (all 47)

Core runtime (built-in and running unless noted):

| Plugin | Role in the workflow |
|--------|----------------------|
| `bb--composer` | The prompt box itself: rich text, `@` mentions, slash commands, send button. Every thread depends on it. |
| `provider-acp` | Supplies `acp-opencode`, `acp-kilo`, `acp-antigravity` — used by 33 of 37 threads. |
| `provider-pi` | Supplies the 4 `pi` threads. |
| `provider-codex`, `provider-claude-code` | Installed and available; no threads in this snapshot used them. |
| `provider-retry` | Retries transient provider overload / limit errors. |
| `environment-project-checkout` | Runs threads in the project's own checkout (36 threads). |
| `environment-git-worktree` | Runs a thread in a fresh worktree (1 thread). |
| `environment-personal-workspace` | Workspace for threads without a project. |
| `navigation`, `thread-list`, `side-chat`, `drafts` | Sidebar navigation, thread list, side chats, saved drafts. |
| `file-tree`, `monaco-editor`, `pdf-preview` | Pinned file tree, file editing, PDF preview. |
| `custom-instructions` | Persistent instructions injected into agent tasks on this host. |
| `concurrency-limit`, `keep-awake`, `push-notifications` | Caps running threads, keeps hosts awake, notifies on attention. |
| `bb-account`, `connect` | getbb.app account/sign-in and remote access link. |
| `bb--provider-usage`, `bb--github-star` | Usage card in settings and the one-time star prompt. |

Working / tooling plugins:

| Plugin | Role in the workflow |
|--------|----------------------|
| `auto-compact` | **This plugin.** Per-pane content script; keeps the follow-up composer compact, hover-to-type, live dictation, `/`/`@` selection fix. Source was `path:/root/themes/bb-plugin-auto-compact`, now published at this repo. |
| `browser-automation` | Used from the CLI for app/theme verification and browser sessions; its skill was invoked in `thr_bxjzh6m8be` and `thr_whxaphrb6h`. |
| `genui` | Inline interactive UI; `check_ui` called twice in `thr_e2rvwamyzj`, `ui_examples` once in `thr_pewqx4y2ys`. |
| `ask-user-question` | Multiple-choice questions; `AskUserQuestion` used in `thr_9b32finecs` and `thr_mxfdd4cuyp`. |
| `theme-preview` | Previewing and validating the Cloudroom theme. |
| `bb--git`, `bb--github-pr-status` | Git status/diff above the composer and PR state for environment branches. |
| `bb--prompt-library`, `bb-guide` | Reusable prompts and bundled agent skills (`bb-plugin-authoring` used in `thr_ms5h6junsx`, `bb-cli`, `skill-creator`). |
| `automations`, `workflows`, `scheduled-send` | Scheduled agent work, durable workflows, send-later drafts. `automations` sweep service is running. |
| `inline-vis`, `agent-annotations` | HTML visualisations in replies; browser annotations as `@` mentions. |
| `secrets` | Secure credential requests into dotenv files. |
| `antigravity` | Catalog plugin, `v0.2.1`; currently `needs-configuration`, but 3 older threads were created with it. |

Disabled in this instance (deliberate, keep them disabled on restore):
`account-pool`, `bb--connect-next`, `bb--mcp`, `bb--storage-retention`,
`bb-ai`, `plugin-api-docs`, `plugin-api-tester`.

### What the logs actually show

- All 37 threads were inspected via `bb thread log --json`. The dominant recorded
  tool calls were `read`/`grep`/`glob`/`exec` (file work), plus the plugin tools
  listed above (`check_ui`, `ui_examples`, `AskUserQuestion`, `skill`).
- A slash-command skill mention (`grill-me`) appears in `thr_qts6firxd2`; that is
  a user skill, not a plugin.
- Plugin ids also appear in many logs inside `bb plugin list` output and agent
  reasoning; those mentions are **not** proof of active use and were not counted
  as usage.
- `auto-compact` ran as a content script in the thread composer, so it does not
  appear as a tool call. Its effect is the compact/focus behaviour in threads.

### Restore this setup in a new environment

```bash
# 1. Same bb line (packaged): plugin engine range is >=0.48; snapshot was 0.49.0
npx bb-app@latest --version

# 2. Built-ins ship with bb. Mirror the explicit choices:
bb plugin disable account-pool bb--connect-next bb--mcp bb--storage-retention bb-ai plugin-api-docs plugin-api-tester
bb plugin enable browser-automation theme-preview

# 3. Catalog plugins used here (with the exact tracked ranges):
bb plugin install antigravity --yes                                  # then configure it
bb plugin install git:github.com/fzrx-ego/bb-plugin-file-tree.git@semver:^0.19.3 --yes
bb plugin install git:github.com/ComicBit/bb-plugin-genui.git@semver:'>=0.3.0 <1.0.0' --yes

# 4. This plugin and the theme:
bb plugin install git:github.com/vineetshaw2008-bot/bb-plugin-auto-compact@v0.1.1 --yes
git clone https://github.com/vineetshaw2008-bot/bb-themes-custom.git ~/.bb/theme/cloudroom
bb theme set cloudroom

# 5. Verify:
bb plugin list        # 47 plugins; auto-compact running
bb provider list      # acp-opencode, acp-kilo, acp-antigravity, pi available
bb theme list
```

`bb plugin disable` accepts one id per invocation; repeat the command for each
id if your bb build does not accept a list. Provider CLIs (`opencode`, `kilo`,
`pi`) and host credentials are **not** in these repos — reinstall/sign in to
them before reopening old threads.

**Caveats:** counts exclude archived/hidden threads and were taken on
2026-11-25 from `bb plugin list`, `bb thread list`, `bb thread show`, and all 37
thread logs. Plugin versions drift (`file-tree` resolved `v0.19.14`, `genui`
`v0.3.2`, `antigravity` `v0.2.1`); pin those tags if exact reproduction matters.
