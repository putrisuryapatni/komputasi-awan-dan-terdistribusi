# Tugas 1 — Analisis Pitfall FoodGo

**Kelompok:** [1]

| Nama | NIM | Kontribusi |
|---|---|---|
| Ida Ayu Putri Suryapatni Basundari | 103072400068 | Pitfall 1 dan 2 |
| I Wayan Juanesa Ryan Pradita | 103072430012 | Pitfall 3 dan 4|

---

## Pitfall 1: [The Network is Reliable] — ditulis oleh Ida Ayu Putri Suryapatni Basundari

**Bukti di skenario:**  
[# network is always reliable, no need for retry]

**Kenapa ini keliru:**  
[Pada kalimat tersebut dikatakan bahwa selalu berjalan dengan baik padahal terjadi beberapa masalah dalam jalannya aplikasi. Ini membuktikan bahwa sistem belum siap menangani permasalahan yang terjadi.]

**Dampak ke FoodGo:**  
[Misalnya modul pesanan ingin menghubungi modul pembayaran. Jika komunikasi gagal, permintaan pembayaran tidak berhasil diproses. Karena FoodGo tidak mempunyai mekanisme retry, sistem tidak mencoba kembali permintaan tersebut. Akibatnya, pesanan pengguna bisa gagal atau tidak mendapatkan respons yang seharusnya.]

**Solusi desain awal:**  
[FoodGo dapat menggunakan:
- Retry -> mencoba kembali jika permintaan gagal.
- Backoff -> memberikan jeda sebelum mencoba kembali.
- Timeout -> menentukan batas waktu menunggu.
- Circuit breaker -> menghentikan sementara permintaan ke service yang sedang bermasalah.]

**Trade-off:**  
[Retry juga memiliki risiko. Jika service sedang terlalu sibuk dan semua permintaan terus mencoba kembali, jumlah permintaan malah semakin banyak. Akibatnya, service tersebut bisa semakin terbebani.]

---

## Pitfall 2: [Latency is Zero] — ditulis oleh Ida Ayu Putri Suryapatni Basundari

**Bukti di skenario:**  
[tidak ada timeout sama sekali pada pemanggilan antar service (modul pesanan memanggil modul pembayaran dan menunggu tanpa batas waktu).]

**Kenapa ini keliru:**  
[Aplikasi menganggap selalu memberikan jawaban dan respon dengan cepat sedangkan adanya keluhan bahwa "aplikasi lambat" tidak selaras dengan apa yang dipikirkan oleh aplikasi. Kenyataannya ketika pengguna sangat banyak, modul pembayaran bisa menjadi lambat. Komunikasi antar-service juga membutuhkan waktu. Jadi, respons dari service lain tidak selalu langsung diterima.]

**Dampak ke FoodGo:**  
[Misalnya ada 1.000 pengguna yang melakukan pemesanan pada saat promo.

Modul pesanan mengirim request ke pembayaran. Jika pembayaran membutuhkan waktu lama, modul pesanan akan terus menunggu karena tidak mempunyai timeout.

Akibatnya:

Pembayaran lambat -> request pesanan menunggu -> resource/thread tertahan -> semakin banyak request masuk -> resource server habis -> aplikasi semakin lambat -> timeout/crash.]

**Solusi desain awal:**  
[FoodGo dapat menetapkan timeout pada komunikasi antar-service. Misalnya, modul pesanan memberikan waktu maksimum tertentu kepada modul pembayaran untuk memberikan respons dan jika melewati batas tersebut, modul pesanan tidak terus menunggu tanpa batas. Selain timeout, FoodGo dapat menggunakan circuit breaker agar kegagalan atau kelambatan service pembayaran tidak terus menyebar ke service lainnya.]

**Trade-off:**  
[Timeout yang terlalu pendek dapat menyebabkan request dianggap gagal padahal service sebenarnya hanya sedang sedikit lambat. Sebaliknya, timeout yang terlalu panjang membuat resource tetap tertahan. Jadi nilai timeout harus disesuaikan dengan karakteristik operasi dan kebutuhan sistem.]

---

## Pitfall 3: [nama pitfall] — ditulis oleh [nama]

(ulangi struktur di atas)

---

## Kesimpulan Kelompok

[Ringkasan: jika FoodGo memperbaiki ketiga pitfall ini, apa arsitektur yang disarankan secara garis besar? Kaitkan dengan Tugas 2.]
