```
██╗  ██╗      ███████╗██╗     ██╗ ██████╗███████╗
██║  ██║      ██╔════╝██║     ██║██╔════╝██╔════╝
███████║█████╗███████╗██║     ██║██║     █████╗
██╔══██║╚════╝╚════██║██║     ██║██║     ██╔══╝
██║  ██║      ███████║███████╗██║╚██████╗███████╗
╚═╝  ╚═╝      ╚══════╝╚══════╝╚═╝ ╚═════╝╚══════╝
```

**Friday Night Funkin' in a single HTML file.** No install, no server, no build
step — double-click `index.html` and play. All seven weeks are here, plus a tool
that turns V-Slice or Psych mods into something it can play.

Engine build **b33**.

## Play

**[Download the latest release](../../releases)**, unzip it anywhere, and open
`index.html`. That's it — it runs straight off your disk, offline.

Cloning works the same way; there's nothing to build.

> Running it from a server (`python3 -m http.server`) isn't required, but it
> gives tighter audio sync, which matters if you're charting.

## What's in it

- **All seven weeks**, in story mode, played in order with a running score
- **Freeplay** with difficulties and song variations
- **Options** — downscroll, middlescroll, ghost tapping, audio offset, scroll
  speed, rebindable keys
- **A stage editor** — press **E** in a song to drag, resize and flip
  characters, frame the camera, and save the layout
- **A mod builder** — `mod-builder.html`, described below

## Add your own songs

Open **`mod-builder.html`** and drop in these nine files, for **one song at a
time**:

```
song-chart.json        the notes
song-metadata.json     tempo, characters, stage
Inst.ogg               the instrumental
Voices-bf.ogg          Boyfriend's vocals
Voices-opponent.ogg    the opponent's vocals
opponent.png           their spritesheet
opponent.xml           its frame data
opponent.json          their animations and offsets
bg.png                 the background
```

Swap `song` for the song's name and `opponent` for the character's — so
`ugh-chart.json`, `tankmanCaptain.png`, `tankman.json`, and so on. Boyfriend and
Girlfriend are built into the engine, so they need no files.

Psych charts work too: one `song.json` per difficulty (`song-hard.json`), and no
metadata file, since Psych keeps the tempo and characters inside the chart.

The builder checks everything before it builds and says what's wrong in plain
words, then hands you a finished mod folder: drop it in `mods/`, add the one line
it gives you to `engineassets/mods.js`, done.

> **Don't drop a whole mod folder or a `.fnfc`.** Both are supported, but picking
> the files yourself is far more reliable — a mod folder holds several songs and
> a lot of art that isn't yours, and it's easy to end up with the wrong one.

Full guide: **[MODDING.md](MODDING.md)**.

## Controls

| | |
|---|---|
| notes | `D F J K` or the arrow keys — rebindable |
| menus | arrows, Enter, Esc |
| pause | Enter · Enter picks · Esc resumes |
| freeplay | up/down song · left/right difficulty · Tab variation |
| stage editor | **E** in a song · **C** camera · **H** flip · **S** save |
| debug overlay | **`** or F3 |

## Layout

```
index.html                  the engine
mod-builder.html            the mod tool
engineassets/
├─ mods.js                  loose mods, one line each
├─ weeks.js                 the weeks and their song order
├─ weeks/week1/bopeebo/…    a mod folder per song
├─ images/  songs/  sounds/ arrows, ratings, characters, menu audio
└─ sprites.js               spritesheet frames, packed for offline use
mods/example-song/          a hand-written mod, to show the shape of one
```

Charts and spritesheet data are packed into `.js` files on purpose: a
double-clicked page isn't allowed to read `.json` or `.xml`, but it can read a
script. That's the whole reason this runs without a server.

## Releases

```bash
git tag v1.0.0 && git push --tags
```

`.github/workflows/release.yml` zips everything and attaches it to the release.

## Credits and licence

The engine, the mod builder and the docs are by **Elias / CyberMeow**, under
[MIT](LICENSE).

**Friday Night Funkin' is by ninjamuffin99, PhantomArcade, evilsk8r and Kawai
Sprite.** The weeks, characters, arrows and music in this repo are their work,
included so the engine is playable as-is — **not** covered by the MIT licence
above, and not official or affiliated.
[Play and support the original.](https://ninja-muffin24.itch.io/funkin)

Details, and how to ask for the assets to be removed: **[CREDITS.md](CREDITS.md)**.
