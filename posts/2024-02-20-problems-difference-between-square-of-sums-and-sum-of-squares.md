+++
title = 'Problem - Difference Between Square of Sums and Sum of Squares'
description = 'penyelesaian perbedaan antara dua operasi dengan menggunakan dua pendekatan yang berbeda. math is "fun" :)'
#tldr = 'Summary to SHOW on detail'
date = 2024-02-22
draft = false
tags =  ['fundamental']
+++

diketahui sebuah persoalan dimana kita diperlukan untuk mencari perbedaan antara nilai dari kuadrat atas penjumlahan angka dari 1 ke N dan nilai dari jumlahan dari angka 1 ke N yang dikuadratkan, dimana N adalah bilangan asli. apabila ditulis dalam notasi matematika, kita mencari hasil dari (Σn)² - (Σn²).

masalah ini dapat diselesaikan menggunakan dua pendekatan:

- brute force O(N)
- pendekatan yang lebih efisien O(1)

# Brute force

dengan pendekatan ini, kita akan melakukan looping untuk melakukan penjumlahan yang kemudian hasilnya kita proses kembali.

```pseudocode
masukkan: N adalah bilangan asli

definisikan SUM dengan nilai awal nol
definisikan SUM_OF_SQUARE dengan nilai awal nol

untuk setiap nilai I dari satu hingga N:
  SUM ditambahkan dengan I
  SUM_OF_SQUARE ditambahkan dengan hasil pangkat 2 dari I

keluaran: SUM dikali SUM dikurangi SUM_OF_SQUARE
```

apabila kita implementasikan dengan Zig, maka akan menjadi:

```Zig
pub fn differenceOfSquare(number: usize) usize {
  var sum: usize = 0;
  var sumOfSquare: usize = 0;

  for(1..number+1) |i| {  // for loop Zip is exclusive at the end
    sum += i;
    sumOfSquare += i*i;
  }

  return sum*sum - sumOfSquare;
}
```

karena ada for loop di situ, maka proses dilakukan sebanyak N kali.

# Pendekatan Optimal

pendekatan yang lebih optimal dilakukan dengan mengetahui sifat dari soal itu sendiri. dimana kita melakukan penyerderhanaan persamaan. mari kita lihat sebuah fungsi F(N) yang merupakan jumlahan dari angka 1 hingga N dan G(N) yang merupakan jumlahan dari angka 1 hingga N yang dikuadratkan.

```
F(N) = 1 + 2 + 3 + ... + N
```

yang apabila ditulis dengan dibalik, akan menjadi:

```
F(N) = N + (N-1) + (N-2) + ... + 1
```

ini menarik karena dua bentuk F(N) memiliki pola ketika disandingkan. yaitu setiap nilai pada index akan membuat persamaan N+1.

```
F(N) = 1 +   2   +   3   + ... + N
F(N) = N + (N-1) + (N-2) + ... + 1
-----------------------------------(+)
2*F(N) = (N+1) + (N+1) + (N+1) + ... + (N+1) = N * (N+1) / 2
```

kita dapat menyerderhanakan F(N) = Σn = N * (N+1) / 2. tidak perlu loop. dengan menggunakan pendekatan yang sama, kita dapat lakukan penyerderhanaan pada G(N).

pendekatan penyerdehanaan G(N) berakar atas persamaan kubik sebuah polinomial.

```
a^3 - b^3 = (a - b)(a^2 + ab + b^2)
```

apabila nilai `a` kita ganti menjadi `n` dan `b` menjadi `n-1` terus menerus hingga `a` bernilai `1` dan b menjadi `0`, maka kita akan mendapatkan hubungan dengan penyerderhanaan fungsi G(N).

```
H(N) = n^3 - (n-1)^3 = (n - (n-1))[(n^2) + (n*(n-1)) + (n-1)^2]
H(N) = n^3 - (n-1)^3 = 3n^2 - 3n + 1

H(N-1) = (n-1)^3 - (n-2)^3 = 3(n-1)^2 - 3(n-1) + 1
H(N-2) = (n-1)^3 - (n-2)^3 = 3(n-1)^2 - 3(n-1) + 1
...
...
H(2) = 2^3 - 1^3 = 3(2)^2 - 3(2) + 1
H(1) = 1^3 - 0^3 = 3(1)^2 - 3(1) + 1
```

apabila seluruh fungsi H dijumlahkan dari 1 hingga ke N, maka akan menjadi G(N). kita dapat melihat pola yang dapat menjadi 0 ketika dijumlahkan.

```
H(N) = n^3 - (n-1)^3 = 3n^2 - 3n + 1

H(N-1) = (n-1)^3 - (n-2)^3 = 3(n-1)^2 - 3(n-1) + 1
H(N-2) = (n-1)^3 - (n-2)^3 = 3(n-1)^2 - 3(n-1) + 1
...
...
H(2) = 2^3 - 1^3 = 3(2)^2 - 3(2) + 1
H(1) = 1^3 - 0^3 = 3(1)^2 - 3(1) + 1
-----------------------------------------------------------------+
G(N) = ΣH(N) = n^3 = 3Σn^2 - 3Σn + n
G(N) = 3Σn^2 = n^3 + 3(n(n+1))/2 + n
G(N) = Σn^2 = [n(n+1)(2n+1)]/6
```

setelah kita mendapatkan fungsi F(N) dan G(N), kita dapat mengimplemtasikannya ke dalam kode.

```Zig
pub fn differenceOfSquare(number: usize) usize {
  var sum: usize = 0;
  var sumOfSquare: usize = 0;

  for(1..number+1) |i| {  // for loop Zip is exclusive at the end
    sum += i;
    sumOfSquare += i*i;
  }

  return number * (number+1) / 2 - number * (number+1) * (2*number+1) / 6;
}
```

hasilnya akan sama dengan menggunakan O(1) operasi.

# Dah.

sering kali untuk melakukan peningkatan performa kita harus memahami pola dan karakter sebuah persoalan. seperti penjumlahan ini yang ternyata dapat dihubungkan dengan sebuah persamaan binominal. yah walaupun dalam pekerjaan sehari-sehari penulis sangat cukup menggunakan for-loop.

sampai bertemu di post lain.
