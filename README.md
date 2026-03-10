<div align="center">

# 🛡️ ModGuard + Sentinel

**Encrypted client verification system for Paper servers**

[![Modrinth](https://img.shields.io/modrinth/v/modguard?label=Modrinth&logo=modrinth&color=1bd96a)](https://modrinth.com/plugin/modguard)
[![Discord](https://img.shields.io/discord/1234567890?label=Discord&logo=discord&color=5865F2)](https://discord.gg/tFXhkPVpxG)
[![Java](https://img.shields.io/badge/Java-21-orange?logo=openjdk)](https://adoptium.net/)
[![Paper](https://img.shields.io/badge/Paper-1.21.x-white?logo=spigotmc)](https://papermc.io/)
[![License](https://img.shields.io/badge/License-MIT-blue)](LICENSE)

</div>

---

ModGuard verifies connecting players using a secure encrypted handshake between the server plugin and a required client mod (Sentinel). It detects banned modifications far more reliably than client-brand checks — the mod list is collected by Sentinel via FabricLoader and returned inside an encrypted, HMAC-signed payload the server decrypts and verifies.

---

## 📦 Components

### 🔌 ModGuard — Server Plugin
Sends an RSA-encrypted challenge on join, verifies the response, checks mod IDs against a blacklist, enforces a minimum Sentinel version, and optionally limits total installed mods. Supports cracked servers, Geyser/Floodgate auto-bypass, configurable kick messages, player whitelist, and persistent mod history.

### 🧩 Sentinel — Client Mod
Installed by the player. Receives the server challenge, collects the full mod list via FabricLoader, encrypts and returns it. Zero configuration. Zero performance impact.

---

## ⚙️ How It Works

```
Player joins
  → Server sends encrypted challenge
      (RSA-2048 public key + nonce + HMAC-SHA256 secret)
  → Sentinel collects mod list via FabricLoader
  → Sentinel encrypts response
      (AES-256-GCM, key wrapped with RSA/OAEP/SHA-256)
  → Server decrypts payload, verifies HMAC
  → Protocol version + Sentinel version checked
  → Mod list compared against blacklist
  → Optional mod count limit checked
  → Player approved or kicked with a configurable message
```

---

## 🔒 Security Model

| Property | Implementation |
|---|---|
| Encryption | AES-256-GCM (authenticated) |
| Key exchange | RSA-2048 / OAEP / SHA-256 |
| Payload signing | HMAC-SHA256 |
| Replay protection | Nonce per challenge |
| Key persistence | Keypair generated fresh each session, never written to disk |
| Spoofing | Protocol version verified inside the encrypted payload |

---

## 🚀 Installation

**Server:**
1. Drop `ModGuard.jar` into `plugins/`
2. Restart — `config.yml` and `modblacklist.json` are generated automatically

**Client:**
1. Drop `Sentinel-<version>.jar` into `.minecraft/mods/`
2. Launch — no configuration needed

---

## 🚫 Default Blacklisted Mods

Pre-seeded in `modblacklist.json` on first run:

| Mod ID |
|---|
| `meteor-client` |
| `impact` |
| `wurst` |
| `liquidbounce` |
| `aristois` |
| `sigma` |
| `thunderhack` |
| `wolfram` |
| `baritone` |
| `freecam` |
| `marlows-crystal-optimizer` |

Add or remove entries with `/modguard blacklist add <modid>` or by editing `modblacklist.json` directly.

---

## ⚙️ Configuration

```yaml
# plugins/ModGuard/config.yml

offline-mode: false               # true for cracked servers
allow-floodgate: true             # Bedrock players via Geyser skip verification

challenge-timeout-seconds: 15    # seconds client has to respond before kick
challenge-delay-ticks: 40        # ticks after join before challenge is sent

min-sentinel-version: "1.0.0"    # kick players on older Sentinel builds
protocol-version: 1              # must match Sentinel — only change if updating both

enable-mod-count-limit: false
max-mod-count: 50

show-mod-list-in-console: true
highlight-banned-mods: true

kick-messages:
  missing-sentinel:   "&cSentinel mod is required to join this server."
  banned-mod:         "&cAccess denied."
  outdated-sentinel:  "&cYour Sentinel mod is outdated. Please update to &ev{version}&c."
  protocol-mismatch:  "&cSentinel version mismatch. Please update your Sentinel mod."
  timeout:            "&cVerification timed out."
  mod-count-exceeded: "&cToo many mods installed. Maximum allowed: &e{max}"
```

---

## 📋 Client Compatibility

| Scenario | Behavior |
|---|---|
| Fabric + Sentinel installed | Full verification |
| Vanilla (no mods) | Handled automatically — no Sentinel needed |
| Bedrock / Geyser | Bypassed automatically |
| Cracked / offline server | Supported via `offline-mode: true` |
| Whitelisted player | Skips verification entirely |
| Forge / NeoForge | Not supported |

---

## 💻 Commands & Permissions

| Command | Description |
|---|---|
| `/modguard blacklist <add\|remove\|list>` | Manage the mod blacklist |
| `/modguard whitelist <add\|remove\|list>` | Manage the player whitelist |
| `/modguard mods <player>` | View a player's verified mod list and last seen time |
| `/modguard check <player>` | Re-send a verification challenge to an online player |
| `/modguard status` | Show online players, pending verifications, and plugin info |
| `/modguard reload` | Reload configuration |

> Permission: `modguard.admin` — defaults to OP.

---

## 📁 Project Structure

```
src/main/java/org/modGuard/
├── ModGuard.java              # Plugin entry point, messaging channel registration
├── ChallengeService.java      # Sends encrypted challenges on join
├── ResponseHandler.java       # Decrypts and validates client responses
├── CryptoService.java         # RSA/AES/HMAC crypto operations
├── SessionManager.java        # Per-player session state and keypair storage
├── BlacklistManager.java      # modblacklist.json CRUD
├── WhitelistManager.java      # whitelist.json CRUD
├── ModHistoryManager.java     # Per-player mod history persistence
├── CommandHandler.java        # /modguard command handler
├── ConfigManager.java         # Config key access
├── PlayerState.java           # Player verification status model
└── Log.java                   # Logging helper
```

---

## 📜 Changelog

See [CHANGELOG.md](CHANGELOG.md)

---

<div align="center">

💬 **[Discord](https://discord.gg/tFXhkPVpxG)** · 🔗 **[Modrinth](https://modrinth.com/user/Treamhiler)** · 🐙 **[GitHub](https://github.com/Treamhilerjava)**

Made by **Treamhiler**

</div>
