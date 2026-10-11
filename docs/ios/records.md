# mvt-ios ile Çıkarılan Kayıtlar

Bu sayfada, günlüklerin, yedeklerin veya dosya sistemi dökümlerinin analizi gerçekleştirilirken MVT tarafından oluşturulan dosyaların (makul ölçüde) güncel bir dökümünü bulabilirsiniz.

## `check-fs` veya `check-backup` ile Çıkarılan Kayıtlar

### `analytics.json`

!!! info "Kullanılabilirlik"
    Yedek (şifrelenmişse): :material-close:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `Analytics` modülü tarafından oluşturulur. Modül, ağ iletişimi, sertifika sabitleme, TLS vb. hatalara ilişkin çeşitli analiz bilgileri içeren *private/var/Keychains/Analytics/\*.db* konumundaki SQLite veritabanlarının içindeki plist dosyalarından kayıtları çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, süreçler ve alan adları plist dosyasının tüm alanlarına karşı kontrol edilir. Eşleşmeler *analytics_detected.json* dosyasında saklanır.

---

### `applications.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `Applications` modülü tarafından oluşturulur. Modül, cihazda yüklü uygulamaların listesini yedekteki `Info.plist` dosyasından veya bir dosya sistemi dökümündeki `iTunesMetadata.plist` dosyalarından çıkarır. Bu kayıtlar, uygulamanın kaynağına ve yüklenmesine ilişkin ayrıntılı bilgiler içerir.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, süreçler ve uygulama kimlikleri her uygulamanın adına karşı kontrol edilir. Ayrıca AppStore'dan yüklenmemiş tüm uygulamalar işaretlenir. Eşleşmeler *applications_detected.json* dosyasında saklanır.

---

### `backup_info.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-close:

Bu JSON dosyası, mvt-ios'un `BackupInfo` modülü tarafından oluşturulur. Modül; ad, telefon numarası, IMEI, product type ve sürüm gibi yedeğe ve cihaza ilişkin bazı ayrıntıları çıkarır.

---

### `cache_files.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-close:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `CacheFiles` modülü tarafından oluşturulur. Modül, diskte *Cache.db* adıyla saklanan tüm SQLite veritabanı dosyalarından kayıtları çıkarır. Bu veritabanları tipik olarak iOS'un [dahili URL önbelleklemesine](https://developer.apple.com/documentation/foundation/nsurlcache) ait verileri içerir. Bu modül aracılığıyla, aksi hâlde erişilemeyecek olan, uygulamalar ve sistem servisleri tarafından gerçekleştirilen HTTP isteklerine ve yanıtlarına ilişkin kayıtları kurtarabilirsiniz. Örneğin, bir iOS servisinin ilk aşama kötü amaçlı yükü indirmeye çalışırken gerçekleştirdiği bir sömürü zincirinin parçası olan HTTP isteklerini görebilirsiniz.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar istenen URL'ye karşı kontrol edilir. Eşleşmeler *cache_files_detected.json* dosyasında saklanır.

---

### `calendar.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `Calendar` modülü tarafından oluşturulur. Bu modül, `Calendar.sqlitedb` veritabanındaki tüm CalendarItems öğelerini çıkarır. Bu veritabanı, telefonda yüklü farklı takvimlerdeki tüm takvim girdilerini içerir.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, e-posta adresleri farklı etkinlikleri davet edenin e-posta adresine karşı kontrol edilir. Eşleşmeler *calendar_detected.json* dosyasında saklanır.

---

### `calls.json`

!!! info "Kullanılabilirlik"
    Yedek (şifrelenmişse): :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `Calls` modülü tarafından oluşturulur. Modül, WhatsApp veya Skype gibi mesajlaşma uygulamalarından yapılanlar da dahil olmak üzere gelen ve giden aramaların kayıtlarını içeren */private/var/mobile/Library/CallHistoryDB/CallHistory.storedata* konumundaki bir SQLite veritabanından kayıtları çıkarır.

---

### `chrome_favicon.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `ChromeFavicon` modülü tarafından oluşturulur. Modül, favicon URL'leri ile bunları yükleyen ziyaret edilmiş URL'ler arasındaki eşlemeyi içeren */private/var/mobile/Containers/Data/Application/\*/Library/Application Support/Google/Chrome/Default/Favicons* konumundaki bir SQLite veritabanından kayıtları çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar hem favicon URL'sine hem de ziyaret edilen URL'ye karşı kontrol edilir. Eşleşmeler *chrome_favicon_detected.json* dosyasında saklanır.

