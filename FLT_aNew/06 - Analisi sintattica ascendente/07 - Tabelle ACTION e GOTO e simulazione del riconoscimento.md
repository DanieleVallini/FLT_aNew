# Tabelle ACTION e GOTO e simulazione del riconoscimento

## 1. Dall’automa LR(0) al riconoscitore

L’automa LR(0) descrive i possibili avanzamenti nei candidati. Per utilizzarlo durante il riconoscimento di una stringa, costruiamo due tabelle:

- **ACTION**, che stabilisce quale operazione eseguire;
- **GOTO**, che stabilisce lo stato da raggiungere dopo una riduzione.

Il riconoscitore mantiene:

1. una pila;
2. la parte di input ancora da leggere;
3. le tabelle ACTION e GOTO.

A ogni passo consulta lo stato in cima alla pila e il prossimo simbolo di input.

> [!important] Convenzione sulla pila
> In questa nota la cima della pila è a **destra**.
> La pila contiene alternativamente stati e simboli della grammatica, iniziando e terminando con uno stato.

Una configurazione della pila ha quindi la forma:

$$
s_0\,X_1\,s_1\,X_2\,s_2\cdots X_m\,s_m
$$

dove gli $X_i$ sono terminali o non terminali.

Lo stato $s_m$ riassume il cammino dell’automa relativo ai simboli attualmente presenti nella pila.

---

## 2. Il marcatore finale nella convenzione dell’esame

Manteniamo la produzione aumentata utilizzata nei temi d’esame:

$$
S'\to S\downarrow
$$

Il simbolo $\downarrow$ viene effettivamente letto mediante uno spostamento.

L’accettazione avviene quando:

