# Jurnal Proses — Tugas 1


## [21-09-2026]
- Peserta: Ida Ayu Putri S. B. dan I Wayan Juanesa R. P.
- Poin diskusi: 
    - Nentuin pitfall yang mana aja dulu
    - Tugek nemu pitfall the network is reliable dan latency is zero
    - Di bagian yang "# network is always reliable, no need for retry" langsung ketahuan kalau ada yang salah, karena pada soal dikatakan adanya kendala pada aplikasi tapi ini malah dibilang selalu berjalan dengan balik
    - Pada bagian "tidak ada timeout sama sekali pada pemanggilan antar service" juga langsung ketahuan bahwa ada yang salah di latency nya
    - Ryan menemukan 2 pitfal lain yaitu "Single Point of Failure" dan "Always-On Assumption"
    - pada soal terlihat "satu server yang menangani semua modul(...)kewalahan" disini juga sudah dibilang satu server menangani semua modul sehingga menemukan pitfal Single Point of Failure
    - untuk bagian "Server backend kadang *crash* total dan perlu di-restart manual." setelah saya brainstroming ke AI mengenai kemungkinan pitfal yang cocok dan saya menemukan pitfal Always-On Assumption
- Perbedaan pendapat (jika ada): ...

## [22-09-2026]
- Peserta: Ida Ayu Putri S. B. dan I Wayan Juanesa R. P.
- Poin diskusi: 
    - Belajar cara githubnya karena masih bingung dan ada sedikit error
    - Pastiin lagi semua analisisnya udah bener, cek lagi solusi yang kita kasi udah sesuai sama ketentuan yang diminta

## Review Silang
- [Ryan] mengomentari analisis [Tugek]: Dari yang sudah saya liat hasil dari analisis punya tugek, sudah sesuai dengan hasil diskusi kita di awal tidak adanya yang melenceng secara tiba-tiba dan di buat dengan sebaik mungkin
- [Tugek] mengomentari analisis [Ryan]: Kalau saya lihat, semua permasalahan yang ditemukan ryan sudah sesuai dan juga solusinya sudah seperti ketentuan yang ada pada soal

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang Diberikan | Ringkasan Saran / Ide AI | Bagaimana Diolah Jadi Tulisan / Kode Sendiri |
| :--- | :--- | :--- | :--- | :--- |
| 21-09-2026 | GPT | Apa itu pitfall | Pitfall adalah sesuatu yang awalnya dianggap aman atau benar, tetapi ternyata bisa menyebabkan masalah ketika kondisi tertentu terjadi. | Tidak ada, karena sebelum membuat tugas ini saya belum tahu apa itu pitfall. |
| 21-09-2026 | GPT | Bantu saya melakukan brainstorming untuk mengidentifikasi potensi pitfall dari: "Server backend kadang *crash* total dan perlu di-restart manual. Kenapa begitu?" | Karena sistem menganggap server akan selalu aktif, padahal server bisa *crash*. Saat *crash* terjadi, sistem tidak punya mekanisme pemulihan otomatis sehingga harus di-restart manual. | Sistem tidak boleh menganggap bahwa server tidak akan pernah mengalami kegagalan, serta tidak boleh bergantung pada manusia untuk melakukan *restart* manual. |
| 21-09-2026 | GPT | Jika satu server yang menangani semua modul (pesanan, pembayaran, notifikasi kurir) kewalahan karena semuanya berjalan di satu proses monolitik yang sama, apa solusi dan *trade-off* yang cocok? | **Solusi:** Memisahkan modul paling berat menjadi beberapa *service* dan menggunakan *queue* untuk proses non-instan.<br><br>**Trade-off:** Menambah kompleksitas pengelolaan dan komunikasi antarservice, namun beban server terbagi dan tahan lonjakan trafik. | **Solusi:** Perlu server cadangan untuk membantu server utama melayani permintaan, serta menggunakan sistem antrean (*queue*) agar diproses bertahap sesuai kapasitas.<br><br>**Trade-off:** Membutuhkan waktu tunggu lebih lama pada antrean dan meningkatkan biaya karena ada pengelolaan server tambahan. |
