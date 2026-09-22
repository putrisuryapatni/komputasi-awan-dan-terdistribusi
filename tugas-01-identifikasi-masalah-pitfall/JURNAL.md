# Jurnal Proses — Tugas 1

> Isi jurnal ini selama proses diskusi berlangsung, bukan ditulis ulang rapi di akhir. Tulis dengan gaya bebas — poin diskusi, kebuntuan, perubahan pikiran.

## ## [21-09-2026]
- Peserta: Ida Ayu Putri S. B. dan I Wayan Juanesa R. P.
- Poin diskusi: 
    - Menentukan pitfall
    - Tugek nemu pitfall the network is reliable dan latency is zero
    - Di bagian yang "# network is always reliable, no need for retry" langsung ketahuan kalau ada yang salah, karena pada soal dikatakan adanya kendala pada aplikasi tapi ini malah dibilang selalu berjalan dengan balik
    - Pada bagian "tidak ada timeout sama sekali pada pemanggilan antar service" juga langsung ketahuan bahwa ada yang salah di latency nya
    - Ryan menemukan 2 pitfal lain yaitu "Single Point of Failure" dan "Always-On Assumption"
    - pada soal terlihat "satu server yang menangani semua modul(...)kewalahan" disini juga sudah dibilang satu server menangani semua modul sehingga menemukan pitfal Single Point of Failure
    - untuk bagian "Server backend kadang *crash* total dan perlu di-restart manual." setelah saya brainstroming ke AI mengenai kemungkinan pitfal yang cocok dan saya menemukan pitfal Always-On Assumption
- Perbedaan pendapat (jika ada): ...

## ## [22-09-2026]
- Belajar cara githubnya karena masih bingung dan ada sedikit error
- Pastiin lagi semua analisisnya udah bener, cek lagi solusi yang kita kasi udah sesuai sama ketentuan yang diminta

## Review Silang
- [Ryan] mengomentari analisis [Tugek]: Dari yang sudah saya liat hasil dari analisis punya tugek, sudah sesuai dengan hasil diskusi kita di awal tidak adanya yang melenceng secara tiba-tiba dan di buat dengan sebaik mungkin

## Log Penggunaan AI (Level 2)

> Wajib diisi sesuai kebijakan Level 2 di [`../RUBRIK-UMUM.md`](../RUBRIK-UMUM.md). Tulis "Tidak memakai AI" pada baris pertama jika memang tidak dipakai. Hanya untuk brainstorming ide/outline — bukan untuk kode/analisis/teks akhir.

| Tanggal | Tool AI | Prompt yang diberikan | Ringkasan saran/ide AI | Bagaimana diolah jadi tulisan/kode sendiri |
|21-09-2026|GPT|Apa itu pitfal| Pitfall = sesuatu yang awalnya dianggap aman/benar, tetapi ternyata bisa menyebabkan masalah ketika kondisi tertentu terjadi.|tidak ada,karena saya sebelum buat tugas ini saya tidak tau pitfal itu apa|
|21-09-2026|GPT|bantu saya melakukan brainstorming untuk mengidentifikasi potensi pitfall dari Server backend kadang *crash* total dan perlu di-restart manual. kenapa begitu?|Karena sistem menganggap server akan selalu aktif, padahal server bisa crash. Saat crash terjadi, sistem tidak punya mekanisme pemulihan otomatis sehingga harus di-restart manual.|sistem tidak menganggap bawa server tidak akan mengalami kegagalan dan tidak harus menunggu untuk me-restart ulang oleh manusia.|
|21-09-2026|GPT|Jika satu server yang menangani semua modul (pesanan, pembayaran, notifikasi kurir) kewalahan karena semuanya berjalan di satu proses monolitik yang sama apasih solusi dan trade off yang cocok dalam permasalahan ini|Solusi: Memisahkan modul yang paling berat menjadi beberapa service dan menggunakan queue untuk proses yang tidak harus langsung dilakukan. Trade-off: Menambah kompleksitas pengelolaan dan komunikasi antarservice, tetapi beban server menjadi lebih terbagi dan sistem lebih tahan terhadap lonjakan trafik.|solusi:Perlu adanya server cadangan untuk membantu server utama untuk melayani permintaan pengguna dan juga perlu menggunakan sistem antrean supaya dapat diproses secara satu per satu sesuai kapasitas server. Trade-off:Beberapa permintaan diperlukan waktu lama untuk menunggu antrean. Selain itu perlu biaya yang lebih karena pengelolaan sistem yang bertambah dengan adanya penambahan server cadangan.|
