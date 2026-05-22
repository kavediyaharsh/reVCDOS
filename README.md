# GTA Vice City — HTML5 Port

# Port made by the DOS.Zone team: [dos.zone/reVCDOS](https://dos.zone/revcdos)
# Official implementation: [GitHub](https://github.com/Carter54git/revcdos) 

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/13GFRIxTwVbixv0Vup9MSVXnB4SLmA3G7?usp=sharing)

> **Fast Start:** Run the server in one click using Google Colab. Click the badge above, run the cell, and use the **"Launch Game"** button. The tunnel password will be copied automatically — just paste it on the page that opens.

Web-based port of GTA: Vice City running in browser via WebAssembly.

## Requirements

- Colab or Docker or Python 3.8+ or PHP 8.0+
- Dependencies from `requirements.txt`

## Quick Start

1.  **Clone the repository**:
    ```bash
    git clone https://github.com/Lolendor/reVCDOS.git
    cd reVCDOS
    ```

2. **Configure Assets** (Optional):

   By default, the project uses **DOS Zone CDN** — no local assets needed. For offline hosting you can use:
   *   **Packed archive** (`--packed` or `--unpacked`) — single `.bin` file with all assets
   *   **Local folders** (`--vcsky_local`, `--vcbr_local`) — unpacked asset directories
   *   **Cache mode** (`--vcsky_cache`, `--vcbr_cache`) — download from CDN once, serve locally after

3. **Launch the Application**:
   Choose one of the setup methods below:
   * **Docker** (Recommended for most users) — fast and isolated.
   * **PHP** — Simply upload the folder to your web server (FTP/Hosting).
   * **Manual Installation** — for development and customization.

## Setup & Running

### Option 1: Using Docker (Recommended)
The easiest way to get started is using Docker Compose:

```bash
PACKED=https://folder.morgen.qzz.io/revcdos.bin docker compose up -d --build
```

To configure server options via environment variables:

```bash
# Set port, enable auth and custom saves
IN_PORT=3000 AUTH_LOGIN=admin AUTH_PASSWORD=secret CUSTOM_SAVES=1 docker compose up -d --build
```

| Environment Variable | Description |
|---------------------|-------------|
| `OUT_HOST` | External host (default: 0.0.0.0) |
| `OUT_PORT` | External port (default: 8000) |
| `IN_PORT` | Internal container port (default: 8000) |
| `AUTH_LOGIN` | HTTP Basic Auth username |
| `AUTH_PASSWORD` | HTTP Basic Auth password |
| `CUSTOM_SAVES` | Enable local saves (set to `1`) |
| `VCSKY_LOCAL` | Serve vcsky from local directory (set to `1`, or path like `/data/vcsky`) |
| `VCBR_LOCAL` | Serve vcbr from local directory (set to `1`, or path like `/data/vcbr`) |
| `VCSKY_URL` | Custom vcsky proxy URL |
| `VCBR_URL` | Custom vcbr proxy URL |
| `VCSKY_CACHE` | Cache vcsky files locally while proxying (set to `1`) |
| `VCBR_CACHE` | Cache vcbr files locally while proxying (set to `1`) |
| `PACKED` | Serve from packed archive (filename or URL, e.g., `revcdos.bin`) |
| `UNPACKED` | Unpack archive to local folders (filename or URL, auto-sets vcsky/vcbr paths) |
| `PACK` | Pack a folder and serve from resulting archive (folder path or MD5 hash) |

### Option 2: Local Installation

1. Install Python dependencies:
```bash
pip install -r requirements.txt
```

2. Start the server:
```bash
python server.py --packed https://folder.morgen.qzz.io/revcdos.bin
```

Server starts at `http://localhost:8000`

### Option 3: Shared Hosting on PHP (No installation)

