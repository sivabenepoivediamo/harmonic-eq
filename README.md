# Harmonic EQ

A browser-based parametric equalizer where musical notes and scales become EQ bands. Open the HTML file in any modern browser — no installation needed.

The idea is simple: instead of dialing in filter frequencies by hand, you play notes on a keyboard (or pick a scale from a library), and each note becomes a peaking EQ band. You then adjust the gain and width of each band with sliders while audio plays through in real time.

---

## Input

Where you tell the EQ which notes to use for the **Input** track.

- **Text field** — type MIDI note numbers separated by spaces (middle C = 60, A4 = 69, etc.). Updates as you type.
- **Set** — commits the current note numbers as the starting point. Useful after typing a list you want to lock in before applying transforms.
- **Clear** — removes all input notes and resets the keyboard state.
- **A = [Hz]** — sets the tuning reference for A4. Default is 440 Hz. Changing it shifts all band frequencies proportionally without removing any bands.

---

## Root Note

Sets the root note for the Scale Library. Click any key on the small piano to choose a pitch class (C, C#, D, etc.), and use the **−/+** buttons to choose which octave. The label below always shows the current root as a note name and MIDI number (e.g. `C4 · 60`). Changing the root immediately reapplies the active scale at the new pitch.

---

## Scale Library

A searchable collection of 400+ scales organised into folders by category (Major/Minor, Pentatonic, Jazz, Indian, Asian, Symmetrical, European, Modal, and more).

- **Search field** — filters scale names across all categories as you type. Clearing it restores folder states.
- **Folders** — click a folder header to expand it, then click any scale name to apply it. Only one scale can be active at a time.
- **Active scale row** — once a scale is applied, a violet banner shows the scale name, root, and its intervals (R, b2, 2, b3, 3, 4, b5, 5, b6, 6, b7, 7). The **✕ clear** button removes the scale and empties the Scale Bands panel.

When you switch scales, existing gain and Q values are preserved where possible — bands that share the same note name keep their settings, and completely new bands are interpolated from their neighbors rather than reset to flat.

---

## EQ Curve

A real-time display of the combined EQ response from 20 Hz to 20 kHz on a log scale.

- The **blue curve** is the total EQ shape from all active bands across both tracks.
- When audio is playing, a **green spectrum** shows the live FFT of the signal behind the curve.
- **Dots** mark each band's position on the curve — blue for input notes, violet for scale notes, amber for notes currently held down.
- **Note name labels** float above each dot for easy identification.
- Horizontal dB gridlines are drawn at ±6, ±12, ±18 dB, with 0 dB highlighted.

---

## Audio Engine

Controls what signal passes through the EQ.

| Source | What it does |
|---|---|
| **Off** | No audio. Default state. |
| **Mic** | Streams your microphone through the EQ live. Requires browser permission. |
| **White** | White noise — flat spectrum, good for hearing the raw EQ shape. |
| **Pink** | Pink noise — softer high frequencies, sounds more natural. |
| **Brown** | Brown noise — heavily bass-weighted, warm rumble. |
| **File…** | Load any audio file. It loops continuously through the EQ. |

**Vol** — master output volume (0–100%).

The status dot shows what's happening: green (running), amber (loading), red (error, e.g. mic denied), grey (off).

---

## Global Link

Lets you link bands across **both tracks** so that moving one slider moves others.

- **MST** — all bands in both tracks share the same gain and Q. Moving any single slider updates every band everywhere.
- **PC** — bands that share the same note name (regardless of octave) are linked across both tracks. Moving C4 also moves C2, C5, etc.

These are toggles. Clicking an active button turns it off. Global link overrides the per-track link buttons in the Band panels.

---

## Input Bands / Scale Bands

Two side-by-side panels — one for notes you play/type (**Input Bands**, blue) and one for scale notes (**Scale Bands**, violet). Each note gets its own column with:

- **Note name** and frequency
- **Gain slider** (tall) — boost or cut from −18 dB to +18 dB. The fill turns red below zero.
- **Q slider** (shorter, amber) — controls band width. Higher Q = narrower and more surgical. Range is 1–30.
- **dB and Q readouts** that update as you drag
- **× button** — removes that individual note

**Interacting with sliders:**
- Click anywhere on the slider track to jump to that value
- Drag up/down to adjust
- Double-click/double-tap to reset to default (0 dB for gain, Q 30 for Q)
- Scroll wheel to nudge by 1 step

**Header controls:**

| Control | What it does |
|---|---|
| **− / +** | Switch between compact (small sliders, more bands visible) and full-size view. Auto-compacts when you have many bands. |
| **MST** | Links all bands *within this track* — moving one moves all. |
| **PC** | Links bands *within this track* that share the same note name. |
| **Clear** | Removes all bands from this track. |
| **Reset Gains** | Sets all gain sliders back to 0 dB. |
| **Reset Q** | Sets all Q sliders back to 30. |

**Band colours** give a quick visual summary of state:

| Colour | Meaning |
|---|---|
| Amber border | Note is currently held down |
| Green background | Linked via Global MST |
| Blue tint (input) / Violet tint (scale) | Linked by pitch class globally or within the track |

---

## MIDI / Keyboard

#### Mode
- **Latch** — notes toggle on and off each time you press a key. Good for building up a static chord.
- **Momentary** — notes are only active while held. Releasing removes them from the band list.

#### Oct
Shifts the octave range of the computer keyboard (0–8). The piano strip updates to show the new range.

#### Highlight
Controls which notes are coloured on the piano keys:
- **Both** — highlights input and scale notes together
- **Input** — highlights only what's in the Input Bands
- **Scale** — highlights only what's in the Scale Bands
- **None** — plain keyboard, no highlights

#### Piano keyboard strip
Shows three octaves of keys. Colours show which notes are active:

| Colour | Meaning |
|---|---|
| Amber pulse | Currently held (MIDI or keyboard) |
| Blue | In Input Bands |
| Violet | In Scale Bands (matched by note name across octaves) |
| Green | In both Input and Scale Bands |

You can click or tap keys directly to trigger notes.

#### Computer keyboard layout

Two rows of the keyboard map to two chromatic octaves:

- **Lower octave:** `Z S X D C V G B H N J M` → C through B
- **Upper octave:** `Q 2 W 3 E R 5 T 6 Y 7 U` → C through B (one octave up)
- **Shift octave:** `−` to go down, `+` to go up

---

## Input Transform / Scale Transform

Each track has its own chain of operations that can reshape the note set before it reaches the EQ bands. Operations are toggled on/off individually and can be reordered by dragging the `⠿` handle. They run top-to-bottom in the order shown.

The default order is: **Mod → Multiply → Transpose → Invert → Complement → Parallel Mode → Relative Mode → Mirror → Negative → Close → Spread → Resize.**

---

### Mod
Strips octave information and reduces all notes to pitch classes (0–11). Removes duplicates. Useful for abstracting a note set into its interval pattern before other operations.

*Example: notes 60, 64, 67, 72 become 0, 4, 7 (a major triad pattern).*

---

### Multiply
Multiplies every note number by an integer. Typically used after Mod to stretch or compress interval relationships.

*Example: pitch classes 0, 4, 7 multiplied by 2 become 0, 8, 14.*

---

### Transpose
Shifts every note up or down by a fixed number of semitones. Positive = up, negative = down.

*Example: entering `+12` moves all notes up one octave.*

---

### Invert
Mirrors the note set around one of its own notes. The number field sets which note is the mirror point (0 = lowest note, 1 = second lowest, etc.).

*Example: a C major chord inverted around index 0 (C) gives the same intervals flipped downward.*

---

### Complement
Replaces the note set with all the notes that are **not** in it, within the same span. If you have 3 notes from a 12-note octave, complement gives you the other 9.

---

### Parallel Mode
Derives a mode of the note set by rotating the interval pattern to start from a different scale degree. The **k** field selects which degree to use as the new root (0 = no change, 1 = start from the second note, etc.). The output always has the same number of notes as the input and is anchored to the same pitch region.

*Example: a C major triad [C, E, G] at k=1 gives the intervals measured from E: [E, G, C] — a minor third up, then a perfect fourth.*

---

### Relative Mode
Rotates through the note set starting at a given offset and reads out a given number of notes, wrapping and extending upward by an automatically calculated period for each cycle. The two fields are **offset** (which note to start from) and **length** (how many notes to output; 0 means same as input count).

*This is how you derive modes from a scale — rotating a major scale by 1 gives Dorian, by 2 gives Phrygian, etc.*

---

### Mirror
Reflects the note set so that its interval structure is flipped upside-down, keeping the lowest note in place. The result has the same outer span but the intervals run in reverse order from the bottom.

*Example: a major triad [C, E, G] mirrored becomes [C, Ab, F] — the major third and minor third swap direction.*

---

### Negative
Applies the canonical harmonic negation of the note set. Each note is reflected around a pitch-class axis derived from the **root** field: axis = (root × 2 + 7) mod 12. With the default root of 0 the axis is 7 (G), which corresponds to the circle-of-fifths reflection used in traditional negative harmony. Octave register is preserved note-by-note. Output is sorted ascending.

*Example: a C major triad negated at root 0 produces an F minor triad — its harmonic mirror in the key of C.*

---

### Close
Folds all notes into a single octave, like Mod, but instead of always landing in octave 0 it uses whichever octave already contains the most notes from the original set. Duplicates (after folding) are removed. Output is sorted ascending.

*Example: notes spread across three octaves collapse into the octave where most of them were already clustered.*

---

### Spread
Voices the notes across octaves to open up clusters. Notes are redistributed so each one is at least a perfect fourth away from the one below it. The **top cluster** checkbox relaxes this rule for the final note only, letting the voicing close up at the top.

*Example: three notes all a semitone apart get spread across multiple octaves into an open voicing.*

---

### Resize
Extracts a slice of notes from the set using cyclic index access. The **start** and **end** fields set the index range. Indices beyond the set size wrap cyclically, adding or subtracting the calculated period — so you can reach into higher or lower octave repetitions of the pattern. If start is greater than end, the output is reversed.

*Example: a 3-note set with start=1, end=4 gives notes at indices 1, 2, 0+octave, 1+octave — extending the pattern upward.*
