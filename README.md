# Jarkom-Modul-5-E02-2021
## Persiapan
## A. Membuat Topologi seperti di soal

![image](https://user-images.githubusercontent.com/70801807/145679079-2bda5a1c-9201-4d73-9b5c-226ae3eb1ab6.png)

## B. Karena kalian telah belajar subnetting dan routing, Luffy ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM. setelah melakukan subnetting, 
### Teknik yang dipilih adalah CIDR dengan struktur topologi seperti berikut :
![image](https://user-images.githubusercontent.com/70801807/145679279-169e07fe-b381-4293-baf8-db349fa37f65.png)


### Tree :
![image](https://user-images.githubusercontent.com/70801807/145679292-f84caae6-75a0-4393-83bf-cfc303e3d294.png)

### Subnetting :
![image](https://user-images.githubusercontent.com/70801807/145679317-6144ea0b-b762-4e67-aff1-4ccacc95937d.png)

### Pembagian IP :
![image](https://user-images.githubusercontent.com/70801807/145679330-5d948039-99fe-4112-8158-e2e9961ac5bb.png)

Baris berwarna merah berarti IP tidak perlu diimplementasikan di network configuration karena mereka menggunakan DHCP untuk mendapatkan IP.

### Setting network configuration pada GNS3 :
- Foosha
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.30.8.1
	netmask 255.255.255.252

auto eth2
iface eth2 inet static
	address 10.30.20.1
	netmask 255.255.255.252
```
- Water 7
```
auto eth0
iface eth0 inet static
	address 10.30.8.2
	netmask 255.255.255.252

auto eth1
iface eth1 inet static
	address 10.30.0.129
	netmask 255.255.255.128

auto eth2
iface eth2 inet static
	address 10.30.0.1
	netmask 255.255.255.248

auto eth3
iface eth3 inet static
	address 10.30.4.1
	netmask 255.255.252.0
```

- Doriki
```
auto eth0
iface eth0 inet static
	address 10.30.0.2
	netmask 255.255.255.248
	gateway 10.30.0.1
```

- Jipangu
```
auto eth0
iface eth0 inet static
	address 10.30.0.3
	netmask 255.255.255.248
	gateway 10.30.0.1
```

- Guanhao
```
auto eth0
iface eth0 inet static
	address 10.30.20.2
	netmask 255.255.255.252

auto eth1
iface eth1 inet static
	address 10.30.18.1
	netmask 255.255.254.0

auto eth2
iface eth2 inet static
	address 10.30.17.1
	netmask 255.255.255.248

auto eth3
iface eth3 inet static
	address 10.30.16.1
	netmask 255.255.255.0
```

- Maingate
```
auto eth0
iface eth0 inet static
	address 10.30.17.3
	netmask 255.255.255.248
	gateway 10.30.17.1
```

- Jorge
```
auto eth0
iface eth0 inet static
	address 10.30.17.2
	netmask 255.255.255.248
	gateway 10.30.17.1
```

- Blueno
```
auto eth0
iface eth0 inet dhcp
```

- Cipher
```
auto eth0
iface eth0 inet dhcp
```

- Elena
```
auto eth0
iface eth0 inet dhcp
```

- Fukurou
```
auto eth0
iface eth0 inet dhcp
```

## Kalian juga diharuskan melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.
Routing :
![image](https://user-images.githubusercontent.com/70801807/145679412-790cffb5-e075-49b5-8872-80b2d04c0487.png)

Berdasarkan tabel di atas, berikut adalah command untuk menambahkan rule pada routing table di masing-masing router yang ada.
- Foosha
```
route add -net 10.30.0.0 netmask 255.255.240.0 gw 10.30.8.2

route add -net 10.30.16.0 netmask 255.255.248.0 gw 10.30.20.2
```
- Water7
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.30.8.1
```
- Guanhao
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.30.20.1
```

Selanjutnya dilakukan konfigurasi untuk beberapa node yaitu :
### Doriki (DNS Server)
Untuk menjadikan Doriki sebagai DNS Server maka dilakukan instalasi sebagai berikut :
```
apt-get update
apt-get install bind9 -y
```

### Jipangu (DHCP Server)
Untuk menjadikan Jipangu sebagai DHCP Server maka dilakukan instalasi sebagai berikut :
```
apt-get update
apt-get install isc-dhcp-server -y
```
Selanjutnya dilakukan konfigurasi pada file-file berikut :
- /etc/default/isc-dhcp-server
Sesuaikan interface seperti di bawah ini :
```
...
INTERFACES=”eth0”
...
```

- /etc/dhcp/dhcpd.conf
Setting subnet Blueno, Cipher, Elena, dan Fukurou sebagai berikut :
```
…
# Blueno
subnet 10.30.0.128 netmask 255.255.255.128 {
    range 10.30.0.130 10.30.0.254;
    option routers 10.30.0.129;
    option broadcast-address 10.30.0.255;
    option domain-name-servers 192.168.122.1;
    default-lease-time 600;
    max-lease-time 7200;
}
# Cipher
subnet 10.30.4.0 netmask 255.255.252.0 {
    range 10.30.4.2 10.30.7.254;
    option routers 10.30.4.1;
    option broadcast-address 10.30.7.255;
    option domain-name-servers 192.168.122.1;
    default-lease-time 600;
    max-lease-time 7200;
}
# Elena
subnet 10.30.18.0 netmask 255.255.254.0 {
    range 10.30.18.2 10.30.19.254;
    option routers 10.30.18.1;
    option broadcast-address 10.30.19.255;
    option domain-name-servers 10.30.0.2;
    default-lease-time 600;
    max-lease-time 7200;
}
# Fukurou
subnet 10.30.16.0 netmask 255.255.255.0 {
    range 10.30.16.2 10.30.16.254;
    option routers 10.30.16.1;
    option broadcast-address 10.30.16.255;
    option domain-name-servers 10.30.0.2;
    default-lease-time 600;
    max-lease-time 7200;
}
```

### Maingate & Jorge (Web Server)
Untuk menjadikan Maingate & Jorge sebagai Web Server maka dilakukan instalasi sebagai berikut :
```
apt-get install apache2 -y
```

### Water7, Guanhao & Foosha (DHCP Relay)
Untuk menajdikan Water7, Guanhao & Foosha sebagai DHCP Relay maka dilakukan instalasi sebagai berikut :
```
apt-get update
apt-get install isc-dhcp-relay -y
```

- Untuk Water7 dilakukan konfigurasi pada file /etc/default/isc-dhcp-relay sebagai berikut:
```
# What servers should the DHCP relay forward requests to?
SERVERS="10.30.0.3"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth2 eth1 eth3"
```

- Untuk Guanhao dilakukan konfigurasi pada file /etc/default/isc-dhcp-relay sebagai berikut:
```
# What servers should the DHCP relay forward requests to?
SERVERS="10.30.0.3"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth0 eth1 eth3"
```

- Untuk Foosha dilakukan konfigurasi pada file /etc/default/isc-dhcp-relay sebagai berikut:
```
# What servers should the DHCP relay forward requests to?
SERVERS="10.30.0.3"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth1 eth2"
```

## 1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.
Konfigurasi iptables untuk Foosha dengan menggunakan SNAT adalah sebagai berikut :
```
ipgateway=$(ip -4 addr show eth0 | grep inet | awk '{ print substr( $2, 1, length($2)-3 ) }')

iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source $ipgateway
```

## 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.
Untuk soal ini, dilakukan pembatasan untuk akses yang mengarah ke subnet yang berisi DHCP Server dan DNS Server yang dalam kelompok kami adalah C1. Untuk melakukan hal itu, digunakan syntax berikut

```
ipgateway=$(ip -4 addr show eth0 | grep inet | awk '{ print substr( $2, 1, length($2)-3 ) }')

iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source $ipgateway
```
Syntax diatas dijalankan pada server `JIPANGU` sebagai DHCP Server dan `DORIKI` sebagai DNS Server.

Pertama-tama, kita memerlukan IP gateway ke nat yang bersifat dinamis. Oleh karena itu, IP Gateway diambil menggunakan `grep` dan `awk` dan disimpan ke dalam variable `ipgateway`.

Setelah mendapatkan ipgateway, dilakukan syntax `iptables`. Syntax ini berarti setiap paket yang keluar dari rooter `FOOSHA` melalui `-eth0`, akan diarahkan ke `ipgateway` yang didapatkan sebelumnya.


## 3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.
Untuk mengerjakan soal ini, dilakukan syntax

```
iptables -A INPUT -p icmp --icmp-type echo-request -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP

iptables -A OUTPUT -p icmp --icmp-type echo-request -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

Syntax diatas dijalankan pada server `JIPANGU` sebagai DHCP Server dan `DORIKI` sebagai DNS Server.

Syntax diatas berarti setiap paket yang masuk ke server `JIPANGU` dan `DORIKI` dengan protokol `icmp` dan tipe `echo-request` sebagai penanda untuk (ping) dan koneksi yang diterima sudah melebihi **3** akan di drop atau ditolak.

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
