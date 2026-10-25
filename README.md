<div align="center">

<img src="snap/gui/memos.png" alt="Memos logo" width="128" />

# Memos

**Capture and organize your notes.**

[![memos](https://snapcraft.io/memos/badge.svg)](https://snapcraft.io/memos)
[![Base: core26](https://img.shields.io/badge/Base-core26-2C8C3C?logo=canonical&logoColor=white)](https://snapcraft.io/core26)
![Confinement: strict](https://img.shields.io/badge/Confinement-strict-2C8C3C)
[![Maintainer: sidaliteir](https://img.shields.io/badge/Maintainer-sidaliteir-2C8C3C?logo=snapcraft&logoColor=white)](https://snapcraft.io/publisher/sidaliteir)
![Architectures: amd64 and arm64](https://img.shields.io/badge/Architectures-amd64%20%7C%20arm64-2C8C3C?logo=linux&logoColor=white)

[Getting Started](#getting-started) · [Configure](#configure) · [Limitations](#limitations) · [Build](#build) · [Support](#support) · [License](#license)

Unofficial snap packaging for [Memos](https://github.com/usememos/memos), a self-hosted note-taking service.

</div>

---

## Getting Started

<a href="https://snapcraft.io/memos"><img alt="Get it from the Snap Store" src="https://snapcraft.io/en/dark/install.svg" width="180"></a>

```bash
sudo snap install memos
```

Open [http://127.0.0.1:8081](http://127.0.0.1:8081). The service starts automatically; the first account becomes the administrator. Create it before exposing Memos to your network.

## Configure

| Setting | Accepted values | Default |
| --- | --- | --- |
| `port` | Integer `1`–`65535` | `8081` |
| `addr` | IP address or hostname; empty listens on all interfaces | `127.0.0.1` |
| `instance-url` | `http://…`, `https://…`, or empty | Unset |
| `log-level` | `debug`, `info`, `warn`, `error` | `info` |

```bash
sudo snap get memos
sudo snap set memos addr=0.0.0.0 port=8081
```

Changes restart an enabled or running service. Visit `http://<device-ip>:8081` from another device. Use an HTTPS reverse proxy for internet access.

The database is `/var/snap/memos/current/memos_prod.db`; attachments are in `/var/snap/memos/common`.

```bash
sudo memos.backup    # Database backup
sudo snap save memos # Full snap snapshot, including attachments
```

Database backups go to `/var/snap/memos/common/backups`. The snap also keeps the last three automatic database backups made before updates; manual backups are retained.

## Limitations

- SQLite only. Upload attachments through the browser; the service cannot read your home directory.
- `snap revert` restores the previous database revision, but shared attachments are not rolled back.
- Switching to a release that cannot open the current database is refused; use `snap revert` or restore a compatible backup.
- Existing databases older than `0.22` must first migrate through `0.25.3`, then `0.31.0`. Upgrades beyond `0.31.0` require that version to have run successfully first.

## Build

```bash
git clone https://github.com/sidalit/memos-snap.git
cd memos-snap
sudo snap install snapcraft --classic
snapcraft pack
```

## Support

- **Installation or snap issues:** [open a packaging issue](https://github.com/sidalit/memos-snap/issues).
- **Application bugs:** [report them upstream](https://github.com/usememos/memos/issues).

## License

Snap packaging: [MIT](https://github.com/sidalit/memos-snap/blob/main/LICENSE).
