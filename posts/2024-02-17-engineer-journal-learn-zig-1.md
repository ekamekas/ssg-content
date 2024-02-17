+++
title = 'Engineer Journal - Learn Zig #1'
description = 'berkenalan dengan Zig - sebuah bahasa pemrograman umum yang katanya seperti C dalam hal performa, namun lebih baik. tapi bukankan sudah ada Go dan Rust? yang bahkan lebih dewasa serta juga battle-tested. artikel ini akan sebagai awal dari jurnal penulis dalam mempelajari Zig, perbandingan dengan bahasa lain, dan aplikasinya dalam kebutuhan penulis.'
#tldr = 'Summary to SHOW on detail'
date = '2024-02-17T18:57:01+07:00'
draft = false
tags = ['zig','engineer-journal']
+++

# Pengenalan

selamat datang dan selamat membaca. sesi ini akan berisikan perjalanan dalam mempelajari [*Zig - a general-purpose programming language*](https://ziglang.org/). target pembaca utama para pemula dan pelajar 'this Zig' karena penulis sendiri juga seorang pemula.

{{< figure src="https://ziglang.org/zero.svg" width="50%" >}}

jadi, mari mulai perjalanan ini.

{{< callout emoji="⚡️" text="perlu diinformasikan juga bahwa penulis memiliki latarbelakang sebagai backend engineer yang umumnya membangun aplikasi web/mobile dengan JavaScript, Java, dan Kotlin. sehingga tulisan ini dapat terpengeruh pengetahuan dan pengalaman yang penulis punya" >}}

# Zig?

> Zig is a general-purpose programming language and toolchain for maintaining robust, optimal and reusable software.

dikutip dari halaman depan [Website Zig](https://ziglang.org) bahwa Zig merupakan bahasa pemrograman:

- *general-purpose* -> digunakan untuk membuat software/program dengan berbagai kasus penggunaan
- *robust* -> program yang dijalankan sesuai dengan apa yang ditulis, apapun kondisinya. bahkan ketika _out-of-memory_ sekalipun
- *optimal* -> menghasilkan kode dan program berjalan se-efektif dan efisien mungkin dalam hal performa
- *reusable* -> kode dapat digunakan kembali sebagai bagian dari program lain, atau menjadi program pada platform yang berbeda

secara umum, [Andrew Kalley - pembuat Zig](https://andrewkelley.me/) ingin membuat bahasa layaknya C namun lebih baik.

# Bahasa Layaknya C

pada [presentasinya](https://www.youtube.com/watch?v=Gv2I7qTux7g), Andrew menjelaskan cara untuk membangun program berkualitas:

- 👎🏻 garbage collector: program ini akan membuat glitch latensi proses yang tidak dapat diprediksi 
- 👎🏻 auto heap allocator: membuat crash program saat _out-of-memory_
- 👎🏻 hidden control flow: menghasilkan banyak tingkah laku program yang tak terdefinisi. sulit untuk debug, integrasi dengan IDE, dan diinterpretasi oleh developer
- 👍🏻 fast like C or better: sip!

jika demikian, mengapa tidak menggunakan C? atau ada juga Rust dan Golang. Golang memiliki garbage collector dan auto head allocator. Rust memiliki domain knowledge bahasa yang rumit seperti konsep pinjam dan meminjamkan. dan C, memiliki hidden control flow dengan directive dan preprosessornya

misal pada C, 
```c
// hello.c
#include <stdio.h>

int main(int argc, char **argv){
  if(true) {
    printf("hello\n"); 
  } else {
    printf("uh?what?\n");
  }
}
```
kode di atas akan menghasilkan teks "hello" pada konsol.
```shell
$ gcc -o hello hello.c
$ ./hello
$ hello
```
namun, behavior berbeda apabila terdapat modifikasi directive,
```c
// hello.c
#include <stdio.h>
#undef true
#undef false
#define true 0
#define false 1

int main(int argc, char **argv){
  if(true) {
    printf("hello\n"); 
  } else {
    printf("uh?what?\n");
  }
}
```
akan menghasilkan program dengan hasil tak sesuai ekspektasi.
```shell
$ gcc -o hello hello.c
$ ./hello
$ uh?what?
```
bayangkan itu ada di library terdalam yang anda gunakan :O


# Projects <3 Zig

beberapa project dan perusahaan yang sudah adopsi Zig:

- *bun* -> JavaScript runtime yang ngebut -> [Site](https://bun.sh/)
- *Uber* -> adoptasi dalam bentuk compiler, untuk support infrastructure yang banyaknya menggunakan arm64 -> [Blog](https://www.uber.com/en-ID/blog/bootstrapping-ubers-infrastructure-on-arm64-with-zig/)
- *Tigerbeetle* -> db dengan penggunaan khusus untuk data model finansial -> [Site](https://tigerbeetle.com/)

# Dah.

penulis sendiri ingin mencoba mempelajari bahasa ini agar menambah opsi bahasa low-level yang dapat digunakan saat membangun solusi yang memiliki performa baik namun optimal. 

Beberapa bahasa pernah penulis pelajari dan implementasikan di pekerjaa.
- JavaScript -> bahasa yang diinterpretasikan dan dijalankan di runtime engine seperti NodeJs dan bun
- Java -> menggunakan pendekatan hybrid namun JVM mengambil resource memori yang tinggi
- Go -> bahasa yang kemudian di-compile ke host native, namun masih memiliki garbage collector
- Rust -> memiliki konsep yang sulit untuk dipahami. sebelum program jalan dengan aman, pengembangkan dipaksa 'bergelut' dengan compiler.

tidak lengkap rasanya jika tidak menyertakan 'hello world',
```zig

// hello.zig
const std = @import("std");

pub fn main() void {
    std.log.debug("hello world\n", .{});
}
```
akan menghasilkan program dengan hasil "hello world". tentunya
```shell
$ zig build-exe hello.zig
$ ./hello
$ hello world
````

dokumentasi lain untuk mempelajari Zig:
- [zig.guide](https://zig.guide/): A structured introduction to Zig
- [Ziglings](https://ziglings.org/): Learn Zig by fixing tiny broken programs
- [Zig on Exercism](https://exercism.org/tracks/zig): Solve coding exercises and get mentored to develop fluency in Zig

sampai bertemu di post lain
