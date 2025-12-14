# Open GunZ (OGZ)

This is the Open GunZ (GunZ The Duel game) source repo. It was forked from the OGZ and (https://github.com/open-gunz/ogz-source)Refined GunZ source (https://github.com/Asunaya/RefinedGunz) and updated by the International GunZ (http://igunz.net) private server developers.
Open-source client-server implementation of GunZ: The Duel.

## Repository Structure

Open GunZ (OGZ) is a GunZ: The Duel client-server implementation split into three directories:

| Directory       | Contents                                                                           |
| --------------- | ---------------------------------------------------------------------------------- |
| **ogz-source/** | C++14 source code - see `ogz-source/CLAUDE.md` for build commands and architecture |
| **ogz-server/** | Server binaries (MatchServer) and XML configuration files                          |
| **ogz-client/** | Client assets: maps, models, sounds, interface resources                           |

## Platform Support

| Component       | Windows       | Linux/Docker    | macOS          |
| --------------- | ------------- | --------------- | -------------- |
| **MatchServer** | Native (.exe) | Native / Docker | Docker or Wine |
| **GunZ Client** | Native        | Wine            | Wine/CrossOver |

## Quick Start (Docker)

```bash
# Build MatchServer
cd ogz-source && ./build-docker.sh

# Deploy and run
cp install/MatchServer ../ogz-server/
cd ../ogz-server && docker compose up -d
```

## Building MatchServer

### Option A: Docker Build (Recommended for macOS/Linux)

**Prerequisites:** Docker Desktop

```bash
cd ogz-source
./build-docker.sh
```

Output: `install/MatchServer`

The build uses Ubuntu 22.04 (linux/amd64) with all dependencies pre-installed.

### Option B: Native Linux Build

**Prerequisites:**

```bash
sudo apt-get install -y \
    build-essential cmake pkg-config \
    libsodium-dev libssl-dev libsqlite3-dev \
    libasio-dev libcurl4-openssl-dev libsystemd-dev zlib1g-dev
```

**Build:**

```bash
cd ogz-source
./build-linux.sh
```

### Option C: Windows Build

**Prerequisites:**

- Visual Studio 2022 with "Desktop development with C++"
- OpenSSL in `C:\OpenSSL-Win32`
- zlib in `C:\Program Files (x86)\zlib`

**Build:**

```bash
cd ogz-source
build-win32-VS2022.bat
```

## Building Gunz.exe (Client)

The Gunz client requires Windows to build. For macOS/Linux developers, use GitHub Actions.

### Option A: GitHub Actions (Recommended for macOS/Linux)

Build automatically in the cloud - no local setup required.

#### How it works

```
Push code → GitHub Actions builds → Download Gunz.exe artifact
```

#### Trigger a build

1. Go to GitHub repo → Actions tab
2. Select "Build Gunz Client"
3. Click "Run workflow"
4. Select branch → Click "Run workflow"

#### Download the build

1. Go to Actions tab
2. Click on completed workflow run
3. Download "Gunz-Client-Windows" artifact
4. Extract zip → Get `Gunz.exe`

#### Run on macOS/Linux

```bash
# Using Wine
wine Gunz.exe

# Or using CrossOver
open -a CrossOver Gunz.exe
```

#### Notes

- Build time: ~5-10 minutes
- Artifacts retained for 30 days
- Private repo: ~100 builds/month (free tier)

### Option B: Native Windows

If you have a Windows machine:

**Prerequisites:**

- Visual Studio 2022 with "Desktop development with C++"
- OpenSSL in `C:\OpenSSL-Win32`
- zlib in `C:\Program Files (x86)\zlib`

```cmd
cd ogz-source
build-win32-VS2022.bat
```

See `ogz-source/README.md` for detailed prerequisites.

---

## Running the Server (Docker)

### 1. Copy the binary

```bash
cp ogz-source/install/MatchServer ogz-server/
```

### 2. Start the server

```bash
cd ogz-server
docker compose build
docker compose up -d
```

### Useful commands

```bash
docker compose logs -f      # View logs
docker compose down         # Stop server
docker compose restart      # Restart server
```

### Docker configuration

The server runs on Ubuntu 22.04 with:

- `network_mode: host` for optimal game server performance
- Config files mounted as volumes (editable without rebuild)
- Port 6000 (TCP/UDP)

## Configuration Files (ogz-server/)

| File              | Purpose                              |
| ----------------- | ------------------------------------ |
| `server.ini`      | Server IPs, ports, database settings |
| `zitem.xml`       | Item definitions                     |
| `channel.xml`     | Server channels                      |
| `channelrule.xml` | Channel rules                        |
| `shop.xml`        | Store inventory                      |
| `npc.xml`         | NPC definitions                      |
| `questmap.xml`    | Quest maps                           |
| `scenario.xml`    | Quest scenarios                      |

### Local development (server.ini)

For local testing, set these IPs to `127.0.0.1`:

- `FREELOGINIP`
- `KEEPERIP`
- `DBAgentIP`

## Client Setup

### Windows

Run `GunZ.exe` from `ogz-client/`

### macOS (Wine)

#### 1. Install Wine via Homebrew

```bash
# Install Homebrew if not already installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Wine
brew install wine-stable

# Or for Apple Silicon (M1/M2/M3) with better compatibility
brew install --cask wine-stable
```

#### 2. Create 32-bit Wine Prefix

GunZ requires a 32-bit Windows environment:

```bash
# Create a 32-bit Wine prefix
WINEARCH=win32 WINEPREFIX=~/.wine32 winecfg
```

This opens Wine configuration. Set Windows version to **Windows 7** or **Windows XP** for best compatibility, then close.

#### 3. Install Required Dependencies

```bash
# Install DirectX and Visual C++ runtime
WINEPREFIX=~/.wine32 winetricks directx9 vcrun2008 vcrun2010
```

If `winetricks` is not installed:

```bash
brew install winetricks
```

#### 4. Run GunZ

**Basic run:**

```bash
cd /path/to/ogz-client
WINEPREFIX=~/.wine32 wine Gunz.exe
```

**Windowed mode with custom resolution (recommended):**

```bash
WINEPREFIX=~/.wine32 wine explorer /desktop=GunZ,1024x768 Gunz.exe
```

**Full command from any directory:**

```bash
WINEPREFIX=~/.wine32 wine explorer /desktop=GunZ,1024x768 /full/path/to/ogz-client/Gunz.exe
```

#### 5. Create Launch Script (Optional)

Create `run-gunz.sh`:

```bash
#!/bin/bash
cd /path/to/ogz-client
WINEPREFIX=~/.wine32 wine explorer /desktop=GunZ,1024x768 Gunz.exe
```

Make executable:

```bash
chmod +x run-gunz.sh
./run-gunz.sh
```

#### Troubleshooting (macOS)

| Issue                  | Solution                                                    |
| ---------------------- | ----------------------------------------------------------- |
| Black screen           | Try different Windows version in `winecfg`                  |
| No sound               | Run `WINEPREFIX=~/.wine32 winetricks sound=alsa`            |
| Graphics glitches      | Disable DXVK: `WINEPREFIX=~/.wine32 winetricks renderer=gl` |
| "Cannot access server" | Check server IP in config.xml and Locator.ini               |

### Linux (Wine)

```bash
# Ubuntu/Debian
sudo apt install wine winetricks

# Create 32-bit prefix
WINEARCH=win32 WINEPREFIX=~/.wine32 winecfg

# Install dependencies
WINEPREFIX=~/.wine32 winetricks directx9 vcrun2008

# Run GunZ
cd /path/to/ogz-client
WINEPREFIX=~/.wine32 wine Gunz.exe
```

### Client Configuration

Create/edit `~/Documents/Open GunZ/config.xml`:

```xml
<XML>
    <SERVER>
        <IP>127.0.0.1</IP>
        <PORT>6000</PORT>
    </SERVER>
</XML>
```

**Note:** On macOS with Wine, this file is located at:

```
~/.wine32/drive_c/users/YOUR_USERNAME/Documents/Open GunZ/config.xml
```

## Documentation

See `ogz-source/CLAUDE.md` for detailed architecture and development information.
