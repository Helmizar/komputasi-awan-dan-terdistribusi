# Tugas 1 (Pekan 1) — Identifikasi Masalah & Pitfall Sistem Terdistribusi

**Materi terkait:** Definisi sistem terdistribusi, tujuan desain (transparansi, skalabilitas, keterbukaan), *Fallacies of Distributed Computing* (pitfall klasik).

## Studi Kasus: FoodGo

Startup **FoodGo** (aplikasi pesan-antar makanan) mengalami kegagalan sistem saat pesanan melonjak (misalnya jam makan siang atau saat promo besar). Gejala yang dilaporkan tim engineering FoodGo:

- Aplikasi jadi sangat lambat, beberapa permintaan *timeout*.
- Server backend kadang *crash* total dan perlu di-restart manual.
- Tim menemukan bahwa kode mereka menulis asumsi seperti `# network is always reliable, no need for retry` dan tidak ada *timeout* sama sekali pada pemanggilan antar service (modul pesanan memanggil modul pembayaran dan menunggu tanpa batas waktu).
- Saat trafik naik, satu server yang menangani semua modul (pesanan, pembayaran, notifikasi kurir) kewalahan karena semuanya berjalan di satu proses monolitik yang sama.

Ini merupakan gejala klasik dari **kesalahan asumsi tentang jaringan dan skala** yang terkenal di literatur sebagai *Fallacies of Distributed Computing* (Peter Deutsch et al.), ditambah masalah desain terkait skalabilitas.

## Tujuan Pembelajaran

Setelah tugas ini, kelompok harus mampu:
1. Mengidentifikasi asumsi keliru spesifik (bukan generik) yang menyebabkan kegagalan sistem terdistribusi.
2. Mengaitkan tiap pitfall dengan **gejala konkret** di skenario (bukan sekadar mengutip definisi buku).
3. Mengusulkan solusi desain awal yang realistis, dengan trade-off yang disadari (bukan solusi "pasang cloud lebih besar" tanpa analisis).

## Tugas Kelompok

1. **Identifikasi minimal 3 pitfall utama** yang dialami FoodGo dari daftar *Fallacies of Distributed Computing* (referensi: "the network is reliable", "latency is zero", "bandwidth is infinite", "the network is secure", "topology doesn't change", "there is one administrator", "transport cost is zero", "the network is homogeneous") **DAN/ATAU** masalah desain sistem terdistribusi lain yang relevan (mis. *single point of failure* karena arsitektur monolitik).
2. Untuk **tiap pitfall**, tulis:
   - Kutipan/paraphrase bagian skenario yang menunjukkan pitfall ini terjadi.
   - Penjelasan **kenapa** asumsi ini keliru dalam sistem terdistribusi nyata.
   - Dampak konkret ke FoodGo (mis. "karena tidak ada timeout, satu service pembayaran yang lambat membuat seluruh thread modul pesanan tertahan, akhirnya server kehabisan resource").
3. Usulkan **solusi desain awal** (tingkat konsep, bukan kode) untuk tiap pitfall — misalnya: timeout + retry dengan backoff untuk asumsi jaringan reliabel, circuit breaker, pemisahan modul jadi service terpisah, dsb.
4. Diskusikan **satu trade-off** dari solusi yang diusulkan (solusi tidak gratis — misalnya retry bisa memperparah beban saat *cascading failure*).

## Langkah Kerja yang Disarankan

1. Kelompok diskusi tatap muka/panggilan (bukan hanya chat teks) untuk membedah skenario bersama — dokumentasikan poin diskusi di `JURNAL.md`.
2. Tiap anggota mengambil 1 pitfall sebagai tanggung jawab utama (tulis analisisnya sendiri di `README.md`, dengan nama di bagian yang ditulis).
3. Gabungkan hasil, diskusikan solusi desain bersama sebagai kelompok.
4. Review silang: tiap anggota membaca dan mengomentari analisis rekan sebelum submit (catat di `JURNAL.md`).

## Struktur Submission

