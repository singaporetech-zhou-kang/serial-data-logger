# Serial Data Logging App

A single-page browser app that reads a sensor over USB serial and logs it to a CSV file. There's nothing to install: everything runs in the page.

**Open it:** https://tools.zhou-kang-group.org/serial-data-logger/

## What it does
- **Connect** to a USB serial device from the browser.
- **Reading:** pick the variable of interest from the incoming lines and watch its latest value.
- **Trend:** live plot.
- **Analysis:** load a CSV and plot it, with summary statistics, a linear fit or a moving average.
- **Record:** stream rows to a `.csv`. The file is written every 2 s, so an interrupted run is still saved.
- **Settings:** the device profile, and device parameters sent as `Set, <name>, <value>`; values the device reports back are shown next to them.
- **Console:** raw serial lines and quick commands.

## Device profiles
The lab spectrophotometer is an **AS7341** 11-channel spectral sensor. Its firmware prints one line per channel (`405-425nm: 1234` … `670-690nm: …`, `Clear: …`, `NIR: …`).

Everything specific to an instrument is in one block at the top of the script in `index.html` (`PROFILES`). The rest of the page is a general serial logger. Students choose a profile in **Settings › What are you measuring?** (the page remembers it). A link can also pick one with `?device=<id>`:

| Profile | `?device=` | Channel logged on connect | Notes |
|---|---|---|---|
| **AS7341 · OD600** (default) | `od600` | `580-600nm` | Cell growth. The AS7341 has no 600 nm channel; 580–600 nm (F6) is the closest. |
| AS7341 · DNS assay | `dns` (old links: `spectro`) | `505-525nm` | Reducing sugars at 515 nm (Lab 1). |
| Any serial device | `generic` | none (you pick) | No device parameters. |

Both AS7341 profiles have a **Gain** parameter (0.5x–512x, default 16x), sent as `Set, Gain, <value>` and shown under the reading. To support another instrument, add a profile with its channel label, parameters, label examples and baud rate.

The app also has a **Read me** tab for students: connecting, blank first, saving labelled points, A = log10(I0/I), recording, what each tab is for, and what to do when something goes wrong.

## Computers: Windows, macOS, Ubuntu
- Use **Chrome** or **Edge**. Firefox and Safari don't support Web Serial.
- **Ubuntu:** use Google Chrome from google.com. Snap Chromium needs `sudo snap connect chromium:raw-usb` first.
- **Ubuntu, if the port won't open**, it's usually permissions. Add a udev rule once (shown here for ESP32 boards, USB vendor `303a`; change the vendor for other boards):
  ```bash
  echo 'ATTRS{idVendor}=="303a", ENV{ID_MM_DEVICE_IGNORE}="1", TAG+="uaccess"' | sudo tee /etc/udev/rules.d/70-kz-serial.rules
  sudo udevadm control --reload-rules && sudo udevadm trigger
  ```
  `uaccess` gives the logged-in user the port straight away, and `ID_MM_DEVICE_IGNORE` stops ModemManager from probing it. The file name must sort before `73-seat-late.rules`. Alternatively, run `sudo usermod -aG dialout $USER`, then log out and in.
- **Ubuntu 22.04+ with a CH340 board:** `sudo apt remove brltty`, which otherwise grabs the port.
- Recording writes straight to a file you choose. That doesn't work from a `file://` page: use the hosted address, or serve the folder with `python3 -m http.server 8000` and open http://localhost:8000/.

## Android
- Use **Chrome**, and plug the device in with a USB-C OTG cable or adapter. Allow the USB permission prompt.
- Works with devices that use **native USB serial (USB CDC)**: ESP32-S3/C3/C6, RP2040, Arduino Leonardo/Micro, SAMD, STM32 CDC and similar. Boards with a **CH340, CP210x or FTDI** USB-serial chip need a computer. The page reaches USB through a small WebUSB driver, ported from Pod Web OS, because Chrome's Web Serial on Android may only reach Bluetooth.
- Android has no file picker, so **recording happens in the browser**: rows are kept in the site's private storage, and the `.csv` downloads when you stop. A copy stays under **Settings › Recordings kept in this browser** (Download / Delete), so a crash or a closed tab loses nothing. The screen is kept on while recording.
- Saved data points stay in the page; use Download to save them.
- To try the Android recording path on a computer, add `?opfs` to the address.

## Licence
MIT © 2026 Singapore Institute of Technology. See [LICENSE](LICENSE).
