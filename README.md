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

scp -P 10022 root@localhost:anaconda-ks.cfg .(jelenlegi mappa) vagy Documents (elérési út)  lekérés
puttynál pscp
egy fájlnák ezke jók, több fájlnál winscp

winscp:
localhost 10022 root a
options -> preferences -> panels show hidden files

# uname parancs milyen op rendszer, -a kapcsolóval minden info
# man info linux parancsról, -k keresés, -K teljes leírásban
cat /etc/redhat-release

# 2. labor
id, whoami kik vagyunk
pwd hol vagyunk
ls list, -al összes fájl, ls -lad /root maga a katalógus d directory
ls -li /root/anaconda-ks.cfg inode number
ls -lZ /root/anaconda-ks.cfg kontextus
egyedi azonosító egy eszközön (inode) meghajtónként azonos is lehet
katalógus olan fájl amiben inode és állomány név párok vannak
ls -ali / 1. oszlop inode number, 3. oszlop az adott állományra hivatkozások száma, 
rootnak ugyanaz a /.. /.
/home 6555760
. 6555760
.. 128 (root)
student 5005590 

/home/student 5005590
. 5005590
.. 6555760
dir 2526714

/home/student/dir 2526714
. 2526714
.. 5005590

egy katalógusban 


# stat /root/anaconda-ks.cfg információ
  File: /root/anaconda-ks.cfg
  Size: 1389            Blocks: 8          IO Block: 4096   regular file
Device: 802h/2050d      Inode: 2097284     Links: 1
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Context: system_u:object_r:admin_home_t:s0
Access: 2022-02-06 17:57:19.959927872 +0100
Modify: 2022-02-06 17:57:20.091928325 +0100
Change: 2022-02-06 17:57:20.091928325 +0100
 Birth: 2022-02-06 17:57:19.959927872 +0100

echo Hello > /home/student/greetings.txt
5 betu + új sor karakter 6 bájt
dnf dnf provides xxd dnf csomagkezelő

# hardlink ln /home/student/greetings.txt /home/student/message.txt így a hivatkozás száma megnő, nem tudok különböző lemezeken hardlinket készíteni, se katalógusra

[root@localhost ~]# cat /home/student/greetings.txt
Hello
[root@localhost ~]# cat /home/student/message.txt
Hello

[root@localhost ~]# echo Szevasz! /home/student/message.txt
Szevasz! /home/student/message.txt
így ugyanúgy a greetings.txt-t módosítottam

az állományoknak a nevén kívűl minden attribútuma az inodehoz kötődik,
pl greetings.txt 5568 inode number 2 hivatkozás:
access vector
linkek száma
utolsó modusítás dátuma stb
Hello\n 

chmod a+w /home/student/message.txt

# softlink ln -s vagyis hivatkozás, l betűvel kezdődik a jogosultságot leíró sztringje lrwxrwxrwx 
