# PlantCare AI — simplified multilingual release

Public flow: Home → upload/camera → local EfficientNetV2B0 → diagnosis report with disease explanation, related product cards and downloadable PDF. Disease and product libraries support English, Urdu (RTL), Roman Urdu. Django admin remains protected by administrator login.

## Run locally (Windows PowerShell)

Backend, Python 3.11 recommended:
```
cd backend
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
```
Set `DEBUG=True` and a unique `SECRET_KEY` in `.env` for local use. The supplied report renderer uses ReportLab; install the listed requirements and use an Urdu-capable font on the system. Then:
```
python manage.py migrate
python manage.py seed_catalog
python manage.py createsuperuser
python manage.py runserver
```
`--replace-products` intentionally deletes all previous product and treatment-option records; back up any existing DB first. It is not required on subsequent runs unless you want to reset your catalogue. Existing disease images are retained if already associated with records. No database was supplied in the source archive.

In a second terminal:
```
cd frontend
npm ci
npm run dev
```
Open http://localhost:5173; admin http://127.0.0.1:8000/admin/ . Production: build frontend with `npm run build`; reverse proxy `/api/` and `/admin/` to Django, serve `dist` with SPA fallback, keep scan files outside public media routes, set secure production env/HTTPS, install system PDF dependencies. Do not expose Django development server.

## Data and limits
- 39 class records in `backend/ml_models/localized_diseases.json`, with full original English guidance and expanded Urdu and Roman Urdu adaptations; scientific/brand names retained for accurate identification.
- 48 commercial entries in `backend/ml_models/products_pk.json`. Hooter (herbicide) and Shield (seed treatment) excluded. Missing direct product links left blank rather than inventing them. Manufacturer catalogue presence is not proof of current Pakistani registration or retail stock. Product mappings conservative, limited to previously identified crop/disease uses; missing mappings display care guidance; no pesticides recommended for uncertain scans. Benevia is vector control, not a viral cure.
- No original product photos supplied in this archive. Admin may upload actual product photos. 31 supplied illustrative thumbnails (including the blank background class) are bundled under frontend/public/disease-thumbnails. Eight classes without supplied thumbnails use existing admin photos or a labeled placeholder.
- Admin fields simplified to name, manufacturer, category, active ingredient, description, translations, precautions, product link, image, associated diseases, active, sort order. Legacy DB columns retained for migration compatibility but not shown/required. External verification and public auth routes removed. Guest reports use UUID bearer links; anyone with a link can open it. Use HTTPS and avoid sharing links; implement retention/cleanup before production. Technical file-size and confidence safeguards kept.
- Prior academic documents in docs/ are historical and not updated to reflect this release.

## Validation
Run `python manage.py test core` and `npm run build` after dependency installation. Test a real leaf upload and download all three report language variants. This archive was source-checked, not deployed or tested with TensorFlow inference in the editing environment.
