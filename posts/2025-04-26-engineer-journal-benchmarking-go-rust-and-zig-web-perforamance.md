+++
title = 'Engineer Journal - Golang, Rust, and Zig Web Performance Benchmark'
description = 'Sebuah eksperimen ringan menguji performa server HTTP sederhana menggunakan Go, Rust, dan Zig. Artikel ini membahas pendekatan, hasil benchmark, serta catatan kecil seputar impresi dari masing-masing bahasa.'
#tldr = 'Summary to SHOW on detail'
date = 2025-04-26T19:27:00+07:00
draft = false
tags = ['engineer-journal']
+++

# Pengenalan

selamat datang dan selamat membaca. sejak kemunculannya pertama kali, **Golang** menjadi salah satu bahasa pemrograman yang diminati. Terlebih dengan populernya sistem arsitektur *microservice* yang memerlukan aplikasi yang cepat, ringan, dan mudah untuk di-maintain.

belakang, nama **Rust** dan **Zig** semakin terdengar. **Rust** hanya terpaut 1 peringkat saja dari **Golang** pada survey bahasa pemrograman paling populer versi Stackoverflow dari 2021 hingga 2024. Sedangkan Zig mulai merintis kepopulerannya dengan masuk ke daftar tersebut sejak tahun 2023. Berdasarkan data dari Google Trend, penelusuran dengan kata kunci 'zig programming' meningkat dalam 3 tahun terakhir, walaupun masih terpaut jauh dari Rust dan Golang.

Rust hadir dengan konsep *memory safety* dengan tidak mengandalkan *garbage collector* selayaknya Golang, ditambah dengan performa yang hampir menyamai C. di sisi yang lain, terdapat Zig, bahasa yang paling muda diantara ketiganya (dan masih belum rilis v1.0.0!) tapi mulai mendapat banyak lirikan karena dianggap sebagai 'pengganti C' dengan desain bahasa yang lebih eksplisit, tanpa *hidden control*, dan debugging serta pemrograman yang lebih menyenangkan (berdasarkan pengalaman penulis). Kedua bahasa tersebut menjadi opsi lain dalam membangun aplikasi web service yang andal, ringan, dan berperforma tinggi.

artikel ini akan membahas bagaimana perbandingan Golang, Rust, dan Zig ketika diberikan beban yang tinggi, relatif terhadap batasan resourcenya. hint: Rust & Zig > Golang


# Pemilihan Framework

ada beberapa pilihan web framework yang dapat digunakan untuk setiap bahasa pemrograman tersebut. pada Golang diantaranya ada Gin, Echo, dan Fiber. pada Rust ada Actix, Axum, dan Rocket. pada Zig yang jauh lebih sedikit opsinya ada zap dan httpz. pemilihan framework yang digunakan dalam benchmark berdasarkan beberapa kriteria:

1. performa yang paling baik dengan perbedaan yang signifikan diantara framework lain
2. dibangun hampir atau seluruhnya oleh bahasa pemograman tersebut
3. populer dan mudah digunakan

Studi empiris yang dilakukan oleh Lie (2024) atas ketiga framework Golang tersebut dan satu lainnya memberikan kesimpulan Echo memberikan performa terbaik diantara ketiganya secara signifikan atas load yang tinggi. pada rust dan zig, penulis tidak menemukan tulisan ilmiah yang membandingkan framework-framework atas kedua bahasa tersebut. pada Rust, dapat disimpulkan baik Axum maupun Actix memiliki performa yang serupa. pada Zig dapat disimpulkan bahwa httpz memiliki keunggulan performa dibandingkan yang lain.

maka dari itu, dalam studi ini menggunakan Echo sebagai framework pilihan Golang, Actix untuk Rust, dan httpz untuk Zig.


# Data Pembanding

seperti pada studi yang dilakukan sebelumnya, studi ini akan melihat pola penggunaan sumber daya ketika diberikan load yang tinggi.


# Percobaan

percobaan dilakukan pada personal computer dengan CPU Intel Core i7-10875 dengan memory 32GiB. penggunaan sumber daya, yaitu CPU dan memory, akan diambil datanya secara terus menerus menggunakan sistem monitoring berbasis cAdvisor dan Prometheus. Data tersebut kemudian divisualisasikan menggunakan Grafana. akan ada satu aplikasi web untuk setiap Golang, Rust, dan Zig yang akan diberikan beban oleh k6 sebagai *load generator*. seluruh aplikasi yang ada akan dijalankan diatas Kubernetes.

