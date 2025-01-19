---
title: Can't SSH to EC2 Instance AWS
date: 2025-01-20 10:03:30 +0700
categories: [aws, ec2]
tags: [cloud, infrastructure]     # TAG names should always be lowercase
---

Mungkin ini case yang tergolong aneh ya, karena saya coba membaca beberapa dokumentasi dari aws terkait tidak bisa melakukan ssh sudah saya lakukan bisa cek disini [https://aws.amazon.com/id/premiumsupport/knowledge-center/ec2-linux-ssh-troubleshooting/](https://aws.amazon.com/id/premiumsupport/knowledge-center/ec2-linux-ssh-troubleshooting/) , oh iya btw ssh yang saya lakukan ini melalui onpremis dengan os ubuntu 20.04, ubuntu 18.04, dan ubuntu14.04 ya dikarenakan kantor saya menggunakan hybrid cloud menggunakan onprem dan public cloud.

Singkat cerita sudah dilakukan pengecheckan oleh saya bersama dengan tim network , mulai cek tunnel onprem ke AWS via forti statusnya up, tunnel dari AWS ke onprem via aws console statusnya UP, saya coba test ping bisa, saya coba telnet ke port 22 bisa, saya coba ssh nggak bisa, security group sudah ditambahkan dengan spesifik ip dari vm yang ingin melakukan ssh tetap tidak bisa. Ini sangat #WHY

Coba saya debug lah koneksi dari onprem ke aws , ditemukan error.

![TEXT](/assets/img/post/2025-01-20.png)

> debug1: kex: algorithm: curve25519-sha256
debug1: kex: host key algorithm: ecdsa-sha2-nistp256
debug1: kex: server->client cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none
debug1: kex: client->server cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none
debug1: expecting SSH2_MSG_KEX_ECDH_REPLY
{: .prompt-warning }

Bisa dibilang error tersebut karena tidak ada reply dari sisi servernya. Coba cek cek ternyata MTU dari interface yang ada di aws lebih besar dari mtu yang ada di onprem. Apa sih MTU? berdasarkan penjelasan dari [https://www.cloudflare.com/learning/network-layer/what-is-mtu/](https://www.cloudflare.com/learning/network-layer/what-is-mtu/) singkatnya besar paket data yang terhubung kejaringan dan akan diterima oleh perangkat. Bisa dicek [https://bugs.launchpad.net/ubuntu/+source/openssh/+bug/1254085/comments/9](https://bugs.launchpad.net/ubuntu/+source/openssh/+bug/1254085/comments/9) ada bug dari sisi OpenSSH yang digunakan oleh Ubuntu

Bisa coba pakai command dibawah ini untuk melakukan perubahan.

> sudo ip li set mtu 1200 dev wlan0
{: .prompt-info }

atau

> sudo ifconfig wlan0 mtu 1200
{: .prompt-info }

Wlan0 bisa diganti dengan interface yang kalian gunakan. Semoga bermanfaat ini sudah di tes di Ubuntu 20.04, 18.04 dan 14.04 dan berhasil