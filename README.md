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
