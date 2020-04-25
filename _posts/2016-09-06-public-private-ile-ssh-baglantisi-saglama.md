---
title:  "Public / Private ile SSH Bağlantısı Sağlama"
layout: post
---

Bu yazımızda public/private ikilisi ile birlikte SSH bağlantısı
sağlamayı akabinde ise sunucu üzerinde clear parola girilmesini yani
pub/private ikilileri olmadan bağlantı sağlanamamasını
gerçekleştireceğiz.

Openssh-server kurulu değilse öncelikle olarak kurulumunu
gerçekleştirin.

    sudo apt-get install openssh-server

Client bilgisayar üzerinde anahtarlarınızı aşağıdaki komut ile birlikte
oluşturalım.

    sudo ssh-keygen -t rsa

**root/.ssh** dizini altına **id\_rsa ve id\_rsa.pub** dosyaları
oluşturulmuş olacaktır. **id\_rsa.pub** dosyanız uzantısından da
anlaşılacağı üzere public key dosyanızdır. **id\_rsa** ise iyi korunaklı
saklamanız gereken private key dosyanızdır.

Artık public anahtarımızı bağlantı yapacağımız sunucuya gönderelim.

        cat ~/.ssh/id_rsa.pub | ssh root@ip 'umask 077; cat >>.ssh/authorized_keys'

Client bilgisayarda artık aşağıdaki komut ile birlikte uzak sunucuya
parola gerektirmeksizin bağlantı yapabilirsiniz.

        ssh uzaksunucu_ip

Artık public/private ikilisi ile birlikte bağlantı yaptığımıza göre uzak
sunucu üzerinde parola ile girişleri kapatabiliriz. Bunun için
/etc/ssh/sshd\_config kısmında aşağıdaki satırı bularak,

    # Change to no to disable tunnelled clear text passwords
    #PasswordAuthentication yes

bununla değiştiriniz.

    # Change to no to disable tunnelled clear text passwords
    PasswordAuthentication no

    service ssh restart
