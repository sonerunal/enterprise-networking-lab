# 🌐 Enterprise-Networking

Yüksek erişilebilirlik (High Availability), yedeklilik (Redundancy), katmanlı ağ güvenliği ve dinamik yönlendirme protokolleri kullanılarak tasarlanmış, uçtan uca bir **kurumsal ağ (Enterprise Network)** mimarisidir. Ağ yapısı **Erişim (Access)**, **Dağıtım/Çekirdek (Distribution/Core)** ve **Kenar (Edge)** katmanlarından oluşur.

---

## 📑 İçindekiler

- [Öne Çıkan Özellikler](#-öne-çıkan-özellikler)
- [Erişim Bilgileri](#-erişim-bilgileri)
- [Cihazlar ve Yönetim IP Adresleri](#️-cihazlar-ve-yönetim-ip-adresleri)
- [VLAN Veritabanı](#️-vlan-veritabanı)
- [HSRP, Spanning Tree ve OSPF Cost](#-hsrp-spanning-tree-ve-ospf-cost)
- [DHCP Havuzları](#-dhcp-havuzları)
- [Access ve Core Switch Yapılandırması](#-access-ve-core-switch-yapılandırması)
- [Layer 3 Bağlantılar ve Yönlendirme](#️-layer-3-bağlantılar-ve-yönlendirme)
- [Syslog ve NTP](#-syslog-ve-ntp)
- [Ağ Güvenliği ve ACL](#️-ağ-güvenliği-ve-acl)
- [Doğrulama ve Optimizasyon Notları](#️-doğrulama-ve-optimizasyon-notları)

---

## 🚀 Öne Çıkan Özellikler

| Özellik | Açıklama |
|---|---|
| **High Availability & Load Balancing** | VLAN bazlı **HSRP** ve **Spanning Tree (PVST+)** Root Bridge yük paylaşımı yapılandırılmıştır. |
| **Link Aggregation** | Core Switch'ler (CSW1 ve CSW2) arasında **LACP (Active)** ile Layer 2 Trunk **EtherChannel (Port-Channel 1)** oluşturulmuştur. |
| **Dynamic Routing** | CSW1, CSW2 ve kenar yönlendiricisi (R1) arasında **OSPF Area 0** ve VLAN bazlı Cost optimizasyonu uygulanmıştır. |
| **WAN Redundancy** | R1 üzerinde **ISP1** ve **ISP2** olmak üzere çift internet çıkışı ve **Floating Static** varsayılan rotalar ile kesintisiz bağlantı sağlanmıştır. |
| **Wireless & Voice** | **WLC (CAPWAP)** üzerinden merkezi kablosuz ağ yönetimi ve IP Telefonlar için özel **Voice VLAN** altyapısı kurulmuştur. |
| **Security & Monitoring** | **PortFast**, **BPDU Guard**, **DHCP Snooping**, **Extended ACL** ile güvenlik; **NTP** ve **Syslog** ile merkezi izleme sağlanmıştır. |

---

## 🔐 Erişim Bilgileri

> ⚠️ Aşağıdaki bilgiler yalnızca **laboratuvar / simülasyon ortamı** içindir. Gerçek ortamlarda güçlü ve benzersiz parolalar kullanın; kimlik bilgilerini asla herkese açık depolarda paylaşmayın.

| Parametre | Değer |
|---|---|
| SSH Kullanıcı Adı / Parola | `admin` / `admin123` |
| Enable Secret | `ccna` |
| VTY Lines | `login local` |

---

## 🖥️ Cihazlar ve Yönetim IP Adresleri

| Hostname | Interface | IP Adresi | Default Gateway | DNS Server |
|---|---|---|---|---|
| CSW1 | VLAN 10 | 10.0.1.2/24 | 10.1.0.1 | 10.0.6.4 |
| CSW2 | VLAN 10 | 10.0.1.3/24 | 10.2.0.1 | 10.0.6.4 |
| SWK-1 | VLAN 10 | 10.0.1.4/24 | 10.0.1.1 | 10.0.6.4 |
| SWK-2 | VLAN 10 | 10.0.1.5/24 | 10.0.1.1 | 10.0.6.4 |
| SWK-3 | VLAN 10 | 10.0.1.6/24 | 10.0.1.1 | 10.0.6.4 |
| SERVERS | VLAN 10 | 10.0.1.7/24 | 10.0.1.1 | 10.0.6.4 |
| WLC | VLAN 1 | 10.0.0.0/24 | 10.0.0.1 | 10.0.6.4 |

---

## 🗂️ VLAN Veritabanı

| VLAN | Ad | Ağ Adresi | Sanal IP (HSRP) | CSW1 | CSW2 |
|---|---|---|---|---|---|
| 1 | AP-Management (CAPWAP) | 10.0.0.0/24 | 10.0.0.1 | 10.0.0.2 | 10.0.0.3 |
| 10 | Management | 10.0.1.0/24 | 10.0.1.1 | 10.0.1.2 | 10.0.1.3 |
| 20 | Operations | 10.0.2.0/24 | 10.0.2.1 | 10.0.2.2 | 10.0.2.3 |
| 30 | Board Of Directors | 10.0.3.0/24 | 10.0.3.1 | 10.0.3.2 | 10.0.3.3 |
| 40 | IT | 10.0.4.0/24 | 10.0.4.1 | 10.0.4.2 | 10.0.4.3 |
| 50 | QA | 10.0.5.0/24 | 10.0.5.1 | 10.0.5.2 | 10.0.5.3 |
| 60 | Servers | 10.0.6.0/24 | 10.0.6.1 | 10.0.6.2 | 10.0.6.3 |
| 70 | Voice | 10.0.7.0/24 | 10.0.7.1 | 10.0.7.2 | 10.0.7.3 |
| 80 | Wi-Fi Corporate | 10.0.8.0/24 | 10.0.8.1 | 10.0.8.2 | 10.0.8.3 |
| 90 | Wi-Fi Guest | 10.0.9.0/24 | 10.0.9.1 | 10.0.9.2 | 10.0.9.3 |
| 100 | Transit-Voice | 10.100.100.0/30 | - | - | - |

---

## 🔄 HSRP, Spanning Tree ve OSPF Cost

Ağ trafiğini CSW1 ve CSW2 arasında dengeli dağıtmak amacıyla HSRP Active/Standby rolleri, Spanning Tree Root Bridge seçimleri ve OSPF Cost değerleri birbiriyle senkronize edilmiştir.

| VLAN | CSW1 Rolü (HSRP & STP) | CSW2 Rolü (HSRP & STP) | CSW1 OSPF Cost | CSW2 OSPF Cost |
|---|---|---|---|---|
| VLAN 1 | Active | Standby | 10 | 100 |
| VLAN 20 | Active | Standby | 10 | 100 |
| VLAN 30 | Standby | Active | 100 | 10 |
| VLAN 40 | Standby | Active | 100 | 10 |
| VLAN 50 | Active | Standby | 10 | 100 |
| VLAN 60 | Active | Standby | 10 | 100 |
| VLAN 70 | Standby | Active | 100 | 10 |
| VLAN 80 | Active | Standby | 10 | 100 |
| VLAN 90 | Standby | Active | 100 | 10 |

---

## 📡 DHCP Havuzları

Her alt ağda ilk 10 IP adresi, statik yapılandırmalar ve sanal ağ geçitleri için hariç tutulmuştur (`ip dhcp excluded-address`).

| VLAN | Ağ | Hariç Tutulan Aralık |
|---|---|---|
| VLAN 1 | 10.0.0.0/24 | 10.0.0.1 - 10.0.0.10 |
| VLAN 20 | 10.0.2.0/24 | 10.0.2.1 - 10.0.2.10 |
| VLAN 30 | 10.0.3.0/24 | 10.0.3.1 - 10.0.3.10 |
| VLAN 40 | 10.0.4.0/24 | 10.0.4.1 - 10.0.4.10 |
| VLAN 50 | 10.0.5.0/24 | 10.0.5.1 - 10.0.5.10 |
| VLAN 70 | 10.0.7.0/24 | 10.0.7.1 - 10.0.7.10 |
| VLAN 80 | 10.0.8.0/24 | 10.0.8.1 - 10.0.8.10 |
| VLAN 90 | 10.0.9.0/24 | 10.0.9.1 - 10.0.9.10 |

---

## 🔌 Access ve Core Switch Yapılandırması

### Access Switch Port Dağılımı (SWK-1, SWK-2, SWK-3)

| Port | Bağlantı |
|---|---|
| `F0/1 - 3` | IP Telefonlar ve son kullanıcı bilgisayarları (Operations, IT, QA, Board Of Directors) |
| `F0/4` | Access Point (APK1, APK2, APK3) |
| `G0/1 - 2` | CSW1 ve CSW2 Trunk uplink bağlantıları |


<img width="649" height="264" alt="image" src="https://github.com/user-attachments/assets/8992ce66-e019-448d-bedd-f708496fac54" />
<img width="646" height="237" alt="image" src="https://github.com/user-attachments/assets/c5a64535-1437-4357-90b7-f49836ab5e0b" />

### Layer 2 EtherChannel (CSW1 ↔ CSW2)

- **Port-Channel 1** — LACP Active Mode, Trunk


<img width="754" height="360" alt="image" src="https://github.com/user-attachments/assets/2f823d2c-dede-4b17-8300-b61b213d1a32" />

---

## 🛣️ Layer 3 Bağlantılar ve Yönlendirme

### Point-to-Point ve Sunucu Bağlantıları

| Bağlantı | Ağ / Adres | Not |
|---|---|---|
| CSW1 - R1 | 10.1.0.0/30 | |
| CSW2 - R1 | 10.2.0.0/30 | |
| R1 - ISP1 | DHCP | Birincil internet çıkışı |
| R1 - ISP2 | DHCP | Yedek internet çıkışı |
| DNS Server | 10.0.6.4 (255.255.255.0) | |
| Web Server | 10.3.0.2 (255.255.255.252) | |

### Core Switch'ler (CSW1 & CSW2) — OSPF

CSW1, CSW2 ve R1 (FW) arasında **OSPF Area 0** çalıştırılmıştır. Son kullanıcı VLAN'larına gereksiz OSPF Hello paketleri gitmesin diye tüm arayüzler pasif yapılmış, yalnızca R1'e bakan port aktif bırakılmıştır.

```
! CSW1
router ospf 1
 network 10.1.0.2 0.0.0.0 area 0
 passive-interface default
 no passive-interface g0/1     ! R1'e bakan port

! CSW2
router ospf 1
 network 10.2.0.2 0.0.0.0 area 0
 passive-interface default
 no passive-interface g0/1     ! R1'e bakan port
```

### R1 (ASBR) Yapılandırması

**1. Yedekli varsayılan rotalar**

```
ip route 0.0.0.0 0.0.0.0 203.0.113.1        ! ISP1 (birincil)
ip route 0.0.0.0 0.0.0.0 203.0.112.1 5      ! ISP2 (floating static, AD 5)
```

**2. OSPF**

```
router ospf 1
 network 10.1.0.1 0.0.0.0 area 0            ! CSW1 yönü
 network 10.2.0.1 0.0.0.0 area 0            ! CSW2 yönü
 default-information originate
```

**3. NAT (PAT / Overload)**

```
interface range g6/0 - g7/0
 ip nat inside
interface range g8/0 - g9/0
 ip nat outside

access-list 10 permit 10.0.0.0 0.0.0.255
access-list 11 permit 10.0.0.0 0.0.0.255

ip nat inside source list 10 interface g8/0 overload
ip nat inside source list 11 interface g9/0 overload
```

---

## 📊 Syslog ve NTP

Cihazların zaman senkronizasyonu ve sistem günlüklerinin merkezi takibi için kullanılan yapılandırma:

```
ntp server 10.0.6.5
logging host 10.0.6.5
logging on
logging trap debugging
service timestamps log datetime msec
```

---

## 🛡️ Ağ Güvenliği ve ACL

### Extended ACL 100 — Sunucu Erişim Kısıtlaması

| Kural | Açıklama |
|---|---|
| ✅ `permit` DNS ve NTP | DNS ve NTP trafiğine izin verilir |
| ⛔ `deny` any → host 10.0.6.5 | Sunucuya giden diğer tüm trafik engellenir |
| ✅ `permit ip any any` | Geri kalan tüm IP trafiğine izin verilir |

### Extended ACL 101 — SSH Yönetim İzolasyonu

| Kural | Açıklama |
|---|---|
| ✅ `permit` VLAN 40 → SSH | Yalnızca IT departmanı (VLAN 40) SSH erişimine sahiptir |
| ⛔ `deny` diğer VLAN'lar → SSH | Diğer tüm VLAN'ların SSH erişimi engellenir |
| ✅ `permit ip any any` | Geri kalan tüm IP trafiğine izin verilir |

### Layer 2 Switch Güvenliği

- **PortFast & BPDU Guard:** Access switch'lerde son kullanıcı portlarında `spanning-tree portfast` ve `spanning-tree bpduguard enable` aktiftir.
- **DHCP Snooping:** Tüm switch'lerde aktiftir. Uplink portları **trusted**, son kullanıcı portları **untrusted** olarak ayarlanmıştır.

---

## 🛠️ Doğrulama ve Optimizasyon Notları

Topoloji ve yapılandırma incelenirken dikkate alınması gereken teknik notlar:

1. **Merkezi sunucu IP planlaması:** DNS, DHCP, Syslog ve NTP servisleri için cihaz tablolarında DNS `10.0.6.4`, Syslog/NTP ve ACL kurallarında ise `10.0.6.5` kullanılmıştır. Tek bir sunucu arayüzü kullanılıyorsa tüm servisler tek bir IP altında birleştirilebilir.
2. **NAT wildcard mask kapsamı:** R1 üzerindeki `10.0.0.0 0.0.0.255` kuralı yalnızca `10.0.0.0/24` alt ağını kapsar. Tüm alt ağların (10.0.1.0/24 - 10.0.9.0/24) internete çıkabilmesi için wildcard maskesinin `0.0.15.255` veya `0.0.255.255` olarak genişletilmesi önerilir.
3. **Layer 3 switch yönlendirmesi:** CSW1 ve CSW2 Layer 3 yönlendirme (OSPF) yaptığı için, yönetim tablosundaki Default Gateway tanımları yerine R1'in `default-information originate` ile yaydığı varsayılan rotayı kullanır.
