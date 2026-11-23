# ITR DELTA2 — Custom row strings

How to write the text of the **Custom** rows of the ITR DELTA2 Garmin watch face.

Each of the 9 rows between the fixed top label (`ITR DELTA`) and the fixed bottom label (`-<VERSION>-`) shows the text of its own setting `Custom1`..`Custom9`. Open Garmin Connect > the watch face > Settings and type the string into the field. If a new setting does not appear, fully close and reopen Garmin Connect. The old ITR DELTA (8 rows, `Custom1`..`Custom8`) uses the same syntax except `$a`, `(WIDE, ...)` and `(BIG, ...)`.

In the tables below, a displayed text is shown for a sample moment; blanks matter, so count characters carefully.

## Rows and widths (characters = cells)

| Setting | Custom1 | Custom2 | Custom3 | Custom4 | Custom5 | Custom6 | Custom7 | Custom8 | Custom9 |
|---|---|---|---|---|---|---|---|---|---|
| chars | 11 | 13 | 15 | 15 | 15 | 15 | 15 | 13 | 11 |

- Text shorter than the row is centred; text longer than the row is cut on the right.
- Custom9 is the last Custom row (BIG acts as WIDE there).
- AOD (always-on low power): rows that contain a time code (`$h $k $m $g $n $a`) are shown as outlines; other rows stay dark.
- Old ITR DELTA: Row1..Row8 = 9 / 12 / 14 / 9 (large) / 9 (large) / 14 / 12 / 9 characters, and a row shows its Custom string only when its kind is set to Custom.

## Syntax

