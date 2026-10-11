# Özel CLI Komutları

MVT, `mvt`, `mvt-ios` ve `mvt-android` içine ek üst düzey komutlar yükleyebilir.
Bir komut paketi, komutlarının bu üçünden hangisine ekleneceğini kendisi
seçer.
Özel komutlar [özel adli modüllerden](index.md#custom-modules) farklıdır:
komutlar yeni CLI işlemleri ekler, modüller ise mevcut `check-*` komutlarına
analiz adımları ekler.

!!! warning

    Özel komutlar, MVT süreci içinde güvenilir Python kodu olarak çalışır.
    Komutları yalnızca güvendiğiniz kaynaklardan kurun veya yükleyin. MVT
    üçüncü taraf komutları sandbox'a almaz ve MVT'nin geliştiricileri bu
    komutların bakımını üstlenmez.

## Bir Komut Paketi Kurma

Python paketleri, MVT CLI'larından bir veya daha fazlasına bir Click komutu ya da
grubu kaydedebilir. Minimal bir paket, bu komutu `my_mvt_plugin.py` dosyasından
şu şekilde sunabilir:

```python
import click


@click.command()
@click.argument("path", type=click.Path(exists=True))
def summarize(path):
    """Summarize an acquisition."""
    click.echo(f"Summarizing {path}")
```

`mvt.plugin` modülündeki `get_plugin_logger(__name__)` üzerinden log tutun.
`logging.getLogger(__name__)` üzerinden kaydedilen kayıtlar `command.log`
dosyasına ulaşmaz ve MVT'nin konsol işleyicisi bunları göstermez.

Nesneyi, paketin `pyproject.toml` dosyasına kaydedin. Giriş noktası adı,
kullanıcıların çağırdığı komuttur:

```toml
[project.entry-points."mvt.ios.cli_plugins"]
summarize = "my_mvt_plugin:summarize"

[project.entry-points."mvt.android.cli_plugins"]
summarize = "my_mvt_plugin:summarize"
```

Her giriş noktası grubu, komutu tek bir CLI'a ekler: `mvt.ios.cli_plugins`
`mvt-ios`'a, `mvt.android.cli_plugins` `mvt-android`'e ve `mvt.cli_plugins`
`mvt`'ye. Komutu, sunması gereken her CLI'ın grubuna kaydedin: platforma özgü
bir komut tek bir platform grubuna, her iki platformun toplamalarını ele alan
bir komut ise yukarıdaki gibi her ikisine aittir. Paketi MVT ile aynı ortama
kurduktan sonra komut doğrudan bu CLI'larda görünür:

```bash
mvt-ios summarize ./ios-backup
mvt-android summarize ./androidqf-output
```

MVT'nin `pipx` ile kurulduğu durumlarda eklentiyi MVT'nin ortamına enjekte
edin:

```bash
pipx inject mvt my-mvt-plugin
```

MVT etkin bir sanal ortama kuruluysa, eklentiyi o ortamda `pip` ile kurun.
`mvt plugins list` kurulu paketleri ve ekledikleri komutları gösterir; bkz.
[Eklentileri Yönetme](plugins.md).

Kendi ayarlarına ihtiyaç duyan komut paketleri (örneğin bir API anahtarı),
bunları MVT'nin kendi `config.yaml` dosyası yerine ad alanlı bir
[eklenti yapılandırma dosyasında](plugin_configuration.md) saklamalıdır.

### `mvt` Üzerindeki Komutlar

Bir MVT eklenti komutu, temel `mvt` komutuna alt komutlar da ekleyebilir. Bu,
belirli bir adli platforma bağlı olmayan komutlar için kullanılabilir:

```toml
[project.entry-points."mvt.cli_plugins"]
my-plugin = "my_mvt_plugin:my_plugin"
```

Bu gruptaki komutlar yalnızca `mvt`'ye eklenir; dolayısıyla bu komut
`mvt my-plugin` olarak çağrılır. `mvt` üzerindeki bir komutun hangi eklentiye
ait olduğunu belirtmek için adından başka hiçbir şeyi yoktur; bu nedenle onu
eklentinin adıyla adlandırın ve eklentinin sunacağı birkaç işlem olduğunda
(örneğin `mvt my-plugin configure`) bunu bir Click grubu yapın.

## Bir Komutu Yerel Olarak Geliştirme

Bir paket, bir komutun dağıtılma biçimidir. Bir komut yazılırken MVT onu
bunun yerine doğrudan dosyasından yükleyebilir; böylece paketin her
değişiklikten sonra yeniden kurulması gerekmez. Paketin düzenlenebilir
(editable) kurulumu da aynı şeyi giriş noktaları üzerinden yapar. `cli`
adında bir Click komutu veya grubu dışa aktaran bir Python dosyası oluşturun:

```python
import click


@click.command("case-summary")
@click.argument("path", type=click.Path(exists=True))
def cli(path):
    """Summarize a case directory."""
    click.echo(f"Summarizing {path}")
```

Dosyayı, özel komutun adından önce verin:

```bash
mvt-ios --load-command ./case_summary.py case-summary ./ios-backup
```

`--load-command` birden çok kez kullanılabilir ve bir klasörü de kabul eder.
MVT, gizli olmayan üst düzey `*.py` dosyalarını sıralı biçimde yükler ve
`__init__.py` dosyasını atlar. Yüklenen her dosya bir `cli` nesnesi dışa
aktarmalıdır.

Bir dosyayı veya klasörü her çağrıda yüklemek için, komutların ait olduğu
CLI'ın ortam değişkenini ayarlayın. Giriş noktası gruplarında olduğu gibi,
her değişken de komutlarını yalnızca tek bir CLI'a ekler:

```bash
export MVT_CUSTOM_COMMANDS=./commands
export MVT_IOS_CUSTOM_COMMANDS=./ios_commands
export MVT_ANDROID_CUSTOM_COMMANDS=./android_commands
```

## Modül Çalıştıran Bir Komut Oluşturma

Bir toplama üzerinde adli modülleri çalıştıran bir komut, `Command` sınıfından
türetilir. `Command` çıktı klasörünü oluşturur ve `command.log` dosyasını yazar.
Modülleri sıralar, bağımlılıklarını çözer ve çalıştırır. Sonuç dosyalarını,
`alerts.json` ve `info.json` dosyalarını yazar. Alt sınıf; `platform`, `name`
ve `modules` değerlerini ayarlar:

```python
from mvt.plugin import Command, MVTModule, convert_unix_to_iso, get_plugin_logger

log = get_plugin_logger(__name__)


class APKManifest(MVTModule):
    supported_commands = (("android", "check-apks"),)

    def run(self):
        self.results = [{"checked_at": convert_unix_to_iso(0)}]


class CmdCheckAPKs(Command):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.platform = "android"
        self.name = "check-apks"
        self.modules = [APKManifest]
```

`platform` ve `name`, modüllerin `supported_commands` içinde bildirdiği çifti
oluşturur. `modules` ise komutun çalıştırdığı modül sınıflarını doğrudan içe
aktarılmış olarak listeler. Başka bir eklentinin modüllerini çalıştıran bir
komut, `pyproject.toml` içinde o pakete bağımlıdır ve onları aynı şekilde içe
aktarır. `init()`, `module_init(module)` ve `finish()` isteğe bağlı
kancalardır. `run()` bunları çalıştırmadan önce, her modülden önce ve
çalıştırmadan sonra çağırır.

Komutu, `--output` seçeneği olan bir Click komutuna sarın. Çalıştırın, ardından
uyarı özetini yazdırın:

```python
import click


@click.command("check-apks")
@click.option("--output", "-o", type=click.Path(exists=False))
@click.argument("TARGET_PATH", type=click.Path(exists=True))
def cli(output, target_path):
    cmd = CmdCheckAPKs(target_path=target_path, results_path=output)
    log.info("Checking APK files at path: %s", target_path)
    cmd.run()
    cmd.show_alerts_brief()
```

`mvt`, `mvt-ios` ve `mvt-android` komutlarının `--verbose` seçeneği bu komut
için de geçerlidir. Komut kendine ait bir `--verbose` seçeneği tanımlamaz. Bir
eklenti komutunun eklediği çift, MVT'nin hiçbir yerinde listelenmez. Bunu
eklentinin README dosyasında belirtin.

## Adlandırma ve Hatalar

Yerleşik MVT komutları değiştirilemez. Harici komut adları da her CLI'da
benzersiz olmalıdır; kurulu paketler veya ortam yolları çakıştığında MVT ilk
komutu korur ve bir uyarı kaydeder. Bir CLI'ın ortam yolu, kurulu paketlerinden
önce kaydedilir; bu nedenle oradan yüklenen bir komut, bir paketle olan
çakışmayı kazanır. Açık bir `--load-command` ile gelen çakışma ise bir kullanım
hatasıdır.

İçe aktarılamayan bir paket giriş noktası veya ortam komutu, diğer MVT
komutlarının çalışmasını engellemeden işaretlenmiş bozuk bir komut olarak
görünür. Paket veya dosya kaynağını ve temel hatayı görmek için o komutu
çağırın. `--load-command` ile açıkça verilen geçersiz bir komut ise hemen bir
kullanım hatasıyla başarısız olur.

Kurulu komut paketleri, CLI komut adı olarak giriş noktası adını kullanır.
Giriş noktası bir `click.Command` veya `click.Group` olarak çözümlenmelidir.
