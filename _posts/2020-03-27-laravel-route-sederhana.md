---
title: "[laravel] Membuat Route Untuk Halaman"
layout: post
date: '2020-03-27 19:15:56'
headerImage: false
star: true
tag:
- php
- laravel
category: blog
author: bellabeen
---

## Pendahuluan
Sejauh ini kita memiliki satu route, yaitu route halaman homepage. Bagaimana kalau kita menambahkan route untuk sebuah halaman? Disini kita buat route untuk halaman "about"

## Isi
Buka file **web.php** tambahkan code dibawah ini untuk menambahkan route halaman about
{% highlight html %}
Route::get('/about', function(){
    return 'Aplikasi <strong>laravelapp</strong> dibuat
    sebagai latihan untuk mempelajari Laravel 6.*';
});
{% endhighlight %}
Dibrowser ketik **http://127.0.0.1:8001/about** ip dan port sesuaikan dengan ip dan port yang running dilaptop kalian.
![Markdowm Image][1]
<figcaption class="caption">Halaman About</figcaption>
Kalian bisa gambar diatas hasil dari membuat route halaman about, sehingga sekarang mempunyai dua route yaitu halaman homepage dan halaman about. Jika kalian masih bingung, kalian bisa menghapus isinya lalu mencoba membuat route route yang lain. Kode dibawah ini merupakan hasil dari 2 route yang telah kita buat
{% highlight html %}
Route::get('/', function(){
    return 'Halaman Homepage. <br>
    Selamat belajar laravel';
});

Route::get('/about', function(){
    return 'Aplikasi <strong>laravelapp</strong> dibuat
    sebagai latihan untuk mempelajari Laravel 6.*';
});
{% endhighlight %}


## Penutup
Coba kalian kreasikan dan eksperimen route yang lainnya agar kalian lebih paham mengenai route. Sekian tutorial membuat route untuk sebuah halaman. Untuk tutorial selanjutnya kalian bisa akses link ini [[laravel] Tutorial Laravel Series](https://bellabeen.github.io/tutorial-laravel-series/)

[1]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/tutorial-laravel/routing/2.png