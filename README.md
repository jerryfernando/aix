# aix

Berikut adalah panduan rinci untuk menginstal dan mengonfigurasi Node Exporter untuk AIX, yang akan memonitor hardware dan metrik OS di AIX menggunakan Prometheus.

Langkah 1: Mengunduh dan Menginstal Node Exporter untuk AIX
Unduh Paket .bff Node Exporter: Pertama-tama, Anda perlu mendapatkan paket .bff Node Exporter untuk AIX. Jika Anda memiliki paket tersebut, letakkan di direktori yang diinginkan di server AIX Anda.

Install Paket dengan installp: Setelah paket diunduh atau didapatkan, instal paket tersebut menggunakan perintah installp sebagai berikut:
```
installp -ac -d <package_file.bff>
Penjelasan Perintah:
```
-a: Install semua file.
-c: Cek dependensi dan keluarkan pesan peringatan jika ada konflik.
-d: Tentukan file paket yang akan diinstal (<package_file.bff>).
Setelah perintah ini dijalankan, Node Exporter akan diinstal di direktori /usr/local/bin/. Proses ini juga secara otomatis membuat SRC (System Resource Controller) service bernama node_exporter_aix.

Start Node Exporter: Setelah instalasi selesai, Node Exporter akan langsung dimulai dengan konfigurasi default. Namun, jika Anda ingin menjalankan Node Exporter secara manual atau setelah restart, Anda bisa menjalankan service dengan perintah:
```
startsrc -s node_exporter_aix
Verifikasi Node Exporter: Untuk memverifikasi apakah Node Exporter sudah berjalan dengan baik, gunakan perintah berikut:
```
```
lssrc -s node_exporter_aix
Ini akan menampilkan status Node Exporter dan memastikan bahwa service sedang berjalan.
```
Langkah 2: Mengonfigurasi Paramater Node Exporter
Jika Anda ingin mengubah konfigurasi default Node Exporter seperti mengaktifkan atau menonaktifkan kolektor tertentu atau mengubah port default, Anda dapat melakukannya dengan menambahkan parameter di service SRC.

Menambahkan Parameter SRC: Gunakan perintah chssys untuk menambahkan parameter ke service node_exporter_aix. Contoh perintah di bawah ini menunjukkan cara menambahkan kolektor CPU, disk, dan partisi serta mengubah frekuensi restart:

```
chssys -p /usr/local/bin/node_exporter_aix -s node_exporter_aix -u root -R -S -f 9 -n 15 -a "-cdP"
Penjelasan Perintah:
```
-p: Menunjukkan path ke executable (/usr/local/bin/node_exporter_aix).
-s: Nama service yang diubah (node_exporter_aix).
-u: Menjalankan service sebagai pengguna root.
-R: Autorestart service.
-S: Jalankan di saat boot.
-f 9: Restart dengan frekuensi setiap 9 detik jika gagal.
-n 15: Tetapkan prioritas CPU.
-a: Menambahkan parameter untuk mengaktifkan kolektor tertentu. Dalam contoh ini, -cdP mengaktifkan kolektor CPU (-c), disk (-d), dan partisi (-P).
Memverifikasi Konfigurasi Baru: Setelah menambahkan konfigurasi baru, pastikan bahwa perubahan tersebut diterapkan dengan benar. Restart Node Exporter dengan perintah berikut:

```
stopsrc -s node_exporter_aix
startsrc -s node_exporter_aix
```
Periksa Status Node Exporter: Setelah memulai kembali service, periksa status Node Exporter dengan perintah berikut:

```
lssrc -s node_exporter_aix
```
Langkah 3: Mengubah Port Default
Secara default, Node Exporter berjalan pada port 9100. Namun, Anda dapat mengubah port tersebut dengan menggunakan parameter -p. Sebagai contoh, jika Anda ingin Node Exporter berjalan di port 9101, Anda dapat menambahkan parameter berikut saat memulai Node Exporter:

```
./build/node_exporter_aix -p 9101
```
Atau, jika Anda telah menginstal Node Exporter menggunakan .bff package, tambahkan parameter ke service SRC seperti berikut:

```
chssys -p /usr/local/bin/node_exporter_aix -s node_exporter_aix -u root -R -S -f 9 -n 15 -a "-p 9101"
```
Langkah 4: Memonitor dan Mengecek Log Node Exporter
Jika Anda mengalami masalah atau ingin memastikan bahwa Node Exporter berjalan dengan benar, Anda dapat menggunakan perintah berikut untuk melihat log:

Melihat Log Node Exporter: Untuk melihat log dari service node_exporter_aix, gunakan perintah berikut:

```
tail -f /var/adm/ras/errlog
```
Memantau Metrik yang Dikumpulkan: Anda dapat memantau metrik yang dikumpulkan oleh Node Exporter dengan mengakses endpoint metriknya menggunakan browser atau perintah curl. Jika Node Exporter berjalan di localhost dengan port default 9100, gunakan:

```
curl http://localhost:9100/metrics
```
Ini akan menampilkan daftar metrik yang dikumpulkan oleh Node Exporter.

Langkah 5: Build Node Exporter dari Sumber (Opsional)
Jika Anda ingin membangun Node Exporter dari kode sumber, ikuti langkah-langkah berikut:

Instal Prasyarat:

AIX 7.1 atau lebih baru.
GCC (kompiler C++17) dan GNU Make dari AIX Toolbox.
bos.adt.insttools untuk packaging .bff.
Mengatur PATH: Untuk memastikan bahwa GNU Make digunakan, set PATH seperti berikut:

```
export PATH=/opt/freeware/bin:$PATH
```
Clone dan Update Git Submodule:

```
git clone <url_node_exporter_repo>
cd node_exporter
git submodule init
git submodule update
```
Build Node Exporter:

```
make
```
Menjalankan Node Exporter:

Setelah selesai dibuild, jalankan Node Exporter:

```
./build/node_exporter_aix
```
Melihat Opsi Konfigurasi: Untuk melihat semua opsi konfigurasi yang tersedia, jalankan:

```
./build/node_exporter_aix -h
```
