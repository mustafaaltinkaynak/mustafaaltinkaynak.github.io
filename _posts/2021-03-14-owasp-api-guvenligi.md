---
title: "OWASP API Güvenliği"
layout: post
comments: true
---
Teknolojinin gelişmesiyle birlikte erişilebilirlik artık mobilize hale gelmiş olduğunu görmekteyiz. HTML & CSS ile başlayan web furyası, cep telefonlarımızın içerisinde 
uygulamalara kadar girmiştir. Artık her sektörün mobilize uygulamalarının yüksek bir kısmı API yöntemiyle orta katman mimarisini tasarlamıştır. Güvenlik bakış açısıyla
değerlendirildiğinde üç katmanlı mimarinin de oluşturulmasında API kullanım yöntemleri karşımıza çıkmaktadır.

Öyle ki; API kullanımı yaygınlaştıkça, bu kullanımın güvenli olmasına yönelik konularda masaya yatırılmıştır. Bu doğrultuda OWASP yani Open Web Application Security Project topluluğu
referans olarak alınabilecek bir TOP 10 listesi yayınlamıştır. Bu yazımız da bu listede yer alan açıklara yer verip, örnekleştirerek sizlere anlatmaya çalışacağım.

2019 yılı itibariyle OWASP API Security Top 10 listesinde yer alan açıklıklar aşağıda yer almaktadır.

Bunlar:
 - API1:2019 Broken Object Level Authentication
 - API2:2019 Broken User Authentication
 - API3:2019 Excessive Data Exposure
 - API4:2019 Lack of Resources & Rate Limiting
 - API5:2019 Broken Function Level Authorization
 - API6:2019 Mass Assignment
 - API7:2019 Security Misconfiguration
 - API8:2019 Injection
 - API9:2019 Improper Assets Management
 - API10:2019 Insufficient Logging & Monitoring

# API1:2019 Broken Object Level Authentication

Listenin ilk sırasında ödül avcılığı programlarında da sıklıkla karşılaşılan, güvenlik araştırmacılarının yakından bildiği ve incelediği IDOR yer almaktadır. 
IDOR en temel bağlamda açıklamak gerekirse doğrulanmamış obje erişimidir. Yani ziyaretçi kendisini erişmek istediği objenin görmeye yetkili veya o objenin sahibi olduğunun **doğrulanmaması** işlemidir.

<p align="center">
  <img src="/assets/images/api19-1.jpg">
  <br/>Şekil 1: Saldırı şeması
</p>


## Örnek saldırı senaryoları (usecase)
- X kullanıcısına ait bir finansal bilgisinin, Y kullanıcısı tarafından görülebilmesi yada tam tersi
- Y kullanıcısına ait olan id2 numaralı finansal numarasının, X kullanıcısı tarafından görüntülenebilmesi

- target/accounts/**id1**/financial_info -> X kullanıcısına ait olan finansal numarası
- target/accounts/**id2**/financial_info -> Y kullanıcısına ait olan finansal numarası

Yukarıdaki istekler değerlendirildiğinde GET metodu ile alınan id ile başlayan parametre, veritabanında sorgulanarak isteği gerçekleştiren kişi **doğrulanmadan** ekrana sonuçlar yazdırılır.

## Önleme Yöntemleri
- Ziyaretçi veya kullanıcı tarafından, yukarıda verdiğimiz saldırı isteklerine benzer bir istek alındığında, burada yer alan ID nesnesinin hangi kullanıcıya aitse, o kullanıcı tarafından
aktif bir oturumun olup, olmadığı sorgulanmalıdır. Eğer ki oturum, ilgili nesnenin sahibine ait değilse giriş sayfasına yönlendirilmelidir.

# API2:2019 Broken Authentication

1.maddeye benzerlik taşıyan bu değerlendirmede ise, kimlik doğrulama mekanizmalarından kaynaklı problemleri ele almaktadır. Bu kapsamda değerlendirildiğinde güvenlik olgusu taşımayan
kimlik doğrulama mekanizmaları oturumların ele geçirilmesi, yetki değişimi gibi problemlere yol açmaktadır.