---

### `chrome_history.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `ChromeHistory` modülü tarafından oluşturulur. Modül, URL ziyaretlerinin geçmişini içeren */private/var/mobile/Containers/Data/Application/\*/Library/Application Support/Google/Chrome/Default/History* konumundaki bir SQLite veritabanından kayıtları çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar ziyaret edilen URL'ye karşı kontrol edilir. Eşleşmeler *chrome_history_detected.json* dosyasında saklanır.

---

### `configuration_profiles.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-close:

Bu JSON dosyası, mvt-ios'un `ConfigurationProfiles` modülü tarafından oluşturulur. Modül, cihaza yüklenmiş iOS yapılandırma profillerine ilişkin ayrıntıları çıkarır. Bunlar hem varsayılan iOS profillerini hem de üçüncü taraf profillerini içermelidir.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bilinen kötü amaçlı profilleri belirlemek amacıyla yapılandırma profili UUID'sine karşı kontrol edilir. Eşleşmeler *configuration_profiles_detected.json* dosyasında saklanır.

---

### `contacts.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `Contacts` modülü tarafından oluşturulur. Modül, telefonun adres defterinin kayıtlarını içeren */private/var/mobile/Library/AddressBook/AddressBook.sqlitedb* konumundaki bir SQLite veritabanından kayıtları çıkarır. Bu veritabanı elbette tek başına herhangi bir kötü amaçlı gösterge içermez; ancak diğer uygulamalardan (iMessage, SMS vb.) gelen kayıtları karşılaştırmak ve bilinmeyen kaynaklardan gelenleri filtrelemek için kullanmak isteyebilirsiniz.

---

### `firefox_favicon.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `FirefoxFavicon` modülü tarafından oluşturulur. Modül, favicon URL'leri ile bunları yükleyen ziyaret edilmiş URL'ler arasındaki eşlemeyi içeren */private/var/mobile/profile.profile/browser.db* konumundaki bir SQLite veritabanından kayıtları çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar hem favicon URL'sine hem de ziyaret edilen URL'ye karşı kontrol edilir. Eşleşmeler *firefox_favicon_detected.json* dosyasında saklanır.

---

### `firefox_history.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `FirefoxHistory` modülü tarafından oluşturulur. Modül, URL ziyaretlerinin geçmişini içeren */private/var/mobile/profile.profile/browser.db* konumundaki bir SQLite veritabanından kayıtları çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar ziyaret edilen URL'ye karşı kontrol edilir. Eşleşmeler *firefox_history_detected.json* dosyasında saklanır.

---

### `global_preferences.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `GlobalPreferences` modülü tarafından oluşturulur. Modül, Lockdown Mode'un etkin olup olmadığı dahil olmak üzere sistem tercihlerini içeren */private/var/mobile/Library/Preferences/.GlobalPreferences.plist* konumundaki bir Plist dosyasından kayıtları çıkarır.

---

### `id_status_cache.json`

