# Kurulum

Devam etmeden önce, MVT'nin çalışmak için Python 3.10 veya daha yeni bir sürüm gerektirdiğini lütfen unutmayın. Çoğu işletim sisteminde mevcut olması gerekse de, devam etmeden önce lütfen bundan emin olun.

## Linux için bağımlılıklar

İlk olarak, gerekli tüm araçları derlemek için ihtiyaç duyulacak bazı temel bağımlılıkları kurun:

```bash
sudo apt install python3 python3-venv python3-pip sqlite3 libusb-1.0-0
```

Yalnızca `mvt-ios` kullanacak ve `mvt-android` kullanmayacaksanız *libusb-1.0-0* gerekli değildir.

(Önerilir) `pipx`'i kurun

Ubuntu 23.04 ve üzeri için:
```bash
sudo apt install pipx
pipx ensurepath
```

Ubuntu 22.04 ve altı için:
```
python3 -m pip install --user pipx
python3 -m pipx ensurepath
```

Diğer dağıtımlar: paket yöneticinizden bir `pipx` veya `python-pipx` olup olmadığını kontrol edin.

Android cihazlarla çalışırken ek olarak [Android SDK Platform Tools](https://developer.android.com/studio/releases/platform-tools) kurmalısınız. Tercih ettiğiniz dağıtımın sunduğu bir paketi kurmayı isterseniz, modern Android cihazlarla uyumluluğu sağlamak için lütfen sürümün güncel olduğundan emin olun.

## macOS için bağımlılıklar

MVT'yi macOS üzerinde çalıştırmak için Xcode ve [homebrew](https://brew.sh) kurulu olmalıdır.

Bağımlılıkları kurmak için şunu kullanın:

```bash
brew install python3 pipx libusb sqlite3
```

Yalnızca `mvt-ios` kullanacak ve `mvt-android` kullanmayacaksanız *libusb* gerekli değildir.

Android cihazlarla çalışırken ek olarak [Android SDK Platform Tools](https://developer.android.com/studio/releases/platform-tools) kurmalısınız:

```bash
brew install --cask android-platform-tools
```

Veya [resmî ikili sürümleri](https://developer.android.com/studio/releases/platform-tools) indirerek.

## Windows'ta MVT

MVT şu anda Windows üzerinde yerel olarak çalışmayı resmî olarak desteklememektedir. İşlevlerin çoğunun kutudan çıktığı gibi çalışması gerekse de, özellikle `mvt-android` ile ilgili bilinen sorunlar vardır.

MVT'yi [Windows Subsystem Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/about) üzerinden kurup çalıştırmayı denemeniz ve tercih ettiğiniz dağıtıma yönelik Linux kurulum talimatlarını izlemeniz önerilir.

## MVT'nin kurulumu

### pipx ile PyPI'dan kurulum (önerilir)
1. İşletim sisteminiz/dağıtımınız için yukarıdaki talimatları izleyerek `pipx`'i kurun. `pipx ensurepath` komutunu çalıştırdığınızdan ve yeni bir terminal penceresi açtığınızdan emin olun.
2. ```bash
   pipx install mvt
   ```

Artık `mvt`, `mvt-ios` ve `mvt-android` araçlarının kurulu olması gerekir. Bu komutların bulunamaması gibi sorunlarla karşılaşırsanız, `pipx ensurepath` komutunu çalıştırdığınızdan ve yeni bir terminal penceresi açtığınızdan emin olun.

### PyPI'dan doğrudan sanal ortama kurulum
Sanal ortamınız için `pipenv`, `poetry` vb. kullanabilirsiniz, ancak verilen örnek yerleşik `venv` aracıyla yapılmıştır:

1. Sanal ortamı, geçerli dizinde `env` adlı bir klasörde oluşturun:
```bash
python3 -m venv env
```

2. Sanal ortamı etkinleştirin:
```bash
source env/bin/activate
```

3. `mvt` paketini sanal ortama kurun:
```bash
pip install mvt
```

Sanal ortam etkin olduğu sürece `mvt`, `mvt-ios` ve `mvt-android` araçları artık komut olarak kullanılabilir olmalıdır.

### pipx ile git kaynağından kurulum
Geliştirme aşamasındaki en yeni özelliklere sahip olmak istiyorsanız, MVT'yi doğrudan git'teki kaynak koddan kurabilirsiniz.

```bash
pipx install --force git+https://github.com/mvt-project/mvt.git
```

Artık `mvt`, `mvt-ios` ve `mvt-android` araçlarının kurulu olması gerekir.

**Notlar:**
1. `--force` bayrağı, paketin yeniden kurulmasını zorlamak için gereklidir.
2. Bir PyPI sürümüne geri dönmek için önce `pipx uninstall mvt` komutunu çalıştırmak gerekli olacaktır.

## Komut tamamlamalarının kurulumu

Bkz. ["Komut tamamlamaları"](command_completion.md)
