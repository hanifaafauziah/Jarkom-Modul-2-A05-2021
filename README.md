# Lapres Praktikum Modul 2 Jaringan Komputer

Kelompok A5 :
<li>05111840000138 - Gema Adi Perwira
<li>05111940000024 - Hanifa Fauziah
<li>05111940000111 - Evelyn Sierra


## Soal 1
EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet
  
### Jawaban : 
* Konfigurasi Topologi GNS3
![image](https://user-images.githubusercontent.com/80946219/139526000-d7ba2f9c-7712-4226-9e3d-cf5d71ed3946.png)

* Masing-masing node dapat mengakses internet
  
  Foosha
![foosha](https://user-images.githubusercontent.com/80946219/139526847-d8779157-35e7-4233-b5e5-44269effe516.png)
  Enieslobby
![enieslobby](https://user-images.githubusercontent.com/80946219/139526860-7192bfd6-3e1a-428e-b122-eb1da2c8c50f.png)
  Water7
![water7](https://user-images.githubusercontent.com/80946219/139526876-2ac97198-04a2-4d0e-a3b6-6b6a04e01a77.png)
  Skypie
![image](https://user-images.githubusercontent.com/80946219/139526913-cf40eacd-e3d7-4598-bef4-4f3bbe5b8f3c.png)
  Loguetown
![image](https://user-images.githubusercontent.com/80946219/139526930-67e27f89-c8a9-4d12-b934-44d7eda91339.png)
  Alabasta
![image](https://user-images.githubusercontent.com/80946219/139526943-8440fec3-9703-4ccc-9a07-445a6dcc97fe.png)
  
## Soal 2
Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku
  
### Jawaban : 

Pada **ENIESLOBBY**
  
Konfigurasi domain pada `/etc/bind/named.conf.local`</li>
```
  zone "franky.a05.com" {
    type master;
    file "/etc/bind/kaizoku/franky.a05.com";
  };
```

Membuat folder `kaizoku` di `etc/bind`

``
  mkdir /etc/bind/kaizoku
``

Meng-copy file db.local
```
  cp /etc/bind/db.local /etc/bind/kaizoku/franky.a05.com
```

Memodifikasi config yang ada pada file /etc/bind/kaizoku/franky.a05.com
```
    GNU nano 2.5.3   File: /etc/bind/kaizoku/franky.a05.com

    ;
    ; BIND data file for local loopback interface
    ;
    $TTL    604800
    @       IN      SOA     franky.a05.com. root.franky.a05.com. (
                            2021102401      ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      franky.a05.com.
    @       IN      A       192.171.2.2     ;IP Enieslobby
    www     IN      CNAME   franky.a05.com.
```

Restart

``service bind9 restart``



Pada **Loguetown & Alabasta**

``echo nameserver 192.171.2.2 > /etc/resolv.conf``

![image](https://user-images.githubusercontent.com/80946219/139528559-73889344-bd42-4ae0-b998-5b5ee9e6e0de.png)

![image](https://user-images.githubusercontent.com/80946219/139528571-3ecbebd8-28fa-4cd3-a573-171b82e94c53.png)


## Soal 3
Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie
  
### Jawaban : 

Memodifikasi config yang ada pada file /etc/bind/kaizoku/franky.a05.com
```
    GNU nano 2.5.3   File: /etc/bind/kaizoku/franky.a05.com

    ;
    ; BIND data file for local loopback interface
    ;
    $TTL    604800
    @       IN      SOA     franky.a05.com. root.franky.a05.com. (
                            2021102401      ; Serial
                             604800         ; Refresh
                              86400         ; Retry
                            2419200         ; Expire
                             604800 )       ; Negative Cache TTL
    ;
    @       IN      NS      franky.a05.com.
    @       IN      A       192.171.2.2     ;IP Enieslobby
    www     IN      CNAME   franky.a05.com.
    super   IN      A       192.171.2.4     ;IP Skypie
    www.super IN    CNAME   super.franky.a05.com.
```

Restart

``service bind9 restart``

