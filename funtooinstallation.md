# Οδηγίες για την εγκατάσταση του λειτουργικού συστήματος [funtoo](https://www.funtoo.org/Welcome) σε Virtual Box

## Προαπαιτούμενα

1. Ενα iso του funtoo (κατα προτίμηση να είναι η τελευταία έκδοση)
2. Το VirtualBox (Μπορείτε να χρησιμοποιήσετε όποια άλλα εικονική μηχανή θέλετε αλλα αυτός ο οδηγός βασίζεται στο VirtualBox)

## VirtualBox set up

1. Επιλέγουμε τα κατάλληλα πεδία όπως φαίνεται στην παρακάτω εικόνα
![setup-1](https://user-images.githubusercontent.com/77148351/219341204-71c9fdf7-356a-4068-a527-d99ae7ea34ba.png)
2. Δίνουμε στη μηχανή τουλάχιστον δύο πυρήνες (ιδανικά 4) και τουλαχιστον 2GB RAM (ιδανικά 4 λόγω του GUI) και 50GB αποθηκευτικό χώρο
3. ‼️: Επιλέγουμε το κουτάκι με την ένδειξη Enable EFI 
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
2. `gdisk /dev/sda` και δίνουμε την εντολη `o` για να γίνει διαγραφή του partition
3. Στη συνέχεια θα δώσουμε την εντολή `n` ώστε να κάνουμε νέο partition με partition number 1
το οποίο θα ξεκινάει απο την αρχή του sector οπότε στο first sector απλα πατάμε enter και στο
last sector του δίνουμε την εντολή `+128M` και HEX code `EF00`. 
‼️: Αυτή η εντολή θα εκτελεσθεί άλλες δύο φορές για να φτιάξουμε το swap partition και το root partition. 
Δείτε [αυτό](https://www.youtube.com/watch?v=SGtyCXjxR2E&t=1479s) το βίντεο για την ολοκλήρωση του βήματος
4. `mkfs.vfat -F 32 /dev/sda1`
5. `mkswap /dev/sda2` *σε περιπτωση που δεν λειτουργήσει με την πρώτη περιμένετε λίγο και ξαναδοκιμάστε*
6. `mkfs.ext4 /dev/sda3`

### Stage3 download
1. `curl to-link-tou arxeiou --remote-name`

### Folder creation and mount
1. `mount /dev/sda3 /mnt/funtoo`
2. `mkdir /mnt/funtoo/boot`
3. `mount /dev/sda1 /mnt/funtoo/boot`
4. Αλλάζουμε θέση το αρχείο που καταβάσαμε την αρχή `mv to-onoma-tou-arxeiou .mnt/funtoo`
5. `cd /mnt/funtoo`
6. `tar --numeric-owner -xpf to-arxeio`

 ### Chroot env setup
 1. `mount --rbind /sys sys`
 2. `mount --rbind /dev dev`
 3. `mount --rbind /dev /mnt/funtoo/dev`
 4. `mount --make-rslave /mnt/funtoo/dev`
 5. `mount -t proc /proc /mnt/funtoo/proc`
 6. `mount --rbind /sys /mnt/funtoo/sys`
 7. `mount --rbind /tmp /mnt/funtoo/tmp`
 9. `cp /etc/resolv.conf /mnt/funtoo/etc/`
 10. `env -i HOME=/root TERM=$TERM /bin/chroot . bash -l`
 11. `install -d /var/git -o 250 -g 250`
 12. `ego sync`
 13. 
