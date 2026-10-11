# pymobiledevice3 Kurulumu

Bir iOS cihazından veri toplamadan önce [pymobiledevice3](https://github.com/doronz88/pymobiledevice3) kurmanızı öneririz. Bu araç, iTunes ile uyumlu yedekler oluşturmak ve çökme günlüklerini çıkarmak için komut satırı araçları sunar.

## Kurulum

[MVT kurulum talimatlarını](../install.md) izleyerek `pipx` kurun, ardından şunu çalıştırın:

```bash
pipx install pymobiledevice3
```

Mevcut bir kurulumu güncellemek için:

```bash
pipx upgrade pymobiledevice3
```

Linux'ta cihazlarla USB üzerinden iletişim kurmak için `usbmuxd` bileşenine de ihtiyacınız vardır. Debian ve Ubuntu'da şu komutla kurun:

```bash
sudo apt install usbmuxd
```

macOS gerekli USB cihaz hizmetini içerir. Diğer platformlar ve ek gereksinimler için [pymobiledevice3 kurulum belgelerine](https://doronz88.github.io/pymobiledevice3/installation/) bakın.

## Bağlantıyı Doğrulama

iOS cihazını bir USB kablosuyla bilgisayarınıza bağlayın ve kilidini açın. **Trust This Computer** (Bu Bilgisayara Güven) istemini kabul edin ve istenirse cihazın parolasını girin. Ardından şunu çalıştırın:

```bash
pymobiledevice3 usbmux list
pymobiledevice3 lockdown info
```

Bu komutlar bağlı cihazı listelemeli ve ayrıntılarını göstermelidir. Eşleştirme gerekirse şunu çalıştırın:

```bash
pymobiledevice3 lockdown pair
```

Hiçbir cihaz bulunamazsa USB bağlantısını kontrol edin, cihazın kilidinin açık olduğundan emin olun ve Linux'ta `usbmuxd` hizmetinin çalıştığını doğrulayın. Daha fazla yardım için [üst proje sorun giderme kılavuzuna](https://doronz88.github.io/pymobiledevice3/guides/troubleshooting/) bakın.

Bağlantı kurulduktan sonra [yedekleme talimatlarını](backup/pymobiledevice3.md) izleyin.
