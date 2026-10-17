Enterprise Campus Network Design & ImplementationBu proje; kurumsal ölçekte yedekli, güvenli ve yüksek erişilebilirlikli (High Availability) bir kampüs ağı mimarisini simüle etmektedir. Ağ yapısı Cisco Packet Tracer üzerinde modellenmiş olup Layer 2 ve Layer 3 yedeklilik, dinamik yönlendirme, güvenli erişim protokolleri ve uç nokta güvenliği pratiklerini içerir.📌 Ağ Topolojisi🚀 Öne Çıkan Özellikler ve ProtokollerLayer 2 Yedeklilik & Optimizasyon: LACP tabanlı EtherChannel (Port-Channel 1) ve STP Root Bridge optimizasyonu.Layer 3 Gateway Yedekliliği: HSRP (Hot Standby Router Protocol) ile VLAN bazlı aktif/yedek yük paylaşımı.Dinamik Yönlendirme: Çekirdek anahtarlar (CSW1, CSW2) ve ASBR (R1) arasında OSPF Area 0 konfigürasyonu.WAN & İnternet Erişimi: ISP1 ve ISP2 üzerinden Floating Static Route ile yedekli internet çıkışı ve NAT Overload (PAT).Ağ Güvenliği: PortFast, BPDU Guard, DHCP Snooping ve yönetim trafiği kısıtlaması için genişletilmiş ACL kuralları.Kablosuz Ağ & Servisler: WLC (Wireless LAN Controller), merkezi DNS, DHCP, NTP ve Syslog sunucuları.🔐 Yönetim ve Erişim Bilgileri (Credentials)ParametreDeğerSSH UsernameadminSSH Passwordadmin123Enable SecretccnaVTY Lineslogin local, transport input ssh📋 IP Adresleme PlanıCihaz Yönetim Adresleri (Device Addressing)HostnameInterface / VLANIP AddressSubnet MaskDefault GatewayDNS ServerCSW1VLAN 1010.0.1.2/2410.1.0.110.0.6.4CSW2VLAN 1010.0.1.3/2410.2.0.110.0.6.4SWK-1VLAN 1010.0.1.4/2410.0.1.110.0.6.4SWK-2VLAN 1010.0.1.5/2410.0.1.110.0.6.4SWK-3VLAN 1010.0.1.6/2410.0.1.110.0.6.4SERVERS-SWVLAN 1010.0.1.7/2410.0.1.110.0.6.4WLCManagement10.0.0.10/2410.0.0.110.0.6.4VLAN ve HSRP VeritabanıVLAN IDVLAN AdıSubnetSanal Ağ Geçidi (VIP)CSW1 IP (HSRP)CSW2 IP (HSRP)HSRP Rolü (CSW1 / CSW2)1AP-Management10.0.0.0/2410.0.0.110.0.0.210.0.0.3Active / Standby10Management10.0.1.0/2410.0.1.110.0.1.210.0.1.3Active / Standby20Operations10.0.2.0/2410.0.2.110.0.2.210.0.2.3Active / Standby30Board Of Directors10.0.3.0/2410.0.3.110.0.3.210.0.3.3Standby / Active40IT10.0.4.0/2410.0.4.110.0.4.210.0.4.3Standby / Active50QA10.0.5.0/2410.0.5.110.0.5.210.0.5.3Active / Standby60Servers10.0.6.0/2410.0.6.110.0.6.210.0.6.3Active / Standby70Voice10.0.7.0/2410.0.7.110.0.7.210.0.7.3Standby / Active80Wi-Fi Corporate10.0.8.0/2410.0.8.110.0.8.210.0.8.3Active / Standby90Wi-Fi Guest10.0.9.0/2410.0.9.110.0.9.210.0.9.3Standby / Active100Transit-Voice10.100.100.0/30———Layer 3 LinkLayer 3 P2P Bağlantıları ve SunucularCSW1 ↔ R1: 10.1.0.0/30 (CSW1: 10.1.0.2, R1: 10.1.0.1)CSW2 ↔ R1: 10.2.0.0/30 (CSW2: 10.2.0.2, R1: 10.2.0.1)DNS Server: 10.0.6.4/24Syslog & NTP Server: 10.0.6.5/24Web Server: 10.3.0.2/30⚙️ Yapılandırma Detayları1. DHCP Havuzu YapılandırmasıAğdaki istemciler için DHCP yapılandırmasında ilk 10 IP adresi statik cihazlar için rezerve edilmiştir:PlaintextVLAN 1  : 10.0.0.0/24 | Exclude: 10.0.0.1 - 10.0.0.10
VLAN 20 : 10.0.2.0/24 | Exclude: 10.0.2.1 - 10.0.2.10
VLAN 30 : 10.0.3.0/24 | Exclude: 10.0.3.1 - 10.0.3.10
VLAN 40 : 10.0.4.0/24 | Exclude: 10.0.4.1 - 10.0.4.10
VLAN 50 : 10.0.5.0/24 | Exclude: 10.0.5.1 - 10.0.5.10
VLAN 70 : 10.0.7.0/24 | Exclude: 10.0.7.1 - 10.0.7.10
VLAN 80 : 10.0.8.0/24 | Exclude: 10.0.8.1 - 10.0.8.10
VLAN 90 : 10.0.9.0/24 | Exclude: 10.0.9.1 - 10.0.9.10
2. Yönlendirme (Routing)OSPF Area 0CSW1 ve CSW2 üzerinde pasif arayüz prensibi uygulanmış, yalnızca omurga yönlendiriciye (R1) giden port aktif bırakılmıştır.Cisco CLI! CSW1 OSPF
router ospf 1
 passive-interface default
 no passive-interface GigabitEthernet0/1
 network 10.1.0.2 0.0.0.0 area 0

