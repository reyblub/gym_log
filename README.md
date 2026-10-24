# GymLog — UTS Pemrograman Mobile

GymLog adalah aplikasi Flutter untuk menjelajahi latihan gym, membaca detail
gerakan, dan menyimpan latihan favorit di perangkat. Katalog latihan berasal
dari REST API publik **wger**, bukan daftar latihan yang ditulis langsung dalam
kode aplikasi.

## Fitur versi UTS

- Beranda dengan kelompok otot dan pilihan latihan yang diperoleh dari API.
- Katalog latihan dengan pencarian di server, filter kategori, dan pagination.
- Detail latihan: gambar, kategori, alat, otot utama, dan panduan dari API.
- Favorit tersimpan lokal dan tetap ada setelah aplikasi ditutup/dibuka lagi.
- Tampilan loading, hasil kosong, gagal koneksi, dan tombol coba lagi.
- Pull to refresh pada beranda dan katalog.
- Informasi sumber, penulis, dan lisensi konten di halaman detail.
- Layout dengan lebar isi terbatas agar nyaman di ponsel maupun layar besar.

Nama/panduan latihan mengikuti bahasa Inggris pada sumber. Label navigasi dan
kategori menggunakan bahasa Indonesia. Sebagian entri sumber tidak memiliki
gambar/panduan lengkap; aplikasi menampilkan ikon atau keterangan pengganti.
Tampilan dan isi gambar mengikuti data API, sehingga dapat berbeda dari mockup.

## Menjalankan di Android Studio / VS Code

1. Ekstrak paket ini. Gunakan folder sederhana, misalnya
   `D:\FlutterProjects\gym_log`. Hindari memindahkan project ke folder dengan
   spasi jika sebelumnya Gradle di perangkat mengalami masalah path.
2. Buka **folder gym_log yang berisi pubspec.yaml** sebagai folder project.
3. Jalankan emulator Android, misalnya Pixel 7a. Tunggu sampai layar Android siap.
4. Buka terminal di folder project, lalu jalankan:

   ```bash
   flutter pub get
   flutter run
   ```

5. Jika muncul pilihan perangkat, pilih emulator Android. Untuk melihat ID-nya:

   ```bash
   flutter devices
   flutter run -d ID_PERANGKAT
   ```

Setelah aplikasi berjalan, tekan `r` di terminal untuk hot reload. Gunakan `R`
untuk hot restart. Emulator membutuhkan akses internet untuk katalog dan gambar.

Project menyertakan platform Android dan Web. Preview di Chrome juga dapat dicoba:

```bash
flutter run -d chrome
```

Versi toolchain yang dipakai untuk pemeriksaan dicatat di `docs/VERIFIKASI.md`.
Constraint kode: Dart >=3.5 dan Flutter >=3.24. Jika SDK lebih lama, sesuaikan SDK
Flutter sebelum menjalankan. Project ini tidak memerlukan API key atau akun wger.

## Struktur kode

```text
lib/
  main.dart                         # entry point, navigasi tab dan route detail
  app_theme.dart                    # warna dan tema Material 3
  models/exercise.dart              # JSON → model, bahasa, kategori, lisensi
  services/exercise_api.dart         # HTTP GET ke wger, timeout, error
  services/favorite_storage.dart     # simpan/baca favorit lokal
  controllers/gym_log_controller.dart # state loading/data/error, pencarian
  screens/
    home_screen.dart
    catalog_screen.dart
    exercise_detail_screen.dart
    favorites_screen.dart
  widgets/common_widgets.dart       # kartu, gambar, tombol favorit, status
test/                               # pengujian tanpa koneksi internet
tool/api_smoke.dart                 # pengecekan API sungguhan
docs/                               # panduan laporan dan verifikasi
```

State menggunakan `ChangeNotifier` dan `AnimatedBuilder` bawaan Flutter. Detail
menggunakan `FutureBuilder`. HTTP dan penyimpanan tidak diletakkan di dalam widget.

## Package

| Package | Kegunaan |
| --- | --- |
| `http` | Permintaan HTTP GET |
| `shared_preferences` | Menyimpan daftar favorit sederhana di perangkat |
| `shared_preferences_android` | Implementasi Android yang dipatok ke 2.3.4 untuk kompatibilitas toolchain |
| `html` | Membaca HTML dan mengubahnya menjadi teks yang dapat ditampilkan |
| `cupertino_icons` | Font ikon standar untuk komponen adaptif Flutter |
| `flutter_test` | Menguji parser, HTTP, penyimpanan, controller, dan navigasi |

