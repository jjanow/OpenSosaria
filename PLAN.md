# PLAN — OpenSosaria: Akalabeth and Ultima I–V in one engine

> **Hot file: read every session.** It holds the current state: decisions, open items, next up, and the step-by-step
> phases. History goes to cold files once they exist; read them only when the task needs them:
>
> | Cold file | Read when… |
> |---|---|
> | `docs/plan/SESSION-LOG.md` | you need what an earlier session did |
> | `docs/plan/BOARD-ARCHIVE.md` | you need the history of a closed item |
> | `docs/plan/RE-REFERENCE.md` | doing RE or emulator work: tags, address conventions, tooling, how to run each emulator on Xvfb |
> | `docs/plan/platform-comparison.md` | choosing a game's base version or art pack (phase 0 results) |
> | `docs/differences/<game>.md` | a manual-versus-program question for that game |
> | `docs/re/<game>/*.md` | the spec(s) the chosen item names |
> | `docs/re/routine-ledger-<game>.md` | which original routines are ported whole, partly, or not yet |

---

## 0. Session protocol

**Start:** read this file and `AGENTS.md` (which lists the memories to load) → `git status` (reconcile with "Last
session") → `dotnet test` once code exists (count must match "Last session"; with parallel agents use
`--disable-build-servers -m:1`) → pick **one** item from "Next up" (or ask the user) and load only the specs it names.

**End:**
1. Update the "Where we are" lines and the items you touched.
2. Closing an item = moving it to `docs/plan/BOARD-ARCHIVE.md` in the same edit. Open lists never hold `[x]`.
3. Add a short entry at the top of `docs/plan/SESSION-LOG.md`; overwrite "Last session" below (one line, test count).
4. A new `[?]` goes on the board under its game so nothing lives only in a code comment.

**Keep it lean:** an open item says what is left now; no history prefixes or strikethrough trails. Each fact lives in
one place (decisions here or in `docs/differences/`, detail in the specs).

**Tags:** `[?]` unconfirmed (spec and code), `[U]` needs a user decision, `[~]` built but not checked against the
original, `[play]` blocks the Playable line, `[release]` blocks the Release line.

**Last session:** s1 (2026-11-25): repo created and bound to `jjanow/OpenSosaria` (private); README, PLAN, AGENTS
(rules carried over from OpenPhantasie and OpenGoldBox), project memories. No code, no tests.

---

## 1. Where we are

Nothing built. Next: phase 0 (sourcing, platform comparison, base-version decisions) and phase 1 (toolchain), which
can run in parallel.

## 2. Decided

**User, 2026-11-25 (s1):**
- Scope: **Akalabeth and Ultima I–V**, one modern engine, cross-platform (Linux, Windows, macOS).
- Same stack as the sister projects: **C# / .NET 10 + MonoGame**, Python for format and emulator tooling.
- Same rules as OpenPhantasie and OpenGoldBox (`AGENTS.md` and the project memories): faithful 1:1 first, ported
  routine by routine; QoL as on/off options, off = original; manual is the source of truth until 1:1, with every
  manual-versus-program difference logged and decided by the user; no invented formulas; data-driven, game-neutral
  Core with no rendering references; keyboard, mouse and pad each play everything; bring-your-own-files importer, no
  original data shipped; Xvfb, sound-off, nice/ionice and clean-up rules for emulators and the Client.
- Two finish lines per game, as in OpenGoldBox: **Playable** (internal: title to ending with no difference that
  changes play) and **Release** (everything, presentation included).

## 3. Open user calls

Recommendations are given; nothing below blocks phase 0 or 1.

