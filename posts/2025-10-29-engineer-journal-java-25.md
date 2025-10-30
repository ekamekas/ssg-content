+++
title = 'Engineer Journal - Java 25'
description = 'Java 25 membawa banyak peningkatan signifikan mulai dari Pattern Matching untuk primitive types hingga import modular dan Structured Concurrency. Versi LTS terbaru ini menjadi langkah besar setelah Java 21.'
date = 2025-10-29
draft = false
tags = ['engineer-journal', 'java']
+++

# Pengenalan

selamat datang dan selamat membaca. Pada September 2025, Java 25 resmi dirilis menjadi versi *Long-Term Support* (LTS) terbaru dengan dukungan hingga September 2035.
Versi LTS terakhir sebelumnya adalah Java 21 (rilis September 2023).
Artinya, Java 25 membawa banyak pembaruan penting bagi developer yang menginginkan kestabilan jangka panjang sekaligus fitur modern dari Java generasi terbaru.

artikel ini akan mengupas pembaruan yang dikemas dalam Java 25 serta mengapa kita perlu merencanakan untuk mengadopsinya.

# Fitur Unggulan

## Kode yang Lebih Ringkas

salah satu fokus besar Java 25 adalah membuat kode lebih sederhana dan ekspresif, tanpa kehilangan kekuatan bahasa Java yang kuat dan terstruktur.


### [Compact Source Files and Instance Main Methods (JEP 512)](https://openjdk.org/jeps/512)

kini anda bisa menulis program Java sederhana tanpa harus menuliskan class wrapper atau `public static void main(String[] args)` secara eksplisit.
contohnya:

```java
void main() {
    System.out.println("Halo, Java 25!");
}
```

fitur ini membuat penulisan program ringkas seperti skrip — sangat cocok untuk demo, pembelajaran, atau utility script ringan.
jadi ambigu antara javascript atau java script, :)


### [Module Import Declaration (JEP 511)](https://openjdk.org/jeps/511)

mengurangi kebutuhan import java.x.* yang panjang dengan cara baru:

```java
import module java.base;
```

fitur ini sangat membantu ketika bekerja dengan library pihak ketiga yang dibungkus dalam satu modul besar.
developer tidak perlu lagi menuliskan banyak import — cukup satu deklarasi modul untuk keseluruhan dependensi.

eits, tapi harus hati-hati. bisa saja dalam dua modul terdapat kelas yang sama:

```java
import module java.base;
import module java.sql;
```

kode diatas akan mengakibatkan eror pada saat kompilasi karena ada dua kelas yang sama `java.sql.Date` dan `java.util.Date`

```sh
    error: reference to Date is ambiguous
         Date d = Date.valueOf("2025-06-15");
         ^
    both class java.sql.Date in java.sql and class java.util.Date in java.util match
    error: reference to Date is ambiguous
```

solusinya bisa secara eksplisit dituliskan import kelas yang ingin digunakan

```java
import module java.base;
import module java.sql;

import java.sql.Date;
```


## Konkurensi yang Lebih Aman dan Efisien

Java 25 menghadirkan peningkatan besar dalam manajemen *multi-threading* dan komputasi paralel.


### [Structured Concurrency (JEP 505)](https://openjdk.org/jeps/505)

menyediakan cara baru untuk mengelola task concurrency agar kode lebih mudah dibaca, serta memastikan lifecycle setiap thread berjalan dalam konteks yang jelas.
konsepnya mirip dengan structured concurrency di Kotlin atau Go — semua thread bersifat terstruktur dan otomatis dibersihkan jika gagal.


### [Scoped Values (JEP 506)](https://openjdk.org/jeps/506)

alternatif efisien untuk ThreadLocal.
scoped Values memungkinkan berbagi data antar-thread secara aman tanpa overhead besar, ideal untuk sistem dengan banyak thread seperti web server atau microservices.


### [Vector API Enhancement (JEP 508)](https://openjdk.org/jeps/508)

peningkatan signifikan pada vector computation memungkinkan Java melakukan operasi vektor yang menyamai komputasi skalar dalam kecepatan.


## Spesifikasi Bahasa

Java 25 juga membawa sejumlah perubahan sintaks sehingga penulisan lebih sederhana dan aman


### [Primitive Types in Patterns, instanceof, and switch (JEP 507)](https://openjdk.org/jeps/507)

memungkinkan pattern matching langsung pada tipe data primitif seperti int, byte, atau double.
sebelumnya hanya bisa dilakukan pada objek. kini, kode menjadi lebih ringkas dan aman tanpa perlu casting manual.

```java
if (x instanceof int i && i > 0) {
    System.out.println("Positive number: " + i);
}
```

### [Flexible Constructor Bodies (JEP 513)](https://openjdk.org/jeps/513)

menambahkan fleksibilitas pada constructor sehingga inisialisasi tertentu bisa dilakukan sebelum pemanggilan super(...) atau this(...).
ini memberikan fleksibilitas bagi developer untuk mengatur urutan inisiasi


# Mengapa Penting?

dengan status LTS (Long-Term Support) hingga 2035, versi ini memberikan:

- stabilitas jangka panjang untuk proyek besar dan enterprise
- peningkatan performa signifikan di JVM dan vector computation
- fitur modern yang membuat Java lebih bersahabat untuk developer baru
- ekosistem modular dan konkurensi yang lebih efisien

kalau anda masih menggunakan Java 8, 11, atau bahkan 17 — Java 25 adalah target migrasi yang sangat layak untuk dipertimbangkan.


# Dah.

Java 25 terasa seperti perubahan besar bagi ekosistem Java: lebih modern, lebih ringkas, dan lebih kuat.
tidak hanya menyederhanakan cara menulis kode, tapi juga mempersiapkan fondasi untuk masa depan komputasi paralel dan modular di platform Java.

sampai bertemu di rilis selanjutnya.
