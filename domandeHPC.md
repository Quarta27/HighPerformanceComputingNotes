# High Performance Computing
## Argomenti del corso con brevi punti descrittivi

## Paolo Quartarone @ Ca'Foscari University, 859724

## 01 - Introduzione

## 02 - SSE Instruction level parallelismo
- *Come posso contare i bit ad uno in un byte:*
  - Linear counting
  - Lookup table
  - Sideway addition
- *SIMD Single instruction multiple data:* grande registri nella CPU come concatenazioni di piccoli pezzi di dati. Una singola istruzione lavora su più grandi registri.
- *SSE Streaming SIMD Execution:* estensione di SIMD
  - Il formato delle istruzioni è `_mm_<intrin_op>_<suffix>`
  - Packed: parallelo $ \rightarrow$ applica la funzione su tutti i valori nel multiple register
  - Scalare: invoca l'operazione solo sul bit meno significativo
  - Valori memorizzati in little endian. Devono essere allineati.

## 03 - Autovectorization
- *Assunzioni sul modello:*
  - Pipeline a 5 stage: instruction fetching, instruction decode, alu, memory, register write back. Pipeline replicabili finchè basta come modello per eseguire più operazioni contemporaneamente.
- *Data dependency:*
  - RAW $\rightarrow$ to check!
  - WAR $\rightarrow$ to check!
  - WAW $\rightarrow$ to check!
  - RAR $\rightarrow$ no a problem!
- *Come ottimizzare un prefix sum:*
  - Instruction reordering to avoid stalls
  - Loop unrolling to execute more information in the same cicle avoiding stalls
  - VLIW very long instruction word: aumenta il parallelismo aumentando il numero di pipeline (larghezza della pipeline). Una singola VLIW contiene più semplici operazioni che possono essere riorganizzate staticamente $\rightarrow$ in questo caso da il massimo dello speedup!
    - *Super-pipelined processor:* processore che sfrutta più pipeline per accrescere il parallelismo
    - *Super-scalar execution:* l'abilità di un processo di eseguire più operazioni nello stesso ciclo.
    - *VLIW:* istruzioni che possono essere eseguite contemporaneamente vengono "impacchettate" insieme e inviate al processore come un unica grande istruzione che verrà eseguita su più unità funzionali contemporaneamente.
      - Pro: il processore ha un grande contesto per selezionare le istruzioni da usare e una grande scelta di possibilità per parallelizzare
      - Cons: il compilatore non ha il program state dinamico per effettuare decisioni
  - *Dynamic scheduling:* l'hardware riorganizza il flusso di esecuzione per ridurre gli stalli. Non tutte le dipendenze sono conosciute a compile time.
  - *Guidelines for auto-vectorizable code:*
    - Evitare loop termination complesse
    - Scrivere lunghe linee di codice
    - Evitare dipendenze tra loop
    - Meglio array notation anzichè pointer (aliasing) pointer potrebbero essere qualunque cosa
    - Usare il loop index direttamente anzichè usare altre var
## 04 - Cache Awarness
- *Cache optimality:*
  - Vengono fatte degli ottimi place replacement
