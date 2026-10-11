# pymobiledevice3 ile Yedek Alma

[pymobiledevice3'ü kurduktan](../install.md) ve cihazınızı eşleştirdikten sonra, `backup2` komutlarını kullanarak iTunes ile uyumlu bir yedek oluşturabilirsiniz.

## Yedek şifrelemeyi etkinleştirme

Şifrelenmiş yedekleri öneririz çünkü bunlar şifrelenmemiş yedeklerden daha fazla veri içerir. Şifreleme henüz etkin değilse, şu komutla etkinleştirin:

```bash
pymobiledevice3 backup2 encryption on 'YOUR_BACKUP_PASSWORD'
```

`YOUR_BACKUP_PASSWORD` yerine güçlü bir parola koyun ve parolayı güvende tutun: yedeğin şifresini analiz için çözerken bu parolaya ihtiyacınız olacak. Komut satırında verilen parolalar kabuk geçmişine kaydedilebilir veya diğer süreçler tarafından görülebilir.

Daha önce bir yedek parolası belirlenmişse o parolayı kullanın. Bilinen bir parolayı değiştirmek için:

```bash
pymobiledevice3 backup2 change-password 'CURRENT_PASSWORD' 'NEW_PASSWORD'
```

Şifrelemeyi `pymobiledevice3 backup2 encryption off 'CURRENT_PASSWORD'` komutuyla devre dışı bırakıp ardından yeniden etkinleştirebilirsiniz. Şifrelemeyi devre dışı bırakmak geçerli parolayı gerektirir; bilinmeyen bir parolayı atlatmanın bir yolu değildir.

!!! warning
    Parolayı kurtaramıyorsanız, [iPhone'un Ayarlar uygulamasındaki Tüm Ayarları Sıfırla](https://support.apple.com/en-us/HT205220) seçeneğiyle sıfırlamak son çare olmalıdır. Ayarları sıfırlamak, işe yarar adli izler içeren dosyaları silebilir.

## Yedek oluşturma

Daha önceki bir edinimin üzerine yazmayı önlemek için yeni bir hedef dizin seçin:

```bash
mkdir -p /path/to/backup/
pymobiledevice3 backup2 backup --full /path/to/backup/
```

Yedek, cihazın UDID'siyle adlandırılan bir alt dizine kaydedilir; örneğin `/path/to/backup/udid/`. Bu dizinin şifresini çözmek ve analiz etmek için [Yedeği mvt-ios ile Kontrol Etme](check.md) sayfasını izleyin.

Ek seçenekler için [pymobiledevice3 backup2 referansına](https://doronz88.github.io/pymobiledevice3/cli/backup2/) bakın.