# Yedeği mvt-ios ile Kontrol Etme

Yedekleme biraz zaman alabilir. Yedekleme işlemi sırasında telefonun
kilidinin açık kaldığından emin olmanız en iyisidir. İşlem tamamlandıktan
sonra, belirttiğiniz yolun altında yedeklediğiniz iPhone'un UDID'i
kullanılarak yeni bir klasör oluşturulur.

## Şifre çözme anahtarını çıkarma ve kaydetme (isteğe bağlı)

Bir yedeğin şifresini her çözdüğünüzde parola girmek istemiyorsanız, MVT
bunun yerine bir anahtar dosyası kabul edebilir. Bu anahtar `decrypt-backup`
komutuyla kullanılabilir.

Bir anahtar dosyası oluşturmak için cihaz yedeğinize ve yedek parolasına
ihtiyacınız olacaktır:

    $ mvt-ios extract-key --help
    Usage: mvt-ios extract-key [OPTIONS] BACKUP_PATH

      Extract decryption key from an iTunes backup

    Options:
      -p, --password TEXT  Password to use to decrypt the backup  [required]
      -k, --key-file FILE  Key file to be written (if unset, will print to STDOUT)
      --help               Show this message and exit.

Parolayı komut satırında belirtebilir veya MVT'nin parola istemesini sağlamak
için `-p` seçeneğini atlayabilirsiniz. `-k` seçeneği, şifre çözme anahtarını
içeren dosyanın nereye kaydedileceğini belirtir. `-k` atlanırsa MVT, şifre
çözme anahtarını kaydetmeden görüntüler.

_Not_: Bu şifre çözme anahtarı hassas veridir! Dosyayı güvende tutun.

Anahtarı çıkarmak ve MVT'nin parola istemesini sağlamak için:

```bash
mvt-ios extract-key -k /path/to/save/key /path/to/backup
```

## Yedeğin şifresini çözme

Şifrelenmiş bir yedeğiniz varsa önce şifresini çözmeniz gerekecektir. Bu
işlem `mvt-ios` ile de yapılabilir:

    $ mvt-ios decrypt-backup --help
    Usage: mvt-ios decrypt-backup [OPTIONS] BACKUP_PATH

      Decrypt an encrypted iTunes backup

    Options:
      -d, --destination TEXT  Path to the folder where to store the decrypted
                              backup  [required]

      -p, --password TEXT     Password to use to decrypt the backup (or, set
                              MVT_IOS_BACKUP_PASSWORD environment variable)
                              NOTE: This argument is mutually exclusive with
                              arguments: [key_file].

      -k, --key-file PATH     File containing raw encryption key to use to decrypt
                              the backup NOTE: This argument is mutually exclusive
                              with arguments: [password].

      --help                  Show this message and exit.

Parolayı `MVT_IOS_BACKUP_PASSWORD` ortam değişkeninde veya komut satırı
argümanı aracılığıyla belirtebilir ya da bir anahtar dosyası verebilirsiniz.
Şifresi çözülen yedeğin saklanacağı hedef yolu belirtmeniz gerekir. Bir
parola bulunamazsa ve anahtar dosyası da belirtilmemişse, MVT parola
soracaktır. Aşağıda, parolayı bir ortam değişkeni aracılığıyla gönderen
örnek bir `decrypt-backup` kullanımı yer almaktadır:

```bash
MVT_IOS_BACKUP_PASSWORD="mypassword" mvt-ios decrypt-backup -d /path/to/decrypted /path/to/backup
```

## Yedek üzerinde `mvt-ios` çalıştırma

Analiz için şifresi çözülmüş bir yedek hazır olduğunda, `check-backup` alt
komutunu kullanabilirsiniz:

    $ mvt-ios check-backup --help
    Usage: mvt-ios check-backup [OPTIONS] BACKUP_PATH

      Extract artifacts from an iTunes backup

    Options:
      -i, --iocs PATH     Path to indicators file
      -o, --output PATH   Specify a path to a folder where you want to store JSON
                          results

      -f, --fast          Avoid running time/resource consuming features
      -l, --list-modules  Print list of available modules and exit
      -m, --module TEXT   Name of a single module you would like to run instead of
                          all

      --help              Show this message and exit.

Aşağıda `check-backup` komutunun temel bir kullanımı yer almaktadır:

```bash
mvt-ios check-backup --output /path/to/output/ /path/to/backup/udid/
```

Bu komut, çıkarımdan elde edilen sonuçları içeren birkaç JSON dosyası
oluşturur. Bir `--output` seçeneği belirtmezseniz, `mvt-ios` verileri diske
sonuç kaydetmeden yalnızca işler.

`--iocs` argümanı aracılığıyla, mvt tarafından yedekten çıkarılan kayıtlarla
karşılaştırılacak kötü amaçlı göstergelerin listesini tanımlayan bir
[STIX2](https://oasis-open.github.io/cti-documentation/stix/intro) dosyası
belirtebilirsiniz. Herhangi bir eşleşme hem terminal çıktısında vurgulanır
hem de çıktı klasörüne JSON dosya adına "*_detected*" soneki eklenerek
kaydedilir.
