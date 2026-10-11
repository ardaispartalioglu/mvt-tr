# MVT Türkçe Dokümantasyon (Gayriresmî Çeviri)

**Mobile Verification Toolkit (MVT)** dokümantasyonunun Türkçe çevirisi.

🌐 **Yayın adresi:** https://ardaispartalioglu.github.io/mvt-tr/

## Künye

- **Orijinal dokümantasyon:** https://docs.mvt.re — [mvt-project/mvt](https://github.com/mvt-project/mvt)
- **Çeviri anlık görüntüsü (snapshot):** upstream commit `b3aa443` (2026-10-09)
- **Çeviri tarihi:** Ekim 2026

## Önemli Notlar

- Bu çeviri **resmî değildir** ve MVT ekibi ya da Amnesty International tarafından gözden geçirilmemiştir.
  Herhangi bir uyuşmazlıkta **İngilizce orijinal metin esastır**.
- MVT ve bu dokümantasyon [MVT License 1.1](LICENSE) ile lisanslanmıştır: yazılım ve türevleri yalnızca
  verisi işlenen kişinin **rızasıyla** ("consensual use") kullanılabilir.
- Teknik terimler (MVT, mvt-ios, sysdiagnose, STIX2 vb.) tutarlılık amacıyla özgün hâliyle bırakılmıştır.

## Çeviri Kalitesi

- Terimler TDK Güncel Türkçe Sözlük (sozluk.gov.tr) üzerinden denetlendi; sözlükte bulunmayan alan terimlerinde (artefakt, triyaj, önbellek vb.) Türkçe DFIR/sektör kullanımı esas alındı.
- Ek uyumu: marka adlarında Apple Türkçe kaynaklarıyla tutarlı kullanım (macOS'te, iTunes'da), ünsüz benzeşmesi (Windows'ta, git'teki) ve tutarlı kesme işareti kuralları gözetildi.

## Katkı

Düzeltme ve iyileştirme PR'ları memnuniyetle karşılanır:

1. Depoyu forklayın, `main` üzerinden bir dal açın.
2. İlgili sayfayı `docs/` altında düzenleyin.
3. PR açın — hangi sayfada neyin düzeltildiğini kısaca yazın.

## Yerel Geliştirme

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements-docs.txt
mkdocs serve        # http://127.0.0.1:8000
mkdocs build        # site/ klasörüne statik çıktı
```

---

*Unofficial Turkish translation of the MVT documentation. The English original at
[docs.mvt.re](https://docs.mvt.re) is authoritative. Licensed under MVT License 1.1.*
