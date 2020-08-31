---
title: "[laravel] Blade Dengan Bootstrap Bagian II"
layout: post
date: '2020-04-11 15:15:56'
headerImage: false
star: true
tag:
- php
- laravel
category: blog
author: bellabeen
---
## Pendahuluan
Tampilan laravelapp sampai saat ini sangat sederhana, ada baiknya kita menggunakan bootstrap untuk merapikannya.
Download materialnya disini

{% highlight html %}
https://cdnjs.cloudflare.com/ajax/libs/html5shiv/3.7.3/html5shiv-printshiv.min.js
{% endhighlight %}

{% highlight html %}
https://cdnjs.cloudflare.com/ajax/libs/jquery/3.4.1/jquery.min.js
{% endhighlight %}

{% highlight html %}
https://cdnjs.cloudflare.com/ajax/libs/respond.js/1.4.2/respond.min.js
{% endhighlight %}

{% highlight html %}
https://codeload.github.com/twbs/bootstrap/zip/v4.4.1
{% endhighlight %}

Ekstrak semua file .zip lalu ambil beberapa file 
Buat folder css didalam folder **laravelapp/public/css** dan buat folder js juga didalam folder **laravelapp/public/js**. Apabila file tersebut berekstensi **.css** masukkan kedalam folder css dan apabila file tersebut berekstensi **.js** masukkan kedalam folder js. Bisa lihat gambar dibawah ini untuk lebih jelasnya
![Markdowm Image][1]
<figcaption class="caption">Hirarki File</figcaption>

Buka file **views/template.blade.php** ubah isinya menjadi :

{% highlight html %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>LaravelApp</title>
    <link rel="stylesheet" type="text/css" href="{{ asset('/css/bootstrap.min.css') }}">
    <link rel="stylesheet" type="text/css" href="{{ asset('/css/style.css') }}">
    <link rel="stylesheet" type="text/css" href="{{ asset('/css/bootstrap.min.css') }}">
    <script type="text/javascript" src="{{ asset('/js/html5shiv.min.js') }}"></script>
    <script type="text/javascript" src="{{ asset('/js/respond.min.js') }}"></script>
</head>
<body>
<div class="container">
    @yield('main')
</div>

    @yield('footer')

    <script type="text/javascript" src="{{ asset('/js/jquery.min.js') }}"></script>
    <script type="text/javascript" src="{{ asset('/js/bootstrap.min.js') }}"></script>
    

</body>
</html>
{% endhighlight %}

**Keterangan**
{% highlight html %}
<link rel="stylesheet" type="text/css" href="{{ asset('/css/bootstrap.min.css') }}">
{% endhighlight %}

Kode diatas merupakan code yang berfungsi memanggil file css

{% highlight html %}
<script type="text/javascript" src="{{ asset('/js/bootstrap.min.js') }}"></script>
{% endhighlight %}

Kode diatas merupakan code yang berfungsi memanggil file js

Lalu buat file **style.css** pada public/css/, lalu ubah isinya menjadi seperti ini :
{% highlight html %}
html {
    position: relative;
    min-height: 100%;
}

body {
    margin-bottom: 70%;
}

#footer {
    position: absolute;
    bottom: 0;
    width: 100%;
    background-color: #f5f5f5;
    font-size: 0.9em;
    height: 50px;
    text-align: center;
    padding-top: 20px;
}
{% endhighlight %}

Pada browser akses file **/siswa** kira kira tampilannya seperti gambar dibawah ini
![Markdowm Image][2]
<figcaption class="caption">Halaman Siswa</figcaption>


[1]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/tutorial-laravel/blade/3.png
[2]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/tutorial-laravel/blade/4.png





