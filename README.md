# Memos snap

[![memos](https://snapcraft.io/memos/badge.svg)](https://snapcraft.io/memos)

Snap packaging for [Memos](https://github.com/usememos/memos), a lightweight, self-hosted, open-source note-taking service.

Unofficial build, not affiliated with or endorsed by the Memos project.

## Build

```bash
snapcraft pack
```

## Install and try

<a href="https://snapcraft.io/memos"><img alt="Get it from the Snap Store" src="https://snapcraft.io/en/dark/install.svg" width="180"></a>

```bash
sudo snap install memos   # or a local build: ./memos_0.31.0_*.snap --dangerous
```

The service starts right away and listens on `127.0.0.1:8081`. Open http://localhost:8081 and create the first account: it becomes the administrator.

## Background service

```bash
sudo snap logs memos       # view logs
sudo snap restart memos    # restart
sudo snap stop --disable memos
sudo snap start --enable memos
```

### Settings

| Setting | Default | Description |
|---|---|---|
| `port` | `8081` | Port to listen on |
| `addr` | `127.0.0.1` | Address to listen on. Use `0.0.0.0` for all networks |
| `instance-url` | not set | Public URL of the instance, for example `https://memos.example.com` |
| `log-level` | `info` | `debug`, `info`, `warn` or `error` |

Changes apply right away. The service restarts by itself.

```bash
sudo snap get memos
sudo snap set memos port=9000
sudo snap unset memos instance-url
```

### Open the service to your network

```bash
sudo snap set memos addr=0.0.0.0
```

Then open `http://<this-machine-ip>:8081` from another device. Your firewall must allow the port (for example `sudo ufw allow 8081/tcp`).

Create the administrator account before opening the service: until then, anyone who reaches the page can create it. For access from the internet, put Memos behind a reverse proxy with HTTPS and set `instance-url`.

## Data, upgrades and backups

| What | Location |
|---|---|
| Database | `/var/snap/memos/current/memos_prod.db` |
| Attachments and caches | `/var/snap/memos/common` |
| Backups | `/var/snap/memos/common/backups` |

Memos upgrades its database when a new release starts. The snap protects these upgrades:

- **Revert.** snapd keeps a copy of the database for each installed revision. If an upgrade causes problems, `sudo snap revert memos` returns to the previous release with the database as it was before the upgrade.
- **Automatic backups.** Before every refresh, the snap saves a copy of the database in `backups/`. The last 3 are kept.
- **Downgrade guard.** Memos cannot open a database from a newer release. If a refresh would start an older release on a newer database (for example, switching from `edge` back to `beta` after an upgrade), the snap stops the refresh and stays on the current release.

To back up the database by hand (kept until you delete it):

```bash
sudo memos.backup
```

To restore a backup:

```bash
sudo snap stop memos
sudo cp /var/snap/memos/common/backups/<backup>.db /var/snap/memos/current/memos_prod.db
sudo rm -f /var/snap/memos/current/memos_prod.db-wal /var/snap/memos/current/memos_prod.db-shm
sudo snap start memos
```

`sudo snap save memos` also creates a full snapshot of the database and attachments.

## Channels

| Channel | Builds |
|---|---|
| `beta` | The release in `snap/snapcraft.yaml` (currently 0.31.0) |
| `edge` | The latest commit on upstream's `main` branch, every week |

`edge` can upgrade the database to a schema that `beta` cannot open yet. After that, switching back to `beta` is refused until `beta` catches up. `snap revert` or a backup restores the older database.

## Known limitations

### SQLite only

The snap uses the built-in SQLite database. MySQL and PostgreSQL are not supported, as reverting a refresh cannot restore an external database.

### Attachments after a revert

`snap revert` restores the database but not attachments, which all revisions share. Files uploaded after the upgrade stay on disk without being listed, and files deleted after the upgrade are missing.

### Databases from older Memos installations

Releases after 0.31.0 only upgrade databases that already reached the 0.31.0 schema. To import a database from an older Memos installation, first open it once with Memos 0.31.0 (0.25.3 first, if it is older than 0.22), for example with upstream's binary or container image. The snap refuses to start a release on a database that is too old, and stays on the current release.

### Home folders

The service runs as root and cannot read or write files in `/home`. Use the web interface to upload attachments.

## Update to a new upstream release

Change `version` in `snap/snapcraft.yaml`, then rebuild.

## Automation

Builds, publishing, and dependency updates use [snap-automation](https://github.com/sidalit/snap-automation).

## License

The packaging is MIT-licensed (see `LICENSE`). Memos is MIT-licensed; its dependencies carry their own licenses.