!!! info "Kullanılabilirlik"
    Yedek (iOS 14.7'den önce): :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `IDStatusCache` modülü tarafından oluşturulur. Modül, Apple kullanıcı kimliği doğrulamasının önbelleğini içeren */private/var/mobile/Library/Preferences/com.apple.identityservices.idstatuscache.plist* konumundaki bir plist dosyasından kayıtları çıkarır. Bu önbellek, Facetime ve iMessage gibi uygulamaların diğer kayıtlı Apple kimlikleriyle ilk kez ne zaman iletişim kurduğunu gösterir. Bu önemlidir; çünkü bu uygulamaların sömürülmesinde rol oynayan kötü niyetli hesapların izlerini içerebilir.

iOS 14.7.0'dan itibaren bu dosya boş ya da yoktur.

---

### `shortcuts.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `Shortcuts` modülü tarafından oluşturulur. Modül, Shortcuts uygulamasına ilişkin kayıtları içeren */private/var/mobile/Library/Shortcuts/Shortcuts.sqlite* konumundaki bir SQLite veritabanından kayıtları çıkarır. Shortcuts, kullanıcıların cihazlarında belirli eylemleri otomatikleştirmesine olanak tanıyan yerleşik bir iOS özelliğidir. Bazı durumlarda meşru Shortcuts uygulaması, casus yazılımlar tarafından enfekte edilmiş cihazlarda kalıcılık sağlamak amacıyla kötüye kullanılabilir.

---

### `interaction_c.json`

!!! info "Kullanılabilirlik"
    Yedek (şifrelenmişse): :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `InteractionC` modülü tarafından oluşturulur. Modül, kullanıcının yüklü uygulamalarla etkileşimlerine ilişkin ayrıntıları içeren */private/var/mobile/Library/CoreDuet/People/interactionC.db* konumundaki bir SQLite veritabanından kayıtları çıkarır.

---

### `locationd_clients.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `LocationdClients` modülü tarafından oluşturulur. Modül, konum servislerine erişim talep eden uygulamaların önbelleğini içeren */private/var/mobile/Library/Caches/locationd/clients.plist* konumundaki bir plist dosyasından kayıtları çıkarır.

---

### `manifest.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-close:

Bu JSON dosyası, mvt-ios'un `Manifest` modülü tarafından oluşturulur. Modül, iTunes yedeklerinde bulunan ve yerel olarak yedeklenen dosyaları iOS cihazındaki orijinal yollarla eşleştiren *Manifest.db* SQLite veritabanından kayıtları çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar orijinal göreli yola karşı kontrol edilir. Bazı durumlarda, örneğin tarayıcı önbellek klasörlerinde olduğu gibi, adında alan adı bulunan dosya kayıtları olabilir. Eşleşmeler *manifest_detected.json* dosyasında saklanır.

Eksik bir yedek, manifest dosyasında yine de edinemediği dosyaları listeler. Bu kayıtlar bir `"missing": true` alanı taşır ve modül bunlardan kaç tanesinin bulunduğunu bildirir. Bunu, bir artefaktın edinilememesi nedeniyle hiçbir sonuç döndürmeyen bir modülü, söz konusu artefakta hiç sahip olmamış bir cihazda hiçbir şey bulamayan bir modülden ayırt etmek için kullanın.

Dosyalar, yedek klasörü içinde beklenen yollarında saklanmalıdır. Modül, yedek dosyalarını listelemeyi tamamlayamazsa bir uyarı kaydeder ve eksik dosya kontrolünü atlar; manifest üst verileri yine de çıkarılır.

---

### `os_analytics_ad_daily.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `OSAnalyticsADDaily` modülü tarafından oluşturulur. Modül, sistemde çalışan süreçlerin veri kullanım geçmişini içeren *private/var/mobile/Library/Preferences/com.apple.osanalytics.addaily.plist* konumundaki bir plist dosyasından kayıtları çıkarır. Ağ istatistiklerinin yanı sıra bu kayıtlar özellikle önemlidir; çünkü kötü amaçlı süreç yürütmelerine ve ilgili zaman aralığına dair izler gösterebilir.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, süreç adlarına karşı kontrol edilir. Eşleşmeler *os_analytics_ad_daily_detected.json* dosyasında saklanır.

---

### `datausage.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `Datausage` modülü tarafından oluşturulur. Modül, sistemde çalışan süreçlerin ağ verisi kullanım geçmişini içeren */private/var/wireless/Library/Databases/DataUsage.sqlite* konumundaki bir SQLite veritabanından kayıtları çıkarır. WiFi üzerinden ağ trafiğini kaydetmez (`WIFI_IN` ve `WIFI_OUT` alanları her zaman boştur) ve `WWAN_IN` ile `WWAN_OUT` alanları bayt cinsinden saklanır. Ağ istatistiklerinin yanı sıra bu kayıtlar özellikle önemlidir; çünkü kötü amaçlı süreç yürütmelerine ve ilgili zaman aralığına dair izler gösterebilir. Özellikle geçerli bir bundle ID'ye sahip olmayan süreçler özel dikkat gerektirebilir.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, süreç adlarına karşı kontrol edilir. Eşleşmeler *datausage_detected.json* dosyasında saklanır. Tam bir dosya sistemi dökümü üzerinde çalışılıyorsa ve komut satırından `--fast` bayrağı etkinleştirilmemişse, mvt-ios şüpheli görünen süreçleri vurgular ve dökümde aynı ada sahip bir ikili dosyanın bulunup bulunmadığını kontrol eder.

---

### `netusage.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-close:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `Netusage` modülü tarafından oluşturulur. Modül, sistemde çalışan süreçlerin veri kullanım geçmişini içeren */private/var/networkd/netusage.sqlite* konumundaki bir SQLite veritabanından kayıtları çıkarır. Ağ istatistiklerinin yanı sıra bu kayıtlar özellikle önemlidir; çünkü kötü amaçlı süreç yürütmelerine ve ilgili zaman aralığına dair izler gösterebilir. Özellikle geçerli bir bundle ID'ye sahip olmayan süreçler özel dikkat gerektirebilir.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, süreç adlarına karşı kontrol edilir. Eşleşmeler *netusage_detected.json* dosyasında saklanır. Tam bir dosya sistemi dökümü üzerinde çalışılıyorsa ve komut satırından `--fast` bayrağı etkinleştirilmemişse, mvt-ios şüpheli görünen süreçleri vurgular ve dökümde aynı ada sahip bir ikili dosyanın bulunup bulunmadığını kontrol eder.

---

### `profile_events.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-close:

Bu JSON dosyası, mvt-ios'un `ProfileEvents` modülü tarafından oluşturulur. Modül, yapılandırma profili işlemlerine ilişkin bir zaman çizelgesi çıkarır. Örneğin, Ayarlar uygulamasından yeni bir profilin ne zaman yüklendiğini veya bir profilin ne zaman kaldırıldığını göstermelidir.

---

### `safari_browser_state.json`

!!! info "Kullanılabilirlik"
    Yedek (şifrelenmişse): :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `SafariBrowserState` modülü tarafından oluşturulur. Modül, açılan sekmelerin kayıtlarını içeren */private/var/mobile/Library/Safari/BrowserState.db* veya */private/var/mobile/Containers/Data/Application/\*/Library/Safari/BrowserState.db* konumundaki SQLite veritabanlarından kayıtları çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar ziyaret edilen URL'ye karşı kontrol edilir. Eşleşmeler *safari_browser_state_detected.json* dosyasında saklanır.

---

### `safari_favicon.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-close:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `SafariFavicon` modülü tarafından oluşturulur. Modül, favicon URL'leri ile bunları yükleyen ziyaret edilmiş URL'ler arasındaki eşlemeleri içeren */private/var/mobile/Library/Image Cache/Favicons/Favicons.db* veya */private/var/mobile/Containers/Data/Application/\*/Library/Image Cache/Favicons/Favicons.db* konumundaki SQLite veritabanlarından kayıtları çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar hem favicon URL'sine hem de ziyaret edilen URL'ye karşı kontrol edilir. Eşleşmeler *safari_favicon_detected.json* dosyasında saklanır.

---

### `safari_history.json`

!!! info "Kullanılabilirlik"
    Yedek (şifrelenmişse): :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `SafariHistory` modülü tarafından oluşturulur. Modül, URL ziyaretlerinin geçmişini içeren */private/var/mobile/Library/Safari/History.db* veya */private/var/mobile/Containers/Data/Application/\*/Library/Safari/History.db* konumundaki SQLite veritabanlarından kayıtları çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar ziyaret edilen URL'ye karşı kontrol edilir. Eşleşmeler *safari_history_detected.json* dosyasında saklanır.

---

### `shutdown_log.json`

!!! info "Kullanılabilirlik"
    Yedek (şifrelenmişse): :material-close:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `ShutdownLog` modülü tarafından oluşturulur. Modül, *private/var/db/diagnostics/shutdown.log* konumundaki kapanma günlüğünden kayıtları çıkarır. Bir iPhone kapatılırken çalışan tüm süreçlere bir SIGTERM gönderilir. `shutdown.log` dosyası, SIGTERM gönderildikten sonra kapanmayan herhangi bir süreci (pid'i ve yoluyla birlikte) kaydeder.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar yollara karşı kontrol edilir. Eşleşmeler *shutdown_log_detected.json* dosyasında saklanır.

---

### `sms.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `SMS` modülü tarafından oluşturulur. Modül, */private/var/mobile/Library/SMS/sms.db* konumundaki SQLite veritabanından bir SMS mesajları listesi çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar çıkarılan HTTP bağlantılarına karşı kontrol edilir. Eşleşmeler *sms_detected.json* dosyasında saklanır.

---

### `urls.json`

Bu JSON dosyası, SMS, iMessage ve WhatsApp mesajlarından çıkarılan
URL'leri toplar. Her girdi; orijinal URL'yi, ihlal göstergesi kontrolü
sırasında MVT kısaltılmış bir URL'yi çözdüyse genişletilmiş hedefini,
mesajın zaman damgasını ve `sms` ya da `whatsapp` kaynağını içerir.

---

### `sms_attachments.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `SMSAttachments` modülü tarafından oluşturulur. Modül, `SMS` modülünün kullandığı aynı veritabanından SMS veya iMessage yoluyla gönderilen ek dosyalara ilişkin ayrıntıları çıkarır. Bu kayıtlar, dosya biçimi ayrıştırıcılarındaki potansiyel güvenlik açıklarından veya Messages uygulamasının diğer dosya işleme biçimlerinden yararlanmaya yönelik sömürü girişimlerine işaret edebilecek benzersiz kalıpları belirtmek için yararlı olabilir.

---

### `tcc.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `TCC` modülü tarafından oluşturulur. Modül, hangi uygulamalara mikrofon, kamera veya konum gibi servislere erişim izni verildiğini ya da verilmediğini gösteren bir liste içeren */private/var/mobile/Library/TCC/TCC.db* konumundaki bir SQLite veritabanından kayıtları çıkarır.

---

### `version_history.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-close:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `IOSVersionHistory` modülü tarafından oluşturulur. Modül, iOS yazılım güncellemelerine ilişkin kayıtları */private/var/db/analyticsd/Analytics-Journal-\*.ips* konumundaki analiz plist dosyalarından çıkarır.

---

### `webkit_indexeddb.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-close:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `WebkitIndexedDB` modülü tarafından oluşturulur. Modül, cihazda yüklü herhangi bir uygulama tarafından oluşturulan IndexedDB dosyalarını içeren */private/var/mobile/Containers/Data/Application/\*/Library/WebKit/WebsiteData/IndexedDB* konumundaki dosya ve klasör adlarının bir listesini çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar çıkarılan adlara karşı kontrol edilir. Eşleşmeler *webkit_indexeddb_detected.json* dosyasında saklanır.

---

### `webkit_local_storage.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-close:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `WebkitLocalStorage` modülü tarafından oluşturulur. Modül, cihazda yüklü herhangi bir uygulama tarafından oluşturulan yerel depolama dosyalarını içeren */private/var/mobile/Containers/Data/Application/\*/Library/WebKit/WebsiteData/LocalStorage/* konumundaki dosya ve klasör adlarının bir listesini çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar çıkarılan adlara karşı kontrol edilir. Eşleşmeler *webkit_local_storage_detected.json* dosyasında saklanır.

---

### `webkit_resource_load_statistics.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `WebkitResourceLoadStatistics` modülü tarafından oluşturulur. Modül, mevcut WebKit ResourceLoadStatistics *observations.db* SQLite3 veritabanlarından kayıtları çıkarır. Bu kayıtlar, uygulamaların iletişim kurduğu alan adlarını bir zaman damgasıyla birlikte göstermelidir.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar çıkarılan alan adlarına karşı kontrol edilir. Eşleşmeler *webkit_resource_load_statistics_detected.json* dosyasında saklanır.

---

### `webkit_safari_view_service.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-close:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `WebkitSafariViewService` modülü tarafından oluşturulur. Modül, SafariViewService tarafından önbelleğe alınan dosyaları içeren */private/var/mobile/Containers/Data/Application/\*/SystemData/com.apple.SafariViewService/Library/WebKit/WebsiteData/* konumundaki dosya ve klasör adlarının bir listesini çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar çıkarılan adlara karşı kontrol edilir. Eşleşmeler *webkit_safari_view_service_detected.json* dosyasında saklanır.

---

### `webkit_session_resource_log.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `WebkitSessionResourceLog` modülü tarafından oluşturulur. Modül, ziyaret edilen farklı alan adları tarafından yüklenen kaynakların kayıtlarını içeren *full_browsing_session_resourceLog.plist* adlı plist dosyalarından kayıtları çıkarır.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar çıkarılan alan adlarına karşı kontrol edilir. Eşleşmeler *webkit_session_resource_log_detected.json* dosyasında saklanır.

---

### `whatsapp.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `WhatsApp` modülü tarafından oluşturulur. Modül, *private/var/mobile/Containers/Shared/AppGroup/\*/ChatStorage.sqlite* konumundaki SQLite veritabanından bir WhatsApp mesajları listesi çıkarır; ayrıca her sohbet oturumu için, o konuşmanın ilk ve son etkileşim tarihlerini içeren bir kayıt (`"record_type": "chat_session"` ile işaretlenmiş) üretir. Sohbet oturumları `chat_first_message` ve `chat_last_message` zaman çizelgesi olaylarını üretir; grup sohbetleri ayrıca bir `group_created` olayı üretir. Sohbetteki en son mesajlar silinmişse, bir sohbet oturumunun son mesaj tarihi, saklanan en yeni mesajının tarihinden daha ileri olabilir.

Güncel WhatsApp sürümleri, bire bir sohbet oturumlarını kişinin telefon numarası yerine opak bir LID tanımlayıcısıyla anahtarlar. Modül bunları, aynı uygulama grubundaki *LID.sqlite* veritabanındaki `ZWAPHONENUMBERLIDPAIR` tablosunu (veya bu tabloyu orada saklayan sürümlerde *ChatStorage.sqlite*'ın kendisini) kullanarak çözer; sohbet oturumu kayıtlarında `partner_resolved_phone_number` alanını doldurur ve zaman çizelgesi olaylarında telefon numarasını kullanır. Her LID-telefon numarası çifti de bir kayıt olarak (`"record_type": "lid_phone_number_pair"`) çıkarılır ve WhatsApp'ın bu ilişkiyi ne zaman öğrendiğini işaretleyen bir `lid_pair_recorded` zaman çizelgesi olayı üretir.

İhlal göstergeleri komut satırı aracılığıyla sağlanırsa, bunlar çıkarılan HTTP bağlantılarına karşı kontrol edilir. Eşleşmeler *whatsapp_detected.json* dosyasında saklanır.

---

### `whatsapp_contacts.json`

!!! info "Kullanılabilirlik"
    Yedek: :material-check:
    Tam dosya sistemi dökümü: :material-check:

Bu JSON dosyası, mvt-ios'un `WhatsappContacts` modülü tarafından oluşturulur. Modül, *private/var/mobile/Containers/Shared/AppGroup/\*/ContactsV2.sqlite* konumundaki SQLite veritabanından WhatsApp kişi kayıtlarını çıkarır; bunlar her kişinin telefon numarasını, WhatsApp ve LID tanımlayıcılarını ve *ChatStorage.sqlite* içinde kaydedilmeyen kişi başına kaybolan mesajlar zamanlayıcısını içerir. Bir kişi kaydında saklanan her zaman damgası bir zaman çizelgesi olayı üretir: `disappearing_mode_set` (kaybolan mesajlar zamanlayıcısının en son ne zaman değiştirildiği), `about_changed` (kişinin "about" metnini en son ne zaman değiştirdiği), `about_expiration` (süreli bir "about" metninin ne zaman sona ereceği) ve `contact_last_updated` (kişi kaydının en son ne zaman güncellendiği).

Bu veritabanı artımlı yedeklerde sıklıkla eksiktir. Bulunamadığında modül bir uyarı kaydeder ve hiçbir sonuç üretmez; bu durumda sohbetlerin kaybolan mesajlar durumu yedekten belirlenemez.


---

## `check-sysdiagnose` ile Çıkarılan Kayıtlar

### `sysdiagnose_info.json` {#sysdiagnose_infojson}

Bu JSON dosyası, mvt-ios'un `SysdiagnoseInfo` modülü tarafından oluşturulur. Modül, cihaza ve sysdiagnose'un kendisine ilişkin ayrıntıları çıkarır: remotectl döküm durumundan ve mobil etkinleştirme isteğinden product type ve model, iOS sürümü ve build, seri numarası, IMEI, MEID ve UDID; App Store daemon veritabanından Apple hesabının adı ve e-posta adresi (yeni iOS sürümlerinde artık bir sysdiagnose'un parçası değildir, eski arşivlerden hâlâ okunur) ve *sysdiagnose.log* dosyasından arşivin orijinal dosya adı ve oluşturulma zamanı.
