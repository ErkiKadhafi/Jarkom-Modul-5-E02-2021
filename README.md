# Jarkom-Modul-5-E02-2021

## Struktur Topologi

## 1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.

## 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.

## 3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

## 4. Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis.

Penyelesaian:

Dengan menambahkan 4 firewall yaitu:
1. ACCEPT input dari source 10.30.0.128/25 setiap hari Senin, Selasa, Rabu, dan Kamis pada pukul 07:00 sampai dengan pukul 15:00.
2. ACCEPT input dari source 10.30.4.0/22 setiap hari Senin, Selasa, Rabu, dan Kamis pada pukul 07:00 sampai dengan pukul 15:00.
3. REJECT input dari source 10.30.0.128/25.
4. REJECT input dari source 10.30.4.0/22.

Urutan dari aturan firewall berpengaruh pada hasil. Jika rule di atas direpresentasikan dalam bentuk pseudocode, maka pseudocodenya adalah sebagai berikut

```pseudo
  if source == 10.30.0.128/25 and day in (Senin, Selasa, Rabu, Kamis) and time between (07:00, 15:00)
    return ACCEPT
  else if source == 10.30.4.0/22 and day in (Senin, Selasa, Rabu, Kamis) and time between (07:00, 15:00)
    return ACCEPT
  else if source == 10.30.0.128/25
    return REJECT
  else if source == 10.30.4.0/22
    return REJECT
```

Perintah yang digunakan untuk menambahkan firewall di atas adalah

```bash
iptables -A INPUT -s 10.30.0.128/25,10.30.4.0/22  --match time --weekdays Mon,Tue,Wed,Thu --timestart $(date -u -d @$(date "+%s" -d "07:00") +%H:%M) --timestop $(date -u -d @$(date "+%s" -d "15:00") +%H:%M) -j ACCEPT

iptables -A INPUT -s 10.30.0.128/25,10.30.4.0/22 -j REJECT

```

## 5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.

Penyelesaian:

Waktu yang tertera overlap dengan hari berikutnya. Untuk mengatasi hal ini, waktu dipecah menjadi dua bagian yaitu pukul 15:01 sampai dengan pukul 23:59 dan pukul 00:00 sampai dengan 06:59. Dengan demikian, rule firewall yang cocok untuk diterapkan adalah sebagai berikut.

1. ACCEPT input dari source 10.30.18.0/23 pada pukul 15:01 sampai dengan pukul 23:59.
2. ACCEPT input dari source 10.30.16.0/24 pada pukul 15:01 sampai dengan pukul 23:59.
3. ACCEPT input dari source 10.30.18.0/23 pada pukul 00:00 sampai dengan pukul 06:59.
4. ACCEPT input dari source 10.30.16.0/24 pada pukul 00:00 sampai dengan pukul 06:59.
5. REJECT input dari source 10.30.18.0/23
6. REJECT input dari source 10.30.16.0/24

Urutan sangat memengaruhi cara kerja dari firewall. Jika firewall di atas direpresentasikan dalam bentuk pseudocode, maka pseudocodenya adalah sebagai berikut.

```pseudo
if source == 10.30.18.0/23 and time between (15:01, 23:59)
  return ACCEPT
else if source == 10.30.16.0/24 and time between (15:01, 23:59)
  return ACCEPT
else if source == 10.30.18.0/23 and time between (00:00, 06:59)
  return ACCEPT
else if source == 10.30.16.0/24 and time between (00:00, 06:59)
  return ACCEPT
else if source == 10.30.18.0/23
  return REJECT
else if source == 10.30.16.0/24
  return REJECT
```

Perintah yang digunakan untuk menambahkan firewall di atas adalah sebagai berikut.

```bash
iptables -A INPUT -s 10.30.18.0/23,10.30.16.0/24 --match time --timestart $(date -u -d @$(date "+%s" -d "15:01") +%H:%M) --timestop $(date -u -d @$(date "+%s" -d "23:59") +%H:%M) -j ACCEPT

iptables -A INPUT -s 10.30.18.0/23,10.30.16.0/24 --match time --timestart $(date -u -d @$(date "+%s" -d "00:00") +%H:%M) --timestop $(date -u -d @$(date "+%s" -d "06:59") +%H:%M) -j ACCEPT

iptables -A INPUT -s 10.30.18.0/23,10.30.16.0/24 -j REJECT
```

## 6. Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate

Penyelesaian:

Untuk mempermudah penjelasan, apa yang diminta oleh soal di atas adalah:

Jika client mengakses http://[IP DNS Server] (pada kasus ini adalah http://10.30.0.2), maka client akan membuka halaman website Maingate dan Jorge secara bergantian. "Secara bergantian" yang dimaksud adalah sebagai berikut. Ketika client mengakses http://10.30.0.2, maka client akan mendapatkan halaman website dari Maingate. Jika client mengakses URL tersebut lagi, maka client akan mendapatkan halaman website dari Jorge. Jika client mengakses URL tersebut lagi, maka client akan mendapatkan halaman website dari Maingate dan seterusnya.

Aturan pengaksesan ini mirip seperti sistem kerja load balancer yang membagi rata request supaya beban kedua server dibagi sama rata. Jika aturan ini direpersentasikan dalam bentuk pseudocode, maka pseudocodenya adalah sebagai berikut.

```pseudo
n = 0 // n adalah urutan akses
void accessHTTP(url, n)
  if url == http://10.30.0.2
      if n mod 2 == 0
          openPage(Maingate)
      else
          openPage(Jipangu)
      n = n + 1
```

## Kesulitan yang Dialami
Kesulitan yang dialami oleh tim E02 dalam mengerjakan soal shift 5 adalah
1. Pada soal terkait ICMP, tim E02 membutuhkan waktu yang sedikit lebih lama untuk mendapatkan referensi bagaimana cara mengimplementasikan firewall untuk ICMP.
