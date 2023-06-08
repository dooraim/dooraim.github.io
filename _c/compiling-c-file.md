---
layout: page
title: About
---

Note take from [Compiling C files with gcc, step by step  by Laura Roudge  Medium](https://medium.com/@laura.derohan/compiling-c-files-with-gcc-step-by-step-8e78318052)

# Compiling C files with gcc, step by step

- [Compiling C files with gcc, step by step](#compiling-c-files-with-gcc-step-by-step)
  - [Compilation](#compilation)
  - [The source code](#the-source-code)
  - [The steps](#the-steps)
  - [1. The preprocessor](#1-the-preprocessor)
  - [2. The compiler](#2-the-compiler)
  - [3. The assembler](#3-the-assembler)
  - [4. The linker](#4-the-linker)
- [`.out` file](#out-file)


![](https://miro.medium.com/v2/resize:fit:1036/1*wHKe6W4opLmk6pb7sxZz6w.png)

## Compilation

La compilazione consente di convertire il codice sorgente in codice oggetto attraverso uno strumento che prende il nome di compilatore.
La compilazione di divide in 4 parti:
- Preprocessore
- Compilazione
- Assembling
- Linking

Come compilatore si userà il GCC.

## The source code

![](https://miro.medium.com/v2/resize:fit:1356/1*9YAWYPU6kcqtLWHGW7mKPw.png)

## The steps

## 1\. The preprocessor

Il processore ha i seguenti ruoli:

- Elimina tutti i commenti nel source file.
- Include il codice del _header file(s)_, il quale contiene le dichiarazioni delle funzioni in C e poi le definizioni delle macro.
- Sostituisce tutte le _macros_ con dei valori

L'output del preprocessore viene salvato in un file con estensione `.i`.

Per fermare la compilazione dopo questo step, bisogna eseguire il comando `gcc` seguito dal parametro `-E`.

```log
[dooaraim@asahi-m1 Documentation]$ gcc -E temp.c
...
```

Il log è molto lungo. Di conseguenza è stato salvato in [Documentation/C/Documentation/log/helloworld.i.log](log/helloworld.i.log)

## 2\. The compiler

Il compilatore prende il codice generato dal preprocessore e genera IR code (Intermediate Representation). Ecco che quindi viene generato il file `.s` che è codice assembly.

Per fermare la compilazione dopo questo step, bisogna eseguire il comando `gcc` seguito dal parametro `-S`.

```
[dooaraim@asahi-m1 Documentation]$ gcc -S temp.c
```

Il log è molto lungo. Di conseguenza è stato salvato in [Documentation/C/Documentation/log/helloworld.s](log/helloworld.s)

## 3\. The assembler

Assembler prende il codice assembly è lo trasforma in codice oggetto o codice binario. Questo viene salvato in un file in formato `.o`.

Per fermare la compilazione dopo questo step, bisogna eseguire il comando `gcc` seguito dal parametro `-c`.

```
[dooaraim@asahi-m1 Documentation]$ gcc -c temp.c
```

Il log è molto lungo. Di conseguenza è stato salvato in [Documentation/C/Documentation/log/helloworld.o](log/helloworld.o)

## 4\. The linker

Il linker crea il binario finale. Questo fa due cose:

* Fa il linking dei source file che vengono usati nel programma scritto. Ad esempio se il main.c utilizza un altra file in *C*, ad esempio `secondary.c`, allora il linker produce il codice oggetto del `secondary.c`, cioè `secondary.o` e lo collega con il codice oggetto del `main.c` cioè `main.o`.
* Ad esempio è in questo punto che vengono linkate le funzioni come la `printf()` o `scanf()`.
* Per eseguire questo task il Linker usa due modi diversi:
  * [_static libraries_](static-dinamic-library.md). 
  * [_dynamic libraries_](static-dinamic-library.md)

# `.out` file

Alla fine del processo sopra si ottiene un file eseguibile che prende il nome di `a.out`, questo file si può ottenere anche usando il comando `gcc temp.c`. Possiamo decidere il nome del file eseguibile con il comando `gcc temp.c -o temp`.

Per eseguire il file basta usare il comando `./temp`.
