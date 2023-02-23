# Οδηγίες για την εγκατάσταση του λειτουργικού συστήματος [funtoo](https://www.funtoo.org/Welcome) σε Virtual Box

## Ο οδηγός αυτός είναι συμπυκνωμένες οδηγίες παρμένες από το [official funtoo installation guide](https://www.funtoo.org/Install/Introduction) που βοήθησαν εμάς προσωπικά να εγκαταστήσουμε το λειτουργικό στο Virtual Box και δεν αποτελούν το μόνο τρόπο εγκατάστασης. Ενθαρρύνουμε όλους να προσπαθήσουν μόνοι τους να κάνουν την εγκατάσταση του λειτουργικού ώστε να δούνε τις διαφορές που έχει από λειτουργικά με systemD και σε περίπτωση αποτυχίας να δοκιμάσουν με τον δικό μας οδηγό. Have fun!!! 😃

|Table of Contents|
|----------------|
| <ul> <li> [Προαπαιτούμενα](#προαπαιτούμενα)</li>  <li> [VirtualBox Set Up](#virtualbox-set-up)</li>  <li> [Funtoo Set Up](#funtoo-set-up) </li> <ul> <li>[Προαπαιτούμενα](#προαπαιτούμενα-1)</li><li>[The Fun Part of Funtoo](#the-fun-part-of-funtoo)</li><li>[Disk Set Up](#disk-set-up)</li><li>[Date Set Up](#date-set-up)</li><li>[Stage3 Download](#stage3-download)</li><li>[Chroot](#chroot)</li><li>[Download Portage Tree](#download-portage-tree)</li><li>[Config Files](#config-files)</li><li>[System Update](#system-update)</li><li>[Bootloader](#bootloader)</li><li>[Network Set Up](#network-set-up)</li><li>[Setting Users](#setting-users)</li><li>[Exit Environment and Reboot](#exit-environment-and-reboot)</li> </ul> <li>[Post Installation Configuration](#post-installation-configuration)</li> </ul> |


## Προαπαιτούμενα

1. Ένα [livecd](https://www.funtoo.org/Welcome) iso του funtoo (κατά προτίμηση να είναι η τελευταία έκδοση)
2. Το Virtual Box (Μπορείτε να χρησιμοποιήσετε όποια άλλα εικονική μηχανή θέλετε αλλά αυτός ο οδηγός βασίζεται στο VirtualBox)
‼️ Ο οδηγός βασίζεται σε εγκατάσταση MBR και όχι GPT καθώς υπήρχαν κάποια προβλήματα με το τελευταίο. Αν κάποιος τα καταφέρει με GPT θα θέλαμε να το συμπεριλάβουμε στον οδηγό μας.

## VirtualBox Set Up

1. Επιλέγουμε τα κατάλληλα πεδία όπως φαίνεται στην παρακάτω εικόνα <br>
![setup-1](https://user-images.githubusercontent.com/77148351/219341204-71c9fdf7-356a-4068-a527-d99ae7ea34ba.png)
2. Δίνουμε στη μηχανή τουλάχιστον δύο πυρήνες (ιδανικά 4) και τουλάχιστον 2GB RAM (ιδανικά 4GB λόγω του GUI) και 30GB αποθηκευτικό χώρο. <br/>
![ramcpu-setup](https://user-images.githubusercontent.com/77148351/219863924-e205cd24-0b4c-4257-a448-240ca0111f43.png)


## Funtoo Set Up

### Προαπαιτούμενα
Τα funtoo προσφέρουν έτοιμα isos σχεδόν για κάθε αρχιτεκτονική επεξεργαστών, όμως προσφέρουν και έτοιμα generic isos τα οποία λειτουργούν με κάθε επεξεργαστή απλά δεν είναι τόσο optimized. Μετά από πολύωρη ενασχόληση με τα specific CPU isos και αρκετά errors δεν καταφέραμε να μας δουλέψουν και έτσι αποφασίσαμε να ασχοληθούμε με τα generic τα οποία και δούλεψαν. Ο οδηγός βασίζεται λοιπόν στα generic isos, και πάλι αν κάποιος τα καταφέρει με συγκεκριμένη αρχιτεκτονική θα θέλαμε να το συμπεριλάβουμε στον οδηγό.

### The Fun Part of Funtoo
Αφού έχουμε bootάρει στο σύστημα ακολουθούμε τα συγκεκριμένα βήματα:

### Disk Set Up
1. `lsblk` για να δούμε το layout των δίσκων
2. `fdisk /dev/sda` και δίνουμε την εντολή `o` για να γίνει διαγραφή του partition
3.  Δημιουργία disk partition boot με το `fdisk`:</br>
![boot](https://user-images.githubusercontent.com/73399706/220938466-085afbde-001a-4ed1-b14f-531ae599630a.png)</br>
4.  Δημιουργία disk partition Swap (προαιρετικό):</br>![swap](https://user-images.githubusercontent.com/73399706/220938923-a8c0ceb9-f71c-41e3-b787-d084e1a8aa19.png)
5.  Δημιουργία disk partition Root:</br>![root](https://user-images.githubusercontent.com/73399706/220939508-77b397c8-e737-4398-8618-2fe2c67f5758.png)

6. `mkfs.ext2 /dev/sda1`
7. `mkfs.ext4 /dev/sda2` (αν έχουμε φτιάξει swap partition τότε είναι το /dev/sda3)
8.  `mkdir -p /mnt/funtoo`
9.  `mount /dev/sda2 /mnt/funtoo`
10.  `mkdir /mnt/funtoo/boot`
11.  `mount /dev/sda1 /mnt/funtoo/boot`

### Date Set Up
1. `date` για να δούμε την ημερομηνία του συστήματος
#### Σε περίπτωση που δεν είναι σωστή η ώρα του συστήματος
2. `date MMDDhhmmYYYY` αντικαθιστούμε τις μεταβλητές με νούμερα πχ. `date 021718502023`
3. `hwclock --systohc` για να ρυθμίσουμε το hardware clock και να μείνει persistent

### Stage3 Download
1. `cd /mnt/funtoo`
2. `links http://build.funtoo.org`
Επιλέγετε τα generic drivers τα οποία βρίσκονται στο `next/x86-64bit/generic_64/2023-01-31` και επιλέγετε όποια έκδοση θέλετε (.xz)(για δική σας ευκολία επιλέξτε κάτι με γραφικό περιβάλλον)
3. `q` για να βγούμε απ το `links`
### stage3 extract
1.`tar --numeric-owner --xattrs --xattrs-include='*' -xpf stage3-latest.tar.xz` (stage3=το αρχείο που κατεβάσατε)

### Chroot
1. `fchroot /mnt/funtoo /bin/bash --login` (οι οδηγίες του funtoo λένε ότι το prompt αλλάζει στη δική μας περίπτωση δεν έγινε κάτι τέτοιο αλλά παρέμεινε και πάλι `livecd`)

### Download Portage Tree
1. `ego sync`

### Config Files
1. `nano -w /etc/fstab` ακολουθήστε τις [οδηγίες](https://www.funtoo.org/Install/Configuration_Files)
![nano](https://user-images.githubusercontent.com/92797427/220631516-af10a8cb-74e7-4649-a08b-3b302df7e787.png)
2. `rm -f /etc/localtime`
3. `ln -sf /usr/share/zoneinfo/Europe/Athens /etc/localtime`


### System Update
Υπάρχουν δυο τρόποι:

1ος τρόπος: `emerge -auDN @world` <br>

2ος τρόπος: `emerge -q -auDN @world` το `-q` κάνει suppress το output

### Bootloader
1. `emerge -av grub`
2. `grub-install --target=i386-pc --no-floppy /dev/sda`
3. `ego boot update` προσπεράστε το warning της AMD 

### Network Set Up 
Ethernet
1. `rc-update add dhcpcd default`
2. `nano /etc/conf.d/hostname` και αλλάζουμε το hostname

Wi-Fi
1. `emerge linux-firmware networkmanager`
2. `rc-update add NetworkManager default`
3. `nmtui`

### Setting Users
1. `passwd` κωδικός για τον root user
2. `useradd -m myuser`
4. `usermod -G wheel,audio,video,plugdev,portage myuser`
5. `passwd myuser`

### Exit Environment and Reboot
1. `exit`
2. `cd /mnt`
3. `umount -lR funtoo`
4. `shutdown` για να επιστρέψουμε στη αρχική του livecd
5.  Τερματίζουμε το vm μας
6.  Από τις ρυθμίσεις του vm για το storage πρέπει να αφαιρέσουμε το livecd απο το "IDE Secondary Device" 

![storage](https://user-images.githubusercontent.com/77148351/220625668-b513b877-5100-4a39-9ea5-91a11079226d.png)

### Post Installation Configuration
Σε αυτό το σημείο καλό είναι να σετάρουμε τα sudo privilege του user μας.
Για να το καταφέρουμε αυτό θα χρειαστεί να κατεβάσουμε το sudo package.
1. `su` και βάζουμε τον κωδικό του root user
2. `emerge --ask app-admin/sudo`

## Granting Sudo Access

### <li> Για να δώσουμε sudo privilege σε όλες τις εντολές για τον χρήστη μας κάνουμε τα εξής βήματα:</li> 
1. `visudo` **ΠΡΟΣΟΧΗ** ΧΡΗΣΙΜΟΠΟΙΟΥΜΕ ΜΟΝΟ ΤΗΝ ΕΝΤΟΛΗ VISUDO ΚΑΤΑ ΤH ΔΙΑΡΚΕΙΑ ΤΟΥ EDIT ΤΟΥ SUDOERS ΑΡΧΕΙΟ. ΟΠΟΙΟΔΗΠΟΤΕ ΑΛΛΟ COMMAND ΤΟΥ ΣΤΥΛ `nano  /etc/sudoers` ΔE ΘΑ ΚΑΝΕΙ ΣΩΣΤΟ ΦΟΡΜΑΤ ΤΟ ΑΡΧΕΙΟ.
2. Κάνουμε uncomment τη γραμμή `%wheel ALL=(ALL:ALL) ALL`  (η συγκεκριμένη γραμμή δίνει sudo access σε όλα τα command που χρησιμοποιούνται από τους χρήστες του group Wheel)

### <li> Για να δώσουμε sudo privilege σε μεμονωμένα command κάνουμε τα εξής βήματα:</li> 
1. `visudo` **ΠΡΟΣΟΧΗ** ΧΡΗΣΙΜΟΠΟΙΟΥΜΕ ΜΟΝΟ ΤΗΝ ΕΝΤΟΛΗ VISUDO ΚΑΤΑ ΤH ΔΙΑΡΚΕΙΑ ΤΟΥ EDIT ΤΟΥ SUDOERS ΑΡΧΕΙΟ ΟΠΟΙΟΔΗΠΟΤΕ ΑΛΛΟ COMMAND ΤΟΥ ΣΤYΛ `nano  /etc/sudoers` ΔE ΘΑ ΚΑΝΕΙ ΣΩΣΤΟ ΦΟΡΜΑΤ ΤΟ ΑΡΧΕΙΟ
2. Προσθέτουμε κάτω από τη γραμμή _User privilege specification_ `myuser localhost = /usr/bin/emerge` (για παράδειγμα `p19arti vboxfun = /usr/bin/emerge`)
Με αυτό τον τρόπο δίνουμε sudo access μόνο για το command emerge δηλαδή για να χρησιμοποιούμε το package manager των Funtoo.

Σε περίπτωση που θέλουμε να δώσουμε και άλλα access, όπως για παράδειγμα για το git ώστε να μπορούμε να κάνουμε `sudo git `, θα πρέπει να συμπληρωθεί στη γραμμή που δίνουμε privilege π.χ. `myuser localhost = /usr/bin/emerge, /usr/bin/git`

Στη δικιά μας περίπτωση δώσαμε full access στον user μας διότι το έχουμε ξανακάνει και μπορούμε να καταλάβουμε πότε είναι προτιμότερο να χρησιμοποιείται η εντολή sudo και πότε όχι.

Στη συνέχειά `su myuser` για να επιστρέψουμε στον user μας και κάνουμε ένα τεστ κατεβάζοντας ένα πακέτο π.χ. το neofetch `emerge --ask app-misc/neofetch`.

**ΔΕΝ ΞΕΧΝΑΜΕ ΠΑΝΤΑ ΝΑ ΚΟΙΤΑΜΕ ΤΑ WIKI ΕΙΤΕ ΤΩΝ ΠΑΚΕΤΩΝ ΠΟΥ ΘΕΛΟΥΜΕ ΝΑ ΕΓΚΑΤΑΣΤΗΣΟΥΜΕ ΕΙΤΕ ΤΟ OFFICIAL WIKI ΤΟΥ FUNTOO/GENTOO ΓΙΑ ΝΑ ΜΑΘΑΙΝΟΥΜΕ ΤΙ ΚΑΝΕΙ Η ΚΑΘΕ ΕΝΤΟΛΗ ΠΟΥ ΧΡΗΣΙΜΟΠΟΙΟΥΜΑΙ ΚΑΙ ΝΑ ΜΗΝ ΠΗΓΑΙΝΟΥΜΕ ΣΤΑ ΤΥΦΛΑ. ΠΑΝΤΑ Η ΑΠΑΝΤΗΣΗ ΒΡΙΣΚΕΤΑΙ ΣΤΑ WIKI**


Feel free να προτείνετε διορθώσεις!

Made with ❤️ by [PaneksypnesDiepafes](https://github.com/PaneksypnesDiepafes)