- *Introduzione alla cache:* la cache è un particolare tipo di memoria, molto piccola rispetto alla RAM o allo storage, utilizzata vicino al processore per mitigare la problematica del caricamento dei dati dai vari livelli e migliorare i tempi di esecuzione. Infatti, se le informazioni richieste dal processore si trovano all'interno della cache saranno reperite con ritardi piccolissimi (nell'ordine dei nanosecondi). Se questa informazioni non sono presenti, sarà necessario caricarle dalla memoria principale (o comunque dal livello inferiore della gerarchi). Tale operazione avrà quindi un costo superiore, facendo perdere cicli di clock e quindi tempo di esecuzione, degradando le performance. Gli obiettivi degli algoritmi che sfruttano la località nella cache è proprio quello di ottimizzare ulteriormente i tempi di computazione, modificando le procedure di accesso ed utilizzo ai dati.
- *Cache to memory coherence:* la cache memory coeherence è una problematica che si riscontra in sistemi multi core. Quando un dato presente nella RAM (o comunque in un livello inferiore e condiviso dai core nella gerarchia) viene richiesto da più processori e tale dato viene successivamente modificato, allora sarà necessario, per coerenza delle informazioni. Fare in modo che la modifica venga propagata a tutti. Per far ciò possono essere implementate due tipi di policy.
  -  *Write Through Policy:* i dati vengono immediatamenti riscritti in memoria dopo la loro modifica
  -  *Write Back Policy:* salva in memoria solo dopo che i dati devono essere espulsi dalla cache.
  -  *Cache to Cache Coherence in Symmetric Multi Processors:* per garantire la coerenza viene utilizzato lo snooping protocol che controlla se i dati vengono modificati. Assume comunque una write through policy. Per garantirne la coerenza effettua una invalidazione del dato o un aggiornamento.
  -  *Cache Invalidate vs. Cache Update:* 
  -  *False sharing:*
  -  *External memory model vs Cache oblivious:*
  -  *Teoremi:*
  -  *Dot product:*
     - *Column Major Order:*
     - *Trasposta matrice B:* per moltiplicare le magtrici efficacemente riga per riga faccio la trasposta $B^T$
     - *Divide and conquer:* $1 + N/B$ trasferimenti con $N$ grandezza della matrice e B lunghezza del blocco della cache. $N_{Trans} = n^2 + n^3/B$. Quindi utilizziamo la divisione a blocchi. Ogni blocco è grande $S*S$ tali che $3*S^2 = M$. Così metto piccoli blocchi in memoria. No layout rows/columns major. Caso base: ho spazio per solo tre blocchi. Complessità: $=(n^2/B + n^3/B*sqrt(N))$
     - *Z-order:*
     - *Bit:*


## 07 - Cache oblivious II
- Funnelsort
- 

## 08 - Threads
- *Formule:* 
  - *Total Overhead:* $T = p*T_p - T_s$
  - *Speedup:* $S = T_s/T_p$
  - *Efficiency:* $E = S/p$
- *Amdahl's law:* Permette di calcolare il massimo improvment possibile parallelizzando la parte parallelela $(1 + /(fs + ((1-f)/p)))$
- *Gustavson law:* Detta anche legge dello speed up, descrive come si comporta la scalabilità del sistema quando $n \rightarrow ∞$. Ci dice che $lim\ n\ \rightarrow ∞ S_p(n) = p$
- *Shared memory programs models:* 
  - *Process:* 
  - *Thread:* 
  - *Direct:* 
- *Into the threads:* 
- *Mutex:* 
  - *Race condition:* 
  - *Critical section:* 
  - *Mutual Exclusion:* 
- *Mutex wrapped:* 
  - *Lock guard:* 
  - *Shared Lock:* 
  - *Unique Lock:* 
  - *(all lock):* 
- *Condition variable:* 
- *Promise, Future, Async:* 

## 09 - OpenMP
- *What is it and how it works*
Il programma viene eseguito linearmente finchè non trova le clausole di openMP. Quindi il primo thread che entra enl blocco diventa il master.
  - *Pragma omp directive...*
- *Basic clauses*
  - *Degreee of concurrency (num_threds)*
  - *Condition parallelism*
- *Variable sharing clausoles*
- *Reduction clause:* indica come gestire l'ultima computazione sulla variabile
- *Specifying task (for/section)*
- *Assign iteration to threads*
  - *Scheduler statistic, dynamic, guided*
  - *no wai /orderd*
- *Sync directive*
  - *barrier, single, master, atomic, critical*
- *Env var*
  - *OMP_NUM_THREADS*
  - *OMP_SET_DYNAMIC*
  - *OMP_NESTED*
  - *OMP_SCHEDULE*
- *OpenMP vs Thread*
  - Con openMP è più facile
  - la gestione esplicita hai più controllo di overhad, sui dati, condivisione, falsa sharing...


## 10 - Patterns
- Pattern of parallelism
  - Concurrent tasks structures
  - Data partitioning techniques
  - Communication structures
