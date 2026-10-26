# Metodi sintattici e principali strutture sintattiche

## 1. Che cosa sono i metodi sintattici

I **metodi sintattici** utilizzano regole formali per descrivere e analizzare la struttura delle stringhe di un linguaggio.

Una grammatica descrive come costruire le frasi ammesse. Un analizzatore sintattico verifica se una sequenza di simboli rispetta queste regole e ne ricostruisce la struttura.

Le domande fondamentali sono:

- La stringa appartiene al linguaggio?
- Da quali componenti è formata?
- Come sono organizzate queste componenti?

Per esempio, davanti all’espressione:

`a + b * c`

non basta riconoscere i singoli simboli: bisogna anche stabilire come sono raggruppati.

## 2. Analisi lessicale, sintattica e semantica

Questi livelli svolgono compiti diversi.

### Analisi lessicale

Trasforma la sequenza di caratteri in una sequenza di **token**, cioè unità lessicali come:

- identificatori;
- numeri;
- parole chiave;
- operatori;
- delimitatori.

Per esempio:

`totale := prezzo + 3`

può essere rappresentata come:

`id := id + num`

I nomi concreti degli identificatori e il valore del numero possono essere conservati come informazioni associate ai token.

### Analisi sintattica

Verifica che la sequenza di token rispetti la grammatica e ne ricostruisce la struttura.

Per esempio:

`id := id + num`

può essere un assegnamento valido.

Invece:

`id := +`

è incompleto rispetto a una grammatica che richiede due operandi per l’operatore `+`.

### Analisi semantica

Controlla proprietà che riguardano il significato e l’uso delle componenti, come:

- dichiarazione delle variabili;
- compatibilità dei tipi;
- corretto utilizzo di funzioni e identificatori.

Un assegnamento può essere sintatticamente corretto ma usare una variabile non dichiarata, in un linguaggio che ne richiede la dichiarazione.

> [!important]
> Sintatticamente corretto non significa automaticamente semanticamente corretto.

## 3. Le principali strutture sintattiche

Molte costruzioni dei linguaggi possono essere descritte combinando pochi schemi:

| Struttura | Significato | Esempio |
|---|---|---|
| Concatenazione | Componenti in un ordine stabilito | Nome, due punti, tipo |
| Alternativa | Scelta tra possibilità diverse | Tipo intero oppure reale |
| Opzionalità | Componente presente oppure assente | Parte facoltativa |
| Ripetizione | Zero o più occorrenze | Sequenza di simboli |
| Lista | Elementi separati da delimitatori | Identificatori separati da virgole |
| Annidamento | Strutture contenute in strutture dello stesso tipo | Parentesi o record annidati |

Le grammatiche dei prossimi esempi sono indipendenti tra loro.

## 4. Concatenazione e alternativa

### Concatenazione

La produzione:

$$
D\to \mathtt{id}\ \mathtt{:}\ T
$$

descrive una dichiarazione composta da:

1. un identificatore;
2. il simbolo `:`;
3. un tipo.

L’ordine è fissato dalla produzione.

### Alternativa

Le produzioni:

$$
T\to \mathtt{integer}\mid\mathtt{real}
$$

permettono di scegliere tra due tipi.

Combinando le produzioni possiamo ottenere:

$$
D\Rightarrow \mathtt{id}\ \mathtt{:}\ T
\Rightarrow \mathtt{id}\ \mathtt{:}\ \mathtt{integer}
$$

oppure:

$$
D\Rightarrow \mathtt{id}\ \mathtt{:}\ T
\Rightarrow \mathtt{id}\ \mathtt{:}\ \mathtt{real}
$$

> [!warning]
> Il simbolo $\mid$ separa produzioni alternative: non è un terminale del linguaggio.

## 5. Opzionalità e ripetizione

### Componente opzionale

Consideriamo:

$$
S\to aAb
$$

$$
A\to c\mid\varepsilon
$$

Il non terminale $A$ rappresenta una componente facoltativa.

Il linguaggio è:

$$
L(G)=\{ab,acb\}
$$

La produzione con $\varepsilon$ permette di omettere la componente.

### Zero o più ripetizioni

La grammatica:

$$
R\to aR\mid\varepsilon
$$

genera:

$$
L(G)=\{a^n\mid n\geq 0\}
$$

