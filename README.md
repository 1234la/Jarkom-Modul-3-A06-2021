```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
# Jarkom-Modul-3-A06-2021
Lapres Praktikum Jaringan Komputer 2021 - Modul 3
- Muh. Nur Fajrin Amiruddin (05111940000005)
- Rihan Farih Bunyamin (05111940000165)
- Lathifa Itqonina Mardiyati (05111940000176)

## Soal 1 & 2

![image](https://user-images.githubusercontent.com/55240758/141098861-36e0e716-5a73-423e-9d90-3368f921d706.png)  

Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server (1) dan Foosha sebagai DHCP Relay (2)
### Jawaban 
**Step 1:** Membuat topologi sesuai gambar topologi yang terlampir, dan mengatur network interface setiap nodenya dengan cara :
- Klik kanan node > edit network configuration atau melalui command line node ```nano /etc/network/interfaces```
- Tambahkan konfigurasi berikut pada network :  

  > **Node Foosha**
  ```
  auto eth1
  iface eth1 inet static
    address 10.2.1.1
    netmask 255.255.255.0

  auto eth2
  iface eth2 inet static
    address 10.2.2.1
    netmask 255.255.255.0

  auto eth3
  iface eth3 inet static
    address 10.2.3.1
    netmask 255.255.255.0
  ```
  
  > **Node Loguetown**
  ```
  auto eth0
  iface eth0 inet static
    address 10.2.1.2
    netmask 255.255.255.0
    gateway 10.2.1.1
  ```
  
  > **Node Alabasta**
  ```
  auto eth0
  iface eth0 inet static
    address 10.2.1.3
    netmask 255.255.255.0
    gateway 10.2.1.1
  ```
  
  > **Node EniesLobby**
  ```
  auto eth0
  iface eth0 inet static
    address 10.2.2.2
    netmask 255.255.255.0
    gateway 10.2.2.1
  ```
  
  > **Node Water7**
  ```
  auto eth0
  iface eth0 inet static
    address 10.2.2.3
    netmask 255.255.255.0
    gateway 10.2.2.1
  ```
  
  > **Node Jipangu**
  ```
  auto eth0
  iface eth0 inet static
    address 10.2.2.4
    netmask 255.255.255.0
    gateway 10.2.2.1
  ```
  
  > **Node Totoland**
  ```
  auto eth0
  iface eth0 inet static
    address 10.2.3.2
    netmask 255.255.255.0
    gateway 10.2.3.1
  ```
  
  > **Node Skypie**
  ```
  auto eth0
  iface eth0 inet static
    address 10.2.3.3
    netmask 255.255.255.0
    gateway 10.2.3.1
  ```

**Step 2:** hubungkan terlebih dahulu internet.  
- Pada node foosha   
karena node foosha merupakan router atau terhubung langsung dengan internet, maka foosha perlu membagikan koneksi internet yang telah dia dapat ke node lain melalui

```iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.2.0.0/16``` 

- Pada node lain 
untuk mendapatkan internet yang telah dibagi oleh node foosha, maka setiap node perlu mengaksesnya dengan  

```echo nameserver 192.168.122.1 > /etc/resolv.conf```

**Step 3:** kemudian dilakukan pengaturan node sesuai perannya dengan menginstall beberapa konfigurasi yang diperlukan.   
perlu kita ingat bahwa pada gns3 tidak tersedia text editor selain vim. Maka agar lebih mudah untuk melakukan pengeditan file, yakni dengan menggunakan text editor nano. Sehingga pada setiap node akan dilakukan penginstalan text editor tersebut. Selain itu dianjurkan sebelum melakukan penginstalan, lakukanlah pengupdetan dengan ``apt-get update``. Maka diperlukan penginstalan pada :

> **Node foosha**
```
apt-get update
apt-get install nano -y
apt-get install isc-dhcp-relay -y
```

![image](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/1.2%20file%20script%20di%20node%20foosha.jpg)  

*Gambar 1.2 file script.sh foosha*

```diff
note :
pada saat penginstalan dhcp relay terdapat pertanyaan :
> Pada server berapa dhcp relay akan meneruskan req dari client?
  isi dengan IP dhcp server : 10.2.2.4
