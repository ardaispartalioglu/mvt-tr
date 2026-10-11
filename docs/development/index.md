# Geliştirme Talimatları

Mobile Verification Toolkit ekibi, yeni adli modül katkılarını veya yazılımı iyileştirmeye yardımcı olan diğer katkıları memnuniyetle karşılar.

## Yerel ortam

MVT, bağımlılık yönetimi için `uv` kullanır. Projeyi ve geliştirme bağımlılıklarını kilitli ortamdan kurmak için şunu çalıştırın:

```bash
make install
```

## Test

MVT, birim ve entegrasyon testleri için `pytest` kullanır. Kod stili tutarlılığı `ruff` ve `mypy` ile korunur. Tümü aşağıdaki komutla otomatik olarak çalıştırılabilir:

```bash
make check
```

Yeni commit'ler oluşturmadan veya PR açmadan önce bu testleri çalıştırın.

## Modül bağımlılıkları {#module-dependencies}

Modüller, sınıflarını `dependencies` içinde bildirerek diğer modüllerin önce çalışmasını gerektirebilir. Komut çalıştırıcısı kararlı bir topolojik sıralama kullanır; böylece bağımlılık kısıtlamalarının izin verdiği her yerde mevcut modül listesi sırası korunur.

```python
class DependentModule(MVTModule):
    dependencies = (PrerequisiteModule,)

    def run(self):
        prerequisite_results = self.get_dependency_results(PrerequisiteModule)
```

Tek bir modül seçmek, onun geçişli bağımlılıklarını da çalıştırır.

Bir modül yalnızca, içinde çalıştığı komutun da sahip olduğu modüllere bağımlı olabilir. Bildirilen bir bağımlılık bunlar arasında olmadığında, komut modülün ve eksik bağımlılığın adını belirterek bir uyarıyı günlüğe kaydeder, o modülü ve ona bağlı olan her şeyi atlar ve analizin geri kalanını çalıştırır. Böyle bir modülü `--module` ile seçmek bu nedenle çalıştırılacak hiçbir şey bırakmaz; uyarı bunu açıklar.

Bağımlılık grafiğindeki bir döngü, bir yapılandırma sorunu değil programlama hatasıdır: komut bir uyarıyı günlüğe kaydeder ve hiçbir modülü çalıştırmaz.

## Özel modüller {#custom-modules}

MVT'nin modül çalıştıran `check-*` komutları, MVT'nin parçası olmayan adli modülleri çalıştırabilir. Özel modüller eklenti paketleri olarak dağıtılır. MVT'nin yanına kurulan bir Python paketi, modüllerini bir giriş noktası aracılığıyla kaydeder. Modüller daha sonra destekledikleri her komutta otomatik olarak yüklenir; bkz. [Kurulu modül paketleri](#installed-module-packages). `mvt plugins list`, kurulu paketleri ve her birinin nereden kurulduğunu gösterir.

