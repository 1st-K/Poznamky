**Cíl:** Získat heslo Wi-Fi sítě _Sablik EU_ metodou 
**Kdy/Kde**:  28. 04. 2026, kybernetický polygon - SSPŠ
**Členové:** Oliver Sobčák, Štěpán Váňa, Ondřej Sejpka
slovníkového útoku. **Parametry hesla:** 8–12 znaků, české slovo (zdroj: sablikdosenatu.cz)


### Úvod
Byly jsme najati Sáblíkovou opozicí abychom našli heslo 

---
#### Postup
**1. Monitorovací mód** Síťová karta byla přepnuta do režimu monitoru pomocí nástroje `airmon-ng`
````bash
sudo airmon-ng start wlan0
````

**2. Skenování sítě** Příkazem `airodump-ng` byla identifikována cílová síť _Sablik EU_ a získali jsme její BSSID.
````bash
sudo airodump-ng wlan0mon
````
**3. Zachycení handshaku** Probíhalo pasivní monitorování provozu až do zachycení WPA autentizačního handshaku.
````bash
sudo airodump-ng wlan0mon --bssid 0A:CD:0E:D1:6C:B5 -w handshakeIHope
````
**4. Generování wordlistu** Slovník byl vygenerován pomocí našeho scriptu.
````bash

````
**5. Slovníkový útok** Nástroj `aircrack-ng` byl spuštěn s kombinací zachyceného handshaku a vygenerovaného wordlistu.
````bash
sudo airmon-ng start wlan0
````
**6. Výsledek** Heslo **vysl3dky** bylo úspěšně nalezeno. Wordlist vygenerovaný pomocí CeWL toto heslo obsahoval a útok proběhl úspěšně.

---
#### Poznámky

Při prvních třech pokusech nebylo heslo nalezeno z důvodu nesprávně sestaveného wordlistu (chybějící leetspeak variace). Po úpravě slovníku byl útok úspěšný.