# Jurnal Proses — Tugas 6

## Implementasi find_successor
- Kesalahan pertama yang ditemui (mis. lupa wrap-around di ring): ...
- Cara memperbaikinya: ...

## Skenario join/leave
- Hasil sebelum & sesudah node baru bergabung: ...
- Hasil sebelum & sesudah node keluar: ...

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|---|---|---|---|---|
| 23 Okt 2026 | Gemini | Dalam simulasi Chord DHT, bagaimana logika penentuan successor jika nilai hash key lebih besar dari seluruh ID node yang ada di ring? | AI menjelaskan konsep ruang melingkar (circular ring), di mana jika sebuah key melebihi ID node tertinggi, pencarian akan berputar balik (wrap-around) ke node dengan ID paling kecil di ring. | Saya menerapkan konsep ini pada fungsi find_successor(), yaitu mengembalikan node pertama dari list node yang sudah diurutkan (sorted_nodes[0]) jika perulangan tidak menemukan node yang >= key_hash. |
| 23 Okt 2026 | Gemini| "Ketika sebuah node baru bergabung (join) ke ring Chord, data mana saja yang seharusnya berpindah tangan ke node baru tersebut? | AI menjelaskan bahwa node baru hanya mengambil alih sebagian data dari successor lamanya, yaitu key yang nilai hash-nya berada di antara posisi node baru dan predecessor-nya. | Saya menyusun alur pada method join() untuk memeriksa seluruh self.data, lalu mencetak perpindahan tanggung jawab key kurir yang kini dipetakan ke node baru tersebut. |
| ... | ... | ... | ... | ... |
