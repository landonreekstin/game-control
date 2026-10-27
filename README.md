# game-control

A small FastAPI dashboard for starting and stopping the game servers on a home server.

Each server is a Docker container run by a NixOS-managed `docker-<name>` systemd unit.
The dashboard shows each one's status and player count (via `rcon-cli` for Java
Minecraft, `send-command` for Bedrock), starts and stops them through systemd (saving
first where RCON allows), and switches the active Astroneer save.

Access is guarded by a shared token: every request needs `?token=…` matching the
`GAME_CONTROL_TOKEN` environment variable. The server list is `SERVERS` in `app.py`.

## Running

```sh
GAME_CONTROL_TOKEN=… uvicorn app:app --host 0.0.0.0 --port 8080
```

Needs `fastapi`, `uvicorn` and `jinja2`, plus `docker` and `systemctl` on PATH and write
access to `/var/lib/game-control` (where it stamps each server's last-active time for an
idle watchdog).

Deployed by [landonreekstin/nixos-config](https://github.com/landonreekstin/nixos-config)
(`pkgs/game-control`, `modules/nixos/homelab/game-control.nix`), which also provides
the token, the service unit and the idle-shutdown watchdog.