> Pada interface berapa dhcp relay mendengarkan ?
  isi dengan : eth1 eth2 eth3
```
Langkah selanjutnya :  
  - lakukan pengecekan pada file isc-dhcp-relay dengan perintah : ```nano /etc/default/isc-dhcp-relay```   
  - cek apakah interface telah sesuai dengan berikut INTERFACES="eth1 eth2 eth3"
   & SERVERS="10.2.2.4"   
   sebagai berikut:  
   
   ![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/1.2%20config%20dhcp%20relay.jpg)
   
   *Gambar 1.2 config dhcp relay*
  - apabila telah sesuai, lakukan restart dengan :  ```service isc-dhcp-relay restart```

> **Node EniesLobby**
```
apt-get update
apt-get install nano -y
apt-get install bind9 -y
apt-get install dnsutils -y
```

![image](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/1.2%20file%20script%20di%20node%20enieslobby.jpg)  
*Gambar 1.2 file script.sh EniesLobby*

- kemudian lakukan restart bind dengan 
```service bind9 restart```

> **Node Jipangu**
```
apt-get update
apt-get install nano -y
apt-get install isc-dhcp-server -y
```

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/1.2%20file%20script%20di%20node%20jipangu.jpg)

*Gambar file script.sh Jipangu*

Langkah selanjutnya :
- buka file isc-dhcp-server dengan perintah ```nano /etc/default/isc-dhcp-server``` & isi INTERFACES = "eth0" seperti gambar berikut 

![image](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/1.2%20config%20dhcp%20server.jpg)  

   *Gambar 1.2 config dhcp server*
   
> **Node Water7**
```
apt-get update
apt-get install nano -y
apt-get install squid -y
apt-get install apache2-utils -y
```

Langkah selanjutnya :
- Backup terlebih dahulu file konfigurasi default yang disediakan Squid dengan perintah ``` mv /etc/squid/squid.conf /etc/squid/squid.conf.bak```


> **Node client**
```
apt-get update
apt-get install nano -y
apt-get install lynx -y
apt install speedtest-cli -y
```

![image](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/1.2%20file%20script%20di%20node%20client.jpg)  

*Gambar 1.2 file script.sh client*

## Soal 3, 4, & 6
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169 (3).
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50 (4).
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit (6).

### Jawab
Pada **node jipangu** buka file dhcpd.conf dengan ```nano /etc/dhcp/dhcpd.conf```. Lalu tambahkan konfigurasi berikut :
```
# switch 1 - 6 menit
subnet 10.2.1.0 netmask 255.255.255.0 {
    range 10.2.1.20 10.2.1.99;
    range 10.2.1.150 10.2.1.169;
    option routers 10.2.1.1;
    option broadcast-address 10.2.1.255;
    option domain-name-servers 10.2.2.2;
    default-lease-time 360; # No. 6
    max-lease-time 7200; # No. 6
}
```

![gambar](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/3.6%20%20config%20dhcp%20server%20subnet%201.jpg)

*Gambar 3.6 config dhcp server subnet1*

```
# switch 3 - 12 menit
subnet 10.2.3.0 netmask 255.255.255.0 {
    range 10.2.3.30 10.2.3.50;
    option routers 10.2.3.1;
    option broadcast-address 10.2.3.255;
    option domain-name-servers 10.2.2.2;
    default-lease-time 720; # No. 6
    max-lease-time 7200; # No. 6
}
```

![gambar](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/3.6%20config%20dhcp%20server%20subnet%202.jpg)

*Gambar 3.6 config dhcp server subnet2*

```
# untuk mengarahkan supaya gatewaynya masuk > solusi fail
subnet 10.2.2.0 netmask 255.255.255.0 {
     option routers 10.2.2.1;
}
```  

![gambar](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/3.6%20config%20dhcp%20server%20pembuka%20gateway.jpg)

*Gambar 3.6 config dhcp server untuk membuka gateway*

- kemudian lakukan restart dengan perintah ```service isc-dhcp-server restart```
- cek status dhcp server, pastikan dhcp server telah berhasil berjalan dengan perintah ```service isc-dhcp-server status```

Langkah Selanjutnya :  
setelah melakukan konfigurasi pada node jipangu sebagai dhcp server,
kemudian lakukan konfigurasi pada **node-node client** dengan
- melakukan edit dengan *command* konfigurasi lama pada file interfaces.  
  untuk membuka file :  ```nano /etc/network/interfaces```
  
- tambahkan pada file interfaces konfigurasi sebagai berikut 
  ```
  auto eth0
  iface eth0 inet dhcp
  ```
  
  ![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/3.6%20konfigurasi%20baru%20pd%20loguetown.jpg)
  
  *Gambar 3.6 konfigurasi baru pada Loguetown*
  
- lakukan restart setiap node client dengan cara
```diff 
> klik kanan node → klik Stop → klik kanan kembali node → klik Start  
```

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/3.6%20restart%20node%20client.jpg)

  *Gambar 3.6 restart node client*

lakukan pengecekan dengan ``ip a`` untuk mengetahui apakah konfigurasi telah berhasil. Jika telah berhasil maka ip akan berubah sesuai range yang di berikan pada konfigurasi di /etc/dhcp/dhcpd.conf sesuai yang diminta soal yaitu pada switch 1 rangenya 10.2.1.20 - 10.2.1.99 & 10.2.1.150 - 10.2.1.169 . Sedangkan pada switch 3 rangenya 10.2.3.30-10.2.3.50. contohnya sebagai berikut :  

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/3.6%20hasil%20ip%20a%20setelah%20config%20pd%20loguetown.jpg)

*Gambar 3.6 hasil ip a setelah config pada Loguetown*

## Soal 5
Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut (5).

### Jawab
Setelah melakukan konfigurasi diatas, node client tidak terhubung dengan internet. Maka agar node client dapat mengakses internet, yakni dengan menggunakan DNS Forwarder untuk mengarahkan DNS Server ke IP yang ingin dituju. Sehingga pada **node enieslobby** buka file named.conf.options pada /etc/bind/ dengan ```nano /etc/bind/named.conf.options``` . Kemudian lakukan:
  
**Step 1:** uncommand forwarders dan tambahkan ip
```
forwarders {
   192.168.122.1;
}; 
```

**Step 2:** command ```dnssec-validation auto``` & tambahkan dibawahnya dengan ``allow-query{any;};``

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/4%20agar%20client%20bisa%20akses%20internet.jpg)

*Gambar 5 agar client bisa akses internet*

**Step 3:** cek apakah client telah mendapatkan DNS yakni melalui ip enieslobby (10.2.2.2) sesuai konfigurasi di DHCP Server dengan perintah ``cat /etc/resolv.conf ``

 ![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/4%20cek%20dns%20loguetown.jpg)
 
*Gambar 5 cek DNS loguetown*

**Step 4:** cek apakah internet dapat diakses dengan ``ping google.com``

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/4%20cek%20internet%20loguetown.jpg)

*Gambar 5 cek internet loguetown*

## Soal 7
Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69 (7).

### Jawab
**Step 1:** untuk membuat alamat ip yang tetap untuk **node Skypie** maka buka ```nano /etc/dhcp/dhcpd.conf``` pada dhcp server atau node jipangu. 
tambahkan konfigurasi berikut :
```
host Skypie {
    hardware ethernet 'hwaddress_milik_Skypie';
    fixed-address [prefix IP].3.69;
}
```
Adapun untuk mengetahui 'hwaddress_milik_Skypie'= f6:e0:b8:d6:03:d3 gunakan command ```ip a``` di node skypie,  kemudian lihat interface yang berhubungan dengan router, 
dalam kasus ini adalah eth1, dan lihat pada bagian _link/ether_, silakan copy address tersebut karena merupakan '_hwaddress_milik_Skypie_' yang akan digunakan untuk konfigurasi dhcpd di Jipangu. 
 
 ![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/7%20ip%20a%20skypie.jpg)
 
*Gambar 7 ip a Skypie*

Maka konfigurasi pada file dhcpd.conf menjadi :
 ```
 host Skypie {
    hardware ethernet f6:e0:b8:d6:03:d3;
    fixed-address 10.2.3.69;
}
```

**Step 2:** Lakukan restart dhcp server pada node Jipangu dengan perintah
  ```service isc-dhcp-server restart```

**Step 3:** Buka ```nano /etc/network/interfaces``` lalu tambahkan konfigurasi berikut 
  ```
  hwaddress ether f6:e0:b8:d6:03:d3
  ```
  
  ![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/7%20konfigurasi%20interfaces.jpg)
  
  
  *Gambar 7 konfigurasi interface*

**Step 4:** Lakukan restart node Skypie dengan :
```diff
  > klik kanan node → klik Stop → klik kanan kembali node → klik Start
