---
title: 'Instalasi Cowrie Honeypot di Ubuntu Desktop 20.04'
description: 'Panduan upgrade Python (3.10+) dan instalasi Cowrie (SSH/Telnet honeypot) untuk mencatat brute force dan interaksi shell.'
pubDate: 'Dec 19 2025'
heroImage: '../../assets/honeypotcover.jpg'
---

Honeypot adalah sistem atau umpan yang sengaja dirancang untuk menarik dan menjebak penyerang siber, mengalihkan mereka dari sistem nyata, dan memungkinkan Anda mempelajari metode serangan mereka. Salah satu contoh honeypot yang populer adalah **Cowrie**.

Cowrie adalah honeypot **SSH** dan **Telnet** dengan tingkat interaksi menengah hingga tinggi (*medium-to-high interaction*) yang dirancang untuk mencatat serangan **brute force** dan interaksi *shell* yang dilakukan oleh penyerang.

> Referensi dokumentasi resmi Cowrie:  
> https://docs.cowrie.org/en/latest/INSTALL.html

---

#### Kebutuhan

- **Python 3.10+**
- **python-virtualenv / venv**

Karena Python bawaan dari Ubuntu Desktop 20.04 umumnya masih versi **3.8.x**, maka kita perlu melakukan **upgrade versi Python** terlebih dahulu.

---
.
#### Upgrade Python (Metode PPA deadsnakes) :
.

---

##### 1) Perbarui indeks paket & install dependensi PPA

```bash
sudo apt update
sudo apt install -y software-properties-common
```
![Alt text](../../image/honeypotcover.jpg)

##### 2) Tambah PPA 

```bash
sudo add-apt-repository ppa:deadsnakes/ppa
```

##### 3) Install Python 3.10

```bash
sudo apt install python3.12 python3.12-distutils python3.12-venv
```

##### 4) Verifikasi instalasi

```bash
python3.12 - version
```


---
.
#### Metode lain apabila PPA gagal :
.

---

##### 1) Update sistem & install dependensi

```bash
apt update && sudo apt upgrade -y
apt install -y software-properties-common build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev curl libsqlite3-dev wget llvm libbz2-dev tk-dev
```

##### 2) Download source code python

```bash
cd /usr/src
wget https://www.python.org/ftp/python/3.12.4/Python-3.12.4.tgz
tar -xf Python-3.12.4.tgz
cd Python-3.12.4
```

##### 3) Compile dan install python
```bash
./configure - enable-optimizations
make -j$(nproc)
make altinstall
```

---
.
#### Instalasi Cowrie
Setelah berhasil mengupgrade versi python, sekarang kita akan mencoba melakukan instalasi cowrie.

---


##### 1) install dependensi

```bash
sudo apt-get install git python3-pip python3-venv libssl-dev libffi-dev build-essential libpython3-dev python3-minimal authbind
```

##### 2) Buat akun user
```bash
sudo adduser - disabled-password cowrie
sudo su - cowrie
```

##### 3) Clone github

```bash
git clone http://github.com/cowrie/cowrie
cd cowrie
```

##### 4) Setup virtual environment

Buat virtual environment.
```bash
pwd
python3.12 -m venv cowrie-env
```

Aktivasi virtual environment.
```bash
source cowrie-env/bin/activate
python3.12 -m pip install - upgrade pip
python3.12 -m pip install -e .
```

##### 5) Instalasi file konfigurasi

Konfigurasi untuk Cowrie disimpan di cowrie.cfg.dist dan cowrie.cfg (terletak di cowrie/etc). Kedua file dibaca saat startup, di mana entri dari cowrie.cfg diutamakan. File .dist dapat ditimpa oleh peningkatan, cowrie.cfg tidak akan disentuh. Untuk menjalankan dengan konfigurasi standar, tidak perlu mengubah apa pun. Untuk mengaktifkan telnet, misalnya, buat cowrie.cfg dan masukkan hanya yang berikut ini:

```bash
[telnet]
enabled = true
```

##### 6) Start Cowrie

```bash
source cowrie-env/bin/activate
cowrie start
```

##### 7) Status Cowrie

```bash
cowrie status
```
atau
```bash
sudo systemctl status cowrie
```
![image](../../image/statusCowrie.png)

Setelah itu, silakan diuji apakah cowrie sudah berhasil jalan dengan baik dengan melakukan SSH ke honeypot cowrie melalui perintah berikut:

```bash
ssh <hostname>@<ip _ubuntu_desktop> -p <port>
```