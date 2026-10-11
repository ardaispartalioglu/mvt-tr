# Dökümü `mvt-ios` ile Kontrol Etme

Hazır olduğunuzda, `mvt-ios` komutunu dosya sistemi dökümü veya bağlama noktası (mount point) üzerinde çalıştırmaya geçebilirsiniz:

    $ mvt-ios check-fs --help
    Usage: mvt-ios check-fs [OPTIONS] DUMP_PATH

      Extract artifacts from a full filesystem dump

    Options:
      -i, --iocs PATH     Path to indicators file
      -o, --output PATH   Specify a path to a folder where you want to store JSON
                          results

      -f, --fast          Avoid running time/resource consuming features
      -l, --list-modules  Print list of available modules and exit
      -m, --module TEXT   Name of a single module you would like to run instead of
                          all

      --help              Show this message and exit.

Aşağıda `check-fs` komutunun temel kullanımına ilişkin bir örnek verilmiştir:

```bash
mvt-ios check-fs /path/to/filesystem/dump/ --output /path/to/output/
```

Bu komut, çıkarma işleminden elde edilen sonuçları içeren birkaç JSON dosyası oluşturur. Bir `--output` seçeneği belirtmezseniz, `mvt-ios` sonuçları diske kaydetmeden veriyi yalnızca işler.

`--iocs` argümanı aracılığıyla, mvt tarafından yedekten çıkarılan kayıtlara karşı kontrol edilecek kötü amaçlı göstergelerden oluşan bir liste tanımlayan bir [STIX2](https://oasis-open.github.io/cti-documentation/stix/intro) dosyası belirtebilirsiniz. Eşleşmeler hem terminal çıktısında vurgulanır hem de çıktı klasörüne JSON dosya adına "*_detected*" soneki eklenerek kaydedilir.
