---
layout: post
category: linux
title: Linux Command cheat sheet
---

- [Cancellare un file/cartella con find](#cancellare-un-filecartella-con-find)
- [Diff di due file](#diff-di-due-file)
- [Diff di due file più leggibile](#diff-di-due-file-più-leggibile)
- [stampare una singola parola di una frase con awk](#stampare-una-singola-parola-di-una-frase-con-awk)
- [stampare una singola parola di una frase con separatore - con cut](#stampare-una-singola-parola-di-una-frase-con-separatore---con-cut)
- [stampare una parola in un file con sed e awk](#stampare-una-parola-in-un-file-con-sed-e-awk)
- [recuperare un dato nel caso di più separatori](#recuperare-un-dato-nel-caso-di-più-separatori)
- [stampare il primo risultato di un find](#stampare-il-primo-risultato-di-un-find)
- [Cancellare la prima colonna di un file](#cancellare-la-prima-colonna-di-un-file)
- [Cancellare l'ultima colonna di un file](#cancellare-lultima-colonna-di-un-file)
- [Cancellare una colonna intermedia](#cancellare-una-colonna-intermedia)
- [Cancellare gli spazi vuoti alla fine di ogni riga](#cancellare-gli-spazi-vuoti-alla-fine-di-ogni-riga)
- [Scrivere un carattere all'inizio di una riga](#scrivere-un-carattere-allinizio-di-una-riga)
- [Scrivere un carattere alla fine di una riga](#scrivere-un-carattere-alla-fine-di-una-riga)
- [Sostituire "foo" con "bar" in ogni riga](#sostituire-foo-con-bar-in-ogni-riga)
  - [replaces only 1st instance in a line](#replaces-only-1st-instance-in-a-line)
  - [replaces only 4th instance in a line](#replaces-only-4th-instance-in-a-line)
  - [replaces ALL instances in a line](#replaces-all-instances-in-a-line)
- [Sostituisci foo con bar all'inizio di una parola](#sostituisci-foo-con-bar-allinizio-di-una-parola)
- [Sostituisci foo con bar la fine di una parola](#sostituisci-foo-con-bar-la-fine-di-una-parola)
- [Substitute "foo" with "bar" ONLY for lines which contain "baz"](#substitute-foo-with-bar-only-for-lines-which-contain-baz)
- [Copiare un file ed avere una progress bar](#copiare-un-file-ed-avere-una-progress-bar)
- [Generatore di password casuali](#generatore-di-password-casuali)
- [Lista dei file aperti](#lista-dei-file-aperti)
  - [Decomprimere file tar.bz2 in una cartella](#decomprimere-file-tarbz2-in-una-cartella)
  - [Correggere chiavette corrotte](#correggere-chiavette-corrotte)
- [Cerca il nome del file che contiene una determinata stringa](#cerca-il-nome-del-file-che-contiene-una-determinata-stringa)
- [Dimensione di una cartella e le sue sottocartelle](#dimensione-di-una-cartella-e-le-sue-sottocartelle)
- [Dimensione di una cartella e le sue sottocartelle escludendo una cartella](#dimensione-di-una-cartella-e-le-sue-sottocartelle-escludendo-una-cartella)
  - [Formattare una chiavetta USB](#formattare-una-chiavetta-usb)
- [](#)
- [Cancellare un file con find](#cancellare-un-file-con-find)
- [Numero di file in una sotto cartella](#numero-di-file-in-una-sotto-cartella)
- [Verifica di Out Of Memory](#verifica-di-out-of-memory)
- [Riferimenti](#riferimenti)

# Cancellare un file/cartella con find

Nel caso di un *file*
```bash
find . -type f -name "<name of file that you want delete>" -exec rm {} ";"
```

Nel caso di una *cartella*
```bash
find . -type f -name "<nome file che si vuole cancellare>" -exec rm -rf {} ";"
```

**Esempio**
```bash
bash-3.2$ ls -la
total 0
drwxr-xr-x   3 nicoorlando  staff   96 12 Nov 16:20 .
drwxr-xr-x  26 nicoorlando  staff  832 12 Nov 16:20 ..
-rw-r--r--   1 nicoorlando  staff    0 12 Nov 16:18 pippo
bash-3.2$ find . -type f -name "pippo" -exec rm {} ";"
bash-3.2$ ls -la
total 0
drwxr-xr-x   2 nicoorlando  staff   64 12 Nov 16:22 .
drwxr-xr-x  26 nicoorlando  staff  832 12 Nov 16:20 ..
```

# Diff di due file

```bash
diff <file1> <file2> | grep '^[<>]'
```

**Esempio**
```bash
bash-3.2$ cat file1
Linux is all about efficiency.
I hope you will enjoy this book.
bash-3.2$ cat file2
MacOS is all about efficiency.
I hope you will enjoy this book.
Have a nice day.
bash-3.2$ diff file1 file2 | grep '^[<>]'
< Linux is all about efficiency.
> MacOS is all about efficiency.
> Have a nice day.
```

# Diff di due file più leggibile

```bash
diff -y <file1> <file2>
```

* `>` indica che è stata aggiunto qualcosa
* `<` indica che è stato tolto qualcosa

```log
[dooaraim@asahi-m1 test]$ diff -y hello.c hello_copy.c
#include <stdio.h>                                              #include <stdio.h>
int main() {                                                    int main() {
   // printf() displays the string inside quotation                // printf() displays the string inside quotation
   printf("Hello, World!");                                        printf("Hello, World!");
   return 0;                                                       return 0;
                                                              >
                                                              > pippi
}                                                               }
```

# stampare una singola parola di una frase con awk

```bash
<frase> | awk '{print $<posizione parola>}'
```

**Esempio**
```bash
bash-3.2$ echo Efficient fun Linux | awk '{print $3}'
fun
```

# stampare una singola parola di una frase con separatore - con cut

```bash
<frase> | cut -f <posizione parola> -d '-'
```

**Esempio**
```bash
echo hello-happy-world-! | cut -f 4 -d '-'
```

# stampare una parola in un file con sed e awk

```bash
df -h | sed '<numero della riga>!d' | awk '{print $<colonna>}'
```

**Esempio**
Si vuole stampare il dato `296Mi` che corrisponde alla colonna 3 e riga 5.
```bash
bash-3.2$ df -h
Filesystem       Size   Used  Avail Capacity iused      ifree %iused  Mounted on
/dev/disk3s1s1  228Gi   14Gi  123Gi    11%  553788 2393071172    0%   /
devfs           203Ki  203Ki    0Bi   100%     702          0  100%   /dev
/dev/disk3s6    228Gi  1.0Gi  123Gi     1%       1 2393624959    0%   /System/Volumes/VM
/dev/disk3s2    228Gi  296Mi  123Gi     1%    1464 2393623496    0%   /System/Volumes/Preboot
/dev/disk3s4    228Gi   17Mi  123Gi     1%      46 2393624914    0%   /System/Volumes/Update
/dev/disk1s2    500Mi  6.0Mi  481Mi     2%       3    5119997    0%   /System/Volumes/xarts
/dev/disk1s1    500Mi  7.4Mi  481Mi     2%      28    5119972    0%   /System/Volumes/iSCPreboot
/dev/disk1s3    500Mi  636Ki  481Mi     1%      38    5119962    0%   /System/Volumes/Hardware
/dev/disk3s5    228Gi   89Gi  123Gi    42%  696418 2392928542    0%   /System/Volumes/Data
map auto_home     0Bi    0Bi    0Bi   100%       0          0  100%   /System/Volumes/Data/home
bash-3.2$ df -h | sed '5!d' | awk '{print $3}'
296Mi
```

# recuperare un dato nel caso di più separatori

```
<file> | sed '<numero riga>!d' | awk -F<primo delimitatore> '{print $<posizione dato>}' | awk '{print $<posizione dato>}'
```

**Esempio**
Si vuole stampare il carattere 1 a riga 6
```bash
bash-3.2$ cat /etc/hosts
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1       localhost
255.255.255.255 broadcasthost
::1             localhost
# Added by Docker Desktop
# To allow the same kube context to work on the host and the container:
127.0.0.1 kubernetes.docker.internal
# End of section
bash-3.2$ cat /etc/hosts | sed '7!d' | awk -F. '{print $4}' | awk '{print $1}'
1
```

# stampare il primo risultato di un find

```bash
find / -type f -name "*<variabile>*" -print -quit | xargs cat
```

# Cancellare la prima colonna di un file

```bash
awk -i inplace '{sub(/^\S+\s*/,"")}1' file
```
Oppure
```bash
awk -i inplace '{$0=gensub(/^\S+\s*/,"",1)}1' file
```

# Cancellare l'ultima colonna di un file

```bash
awk -i inplace '{sub(/\s*\S+$/,"")}1' file
```
Oppure
```bash
awk -i inplace '{$0=gensub(/\s*\S+$/,"",1)}1' file
```

# Cancellare una colonna intermedia

```bash
awk -i inplace '{$0=gensub(/\s*\S+/,"",<colonna che si vuole cancellare>)}1' file
```

# Cancellare gli spazi vuoti alla fine di ogni riga

```
sed 's/[ \t]*$//'
```

# Scrivere un carattere all'inizio di una riga

```bash
sed -i "s/^/[OK]/g" <nome file>
```

# Scrivere un carattere alla fine di una riga

```bash
sed -i "s/$/[FINE]/g" <nome file>
```

# Sostituire "foo" con "bar" in ogni riga

## replaces only 1st instance in a line

```bash
sed 's/foo/bar/'
```

## replaces only 4th instance in a line

```bash
 sed 's/foo/bar/4'
```

## replaces ALL instances in a line
```bash
sed 's/foo/bar/g'
```

# Sostituisci foo con bar all'inizio di una parola

```bash
sed 's/\(.*\)foo\(.*foo\)/\1bar\2/'
```

# Sostituisci foo con bar la fine di una parola

```bash
sed 's/\(.*\)foo/\1bar/'
```

# Substitute "foo" with "bar" ONLY for lines which contain "baz"

```bash
sed '/baz/s/foo/bar/g'
```

# Copiare un file ed avere una progress bar

```bash
rsync -avh --progress <source> <destination>
```

# Generatore di password casuali

```bash
mkpasswd -l <lunghezza password>
```

# Lista dei file aperti

```bash
lsof
```

## Decomprimere file tar.bz2 in una cartella

```bash
mkdir test_rootfs
tar -xf <nome file>.tar.bz2 -C ./test_rootfs/.
```

## Correggere chiavette corrotte

In caso di errore come questo: **FAT-fs (vda1): Volume was not properly unmounted. Some data may be corrupt. Please run fsck.**, eseguire il seguente comando:
```
fsck.vfat /dev/<sd?1>
```
Nel caso in cui non si trovi il comando installare il package:
```
sudo apt install dosfstools
```

# Cerca il nome del file che contiene una determinata stringa

Utile nel caso in cui si debba cercare un driver del kernel attraverso il compatible
```
find . -name '*.c' -exec grep -H '\.compatible.*=.*tsl2550"' {} \;
```

# Dimensione di una cartella e le sue sottocartelle

```
du . -hacS --apparent-size
```

# Dimensione di una cartella e le sue sottocartelle escludendo una cartella

```
du . --exclude "**/.git" -hacS --apparent-size
```

## Formattare una chiavetta USB
#
[Link](https://recoverit.wondershare.it/flashdrive-recovery/linux-format-usb.html)
Eseguire il comando:
```bash
df
sudo umount /dev/<nome_device>
```
Successivamente eseguire uno dei tre comandi:
* Per il file system vFAT (FAT32):
  ```bash
  sudo mkfs.vfat /dev/<nome_device>
  ```
* Per file system NTFS:
  ```bash
  sudo mkfs.ntfs /dev/<nome_device>
  ```
* Per il file system EXT4:
  ```bash
  sudo mkfs.ext4 /dev/<nome_device>
  ```

Se si vule associare un nome quando si formatta la chiavetta USB allora bisogna andare a utilizzare il comando:\
```bash
sudo mkfs.vfat -n <nome chiavetta> /dev/<nome_device>
```

# Cancellare un file con find

```bash
find . -name '.DS*' -delete
```

# Numero di file in una sotto cartella

Per poter determinare il numero di file all'interno di tutte le sotto-directory, bisogna eseguire il comando:

```bash
$ find . -maxdepth 1 -type d | while read -r dir
do printf "%s:\t" "$dir"; find "$dir" -type f | wc -l; done
```

Se invece si vuole solo visualizzare il numero di file in tutte le sotto directory, allora basta usare il comando:

```bash
$ find . -type f -print | wc -l
```

# Verifica di Out Of Memory

Per verificare se il sistema è in oom, eseguire il comando seguente

```
$ dmesg -T -l 3 | tail -n 10
```

# Riferimenti
* [The most important Linux commands that nobody teaches you.  by Joel Belton  Medium](https://medium.com/@joelbelton/the-most-important-linux-commands-that-nobody-teaches-you-ce423ef2ae28)