<p align="center">
  <img src="/assets/images/api19-2.jpg">
  <br/>Şekil 2: Saldırı şeması
</p>

## Örnek saldırı senaryoları
- Kimlik doğrulaması bulunmayan API noktaları
- Best practices yöntemleri kullanmayan kimlik doğrulama yöntemleri (örneğin base64 encoding, md5 hashing gibi)
- Zayıf, tahmin edilebilen, ön tanımlı gelen, tuzlanmamış (hashing), ortak kullanılan kimlik doğrulamaları
- Brute force (kaba kuvvet) saldırılarına karşı güvenli kimlik doğrulama
- Query String veya GET parametresi ile alınan kimlik bilgileri/anahtarlar
- Password recovery (parola kurtarma) için oluşturulan anahtar değerinin tahmin edilebilir olması, istek limitlenmesi

- target/api/login/?username=admin&password=123456 -> GET parametresi ile alınan kimlik bilgileri
- target/api/recovery/?username=admin&recovery_token=123456 -> recovery_token'in tahmin edilebilir ve artan değerli bulunabiliyor olması

## Önleme Yöntemleri
- 2FA (Two factor authentication) kullanılması
- Oturum süresinin belirlenmesi
- Parola kurtarma anahtarlarının süreli veya belirli istek sayısıyla kısıtlanması
- Parola saklamalarında tuzlama kullanılması

# API3:2019 Excessive Data Exposure

API noktalarının vermiş olduğu response paketleri (yanıtlar) içerisinde bir çok veri yer alabilir. UI tarafında bu bilgilerin filtreleneceği düşünülür. Fakat her zaman durum böyle olmaz.
Kullanıcı bilgilerinin olduğu (Users) bir model düşünün. Bu model içerisinde ad, soyad, kullanıcı adı ve parola gibi alanlar bulunur. Eğer ki bu alanların tümü yanıt paketleri içerisinde yer verilirse,
bu kategoriye ilişkin açıklığa sebebiyet verilmiş olur.

<p align="center">
  <img src="/assets/images/api19-3.jpg">
  <br/>Şekil 3: Saldırı şeması
</p>

## Örnek saldırı senaryoları
- Modellere ilişkin tüm alanların yanıt paketleri içerisinde yer alması
- UI uygulamasının yanıt paketlerinin filtrelenmesi sonucu, yalnızca kullanıcının görmesi gereken alanların ekranda yer alması

## Önleme Yöntemleri
- Deployment ve Production ortamlarının iyi kurgulanması, hata mesajlarının prod ortamda yer almaması
- Bir modele ilişkin yalnızca ilgili alanların yanıt paketleri içerisinde yer alması
- Kullanıcıların aslında saldırganların UI arkasında yer alan API noktalarını görülebileceği unutulmamalıdır.

# API4:2019 Lack of Resources and Rate Limiting

Güvenlik test uzmanlarının da sıklıkla karşılaştığı problemlerden biri de rate limiting konusudur. En temelde bir hizmetin istek sınırlaması olmaksızın talepleri karşılamaya çalışmasıdır.
Bu bir SMS servisi olabileceği gibi, giriş ekranı parola sıfırlama modülü de olabilir. Kaynakların tüketilmesine sebep olan bu olayda kritik önem taşıyan e-posta, SMS gibi hizmetler
kuyruk mantığıyla çalışması sebebiyle aksamalara sebep verebilir. 

<p align="center">
  <img src="/assets/images/api19-4.jpg">
  <br/>Şekil 4: Saldırı şeması
</p>

## Örnek saldırı senaryoları
- Dosya yükleme alanlarının, verinin kontrol edilmemesi sonucu büyük boyutlu verinin disk üzerinde yer kaplaması
- Tek kullanımlık parolaların gönderildiği servislere yönelik birden fazla istek sonucu ilgili servisin hizmet verememesi
- Tek kullanımlık parolanın istek limiti olmadığından dolayı tahmin edilebilmesi
- Parola kurtarma sonuc

