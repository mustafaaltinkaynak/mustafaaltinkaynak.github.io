---
title:  "UFW Kurulumu ve Yapılandırma"
layout: post
---

## UFW nedir?
UFW yani Uncomplicated Firewall güvenlik duvarı yönetim aracıdır. Iptables güvenlik duvarı yapılandırmasını kolaylaştırmak için geliştirilen ufw, ipv4 veya v6 ile desteğiyle birlikte kullanıcıların işini epeyi kolaylaştırmaktadır. Varsayılan olarak UFW devre dışı olarak gelmektedir. UFW'yi konsol üzerinden yönetebileceğiniz gibi GUI desteği sayesinde konsol kullanmadan [Gufw](https://help.ubuntu.com/community/Gufw) ile de yönetebilirsiniz. Ubuntu için varsayılan güvenlik duvarı yapılandırma aracıdır.

## UFW kurulumu

UFW ubuntu üzerinde varsayılan geldiğinden dolayı farklı bir işletim sistemi kuruyorsanız aşağıdaki adımı izleyebilirsiniz.

    mustafa@altinkaynak:/# apt-get install ufw

Kurulum işleminin ardından aşağıdaki komut ile birlikte versiyon kontrolü sağlayabilirsiniz.

    mustafa@altinkaynak:/# ufw version

**ÖNEMLİ:** Eğer ki ev kullanıcı iseniz UFW sizler için varsayılan bir profil ile birlikte sisteminize dahil olmaktadır. Kısacası, ev kullanıcıları için istisnalar dışında tüm gelen istekler reddedilmektedir.

## UFW yönetimi

UFW'yi etkinleştirmek yada pasifleştirmek için aşağıdaki komutu kullanabilirsiniz.

    mustafa@altinkaynak:/# ufw enable
    Firewall is active and enabled on system startup

    mustafa@altinkaynak:/# ufw disable
    Firewall stopped and disabled on system startup

Mevcut UFW'ye ait kurallar dizisini görmek için aşağıdaki komutu kullanabilirsiniz.

    mustafa@altinkaynak:/# ufw status verbose
    Status: active
    Logging: on (full)
    Default: deny (incoming), allow (outgoing), disabled (routed)
    New profiles: skip

Henüz bir kural tanımlamadığımız kurallar dizisi görünmemektedir. Ancak UFW'nin mevcut durumuna ilişkin bilgi sahibi olabileceğimiz bilgiler yer almaktadır. Firewall'ın durumu, loglama hassasiyeti, ön tanımlı davranışını bu çıktı da yer almaktadır.

Kural içerikleri **"/etc/ufw"** altında **".rules"** uzantısı ile biten dosyalarda yer almaktadır.

## Kural oluşturma

UFW üzerinde iki farklı davranış allow, deny belirleyebilirsiniz. Allow ile izin verebilir, deny ile gelen istekleri reddedebilirsiniz.

Allow içeren kurallar aşağıdaki şekilde tanımlanabilir.

    ufw allow <port>/<optional: protocol>

53.port üzerine gelen TCP ve UDP bağlantılarına izin verelim.

    mustafa@altinkaynak:/# ufw allow 53

Yalnızca TCP üzerinden gelen bağlantılara izin vermek için,

    mustafa@altinkaynak:/# ufw allow 53/tcp

Yalnızca UDP üzerinden gelen bağlantılara izin vermek için,

    mustafa@altinkaynak:/# ufw allow 53/udp


Deny içeren kurallar aşağıdaki şekilde tanımlanabilir.

    ufw deny <port>/<optional: protocol>

53.port üzerine gelen TCP ve UDP bağlantılarını engelleyelim.

    mustafa@altinkaynak:/# ufw deny 53

Yalnızca TCP üzerinden gelen bağlantıları engellemek için,

    mustafa@altinkaynak:/# ufw deny 53/tcp

Yalnızca UDP üzerinden gelen bağlantıları engellemek için,

    mustafa@altinkaynak:/# ufw deny 53/udp

Belirli Subnet'e izin verelim.

    mustafa@altinkaynak:/# ufw allow from 192.168.1.0/24

Belirlenmiş bir IP adresine erişim izni verelim.

    mustafa@altinkaynak:/# ufw allow from 10.50.1.55

Belirlenmiş bir IP adresine yalnızca bir port için izin verelim.

    mustafa@altinkaynak:/# ufw allow from 10.50.1.55 to any port 22

