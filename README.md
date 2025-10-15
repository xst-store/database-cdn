# README.md

## Endpoint
### 1) `GET /`
- Render `views/index.ejs`.  
- Variabel view: `{ BASE_URL }`.

### 2) `POST /upload`
- Form-Data: `file` (field tunggal).  
- Proses:
  - File dibaca di memori (Multer memoryStorage).
  - Nama GitHub: `dir/DD-mmmm-YYYY~HH:mm:ss<ext>` dalam TZ `Asia/Jakarta`, huruf kecil.
  - `dir` dipilih otomatis dari ekstensi:
    - `image`: `.jpg .jpeg .png .gif .webp`
    - `videos`: `.mp4 .mov .mkv .webm`
    - `archives`: `.zip .rar .7z`
    - `docs`: `.pdf .docx .xlsx`
    - `texts`: `.txt`
    - `data`: `.csv .json`
    - selain itu → `files`
  - Disimpan ke GitHub via `uploadBufferToGitHub({ buffer, ghPath })`.
  - Dibuat slug acak (10 chars) dan dipetakan ke metadata di `data/urls.json`.
- Respons 200 (JSON):
```json
{
  "slug": "AbC123xYz9",
  "link": "https://url.arsyilla.my.id/AbC123xYz9",
  "raw": "https://raw.githubusercontent.com/<owner>/<repo>/<branch>/<path>",
  "repo_url": "https://github.com/<owner>/<repo>/blob/<branch>/<path>"
}
```
- Error 400: `file kosong` jika tidak ada file.  
- Error 500: `{ "error": "upload_gagal", "detail": "<pesan>" }`.

### 3) `GET /:slug`
- Render `views/view.ejs`.  
- Variabel view: `{ BASE_URL, slug, rec }`, di mana `rec` berisi:
```json
{
  "rawUrl": "...",
  "htmlUrl": "...",
  "mime": "image/jpeg",
  "name": "nama-asli.jpg",
  "size": 12345,
  "createdAt": "2025-10-15T12:34:56.000Z"
}
```
- Cache: `public, max-age=60`.

### 4) `GET /:slug/download`
- Redirect ke `rawUrl` dengan header `Content-Disposition` agar terunduh sebagai `rec.name`.