- è stato raggiunto il candidato $[S'\to S\downarrow\cdot]$;
- l’input è terminato.

Per rendere esplicita questa situazione, nelle tabelle aggiungiamo una colonna chiamata **fine**.

> [!warning] Due concetti distinti
> $\downarrow$ è il marcatore presente nell’input.
> **fine** indica che non rimangono simboli da leggere: non è un ulteriore terminale e non viene spostato.

Quindi, per riconoscere la parola `bacc`, elaboriamo:

$$
bacc\downarrow
$$

Dopo lo spostamento di $\downarrow$, l’input residuo è $\varepsilon$.

---

## 3. Significato delle celle ACTION

La cella:

$$
\operatorname{ACTION}[i,t]
$$

indica l’operazione da eseguire quando:

- lo stato in cima alla pila è $i$;
- il prossimo simbolo è $t$, oppure siamo nella condizione **fine**.

Le possibilità sono:

| Contenuto | Significato |
|---|---|
| $s_j$ | Sposta il prossimo simbolo e inserisci lo stato $j$ |
| $r_k$ | Riduci mediante la produzione numero $k$ |
| `acc` | Accetta e termina |
| Cella vuota | Segnala un errore sintattico |

### Spostamento

Se:

$$
\operatorname{ACTION}[i,t]=s_j
$$

il riconoscitore:

1. consuma $t$ dall’input;
2. inserisce nella pila $t$ e poi $j$.

Per esempio:

$$
0\,S\,1
\quad\xrightarrow{\text{shift }a}\quad
0\,S\,1\,a\,3
$$

### Riduzione

Supponiamo che la produzione numero $k$ sia:

$$
A\to\beta
$$

e che:

$$
|\beta|=m
$$

Se ACTION prescrive $r_k$:

1. si eliminano dalla pila $m$ coppie simbolo-stato;
2. si legge lo stato $q$ rimasto in cima;
3. si consulta $\operatorname{GOTO}[q,A]$;
4. si inseriscono $A$ e lo stato ottenuto.

> [!important] Una riduzione non consuma input
> Il prossimo simbolo da leggere rimane lo stesso. Cambiano la pila e lo stato corrente.

Nella rappresentazione alternata si eliminano quindi **$2m$ elementi**.

Se si usa una rappresentazione contenente soltanto gli stati, si eliminano invece $m$ stati.

---

## 4. Significato della tabella GOTO

La cella:

$$
\operatorname{GOTO}[i,A]=j
$$

indica lo stato da inserire dopo aver riconosciuto il non terminale $A$ a partire dallo stato $i$.

La funzione GOTO dell’automa è definita per tutti i simboli della grammatica. Nella tabella del riconoscitore:

- le transizioni sui terminali producono azioni di spostamento in ACTION;
- le transizioni sui non terminali vengono riportate in GOTO.

> [!warning] Quale stato usare dopo una riduzione?
> GOTO si consulta usando lo stato rimasto in cima **dopo aver eliminato il corpo della produzione**.

Non si usa necessariamente lo stato nel quale è stata decisa la riduzione.

---

## 5. Costruzione delle tabelle LR(0)

Supponiamo di avere già costruito tutti gli stati dell’automa LR(0).

### 5.1 Spostamenti

Se:

$$
[A\to\alpha\cdot t\beta]\in I_i
$$

con $t$ terminale, e:

$$
\operatorname{GOTO}(I_i,t)=I_j
$$

allora inseriamo:

$$
\operatorname{ACTION}[i,t]=s_j
$$

### 5.2 Riduzioni

Se uno stato contiene un candidato completo:

$$
[A\to\beta\cdot]
$$

con $A\ne S'$, inseriamo la corrispondente riduzione in **tutte le colonne ACTION**, inclusa la colonna **fine**.

Questa è la regola di riduzione del metodo LR(0).

Le restrizioni delle riduzioni mediante FOLLOW verranno introdotte con il metodo SLR(1).

### 5.3 Accettazione

Nella convenzione adottata, se:

$$
[S'\to S\downarrow\cdot]\in I_i
$$

inseriamo:

$$
\operatorname{ACTION}[i,\text{fine}]=\mathrm{acc}
$$

La produzione aumentata viene trattata come caso speciale.

### 5.4 Transizioni sui non terminali

Se:

$$
\operatorname{GOTO}(I_i,A)=I_j
$$

con $A$ non terminale, inseriamo:

$$
\operatorname{GOTO}[i,A]=j
$$

Le celle rimaste vuote rappresentano errori.

> [!warning] Celle con più azioni
> Se le regole impongono azioni diverse nella stessa cella, esiste un conflitto. Non si sceglie arbitrariamente quale conservare.

---

## 6. Esempio completo: grammatica dell’esame del 2011

Consideriamo:

$$
\begin{aligned}
(0)\quad&S'\to S\downarrow\\
(1)\quad&S\to SA\\
(2)\quad&S\to b\\
(3)\quad&A\to aA\\
(4)\quad&A\to c
\end{aligned}
$$

Per questo esempio usiamo gli stati $J_0,\ldots,J_7$. La numerazione è indipendente da quella usata per l’esame di gennaio 2026 nella nota precedente.

### 6.1 Stati dell’automa

| Stato | Candidati LR(0) |
|---|---|
| $J_0$ | $S'\to\cdot S\downarrow$; $S\to\cdot SA$; $S\to\cdot b$ |
| $J_1$ | $S'\to S\cdot\downarrow$; $S\to S\cdot A$; $A\to\cdot aA$; $A\to\cdot c$ |
| $J_2$ | $S\to b\cdot$ |
| $J_3$ | $A\to a\cdot A$; $A\to\cdot aA$; $A\to\cdot c$ |
| $J_4$ | $A\to c\cdot$ |
| $J_5$ | $S\to SA\cdot$ |
| $J_6$ | $A\to aA\cdot$ |
| $J_7$ | $S'\to S\downarrow\cdot$ |

### 6.2 Tabella ACTION e GOTO

Nelle celle e nella pila scriviamo direttamente il numero dello stato.

| Stato | ACTION $a$ | ACTION $b$ | ACTION $c$ | ACTION $\downarrow$ | ACTION fine | GOTO $S$ | GOTO $A$ |
|---|---|---|---|---|---|---|---|
| 0 | — | $s_2$ | — | — | — | 1 | — |
| 1 | $s_3$ | — | $s_4$ | $s_7$ | — | — | 5 |
| 2 | $r_2$ | $r_2$ | $r_2$ | $r_2$ | $r_2$ | — | — |
| 3 | $s_3$ | — | $s_4$ | — | — | — | 6 |
| 4 | $r_4$ | $r_4$ | $r_4$ | $r_4$ | $r_4$ | — | — |
| 5 | $r_1$ | $r_1$ | $r_1$ | $r_1$ | $r_1$ | — | — |
| 6 | $r_3$ | $r_3$ | $r_3$ | $r_3$ | $r_3$ | — | — |
| 7 | — | — | — | — | `acc` | — | — |

Il simbolo `—` indica una cella vuota.

Per esempio:

- nello stato 0 si può spostare `b`, raggiungendo 2;
- nello stato 2 si riduce sempre mediante $S\to b$;
- dopo questa riduzione, $\operatorname{GOTO}[0,S]=1$;
- nello stato 7 si accetta soltanto a input terminato.

Ogni cella contiene al massimo un’azione: la grammatica è **LR(0)**.

---

## 7. Simulazione di `bacc↓`

La pila iniziale contiene soltanto lo stato 0.

Ogni riga mostra la configurazione **prima** dell’azione indicata.

| Passo | Pila | Input residuo | Azione |
|---|---|---|---|
| 0 | `0` | `bacc↓` | $s_2$: sposta `b` |
| 1 | `0 b 2` | `acc↓` | $r_2$: $S\to b$ |
| 2 | `0 S 1` | `acc↓` | $s_3$: sposta `a` |
| 3 | `0 S 1 a 3` | `cc↓` | $s_4$: sposta `c` |
| 4 | `0 S 1 a 3 c 4` | `c↓` | $r_4$: $A\to c$ |
| 5 | `0 S 1 a 3 A 6` | `c↓` | $r_3$: $A\to aA$ |
| 6 | `0 S 1 A 5` | `c↓` | $r_1$: $S\to SA$ |
| 7 | `0 S 1` | `c↓` | $s_4$: sposta `c` |
| 8 | `0 S 1 c 4` | `↓` | $r_4$: $A\to c$ |
| 9 | `0 S 1 A 5` | `↓` | $r_1$: $S\to SA$ |
| 10 | `0 S 1` | `↓` | $s_7$: sposta `↓` |
| 11 | `0 S 1 ↓ 7` | $\varepsilon$ | `acc` |

La parola viene accettata.

### 7.1 Prima riduzione di `c`

Al passo 4 abbiamo:

$$
0\,S\,1\,a\,3\,c\,4
$$

Riduciamo mediante:

$$
A\to c
$$

Eliminiamo `c 4`, ottenendo:

$$
0\,S\,1\,a\,3
$$

Lo stato ora in cima è 3. Poiché:

$$
\operatorname{GOTO}[3,A]=6
$$

otteniamo:

$$
0\,S\,1\,a\,3\,A\,6
$$

### 7.2 Riduzione di `aA`

Ora applichiamo:

$$
A\to aA
$$

Il corpo contiene due simboli: eliminiamo le due coppie `a 3` e `A 6`.

Rimane:

$$
0\,S\,1
$$

Poiché:

$$
\operatorname{GOTO}[1,A]=5
$$

la nuova pila è:

$$
0\,S\,1\,A\,5
$$

### 7.3 Riduzione di `SA`

Con:

$$
S\to SA
$$

eliminiamo `S 1 A 5`.

Rimane lo stato 0 e consultiamo:

$$
\operatorname{GOTO}[0,S]=1
$$

ottenendo:

$$
0\,S\,1
$$

Durante queste tre riduzioni l’input residuo è rimasto sempre `c↓`.

> [!important] Avere riconosciuto $S$ non basta per accettare
> Al passo 7 la pila contiene già $S$, ma rimane una `c` da elaborare.
> L’accettazione richiede il completamento dell’intero input e della produzione aumentata.

---

## 8. Riduzioni mediante produzioni epsilon

Se una produzione è:

$$
A\to\varepsilon
$$

il corpo ha lunghezza zero.

Quando la tabella prescrive questa riduzione:

1. non si elimina alcun elemento dalla pila;
2. si legge lo stato corrente $q$;
3. si inseriscono $A$ e $\operatorname{GOTO}[q,A]$.

In forma schematica:

$$
\gamma\,q
\quad\longmapsto\quad
\gamma\,q\,A\,j
$$

dove:

$$
j=\operatorname{GOTO}[q,A]
$$

L’input rimane invariato.

> [!warning] Epsilon non viene spostato
> Una produzione vuota provoca una riduzione con zero simboli da rimuovere. Non introduce un simbolo $\varepsilon$ nella pila o nell’input.

Questa regola sarà fondamentale per la grammatica dell’esame di gennaio 2026, che contiene $A\to\varepsilon$ e $B\to\varepsilon$.

---

## 9. Procedura da seguire all’esame

1. Numera le produzioni.
2. Costruisci l’automa LR(0) completo.
3. Inserisci gli spostamenti dalle transizioni sui terminali.
4. Inserisci GOTO dalle transizioni sui non terminali.
5. Inserisci le riduzioni secondo il metodo richiesto.
6. Inserisci l’accettazione secondo la convenzione sul marcatore.
7. Controlla le celle con più azioni.
8. Se la tabella è deterministica, simula l’input indicando pila, input residuo e azione.

Durante la simulazione controlla soprattutto:

- la riduzione non consuma input;
- il numero di coppie eliminate coincide con la lunghezza del corpo;
- GOTO usa lo stato scoperto dopo l’eliminazione;
- una produzione epsilon non elimina coppie;
- una cella vuota causa errore;
- un conflitto richiede un’analisi, senza introdurre priorità arbitrarie.

La grammatica di gennaio 2026, costruita nella nota precedente, presenta conflitti LR(0). Per simularla deterministicamente useremo la tabella SLR(1) nella nota dedicata.

## Collegamenti

- [[05 - Chiusura e funzione GOTO]]
- [[06 - Costruzione dell automa LR0]]
- [[08 - Conflitti e classificazione LR0]]
- [[10 - Analisi SLR1 e risoluzione dei conflitti]]