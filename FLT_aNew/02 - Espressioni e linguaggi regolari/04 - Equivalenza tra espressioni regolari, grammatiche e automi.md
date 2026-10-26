# Equivalenza tra espressioni regolari, grammatiche e automi

## 1. Tre modi per descrivere gli stessi linguaggi

Espressioni regolari, grammatiche regolari e automi finiti descrivono esattamente la stessa classe di linguaggi.

Per un linguaggio $L\subseteq\Sigma^*$, sono equivalenti le seguenti affermazioni:

1. Esiste un’espressione regolare $r$ tale che $L=L(r)$.
2. Esiste una grammatica regolare $G$ tale che $L=L(G)$.
3. Esiste un automa finito che riconosce $L$.

L’equivalenza tra espressioni regolari e automi finiti è nota come **teorema di Kleene**.

> [!important]
> I tre formalismi sono equivalenti per potere espressivo, ma svolgono ruoli diversi:
>
> - un’espressione regolare descrive un linguaggio;
> - una grammatica genera le sue stringhe;
> - un automa riconosce le stringhe che vi appartengono.

## 2. Che cosa significa equivalenza

Due descrizioni sono equivalenti quando individuano esattamente lo stesso insieme di stringhe.

Per esempio, un automa $M$ e una grammatica $G$ sono equivalenti se:

$$
L(M)=L(G)
$$

Per dimostrarlo bisogna verificare entrambe le direzioni:

$$
w\in L(G)\implies w\in L(M)
$$

e:

$$
w\in L(M)\implies w\in L(G)
$$

Non devono avere lo stesso numero di stati o non terminali, né una struttura identica.

### Determinismo degli automi

Anche i seguenti modelli riconoscono la stessa classe di linguaggi:

- automi finiti deterministici;
- automi finiti non deterministici;
- automi finiti non deterministici con epsilon-mosse.

Il non determinismo e le epsilon-mosse possono rendere una costruzione più comoda, ma non aumentano il potere espressivo degli automi finiti.

## 3. Da automa finito a grammatica lineare destra

Questa conversione è particolarmente importante per l’esame.

Sia:

$$
M=(Q,\Sigma,\delta,q_0,F)
$$

un automa finito, eventualmente non deterministico e con epsilon-mosse.

### Non terminali

A ogni stato $q\in Q$ associamo un non terminale:

$$
A_q
$$

### Simbolo iniziale

Lo stato iniziale determina il simbolo iniziale:

$$
S=A_{q_0}
$$

### Produzioni per le transizioni

Per ogni transizione:

$$
q\xrightarrow{a}p
$$

inseriamo:

$$
A_q\to aA_p
$$

Per ogni epsilon-transizione:

$$
q\xrightarrow{\varepsilon}p
$$

inseriamo:

$$
A_q\to A_p
$$

### Produzioni per gli stati finali

Per ogni stato finale $q\in F$, inseriamo:

$$
A_q\to\varepsilon
$$

Questa produzione permette di terminare la derivazione quando il cammino dell’automa ha raggiunto uno stato finale.

> [!warning]
> Uno stato finale può avere transizioni uscenti.
>
> Il non terminale corrispondente può quindi avere sia produzioni che continuano la generazione, sia la produzione con $\varepsilon$.

### Perché la costruzione funziona

Ogni transizione su un simbolo corrisponde alla generazione di quel simbolo.

Un cammino:

$$
q_0\xrightarrow{a_1}q_1
\xrightarrow{a_2}\cdots
\xrightarrow{a_n}q_n
$$

corrisponde alla derivazione:

$$
A_{q_0}
\Rightarrow
a_1A_{q_1}
\Rightarrow
a_1a_2A_{q_2}
\Rightarrow\cdots\Rightarrow
a_1\cdots a_nA_{q_n}
$$

Se $q_n$ è finale, possiamo concludere:

$$
a_1\cdots a_nA_{q_n}
\Rightarrow
a_1\cdots a_n
$$

Le epsilon-transizioni corrispondono invece a passi che cambiano non terminale senza aggiungere terminali.

La corrispondenza vale anche al contrario: una derivazione terminale descrive un cammino accettante.

## 4. Da grammatica lineare destra ad automa finito

Consideriamo una grammatica con produzioni nella forma:

$$
A\to aB
\qquad
A\to a
\qquad
A\to\varepsilon
$$