```
tugas-01-identifikasi-masalah-pitfall/
├── README.md      # Isi dengan template ANALISIS-TEMPLATE.md di bawah
├── JURNAL.md       # Log diskusi & proses berpikir kelompok
└── bukti/          # (opsional untuk tugas ini) screenshot diskusi/whiteboard
```

Gunakan [`ANALISIS-TEMPLATE.md`](ANALISIS-TEMPLATE.md) sebagai kerangka — salin isinya ke `README.md` kelompok kalian lalu isi bagian `[...]`.

# Tugas 1 — Analisis Pitfall FoodGo

**Kelompok:** [nama kelompok]

| Nama | NIM | Kontribusi |
|---|---|---|
| Fahmi Fajar Maulana | 103072400069 | [pitfall/bagian yang dikerjakan] |
| Ical Helmizar Tambunan | 103072400074 | Latency Is Zero |
| Ulil Albab An-Nuha | 103072430017 | The Network is Reliable |

## Pitfall 1: [nama pitfall] — ditulis oleh [nama]

**Bukti di skenario:** [kutip/paraphrase bagian skenario]

**Kenapa ini keliru:** [penjelasan]

**Dampak ke FoodGo:** [mekanisme kegagalan konkret]

**Solusi desain awal:** [usulan solusi]

**Trade-off:** [apa yang dikorbankan/risiko dari solusi ini]

---

## Pitfall 2: Latency Is Zero — ditulis oleh ICAL HELMIZAR TAMUBUNAN

**Bukti di skenario:** Di sistem FoodGo, belum ada pengaturan timeout saat satu service memanggil service lainnya. Pada skenario disebutkan bahwa “modul pesanan memanggil modul pembayaran dan menunggu tanpa batas waktu.” Akibatnya, ketika jumlah pesanan meningkat, proses di aplikasi menjadi semakin lambat karena banyak permintaan yang terus menunggu. Kondisi ini bahkan menyebabkan server mengalami crash total dan harus di-restart secara manual.

**Kenapa ini keliru:** Karena komunikasi antar jaringan pasti membutuhkan waktu, jadi respons dari server tidak selalu bisa diterima dengan cepat. Waktu respons juga bisa dipengaruhi oleh kondisi jaringan dan beban pada server yang dituju. Kalau waktu tunggu tidak dibatasi, ketika server tujuan sedang bermasalah atau lambat, aplikasi yang memanggilnya akan ikut menunggu terlalu lama dan akhirnya bisa terhenti.

**Dampak ke FoodGo:** Saat jam makan siang, jumlah pesanan meningkat sehingga modul pembayaran menjadi lebih lambat. Karena tidak ada timeout, banyak thread pada modul pesanan yang harus menunggu respons dari modul pembayaran. Ketika ratusan pesanan masuk secara bersamaan, semakin banyak thread yang ikut tertahan dan menggunakan sumber daya server. Jika kondisi ini terus terjadi, sumber daya server bisa habis dan akhirnya menyebabkan server FoodGo mengalami crash atau tidak dapat digunakan.

**Solusi desain awal:** Menerapkan timeout dengan batas waktu tertentu, misalnya maksimal 3 detik, pada komunikasi antar-modul dan menggunakan Circuit Breaker. Jika modul pembayaran tidak memberikan respons dalam waktu 3 detik, permintaan akan dihentikan sehingga thread tidak terus menunggu. Jika kegagalan terjadi berulang kali, Circuit Breaker akan memutus sementara komunikasi dengan modul pembayaran dan sistem bisa langsung memberikan respons alternatif kepada pengguna.

**Trade-off:** Menentukan waktu timeout juga perlu diperhatikan. Kalau waktunya terlalu singkat, transaksi yang sebenarnya berhasil bisa dianggap gagal hanya karena responsnya terlambat. Selain itu, jika sistem pembayaran tidak memiliki mekanisme untuk mencegah transaksi yang sama diproses dua kali, pelanggan bisa saja mencoba melakukan pembayaran lagi karena mengira transaksi sebelumnya gagal. Akibatnya, transaksi yang sama berisiko diproses dua kali dan saldo pelanggan bisa terpotong lebih dari sekali.


