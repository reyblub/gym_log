# Catatan verifikasi GymLog

Tanggal: 21 Oktober 2026.

Lingkungan pemeriksaan: Linux, Flutter 3.47.7 stable, Dart 3.13.5.

| Pemeriksaan | Hasil |
| --- | --- |
| `flutter analyze` | Tidak ada issue |
| `flutter test --reporter expanded` | 9 pengujian lolos |
| `flutter build web --release` | Build berhasil |
| `dart run tool/api_smoke.dart` | Endpoint API sungguhan berhasil diakses |

Pengujian meliputi pemilihan bahasa dan gambar, pembersihan HTML, parameter
pencarian/filter/pagination, penanganan HTTP error/JSON rusak/timeout, serialisasi
favorit, kegagalan penyimpanan, pengabaian respons pencarian lama, navigasi ke
detail, simpan/hapus favorit, retry, dan reset hasil kosong.

Widget test memakai ukuran 360 × 780 serta ukuran pengujian bawaan 800 × 600.
Data pengujian di `test/test_support.dart` merupakan fixture khusus test, bukan
fallback katalog aplikasi.

Pengecekan API asli mengembalikan 8 kategori; pencarian `bench` dengan kategori
Chest mengembalikan 16 hasil; detail pertama yang diperiksa adalah ID 73,
Bench Press. Jumlah/nama ini dapat berubah sesuai pembaruan server.

## Pemeriksaan lanjutan pada perangkat mahasiswa

Build APK dan pengujian pada emulator/perangkat Android belum dilakukan dalam
lingkungan pemeriksaan ini. Jalankan project pada emulator untuk mengambil
screenshot laporan serta memeriksa koneksi dan penyimpanan perangkat asli.

Langkah manual yang disarankan:

1. Jalankan `flutter pub get`, kemudian `flutter run` dengan emulator aktif.
2. Buka katalog, cari `bench`, lalu pilih filter Dada.
3. Buka detail dan simpan satu latihan ke favorit.
4. Tutup proses app dan buka kembali; periksa favorit masih tersimpan.
5. Matikan internet lalu refresh katalog; periksa pesan error dan tombol retry.
6. Aktifkan internet dan coba kembali; periksa data tampil.
7. Buka favorit, hapus latihan, dan periksa daftar berubah.

Pengujian penyimpanan otomatis memakai SharedPreferences mock. Langkah 3–4
memeriksa penyimpanan pada perangkat Android yang dipakai untuk pengumpulan.