If you want to run the game from a hosted environment with `PHP 8.0` or above, just copy the contents of this repo to your desired hosting
By default the `index.php` and `.htaccess` will get the job done. 
## Server Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--port` | int | 8000 | Server port |
| `--custom_saves` | flag | disabled | Enable local save files (saves router) |
| `--login` | string | none | HTTP Basic Auth username |
| `--password` | string | none | HTTP Basic Auth password |
| `--vcsky_local` | string/flag | disabled | Serve vcsky from local directory. Use flag for `vcsky/` or specify path |
| `--vcbr_local` | string/flag | disabled | Serve vcbr from local directory. Use flag for `vcbr/` or specify path |
| `--vcsky_url` | string | `https://cdn.dos.zone/vcsky/` | Custom vcsky proxy URL |
| `--vcbr_url` | string | `https://br.cdn.dos.zone/vcsky/` | Custom vcbr proxy URL |
| `--vcsky_cache` | flag | disabled | Cache vcsky files locally while proxying |
| `--vcbr_cache` | flag | disabled | Cache vcbr files locally while proxying |
| `--packed` | string | disabled | Serve from packed archive file. Accepts file path or URL |
| `--unpacked` | string | disabled | Unpack archive to `unpacked/{hash}/` and serve from there. Accepts file path or URL |
| `--pack` | string | disabled | Pack a folder and serve from resulting `{hash}.bin` archive. Accepts folder path or MD5 hash |

**Examples:**
```bash
# Start on custom port
python server.py --port 8000

# Enable local saves
python server.py --custom_saves

# Enable HTTP Basic Authentication
python server.py --login admin --password secret123

# Use local vcsky and vcbr files
python server.py --vcsky_local --vcbr_local

# Cache files locally while proxying (hybrid mode) (recommended)
python server.py --vcsky_cache --vcbr_cache

# Serve from packed archive (local file)
python server.py --packed revcdos.bin

# Serve from packed archive (download from URL if not present)
python server.py --packed https://example.com/revcdos.bin

# Unpack archive and serve from unpacked files
python server.py --unpacked revcdos.bin

# Stream-unpack from URL (downloads and unpacks simultaneously)
python server.py --unpacked https://example.com/revcdos.bin

# Pack a folder and serve from the resulting archive
python server.py --pack /path/to/assets  # Creates {folder_hash}.bin

# Pack from existing unpacked folder by MD5 hash
python server.py --pack a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6  # Uses unpacked/{hash}/
```

> **Note:** HTTP Basic Auth is only enabled when both `--login` and `--password` are provided.

> **Note:** By default, vcsky and vcbr are proxied from DOS Zone CDN. Use `--vcsky_local` and `--vcbr_local` flags to serve files from local directories instead. You can optionally specify a custom path.

> **Note:** Use `--vcsky_cache` and `--vcbr_cache` to cache proxied files locally. Files are downloaded once and served from local storage on subsequent requests.

> **Note:** `--packed` serves files directly from a compressed archive without unpacking (faster and more compressed). `--unpacked` extracts the archive once and serves from local files (if you want edit assets).

> **Note:** When using URL with `--unpacked`, the archive is streamed and unpacked simultaneously during download using `downloader_brotli.py`.

> **Note:** You can pass a raw MD5 hash (32 hex characters) to `--unpacked` to use an existing unpacked folder without needing the original archive. Example: if you have `unpacked/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6/`, you can start the server with `--unpacked a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6`.

> **Note:** `--pack` creates a packed archive from a folder containing subfolders (like `vcsky/` and `vcbr/`). Each subfolder is packed sequentially: the first creates the archive, subsequent ones are appended. After packing, the server automatically uses the created archive via `--packed` mode.

## URL Parameters

| Parameter | Values | Description |
|-----------|--------|-------------|
| `lang` | `en`, `ru` | Game language |
| `cheats` | `1` | Enable cheat menu (F3) |
| `request_original_game` | `1` | Request original game files before play |
| `fullscreen` | `0` | Disable auto-fullscreen |
| `max_fps` | `1-240` | Limit frame rate (e.g., `60` for 60 FPS) |
| `configurable` | `1` | Show configuration UI before play button |


**Examples:**
- `http://localhost:8000/?lang=ru` — Russian version
- `http://localhost:8000/?lang=en&cheats=1` — English + cheats
- `http://localhost:8000/?configurable=1` — Show settings UI before play

## Project Structure

