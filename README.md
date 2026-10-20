# KTÜ OFTF Yazılım Mühendisliği — Güvenli Haberleşme Projesi

Bu proje, **Karadeniz Teknik Üniversitesi Of Teknoloji Fakültesi Yazılım Mühendisliği Bölümü Kriptoloji Dersi** kapsamında geliştirilmektedir. 

Sistem; bir merkezi sunucu (Master) ve iki bağımsız istemci (Slave) arasında uçtan uca güvenli ve güvensiz veri (metin ve PDF dosyası) aktarımını sağlamak, farklı anahtar değişim protokollerinin performans metriklerini ölçmek ve kütüphanesiz AES-128 algoritmasını doğrulamak amacıyla tasarlanmıştır.

---

## 👥 Grup Bilgileri ve Görev Dağılımı

| Öğrenci No | Adı Soyadı | Rol | Sorumluluk Alanı / Branch |
| :--- | :--- | :--- | :--- |
| **445867** | **Ege Erdoğan** | **Master (Takım Kaptanı)** | `master-server`<br>• Çoklu istemci destekli TCP Sunucu mimarisi<br>• Mesaj ve dosya yönlendirme (Routing) köprüsü<br>• AES-128 Re-encryption (Yeniden Şifreleme) mekanizması<br>• Ağ protokolü standardı (`PacketProtocol`) ve Wireshark analizi |
| **445905** | **Muhammed Mert Sığırcı** | **Slave 1** | `slave-1-client-crypto`<br>• TCP İstemci ağ bağlantısı ve multithreading altyapısı<br>• Bouncy Castle ile hibrit anahtar değişimi (RSA-2048, DH-2048, ECC, Kyber/ML-KEM-768)<br>• Kütüphaneli AES-128 şifreleme/çözme motoru<br>• Süre (ms) ve paket boyutu benchmark modülü |
| **442970** | **Muhammet Esatoğlu** | **Slave 2** | `slave-2-manual-aes-ui`<br>• Kütüphanesiz (Sıfırdan) AES-128 implementasyonu (S-Box, ShiftRows, MixColumns, AddRoundKey)<br>• Manuel AES vs. Kütüphaneli AES doğrulama testleri<br>• PDF dosyası binary okuma/yazma ve chunk iletim yöneticisi<br>• İstemci kullanıcı arayüzü ve hata yönetimi |

---

## 🛠️ Teknoloji ve Kütüphane Tercihleri

* **Programlama Dili:** Java 17+
* **Sunucu Çatısı:** Spring Boot 3.x / Standart Java Sockets
* **Kriptografi Kütüphanesi:** Bouncy Castle (`bcprov-jdk18on`)
* **Proje Yönetimi ve Derleme:** Maven (Multi-Module Architecture)
* **Paket Analizi:** Wireshark (`.pcapng`)
* **Sürüm Kontrol Sistemi:** Git & GitHub (3 Bağımsız Geliştirme Branch'i)

---

## 🏛️ Sistem Mimarisi ve Çalışma Prensipleri

İstemciler birbirine doğrudan bağlanmaz. Tüm trafik merkezi sunucu üzerinden akar:
1. **Güvensiz Mod:** Veriler (metin veya PDF) ağ üzerinde şifrelenmeden düz metin (plaintext) / ham bayt olarak iletilir. Paketler Wireshark ile açıkça izlenebilir.
2. **Güvenli Mod:** 
   * İstemciler ile Sunucu arasında seçilen anahtar dağıtım protokolü (**RSA-2048, DH-2048, ECC/ECDH veya Kyber ML-KEM-768**) üzerinden bir **AES-128** simetrik oturum anahtarı paylaşılır.
   * Gönderici istemci veriyi kendi oturum anahtarıyla şifreler.
   * Sunucu gelen şifreli veriyi çözer, doğrular ve hedef istemcinin oturum anahtarıyla **yeniden şifreleyerek (re-encrypt)** hedefe ulaştırır.
3. **Manuel AES-128:** Harici hiçbir kripto kütüphanesi kullanılmadan saf Java ile kodlanan blok şifreleme adımları, kütüphaneli sonuçla test vektörleri üzerinden karşılaştırılır.

---

## 📂 Dizin Yapısı

```text
kriptoloji-guvenli-haberlesme/
├── server/               # Master Sunucu (Spring Boot & TCP Socket Server)
├── client/               # Slave İstemci Uygulaması (Ağ, UI ve Hibrit Kripto)
├── manual-aes/           # Kütüphanesiz Sıfırdan AES-128 İmplementasyonu
├── pcap_logs/            # Wireshark trafik yakalama kayıtları (.pcapng)
├── docs/                 # Sistem mimari şemaları ve teknik rapor taslağı
└── pom.xml               # Ana Maven modül yapılandırması
