# A. How much data your publisher program will send to the message broker in one run?

Program publisher ini mengirimkan total 5 pesan ke message broker dalam satu kali menjalankan program. Setiap pesan berupa UserCreatedEventMessage yang berisi informasi pengguna dengan struktur user_id dan user_name. Dapat dilihat bahwa program melakukan 5 kali operasi publish_event dengan detail data pengguna yang berbeda (dari user_id 1 sampai 5 dengan nama 2306208855-Amir hingga 2306208855-Emir). Masing-masing pesan ini dikirim dengan action "user_created" yang akan digunakan oleh subscriber untuk menentukan handler yang sesuai.

# B. The url of: “amqp://guest:guest@localhost:5672” is the same as in the subscriber program, what does it mean?

URL "amqp://guest:guest@localhost:5672" memiliki arti bahwa:

- Protokol: amqp:// menunjukkan bahwa komunikasi menggunakan protokol Advanced Message Queuing Protocol, yang merupakan protokol standar untuk message broker.
- Kredensial Otentikasi: guest:guest merupakan kombinasi username dan password default yang digunakan untuk autentikasi ke message broker. Username-nya adalah "guest" dan password-nya juga "guest".
- Host: localhost menunjukkan bahwa message broker berjalan pada mesin lokal yang sama dengan program.
- Port: 5672 adalah port standar yang digunakan oleh protokol AMQP.

Penggunaan URL yang sama di program publisher dan subscriber mengindikasikan bahwa keduanya terhubung ke message broker yang sama. Ini memungkinkan terjadinya komunikasi antara keduanya melalui broker tersebut. Ketika publisher mengirim pesan ke broker, subscriber yang terhubung ke broker yang sama akan dapat menerima pesan tersebut. Dengan demikian, komponen publisher dan subscriber dapat berkomunikasi secara asinkron tanpa harus saling mengenal secara langsung (decoupled), hanya perlu mengetahui alamat broker yang sama.

# Running RabbitMQ as message broker.

![rabbitmq](image.png)

# Sending and processing event.

![alt text](image-2.png)
![alt text](image-1.png)

Kedua gambar tersebut memperlihatkan implementasi komunikasi asinkron menggunakan RabbitMQ sebagai message broker dalam aplikasi Rust, di mana gambar pertama menunjukkan eksekusi program publisher yang mengirimkan lima data pengguna berbeda (dari 2306208855-Amir hingga 2306208855-Emir) ke dalam message queue dalam satu kali proses eksekusi, sedangkan gambar kedua menampilkan program subscriber yang sedang berjalan dan berhasil mengonsumsi pesan-pesan tersebut dari queue, kemudian memprosesnya dengan menampilkan informasi pengguna yang diterima pada console terminal sebagai konfirmasi bahwa komunikasi antar layanan telah berhasil dilakukan dengan baik.

# Monitoring chart based on publisher.

![alt text](image-3.png)

Pada grafik kedua, terlihat adanya lonjakan (spike) yang terjadi ketika publisher dijalankan berulang kali. Lonjakan ini disebabkan oleh pengiriman pesan yang dilakukan secara bertubi-tubi ke dalam sistem message queue setiap kali publisher dijalankan. Ketika publisher mengirimkan beberapa pesan sekaligus dalam waktu singkat, beban pada sistem meningkat secara tiba-tiba, yang tercermin sebagai spike pada grafik. Hal ini menunjukkan bahwa setiap eksekusi publisher menghasilkan sejumlah pesan yang diproses oleh consumer, dan semakin sering publisher dijalankan, semakin banyak pula lonjakan yang terlihat. Lonjakan tersebut menggambarkan aktivitas pengiriman pesan yang intens dalam periode waktu yang singkat, yang kemudian diikuti oleh penurunan ketika pesan-pesan tersebut selesai diproses oleh consumer.