La produzione ricorsiva aggiunge una `a`; quella con $\varepsilon$ termina la ripetizione.

### Una o più ripetizioni

La grammatica:

$$
R\to aR\mid a
$$

genera invece:

$$
L(G)=\{a^n\mid n\geq 1\}
$$

> [!important]
> Il caso base stabilisce se la ripetizione possa essere vuota.
>
> Prima di usare una produzione con $\varepsilon$, controlla che il linguaggio richiesto ammetta davvero l’assenza della componente.

## 6. Liste con separatore

Una lista non vuota di identificatori separati da virgole può essere descritta da:

$$
L\to \mathtt{id}
\mid
\mathtt{id}\ \mathtt{,}\ L
$$

Esempi di stringhe generate:

- `id`
- `id,id`
- `id,id,id`

La grammatica non genera:

- la stringa vuota;
- una virgola iniziale;
- una virgola finale;
- due virgole consecutive.

Una derivazione è:

$$
L
\Rightarrow
\mathtt{id}\ \mathtt{,}\ L
\Rightarrow
\mathtt{id}\ \mathtt{,}\ \mathtt{id}
$$

### Ricorsione destra e sinistra

La produzione:

$$
L\to \mathtt{id}\ \mathtt{,}\ L
$$

è direttamente ricorsiva a destra.

Una grammatica alternativa è:

$$
L\to \mathtt{id}
\mid
L\ \mathtt{,}\ \mathtt{id}
$$

che utilizza ricorsione a sinistra e genera le stesse liste.

La scelta della forma può influenzare il funzionamento dell’analizzatore.

In particolare, la ricorsione sinistra diretta può causare ricorsione infinita in un analizzatore discendente ingenuo. Non costituisce invece un divieto generale per l’analisi LR.

> [!tip] Collegamento con l’esame
> Le liste servono per costruire grammatiche di dichiarazioni, campi di record e sequenze di istruzioni.
>
> Controlla sempre se la lista debba essere vuota o non vuota e dove possano comparire i separatori.

## 7. Annidamento

L’annidamento permette di inserire una struttura dentro un’altra struttura dello stesso tipo.

Esempi:

- parentesi dentro altre parentesi;
- record contenenti record;
- blocchi di istruzioni dentro altri blocchi.

### Parentesi bilanciate

La grammatica:

$$
S\to \mathtt{(}\ S\ \mathtt{)}\ S
\mid
\varepsilon
$$

genera le sequenze di parentesi correttamente bilanciate.

Per esempio:

- $\varepsilon$
- `()`
- `(())`
- `()()`
- `(()())`

La produzione ricorsiva descrive:

1. una parentesi aperta;
2. una sequenza bilanciata interna;
3. una parentesi chiusa;
4. un’ulteriore sequenza bilanciata.

L’ultimo $S$ consente di affiancare più gruppi.

### Quantità uguali non basta

La stringa:

`)(`

contiene lo stesso numero di parentesi aperte e chiuse, ma non è bilanciata.

Per essere bilanciata deve rispettare due condizioni:

1. in ogni prefisso, il numero delle parentesi aperte è almeno pari a quello delle chiuse;
2. nella stringa completa, i due numeri sono uguali.

### Semplice annidamento e sequenze di gruppi

La grammatica:

$$
S\to \mathtt{(}\ S\ \mathtt{)}
\mid
\varepsilon
$$

genera soltanto:

$$
\{\mathtt{(}^{\,n}\mathtt{)}^{\,n}\mid n\geq 0\}
$$

Include `(())`, ma non `()()`.

> [!warning]
> Due grammatiche che descrivono entrambe delle parentesi non generano necessariamente lo stesso linguaggio.
>
> Verifica se permettono soltanto l’annidamento o anche la concatenazione di gruppi.

## 8. Espressioni: precedenza e associatività

Le espressioni richiedono di stabilire come si raggruppano gli operandi.

### Una grammatica ambigua

Consideriamo:

$$
E\to E+E\mid E*E\mid\mathtt{id}
$$

La stringa:

`id + id * id`

ammette due strutture:

$$
\mathtt{id}+(\mathtt{id}*\mathtt{id})
$$

oppure:

$$
(\mathtt{id}+\mathtt{id})*\mathtt{id}
$$

Le parentesi qui evidenziano i raggruppamenti, non sono simboli aggiuntivi della stringa originale.

