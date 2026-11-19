# PlantCare AI — Hostinger VPS deployment

Use an Ubuntu x86-64 VPS with Docker Engine, Compose v2 and Nginx installed. Recommend at least 4 GB RAM (8 GB preferable for building and TensorFlow inference). No external AI API is required. The model is included.

1. Upload and unzip the project, then `cd PlantCare_Final`.
2. `cp .env.example .env`; generate a secret using `openssl rand -hex 48`. Edit `.env` with that secret and your actual domain (replace all example domains).
3. `docker compose up -d --build`; inspect `docker compose logs -f api`. First build downloads Python and Node dependencies.
4. Create an admin: `docker compose exec api python manage.py createsuperuser`.
5. Point your domain A record to the VPS IP. Copy `deploy/host-nginx.conf` to `/etc/nginx/sites-available/plantcare`, replace the domain, symlink into `sites-enabled`, run `sudo nginx -t` and `sudo systemctl reload nginx`. Install Certbot and its Nginx plugin, then run `sudo certbot --nginx -d YOUR_DOMAIN`. Allow ports 80/443 in the VPS firewall, protect SSH. Do not expose port 8080 externally. If using Cloudflare, use Full (strict) TLS after certificate issuance.
6. Visit `https://YOUR_DOMAIN/api/status/`, `/admin/`, then test diagnosis with a known leaf photo and download the PDF. Admin → Instruction videos → select diagnosis or about, enter title, description, HTTPS YouTube/Vimeo or direct MP4 URL, enable and save. Videos are optional; diagnosis never waits for playback. Replace `frontend/public/mazain-logo.svg` with the official brand asset and rebuild web if desired.
7. Product catalog contains 48 source records, not verified prescriptions. Admin must independently confirm registration and exact crop/disease mapping, then enter label URL, registration region/reference, tick verified and published. Only mapped active verified published fungicides are recommended for qualifying fungal/oomycete predictions. No claim of diagnostic certainty is made. No products are auto-verified.
8. Back up the persistent `plantcare_data` volume regularly, preferably with containers stopped for a consistent SQLite backup. Do not use `docker compose down -v` unless deliberately deleting data. Keep one API worker for model memory and SQLite. Update using `docker compose up -d --build`.

## Merge notes
02 is the runtime base (public UUID scan workflow, model, diagnosis frontend, catalog). 01 supplies English disease field guidance and the tabular ReportLab design; 02 Urdu/Roman translations retained (not expanded translations of all longer 01 text). Original 01 research product records excluded. Two admin video slots and an About route added. Logo is a replaceable provisional brand mark. No official video or logo was supplied. Source archives do not contain a database, so uploaded media cannot be reliably matched to disease rows automatically. Assign images in admin. Full runtime and VPS deployment need validation on the server.