A ogni non terminale associamo uno stato.

Il simbolo iniziale della grammatica determina lo stato iniziale.

### Produzione A → aB

Inseriamo la transizione:

$$
A\xrightarrow{a}B
$$

### Produzione A → a

Introduciamo uno stato finale ausiliario $f$ e inseriamo:

$$
A\xrightarrow{a}f
$$

Lo stesso stato $f$, nuovo e senza transizioni uscenti, può essere utilizzato per tutte le produzioni di questo tipo.

### Produzione A → ε

Rendiamo finale lo stato associato ad $A$.

### Produzione unitaria A → B

Se la grammatica ammette anche produzioni unitarie, inseriamo:

$$
A\xrightarrow{\varepsilon}B
$$

### Produzioni con più terminali

Una produzione come:

$$
A\to abcB
$$

può essere prima scomposta introducendo non terminali nuovi, oppure rappresentata da una catena di transizioni su `a`, `b` e `c`.

> [!important]
> L’automa ottenuto può essere non deterministico.
>
> Per esempio, dalle produzioni $A\to aB$ e $A\to aC$ si ottengono due possibili destinazioni leggendo `a`.

## 5. Esempio completo: il linguaggio a*b

Consideriamo:

$$
L=\{a^n b\mid n\geq 0\}
$$

### Espressione regolare

$$
r=a^*b
$$

### Grammatica lineare destra

$$
S\to aS\mid b
$$

La produzione ricorsiva genera le `a`; quella terminale genera la `b` conclusiva.

### Automa finito

Possiamo usare due stati:

- $q_0$: iniziale;
- $q_1$: finale.

La funzione di transizione, espressa come insiemi di destinazioni, è:

| Stato | a | b |
|---|---|---|
| $q_0$ | $\{q_0\}$ | $\{q_1\}$ |
| $q_1$ | $\varnothing$ | $\varnothing$ |

Il valore $\varnothing$ indica che non esiste una transizione.

L’automa legge un numero arbitrario di `a`, poi una `b`, dopo la quale non può consumare altri simboli.

Accetta:

$$
b,\quad ab,\quad aab,\quad aaab,\ldots
$$

Non accetta $\varepsilon$, `a`, `bb` o `aba`.

### Conversione dell’automa in grammatica

Applicando il metodo generale otteniamo:

$$
A_0\to aA_0\mid bA_1
$$

$$
A_1\to\varepsilon
$$

Per esempio:

$$
A_0\Rightarrow aA_0
\Rightarrow aaA_0
\Rightarrow aabA_1
\Rightarrow aab
$$

La grammatica ottenuta non è identica a $S\to aS\mid b$, ma genera lo stesso linguaggio.

## 6. Da espressione regolare ad automa

Una costruzione standard è quella di **Thompson**, che produce un automa con epsilon-mosse.

L’idea è costruire un piccolo frammento per ogni sottoespressione e poi collegare i frammenti.

Ogni frammento ha uno stato di ingresso e uno di uscita. Si utilizzano stati distinti quando si combinano frammenti diversi.

### Casi base

- Per $\varnothing$: ingresso e uscita senza alcun cammino che li colleghi.
- Per $\varepsilon$: una epsilon-transizione dall’ingresso all’uscita.
- Per $a$: una transizione etichettata `a` dall’ingresso all’uscita.

### Unione

Per:

$$
r\mid s
$$

introduciamo un nuovo ingresso e una nuova uscita.

Dall’ingresso possiamo scegliere, tramite epsilon-transizioni, il frammento di $r$ oppure quello di $s$.

Le uscite dei due frammenti sono collegate alla nuova uscita tramite epsilon-transizioni.

### Concatenazione

Per:

$$
rs
$$

colleghiamo l’uscita del frammento di $r$ all’ingresso di quello di $s$ con una epsilon-transizione.

Si attraversa prima $r$, poi $s$.

### Stella

Per:

$$
r^*
$$

aggiungiamo collegamenti che permettano:

- di saltare il frammento, accettando $\varepsilon$;
- di attraversarlo;
- di ripeterlo;
- di uscire dopo una ripetizione.

Le connessioni aggiunte sono epsilon-transizioni.

L’automa ottenuto può poi essere trasformato in uno deterministico mediante epsilon-chiusure e costruzione per sottoinsiemi.

