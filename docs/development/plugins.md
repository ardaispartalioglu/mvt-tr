# Eklentileri Yönetme

Eklenti paketleri MVT'yi ek [adli modüller](index.md#custom-modules) ve
[CLI komutları](custom_commands.md) ile genişletir. Kurulu paketler otomatik
olarak yüklendiğinden, `mvt plugins` neyin kurulu olduğunu denetler ve
güncellemelerin mevcut olup olmadığını kontrol eder. Komut yalnızca `mvt`
üzerinde bulunur; ancak listelediği paketler `mvt-ios` ve `mvt-android`'i de
genişletir.

## Kurulu eklentileri listeleme

```bash
mvt plugins list
```

```
                        Installed MVT plugins
┏━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━┳━━━━━━━━━━━┓
┃ Name                ┃ Version ┃ Origin       ┃ Modules ┃ Commands  ┃
┡━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━╇━━━━━━━━━━━┩
│ mvt-plugin-example  │ 1.2.0   │ pypi         │       4 │ summarize │
│ mvt-plugin-research │ 0.1.0   │ git+3f9a1c7d │       2 │ -         │
│ mvt-plugin-local    │ 0.0.1   │ local        │       1 │ triage    │
└─────────────────────┴─────────┴──────────────┴─────────┴───────────┘
```

Origin, her paketin nereden kurulduğunu kaydeder: bir paket dizininden
kurulan bir paket için `pypi`, doğrudan bir depodan kurulan bir paket için
`git+<commit>`, bir dizinden değil yerel bir klasörden veya arşivden kurulan
bir paket için (düzenlenebilir bir geliştirme kurulumu dahil) `local`. Son
iki sütun, paketin kaç adli modül katkıda bulunduğunu ve hangi CLI
komutlarını eklediğini gösterir.

Modülleri içe aktarılamayan bir eklenti, listelemeyi bozmak yerine `Modules`
sütununda `error` ile listelenir.

## Güncellemeleri kontrol etme

```bash
mvt plugins check-updates
```

```
Plugin updates available:
  mvt-plugin-example  1.2.0 → 1.3.0
    Upgrade with: pip install -U mvt-plugin-example

MVT does not install plugin updates. Run the command above when you decide to
upgrade.
```

Bir paket dizininden kurulan paketler, kendileri için yayımlanan en son
sürümle karşılaştırılır. Hiç yayımlanmamış bir paket, örneğin yalnızca bir
kuruluş içinde dağıtılan bir eklenti, sessizce atlanır.

!!! note

    `pypi` origin'i ile gösterilen paketler, hangi dizinden kurulmuş
    olurlarsa olsunlar [PyPI](https://pypi.org) ile karşılaştırılır. Bu
    nedenle, adı PyPI'da da bulunan bir ad altında özel bir dizinden kurulan
    bir eklenti, o adı taşıyan ilgisiz genel paketle karşılaştırılır. Özel
    bir dizinde yayımlanan eklentilere PyPI'da kullanılmayan bir ad verin ve
    beklenmedik bir güncelleme önerisini, paketin nereden geleceğini kontrol
    etmek için bir neden olarak değerlendirin.

!!! warning

    MVT bir eklentiyi asla kendisi kurmaz veya yükseltmez; yalnızca bunu
    yapan komutu yazdırır. Bir soruşturmanın ortasında bir eklentiyi
    yükseltmek, sonuçları üreten modülleri değiştirir ve bir eklenti MVT
    sürecinin içinde güvenilir kod olarak çalışır; dolayısıyla yeni bir
    sürümü çekmek, bir kontrolün çalıştırılmasının yan etkisi değil,
    analistin bilinçli olarak vermesi gereken bir karardır.

## Otomatik güncelleme kontrolleri

MVT ayrıca, bir komut başladığında yazdırılan banner'da mevcut eklenti
güncellemelerini bildirir:

```
        MVT - Mobile Verification Toolkit

        https://mvt.re
        Version: 2026.7.29

        Plugin updates available:
          mvt-plugin-example  1.2.0 → 1.3.0   (pip install -U mvt-plugin-example)
```

Bu kontrol en fazla 12 saatte bir çalışır. Kontroller arasında MVT, hiçbir
bağlantı kurmadan en son kontrolün bulgularını yazdırır; böylece bir eklenti
güncellemesi, her komutta yeniden sorgulama yapılmadan görünür kalır.
`mvt plugins check-updates` komutu ise son kontrolün ne zaman yapıldığından
bağımsız olarak hemen kontrol eder.

Otomatik kontrol; `--disable-update-check` seçeneği kullanıldığında, MVT
yapılandırmasında `NETWORK_ACCESS_ALLOWED` devre dışı bırakıldığında ve
hiçbir eklenti kurulu olmadığında atlanır.

## Depodan kurulan eklentiler

`pip install "mvt-plugin-example @ git+<url>"` ile kurulan bir eklenti,
kurulu revizyonun artık hangi commit'e işaret ettiği uzak depoya sorularak
kontrol edilir. MVT, git ve ssh'i toplu (batch) modda çalıştırır; bu nedenle
MVT'de zaten bulunmayan kimlik bilgilerini gerektiren bir depo, bunları
sormak yerine kontrolü başarısız kılar. Kontrol; git mevcut olmadığında,
depoya ulaşılamadığında ve depoya erişim reddedildiğinde sessizce atlanır.

Eklentinin nasıl kurulduğu, bir güncellemenin ne anlama geldiğini belirler:

- Bir daldan kurulan bir eklenti, dal kurulu commit'in ötesine geçtiğinde
  güncel olmadığı bildirilir.
- Belirli bir commit'ten veya bir etiketten (tag) kurulan bir eklenti
  sabitlenmiştir. Geldiği dal ne kadar ilerlerse ilerlesin, güncel olmadığı
  asla bildirilmez.

Bu nedenle bir eklentiyi bir commit'e veya bir etikete sabitlemek, bir
soruşturma boyunca kullanılan modülleri kararlı tutmanın yoludur.
