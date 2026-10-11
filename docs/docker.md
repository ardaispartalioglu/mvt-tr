# Docker Kullanımı

Docker kullanmak, MVT'yi bağımlılıkları hazır kurulu şekilde çalıştırmayı kolaylaştırır. Bunun bir Linux ana makinesi gerektirdiğini unutmayın; çünkü Windows ve Mac için Docker [USB aygıtlarının aktarılmasını desteklemez](https://docs.docker.com/desktop/faqs/#can-i-pass-through-a-usb-device-to-a-container).

Ana ve iOS Docker imajları, [iOS yedeği oluşturmak](ios/backup/pymobiledevice3.md) için pymobiledevice3 içerir. pymobiledevice3'ü ana makinede ayrıca kurmanız gerekmez. Eski imaj etiketleri bunun yerine hâlâ libimobiledevice içerebilir; pymobiledevice3 kullanılamıyorsa güncellenmiş kaynaktan derleyin.

Docker'ı [resmî dokümantasyonu](https://docs.docker.com/get-docker/) izleyerek kurun.

Docker kurulduktan sonra MVT'yi, önceden derlenmiş bir MVT Docker imajı indirerek ya da MVT kaynak deposundan kendiniz bir Docker imajı derleyerek çalıştırabilirsiniz.

### Önceden Derlenmiş Docker İmajını Kullanma

```bash
docker pull ghcr.io/mvt-project/mvt
```

Ardından Docker konteynerini şu komutla çalıştırabilirsiniz:

```
docker run -it ghcr.io/mvt-project/mvt
```


### Docker İmajını Kaynaktan Derleme ve Çalıştırma

```bash
git clone https://github.com/mvt-project/mvt.git
cd mvt
docker build -t mvt .
```

İmajın başarıyla oluşturulup oluşturulmadığını test edin:

```bash
docker run -it mvt
```

İstem (prompt) başarıyla açılırsa `exit` ile kapatabilirsiniz.

## Docker'dan iOS Cihazına Erişme

Linux ana makinesinde [usbmuxd](https://github.com/libimobiledevice/usbmuxd) kurup başlatın, ardından iOS cihazını bağlayın ve kilidini açın. Daemon, cihazı `/var/run/usbmuxd` soketi üzerinden sunar.

pymobiledevice3'ün cihazla iletişim kurabilmesi için bu soketi konteynere bağlayın. Ayrıca alınan yedeklerin konteyner kapandıktan sonra da kalıcı olması için yerel bir dizini bağlayın:

```bash
mkdir -p "$PWD/cases"
docker run --rm -it \
    --mount type=bind,source=/var/run/usbmuxd,target=/var/run/usbmuxd \
    --mount type=bind,source="$PWD/cases",target=/home/cases \
    ghcr.io/mvt-project/mvt
```

Konteyner içinde bağlantıyı doğrulayın:

```bash
pymobiledevice3 lockdown info
```

İstenirse kilidi açık cihazdaki güven istemini kabul edin. Ardından [yedek alma talimatlarını](ios/backup/pymobiledevice3.md) izleyin; hedef olarak `/home/cases` altında bir konum (örneğin `/home/cases/backup`) kullanın.

Yalnızca iOS için olan imaj varsayılan olarak `mvt-ios` çalıştırır. Bunun yerine pymobiledevice3 kullanmak için imajın entrypoint'ini geçersiz kılın:

```bash
docker run --rm -it \
    --mount type=bind,source=/var/run/usbmuxd,target=/var/run/usbmuxd \
    --entrypoint pymobiledevice3 \
    ghcr.io/mvt-project/mvt:latest-ios lockdown info
```

İmajı kaynaktan derlediyseniz, `ghcr.io/mvt-project/mvt` yerine `mvt` kullanın.
