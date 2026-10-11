# Dosya Sistemi Dökümü Alma

iTunes yedeği çok sayıda faydalı veritabanı ve tanılama verisi sağlasa da, bazı durumlarda cihaza jailbreak yapıp tam bir dosya sistemi dökümü almak isteyebilirsiniz. Bu durumda, çoğu yeni iPhone modelinde root elde etmenin kolay bir yolunu sunan [checkra1n](https://checkra.in/) aracına göz atmalısınız.

!!! warning
    Herhangi bir cihaza checkra1n uygulamadan önce tam bir yedek aldığınızdan ve cihazı geri yüklemeden önce tam bir fabrika ayarlarına sıfırlama yapmaya hazır olduğunuzdan emin olun. checkra1n'in "Restore System" özelliğini kullandıktan sonra bile cihazda jailbreak'e dair bazı izler kalır ve [jailbreak karşıtı uygulamalar bu izleri tespit ederek](https://github.com/checkra1n/BugTracker/issues/279) çalışmayı durdurur.

Cihaza jailbreak yaptıktan sonra telefonunuza ssh üzerinden erişebilmelisiniz. Bunun için genellikle iproxy kullanmanız gerekir; iproxy, Debian/Ubuntu sistemlerinde `libusbmuxd-tools` ile kurulabilir. Şu komutu çalıştırın:

```bash
iproxy 2222 44
```

Artık localhost'a 2222 portundan root olarak ve `alpine` parolasıyla ssh yapabilirsiniz. Not: checkra1n dışında bir jailbreak kullandıysanız, 44 yerine farklı bir port numarası belirtmeniz gerekebilir.

Bu noktada cihazın içeriğine bilgisayarınızdan erişmeniz gerekir. Bir yol, `ssh root@localhost -p 2222 tar czf - /private > dump.tar.gz` gibi bir komut çalıştırmaktır; bu, telefondaki */private/* klasörünün tarball'ını ana makineye kaydeder. Bu işlem biraz zaman alacaktır.

Alternatif olarak, iOS için `sftp-server` çalıştırmayı deneyebilir ve dosya sistemini `sshfs` kullanarak yerel olarak bağlayabilirsiniz.


## iOS'ta `sshfs` Kullanımı

sshfs kullanmayı denemeye karar verirseniz, önce derlenmiş bir sftp-server kopyasını yerel olarak indirmeniz gerekir:

```bash
wget https://github.com/dweinstein/openssh-ios/releases/download/v7.5/sftp-server
```

Ardından ikili dosyayı iPhone'a yükleyin:

```bash
scp -P2222 sftp-server root@localhost:.
```

`sftp-server`'ın çalışmasına izin vermek için cihaza ssh ile bağlanmanız ve bazı yetkilendirmeleri (entitlements) ayarlamanız gerekir. Bu yetkilendirmeler mevcut bir ikili dosyadan kopyalanabilir:

```bash
chmod +x sftp-server
ldid -e /binpack/bin/sh > /tmp/sh-ents
ldid -S /tmp/sh-ents sftp-server
```

Artık ana makinede bir klasör oluşturup bunu bağlama noktası olarak kullanabilirsiniz (**not:** bu klasörü /tmp/ içinde oluşturmayın):

```bash
mkdir root_mount
sshfs -p 2222 -o sftp_server=/var/root/sftp-server root@localhost:/ root_mount
```
