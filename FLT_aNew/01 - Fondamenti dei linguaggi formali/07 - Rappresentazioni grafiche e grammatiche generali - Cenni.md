# Rappresentazioni grafiche e grammatiche generali - Cenni

## 1. Perché usare rappresentazioni grafiche

Una rappresentazione grafica rende visibile una struttura descritta formalmente da regole o transizioni.

In questo corso sono utili soprattutto:

| Rappresentazione | Che cosa descrive |
|---|---|
| Diagramma di un automa | Stati e transizioni durante il riconoscimento |
| Diagramma sintattico | Percorsi che descrivono una costruzione sintattica |
| Albero di derivazione | Struttura di una frase generata da una grammatica context-free |

> [!important]
> Nel programma le rappresentazioni grafiche sono indicate come argomento da conoscere per cenni. Negli esami, però, leggere e costruire correttamente automi e strutture sintattiche è una capacità operativa importante.

## 2. Diagrammi degli automi

Un automa finito può essere rappresentato come un grafo orientato.

| Elemento grafico | Significato |
|---|---|
| Cerchio | Stato |
| Freccia proveniente dall'esterno | Stato iniziale |
| Doppio cerchio | Stato finale, o accettante |
| Arco orientato con etichetta | Transizione |
| Arco che ritorna allo stesso stato | Transizione dallo stato a se stesso |

Una transizione:

$$
q_i\xrightarrow{a}q_j
$$

significa che, leggendo il simbolo $a$, l'automa può passare da $q_i$ a $q_j$.

Un'etichetta come $a\mid b$ su un arco è una scrittura compatta per due transizioni con gli stessi estremi: una su $a$ e una su $b$.

### Esempio: numero pari di a

Il seguente automa riconosce:

$$
L=\{w\in\{a,b\}^*\mid w\text{ contiene un numero pari di }a\}
$$

![[Allegati/FLT_07/flt_07_01_automa.svg|900]]

L'interpretazione degli stati è:

- $q_0$: il numero di `a` lette finora è pari;
- $q_1$: il numero di `a` lette finora è dispari.

Leggere una `a` cambia la parità; leggere una `b` non la cambia.

Per esempio, `aba` è accettata:

$$
q_0\xrightarrow{a}q_1
\xrightarrow{b}q_1
\xrightarrow{a}q_0
$$

Lo stato iniziale è anche finale: la stringa vuota è accettata perché contiene zero `a`.

### Quando una stringa è accettata

Per un automa finito non deterministico basta che esista un cammino che:

1. parta dallo stato iniziale;
2. consumi tutta la stringa;
3. termini in uno stato finale.

Non basta attraversare uno stato finale mentre restano ancora simboli da leggere. Uno stato finale può avere transizioni uscenti.

### Epsilon-mosse

Un arco etichettato con $\varepsilon$ permette di cambiare stato senza consumare simboli dell'ingresso.

$\varepsilon$ non è un simbolo da leggere.

In presenza di epsilon-mosse, la stringa vuota può essere accettata anche se lo stato iniziale non è finale: basta poter raggiungere uno stato finale senza consumare simboli.

## 3. Diagrammi sintattici

I **diagrammi sintattici**, detti anche *railroad diagrams*, rappresentano graficamente le costruzioni ammesse da una grammatica.

Si seguono i percorsi orientati dall'ingresso all'uscita.

Nelle figure adottiamo queste convenzioni:

- una capsula rappresenta un terminale da leggere;
- un rettangolo rappresenta un non terminale, cioè un richiamo al suo diagramma;
- una biforcazione rappresenta una scelta;
- un ritorno rappresenta una possibile ripetizione;
- un percorso senza simboli rappresenta la stringa vuota.

Le forme dei riquadri possono variare tra testi: conta la convenzione dichiarata.

![[Allegati/FLT_07/flt_07_02_diagrammi_sintattici.svg|900]]

### Sequenza

Il primo diagramma rappresenta:

$$
D\to\mathtt{id}\ \mathtt{:}\ T
$$

Occorre leggere `id`, poi `:`, poi una stringa descritta da $T$.

### Alternativa

Il secondo diagramma rappresenta:

$$
T\to\mathtt{integer}\mid\mathtt{real}
$$

Si sceglie un percorso: non si leggono entrambe le alternative.

### Ripetizione

Il terzo diagramma descrive lo stesso linguaggio della grammatica:

$$
R\to aR\mid\varepsilon
$$

Il percorso vuoto permette zero occorrenze; il ciclo permette di ripetere `a`.

Il linguaggio è:

$$
L=\{a^n\mid n\geq 0\}
$$

## 4. Alberi di derivazione

Per una grammatica context-free, un **albero di derivazione** è un albero radicato e ordinato che mostra come è costruita una frase.

- La radice è etichettata con il simbolo iniziale.
- Ogni nodo interno è etichettato con un non terminale.
- Se un nodo $A$ ha figli $X_1,\ldots,X_k$, in quest'ordine, deve esistere la produzione:

$$
A\to X_1\cdots X_k
$$

- Per una produzione $A\to\varepsilon$, possiamo rappresentare una foglia $\varepsilon$ sotto $A$.

L'ordine dei figli è essenziale: determina l'ordine dei simboli nella stringa.

### Esempio

Consideriamo:

$$
S\to aSb\mid\varepsilon
$$

La derivazione:

$$
S\Rightarrow aSb\Rightarrow aaSbb\Rightarrow aabb
$$

ha il seguente albero:

![[Allegati/FLT_07/flt_07_03_albero_derivazione.svg|900]]

### Frontiera dell'albero

La **frontiera** si ottiene leggendo le foglie da sinistra a destra.

In questo esempio:

$$
a\cdot a\cdot\varepsilon\cdot b\cdot b=aabb
$$

La foglia $\varepsilon$ non aggiunge simboli.

Se tutte le foglie sono terminali o $\varepsilon$, l'albero descrive una frase del linguaggio. Se rimangono foglie non terminali, la costruzione è ancora parziale.

> [!warning]
> La stringa non si ottiene leggendo i nodi per livello o nell'ordine in cui sono stati disegnati: si leggono le foglie da sinistra a destra.

## 5. Albero e ordine della derivazione

L'albero descrive la struttura, mentre una derivazione descrive anche l'ordine con cui vengono applicate le produzioni.

Con:

$$
S\to AB,\qquad A\to a,\qquad B\to b
$$

possiamo avere:

$$
S\Rightarrow AB\Rightarrow aB\Rightarrow ab
$$

oppure:

$$
S\Rightarrow AB\Rightarrow Ab\Rightarrow ab
$$

Le due derivazioni sviluppano prima non terminali diversi, ma corrispondono allo stesso albero.

Per questo due derivazioni diverse non dimostrano automaticamente l'ambiguità.

## 6. Riconoscere graficamente l'ambiguità

Una grammatica context-free è **ambigua** se esiste almeno una stringa del linguaggio con due alberi di derivazione distinti.

Consideriamo:

$$
E\to E+E\mid E*E\mid\mathtt{id}
$$

La stringa:

`id + id * id`

ammette entrambi gli alberi seguenti:

![[Allegati/FLT_07/flt_07_04_ambiguita.svg|1100]]

Nel primo albero l'addizione combina un identificatore con il risultato della moltiplicazione.

Nel secondo la moltiplicazione combina il risultato dell'addizione con un identificatore.

La frontiera è identica, ma la struttura cambia: la grammatica è ambigua.

Le parentesi nelle intestazioni delle figure evidenziano i raggruppamenti; non sono token aggiunti alla stringa originale.

> [!important]
> Per risolvere l'ambiguità non basta disegnare soltanto l'albero che rispetta la precedenza matematica abituale.
>
> Per ottenere una struttura univoca bisogna imporre la scelta nella grammatica oppure attraverso regole esplicite del metodo di analisi. Una scelta effettuata dal parser non rende, da sola, non ambigua la grammatica originaria.

## 7. Grammatiche generali: il limite della rappresentazione ad albero

Le grammatiche generali ammettono produzioni:

$$
\alpha\to\beta
$$

con:

$$
\alpha\in V^*NV^*,\qquad\beta\in V^*
$$

Dove $V=N\cup\Sigma$. La parte sinistra può quindi contenere più simboli, non soltanto un singolo non terminale.

Per esempio:

$$
aA\to ab
$$

La regola richiede che $A$ sia preceduto da `a`.

Un comune albero di derivazione context-free descrive invece la sostituzione di un singolo non terminale mediante i suoi figli.

Per questo non possiamo trasferire automaticamente alle grammatiche generali la stessa rappresentazione locale delle produzioni.

Le forme delle grammatiche generali e la loro posizione nella gerarchia sono approfondite nella nota sulle classi di grammatiche e linguaggi; qui basta ricordare questa differenza.

## 8. Collegamenti con l'esame

- **Automi finiti:** leggere senza errori archi, cicli, stati iniziali e finali e epsilon-mosse.
- **Grammatiche:** controllare che ogni espansione rappresentata nell'albero corrisponda a una produzione.
- **Analisi LR:** gli stati dell'automa saranno insiemi di candidati, collegati da transizioni.
- **Attributi:** distinguere le diverse occorrenze dei simboli nell'albero, per esempio $A$ e $A_1$.
- **Traduzione:** riconoscere i sottoalberi a cui associare attributi, azioni e codice.

## 9. Errori da evitare

- Considerare il doppio cerchio come un ordine di arresto immediato.
- Accettare una stringa senza consumare tutto l'ingresso.
- Leggere $\varepsilon$ come un carattere.
- Confondere una scelta tra percorsi con una concatenazione.
- Leggere la frontiera di un albero per livelli.
- Confondere due ordini di espansione con due alberi diversi.
- Supporre che una grammatica rispetti automaticamente le precedenze abituali.
- Trattare stati di un automa e nodi di un albero come se avessero lo stesso significato.