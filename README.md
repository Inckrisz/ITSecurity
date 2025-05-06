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


# stat /root/anaconda-ks.cfg információ, -L felbontja a hivatkozást
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

# softlink ln -s vagyis hivatkozás, l betűvel kezdődik a jogosultságot leíró sztringje lrwxrwxrwx ez ne m ad plusz jogosultságot az tudja olvasni, aki a hivatkozást is tudja
ha törlöm, akkor no such file directory error kaphatok cat-eléskor

chmod +x-r /home/student/dir

# 3. labor
uname student pass student student felhasználó

set uid (első x) | set gid (második x) | sticky (harmadik x)
sst
421
   owner owner
   user  group others
   rwx   rwx   rxw
   421   421   421

kis s ha alapból volt x jog, amúgy nagy S

echo Hello > /home/student/greeti                ngs.txt
student others

chmod 664 /home/student/greetings.txt
u=r user read, go+x

[root@localhost ~]# chmod u=r,go+x /home/student/greetings.txt
[root@localhost ~]# chmod 662 /home/student/greetings.txt
[root@localhost ~]# chmod 000 /home/student/greetings.txt

chmod o=r /home/student/dir1/

[student@localhost ~]$ ls -al dir1/
ls: cannot access 'dir1/.': Permission denied
ls: cannot access 'dir1/..': Permission denied
ls: cannot access 'dir1/greetings.txt': Permission denied
total 0
d????????? ? ? ? ?            ? .
d????????? ? ? ? ?            ? ..
-????????? ? ? ? ?            ? greetings.txt

chmod 222 /home/student/dir1/greetings.txt
csak írni tudja a student

umask
0002

d: 7 7 7 0002 -> 775 ha a student csinál directoryt
-: 6 6 6 0002 -> 664 ha a student más fájlt csinál

# 1.
    sticky bit ha be volt állítva egy állományon akkor megmaradt a memóriában (erre régen volt szükség)
    katalógus esetén akkor törölni csak az állomány tulajdonosa tud a katalógusból törölni állományt pl /tmp
    chmod o+t /home/student/kat/
    drwxrwxrwt. 2 root    root     19 Mar  4 13:24 kat
    [student@localhost ~]$ rm kat/2.txt
    rm: remove write-protected regular empty file 'kat/2.txt'? y
    rm: cannot remove 'kat/2.txt': Operation not permitted
    
    chown student:student /home/student/kat/2.txt
    rm kat/2.txt
    most már le lehet törölni mert student owner lett 

# 2.
    ha olyan katalógusba teszek ahol be van állítva a set gid, akkor az állományt tulajdonló csoport az ugyanaz lesz mint a katalógust tulajdonló cosport

# 3.
    annak a flehasználónak a jogosítványait örökli aki a futtatható állományon bejegyzett tulajdonos ill. felhasználói csoport


    set uid | set gid | sticky
d:     -       2.         1.
-:     3.                   -


# 4. labor
ACL bejegyzések
.zshrc 

0-s uid rendszergazd


[root@localhost ~]# getfacl ./secret-message.txt
# file: secret-message.txt
# owner: root
# group: root
user::rw-
group::r--
other::r--

umask mondja meg hogy milyen jogosultsággal jönnek létre a fájlok

setfacl 
-m (hozzáadni v felülírni acl bejegyzést) user: <username>: rw group: csoport : rw

-b -> törli az aclt
-x -> acl bejegyzés törlése  
-n -> a maszk marad   


setfacl -m user:student:r ./secret-message.txt

[root@localhost ~]# getfacl ./secret-message.txt
# file: secret-message.txt
# owner: root
# group: root
user::rw-
user:student:r--
group::---
mask::r--
other::---

maszk összevagyolása a 2 kijelölt sornak, vagyis user:student:r-- és group::---

[root@localhost ~]# setfacl -m g:users:w ./secret-message.txt
[root@localhost ~]# getfacl ./secret-message.txt
# file: secret-message.txt
# owner: root
# group: root
user::rw-
user:student:r--
group::---
group:users:-w-
mask::rw-
other::---

itt pedig user:student:r-- group::--- group:users:-w- a maszk

