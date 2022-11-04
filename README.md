# Fritz!Repeater 1200 flashen

Am einfachsten ist das Flashen von diesem Ger&auml;t mit den bereitgestellten Tools vom Github Repository von mrtuxa [**Link**](https://github.com/mrtuxa/ffl-firmware-flash/releases/tag/final)

*Die meisten Teile dieser Anleitung beziehen sich auf Linux (Ubuntu/Debian)*



## Ben&ouml;tigte Dateien

  ### Tools

- Die Tools sind unte diesem Link zu finden: https://github.com/mrtuxa/ffl-firmware-flash/releases/tag/final

- Hinweis:  *Nur die Tools.zip Datei downloaden*

  

  ### Firmware

  - Aktuell ist es empfehlenswert das Nightly Build zu verwenden (Die Stable Version hat keine Auto Updates)
  - http://download.freifunk-dresden.de/firmware/.nightly/firmware/ipq40xx.generic.fritz-tffs-nand/openwrt-ipq40xx-generic-avm_fritzrepeater-1200-squashfs-sysupgrade.bin

  

1. Ben&ouml;tigte Tools in ein beliebiges Verzeichnis entpacken

2. Installieren eines FTP Server

   

​		`sudo apt install tftpd-hpa`

4. Kopiere die FRITZ1200.bin in den TFTP-Root Ordner (standartm&auml;ßig `/srv/tftp`)

   `cp FRITZ1200.bin` /srv/tftp

5. Konfiguriere die IP-Adressen `192.168.178.10/24` und `192.168.1.70/24`

   (`enp2s0` durch den Namen des entsprechenden Netzwerkinterfaces ersetzen)

   `ip a a 192.168.178.10/24 dev enp2s0 && ip a a 192.168.1.70/24 enp2s0`

6. Verbinde den PC mit dem Router und schalte ihn ein

7. Warte bis ein Link auf dem Netzwerkinterface erkannt wird.

8. Starte U-Boot auf dem Router (Dieser Schritt kann mehrere Versuche beanspruchen sollte dieser Schritt nicht beim ersten mal funktionieren, bitte das Ger&auml;t neustarten und den Command erneut ausf&uuml;hren)

   `./eva_ramboot.py --offset 0x85000000 192.168.178.1 uboot-fritz1200.bin`

9. Nach einigen Minuten sollter der Router per SSH auf 192.168.178.1 erreichbar sein

10. Logge dich per SSH auf dem Router ein

    `ssh root@192.168.1.1`

11. Kopiere die uboot-fritz1200.bin auf den Router

​      `scp /path/to/directory/uboot-fritz1200.bin root@192.168.1.1:/tmp`



12. Kopiere das Sysupgrade-Firmware Image auf den Router

​      `scp /path/to/directory/openwrt-ipq40xx-generic-avm_fritzrepeater-1200-squashfs-sysupgrade.bin root@192.168.1.1:/tmp`

13. Installiere U-Boot mit folgendem Command in der SSH-Sitzung

    `mtd write /tmp/uboot-fritz1200.bin uboot0 && mtd write /tmp/uboot-fritz1200.bin uboot1`

14. L&ouml;sche das alte Betriebssystem mit dem folgendem Command in der SSH-Sitzung

​      `ubirmvol /dev/ubi0 --name=avm_filesys_0 && ubirmvol /dev/ubi0 --name=avm_filesys_1` 



15. Installiere die Firmware mit folgendem Command in der SSH-Sitzung

​      `sysupgrade -n /tmp/openwrt-ipq40xx-generic-avm_fritzrepeater-1200-squashfs-sysupgrade.bin`





