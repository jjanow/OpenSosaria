# OpenSosaria

An open-source, cross-platform engine for **Akalabeth: World of Doom** and **Ultima I–V**, written in C# on .NET 10
and MonoGame. It is the sister project of [OpenPhantasie](https://github.com/jjanow/OpenPhantasie) and
[OpenGoldBox](https://github.com/jjanow/OpenGoldBox) and is built the same way.

## Goals

- **Six games, one engine.** Akalabeth, Ultima I: The First Age of Darkness, Ultima II: The Revenge of the
  Enchantress, Ultima III: Exodus, Ultima IV: Quest of the Avatar and Ultima V: Warriors of Destiny, played from one
  launcher with one set of controls.
- **Bring your own files.** OpenSosaria ships no original game data. A built-in importer reads your own copy of each
  game (for example the DOS versions sold today, or disk images of the original releases) and builds the game data on
  your machine.
- **Faithful first.** Each game plays 1:1 with a chosen original version. The original program is studied routine by
  routine and its behaviour ported whole, checked side by side against the original running in an emulator. Anything
  not yet confirmed is marked `[?]` in the specs and the code; nothing is guessed. Where the program and the manual
  disagree, the difference is written up.
- **Quality of life as options.** Conveniences that later games and ports added (mouse support, larger maps, faster
  animation, better menus, auto-mapping and the like) are settings you switch on. Off means the original.
- **Keyboard, mouse and gamepad**: each one plays every game on its own.
- **Pick your art.** Each game can use the art and sound of any port it shipped on that you own (Apple II, DOS, C64,
  Amiga, Atari ST and others), with a sensible default per game.
- **Your character across the series**, following the transfers the originals allowed, and options to carry later
  games' features into earlier ones.
- **Cross-platform**: Linux, Windows and macOS.

## Status

**Planning.** Nothing is built yet. The step-by-step plan is in [PLAN.md](PLAN.md): sourcing and toolchain first,
then the shared engine, then the games one by one.

## Getting started

There is nothing to run yet. When there is, it will look like the sister projects:

```
./play.sh --game ~/games/ULTIMA4        # first run imports your copy, then plays
./play.sh --help
dotnet test
```

You will need the .NET 10 SDK and your own copy of the games.

## Layout (planned)

| Path | What it is |
|---|---|
| `src/OpenSosaria.Core` | Rules, game state, world, conversation, combat, saves. No graphics; unit-tested. |
| `src/OpenSosaria.Data` | The open game-data format, schemas and loaders. |
| `src/OpenSosaria.Importer` | Converts your original games (and their saves) into game data. |
| `src/OpenSosaria.Client` | The MonoGame front end. |
| `src/OpenSosaria.Tools` | Developer tools: viewers, script runners, comparisons against the originals. |
| `docs/re/` | Our own specs of the original file formats and program behaviour, with what is still unconfirmed. |
| `docs/differences/` | Every manual-versus-program difference per game, and what OpenSosaria does about it. |
| `tools/scripts/` | Python tools for the original formats and for testing against emulators. |

## License

MIT (see [LICENSE](LICENSE)). This covers OpenSosaria's own code and documentation only. The original games, their
content and their trademarks belong to their respective owners; OpenSosaria is not affiliated with or endorsed by them.
