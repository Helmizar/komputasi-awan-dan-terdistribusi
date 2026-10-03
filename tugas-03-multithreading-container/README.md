# Tugas 3 (Pekan 3) — Efisiensi Proses & Kontainer

**Materi terkait:** Threading, Virtualization, Containers.

## Studi Kasus

Server FoodGo boros sumber daya karena setiap permintaan pesanan masuk diproses sebagai **proses baru yang berat** (mis. `fork()` proses OS penuh per request). Saat 100 pesanan masuk bersamaan, server kehabisan memori karena tiap proses membawa overhead-nya sendiri.

## Tugas Kelompok

1. Implementasikan **simulasi pesanan masuk** di Python (`src/order_simulator.py`) yang memproses banyak pesanan **secara konkuren memakai multithreading** (bukan multiprocessing, bukan sekuensial biasa).
2. Program harus mensimulasikan **race condition yang sengaja dibuat lalu diperbaiki** — buktikan pemahaman kalian tentang `Lock`/sinkronisasi dengan cara:
   - Jalankan dulu versi TANPA lock, tunjukkan hasil counter yang salah (screenshot/log).
   - Perbaiki dengan `threading.Lock()`, tunjukkan hasil counter yang benar.
   - Tulis perbandingan ini di `JURNAL.md`.
3. Paketkan program ke dalam **Docker container** (`Dockerfile` disediakan skeleton-nya, lengkapi bagian yang kosong).
4. Jalankan container di laptop, buktikan program tetap berjalan benar di dalam container (screenshot/video di `bukti/`).

## Skeleton yang Disediakan

- `src/order_simulator.py` — kerangka program dengan `# TODO` di bagian logika inti (worker function, penggunaan lock, agregasi hasil). **Kalian wajib mengisi bagian TODO sendiri** — ini bagian penilaian utama.
- `requirements.txt` — kosong/minimal (program ini sengaja hanya pakai standard library Python, tidak perlu dependency eksternal).
- `Dockerfile` — kerangka dengan beberapa baris `# TODO`, lengkapi agar image bisa di-build dan dijalankan.

## Cara Menjalankan (Setelah Skeleton Dilengkapi)

Tanpa Docker (langsung di laptop, untuk debugging cepat):
```bash
cd tugas-03-multithreading-container
python3 src/order_simulator.py
```

Dengan Docker (wajib untuk submission akhir):
```bash
cd tugas-03-multithreading-container
docker build -t foodgo-order-sim .
docker run --rm foodgo-order-sim
```

## Struktur Submission

```
tugas-03-multithreading-container/
├── README.md          # Analisis: race condition, perbaikan, kenapa threading (bukan multiprocessing/proses OS)
├── JURNAL.md           # Log sebelum/sesudah lock, error yang ditemui saat build Docker
├── Dockerfile
├── requirements.txt
├── src/
│   └── order_simulator.py
└── bukti/              # Screenshot/video: hasil counter salah (tanpa lock), hasil benar (dengan lock), container jalan
```

## Jawaban

1. Analisis Simulasi Race Condition & Perbaikannya
   Dalam simulasi sistem pesanan FoodGo yang kami buat, kami mendapati bahwa pemrosesan pesanan yang dijalankan secara serentak (konkuren) tanpa mekanisme sinkronisasi memicu terjadinya Race Condition. Saat beberapa thread membaca dan memperbarui variabel global (processed_count) di waktu yang bersamaan, data akan saling menimpa (overwrite). Akibatnya, dari 100 pesanan yang masuk, sistem terkadang hanya mencatat 59 atau 70 pesanan yang selesai. Perbaikannya Kami menyelesaikan masalah ini dengan mengimplementasikan objek threading.Lock(). Dengan adanya Lock, bagian kode yang rawan (saat menambahkan jumlah pesanan) dikunci alias hanya boleh diakses oleh satu thread pada satu waktu secara bergantian (mutual exclusion). Hasilnya, simulasi berjalan akurat dan selalu memproses genap 100 pesanan.

2. Analisis Efisiensi: Kenapa Menggunakan Threading dan Bukan Multiprocessing?
   Pada studi kasus, disebutkan bahwa server FoodGo boros sumber daya dan kehabisan memori saat menerima 100 pesanan bersamaan. Akar masalahnya adalah sistem lama menggunakan Proses OS (seperti fork()) untuk setiap pesanan. Membuat proses OS baru adalah tugas yang sangat berat (heavyweight). Setiap proses OS membutuhkan alokasi memori (RAM) yang terisolasi dan sumber daya yang besar dari sistem operasi untuk berjalan. Jika 100 pesanan masuk, server harus menduplikasi 100 alokasi memori penuh, yang berujung pada kehabisan kapasitas RAM dan server crash.

   Solusi dengan Multithreading:
   Kami mengubah penanganan pesanan menggunakan pendekatan Multithreading. Berbeda dengan proses OS, thread adalah unit eksekusi yang jauh lebih ringan (lightweight). Keunggulan utamanya adalah semua thread tersebut berjalan di dalam *satu proses yang sama dan berbagi ruang memori yang sama (shared memory).
   Dengan multithreading, ketika ada 100 pesanan masuk, server FoodGo cukup menjalankan 1 proses utama dan membagi tugasnya kepada 10-100 thread. Karena thread tidak menduplikasi memori, beban RAM server turun secara drastis, perpindahan tugas antar pekerja (context switching) menjadi jauh lebih cepat, dan server tidak lagi mengalami kehabisan resource meskipun diserbu oleh pesanan yang masif.

## Rubrik Penilaian (Tugas 3)

| Komponen | Bobot | Kriteria |
|---|---|---|
| Implementasi multithreading benar | 30% | Worker benar-benar konkuren (bukan `time.sleep` yang menyamarkan sekuensial), pakai `threading` |
| Bukti race condition & perbaikan lock | 25% | Ada bukti nyata (log/screenshot) sebelum & sesudah, bukan cuma klaim di teks |
| Dockerfile & eksekusi container | 20% | Image ter-build, container jalan dan hasilkan output yang sama seperti tanpa Docker |
| Analisis (kenapa threading, bukan proses berat) | 15% | Mengaitkan balik ke masalah "server boros resource" di studi kasus |
| Proses & kontribusi kelompok | 10% | `JURNAL.md`, commit history |

## Batasan Penggunaan AI (Level 2)

Kebijakan **Level 2 (AI Assisted Idea Generation & Structuring)** berlaku — lihat [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Boleh bertanya ke AI soal opsi umum menangani race condition (mis. "apa saja cara sinkronisasi thread di Python"); **tidak boleh** meminta AI menuliskan isi bagian `# TODO` di `order_simulator.py`/`Dockerfile`. Catat pemakaian AI di "Log Penggunaan AI" pada `JURNAL.md`.

- Bagian `# TODO` di `order_simulator.py` dan `Dockerfile` sengaja dikosongkan — solusi yang identik persis antar kelompok (termasuk nama variabel, komentar) akan diperiksa lebih lanjut.
- `JURNAL.md` wajib menunjukkan bukti nyata percobaan **sebelum** (race condition muncul) dan **sesudah** (`Lock()` dipasang) — bukan cuma klaim tanpa data pembanding.
