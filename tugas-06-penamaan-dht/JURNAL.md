# Jurnal Proses — Tugas 6

## Implementasi find_successor
- Kesalahan pertama yang ditemui : Lupa bahwa Chord adalah ring, ketika tidak ada node dengan ID >= key_hash, fungsi mengembalikan None instead of wrap-around ke node terkecil, sehingga lookup gagal untuk key_hash yang lebih besar dari semua node.
- Cara memperbaikinya: Menambahkan fallback setelah loop dan jika tidak ada kandidat yang ditemukan, kembalikan sorted_nodes[0] yaitu node dengan ID terkecil di ring.

- Kesalahan kedua yang ditemui : Sempat ragu apakah aturan successor menggunakan > atau >= sehingga node dengan ID persis sama dengan key_hash tidak ikut dipertimbangkan.
- Cara memperbaikinya: Baca ulang definisi Chord aturannya adalah terkecil yang >= key_hash, artinya node dengan ID sama persis pun valid sebagai successor. Kondisi di loop diubah dari > menjadi >=.

## Skenario join/leave
- Hasil sebelum & sesudah node baru bergabung: Node medan-1 masuk di antara node-jakarta-2 (102) dan node-surabaya-1 (136). Karena kurir-001 punya key_hash=50, successor-nya tetap node-jakarta-2 (102) — tidak berubah. Tidak ada data yang berpindah pada skenario ini karena tidak ada kurir dengan key_hash di rentang (102, 123].
- Hasil sebelum & sesudah node keluar: node-jakarta-1 memiliki ID terbesar (177), sehingga ia bertanggung jawab atas key_hash di rentang (136, 177]. Ketiga kurir yang didaftarkan (hash: 50, 28, 132) tidak masuk rentang tersebut, jadi tidak ada perpindahan data. Setelah node keluar, ring menjadi 4 node dan successor dari node-surabaya-1 (136) wrap-around ke node-bandung-1 (40).

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| 23 Okt 2026 | Gemini | Dalam simulasi Chord DHT, bagaimana logika penentuan successor jika nilai hash key lebih besar dari seluruh ID node yang ada di ring? | AI menjelaskan konsep ruang melingkar (circular ring), di mana jika sebuah key melebihi ID node tertinggi, pencarian akan berputar balik (wrap-around) ke node dengan ID paling kecil di ring. | Saya menerapkan konsep ini pada fungsi find_successor(), yaitu mengembalikan node pertama dari list node yang sudah diurutkan (sorted_nodes[0]) jika perulangan tidak menemukan node yang >= key_hash. |
| 23 Okt 2026 | Gemini| "Ketika sebuah node baru bergabung (join) ke ring Chord, data mana saja yang seharusnya berpindah tangan ke node baru tersebut? | AI menjelaskan bahwa node baru hanya mengambil alih sebagian data dari successor lamanya, yaitu key yang nilai hash-nya berada di antara posisi node baru dan predecessor-nya. | Saya menyusun alur pada method join() untuk memeriksa seluruh self.data, lalu mencetak perpindahan tanggung jawab key kurir yang kini dipetakan ke node baru tersebut. |
| 31 Okt 2026 | ChatGPT | Apa perbedaan DHT/Chord dengan flat naming sederhana dan DNS konvensional untuk kasus pencarian IP kurir yang sering berubah? | AI memberikan gambaran mengenai pembagian tanggung jawab data pada DHT, ketergantungan pada server pusat, dan pengelolaan cache pada DNS. | Saya mempelajari perbedaan tersebut dan menggunakannya sebagai bahan untuk menyusun perbandingan sesuai konteks simulasi Chord. |
| 31 Okt 2026 | ChatGPT | Apa yang terjadi pada ring Chord ketika sebuah node keluar, dan bagaimana menjelaskan perubahan successor-nya? | AI menjelaskan konsep pengalihan tanggung jawab ke successor berikutnya dan mekanisme wrap-around ketika node dengan ID terbesar keluar. | Saya menggunakan penjelasan tersebut untuk memahami perubahan topologi dan membandingkannya dengan output program. |
| ... | ... | ... | ... | ... |
