# Tugas 2 (Pekan 2) — Perancangan Arsitektur untuk FoodGo

**Materi terkait:** Architectural style (Layered, SOA, Peer-to-Peer, Publish-Subscribe).

## Studi Kasus

Melanjutkan Tugas 1: FoodGo butuh sistem yang **decoupled** agar tim kurir dan tim resto tidak saling mengganggu ketika salah satu modul diperbarui/deploy ulang. Saat ini semua modul (pesanan, pembayaran, notifikasi kurir, katalog resto) berjalan sebagai satu aplikasi monolitik — sekali deploy, semua modul ikut restart dan berisiko downtime total.

## Tugas Kelompok

1. Pilih **satu** gaya arsitektur utama: **Service-Oriented Architecture (SOA)** atau **Publish-Subscribe**. Boleh dikombinasikan (mis. SOA untuk service inti + Pub-Sub untuk notifikasi), tapi harus dijustifikasi kenapa kombinasi ini yang dipilih.
   
   **JAWAB :**

   Kami memilih Service-Oriented Architecture (SOA) sebagai arsitektur utama dan Publish-Subscribe sebagai pendukung komunikasi antar-service. Pemilihan ini disesuaikan dengan permasalahan pada sistem FoodGo yang sebelumnya masih menggunakan arsitektur monolitik, sehingga setiap modul masih saling bergantung dan ketika dilakukan perubahan atau deployment pada satu bagian, modul lainnya juga dapat ikut terdampak.Dengan menggunakan SOA, modul utama pada FoodGo seperti Pesanan, Pembayaran, Kurir/Notifikasi, dan Katalog Resto dapat dipisahkan menjadi beberapa service. Setiap service memiliki tugas masing-masing sehingga pengembangan atau perubahan pada satu service tidak harus mengubah seluruh sistem.
Selain itu, kami menggunakan konsep Publish-Subscribe untuk menangani komunikasi yang bersifat asinkron, terutama pada proses pengiriman event dan notifikasi. Contohnya ketika pesanan berhasil dibuat, Service Pesanan dapat mengirimkan event melalui Message Broker. Service Kurir/Notifikasi yang membutuhkan informasi tersebut dapat menerima event tanpa harus berkomunikasi secara langsung dengan Service Pesanan.
Dengan menggunakan SOA, modul utama pada FoodGo seperti Pesanan, Pembayaran, Kurir/Notifikasi, dan Katalog Resto dapat dipisahkan menjadi beberapa service. Setiap service memiliki tugas masing-masing sehingga pengembangan atau perubahan pada satu service tidak harus mengubah seluruh sistem.

3. Gambarkan minimal 4 komponen berikut dan interaksinya: modul Pesanan, modul Pembayaran, modul Kurir/Notifikasi, modul Katalog Resto (dan message broker/API gateway jika relevan).
```mermaid
graph TD
    Client[Aplikasi Pelanggan] -->|1. HTTP POST - Sinkron| Gateway[API Gateway]
    Gateway -->|2. HTTP Forward - Sinkron| OrderSvc[Service Pesanan]
    
    OrderSvc -->|3. RPC Request - Sinkron| PaymentSvc[Service Pembayaran]
    
    OrderSvc -->|4. Publish Event - Asinkron| Broker[(Message Broker)]
    
    Broker -.->|5a. Subscribe Event - Asinkron| RestoSvc[Service Katalog Resto]
    Broker -.->|5b. Subscribe Event - Asinkron| CourierSvc[Service Notifikasi Kurir]
    
    RestoSvc -->|6. Kirim Notif ke Resto| AppResto[Aplikasi Mitra Resto]
    CourierSvc -->|7. Tugaskan Kurir| AppCourier[Aplikasi Kurir]
```
3. Jelaskan alur satu skenario penuh secara end-to-end di diagram (misalnya: pelanggan buat pesanan → bayar → resto terima notifikasi → kurir ditugaskan) — tunjukkan komponen mana berkomunikasi dengan siapa, dan **jenis komunikasinya** (sinkron/asinkron, request-response/event).
   
   **JAWAB :**

   Berikut adalah alur komunikasi ketika pelanggan membuat pesanan hingga pesanan diterima oleh restoran dan kurir:
   Membuat Pesanan : Pelanggan menekan tombol "Pesan", aplikasi mengirimkan request ke API Gateway yang diteruskan ke Service Pesanan.
   Validasi Pembayaran : Service Pesanan melakukan komunikasi sinkron (request-response) dengan Service Pembayaran untuk memotong saldo e-wallet pelanggan. Service Pesanan akan "menunggu"  hingga Service Pembayaran membalas sukses/gagal.
   Publish Event : Setelah pembayaran sukses, Service Pesanan tidak memanggil Service Resto/Kurir secara langsung. Sebaliknya, ia mengirim (publish) pesan "OrderPaid" (Pesanan Dibayar) ke Message Broker , lalu langsung merespons "Pesanan Berhasil" ke layar HP pelanggan.
   Subscribe Event : Service Katalog Resto dan Service Notifikasi Kurir yang terhubung ke Message Broker akan mendeteksi event "OrderPaid" tersebut.
   Eksekusi Paralel: Service Resto langsung meneruskan pesanan ke layar dapur mitra resto, sementara di saat yang bersamaan Service Kurir mengeksekusi algoritma pencarian kurir terdekat. Keduanya berjalan paralel tanpa mengganggu jalannya Service Pesanan.
   
