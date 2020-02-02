---
title: "Créer une clé USB d'installation de Windows avec le terminal de MacOS"
date: 2020-01-05T18:17:15+01:00
draft: true
tags: ["macos", "windows"]
slug: "make-windows-bootable-usb-drive-on-macos-with-command-line"
---

Ce tutoriel permet de créer une clé USB d'installation de Windows en utilisant juste le terminal de commande de macOS. 

Il est destiné aux personnes ne possédant pas de machines sous Windows et qui, pour certains besoins spécifiques (comme accéder au Rift Store pour découvrir certains jeux VR), ont besoin d'installer Windows sur un nouveau PC. 

Si vous possédez une autre machine sous Windows, il existe des moyens bien plus simples pour créer un tel support d'installation (par exemple, avec Rufus).

La clé USB créée fonctionne avec un BIOS en mode **LEGACY** ou **UEFI CSM**. Elle n'est pas compatible avec un BIOS en mode **UEFI ONLY**.

## Prérequis :

- macOS *-> version utilisée : 10.14.2 (Mojave)*
- image ISO de Windows 10 (64 bits) *-> version utilisée : 1709*
- une clé USB de 8 GB ou plus

J'utilise la version 1709 de Windows 10 (64 bits) car la taille du fichier `install.wim` contenu dans l'image ISO est inférieur à 4 GB. Pour des versions plus récentes, il faut splitter ce fichier pour pouvoir le copier sur une partition FAT32.

Si vous voulez une version spécifique de Windows (et que vous possédez déjà une licence), il est possible de la télécharger sur le site [https://tb.rg-adguard.net/](https://tb.rg-adguard.net/public.php).

## Étape 1. Préparer la clé USB

### Identifier la clé USB

    diskutil list

{{< terminal >}}
$ diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI EFI                     209.7 MB   disk0s1
   2:                 Apple_APFS Container disk1         500.1 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +500.1 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD            387.7 GB   disk1s1
   2:                APFS Volume Preboot                 46.2 MB    disk1s2
   3:                APFS Volume Recovery                510.3 MB   disk1s3
   4:                APFS Volume VM                      2.1 GB     disk1s4

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *8.1 GB     disk2
   1:                 DOS_FAT_32 UNTITLED                8.1 GB     disk2s1
{{< /terminal >}}

Sur mon mac, la clé USB est désigné par `/dev/disk2`, mais cette valeur peut être différente pour votre machine.

Dans la suite de ce tutoriel, il s'agira de remplacer `/dev/disk2` ou `/dev/diskX` par la valeur correspondante à votre système.

### Partitionner et Formater 

ATTENTION : Toutes les données contenues sur la clé USB sont supprimées!

    diskutil partitionDisk /dev/diskX 1 MBR FAT32 WINDOWS R

{{< terminal >}}
$ diskutil partitionDisk /dev/disk2 1 MBR FAT32 WINDOWS R
Started partitioning on disk2
Unmounting disk
Creating the partition map
Waiting for partitions to activate
Formatting disk2s1 as MS-DOS (FAT32) with name WINDOWS
512 bytes per physical sector
/dev/rdisk2s1: 15697944 sectors in 1962243 FAT32 clusters (4096 bytes/cluster)
bps=512 spc=8 res=32 nft=2 mid=0xf8 spt=32 hds=255 hid=2048 drv=0x80 bsec=15728640 bspf=15331 rdcl=2 infs=1 bkbs=6
Mounting disk
Finished partitioning on disk2
/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *8.1 GB     disk2
   1:                 DOS_FAT_32 WINDOWS                 8.1 GB     disk2s1
{{< /terminal >}}

### Rendre clé USB bootable

Il est obligatoire de se connecter avec un utilisateur possédant les droits d'administration sur macOS pour éxécuter ces commandes.

    fdisk -e /dev/diskX

{{< terminal >}}
$ sudo fdisk -e /dev/disk2
Password:
fdisk: could not open MBR file /usr/standalone/i386/boot0: No such file or directory
Enter 'help' for information
fdisk: 1> flag 1
Partition 1 marked active.
fdisk:*1> write
Device could not be accessed exclusively.
A reboot will be needed for changes to take effect. OK? [n] y
Writing MBR at offset 0.
fdisk: 1> exit
{{< /terminal >}}

## Étape 2. Installer les fichiers

### Télécharger et décompresser les fichiers 

Pour réaliser la suite de ce tutoril, les fichiers `syslinux` sont récupérées sur internet et décompressées dans un répertoire de travail.

    curl https://www.oueta.com/wp-content/uploads/2018/10/syslinux-4.03.tar.gz -o syslinux-4.03.tar.gz
    tar -zxvf syslinux-4.03.tar.gz

    curl https://www.oueta.com/wp-content/uploads/2018/10/syslinux-mac.tar.gz -o syslinux-mac.tar.gz
    tar -zxvf syslinux-mac.tar.gz
    chmod +x syslinux

### Copier le code bootstrap dans le MBR

Il est obligatoire de se connecter avec un utilisateur possédant les droits d'administration sur macOS pour éxécuter ces commandes.

    diskutil unmountDisk /dev/diskX
    sudo dd if=syslinux-4.03/mbr/mbr.bin of=/dev/diskX bs=440 count=1
    sudo ./syslinux -i /dev/disk2s1

{{< terminal >}}
$ diskutil unmountDisk /dev/disk2
Unmount of all volumes on disk2 was successful
$ sudo dd if=syslinux-4.03/mbr/mbr.bin of=/dev/disk2 bs=440 count=1
Password:
1+0 records in
1+0 records out
440 bytes transferred in 0.000449 secs (980082 bytes/sec)
$ sudo ./syslinux -i /dev/disk2s1
syslinux for Mac OS X; created by Geza Kovacs for UNetbootin unetbootin.sf.net
"/dev/disk2s1" unmounted successfully.
/dev/disk2s1        	DOS_FAT_32                     	/Volumes/WINDOWS
mountpoint is /Volumes/WINDOWS
checkpoint1
checkpoint1.5
checkpoint1.6
/Volumes/WINDOWS/ldlinux.sys ldlinuxname
checkpoint2
checkpoint3
"/dev/disk2s1" unmounted successfully.
checkpoint4
checkpoint5
checkpoint6
checkpoint7
checkpoint8
/dev/disk2s1        	DOS_FAT_32                     	/Volumes/WINDOWS
{{< /terminal >}}

### Copier les modules syslinux BIOS 

    mkdir /Volumes/WINDOWS/syslinux
    cp syslinux-4.03/com32/modules/*.c32 /Volumes/WINDOWS/syslinux

### Create with a text editor 

`/Volumes/WINDOWS/syslinux/syslinux.cfg` 

    default boot
    LABEL boot
    MENU LABEL boot
    COM32 chain.c32
    APPEND fs ntldr=/bootmgr

## Étape 3. Copier les fichiers Windows

### Mount the Microsoft Windows ISO image

    hdiutil mount /example/windows.iso -mountpoint /Volumes/WINSETUP

###

    cp -Rv /Volumes/WINSETUP/ /Volumes/WINDOWS/

###

    diskutil unmountDisk /dev/diskX

Votre clé USB est enfin prête pour installer Windows!

---

**Références :**

- https://www.oueta.com/macos/create-windows-7-8-10-bootable-usb-drive-in-macos-with-command-line-mbr-partitioning-scheme/
- https://tb.rg-adguard.net/public.php
- https://github.com/gohugoio/hugo/issues/3216
