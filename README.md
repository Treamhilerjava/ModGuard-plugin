# 🛡️ ModGuard + Sentinel — Client Verification System

A **server plugin + client mod security system** designed to help Minecraft servers verify legitimate clients and detect suspicious modifications.

Instead of relying on weak client-brand checks, ModGuard uses a **secure encrypted handshake** between the Sentinel client mod and the ModGuard server plugin to validate players on join.

> 🔗 **More projects by Treamhiler:** [modrinth.com/user/Treamhiler](https://modrinth.com/user/Treamhiler)
> 💬 **Support & Downloads:** [discord.gg/tFXhkPVpxG](https://discord.gg/tFXhkPVpxG)
> 🐙 **GitHub:** [github.com/Treamhilerjava](https://github.com/Treamhilerjava)

---

## 📦 Components

### 🔌 ModGuard — Server Plugin
> Runs on Paper / Spigot servers

- Sends an encrypted challenge to connecting players
- Verifies the client response and checks for banned mods
- Supports configurable kick messages, whitelist, and blacklist
- Works on both online-mode and cracked (offline-mode) servers
- Bedrock players (via Geyser/Floodgate) are automatically bypassed

### 🧩 Sentinel — Client Mod
> Runs on the player's Fabric client

- Receives the server challenge and responds with an encrypted mod list
- Uses AES-256-GCM + RSA-2048 encryption
- Zero configuration needed — works automatically on join
- Zero performance impact during gameplay

### 🐍 Blacklist Builder — Python Script
> Utility to generate ModGuard blacklists from hack client jars

- Scans a folder of Fabric hack client `.jar` files
- Extracts mod IDs from `fabric.mod.json` inside each jar
- Outputs a ready-to-use `modblacklist.json` for ModGuard

---

## ⚙️ How It Works

```
Player joins
    → Server sends encrypted challenge (RSA-2048 public key + nonce + HMAC secret)
    → Sentinel collects mod list via FabricLoader
    → Sentinel encrypts response (AES-256-GCM, key wrapped with RSA)
    → Sentinel sends encrypted response back to server
    → Server decrypts, verifies HMAC, checks blacklist
    → Player approved or kicked
```

---

## 🚀 Installation

### Server (ModGuard)
1. Download `ModGuard.jar`
2. Drop it into your server's `plugins/` folder
3. Start the server — `plugins/ModGuard/config.yml` is generated automatically

### Client (Sentinel)
1. Download the correct `Sentinel-<version>.jar` for your Minecraft version
2. Drop it into your `.minecraft/mods/` folder
3. Launch the game — no configuration needed

---

## 📋 Supported Versions

| Component | Versions |
|---|---|
| ModGuard (Plugin) | Paper / Spigot 1.21.x |
| Sentinel (Client Mod) | Fabric 1.21 — 1.21.11 |

---

## 📦 Mod Loader Compatibility

| Loader | Requires Sentinel |
|---|---|
| Fabric | ✅ Yes |
| Quilt | ✅ Yes (not officially tested) |
| Vanilla | ❌ No — automatically handled |
| Bedrock (Geyser) | ❌ No — automatically bypassed |
| Forge | ❌ Not supported yet |
| NeoForge | ❌ Not supported yet |
| OptiFine | ❌ Not supported (runs on Forge) |

> **Mobile launchers (PojavLauncher, Zalith, Fold Craft, etc.):**
> Running Fabric → needs Sentinel | Running Vanilla → no Sentinel needed

---

## 🔧 ModGuard Configuration

```yaml
challenge-timeout-seconds: 15
challenge-delay-ticks: 40
allow-floodgate: true
show-mod-list-in-console: true
highlight-banned-mods: true
enable-mod-count-limit: false
max-mod-count: 50

protocol-version: 1
min-sentinel-version: "1.0.0"
offline-mode: false

kick-messages:
  missing-sentinel:   "&cSentinel mod is required to join this server."
  banned-mod:         "&cBanned mod(s) installed: &e{mods} &cremove them to join."
  timeout:            "&cVerification timed out. Please install the Sentinel mod."
  mod-count-exceeded: "&cToo many mods installed. Maximum allowed: &e{max}"
  outdated-sentinel:  "&cYour Sentinel mod is outdated. Please update to &ev{version}&c."
  protocol-mismatch:  "&cSentinel version mismatch. Please update your Sentinel mod."
```

---

## 💻 Commands

Permission node: `modguard.admin`

| Command | Description |
|---|---|
| `/modguard blacklist <add\|remove\|list> [modId]` | Manage the mod blacklist |
| `/modguard whitelist <add\|remove\|list> [player]` | Manage the player whitelist |
| `/modguard mods <player>` | View a player's mod list |
| `/modguard check <player>` | Manually re-verify a player |
| `/modguard version` | Show version and protocol info |
| `/modguard status` | Show current verification status |
| `/modguard reload` | Reload configuration |

---

## 🔒 Security

- **RSA-2048** keypair generated fresh per player session — never written to disk
- **AES-256-GCM** encrypts the mod list payload — authenticated and tamper-proof
- **HMAC-SHA256** signs the payload — server secret never sent to client
- **Nonce** prevents replay attacks
- **Session ID** binds each challenge to its response
- Protocol and Sentinel version are verified inside the encrypted payload — cannot be spoofed

---

## 🐍 Blacklist Builder

```
blacklistbuilder/
    blacklist_builder.py
    hack_clients/         ← drop hack client jars here
    output/
        modblacklist.json ← generated output
```

```bash
python blacklist_builder.py
```

No pip dependencies — Python 3.x stdlib only.

---

## 📁 Project Structure

```
ModGuard/               ← Server plugin (Maven / Paper)
Sentinel/               ← Client mod (Gradle / Fabric)
blacklistbuilder/       ← Utility script (Python)
```

---

## 🙏 Credits

Developed by **Treamhiler**
[modrinth.com/user/Treamhiler](https://modrinth.com/user/Treamhiler) • [discord.gg/tFXhkPVpxG](https://discord.gg/tFXhkPVpxG) • [github.com/Treamhilerjava](https://github.com/Treamhilerjava)
