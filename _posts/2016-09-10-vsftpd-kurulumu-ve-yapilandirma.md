---
title:  "VSFTPD Kurulumu ve Yapılandırma"
layout: post
---
Yazımıza başlamadan önemli bir hatırlatmak isterim. FTP Protokolü cleartext trafik içermesi nedeniyle
**güvenli** değildir. Bu yüzden **SFTP** protokolünü kullanmanızı öneririm.

## Kurulum

Vsftpd server Ubuntu'nun default repolarından erişilebilmektedir. Ek olarak bir kaynak belirtmenize gerek yoktur.

    sudo apt-get install vsftpd

## Yapılandırma

Vsftpd kurulumu ardından **etc/vsftpd.conf** dosyası içerisinde yapılandırma ayarları bulunmaktadır. Bu dosya içerisinde aşağıdaki değişiklikleri yapmanızı öneririm.

FTP için anonymous bağlantı gerçekleştirmelerini kapatmak için aşağıdaki değeri varsa yorum satırından çıkartıp, "NO" olarak belirtiyoruz.

    anonymous_enable=NO

Sunucu üzerinde yer alan kullanıcıların FTP'ye bağlanabilmelerini sağlamaları amacıyla aşağıdaki değeri varsa yorum satırından çıkartıp, "YES" olarak belirtiyoruz.

    local_enable=YES

Kullanıcıların dosyalar üzerinde yazma işlemi yapabilmelerini sağlamaları amacıyla aşağıdaki değeri "YES" olarak belirtiyoruz.

    write_enable=YES

Kullanıcıların home dizinlerine erişim hakkı vermek amacıyla aşağıdaki değeri "YES" olarak belirtiyoruz.

    chroot_local_user=YES

Bu dosyayı kaydedip, çıkıyoruz.

## FTP Kullanıcı Hesabı Oluşturma

Kullanıcı oluşturalım.

    sudo adduser ftpkullanicisi

Kullanıcıyı oluşturduktan sonra, bu kullanıcıya ait dizin için root sahipliği verelim.

    sudo chown root:root /home/ftpkullanicisi

Şimdi ise kullanıcıya dosyalarını gönderebileceği bir klasör oluşturalım ve bu kullanıcıya erişim hakkı verelim.

    sudo mkdir /home/ftpkullanicisi/dosyalar
    sudo chown ftpuser:ftpuser /home/ftpkullanicisi/files

Vsftpd'yi yeniden başlatalım.

    sudo service vsftpd restart

Tüm işlem bu kadar.
