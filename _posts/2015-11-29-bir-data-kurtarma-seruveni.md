---
title: "Bir Data Kurtarma Serüveni"
layout: post
comments: true
---

Herkese merhabalar bu yazımda sizlere başımdan geçen olay üzerine, üzerinde yoğun bir çalışma yaptığım recovery (geri yükleme) işlemlerinden bahsedeceğim. Bu anlamda kullanmadığım program kalmadı diyebilirim. 1 TB harddiskimi yanlışlıkla bootable sürücüsü olarak seçince olanlar oldu. Olayı saniyesinde fark etsemde datalarım gitmiş içerisine ISO dosyasının boot hali yazdırılmıştı. Yaklaşık 400 gb veri yok olmuştu :(

Bu dakikadan sonra soğuk soğuk terlemeye başlamıştım. Ancak biran evvel denemelere başlamam gerekiyordu. Daha öncesinde hiç veri kurtarmayla ilgili tecrübem yoktu. Bu yüzden bu konuda oldukça acemiydim.

## İlk deneme : "Restorer"
İlk denememde uzun bir süre bekledim. Yaklaşık 19 saat süren tarama sonrasında bana çok sayıda dosya listelemiş fakat isimlendirmeyi de random (rastgele) yapmıştı. 10-15 dosyayı geri yükleyip içeriğine baktığımda en lazım olmayan dosyalarımın olduğunu görünce bu programdan verim alamayacağımı anladım.

## İkinci deneme : "Do your data recovery"
İlkinden neredeyse farksız olan bu yazılımda da sürenin uzun olması beni çıldırttı. 4 gün geçmesine rağmen hiçbir programdan verim alamıyordum.

## Son deneme : "Active@ Partition Recovery for Windows Enterprise"
Harddiskimi iki farklı şekilde scan edebiliyordum. Birincisi Quick scan diğeri ise daha yavaş ve uzun süren Super scan. Ben ikincisini tercih ettim. 23 saat kadar harddisk taraması sürdü. Akabinde Recovery süreci başladı. Fakat kendisinin birebir boyutunda harici bir belleğe ihtiyaç duyuyordu. 1 gün gecikme ile farklı bir harici hdd bulup recovery işlemine başladım. 25 Aralık 2015 18:20'de başlayan recovery işlemi, 26 Aralık 2015 16:30'da sona erdi.

ve mutlu son.

Evet harddiskimin silinen dosyalarını bir başka hdd'ye aktarmayı başarmıştım. Ancak beni bir sorun daha bekliyordu. Recovery işlemi formatsız olarak yapılmış yani windows, linux türev işletim sistemlerinde harddisk içerisini göremiyor ve beni biçimlendirme ekranlarına yönlendiriyordu. Disk yönetiminden baktığım zaman da sanki içerisinde hiç veri yokmuş gibi davranıyordu. Nette yaptığım kısa bir araştırma sonrasında chkdsk yapılması sonrasında düzeldiği şeklinde yorumlar okudum. chkdsk işlemi sonrasında maalesef datalar tekrar yok oldu.

Aynı işlemleri tekrar etmek zorunda kaldım. Bir kez daha silinen harddiski taradım, dataları tekrar farklı hddye aldım. Nette araştırma ardından Disk internal partition recovery 4.2 yazılımını kullanan kişileri gördüm. Bu yazılım formatsız verileri kurtarmaya yarıyordu. Evet bu recovery işlemide burada yapmam gerekiyordu. Sabah 08:00 gibi başladığım recovery akşam 19:00 gibi sona ermişti. Sağlıcakla datalarıma kavuştum.

Kısaca toparlamak gerekirse şöyle adımlayayım.

- Harddiskinizi Active Partition Recovery for Windows Enterprise ile scan edip datalarınızın gelmesini bekleyin.
- Recovery işlemini başlatın ve bir harddiske yedek alın.
- Eğer formatsız bir biçimde kaydedildiyse bunu parametre hatası verdiğinde anlarsınız. Başka bir işlem yapmadan Disk internal partition recovery 4.2 yazılımıyla recovery yapabilirsiniz.

Herkese iyi çalışmalar