```

**Step 5:** Lalu lakukan testing pada node skypie dengan command ```ip a```

 
 ![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/7%20hasil%20testing.jpg)
 
 *Gambar 7 hasil testing*
  
## Soal 8
Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000 (8).

### Jawab

>**Pada Node Enieslobby**  

**Step 1:** Buka ```nano /etc/bind/named.conf.local``` lalu tambahkan konfigurasi sebagai berikut
```
zone "jualbelikapal.a06.com" {
	type master;
	file "/etc/bind/jarkom/jualbelikapal.a06.com";
};
```

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/8%20config%20named.conf.local.jpg)

*Gambar 8 config named.conf.local*

**Step 2:** Buat folder jarkom di dalam /etc/bind pada enieslobby dengan perintah ```mkdir /etc/bind/jarkom```

**Step 3:** Copykan file db.local pada path /etc/bind ke dalam folder jarkom yang baru saja dibuat dan ubah namanya menjadi jualbelikapal.a06.com dengan perintah ```cp /etc/bind/db.local /etc/bind/jarkom/jualbelikapal.a06.com```

**Step 4:** kemudian buka file jualbelikapal.a06.com dengan ```nano /etc/bind/jarkom/jualbelikapal.a06.com``` dan lakukan pengeditan seperti gambar berikut :

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/8%20file%20jualbelikapal.a06.com.jpg)

*Gambar 8 file jualbelikapal.a06.com*

**Step 5:** setelah itu lakukan restart bind9 dengan perintah ```service bind9 restart```


----------------------------
> **Pada Node Water7**   

**Step 1:** buka ```nano /etc/squid/squid.conf``` lalu tambahkan konfigurasi berikut
```
http_port 5000
visible_hostname jualbelikapal.a06.com
```

**Step 2:** Restart squid dengan cara mengetikkan perintah:
```service squid restart```

----------------------------
> **Pada Node Loguetown**   

**Step 1:** untuk memeriksa apakah konfigurasi pembuatan domain berhasil atau tidak dengan perintah
```ping jualbelikapal.a06.com```

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/8%20hasil%20ping%20jualbelikapal.a06.com.jpg)

*Gambar hasil ping jualbelikapal.a06.com*

**Step 2:** lakukan konfigurasi proxy dengan mengaktifkan proxy sebagai berikut ```export http_proxy="http://ip-proxy-server:port"```

- dengan menggunakan ip
```export http_proxy="http://10.2.2.3:5000"```

- dengan menggunakan domain
```export http_proxy="http://jualbelikapal.a06.com:5000"```

**Step 3:** Untuk memeriksa apakah konfigurasi proxy pada client berhasil, silahkan lakukan perintah berikut
```env | grep -i proxy``` 

Dalam kasus ini, ip-proxy-server merupakan IP dari water7 (10.2.2.3), dengan port yang sudah didefinisikan pada saat melakukan konfigurasi Squid.
Selain itu proxy dapat diakses juga dengan nama jualbelikapal.a06.com . Apabila berhasil, maka environment kita telah berhasil menggunakan proxy. Tampilan apabila konfigurasi berhasil :

- jika menggunakan export dengan 10.2.2.3:5000

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/8%20export%20http%20proxy.jpg)

*Gambar 8 export http proxy*

- jika menggunakan export dengan jualbelikapal.a06.com:5000

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/8%20export%20http%20proxy%202.jpg)

*Gambar 8 export http proxy 2*

**Step 4:** coba untuk mengakses web http://its.ac.id dengan perintah ```lynx http://its.ac.id```. Maka akan muncul halaman seperti berikut:

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/8%20halaman%20proxy.jpg)