## 7. Da automa a espressione regolare

Un metodo generale è l’**eliminazione degli stati**.

Durante la procedura, gli archi vengono etichettati con espressioni regolari che descrivono i possibili percorsi.

### Preparazione

Si introducono:

- un nuovo stato iniziale, collegato con $\varepsilon$ al precedente;
- un nuovo stato finale, raggiungibile con $\varepsilon$ da ogni precedente stato finale.

Tra due stati, le etichette di archi alternativi vengono unite con $\mid$.

Un collegamento assente ha etichetta $\varnothing$.

### Eliminazione di uno stato

Per eliminare uno stato $k$, aggiorniamo le etichette tra gli stati rimanenti.

La formula fondamentale è:

$$
R_{ij}
\leftarrow
R_{ij}
\mid
R_{ik}(R_{kk})^*R_{kj}
$$

Il nuovo collegamento permette:

- il percorso già disponibile, descritto da $R_{ij}$;
- oppure l’ingresso in $k$, zero o più giri sul suo ciclo e l’uscita verso $j$.

Dopo aver eliminato tutti gli stati intermedi, l’etichetta dal nuovo iniziale al nuovo finale descrive il linguaggio dell’automa.

> [!tip]
> Se lo stato eliminato non ha un ciclo, allora $R_{kk}=\varnothing$.
>
> Nella formula:
>
> $$
> (\varnothing)^*\equiv\varepsilon
> $$
>
> Il percorso attraverso lo stato resta quindi possibile senza compiere giri.

## 8. Come collegare tutte le conversioni

| Conversione richiesta | Procedimento |
|---|---|
| Espressione regolare → automa | Costruzione per sottoespressioni, per esempio Thompson |
| Automa → grammatica lineare destra | Stati come non terminali; transizioni come produzioni |
| Grammatica lineare destra → automa | Non terminali come stati; produzioni come transizioni |
| Automa → espressione regolare | Eliminazione degli stati |
| Espressione regolare → grammatica regolare | Passaggio attraverso un automa |
| Grammatica regolare → espressione regolare | Passaggio attraverso un automa |

Non è necessario trovare sempre una conversione diretta: si può utilizzare un formalismo intermedio.

## 9. Che cosa l’equivalenza non permette di concludere

### Non tutti i linguaggi context-free sono regolari

Una grammatica context-free può generare un linguaggio non riconoscibile da alcun automa finito.

Per esempio:

$$
S\to aSb\mid\varepsilon
$$

genera:

$$
\{a^n b^n\mid n\geq 0\}
$$

che non è regolare.

L’equivalenza di questa nota riguarda le grammatiche regolari, non tutte le grammatiche context-free.

### La costruzione non garantisce la minimalità

Un automa equivalente può avere stati ridondanti.

Determinizzazione e minimizzazione sono operazioni distinte:

- la determinizzazione elimina le scelte non deterministiche;
- la minimizzazione riduce un automa deterministico eliminando le distinzioni non necessarie tra stati.

Un automa deterministico non è automaticamente minimo.

## 10. Priorità per l’esame

Devi soprattutto saper:

- spiegare l’equivalenza tra i tre formalismi;
- convertire sistematicamente un automa in una grammatica lineare destra;
- gestire stati finali ed epsilon-transizioni nella conversione;
- riconoscere quando una costruzione produce un automa non deterministico;
- fornire espressione regolare e grammatica per un linguaggio individuato;
- distinguere equivalenza, determinismo e minimalità.

Per verificare una conversione, controlla almeno:

1. la presenza o assenza di $\varepsilon$;
2. alcune stringhe brevi accettate;
3. alcune stringhe che devono essere escluse;
4. il motivo generale per cui la corrispondenza vale in entrambe le direzioni.

## 11. Errori da evitare

- Dimenticare le produzioni con $\varepsilon$ per gli stati finali.
- Tradurre una epsilon-transizione come se consumasse un terminale.
- Invertire la direzione delle transizioni nella costruzione della grammatica.
- Pensare che lo stato finale debba essere privo di transizioni uscenti.
- Confondere un’espressione regolare equivalente con una semplice lista di esempi.
- Considerare deterministico o minimo qualsiasi automa ottenuto da una conversione.
- Applicare queste equivalenze a un linguaggio non regolare.