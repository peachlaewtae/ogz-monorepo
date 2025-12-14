## Project Overview

Open GunZ (OGZ) is the open-source client-server codebase for GunZ: The Duel, a multiplayer online action game. C++14 codebase supporting Windows (primary) and Linux (partial).

## Build Commands

### Windows (VS2022/VS2019)

```bash
build-win32-VS2022.bat    # or build-win32-VS2019.bat
```

Prerequisites: Visual Studio 2022 with "Desktop development with C++", C++ MFC tools, GCC x64, CMake 3.7+, OpenSSL in `C:\OpenSSL-Win32`, zlib in `C:\Program Files (x86)\zlib`

Output: `build/win32/` and `install/`

### Linux

```bash
./build-linux.sh
```

Prerequisites: `cmake zlib1g-dev build-essential libsodium-dev libssl-dev libsqlite3-dev libasio-dev libcurl4-openssl-dev libsystemd-dev`

Output: `install/`

## Architecture

### Core Components (src/)

| Directory        | Purpose                                                                    |
| ---------------- | -------------------------------------------------------------------------- |
| **Gunz/**        | Game client (~436 files) - player-facing application                       |
| **MatchServer/** | Game server (~194 files) - handles matchmaking, game state                 |
| **CSCommon/**    | Client-Server Common - shared networking, commands, game logic             |
| **RealSpace2/**  | 3D graphics engine (D3D9/Vulkan abstraction)                               |
| **Mint2/**       | GUI/UI framework (Windows-only)                                            |
| **RealSound/**   | Audio system (Windows-only)                                                |
| **SafeUDP/**     | UDP networking with reliability layer                                      |
| **cml/**         | Core Math Library                                                          |
| **sdk/**         | Third-party libraries (zlib, curl, libsodium, sqlite, bullet, imgui, etc.) |

### Naming Conventions

- **Z\*** prefix: Client-side classes (ZGame, ZCharacter, ZWorld)
- **M\*** prefix: Common/Match classes (MCommand, MMatchClient, MPacket)
- **R\*** prefix: Rendering/RealSpace classes (RMesh, RAnimation, RBspObject)

### Key Systems

**Networking**: MCommand-based message system. Client uses `MMatchClient`, server uses `MBMatchServer`. Packets encrypted via `MPacketCrypter` (libsodium).

**Graphics Pipeline**: RealSpace2 engine with `RMesh` (models), `RAnimation` (skeletal animation), `RBspObject` (BSP collision/rendering), `RLightList` (lighting).

**Game Logic**: `ZGame` manages client game instance. `ZCharacter`/`ZMyCharacter` for player entities. `MMatchItem` for inventory. Quest system via `MQuestMap`/`MQuestItem`/`MQuestNPC`.

### Client-Server Communication

Both client and server share CSCommon for:

- Command definitions (`MMatchUtil`, `MCommand`)
- Packet serialization (`MPacket`)
- Game type definitions (`MMatchGameType`)
- Global constants (`MMatchGlobal`)

## Testing

No formal test framework. Manual testing against local server:

1. Build client and server
2. Start MatchServer.exe
3. Run GunZ.exe with config pointing to 127.0.0.1

## Platform Considerations

- Windows-only: Mint2 GUI, RealSound audio, Bullet physics, SQLite
- Cross-platform: Core networking, game logic, server
- Conditional compilation via `#ifdef WIN32` / `#ifdef UNIX`
- Unity builds enabled (500 CPP files per unity file) for faster compilation
