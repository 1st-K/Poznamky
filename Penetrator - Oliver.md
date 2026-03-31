**Členové:** Oliver Sobčák **Datum:** 30. 3. 2026 **Místo:** 'ssh -o UserKnownHostsFile=/dev/null student@3.66.227.169 -p 22' | heslo: Klasifikováno
### 1. Úvod

Dostal jsem za úkol najít slabinu za pomocí dhcp.
Moje očekávání se splnily a jsou rozepsány na následující straně.

## 2. Scope 

	Pro koho: Haxagon.cz.Sro Cíl: Najít slabinu*

---
## 3. Postup

### 3.1 Připojení
Sshnul jsem se

### 3.2 installace package
sudo apt update
sudo apt install isc-dhcp-server neovim nmap tcpdump termshark
### 3.3  povolení forwardování

sudo su
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -P FORWARD ACCEPT
iptables -t nat -A POSTROUTING -j MASQUERADE
su student



### 3.4 Konfigurace dhcp


Do /etc/dhcp/dhcpd.conf jsem dal tuto base konfiguraci od ubuntu:

```bash
# minimal sample /etc/dhcp/dhcpd.conf
default-lease-time 600;
max-lease-time 7200;
    
subnet 192.168.1.0 netmask 255.255.255.0 {
 range 192.168.1.150 192.168.1.200;
 option routers 192.168.1.254;
 option domain-name-servers 192.168.1.1, 192.168.1.2;
}
```
sudo systemctl enable --now isc-dhcp-server

### 3.5
Zkusil jsem prvně po tomto analyzovat provoz pomocí 

sudo termshark -i any 

Kde jsem nic  nenašel

### 3.6
Uvědomil jsem si chybu a pomocí 'ip a show eth1'
jsem v /etc/dhcp/dhcpd.conf upravil na tento blok

```bash

default-lease-time 600;
max-lease-time 7200;

subnet 192.168.0.0 netmask 255.255.255.0 {
 range 192.168.0.200 192.168.0.240;
 option routers 192.168.0.159;
 option domain-name-servers 8.8.8.8;
}
```
* Časování (lease-time): Zapůjčení IP na 10-120 minut. Rychlá obměna nutí zařízení přijmout útočníkova pravidla.
* Subnet a Range: Vymezuje prostor útoku (192.168.0.0/24) a zásobu IP adres k rozdání.
* Option routers (.159): Kritický bod. Lžeme obětem, že jsme hlavní router. Jejich provoz do internetu teče přes nás (odposlech).
* DNS (8.8.8.8): Funkční DNS od Googlu. Udržuje iluzi normálního stavu a oddaluje odhalení.



sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server
sudo termshark -i eth1

kde po prozkoumání packetů jsem našel vlajku:
'haxagon{xxxxxx}'



---

## 4. Návrhy na zabezpečení

**Objektivní řešení:**

1. **DHCP Snooping:** Průmyslový standard. Konfigurace L2 switchů k aktivnímu zahazování `DHCPOFFER` paketů z nedůvěryhodných (untrusted) portů.
    
2. **802.1X (NAC):** Striktní kontrola přístupu k síti. Útočníkovo zařízení bez platného certifikátu nebo RADIUS ověření nezíská přístup do datové VLAN.

### Závěr

Penetrační test úspěšně prokázal kritickou zranitelnost nechráněné L2 vrstvy. Spoléhat na pouhou rychlost odpovědi legitimního DHCP serveru je architektonická naivita. Podvržením konfigurace přes vlastní rogue DHCP byl bez odporu ustaven Man-in-the-Middle útok, což vedlo k plné kompromitaci zachyceného TCP provozu a úspěšné exfiltraci vlajky.

Síť bez aktivního nasazení DHCP Snoopingu nebo 802.1X je z podstaty nedůvěryhodná. Zavedení navržených mitigací je naprosté minimum pro přesun sítě z kategorie „hračka“ do kategorie „produkční prostředí“.