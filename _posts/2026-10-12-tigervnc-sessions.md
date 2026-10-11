---
layout: post
title: TigerVNC sessions
categories: [ops]
---

Start, list, and stop localhost-only TigerVNC desktops (XFCE), then connect through an SSH tunnel.

## Everyday commands

| Command | What it does |
|---|---|
| `vncstart` | Free display `:1`–`:99`, `-localhost yes`, default lifetime **7 days** |
| `vncstart long` | Same, lifetime **60 days** |
| `vncstart 14d` | Custom lifetime (`7d`, `2w`, `1m` also work) |
| `vncstart -geometry 1920x1080` | Set desktop size |
| `vnckill :N` | Stop display `:N` and clear its expire stamp |
| `vncserver -list` | List your sessions |
| `vncwhich` | Show `DISPLAY`, live `Xvnc`, and ports 5900–5999 |
| `vncuse N` | `export DISPLAY=:N.0` in this shell |
| `vncpasswd` | Set the VNC password (first 8 characters only) |

Geometry shortcuts: `vncsmall` (1440×900), `vnc4k`, `vncv`, `vncv2`.

```bash
vncstart -geometry 1920x1080
vncserver -list
vnckill :N
```

## Display and port

Display `:N` uses RFB port `5900 + N`. Skip numbers already taken by another process, X socket, or reserved systemd mapping. Prefer a free high number for extra sessions.

`vncstart` effectively runs:

```bash
vncserver :N -localhost yes [options]
```

and records an expire stamp so a lifetime sweep can stop the session later.

## Connect from a laptop

Sessions bind to localhost only. Tunnel, then open the viewer locally:

```bash
ssh -N -L 590N:127.0.0.1:590N USER@HOST
# viewer → localhost:N  or  127.0.0.1:590N
# prefer VncAuth (classic VNC password)
```

## Notes

- Do not stop another user’s `Xvnc`.
- Do not open VNC on the public network; keep `-localhost yes` and tunnel.
- After too many failed logins, restart that display as its owner.
- For GUI apps in a terminal: `vncwhich`, then `vncuse N`.