---

## Pitfall 3: The Network is Reliable — ditulis oleh Ulil Albab An-Nuha

**Bukti di skenario:** Di dalam soal disebutkan tim engineering FoodGo menuliskan asumsi di kodenya seperti #network is always reliable, no need for retry. Selain itu, mereka membuat modul pesanan memanggil modul pembayaran tanpa adanya batas waktu tunggu atau timeout, jadi dibiarkan menunggu tanpa batas waktu.

**Kenapa ini keliru:** Karena di dunia nyata, koneksi jaringan antar server tidak aada yang stabil terus menerus. Pasti ada saja momen dimana koneksi mendadak lemot, ada gangguan router, atau ada paket data yang hilang atau biasa disebut packet loss. Kalau kita membuat sistem monolitik di satu mesin mungkin aman aman saja, tapi dalam sistem terdistribusi yang komunikasinya lewat jaringan, berasumsi kalau jaringan bakal selalu lancar tanpa halangan itu adalah sebuah kesalahan besar.

**Solusi desain awal:** Solusi dasarnya, kita wajib memasang Timeout di setiap interaksi antar service. Jadi misalnya dalam 3 detik modul pembayaran tidak memberikan respons, requestnya langsung digagalkan saja (dilempar error ke user) daripada membiarkan server menunggu selamanya. Agar lebih aman, bisa dipadukan dengan mekanisme Retry dengan Exponential Backoff (mencoba kembali tapi dengan jeda waktu yang bertambah) atau Circuit Breaker (langsung memutus koneksi sementara kalau service tujuan terdeteksi sedang down).

**Trade-off:** Kalau kita menerapkan mekanisme Retry, risikonya adalah saat modul pembayaran benar-benar sedang mati atau kewalahan, mencoba mengirimkan request berulang-ulang justru akan menambah beban kerja modul tersebut jadi akan makin parah (bisa memicu cascading failure). Di sisi lain, trade-off dari memasang Timeout yang terlalu cepat adalah user experience bisa terganggu.  Transaksi yang sebenarnya hanya butuh waktu sedikit lebih lama malah terlanjur dibatalkan oleh sistem.

---

## Kesimpulan Kelompok

[Ringkasan: jika FoodGo memperbaiki ketiga pitfall ini, apa arsitektur yang disarankan secara garis besar? Kaitkan dengan Tugas 2.]

## Rubrik Penilaian (Tugas 1)

| Komponen | Bobot | Kriteria |
|---|---|---|
| Ketepatan identifikasi pitfall | 25% | Pitfall yang dipilih benar-benar tercermin di skenario, bukan asal tempel definisi |
| Kedalaman analisis dampak | 30% | Menjelaskan mekanisme kegagalan (kenapa & bagaimana), bukan cuma "ini menyebabkan lambat" |
| Kualitas solusi & trade-off | 25% | Solusi realistis untuk tim kecil (bukan solusi enterprise berlebihan), trade-off disadari |
| Proses & kontribusi kelompok | 20% | `JURNAL.md` menunjukkan diskusi asli, tiap anggota terlihat kontribusinya |

## Batasan Penggunaan AI (Level 2)

Tugas ini memakai kebijakan **Level 2 (AI Assisted Idea Generation & Structuring)** — lihat [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md) untuk aturan lengkap. Boleh memakai AI untuk brainstorming pitfall apa saja yang mungkin relevan atau menyusun outline analisis; **tidak boleh** meminta AI menuliskan analisis akhirnya (kaitan ke skenario, penjelasan dampak, usulan solusi) yang tinggal ditempel ke `README.md`. Catat setiap sesi pemakaian AI di bagian "Log Penggunaan AI" pada `JURNAL.md`.

Karena tugas ini murni analisis (rawan sekadar salin-tempel dari AI), verifikasi tambahan yang berlaku:
- Setiap pitfall harus dikaitkan dengan **kalimat spesifik** dari skenario di atas — jawaban generik yang bisa dipakai untuk skenario apa saja akan dinilai rendah pada komponen kedalaman analisis.
