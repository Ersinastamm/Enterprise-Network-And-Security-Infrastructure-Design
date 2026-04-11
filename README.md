# Enterprise-Network-And-Security-Infrastructure-Design

EVE-NG üzerinde tasarlanmış; **FortiGate Active-Passive HA**, **Site-to-Site IPsec VPN** ve **SSL VPN** teknolojilerini içeren yedekli kurumsal ağ ve güvenlik altyapısı tasarımı.

---

## 📝 Proje Özeti
Bu çalışma; EVE-NG üzerinde, bir kurumun Merkez (HQ) ve Şube (Branch) lokasyonları arasındaki ağ trafiğini ve güvenliğini yönetmek için kurgulanmıştır. Temel odak noktası; cihaz veya hat kesintilerinde trafiğin durmaması ve uzak çalışanların merkeze güvenli erişimidir.

### 🛠 Yapılandırmalar
* **Yüksek Erişilebilirlik (HA):** Merkez ofiste iki adet FortiGate cihazı Active-Passive HA modunda yapılandırılmıştır.
* **Güvenli Şubeler Arası Bağlantı:** HQ ve Branch lokasyonları arasında Site-to-Site IPsec VPN tüneli kurulmuştur.
* **Ağ Segmentasyonu Ve Yedeklilik (VLAN, HSRP):** İç ağ; Satış, Finans, İK ve IT olmak üzere VLAN'lara bölünmüştür. Core Switch'ler üzerinde HSRP ile yedeklilik sağlanmıştır.
* **Uzak Erişim (SSL-VPN):** Dışarıdan çalışan kullanıcılar için FortiClient SSL-VPN portalı yapılandırılmıştır.

---

## 📊 Detaylı IP ve VLAN Planlaması

### Merkez (HQ) Ofis
| VLAN ID | Departman / Kullanım | IP Bloğu | Gateway |
| :--- | :--- | :--- | :--- |
| **VLAN 10** | Sales | 192.168.10.0/24 | 192.168.10.1 |
| **VLAN 20** | Finance | 192.168.20.0/24 | 192.168.20.1 |
| **VLAN 30** | HR | 192.168.30.0/24 | 192.168.30.1 |
| **VLAN 40** | IT | 192.168.40.0/24 | 192.168.40.1 |

### Şube (Branch) Ofis
| Birim | IP Bloğu | Gateway |
| :--- | :--- | :--- |
| **Şube LAN 100** | 172.16.100.0/24 | 172.16.100.1 |
| **Şube LAN 200** | 172.16.200.0/24 | 172.16.200.1 |

---

## 🗺 TOPOLOGY
<img width="1869" height="897" alt="Topology" src="https://github.com/user-attachments/assets/54dbd9bd-0543-4ce4-9b8b-aa586044f0c7" />

---

## 🛡 Yüksek Erişilebilirlik (HA) Yapılandırması

### FGT-1 HA Yapılandırması
Aşağıdaki görselde de görüldüğü üzere; sistem Active-Passive HA modunda yapılandırılmıştır. Donanımsal yedekliliğin yanı sıra, kritik ağ bacaklarında yaşanabilecek kesintilere karşı **Interface Monitoring** özelliği aktif edilmiştir. Bu sayede, izlenen arayüzlerden herhangi birinin 'down' olması durumunda cihaz otomatik olarak failover yaparak trafiği yedek üniteye aktarır. 

Yapılandırmada **Session Pick-up** özelliği etkinleştirilerek, geçiş esnasında aktif TCP oturumlarının ve VPN tünellerinin korunması sağlanmıştır.

<img width="1899" height="908" alt="FGT-1 HA Config" src="https://github.com/user-attachments/assets/2f9ff6f6-7fce-44d8-a4c5-8385390dda1c" />

### FGT-2 HA Yapılandırması
Sistemde yer alan ikinci FortiGate cihazı Passive (Standby) modda beklemektedir. Bu ünite, Primary cihaz ile sürekli bir 'Heartbeat' bağı kurarak konfigürasyon, firewall session tablosu ve IPsec tünel bilgilerini gerçek zamanlı olarak senkronize eder.

<img width="1904" height="903" alt="FGT-2 HA Config" src="https://github.com/user-attachments/assets/a37f5099-e372-4359-a32d-a2867df56722" />

### HA Senkronizasyon Durumu
Görselde görüldüğü üzere; FGT-1 ve FGT-2 cihazları **'Synchronized'** statüsündedir. Bu durum, her iki ünite arasındaki konfigürasyon veritabanının ve çalışma parametrelerinin birbiriyle tam uyumlu olduğunu göstermektedir.

<img width="1902" height="902" alt="HA Sync Status" src="https://github.com/user-attachments/assets/82f31aa2-cc79-4dde-afcc-7ef4410c0ecf" />

---

## 🧪 Failover Senaryosu ve Test Sonuçları

### Cihaz Kesintisi Simülasyonu
Test senaryosu kapsamında, Merkez ofisteki Primary FortiGate ünitesinde donanımsal bir arıza simüle edilerek cihaz kapatılmıştır.

<img width="774" height="910" alt="Master Down" src="https://github.com/user-attachments/assets/753c800a-739b-43c3-bb71-18f1c0c40b2c" />

