# Panduan laporan UTS teori — GymLog

Dokumen ini adalah panduan isi laporan. Lengkapi identitas, screenshot hasil
menjalankan aplikasi, dan catatan percobaan dari perangkat yang digunakan.

## BAB I. PENDAHULUAN

### 1.1 Latar Belakang dan Gambaran Aplikasi

Jelaskan kebutuhan pengguna untuk mencari referensi gerakan gym berdasarkan
kelompok otot dan peralatan. GymLog menyediakan katalog dari REST API wger,
detail gerakan, dan penyimpanan favorit lokal. Aplikasi dibuat menggunakan
Flutter dan Dart. Batasan UTS: katalog, pencarian/filter, detail, dan favorit.

### 1.2 Fitur Aplikasi

- Beranda dengan navigasi kelompok otot dan pilihan latihan.
- Katalog berbasis API dengan pencarian dan filter kategori.
- Detail latihan yang memuat gambar, alat, otot utama, serta panduan.
- Favorit yang disimpan pada perangkat.
- Status loading, hasil kosong, error dan retry, serta pagination.

## BAB II. PEMBAHASAN

### 2.1 Persiapan Project dan Package

Cantumkan versi Flutter/Dart dan editor yang benar-benar digunakan. Jelaskan
fungsi package `http`, `shared_preferences`, dan `html`. Tampilkan struktur
folder utama dari README. Jelaskan izin INTERNET pada manifest Android.

### 2.2 Desain User Interface

Jelaskan warna hijau sebagai aksen, teks gelap, kartu putih, dan navigasi bawah.
Empat halaman utama: beranda, katalog, detail, dan favorit. Beranda/katalog/favorit
diakses melalui NavigationBar; detail dibuka melalui Navigator.push.

Sertakan referensi mockup jika digunakan, lalu screenshot aktual berikut:

1. Beranda setelah data berhasil dimuat.
2. Katalog dan hasil pencarian kata `bench` atau kategori Dada.
3. Detail salah satu latihan beserta panduan dan atribusi.
4. Favorit setelah menyimpan latihan dan membuka ulang aplikasi.
5. Kondisi error ketika internet dimatikan lalu katalog dimuat ulang.

Beri caption seperti “Gambar 2.1 Tampilan beranda GymLog”. Status loading/error
merupakan kondisi tampilan pada halaman yang sama.

### 2.3 Implementasi Pengambilan dan Pengolahan Data dari API

Cantumkan endpoint yang benar-benar dipakai pada README. Jelaskan:

- `WgerApiService.fetchExercises`: membentuk URL HTTPS dan mengirim HTTP GET.
- `Exercise.fromApiJson`: memilih data Inggris dari `translations`, membaca
  kategori/alat/otot/gambar, dan membersihkan HTML.
- `GymLogController`: mengelola loading, data, error, debounce pencarian,
  pagination, serta mengabaikan respons pencarian yang sudah tidak relevan.
- `ExerciseDetailScreen`: FutureBuilder mengambil detail berdasarkan ID.
- `LocalFavoriteStorage`: mengubah model ke JSON dan menyimpannya lokal.

Pilih cuplikan kode pendek yang menjelaskan alur tersebut. Hasil pengujian harus
menggambarkan apa yang diamati pada perangkat; jangan mengklaim build Android
berhasil sebelum benar-benar menjalankannya.

## BAB III. PENUTUP

### 3.1 Kesimpulan

Rangkum hasil implementasi Flutter, pengambilan data dinamis melalui API,
penyajian data, navigasi, dan favorit. Sebutkan batasan sumber: konten bahasa
Inggris, gambar tidak selalu tersedia, serta kebutuhan internet untuk katalog.

### 3.2 Saran

Pengembangan UAS dapat berupa penyusunan rutinitas, pencatatan set/repetisi/beban,
database lokal, dan grafik riwayat. Sesuaikan fitur lanjutan dengan soal UAS yang
benar-benar diterbitkan. Modul nutrisi dapat menjadi pengembangan berikutnya.

## Sebelum mengumpulkan

- Isi identitas mahasiswa dan judul aplikasi.
- Gunakan screenshot hasil implementasi aktual.
- Ekspor laporan ke PDF.
- Upload source ke GitHub public dan PDF ke Google Drive.
- Uji kedua tautan melalui jendela incognito tanpa login.
