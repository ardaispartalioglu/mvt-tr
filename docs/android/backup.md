# Android Yedeği Kontrolü (SMS Mesajları)

Android, destekleyen tüm kurulu uygulamaların yedek arşivinin oluşturulmasını destekler. Ancak yıllar içinde bu işlevsellik, kullanıcıların kişisel verilerini bulut üzerinden uzaktan yedekleyebilmesinin sağlanması lehine giderek terk edilmiştir. Bu nedenle uygulama geliştiricileri, uygulamalarının verilerinin yerel olarak dışa aktarılmasına izin vermemeyi seçebilir.

Bu belgenin yazıldığı sırada, yedek oluşturmaya yönelik Android Debug Bridge (adb) komutu hâlâ kullanılabilir durumdadır ancak kullanımdan kaldırılmış (deprecated) olarak işaretlenmiştir.

Bununla birlikte, Android'in çoğu sürümü hâlâ SMS mesajlarının yerel olarak yedeklenmesine izin vermelidir; mesajlar hâlâ kimlik avı (phishing) ve kötü amaçlı yazılım saldırıları için başlıca araçlardan biri olduğundan, bu işlevsellik desteklendiği sürece ondan yararlanmak isteyebilirsiniz.

## Yedek oluşturma

`mvt-android check-backup` şu anda yalnızca SMS mesajlarının kontrol edilmesini desteklediğinden, yalnızca bunların yedeklenmesini belirtebilirsiniz:

```bash
adb backup -nocompress com.android.providers.telephony
```

Yine de tam bir yedek almak isterseniz, bunu şu şekilde yapabilirsiniz:

```bash
adb backup -nocompress -all
```

Bazı yeni telefonlar, yedek arşivini şifrelemek için bir parola kullanılmasını zorunlu tutar. Bu durumda, verileri daha sonra çıkarmak ve analiz etmek için parolanın gerekli olacağı açıktır.

## Yedeği açma ve kontrol etme

MVT, Java ile Python arasındaki sıkıştırma algoritması uygulama farkı nedeniyle Android Yedeği ayrıştırmasının kısmi bir uygulamasını içerir. Yukarıdaki bölümde adb'ye iletilen `-nocompress` seçeneği bu sorundan kaçınmayı sağlar. SMS'leri yedekten doğrudan MVT ile analiz edebilir ve çıkarabilirsiniz:

```bash
$ mvt-android check-backup --output /path/to/results/ /path/to/backup.ab
14:09:45 INFO     [mvt.android.cli] Checking ADB backup located at: backup.ab
         INFO     [mvt.android.modules.backup.sms] Running module SMS...
         INFO     [mvt.android.modules.backup.sms] Processing SMS backup file at
                  apps/com.android.providers.telephony/d_f/000000_sms_backup
         INFO     [mvt.android.modules.backup.sms] Extracted a total of 64 SMS messages
```

Yedek şifrelenmişse MVT sizden parolayı girmenizi isteyecektir. Yedek parolası, `--backup-password` komut satırı seçeneğiyle veya `MVT_ANDROID_BACKUP_PASSWORD` ortam değişkeni aracılığıyla da sağlanabilir. Aynı seçenekler, `mvt-android check-androidqf` komutunda AndroidQF aracılığıyla toplanan şifreli bir yedeği analiz ederken de kullanılabilir:

```bash
$ mvt-android check-backup --backup-password "password123" --output /path/to/results/ /path/to/backup.ab
```

`--iocs` argümanı aracılığıyla, MVT tarafından yedekten çıkarılan kayıtlarla karşılaştırılacak kötü amaçlı göstergelerden oluşan bir liste tanımlayan bir [STIX2](https://oasis-open.github.io/cti-documentation/stix/intro) dosyası belirtebilirsiniz. Eşleşmeler terminal çıktısında vurgulanacaktır.

## Yedeği açmak ve kontrol etmek için alternatif yollar

Yedeğin analizi sırasında bir sorunla karşılaşırsanız, alternatif olarak yedeği okunabilir bir dosya biçimine dönüştürmek için [Android Backup Extractor (ABE)](https://github.com/nelenkov/android-backup-extractor) kullanabilirsiniz. Sisteminizde Java'nın kurulu olduğundan emin olun ve aşağıdaki komutu kullanın:

```bash
java -jar ~/path/to/abe.jar unpack backup.ab backup.tar
tar xvf backup.tar
```

Yedek şifrelenmişse ABE sizden parolayı girmenizi isteyecektir.

Alternatif olarak, bu amaçla [ab-decrypt](https://github.com/joernheissler/ab-decrypt) kullanılabilir.

Daha sonra, `.ab` dosyası yerine klasör yolunu parametre olarak geçirerek SMS'leri MVT ile çıkarabilirsiniz: `mvt-android check-backup --output /path/to/results/ /path/to/backup/` (verilen yedek yolu, `apps` klasörünü içeren klasör olmalıdır).

Bir çıktı klasörü belirtildiğinde, SMS ve MMS mesajlarından çıkarılan URL'ler
de `urls.json` dosyasına yazılır. Her kayıt; URL'yi, gösterge kontrolü sırasında
MVT kısaltılmış bir URL'yi çözümlediğinde genişletilmiş hedefini, mesajın zaman
damgasını ve `sms` kaynağını içerir. Aynı dosya, iç içe Android yedeği URL içeren
mesajlar barındırdığında `check-androidqf` tarafından da oluşturulur.
