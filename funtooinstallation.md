# Οδηγίες για την εγκατάσταση του λειτουργικού συστήματος [funtoo](https://www.funtoo.org/Welcome) σε Virtual Box

## Προαπαιτούμενα

1. Ενα iso του funtoo (κατα προτίμηση να είναι η τελευταία έκδοση)
2. Το VirtualBox (Μπορείτε να χρησιμοποιήσετε όποια άλλα εικονική μηχανή θέλετε αλλα αυτός ο οδηγός βασίζεται στο VirtualBox)

## VirtualBox set up

1. Επιλέγουμε τα κατάλληλα πεδία όπως φαίνεται στην παρακάτω εικόνα <br>
![setup-1](https://user-images.githubusercontent.com/77148351/219341204-71c9fdf7-356a-4068-a527-d99ae7ea34ba.png)
2. Δίνουμε στη μηχανή τουλάχιστον δύο πυρήνες (ιδανικά 4) και τουλαχιστον 2GB RAM (ιδανικά 4 λόγω του GUI) και 50GB αποθηκευτικό χώρο
3. ‼️: Επιλέγουμε το κουτάκι με την ένδειξη Enable EFI <br>
![setup-2](https://user-images.githubusercontent.com/77148351/219342471-09ab68e3-f280-4e80-8476-2403d4859d72.png)

## Funtoo set up

### Προαπαιτούμενα
1. Βρίσκουμε την αρχιτεκτονική του επεξεργαστή μας.
2. Πηγαίνουμε στην [ιστοσελίδα](https://www.funtoo.org/Subarches) των funtoo και επιλέγουμε την κατάλληλη αρχιτεκτονική.
3. Επιλεγουμε την επιλογή browse και μας βγαίνει ενα πινακάκι με όλες τις επιλογές που έχουμε (Το πινακάκι είναι ενδεικτικό για τον δικο μου επεξεργαστή και ενδεχωμένως να υπάρχουν αλλαγες)
![setup-3](https://user-images.githubusercontent.com/77148351/219346400-2331f272-674d-4067-bacb-c6ee7f7ffbf4.png)
4. Πατάμε δεξί κλικ πάνω στο αρχείο .tar.xz και όχι το .tr.xz.gpg που θέλουμε να κατεβάσουμε, αντιγραφή συνδέσμου και το αποθηκέυουμε σε κάποιο txt αρχείο για
μετέπειτα χρήση.

### The fun part of funtoo
Αφού έχουμε bootάρει στο σύστημα ακολουθόυμε τα συγκεκριμένα βήματα:

### Disk set up
1. `lsblk` για να δούμε το layout των δίσκων
2. `fdisk /dev/sda` και δίνουμε την εντολη `o` για να γίνει διαγραφή του partition
3.  Ακολουθούμε τα βήματα απο το [official guide](https://www.funtoo.org/Install/MBR_Partitioning) για το partitioning όμως δημιουργούμε 2 μονο partitions αντι για 3. (Το swap partition είναι προαιρετικό)
4. `mkfs.ext2 /dev/sda1`
5. `mkfs.ext4 /dev/sda2` (αν έχουμε φτιάξει swap parttion τοτε είναι το /dev/sda3)
6.  `mkdir -p /mnt/funtoo`
7.  `mount /dev/sda3 /mnt/funtoo`
8.  `mkdir /mnt/funtoo/boot`
9.  `mount /dev/sda1 /mnt/funtoo/boot`

### Date set up
1. `date` για να δούμε την ημερομηνία του συστήματος 
#### Σε περίπτωση που δεν είναι σωστή η ώρα του συστήματος
2. `date MMDDhhmmYYYY` αντικαθιστούμς τις μεταβλητες με νούμερα πχ. `date 021718502023`
3. `hwclock --systohc` για να ρυθμίσουμε το hardware clock και να μείνει persistent

### Stage3 download
1. `cd /mnt/funtoo`
2. `links http://build.funtoo.org`
Επιλέγετε τα generic drivers τα οποία βρίσκονται στο `next/x86-64bit/generic_64/` και επιλεγεται όποια έκδοση θέλετε (για δική σας ευκολία επιλέξτε κάτι με γραφικό περιβάλλον)
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
1. `mount /dev/sda3 /mnt/funtoo`
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