[root@localhost ~]# setfacl -m g::x /secret-message.txt
[root@localhost ~]# getfacl /secret-message.txt
getfacl: Removing leading '/' from absolute path names
# file: secret-message.txt
# owner: root
# group: root
user::rw-
user:student:r--
group::--x
group:users:-w-
mask::rwx
other::---

[root@localhost ~]# usermod -G users student
[root@localhost ~]# id student
uid=1000(student) gid=1000(student) groups=1000(student),100(users)

[root@localhost ~]# chown student:users /secret-message.txt
[root@localhost ~]# getfacl /secret-message.txt
getfacl: Removing leading '/' from absolute path names
# file: secret-message.txt
# owner: student
# group: users
user::rw-
user:operator:rw-
user:student:r--
group::--x
group:users:-w-
mask::rwx
other::---

man ACL

ha 11. bit hianyzik nem tamogatja az aclt, ha '.' akkor támogatja de nincs , ha + akkor támogatja és be is van állítva
# acl bejegyzéssel olvasni tudja az adott operátor feladat
# távolítsunk el minden acl bejegyzést feladat

dir katalogust csinalva arra aclt rakva nem örökli meg pl az a.txt

ha a katalógusban létrehozott állományokra is érvényes legyen
d:
[root@localhost ~]# setfacl -m default:user:student:rw /dir/a.txt
setfacl: /dir/a.txt: Only directories can have default ACLs
[root@localhost ~]# setfacl -m default:user:student:rw /dir
[root@localhost ~]# ls -ld /dir
drwxr-x---+ 2 root root 19 Mar 11 13:10 /dir

[root@localhost ~]# ls -l /dir/
total 8
-rw-r--r--. 1 root root 2 Mar 11 13:10 a.txt
-rw-rw----+ 1 root root 2 Mar 11 13:14 b.txt
[root@localhost ~]# getfacl /dir/b.txt
getfacl: Removing leading '/' from absolute path names
# file: dir/b.txt
# owner: root
# group: root
user::rw-
user:student:rw-
group::---
mask::rw-
other::---

# HF man ACL algoritmus  

# Security Enhanced Policy

dnf update
dnf install httpd

ha nem elég lemez almalinux 8.5 gpg key update
https://serverfault.com/questions/1144827/alma-linux-8-update-fails-for-any-package-with-gpg-keys-check-fail
sudo rpm --import https://repo.almalinux.org/almalinux/RPM-GPG-KEY-AlmaLinux

# 5. labor find parancs

# 6. labor

e emergency console root előtt rd.break et beírom

utána mount -o remount, rw /sysroot/
chroot /sysroot/

passwd vmire átírjuk a jelszót

touch /.autorelabel

exit 

exit

kb 2 perc utána rebootol

utána enter az almalinuxra

bejelentkezés új jelszóval

https://exam.progcont.eu/docs/IT-Security/deprecated/reset-root-password.mp4

lsblk - block deviceok listázása
vboxban storage
sda commandlineban az a -disk001.vdi
sda 2 részre van osztva
003.vdi az sr0

lsblk -f egyedi azonosítókkal is kiírja a block deviceokat


stat /dev/zero character special állomány, kiolvasni lehet belőle tetszőlegesen sok biteot, de ezek mind 0 byteok
dd if=/dev/zero of=/disk0 bs=4096 count=10000
dd if=/dev/zero of=/disk1 bs=4096 count=10000
ezek játsszák a merevlemez szerepét

losetup -l loopdeviceok listázása
losetup -f 
losetup -f /disk0 
ezután lsblk loop0 is látszódik

mkfs make file system (fájl rendszer csinálása a loop0hoz)
különbség hogy hogyan ábrázolják a katalógus hierarchiákat
mkfs.ext2 /dev/loop0
így a loop0 kap egy azonosítót (lsblk -f)

csatoljuk föl
mkdir /mnt/d0
mount /dev/loop0 /mnt/d0/
find /mnt

echo PASSWORD=1234 > /mnt/d0/secret.txt

törlés
umount /mnt/d0 
losetup -d /dev/loop0
losetup -l 

mc (midnight commander)
disk0 még benne van a /-ban és benne van a PASSWORD=1234
tehát ha valaki hozzáférne a törölt lemezhez akkor is ki tudja olvasni

losetup /dev/loop1 /disk1
losetup -l 

