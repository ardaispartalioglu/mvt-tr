# Android İzinsiz Giriş Logları

Android'in son sürümleri yapılandırılmış *İzinsiz Giriş Logları* üretebilir — platformun [SecurityLog API](https://developer.android.com/reference/android/app/admin/SecurityLog) üzerinden türetilen, satır sonuyla ayrılmış JSON kayıtları. İzinsiz Giriş Loglama, kullanıcıların cihazlarında katılabileceği Android'in **Gelişmiş Koruma Modu** (Advanced Protection Mode) altında yeni bir seçenek olarak sunulur; herhangi bir MDM veya cihaz ilkesi yapılandırması gerekmez. Etkinleştirildiğinde bu loglar; süreç başlatmaları, DNS sorguları, dışa giden ağ bağlantıları, ADB etkinliği, keyguard olayları ve diğer güvenlikle ilgili işlemlerin yüksek doğrulukta kaydını sunar. İlk İzinsiz Giriş Loglama özelliği, Mayıs 2026'da Android 16 için yayımlandı. Özelliğin ve desteklenen olayların zamanla genişletilmesi beklenmektedir.

Bu veri kaynağının nasıl tanıtıldığına ve neden adli açıdan değerli olduğuna ilişkin arka plan için Amnesty International Security Lab duyurusuna bakın: [Rıza temelli adli analiz için yeni bir veri kaynağı olarak Android İzinsiz Giriş Loglaması](https://securitylab.amnesty.org/latest/2026/05/android-intrusion-logging-as-a-new-source-of-data-for-consensual-forensic-analysis/).

## Önerilen iş akışı: AndroidQF ile toplama

[AndroidQF](https://github.com/mvt-project/androidqf), MVT ile analiz için bir Android cihazdan veri toplamanın önerilen yoludur. Toplama sırasında AndroidQF, kullanıcıdan cihazdaki izinsiz giriş loglarını da toplamasını ister ve bunları toplama çıktısının `intrusion-logs/` alt dizinine yazar.

Böyle bir toplamayı `mvt-android check-androidqf` ile analiz ettiğinizde, MVT `intrusion-logs/` dizinini otomatik olarak algılar ve aşağıda açıklanan izinsiz giriş logu kontrollerini çalıştırır — ayrı bir komut çağırmanıza gerek yoktur:

```bash
mvt-android check-androidqf --output /path/to/results/ /path/to/androidqf-output/
```

Cihazın saat dilimi AndroidQF toplamasından (`getprop.txt`) okunur ve olay zaman damgalarına otomatik olarak uygulanır.

## Bağımsız komut: `check-intrusion-logs`

`mvt-android check-intrusion-logs` komutu, izinsiz giriş logu analizini doğrudan bir log dosyası kümesi üzerinde çalıştırır. Yukarıdaki AndroidQF iş akışını tercih edin; bağımsız komutu, izinsiz giriş logları bir AndroidQF toplamasının dışında toplandığında veya yalnızca bir izinsiz giriş logu kümesi yeniden analiz edileceğinde kullanın.

## Beklenen girdi

`check-intrusion-logs` şunlardan birini kabul eder:

- bir veya daha fazla `.txt` dosyası içeren bir **dizin** (özyineli olarak) veya
- bu tür `.txt` dosyaları içeren bir **`.zip` arşivi** (iç içe `.zip` arşivleri de taranır).

Her `.txt` dosyasının, her satırda bir JSON nesnesi olacak şekilde satır sonuyla ayrılmış JSON içermesi beklenir. Her nesne, türünü belirten bir üst düzey anahtar altında tek bir olayı sarar; örneğin:

```json
{"dns_event": {"event_time": 1746979200000, "hostname": "example.com", "ip_addresses": ["93.184.216.34"], "package_name": "com.example.app"}}
{"connect_event": {"event_time": 1746979201000, "ip_address": "93.184.216.34", "port": 443, "package_name": "com.example.app"}}
{"security_event": {"event_time": 1746979202000, "tag": 210005, "data": ["..."]}}
```

Birden çok örtüşen log dosyasında (örneğin log rotasyonları) görünen aynı olaylar, ilk görülme esasına göre tekilleştirilir.

## Analizi çalıştırma

```bash
mvt-android check-intrusion-logs --output /path/to/results/ /path/to/intrusion-logs/
```

Dizin yerine doğrudan bir `.zip` arşivi de verilebilir:

```bash
mvt-android check-intrusion-logs --output /path/to/results/ /path/to/intrusion-logs.zip
```

### Seçenekler

| Seçenek | Açıklama |
| --- | --- |
| `-i, --iocs PATH` | Bir STIX2 gösterge dosyasının yolu. Birden çok kez verilebilir. |
| `-o, --output PATH` | JSON sonuçlarının ve zaman çizelgesi CSV dosyasının yazılacağı dizin. |
| `-l, --list-modules` | Kullanılabilir izinsiz giriş logu modüllerini listeler ve çıkar. |
| `-m, --module NAME` | Tümü yerine tek bir modül çalıştırır (örneğin `DnsEvent`). |
| `-t, --timezone TZ` | Cihaz için IANA saat dilimi adı (örneğin `Europe/Paris`). Ayarlandığında olay zaman damgaları UTC yerine cihazın yerel saatine dönüştürülür. |
| `-v, --verbose` | Ayrıntılı loglama. Uyumluluk için korunmuştur ve gelecekteki bir sürümde kaldırılacaktır: bunun yerine `--verbose` seçeneğini doğrudan `mvt-android`'e verin. |

## Modüller

Komut, ayrıştırılan olaylar üzerinde aşağıdaki modülleri çalıştırır:

- **`DnsEvent`** — DNS çözümleme olayları. Ana bilgisayar adları ve çözümlenen IP adresleri alan adı göstergelerine karşı, isteği yapan `package_name` ise uygulama tanımlayıcı göstergelerine karşı kontrol edilir.
- **`ConnectEvent`** — Dışa giden ağ bağlantısı olayları. Hedef IP'ler (localhost adresleri atlanarak) alan adı göstergelerine karşı ve `package_name` uygulama tanımlayıcı göstergelerine karşı kontrol edilir.
- **`SecurityEvent`** — Android `SecurityLog` etiket kimliklerine göre anahtarlanmış güvenlik logu olayları (örneğin `app_process_start`, `adb_shell_cmd`, `keyguard_dismissed`, `os_startup`, `cert_*` olayları). Bunlar, şüphelenilen olaylar çevresindeki cihaz etkinliğini yeniden oluşturmaya yardımcı olmak için zaman çizelgesinde gösterilir.

Her üç modül de girdi üzerinde tek bir ön ayrıştırma geçişini paylaşır; böylece gelecekte daha fazla modül eklenmesi G/Ç maliyetini artırmaz. İzinsiz Giriş Loglama özelliği tarafından üretilen yeni olay türlerini desteklemek için gelecekte ek modüller eklenecektir.

## Sonuçları yorumlama

Başarılı bir IOC eşleşmesi; eşleşen göstergeyi, söz konusu olayı ve olayın zaman damgasını içeren bir `CRITICAL` uyarısı oluşturur. Uyarılar çalıştırmanın sonunda özetlenir ve modül başına JSON sonuçlarının yanında kalıcı olarak saklanır.

`--timezone` verildiğinde, zaman çizelgesindeki ve JSON çıktısındaki zaman damgaları cihazın yerel duvar saati zamanını yansıtır. Aksi durumda zaman damgaları, MVT'nin geri kalanıyla tutarlı olarak UTC cinsindendir.

## Sınırlamalar

- Bu sayfa, izinsiz giriş loglarının cihazdan zaten toplandığını varsayar. Önerilen toplama yolu AndroidQF üzerindendir (yukarıya bakın); izinsiz giriş loglamanın kendisinin daha önceden cihazda, Android'in Gelişmiş Koruma moduna katılarak ve isteğe bağlı İzinsiz Giriş Loglama özelliği de etkinleştirilerek açılmış olması gerekir (ayrıntılar için [Amnesty blog yazısına](https://securitylab.amnesty.org/latest/2026/05/android-intrusion-logging-as-a-new-source-of-data-for-consensual-forensic-analysis/) bakın).
- Tüm IOC tabanlı analizlerde olduğu gibi, yalnızca herkese açık göstergeler bir cihazın ihlal edilmediği sonucuna varmak için yeterli değildir. Bağlam için [İhlal Göstergeleri](../iocs.md) sayfasına bakın.
