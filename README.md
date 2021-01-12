<p align="center">
 <img src="https://cdn.discordapp.com/attachments/767393287422083102/798238427547697182/IT.png" width="100px"></a>
</p>

<h3 align="center">Pengamanan Website Pemerintah Menggunakan WAF Guna Mencegah Serangan</h3>

<div align="center">

  [![Follower](https://img.shields.io/github/followers/peacefulhack?style=social)]()
  [![Status](https://img.shields.io/badge/status-active-success.svg)]()
  [![GitHub Download](https://img.shields.io/github/downloads/peacefulhack/Modsecurity_azure/total)]()

<!--   <a href="https://www.producthunt.com/posts/the-documentation-compendium?utm_source=badge-top-post-badge&utm_medium=badge&utm_souce=badge-the-documentation-compendium" target="_blank"><img src="https://api.producthunt.com/widgets/embed-image/v1/top-post-badge.svg?post_id=157965&theme=dark&period=daily" alt="The Documentation Compendium - Beautiful README templates that people want to read. | Product Hunt Embed" style="width: 250px; height: 54px;" width="250px" height="54px" /></a> -->

</div>

---



<p align = "center">💡 Perlindungan Waf yang kami ambil disini adalah Modsecurity, dan melakukan integrasi pada bot Discord.</p>


## Table of Contents

- [Dependency](#dependency)
- [Membuat & Masuk VM](#setup_azure)
- [Installasi Modsecurity](#install_modsec)
- [Konfigurasi Modsecurity](#conf_modsec)
- [Pengujian Modsecurity](#uji_modsec)
- [Screenshoot](#ss)
  - [Novice Technical Writers](https://www.writethedocs.org/guide/#new-to-caring-about-documentation)
  - [Experienced Technical Writers](https://www.writethedocs.org/guide/#experienced-documentarian)
  - [API Documentation](https://www.writethedocs.org/guide/#api-documentation)
  - [Adding badges](https://github.com/badges/shields/blob/master/README.md#examples)
  - [Tools](https://www.writethedocs.org/guide/#tools-of-the-trade)
- [Technical Writer Programs](#programs)
- [Awesome Technical Writing Sources](#sources)
- [Get Feedback](#feedback)
- [Acknowledgements](#acknowledgements)


## Dependency <a name = "dependency"></a>

- Untuk mengikuti panduan github ini, pastikan anda menginstall/memiliki berikut:

| Nama          | Link          |
| ------------- | ------------- |
| `Visual Studio Code`     | [Menuju Situs](https://code.visualstudio.com/download)  |
| `Node Js`  | [Menuju Situs](https://nodejs.org/en/download/package-manager/)  |
| `Akun Azure`  | [Menuju Situs](https://azure.microsoft.com/en-us/)  |


## Membuat & Masuk VM<a name = "setup_azure"></a>

![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) `Step ini tidak diperlukan jika anda tidak ingin membuat keamanan pada VM dan langsung melakukan pada sistem operasi anda.`


Pada tutorial ini, saya menggunakan azure student, yaitu program azure yang memberikan jasanya secara gratis, anda dapat juga mendapatkannya menggunakan github student!
namun keseluruhan sama dan dapat diikuti meski anda memilih untuk opsi membayar

- menuju [portal.azure](https://portal.azure.com/#home) lalu klik pada pilihan [virtual machine](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Compute%2FVirtualMachines) yang ada pada portal.azure, selanjutnya tekan +add -> Virtual Machine. akan muncul sebuah form, Isi pilihan VM sesuai dengan kebutuhan anda, atau jika kalian ingin, dapat mengikuti pilihan saya

| Nama          | Pilihan          |
| ------------- | ------------- |
| Subscription     | Azure for Students  |
| Virtual machine name  | peacefulhack  |
| Resource group  | ITS  |
| Region  | East Asia  |
| Image  | Ubuntu 18.04 LTS - Gen 1  |
| Azure spot instance  | uncheck  |
| Size  | Standard_B1s - 1 Vcpu, 1 GiB Memory  |
| Authentication Type  | SSH public key  |
| username  | azureuser |

- lalu tekan Review & Create, lalu tekan create, setelah pembuatan selesai, simpan private key ke dalam folder terserah kalian, pastikan memiliki keamanan setara **CHMOD 400**, setelah itu cek IP public kalian pada VM, lalu pada command prompt kalian, ketik
`ssh -i [lokasi private key] [nama user]@[IP Publik]`
contohnya seperti ini:
`ssh -i C:\azurekey\key.pem azureuser@192.168.0.0`
- setelah itu, anda sudah masuk kedalam VM anda!

**Penting untuk dihindari:**

- jangan gunakan folder/nama yang memiliki spasi, dapat mengakibatkan error
- jangan gunakan nama user yang terlalu singkat atau terlalu panjang, akan menyakitkan jika menulis satu lagi pada Command prompt anda :)
- Jangan lupa backup setiap hari, agar terhidar dari kehilangan data


## Installasi Modsecurity <a name = "install_modsec"></a>

Pertama, lakukan instalasi Apache2

```bash
sudo apt-get update
sudo apt-get install Apache2
```

lalu tekan **Y** dan tekan **Enter** untuk mengkonfirmasi instalasi, selanjutnya melakukan instalasi modsecurity, gunakan perintah berikut:

```cmd
sudo apt-get install libapache2-mod-security2
sudo service apache2 restart
```
setelah anda melakukan restart pada service apache, anda dapat mengecek bahwa apache berjalan menggunakan perintah dibawah

```cmd
sudo apachectl -M | grep security
```

maka akan muncul seperti ini

```
security2_module (shared)
```

## Konfigurasi Modsecurity <a name = "conf_modsec"></a>

ModSecurity membutuhkan *rule* agar berfungsi. rule memutuskan bagaimana komunikasi ditangani di server web. Bergantung pada konfigurasinya, ModSecurity dapat meneruskan, menjatuhkan, mengarahkan ulang, menjalankan skrip, atau bahkan menampilkan kode status.
File konfigurasi default ada pada **/etc/modsecurity/modsecurity.conf-recommended** yang harus anda hilangkan **-recommended** nya agar berjalan, lakukan rename menjadi **/etc/modsecurity/modsecurity.conf** 

```
sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
```

Kemudian, edit file yang telah Anda salin menggunakan editor nano:

```
sudo nano /etc/modsecurity/modsecurity.conf
```

Ubah nilai `SecRuleEngine`  dari `DetectionOnly`  menjadi `On`.

```
SecRuleEngine = On
```

Tekan> **CTRL + X** lalu **Y** dan **Enter** untuk menyimpan file.
Mulai ulang Apache agar perubahan diterapkan.

```
sudo systemctl restart apache2
```

ModSecurity memiliki aturan default yang ditetapkan di direktori /usr/share/modsecurity-crs . Namun, selalu disarankan untuk mengunduh rule yang ditetapkan dari GitHub:

Sebelum kamu melakukan ini, ganti nama direktori rule default:

```
sudo mv / usr / share / modsecurity-crs /usr/share/modsecurity-crs.bk
```

Kemudian, unduh kumpulan rule baru dari GitHub menggunakan perintah di bawah ini:

```
sudo git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git /usr/share/modsecurity-crs
```

Salin file konfigurasi contoh dari rule yang didownload menggunakan perintah di bawah ini:

```
sudo cp /usr/share/modsecurity-crs/crs-setup.conf.example /usr/share/modsecurity-crs/crs-setup.conf
```

Agar aturan ini berfungsi di Apache, Anda harus mengedit file /etc/apache2/mods-enabled/security2.conf menggunakan editor nano

```
sudo nano /etc/apache2/mods-enabled/security2.conf
```

lalu tambahkan dua baris berikut di akhir

```
IncludeOptional /usr/share/modsecurity-crs/*.conf
IncludeOptional "/usr/share/modsecurity-crs/rules/*.conf
```

Restart Apache

```
sudo systemctl restart apache2
```

## Pengujian Modsecurity <a name = "uji_modsec"></a>

Sekarang Anda dapat mencoba menjalankan skrip berbahaya di browser dan melihat apakah rule ModSecurity akan *tertrigger*. Masukkan URL di bawah ini di browser. Ingatlah untuk mengganti alamat IP dengan alamat IP publik server atau nama domain Anda

```
http://[IP PUBLIC ANDA]/index.html?exec=/bin/bash
```

Anda harus mendapatkan pesan warning:
Forbidden You don't have permission to access / on this server. Apache/2.4.29 (Ubuntu) Server at 127.0.0.1 Port 80

## Implementasi Discord bot <a name = "sources"></a>

lakukan instalasi discord bot menggunakan Discord.js, ikuti langkah penginstallan pada [Discord.js Guide](https://discordjs.guide/) dari awal sampai command handler -> [additional feature](https://discordjs.guide/command-handling/adding-features.html#reloading-commands), setelah selesai buatlah command bernama **mod-logs.js** berisi:

```js
                                                                                                                                                         
module.exports = {                                                                                                                                       
        name: 'mods-log',                                                                                                                                
        type: 'util',                                                                                                                                    
        description: 'Discord User Information',                                                                                                         
        cooldown: 1,                                                                                                                                     
        execute(message, args) {                                                                                                                         
        const Discord = require('discord.js');                                                                                                           
        const embed = new Discord.MessageEmbed();                                                                                                        
                                                                                                                                                         
        Tail = require('tail').Tail;                                                                                                                     
        tail = new Tail("/var/log/apache2/error.log");                                                                                                   
        tail.on("line", function(data) {                                                                                                                 
            if(data.includes('Anomaly Score Exceeded')){                                                                                                 
                //message.channel.send(data);                                                                                                            
                var i           = 0;                                                                                                                     
                var regex       = /[\]]/g;                                                                                                               
                var subregex    = "$&--";                                                                                                                
                var hasil       = data.replace(regex, subregex);                                                                                         
                var res = hasil.split("-- ");                                                                                                            
                embed.setTitle("Skyfall Defence System")                                                                                                 
                        .setColor(0xff0000)                                                                                                              
                        .setThumbnail('https://pbs.twimg.com/media/DtLOoJtXcAYMjxR.png')                                                                 
                        .setFooter(res[0], "https://i.pinimg.com/originals/0f/00/e1/0f00e1fdbdc45f7abeac51108dc6e804.png")                               
                        .setAuthor("Peacefulhack", "https://cdn.discordapp.com/avatars/453895628439814165/3726ed7013a34de9b90de18e7297eb23.webp")        
                        .setDescription("**Identitas**" + "\n" +                                                                                         
                                        res[2] + "\n" +                                                                                                  
                                        res[3] + "\n" +                                                                                                  
                                        res[4] + "\n\n" +                                                                                                
                                        "**Deskripsi**"+ "\n" +                                                                                          
                                        res[5] + "\n\n" +                                                                                                
                                        res[8]                                                                                                           
                );                                                                                                                                       
                message.channel.send(embed);                                                                                                             
            }                                                                                                                                            
            //console.log(data);                                                                                                                         
        });                                                                                                                                              
        tail.on("error", function(error) {                                                                                                               
            console.log('ERROR: ', error);                                                                                                               
        });                                                                                                                                              
                                                                                                                                                         
                                                                                                                                                         
        },                                                                                                                                               
};                                                                                                                                                       
 ```
 
agar dapat menggunakan dependency Tail, anda harus menginstallnya menggunakan

```
npm install tail
```

selanjutnya, jalankan perintah menggunakan `node index.js` lalu pada discord, ketik (prefix)mod-logs pada channel yang anda inginkan, lalu bot akan otomatis mengupdate setiap ada serangan dari luar kedalam channel tersebut.

## Screenshoot <a name = "ss"></a>

- [feedmereadmes](https://github.com/LappleApple/feedmereadmes) - Free README editing + feedback to make your open-source projects grow. See the README maturity model to help you keep going
- [maintainer.io](https://maintainer.io/) - Free README standardization and feedback if you click on 'Book an audit'


## Acknowledgements <a name = "acknowledgements"></a>

1. [Documenting your projects on GitHub](https://guides.github.com/features/wikis/) - GitHub Guides
2. [documentation-handbook](https://github.com/jamiebuilds/documentation-handbook) - jamiebuilds
3. [Documentation Guide](https://www.writethedocs.org/guide/) - Write the Docs