1. `[U]` **Base version per game** (rules and numbers). Recommendation, following the sister projects' "DOS where it
   exists" rule, adjusted where DOS is not the real game:
   - Akalabeth: **Apple II 1980** (the original; Applesoft BASIC, so the program reads almost as a spec). The DOS
     version in the Ultima Collection is a later rewrite; reference only.
   - Ultima I: **the 1986 remake, DOS** (the version all later ports and today's store copies derive from). The 1981
     Apple II original plays differently enough to be its own optional rules profile later, not a variant.
   - Ultima II: **DOS** (Sierra 1983). Apple II as reference.
   - Ultima III: **DOS** (1985). Apple II as reference (every port derives from it).
   - Ultima IV: **DOS** (1987). Apple II as reference.
   - Ultima V: **DOS** (1988). Apple II and C64 as reference.
2. `[U]` **Default art and sound per game**, picked from side-by-side shots of every port (phase 0, step 0.4), as for
   Pool of Radiance. Likely candidates: Amiga/Atari ST for III and IV (16-bit ports with enhanced graphics), DOS EGA or
   Amiga for V, DOS EGA for the I remake; the Japanese computer ports (PC-98, X68000, FM Towns) are worth a look.
   Console versions (NES, Master System) are different games and stay out of scope unless you say otherwise.
3. `[U]` **Game order.** Recommendation: **chronological** (Akalabeth → I → II → III → IV → V). Akalabeth is small and
   shakes out the whole pipeline (import, spec, port, compare, release); each later game adds to the engine the
   previous one built (overworld and towns in I, time travel and space in II, the party and tactical combat in III,
   conversation and virtues in IV, schedules and day/night in V). The alternative is **IV first** (best known, best
   documented, free on GOG), then V, then back-fill.
4. `[U]` **Public/private split.** The sister projects keep a private workspace (`*-remaster`, copyrighted material
   and RE notes allowed) and a public `Open*` repo fed by an export script with a leak check. This repo is private for
   now. Before it goes public: either make this repo the private workspace and create a public mirror, or keep this
   one public-clean and add a private `sosaria-remaster`.

## 4. Games and platforms

Every port each game shipped on (sources: Wikipedia, MobyGames, the Ultima Codex, 2026-11-25). Phase 0 confirms each
row against an actual image; `[?]` rows are uncertain.

| Game | Original | Ports |
|---|---|---|
| **Akalabeth: World of Doom** | Apple II, 1979/1980 (California Pacific) | DOS rewrite (Ultima Collection 1998; TCRF lists a 1996 DOS version, two ports `[?]`); C64 `[?]` |
| **Ultima I** (1981) | Apple II, 1981 (California Pacific) | Atari 8-bit (1983, same code as the Apple II original) |
| **Ultima I** (1986 remake) | Apple II, DOS (1986/87), C64 (Origin) | Apple IIGS (1987, rare); PC-88, PC-98, Sharp X1 (1988, Pony Canyon); MSX2 (1989); FM Towns (Ultima Trilogy, a further remake) |
| **Ultima II** | Apple II, 1982 (Sierra) | Atari 8-bit (1983), C64 (1984), DOS, Atari ST, Macintosh, MSX, FM-7, PC-88, PC-98; FM Towns (Ultima Trilogy) |
| **Ultima III: Exodus** | Apple II, 1983 | Atari 8-bit, C64 (1983), DOS (1985), Amiga and Atari ST (1986, mouse support), Macintosh, PC-88, PC-98, Sharp X1, FM-7, MSX2 (same as NES); NES/Famicom (1989, remake); FM Towns (Ultima Trilogy) `[?]` |
| **Ultima IV** | Apple II, 1985 | C64, DOS, Atari 8-bit (1985–87), Amiga and Atari ST (1988, enhanced; ST transfers the Avatar to V), PC-98, MSX2, FM-7 (1987), PC-88, Sharp X1, FM Towns, X68000; NES and Master System (1989/90) |
| **Ultima V** | Apple II, C64, DOS (1988) | C128, Amiga, Atari ST (16-colour, mouse), PC-88, PC-98, FM Towns, X68000; NES (1993, heavily changed) |

Engine lineage to design for: Akalabeth's first-person wireframe dungeons carry into I–III; I–III share the tile
overworld and towns; III brings the four-member party and a tactical combat screen that IV and V keep; IV adds keyword
conversation, virtues and moongate travel; V adds schedules, day and night, and scripted conversation.

## 5. Step-by-step plan

### Phase 0 — Sourcing and the platform comparison

0.1 Manuals and reference cards for every game (scans plus a text copy), `docs/manuals/<game>/` (kept local until the
    split is decided). They are the source of truth.
0.2 Clean disk images of every port worth comparing, per platform and game under
    `reverse-engineering/<platform>/original-images/<game>/`, each logged in `SOURCES.txt` (URL, size, sha256,
    clean/played/cracked). Prefer verified dumps (TOSEC `[!]`, 4am's Apple II archive, WOZ preservation dumps). Also
    note the store versions (GOG/EA) and which build each one is.
0.3 Extract the working files of each base candidate to `original-files/` next to the images; record versions.
0.4 Side-by-side comparison per game: boot each port in its emulator on Xvfb, capture the same screens (title,
    overworld, town, dungeon, combat, status), write `docs/plan/platform-comparison.md` → user picks base version and
    default art (open calls 1 and 2).
0.5 Collect prior work as hints only (file-format notes, community tools and fan engines for IV and V): useful for
    finding things, never trusted without checking against the files and the program.

### Phase 1 — Toolchain

1.1 Emulators, all scriptable on Xvfb with sound to a null sink: **DOSBox-X** (DOS), **MAME** (Apple II/IIe/IIGS,
    and the Japanese computers where it runs them well), **VICE** (C64/C128), **Atari800** (Atari 8-bit), **FS-UAE**
    (Amiga), **Hatari** (Atari ST), plus a PC-98 / X68000 emulator once those ports are in play.
1.2 Wrapper scripts in `tools/scripts/`: `dosbox_xvfb.sh`, `mame_xvfb.sh`, `vice_xvfb.sh`, `fsuae_xvfb.sh`,
    `hatari_xvfb.sh`, `client_xvfb.sh` (start / key / type / shot / frames / audio / stop on a given display, scratch
    game copy only); `vpad.py` from OpenGoldBox; a watchdog that moves stray audio streams to the null sink.
1.3 Disk-image tools: Apple II (AppleCommander or equivalent, Applesoft BASIC detokenizer), C64 (`c1541`), Atari
    (`atr` tools), DOS (`mtools`), Amiga (`xdftool`).
1.4 RE setup: a Ghidra project per base binary through `retrore` (x86 real mode for DOS; 6502 for Apple II / C64 /
    Atari; 68000 for Amiga / ST), overlay and segment maps, naming conventions in `docs/plan/RE-REFERENCE.md`.
1.5 Golden captures: a way to dump emulator state (RAM, screen, RNG seed) at chosen points, so the engine can be
    compared screen by screen and frame by frame.

### Phase 2 — Shared engine skeleton

2.1 Solution `OpenSosaria.sln`: `OpenSosaria.Core`, `.Data`, `.Importer` (+ CLI), `.Client`, `.Tools`, test projects
    for each; CI building and testing on Linux, Windows and macOS.
2.2 Game-data format (`docs/data-format.md`): tile sets, maps (overworld, towns, dungeons, combat maps), creatures,
    items, spells, conversations, schedules, strings; versioned; an importer must rebuild each original block byte for
    byte from it, as in OpenGoldBox.
2.3 Rules profiles: one per game (and later per base version), selecting the game's rules over shared systems; the
    shared systems are built only when the first game needs them.
2.4 Core basics: deterministic RNG matching each original's generator, clock and turn system, party and character
    model (game-neutral, so transfers work later), inventory, save/load (engine format, plus import/export of the
    originals' saves).
2.5 Client basics: window and integer scaling, tile renderer, text console with each original's font, the action
    layer for keyboard / mouse / pad with rebinding and device-aware prompts, sound (Apple speaker, PC speaker, SID,
    Paula, YM / PSG) through one mixer, options screen with per-game options.
2.6 Launcher: the list of imported games, import from a folder, art pack per game.

### Phase 3 onward — One game at a time

Every game follows the same steps; the per-game phases below list only what that game adds.

- **G.1 Formats:** decode every data file of the base version; spec in `docs/re/<game>/formats.md`; importer reads them
  and rebuilds them byte for byte.
- **G.2 Routine ledger:** list the original program's routines (`docs/re/routine-ledger-<game>.md`), each marked
  ported whole / partly / not yet.
- **G.3 Specs and ports, routine by routine:** spec the behaviour (`docs/re/<game>/*.md`), port it whole, test it
  headless in Core against golden captures.
- **G.4 Manual check:** read the manual against the specs; every difference goes to `docs/differences/<game>.md` and
  to the user.
- **G.5 Client screens:** each screen against the emulator, pixel for pixel where the art pack is the base platform's;
  keyboard, mouse and pad on every screen.
- **G.6 Art and sound packs:** the default pack (open call 2) and the other ports' packs, read from the player's files.
- **G.7 Playable:** title to ending with no difference that changes play (tested by jumping to each part, never a full
  play-through).
- **G.8 Release:** presentation polish, release notes, tagged build.

**Phase 3 — Akalabeth.** Overworld view, towns (shop), the first-person wireframe dungeon and its monsters, Lord
British's quests, the original's random world from the player's lucky number. Shakes out the whole pipeline.

**Phase 4 — Ultima I.** Sosaria's four continents, towns and castles, signposts, the wireframe dungeons from
Akalabeth, vehicles (horse, cart, raft, frigate, aircar, shuttle), the first-person space combat, time machine and
ending. Optional later: the 1981 rules profile.

**Phase 5 — Ultima II.** Earth across five eras, time doors, towns and castles, dungeons and towers, the planets and
space travel, the player-adjustable stats at character creation, ending.

**Phase 6 — Ultima III.** Four-member party and character creation, the tactical combat screen, Moongates, ships,
dungeons with marks and fountains, the Time Lord, the Exodus ending; music on the ports that have it.

**Phase 7 — Ultima IV.** The eight virtues and karma, keyword conversation, the gypsy's character questions, party
recruitment, moongates, dungeons with altar rooms, the Codex ending; transfer of the Avatar to V where the originals
allowed it.

**Phase 8 — Ultima V.** Schedules and day/night, scripted conversation, the Underworld, the Shadowlords, karma, the
U4 Avatar import, the ending.

### Phase 9 — The series layer

Once two games are Playable: one launcher for all six; character transfers exactly as the originals allowed (first
check each manual and program for what they allowed; never invent one); cross-game features as options (for example
III–V conveniences in I and II), each game's own behaviour the default; art pack per game from any port the player
owns.

### Phase 10 — Quality of life

After a game's 1:1 port: options with a source in some original or port first (mouse support from the Amiga/ST ports,
faster text, larger views on the Japanese ports, and so on), then others. Each option is off by default and listed in
the options screen.

### Phase 11 — Release

Builds for Linux, Windows and macOS from CI; hand-written release notes on every release; the public repo (open call 4)
with its export script and leak check; the README kept current with what plays.

## 6. Next up

1. Phase 0.1–0.2: gather manuals and clean images for the base candidates of all six games.
2. Phase 1.1–1.2: emulator wrappers for DOSBox-X and MAME (Apple II) on Xvfb, sound to a null sink.
3. Phase 0.4: the platform comparison, then the open calls 1–3.
