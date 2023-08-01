---
layout: post
category: linux
title: Linux Perf
---

- [Installazione](#installazione)
- [List avaible event](#list-avaible-event)
- [perf top](#perf-top)
  - [introduzione al comando](#introduzione-al-comando)
  - [vmlinux](#vmlinux)
  - [parametri](#parametri)
    - [esempi](#esempi)
- [analisi codice sorgente](#analisi-codice-sorgente)
- [Errori](#errori)
  - [perf.data file has no samples](#perfdata-file-has-no-samples)


# Installazione

Per usare `perf` installa i seguenti package

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
reboot
sudo apt-get install linux-headers-$(uname -r)
sudo apt install linux-tools-$(uname -r) linux-tools-generic
```

Check che sia stato installato correttamente.

```
dooraim@ubuntu-2202:~$ perf --version
perf version 5.15.99
dooraim@ubuntu-2202:~$ perf --help

 usage: perf [--version] [--help] [OPTIONS] COMMAND [ARGS]

 The most commonly used perf commands are:
   annotate        Read perf.data (created by perf record) and display annotated code
   archive         Create archive with object files with build-ids found in perf.data file
   bench           General framework for benchmark suites
   buildid-cache   Manage build-id cache.
   buildid-list    List the buildids in a perf.data file
   c2c             Shared Data C2C/HITM Analyzer.
   config          Get and set variables in a configuration file.
   daemon          Run record sessions on background
   data            Data file related processing
   diff            Read perf.data files and display the differential profile
   evlist          List the event names in a perf.data file
   ftrace          simple wrapper for kernel's ftrace functionality
   inject          Filter to augment the events stream with additional information
   iostat          Show I/O performance metrics
   kallsyms        Searches running kernel for symbols
   kmem            Tool to trace/measure kernel memory properties
   kvm             Tool to trace/measure kvm guest os
   list            List all symbolic event types
   lock            Analyze lock events
   mem             Profile memory accesses
   record          Run a command and record its profile into perf.data
   report          Read perf.data (created by perf record) and display the profile
   sched           Tool to trace/measure scheduler properties (latencies)
   script          Read perf.data (created by perf record) and display trace output
   stat            Run a command and gather performance counter statistics
   test            Runs sanity tests.
   timechart       Tool to visualize total system behavior during a workload
   top             System profiling tool.
   version         display the version of perf binary
   probe           Define new dynamic tracepoints
   trace           strace inspired tool

 See 'perf help COMMAND' for more information on a specific command.

```

# List avaible event

```bash
sudo perf list
```

Il numero di eventi è

```bash
dooraim@ubuntu-2202:~$ cat perf-list.log | wc -l
1709
```

```bash
dooraim@ubuntu-2202:~$ sudo perf list
alignment-faults                                   [Software event]
  bpf-output                                         [Software event]
  cgroup-switches                                    [Software event]
  context-switches OR cs                             [Software event]
  cpu-clock                                          [Software event]
  cpu-migrations OR migrations                       [Software event]
  dummy                                              [Software event]
  emulation-faults                                   [Software event]
  major-faults                                       [Software event]
  minor-faults                                       [Software event]
  page-faults OR faults                              [Software event]
  task-clock                                         [Software event]
  duration_time                                      [Tool event]
  rNNN                                               [Raw hardware event descriptor]
  cpu/t1=v1[,t2=v2,t3 ...]/modifier                  [Raw hardware event descriptor]
  mem:<addr>[/len][:access]      
...
```

Se si vogliono avere solo gli eventi `hardware` allora bisogna usare il comando

```bash
sudo perf list hardware
```

# perf top

## introduzione al comando

Il comando `perf top` è un'utilità della suite `perf`, uno strumento di profilazione e analisi delle prestazioni del kernel di Linux. `perf top` viene utilizzato per visualizzare un elenco interattivo dei simboli (funzioni) del kernel e degli spazi utente che stanno consumando più tempo di esecuzione durante l'esecuzione di un'applicazione o di un carico di lavoro specifico. In sostanza, fornisce una panoramica in tempo reale delle chiamate di sistema e delle funzioni che richiedono più tempo CPU.

L'output di `perf top` può essere utile per identificare le parti del codice che potrebbero essere ottimizzate per migliorare le prestazioni dell'applicazione. Può essere particolarmente utile per il debug delle prestazioni del kernel o per individuare punti critici in applicazioni di sistema.

Per utilizzare il comando `perf top`, è necessario avere i permessi di root o essere in grado di eseguire comandi con privilegi elevati, poiché l'accesso ai dati di profilazione del kernel richiede tali autorizzazioni.

L'esecuzione di `perf top` mostrerà un elenco dinamico delle funzioni più attive durante l'intervallo di campionamento, insieme a informazioni come il tempo trascorso, il numero di campionamenti e altri dati relativi alle prestazioni. Per uscire dalla visualizzazione di `perf top`, è sufficiente premere il tasto "q".

È importante notare che la suite `perf` offre una vasta gamma di comandi e opzioni per l'analisi delle prestazioni, e `perf top` è solo uno dei tanti strumenti disponibili per esplorare e comprendere il comportamento del sistema.

Poiché il comando `perf top` genera un output dinamico in tempo reale, l'aspetto esatto dell'output dipenderà dalle attività del sistema durante l'esecuzione del comando. Tuttavia, posso fornirti un esempio generico di come potrebbe apparire l'output di `perf top`:

```bash
Samples: 10K of event 'cycles:u'

Overhead  Command          Shared Object        Symbol
   8.18%  my_app           libc-2.33.so         [.] memcpy
   6.79%  my_app           libmysqlclient.so.21 [.] mysql_query
   5.12%  my_app           libcrypto.so.1.1     [.] EVP_DigestFinal_ex
   4.95%  my_app           [kernel]             [k] do_syscall_64
   4.61%  my_app           libglib-2.0.so.0     [.] g_hash_table_lookup
   3.91%  my_app           [kernel]             [k] page_fault
   3.32%  my_app           [kernel]             [k] find_vma
   2.98%  my_app           [kernel]             [k] handle_mm_fault
   2.85%  my_app           libxml2.so.2.9.12    [.] xmlParseElement
   2.13%  my_app           libsqlite3.so.0.8.6  [.] sqlite3_step

  (Hit the 'f' key for details.)
```

In questo esempio, `perf top` sta mostrando un elenco dei simboli del kernel e delle librerie utente che hanno contribuito alla maggior parte dei campionamenti durante l'intervallo di profilazione. Viene visualizzato un elenco di simboli insieme al loro rispettivo overhead, cioè la percentuale di campionamenti in cui il simbolo è stato coinvolto rispetto al totale dei campionamenti effettuati durante l'esecuzione di `perf top`.

La colonna "Overhead" indica la percentuale di tempo di CPU speso nel simbolo specifico rispetto al totale dei campionamenti.

Nel log [k] indica il kernel space e [.] user space.

Il comando `perf top` fornisce anche comandi aggiuntivi per approfondire le informazioni, come ad esempio premere la lettera 'f' per visualizzare i dettagli dei simboli all'interno di una funzione.

Ricorda che l'output effettivo potrebbe variare a seconda dell'applicazione, del carico di lavoro, delle librerie coinvolte e del sistema in cui viene eseguito il comando `perf top`.

## vmlinux

Per migliorare l'uso di `perf top` conviene installare il binario `vmlinux`

```
sudo apt-get update && sudo apt-get install linux-image-$(uname -r)-dbgsym
```

## parametri

```log
Couldn't read the cpuid for this machine: No such file or directory

 Usage: perf top [<options>]

    -a, --all-cpus        system-wide collection from all CPUs
    -b, --branch-any      sample any taken branches
    -c, --count <n>       event period to sample
    -C, --cpu <cpu>       list of cpus to monitor
    -d, --delay <n>       number of seconds to delay between refreshes
    -D, --dump-symtab     dump the symbol table used for profiling
    -E, --entries <n>     display this many functions
    -e, --event <event>   event selector. use 'perf list' to list available events
    -f, --count-filter <n>
                          only display functions with more events than this
    -F, --freq <freq or 'max'>
                          profile at this frequency
    -g                    enables call-graph recording and display
    -G, --cgroup <name>   monitor event in cgroup name only
    -i, --no-inherit      child tasks do not inherit counters
    -j, --branch-filter <branch filter mask>
                          branch stack filter modes
    -K, --hide_kernel_symbols
                          hide kernel symbols
    -k, --vmlinux <file>  vmlinux pathname
    -M, --disassembler-style <disassembler style>
                          Specify disassembler style (e.g. -M intel for intel syntax)
    -m, --mmap-pages <pages>
                          number of mmap data pages
    -n, --show-nr-samples
                          Show a column with the number of samples
    -p, --pid <pid>       profile events on existing process id
    -r, --realtime <n>    collect data with this RT SCHED_FIFO priority
    -s, --sort <key[,key2...]>
                          sort by key(s): pid, comm, dso, symbol, parent, cpu, srcline, ... Please refer the man page for the complete list.
    -t, --tid <tid>       profile events on existing thread id
    -U, --hide_user_symbols
                          hide user symbols
    -u, --uid <user>      user to profile
    -v, --verbose         be more verbose (show counter open errors, etc)
    -w, --column-widths <width[,width...]>
                          don't try to adjust column width, use these fixed values
    -z, --zero            zero history across updates
        --all-cgroups     Record cgroup events
        --asm-raw         Display raw encoding of assembly instructions (default)
        --call-graph <record_mode[,record_size],print_type,threshold[,print_limit],order,sort_key[,branch]>
                          setup and enables call-graph (stack chain/backtrace):

				record_mode:	call graph recording mode (fp|dwarf|lbr)
				record_size:	if record_mode is 'dwarf', max size of stack recording (<bytes>)
						default: 8192 (bytes)
				print_type:	call graph printing style (graph|flat|fractal|folded|none)
				threshold:	minimum call graph inclusion threshold (<percent>)
				print_limit:	maximum number of call graph entry (<number>)
				order:		call graph order (caller|callee)
				sort_key:	call graph sort key (function|address)
				branch:		include last branch info to call graph (branch)
				value:		call graph value (percent|period|count)

				Default: fp,graph,0.5,caller,function
        --children        Accumulate callchains of children and show total overhead as well
        --comms <comm[,comm...]>
                          only consider symbols in these comms
        --demangle-kernel
                          Enable kernel symbol demangling
        --dsos <dso[,dso...]>
                          only consider symbols in these dsos
        --fields <key[,keys...]>
                          output field(s): overhead, period, sample plus all of sort keys
        --force           don't complain, do it
        --group           put the counters into a counter group
        --group-sort-idx <n>
                          Sort the output by the event at the index n in group. If n is invalid, sort by the first event. WARNING: should be used on grouped events.
        --hierarchy       Show entries in a hierarchy
        --ignore-callees <regex>
                          ignore callees of these functions in call graphs
        --ignore-vmlinux  don't load vmlinux even if found
        --kallsyms <file>
                          kallsyms pathname
        --max-stack <n>   Set the maximum stack depth when parsing the callchain. Default: kernel.perf_event_max_stack or 127
        --namespaces      Record namespaces events
        --no-bpf-event    do not record bpf events
        --num-thread-synthesize <n>
                          number of thread to run event synthesize
        --objdump <path>  objdump binary to use for disassembly and annotations
        --overwrite       Use a backward ring buffer, default: no
        --percent-limit <percent>
                          Don't show entries under that percent
        --percentage <relative|absolute>
                          How to display percentage of filtered entries
        --prefix <prefix>
                          Add prefix to source file path names in programs (with --prefix-strip)
        --prefix-strip <N>
                          Strip first N entries of source file path name in programs (with --prefix)
        --proc-map-timeout <n>
                          per thread proc mmap processing timeout in ms
        --raw-trace       Show raw trace event output (do not use print fmt or plugins)
        --show-on-off-events
                          Show the on/off switch events, used with --switch-on and --switch-off
        --show-total-period
                          Show a column with the sum of periods
        --source          Interleave source code with assembly code (default)
        --stdio           Use the stdio interface
        --stitch-lbr      Enable LBR callgraph stitching approach
        --switch-off <event>
                          Stop considering events after the ocurrence of this event
        --switch-on <event>
                          Consider events after the ocurrence of this event
        --sym-annotate <symbol name>
                          symbol to annotate
        --symbols <symbol[,symbol...]>
                          only consider these symbols
        --tui             Use the TUI interface
```

Il comando `perf top` offre diversi parametri interessanti che consentono di personalizzare la visualizzazione e ottenere ulteriori informazioni sulle prestazioni del sistema. Alcuni dei parametri più interessanti includono:

1. `-e <event>`: Specifica l'evento di profilazione da monitorare. Gli eventi possono essere, ad esempio, "cycles" (cicli di CPU), "instructions" (istruzioni eseguite), "cache-references" (riferimenti alla cache), "cache-misses" (cache miss), e molti altri.

2. `-p <PID>`: Specifica il PID del processo per il quale si desidera eseguire la profilazione. Questo permette di concentrarsi su un processo specifico in esecuzione sul sistema.

3. `-k`: Mostra i simboli del kernel invece che quelli dello spazio utente. Utile per analizzare il comportamento del kernel e identificare chiamate di sistema costose.

4. `--sort <field>`: Specifica il campo di ordinamento per l'elenco dei simboli. Ad esempio, `--sort comm` ordina per nome del comando, `--sort symbol` per nome del simbolo, `--sort dso` per l'oggetto condiviso associato, ecc.

5. `--ns`: Abilita il supporto del namespace per PID e/o MOUNT, consentendo di visualizzare i simboli in uno spazio dei nomi specifico.

6. `--show-nr-samples`: Mostra il numero di campionamenti per ogni simbolo insieme alla percentuale di tempo di CPU impiegato.

7. `--call-graph <type>`: Specifica il tipo di call graph da utilizzare per analizzare la gerarchia delle funzioni. I tipi includono "fp" (frame pointer), "dwarf" (DWARF unwind), "lbr" (Last Branch Record), ecc.

8. `-i <delay>`: Imposta l'intervallo di campionamento in millisecondi. Più lungo è l'intervallo, meno preciso è il campionamento, ma può ridurre l'overhead della profilazione.

Questi sono solo alcuni dei parametri disponibili per personalizzare l'output e l'analisi del comando `perf top`. Per visualizzare tutti i parametri disponibili e le opzioni di `perf top`, puoi eseguire il comando `perf top --help`. Inoltre, sperimentare con diversi parametri può aiutarti a ottenere una visione più approfondita delle prestazioni del sistema e delle applicazioni in esecuzione.

### esempi

1. Esempio con parametro `-e <event>`:

```bash
$ perf top -e cycles:u

Samples: 100K of event 'cycles:u'

Overhead  Command          Shared Object        Symbol
   8.18%  my_app           libc-2.33.so         [.] memcpy
   6.79%  my_app           libmysqlclient.so.21 [.] mysql_query
   ...
```

In questo esempio, `perf top` sta monitorando l'evento "cycles:u" (cicli non privilegiati) e sta mostrando i simboli che hanno richiesto la maggior parte dei cicli di CPU durante l'intervallo di campionamento.

2. Esempio con parametro `-p <PID>`:

```bash
$ perf top -p 12345

Samples: 50K of event 'cycles:u' for process 12345

Overhead  Symbol
   10.21%  [kernel] do_syscall_64
    8.74%  my_app_function1
    7.81%  [kernel] page_fault
    ...
```

In questo esempio, `perf top` sta eseguendo la profilazione per il processo con PID 12345 e sta mostrando i simboli più attivi all'interno di tale processo.

3. Esempio con parametro `-k`:

```bash
$ perf top -k

Samples: 200K of event 'cycles:u'

Overhead  Command            Shared Object        Symbol
   8.18%  my_app             libc-2.33.so         [.] memcpy
   6.79%  my_app             libmysqlclient.so.21 [.] mysql_query
   ...
   5.92%  [kernel]           [k] do_syscall_64
   4.81%  [kernel]           [k] page_fault
   ...
```

In questo esempio, `perf top` sta mostrando sia i simboli dello spazio utente (applicazione) che i simboli del kernel che hanno richiesto la maggior parte dei cicli di CPU.

4. Esempio con parametro `--sort <field>`:

```bash
$ perf top --sort comm

Samples: 80K of event 'cycles:u'

Overhead  Command            Shared Object        Symbol
   6.18%  apache2            [kernel]             [k] do_syscall_64
   5.21%  apache2            libc-2.33.so         [.] memcpy
   4.97%  chrome             libGLESv2.so         [.] glDrawElements
   ...
```

In questo esempio, `perf top` sta ordinando l'output in base al nome del comando (`--sort comm`) in ordine alfabetico crescente.

Questi sono solo esempi teorici e i risultati reali possono variare a seconda delle attività del sistema durante l'esecuzione di `perf top`.

# analisi codice sorgente

Ecco un esempio di come utilizzare `perf` per analizzare il codice sorgente correlato a una funzione specifica:

Supponiamo di avere un programma C molto semplice, denominato "my_program.c", che contiene una funzione chiamata "my_function" che vogliamo profilare.

Contenuto di "my_program.c":

```c
#include <stdio.h>

void my_function() {
    for (int i = 0; i < 1000000; i++) {
        printf("Hello, world!\n");
    }
}

int main() {
    my_function();
    return 0;
}
```

Compiliamo il programma con i simboli di debug abilitati:

```bash
gcc -g -o my_program my_program.c
```

Ora eseguiamo la profilazione di "my_program" utilizzando `perf`:

```bash
sudo perf record -g ./my_program
```

Aspetta che il programma si concluda (poiché abbiamo messo un ciclo for che esegue un milione di volte, ci vorrà qualche secondo).

Successivamente, analizziamo i dati raccolti con `perf report`:

```bash
sudo perf report
```

L'output mostrerà una tabella con le funzioni e i simboli che hanno richiesto il maggior tempo di CPU durante l'esecuzione del programma. Cerca la voce corrispondente alla tua funzione "my_function" e prendi nota del suo indirizzo (generalmente in esadecimale).

Ora, utilizziamo `perf annotate` per visualizzare il codice sorgente correlato alla funzione "my_function":

```bash
sudo perf annotate -s my_function
```

Questo visualizzerà il codice sorgente di "my_function" con i numeri di linea delle istruzioni che hanno richiesto la maggior quantità di tempo CPU durante la profilazione. Potrai vedere le linee specifiche che hanno consumato più tempo e ottenere un'idea di quale parte del codice può essere ottimizzata o richiede ulteriori modifiche per migliorare le prestazioni.

Ricorda che il processo di profilazione può variare a seconda del programma e delle sue esigenze, ma questo esempio ti darà un'idea di come utilizzare `perf` per analizzare il codice sorgente correlato a una funzione specifica e identificare punti critici delle prestazioni.

# Errori

## perf.data file has no samples

Nel caso appaia il seguente errore, usare `-e cpu-clock`