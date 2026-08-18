# vibeSudo

One-shot passwordless sudo setup for Debian/Ubuntu — detects, warns, configures, validates, rolls back.

## What it does

1. **Detects** the current state: is the user in the sudo group? Is NOPASSWD already active? Do we have root or working sudo?
2. **Warns** prominently about the security implications of passwordless sudo before anything changes
3. **Configures** everything needed: adds user to sudo group, creates `/etc/sudoers.d/<user>` drop-in with `NOPASSWD: ALL`, chmod 0440
4. **Validates** with `visudo -c` — rolls back automatically on syntax error
5. **Verifies** the result with a live `sudo -n` check

## Quick Start (frisches System ohne irgendetwas)

Du hast ein blankes Debian/Ubuntu vor dir — kein git, kein sudo, keine tools.
Nur ein root-login und ein terminal. So geht's:

### Variante A: mit curl (empfohlen)

```bash
# 1. sudo + curl installieren (braucht man einmal als root)
apt update && apt install -y sudo curl

# 2. vibeSudo herunterladen
curl -fsSL https://raw.githubusercontent.com/vibeopsde/vibeSudo/main/vibeSudo -o /tmp/vibeSudo
chmod +x /tmp/vibeSudo

# 3. erst mal nur angucken — ändert nichts, zeigt nur status + warnung
/tmp/vibeSudo --dry-run

# 4. wenn alles plausibel: echt ausführen
/tmp/vibeSudo
```

### Variante B: mit wget (falls kein curl)

```bash
apt update && apt install -y sudo wget
wget -qO /tmp/vibeSudo https://raw.githubusercontent.com/vibeopsde/vibeSudo/main/vibeSudo
chmod +x /tmp/vibeSudo
/tmp/vibeSudo --dry-run
/tmp/vibeSudo
```

### Variante C: mit git

```bash
apt update && apt install -y git sudo
git clone https://github.com/vibeopsde/vibeSudo.git
cd vibeSudo
./vibeSudo --dry-run
./vibeSudo
```

### Nach der installation

Der user hat jetzt passwordless sudo. Ab hier kannst du als normaler user
weiterarbeiten — z.b. tools installieren, repos clonen, hermes einrichten:

```bash
sudo apt install -y python3 python3-venv  # oder was auch immer du brauchst
```

### Rollback

```bash
sudo rm /etc/sudoers.d/<username>
```

## Usage

```bash
# Check current state, change nothing
./vibeSudo --dry-run

# Set up passwordless sudo for the current user
./vibeSudo

# Set up for a specific user (needs root)
./vibeSudo --user bob

# Skip confirmation prompt
./vibeSudo -y
```

## Requirements

- Debian or Ubuntu
- Root access (directly or via `su -`) or working sudo on the target machine
- `sudo` package installed

## Security

Passwordless sudo means **every process** running as the user can execute **any command** as root — without a password. This includes malware, browser extensions, and any `curl | bash` script.

Use only on trusted single-user systems. Never on shared or production servers without careful consideration.

Rollback at any time:
```bash
sudo rm /etc/sudoers.d/<user>
```

## License

MIT