# Sysdiagnose Kontrolü

`mvt-ios check-sysdiagnose` bir iOS sysdiagnose arşivini analiz eder. MVT'nin
kendi `SysdiagnoseInfo` modülü, cihazla ve arşivle ilgili ayrıntıları çıkarır
(bkz. [`sysdiagnose_info.json`](records.md#sysdiagnose_infojson)); kontroller
ise, bu komutu desteklediğini bildiren kurulu
[eklenti paketlerinin](../development/index.md#installed-module-packages)
modüllerinden gelir. Böyle bir modül yoksa komut yine de cihaz ayrıntılarını
kaydeder ve hiçbir adli sysdiagnose modülünün yüklenmediği uyarısını verir;
böylece bu çalıştırma temiz bir analiz sayılamaz.

Komut ya çıkarılmış bir sysdiagnose dizinini ya da özgün gzip ile
sıkıştırılmış tar arşivini kabul eder.

```bash
mvt-ios check-sysdiagnose --output ./results \
    ./sysdiagnose_2024.01.02_03-04-05+0200.tar.gz
```

`info.json` dosyasına analiz edilen dosyaların hash'lerini de dahil etmek
için `--hashes`, uygun modülleri çalıştırmadan görüntülemek için
`--list-modules` kullanın.

## Özel bir modül yazma

`mvt.plugin` içindeki `SysdiagnoseExtraction` sınıfını genişletin; bkz.
[Modül Yazma](../development/index.md#writing-a-module). Modül, MVT'ye bir
klasör ya da bir tar arşivi verilmiş olmasına bakılmaksızın arşivi aynı
şekilde okur. Komut desteğini `supported_commands` içinde bildirir. Bir
modül yazarken
[onu kendi dosyasından yükleyin](../development/index.md#developing-modules-locally).

```python
from mvt.plugin import SysdiagnoseExtraction


class ExampleSysdiagnoseModule(SysdiagnoseExtraction):
    supported_commands = (("ios", "check-sysdiagnose"),)
    slug = "example_sysdiagnose"

    def run(self):
        paths = self._get_files_by_pattern("*/example.log")
        if paths:
            content = self._get_file_content(paths[0]).decode("utf-8", "replace")
            self.results = [{"content": content}]

    def check_indicators(self):
        pass

    def serialize(self, result):
        return None
```

MVT önce tar arşivini çıkarır. `run()` çağrılmadan önce her modül için
`from_sysdiagnose_folder()` çağırır. `ips_files` IPS çökme raporlarını
listeler.

`_get_files_by_pattern()` ve `_get_file_content()`, temel sınıfın dahili
yardımcılarıdır. Arşivi okumak için bunları kullanın. Adları ve imzaları
sürümler arasında değişebilir. Bkz.
`src/mvt/ios/modules/sysdiagnose/base.py`.