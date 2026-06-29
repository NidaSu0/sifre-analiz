[README.md](https://github.com/user-attachments/files/29467801/README.md)
<div align="center">

<img src="https://capsule-render.vercel.app/api?type=cylinder&color=0:0d1117,40:1a0a0a,70:f85149,100:0d1117&height=180&section=header&text=🔐+Şifre+Analiz&fontSize=46&fontColor=f85149&fontAlignY=55&animation=fadeIn&desc=Parola+Güvenliği+%7C+Hash+%7C+Üretici+%7C+Geçmiş&descAlignY=78&descSize=16&descColor=8b949e" width="100%"/>

</div>

<div align="center">

[![Typing SVG](https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=700&size=20&pause=800&color=F85149&center=true&vCenter=true&multiline=false&repeat=true&width=680&height=50&lines=Şifreni+yaz+→+anında+analiz+et.;Brute-force+süresini+gör.;MD5+mi+SHA-512+mi+%3F+Karşılaştır.;Güvenli+şifre+üret%2C+kaydet%2C+kullan.)](https://git.io/typing-svg)

<br/>

![Python](https://img.shields.io/badge/Python-3.6%2B-f85149?style=flat-square&logo=python&logoColor=white)
![Tkinter](https://img.shields.io/badge/GUI-Tkinter-d29922?style=flat-square&logo=python&logoColor=white)
![SQLite Free](https://img.shields.io/badge/Kurulum-Sıfır_Bağımlılık-3fb950?style=flat-square&logo=checkmarx&logoColor=white)
![Platform](https://img.shields.io/badge/Windows_%7C_macOS_%7C_Linux-0d1117?style=flat-square&logo=windows&logoColor=f85149)
![Offline](https://img.shields.io/badge/Tamamen_Offline-Veri_Çıkmaz-58a6ff?style=flat-square&logo=shield&logoColor=white)

</div>

---

<br/>

<table align="center">
<tr>
<td align="center" width="200">
<img src="https://img.shields.io/badge/%E2%8F%B1%EF%B8%8F-ANLIK_ANALİZ-f85149?style=for-the-badge" /><br/>
<sub>Yazdıkça anlık skor</sub>
</td>
<td align="center" width="200">
<img src="https://img.shields.io/badge/%F0%9F%94%90-HASH_MOTORu-d29922?style=for-the-badge" /><br/>
<sub>MD5 / SHA-1 / 256 / 512</sub>
</td>
<td align="center" width="200">
<img src="https://img.shields.io/badge/%E2%9A%99-ŞİFRE_ÜRETİCİ-3fb950?style=for-the-badge" /><br/>
<sub>Kriptografik rastgele</sub>
</td>
<td align="center" width="200">
<img src="https://img.shields.io/badge/%F0%9F%93%8B-GEÇMİŞ_KAYIT-58a6ff?style=for-the-badge" /><br/>
<sub>JSON tabanlı log</sub>
</td>
</tr>
</table>

<br/>

---

## Ne Yapar?

Bir şifreyi kutuya yazıyorsun. Program o anda puanı hesaplıyor, hangi karakterlerin eksik olduğunu söylüyor, brute-force saldırısının ne kadar süreceğini tahmin ediyor. Hash sekmesine geçip aynı şifrenin MD5'ini, SHA-256'sını görebiliyorsun. Üretici sekmesinde uzunluğu ve miktarı ayarlayıp rastgele şifreler üretebiliyorsun. Yaptığın her analiz JSON'a kaydoluyor, geçmişi tablo olarak görebiliyorsun.

Tek `.py` dosyası. Kurulum yok, hesap yok, internet yok.

---

## 🗂️ Sekmeler

```
┌─────────────────────────────────────────────────────────────────────┐
│  🔐  Şifre Güvenliği Analiz Aracı           Nida Subaşı  |  2026  │
├──────────────────┬──────────────────────────────────────────────────┤
│  🔍 ANALİZ       │  ┌──────────────────────────────────────────┐   │
│  ⚙  ÜRETİCİ     │  │  ●●●●●●●●●●●●●●         [👁]           │   │
│  # HASH          │  └──────────────────────────────────────────┘   │
│  📋 GEÇMİŞ      │                                                  │
│                  │   72 / 100              GÜCLU                    │
│                  │   ████████████████░░░░░░░                       │
│                  │                                                  │
│                  │   ✓ Küçük harf    ✓ Büyük harf                  │
│                  │   ✓ Rakam         ✓ Özel karakter               │
│                  │   ✗ Tekrar eden   ✓ Yaygın değil                │
│                  │                                                  │
│                  │   Uzunluk: 14     Entropi: 3.87 bit              │
│                  │   GPU BF: ~4.2 yıl                              │
│                  │                                                  │
│  ● Sistem Hazır  │   Nida Subaşı  |  2026                ——————   │
└──────────────────┴──────────────────────────────────────────────────┘
```

### 🔍 Analiz Sekmesi
Şifreyi yazınca her tuşa basışta skor güncelleniyor. 0–100 arası puan hesaplanıyor: uzunluk, büyük/küçük harf, rakam, özel karakter, tekrar eden harf ve yaygın şifre listesi kontrol ediliyor. Sağ panelde eksik olan her şeyi söylüyor, brute-force süresi tahminini de gösteriyor.

### ⚙ Üretici Sekmesi
Uzunluk ve adet ayarlayıp "Üret" diyince kriptografik olarak güvenli (`secrets` modülü) şifreler oluşturuyor. Her üretilen şifrenin yanında puanı da yazıyor. Listeden tıklayıp Ctrl+C ile kopyalayabiliyorsun.

### # Hash Sekmesi
Şifreyi girip "Hash Çıkar" diyince aynı anda dört hash algoritmasının çıktısını gösteriyor. MD5 ve SHA-1 kırmızı uyarıyla geliyor çünkü artık zayıf kabul ediliyor. SHA-256 ve SHA-512 yeşil.

### 📋 Geçmiş Sekmesi
Her analiz yapıldığında tarih, uzunluk, puan, seviye ve entropi değeri JSON'a yazılıyor. Bu sekmede son 50 kaydı tablo olarak görüyorsun; toplam analiz sayısı, ortalama, max ve min puan altta özetleniyor.

---

## ⚡ Kurulum ve Çalıştırma

Flet, npm, pip, sanal ortam — hiçbiri yok. Python yeterli.

### Windows

```bash
# Çift tıklama ile açılır
# ya da CMD'de:
python sifre_analiz.py
```

### macOS / Linux

```bash
python3 sifre_analiz.py
```

> Python yoksa → [python.org/downloads](https://www.python.org/downloads/)
> Windows kurulumunda **"Add Python to PATH"** kutusunu işaretle.

---

## 📐 Puan Sistemi

```
Uzunluk puanı (maks 30)
  ≥ 20 karakter  →  +30
  ≥ 16           →  +25
  ≥ 12           →  +20
  ≥  8           →  +12
  ≥  6           →  +6
  <  6           →  +2

Karakter puanı (maks 50)
  Küçük harf     →  +10
  Büyük harf     →  +10
  Rakam          →  +10
  Özel karakter  →  +20
  Tekrar (3+)    →  -10  ← ceza

Yaygın şifre listesindeyse → tavan 15/100
```

Sonuç `0–100` arasında klamplandıktan sonra seviye belirleniyor:

| Puan | Seviye |
|:----:|:-------|
| ≥ 70 | 🟢 ÇOK GÜÇLÜ |
| ≥ 55 | 🟢 GÜÇLÜ |
| ≥ 40 | 🟡 ORTA |
| ≥ 25 | 🔴 ZAYIF |
| < 25 | 🔴 ÇOK ZAYIF |

---

## 🔑 Hash Karşılaştırması

| Algoritma | Durum | Ne İçin? |
|:----------|:-----:|:---------|
| MD5 | ⚠️ Guvensiz | Sadece dosya doğrulama |
| SHA-1 | ⚠️ Zayıfladı | Collision riski var |
| SHA-256 | ✅ Güvenli | Modern sistemler |
| SHA-512 | ✅ Çok Güçlü | Yüksek güvenlik gerektiren yerler |

---

## 📁 Dosyalar

```
sifre-analiz/
│
├── sifre_analiz.py              # Tek dosyada her şey — GUI + mantık
├── gecmis.json                  # Analiz geçmişi (otomatik oluşur)
└── NASIL_CALISTIRILIR_sifre.txt # Kurulum kılavuzu
```

`gecmis.json` programla aynı klasörde oluşur. Silersen geçmiş sıfırlanır, program çalışmaya devam eder.

---

## 🧠 Teknik Notlar

- **GUI:** Tkinter — Python'un standart kütüphanesi, harici kurulum yok
- **Hash:** `hashlib` — yine standart, dışarıdan bir şey çekilmiyor
- **Şifre üretimi:** `secrets` modülü — `random` değil, kriptografik
- **Veri:** JSON dosyası — SQLite eklemek istedim ama Tkinter ile yeterince sade kaldı
- **Tema:** GitHub Dark renk paleti (`#0d1117`, `#161b22`, `#f85149`)
- **Gizle/Göster butonu:** Entry widget'ının `show` parametresiyle — platform farkı yok

---

<div align="center">

<br/>

[![Nida Subaşı](https://img.shields.io/badge/Nida_Subaşı-Üniversite_Bitirme_Projesi-f85149?style=for-the-badge&logo=github&logoColor=white)](https://github.com/NidaSu0)
&nbsp;
![2026](https://img.shields.io/badge/2026-Bilişim_Güvenliği-0d1117?style=for-the-badge&logo=shield&logoColor=f85149)

<br/><br/>

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:0d1117,50:1a0505,100:0d1117&height=2&section=footer" width="100%"/>

```
şifreni bilen seni bilir.
```

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0d1117,40:1a0a0a,80:f85149,100:0d1117&height=100&section=footer&animation=twinkling&reversal=true" width="100%"/>

</div>