RFC1918 adresleri yani 10.0.0.0/8, 172.16.0.0/12, 169.254.0.0/16, 192.168.0.0/16 olarak belirlenen adreslere izin verelim.

    mustafa@altinkaynak:/# ufw allow from 10.0.0.0/8
    Rule added
    mustafa@altinkaynak:/# ufw allow from 172.16.0.0/12
    Rule added
    mustafa@altinkaynak:/# ufw allow from 192.168.0.0/16
    Rule added

Bazı durumlarda kuralların birbirlerini bastırdığı durumlar olabilir. Örneğin: SSH bağlantılarını tümüyle kapatan bir kural eklediniz. Akabinde de spesifik bir adres için SSH izni verdiniz. Bu durumda ilk eklediğiniz kural gerçekleşecektir ve spesifik IP adresi bağlantı gerçekleştiremeyecektir.

Bu durumda araya kural eklemesi yapabilirsiniz.

    mustafa@altinkaynak:/# ufw insert 1 allow from 10.50.1.55 to any port 22
    Rule inserted

Belirli bir IP adresi veya subnet için protokol sınırlaması getirelim.

    mustafa@altinkaynak:/# ufw allow from 10.50.1.55 to any port 22 proto tcp
    Rule added

    mustafa@altinkaynak:/# ufw allow from 192.168.0.0/16 to any port 22 proto tcp
    Rule added


## Kural kaldırma

UFW üzerinde kural kaldırmak için kural oluşturma sırasında yazmış olduğunuz koda delete eklemeniz yeterlidir.

80.porta gelen bağlantıları engellemek için,
    mustafa@altinkaynak:/# ufw deny 80/tcp

Belirlenen bu kuralı silmek için,

    mustafa@altinkaynak:/# ufw delete deny 53/udp

Yada tüm kurallar içerisinden belirlemiş olduğunuzu silmek istiyorsanız öncelikle aşağıdaki komutla kuralları numaralandırılmış halde listeleyin, ardından silmek istediğiniz kuralın numarasını ufw delete komutuna vermeniz yeterlidir.

    mustafa@altinkaynak:/# ufw status numbered
    Status: active
    To                         Action      From
    --                         ------      ----
    [ 1] 80/tcp                     DENY IN     Anywhere
    [ 2] 80/tcp (v6)                DENY IN     Anywhere (v6)

    mustafa@altinkaynak:/# ufw delete 1
    Deleting:
     deny 80/tcp
    Proceed with operation (y|n)? y
    Rule deleted

## Servisler için kural yazma

UFW /etc/services dosyasını da okuyabildiği için hizmet adına göre de izin verebilir veya reddedebilirsiniz. Servislerin listesini görmek için **"/etc/services"** dosyasına göz atabilirsiniz.

Servis ismiyle bağlantıya izin verilen kurallar aşağıdaki şekilde tanımlanabilir.

    ufw allow <service name>

SSH servisine bağlantılara izin vermek için,

    mustafa@altinkaynak:/# ufw allow ssh
    Rule added
    Rule added (v6)

Servis ismiyle bağlantının reddedildiği kurallar aşağıdaki şekilde tanımlanabilir.

    ufw deny <service name>

SSH servisine bağlantıları reddetmek için,

    mustafa@altinkaynak:/# ufw deny ssh
    Rule added
    Rule added (v6)

## Loglama

UFW loglama için farklı bir değer belirtilmedikçe "low" yani düşük seviye loglama işlemi yapmaktadır. Loglama konusunda hassas davranmak istiyorsanız aşağıdaki komutu 'off', 'low', 'medium', 'high' ve 'full' değerleriyle birlikte kullanabilirsiniz.

Log kaydetme seviyeleri yükseldikçe daha fazla çıktı üreterek, yoğunluğunuza göre diskinizi hızla doldurabilir. Production ortamlarda yapılacak olan seviye belirlemelerinde bunu göz önünde bulundurmalısınız.

Loglama işlemini devre dışı bırakmak için,

    mustafa@altinkaynak:/# ufw logging off
    Logging disabled

En yüksek düzeyde loglama gerçekleştirmek için,

    mustafa@altinkaynak:/# ufw logging full
    Logging enabled

Loglama işlemini tamamiyle devre dışı bırakmak için,

    mustafa@altinkaynak:/# ufw logging off
    Logging disabled

Bir sonraki yazımda görüşmek dileğiyle.
