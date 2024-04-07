# Qubic.li - Client Türkçe Kazım Rehberi
Bu rehber (https://github.com/qubic-li/client?tab=readme-ov-file)' ın türkçeye çevirilmiş halidir.


Bu, qubic.li'nin ana istemci bileşenidir. Arka uç API'ye bağlanır ve gerçekleştirilecek görevleri alır.

İstemci Docker, Windows ve Linux üzerinde çalışır. Aşağıda kullanım talimatlarını bulabilirsiniz.

1. [Güvenlik Uyarısı](#security-warning)
2. [Minin Havuzu](#pool-mining)
3. [İndir](#download)
4. [Neler Lazım](#whats-needed)
5. [Windows](#windows)
7. [Linux Seçenek 1: qli-Client'i Linux ekranında Çalıştırmak](#linux-option-1-screen-session)
6. [Linux Seçenek 2: qli-Client'i Servis olarak Çalıştırmak (Daha Zor)](#linux-option-2-expert-systemd-linux-service)  
   a. [Ubuntu 22.04](#ubuntu-2204)     
   b. [Debian 12](#debian-12)  
   c. [Redhat Enterprise 8.8](#redhat-enterprise-88)  
   d. [Görüntüleme](#service-monitoring)  
   e. [Client'i Kaldır](#remove-client) 
8. [HiveOs](#hiveos)
9. [Appsettings.json Kişiselleştirmek](#customizing)
10. [Sorun Çözümü](#troubleshooting)

## Güvenlik Uyarısı
Client yapat zeka görevleri için sürücüler indirebilir. Bu kötü bir amaç için kullanılabilir. Clienti çalıştırabileceğiniz en düşük ayrıcalıkla çalıştırınız. Örneğin windowsta Admin ;Linuxta root olarak çalıştırmayın.

Daha fazla "Düşük öncelik prensibi" hakkında bilgi için : https://en.wikipedia.org/wiki/Principle_of_least_privilege

## Mining Havuzu
Eğer kendi qubic altyapını yönetmek istemiyorsan qublic.li havuzuna katılabilirsin. http://app.qubic.li 'de bir hesap açabilirsin(Önerilen) veya minig havuzuna hesap açmadan da katılabilirsin.

Eğer kendi hesabını açmak istiyorsan buradaki talimatları takip et: https://doc.qubic.world/becoming-a-computor-miner/how-to-participate

Eğer kendi hesabını açmak istemiyor isen buradaki talimatları takip et:
1. Bir Qubic Id oluştur (e.g. on https://wallet.qubic.li)
2. Aşşağıda bulunan Clienti indir (Versiyon >=1.2)
3. Clientini bu şekilde başlat `qli-Client <QUBICID> <Kullanmak_istediğin_Çekirdek_Sayısı>`
4. Performansını kontrol et: https://app.qubic.li/public

Ayrıca eğer kodlarla uğraşmak istemiyosan PayoutID yi [Konfigasyon Dosyası](#customizing) buraya yazabilirsiniz

## İndir
Eğer versiyon değişirse Clientinizi güncellemenizi tavsiye ederiz (Örneğin 1.3 den 1.4 e).
Hata düzeltme güncellemelerinde (Örneğin 1.3.1 ten 1.3.2 e) Client güncellemeleri sizin tercihinizdir.

| İşletim Sistemi |  Platform 	|  Versiyon  | İndir | MD5 Hash (qli-Client) | Açıklama
|--- |---	|---	|--- |---	|--- |
| Windows | x64	| 1.8.10 | https://dl.qubic.li/downloads/qli-Client-1.8.10-Windows-x64.zip | 8837D676B455C0423C20DB94E79F1CDC 	|
| Windows | x64	| 1.8.10 | https://dl.qubic.li/downloads/qli-Client-1.8.10-Windows-x64-Plain.zip | 8837D676B455C0423C20DB94E79F1CDC 	| Varsayılan Konfigirasyonsuz Versiyon.
| Linux | x64	| 1.8.10 | https://dl.qubic.li/downloads/qli-Client-1.8.10-Linux-x64.tar.gz | 66d428e71b889dddab885a92bd3601f2 	| 
| Linux | x64	| 1.9.0 beta | https://dl.qubic.li/downloads/qli-Client-1.9.0-Linux-x64.tar.gz | E1A28AB7BE0B73B28B8A13A99713D9D5 | Deneysel Beta.
| HiveOs | x64	| 1.8.10 | https://github.com/qubic-li/hiveos | | Lütfen HiveOs için talimatları takip edin.


## İhtiyacınız Olanlar
Windowstaki sürücü VC Redistributable gerektirir, VC Redistributable 'ı buradan indirebilirsiniz: https://learn.microsoft.com/en-US/cpp/windows/latest-supported-vc-redist?view=msvc-170

Linuxtaki sürücü en az **GLIBC 2.34** gerektirir.
Client'te çalıştıracağınız işlemci **MUTLAKA** `AVX2` veya `AVX512` işlemci altyapısını desteklemesi lazım.

### Huge Pages
Ortamınıza bağlı olarak, huge pages'e izin vermek isteyebilirsiniz. Bu, saniyedeki it/s 'inizi artırabilir

Client size olası bir hata ile karşılaşılması durumunda optimal ayarları söyleyecektir.
```bash
2024-03-16 11:34:13     INFO    Trainer: WARNING: Free number of hugepages is smaller than needed, have: 10 - want: 1612 (52 x number of threads). Falling back to use malloc memory.
2024-03-16 11:34:13     INFO    Trainer: To add more hugepages, please run this command as root before starting the miner
2024-03-16 11:34:13     INFO    Trainer: /usr/sbin/sysctl -w vm.nr_hugepages=1612
2024-03-16 11:34:13     INFO    Trainer: OR: /sbin/sysctl -w vm.nr_hugepages=1612
```

eğer bunu görürseniz, bu komutu çalıştırabilirsiniz : `/usr/sbin/sysctl -w vm.nr_hugepages=1612` veya root olarak `sudo /usr/sbin/sysctl -w vm.nr_hugepages=1612`


## Komutların Anlamları ve Konfigürasyon
Clientinizi isterseniz komutlarla veya [appsettings.json](#customizing) dosyasından başlatabilirsiniz.

|  Argüment 	|  Varsayılan Değer 	|  Açıklama 	|
|---	|---	|---	|
| PayoutId	| NULL	| Mining havuzuna katılanların madencilik ödüllerinin verileceği cüzdan adresi	|
| Threads	| 1	| Yapay zeka eğitimi için kaç işlemci çekirdeği kullanılacağı.	|
| Alias	| qubic.li Client	| Clientinize bir ad verebilirsiniz. Makinenizin adı app.qubic.li de gözükecektir. Eğer ad koymak istemiyosanız makinanız Alias adında gözükecektir |

## Windows
Clienti direk olarak Windowstan çalıştırabilirsiniz. Client dosyası .exe uygulamasını içermektedir. Çift tıklauarak kolay bir şekilde kazıma başlayabilirsiniz.

Clineti yukarıda bulunan linkten indirin. Client sizin konfigürasyon ayarlarınızla birlikte gelmez.

## Linux Opsiyon 1: Ekrandan kontrol etmek  
qubic.li Clientini çalıştırmak için bu kurulum rehberini takip edin. Komutları dizin tercihlerinize uyacak şekilde uyarlamayı unutmayın. Tüm komutların root kullanıcı olarak yürütülmesi gerektiğini unutmayın, veya uygun yetkilendirme için komutlardan önce sudo komutunu eklemelisiniz.

**1. qli-Client ini İndirmek ve Kurmak:**  
*qli-Client i indirmek için takip eden örneği uygulayın. Bu örnek bu paketi kullanmaktadır: qli-Client-1.8.8-Linux-x64.tar.gz. Lütfen komutu en son desteklenen versiyonla güncelleyin.*  
```bash
mkdir ~/qubic;
cd ~/qubic;
wget https://dl.qubic.li/downloads/qli-Client-1.8.8-Linux-x64.tar.gz;
tar -xvf qli-Client-1.8.8-Linux-x64.tar.gz;
rm qli-Client-1.8.8-Linux-x64.tar.gz;
```  
**2. appsettings.json dosyanızı kendi bilgilerinizle değiştirin**
```bash
nano appsettings.json
```
Ekran kartı için appsettings.json örneği :
```json
{
  "Settings": {
    "baseUrl": "https://mine.qubic.li/",
    "allowHwInfoCollect": true,
    "overwrites": {
      "CUDA": "12"
    },
    "accessToken": "YOURACCESSTOKEN",
    "alias": "YOURALIAS"
  }
}
```
10 çekirdekli bir işlemci için appsettings.json örneği:
```json
{
  "Settings": {
    "baseUrl": "https://mine.qubic.li/",
    "overwrites": {},
    "accessToken": "YOURACCESSTOKEN",
    "amountOfThreads": 10,
    "alias": "YOURALIAS"
  }
}
```

**3. qli-Client 'i "qubic" isimli pencede çalıştırın**

```bash
screen -S qubic ./qli-Client
```
Temel pencere komutları:  

- Ekrandan çıkmak : ctrl a + d  
- Ekranı kapatmak : ctrl + c  
- qubic penceresini görüntülemek: ``` screen -r qubic```  

## Linux Opsiyonu 2 (Zor): Systemd Linux Servisi
Bütün qubic.li servislerini indirmek için kolay kurulum rehberini takip edebilirsiniz. Lütfen servis kurulum scriptinin içeriğini kontrol etmeyi ihmal etmeyin.
Bütün komutların root olarak uygulanması gerekir veya komutların başına `sudo` komutunuz eklemeniz lazım.

Kurulum scripti bütün qubi.li dosyalarını `/q` içine aktarır.


### Ubuntu 22.04
Lütfen alttaki mining havuzu token'ini kendi mining havuzu tokeninizle değiştirmeyi untumayın. Kendi mining havuzu tokeninizi [Buradaki siteden](https://app.qubic.li/main/mining/pool) burdan alabilirsiniz(Sitede hesap oluşturmayı unutmayın).

```bash
# Sürücüleri güncelle
apt update
# Servis indirme scriptini otomatik günceleme ile indir
# wget -O qli-Service-install.sh https://dl.qubic.li/cloud-init/qli-Service-install-auto.sh
# Servis indirme scriptini otomatik günceleme omadan indirmek
wget -O qli-Service-install.sh https://dl.qubic.li/cloud-init/qli-Service-install.sh
# Scripti uygulanabilir olarak ayarla
chmod u+x qli-Service-install.sh
# qubic.li Clientini systemd servisi olarak indirme
# Söz dizimi: qli-Service-install.sh <çekirdek> <accessToken|payoutId> [alias]
./qli-Service-install.sh 2 eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJJZCI6ImMyYmUyNTVkLWRkNzAtNDE4Mi04MDdkLWM1M2M5Yjc5ZDgyNiIsIk1pbmluZyI6IiIsIm5iZiI6MTcwNDczODc4NSwiZXhwIjoxNzM2Mjc0Nzg1LCJpYXQiOjE3MDQ3Mzg3ODUsImlzcyI6Imh0dHBzOi8vcXViaWMubGkvIiwiYXVkIjoiaHR0cHM6Ly9xdWJpYy5saS8ifQ.j8oI56OqV-gjoHxacwegetd2nha1zHLfSjW-REcNsp8q0lWW-NbvClPIuy_nig-YqpbyPXRPAZvYjh1SUjkw7g
```
**ubuntu 20.04** için daha güncel libc versiyonunu indirmeniz lazım olabilir.
```
# Depo ekle (konumunuza yakın bir aynadan yararlanın: https://packages.ubuntu.com/jammy/amd64/libc6/download)
echo "deb http://cz.archive.ubuntu.com/ubuntu jammy main" >> /etc/apt/sources.list
apt update
apt install libc6
apt install -y g++-11
```

### Debian 12
```bash
# Servis indirme scriptini otomatik günceleme ile indir
# wget -O qli-Service-install.sh https://dl.qubic.li/cloud-init/qli-Service-install-auto.sh
# İndirme servisi scriptini indir
wget -O qli-Service-install.sh https://dl.qubic.li/cloud-init/qli-Service-install.sh
# Scripti uygulanabilir olarak ayarla
chmod u+x qli-Service-install.sh
# qubic.li Clientini systemd servisi olarak kur
# Söz dizimi: qli-Service-install.sh <çekirdek> <accessToken|payoutId> [alias]
./qli-Service-install.sh 2 eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJJZCI6ImMyYmUyNTVkLWRkNzAtNDE4Mi04MDdkLWM1M2M5Yjc5ZDgyNiIsIk1pbmluZyI6IiIsIm5iZiI6MTcwNDczODc4NSwiZXhwIjoxNzM2Mjc0Nzg1LCJpYXQiOjE3MDQ3Mzg3ODUsImlzcyI6Imh0dHBzOi8vcXViaWMubGkvIiwiYXVkIjoiaHR0cHM6Ly9xdWJpYy5saS8ifQ.j8oI56OqV-gjoHxacwegetd2nha1zHLfSjW-REcNsp8q0lWW-NbvClPIuy_nig-YqpbyPXRPAZvYjh1SUjkw7g
```
for **debian 11** you might need to install a more recent libc version.

### Redhat Enterprise 8.8
> DEPRECATED
```bash
# Sürücüleri güncelle
sudo yum update
# Servis indirme scriptini otomatik günceleme ile indir
# wget -O qli-Service-install.sh https://dl.qubic.li/cloud-init/qli-Service-install-auto.sh
# İndirme servisi scriptini indir
rm qli-Service-install.sh || wget https://dl.qubic.li/cloud-init/qli-Service-install.sh
# Scripti uygulanabilir olarak ayarla
chmod u+x qli-Service-install.sh
# qubic.li Clientini systemd servisi olarak kur
# Söz dizimi: qli-Service-install.sh <çekirdek> <accessToken|payoutId> [alias]
sudo ./qli-Service-install.sh 2 eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJJZCI6ImMyYmUyNTVkLWRkNzAtNDE4Mi04MDdkLWM1M2M5Yjc5ZDgyNiIsIk1pbmluZyI6IiIsIm5iZiI6MTcwNDczODc4NSwiZXhwIjoxNzM2Mjc0Nzg1LCJpYXQiOjE3MDQ3Mzg3ODUsImlzcyI6Imh0dHBzOi8vcXViaWMubGkvIiwiYXVkIjoiaHR0cHM6Ly9xdWJpYy5saS8ifQ.j8oI56OqV-gjoHxacwegetd2nha1zHLfSjW-REcNsp8q0lWW-NbvClPIuy_nig-YqpbyPXRPAZvYjh1SUjkw7g
```

### Servis Monitörü
qubic.li Client inizi systemd kontol ile yönetebilirsiniz.

Sevisi Başlat: `systemctl start qli`
Servisi Durdur: `systemctl stop qli`
Servis Durumu: `systemctl status qli`

Olayları izlemek için günlüklere bakabilirsiniz.
Servis günlükleri `/var/log/qli.log` veya `/var/log/qli.error.log` dosyalarına kaydedilir.

Canlı olarak konsolda izlemek için `tail -f /var/log/qli.log` komutunu kullanın.


### AccessToken ile örnek konfigürasyon
```json
{
	"Settings": {
		"baseUrl": "https://mine.qubic.li/",
		"amountOfThreads": 16,
		"alias": "Client 3",
		"accessToken": "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJJZCI6ImMyYmUyNTVkLWRkNzAtNDE4Mi04MDdkLWM1M2M5Yjc5ZDgyNiIsIk1pbmluZyI6IiIsIm5iZiI6MTcwNDczODc4NSwiZXhwIjoxNzM2Mjc0Nzg1LCJpYXQiOjE3MDQ3Mzg3ODUsImlzcyI6Imh0dHBzOi8vcXViaWMubGkvIiwiYXVkIjoiaHR0cHM6Ly9xdWJpYy5saS8ifQ.j8oI56OqV-gjoHxacwegetd2nha1zHLfSjW-REcNsp8q0lWW-NbvClPIuy_nig-YqpbyPXRPAZvYjh1SUjkw7g"
	}
}
```
### PayoutID ile örnek konfigürasyon
```json
{
	"Settings": {
		"baseUrl": "https://mine.qubic.li/",
		"amountOfThreads": 16,
		"alias": "My super miner",
		"payoutId": "VGIWRRNVVRRXSEASPENCIMVNANPCFHAASZVPBIEFLCRYHWSZYSGXHNSBYPVN"
	}
}
```

## Clienti Silmek
qubic.li Clientini silmek için aşşağıdaki komutları uygulayın.
```
# Servisi durdur
systemctl stop qli --no-block
# Servisin tanımını değiştir
rm /etc/systemd/system/qli.service
# Servisi tekrar yükle
systemctl daemon-reload
# İlgili bütün dsoyaları sil
rm -R /q
rm /var/log/qli.log
```

## HiveOs
[HiveOs Rehberi](https://github.com/qubic-li/hiveos)

## Konfigürasyon

Appsettings.json Clientinizdeki konfigürasyonları içerir ve düzgünce uygulanabilmesi için aynı yerlere yerleştirişmesi lazım.

Eğer Linux Screen oturumu tercih ederseniz (Opsiyon 1):
 - Clientinizin ayarlarını ayarlar dosyasından konfigüre edebilirsiniz ` ~/qubic/appsettings.json`

Eğer Systemd Linux Servisini tercih ederseniz (Opsiyon 2):
 - Clientinizin ayarlarını ayarlar dosyasından konfigüre edebilirsiniz `/q/appsettings.json`
 - Bu isimle kendinize özel bir dosya oluşturabilirsiniz. `appsettings.production.json` oluşturduğunuz dosyanın yüksek yükleme önceliği vardır.

|  Ayarlar 	|  Varsayılan Değer 	|  Açıklama 	|
|---	|---	|---	|
|  baseUrl 	|  https://mine.qubic.li/ 	|  API'nin Temel URL'si 	|
|  amountOfThreads 	|  1 	|  Yapay zeka eğitimi için kaç çekirdek kullanılacak.	|
|  accessToken* 	|  JWT Token 	| Bu sizi kişisel JWT Token'iniz. qubic.li kontrol panelinden alabilirsiniz	|
|  payoutId* 	|  NULL 	| Bulduğunuz solutıonslar için ödeme yapılacak cüzdan adresi.	|
|  alias 	|  qli Client 	| Clientinize bir ad verebilirsiniz. Makinenizin adı app.qubic.li de gözükecektir. Eğer ad koymak istemiyosanız makinanız Alias adında gözükecektir.	|
|  allowHwInfoCollect	|  false 	| Seçenek `true` olarak ayarlandığında, Client işlemci modeli, işlemci Önbellek Boyutu ve RAM Boyutunu toplayarak makineniz için optimal çalıştırıcıyı elde edecektir.	|
|  threadsDaySchedule 	|  empty 	| Miningi planlamak için kullanılabilir. Örneğin, sadece gece saatlerinde çalışmalıdır. |
|  customRunner	|  false 	|Bunu `true` olarak ayarlayarak özel bir eğitmen kullanılabilir. Client otomatik olarak çalıştırıcıyı güncellemez. [Detaylar](CustomRunner.md) | 
|  serviceLock	|  false 	| Bunu `true`  olarak ayarlayarak özel eğitmeni qiner protocol ile kullanmaya yarar. | 
|  overwrites	|  {} 	| Belirli ayarları geçersiz kılmak için bir nesne. (örneğin, "AVX512":false ile AVX512'yi devre dışı bırakın) | 
|  autoupdateEnabled	|  false 	| Servis istemcisinin otomatik güncellemesini etkinleştirmek için bunu `true` olarak ayarlayın (sürüm 1.7.8'den itibaren) | 
|  checkUpdateEnabled	|  true 	| Başlarken servis istemci için yeni bir sürümün olup olmadığını kontrol eder. | 


*Bunlardan Sadece Bir Tanesi Tanımlanabilir.


## Sorun Giderme
İstemci, tüm hata mesajlarının saklandığı bir `log` klasörü oluşturur. İstemci beklenmedik şekilde durursa veya açılmazsa, mevcut tarihe sahip bir günlük dosyası olduğunu ve hata mesajlarını kontrol edin.

### AVX CPU (Intel) çalışmıyor
Bazı durumlarda AVX2/AVX512 tespiti doğru olmayabilir ve yanlış eğitmen yükleyebilir.
Bu sizin başınıza gelirse, belirli bir yapılandırmayı zorlayabilirsiniz.

### SKYLAKE'i Uygulamak

appsettings.json'da {"SKYLAKE": true} ekleyin

```json
{
	"Settings": {
		"....": "..."
		"overwrites": {
			"SKYLAKE": true
		}
	}
}
```

#### AVX2'i Uygulamak
appsettings.json'da `"AVX512": false` değerini geçersiz kılmak için ekleyin.

```json
{
	"Settings": {
		"....": "..."
		"overwrites": {
			"AVX512": false
		}
	}
}
```

### Konfigürasyonu Sıfırla
Eğer makineniz düzgün çalışmıyosa aşşağıdan yerel yapılandırmayı sıfırlayıb:
```
# Makineyi durdur
systemctl stop qli --no-block
# Bütün qli sevislerinin kapandığından emin ol
pkill -f qli
# Olası kofifürasyon kilitlerini sil
rm /q/*.lock
# Şu anki çalıştırıcıyı sil
rm /q/qli-runner
# Eski solutionsları sil
find /q/. -maxdepth 1 -regextype posix-extended -regex '.*\.e[[:digit:]]+' -delete
# Durum dosyalarını sil
find /q/. -maxdepth 1 -regextype posix-extended -regex 'state\.[[:digit:]]+' -delete
# Makineyi başlat
systemctl start qli --no-block
```

### Serveri Başlat
Eğer istemcinin yanlış veya rastgele bir hash hızı raporladığını düşünüyorsanız, sunucunuzu yeniden başlatabilirsiniz. veya tüm qli hizmetlerini durdurabilirsiniz. (örneğin, Linux için: `systemctl stop qli --no-block && pkill -f qli && systemctl start qli`)

### Mümkün Optimizasyonlar
Her belirli CPU veya genel Donanım yapılandırması belirli performans ayarları gerektirebilir. Kurulumunuza bağlı olarak şunları deneyebilirsiniz:

1. BIOS'ta SMT/HT'yi Etkinleştirme/Devre Dışı Bırakma
2. Web Arayüzü üzerinden başka bir makine seçme: https://app.qubic.li/main/mining/control
3. Performans Artışını Etkinleştirme/Devre Dışı Bırakma (Windows)
4. Belirli Güç Şemasını Etkinleştirme/Devre Dışı Bırakma (Ubuntu)
5. `wine` eklemek/kaldırmak (Linux için). (Ubuntu için: `apt install wine -y` veya `apt autoremove wine -y`)
6. İhtiyacınıza göre İş Parçacıklarını Ayarlama. Bazı durumlarda, çok fazla iş parçacığından daha azı daha etkili olabilir.