```
├── server.py           # FastAPI proxy server
├── index.php           # PHP proxy server
├── .htaccess           # Apache config for PHP
├── requirements.txt    # Python dependencies
├── revcdos.bin         # Packed archive (optional)
├── additions/          # Server extensions
│   ├── auth.py         # HTTP Basic Auth middleware
│   ├── cache.py        # Proxy caching and brotli decompression
│   ├── packed.py       # Packed archive serving module
│   └── saves.py        # Local saves router
├── utils/              # Utility modules
│   ├── packer_brotli.py # Archive packer with brotli compression
│   └── downloader_brotli.py # Archive packer with brotli compression
├── unpacked/           # Auto-created by --unpacked flag
│   └── {md5_hash}/     # Unpacked files organized by source hash
│       ├── vcsky/      # Decompressed game assets
│       └── vcbr/       # Brotli-compressed binaries
├── dist/               # Game client files
│   ├── index.html      # Main page
│   ├── game.js         # Game loader
│   ├── index.js        # Module loader
│   ├── GamepadEmulator.js  # Touch controls
│   ├── idbfs.js        # IndexedDB filesystem
│   ├── jsdos-cloud-sdk.js  # Cloud saves (DOS Zone)
│   ├── jsdos-cloud-sdk-local.js  # Local saves (--custom_saves)
│   └── modules/        # WASM modules
│       ├── runtime.js      # WASM runtime initialization
│       ├── loader.js       # Asset/package loading
│       ├── fs.js           # Virtual filesystem
│       ├── audio.js        # Audio system
│       ├── graphics.js     # Rendering pipeline
│       ├── events.js       # Input events handling
│       ├── fetch.js        # Network requests (Real-time asset streaming)
│       ├── syscalls.js     # System calls
│       ├── main.js         # Main entry point
│       ├── cheats.js       # Cheat engine (F3)
│       ├── asm_consts/     # Language-specific ASM constants
│       │   ├── en.js
│       │   └── ru.js
│       └── packages/       # Language-specific data packages
│           ├── en.js
│           └── ru.js
├── vcbr/               # Brotli-compressed game data (optional)
│   ├── vc-sky-en-v6.data.br
│   ├── vc-sky-en-v6.wasm.br
│   ├── vc-sky-ru-v6.data.br
│   └── vc-sky-ru-v6.wasm.br
└── vcsky/              # Decompressed assets (optional)
    ├── fetched/        # English version files
    │   ├── data/
    │   ├── audio/
    │   ├── models/
    │   └── anim/
    └── fetched-ru/     # Russian version files
        ├── data/
        ├── audio/
        └── ...
```

## Features

- 🎮 Gamepad emulation for touch devices
- ☁️ Cloud saves via js-dos key
- 💾 Local saves (with `--custom_saves` flag)
- 🌍 English/Russian language support
- 🔧 Built-in cheat engine (memory scanner, cheats)
- 📱 Mobile touch controls

## Local Saves

When local saves are enabled (`--custom_saves` flag), enter any 5-character identifier in the "js-dos key" input field on the start page. This identifier will be used to store your saves in the `saves/` directory on the server.

Example: Enter `mykey` or `12345` — saves will be stored as `mykey_vcsky.saves` or `12345_vcsky.saves`.

## Controls (Touch)

Touch controls appear automatically on mobile devices. Virtual joysticks for movement and camera, context-sensitive action buttons.

## Cheats

Enable with `?cheats=1`, press **F3** to open menu:
- Memory scanner (find/edit values)
- All classic GTA VC cheats
- AirBreak (noclip mode)

## License

MIT. Do what you want (but credit the port authors and me). Not affiliated with Rockstar Games.

---

**Authors:** DOS Zone ([@specialist003](https://github.com/okhmanyuk-ev), [@caiiiycuk](https://www.youtube.com/caiiiycuk), [@SerGen](https://t.me/ser_var))

**Deobfuscated by**: [@Lolendor](https://github.com/Lolendor) [@kavediyaharsh](https://github.com/kavediyaharsh)

**Russian translation:** [GamesVoice](https://www.gamesvoice.ru/)

**Added by the community:**
* PHP Support by [Rohamgames](https://github.com/Rohamgames)

## Support [me](https://github.com/kavediyaharsh)