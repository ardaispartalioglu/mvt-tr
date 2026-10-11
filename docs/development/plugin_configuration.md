# Eklenti Yapılandırması

[Özel CLI komutları](custom_commands.md) veya [modüller](index.md#installed-module-packages) ekleyen eklenti paketlerinin genellikle kendi ayarlarını saklaması gerekir; örneğin bir API anahtarı, bir sunucu URL'si veya son eşitlemenin zaman damgası. MVT, her eklentinin yapılandırmasını kendi dosyasında tutmasını sağlayan ad alanlı bir ayar temel sınıfı ve bir eklentinin diskte saklaması gereken diğer her şey için bir veri klasörü sunar.

!!! warning

    Eklenti ayarlarını MVT'nin kendi `config.yaml` dosyasına yazmayın. MVT, her
    başlatıldığında bu dosyayı bildiği ayarlarla yeniden yazar; bu nedenle başka
    her bölüm silinir.

## Ayarların Saklandığı Yer

Her eklenti, MVT'nin kendi yapılandırmasının yanındaki bir `plugins` klasöründe tek bir YAML dosyasına sahip olur:

```
~/.config/mvt/plugins/<plugin name>.yaml
```

Üst klasörün tam konumu, MVT'nin `config.yaml` dosyası için kullanılan platform kuralını izler (örneğin macOS'te `~/Library/Application Support/mvt`). Yolu elle oluşturmak yerine `mvt.plugin` içindeki `plugin_config_path()` fonksiyonunu kullanın.

Eklenti adları küçük harfli olmalı ve yalnızca harf, rakam ve tire içerebilir; bu, `mvt-plugin-<name>` paket adlandırma kuralıyla eşleşir. MVT, `plugins` klasörünü `0700` izinleriyle oluşturur ve ayar dosyalarını `0600` izinleriyle yazar; çünkü bu dosyalar genellikle kimlik bilgileri barındırır. Dosyalar geçici bir dosya üzerinden yazılır ve yerine taşınır; böylece kesintiye uğrayan bir kaydetme işlemi geride hiçbir zaman kısmen yazılmış bir ayar dosyası bırakmaz.

## Eklenti Veri Klasörü

Bir eklentinin diskte tuttuğu diğer her şey — örneğin bir önbellek, indirilen bir artefakt veya eşitleme durumu — eklentinin ayar sınıfındaki `data_folder()` sınıf yönteminin ya da eklentinin ayar sınıfı yoksa eklenti adıyla çağrılan `mvt.plugin` içindeki `plugin_data_folder()` fonksiyonunun döndürdüğü klasöre aittir:

```
~/.local/share/mvt/plugin-data/<plugin name>/                # Linux
~/Library/Application Support/mvt/plugin-data/<plugin name>/ # macOS
```

Bu klasör, indirilen göstergeler gibi MVT'nin kendi verilerinin yanında yer alır. Eksikse `0700` izinleriyle oluşturulur. Tekrar istendiğinde aynı yol döndürülür ve içeriğe dokunulmaz; böylece bir eklenti klasöre her ihtiyaç duyduğunda onu isteyebilir. Aşağıdaki `ExamplePluginSettings`, sonraki bölümde tanımlanan ayar sınıfıdır:

```python
import os


def cache_path() -> str:
    folder = ExamplePluginSettings.data_folder()
    return os.path.join(folder, "virustotal_lookups_cache.json")
```

Ayar sınıfı olmayan bir eklenti bunun yerine `plugin_data_folder("example-plugin")` çağrısı yapar.

Kendinize ait `~/.cache/example-plugin` gibi bir yola başvurmayın: bu yalnızca Linux'a özgü bir kuraldır ve MVT bunu sizin için oluşturmaz.

## Eklenti Ayarlarını Tanımlama

`MVTPluginSettings` sınıfından türetin, `plugin_name` değerini ayarlayın ve varsayılan değerlerle tipli alanlar bildirin:

```python
from typing import Optional

from mvt.plugin import MVTPluginSettings


class ExamplePluginSettings(MVTPluginSettings):
    plugin_name = "example-plugin"

    API_KEY: Optional[str] = None
    MAX_RESULTS: int = 25
    LAST_SYNC: Optional[str] = None
```

`load()` mevcut ayarları döndürür, `save()` ise bunları geri yazar:

```python
from datetime import datetime, timezone

import click

from mvt.plugin import plugin_env_prefix


def sync():
    settings = ExamplePluginSettings.load()
    if not settings.API_KEY:
        prefix = plugin_env_prefix(settings.plugin_name)
        raise click.ClickException(
            f"No API key configured. Set {prefix}API_KEY or "
            "run 'example-plugin configure'."
        )

    settings.LAST_SYNC = datetime.now(timezone.utc).isoformat()
    settings.save()
```

İleti, değişken adını `plugin_env_prefix()` ile oluşturur; bkz. [Ortam Değişkenleri](#environment-variables).

Eksik bir ayar dosyası hata değildir: bu durumda eklenti, alan varsayılanlarıyla ve ortamın sağladığı değerlerle çalışır. `save()` yalnızca varsayılanlardan farklı olan değerleri kalıcı hâle getirir ve MVT'nin `config.yaml` dosyasına asla dokunmaz. Ayrıştırılamayan veya ayar adlarından değerlere bir eşleme içermeyen bir ayar dosyası, dosyanın adını belirten bir `PluginConfigLoadError` yükseltir.

Kendi `plugin_name` değerini tanımlayan her alt sınıf kendi dosyasına ve kendi ortam ad alanına sahip olur. `plugin_name` değerini yeniden tanımlamayan bir alt sınıf bu değeri miras alır ve dolayısıyla üst sınıfının dosyasını ve ortam değişkenlerini paylaşır.

## Ortam Değişkenleri {#environment-variables}

Her alan bir ortam değişkeniyle de ayarlanabilir. Önek `MVT_PLUGIN_` olup bunu, tireleri alt çizgiyle değiştirilmiş biçimde büyük harfe dönüştürülmüş eklenti adı ve ardından alan adı izler. Yukarıdaki örnek için:

```bash
export MVT_PLUGIN_EXAMPLE_PLUGIN_API_KEY=...
export MVT_PLUGIN_EXAMPLE_PLUGIN_MAX_RESULTS=50
```

Alan adında eklenti adını tekrarlamayın: `SCANNER_API_KEY` alanına sahip `example-scanner` adlı bir eklenti, kullanıcıdan `MVT_PLUGIN_EXAMPLE_SCANNER_SCANNER_API_KEY` değerini ister. Alanı `API_KEY` olarak adlandırın.

Ayarlar, en yüksekten en düşük önceliğe doğru şu sırayla çözümlenir:

1. Ayar sınıfına doğrudan geçirilen argümanlar, örneğin
   `ExamplePluginSettings(API_KEY="...")`
2. Ortam değişkenleri
3. Eklentinin YAML dosyası
4. Ayar sınıfında bildirilen alan varsayılanları

!!! tip

    Paylaşılan veya çok kullanıcılı makinelerde, API anahtarlarını eklenti
    dosyasına kaydetmek yerine ortam değişkenleri aracılığıyla iletmeyi tercih
    edin. `save()` ortamın o anda sağladığı her değeri atlar; böylece bu yolla
    sağlanan bir kimlik bilgisi, eklenti ilgisiz bir ayarı kaydettiğinde ayar
    dosyasına kopyalanmaz.

Bir eklentinin YAML dosyasındaki bilinmeyen anahtarlar yok sayılır; böylece eklentinin daha yeni bir sürümü tarafından yazılmış bir ayar dosyası, daha eski bir sürümü bozmaz.
