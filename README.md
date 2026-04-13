# advprog-modul6

## Commit 1 Reflection Notes

Pada commit ini saya memahami dasar cara kerja web server sederhana di Rust, yaitu dengan `TcpListener` yang mendengarkan koneksi pada `127.0.0.1:7878` dan memproses setiap koneksi secara berurutan dalam satu thread. Saya juga melihat bahwa `handle_connection` menggunakan `BufReader` untuk membaca request browser baris demi baris sampai header selesai, lalu menampilkan isi request ke terminal. Dari tahap ini terlihat jelas bahwa browser mengirim request HTTP lengkap berisi method, path, host, dan header lain, sehingga saya jadi lebih paham alur komunikasi dasar antara client dan server.

## Commit 2 Reflection Notes

Pada commit ini saya memahami bahwa browser tidak cukup hanya menerima koneksi, tetapi juga harus mendapat HTTP response yang valid agar bisa menampilkan halaman. Karena itu `handle_connection` saya ubah agar mengirim status line `HTTP/1.1 200 OK`, header `Content-Length`, dan isi file `hello.html` sebagai body response. Dari sini saya belajar bahwa `Content-Length` penting untuk memberi tahu ukuran data yang dikirim, dan pemisahan konten HTML ke file terpisah membuat kode server lebih rapi karena logika jaringan tetap berada di Rust sementara isi halaman dikelola di file HTML.

## Commit 3 Reflection Notes

Pada commit ini saya memahami bagaimana server mulai memvalidasi request dan merespons secara selektif, bukan selalu mengirim `hello.html` untuk semua path. Program sekarang membaca request line pertama, lalu membedakan antara `GET / HTTP/1.1` yang harus dibalas dengan `200 OK` dan path lain yang harus dibalas dengan `404 NOT FOUND` beserta `404.html`. Saya juga belajar bahwa refactoring dengan memisahkan `status_line` dan `filename` membuat kode lebih mudah dibaca dan dikembangkan, karena logika penentuan response dipisahkan dari proses membangun response HTTP.

## Commit 4 Reflection Notes

Pada commit ini saya memahami kelemahan utama server single-threaded dengan menambahkan route `/sleep` yang sengaja menunda response selama 10 detik. Saat satu request masuk ke `/sleep`, thread utama berhenti di `thread::sleep`, sehingga request lain seperti `/` juga ikut menunggu walaupun sebenarnya ringan. Dari simulasi ini terlihat jelas bahwa satu request lambat dapat memblokir seluruh server, dan hal itu menjadi alasan utama kenapa web server perlu concurrency agar tetap responsif saat menerima banyak koneksi secara bersamaan.

## Commit 5 Reflection Notes

Pada commit ini saya memahami bagaimana `ThreadPool` membuat server menjadi multithreaded tanpa harus membuat thread baru untuk setiap request. Di `main`, koneksi yang datang dikirim ke pool melalui `pool.execute`, lalu worker thread yang tersedia akan menjalankan `handle_connection` secara paralel. `ThreadPool` sendiri bekerja dengan channel untuk mengirim job dan beberapa worker yang berbagi receiver menggunakan `Arc<Mutex<_>>`, sehingga request lambat seperti `/sleep` tidak lagi memblokir semua request lain. Menurut saya ini lebih efisien dan lebih rapi karena logika concurrency dipisahkan ke `src/lib.rs`, sementara `main.rs` tetap fokus pada alur server.

## Commit Bonus Reflection Notes

Pada bonus ini saya memahami bahwa fungsi `build` lebih baik digunakan sebagai pengganti `new` untuk membuat `ThreadPool`, karena `build` mengembalikan `Result` dan membuat validasi ukuran pool menjadi lebih aman serta eksplisit. Berbeda dengan `new` yang biasanya memakai `assert!` dan langsung panic saat input tidak valid, `build` memberi kesempatan kepada pemanggil untuk menangani error dengan cara yang lebih jelas, misalnya lewat `expect`, fallback, atau mekanisme lain. Menurut saya pendekatan ini lebih baik untuk pengembangan jangka panjang karena API menjadi lebih robust dan lebih mudah diperluas jika nanti ada validasi tambahan.
