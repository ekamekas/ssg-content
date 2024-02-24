+++
title = 'Database Engineering - Parameterized Query Pada PostgreSQL'
#description = 'Summary to SHOW on post list'
#tldr = 'Summary to SHOW on detail'
date = 2024-02-24T11:27:08+07:00
draft = false
tags =  ['database-engineering']
+++

pada saat di universitas, penulis pertama kali mendengar istilah 'injection attack' dalam kelas __cyber security__.
setelah beberapa tahun berkontribusi di industri IT, istilah ini pun tetap disinggung. lalu, apa itu 'injection attack' itu?
'injection attack' adalah serangan yang menyisipkan kode berbahaya pada sistem melalui media seperti form. 
salah satunya adalah 'SQL injection attack', dimana kita memasukkan pola SQL tertentu sehingga mendapatkan informasi yang sebetulnya tidak kita dapatkan aksesnya.

misal kita memiliki form login untuk memasukkan username dan password,

username: user_akoe
password: password_''; DROP TABLE user_table;

yang terjadi, apabila tidak ada sanitasi SQL apapun adalah, akan mengekseksui Query

```SQL
SELECT * FROM user_table WHERE username = 'user_akoe' AND password = 'password_'''; DROP TABLE user_table;
```

yang merupakan valid SQL. alhasil, user memang gagal untuk otentikasi; namun, seluruh data pada database akan hilang.

# Parameterized Query

opsi untuk mengatasi hal tersebut adalah dengan menggunakan parameterized query. lainnya mungkin melakukan sanitasi dari level aplikasi.

dalam database postgreSQL sendiri, telah tersedia command 'PREPARE'.
command ini akan menyiapkan sebuah statement yang akan diparsing, analisa, serta dioptimasi ketika dieksekusi.
terkait parameterized query, 'PREPARE' dapat menerima definisi parameter yang nilainya akan diberikan ketika dieksekusi. berikut syntaxnya:

```SQL
PREPARE statement_name (data_type, data_type, ...) AS
    SQL_QUERY;S
```

dan untuk dieksekusi, jalankan

```SQL
EXECUTE statement_name (value, value, ...);
```

# Dah.

sampai bertemu di post lain.