*Gambar 8 halaman proxy*

## Soal 9
Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yy(9).

### Jawab
**Step 1:** Pada node Water7 membuat user & password dengan enskripsi md5 (gunakan -m)
- username ```luffybelikapala06``` -> pass: ```luffy_a06```
```
htpasswd -m /etc/squid/passwd luffybelikapala06 
```

- username ```zorrobelikapala06``` -> pass: ```zoro_a06```
```
htpasswd -m /etc/squid/passwd zorobelikapala06 
```

Ketikkan password yang telah ditentukan. Jika sudah maka akan muncul notifikasi:

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/9%20notifikasi%20password.jpg)

*Gambar 9 notifikasi password*

**Step 2:** melakukan edit file dengan membuka ```nano /etc/squid/squid.conf``` Lalu tambahkan konfigurasi berikut 
```
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS
```

sebagai berikut :

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/9%20config%20squid.conf.jpg)

*Gambar 9 config squid.conf*

**Step 3:** Restart squid dengan :
```service squid restart```

**Step 4:** coba untuk mengakses web http://its.ac.id dengan perintah ```lynx http://its.ac.id``` . Maka kemudian akan di arahkan ke halaman login, isikan dengan username & password yang telah dibuat.

- Login dengan username luffybelikapala06  

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/9%20halaman%20login%20user%20luffy.jpg)

