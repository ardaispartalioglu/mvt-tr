# Komut Tamamlama

MVT, komut satırı arayüzünü oluşturmak için [Click](https://click.palletsprojects.com/en/stable/) kütüphanesini kullanır.

Click; Bash (4.4 ve üzeri sürümler), Zsh ve Fish için sekme tamamlama desteği sunar.

Etkinleştirmek için shell'inize bir tamamlama betiği kaydetmeniz gerekir; bu, kullandığınız shell'e göre değişir.

`mvt completion`, `mvt`, `mvt-ios` ve `mvt-android` komutlarını kapsayan tek bir betik üretir. Aşağıda bu betiğin nasıl oluşturulacağı ve shell yapılandırmanıza nasıl ekleneceği açıklanmaktadır.

> **Not: Değişikliklerin etkili olması için yeni bir shell başlatmanız gerekir.**

### Bash için

```bash
# bash komut tamamlama betiğini oluştur
mvt completion bash > ~/.mvt-complete.bash
```

`~/.bashrc` dosyasına aşağıdakileri ekleyin:
```bash
# mvt komut tamamlama betiğini source et
[ -f ~/.mvt-complete.bash ] && . ~/.mvt-complete.bash
```

### Zsh için

```bash
# zsh komut tamamlama betiğini oluştur
mvt completion zsh > ~/.mvt-complete.zsh
```

`~/.zshrc` dosyasına aşağıdakileri ekleyin:
```bash
# mvt komut tamamlama betiğini source et
[ -f ~/.mvt-complete.zsh ] && . ~/.mvt-complete.zsh
```

### Fish için

```bash
# fish komut tamamlama betiğini oluştur
mkdir -p ~/.config/fish/conf.d
mvt completion fish > ~/.config/fish/conf.d/mvt-completion.fish
```

Fish, `~/.config/fish/conf.d` içindeki dosyaları otomatik olarak yükler.

### Otomatik Kurulum

MVT, `--install` seçeneğini verdiğinizde tamamlama dosyasını yazabilir ve Bash ile Zsh için ilgili shell yapılandırmasını güncelleyebilir:

```bash
mvt completion bash --install
```

Gerektiğinde `bash` yerine `zsh` veya `fish` yazın. Fish için `--install`, tamamlama dosyasını `~/.config/fish/conf.d` içine yazar ve hiçbir shell yapılandırmasını değiştirmez.

!!! note

    Eski sürümler, `mvt-ios completion` ve `mvt-android completion` ile her komut
    için ayrı bir betik üretiyordu. Bunların yazdığı dosyalar çalışmaya devam eder.
    Tek betiğe geçtiğinizde, eski dosyaları ve bunları shell yapılandırmanızdan
    yükleyen satırları kaldırın.

Daha fazla bilgi için resmî [Click Docs](https://click.palletsprojects.com/en/stable/shell-completion/#enabling-completion) sayfasını ziyaret edin.
