---
layout: post
category: git
title: Git Cheat-Sheet
---

- [Git Log](#git-log)
  - [Commit di un file](#commit-di-un-file)


# Git Log

## Commit di un file

Per vedere i commit legati a un file specifico utilizzando Git, puoi utilizzare il seguente comando:

```
git log -- <nome_file>
```

Sostituisci `<nome_file>` con il percorso del file di cui desideri vedere la cronologia dei commit. Questo comando ti mostrerà tutti i commit in cui il file specificato è stato modificato, con le relative informazioni, come autore, data, messaggio del commit e altro ancora.

Se desideri visualizzare solo le modifiche apportate a quel file in ciascun commit, puoi utilizzare il comando:

```
git log -p -- <nome_file>
```

Questo mostrerà anche le differenze tra le versioni del file in ciascun commit.

![](../image/git/Immagine%202023-07-29%20100741.png)

Inoltre, se vuoi vedere solo i commit di un file in una determinata branch, puoi specificare il nome della branch dopo il nome del file, come ad esempio:

```
git log -- <nome_file> <nome_branch>
```

Assicurati di trovarti nella repository Git giusta o specificare il percorso assoluto o relativo del file corretto.
