# Setup Google Sheets — Konfirmasi Kehadiran

Ikuti langkah berikut **satu kali** untuk menghubungkan form ke Google Sheets.

---

## Langkah 1 — Buat Google Spreadsheet

1. Buka [sheets.google.com](https://sheets.google.com) → buat spreadsheet baru
2. Beri nama: `Konfirmasi Kehadiran Arik & Dania`
3. Di **baris pertama (header)**, isi kolom berikut persis seperti ini:

| A | B | C | D | E |
|---|---|---|---|---|
| Timestamp | Nama | Jumlah Tamu | Kehadiran | Ucapan & Doa |

---

## Langkah 2 — Buat Google Apps Script

1. Di spreadsheet, klik menu **Ekstensi → Apps Script**
2. Hapus semua kode yang ada, ganti dengan kode berikut:

```javascript
function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    const data  = JSON.parse(e.postData.contents);

    sheet.appendRow([
      data.timestamp || new Date().toLocaleString('id-ID', { timeZone: 'Asia/Jakarta' }),
      data.nama      || '',
      data.jumlah    || '',
      data.hadir     || '',
      data.ucapan    || '',
    ]);

    return ContentService
      .createTextOutput(JSON.stringify({ status: 'ok' }))
      .setMimeType(ContentService.MimeType.JSON);

  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'error', message: err.message }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

// Fungsi test — jalankan manual dari Apps Script editor untuk cek koneksi
function testInsert() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  sheet.appendRow(['2026-11-01 10:00:00 WIB', 'Test Tamu', '2', 'Insyaallah Hadir', 'Barakallahu fiikum']);
}
```

3. Klik **Simpan** (ikon disket atau Ctrl+S)

---

## Langkah 3 — Deploy sebagai Web App

1. Klik tombol **Deploy → New deployment**
2. Klik ikon ⚙️ di samping "Select type" → pilih **Web app**
3. Isi konfigurasi:
   - **Description**: `Konfirmasi Kehadiran Wedding`
   - **Execute as**: `Me (email kamu)`
   - **Who has access**: **Anyone** ← wajib ini
4. Klik **Deploy**
5. Setujui permission yang diminta (klik "Allow")
6. **Copy URL** yang muncul — bentuknya seperti:
   ```
   https://script.google.com/macros/s/AKfycb.../exec
   ```

---

## Langkah 4 — Pasang URL di Kode

Buka file `src/pages/konfirmasi.astro`, cari baris:

```javascript
const APPS_SCRIPT_URL = 'PASTE_YOUR_APPS_SCRIPT_URL_HERE';
```

Ganti `PASTE_YOUR_APPS_SCRIPT_URL_HERE` dengan URL dari langkah 3.

Contoh:
```javascript
const APPS_SCRIPT_URL = 'https://script.google.com/macros/s/AKfycb.../exec';
```

---

## Langkah 5 — Test

1. Buka halaman `/konfirmasi` di browser
2. Isi form dan submit
3. Cek Google Sheets — data baru harus muncul dalam hitungan detik

Jika ingin test tanpa membuka web, jalankan fungsi `testInsert()` langsung dari editor Apps Script.

---

## Catatan Penting

- Setiap kali mengubah kode Apps Script, buat **deployment baru** (bukan edit yang lama) dan update URL-nya di kode
- Spreadsheet bisa dibagikan ke panitia lain dengan klik **Share** di Google Sheets
- Data masuk secara real-time, tidak ada delay
