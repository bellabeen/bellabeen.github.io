---
title: "[laravel] Memahami View"
layout: post
date: '2020-03-30 15:15:56'
headerImage: false
star: true
tag:
- php
- laravel
category: blog
author: bellabeen
---

## Pendahuluan
Pada artikel sebelumnya kalian sudah membuat route dan menampilkan pesan berupa string sebagai respon untuk halaman homepage dan about. Laravel menganut pola Model-View-Controller (MVC) dengan V sebagai **view**. Bagaimana memberikan respon berupa view pada laravel? Latihan berikut ini kita akan mengganti respon string untuk halaman homepage dan about menjadi respon view

## Isi
Buat sebuah file bernama **homepage.php** , kemudian simpan di folder **laravelapp/resources/views**
{% highlight html %}
<div id="homepage">
<h2>Homepage</h2>
<p>Selamat belajar laravel!</p>
</div>
{% endhighlight %}

Buat sebuah file bernama **about.php**, kemudian simpan di folder **laravelapp/resources/views**
{% highlight html %}
<div id="about">
<h2>About</h2>
<p>Aplikasi <strong>laravelapp</strong> dibuat sebagai latihan untuk mempelajari Laravel</p>
</div>
{% endhighlight %}

Buka file **web.php** lalu ubah isinya menjadi
{% highlight html %}
Route::get('/', function () {
    return view('homepage');
});

Route::get('/about', function(){
    return view('about');
});
{% endhighlight %}

Fungsi **view()** adalah fungsi helper laravel untuk memanggil file view. Untuk memanggil suatu file view, kita cukup memberikan namanya tanpa ekstensi **.php**, seperti pada route diatas. Laravel akan mencari dilokasi default file file view disimpan, yaity folder **laravelapp*/resources/views**
Pada browser kita coba refresh maka tampilan halaman homepage berubah menjadi seperti ini
![Markdowm Image][1]
<figcaption class="caption">Halaman Homepage</figcaption>

Coba akses link **about** , maka tampilan halaman about menjadi seperti gambar dibawah ini
![Markdowm Image][2]
<figcaption class="caption">Halaman About</figcaption>

Bagaimana kalau viewnya itu berada didalam sub folder?
Secara default lokasi penyimpanan view berada difolder **laravelapp/resources/views**. Jika jumlah view masih sedikit tidak jadi masalah, jika jumlah view semakin banyak maka akan sulit untuk mengaturnya.
Agar file - file view itu lebih teratur dan terorganisasi , maka kita harus mengelompokkan di foldernya masing - masing. Folder folder tersebut akan menjadi sub-folder didalam folder **laravelapp/resources/views**

Buat folder bernama **pages** didalam folder **views**, sehingga terbentuk **laravelapp/resources/views/pages**. Lalu cut/pindahkan file homepage.php dan about.php ke folder pages. Rubah isi file **web.php** menjadi
{% highlight html %}
Route::get('/pages/homepage', function () {
    return view('pages/homepage');
});

Route::get('/pages/about', function(){
    return view('pages.about');
});
{% endhighlight %}

## Penutup
Mungkin itu sedikit pengenalan mengenai view, see you ditutorial selanjutnya.
[1]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/tutorial-laravel/view/1.png
[2]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/tutorial-laravel/view/3.png