## Önleme Yöntemleri
- Uygun şekilde hız sınırlaması tanımlanması, örneğin saniyede 100 ziyaretçi vb. gibi
- Dosya yükleme limitlerinin tanımlanması.
- Giriş, parola sıfırlama gibi ekranlarda belirlenen sayı üzerinde aynı kaynaktan istek gelmesi durumunda captcha çıkartılması


# API5:2019 Broken Function Level Authorization

API noktalarına karşılık gelen hizmetlere erişirkenn belirli roller için farklı yollar belirlenebilir. İstemciler için client path'i kullanılırken, yöneticiler için admin pathi
kullanılabilir. Bu doğrultuda tahmini çok da zor olmayan bu pathlere erişim sağlanması sonrasında rol değişimi gerçekleştirilebilir. 1.madde de benzer önlemler bu madde içinde geçerlidir.

<p align="center">
  <img src="/assets/images/api19-5.jpg">
  <br/>Şekil 5: Saldırı şeması
</p>

## Örnek saldırı senaryoları
- Ayrıcalıklı kullanıcıların istek göndermiş olduğu serviste yetki kontrolü bulunmaması
- target/**users**/v1/user/myinfo -> Sıradan kullanıcılara sunulan API hizmeti
- target/**admins**/v1/user/myinfo -> Ayrıcalıklı kullanıcılara sunulan API hizmeti

## Önleme Yöntemleri
- Varsayılan olarak tüm erişimleri reddedin.
- Hizmete sunulan servise çağıran kullanıcıyı ve rolünü teyit edin.
- Aksi durumlarda uyarı oluşturacak mekanizmaları tasarlayın.

# API6:2019 Mass Assignment

API, istemciden aldığı verileri ilgili modelin alanlarına kontrolsüz olarak yazması olayı sonrasında ortaya çıkan güvenlik problemidir. Örneğin profil bilgilerinin güncellendiği bir sayfa yapısı düşünelim.
Bu sayfa yapısında "ad, soyad" bilgilerinin **güncellenememesini**, yalnızca e-posta alanının güncellenebilir olduğunu varsayalım.

<p align="center">
  <img src="/assets/images/api2019-6.jpg">
  <br/>Şekil 6: Saldırı şeması
</p>

## Örnek saldırı senaryoları
- Eğer ki arkaplanda user modeli için aşağıdaki yapı tasarlandıysa saldırganlar sizin güncellenmesini istemediğiniz alanlar için de işlem yapabilir durumdadır.
- Rails için örnek kod yapısı aşağıdaki verilmiştir. Bu satırda kullanıcıdan alınan parametreler doğrudan User modelinin alanlarına yazılmaktadır. Herhangi bir kontrol işlemi yapılmamaktadır.
   - @user = User.new(params[:user])

## Önleme Yöntemleri
- Kullanıcıdan alınan verileri doğrudan nesnenin alanlarına bağlamayın.
- Kullanıcıdan gelen parametrelerin aksine hangi parametrelerin gelebileceğini bir liste olarak belirleyin. 
- API'ler tarafından alınabilecek fakat güncellenmesini istemediğiniz tüm alanlar için readonly özelliğini kullanın.

# API7:2019 Security Misconfiguration

Genel bir güvenlik problemi olan bu başlık, hatalı veya eksik yapılandırılmış noktaları ele alır. Bir hizmet tasarlanırken ön tanımlı ayarların kullanılmış olması da 
bu başlık altında değerlendirilebilir. 

<p align="center">
  <img src="/assets/images/api2019-7.jpg">
  <br/>Şekil 7: Saldırı şeması
</p>

## Örnek saldırı senaryoları
- Güncel olmayan sistemler
- Güncel ve güvenilir olmayan şifreleme/deşifreleme çözümleri (TLS, SSL)
- Listelenen dizin ve dosyalar (directory listing özelliği)
- Herkese açık yönetici arabirimleri
- Hata mesajlarının güvenli yapılandırılmaması

## Önleme Yöntemleri
- Sistem ve sunucular üzerinde hardening (sıkılaştırma) uygulanması
- Yapılandırmadan kaynaklı problemleri bulmasının keşfedilmesi 
- Yönetici arabirimlerinin kısıtlanması
- Hata mesajlarının güvenli kanallar üzerinden ilgililere ulaştırılması