4. Analisis tertulis: kenapa gaya ini mengatasi masalah *coupling* dari Tugas 1, dan apa trade-off-nya (mis. Pub-Sub menambah kompleksitas debugging karena alur tidak linear).
   A. Mengatasi Masalah Coupling (Keterikatan Beban & Waktu) Kombinasi SOA dan Publish-Subscribe terbukti mampu mengatasi masalah coupling (ketergantungan erat) pada sistem monolitik di Tugas 1, melalui dua cara:

Isolasi Kegagalan (Fault Isolation) via SOA: Karena setiap modul sekarang berjalan sebagai servis yang terpisah, jika Service Notifikasi Kurir tiba-tiba crash karena beban request yang tinggi, CPU dan RAM milik Service Pesanan tidak akan ikut tersedot. Pelanggan tetap bisa melakukan pemesanan dan pembayaran dengan lancar.
Decoupling Waktu & Logika via Pub-Sub: Saat Service Pesanan selesai memotong saldo, ia tidak perlu menunggu balasan dari Service Resto atau Kurir. Ia cukup menaruh pesan di Message Broker. Seandainya Service Resto sedang down atau sedang di-deploy ulang (di-update), pesan tersebut akan tersimpan aman di Message Broker. Saat Service Resto nyala kembali, ia tinggal mengambil pesan yang tertunda tersebut. Tidak ada lagi sistem yang "menunggu selamanya" seperti di skenario awal.

B. Analisis Trade-off (Risiko dari Arsitektur Baru) Walaupun sistem menjadi lebih kuat dan mandiri, arsitektur yang terdistribusi ini membawa beberapa kelemahan dan kompleksitas baru yang harus dikelola oleh tim FoodGo:

Kompleksitas Debugging dan Tracing (Pelacakan Error): Pada aplikasi monolitik, melacak pesanan yang gagal sangat mudah karena semua log berada di satu tempat yang berurutan. Dengan skema Pub-Sub, alurnya tidak lagi linear. Jika restoran menelepon CS karena tidak menerima pesanan pelanggan, tim developer harus mencari error di banyak tempat: apakah gagal di Service Pesanan? Menyangkut di API Gateway? Macet di Message Broker? Atau Service Katalog Resto-nya yang rusak?
Latensi Jaringan Tambahan (Network Overhead): Komunikasi antar-servis yang dulunya hanya berupa pemanggilan fungsi internal (sangat cepat), kini harus melewati jaringan (HTTP/RPC) dan perantara (Message Broker). Ini menambah risiko terkait gangguan jaringan (packet loss atau koneksi lambat).
Inkonsistensi Data Sementara (Eventual Consistency): Karena proses pengiriman notifikasi ke kurir dan resto berjalan secara asinkron (di latar belakang), ada jeda waktu kecil di mana data pelanggan di aplikasi mungkin sudah berstatus "Dibayar", tapi sistem resto belum menerimanya. Selain itu, tim juga harus menangani kasus jika pesan dari broker tidak sengaja terkirim dua kali agar kurir yang ditugaskan tidak ganda (idempotency).

## Cara Membuat Diagram (Gratis, Cukup Laptop)

Tidak perlu software berbayar. Dua opsi:

**Opsi A — Mermaid di dalam Markdown (disarankan).** Ditulis sebagai teks biasa di `README.md`, otomatis dirender jadi diagram oleh GitHub — tidak perlu install apa pun.

````markdown
```mermaid
graph LR
  Client[Pelanggan] -->|HTTP request pesan| OrderSvc[Service Pesanan]
  OrderSvc -->|RPC sinkron| PaymentSvc[Service Pembayaran]
  OrderSvc -->|publish event OrderCreated| Broker[(Message Broker)]
  Broker -->|subscribe| NotifSvc[Service Notifikasi Kurir]
  Broker -->|subscribe| RestoSvc[Service Katalog Resto]
```
````

**Opsi B — draw.io / diagrams.net** (gratis, jalan di browser tanpa akun, atau app desktop offline di [app.diagrams.net](https://app.diagrams.net/)). Ekspor sebagai `.png` dan simpan di folder `diagram/`.

## Struktur Submission

```
tugas-02-perancangan-arsitektur/
├── README.md          # Analisis + diagram Mermaid (jika Opsi A) atau referensi ke diagram/
├── JURNAL.md
└── diagram/            # File .png/.drawio jika pakai Opsi B
```

## Rubrik Penilaian (Tugas 2)

| Komponen | Bobot | Kriteria |
|---|---|---|
| Ketepatan pemilihan gaya arsitektur | 20% | Justifikasi SOA/Pub-Sub sesuai kebutuhan *decoupling* di skenario |
| Kelengkapan & kejelasan diagram | 30% | Semua komponen kunci ada, jenis komunikasi (sinkron/asinkron) jelas ditandai |
| Analisis trade-off | 30% | Bukan hanya kelebihan — kekurangan/kompleksitas baru juga dibahas |
| Proses & kontribusi kelompok | 20% | `JURNAL.md`, commit history |

## Batasan Penggunaan AI (Level 2)

Kebijakan **Level 2 (AI Assisted Idea Generation & Structuring)** berlaku — lihat [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Boleh memakai AI untuk brainstorming komponen apa saja yang umum ada di gaya arsitektur SOA/Pub-Sub; **tidak boleh** meminta AI menggambar diagram final atau menuliskan analisis trade-off yang tinggal ditempel. Catat pemakaian AI di "Log Penggunaan AI" pada `JURNAL.md`.

- Diagram Mermaid/draw.io yang "terlalu generik" (identik dengan contoh tutorial di internet tanpa penyesuaian ke kasus FoodGo) akan dinilai rendah pada komponen kelengkapan & kejelasan diagram.
