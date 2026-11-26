# ITR DELTA - Custom row manual

**ITR DELTA** is a dot-matrix watch face for Garmin watches: the whole round screen is a grid of 5x7 dot characters, and nine of its rows show text you write yourself - time, date, GMT time, heart rate, steps, Body Battery, SpO2, gauges, history graphs, wide and big letters, even small calculations.

> THIS WATCHFACE IS EXTREMELY CUSTOMIZABLE. ANY DESIGN IS POSSIBLE.

- Get it on Garmin Connect IQ: [ITR DELTA](https://apps.garmin.com/apps/4bc2191c-0fc0-4380-b7a0-36d3c6907dc3)
- Design gallery: **[gallery](https://developitrjp.github.io/itr_delta_custom_line/gallery.html)** - designs made with the checker below; open one in the checker and make it your own
- How to write the Custom rows: **[CUSTOM_STRING.md](CUSTOM_STRING.md)** (codes, gauges, graphs, WIDE / BIG, alignment, examples and the default settings); Japanese: **[CUSTOM_STRING_ja.md](CUSTOM_STRING_ja.md)** (日本語版)

## Let an AI write your Custom settings

You can give the manual to an AI assistant (Claude, ChatGPT, Gemini, ...) and ask it to design a row for you.

1. Give the AI the manual: paste the whole text of [CUSTOM_STRING.md](CUSTOM_STRING.md), or give it the raw link `https://raw.githubusercontent.com/developitrjp/itr_delta_custom_line/main/CUSTOM_STRING.md` (some AIs cannot open links; then paste the text or attach the file).
2. Say which row and what you want to see, for example:
   - "Using this manual, write Custom3 (15 characters) for a row that shows New York time, e.g. NY 22:32."
   - "Make Custom7 show a heart-rate graph and the pressure graph side by side."
   - "Fill all nine rows with a design for running: big time, heart rate, steps and battery."
3. Copy the string it gives you (from its code block) into the Custom setting of the watch face in Garmin Connect: Garmin Connect > the watch face > Settings > Custom1..Custom9. If a new setting does not appear, fully close and reopen Garmin Connect.
4. Check the result on the watch. The AI cannot see your watch: if the row looks wrong, tell it what you see (and the row number) and ask it to fix the string. Time-zone rows depend on the GMT offset setting; AM/PM on the watch's 12 / 24 h setting.

The manual contains a section "For AI assistants" with the rules the AI should follow (row widths, common mistakes, output format).

## Try your strings in the browser

**[Custom row checker](https://developitrjp.github.io/itr_delta_custom_line/custom_checker.html)**: a single web page (`custom_checker.html`, works offline too: download it and open it in a browser) where you type the nine Custom strings, pick the alignment and see roughly how the watch face will show them, with sample values for time, heart rate, steps and so on. It also warns about strings that are too long, characters that cannot be shown and more than 4 GRAPH items. It is a quick check, not the exact watch rendering.

Share your design: everything you type is stored in the page address, so **Copy share link** gives a URL that opens the same design for anyone. **Download PNG** saves the preview, and **Post to Discussions** opens a new post in the [Discussions](https://github.com/developitrjp/itr_delta_custom_line/discussions) of this repository with your design filled in (attach the PNG there). Look through the posts for ideas, or share your own.

**[Design gallery](https://developitrjp.github.io/itr_delta_custom_line/gallery.html)**: a page that shows a selection of designs (drawn in your browser from `designs.json`). "Open in checker" loads a design into the checker, and each row's **Copy** button copies its setting so you can paste it into Garmin Connect. **Copy design code** (checker and gallery) copies a whole design, and the checker's **Load code** box reads one back (nine rows, alignments, colour) as one code: paste it into the last setting of the watch face, **Design code**, in Garmin Connect, save, and after the next sync everything is filled in.

This repository holds the manual only; the watch face source code is not published.

## Support

If you find ITR DELTA useful, you can support its development: [Buy Me a Coffee](https://buymeacoffee.com/develop.itr.jp)

## License

See `LICENSE`.