*Gambar 9 halaman login user luffy*

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/9%20halaman%20login%20pass%20user%20luffy.jpg)

*Gambar 9 halaman login pass luffy*

setelah login muncul notif SSL tekan y (yes) maka website akan ditampilkan  

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/9%20halaman%20its%20luffy.jpg)

*Gambar 9 halaman its luffy*

- login dengan username zorobelikapala06  

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/9%20halaman%20login%20user%20zoro.jpg)

*Gambar 9 halaman login user zoro*

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/9%20halaman%20pass%20user%20zoro.jpg)

*Gambar 9 halaman login pass zoro*

setelah login muncul noitf SSl tekan y (yes) maka website akan ditampilkan:

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/9%20halaman%20its%20zoro.jpg)

*Gambar 9 halaman its zoro*

## Soal 10
Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00) (10).

### Jawab

untuk membuat batasan waktu akses maka pada node Water7:

**Step 1:** Buat file baru bernama acl.conf di folder squid dengan perintah ```nano /etc/squid/acl.conf```

**Step 2:** Tambahkan pada file acl.conf konfigurasi berikut 

```
acl AVAILABLE_WORKING time MTWH 07:00-11:00
acl AVAILABLE_WORKING_2 time TWHF 17:00-23:59
acl AVAILABLE_WORKING_3 time A 00:00-03:00
```

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/10%20acl.conf.jpg)
*Gambar 10 acl.conf*

**Step 3:** Buka file squid.conf dengan perintah ```nano /etc/squid/squid.conf``` & tambahkan konfigurasi berikut pada file squid.conf

```
include /etc/squid/acl.conf
```

