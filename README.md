# Enterprise-Network-And-Security-Infrastructure-Design
EVE-NG üzerinde tasarlanmış; FortiGate A-P HA , Site-to-Site IPsec ve SSL VPN teknolojilerini içeren yedekli kurumsal ağ ve güvenlik altyapısı tasarımı.


Proje Özeti: 

Bu çalışma; EVE-NG üzerinde, bir kurumun Merkez (HQ) ve Şube (Branch) lokasyonları arasındaki ağ trafiğini ve güvenliğini yönetmek için kurgulanmıştır. Temel odak noktası; cihaz veya hat kesintilerinde trafiğin durmaması ve uzak çalışanların merkeze güvenli erişimidir.

Yapılandırmalar:  

Yüksek Erişilebilirlik (HA): Merkez ofiste iki adet FortiGate cihazı Active-Passive HA modunda yapılandırılmıştır. Bu sayede aktif cihazın arızalanması veya izlenen (monitör edilen) arayüzlerde bağlantı kaybı yaşanması durumunda, sistem otomatik olarak yedek cihaza geçerek (failover) iş sürekliliğini kesintisiz bir şekilde gerçekleştirilmesi sağlanmıştır.


Güvenli Şubeler Arası Bağlantı:

Güvenli Şubeler Arası Bağlantı: HQ ve Branch lokasyonları arasında Site-to-Site IPsec VPN tüneli kurulmuştur. Bu sayede 192.168.x.x ve 172.16.x.x blokları güvenli bir tünel üzerinden haberleşmektedir.


Ağ Segmentasyonu Ve Yedeklilik (VLAN,HSRP): İç ağ; Satış, Finans , İK  VE IT olmak üzere VLAN'lara bölünmüştür. Core Switch'ler üzerinde HSRP ile iç ağ yedekliliği sağlanmıştır.

Uzak Erişim (SSL-VPN):Evden veya dışarıdan çalışan kullanıcılar için FortiClient SSL-VPN portalı yapılandırılmış, kurumsal kaynaklara güvenli erişim simüle edilmiştir.


### Detaylı IP ve VLAN Planlaması

#### Merkez (HQ) Ofis - 
| VLAN ID | Departman / Kullanım | IP Bloğu | Gateway |
| :--- | :--- | :--- | :--- |
| **VLAN 10** | Sale     | 192.168.10.0/24 | 192.168.10.1 |
| **VLAN 20** | Finance  | 192.168.20.0/24 | 192.168.20.1 |  
| **VLAN 30** | HR       | 192.168.30.0/24 | 192.168.30.1 |
| **VLAN 40** | IT       | 192.168.40.0/24 | 192.168.40.1 |

#### Şube (Branch) Ofis - 
| Birim | IP Bloğu | Gateway |
| :--- | :--- | :--- |
| **Şube LAN 100** | 172.16.100.0/24 | 172.16.100.1 |
| **Şube LAN 200** | 172.16.200.0/24 | 172.16.200.1 |

TOPOLOGY:

<img width="1869" height="897" alt="Topology" src="https://github.com/user-attachments/assets/54dbd9bd-0543-4ce4-9b8b-aa586044f0c7" />



Yüksek Erişilebilirlik (HA) Yapılandırması:


FGT-1 HA Yapılandırması:

Aşağıdaki görselde de görüldüğü üzere; sistem Active-Passive HA modunda yapılandırılmıştır. Donanımsal yedekliliğin yanı sıra, kritik ağ bacaklarında yaşanabilecek kesintilere karşı Interface Monitoring özelliği aktif edilmiştir. Bu sayede, izlenen arayüzlerden herhangi birinin 'down' olması durumunda cihaz otomatik olarak failover yaparak trafiği yedek üniteye aktarır.

Yapılandırmada Session Pick-up özelliği etkinleştirilerek, geçiş esnasında aktif TCP oturumlarının ve VPN tünellerinin korunması sağlanmış, böylece kullanıcı tarafında hissedilebilir bir kesinti yaşanmadan iş sürekliliği garanti altına alınmıştır.


<img width="1899" height="908" alt="image" src="https://github.com/user-attachments/assets/2f9ff6f6-7fce-44d8-a4c5-8385390dda1c" />


FGT-2 HA Yapılandırması:

Sistemde yer alan ikinci FortiGate cihazı Passive (Standby) modda beklemektedir. Bu ünite, Primary cihaz ile sürekli bir 'Heartbeat' bağı kurarak konfigürasyon, firewall session tablosu ve IPsec tünel bilgilerini gerçek zamanlı olarak senkronize eder


<img width="1904" height="903" alt="image" src="https://github.com/user-attachments/assets/a37f5099-e372-4359-a32d-a2867df56722" />




HA Senkronizasyon Durumu:

Görselde görüldüğü üzere; FGT-1 ve FGT-2 cihazları 'Synchronized' statüsündedir. Bu durum, her iki ünite arasındaki konfigürasyon veritabanının ve çalışma parametrelerinin birbiriyle tam uyumlu olduğunu göstermektedir.

<img width="1902" height="902" alt="image" src="https://github.com/user-attachments/assets/82f31aa2-cc79-4dde-afcc-7ef4410c0ecf" />


Failover Testleri:



<img width="774" height="910" alt="image" src="https://github.com/user-attachments/assets/753c800a-739b-43c3-bb71-18f1c0c40b2c" />

<img width="1225" height="805" alt="image" src="https://github.com/user-attachments/assets/7dec0dca-ac2d-4ed5-9e6e-90a3a832f850" />

<img width="1889" height="894" alt="image" src="https://github.com/user-attachments/assets/a60fb45c-6f63-40ba-a041-6651f88f4a81" />


<img width="1904" height="908" alt="image" src="https://github.com/user-attachments/assets/4c0f0ad5-14e5-4427-a13c-b210ac227ec2" />





Site-to-Site IPsec VPN


Uzak Erişim (SSL-VPN)

<img width="1877" height="913" alt="image" src="https://github.com/user-attachments/assets/fa1e37a3-5fdb-4513-956b-9995efe4f603" />





Firewall Güvenlik Politikaları (Policies)




