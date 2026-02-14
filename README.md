# media-server

## Description
A project to set up a media server on a small computer.

## Fixing QBitTorrent

If qBittorrent is not accessible after restart, run this command to configure it to bind to all IPv4 addresses on the wg0 interface:

```bash
docker compose exec qbittorrent sed -i 's/Session\\InterfaceAddress=.*/Session\\InterfaceAddress=0.0.0.0/' /config/config/qBittorrent.conf && docker compose restart qbittorrent
```

This sets:
- Network interface: `wg0` (WireGuard)
- Optional IP to bind to: `0.0.0.0` (all IPv4 addresses)

When set to `0.0.0.0`, qBittorrent will automatically bind to whatever IPv4 address wg0 receives, regardless of Pi restarts or network changes.

### Why This Works

- PIA assigns a new IP to `wg0` each time the container restarts
- Instead of chasing a moving target with IP updates, bind to `0.0.0.0` (all IPs) on the interface
- qBittorrent automatically uses the current IP without manual intervention

### Verify It's Working

Check within container:
```bash
docker compose exec qbittorrent grep "Session\\Interface" /config/config/qBittorrent.conf
```

Should show:
```
Session\Interface=wg0
Session\InterfaceAddress=0.0.0.0
Session\InterfaceName=wg0
```

Access WebUI at: `http://localhost:8080`

