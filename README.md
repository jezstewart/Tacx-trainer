# Tacx Route Rider

A single-page web app for riding routes on a Tacx smart trainer from an Android tablet or phone. It reads power, cadence and speed over Bluetooth, and sets the trainer's resistance from the gradient of the route you are riding.

There is nothing to install and no build step. The whole app is one file, `index.html`.

## What it does

- Connects to a Tacx trainer over Bluetooth and shows live power, cadence and speed
- Sets the road gradient (simulation mode) or a target power (ERG mode)
- Loads routes from GPX files, with an elevation profile and a topographic map
- Looks up elevation automatically for GPX files that have none
- Lets you plan a new route by tapping on a map, following roads and paths that suit bikes
- Moves you along the route at the speed the trainer reports, and sends the gradient ahead of you to the trainer, eased so it never jumps
- Keeps a record for each rider: a gradient scale, rider weight and bike weight, plus a history of rides
- Exports rides as FIT files (virtual rides) and as CSV, and backs up all records
- Has a full-screen ride display with large numbers over the map and elevation graph
- Has an optional 3D view with real terrain, 3D buildings, roads, relief shading, vineyards and orchards, and a sky, seen from a camera that follows you along the route

## What you need

| Item | Notes |
| --- | --- |
| **Browser** | Chrome on Android. Web Bluetooth is not available in Firefox or Safari. |
| **Hosting over HTTPS** | Web Bluetooth only works on secure pages. GitHub Pages works. A plain `http://` address on your home network does not. |
| **Trainer** | Developed and tested with a Tacx Flux. It uses the Tacx FE-C over Bluetooth service (`6e40fec1-b5a3-f393-e0a9-e50e24dcca9e`). Other Tacx trainers that offer this service may work. Trainers that only offer the standard FTMS service are not supported. |
| **Internet** | Needed for map tiles, route planning, elevation lookup and the 3D view. Trainer control and the elevation profile of an already loaded route do not need it. |
| **WebGL** | Needed only for the 3D view. |

## Setting it up

1. Create a public repository on GitHub and upload `index.html` (the file must have this exact name).
2. In the repository, open **Settings > Pages**, set the source to **Deploy from a branch**, choose the `main` branch and the `/ (root)` folder, and save.
3. After a minute or two, GitHub shows the address of your page, such as `https://yourusername.github.io/your-repo/`.
4. Open that address in Chrome on your Android device.

To see a new version after you update the file, add something like `?v=2` to the end of the address, or clear only the cached files in Chrome. Do not clear site data, because that also deletes your riders and rides.

## Using it

**First ride**

1. Close Zwift or any other app that is connected to the trainer.
2. Wake the trainer by pedalling, then press **Connect to trainer** and pick it in the pop-up.
3. Add a rider with **New rider**, then set the rider's gradient scale and weights.
4. Choose a route (see below), press **Start ride**, and pedal.
5. Press **Finish & save** when you are done, or let it save itself at the end of the route.

**Routes**

- **Demo route:** built in, with a few climbs. It has no map coordinates, so the map and 3D view are not available for it.
- **Load GPX file:** pick a GPX file from your device. If it has no elevation, the page looks it up online.
- **Plan a route:** open the section, search for a place, and tap the map to add points. Leave **Follow roads** ticked to snap to bike-friendly roads. Press **Ride this route** to ride it, or **Download GPX** to keep it.

**Rider settings**

- **Gradient scale:** a percentage applied to every gradient before it goes to the trainer. At 75%, a 10% climb is sent as 7.5%.
- **Rider and bike weight:** sent to the trainer when a ride starts, so that the virtual speed matches the rider.

**Ride screen**

Press **Ride screen** for a full-screen display. It shows power, cadence, gradient and speed over the map, and distance, distance to go and time over the elevation graph. Start/Pause, Finish & save and Exit are on screen. Press **3D** to switch the map to the 3D view and **2D** to switch back. In 3D, the **Topo** button switches to a lighter topographic map without buildings, and **Detailed** switches back.

**Ride history**

Each saved ride shows in a table, with a **FIT** button to download it and a **Delete** button. **Export rides (CSV)** saves the table for the selected rider. **Back up all riders** and **Restore backup** save and reload every rider's summary records.

## Where your data is kept

Everything is stored in the browser on the device you use (local storage). Nothing is sent to a server of mine, and there is no account.

- Clearing Chrome's site data deletes riders and rides. Use **Back up all riders** now and then.
- The backup file holds ride summaries. The per-second data used for FIT files is stored separately and is not in the backup, so download the FIT files of rides you want to keep.
- Per-second ride data uses roughly 150 KB per hour of riding.

## Online services used

The page calls these free services from your browser. They are meant for light, personal use.

| Purpose | Service |
| --- | --- |
| Topographic map tiles | OpenTopoMap |
| Street map tiles | OpenStreetMap |
| Elevation lookup | Open-Meteo Elevation API (Copernicus DEM, about 90 m resolution) |
| Bike routing when planning | OpenStreetMap bike router at routing.openstreetmap.de |
| Place search | Nominatim (OpenStreetMap) |
| 3D map, buildings and roads | OpenFreeMap (Liberty style) |
| 3D terrain | AWS terrain tiles (Tilezen) |
| Map libraries | Leaflet (cdnjs) and MapLibre GL JS (jsDelivr) |

Elevation from a terrain model smooths out small bumps, bridges and tunnels. A file recorded with a GPS device will generally be more accurate than a looked-up one.

## How it talks to the trainer

The page uses the Tacx FE-C over Bluetooth service.

- **Reads:** data page `0x10` (speed) and `0x19` (cadence and power).
- **Writes:** page `0x33` (track resistance, the gradient), `0x31` (target power) and `0x37` (rider and bike weight).

## Known issues and to do

- Very large GPX files, such as one of about 7.6 MB, still fail with a "maximum call stack size exceeded" error. A first fix was not enough.
- FIT export has been checked with a standard FIT reader, but I have not yet confirmed an upload to Strava or Garmin Connect.
- The 3D view has not yet been tried on a trainer ride. It needs WebGL, which some browsers, including some on a Raspberry Pi, do not provide.
- Planned: an appearance tab to change the size, colour and opacity of the ride screen numbers, camera settings for the 3D view, and more scenery such as trees.
- Planned: a ride-along video mode that plays a filmed route in step with your speed.

## Credits and licences

- Map data is by [OpenStreetMap contributors](https://www.openstreetmap.org/copyright). OpenTopoMap is licensed CC-BY-SA.
- Elevation data is Copernicus DEM, served by [Open-Meteo.com](https://open-meteo.com/).
- The 3D map uses [OpenFreeMap](https://openfreemap.org/) vector tiles, built from OpenStreetMap data, and [Tilezen](https://github.com/tilezen/joerd/blob/master/docs/attribution.md) elevation tiles.
- Maps are drawn with [Leaflet](https://leafletjs.com/) and [MapLibre GL JS](https://maplibre.org/).

This project does not yet have a licence. Add a `LICENSE` file if you intend to share or reuse it.