# API8:2019 Injection

Injection başlığı OWASP Top 10'da en üst sırada yer almaktadır. Kullanıcıdan alınan girdinin herhangi kontrolsüz bir şekilde işlenmesi problemin kaynağında yer alır.
SQL injection, LDAP, OS Command başta olmak üzere çeşitleri bulunur. Örneğin kullanıcıdan alınan bir IP adresine ping atma işleminde kontrolsüz kullanım varsa os command açıklığına, kullanıcıdan
alınan giriş bilgilerini kontrolsüz şekilde SQL sorgularında kullanılıyorsa SQL Injection açıklığına sebep olur.

<p align="center">
  <img src="/assets/images/api2019-8.jpg">
  <br/>Şekil 8: Saldırı şeması
</p>

## Örnek saldırı senaryoları
- Kullanıcıdan alınan girdilerin;
  - özel yazılmış SQL sorgularında kullanılması
  - noSQL yapılarda kullanılması
  - LDAP sorgularında kullanılması
  - OS Command çağrılarında kullanılması
  - XML ayrıştırılarında kullanılması

## Önleme Yöntemleri
- Kullanıcıdan alınan hiçbir girdiye güvenilmemelidir. 
- Kullanıcıdan alınan girdileri doğrulayın, filtreleyin, temizleyin.
- Güncel frameworkler tercih edilmelidir.

# API9:2019 Improper Assets Management

Bir çok organizasyon uygulama yönetimlerinde ortam ayrılığına gitmiştir. Bu doğrultuda en temel olarak development (geliştirme), production (üretiim) ortamlar ile karşılaşılır.
Yukarıda da ifade edildiği üzere üretim ortamı ve geliştirici ortamı arasında farklılıklar olabilir. Bir ortamda hata mesajları açıkken, diğer ortamda kapalı tutulması gibi. Saldırganlar
tarafından tespit edilen test ortamlarında güvenliğin prod ortama nispeten daha zayıf olduğu düşünülerek bu ortam hedef alınır.

<p align="center">
  <img src="/assets/images/api2019-9.jpg">
  <br/>Şekil 9: Saldırı şeması
</p>

## Örnek saldırı senaryoları
- Eski ve güncel olmayan API'lerin daha güvensiz olduğu düşünülerek bu ortamlar hedef alınır.
- Geliştirme ortamından, üretim ortamına yayılım gerçekleştirilebilir.

## Önleme Yöntemleri
- API envanteri tutun.
- Geliştirme ortamına erişimleri sınırlayın. Bu ortamdaki verileri karmaşıklaştırarak kullanın.
- API güvenlik duvarı kullanın.

# API10:2019 Insufficient Logging and Monitoring

Bu başlık kayıt altına alma ve izleme/görüntüleme eksikliklerini içerir. Doğru yapılandırılmamış kayıt altına alma (loglama) ve izleme eylemleri şüpheli aktivitenin görünürlüğünü zorlaştırır.

<p align="center">
  <img src="/assets/images/api2019-10.jpg">
  <br/>Şekil 10: Saldırı şeması
</p>

## Örnek saldırı senaryoları
- Logların bütünlüğü bozulabilir.
- Logların herhangi bir SIEM'e aktarılmaması, korelasyonların yazılmamış ve gerçekleştirilmemiş olması

## Önleme Yöntemleri
- Başarısız girişimleri, erişim ve girdi hatalarını kayıt altına alın.
- Kayıt altına alınan logların biçimlendirilmesi farklı formatları desteklemelidir.
- Logların güvenli saklanması ve bütünlüğünün bozulmaması sağlanmalıdır.
- Loglarda hassas veriler yer almamalıdır.
- SIEM aktif olarak kullanılmalı, korelasyonların çalışıp çalışmadığı düzenli health checklerle kontrol edilmelidir.

**Kaynaklar**
- [Apisecurity.io](https://apisecurity.io/encyclopedia/content/owasp/owasp-api-security-top-10)
- [OWASP API Security](https://owasp.org/www-project-api-security/)









