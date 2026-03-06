# 📋 Panduan Setup LearningHub

## File yang Perlu Di-upload ke GitHub

```
index.html          ← Portal utama karyawan
hr-dashboard.html   ← Dashboard HR (login: hr2024)
```

---

## Cara Deploy ke GitHub Pages

1. Buat repo baru di GitHub (Public)
2. Upload kedua file HTML
3. Settings → Pages → pilih branch main → Save
4. URL: `https://username.github.io/nama-repo`

---

## Login Karyawan
- **Nama Lengkap** + **Outlet/Cabang**
- Tidak perlu password — cukup isi nama dan outlet

## Login HR / Admin
- Klik "Login sebagai HR / Admin" di halaman login
- Password default: `hr2024`
- Ganti password di baris kode: `if(pw === 'hr2024')`

---

## (Opsional) Integrasi Google Sheets untuk Tracking Real-time

Agar HR bisa pantau progress dari Google Sheets secara otomatis:

### Langkah 1 — Buat Google Sheet
1. Buat Google Sheet baru
2. Beri nama sheet: `Progress`
3. Tambah header di baris 1:
   `Timestamp | Event | Nama | Outlet | Modul | Skor | Total`

### Langkah 2 — Buat Apps Script
1. Di Google Sheet: **Extensions → Apps Script**
2. Hapus kode yang ada, paste kode berikut:

```javascript
function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('Progress');
    const data = JSON.parse(e.postData.contents);
    sheet.appendRow([
      data.timestamp || new Date().toISOString(),
      data.event || '',
      data.name || '',
      data.outlet || '',
      data.module || '',
      data.score !== undefined ? data.score : '',
      data.total || ''
    ]);
    return ContentService.createTextOutput(JSON.stringify({status:'ok'}))
      .setMimeType(ContentService.MimeType.JSON);
  } catch(err) {
    return ContentService.createTextOutput(JSON.stringify({status:'error',msg:err.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  return ContentService.createTextOutput('LearningHub API OK');
}
```

3. Klik **Deploy → New Deployment**
4. Type: **Web App**
5. Execute as: **Me**
6. Who has access: **Anyone**
7. Klik **Deploy** → Copy URL yang muncul

### Langkah 3 — Paste URL ke index.html
Di `index.html`, cari baris:
```javascript
const SCRIPT_URL = 'PASTE_YOUR_APPS_SCRIPT_URL_HERE';
```
Ganti dengan URL Apps Script yang di-copy tadi.

---

## Menambah Konten Modul Baru

Di `index.html`, tambahkan di bagian `const CONTENT = { ... }`:

```javascript
fin6: {
  badge: '💰 Modul 6 · Keuangan Pribadi',
  title: 'Judul Modul Baru',
  subtitle: 'Deskripsi singkat modul.',
  blocks: `<div class="cb">...</div>`,
  quiz: [
    {
      q: 'Pertanyaan quiz?',
      opts: ['Pilihan A', 'Pilihan B', 'Pilihan C', 'Pilihan D'],
      correct: 1,  // index jawaban benar (0-3)
      fb: 'Feedback setelah menjawab.'
    }
  ]
}
```

---

## Membuka Akademi Baru (Kesehatan, Mindset, Marketing)

Di bagian `const ACADEMIES = [...]`, ubah `locked: true` menjadi `locked: false` untuk akademi yang ingin dibuka.

---

## Mengganti Password HR

Di `index.html`, cari:
```javascript
if(pw === 'hr2024')
```
Ganti `hr2024` dengan password pilihan kamu.
