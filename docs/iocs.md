# İhlal Göstergeleri (IOC)

MVT, olası ihlal izlerini tespit etmek için [Structured Threat Information Expression (STIX)](https://oasis-open.github.io/cti-documentation/stix/intro.html) dosyalarını kullanır.

Bu ihlal göstergeleri, `.stix2` veya `.json` uzantılarına sahip, belirli bir [JSON](https://en.wikipedia.org/wiki/JSON) yapısı içeren bir dosyada yer alır.

iPhone yedeklerini veya dosya sistemi dökümlerini kontrol ederken bir STIX2 gösterge dosyasının yolunu belirtebilirsiniz. Örneğin:

```bash
mvt-ios check-backup --iocs ~/ios/malware.stix2 --output /path/to/iphone/output /path/to/backup
```

Veya bir Android yedeğinden alınan verilerle:

```bash
mvt-android check-backup --iocs ~/iocs/malware.stix2 /path/to/android/backup/
```

Bir cihazdan adli verileri çıkardıktan sonra, bunları belirttiğiniz herhangi bir STIX2 dosyasıyla karşılaştırabilirsiniz:

```bash
mvt-ios check-iocs --iocs ~/iocs/malware.stix2 /path/to/iphone/output/
```

`--iocs` seçeneği, MVT'nin aynı anda birden fazla STIX2 dosyasını içe aktarmasına olanak sağlamak için birden çok kez kullanılabilir. Örneğin:

```bash
mvt-ios check-backup --iocs ~/iocs/malware1.stix --iocs ~/iocs/malware2.stix2 /path/to/backup
```

STIX2 dosyalarını `MVT_STIX2` ortam değişkeni üzerinden otomatik olarak yüklemek de mümkündür:

```bash
export MVT_STIX2="/home/user/IOC1.stix2:/home/user/IOC2.stix2"
```

## Ağ Erişimi

URL göstergelerini kontrol ederken MVT, tanınan kısaltılmış URL'leri bir HTTP `HEAD` isteğiyle takip eder. URL kontrollerinin yinelenenleri ayıklanır ve eş zamanlı olarak yürütülür; aynı anda en fazla 20 istek ilerlemede olur. Tek bir URL zinciri içindeki yönlendirmeler ise yine sıralı olarak takip edilir. Bu istekleri aşağıdaki ortam değişkenleri kontrol eder:

- `MVT_NETWORK_ACCESS_ALLOWED` ağ isteklerini etkinleştirir veya devre dışı bırakır. Varsayılan değeri `true`'dur. MVT'nin kısaltılmış URL'leri çözümlemeye çalışmasını önlemek için `false` olarak ayarlayın.
- `MVT_NETWORK_TIMEOUT` istek zaman aşımını saniye cinsinden ayarlar. Varsayılan değeri `15`'tir.

Örneğin, kısaltılmış URL'leri çözümlemeden IOC kontrollerini çalıştırmak için:

```bash
MVT_NETWORK_ACCESS_ALLOWED=false mvt-ios check-iocs \
    --iocs ~/iocs/malware.stix2 /path/to/iphone/output/
```

## STIX2 Desteği

MVT şu ana kadar [STIX2 spesifikasyonlarının](https://docs.oasis-open.org/cti/stix/v2.1/csprd01/stix-v2.1-csprd01.html) yalnızca bir alt kümesini uygulamaktadır:

* Yalnızca tek bir değere yönelik kontrolleri destekler (örneğin `[domain-name:value='DOMAIN']`) ve birden fazla karşılaştırma içeren boolean ifadelerini desteklemez
* Yalnızca şu türleri destekler: `domain-name:value`, `process:name`, `email-addr:value`, `file:name`, `file:path`, `file:hashes.md5`, `file:hashes.sha1`, `file:hashes.sha256`, `app:id`, `configuration-profile:id`, `android-property:name`, `url:value` (ancak her tür, yalnızca elde edilen verinin türüyle ilgiliyse bir modül tarafından kontrol edilir)

## Bilinen STIX2 IOC depoları

- [Amnesty International soruşturmalar deposu](https://github.com/AmnestyTech/investigations), aşağıdakiler için STIX biçimli IOC'ler içerir:
    - [Pegasus](https://en.wikipedia.org/wiki/Pegasus_(spyware)) ([STIX2](https://raw.githubusercontent.com/AmnestyTech/investigations/master/2021-07-18_nso/pegasus.stix2))
    - [Cytrox'tan Predator](https://citizenlab.ca/2021/12/pegasus-vs-predator-dissidents-doubly-infected-iphone-reveals-cytrox-mercenary-spyware/) ([STIX2](https://raw.githubusercontent.com/AmnestyTech/investigations/master/2021-12-16_cytrox/cytrox.stix2))
    - [Paralı asker şirketiyle bağlantılı bir Android casus yazılım kampanyası](https://github.com/AmnestyTech/investigations/tree/master/2023-03-29_android_campaign) ([STIX2](https://github.com/AmnestyTech/investigations/blob/master/2023-03-29_android_campaign/malware.stix2))
- [Bu depo](https://github.com/Te-k/stalkerware-indicators), [MVT uyumlu bir STIX dosyası](https://raw.githubusercontent.com/Te-k/stalkerware-indicators/master/generated/stalkerware.stix2) dahil olmak üzere Android stalkerware (takip yazılımı) için IOC'ler içerir.
- Ayrıca kamuya açık casus yazılım kampanyalarından STIX biçimindeki [bir IOC listesini](https://github.com/mvt-project/mvt-indicators) sürdürüyoruz.

`mvt download-iocs` komutuyla en güncel genel gösterge dosyalarını otomatik olarak indirebilirsiniz. Platforma özel biçimler olan `mvt-ios download-iocs` ve `mvt-android download-iocs` da aynı şeyi yapar. Bu komutlar, gösterge listesini [mvt-indicators](https://github.com/mvt-project/mvt-indicators/blob/main/indicators.yaml) deposundan indirir ve [appdir](https://pypi.org/project/appdirs/) klasöründe saklar. Ardından MVT tarafından otomatik olarak yüklenirler.

STIX biçimli yeni IOC kaynakları önermek için lütfen [bir issue açın](https://github.com/mvt-project/mvt/issues/).

