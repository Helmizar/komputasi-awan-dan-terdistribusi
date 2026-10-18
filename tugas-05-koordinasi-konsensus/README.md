# Tugas 5 (Pekan 5) — Koordinasi & Konsensus

**Materi terkait:** Physical/Logical Clock, Election Algorithm (Bully/Raft).

## Studi Kasus

Terjadi konflik urutan waktu pada pesanan FoodGo (dua server mencatat urutan pesanan berbeda karena jam tidak sinkron), dan sistem membutuhkan satu **"Node Leader"** untuk mengelola antrean pesanan pusat agar tidak terjadi konflik pemrosesan ganda.

## Tugas Kelompok

Implementasikan simulasi **Bully Algorithm** untuk memilih leader di antara **5 node virtual** yang berjalan sebagai 5 proses Python terpisah di laptop yang sama (tiap node = satu proses di port berbeda, komunikasi lewat TCP socket di `localhost`).

Skeleton yang disediakan (`src/node.py`) sudah menangani bagian **infrastruktur jaringan** (membuka socket, menerima & mengirim pesan antar node) — bagian **algoritma Bully sendiri sengaja dikosongkan** (`# TODO`), karena itu inti pembelajaran tugas ini.

### Aturan Bully Algorithm yang harus diimplementasikan

1. Node yang mendeteksi leader tidak merespons (atau saat start pertama kali) mengirim pesan `ELECTION` ke semua node dengan ID **lebih besar** darinya.
2. Jika ada node ber-ID lebih besar yang merespons `OK`, node yang memulai election **berhenti mencalonkan diri** dan menunggu pesan `COORDINATOR`.
3. Jika **tidak ada** node ber-ID lebih besar yang merespons dalam batas waktu tertentu, node tersebut menjadi leader dan mem-broadcast `COORDINATOR` ke semua node lain.
4. Semua node mencatat siapa leader saat ini.

### Cara Menjalankan

Buka 5 terminal terpisah (satu per node):
```bash
python3 src/node.py --id 1 --port 5001 --peers 5002,5003,5004,5005
python3 src/node.py --id 2 --port 5002 --peers 5001,5003,5004,5005
python3 src/node.py --id 3 --port 5003 --peers 5001,5002,5004,5005
python3 src/node.py --id 4 --port 5004 --peers 5001,5002,5003,5005
python3 src/node.py --id 5 --port 5005 --peers 5001,5002,5003,5004
```

### Skenario Wajib Diuji & Dicatat

1. **Start normal**: nyalakan node 1–5 hampir bersamaan, catat siapa yang terpilih jadi leader (harusnya node ber-ID tertinggi, yaitu node 5).
2. **Leader mati**: matikan (Ctrl+C) proses node leader (node 5), amati node lain mendeteksi leader hilang dan memicu election baru. Catat leader baru yang terpilih (harusnya node 4).
3. Simpan log terminal (screenshot atau `> log_nodeX.txt` redirect) dari kedua skenario ke folder `bukti/`.

### Analisis 
#### Mengapa perlu leader?
Pada kasus FoodGo, dua server pesanan yang berjalan paralel tanpa koordinasi dapat menyebabkan masalah serius. Pertama, dua server bisa memproses pesanan yang sama secara bersamaan sehingga terjadi double processing, misalnya satu pesanan dikirim ke dua kurir berbeda. Kedua, karena jam fisik tiap mesin tidak pernah sinkron sempurna (clock drift), server A dan server B bisa mencatat urutan pesanan yang berbeda meskipun pesanan datang di waktu yang hampir sama. Kehadiran satu node leader menyelesaikan kedua masalah ini sekaligus. Semua pesanan masuk diarahkan ke leader terlebih dahulu leader yang memutuskan urutan resmi menggunakan antrean internalnya sebelum meneruskannya ke node lain, sehingga konsistensi data terjaga.

#### Kenapa Bully pilih ID tertinggi?
Bully Algorithm bekerja berdasarkan asumsi bahwa ID node merepresentasikan prioritasnya untuk menjadi leader. Ketika sebuah node mendeteksi tidak ada leader, ia mengirimkan ELECTION ke semua node ber-ID lebih tinggi. Yang merasa lebih layak membalas OK lalu menjalankan election-nya sendiri ke atas. Proses ini naik berantai sampai ke node ID tertinggi yang masih hidup karena tidak ada yang membalasnya, setelah ELECTION_TIMEOUT ia langsung broadcast COORDINATOR. Hasilnya selalu deterministik: selama ID unik, selalu ada tepat satu pemenang.

#### Kelemahan Bully vs Raft?
Meskipun mudah diimplementasikan, Bully memiliki beberapa kelemahan. Dari sisi efisiensi, pesan yang dikirim bersifat O(n²) karena election memicu pesan berantai dari bawah ke atas, sedangkan Raft hanya O(n). Selain itu, Bully memiliki leadership gap (ada jeda) di mana sistem tidak punya leader sama sekali antara leader lama mati dan leader baru terpilih sementara Raft meminimalkan ini lewat heartbeat dan penomoran term. Kelemahan terbesar Bully adalah rentan split-brain: saat jaringan terpartisi, dua segmen bisa memilih leader masing-masing sehingga terjadi inkonsistensi data, sedangkan Raft mencegahnya dengan quorum. Meski demikian, untuk 5 node di localhost seperti tugas ini, semua kelemahan itu tidak relevan secara praktis Bully dipilih karena kesederhanaan implementasinya sudah cukup.

## Struktur Submission

```
tugas-05-koordinasi-konsensus/
├── README.md      # Analisis: kenapa perlu leader, kenapa Bully (vs Raft), hasil 2 skenario
├── JURNAL.md
├── src/
│   └── node.py
└── bukti/          # Log/screenshot skenario start normal & leader mati
```

## Rubrik Penilaian (Tugas 5)

| Komponen | Bobot | Kriteria |
|---|---|---|
| Implementasi Bully Algorithm benar | 35% | Election, OK, COORDINATOR message bekerja sesuai aturan algoritma |
| Bukti skenario leader mati → re-election | 30% | Log jelas menunjukkan leader baru terpilih otomatis |
| Analisis (kenapa perlu leader, trade-off Bully vs Raft) | 20% | Dikaitkan ke masalah urutan pesanan di skenario FoodGo |
| Proses & kontribusi kelompok | 15% | `JURNAL.md`, commit history |

## Batasan Penggunaan AI (Level 2)

Kebijakan **Level 2 (AI Assisted Idea Generation & Structuring)** berlaku — lihat [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Boleh bertanya ke AI tentang gambaran umum algoritma election; **tidak boleh** meminta AI menuliskan isi `# TODO` di `src/node.py` (logika `on_message`, `start_election`, `declare_leader`, `monitor_leader`). Catat pemakaian AI di "Log Penggunaan AI" pada `JURNAL.md`.

- `README.md` wajib menjelaskan kenapa Bully Algorithm selalu memilih node ber-ID tertinggi yang masih hidup, dan apa kelemahannya dibanding algoritma lain (mis. jumlah pesan yang dikirim saat election, single point of failure sebelum leader baru terpilih) — bukan sekadar definisi generik.