{{<figure src="/2025-04-26-engineer-journal-benchmarking-go-rust-and-zig-web-performance-architecture.svg" width="50%">}}

akibat keterbatasan perangkat keras yang menyebabkan sulitnya mencapai beban yang tinggi, maka aplikasi tersebut dijalankan dengan batasan sumber daya yang kecil. 


```yaml
spec:
  template:
    spec:
      containers:
        - name: app
          resources:
            requests:
              memory: "64Mi"
              cpu: "125m"
            limits:
              memory: "64Mi"
              cpu: "125m"
```

script dijalankan menggunakan dua skenario:

1. 500 virtual user (thread) dan RPS 2000
2. 1000 virtual user (thread) dan RPS 1000

kedua skenario tersebut dijalankan selama 2 menit dengan metode *constant arrival rate*.


# Hasil

## Skenario 1

{{<figure src="/2025-04-26-engineer-journal-benchmarking-go-rust-and-zig-web-performance-result-scenario-one.png" width="50%">}}


## Skenario 2

{{<figure src="/2025-04-26-engineer-journal-benchmarking-go-rust-and-zig-web-performance-result-scenario-one.png" width="50%">}}

**penggunaan resource**. baik dalam percobaan satu dan dua, rust memiliki penggunaan utilisasi CPU paling baik. Zig menggunakan manajemen memori lebih baik dibanding Rust ketika beban koneksi secara bersamaan lebih tinggi. Golang paling rendah performanya diantara ketiganya.

**Golang Crash**. Mengapa Golang dapat memberikan target RPS sebesar 2000 namun crash ketika RPS hanya 1000 pada skenario dua? hal ini tentu berkaitan dengan mekanisme k6 dalam memberikan beban. pada skenario satu, walaupun RPSnya lebih tinggi namun jumlah user (virual) hanya 500. setiap user akan melakukan 2x request HTTP **pada koneksi yang sama** karena secara default, k6 akan mempertahankan koneksi tersebut dengan header 'Connection: Keep-Alive'. sehingga request ke-2 untuk setiap user yang ada, tidak perlu membuat koneksi ulang yang prosesnya berat. Hal ini terlihat ketika terdapat 1000 user yang masing-masing hanya melakukan 1x request. karena setiap permintaan adalah permintaan awal sehingga diperlukan untuk pembuatan koneksi dan itu yang memberatkan proses. menghasilkan crash pada aplikasi.


# Dah.

Dari hasil benchmark sederhana ini, ketiga bahasa — Go, Rust, dan Zig — menunjukkan performa yang cukup baik untuk server HTTP ringan. Rust sedikit lebih unggul dalam raw performance, Go menawarkan kemudahan dan kecepatan development, sementara Zig memberikan kontrol mendalam namun butuh lebih banyak usaha di setup awal. Pilihan terbaik tetap bergantung pada kebutuhan proyek dan preferensi developer.

sampai bertemu di post lain


# Referensi

- https://survey.stackoverflow.co/2020/#technology
- https://survey.stackoverflow.co/2021/#technology
- https://survey.stackoverflow.co/2022/#technology
- https://survey.stackoverflow.co/2023/#technology
- https://survey.stackoverflow.co/2024/technology
- https://trends.google.com/trends/explore?date=today%205-y&q=zig%20programming,go%20programming,rust%20programming&hl=id
- https://github.com/actix/actix-web
- https://github.com/labstack/echo
- https://github.com/karlseguin/http.zig
- K. E. Lie, M. S. Astriani and I. B. Kerthyayana Manuaba, "Analyzing the Performance of Golang Web Frameworks Utilizing GORM in the Oil and Gas Industry," 2024 2nd International Conference on Technology Innovation and Its Applications (ICTIIA), Medan, Indonesia, 2024, pp. 1-6, doi: 10.1109/ICTIIA61827.2024.10761385.
- https://www.techempower.com/benchmarks/#section=data-r23
- https://github.com/randiekas/rust-web-framework-benchmark
- https://github.com/programatik29/rust-web-benchmarks/blob/master/result/hello-world.md
