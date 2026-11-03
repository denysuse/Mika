# LAPIS-REVISIONS — Fork pengaman Mika oleh Lapis untuk Densus

Fork dari https://github.com/wangzan101/Mika (Apache-2.0), dibuat 3 Nov 2026
setelah audit statis oleh Lapis. Tujuan fork: menonaktifkan mekanisme
"override izin" Mika sambil mempertahankan fungsi agennya.

## Hasil audit ringkas (statis, tanpa menjalankan APK)

- Tidak ditemukan backend milik penulis, telemetri, atau SDK analitik.
  Lalu lintas keluar hanya ke penyedia model yang dikonfigurasi pengguna
  (OpenAI, OpenRouter, xAI, Gemini, BigModel/GLM), pemeriksaan versi ke
  GitHub Releases, dan layanan suara opsional (Doubao).
- Kekuatan bawaan desainnya (bukan disembunyikan): aksesibilitas maksimal
  (membaca isi layar semua aplikasi, melakukan gestur, mengambil tangkapan
  layar), klien ADB bawaan yang berpasangan dengan adbd HP sendiri
  (debugging nirkabel), rootfs Alpine Linux di dalam proot untuk sandbox
  perkakas, dan isi layar mengalir ke penyedia LLM awan pilihan pengguna.

## Yang diubah di fork ini (potensi override izin)

1. `RecoveryPermissions.kt`
   - Cabang pemberian `WRITE_SECURE_SETTINGS` kepada diri sendiri saat
     kemampuan dipulihkan diganti `emptyList()`: fork ini tidak pernah
     memberi dirinya izin mengubah pengaturan aman.
   - Kemampuan `RecoveryCapability.Accessibility` menjadi no-op: aplikasi
     tidak lagi menulis ulang dirinya ke daftar layanan aksesibilitas
     yang aktif. Bila aksesibilitas dicabut pengguna atau sistem, hanya
     pengguna yang dapat mengaktifkannya lagi dari Pengaturan.
   - `restartAccessibility()` langsung selesai tanpa aksi: perbaikan dan
     penyambungan ulang aksesibilitas otomatis (AccessibilityRepair)
     dinonaktifkan.
2. `AndroidManifest.xml`
   - Deklarasi `WRITE_SECURE_SETTINGS` dihapus, sehingga izin itu bahkan
     tidak dapat diberikan lewat shell.
   - Deklarasi `REQUEST_INSTALL_PACKAGES` dihapus: aplikasi tidak dapat
     memasang APK; pembaruan dilakukan dengan mengunduh manual dari
     halaman rilis lalu memasang seperti aplikasi biasa.

## Risiko sisa yang tetap ada (sifat kategorinya)

- Aksesibilitas yang diaktifkan manual tetap dapat membaca dan
  mengoperasikan semua aplikasi. Jangan aktifkan di HP yang memuat data
  nasabah/keuangan tanpa pertimbangan khusus; idealnya HP atau profil
  terpisah.
- Pasangan ADB tetap tersedia sebagai fitur; tanpa revisi di atas pun
  pasangan itu memberi kanal shell tingkat sistem. Di fork ini kanal
  tersebut tidak lagi dipakai untuk memulihkan izin secara otomatis.
- Isi layar yang diproses agen dikirim ke penyedia model yang dipilih
  pengguna. Pilih penyedia dan kunci API dengan sadar.

Catatan: revisi ini hasil edit statis terarah dan belum diuji dengan
build Gradle penuh; verifikasi build pertama akan mengonfirmasi tidak
ada rujukan yang tertinggal.
