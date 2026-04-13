# advprog-modul6

## Commit 1 Reflection Notes

Program ini bekerja sebagai single-threaded web server sederhana dengan `TcpListener` yang bind ke `127.0.0.1:7878`. Setiap koneksi masuk diproses satu per satu pada thread utama, sehingga server belum bisa menangani banyak request secara paralel.

Fungsi `handle_connection` menerima `TcpStream`, lalu membungkusnya dengan `BufReader` agar request dari browser dapat dibaca baris demi baris. Hanya bagian request header yang dikumpulkan, karena pembacaan berhenti saat menemukan baris kosong yang menandai akhir header HTTP.

Saat browser mengakses server, program akan mencetak isi request ke terminal. Dari output tersebut bisa dilihat method HTTP, path, host, user-agent, dan header lain yang dikirim browser, sehingga tahap ini membantu memahami pola komunikasi dasar antara browser dan web server.

## Commit 2 Reflection Notes

Pada tahap ini, fungsi `handle_connection` tidak hanya membaca request, tetapi juga mengirimkan HTTP response yang valid. Response terdiri dari status line `HTTP/1.1 200 OK`, header `Content-Length`, baris kosong sebagai pemisah header dan body, lalu isi file HTML yang dibaca dari `hello.html`.

Saya memahami bahwa browser membutuhkan format response HTTP yang benar agar bisa merender halaman. Penambahan `Content-Length` penting karena memberi tahu browser ukuran body yang diterima, sehingga browser bisa mengetahui kapan respons selesai dibaca.

Dengan memisahkan konten HTML ke file `hello.html`, server menjadi lebih mudah dipahami dan dikembangkan. Kode Rust menangani komunikasi jaringan, sedangkan isi halaman dikelola terpisah dalam file HTML sederhana yang dapat diubah tanpa mengubah struktur dasar server.

## Commit 3 Reflection Notes

Pada tahap ini server tidak lagi selalu mengirim `hello.html`. Program sekarang membaca request line pertama, lalu memvalidasi apakah request tersebut adalah `GET / HTTP/1.1`. Jika cocok, server mengirim halaman utama dengan status `200 OK`; jika tidak, server mengirim `404.html` dengan status `404 NOT FOUND`.

Refactoring dilakukan dengan memisahkan hasil validasi request menjadi dua bagian, yaitu `status_line` dan `filename`. Pemisahan ini dibutuhkan agar keputusan logika tidak bercampur dengan proses membangun response. Dengan begitu, alur program menjadi lebih jelas: pertama tentukan request valid atau tidak, lalu baca file yang sesuai, setelah itu bentuk HTTP response dari data tersebut.

Pendekatan ini memudahkan pengembangan fitur berikutnya karena setiap cabang request cukup menentukan pasangan status dan file yang akan dikirim. Struktur ini juga lebih mudah diperluas dibanding menulis ulang seluruh response string untuk setiap kondisi.

## Commit 4 Reflection Notes

Pada tahap ini ditambahkan rute `GET /sleep HTTP/1.1` yang secara sengaja menunda respons selama 10 detik sebelum mengirim `hello.html`. Tujuannya bukan untuk menambah fitur baru bagi pengguna, tetapi untuk mensimulasikan request lambat agar keterbatasan server single-threaded terlihat jelas.

Ketika satu browser membuka `/sleep`, thread utama akan berhenti di `thread::sleep` dan tidak bisa memproses koneksi lain selama waktu tunggu itu. Akibatnya, request ke `/` dari jendela browser lain juga ikut tertahan walaupun halaman tersebut seharusnya cepat. Ini menunjukkan bahwa pada server single-threaded, satu request lambat dapat memblokir seluruh server.

Dari simulasi ini saya memahami alasan perlunya concurrency pada web server. Jika banyak pengguna mengakses server bersamaan, pendekatan satu thread untuk semua koneksi akan membuat respons terasa lambat dan tidak skalabel, sehingga tahap berikutnya perlu memindahkan penanganan request ke banyak thread atau thread pool.
