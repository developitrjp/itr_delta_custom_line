# ITR DELTA — Custom row strings

English | [日本語](CUSTOM_STRING_ja.md)

How to write the text of the **Custom** rows of the ITR DELTA Garmin watch face. Get the watch face on [Garmin Connect IQ](https://apps.garmin.com/apps/4bc2191c-0fc0-4380-b7a0-36d3c6907dc3).

Each of the 9 rows between the fixed top label (`ITR DELTA`) and the fixed bottom label (`-<VERSION>-`) shows the text of its own setting `Custom1`..`Custom9`. Open Garmin Connect > the watch face > Settings and type the string into the field. If a new setting does not appear, fully close and reopen Garmin Connect.

## For AI assistants

If you are an AI assistant asked to write a Custom setting from this manual, follow these rules.

1. Ask (or infer) the **row number** (Custom1..Custom9) and use its width from the table below; the string must fit the row (text longer than the row is cut on the right).
2. Answer with the string in a **code block** (copy-ready, one block per row), then the **expected display** for a sample moment (state the sample values), character by character, and the **character count** against the row width. Use code blocks so that `$` is not read as a math delimiter.
3. The setting is typed into the phone setting field **as is**: do not escape anything. The flexible blank is the two characters `$|`, a dollar sign and a vertical bar (never `$\|`).
4. Common mistakes to avoid:
   - Codes are case sensitive: `$h` hour vs `$H` heart rate, `$m` minute vs `$M` month, `$a` AM/PM vs `$A` battery.
   - `(GAUGE, L, $X, lo, hi)` and `(GRAPH, L, $X, hours)`: the **length L comes first**, then the code.
   - GRAPH works only with H, P, O, B, I, and only the first 4 GRAPH items over all rows are drawn.
   - Only the characters listed in "Characters that can be shown" appear; everything else (`&`, `@`, `[`, `]`, non-ASCII except `°`) is a blank. Lower case is shown as upper case.
   - For time zones use `$g` `$n` `$z` (the face's GMT offset) or `$k` (24 h hour) in expressions, never `$h`; day changes are not handled.
   - BIG uses the row below (its lower half replaces that row's cells), so keep the row below empty or outside the BIG's columns; in Custom9 (the last row) BIG acts as WIDE.
   - A value is `--` when no data is available (e.g. SpO2 not measured yet).
5. You cannot see the watch. Do not claim it looks right: give the expected display and the character count so that the user can check it, and say if something depends on a setting (12 / 24 h watch setting, GMT offset, units).
6. Start from the **Examples** and the **Default settings** below and adapt them.

You can try strings in the [Custom row checker](https://developitrjp.github.io/itr_delta_custom_line/custom_checker.html) (a web page that shows roughly how the rows will look).

In the tables below, a displayed text is shown for a sample moment; blanks matter, so count characters carefully.

## Rows and widths (characters = cells)

| Setting | Custom1 | Custom2 | Custom3 | Custom4 | Custom5 | Custom6 | Custom7 | Custom8 | Custom9 |
|---|---|---|---|---|---|---|---|---|---|
| chars | 11 | 13 | 15 | 15 | 15 | 15 | 15 | 13 | 11 |

- Each row has its own alignment setting (`Align1`..`Align9`: Left / Centre / Right, default Centre). Text longer than the row is cut on the right, whatever the alignment. A `$|` (flexible blank) fills the row, so alignment makes no difference then.
- Custom9 is the last Custom row (BIG acts as WIDE there).
- AOD (always-on low power): rows that contain a time code (`$h $k $m $a`; the GMT codes `$g $n $z` do not count, so a GMT row is dark in AOD) are shown as outlines; other rows stay dark.

## Syntax

**The escape character is `$`.** A literal `$` is `$$`, which the font cannot show (blank).

| Write | Meaning |
|---|---|
| plain text | shown as is; lower case is shown as UPPER case |
| `$X` | value of code X, as many digits as needed |
| `$wX` | right aligned in w characters, blank padded (`$3B` -> ` 74`) |
| `$0wX` | zero padded (`$03B` -> `074`) |
| `${expr}` / `$0w{expr}` / `{expr}` | integer expression, formatted the same way |
| `(GAUGE, L, $X [, lo, hi])` | bar gauge of L characters, lo..hi = empty..full |
| `(GRAPH, L, $X [, hours])` | history graph of L characters (5 L columns) |
| `(WIDE, text)` | text in double-width characters |
| `(BIG, text)` | text in double-width AND double-height characters (2x2 cells), spanning this row and the row below |
| `$c` (c not a code) | the character c itself |

The flexible blank is written with two characters: a dollar sign and a vertical bar, `$|` (shown in a list here because the vertical bar breaks Markdown tables). It takes all spare characters of the row (several share them, first ones get the remainder). Example: `NY$|$02{($k - 14) % 24}:$02m`.

Rules:
- With a width the value never grows past it: larger values stop at all 9s (`$04S` at 12000 steps shows `9999`; a minus sign takes one column). Without a width all digits are shown. No value -> `--` (padded to the width).
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
| a | **AM / PM**: 12 h watch setting -> `AM` (00:00-11:59) or `PM` (12:00-23:59); 24 h setting -> two blanks. Always 2 characters (`$5a` pads to 5, right aligned). Text, so not usable in `{expr}`, GAUGE or GRAPH. Counts as a time code (AOD) | every update |
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

`(GAUGE, L, value [, lo, hi])`: value is `$X` or `{expr}`. L = 1..15 characters, each character = 5 columns of bar. Default ranges when lo/hi are omitted:

| Code | Range |
|---|---|
| A, B | 0..100 |
| O | 80..100 |
| T, I | -10..40 °C (14..104 °F if the watch uses °F) |
| P | 950..1050 |
| H | 40..180 |
| others, {expr} | 0..100 |

### GRAPH

`(GRAPH, L, $X [, hours])`: only H (heart rate), P (pressure), O (SpO2), B (Body Battery) and I (watch sensor temperature) from the watch's sensor history (`$T` has no graph: there is no weather history). L characters = 5 L columns, 8 levels, scaled to the min..max of the shown range. Default 12 minutes per column (L = 5 -> 5 h, L = 10 -> 10 h); with `hours`, the whole graph covers that many hours (max 48; longer values are cut to 48). Refreshed every 5 min. Only the first 4 GRAPH items over all rows (top row first) read the sensor history; any further GRAPH is shown blank, so do not use more than 4 GRAPH items. Where the sensor records rarely (SpO2: about one sample an hour or less), the columns between samples are filled by a straight line between the neighbouring samples.

### GAUGE / GRAPH syntax notes

- The keyword is case insensitive; spaces around commas are fine.
- A `(` that is not a valid GAUGE / GRAPH / WIDE / BIG is shown as a normal `(` character.

### WIDE and BIG 

One normal character is 5x7 dots in one cell. `(WIDE, text)` stretches every character to **2 cells wide** (10x7 dots, 1 row high), except `:` which stays 1 cell. `(BIG, text)` stretches every character to **2 cells wide and 2 rows high** (10x14 dots); `:` is 1 cell wide and 2 rows high.

- `text` may contain letters, digits, `$X` codes and `{expr}` (with widths like `$02h`). GAUGE, GRAPH and `$|` inside are ignored. WIDE / BIG can be mixed with other text, gauges and `$|` in the same string; they count as their cell width (2 per character, 1 per `:`) when the row is laid out and centred.
- Cell width example: `(BIG, $02h:$02m)` = `$02h` (2 characters = 4 cells) + `:` (1 cell) + `$02m` (4 cells) = **9 cells**.
- **BIG uses the row below.** The upper half of each character is drawn in this row, the lower half in the next row at the same columns. All neighbouring Custom rows differ in width by 0 or 2 cells and are centred, so the halves line up exactly (lower half at column + floor((n_below - n) / 2)).
- **Overlap rule:** where a lower half lies, it REPLACES whatever the row below has in those cells (its text, gauge or graph). The row below keeps its own content in all other cells. So under a BIG leave the row empty, or put text only outside the BIG's columns (e.g. seconds at the right end).
- Avoid a BIG in the row directly below another BIG: the lower halves of the first one overwrite part of the second one's upper half. Leave a row between two BIGs.
- A BIG in Custom9 (the last Custom row) has no free row below (the bottom row is the fixed version label), so it is drawn like WIDE (1 row high). A BIG never touches the row above.
- AOD: if the row holding the BIG has a time code (e.g. `$02h:$02m`), both of its halves are shown in AOD.

## Characters that can be shown

`0-9`, `A-Z` (all letters), space, and `: - % . / + ° ( ) , ! ? = ' * # < > _`. Anything else (including `$` and `\` themselves, `&`, `@`, `[`, `]`, `{` `}` as text, non-ASCII except `°`) is shown as a blank. Lower case letters are converted to upper case. Use replacements such as `°` for `deg` or `+` for `&`. (The fixed bottom label `-<VERSION>-` is always shown in upper case, too.)

## Examples (ITR DELTA; sample 12:32:05 JST, BB 74, HR 72, RHR 58)

- Custom1 (11): `CUSTOM $03B%`  ->  `CUSTOM 074%` (11 chars, fills the row)
- Custom2 (13): `UK $02{$k - 9}:$02m`  ->  `UK 03:32` centred (8 chars)
- Custom3 (15): `NY$|$02{($k - 14) % 24}:$02m`  ->  `NY` + 8 blanks + `22:32`
- Custom3 (15): `GMT $02g:$02n:$02z`  ->  `GMT 03:32:05` centred (GMT offset = UTC+0)
- Custom3 (15): `IST$|$02g:$02n`  ->  `IST` + 7 blanks + `09:02` (GMT offset UTC+5:30)
- Custom3 (15): `$02h:$02m $a`  ->  `12:32 PM` centred (12 h) / `12:32` + 3 blanks (24 h)
- Custom3 (15): `$W $Y-$02M-$02D`  ->  `TUE 2026-11-17`
- Custom4 (15): `(BIG, $02h:$02m)`  ->  big `12:32`, 9 cells wide, centred; upper half here, lower half in Custom5's row
- Custom5 (15): `$|:$02s`  ->  `:05` at the right end (cells 12..14), clear of the BIG's columns 3..11
- Custom4 (15): `(WIDE, $02m:$02s)`  ->  `32:05` double width, 9 cells, 1 row high
- Custom6 (15): `HR $H$|RHR $R`  ->  `HR 72` + blanks + `RHR 58`
- Custom7 (15): `HR(GRAPH, 12, $H)`  ->  `HR` + 12-character heart-rate graph (60 columns = 12 h)
- Custom2 (13): `T$|(GAUGE, 10, $T)`  ->  `T` + 2 blanks + 10-character temperature bar
- Custom8 (13): `STP$|$S`  ->  `STP` + blanks + steps
- Custom9 (11): `BB$|(GAUGE, 8, $B)`  ->  `BB` + 1 blank + 8-character Body Battery bar
- Custom9 (11): `(BIG, $3A)`  ->  acts as WIDE: battery % in 3 digits = 6 cells

Default settings of ITR DELTA:

| Setting | Default string | Alignment |
|---|---|---|
| Custom1 | ` $W $02M-$02D ` | Centre |
| Custom2 | `GMT $02g:$02n` | Centre |
| Custom3 | `$a (BIG, $02h:$02m)` | Left |
| Custom4 | `$02s` | Right |
| Custom5 | `(GAUGE, 12, $s, 0, 60)` | Right |
| Custom6 | `BATT (GAUGE, 10, $A)` | Centre |
| Custom7 | `AP(GRAPH, 5, $P) O2(GRAPH, 5, $O)` | Centre |
| Custom8 | `STEP (GAUGE, 8, $S, 0, 10000)` | Centre |
| Custom9 | `HR $03H__$03R` | Centre |

## Support

If you find ITR DELTA useful, you can support its development: [Buy Me a Coffee](https://buymeacoffee.com/develop.itr.jp)

## License

See `LICENSE`.