MVT ayrıca modül dosyalarını yolla da yükleyebilir: `--load-module` ve `MVT_CUSTOM_MODULES` ile; bkz. [Modülleri yerel olarak geliştirme](#developing-modules-locally). Bu, bir modül yazarken kullanılabilir. Bir modülü dağıtmak için bir Python paketi kullanın.

Bir özel modül, `supported_commands` içinde çalıştığı platform ve komut çiftlerini bildirir. `supported_commands` değeri boş olan bir modül çalışmaz ve MVT bir uyarıyı günlüğe kaydeder. Dokuz çift şunlardır:

```python
("ios", "check-backup")
("ios", "check-fs")
("ios", "check-iocs")
("ios", "check-sysdiagnose")
("android", "check-backup")
("android", "check-bugreport")
("android", "check-androidqf")
("android", "check-intrusion-logs")
("android", "check-iocs")
```

`check-iocs`, bir edinim yerine saklanan sonuçları yeniden kontrol eder. Sonuçlar klasöründeki her `<slug>.json` dosyasını, o slug'a sahip modülle eşleştirir. Ardından o modülün `check_indicators()` yöntemini yeniden çalıştırır. `check_indicators()` uygulayan bir modül, platformu için `check-iocs`'a dahil edilir. `check-iocs` çiftini bildirmesi gerekmez.

### Bir modül yazma {#writing-a-module}

Bir modül, `MVTModule` veya aşağıdaki temel sınıflardan birinden türetilir ve `run()` yöntemini uygular. `check_indicators()` ve `serialize()` isteğe bağlıdır. Birincisi sonuçları IOC'lerle veya tespitlerle eşleştirir. İkincisi zaman çizelgesi kayıtlarını döndürür.

```python
from mvt.plugin import IOSExtraction, convert_unix_to_iso


class ExampleCustomModule(IOSExtraction):
    supported_commands = (
        ("ios", "check-backup"),
        ("ios", "check-fs"),
    )
    slug = "example_custom_module"

    def run(self):
        self.results = [{"checked_at": convert_unix_to_iso(0)}]

    def check_indicators(self):
        pass

    def serialize(self, result):
        return None
```

Temel sınıflar şunlardır:

- `MVTModule`: her modülün temeli. `self.results`, `self.alertstore`, `self.log`, `self.indicators` ve `get_dependency_results()` sağlar. Yalnızca diğer modüllerin sonuçlarını okuyan bir modül için doğrudan ondan türetin.
- `IOSExtraction`: `("ios", "check-backup")` ve `("ios", "check-fs")`. Bir modülün veri tabanını bir yedekte veya dosya sistemi dökümünde bulan ve bozuksa onaran `_find_ios_database()` yöntemini ekler. `_get_backup_files_from_manifest()`, `_get_backup_file_from_id()` ve `_get_fs_files_from_patterns()` yöntemlerini ekler. Bir veri tabanını salt okunur olarak açan `_open_sqlite_db()` yöntemini ekler.
- `SysdiagnoseExtraction`: `("ios", "check-sysdiagnose")`. MVT arşivi çıkarır ve `run()` öncesinde `from_sysdiagnose_folder()` çağırır. Modül, dosyaları `_get_files_by_pattern()` ve `_get_file_content()` ile okur. `ips_files`, çökme raporlarını listeler. Bkz. [iOS Sysdiagnose Kontrolü](../ios/sysdiagnose.md).
- `AndroidQFModule`: `("android", "check-androidqf")`. MVT, edinimin dosya listesiyle `from_dir()` veya `from_zip()` çağırır. Modül, dosyaları `_get_files_by_pattern()` ve `_get_file_content()` ile okur. `_get_device_timezone()`, cihazın saat dilimini döndürür.
- `AndroidBackupModule`: `("android", "check-backup")`. MVT `from_dir()` veya `from_ab()` çağırır. Modül, dosyaları `_get_files_by_pattern()` ve `_get_file_content()` ile okur.
- `BugReportModule`: `("android", "check-bugreport")`. MVT `from_dir()` veya `from_zip()` çağırır. Modül, dosyaları `_get_files_by_pattern()`, `_get_files_by_patterns()` ve `_get_file_content()` ile okur. `_get_dumpstate_file()` dumpstate dosyasını, `_get_file_modification_time()` ise bir dosyanın değiştirilme zamanını döndürür.

Alt çizgiyle adlandırılan yardımcılar, temel sınıfların iç kullanımına yöneliktir. Eklenti modülleri bunları çağırabilir. Adları ve imzaları sürümler arasında değişebilir. Birine güvenmeden önce `src/mvt/ios/modules` veya `src/mvt/android/modules` içindeki temel sınıfı okuyun.

### Yerleşik bir modüle bağımlılık

Bir veya daha fazla yerleşik MVT modülü tarafından üretilen kayıtları sonradan işleyen bir modül, kaynak modülleri `dependencies` içinde bildirmek zorundadır. Sonuçlarını `get_dependency_results()` ile okur. Sınıfı, ait olduğu aile paketinden içe aktarın: `mvt.ios.modules.backup`, `mvt.ios.modules.fs`, `mvt.ios.modules.mixed`, `mvt.android.modules.androidqf`, `mvt.android.modules.backup`, `mvt.android.modules.bugreport` veya `mvt.android.modules.intrusion_logs`.

```python
from mvt.ios.modules.backup import Manifest
from mvt.plugin import MVTModule


class DependentCustomModule(MVTModule):
    supported_commands = (("ios", "check-backup"),)
    dependencies = (Manifest,)

    def run(self):
        manifest_results = self.get_dependency_results(Manifest)
        self.results = [{"manifest_entries": len(manifest_results)}]
```

Bağımlılıklar, yerleşik modüllerde olduğu gibi sıralanır ve özel modüller yerleşiklerin ardına eklenir; bkz. [Modül bağımlılıkları](#module-dependencies). Bir bağımlılık, modülün desteklediği her komutta çalışmak zorundadır. Bunun geçerli olmadığı durumda MVT, modülü bir uyarıyla atlar.

`get_dependency_results()`, modülün ürettiği düz sözlükleri döndürür. Bunlar, modülün `<slug>.json` dosyasına yazdığı kayıtlarla aynıdır. Modül başına tiplendirilmiş sonuçlar planlanmaktadır.

### Yerleşik bir modülü değiştirme

Yerleşik bir modülü genişleten özel bir modül, onunla birlikte çalışır ve ikisi aynı slug'ı paylaştığında aynı sonuç dosyasına yazarlar. MVT bu çakışma konusunda, her iki modülün adını, her birinin nereden geldiğini ve sonrakinin üzerine yazdığı dosyayı belirterek uyarır; ancak ikisini de çalıştırır. Bunun yerine o modülün yerini almak için `replaces` değerini, yerini alacağınız sınıfa ayarlayın: her ikisi de bir komut için kullanılabilir olduğunda, adı belirtilen modül çalıştırmadan çıkarılır.

```python
from mvt.ios.modules.backup import Manifest as BuiltinManifest


class Manifest(BuiltinManifest):
    supported_commands = (("ios", "check-backup"),)
    replaces = BuiltinManifest
```

Yukarıdaki gibi, değiştirilen modülün sınıf adını korumak, `--module` seçeneğinin modülü seçtiği adı ve sonuç dosyasının adını aldığı slug'ı korur. Farklı bir sınıf adına sahip bir değiştirme, `slug` değerini değiştirdiği modülün slug'ına ayarlamadığı sürece kendi slug'ı adına sahip bir dosyaya yazar; `--module` onu yine de değiştirilen modülün adıyla seçer ve MVT bunu günlüğe kaydeder. Değiştirilen bir modülün slug'ını devralmak bir çakışma değildir ve uyarıya konu olmaz, çünkü o modül artık çalıştırmanın parçası değildir.

Değiştirilen modüle bağımlı olan modüller, onun yerine değiştirmeyi alır; böylece `get_dependency_results(BuiltinManifest)` değiştirmenin sonuçlarını döndürür. Bir modül, değiştirdiği modüle bağımlı olmamalıdır: o modül çalışmaz, dolayısıyla veriyi değiştirmenin kendisi üretmek zorundadır ve bir modül her ikisini birden bildirdiğinde MVT bir uyarıyı günlüğe kaydeder.

Bir değiştirme, değiştirdiği modülün yükümlülüklerini devralır. Komutun sağlamadığı bir bağımlılık bildirirse, kullanılamayan bağımlılığı olan diğer modüller gibi atlanır ve değiştirdiği modül de çalıştırmanın dışında kalır: ikisi de sonuç üretmez ve değiştirilen modüle bağımlı olan modüller de atlanır.

Değiştirilen modülden türetme zorunlu değildir; ancak alt sınıf olmayan bir değiştirme bir uyarıyla günlüğe kaydedilir, çünkü sonuçları, değiştirilen modüle bağımlı modüllerin beklediği gibi olmayabilir. Komutun çalıştırmadığı bir modülün adını belirtmenin bir etkisi yoktur ve bir döngü içinde birbirinin yerini alan modüller çalışmaya devam eder ve hiçbir şeyin yerini almaz. Uygulanan her ikame günlüğe kaydedilir; böylece komut bir `--output` klasörüyle çalıştırıldığında `command.log` dosyasına kaydedilir.

Her komut, değiştirmeleri kendi başına çözer. `check-iocs`, saklanan sonuç dosyalarını o komut için kullanılabilir modüllerin slug'larıyla eşleştirir. Değiştirdiği modülden türeyen bir değiştirme, onun `check_indicators()` yöntemini devralır. Böylece platformu için `check-iocs`'un parçası olur ve kendi slug'ı adına sahip sonuç dosyasını yeniden kontrol eder. `check_indicators()` içermeyen bir değiştirme `check-iocs`'un parçası değildir. Değiştirdiği yerleşik modül, dosyayı yeniden kontrol eder. `check-iocs`, `--module` seçeneğini yalnızca sınıf adıyla eşleştirir. Oraya, farklı adlandırılmış bir değiştirmenin kendi adını verin; değiştirdiği modülün adını değil.

### MVT'den içe aktarma

İhtiyacınız olanı sağlıyorsa `mvt.plugin`'den içe aktarın. Dışa aktardığı adların çalışmaya devam etmesi en iyi çaba temelinde sağlanır. Bunlardan birinde yapılan değişiklik sürüm notlarında duyurulur. `mvt` içindeki diğer her şey de içe aktarılabilir; ancak sürümler arasında bildirim yapılmadan değişebilir. Eklenti arayüzü en iyi çaba esasına dayanır.

`mvt.plugin`, yukarıdaki temel sınıfları ve sonuç türleri olan `Command`, `Alert` ve `AlertLevel`'i, `DatabaseNotFoundError` ve `DatabaseCorruptedError`'u, zaman damgası dönüştürücülerini, [Eklenti Yapılandırması](plugin_configuration.md) ayarlar API'sini, MVT'nin kendi `settings`'ini, `get_plugin_logger()` ve `MVT_VERSION`'ı dışa aktarır. Listenin tamamı `src/mvt/plugin.py` dosyasındadır. `NETWORK_ACCESS_ALLOWED` ve `NETWORK_TIMEOUT` gibi değerler için MVT'nin `settings`'ini okuyun. Eklenti değerleri, eklentinin kendi ayar dosyasına yazılır.

## Kurulu modül paketleri {#installed-module-packages}

Python paketleri, `--load-module` veya `MVT_CUSTOM_MODULES` kullanmadan, modül çalıştıran her `check-*` komutunda otomatik olarak yüklenmeleri için modül kaydedebilir. Paketin `pyproject.toml` dosyasındaki `mvt.modules` grubuna bir giriş noktası kaydedin:

```toml
[project.entry-points."mvt.modules"]
mvt-plugin-example-org = "mvt_plugin_example_org:get_modules"
```

Giriş noktası, `MVTModule` alt sınıflarından oluşan bir iterable'a veya bunlardan birini döndüren bir çağrılabilire çözümlenmelidir:

```python
from mvt.plugin import MVTModule


class PackagedModule(MVTModule):
    supported_commands = (("ios", "check-backup"),)

    def run(self):
        self.results = [{"message": "packaged module ran"}]


def get_modules() -> list[type[MVTModule]]:
    return [PackagedModule]
```

`get_modules()`, paketin elle yazılmış modül listesidir. Modüllerini ayrı dosyalarda tutan bir paket, her sınıfı orada içe aktarır ve listeler. Listede yer almayan bir modül yüklenmez.

Kurulu modüller, diğer özel modüllerle aynı kurallara uyar: her modül `supported_commands` bildirmelidir ve bağımlılıklar standart sıralama mantığıyla çözülür. Bozuk bir giriş noktası bir uyarıyla atlanır ve MVT'nin çalışmasını engellemez. Özel komutlarda olduğu gibi, kurulu modül paketleri MVT süreci içinde güvenilir kod olarak çalışır; bu nedenle yalnızca güvendiğiniz kaynaklardan gelen paketleri kurun.

`pipx` ile yapılan bir MVT kurulumu için paketi MVT'nin ortamına enjekte edin:

```bash
pipx inject mvt mvt-plugin-example-org
```

API anahtarı gibi kendi ayarlarına ihtiyaç duyan modül paketleri, bunları MVT'nin kendi `config.yaml` dosyası yerine ad alanlı bir [eklenti yapılandırma dosyasında](plugin_configuration.md) saklamalıdır.

### Modül paketlerini adlandırma

Modül paketlerini `mvt-plugin-<name>` olarak adlandırın (içe aktarma paketi `mvt_plugin_<name>`) ve farklı gruplardan paketlerin çakışmaması için yayınlayan kuruluşun veya yazarın adını ekleyin: örneğin, bir kuruluşun özel modülleri, `mvt_plugin_example_org` içe aktarma paketiyle birlikte `mvt-plugin-example-org` olarak dağıtılır.

Bu ön ek, modül paketlerinin PyPI'da kolayca bulunmasını sağlar ve içe aktarma adlarının ilgisiz Python paketleriyle çakışmasını önler. Teknik bir gereklilik değil, bir gelenektir: paketin adı ne olursa olsun modüller `mvt.modules` giriş noktası üzerinden yüklenir ve başka adlara sahip mevcut paketler çalışmaya devam eder. Bu ön ekin aynı zamanda bir özgünlük işareti olmadığını unutmayın — herkes mevcut herhangi bir adla paket yayınlayabilir; bu nedenle, adı ne olursa olsun, kurmadan önce modül paketini ve yayıncısını inceleyin.

### Modül günlükleme

Modüller `self.log` üzerinden günlük kaydeder ve MVT, günlükleyiciyi modülün nereden geldiğine göre adlandırır. MVT'nin kendi modülleri, noktalı yolları altında günlük kaydeder (örneğin `mvt.ios.modules.mixed.whatsapp`). Harici olan her şey, yerleşik modüllerden görsel olarak ayrı tutulmak ve MVT'nin iç günlükleyici ağacından yalıtılmak için `mvt.ext` altında ad alanına alınır:

- Kurulu paketler, `mvt_plugin_` ön eki çıkarılarak `mvt.ext.<package>` altında günlük kaydeder: `mvt_plugin_example_org` içindeki modüller `mvt.ext.example_org.*` olarak günlük kaydeder.
- `--load-module` veya `MVT_CUSTOM_MODULES` ile yüklenen dosyalar, `mvt.ext.<file name>` olarak günlük kaydeder.

Bir modül sınıfının dışında, örneğin özel bir komut satırı işleyicisinde, `get_plugin_logger(__name__)` üzerinden günlük kaydedin. Bu, aynı ad alanında bir günlükleyici döndürür.

## Modülleri yerel olarak geliştirme {#developing-modules-locally}

Bir modül yazılırken onu kendi dosyasından yükleyin. `--load-module`, modül çalıştıran her komutta bir Python dosyası veya bunlardan oluşan bir klasör alır ve tekrarlanabilir:

```bash
mvt-ios check-backup --load-module ./example_module.py --output ./out ./backup
```

Bir klasör için MVT, gizli olmayan üst düzey `*.py` dosyalarını sıralı düzende yükler ve `__init__.py` dosyasını atlar. `MVT_CUSTOM_MODULES`, herhangi bir `--load-module` yolundan önce, modül çalıştıran her komutta yüklenecek bir klasörü belirtir:

```bash
MVT_CUSTOM_MODULES=./custom_modules mvt-android check-bugreport ./bugreport.zip
```

Bu şekilde yüklenen dosyalar, paketlenmiş modüllerle aynı kurallara uyar. `--list-modules`, bunları sürüm yerine dosyanın SHA-256 karmasıyla bildirir. Paketin düzenlenebilir kurulumu (`pip install -e .`) da çalışır: modüller giriş noktası üzerinden yüklenir ve `mvt plugins list`, paketi `local` kaynağıyla gösterir.

Yola göre yükleme geliştirme amaçlıdır. Bir modül çalışır hale geldikten sonra onu bir pakete taşıyın.

## Yüklenen modüllerin denetimi

Kurulu modül paketleri otomatik olarak yüklendiğinden MVT, her modülün nereden geldiğini kaydeder:

- `--list-modules`, kullanılabilir modülleri kaynağa göre gruplar: MVT'nin kendisi (sürümüyle), kurulu her paket (sürümüyle ve doğrudan bir depodan kurulduysa commit'iyle) ve `--load-module` veya `MVT_CUSTOM_MODULES` ile yüklenen her dosya (dosyanın SHA-256 karmasıyla).
- Bir komut `--output` klasörüyle çalıştırıldığında, `command.log` dosyası her modül kaynağı için, kaynağın sürümü veya karması ile ondan yüklenen modüllerin listesini içeren bir satır kaydeder.
- `mvt plugins list`, kurulu paketleri, her birinin nereden kurulduğunu ve kaç modül katkıda bulunduğunu listeler; bkz. [Eklentileri Yönetme](plugins.md).

## Profilleme

Bazı MVT modülleri, analiz sürecinde veya sonuçları bilinen göstergelerle kontrol ederken önemli miktarda veri çıkarır ve işler. Yeni modüller eklerken verimsiz kod yollarından kaçınmaya özen gösterilmelidir.

MVT modülleri, `MVT_PROFILE` ortam değişkeni ayarlanarak Python'un yerleşik `cProfile` aracıyla profillenebilir.

```bash
MVT_PROFILE=1 dev/mvt-ios check-backup test_backup
```

MVT ile bir cihazı analiz ederken önemli performans sorunlarıyla karşılaşıyorsanız bir issue açın veya PR gönderin.