`SharedPreferences` dipakai untuk favorit yang jumlahnya kecil. Untuk fitur UAS
seperti catatan sesi/set/repetisi yang lebih kompleks, penyimpanan dapat diganti
dengan database lokal melalui interface `FavoriteStorage`/repository baru.

## Endpoint API

Base URL: `https://wger.de/api/v2/`

| Tujuan | Endpoint |
| --- | --- |
| Katalog | `exerciseinfo/?language__code=en&limit=24&offset=0&ordering=id` |
| Pencarian | `exerciseinfo/?language__code=en&name__search=bench` |
| Filter kategori | `exerciseinfo/?language__code=en&category=11` |
| Detail | `exerciseinfo/57/` |
| Daftar kategori | `exercisecategory/?limit=100` |

Dokumentasi: https://wger.readthedocs.io/en/latest/api/api.html

Schema: https://wger.de/api/v2/schema

Parameter pencarian yang dipakai adalah **`name__search`**. Parameter `search`
atau `name` biasa tidak digunakan karena tidak menerapkan filter pada server
yang diperiksa. Ketika pengguna mengetik, aplikasi menunggu 400 ms sebelum
meminta data. Respons pencarian lama diabaikan jika pengguna sudah mengganti
kata kunci. Pagination memakai offset dari respons server, lalu tetap membuat
permintaan HTTPS ke host wger.

## Alur pengambilan data

1. Controller memanggil service ketika aplikasi dibuka atau filter berubah.
2. Service mengirim HTTP GET dan memeriksa status respons.
3. JSON katalog dibaca dari `results`; jumlah hasil dari `count`; halaman berikutnya
   dari `next`.
4. `Exercise.fromApiJson` memilih terjemahan Inggris, membaca kategori/alat/otot,
   memilih gambar utama, dan membersihkan tag HTML.
5. Controller memberi tahu UI untuk menampilkan loading, hasil, kondisi kosong,
   atau error.
6. Detail meminta kembali data berdasarkan ID. Jika gagal, data yang sudah
   dimuat dari katalog/favorit masih ditampilkan dengan pemberitahuan.
7. Tombol favorit menyimpan salinan model yang diperlukan, termasuk atribusi.
   UI baru mengubah status bookmark setelah penyimpanan berhasil.

Tidak ada fallback data dummy pada katalog produksi. Pada kondisi offline,
katalog menampilkan pesan error. Teks favorit yang telah disimpan dapat dibaca
kembali; gambar tetap memerlukan internet. Uninstall atau clear app data akan
menghapus favorit lokal.

## Pemeriksaan dan build

```bash
flutter analyze
flutter test
dart run tool/api_smoke.dart
```

Perintah terakhir memakai internet dan memeriksa endpoint asli. Test otomatis
menggunakan fixture khusus pengujian sehingga tidak bergantung pada server.

Build APK pada komputer yang telah memiliki Android SDK/JDK:

```bash
flutter build apk --release
```

Hasilnya berada di `build/app/outputs/flutter-apk/app-release.apk`. Izin internet
sudah dipasang di manifest utama untuk build debug maupun release.

## Upload GitHub

1. Buat repositori baru **Public**, misalnya `gym_log`. Buat repo kosong agar
   tidak terjadi konflik README pertama.
2. Dari terminal folder project:

   ```bash
   git init
   git add .
   git commit -m "Build GymLog for Mobile Programming UTS"
   git branch -M main
   git remote add origin https://github.com/USERNAME/gym_log.git
   git push -u origin main
   ```

Ganti `USERNAME` dengan akun sendiri. Jangan upload `.dart_tool`, `build`, atau
`android/local.properties`; berkas tersebut sudah masuk `.gitignore`.

## Dokumentasi tugas

`docs/PANDUAN_LAPORAN_UTS.md` memetakan implementasi ini ke struktur laporan teori.
Gunakan screenshot aplikasi yang benar-benar dijalankan untuk hasil implementasi.
Gambar mockup adalah referensi perancangan UI.

Keluaran tugas yang perlu disiapkan setelah menjalankan dan mendokumentasikan app:

1. URL repositori GitHub public.
2. URL laporan PDF di Google Drive dengan akses Anyone with the link, Viewer.

## Sumber dan lisensi

Kode aplikasi GymLog dalam paket ini menggunakan lisensi MIT pada `LICENSE`.
Data teks/gambar latihan berasal dari **wger** dan memiliki lisensi tersendiri
sebagaimana dikembalikan oleh API pada setiap entri. Hak konten wger tidak
diubah menjadi MIT. Halaman detail menyertakan sumber, penulis, serta lisensi
teks dan gambar yang dipakai. Format deskripsi diubah dari HTML menjadi teks;
isi tidak diterjemahkan otomatis. Lihat juga https://github.com/wger-project/wger.
