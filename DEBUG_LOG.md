# DEBUG LOG — Katalog Karya (Latihan P10)

Alat: Google Chrome DevTools (Network, Elements, Console, Sources) dan Git.
Nomor baris di bawah adalah nomor baris **sebelum** perbaikan.

---

## BUG-01 (G1): Logo di navbar tidak tampil
- **Reproduksi:** buka `http://localhost:8000/`. Di navbar muncul ikon gambar rusak, bukan logo.
- **Hipotesis 1:** berkas `logo.svg` tidak ada. **GUGUR**: berkasnya ada di `assets/logo.svg` (terlihat di Explorer).
- **Hipotesis 2:** path di tag `<img>` menunjuk folder yang salah. **TERBUKTI**.
- **Bukti DevTools (Network):** permintaan `GET /img/logo.svg` berstatus 404. Log server Python juga menampilkan `GET /img/logo.svg 404`. Screenshot: `g1-sebelum.png`.
- **Perbaikan:** `index.html:13`, `src="img/logo.svg"` diubah menjadi `src="assets/logo.svg"`. Commit `3476cd6`.
- **Uji ulang:** logo tampil dan `logo.svg` berstatus 200 di Network. Screenshot: `g1-sesudah.png`.

---

## BUG-02 (G2): Kartu tersusun satu kolom di layar lebar
- **Reproduksi:** buka halaman dengan lebar layar lebih dari 992 px. Semua kartu menumpuk ke bawah, satu per baris.
- **Hipotesis 1:** class kolom pada kartu salah (`col-lg-4`). **GUGUR**: class di `main.js:19` sudah `col-12 col-sm-6 col-lg-4`, sesuai Bootstrap.
- **Hipotesis 2:** pembungkus kartu tidak berupa grid. **TERBUKTI**.
- **Bukti DevTools (Elements):** `<div id="galeri" class="g-3">` tidak punya class `row`. Di panel Computed, `display` bernilai `block`, bukan `flex`, jadi class `col-*` tidak berlaku. Screenshot: `g2-sebelum.png`.
- **Perbaikan:** `index.html:34`, `class="g-3"` diubah menjadi `class="row g-3"`. Commit `5e09747`.
- **Uji ulang:** kartu tersusun 3 kolom di layar lebar. Screenshot: `g2-sesudah.png`.

---

## BUG-04 (G4): Teks "Menampilkan … karya" tidak berubah
- **Reproduksi:** ketik `Video` di kotak cari. Kartu tinggal 2, tetapi teks tetap "Menampilkan 6 dari 6 karya".
- **Hipotesis 1:** fungsi `saring()` mengembalikan hasil yang salah. **GUGUR**: kartu yang tampil sudah benar (2), jadi hasil saringan tidak bermasalah.
- **Hipotesis 2:** teks jumlah memakai variabel yang salah. **TERBUKTI**.
- **Bukti DevTools (Sources, breakpoint):** breakpoint dipasang di `main.js:38`. Saat berhenti, panel Scope menunjukkan `hasil.length = 2`, padahal template string memakai `KARYA.length` (6). Screenshot: `g4-sebelum.png`.
- **Perbaikan:** `main.js:38`, `${KARYA.length} dari` diubah menjadi `${hasil.length} dari`. Commit `7e19f19`.
- **Uji ulang:** mengetik `Video` menampilkan "Menampilkan 2 dari 6 karya", `xyz` menampilkan "0 dari 6", dan kotak kosong menampilkan "6 dari 6". Screenshot: `g4-sesudah.png`.

---

## BUG-05 (G5): Tombol Detail mati setelah melakukan pencarian
- **Reproduksi:** saat halaman baru dibuka, tombol Detail berfungsi. Ketik `Animasi` di kotak cari, lalu klik Detail: tidak ada yang terjadi.
- **Hipotesis 1:** atribut `data-id` salah sehingga `KARYA.find` gagal. **GUGUR**: kalau itu penyebabnya akan ada error di Console, tetapi tidak ada error. Tombolnya sama sekali tidak bereaksi.
- **Hipotesis 2:** tombol hasil render ulang tidak punya event listener. **TERBUKTI**.
- **Bukti DevTools (Elements, Event Listeners):** sebelum pencarian, tombol Detail punya listener `click`. Setelah mengetik di kotak cari, tombol Detail yang baru tidak punya listener apa pun. Penyebabnya, `tampil()` memakai `replaceChildren` yang membuat tombol baru, sedangkan `pasangDetail()` hanya dipanggil sekali (`main.js:52`). Screenshot: `g5-sebelum.png`.
- **Perbaikan:** `main.js:41-49` dan `main.js:52`. Fungsi `pasangDetail()` dan pemanggilannya dihapus, diganti satu listener `click` pada `galeri` dengan event delegation (`e.target.closest('button[data-id]')`), sehingga tetap berfungsi walau isi galeri dirender ulang. Commit `a829419`.
- **Uji ulang:** tombol Detail berfungsi setelah mencari `Animasi` maupun `Video`. Screenshot: `g5-sesudah.png`.

---

## BUG-06 (G6): Pencarian tidak menemukan karya jika diketik huruf kecil
- **Reproduksi:** ketik `animasi` (huruf kecil) di kotak cari: tidak ada kartu. Ketik `Animasi`: muncul 2 kartu.
- **Hipotesis 1:** `trim()` pada kata kunci merusak pencarian. **GUGUR**: kata `animasi` tidak punya spasi, jadi `trim()` tidak berpengaruh, dan hasilnya tetap kosong.
- **Hipotesis 2:** perbandingan teks peka huruf besar-kecil. **TERBUKTI**.
- **Bukti DevTools (Console):** `'Animasi Logo Himpunan'.includes('animasi')` bernilai `false`. Screenshot: `g6-sebelum.png`.
- **Perbaikan:** `main.js:14`, `k.judul.includes(kata)` diubah menjadi `k.judul.toLowerCase().includes(kata.toLowerCase())`. Commit `7e19f19`.
- **Uji ulang:** `animasi`, `ANIMASI`, dan `VIDEO` masing-masing menampilkan 2 kartu. Screenshot: `g6-sesudah.png`.