titkosítás keretrendszer
cryptsetup luksFormat /dev/loop1 megkérdezi hogy formázni akarjuk-e yes
8 betűs kisbetű nagybetűs jelszó
csak azok tudják használni ezt a meghajtót akik ismerik a meghajtót

cryptsetup luksOpen /dev/loop1 secret

mkfs.ext2 /dev/mapper/secret 
lsblk -f 

mkdir /mnt/d1
mount /dev/mapper/secret /mnt/d1/
lsblk -f 

find /mnt/

beleírjuk megint
echo PASSWORD > /mnt/d1/secret.txt
find /mnt/ 

törlés
umount /mnt/d1/
cryptsetup luksClose secret
losetup -d /dev/loop1 

megint mc-vel ellenőrizzük csak most a disk1-et, de titkosítva van
https://exam.progcont.eu/docs/IT-Security/deprecated/slides-hu/encrypted-device.pdf


# 7. labor

sda, sdb sdc 
mbr gpt
fdisk gdisk el lehet partíciókat
dnf install gdisk
gdisk /dev/sdb 
p kilistázza a partíciókat
n új partíció alapok aztán w -vel írjuk
utána gdisk /dev/sdc ugyanez

volume groupot csinálunk az sdb és sdcből  (sdb1 sdc1) és ebből logikai meghajtókat 

pvs
vgs
pvcreate /dev/sdb1

vgcreate -name ig /dev/sdb1

vgcreate --+name big /dev/sdb1

vgcreate big /dev/sdb1

lvcreate --name data -L 20M big

lvs

lsblk

pvcreate /dev/sdc1 
pvs
vgextend big /dev/sdc1
pvs
vgs

lvs

xfs_growfs /dev/mapper/big-data

lsblk

lvcreate --name small -L 10M big

mkfs.xfs /dev/mapper/big-small

cryptsetup luksFormat /dev/mapper/big-data
YES (umount előbb)

cryptsetup luksOpen /dev/mapper/big-data secret
lsblk -f 

mkfs.xfs /dev/mapper/secret

mount /dev/mapper/secret /mnt
lsblk -f 


reboot után csak ezután látszódik a secret xfs fájlrendszer
cryptsetup luksOpen /dev/mapper/big-data secret
lsblk -f 
mount /dev/mapper/secret /mnt

cat /etc/fstab
nano /etc/fstab 

UUID= (secret UUIDja ami lsblk val jön elő) /mnt xfs defaults 0 0
reboot
nem fog rebootolni
nano /etc/fstab ilyenkor a defaults,nofail -re át kell írni
reboot 
lsblk -f a secret még mindig nem látszik
cryptsetup luksOpen /dev/mapper/big-data example (nem muszaj secretnek lennie)

echo -n (nincs újsor) almaAlma > /key
umount /mnt 
cryptsetup luksClose example
lsblk -f 

cryptsetup luksOpen /dev/mapper/big-data example --key-file=/key
lsblk -f big-data UUID ctrl c

nano /etc/crypttab első oszlop milyen néven, masodik uuid harmadik kulcs negyedik mivel szeretném használni
valami UUID={big-data UUID} /key luks
reboot
ha most lsblk -f akkor látszik a secret (valami)

vboxban lemezt hozzáadni 
lsblk 
mkfs.ext2 /dev/sdd
mkdir /keys
lsblk -f sdd UUID ctrl c

nano /etc/fstab hozzáadjuk /keys ext2 defaults,nofail 0 0
mount /keys
lsblk 
mv /key /keys/
nano /etc/crypttab /keys/key luks átírni
reboot

crypttabban lehet - (vagy none) is a kulcsfájl helyénél így bootoláskor írja ki a gép

feladat home directoryt encrypteld de ha vki bootol akkor ő is tudja használni de a homeban lévőkhöz ne férjen hozzá

fdisk /dev/sda 
n 
p
w

lsblk -f (sda3)

cryptsetup luksFormat /dev/sda3

cryptsetup luksOpen /dev/sda3 secret-home
mkfs.xfs /dev/mapper/secret-home fájl rendszert rakunk rá
mount /dev/mapper/secret-home /mnt 

find /home/
mv /home/student /mnt/
umount /mnt
mount /dev/mapper/secret-home /home/
find /home/

