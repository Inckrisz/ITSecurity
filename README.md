# 1. labor
# ITSecurity, Szükséges eszközök

almalinux
putty
winscp

vbox file -> import appliances -> ova fájl
login: root password: a
capturenél jobb control

# poweroff kikapcsolás
Snapshot visszaállítási pont, Take kikapcsolt állapotban

Virtualbox settings -> network -> adapter 1 advanced -> port forwarding -> beállítani

putty -> 10022 port, localhost, save session -> open
ugyanaz név, jelszó

win cmd ssh -p 10022 root@localhost
logout close session

scp -P 10022 root@localhost:anaconda-ks.cfg .(jelenlegi mappa) vagy Documents (elérési út)  
