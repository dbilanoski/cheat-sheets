# Audiobookshelf Notes

Audiobookshelf is a self-hosted server for **audiobooks and podcasts**, similar to Plex but for audio books.

+ Designed for long-form audio (chapters, progress sync, bookmarks)
- Not a music server replacement

Supports web, mobile apps, and external players.


## Deploying Audiobookshelf (Docker Compose)

- Default port: `13378`
- Safe to expose behind reverse proxy / Cloudflare Tunnel
- Read-only media volumes recommended

**Prerequisites:**
- Docker + Docker Compose installed on the server
- Audiobook media stored on the host filesystem

**Minimal `docker-compose.yml`**

```yaml
version: "3.8"

services:
  audiobookshelf:
    image: advplyr/audiobookshelf:latest
    container_name: audiobookshelf
    restart: unless-stopped
    ports:
      - "13378:80"
    volumes:
      - /srv/audiobookshelf/config:/config
      - /srv/audiobookshelf/metadata:/metadata
      - /srv/media:/media:ro
    environment:
      - TZ=Europe/Zagreb
```

### Steps

1. Create folders for Audiobookshelf app data:    
    - `/srv/audiobookshelf/config` -> app config, database
    - `/srv/audiobookshelf/metadata` -> covers, processed metadata
        
2. Ensure your audiobook files exist somewhere under `/srv/media`
3. Write `docker-compose.yml` in your docker services folder
4. Start the container: `docker compose up -d`

Audiobookshelf is now accessible at:  `http://<host-ip>:13378`

## Library Setup

Create **separate libraries per media type**:
- **Audiobooks** **->** type: _Audiobooks_
- **Podcasts** -> type: _Podcasts_
    
Point each library to the appropriate folder under `/media`.


## Library Structure

Audiobookshelf relies heavily on **folder structure and filenames**.

### Audiobooks (Recommended)
- One folder per book
- One parent folder per author
- One top-level folder per series (if applicable)
- Multi-file audiobooks are expected

Example with separate books part of a series:

```text
/media/audiobooks/
└── Cixin Liu/
    └── Remembrance of Earth's Past/
        ├── Book 01 - The Three-Body Problem/
        │   ├── 01 - Chapter 01.mp3
        │   ├── 02 - Chapter 02.mp3
        │   └── cover.jpg
        ├── Book 02 - The Dark Forest/
        │   └── ...
        └── Book 03 - Death's End/
            └── ...

```

Example with singe file audio books:

```text
/media/audiobooks/
└── Andy Weir/
    └── Project Hail Mary/
        ├── Project Hail Mary.m4b
        └── cover.jpg
```

### Metadata Notes

- Folder and filename are the primary source
- Embedded audio tags (ID3, M4B) are supported
- External `.opf` files are ignored
- Metadata and covers are cached under `/metadata`
- Series detection works best with explicit folder naming

For metadata prep, tools like **MusicBrainz Picard** or **mp3tag** work well.

## AppData / Config Files

- `/config` is critical - back it up.
- `/metadata` can be regenerated, but backups save time.
- Media files are never modified.

Audiobookshelf runtime data lives outside the container:

```text
/srv/audiobookshelf/
├── config/      # Database, users, settings
├── metadata/    # Covers, processed metadata
```


## Backups, Reinstalling & Migration

### Backup

Back up:
- `/srv/audiobookshelf/config`
- `/srv/audiobookshelf/metadata` (optional but recommended)
    

Example cron-style script:

```bash
tar -czf audiobookshelf-backup-$(date +%F).tar.gz /srv/audiobookshelf
```

### Migration / Reinstall Checklist

1. Stop container
2. Copy `/srv/audiobookshelf/config`
3. Copy `/srv/audiobookshelf/metadata`
4. Copy `/srv/media`
5. Recreate container with same volume paths
6. Start container

Playback progress and users are preserved.


## References
1. https://www.audiobookshelf.org
2. https://www.mp3tag.de/en/
