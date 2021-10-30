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


## Soal 4
Buat juga reverse domain untuk domain utama 

### Jawaban : 
Menambahkan 2.171.192.in-addr.arpa pada file `/etc/bind/named.conf.local`

``
   zone "2.171.192.in-addr.arpa" {
       type master;
       file "/etc/bind/kaizoku/2.171.192.in-addr.arpa";
   };

``
Memodifikasi config yang ada pada `/etc/bind/kaizoku/2.171.192.in-addr.arpa`

```
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     franky.a05.com. root.franky.a05.com. (
                         2021102401     ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.171.192.in-addr.arpa.       IN      NS      franky.a05.com.
2                             IN      PTR     franky.a05.com.
```

![image](https://user-images.githubusercontent.com/80946219/139533541-9fd97f25-e9f1-4868-8527-8df44e8469ac.png)




## Soal 5
Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama 
  
### Jawaban : 

Menambahkan konfigurasi dibawah ini di file `/etc/bind/named.conf.local` bagian zone.
``
  notify true	
  also-notify { 192.171.2.3; };
``

Menambahkan zone franky.a05.com pada /etc/named.conf.local pada Water7 sebagai DNS Slave dari Enieslobby

![image](https://user-images.githubusercontent.com/80946219/139534241-eac3ca56-92b7-45e0-a79e-77f514f49d1f.png)

Mematikan DNS Server EniesLobby

``
service bind9 stop
``

Mengatur nameserver

```
nameserver 192.171.2.2
nameserver 192.171.2.3
```

## Soal 6
Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo
  
### Jawaban : 

Pada **EniesLobby**

Menambahkan konfigurasi BIND data pada /etc/bind/kaizoku/franky.a05.com

```
  ns1     IN  A       192.171.2.3
  
  mecha   IN  NS      ns1
  
  www     IN  CNAME   ns1
```

Mengedit `/etc/bind/named.conf.options` dengan `menambahkan allow-query{any;};` dan mengkomen `dnssec-validation auto;`.

Menambahkan `allow-transfer { 192.171.2.3; }` pada `/etc/bind/named.conf.local`


Pada **Water7**

Mengedit `/etc/bind/named.conf.options` dengan `menambahkan allow-query{any;};` dan mengkomen `dnssec-validation auto;`.

Menambah Zone pada `/etc/bind/named.conf.local`

```
zone "mecha.franky.a05.com" {
     type master;
     file "/etc/bind/sunnygo/mecha.franky.a05.com";
};
```

Memodifikasi config pada file /etc/bind/sunnygo/mecha.franky.a05.com

```
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     mecha.franky.a05.com. root.mecha.franky.a05.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      mecha.franky.a05.com.
@       IN      A       192.171.2.4
www     IN      CNAME   mecha.franky.a05.com.
```



![image](https://user-images.githubusercontent.com/80946219/139534399-0701c4f7-9690-43e8-a9e5-895adf46bcd1.png)



## Soal 7
 Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie
  
### Jawaban : 

Memodifikasi config pada `/etc/bind/sunnygo/mecha.franky.a05.com`

![image](https://user-images.githubusercontent.com/80946219/139534757-fb0d6ab4-450f-4503-9ac4-e7dffcf15948.png)


## Soal 8

  
### Jawaban : 


## Soal 9

  
### Jawaban : 

## Soal 10

  
### Jawaban : 


## Soal 11

  
### Jawaban : 

