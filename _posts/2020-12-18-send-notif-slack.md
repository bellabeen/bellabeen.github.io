---
title: "Send Notifikasi Slack With Webhook"
layout: post
date: '2020-12-18 09:22:56'
headerImage: false
star: true
tag:
- php
category: blog
author: bellabeen
---

Hai apakabar? 
Sudah lama tidak menulis lagi. Pada kesempatan kali ini saya mau bahas membuat notifikasi yang langsung dipush langsung ke slack menggunakan webhook. Notifikasi ini bertujuan untuk melakukan pengecekan pada sebuah folder dimana file pada folder tersebut telah dikirimkan pada periode tertentu, jadi kita tidak perlu melakukan pengecekan secara manual di folder tersebut. Untuk penjelasan apa itu slack, webhook bisa disearch ya atau next time akan kita bahass

1. Pastikan kalian sudah punya akun slack 
2. Pastikan kalian punya workspace yang ingin dipush dinotifikasinya
3. Kalian bisa melihat didokumentasi slack <https://api.slack.com/tutorials/slack-apps-hello-world> untuk membuat apps dislack untuk membantu mengirimkan notifikasi keslack 
4. Buat channel sendiri (*optional*s) untuk testing sebelum naik ke production
5. Kalau semua sudah disiapkan kalian bisa gunakan simple code dibawah ini untuk mengirimkan notifikasi dislack based on *php*

{% gist bellabeen/078a4aba35a50206da1e2073615904db %}

**Hasilnya notifikasi slack**

![Markdowm Image][1]
<figcaption class="caption">Send Notifikasi Slack</figcaption>

Untuk otomasisasi sesuai dengan schedule bisa gunakan cronjob untuk *triggered* file tersebut

Oke mungkin sekian kalau ada pertanyaan atau bingung kalian bisa chat saya di <https://t.me/bellabeen>

Next time, semoga bisa didevelop bot slack untuk proses yang masih dilakukan oleh manusia. See you~

[1]: https://raw.githubusercontent.com/bellabeen/bellabeen.github.io/master/assets/images/send-notif-mft.jpeg