lsblk -f fstabhoz az xfs fájlrendszer UUIDja
nano /etc/fstab /home xfs default,nofail 0 0
nano /etc/crypttab 
secret-home UUID={sda3 UUID} - luks

boot időben fog jelszót kérni a rendszer és nem csatolja fel a home katalógust 

# 8. labor

id - id student felhasználóról info
cat /etc/passwd első oszlop fnév, utána jelszó, utána komment mező, home könyvtár, alapértelmezett shell
/sin/nologin az különböző felhasználók számára van amik nem jelentkeznek be (pl processek)

cat /etc/group csoportok, név:jelszó:id

cat /etc/shadow a jelszavakat tartalmazza passwdből a shadowbe lettek átrakva a jelszavak, hashelve tároljuk

useradd -c "Bob" bob felh létrehozása
usermod -aG users bob hozzáadja az users csoporthoz bobot
grep bob /etc/passwd grep bob /etc/shadow grep bob /etc/group

chage -l bob jelszó manipulálásal kapcsolatos

-m min days
-M max days
-W figyelmeztetés jelszó változtatásra
-I inactive days

shadow fileban 
20200 1970től eltelt napok
bob:!!:20200:0:99999:7:::

passwd bob jelszó változtatás

passwd -S bob

passwd -l bob locked
passwd -u bob unlocked 

puttygen kulcspár generálásra windows

mkdir /home/bob/.ssh
cat > /home/bob/..sh/authorized_key public key

chown -R bob:bob /home/bob/.ssh/
chmod 700 /home/bob/.ssh/
chmod 600 /home/bob/.ssh/authorized_keys
ls -al /home/bob/.ssh/

puttyon belül connection -> ssh -> authentication -> credentials -> private key for authentication

ssh-keygen -l -f /etc/ssh/ssh_host_ecdsa_key.pub
useradd eve
su eve
cd 
find 
ssh-keygen -f eve_key
bob authorized key-jébe kell eve authorized key-jét (.pub)
ssh bob@localhost 'cat >> .ssh/authorized_keys' < eve_key.pub 
ssh -i eve_key bob@localhost (így nem kér bejelentkezést)

mv eve_key .ssh/id_rsa
így ssh bob@localhost is elég 

winscp-vel is lehet csak advancednél jelszó helyett meg kell adni a keyt

https://exam.progcont.eu/docs/IT-Security/deprecated/slides-hu/users.pdf
https://exam.progcont.eu/docs/IT-Security/deprecated/slides-hu/ssh-key-auth.pdf


# 9. labor
OpenSSL

[root@localhost ~]# man openssl
[root@localhost ~]# date > d
[root@localhost ~]# cat d
Tue Apr 29 12:10:55 CEST 2025
[root@localhost ~]# dd if=/dev/zero of=z bs=8 count=6
6+0 records in
6+0 records out
48 bytes copied, 6.0716e-05 s, 791 kB/s
[root@localhost ~]# xxd z
-bash: xxd: command not found
[root@localhost ~]# dnf provides xxd
[root@localhost ~]# dnf install vim-common
root@localhost ~]# xxd d
[root@localhost ~]# openssl des-ecb -in d -K 0123456789012345 -out d.des-ecb
 a bájtokat 8 bájtos blokkokra bnotjuk és titkosítjuk -K kulcs 16 hex szám
[root@localhost ~]# xxd d.des-ecb
helyes kulcssal lehet csak visszafejteni (1el több van a végén ezért nem tudja)
[root@localhost ~]# openssl des-ecb -d -in d.des-ecb -K 01234567890123454

most a z fájlból
[root@localhost ~]# openssl des-ecb -in z -K 0123456789012345 -out z.des-ecb

[root@localhost ~]# openssl des-cbc -in z -K 0123456789012345 -iv 0000000000000000 -out z.des-cbc
kell hozzá -iv iinicalizáló blokk


[root@localhost ~]# openssl des-cbc -in z -k alma -p -out z.des-cbc                    *** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
salt=359FF87D91F5DC84
key=6CEEEC942ED74032
iv =F90D8B7B231B316D
almából és saltból jön létre az iv

[root@localhost ~]# openssl des-cbc -in z.des-cbc -k alma -d

[root@localhost ~]# openssl des-cbc -in z -k alma -p -out z.des-cbc -S SALT SALT helyére a megadott salt

