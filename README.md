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

<img width="1869" height="897" alt="Topology" src="https://github.com/user-attachments/assets/54dbd9bd-0543-4ce4-9b8b-aa586044f0c7" />

