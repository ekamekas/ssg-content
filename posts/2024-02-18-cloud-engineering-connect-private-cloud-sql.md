+++
title = 'Cloud Engineering - Menghubungkan Client On-Premise Ke Private Cloud SQL'
description = 'saat migrasi ke cloud tidak langsung seluruhnya, bisa jadi data terlebih dahulu yang dimigrasikan. lalu bagaimana menghubungkan aplikasi yang sudah ada di on-premise dengan Cloud SQL database, terutama ketika dijalankan pada jaringan privat?. artikel ini akan membahas mengenai beberapa opsi yang dilakukan, dan penggunaan Cloud Auth Proxy sebagai salah satu pilihan solusi.'
#tldr = 'Summary to SHOW on detail'
date = 2024-02-18T10:22:30+07:00
draft = false
tags =  ['cloud-engineering','google']
+++

kita sedang dalam tahap migrasi, dimana data dan database yang terlebih dahulu dimigrasikan ke Google Cloud Provider. solusi yang dipakai adalah menggunakan DaaS (database as a service) Cloud SQL dan dikonfigurasi hanya menggunakan private IP.

{{<figure src="/2024-02-18-cloud-engineering-connect-private-cloud-sql_deployment-diagram.svg" width="50%">}}

aplikasi yang belum dimigrasi, berada dalam data center (on-premise) dan tidak ada koneksi antar data center ke jaringan Google Cloud. lalu, bagimana aplikasi dapat terhubung sebagai client ke Cloud SQL sebagai server?

ada beberapa cara yang dapat dilakukan:

- Cloud VPN -> menguhubungkan jaringan on-premise dengan Google Cloud melalu mekanisme network tunneling
- Interconnect -> seperti halnya VPN namun tidak melewati internet (tunnelling), melainkan menghubungkan langsung kedua jaringan
- Cloud Auth Proxy -> konektor tanpa setup rumit seperti jaringan dan SSL

Cloud VPN dan Interconnect akan dibahas lain waktu,

# Cloud Auth Proxy

Layanan [Cloud Auth Proxy](https://cloud.google.com/sql/docs/mysql/sql-proxy) memungkinkan untuk menghubungkan ke database Cloud SQL yang berada di jaringan privat. tanpa ribet. kurang lebih layanan ini sugar-coating layanan koneksi [Private Service Connect](https://cloud.google.com/sql/docs/mysql/about-private-service-connect) dengan beberapa penambahan:

- SSL sudah terkonfigurasi sehingga lalu lintas data dijamin terenkripsi
- terintegrasi dengan [IAM](https://cloud.google.com/iam/docs/overview) sehingga manajemen otentikasi dan otorisasi sudah teratasi. IAM dapat sebagai user maupun service account dimana service account lebih direkomendasikan.

bagimana Cloud Auth Proxy bekerja? client akan terhubung ke database dan melakukan pertukaran melalui sebuah proxy. proxy tersebut terdapat di local tempat client berada serta di server. apabila koneksi baru, maka proxy akan meminta konfigurasi SSL pada Cloud SQL Admin API yang kemudian selalu diperharui apabila kadaluarsa (setiap 1 jam).

{{<figure src="/2024-02-18-cloud-engineering-connect-private-cloud-sql_cloud-auth-proxy-deployment-diagram.svg">}}

untuk menggunakan Cloud Auth Proxy, syarat-syarat harus terpenuhi:

- Cloud SQL Admin API
- Google Cloud authentication credentials dengan permission `cloudsql.instances.connect`
- database credential

untuk menggunakan Cloud Auth Proxy, dilakukan langkah berikut:

- download [Cloud Auth Proxy binary](https://cloud.google.com/sql/docs/mysql/connect-auth-proxy#install)
- dapatkan nama database `gcloud sql instances describe INSTANCE_NAME --format='value(connectionName)'`
- jalankan proxy `./cloud-sql-proxy --port 3306 INSTANCE_CONNECTION_NAME`. gunakan konfig --credential-file untuk mendefinisikan lokasi iam credential (sangat direkomendasikan di production).

setelah proxy berjalan, kita bisa mengubungkan client dengan database selayaknya proses koneksi database pada umumnya.

# Dah.

atas dasar keamanan umumnya kita akan konfigurasikan database dan service backend berada dibelakang firewall, dmz, dan tidak terbuka oleh publik (internet). namun ada beberapa kasus di mana terdapat aplikasi yang berada di luar jaringan tertutup tersebut sehingga perlu koneksi yang aman. kasus lain adalah ketika dalam proses pengembangan tentu akan banyak koneksi yang dilakukan dari komputer masing-masing pengembang yang bersifat lokal. Cloud Auth Proxy ini akan dapat menjadi solusi diantara Cloud VPN yang lambat serta konfigurasinya rumit dan Cloud Interconnect yang akan menambah biaya.

sampai bertemu di post lain
