# OpenSosaria — Ground Rules

The same ground rules as OpenPhantasie (`../phantasie-remaster`) and OpenGoldBox (`../goldbox-remaster`), adapted to
Akalabeth and Ultima I–V. They are **guidelines, not hard limits**: apply them with common sense and change them when
they no longer fit.

## How to work this repo

- **Start every session by reading `PLAN.md`** (repo root): the live board (open items only). Its header lists the cold
  files; read those only when the task needs them. Before ending a session, follow its "Session protocol" end steps.
- Load the standing instructions and project memories at the start of every session, without waiting to be asked:
  - `/home/administrator/.agents/AGENTS.md` (shared global instructions).
  - `/home/administrator/.claude/CLAUDE.md` (additional global instructions), including referenced instruction files.
  - `/home/administrator/.claude/projects/-home-administrator-repos-OpenSosaria/memory/MEMORY.md` and every other
    Markdown file in that memory directory (project decisions, preferences and workflows).
  - Any applicable `CLAUDE.md` files in the repository and parent directories.
  Use the canonical memory directory above even when working in a worktree. Keep those files as the source of truth
  rather than copying their contents here.
- **No real name anywhere in this project** (user, 2026-11-25): commits are authored as `jjanow` with the GitHub
  noreply address (set in this repo's local git config; worktrees and agents must keep it), and documents, LICENSE,
  release notes and code use `jjanow` only. Check `git log --format='%an %ae %cn %ce'` before every push.
- No AI attribution anywhere: commit messages, trailers, PR bodies,
  release notes, docs, code comments, branch names. This covers sub-agents' commits; say so in every agent brief.
- Original game files (disk images, ROMs, extracted files) never go into git; `.gitignore` blocks the usual image
  extensions. Until the public/private split is decided (PLAN, "Open user calls"), nothing from the original games
  (files, art, text, disassembly listings) is committed here either.

## Source of truth and parity

- **Faithful 1:1 first, QoL after.** Each game plays 1:1 with its chosen base version (PLAN, "Games and platforms").
  Quality-of-life features are **on/off options, off = original**, and wait until the game's 1:1 port works; until
  then QoL ideas go in PLAN.md, not in code.
- **Routine by routine.** The original routine behind a screen or rule is ported whole and faithfully (every branch)
  as the parity baseline, not patched difference by difference. Bug fixes and QoL are separate, written decisions on
  top of the faithful port.
- **The game's original manual (and its reference cards) is the source of truth** for rules until that game's 1:1
  version is finished. When the program contradicts the manual, **tell the user in the reply as soon as it's found**,
  don't pick a side, record both in the spec, log it as `[U]` in PLAN.md and add an entry to that game's
  `docs/differences/<game>.md` (what the manual says, what the code does, the decision, what OpenSosaria implements).
- **Between versions** of the same game, the chosen base version wins on numbers and rules; other ports are references
  for what the base lacks. Visuals follow the chosen art pack, never the rules base.
- **No invented formulas.** Undecoded behaviour is marked `[?]` in the spec *and* in the code. `[U]` = needs a user
  decision. `[~]` = built but not yet checked against the original.
- Reverse engineering any port (Apple II, DOS, C64, Atari, Amiga, ST, Japanese computers, consoles) to settle an open
  question is allowed without asking. Check the originals and manuals before proposing any feature; if no original has
  it, say so plainly.
- Original images live in `reverse-engineering/<platform>/original-images/<game>/` with a `SOURCES.txt` per platform
  (URL, size, sha256, clean/played/cracked). Never edit those masters; work on copies.

## Architecture constraints

- **Behavioural spec before code.** Specs are readable rules/pseudocode in `docs/re/` (or `reverse-engineering/`),
  never a line-by-line transpile of 6502 or x86 into C#.
- **Data-driven, game-neutral.** Maps, tiles, monsters, items, spells, conversations and schedules are module data
  built by the importer from the player's own files, loaded by `OpenSosaria.Data`. Each game is data plus a rules
  profile (delta rules), not a fork. New code must not hardwire one game, one platform or English-only text.
- **Core has zero rendering dependencies.** `OpenSosaria.Core` runs and is tested headless; `OpenSosaria.Client`
  (MonoGame) is a thin shell over it.
- **Keyboard, mouse and gamepad** each play every screen on their own, from the first Client code: one rebindable
  action layer with device-aware prompts.
- **Client layout:** never drop lines, shrink or crop art to make something fit; resize, move, or add a scrollbar.
- Engine saves don't need migrations between our own versions; a breaking change just bumps the version. Importing and
  exporting the originals' saves is a feature and stays.

## Running the Client, emulators or other GUI tools

- **Virtual screen, never the user's desktop.** The Client, every emulator, `xdotool` input and screenshots run on a
  private Xvfb display. Pick a free number (`/tmp/.X11-unix/X<N>` absent); never kill an Xvfb you did not start. One
  display, game copy and output folder per run, so several agents can work at once. Use the `tools/scripts/*_xvfb.sh`
  wrappers once they exist (PLAN phase 1).
- **Setting `DISPLAY` is not enough:** the desktop is Wayland, so SDL programs open on the real screen whenever the
  Xvfb isn't up. Launch with `env -u WAYLAND_DISPLAY SDL_VIDEODRIVER=x11`, check `xdpyinfo` on the Xvfb first and abort
  if it fails, and wait for an old Xvfb to exit before reusing its number.
- **No sound on the speakers.** Audio goes to a PulseAudio null sink (`PULSE_SINK=<own sink>`) or is off. OpenAL
  programs (the Client) also need `ALSOFT_DRIVERS=pulse`; start metacity with `CANBERRA_DRIVER=null`. Check
  `pactl list sink-inputs` afterwards.
- **Controllers:** test pad input only with a virtual pad (`tools/scripts/vpad.py`, carried over from OpenGoldBox),
  never a real pad or the real mouse. Quit the virtual device when done (it is system-wide).
- **Keys:** SDL ignores `xdotool --window`; focus the window, then send real XTEST keys held about 0.12 s.
- **Killing:** `pkill -f`/`pgrep -f` match the calling shell too; kill by PID or `pkill -x NAME`, and wait on a PID
  (`while kill -0 PID`), never on `pgrep -f`. Emulators that ignore SIGTERM get SIGKILL.
- **Priority:** emulators, Xvfb, the Client, `dotnet build/test` and heavy scripts run under
  `nice -n 19 ionice -c 3`, no warp mode except briefly. DOSBox-X and Amiga emulators: up to 6 of each at once if audio
  and stability hold up; other emulators one at a time. Close each after its capture.
- **Testing a screen = jumping to it** (saves, start options, emulator save states), never a full play-through.
- **Clean up:** before finishing, stop everything this session or its agents started (Xvfb, emulators, metacity, the
  Client, null sinks, MSBuild/build servers, background tasks) and say so in the reply.
- **Sub-agents:** every brief for an agent that may launch any of this states these rules (virtual screen, sound off,
  nice/ionice, virtual pad only, clean up, checkpoint commits, no AI attribution).
- Use the real screen only when the user asks or the check can't be done on Xvfb (say why first).

## "wrap it"

When the user says **"wrap it"** (same meaning as in the sibling projects): run the tests; update PLAN.md (open items,
"Last session") and the session log; purge stale notes and `[?]`/`[~]` markers the work made outdated; commit (user's
identity, no AI attribution) and push to `origin`; refresh the public repo if one exists and anything it ships changed
(export script plus leak check; never original files, art, text or disassembly); prune merged, clean agent worktrees;
delete this session's scratch folders and stop its processes. Report the commit hashes. Without "wrap it", don't push
unasked.
