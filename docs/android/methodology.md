# Android Adli Bilişim Metodolojisi

Ne yazık ki Android cihazlar, iOS muadillerine kıyasla adli açıdan kullanışlı daha az sayıda eksiksiz veri kaynağı sunar. iOS'un aksine, Android yedekleme özelliği yalnızca sınırlı miktarda ilgili veri sağlar.

*bugreport dosyaları* gibi Android tanılama günlükleri, farklı Android sürümleri ve cihaz üreticileri arasında biçim ve yapı bakımından tutarsız olabilir. Mevcut sınırlı tanılama bilgisi, olası ele geçirilmelerin triyajını zorlaştırır ve bu nedenle `mvt-android` yetenekleri de sınırlıdır.

Ancak her şey kaybedilmiş değil.

## Android Cihazlarının AndroidQF ve MVT ile Kontrolü

[AndroidQF](https://github.com/mvt-project/androidqf) aracı; bir Android yedeği, bir bugreport dosyası ve bir dizi sistem günlüğü dahil olmak üzere bir Android cihazdan çok çeşitli adli artefaktlar toplamak için kullanılabilir. MVT, oluşturulan AndroidQF çıktısının cihaz ele geçirilmesi belirtileri açısından analiz edilmesini yerel olarak destekler.

### AndroidQF Neden Kullanılmalı?

- **Eksiksiz ve ham veri çıkarımı**
  AndroidQF, cihaz üzerinde çalışan bir adli toplama aracısı kullanarak tüm adli artefaktları toplar ve hiçbir kritik verinin gözden kaçmamasını sağlar. Veri toplama, cihazdaki shell ortamına veya cihazda bulunan yardımcı araçlara bağlı değildir.

- **Tutarlı ve standartlaştırılmış çıktı**
  AndroidQF, önceden tanımlanmış ve eksiksiz bir adli dosya kümesi toplayarak farklı Android cihazlar arasında veri ediniminde tutarlılık sağlar.

- **Geleceğe hazır analiz**
  Tam adli artefaktlar korunduğu için analistler, orijinal cihaza erişim gerekmeden yeni kanıtlar çıkarabilir veya güncellenmiş analiz tekniklerini uygulayabilir.

- **Bağımlılık gerektirmeyen platformlar arası araç**
  AndroidQF, cihaz sahibinin MVT veya bir Python ortamı kurmasına gerek kalmadan bir Android cihazdan uzaktan veri toplamak için kullanılabilen, tek başına çalışan bir Go ikili dosyasıdır.

### AndroidQF ile Android Adli Bilişim Analizi İş Akışı

AndroidQF ile analiz süreci, ayrı bir veri toplama ve veri analizi aşamasına bölünür.

1. **AndroidQF Kullanarak Veri Çıkarın**
  Tüm ilgili adli artefaktları Android cihazdan elde etmek için AndroidQF adli toplama aracını devreye alın.

2. **Çıkarılan Verileri MVT ile Analiz Edin**
  Çıkarılan artefaktlar üzerinde adli analiz gerçekleştirmek için `mvt-android check-androidqf` komutunu kullanın.

Artifakt toplamayı adli analizden ayıran bu yaklaşım, Android adli bilişim incelemeleri için daha güvenilir ve ölçeklenebilir bir metodoloji sağlar.

Daha fazla bilgi için [AndroidQF proje dokümantasyonuna](https://github.com/mvt-project/androidqf) bakın.

### VirusTotal paket sorgulamaları

AndroidQF, APK dosya karmalarını `packages.json` dosyasına kaydeder. MVT, bir AndroidQF edinimini kontrol ederken sistem dışı APK karmalarını isteğe bağlı olarak VirusTotal üzerinde sorgulayabilir:

```bash
MVT_VT_API_KEY=<key> mvt-android check-androidqf --virustotal /path/to/androidqf-output
```

`--virustotal` seçeneği varsayılan olarak devre dışıdır; çünkü APK karmalarını VirusTotal'a gönderir ve ağ erişimi gerektirir. Bu seçenek, `MVT_VT_API_KEY` ortam değişkeni aracılığıyla da sağlanabilen `VT_API_KEY` MVT yapılandırma değerini kullanır.

Ücretsiz VirusTotal API kotalarının tükenmesini önlemek için MVT, paket karma istekleri arasında varsayılan olarak 16 saniye bekler. Gecikmeyi değiştirmek için `--delay`, hız sınırlamayı devre dışı bırakmak için `--delay 0` kullanın:

```bash
mvt-android check-androidqf --virustotal --delay 30 /path/to/androidqf-output
```

## Android İzinsiz Giriş Logları

Kullanıcının Android'in [**Gelişmiş Koruma Modu**](https://support.google.com/android/answer/16339980) özelliğini etkinleştirdiği ve isteğe bağlı İzinsiz Giriş Logu özelliğini açtığı cihazlarda Android, yapılandırılmış *İzinsiz Giriş Logları*'ni şifrelenmiş bir biçimde oluşturabilir ve arşivleyebilir. Bu loglar; DNS sorgularını, dışa giden ağ bağlantılarını, süreç başlatmalarını, ADB etkinliğini ve diğer güvenlikle ilgili olayları kaydeder ve AndroidQF ediniminin geri kalanına yüksek doğruluklu bir tamamlayıcıdır. Günlükler cihaz üzerinde oluşturulur ve cihazla ilişkili Google hesabında saklanmadan önce şifrelenir. Şifreleme anahtarı, kullanıcının cihaz PIN'i tarafından korunur. İzinsiz giriş günlüğü verilerine Google erişemez.

AndroidQF, bir edinim kapsamında cihaz izinsiz giriş loglarını indirmesi, şifresini çözmesi ve toplaması için kullanıcıya istem gösterir. Bu loglar mevcut olduğunda `mvt-android check-androidqf`, diğer AndroidQF modüllerinin yanı sıra izinsiz giriş logu kontrollerini de otomatik olarak çalıştırır; ek bir komut gerekmez. Bu, MVT ile Android adli bilişim analizi için önerilen iş akışıdır.

İzinsiz giriş loglarının bir AndroidQF edinimi dışında toplandığı durumlarda, tek başına çalışan `mvt-android check-intrusion-logs` komutu bunları doğrudan analiz edebilir. Ayrıntılar için [Android İzinsiz Giriş Loglarınin Kontrolü](intrusion_logs.md) sayfasına, veri kaynağının arka planı için [Amnesty International Güvenlik Laboratuvarı'nın özellik duyurusuna](https://securitylab.amnesty.org/latest/2026/05/android-intrusion-logging-as-a-new-source-of-data-for-consensual-forensic-analysis/) bakın.

## Android Debug Bridge analizi kaldırıldı

Android cihazları doğrudan ADB üzerinden analiz etme yeteneği MVT'den kaldırılmıştır. ADB üzerinden doğrudan veri çıkarımı hataya açıktı ve sıklıkla ADB ile AndroidQF edinimleri arasında tutarsız veri toplanmasıyla sonuçlanıyordu. Cihaz edinimi için AndroidQF'yi, analiz için `mvt-android check-androidqf` komutunu kullanın.

## Android Yedeği Kontrolü (SMS mesajları)

Android yedekleri kullanımdan kaldırılıyor olsa da hâlâ bir yedek oluşturmak mümkündür. Ne yazık ki günümüzde uygulamalar tipik olarak yedeği bulut üzerinden almayı tercih ettiğinden, kullanılabilir veri miktarı sınırlıdır.

AndroidQF çıkarımı bir SMS yedeği içeriyorsa `mvt-android check-androidqf` komutu bir Android yedeğini ve SMS mesajlarını otomatik olarak kontrol eder.

`mvt-android check-backup` komutu da doğrudan bir Android yedek dosyasıyla kullanılabilir.
