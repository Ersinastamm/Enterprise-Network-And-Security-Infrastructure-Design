# Enterprise-Network-And-Security-Infrastructure-Design
EVE-NG üzerinde tasarlanmış; FortiGate A-P HA , Site-to-Site IPsec ve SSL VPN teknolojilerini içeren yedekli kurumsal ağ ve güvenlik altyapısı tasarımı.


Proje Özeti: 

EVE-NG simülasyon ortamında, kurumsal bir şirketin Merkez (HQ) ve Şube (Branch) lokasyonları arasındaki ağ ve güvenlik altyapısını uçtan uca kurgulamak amacıyla hazırlanmıştır. Proje, sadece bağlantı kurmayı değil, donanımsal ve hat bazlı yedekliliği (redundancy) ve güvenli uzak erişimi hedeflemektedir.


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