[root@localhost ~]# openssl des-cbc -in z -k alma -p -out z.des-cbc -pbkdf2
[root@localhost ~]# openssl des-cbc -k alma -in z.des-cbc -pbkdf2

[root@localhost ~]# openssl des-cbc -k alma -in z -out  z.des-cbc -pbkdf2 -p

[root@localhost ~]# openssl des-ede-cbc -k alma -in z -out  z.des-cbc -pbkdf2 -p
dupla akkora lesz a kulcs

[root@localhost ~]# openssl aes-128-cbc -k alma -in z -out z.aes-128-cbc -pbkdf2 -p
128 bites kulcs
192 bites
256 bites

[root@localhost ~]# openssl genrsa 512
[root@localhost ~]# openssl genrsa -out rsa_key 512

[root@localhost ~]# openssl rsa -in rsa_key -noout -text

[root@localhost ~]# openssl rsa -in rsa_key -out rsa_key.pub -pubout
[root@localhost ~]# openssl rsa -in rsa_key.pub -noout -text -pubin

[root@localhost ~]# openssl pkcs8 -in rsa_key -topk8 -out rsa_key.enc
encrypted 
[root@localhost ~]# openssl pkcs8 -in rsa_key.enc -topk8 -nocrypt

[root@localhost ~]# openssl pkeyutl -encrypt -in d -inkey rsa_key.pub -pubin -out d.rsa titkosítás
[root@localhost ~]# openssl pkeyutl -decrypt -in d.rsa -inkey rsa_key
visszafejtés

[root@localhost ~]# openssl pkeyutl -sign -inkey rsa_key -in d -out d.rsa2
[root@localhost ~]# openssl pkeyutl -verifyrecover -inkey rsa_key.pub -pubin -in d.rsa2

[root@localhost ~]# openssl dgst -sign rsa_key -sha256 -out anaconda-ks.cfg.sign anacon
[root@localhost ~]# openssl dgst -verify rsa_key.pub -signature anaconda-ks.cfg.sign -sha256 anaconda-ks.cfg
privát kulccsal titkosított hash érték

feladatok: visszafejteni rövid szöegeket
pkcs8-al stb

nyílvános kulcsot aláíratjuk valakivel 
pl wikipédia digicert, digicert önmagát írja alá
cert mgr windowson, itt lesz a digicert

[root@localhost ~]# openssl genrsa -out ca.key 4096

tanúsítvány létrehozása
[root@localhost ~]# openssl req -key ca.key -new -out ca.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:HU
State or Province Name (full name) []:Hajdu-Bihar
Locality Name (eg, city) [Default City]:Debrecen
Organization Name (eg, company) [Default Company Ltd]:Unideb
Organizational Unit Name (eg, section) []:DEIK
Common Name (eg, your name or your server's hostname) []:inf.unideb.hu
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

[root@localhost ~]# openssl req -in ca.csr -text -noout
ezzel megnézhetjük a tanúsítvány tartalmát

[root@localhost ~]# openssl x509 -req -days 100 -in ca.csr -out ca.crt -signkey ca.key
önmagunk írjuk alá ehhez csak a privát kulcsunk kell

unidebhez 
[root@localhost ~]# openssl genrsa -out web.key 4096
[root@localhost ~]# openssl req -new -key web.key -out web.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:HU
State or Province Name (full name) []:Hajdu-Bihar
Locality Name (eg, city) [Default City]:Debrecen
Organization Name (eg, company) [Default Company Ltd]:Unideb
Organizational Unit Name (eg, section) []:DEIK
Common Name (eg, your name or your server's hostname) []:www.inf.unideb.hu
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

[root@localhost ~]# openssl x509 -req -days 10 -in web.csr -out web.crt -CA ca.crt -CAkey ca.key -CAcreateserial -CAserial ca.srl
aláírás

trust list milyen tanúsítványok vannak az OS-ben

[root@localhost ~]# trust anchor ca.crt saját tanúsítvány --remove-al eltávolítani

feladat: saját tanúsítvány, seriallal
utolsó előtti hét zh

# Utolsó óra

https://exam.progcont.eu/it-security/login

exam parancs

exaim init
exam submit

várható feladatok: honlapon lévő videók
https://exam.progcont.eu/docs/IT-Security/deprecated/ 

