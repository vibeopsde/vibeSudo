# vibeSudo

One-shot passwordless sudo setup for Debian/Ubuntu — detects, warns, configures, validates, rolls back.

## What it does

1. **Detects** the current state: is the user in the sudo group? Is NOPASSWD already active? Do we have root or working sudo?
2. **Warns** prominently about the security implications of passwordless sudo before anything changes
3. **Configures** everything needed: adds user to sudo group, creates `/etc/sudoers.d/<user>` drop-in with `NOPASSWD: ALL`, chmod 0440
4. **Validates** with `visudo -c` — rolls back automatically on syntax error
5. **Verifies** the result with a live `sudo -n` check

## Quick Start (fresh system, nothing installed)

You're staring at a blank Debian/Ubuntu box — no git, no sudo, no tools.
Just a root login and a terminal. Here's how:

### Option A: with curl (recommended)

```bash
# 1. install sudo + curl (one-time, as root)
apt update && apt install -y sudo curl

# 2. download vibeSudo
curl -fsSL https://raw.githubusercontent.com/vibeopsde/vibeSudo/main/vibeSudo -o /tmp/vibeSudo
chmod +x /tmp/vibeSudo

# 3. dry-run first — shows status + warnings, changes nothing
/tmp/vibeSudo --dry-run

# 4. if it looks good: run for real
/tmp/vibeSudo
```

### Option B: with wget (if no curl)

```bash
apt update && apt install -y sudo wget
wget -qO /tmp/vibeSudo https://raw.githubusercontent.com/vibeopsde/vibeSudo/main/vibeSudo
chmod +x /tmp/vibeSudo
/tmp/vibeSudo --dry-run
/tmp/vibeSudo
```

### Option C: with git

```bash
apt update && apt install -y git sudo
git clone https://github.com/vibeopsde/vibeSudo.git
cd vibeSudo
./vibeSudo --dry-run
./vibeSudo
```

### After installation

The user now has passwordless sudo. From here you can work as a normal user —
install tools, clone repos, set up your environment:

```bash
sudo apt install -y python3 python3-venv  # or whatever you need
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