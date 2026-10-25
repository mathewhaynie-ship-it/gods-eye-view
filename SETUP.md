# Getting started

God's Eye View is a website plus a small local server. The server fetches live flights, cameras, and the other feeds, then the browser draws them on a 3D globe. You can run the whole thing on your own computer with no API keys.

## What to install

Install **Node.js 24.14 or newer on the 24 line, or Node.js 26**.

- Download the **24 LTS** installer from [nodejs.org](https://nodejs.org/).
- On a Mac you can instead use Homebrew: `brew install node@24`.
- Skip Node 22 and older. Skip Node 25 (it is end-of-life).
- After installing, close the terminal and open a new one. `node -v` should start with `v24.` or `v26.`.

You also need Git if you are cloning the project. The Node installer on Windows can install Git's tools, or install Git from [git-scm.com](https://git-scm.com/).

Nothing else is required. API keys are optional upgrades.

## Mac and Linux

In a terminal:

```bash
git clone https://github.com/bilawalsidhu/gods-eye-view.git
cd gods-eye-view
npm ci
npm run doctor
npm run build
npm run dev
```

Then open [http://localhost:4173](http://localhost:4173).

`npm run doctor` checks Node and tells you which optional keys are missing. It should end with "Ready".

`npm run build` creates the production files in `dist/`. `npm run dev` is the everyday server: it reloads when the code changes and it serves the live-data routes.

To serve the production build instead:

```bash
npm run build
npm run preview
```

Open [http://localhost:4173](http://localhost:4173) again. Stop the dev server first if that port is already in use.

Optional Mac shortcut (also reads keys from the Keychain when you have saved some):

```bash
./scripts/dev-fresh.sh
```

## Windows

Use PowerShell or Command Prompt. The commands are the same:

```bat
git clone https://github.com/bilawalsidhu/gods-eye-view.git
cd gods-eye-view
npm ci
npm run doctor
npm run build
npm run dev
```

Open [http://localhost:4173](http://localhost:4173).

Do not run `./scripts/dev-fresh.sh` on Windows. That script is for Mac and Linux.

If `node -v` is not 24 or 26, install the Node 24 LTS installer, close every terminal, open a new one, and try again.

## What you should see

The first screen asks you to choose a view. **Live Contacts** turns on aircraft. **Environmental** turns on earthquakes. **Explore Manually** leaves the globe empty until you switch layers on yourself.

With no keys, the globe uses Esri satellite imagery. If Esri cannot be reached, it switches to OpenStreetMap. Flights, military traffic, satellites, earthquakes, public cameras, radio, and launches still try to load.

## What works with no keys

These do not need an account:

| What you get | Notes |
| --- | --- |
| Satellite globe | Esri imagery, with OpenStreetMap as the backup |
| Live flights | OpenSky, anonymous and rate-limited. If OpenSky is slow, the app asks adsb.lol for aircraft near the view |
| Military flights, satellites, earthquakes | Public feeds |
| Public cameras | Many cities. Ontario's 511 cameras are the exception: they need a free key |
| Radio, transit, bike-share, directions | Public feeds |
| Weather, wind, cyclone tracks | NOAA and ECMWF, no key |
| Launches | Launch Library 2 public access |
| Traffic | A built-in simulation on real roads. A TomTom key adds live speeds |
| Street-level photos | KartaView and Panoramax. Mapillary and Google Street View need keys |

Ships and NASA fire detections stay empty until you add their free keys. Voice control stays off until you add an OpenAI or Gemini key.

## Optional keys

Copy `.env.example` to `.env` only if you want to type keys by hand. You can also run the app and use **POWER UP** (bottom-right). That panel writes `.env` for you. Do not commit `.env`.

No key below is required to start. "Free" means the provider has a signup tier; quotas and eligibility still apply, and you should read their current terms.

| Variable | Required? | What it unlocks | Where to get it | Cost |
| --- | --- | --- | --- | --- |
| `CESIUM_ION_TOKEN` | Optional | Photorealistic 3D cities and world terrain through Cesium ion | [cesium.com/ion](https://cesium.com/ion) — create a token with `assets:read` | Free Community plan for eligible personal, non-commercial use, with quotas |
| `GOOGLE_MAPS_API_KEY` | Optional | Google 3D tiles directly, Google place search, Street View | [Google Cloud Console](https://console.cloud.google.com/). Restrict the key by website | Metered. Billing must be on. Google currently includes a monthly free allowance; set a budget alert |
| `GOOGLE_MAPS_SERVER_API_KEY` | Optional | Same Google account, but only for server-side place and Street View calls | Same console. Restrict this one by server IP | Metered. If you leave it blank, the app uses `GOOGLE_MAPS_API_KEY` |
| `OPENAI_API_KEY` | Optional | Voice control and the short AI summary of the view | [platform.openai.com](https://platform.openai.com) | Metered. The app caps a voice session around $5 |
| `GEMINI_API_KEY` | Optional | Voice control through Gemini instead of OpenAI | [Google AI Studio](https://aistudio.google.com/apikey) | Free tier with quotas, then metered |
| `AISSTREAM_API_KEY` | Optional | Live ships | [aisstream.io](https://aisstream.io) | Free signup |
| `FIRMS_MAP_KEY` | Optional | NASA active-fire detections | [FIRMS map key](https://firms.modaps.eosdis.nasa.gov/api/map_key/) | Free |
| `TOMTOM_API_KEY` | Optional | Live traffic speeds. The simulated traffic layer already works | [my.tomtom.com/keys](https://my.tomtom.com/keys) | Free tier (monthly tile allowance, no card required) |
| `MAPILLARY_CLIENT_TOKEN` | Optional | Mapillary street-level photos | [mapillary.com/dashboard/developers](https://www.mapillary.com/dashboard/developers) — register an app and copy the client token | Free |
| `OPENSKY_CLIENT_ID` and `OPENSKY_CLIENT_SECRET` | Optional | More flight-polling room. Anonymous already works | [opensky-network.org](https://opensky-network.org) account dashboard | Free account |
| `OPENSKY_USERNAME` and `OPENSKY_PASSWORD` | Optional | Only if you set `OPENSKY_AUTH_MODE` to `basic` or `auto` | Same OpenSky account | Free account |
| `ONTARIO_511_API_KEY` | Optional | Ontario highway cameras | Sign up at [511on.ca/my511/register](https://511on.ca/my511/register), then request a key at [511on.ca/developers/doc](https://511on.ca/developers/doc) | Free developer key |
| `TFL_APP_KEY` | Optional | London cameras already load without it. The key only raises the list rate limit | [TfL API portal](https://api-portal.tfl.gov.uk/) | Free |
| `LL2_API_TOKEN` | Optional | Higher launch-feed limits. Leave blank for normal public access | [thespacedevs.com/supportus](https://thespacedevs.com/supportus) | Paid Patreon token. There is no free API key |

Three of those keys are visible in the browser on purpose: `GOOGLE_MAPS_API_KEY`, `CESIUM_ION_TOKEN`, and `MAPILLARY_CLIENT_TOKEN`. Restrict them at the provider. The others stay on the local server.

`.env.example` also lists tuning knobs (ports, camera caps, rate limits). Leave those commented unless you mean to change them.

Useful ones:

| Variable | What it does |
| --- | --- |
| `PORT` | Defaults to `4173` |
| `HOST` | Defaults to this computer only. `0.0.0.0` opens it to your local network |
| `GEV_ALLOWED_HOSTS` | Extra hostnames, such as `globe.lan`, when you are not using localhost |
| `OPENSKY_AUTH_MODE` | `oauth` (default), `anon`, `basic`, or `auto`. Blank OAuth credentials still mean anonymous flights |

## Putting it on the internet

This is not a static site. Vercel, Netlify, and Cloudflare Pages can host plain HTML, but they will not run this app's data server, so the globe would come up without the live feeds. Do not deploy it there as a static site.

The app needs a computer that stays on and can run **Node.js 24 or 26** (or Docker).

Simplest public setup:

1. Rent a small Linux server (a low-cost VPS is enough) or use a home computer that stays on.
2. Install Docker, clone this project, and start it:

   ```bash
   git clone https://github.com/bilawalsidhu/gods-eye-view.git
   cd gods-eye-view
   docker compose up -d
   ```

3. Put HTTPS in front of it (Caddy or Nginx are the usual choices) and point a domain at that machine.
4. If strangers can open it, do not leave your API keys in `.env` unless you accept that they share your quota. For a shared copy, set `GEV_KEY_SETUP_TRUSTED_PEERS=none` so the key-entry panel does not trust the network gateway. Details are in [SECURITY.md](SECURITY.md).

`npm run preview` is only for checking a build on your own machine. It is not a hardened public server. For a machine you leave running, prefer Docker.

Anyone who can reach the server can use whatever keys that server has. Keep the default bind (`localhost`) until you mean to share it.