### Kesintisiz Trafik (Ping Testi)
Cihazın kapanma anında aktif trafik (ICMP) izlenmiştir. Görselde görüldüğü üzere, sadece 2 paketlik bir gecikme ile trafik kesintiye uğramadan yedek üniteye devredilmiştir.

<img width="1225" height="805" alt="Ping Failover" src="https://github.com/user-attachments/assets/7dec0dca-ac2d-4ed5-9e6e-90a3a832f850" />

### Rol Değişimi
Failover sonrası Secondary ünite, cluster içindeki 'Primary' rolünü otomatik olarak üstlenmiş ve network yönetimini devralmıştır.

<img width="1889" height="894" alt="Role Change" src="https://github.com/user-attachments/assets/a60fb45c-6f63-40ba-a041-6651f88f4a81" />

### Log Kaydı Doğrulaması
Secondary cihaz üzerindeki trafik logları incelendiğinde, istemci (Client) IP adreslerinin sorunsuz bir şekilde oturumlarını sürdürdüğü ve firewall kuralları üzerinden trafiğin akmaya devam ettiği teyit edilmiştir.

<img width="1904" height="908" alt="Traffic Logs" src="https://github.com/user-attachments/assets/4c0f0ad5-14e5-4427-a13c-b210ac227ec2" />

---

## 🌐 Site-to-Site IPsec VPN Yapılandırması (HQ & Branch)

### HQ-FIREWALL IPsec VPN Yapılandırması

**Tünel Ayarları (Phase 1 & Phase 2):** Görselde görüldüğü üzere; Remote Gateway olarak Branch ofis IP adresi tanımlanmış ve IKEv1 parametreleri (DES-MD5) set edilmiştir. Phase 2 aşamasında ilgili networklerin el sıkışması için gerekli selector tanımlamaları yapılmıştır.

<img width="1882" height="912" alt="HQ VPN Config 1" src="https://github.com/user-attachments/assets/b7d28656-8776-4d1c-9848-427d5d473949" />
<img width="1910" height="930" alt="HQ VPN Config 2" src="https://github.com/user-attachments/assets/93601d29-dc60-40fa-b7cd-bc391654f4bf" />
<img width="1900" height="907" alt="HQ VPN Config 3" src="https://github.com/user-attachments/assets/8fc44a87-abe1-4285-a564-55a7ea65c2f9" />

**Routing (Rota):** Şube networküne (Branch LAN) erişim sağlamak amacıyla hedef networkler IPsec tünel arayüzüne yönlendirilmiş, böylece trafiğin tünel içine girmesi sağlanmıştır.

<img width="1907" height="916" alt="HQ Static Route" src="https://github.com/user-attachments/assets/e120aab1-d2e1-4d6f-91a1-f9e8d49be61a" />

---

### BR-FIREWALL (Şube) IPsec Yapılandırması

**Tünel Ayarları:** HQ-Firewall üzerindeki Phase 1 ve Phase 2 ayarları ile birebir eşleşecek (Mirror Config) şekilde konfigürasyon yapılmıştır. IKE protokolü üzerinden karşılıklı güvenli anahtar değişimi sağlanmıştır.

<img width="1902" height="911" alt="BR VPN Config 1" src="https://github.com/user-attachments/assets/12ea7473-c45f-4531-bd4c-afd620b8e137" />
<img width="1902" height="904" alt="BR VPN Config 2" src="https://github.com/user-attachments/assets/4d7bca7d-177a-4a06-9027-c168bdbded9e" />
<img width="1902" height="917" alt="BR VPN Config 3" src="https://github.com/user-attachments/assets/f3295ba7-e4fc-47c3-af60-22add2d7b83e" />

**Routing (Rota):** Merkez ofis networklerine (HQ LAN) giden tüm taleplerin IPsec tüneline (Branch-to-HQ) yönlendirilmesi için statik rotalar yazılmıştır.

<img width="1908" height="908" alt="BR Static Route" src="https://github.com/user-attachments/assets/206d3c04-1d8b-46ac-b03c-977e7db33495" />

**Firewall Policy:** Şube tarafındaki kullanıcıların merkez kaynaklarına erişebilmesi için gerekli izin politikaları arayüz bazlı (LAN -> VPN) olarak tanımlanmıştır.

<img width="1899" height="908" alt="BR Policy 1" src="https://github.com/user-attachments/assets/c6d22c5c-cf69-4971-8c91-2ad7e1cfa8cc" />
<img width="1906" height="906" alt="BR Policy 2" src="https://github.com/user-attachments/assets/73b00d20-38fa-4a11-9d09-1dfee4983ce0" />

---

## 🔐 Uzak Erişim (SSL-VPN)

Evden veya dışarıdan çalışan kullanıcılar için FortiClient SSL-VPN portalı yapılandırılmış, kurumsal kaynaklara güvenli erişim simüle edilmiştir.

<img width="1877" height="913" alt="SSL VPN Status" src="https://github.com/user-attachments/assets/fa1e37a3-5fdb-4513-956b-9995efe4f603" />

---

**NOC Uzmanı Notu:** Yapılandırma sonrası her iki lokasyonda da yapılan son kontrollerde; IPsec tünel durumunun "Up" olduğu ve veri paketlerinin başarılı bir şekilde şifrelenerek iletildiği doğrulanmıştır.
