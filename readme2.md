# Enterprise-Networking

A resilient, scalable, and secure campus network architecture simulated on Cisco Packet Tracer, designed in alignment with enterprise-grade and CCNA-level networking standards[cite: 1, 2].

---

## 📑 İçindekiler (Table of Contents)
- [Ağ Mimarisi ve Topoloji](#-ağ-mimarisi-ve-topoloji)
- [Temel Özellikler](#-temel-özellikler)
- [Cihaz Erişim Bilgileri](#-cihaz-erişim-bilgileri)
- [IP Adresleme ve VLAN Planı](#-ip-adresleme-ve-vlan-planı)
  - [Cihaz Yönetim Tablosu](#cihaz-yönetim-tablosu)
  - [VLAN ve HSRP / STP Dağılımı](#vlan-ve-hsrp--stp-dağılımı)
  - [Layer 3 P2P ve Sunucu Bağlantıları](#layer-3-p2p-ve-sunucu-bağlantıları)
- [Konfigürasyon Detayları](#-konfigürasyon-detayları)
  - [DHCP Havuzları](#1-dhcp-havuzları)
  - [Yönlendirme (OSPF & Floating Static Route)](#2-yönlendirme-ospf--floating-static-route)
  - [NAT (Network Address Translation)](#3-nat-yapılandırması)
  - [Anahtarlama ve Port Dağılımı (Switching)](#4-anahtarlama-ve-port-dağılımı)
  - [Ağ Güvenliği ve Erişim Denetimi (Security & ACL)](#5-ağ-güvenliği-ve-erişim-denetimi)
  - [Zaman ve Log Yönetimi (NTP & Syslog)](#6-zaman-ve-log-yönetimi)
- [Doğrulama ve Ekran Görüntüleri](#-doğrulama-ve-ekran-görüntüleri)

---

## 📌 Ağ Mimarisi ve Topoloji

Ağ; çekirdek (Core/Distribution) seviyesinde tam yedekli iki adet Layer 3 switch (CSW1, CSW2), uç seviyede Layer 2 access switch'ler (SWK-1, SWK-2, SWK-3), bir ASBR kenar yönlendirici (R1) ile çift ISP çıkışından meydana gelmektedir[cite: 1, 2].

<p align="center">
  <img src="https://github.com/user-attachments/assets/ba04d307-3cab-4ed2-af63-d8eb18c1a46d" alt="Topoloji Şeması" width="850">
</p>

---

## 🚀 Temel Özellikler

* **High Availability (HA):** HSRP ile VLAN bazlı Aktif/Yedek yük paylaşımı.
* **L2 Link Aggregation:** CSW1 ve CSW2 arasında LACP protokolü ile Port-Channel 1 (Trunk).
* **Dinamik Yönlendirme:** CSW1, CSW2 ve R1 arasında OSPF Area 0, pasif arayüz optimizasyonu ve maliyet (cost) metrik ayarlamaları.
* **Yedekli WAN:** ISP1 (Primary) ve ISP2 (Backup) için Floating Static Route yapılandırması.
* **Katman 2 Güvenliği:** Uç portlarda Spanning Tree PortFast, BPDU Guard ve ağ genelinde DHCP Snooping.
* **Erişim Denetimi:** Genişletilmiş ACL'ler ile SSH yönetim kısıtlaması ve merkezi sunucu koruması.

---

## 🔑 Cihaz Erişim Bilgileri

| Parametre | Değer |
| :--- | :--- |
| **SSH Username** | `admin` |
| **SSH Password** | `admin123` |
| **Enable Secret** | `ccna` |
| **VTY Lines** | `login local` |

---

## 🌐 IP Adresleme ve VLAN Planı

### Cihaz Yönetim Tablosu

| Hostname | Interface | IP Address | Subnet Mask | Default Gateway | DNS Server |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **CSW1** | VLAN 10 | `10.0.1.2` | `/24` | `10.1.0.1` | `10.0.6.4` |
| **CSW2** | VLAN 10 | `10.0.1.3`[cite: 1] | `/24`[cite: 1] | `10.2.0.1`[cite: 1] | `10.0.6.4`[cite: 1] |
| **SWK-1** | VLAN 10[cite: 1] | `10.0.1.4`[cite: 1] | `/24`[cite: 1] | `10.0.1.1`[cite: 1] | `10.0.6.4`[cite: 1] |
| **SWK-2** | VLAN 10[cite: 1] | `10.0.1.5`[cite: 1] | `/24`[cite: 1] | `10.0.1.1`[cite: 1] | `10.0.6.4`[cite: 1] |
| **SWK-3** | VLAN 10[cite: 1] | `10.0.1.6`[cite: 1] | `/24`[cite: 1] | `10.0.1.1`[cite: 1] | `10.0.6.4`[cite: 1] |
| **SERVERS** | VLAN 10[cite: 1] | `10.0.1.7`[cite: 1] | `/24`[cite: 1] | `10.0.1.1`[cite: 1] | `10.0.6.4`[cite: 1] |
| **WLC** | VLAN 1[cite: 1] | `10.0.0.0`[cite: 1] | `/24`[cite: 1] | `10.0.0.1`[cite: 1] | `10.0.6.4`[cite: 1] |

### VLAN ve HSRP / STP Dağılımı

| VLAN | İsim | Ağ Adresi | Virtual IP (VIP) | CSW1 IP | CSW2 IP | HSRP Rolü (CSW1 / CSW2) |
| :---: | :--- | :--- | :--- | :--- | :--- | :---: |
| **1** | Ap-Management(CAPWAP)[cite: 1] | `10.0.0.0/24`[cite: 1] | `10.0.0.1`[cite: 1] | `10.0.0.2`[cite: 1] | `10.0.0.3`[cite: 1] | Active / Standby[cite: 1] |
| **10** | Management[cite: 1] | `10.0.1.0/24`[cite: 1] | `10.0.1.1`[cite: 1] | `10.0.1.2`[cite: 1] | `10.0.1.3`[cite: 1] | — |
| **20** | Operations[cite: 1] | `10.0.2.0/24`[cite: 1] | `10.0.2.1`[cite: 1] | `10.0.2.2`[cite: 1] | `10.0.2.3`[cite: 1] | Active / Standby[cite: 1] |
| **30** | Board Of Directors[cite: 1] | `10.0.3.0/24`[cite: 1] | `10.0.3.1`[cite: 1] | `10.0.3.2`[cite: 1] | `10.0.3.3`[cite: 1] | Standby / Active[cite: 1] |
| **40** | IT[cite: 1] | `10.0.4.0/24`[cite: 1] | `10.0.4.1`[cite: 1] | `10.0.4.2`[cite: 1] | `10.0.4.3`[cite: 1] | Standby / Active[cite: 1] |
| **50** | QA[cite: 1] | `10.0.5.0/24`[cite: 1] | `10.0.5.1`[cite: 1] | `10.0.5.2`[cite: 1] | `10.0.5.3`[cite: 1] | Active / Standby[cite: 1] |
| **60** | Servers[cite: 1] | `10.0.6.0/24`[cite: 1] | `10.0.6.1`[cite: 1] | `10.0.6.2`[cite: 1] | `10.0.6.3`[cite: 1] | Active / Standby[cite: 1] |
| **70** | Voice[cite: 1] | `10.0.7.0/24`[cite: 1] | `10.0.7.1`[cite: 1] | `10.0.7.2`[cite: 1] | `10.0.7.3`[cite: 1] | Standby / Active[cite: 1] |
| **80** | Wi-Fi Corporate[cite: 1] | `10.0.8.0/24`[cite: 1] | `10.0.8.1`[cite: 1] | `10.0.8.2`[cite: 1] | `10.0.8.3`[cite: 1] | Active / Standby[cite: 1] |
| **90** | Wi-Fi Guest[cite: 1] | `10.0.9.0/24`[cite: 1] | `10.0.9.1`[cite: 1] | `10.0.9.2`[cite: 1] | `10.0.9.3`[cite: 1] | Standby / Active[cite: 1] |
| **100** | Transit-Voice[cite: 1] | `10.100.100.0/30`[cite: 1] | — | — | — | Layer 3 Transit[cite: 1] |

### Layer 3 P2P ve Sunucu Bağlantıları

| Bağlantı Noktaları | Ağ / IP Adresi | Subnet Mask | Açıklama |
| :--- | :--- | :--- | :--- |
| **CSW1 ↔ R1** | `10.1.0.0/30`[cite: 1] | `255.255.255.252` | Point-to-Point Uplink[cite: 1] |
| **CSW2 ↔ R1** | `10.2.0.0/30`[cite: 1] | `255.255.255.252` | Point-to-Point Uplink[cite: 1] |
| **R1 ↔ ISP1** | DHCP[cite: 1] | — | Primary Internet Hattı[cite: 1] |
| **R1 ↔ ISP2** | DHCP[cite: 1] | — | Secondary (Backup) Internet Hattı[cite: 1] |
| **DNS Server** | `10.0.6.4`[cite: 1] | `255.255.255.0`[cite: 1] | DNS Hizmeti[cite: 1] |
| **Syslog & NTP Server** | `10.0.6.5`[cite: 1] | `255.255.255.0` | Merkezi Log ve Zaman Sunucusu[cite: 1] |
| **Web Server** | `10.3.0.2`[cite: 1] | `255.255.255.252`[cite: 1] | DMZ / Dış Servis[cite: 1] |

---

## ⚙️ Konfigürasyon Detayları

### 1. DHCP Havuzları

Ağdaki istemciler için DHCP tanımlamalarında ilk 10 IP adresi statik cihazlar için rezerve edilmiştir[cite: 1]:

```text
VLAN 1  : 10.0.0.0/24  (Exclude: 10.0.0.1 - 10.0.0.10)
VLAN 20 : 10.0.2.0/24  (Exclude: 10.0.2.1 - 10.0.2.10)
VLAN 30 : 10.0.3.0/24  (Exclude: 10.0.3.1 - 10.0.3.10)
VLAN 40 : 10.0.4.0/24  (Exclude: 10.0.4.1 - 10.0.4.10)
VLAN 50 : 10.0.5.0/24  (Exclude: 10.0.5.1 - 10.0.5.10)
VLAN 70 : 10.0.7.0/24  (Exclude: 10.0.7.1 - 10.0.7.10)
VLAN 80 : 10.0.8.0/24  (Exclude: 10.0.8.1 - 10.0.8.10)
VLAN 90 : 10.0.9.0/24  (Exclude: 10.0.9.1 - 10.0.9.10)