La grammatica non stabilisce una precedenza tra `+` e `*`.

### Separare i livelli di precedenza

Una grammatica adatta è:

$$
E\to E+T\mid T
$$

$$
T\to T*F\mid F
$$

$$
F\to \mathtt{(}\ E\ \mathtt{)}\mid\mathtt{id}
$$

I livelli sono:

- $E$: espressioni con addizioni;
- $T$: termini con moltiplicazioni;
- $F$: fattori, cioè identificatori o espressioni tra parentesi.

La moltiplicazione ha precedenza sull’addizione perché viene costruita nel livello dei termini.

### Associatività

L’associatività stabilisce il raggruppamento tra operatori dello stesso livello.

Per esempio, con:

$$
E\to E-T\mid T
$$

una sequenza di sottrazioni viene raggruppata a sinistra:

$$
a-b-c
\quad\longrightarrow\quad
(a-b)-c
$$

Con una struttura ricorsiva a destra:

$$
E\to T-E\mid T
$$

il raggruppamento è invece:

$$
a-(b-c)
$$

La distinzione è importante perché i risultati possono essere diversi.

> [!important]
> Precedenza e associatività sono proprietà distinte:
>
> - la precedenza riguarda operatori di livelli diversi;
> - l’associatività riguarda il raggruppamento all’interno dello stesso livello.

## 9. Riconoscimento e analisi sintattica

Il **riconoscimento** risponde alla domanda:

$$
w\in L(G)\ ?
$$

L’**analisi sintattica**, o parsing, ricostruisce anche la struttura della frase, per esempio attraverso un albero di derivazione o una sequenza di produzioni.

Non basta quindi sapere che una stringa è ammessa: per tradurla bisogna sapere come è composta.

### Analisi discendente

Parte dal simbolo iniziale e cerca di costruire la stringa:

$$
S\Rightarrow^*w
$$

La struttura viene sviluppata dalla radice verso le foglie.

### Analisi ascendente

Parte dai simboli della stringa e ricostruisce progressivamente strutture più grandi, fino al simbolo iniziale.

Per esempio, con:

$$
S\to aSb\mid c
$$

la derivazione:

$$
S\Rightarrow aSb\Rightarrow acb
$$

può essere ricostruita al contrario attraverso le riduzioni:

$$
acb\rightsquigarrow aSb\rightsquigarrow S
$$

Il simbolo $\rightsquigarrow$ indica qui una riduzione, non una produzione.

L’analisi LR appartiene ai metodi ascendenti e sarà approfondita nelle note dedicate.

## 10. Dalla struttura alla traduzione

Dopo aver riconosciuto le componenti sintattiche, possiamo associarvi informazioni e operazioni.

Per esempio, nella struttura di:

`a + b * c`

possiamo:

- calcolare tipi e valori;
- verificare la compatibilità degli operandi;
- generare il codice della moltiplicazione;
- usare il risultato per generare il codice dell’addizione.

Questo collega l’analisi sintattica alla seconda parte dell’esame:

- attributi sintetizzati ed ereditati;
- regole semantiche;
- ordine di esecuzione delle azioni;
- generazione del codice.

Una grammatica non ambigua non è automaticamente LR(0) o SLR(1): queste proprietà richiedono verifiche specifiche.

## 11. Cosa saper fare per l’esame

- Riconoscere concatenazioni, alternative, ripetizioni e annidamenti.
- Costruire liste rispettando separatori e casi base.
- Distinguere quantità uguali da strutture correttamente annidate.
- Capire come una grammatica imponga precedenza e associatività.
- Distinguere riconoscimento, parsing e traduzione.
- Collegare l’analisi ascendente alle riduzioni e ai metodi LR.

## 12. Errori da evitare

- Confondere correttezza sintattica e correttezza semantica.
- Inserire $\varepsilon$ quando la struttura deve essere non vuota.
- Permettere involontariamente separatori iniziali o finali.
- Confondere un singolo gruppo annidato con una sequenza di gruppi.
- Pensare che uguali quantità di delimitatori garantiscano il bilanciamento.
- Affidare la precedenza alle consuetudini matematiche quando la grammatica non la esprime.
- Confondere la ricorsione sinistra con l’ambiguità.
- Concludere che una grammatica sia LR(0) soltanto perché non è ambigua.