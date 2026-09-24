# Making mods for H-Slice

A mod is **one folder and one line of text**. The mod builder makes the folder;
you add the line. This guide takes you from a pile of files to a song in
Freeplay, then covers everything underneath for when you want more control.

**The short version:** open `mod-builder.html`, drop in the song's files, press
Build, put the folder in `mods/`, add one line to `mods.js`. Done.

| | |
|---|---|
| [1. Quick start](#1-quick-start) | the whole process in eight steps |
| [2. Getting the files](#2-getting-the-files) | where things live in V-Slice, Psych and `.fnfc` mods |
| [3. The mod builder](#3-the-mod-builder) | what each part of the screen tells you |
| [4. Setting up the stage](#4-setting-up-the-stage) | background, positions and sizes, in the game |
| [5. Engine assets](#5-engine-assets--do-this-once) | arrows, ratings, Boyfriend — one-time setup |
| [6. What's in a mod](#6-whats-in-a-mod) | the files the builder makes, and why |
| [7. Writing a mod by hand](#7-writing-a-mod-by-hand) | the `HSLICE` API, for full control |
| [8. When something's wrong](#8-when-somethings-wrong) | messages and their fixes |
| [9. What H-Slice doesn't do yet](#9-what-h-slice-doesnt-do-yet) | so nothing surprises you |

---

## Why mods are `.js` files

You'll notice a mod's main file is `mod.js`, not a `.json`. That's deliberate,
and it's why H-Slice runs from a double-click with no server.

When you open `index.html` straight from your disk, the browser treats the page
as untrusted and **blocks it from reading `.json` and `.xml` files** — that's
what stops a downloaded web page from reading your documents. It *does* allow
`<script>`, `<img>` and `<audio>`. So the builder packs the chart, the metadata,
the characters and the spritesheet data into a script, and everything else is
an image or a sound. Nothing needs the blocked route, so nothing needs a server.

---

## 1. Quick start

1. **Collect the song's files** — its chart, the opponent's spritesheet, the
   audio. [Section 2](#2-getting-the-files) shows where they are. Or just grab
   the whole mod folder.
2. **Open `mod-builder.html`.** Double-click it; it works offline.
3. **Drop the files in** — or the whole folder, or a `.fnfc`.
4. **Read the review.** Anything red blocks the build and tells you why. Amber
   still plays, just not perfectly. Grey is for information.
5. **Press Build.** You get `<song>.zip`.
6. **Open the zip and drag the `<song>` folder into `h-slice/mods/`.** No `mods`
   folder yet? Make one next to `index.html`.
7. **Add one line to `h-slice/engineassets/mods.js`** — the builder shows it with
   a Copy button:
   ```js
   HSLICE.load('mods/bro/mod.js');
   ```
   One line per mod. No `mods.js` yet? The builder can download one for you.
8. **Open `index.html`.** The song is in Freeplay.

What you end up with:

```
h-slice/
├─ index.html
├─ mod-builder.html
├─ engineassets/
│  └─ mods.js          HSLICE.load('mods/bro/mod.js');
└─ mods/
   └─ bro/
      ├─ mod.js        chart, metadata, characters, sheet data
      ├─ stage.js      the layout (optional — see section 4)
      ├─ Inst.ogg
      ├─ Voices-bf.ogg
      ├─ Voices-bingo.ogg
      ├─ bingo_sheet.png
      ├─ icon-bingo.png
      └─ bg.png
```

The first time, you'll also want the engine assets — the arrows, ratings and
Boyfriend. That's a one-time job; see [section 5](#5-engine-assets--do-this-once).

---

## 2. Getting the files

**Pick the files out by hand, one song at a time.** It's more reliable than
dropping a whole mod folder or a `.fnfc` — those work, but a mod folder holds
several songs and a pile of art that isn't part of yours, and it's easy to end up
building the wrong thing.

For one song you want nine files:

```
song-chart.json  ·  song-metadata.json          the chart
Inst.ogg  ·  Voices-bf.ogg  ·  Voices-opponent.ogg   the audio
opponent.png  ·  opponent.xml  ·  opponent.json      the opponent
bg.png                                                the background
```

Boyfriend and Girlfriend need nothing — they're built in. Here's where each
file lives.

### A V-Slice mod (Funkin' 0.3 and later)

| what | where |
|---|---|
| chart | `data/songs/<song>/<song>-chart.json` |
| metadata | `data/songs/<song>/<song>-metadata.json` |
| a variation (erect, pico…) | `data/songs/<song>/<song>-chart-erect.json` and `-metadata-erect.json` |
| audio | `songs/<song>/Inst.ogg`, `Voices-<character>.ogg` |
| variation audio | `songs/<song>/Inst-erect.ogg`, `Voices-<character>-erect.ogg` |
| character sheets | `images/characters/<name>.png` + `.xml` |
| character data | `data/characters/<name>.json` |
| stage | `data/stages/<stage>.json`, plus the images it names |
| icons | `images/icons/icon-<name>.png` |

You need **both** the chart and the metadata — the chart has the notes, the
metadata has the tempo and the characters.

### A `.fnfc` file

That's the Funkin' chart editor's export: a zip holding the chart, metadata and
audio. The builder opens it, but you're better off unzipping it and picking the
files out yourself — it carries no spritesheets, so you'd be adding those
separately anyway.

### A Psych Engine mod

| what | where |
|---|---|
| chart | `data/<song>/<song>.json` (normal), `<song>-hard.json`, `<song>-easy.json`… |
| audio | `songs/<song>/Inst.ogg`, and `Voices.ogg` — or `Voices-Player.ogg` + `Voices-Opponent.ogg` in Psych 1.0 |
| character sheets | `images/characters/<name>.png` + `.xml` |
| character data | `characters/<name>.json` |
| stage | `stages/<stage>.json` — positions only (see below) |
| icons | `images/icons/icon-<name>.png` |

Psych 0.6, 0.7 and 1.0 charts all work, including 1.0's new note format.

Psych stages are drawn by Lua scripts, which H-Slice can't run — so a Psych
stage file only gives you the characters' positions. Add the stage's background
picture yourself and arrange it in the game.

### The base game

Boyfriend and Girlfriend are **built into H-Slice** — you only ever need their
`.png` files, once. Their `.xml` files aren't needed. See
[section 5](#5-engine-assets--do-this-once).

### What the builder ignores

Lua and HScript files, `pack.json`, Psych's separate `events.json`, menu art,
freeplay art, and anything else a song doesn't use. Hover a file in the
builder's list to see why it was set aside.

---

## 3. The mod builder

Everything happens in your browser — nothing is uploaded.

### The file list

On the left, grouped by type. Hover any file to see its full path and, if it
isn't used, why. The **×** removes a file. Dropping the same file again
replaces the old copy.

### Song

- **Name in Freeplay** — what players see.
- **Mod folder** — the folder name inside `mods/`, and the name in the
  `HSLICE.load` line. Letters, numbers and dashes.
- **Artist** and **Freeplay colour** — the colour defaults to the opponent's
  health-bar colour.

Below that: the tempo, the stage name, the number of camera events, any
variations, and each difficulty with its note count and scroll speed. If a
folder holds several songs, a picker appears above this card.

### Characters

One row per character the chart needs.

**The preview** draws the first frame of the idle, straight from the sheet. If
it shows the wrong character, the sheet's matched wrong — fix it with the
dropdown next to the name.

**The dropdown** says where the character comes from:

| choice | means |
|---|---|
| Built-in Boyfriend / Girlfriend | uses the engine's own — nothing is added to the mod |
| Sheet: `<name>` | uses that spritesheet |
| Leave it out | shows a coloured block instead |

How the builder matches sheets to characters, in order:

1. a character `.json` that names its sheet
2. the closest name — `bingo` finds `bingo.png`, `bingo_sheet.png`, `BINGO-assets.png`
3. if exactly one sheet and one character are left over, they're paired (and
   the builder says it guessed, so check the preview)
4. `bf` and `gf` default to the built-ins; so do `bf-car`, `gf-christmas` and
   other variants, until you give them a sheet of their own

**The animations.** With a character `.json`, each animation shows as a chip —
green if it's in the sheet, amber if it isn't. Without one, the builder reads
the animations off the sheet and shows a dropdown for each:

| slot | used for |
|---|---|
| `idle` | standing between notes |
| `danceLeft`, `danceRight` | alternating instead of `idle`, like Girlfriend |
| `singLEFT`, `singDOWN`, `singUP`, `singRIGHT` | the notes |
| `singLEFTmiss` … | a miss, if the sheet has one |

Each dropdown lists every animation in the sheet with its frame count. If the
guess is wrong, pick the right one.

A sheet with a **single animation** gets a warning — that's almost always a
cutscene, not a character.

### Audio

Checks the instrumental and each character's vocals, for the song and every
variation. Files are renamed to exactly what the engine looks for — `inst.ogg`
becomes `Inst.ogg`, for example — and the list shows each rename.

Songs need `.ogg`, `.mp3` or `.wav`. Anything else is flagged; Audacity
converts for free.

### Background

Pick the picture to use. It's placed behind everyone, moves with the camera,
and is sized so its edges never show — even when the chart zooms out. You can
fine-tune it in the game ([section 4](#4-setting-up-the-stage)).

If you dropped a V-Slice stage file, its layers are used instead, and each
picture is put exactly where the stage file expects it.

### Checks

| colour | means |
|---|---|
| **red** | blocks the build, and says what to add or fix |
| **amber** | builds and plays, with something missing — a silent part, a character with no sheet |
| **grey** | information: what was left out and why, and anything H-Slice won't do with this song |

### Build

Downloads `<song>.zip` and shows the install steps with the exact `mods.js`
line and a Copy button, plus a picture of where every file goes.

---

## 4. Setting up the stage

### The background

Any `bg.png` (or `background.png`, `.jpg`, `.webp`) in the mod folder is used
automatically, unless the stage file already defines layers. The builder names
it `bg` for you.

### Arranging in the game

Play the song and press **E**. The song pauses, the camera pulls back, and
everything gets an outline you can grab.

| | |
|---|---|
| drag | move a character or the background |
| mouse wheel, or `-` / `=` | resize — characters grow from their feet so they stay on the floor |
| arrows | nudge 1 pixel, Shift for 10 |
| Tab | switch between them |
| **H** | flip the selected character to face the other way (their left and right poses swap to match) |
| **C** | the camera tool — set a fixed shot for this stage (drag, wheel to zoom, Backspace removes it) |
| **S** | save |
| R | reset |
| F | fit the characters on screen · Shift+F includes the background |
| E | done |

### Saving: `stage.js`

**S downloads `stage.js`. Put it in the mod's folder**, next to `mod.js`. It
loads automatically — nothing to edit. It's the only place a mod's layout is
kept, so what's in the file is what everyone gets.

It's short, and fine to edit by hand:

```js
HSLICE.stage("basement", {
  bf:  { x: 1047, y: 386, scale: 1.1 },
  gf:  { x: 297,  y: 195, scale: 0.9 },
  dad: { x: -82,  y: 487, scale: 1 },
  bg:  { image: "bg", x: -987, y: -361, scale: 1.85 },
  camera: { x: 640, y: 420, zoom: 0.7 },   // only if you set one with C
});
```

### More than one layer

For depth, or props in front of the characters, use `layers` instead of `bg`.
The pictures go in the mod folder:

```js
HSLICE.stage("basement", {
  zoom: 0.9,
  bf:  { x: 1047, y: 386, scale: 1 },
  gf:  { x: 297,  y: 195, scale: 1 },
  dad: { x: -82,  y: 487, scale: 1 },
  layers: [
    { image: "sky",   x: -900, y: -500, scale: 1.4, scroll: 0.3 },
    { image: "wall",  x: -600, y: -300, scale: 1.2, scroll: 0.9 },
    { image: "floor", x: -650, y: 600,  scale: 1 },
    { image: "table", x: -200, y: 700,  scale: 1, front: true },
  ],
});
```

| field | meaning |
|---|---|
| `image` | a picture in the mod folder, no extension needed |
| `x`, `y`, `scale` | position and size. `scale` can be `[x, y]` |
| `scroll` | depth. `1` moves with the world; below 1 lags behind (far away); `0` is fixed to the screen; above 1 races ahead (foreground) |
| `front` | `true` draws it over the characters |
| `alpha`, `flipX`, `flipY` | as named |

Layers draw in the order listed. The editor moves and resizes these too, and
**S** writes them all back.

---

## 4b. Weeks (story mode)

Songs can be grouped into weeks, played back to back with the score carried
across. Each song is still an ordinary mod folder — it just lives in a week
folder instead of `mods/`:

```
engineassets/
├─ weeks.js
└─ weeks/
   ├─ week1/
   │  ├─ tutorial/     ← a mod folder, exactly as the builder makes it
   │  ├─ bopeebo/
   │  ├─ fresh/
   │  └─ dadbattle/
   └─ week2/
      ├─ spookeez/
      └─ south/
```

`engineassets/weeks.js` names the weeks and sets the song order:

```js
HSLICE.week('week1', 'Week 1', ['tutorial', 'bopeebo', 'fresh', 'dadbattle']);
HSLICE.week('week2', 'Week 2', ['spookeez', 'south', 'monster']);
```

The names in the list are the **folder names** inside that week. A song listed
but not installed yet is simply shown as missing — the rest still play.

These songs don't need a line in `mods.js`; `weeks.js` loads them. They appear
in Freeplay too, in week order, before any loose mods.

**Story** appears on the main menu once a week exists. Pick a week, choose the
difficulty, and Enter plays the whole week in order. Dying and retrying keeps
your place; quitting from the pause menu returns to the week list.

---

## 5. Engine assets — do this once

These belong to H-Slice itself, not to any one mod: the note arrows, ratings,
combo numbers, Boyfriend and Girlfriend, health icons, the title logo, and the
menu music and sounds. Set them up once and every mod uses them.

**Already done if you downloaded the release** — it ships with them. This
section is for building a set from your own files.

1. Open `mod-builder.html` and switch to the **Engine assets** tab.
2. Drop the files in (table below).
3. Press **Build**. You get `engineassets.zip`.
4. Open it and drag its `engineassets` folder next to `index.html`. When asked,
   choose **merge** or **replace** — your `mods.js` stays.

| drop in | what it's for |
|---|---|
| `NOTE_assets.png` + `.xml` | the arrows, from Psych or the base game |
| `sick.png` `good.png` `bad.png` `shit.png` | rating pop-ups |
| `num0.png` … `num9.png` | combo numbers |
| `BOYFRIEND.png`, `GF_assets.png` | Boyfriend and Girlfriend (no `.xml` needed) |
| `icon-bf.png`, `icon-gf.png`… | health icons |
| `logo.png` | the title screen logo — a single still image |
| `gameover.png` | the game over screen — a transparent PNG of the dead Boyfriend |
| `gameOver.ogg` | game over music, loops until you retry |
| `freakyMenu.ogg` | menu music |
| `scrollMenu.ogg` `confirmMenu.ogg` `cancelMenu.ogg` | menu sounds |

The tab shows where each file will go, and checks `NOTE_assets` has every arrow,
press, confirm and hold animation the engine uses.

**Drop all your sheets at once:** the tab writes `engineassets/sprites.js`,
which replaces any earlier one.

Use Psych's or the base game's `NOTE_assets.xml`. V-Slice's `notes.xml` is a
different skin format, and the builder will tell you so.

---

## 6. What's in a mod

| file | what it is |
|---|---|
| `mod.js` | the chart, metadata, characters and spritesheet frames |
| `stage.js` | the layout, if you saved one from the editor or dropped a stage file |
| `Inst.ogg`, `Voices-*.ogg` | the audio |
| `<sheet>.png` | each character's spritesheet |
| `icon-<name>.png` | health icons |
| `bg.png` | the background |
| `README.txt` | install steps, for anyone you share the mod with |

A few details worth knowing:

- **Spritesheets are pre-read.** The builder turns each `.xml` into a frame
  table inside `mod.js`, so the game never parses XML — a stray byte-order mark
  or a malformed file can't break it. The `.xml` isn't needed in the mod folder.
- **Everything is named per mod.** Two mods can both have a `bingo` with a
  `bingo_sheet.png`, and each gets its own.
- **One V-Slice chart holds every difficulty**, so it's stored once, not once
  per difficulty.

### Audio names

The builder renames these for you. If you're adding audio by hand:

| file | when |
|---|---|
| `Inst.ogg` | always |
| `Voices-<character>.ogg` | V-Slice: one per character, as the metadata lists them |
| `Voices.ogg` | Psych: one file for both singers |
| `Voices-Player.ogg`, `Voices-Opponent.ogg` | Psych 1.0 |
| `Inst-erect.ogg`, `Voices-bf-erect.ogg` | a variation's own audio |

`.ogg`, `.mp3` or `.wav`. A variation with no audio of its own uses the base
song's.

---

## 7. Writing a mod by hand

You don't need this if you use the builder. It's here for when you want
control the builder doesn't give, or want to understand what it made.

Everything a mod does goes through the `HSLICE` object. Wrap your mod in
`(() => { ... })();` so its names can't clash with another mod's.

### `HSLICE.song({...})`

Registers a song in Freeplay.

```js
(() => {
  HSLICE.song({
    id: "bro",                          // matches the folder name
    name: "Bro",
    artist: "Kawai Sprite",
    icon: "bingo",                      // health icon id for the opponent
    color: "#6b8f3a",                   // Freeplay background tint
    stage: "basement",
    audio: "mods/",                     // audio sits in mods/<id>/
    difficulties: ["easy", "normal", "hard"],

    // V-Slice: paste the files' contents in as-is
    metadata: { /* bro-metadata.json */ },
    charts:   { easy: chart, normal: chart, hard: chart },   // one chart holds them all

    // optional: variations with their own data and difficulties
    variations: {
      erect: { metadata: { /* ... */ }, difficulties: ["erect", "nightmare"],
               charts: { erect: erectChart, nightmare: erectChart } },
    },
  });
})();
```

For a Psych chart, leave out `metadata` and give one chart per difficulty:
`charts: { normal: {...}, hard: {...} }`.

### `HSLICE.character(name, definition)`

Defines a character for this mod. Takes a Psych **or** a V-Slice character
`.json`'s contents unchanged — just set `image` to the sheet:

```js
HSLICE.character("bingo", {
  image: "mods/bro/bingo_sheet",        // the .png in this mod's folder, no extension
  scale: 1,
  flip_x: false,
  healthicon: "bingo",
  healthbar_colors: [107, 143, 58],
  animations: [
    { anim: "idle",      name: "Bingo Idle",  fps: 24, loop: false, offsets: [0, 0] },
    { anim: "singLEFT",  name: "Bingo Left",  fps: 24, loop: false, offsets: [12, -4] },
    { anim: "singDOWN",  name: "Bingo Down",  fps: 24, loop: false, offsets: [0, -30] },
    { anim: "singUP",    name: "Bingo Up",    fps: 24, loop: false, offsets: [-8, 20] },
    { anim: "singRIGHT", name: "Bingo Right", fps: 24, loop: false, offsets: [-20, -5] },
  ],
});
```

`anim` is the slot, `name` is the animation's name in the sheet. Frame
`indices`, per-animation `offsets`, `position`, `camera_position` and
`sing_duration` all work as they do in Psych.

To borrow a built-in: `HSLICE.character("bf-car", { alias: "bf" })`.

### `HSLICE.sheet(key, frames)` and `HSLICE.atlas(key, xml)`

A spritesheet's frame data. `key` is the same path you give `image`.

- `HSLICE.sheet` takes a pre-read frame table — the builder writes these.
- `HSLICE.atlas` takes the `.xml` file's text as a string, if you're writing
  by hand. It's read in the browser; a byte-order mark is handled.

Give every sheet one or the other. Without them the engine has to read the
`.xml` from disk, which a double-clicked page isn't allowed to do — the
character would only appear when you run a server.

### `HSLICE.stage(name, definition)`

The layout, as described in [section 4](#4-setting-up-the-stage). Normally lives in
`stage.js`. A V-Slice stage `.json` pasted in unchanged also works — `props`,
`characters`, `cameraZoom` and `zIndex` are all understood.

### `HSLICE.load(src)`

Loads a script. `engineassets/mods.js` is just a list of these.

---

## 8. When something's wrong

The builder names most problems before you build. In the game, press **`**
(backtick) for the debug overlay — it shows each character's status and the
build number.

### In the builder

| message | fix |
|---|---|
| No chart found | add the chart `.json`, or the `.fnfc` |
| a chart but no metadata | V-Slice needs both halves — add `<song>-metadata.json` |
| only the "erect" variation is here | add the base `<song>-chart.json` and `-metadata.json` too |
| more than one song | pick one in the song picker; build each separately |
| mix a V-Slice chart with a Psych chart | drop one song's files at a time |
| not valid JSON — …line/column… | the file is damaged; it says where |
| looks like a cutscene, not a character sheet | find the character's real sheet — cutscene sheets have one long animation |
| has no matching `.png` | add the picture with the same name as the `.xml` |
| animation from the JSON isn't in the sheet | the JSON and the sheet don't match; that animation won't play |
| No instrumental / vocals missing | add the audio, or that part stays silent |
| `.m4a` — songs need `.ogg`, `.mp3` or `.wav` | convert it |

### In the game

| symptom | fix |
|---|---|
| the song isn't in Freeplay | check the `HSLICE.load(...)` line in `engineassets/mods.js`, and that the folder name matches |
| a character is a coloured block | it has no sheet — the block's text says why |
| a character faces the wrong way | set `flip_x` in its definition |
| a character floats or sinks | press E and drag them, or fix `offsets` in the definition |
| the background doesn't show | `bg.png` must be in the mod folder, next to `mod.js` |
| the layout didn't stick | after pressing S, move the downloaded `stage.js` into the mod folder |
| arrows are plain shapes | set up the engine assets ([section 5](#5-engine-assets--do-this-once)) |
| audio slightly out of sync | expected from a double-click; for charting, run a server (`python3 -m http.server`) for exact sync |
| changes don't show | Ctrl+Shift+R to skip the browser's cache |

---

## 9. What H-Slice doesn't do yet

So nothing surprises you — the builder mentions each of these when it applies:

- **Scripts.** Psych Lua and V-Slice HScript don't run. Stage pictures a Lua
  script would have placed need adding as a background by hand.
- **Events** other than camera focus, camera zoom, camera bop and play-animation.
  Things like *Change Character* or *Hey!* are skipped.
- **Special notes.** Hurt notes, alt-animation notes and other custom note types
  play as ordinary notes.
- **Animate-atlas characters.** Only Sparrow sheets (`.png` + `.xml`).
- **Animated stage props.** They show as still pictures.
- **Other note skins.** The arrows come from `NOTE_assets`; pixel notes and
  V-Slice note styles aren't supported.
- **Song audio** has to be `.ogg`, `.mp3` or `.wav`.
