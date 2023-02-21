# Οδηγίες για την εγκατάσταση του λειτουργικού συστήματος [funtoo](https://www.funtoo.org/Welcome) σε Virtual Box

# Ο οδηγός αυτός είναι συμπυκνωμένες οδηγίες παρμένες από το [official funtoo installation guide](https://www.funtoo.org/Install/Introduction) που βοήθησαν εμάς προσωπικά να εγκαταστήσουμε το λειτουργικό στο Virtual Box και δεν αποτελούν το μόνο τρόπο εγκατάστασης. Ενθαρρύνουμε όλους να προσπαθήσουν μόνοι τους να κάνουν την εγκατάσταση του λειτουργικού ώστε να δούνε τις διαφορές που έχει από λειτουργικά με systemD και σε περίπτωση αποτυχίας να δοκιμάσουν με τον δικό μας οδηγό. Have fun!!! 😃

## Προαπαιτούμενα

1. Ένα [livecd](https://www.funtoo.org/Welcome) iso του funtoo (κατά προτίμηση να είναι η τελευταία έκδοση)
2. Το Virtual Box (Μπορείτε να χρησιμοποιήσετε όποια άλλα εικονική μηχανή θέλετε αλλά αυτός ο οδηγός βασίζεται στο VirtualBox)
‼️ Ο οδηγός βασίζεται σε εγκατάσταση MBR και όχι GPT καθώς υπήρχαν κάποια προβλήματα με το τελευταίο. Αν κάποιος τα καταφέρει με GPT θα θέλαμε να το συμπεριλάβουμε στον οδηγό μας.

## VirtualBox set up

1. Επιλέγουμε τα κατάλληλα πεδία όπως φαίνεται στην παρακάτω εικόνα <br>
![setup-1](https://user-images.githubusercontent.com/77148351/219341204-71c9fdf7-356a-4068-a527-d99ae7ea34ba.png)
2. Δίνουμε στη μηχανή τουλάχιστον δύο πυρήνες (ιδανικά 4) και τουλάχιστον 2GB RAM (ιδανικά 4GB λόγω του GUI) και 30GB αποθηκευτικό χώρο
![ramcpu-setup](https://user-images.githubusercontent.com/77148351/219863924-e205cd24-0b4c-4257-a448-240ca0111f43.png)


## Funtoo set up

### Προαπαιτούμενα
Τα funtoo προσφέρουν έτοιμα isos σχεδόν για κάθε αρχιτεκτονική επεξεργαστών, όμως προσφέρουν και έτοιμα generic isos τα οποία λειτουργούν με κάθε επεξεργαστή απλά δεν είναι τόσο optimized. Μετά από πολύωρη ενασχόληση με τα specific CPU isos και αρκετά errors δεν καταφέραμε να μας δουλέψουν και έτσι αποφασίσαμε να ασχοληθούμε με τα generic τα οποία και δούλεψαν. Ο οδηγός βασίζεται λοιπόν στα generic isos, και πάλι αν κάποιος τα καταφέρει με συγκεκριμένη αρχιτεκτονική θα θέλαμε να το συμπεριλάβουμε στον οδηγό.

<!-- ###
1. Κατεβάζουμε το [generic iso](url)


1. Βρίσκουμε την αρχιτεκτονική του επεξεργαστή μας.
2. Πηγαίνουμε στην [ιστοσελίδα](https://www.funtoo.org/Subarches) των funtoo και επιλέγουμε την κατάλληλη αρχιτεκτονική.
3. Επιλέγουμε την επιλογή browse και μας βγαίνει ενα πινακάκι με όλες τις επιλογές που έχουμε (Το πινακάκι είναι ενδεικτικό για τον δικό μου επεξεργαστή και ενδεχομένως να υπάρχουν αλλαγές)
![setup-3](https://user-images.githubusercontent.com/77148351/219346400-2331f272-674d-4067-bacb-c6ee7f7ffbf4.png)
4. Πατάμε δεξί κλικ πάνω στο αρχείο .tar.xz και όχι το .tr.xz.gpg που θέλουμε να κατεβάσουμε, αντιγραφή συνδέσμου και το αποθηκεύουμε σε κάποιο txt αρχείο για
μετέπειτα χρήση. -->

### The fun part of funtoo
Αφού έχουμε bootάρει στο σύστημα ακολουθούμε τα συγκεκριμένα βήματα:

### Disk set up
1. `lsblk` για να δούμε το layout των δίσκων
2. `fdisk /dev/sda` και δίνουμε την εντολή `o` για να γίνει διαγραφή του partition
3.  Ακολουθούμε τα βήματα από το [official guide](https://www.funtoo.org/Install/MBR_Partitioning) για το partitioning όμως δημιουργούμε 2 μόνο partitions αντί για 3. (Το swap partition είναι προαιρετικό)
4. `mkfs.ext2 /dev/sda1`
5. `mkfs.ext4 /dev/sda2` (αν έχουμε φτιάξει swap partition τότε είναι το /dev/sda3)
6.  `mkdir -p /mnt/funtoo`
7.  `mount /dev/sda2 /mnt/funtoo`
8.  `mkdir /mnt/funtoo/boot`
9.  `mount /dev/sda1 /mnt/funtoo/boot`

### Date set up
1. `date` για να δούμε την ημερομηνία του συστήματος
#### Σε περίπτωση που δεν είναι σωστή η ώρα του συστήματος
2. `date MMDDhhmmYYYY` αντικαθιστούμε τις μεταβλητές με νούμερα πχ. `date 021718502023`
3. `hwclock --systohc` για να ρυθμίσουμε το hardware clock και να μείνει persistent

### Stage3 download
1. `cd /mnt/funtoo`
2. `links http://build.funtoo.org`
Επιλέγετε τα generic drivers τα οποία βρίσκονται στο `next/x86-64bit/generic_64/2023-01-31` και επιλέγετε όποια έκδοση θέλετε (για δική σας ευκολία επιλέξτε κάτι με γραφικό περιβάλλον)
3. `q` για να βγουμε απο το `links`
### stage3 extract
1.`tar --numeric-owner --xattrs --xattrs-include='*' -xpf stage3-latest.tar.xz` (stage3=το αρχείο που κατεβάσατε)

### Chroot
1. `fchroot /mnt/funtoo /bin/bash --login` (οι οδηγίες του funtoo λένε ότι το prompt αλλάζει στη δική μας περίπτωση δεν έγινε κάτι τέτοιο αλλά παρέμεινε και πάλι `livecd`)

### Download Portage Tree
1. `ego sync`

### Config files
1. `nano -w /etc/fstab` ακολουθήστε τις [οδηγίες](https://www.funtoo.org/Install/Configuration_Files)
2. `rm -f /etc/localtime`
3. `ln -sf /usr/share/zoneinfo/Europe/Athens /etc/localtime`

### Folder creation and mount
1. `mount /dev/sda2 /mnt/funtoo`
2. `mkdir /mnt/funtoo/boot`
3. `mount /dev/sda1 /mnt/funtoo/boot`
4. Αλλάζουμε θέση το αρχείο που καταβάσαμε την αρχή `mv to-onoma-tou-arxeiou .mnt/funtoo`
5. `cd /mnt/funtoo`
6. `tar --numeric-owner -xpf to-arxeio`

### System Update
1. `emerge -auDN @world`

### Bootloader
1. `emerge -av grub`
2. `grub-install --target=i386-pc --no-floppy /dev/sda`
3. `ego boot update`
4. `passwd myuser`

### Setting users
1. `passwd` κωδικός για τον root user
2. `useradd -m myuser`
3. `usermod -G wheel,audio,video,plugdev,portage myuser`

### Exit Environment and Reboot
1. `exit`
2. `cd /mnt`
3. `umount -lR funtoo`
4. `reboot` κλείνουμε το vm και αφαιρούμε το livecd


Feel free να προτείνετε διορθώσεις

Made with ❤️ by [PaneksypnesDiepafes](https://github.com/PaneksypnesDiepafes)
