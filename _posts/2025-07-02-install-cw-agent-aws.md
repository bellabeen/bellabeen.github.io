---
title: How to Install Cloudwatch Agent AWS on Linux
date: 2025-07-02 09:03:30 +0700
categories: [aws, ec2]
tags: [cloud, infrastructure]     # TAG names should always be lowercase
---

**Amazon CloudWatch Agent** adalah alat yang memungkinkan Anda untuk memonitor log, metrik, dan performa sistem secara real-time. Ini cocok untuk Anda yang ingin mengintegrasikan CloudWatch dengan log dan metrik dari aplikasi atau server Anda.

## **Langkah 1: Unduh CloudWatch Agent**
Pertama, kita perlu mengunduh file instalasi CloudWatch Agent dari repositori resmi AWS menggunakan perintah berikut:
```bash
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
```

## **Langkah 2: Instalasi CloudWatch Agent**
Setelah file .deb berhasil diunduh, instal paket tersebut dengan perintah:
```bash
sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
```
Perintah ini akan memasang CloudWatch Agent di sistem Anda.

## **Langkah 3: Konfigurasi CloudWatch Agent Template**
Konfigurasi CloudWatch Agent dapat disesuaikan sesuai kebutuhan Anda. Buat atau edit file konfigurasi di lokasi berikut:
```bash
sudo nano /opt/aws/amazon-cloudwatch-agent/bin/config.json
```
Gunakan konfigurasi berikut sebagai contoh:

```json
{
    "agent": {
        "metrics_collection_interval": 60,
        "run_as_user": "root"
    },
    "logs": {
        "logs_collected": {
            "files": {
                "collect_list": [
                    {
                        "file_path": "/path-log/example-server.log",
                        "log_group_name": "example-server.log",
                        "log_stream_name": "{instance_id}"
                    }
                ]
            }
        }
    },
    "metrics": {
        "append_dimensions": {
            "AutoScalingGroupName": "${aws:AutoScalingGroupName}",
            "ImageId": "${aws:ImageId}",
            "InstanceId": "${aws:InstanceId}",
            "InstanceType": "${aws:InstanceType}"
        },
        "aggregation_dimensions": [
            ["AutoScalingGroupName"],
            ["AutoScalingGroupName", "InstanceType"],
            ["AutoScalingGroupName", "ImageId"]
        ],
        "metrics_collected": {
            "cpu": {
                "measurement": [
                    "cpu_usage_idle",
                    "cpu_usage_iowait",
                    "cpu_usage_user",
                    "cpu_usage_system"
                ],
                "metrics_collection_interval": 10
            },
            "disk": {
                "measurement": [
                    "used_percent",
                    "inodes_free"
                ],
                "metrics_collection_interval": 10
            },
            "diskio": {
                "measurement": [
                    "io_time"
                ],
                "metrics_collection_interval": 10
            },
            "mem": {
                "measurement": [
                    "mem_used_percent"
                ],
                "metrics_collection_interval": 10
            },
            "swap": {
                "measurement": [
                    "swap_used_percent"
                ],
                "metrics_collection_interval": 10
            }
        }
    }
}
```
> Template diatas, menggabungkan semua instance dalam AutoScalling untuk metric yang dipakai.
{: .prompt-info }

Pastikan kalian mengganti parameter sesuai kebutuhan, seperti `file_path` untuk lokasi log dan `log_group_name` untuk grup log CloudWatch.

## **Langkah 4: Instalasi CollectD**
Untuk pengumpulan metrik tambahan, bisa menggunakan collectd, instal **CollectD** menggunakan perintah berikut:
```bash
sudo apt install collectd
```

## **Langkah 5: Menjalankan CloudWatch Agent**
Setelah semua selesai, jalankan CloudWatch Agent dan gunakan konfigurasi yang telah Anda buat:
```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -s -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s
```
Perintah diatas menjalankan konfigurasi dan memulai CloudWatch Agent.

---

## **Kesimpulan**
Jangan lupa untuk memastikan instance Anda memiliki izin IAM yang cukup untuk mengirim log dan metrik ke CloudWatch.

Semoga bermanfaat! Jika ada pertanyaan, jangan ragu untuk meninggalkan komentar. 😊