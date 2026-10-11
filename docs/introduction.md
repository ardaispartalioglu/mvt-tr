# Giriş

Mobile Verification Toolkit (MVT), iOS ve Android cihazlarda herhangi bir ihlal belirtisi olup olmadığını tespit etmek amacıyla bu cihazların rıza temelli adli edinimini kolaylaştırmak için tasarlanmış bir araç setidir. MVT'nin yetenekleri sürekli gelişmektedir; ancak başlıca özelliklerinden bazıları şunlardır:

- Şifrelenmiş iOS yedeklerinin şifresini çözme.
- Çok sayıda iOS sistem ve uygulama veri tabanından, günlükten (log) ve sistem analitiğinden kayıtları işleme ve ayrıştırma.
- Android cihazlardan kurulu uygulamaları çıkarma.
- adb protokolü üzerinden Android cihazlardan tanılama bilgileri çıkarma.
- Çıkarılan kayıtları, STIX2 biçiminde sağlanan bir kötü amaçlı gösterge listesiyle karşılaştırma.
- Çıkarılan kayıtların JSON günlüklerini ve tespit edilen tüm kötü amaçlı izlerin ayrı JSON günlüklerini oluşturma.
- Çıkarılan kayıtların birleşik bir kronolojik zaman çizelgesini ve tespit edilen tüm kötü amaçlı izlerin bir zaman çizelgesini oluşturma.

MVT, teknoloji uzmanları ve araştırmacılar için tasarlanmış bir adli araştırma aracıdır. Kullanımı, adli analizin temellerini anlamayı ve komut satırı araçlarını kullanmayı gerektirir. MVT, son kullanıcının kendi kendini değerlendirmesi için tasarlanmamıştır. Cihazınızın güvenliği konusunda endişe duyuyorsanız lütfen bir uzmandan yardım alın.

## İhlal Göstergeleri

MVT, mobil cihazlarda bilinen casus yazılım kampanyaları tarafından hedeflenme veya bulaşmanın olası izlerini taramak için [ihlal göstergelerinin (IOC)](https://github.com/mvt-project/mvt-indicators) kullanılmasını destekler. Buna [Amnesty International](https://github.com/AmnestyTech/investigations/) ve diğer araştırma grupları tarafından yayımlanan IOC'ler dahildir.

!!! warning
    Kamuya açık ihlal göstergeleri, bir cihazın "temiz" olduğunu ve belirli bir casus yazılım aracıyla hedeflenmediğini tespit etmek için yetersizdir. Yalnızca kamuya açık göstergelere dayanmak, güncel adli izleri kaçırabilir ve yanlış bir güvenlik hissine yol açabilir.

    Güvenilir ve kapsamlı dijital adli destek ve triyaj, kamuya açık olmayan göstergelere, araştırmalara ve tehdit istihbaratına erişim gerektirir.

    Bu tür destek, sivil toplum için [Amnesty International'ın Security Lab](https://securitylab.amnesty.org/get-help/?c=mvt_docs) veya [Access Now'ın Digital Security Helpline](https://www.accessnow.org/help/) hizmetleri aracılığıyla mevcuttur.

MVT ile ihlal göstergelerinin kullanımı hakkında daha fazla bilgi [dokümantasyonda](iocs.md) mevcuttur.


## Rıza Temelli Adli Analiz {#consensual-forensics}

MVT, bir cep telefonunda tipik olarak bulunan çok kişisel nitelikte çeşitli kayıt türlerini (arama geçmişi, SMS ve WhatsApp mesajları vb. gibi) çıkarabilme ve işleyebilme yeteneğine sahip olsa da, bu özellik kötü amaçlı SMS mesajlarının sömürüye (exploitation) yol açması gibi olası saldırı vektörlerinin belirlenmesine yardımcı olmak içindir.

MVT'nin amacı, rıza göstermeyen kişilerin cihazlarında karşı taraf adli analizi (adversarial forensics) yapılmasını kolaylaştırmak değildir. MVT'nin ve türev ürünlerinin; işleme rıza göstermeyen kişiler tarafından kullanılan cihazlardan veri çıkarmak ve/veya bu verileri analiz etmek amacıyla kullanılması [lisans](license.md) kapsamında açıkça yasaklanmıştır.
