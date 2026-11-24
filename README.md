# Remote Dev Tunnel

A configurable helper for using a local proxy from a remote Linux development server through an SSH reverse tunnel. It supports VS Code Remote-SSH and remote CLI workflows.

## Features

- Checks the local proxy and SSH connection.
- Checks outbound connectivity through the remote proxy.
- Creates a reverse SSH tunnel when needed.
- Opens a VS Code Remote-SSH workspace.
- Starts a configured remote CLI command (Codex CLI by default).

## Requirements

- macOS, Bash, OpenSSH, `nc`, and `curl`
- A working SSH host alias in `~/.ssh/config`
- Optional: VS Code with the Remote - SSH extension
- Optional: the chosen CLI installed on the remote host

## Install

```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/remote-dev-tunnel.git
mkdir -p ~/bin ~/.config/remote-dev-tunnel
cp remote-dev-tunnel/scripts/remote-dev-tunnel ~/bin/remote-dev-tunnel
cp remote-dev-tunnel/config/config.example ~/.config/remote-dev-tunnel/config
chmod +x ~/bin/remote-dev-tunnel
chmod 600 ~/.config/remote-dev-tunnel/config
```

Ensure `~/bin` is in your `PATH`. For zsh, add `export PATH="$HOME/bin:$PATH"` to `~/.zshrc` if needed.

Edit your private config:

```bash
nano ~/.config/remote-dev-tunnel/config
```

## Usage

```bash
remote-dev-tunnel status
remote-dev-tunnel tunnel
remote-dev-tunnel vscode
remote-dev-tunnel codex
```

You can pass an alternate remote directory:

```bash
remote-dev-tunnel vscode /path/to/project
remote-dev-tunnel codex /path/to/project
```

| Command | Purpose |
|---|---|
| `status` | Check local proxy, SSH, and remote proxy |
| `tunnel` | Ensure the reverse tunnel is working |
| `vscode` | Open the configured VS Code Remote-SSH workspace |
| `codex` | Run the configured CLI command remotely |
| `help` | Show help |

## Configuration

See `config/config.example`. The actual config belongs at `~/.config/remote-dev-tunnel/config`, outside this repository.

Set `SSH_HOST` to an alias from your local SSH config. Do not put private IPs, usernames, or hostnames in the public example file.

## Optional: remote VS Code proxy environment

Some remote extensions need proxy variables in the environment of the remote VS Code Server / extension host. If appropriate for your shell and server policy, add the following to your remote user's `~/.bashrc`, adapting the port to match your config:

```bash
export HTTP_PROXY="http://127.0.0.1:8080"
export HTTPS_PROXY="http://127.0.0.1:8080"
export http_proxy="$HTTP_PROXY"
export https_proxy="$HTTPS_PROXY"
export NO_PROXY="localhost,127.0.0.1,::1"
```

Check for existing settings before adding duplicates. This project does not modify remote shell files automatically. Reconnect or restart the remote VS Code Server if required.

## Security

- Never commit SSH keys, API keys, tokens, cookies, or personal config.
- Do not publish private hostnames, IP addresses, usernames, filesystem paths, or internal project names.
- Use only on servers where SSH forwarding is authorized; follow administrator policy.
- The reverse tunnel binds to `127.0.0.1` on the remote host by default. Avoid exposing it on `0.0.0.0`.
- The remote proxy is available only while the SSH tunnel remains alive.

## Troubleshooting

- **Local proxy unavailable:** start the local proxy application and check its configured listener.
- **SSH fails:** test the SSH alias directly with `ssh YOUR_ALIAS`.
- **Remote proxy fails:** check local proxy, SSH forwarding permissions, remote port availability, and remote `curl`.
- **VS Code does not connect:** install Remote - SSH and use `Remote-SSH: Connect to Host...`.
- **CLI not found:** ensure the CLI is installed and in the non-interactive remote `PATH`; test with `ssh YOUR_ALIAS 'command -v codex'`.

## License

MIT. See `LICENSE`.
