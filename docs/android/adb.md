# ADB Analizi Kaldırıldı

MVT'de Android cihazların doğrudan [Android Debug Bridge (adb)](https://developer.android.com/studio/command-line/adb) üzerinden analiz edilmesi özelliği kaldırılmıştır.

Bir Android cihazdan adli artefakt toplamak için [AndroidQF](https://github.com/mvt-project/androidqf) kullanın, ardından toplanan çıktıyı şununla analiz edin:

```bash
mvt-android check-androidqf /path/to/androidqf-output
```

Bağımsız bir Android hata raporu (bug report) için şunu kullanın:

```bash
mvt-android check-bugreport /path/to/bugreport.zip
```

## Kaldırılma Nedenleri

1. **Cihazlar Arasında Tutarsız Veri Toplama**
   Android cihazlar sistem mimarisi, güvenlik politikası ve tanılama günlüğü kullanılabilirliği açısından önemli ölçüde farklılık gösterir. Bu durum, cihazlar arasında güvenilir adli veri toplamayı zorlaştırıyordu.

2. **Eksik Adli Veri Elde Etme**
   Doğrudan ADB analizi eksiksiz bir adli anlık görüntü elde edemiyordu. Tam hata raporları gibi kritik artefaktlar eksik olabiliyordu.

3. **Yinelenen Analiz Yolları**
   Benzer artefaktlar ayrı ADB, AndroidQF ve bugreport modülleri üzerinden ayrıştırılıyordu; bu da davranışı tutarlı tutmayı zorlaştırıyordu.

4. **İş Akışı Tutarlılığı**
   MVT artık doğrudan canlı cihazlarla etkileşim kurmak yerine, toplanan artefaktların analizine odaklanıyor.