**The escape character is `$`** (since 11-21; Garmin Connect did not keep the backslash `\`). The backslash is still accepted with the same meaning, but always write `$`. A literal `$` is `$$`, which the font cannot show (blank).

| Write | Meaning |
|---|---|
| plain text | shown as is; lower case is shown as UPPER case |
| `$X` | value of code X, as many digits as needed |
| `$wX` | right aligned in w characters, blank padded (`$3B` -> ` 74`) |
| `$0wX` | zero padded (`$03B` -> `074`) |
| `${expr}` / `$0w{expr}` / `{expr}` | integer expression, formatted the same way |
| `$\|` | flexible blank: takes all spare characters of the row (several share them, first ones get the remainder) |
| `(GAUGE, L, $X [, lo, hi])` | bar gauge of L characters, lo..hi = empty..full |
| `(GRAPH, L, $X [, hours])` | history graph of L characters (5 L columns) |
| `(WIDE, text)` | DELTA2 only: text in double-width characters |
| `(BIG, text)` | DELTA2 only: text in double-width AND double-height characters (2x2 cells), spanning this row and the row below |
| `$c` (c not a code) | the character c itself |

Rules:
- A value longer than its width keeps all its digits (never truncated). No value -> `--` (padded to the width).
- Negative numbers with zero padding: `$03{$T}` at -5 -> `-05`.
- `$s` and `$z` (seconds) are blank during MIP sleep (fenix etc. update once a minute).
- At most 64 characters of the setting are read.

### Codes

Values are read in the background one at a time, so a just-changed setting takes a few seconds to fill in and a value can lag by its interval.

| Code | Value | Updated |
|---|---|---|
| H | heart rate (current) | 1 min |
| S | steps | 1 min |
| C | calories | 1 min |
| B | Body Battery | 5 min |
| T | temperature (weather, °C or °F by watch units) | 5 min |
| I | temperature of the watch's own sensor (same units; reads high on the wrist, so it differs from `$T`) | 5 min |
| P | pressure hPa | 5 min |
| O | SpO2 % | 5 min |
| R | resting heart rate (today) | 10 min |
| A | watch battery % | every update |
| W | weekday MON..SUN (text, not usable in expressions) | every update |
| a | **AM / PM** (DELTA2 only): 12 h watch setting -> `AM` (00:00-11:59) or `PM` (12:00-23:59); 24 h setting -> two blanks. Always 2 characters (`$5a` pads to 5, right aligned). Text, so not usable in `{expr}`, GAUGE or GRAPH. Counts as a time code (AOD) | every update |
| h | hour, 12 or 24 h as the watch is set | every update |
| k | hour, always 24 h (use this for time-zone math) | every update |
| m / s | minute / second | every update |
| Y / M / D | year (4 digits) / month / day | every update |
| g | GMT hour, always 24 h (UTC + the face's "GMT offset" setting, same time as the GMT row) | every update |
| n | GMT minute | every update |
| z | GMT second (same as `$s`, offsets are whole minutes) | every update |

Codes are case sensitive (`$h` hour vs `$H` heart rate, `$M` month vs `$m` minute, `$a` AM/PM vs `$A` battery).

The GMT codes follow the face setting "GMT offset" (phone setting "GMT (minutes from UTC)", or Customize > GMT offset on the watch, 15-minute steps, UTC-12:00..UTC+14:00). Only one GMT offset exists for the whole face.

Example: `$02h:$02m $a` -> `12:32 PM` (12 h) or `12:32` followed by 3 blanks (24 h: `$a` = 2 blanks).

### Expressions `{ }`

- Integers only: `$X` codes, numbers, `+ - * / %`, parentheses, unary minus. Spaces are allowed.
- `/` truncates toward zero. `%` is never negative for a positive divisor, so `($k - 9) % 24` wraps correctly (e.g. 3 - 9 -> 18).
- Division by 0, a missing value, `$W`, `$a`, or a syntax error -> `--`.
- Time zones: for the zone set as "GMT offset" simply use `$g` / `$n` / `$z` (handles half-hour zones; no math needed). For a second zone, compute from `$g` (offset relative to the GMT setting) or from `$k` (offset relative to the watch's local time). Use `$k`, not `$h`. Hour in zone UTC+z when the watch is at UTC+9: `$02{($k + z - 9 + 24) % 24}` (the `+ 24` is optional because `%` already wraps). Half-hour zones need minute math, e.g. India (+5:30) from Japan (+9): hour `$02{($k * 60 + $m - 210 + 1440) % 1440 / 60}`, minute `$02{($m + 30) % 60}`.
- Two-digit year: `$02{$Y % 100}`.
- Day-of-month wrap is NOT handled (the date does not change with a time-zone offset); a foreign date therefore cannot be shown.

### GAUGE

`(GAUGE, L, value [, lo, hi])`: value is `$X` or `{expr}`. L = 1..14 characters, each character = 5 columns of bar. Default ranges when lo/hi are omitted:

| Code | Range |
|---|---|
| A, B | 0..100 |
| O | 80..100 |
| T, I | -10..40 °C (14..104 °F if the watch uses °F) |
| P | 950..1050 |
| H | 40..180 |
| others, {expr} | 0..100 |

### GRAPH

`(GRAPH, L, $X [, hours])`: only H (heart rate), P (pressure), O (SpO2), B (Body Battery) and I (watch sensor temperature) from the watch's sensor history (`$T` has no graph: there is no weather history). L characters = 5 L columns, 8 levels, scaled to the min..max of the shown range. Default 12 minutes per column (L = 5 -> 5 h, L = 10 -> 10 h); with `hours`, the whole graph covers that many hours (max 48; longer values are cut to 48). Refreshed every 5 min. Only the first 4 GRAPH items over all rows (top row first) read the sensor history; any further GRAPH is shown blank Do not use more than 4 GRAPH items.

### GAUGE / GRAPH syntax notes

- The keyword is case insensitive; spaces around commas are fine.
- A `(` that is not a valid GAUGE / GRAPH / WIDE / BIG is shown as a normal `(` character.

### WIDE and BIG (ITR DELTA2 only)

One normal character is 5x7 dots in one cell. `(WIDE, text)` stretches every character to **2 cells wide** (10x7 dots, 1 row high), except `:` which stays 1 cell. `(BIG, text)` stretches every character to **2 cells wide and 2 rows high** (10x14 dots); `:` is 1 cell wide and 2 rows high.

- `text` may contain letters, digits, `$X` codes and `{expr}` (with widths like `$02h`). GAUGE, GRAPH and `$|` inside are ignored. WIDE / BIG can be mixed with other text, gauges and `$|` in the same string; they count as their cell width (2 per character, 1 per `:`) when the row is laid out and centred.
- Cell width example: `(BIG, $02h:$02m)` = `$02h` (2 characters = 4 cells) + `:` (1 cell) + `$02m` (4 cells) = **9 cells**.
- **BIG uses the row below.** The upper half of each character is drawn in this row, the lower half in the next row at the same columns. All neighbouring Custom rows differ in width by 0 or 2 cells and are centred, so the halves line up exactly (lower half at column + floor((n_below - n) / 2)).
- **Overlap rule:** where a lower half lies, it REPLACES whatever the row below has in those cells (its text, gauge or graph). The row below keeps its own content in all other cells. So under a BIG leave the row empty, or put text only outside the BIG's columns (e.g. seconds at the right end).
- Avoid a BIG in the row directly below another BIG: the lower halves of the first one overwrite part of the second one's upper half. Leave a row between two BIGs.
- A BIG in Custom9 (the last Custom row) has no free row below (the bottom row is the fixed version label), so it is drawn like WIDE (1 row high). A BIG never touches the row above.
- AOD: if the row holding the BIG has a time code (e.g. `$02h:$02m`), both of its halves are shown in AOD.

## Characters that can be shown

`0-9`, `A-Z` (all letters), space, and `: - % . / + ° ( ) , ! ? = ' * # < > _`. Anything else (including `$` and `\` themselves, `&`, `@`, `[`, `]`, `{` `}` as text, non-ASCII except `°`) is shown as a blank. Lower case letters are converted to upper case. Use replacements such as `°` for `deg` or `+` for `&`. (The fixed bottom label of DELTA2 uses a lowercase `p` glyph, e.g. `V01p0`; it exists only there, a `p` typed in a Custom string is shown as `P`.)

## Examples (ITR DELTA2; sample 12:32:05 JST, BB 74, HR 72, RHR 58)

| Setting (chars) | String | Display |
|---|---|---|
| Custom1 (11) | `CUSTOM $03B%` | `CUSTOM 074%` (11 chars, fills the row) |
| Custom2 (13) | `UK $02{$k - 9}:$02m` | `UK 03:32` centred (8 chars) |
| Custom3 (15) | `NY$\|$02{($k - 14) % 24}:$02m` | `NY` + 8 blanks + `22:32` |
| Custom3 (15) | `GMT $02g:$02n:$02z` | `GMT 03:32:05` centred (GMT offset = UTC+0) |
| Custom3 (15) | `IST$\|$02g:$02n` | `IST` + 7 blanks + `09:02` (GMT offset UTC+5:30) |
| Custom3 (15) | `$02h:$02m $a` | `12:32 PM` centred (12 h) / `12:32` + 3 blanks (24 h) |
| Custom3 (15) | `$W $Y-$02M-$02D` | `TUE 2026-11-17` |
| Custom4 (15) | `(BIG, $02h:$02m)` | big `12:32`, 9 cells wide, centred; upper half here, lower half in Custom5's row |
| Custom5 (15) | `$\|:$02s` | `:05` at the right end (cells 12..14), clear of the BIG's columns 3..11 |
| Custom4 (15) | `(WIDE, $02m:$02s)` | `32:05` double width, 9 cells, 1 row high |
| Custom6 (15) | `HR $H$\|RHR $R` | `HR 72` + blanks + `RHR 58` |
| Custom7 (15) | `HR(GRAPH, 12, $H)` | `HR` + 12-character heart-rate graph (60 columns = 12 h) |
| Custom2 (13) | `T$\|(GAUGE, 10, $T)` | `T` + 2 blanks + 10-character temperature bar |
| Custom8 (13) | `STP$\|$S` | `STP` + blanks + steps |
| Custom9 (11) | `BB$\|(GAUGE, 8, $B)` | `BB` + 1 blank + 8-character Body Battery bar |
| Custom9 (11) | `(BIG, $3A)` | acts as WIDE: battery % in 3 digits = 6 cells |

Defaults of ITR DELTA2: Custom1 `GMT $02g:$02n`, Custom2 `BAT$|(GAUGE, 8, $A)`, Custom3 `$W $Y-$02M-$02D`, Custom4 `(BIG, $02h:$02m)`, Custom5 `$|:$02s`, Custom6 `HR $H$|RHR $R`, Custom7 `HR(GRAPH, 12, $H)`, Custom8 `STP$|$S`, Custom9 `BB$|(GAUGE, 8, $B)` (the current ones are in `resources/settings/properties.xml`).

## Old ITR DELTA examples (rows 9 / 12 / 14 characters)

| Row (chars) | String | Display (sample) |
|---|---|---|
| 12 | `CUSTOM $03B%` | `CUSTOM 074%` |
| 9 | `UK $02{$k - 9}:$02m` | `UK 03:32` at 12:32 JST |
| 12 | `NY$\|$02{($k - 14) % 24}:$02m` | `NY     22:32` at 12:32 JST |
| 9 | `NY $02g:$02n` | `NY 22:32` with GMT offset set to UTC-5 |
| 14 | `HPA $P$\|(GRAPH, 5, $P)` | `HPA 1013` + blanks + 5-character pressure graph |
| 12 | `BB(GRAPH, 10, $B, 24)` | `BB` + 24 h Body Battery graph |
| 9 | `SPO2$\|$O%` | `SPO2  96%` |
| 14 | `$W $02D/$02M $02{$Y % 100}` | `TUE 17/11 26` |
| 9 | `$3H/$3R` | ` 72/ 58` (current / resting HR) |

## License

See `LICENSE`.
