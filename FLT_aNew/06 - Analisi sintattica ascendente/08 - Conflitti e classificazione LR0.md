# Conflitti e classificazione LR0

## 1. Significato di un conflitto

Durante la costruzione della tabella ACTION può accadere che due regole impongano **azioni diverse nella stessa cella**.

Questa situazione prende il nome di **conflitto**.

Per esempio:

$$
\operatorname{ACTION}[i,a]=\{s_j,r_k\}
$$

indica che, nello stato $i$ e con prossimo simbolo $a$, la costruzione prescrive sia uno spostamento sia una riduzione.

Una grammatica è **LR(0)** se la tabella costruita con le regole LR(0) contiene al massimo un’azione in ogni cella.

Le celle vuote rappresentano errori sintattici e sono ammesse.

> [!important] Conflitto ed errore sintattico
> Un conflitto riguarda la costruzione del riconoscitore per una grammatica.
> Un errore sintattico viene rilevato durante il riconoscimento di un particolare input.

I due tipi fondamentali di conflitto sono:

- **shift/reduce**, cioè spostamento/riduzione;
- **reduce/reduce**, cioè riduzione/riduzione.

Manteniamo la convenzione dell’esame:

$$
S'\to S\downarrow
$$

Il completamento della produzione aumentata viene gestito mediante l’accettazione, separatamente dalle riduzioni ordinarie.

---

## 2. Conflitto shift/reduce

Uno stato presenta un conflitto shift/reduce quando contiene contemporaneamente:

$$
[A\to\alpha\cdot]
$$

e:

$$
[B\to\beta\cdot a\gamma]
$$

dove $a$ è un terminale e la prima produzione è ordinaria, quindi $A\ne S'$.

Il primo candidato prescrive una riduzione.

Il secondo permette uno spostamento di $a$.

Nel metodo LR(0), la riduzione viene inserita in tutte le colonne ACTION. Di conseguenza, nella colonna $a$ compaiono entrambe le azioni.

### 2.1 Esempio

Consideriamo:

$$
\begin{aligned}
S'&\to S\downarrow\\
S&\to a\\
S&\to ab
\end{aligned}
$$

Dopo aver letto `a`, raggiungiamo uno stato contenente:

$$
I=
\{
[S\to a\cdot],
[S\to a\cdot b]
\}
$$

Il candidato:

$$
[S\to a\cdot]
$$

prescrive la riduzione mediante $S\to a$.

Il candidato:

$$
[S\to a\cdot b]
$$

permette lo spostamento di `b`.

Quindi:

$$
\operatorname{ACTION}[I,b]
=
\{
\text{riduci }S\to a,\;
\text{sposta }b
\}
$$

La grammatica non è LR(0).

Per capire il problema operativo:

- nell’input `a↓` occorre ridurre `a` a $S$;
- nell’input `ab↓` occorre prima spostare `b`.

La costruzione LR(0) prescrive la riduzione senza limitarla in base al prossimo simbolo e produce il conflitto.

---

## 3. Conflitto reduce/reduce

Uno stato presenta un conflitto reduce/reduce quando contiene due candidati completi relativi a produzioni ordinarie distinte:

$$
[A\to\alpha\cdot]
$$

$$
[B\to\beta\cdot]
$$

Le due produzioni possono anche avere lo stesso non terminale a sinistra, purché siano produzioni diverse.

Nel metodo LR(0), entrambe le riduzioni vengono inserite in tutte le colonne ACTION.

### 3.1 Esempio

Consideriamo:

$$
\begin{aligned}
S'&\to S\downarrow\\
S&\to Aa\mid Bb\\
A&\to c\\
B&\to c
\end{aligned}
$$

Dallo stato iniziale, leggendo `c`, si raggiunge:

$$
I=
\{
[A\to c\cdot],
[B\to c\cdot]
\}
$$

Il riconoscitore dovrebbe decidere se ridurre `c` ad $A$ oppure a $B$.

Nella costruzione LR(0):

$$
\operatorname{ACTION}[I,t]
=
\{
\text{riduci }A\to c,\;
\text{riduci }B\to c
\}
$$

per ogni colonna $t$.

La grammatica non è LR(0).

Il prossimo simbolo aiuterebbe a distinguere i due casi:

- per riconoscere `ca`, occorre ridurre mediante $A\to c$;
- per riconoscere `cb`, occorre ridurre mediante $B\to c$.

Questo esempio mostra che anche un conflitto reduce/reduce può dipendere dall’insufficienza del metodo utilizzato.

---

## 4. Criterio pratico per classificare una grammatica LR(0)

Dopo aver costruito l’intera collezione canonica, esaminiamo ogni stato.

Escludendo il candidato completo della produzione aumentata, controlliamo:

1. se sono presenti almeno due candidati completi distinti: esiste un conflitto **reduce/reduce**;
2. se è presente un candidato completo e un candidato con il punto immediatamente prima di un terminale: esiste un conflitto **shift/reduce**.

Se nessuno stato presenta questi conflitti, la grammatica è LR(0).

> [!warning] Un conflitto basta per escludere LR(0)
> Per dimostrare che una grammatica non è LR(0), basta esibire uno stato raggiungibile con un conflitto.
> Per dimostrare che è LR(0), bisogna controllare tutti gli stati.

### 4.1 Più candidati nello stesso stato

Uno stato può contenere molti candidati senza presentare conflitti.

Per esempio:

$$
I=
\{
[A\to\alpha\cdot a\beta],
[B\to\gamma\cdot b\delta]
\}
$$

permette uno spostamento su `a` e uno su `b`.

Le azioni occupano colonne diverse, quindi non competono nella stessa cella.

Anche più candidati che permettono lo spostamento dello stesso terminale sono compatibili: la funzione GOTO li riunisce in un unico stato di destinazione.

### 4.2 Determinismo dell’automa e determinismo del parser

La costruzione canonica produce un automa deterministico sui simboli della grammatica:

$$
\operatorname{GOTO}(I,X)
$$

ha un unico risultato per ogni coppia $(I,X)$.

Questo resta vero anche quando la grammatica non è LR(0).

I conflitti riguardano le **azioni del parser**, in particolare la scelta fra spostare e ridurre o fra riduzioni diverse.

> [!important]
> Un automa LR(0) con transizioni deterministiche può comunque produrre una tabella ACTION con conflitti.

### 4.3 Produzioni epsilon e transizioni sui non terminali

Una produzione:

$$
A\to\varepsilon
$$

produce il candidato completo:

$$
[A\to\cdot]
$$

La sua presenza richiede attenzione, ma non dimostra da sola che la grammatica non sia LR(0).

Consideriamo:

$$
\begin{aligned}
S'&\to S\downarrow\\
S&\to Ab\\
A&\to\varepsilon
\end{aligned}
$$

Lo stato iniziale è:

$$
I_0=
\{
[S'\to\cdot S\downarrow],
[S\to\cdot Ab],
[A\to\cdot]
\}
$$

Contiene una riduzione, ma nessun candidato con il punto immediatamente prima di un terminale.

Le transizioni su $S$ e $A$ alimentano la tabella GOTO e non competono con la riduzione nella tabella ACTION.

Anche gli altri stati sono privi di conflitti: questa grammatica è LR(0).

---

## 5. Applicazione all’esame del 26 gennaio 2026

Riprendiamo la numerazione delle produzioni e degli stati della nota sull’automa LR(0):

$$
\begin{aligned}
(0)\quad&S'\to S\downarrow\\
(1)\quad&S\to ABcS\\
(2)\quad&S\to d\\
(3)\quad&A\to aA\\
(4)\quad&A\to\varepsilon\\
(5)\quad&B\to bB\\
(6)\quad&B\to\varepsilon
\end{aligned}
$$

### 5.1 Un conflitto già nello stato iniziale

Lo stato $I_0$ contiene, fra gli altri:

$$
[A\to\cdot]
$$

$$
[A\to\cdot aA]
$$

$$
[S\to\cdot d]
$$

Il primo candidato prescrive la riduzione $r_4$.

Gli altri due permettono rispettivamente:

$$
\operatorname{GOTO}(I_0,a)=I_4
$$

$$
\operatorname{GOTO}(I_0,d)=I_3
$$

Quindi:

$$
\operatorname{ACTION}[0,a]=\{r_4,s_4\}
$$

$$
\operatorname{ACTION}[0,d]=\{r_4,s_3\}
$$

Questo basta per concludere:

$$
\boxed{\text{La grammatica non è LR(0).}}
$$

### 5.2 Tutti gli stati con conflitti

Esaminando l’intero automa si ottiene:

| Stato | Riduzione concorrente | Spostamenti concorrenti | Colonne con conflitto |
|---|---|---|---|
| $I_0$ | $r_4$: $A\to\varepsilon$ | Su `a`: $s_4$; su `d`: $s_3$ | `a`, `d` |
| $I_2$ | $r_6$: $B\to\varepsilon$ | Su `b`: $s_7$ | `b` |
| $I_4$ | $r_4$: $A\to\varepsilon$ | Su `a`: $s_4$ | `a` |
| $I_7$ | $r_6$: $B\to\varepsilon$ | Su `b`: $s_7$ | `b` |
| $I_9$ | $r_4$: $A\to\varepsilon$ | Su `a`: $s_4$; su `d`: $s_3$ | `a`, `d` |

In totale abbiamo:

- **5 stati** con conflitti;
- **7 celle ACTION** con conflitti shift/reduce;
- nessun conflitto reduce/reduce.

> [!tip] Formulazione adatta all’esame
> «La grammatica non è LR(0), perché nello stato raggiungibile $I_0$ compaiono sia il candidato completo $[A\to\cdot]$, sia il candidato $[A\to\cdot aA]$. La cella ACTION relativa ad `a` contiene quindi una riduzione e uno spostamento.»

Se la consegna richiede di costruire tutto l’automa, bisogna completarlo anche se il primo conflitto è già sufficiente per la classificazione.

---

## 6. Un esempio positivo: grammatica del 2011

Nella nota precedente abbiamo considerato:

$$
\begin{aligned}
S'&\to S\downarrow\\
S&\to SA\mid b\\
A&\to aA\mid c
\end{aligned}
$$

Gli stati con riduzioni ordinarie sono:

| Stato | Unico candidato presente |
|---|---|
| $J_2$ | $[S\to b\cdot]$ |
| $J_4$ | $[A\to c\cdot]$ |
| $J_5$ | $[S\to SA\cdot]$ |
| $J_6$ | $[A\to aA\cdot]$ |

In ciascuno di questi stati:

- esiste una sola riduzione;
- non sono possibili spostamenti.

Gli altri stati non introducono conflitti e $J_7$ gestisce l’accettazione.

La grammatica è quindi LR(0).

La produzione ricorsiva sinistra $S\to SA$ è compatibile con questa classificazione: la ricorsione sinistra non impedisce, da sola, l’analisi LR(0).

---

## 7. Rapporto fra conflitti e ambiguità

Una grammatica LR(0) è non ambigua: le regole del metodo permettono di ricostruire univocamente la derivazione destra in ordine inverso.

Il contrario non vale.

Una grammatica può essere non ambigua e presentare conflitti LR(0).

Per esempio:

$$
S\to a\mid ab
$$

genera:

$$
L=\{a,ab\}
$$

Ogni parola ha un’unica derivazione, ma abbiamo visto che la grammatica presenta un conflitto shift/reduce.

Anche l’esempio:

$$
S\to Aa\mid Bb,\qquad A\to c,\qquad B\to c
$$

è non ambiguo, pur presentando un conflitto reduce/reduce LR(0).

Quindi:

$$
G\text{ LR(0)}
\implies
G\text{ non ambigua}
$$

ma:

$$
G\text{ non LR(0)}
\not\implies
G\text{ ambigua}
$$

> [!warning] Come dimostrare l’ambiguità
> Occorre esibire una parola con due alberi di derivazione distinti, oppure con due derivazioni sinistre distinte o due derivazioni destre distinte.
> Un conflitto LR(0), da solo, non basta.

---

## 8. Passaggio ai metodi più potenti

Una grammatica non LR(0) potrebbe essere riconoscibile mediante un metodo che usa informazioni aggiuntive.

Nel metodo SLR(1), una riduzione mediante:

$$
A\to\alpha
$$

viene inserita soltanto nelle colonne appartenenti a:

$$
\operatorname{FOLLOW}(A)
$$

Gli insiemi FOLLOW descrivono quali terminali possono seguire un non terminale nelle forme sentenziali.

Questa restrizione può eliminare conflitti presenti nella tabella LR(0). Il metodo SLR(1) mantiene lo stesso automa di candidati LR(0).

> [!important] Classificazione e priorità arbitrarie
> Scegliere sempre lo spostamento, oppure scegliere la prima produzione, non rende una grammatica LR(0).
> La classificazione riguarda la tabella ottenuta applicando le regole del metodo senza priorità aggiuntive.

Per la grammatica di gennaio 2026 studieremo proprio se le restrizioni SLR(1) eliminano i conflitti trovati.

---

## 9. Procedura da applicare all’esame

1. Costruisci gli stati completi, includendo tutte le chiusure.
2. Individua i candidati completi delle produzioni ordinarie.
3. Cerca eventuali altre riduzioni nello stesso stato.
4. Cerca candidati con il punto immediatamente prima di un terminale.
5. Indica il tipo di conflitto e almeno una cella ACTION coinvolta.
6. Formula la conclusione sulla grammatica.

Per sostenere una risposta negativa, riporta sempre:

$$
\text{stato}
+
\text{candidati responsabili}
+
\text{azioni incompatibili}
$$

Per sostenere una risposta positiva, verifica l’assenza di conflitti nell’intera collezione canonica.

## Collegamenti

- [[03 - Analisi a spostamento e riduzione]]
- [[06 - Costruzione dell automa LR0]]
- [[07 - Tabelle ACTION e GOTO e simulazione del riconoscimento]]
- [[09 - Insiemi FIRST e FOLLOW]]
- [[10 - Analisi SLR1 e risoluzione dei conflitti]]