- *Classificazione in base alla decomposizione:*
  - data parallelism (finer grain)
    - IN (decompone in task indipendenti l'input e fa un merge economico)
    - OUT (decomposizione in task indipendenti sullo stesso input. Ogni output è indipendente dall'altro)
  - task parallelism (coarse grain)
  - stream parallelism (pipeline)
  - Combination of the above (eg.: task + data)
- Task dependency graph (TDG)
  - decompone il problema in task paralleli
  - Granularità (fine o coarse)
  - Archi sono le dipendenze tra i nodi
  - Nodi sono i task, label interna costo
- Parallelismo degree (numero di task eseguiti in parallelo)
- Critical path
  - Directed path (se quenza di task collegati da dipen d)
  - lunghezza del path (somma dei pesi dei nodi)
  - Critical path (path con costo maggiore)
  - avg paralllem degree
- TIG Task interaction grapgh
  - Nodi task
  - Archi iterazioni (con label costo dell'iterazione)
- Mapping: come assegnare i task al processore
  - Mapping sulla base di DAG E TIG che aiutano a bilanciare e diminuire il tempo di esec
- TDG in livelli
  - Livelli sono indipendenti
  - Il max critical path lo calcoli in verticale
- Prefix sum 
  - Naive: O(n)
  - Naive parallel: O(nlog(n))
  - Exclusive: (log(n)) con fasi upsweep e down sweep --> O(n)

## 11 - Pattern of Parallelism II
- *Pattern di parallelismo:* sono soluzioni per gestire il parallelismo.
  - Thread pool
    - Emitters, workers, collectors
    - best threadpool size = Towrk/Tcomm
    - improv: pre-fetching
  - Dividi and conquer
    - Decomponi in problemi più piccoli e usa ricorsione
  - pipeline
    - diversi stage, asintoticamente raggiunge uno speed up ugualer al numero di stage. Stage eseguiti parallelamente
    - non bilanciata, dipende dallo stage più lento (conviene spezzarla in task minori o parallelizzare gli stage più pesanti)
  - Exploratory search
    - tutte le possibilità. Indipendenza tra le varie soluzioni
    - Tecnica exploratory decomposition
    - serial work vs parallel work
    - task explored in depth-first
  - Dynamic Task Creation
    - Creazione dinamica di tassk
    - Partener selection
      - Global round robin
      - Local round Robin
- Quick sort
  - Naive parallel : sbilanciato
  - Figo parallel: divide per processori, prende pivot, fa local arrangment, poi il global alla fine
  - Alto parallel degreee, balanced
- Odd Even trasposition
  - Confronta gli elementi con i loro vicini a coppie prima partendo dai pari poi da dispari e così via per n passaggi.
- Bitonic sort
  - Bitonic sequence: sequenza che decrescee poi cresce monotonicamente

## 12 - Map Reduce
- Cluster structure
- Sequence vs Map File
- Main functions to implement
  - map(k1,v1) --> list(k2,v2) split the output in different list
  - reduce(k2, list(v2))àlist(k3,v3) reduce a portion of the output to keyes
- shuffle: fase intermedia che manda i dati verso i giusti reducer
- Coordination of map reduce
  - Task status
  - quando un task viene completato invia al master la posizioen dove trovarlo
  - il master da le info ai reducer
- Fallimenti
- Combine
- backup task
- MapReduce si perchè?

## 13 - Spark
- *Metodi:*
  - *Azioni:* Count, etc. Triggera Spark (quindi hanno la priorità) e non modificano RDD ma lo scansionano in lettura e basta.
  - *Trasformazioni:* Map, FlatMap, applicano trasformazioni sul RDD.
- *Driver and workers:*
  - Il driver contiene il main del programma, è il programma principale
  - Workers sono le unità di elaborazione
- *Resiliend Distributed Dataset (RDD):* struttura dati salvata in locale. 
- *Transformation and actions (lazy evaluation):*

## 14 - Patterns of parallelism III
- *Document all pair similarity:* un documento è un vettore $d$ che contiene $N$ elementi distinti. Il valore all'interno della cella $d[i]$ rappresenta la frequenza del termine $i$ nel testo $d$. Possiamo calcolare la similarità tra due documenti effettuando il prodotto dei valori nelle celle $i$.
  - *with map reduce:* la parola è la chiave, il valore la frequenza. La funzione di riduzione calcolerà la similarità.
```python
Map(doc_id, document):
  for each term t in document:
    emit(t,[doc_id,document])

Reduce(t, list([doc_id,document])):
  for id1,d1 in list([doc_id,document]):
    for id2,d2 in list([doc_id,document]):
      if sim(d1,d2)>=threshold:
        emit([id1, id2, sim(d1,d2)])
```
  - *Problema 1:* Replication, ovvero se un documento ha 1000 termini, esso verrà replicato 1000 volte.
    - *Soluzione:* Prefix filtering, riordina i termini nel documento in ordine di frequenza decrescente. Crea il documento dei massimi $d^*$, ovvero il vettore che conterrà i massimi di tutti documenti. Se $sim(d,d^*) < \epsilon$ (where $\epsilon$ is the threeshold), allora $d$ non ha documenti similari nel corpo. Sia $b(d)$ il termine maggiore tale che la similiarità al documento $d$ deve condividere con $d$ almeno un termine $t > b(d)$. Il seguente codice mostra come sarà le nuova funzione `Map`.
```python
Map(doc_id, document):
  for each term t in sorted(document):
    if t > b(document):
      emit(t,[doc_id,document])
```
  - *Problema 2:* Troppo output, ovvero se due documenti condividono 10 termini dopo il filtering, la loro similarità verrà calcolata 10 volte.
    - *Soluzione naive:* Riordina l'output e rimuovi i duplicati
    - *Soluzione buona:* Solo un reducer calcolerà la similarità. Ogni reducer ottiene i due documenti full e non capiscve quale sono i 10 termini in comune. Se il reducer ha la chiave equivalente al termine maggiore, calcolerà la similarità. Sotto il codice per la nuova versione del `Reducer`.
```python
Reduce(t, list([doc_id,document])):
  for id1,d1 in list([doc_id,document]):
    for id2,d2 in list([doc_id,document]):
      if t = max (d1 ∩ d2):
        if sim(d1,d2)>=threshold:
          emit([id1, id2, sim(d1,d2)])
```

## 15 - Patterns of parallelism IV
- *Vertex centric paradigm:* il problema nasce in quanto è difficile paralellizzare algoritmi basati su grafi. Per far permetterne la parallelizzazione nasce il `Vertex centric paradigm`.
```pseudocode
  ogni nodo conosce i propri vicini
  ogni nodo potrebbe contenere altre informazioni
  la stessa procedura chiave viene eseguita su ogni nodo
  ripeti fino alla convergenza
```
  - *Pro:* Permette di progettare facilmente un algoritmo locale per ogni singolo nodo. Alto grado di parallelismo. Implementabile in MapReduce
  - *Cons:* Potremmo non ottenere i risultati ottimali.
- *Label propagation:* permette di condividere una label che rispetta una certa condizione globale. Ad esempio, noi abbiamo visto al condivisione del minimo in tutti i nodi. Prende in input una lista di coppie di nodi `X` e `Y`, ovvero indica che vi è una connessione tra i due. Da in output `X` e `Z`
```algorithm
  Inizialmente il nodo `X` è sul componente con id `X`
  Il nodo `X` invia ai suoi vicini il proprio id
  Il nodo `X` riceve una lista di id delle componenti e tiene il minimo.
```
  - Complexity: $O(d)$ dove $d$ è il diametro. 
- *Hash to min:* Anche qui permette di diffondere all'interno del grafo il valore che rispetta una certa condizione. Nell'esempio visto trasmette il minimo. Prende in input una lista di coppie di nodi `X` e `Y`, ovvero indica che vi è una connessione tra i due. Da in output `X` e `Z`
```algorithm
  1. Inizialmente il nodo `X` conosce il cluster `C = X` e i suoi vicini
  2. Il nodo `X` invia a `C` il più piccolo nodo in `C`
  3. Il nodo `X` invia il nodo più piccolo di `C` in ogni altro nodo in `C`
  4. Il nodo `X`crea un nuovo `C` unendo tutti i messaggi ricevuti
  Ripeti fino alla convergenza dal punto 2.
```
  - Complexity: $O(log(d))$ dove $d$ è il diametro. 

## 16 - GPU and CUDA
- *CPU vs GPU:*
  - CPU: A Control, some ALU and a cache. DRAM external.
  - GPU: One control and one cache for many rows of ALUs.
- *CUDA:* Compute Unified Device Architecture
- *Host:* will be the machine that run serially or modestly parallel code
- *Device:* external device that runs massive data parallelism. Tipically a GPU or TPU (Tensor Process Unit).
- *Kernel:* C function that will be executed in parallel with CUDA threds.
- *Thread hierarchy:*  i threads sono organizzati in blocchi.
  - *Grid:*
  - *Block:* i thread nello stesso blocco condividono lo stesso processore e memoria. Un blocco contiene anche 2048 threads. Threads di diversi blocchi possono cooperare scambiandosi informazioni tramite la shared memory.
  - *Thead (kernel..):*
- *Memory:*
  - *Global (coalescing):*
  - *Shared:*
  - *Registers:*
- *Automatic scalability:*
- *Vector add (example):*
- *Dot product:*
- *Thread scheduling and divergence:*
- *Matrix transpose example:*