```
#Cara 2 pembatasan waktu akses
http_access allow USERS AVAILABLE_WORKING
http_access allow USERS AVAILABLE_WORKING_2
http_access allow USERS AVAILABLE_WORKING_3
```
maka letakkan konfigurasi sesuai gambar berikut :

![10 squid conf 2](https://user-images.githubusercontent.com/55240758/141646403-7f5823c4-c149-4215-8aa0-a2b1f3c929b2.jpg)


*Gambar 10 squid.conf2*

**Step 4:** Lakukan restart squid dengan perintah ```service squid restart```

**Step 5:** Coba untuk mengakses web http://its.ac.id. Akan muncul halaman error jika mengakses diluar waktu yang telah ditentukan sebagai berikut 

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/10%20halaman%20http%20forbidden.jpg)

*Gambar 10 halaman http forbidden*

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/10%20halaman%20denied%20proxy.jpg)

*Gambar 10 halaman dinied proxy*

## Saol 11
Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie.

### Jawab

> **Pada Node EniesLobby**

**Step 1:** buka file named.conf.local dengan ```nano /etc/bind/named.conf.local``` & tambahkan konfigurasi berikut
```
zone "super.franky.a06.com" {
        type master;
        file "/etc/bind/sunnygo/super.franky.a06.com";
};
```

![11 named conf local](https://user-images.githubusercontent.com/55240758/141646442-f4972760-079a-420a-8364-1c862d325484.jpg)

*Gambar 11 named.conf.local*

**Step 2:** buat folder sunnygo dengan perintah ```mkdir /etc/bind/sunnygo```

**Step 3:** Copykan file db.local pada path /etc/bind ke dalam folder sunnygo dan ubah namanya menjadi super.franky.a06.com dengan perintah 

```cp /etc/bind/db.local /etc/bind/sunnygo/super.franky.a06.com```

**Step 4:** sesuaikan konfigurasi pada file super.franky.a06.com seperti gambar berikut

![11 super franky a06 com ](https://user-images.githubusercontent.com/55240758/141646583-ead2870b-2167-4c37-84ea-8e5d70d15275.jpg)

*Gambar 11 super.franky.a06.com*

**Step 5:** lakukan restart bind ```service bind9 restart```

-----------------------------------
> **Pada Node Skypie**

**Step 1:** install apache2 dengan perintah
```
apt-get update
apt-get install apache2 -y
```
**Step 2:** aktifkan apache2 dengan service apache2 restart

**Step 3:** Pindah ke directory /var/www dengan ```cd /var/www```

**Step 4:** buat sebuah directory baru di dalam /var/www dengan nama super.franky.a06.com, dengan perintah
```mkdir /var/www/super.franky.a06.com```

**Step 5:** buka directory dengan perintah ```cd /var/www/super.franky.a06.com```

**Step 6:** lakukan pengunduhan file https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/super.franky.zip . serta unzip dan pindahkan file isi dari zip keluar.

- untuk pengunduhan lakukan penginstalan wget : ```apt-get install wget -y``` 
- kemudian unduh file dengan perintah ```wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/super.franky.zip```
- untuk unzip file yang telah diunduh lakukan penginstalan unzip : ```apt-get install unzip```
- kemudian untuk unzip file lakukan perintah : ```unzip super.franky.zip```
- pindahkan file dalam folder super.franky ke folder super.franky.a06.com lakukan :  ```cp -r super.franky/. .``` 

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/11%20isi%20folder%20vr%20ww%20super.franky.a06.jpg)

*Gambar 11 isi folder vr ww super.franky.a06*

**Step 7:** buka folder sites-available dengan ```cd /etc/apache2/sites-available``` & kemudian buat file nano super.franky.a06.com.conf dengan perintah

```cp 000-default.conf super.franky.a06.com.conf```

**Step 8:** tambahkan konfigurasi seperti gambar berikut pada file super.franky.a06.com.conf

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/11%20super.franky.a06.jpg)

*Gambar 11 super.franky.a06.com.conf*