! CSW2 OSPF
router ospf 1
 passive-interface default
 no passive-interface GigabitEthernet0/1
 network 10.2.0.2 0.0.0.0 area 0
OSPF Cost Metrik DağılımıTrafiği istenen anahtarlar üzerinden optimize etmek amacıyla VLAN SVI portlarına uygulanan OSPF maliyetleri:VLANCSW1 CostCSW2 CostTercih Edilen Yol20, 50, 60, 80, 110100CSW130, 40, 70, 9010010CSW2Kenar Yönlendirici (R1 / ASBR)Cisco CLI! Varsayılan Rota (Floating Static Route - Yedekli ISP)
ip route 0.0.0.0 0.0.0.0 203.0.113.1        ! ISP1 (Primary)
ip route 0.0.0.0 0.0.0.0 203.0.112.1 5      ! ISP2 (Backup)

! OSPF Bilgi Yayılımı
router ospf 1
 network 10.1.0.1 0.0.0.0 area 0
 network 10.2.0.1 0.0.0.0 area 0
 default-information originate

! Dynamic NAT Overload (PAT)
interface range GigabitEthernet6/0 - 7/0
 ip nat inside
interface range GigabitEthernet8/0 - 9/0
 ip nat outside

access-list 10 permit 10.0.0.0 0.0.255.255
ip nat inside source list 10 interface GigabitEthernet8/0 overload
ip nat inside source list 10 interface GigabitEthernet9/0 overload
3. Anahtarlama & Port Yapılandırması (Switching)EtherChannel: CSW1 ve CSW2 arasında LACP (mode active) kullanılarak Port-Channel 1 oluşturulmuş ve Trunk moduna alınmıştır.Erişim Anahtarları Port Dağılımı:Fa0/1 - Fa0/3: IP Phones & PC DataFa0/4: Access Point (CAPWAP / VLAN 1)Gi0/1 - Gi0/2: Uplink Portları (CSW1 & CSW2)4. Güvenlik ve Servisler (Security & Services)Katman 2 Güvenliği (Uç Switchler)Cisco CLI! Portfast ve BPDU Guard
interface range FastEthernet0/1 - 24
 spanning-tree portfast
 spanning-tree bpduguard enable

! DHCP Snooping
ip dhcp snooping
ip dhcp snooping vlan 1,20,30,40,50,70,80,90

! Uplink Portları (Trusted)
interface range GigabitEthernet0/1 - 2
 ip dhcp snooping trust
Erişim Kontrol Listeleri (Extended ACL)Cisco CLI! ACL 100: Kritik Sunucu İzolasyonu
ip access-list extended 100
 permit udp any host 10.0.6.5 eq domain
 permit udp any host 10.0.6.5 eq ntp
 deny   ip any host 10.0.6.5
 permit ip any any

! ACL 101: Güvenli Yönetim Erişimi (Yalnızca IT VLAN)
ip access-list extended 101
 permit tcp 10.0.4.0 0.0.0.255 any eq 22
 deny   tcp any any eq 22
 permit ip any any
Log ve Zaman Senkronizasyonu (Syslog & NTP)Cisco CLIntp server 10.0.6.5
logging host 10.0.6.5
logging trap debugging
service timestamps log datetime msec
logging on
📷 Doğrulama ve Ekran GörüntüleriSWK-1 VLAN & Trunk DurumuCSW1 - CSW2 LACP EtherChannel
