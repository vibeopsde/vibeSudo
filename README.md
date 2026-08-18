# vibeSudo

One-shot passwordless sudo setup for Debian/Ubuntu — detects, warns, configures, validates, rolls back.

## What it does

1. **Detects** the current state: is the user in the sudo group? Is NOPASSWD already active? Do we have root or working sudo?
2. **Warns** prominently about the security implications of passwordless sudo before anything changes
3. **Configures** everything needed: adds user to sudo group, creates `/etc/sudoers.d/<user>` drop-in with `NOPASSWD: ALL`, chmod 0440
4. **Validates** with `visudo -c` — rolls back automatically on syntax error
5. **Verifies** the result with a live `sudo -n` check

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