**Step 7:** Aktifkan konfigurasi super.franky.a06.com.conf menggunakan perintah ```a2ensite super.franky.a06.com.conf```

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/11%20a2ensite.jpg)

*Gambar 11 a2ensite*

**Step 8:** Restart apache2 dengan perintah ```service apache2 restart```

-----------------------------------
> **Pada node water7**  

**Step 1:** buka ```nano /etc/squid/squid.conf``` & tambahkan konfigurasi berikut kedalam file
```
acl site dstdomain .google.com 
deny_info http://super.franky.a06.com/ site
http_reply_access deny site
```
![11 squid conf](https://user-images.githubusercontent.com/55240758/141305654-96b0371f-cd24-4f5e-b616-1403bf45e76b.jpg)

*Gambar 11 squid.conf*

**Step 2:** ubah nameserver menjadi 10.2.2.2 dengan membuka file resolv.conf melalui perintah ```nano /etc/resolv.conf```

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/11%20resolv.conf.jpg)

*Gambar 11 resolv.conf*

**Step 3:** restart squid dengan perintah ```service squid restart```

**Step 4:** coba akses super.franky.a06.com pada client yang menggunakan proxy (loguetown) dengan ``lynx google.com`` maka akan di redirect ke super.franky.a06.com setalah melakukan login.

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/11%20halaman%20user.jpg)

*Gambar 11 halaman username*

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/11%20halaman%20pass.jpg)

*Gambar 11 halaman pass*

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/11%20notif%20redirect.jpg)

*Gambar 11 notif redirect*

![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/11%20halaman%20super.jpg)

*Gambar 11 halaman super*

## Soal 12
Saatnya berlayar! Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.yyy.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, 
ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps(12).

**Step 1:** tambahkan konfigurasi berikut pada file squid.conf di node Water7
```
include /etc/squid/acl-bandwidth.conf
```

**Step 2:** buat file baru dengan nano /etc/squid/acl-bandwidth.conf dan tambahkan konfigurasi sebagai berikut 
```
acl download url_regex -i \.jpg$ \.png$

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd

acl luffy proxy_auth luffybelikapala06
acl zoro proxy_auth zorobelikapala06

delay_pools 2
delay_class 1 1
delay_parameters 1 1250/1250
delay_access 1 allow luffy
delay_access 1 deny zoro
delay_access 1 allow download
delay_access 1 deny all
```
Adapun untuk lebih jelasnya mengenai config tersebut dapat dibaca pada laman ini: https://sites.google.com/site/huyadesakai/linux/mengatur-bandwidth-di-squid-proxy

pada config di atas bagian ``delay_parameters``, diperoleh 1250 untuk max (sebagai pembilang) maupun throughputnya (sebagai penyebut) agar diperoleh bandwith yang sesuai dengan soal yakni 10 kilobit. Dimana default dari pengaturan tersebut adalah dalam satuan byte maka asumsikan bahwa untuk mendapatkan 10 kilobyte yakni dengan 10000 untuk max maupun throughputnya. Namun dikarenakan satuan yang diinginkan adalah dalam bentuk bit, maka dapat kita ketahui bahwa :

