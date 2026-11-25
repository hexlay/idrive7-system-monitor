# iDrive 7 System Monitor

An activity monitor for BMW iDrive 7 head units (MGU): what uses the head unit's memory and processor. It is installed through the [MGU Store](https://github.com/robertnisipeanu/mgu-store) and runs on the app runtime and GPU renderer from [cauliflowermossgrape/idrive7-apps](https://github.com/cauliflowermossgrape/idrive7-apps).

| App | Version | Internet | Reads from the car |
| :--- | :---: | :---: | :--- |
| [System Monitor](#system-monitor) | 1.0.9 | No | Nothing, only the head unit's own processes |

## Install

1. In the car, open **MGU Store > Sources > Add source** and enter `hexlay/idrive7-system-monitor`.
2. Add `cauliflowermossgrape/idrive7-apps` as a source too, and install the renderer (`idrive7-renderd`) and the controller gate (`mgu-inputgate`) from it. Most cars that run these apps already have them.
3. Install System Monitor. New versions show up in the store.

## System Monitor

![System Monitor](docs/screenshots/sys_monitor.png)

Shows what uses the head unit's memory and processor, like a computer's activity monitor.

- Overview, per-core processor use and temperature, and every app and program with its memory and how much it grew in the last 3 minutes, which makes leaks easy to spot. Tap any column heading to sort by it, again to reverse; tap a row for its details, with a chart of its memory over those 3 minutes.
- Apps from the MGU Store are shown by name, BMW's own apps by their user id.
- It only reads `/proc` and `/sys`. It can't stop or change anything, and needs no car signals or internet.
- Foreground only: nothing is read or drawn while the app is off screen, and leaving it lets go of everything it gathered. The head unit ends the idle process itself (an app may not quit on its own: the head unit treats that as a crash).

| Processor | Processes |
| :---: | :---: |
| ![System Monitor Processor](docs/screenshots/sys_monitor_processor.png) | ![System Monitor Processes](docs/screenshots/sys_monitor_processes.png) |

The screenshots show the sample data it uses in the emulator.

### No temperature tile?

The temperature comes from the kernel's sensors (`/sys/class/thermal` and `/sys/class/hwmon`). When the head unit doesn't let an app read any of them, the tile is left out, and the app's log (`sys_monitor.log` in its data folder) lists every sensor it found. The log only holds problems like this one; when everything works it stays empty.

## Development

- `python3 kit/tools/build.py sys_monitor` builds `dist/sys_monitor@<version>.mgu`. It needs `oap-sdk`, extracted from your own head unit with `kit/tools/extract_sdk.js`.
- `node kit/tools/emu.js sys_monitor` runs the app in the browser emulator, with sample data on a computer without Linux `/proc`.
- The tests are in `kit/tools/test_*.js` and run in CI on Node 8.9.4 and 8.16.2, the versions on the head units.
- `node kit/tools/test_sys_monitor_layout.js [dir]` draws every page from the sample data and fails when a text leaves the page or its panel or overlaps another; with a folder, it also saves each page there as a PNG.

For the platform itself (signals, renderer, controller input, deploying over SSH) see [idrive7-apps](https://github.com/cauliflowermossgrape/idrive7-apps).

## Credits and license

Built on [cauliflowermossgrape/idrive7-apps](https://github.com/cauliflowermossgrape/idrive7-apps) (MIT): the runtime and renderer. The [MGU Store](https://github.com/robertnisipeanu/mgu-store) is by Robert Nisipeanu.

Released under GPL-3.0-or-later ([`LICENSE`](LICENSE)). The MIT notice of idrive7-apps is kept in [`LICENSE.MIT`](LICENSE.MIT) and ships with every app package. `kit/renderd` keeps its own licence files.

Not affiliated with or endorsed by BMW AG.
