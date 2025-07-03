---
title: How to Setup Password Authentication For AWS EC2 Instances
date: 2025-07-04 09:55:30 +0700
categories: [aws, ec2]
tags: [cloud, infrastructure]     # TAG names should always be lowercase
---

![TEXT](/assets/img/post/ssh_ec2.png)

**What is the default password for ec2?**
By default, ec2 instances *don’t have password authentication*. You have to use the private key to connect to the instances.
So it is possible to set up an ec2 user password manually.
## **Step 1:**
Log in to the server using the ssh client of your choice using the private key or SSM (Session Manager). For Windows machines, you can use putty for connecting to the instance. 
If you are using Mac or Linux, you can use the following command to connect to the instance.
```bash
ssh -i your-key.pem username@(public-ip-address/private-ip-address)
```
## **Step 2:**
Open the sshd_config file
```bash
sudo vi /etc/ssh/sshd_config
```
## **Step 3:**
Find the line containing “PasswordAuthentication parameter and change its value from “no” to “yes“
```bash
PasswordAuthentication yes
```
If you want to set up “root” ec2 user password, find  “PermitRootLogin” parameter and change its value from “prohibit-password” to “yes“
After the changes, save the file and exit.
## **Step 4:**
Setup ec2 user password using the “passwd” command along with the username.  
You need to enter the password twice. For example, if you want to set up a password for “ubuntu” user, use the following command.
```bash
sudo passwd ubuntu
```
In AWS, different ec2 instances have different user names. Following are the default usernames of common ec2 instances.
For details, you can visit this [reference](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-users.html#ami-default-user-names).
## **Step 5:**
Now, restart the “sshd” service using the following command.
```bash
sudo service sshd restart
```
## **Step 6:**
Now you can log out and log in using the password you set for the user. For example,
```bash
ssh ubuntu@xxxx.yyyy.zzzz.aaa
```