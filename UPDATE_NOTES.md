# Disease library, product catalog and About update

Base: PlantCare_Final_Hostinger(1).zip. The model, class order, AI diagnostic workflow, report-generation code and source product data were retained. The uploaded base uses guest scans (UUID links), not authenticated scan history. This update does not reintroduce user login or change recommendation safety rules.

## Local update (existing project)

Back up your database and media before replacing files. Keep your own backend/.env, db.sqlite3 and existing media when updating. Do not copy example environment settings over production secrets.

From the backend directory in PowerShell, after dependencies are installed:

```powershell
.\.venv\Scripts\python.exe manage.py migrate
.\.venv\Scripts\python.exe manage.py seed_catalog
.\.venv\Scripts\python.exe manage.py runserver
```

Do not add `--replace-products` unless deliberately resetting the entire product catalog. The normal seed refreshes the stock disease descriptions and translations, adds missing product rows, and restores nonempty source product URLs to previously seeded matching rows (name + manufacturer). Back up custom disease translations before running the seed. Admin-added products with other names are not deleted.

In the frontend terminal:

```powershell
npm ci
npm run dev
```

To deploy using the provided Docker configuration: keep the existing `.env` and persistent volume, rebuild the frontend and API images with `docker compose up -d --build`. Do not delete the database volume. Verify HTTPS and the API after deployment.

## Images and languages

- 31 cropped rectangular WebP thumbnails: 30 leaf images and one blank background class, faithfully extracted from the four supplied image sheets. Thumbnails are illustrative reference graphics, not laboratory-verified diagnostic photographs.
- The 8 classes missing supplied images are Raspberry healthy, Soybean healthy, Squash powdery mildew, Strawberry leaf scorch, Strawberry healthy, Tomato bacterial spot, Tomato early blight and Tomato late blight. They fall back to an existing database image, or a clearly labeled generic illustration. The provided image map is in `frontend/src/lib/disease-images.json`; bundled images live in `frontend/public/disease-thumbnails/`.
- All 39 classes have five localized fields: description, symptoms, possible causes, prevention and management. The original detailed English text is retained. Urdu and Roman Urdu are expanded localized adaptations of the main disease-specific facts, not complete word-for-word translations of every English sentence. Scientific names are retained. Existing blank/English-only API translations are supplemented by the bundled translations. The seed refreshes existing stock rows to the expanded translations.
- About section: 14 crops, 39 classes (26 conditions, 12 healthy, 1 background), Botany explanation left and University of Narowal logo right on desktop, stacked on mobile, with Mazain Solution centered underneath. The logo is the supplied low-resolution JPEG. Mazain Solution remains in English.
- All 48 source product rows and 31 nonempty supplied URL strings retained. The remaining 17 product URLs are blank in the file; no guessed links added. Captan, sulfur and lime sulfur are not separate product entries in this base archive. No product photos were included. The current links were not independently verified for availability or current registration. Product photo fallback and modal layout improved.

## Verification checklist

1. Open `/library`, switch to Urdu, open Apple scab, switch to Roman Urdu and English without refreshing, and inspect all five content sections.
2. Toggle dark/light while on the grid and disease detail. Test 360px, 768px and desktop widths, verify text/readability and no horizontal scrolling.
3. Open `/fungicides`, inspect Acrobat and Acute 47% WP links. Verify native dialog closes via its button, Escape and backdrop. Check mobile layout.
4. Check `/about` in all three languages, including crop list and institutional positioning.
5. Test a real leaf scan and report download. Existing diagnosis and report logic were not changed.

Source syntax and data mapping were checked in the editing environment; actual browser, Django runtime and TensorFlow inference require installed dependencies and have not been run here. This is not a fresh production-security audit.
