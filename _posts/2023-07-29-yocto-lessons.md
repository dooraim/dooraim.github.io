---
layout: post
category: yocto
title: Yocto Lessons
---

- [Configuration of kernel build](#configuration-of-kernel-build)
- [Create Patch](#create-patch)


# Configuration of kernel build

Per vedere la configurazione di default del kernel utilizzata da Yocto, puoi seguire questi passaggi:

1. Accedi alla directory principale del tuo progetto Yocto, in cui si trova il file `build` o la cartella `build`.

2. All'interno della directory `build`, dovresti trovare una cartella denominata `tmp`.

3. Nella cartella `tmp`, cerca la directory `work`. Questa directory contiene le informazioni e i file generati per ogni pacchetto (come il kernel) durante il processo di compilazione.

4. All'interno della directory `work`, dovresti trovare una sottodirectory corrispondente al pacchetto del kernel. Di solito, il nome della directory sarà qualcosa come `linux-yocto`.

5. Naviga nella directory del kernel, che dovrebbe essere qualcosa del genere: `work/<machine>-poky-linux-gnueabi/linux-yocto/<version>/`.

6. All'interno di questa directory del kernel, cerca un file chiamato `.config`. Questo file contiene la configurazione di default del kernel utilizzata da Yocto.

Puoi visualizzare il contenuto del file `.config` utilizzando un editor di testo o un comando come `cat` o `less`:

```bash
cat /path/to/your/yocto/build/tmp/work/<machine>-poky-linux-gnueabi/linux-yocto/<version>/.config
```

Sostituisci `<machine>` con il nome della tua macchina target e `<version>` con la versione specifica del kernel che stai utilizzando.

Se hai più layer o configurazioni personalizzate, potrebbero esserci più file `.config` per configurazioni diverse. In tal caso, dovresti essere sicuro di utilizzare il file corretto che corrisponde alla configurazione desiderata.

Ad esempio

```
/home/norlando/yocto/poky/qemu-test/qemu-arm-a9/build/tmp/work/qemuarma9-poky-linux-gnueabi/linux-yocto/5.4.237+gitAUTOINC+c7e2e52889_936721bc39-r0/linux-qemuarma9-standard-build
```

# Create Patch

Scaricare i sorgenti del kernel che contengono la defconfig da modificare, poi eseguire i comandi seguenti in modo da modificare la defconfig e salvarla.

```bash
$ export ARCH=arm64
$ export CROSS_COMPILE=aarch64-linux-gnu-
$ make defconfig
$ make menuconfig
$ make savedefconfig
$ cp defconfig arch/arm64/configs/defconfig‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍
```

Una volta modificata si va a fare un commit e creare un file di patch.

```bash
$ git add arch/arm64/configs/defconfig
$ git commit -s -m "defconfig: Customize defconfig"
$ git format-patch -1
0001-defconfig-Customize-defconfig.patch‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍
```

A questo punto bisogna copiare la patch all'interno della directory di yocto

```
$ cp 0001-defconfig-Customize-defconfig.patch <BSP_DIR>/sources/meta-pippo/meta-bsp/recipes-kernel/linux/linux-pippo/files/.
```
‍‍
Infine all'interno della ricetta del kernel bisogna andare ad aggiungere la riga seguente

```
SRC_URI += "file://0001-defconfig-Customize-defconfig.patch"‍
```
