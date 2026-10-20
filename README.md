# Serial Data Logging App

A single-page browser app that reads a sensor over USB serial and logs it to a CSV file. There's nothing to install: everything runs in the page.

**Open it:** https://singaporetech-zhou-kang.github.io/serial-data-logger/ (Chrome or Edge on a computer)

## What it does
- **Connect** to a USB serial device from the browser (Web Serial).
- **Reading:** pick the variable of interest from the incoming lines and watch its latest value.
- **Trend:** live plot.
- **Analysis:** load a CSV and plot it, with summary statistics, a linear fit or a moving average.
- **Record:** stream rows straight to a `.csv` on your disk. The file is written every 2 s, so an interrupted run is still saved.
- **Settings:** device parameters, sent as `Set, <name>, <value>` lines; values the device reports back are shown next to them.
- **Console:** raw serial lines and quick commands.

## Requirements
- Chrome or Edge on Windows, macOS, Linux or ChromeOS. Firefox and Safari don't support Web Serial.
- To use it offline, download `index.html` and serve its folder locally (`python3 -m http.server 8000`, then open http://localhost:8000/). Writing to disk doesn't work from a `file://` page.

## Licence
MIT © 2026 Singapore Institute of Technology. See [LICENSE](LICENSE).
