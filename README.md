# 📊 Veri Seti ve Kaynakça

> Türkçe SMS dolandırıcılık tespiti projesi için hazırlanan, **KVKK uyumlu** ve **maskelenmiş** sentetik veri seti.

---

## 📌 Genel Bakış

| Özellik | Değer |
|---|---|
| **Sınıflar** | Normal · Spam · Scam (3 sınıf) |
| **Dağılım** | Dengeli (1:1:1) |
| **Boyut** | 2.000+ SMS *(aynı metodolojiyle kolayca çoğaltılabilir)* |
| **Dil** | Türkçe |
| **Format** | CSV (`text`, `label`, `label_name`) |
| **Lisans** | MIT *(kaynak veri: Kaggle — ayrı lisans)* |
| **PII Durumu** | ✅ Tamamen maskelenmiş |

---

## 🧭 Veri Seti Hazırlama Süreci

### 1️⃣ Referans Veri Seti

Gerçek dünya referansı olarak Kaggle'da açık kaynak olarak paylaşılan **Turkish SMS Collection** kullanılmıştır.

| | |
|---|---|
| **Veri Seti** | Turkish SMS Collection |
| **Yayıncı** | Onur Karasoy |
| **Kaynak** | [Kaggle](https://www.kaggle.com/datasets/onurkarasoy/turkish-sms-collection) |
| **Boyut** | 4.000+ SMS |
| **Sınıflar** | Normal / Spam |
| **Rolü** | Referans + doğrulama (ana eğitim verisi **değil**) |

Bu veri seti iki sınıflı yapısı ve "Scam" sınıfını içermemesi nedeniyle tek başına yeterli görülmemiş; **referans ve test** amacıyla kullanılmıştır.

### 2️⃣ Sentetik Veri Üretimi

Kaggle veri setindeki dil yapısı, kalıplar ve sınıf özellikleri referans alınarak **kontrollü sentetik veri üretimi** gerçekleştirilmiştir.

- **Üretim araçları:** DeepSeek + Gemini *(eşit sayıda örnek)*
- **Sınıflar:** Normal · Spam · Scam
- **Amaç:** Kaggle'da bulunmayan "Scam" sınıfını temsil eden dengeli bir veri seti oluşturmak
- **Yöntem:** İki bağımsız LLM'in çıktıları birleştirilerek tek modele bağımlılık azaltılmış, veri çeşitliliği artırılmıştır
- **Genişletilebilirlik:** Aynı metodoloji tekrarlanarak veri seti ihtiyaç halinde kolayca büyütülebilir

### 3️⃣ Maskeleme (Sanitization)

Ham veri seti, **KVKK uyumluluğu** ve **ezberin engellenmesi** amacıyla kapsamlı bir maskeleme sürecinden geçirilmiştir.

| Kategori | Token | Örnek |
|---|---|---|
| Telefon | `[PHONE]` | `0532 123 45 67` → `[PHONE]` |
| E-posta | `[EMAIL]` | `ornek@email.com` → `[EMAIL]` |
| T.C. Kimlik No | `[ID_NUMBER]` | `12345678901` → `[ID_NUMBER]` |
| IBAN | `[IBAN]` | `TR12 3456 ...` → `[IBAN]` |
| MERSIS | `[MERSIS]` | `0123456789012345` → `[MERSIS]` |
| Marka | `[BRAND]` | `Garanti`, `Trendyol` → `[BRAND]` |
| URL | `[URL]` | `ornek.com`, `bit.ly/xyz` → `[URL]` |

**Maskelemenin gerekçeleri:**

- 🔒 **KVKK uyumluluğu** — Kişisel veriler tamamen ortadan kaldırıldı
- 🧠 **Ezberin engellenmesi** — Model "marka geçiyorsa scam'dir" gibi yüzeysel kalıplar yerine **semantik özelliklere** (aciliyet, belirsiz ödül, yönlendirme) odaklanır
- 🌍 **Genellenebilirlik** — Görülmemiş marka, telefon ve URL'lere karşı dayanıklılık artar

---

## 📦 Veri Seti Erişimi

- **Depo:** [github.com/eraygdev/Scam_Dedector_Dataset_Turkish](https://github.com/eraygdev/Scam_Dedector_Dataset_Turkish)
- **Dosya:** [`Synthetic_Raw_Dataset.csv`](https://github.com/eraygdev/Scam_Dedector_Dataset_Turkish/blob/main/Synthetic_Raw_Dataset.csv)

### Örnek Kayıt

```csv
text,label_name,label
"Banka: [BRAND] kartınız bloke, [PHONE] arayın",Scam,2
"Toplantı saat 14:00'te olacak mı",Normal,0
"[BRAND]'de %50 indirim, detay: [URL]",Spam,1