![1636439023226](https://user-images.githubusercontent.com/55240758/141602119-dda406d5-e8f2-4967-b023-1577d1b6926c.jpg)

sehingga 10000 byte tersebut dibagi dengan 8 agar barubah menjadi satuan bit. Maka diperoleh 1250 byte agar kecepatan yang diperoleh adalah kisaran 10 kilobit.

![12 acl-bandwith conf](https://user-images.githubusercontent.com/55240758/141309192-0fdcdd5e-fcad-4679-88b1-8ae8130135a3.jpg)


**Step 2:** Lakukan restart squid dengan perintah ```service squid restart```

**Step 3:** Cek hasil konfigurasi dengan membuka halaman super.franky.a06.com melalui perintah ```lynx super.franky.a06.com``` 

**Step 4:** Login dengan menggunakan akun luffy, username : ```luffybelikapala06``` & pass : ```luffy_a06```

![9 halaman login user luffy](https://user-images.githubusercontent.com/55240758/141309098-2d367b93-c2d1-488e-a273-3810aeae8a57.jpg)

**Step 5:** lakukan pengunduhan file yang berekstensi .jpg atau .png. Adapun cara untuk melakukan pengunduhan yaitu :

lynx super.franky.xxx.com > arahkan ke folder public menggunakan panah kanan > enter folder public > pilih file mana yang mau di download dengan menekan tombol panah kanan sampai file yang ingin di download > untuk melakukan download, ketika sudah berada di file yang mau di download tekan tombol ```d``` atau key ``d``. Maka dapat diketahui pengunduham memiliki batasan kecepatan internet sebagai berikut

![12 luffy download jpg](https://user-images.githubusercontent.com/55240758/141309272-d7064155-e4e5-4d4e-ad4f-3eb386106d2c.jpg)

*Gambar 12*

## Soal 13
Sedangkan, Zoro yang sangat bersemangat untuk mencari harta karun, sehingga kecepatan kapal Zoro tidak dibatasi ketika sudah mendapatkan harta yang diinginkannya.

**Step 1:** tambahkan konfigurasi berikut pada file acl-bandwidth.conf yang ada pada node water7
```
delay_class 2 1
delay_parameters 2 -1/-1
delay_access 2 allow zoro
delay_access 2 deny luffy
delay_access 2 deny all
```

Adapun untuk lebih jelasnya mengenai config tersebut dapat dibaca pada laman ini: https://sites.google.com/site/huyadesakai/linux/mengatur-bandwidth-di-squid-proxy


![13 acl-bandwith conf](https://user-images.githubusercontent.com/55240758/141309241-483757d8-d1d8-45ae-ad1b-604ea2575f1f.jpg)


**Step 2:** Lakukan restart squid dengan perintah ```service squid restart```

**Step 3:** Cek hasil konfigurasi dengan membuka halaman super.franky.a06.com melalui perintah ```lynx super.franky.a06.com``` 

**Step 4:** Login dengan menggunakan akun zorro, username : ```zorobelikapala06``` & pass : ```zoro_a06```

![13 login zoro](https://user-images.githubusercontent.com/55240758/141308639-bf63545d-7000-42a5-9f80-884b0f0c20f4.jpg)

**Step 5:** lakukan pengunduhan file yang berekstensi .jpg atau .png. Adapun cara untuk melakukan pengunduhan yaitu :

lakukan ``lynx super.franky.xxx.com`` > arahkan ke folder public menggunakan panah kanan > enter folder public > pilih file mana yang mau di download dengan menekan tombol panah kanan sampai file yang ingin di download > untuk melakukan download, ketika sudah berada di file yang mau di download tekan tombol ```d``` atau key ``d``. Maka dapat diketahui pengunduham memiliki batasan kecepatan internet sebagai berikut

![13 mau download ini](https://user-images.githubusercontent.com/55240758/141308803-711f9553-62da-4019-9a5d-64bd56b02ea9.jpg)

kemudian diarahkan kehalaman selanjutnya untuk menentukan tempat penyimpanan pengunduhan

![13 halaman download](https://user-images.githubusercontent.com/55240758/141308882-f515089b-48cf-4b98-a978-eb4a36f3c5a8.jpg)

Adapun zoro dapat melakukan pengunduhan file .jpg atau .png tanpa ada batasan sebagai berikut 

![13 mau download jpg](https://user-images.githubusercontent.com/73771452/141420822-caf7d417-a6a9-46ef-909e-0d5932ccbe87.jpg)

*Gambar 13*

## Kendala dalam pengerjaan
* Kepadatan jadwal membuat koordinasi kelompok kurang baik
* Salah satu anggota mengalami error pada saat mencoba mengerjakan
![pic](https://github.com/1234la/Jarkom-Modul-3-A06-2021/blob/main/pic/messageImage_1636353211045.jpeg)

