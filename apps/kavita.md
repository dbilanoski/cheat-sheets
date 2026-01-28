# Kavita Notes

Kavita is a self-hosted digital library server for **manga, comics, and books** (CBZ/CBR, EPUB, PDF).

+ Use Kavita as the reader, not the librarian.  
- Do metadata and organization outside, then let Kavita index.
## Deploying Kavita (Docker Compose)

- Default port: `5000`
- Safe to expose behind reverse proxy / Cloudflare Tunnel
- Read-only media volumes recommended

**Prequisites:**
- Docker + Docker Compose installed on the server.
- Media stored on the host filesystem.

**Minimal `docker-compose.yml`**

```yaml
version: "3.8"

services:
  kavita:
    image: kizaing/kavita:latest
    container_name: kavita
    restart: unless-stopped
    ports:
      - "5000:5000"
    volumes:
      - /srv/kavita/config:/kavita/config # This is where your kavita config is stored
      - /srv/media:/media # This is where you keep your media files
    environment:
      - TZ=Europe/Zagreb
```

### Steps

1. Create folder for Kavita app data
	- This is mapped to `/kavita/config` in Docker.
	- database, `config.json`, logs, cache and other runtime data lives there.
	- Backups are highly recommended. 
2. Create folder for Kavita docker instance where you keep your docker compose instances.
3. Write `docker-compose.yaml` there as proposed earlier making sure you adjust `volumes` to your actual folders.
4. Navigate to that folder and start the container with:
   
   ```bash
   docker compose up -d
   ```

Kavita is now accessible at: `http://<host-ip>:5000`

## Library Setup

Create **separate libraries per media type**:

- **Manga** -> type: _Manga_
- **Comics** -> type: _Comic_
- **Books** -> type: _Book_
    
Point each library to the correct folder under `/media`.

##  Library Structure

- One series per folder
- Always include volume/book numbers
- Avoid mixing formats in the same library
- Do not rely on Calibre `.opf` metadata
- Keep naming consistent across all media

### Manga / Comics (CBZ/CBR)

- One folder = one series
- Volume numbering is required
- Kavita ignores `.opf` files
   

```text
/media/manga/
└── One Piece/
    ├── Vol 01.cbz
    ├── Vol 02.cbz
    └── Vol 03.cbz
```


### Books (EPUB/PDF)

- Series-first layout works best
- Explicit numbering ensures correct order
- Author folders are optional

Example where separate books are part of a series:

```text
/media/books/
└── Cixin Liu
	└── Remembrance of Earth's Past/
			├── Remembrance of Earth's Past - Book 01 - The Three-Body Problem.epub
			├── Remembrance of Earth's Past - Book 02 - The Dark Forest.epub
			└── Remembrance of Earth's Past - Book 03 - Death's End.epub
```


### Metadata Notes

- Kavita primarily uses **folder and filename**
- Embedded EPUB metadata is supported
- `.opf` files (Calibre) are ignored
- Advanced scraping requires paid features

For prep work, use [Calibre](https://calibre-ebook.com/) software to scrape embed metadata to EPUB. 


## Backups, Reinstalling & Migration

Kavita stores all runtime data in the config directory so those files should be backed up and used for migration\reinstallation.

### Backup

Back up content of:  `/srv/config/`

Example (simple cron-style backup):

`tar -czf kavita-backup-$(date +%F).tar.gz /srv/kavita/config`

### Migration / Reinstall Checklist

If you ever move Kavita to another server:

1. Stop container
2. Copy `/srv/kavita/config`
3. Copy `/your-media-folfers`
4. Recreate container with same volume paths
5. Start container
    
Everything resumes exactly where you left off.


## References
1. [Kavita project web](https://www.kavitareader.com/)
2. [Calibre eBook management tool web](https://calibre-ebook.com/)