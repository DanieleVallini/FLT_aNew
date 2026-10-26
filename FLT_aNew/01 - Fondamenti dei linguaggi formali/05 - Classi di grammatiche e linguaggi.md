# Classi di grammatiche e linguaggi

## 1. Grammatica e linguaggio: due classificazioni distinte

Una **grammatica** si classifica osservando la forma delle sue produzioni.

Un **linguaggio** si classifica in base all’esistenza di una grammatica o di un automa capace di descriverlo o riconoscerlo.

Per esempio:

- una grammatica è regolare se le sue produzioni rispettano determinati vincoli;
- un linguaggio è regolare se esiste almeno una grammatica regolare che lo genera.

> [!important]
> La grammatica fornita potrebbe essere più complessa del necessario.
>
> Una grammatica context-free può generare un linguaggio regolare. Per dimostrare che il linguaggio è regolare basta trovare una descrizione regolare equivalente.

## 2. La gerarchia di Chomsky

La gerarchia di Chomsky distingue quattro classi principali.

| Tipo | Grammatiche | Linguaggi | Modello di riconoscimento |
|---|---|---|---|
| 3 | Regolari | Regolari | Automi finiti |
| 2 | Context-free | Context-free | Automi a pila non deterministici |
| 1 | Context-sensitive | Context-sensitive | Automi linearmente limitati |
| 0 | Generali, o non ristrette | Ricorsivamente enumerabili | Macchine di Turing |

Le classi di linguaggi soddisfano:

$$
\mathrm{REG}
\subsetneq
\mathrm{CFL}
\subsetneq
\mathrm{CSL}
\subsetneq
\mathrm{RE}
$$

Il simbolo $\subsetneq$ indica un’inclusione propria: ogni classe contiene tutti i linguaggi della precedente, ma anche altri.

Per includere uniformemente la stringa vuota nella gerarchia si adotta la convenzione per le grammatiche context-sensitive descritta più avanti.

## 3. Grammatiche regolari — Tipo 3

Una grammatica **lineare destra regolare**, in una forma comune, ammette produzioni:

$$
A\to aB
$$

$$
A\to a
$$

$$
A\to\varepsilon
$$

con:

$$
A,B\in N,\qquad a\in\Sigma
$$

Nella parte destra compare al massimo un non terminale, che deve trovarsi all’estremità destra.

Esistono anche grammatiche **lineari sinistre**, con produzioni:

$$
A\to Ba
$$

$$
A\to a
$$

$$
A\to\varepsilon
$$

Entrambe le famiglie generano esattamente i linguaggi regolari.

Si possono ammettere anche varianti delle produzioni con intere stringhe di terminali: il potere espressivo rimane lo stesso.

### Esempio

$$
S\to aS\mid b
$$

genera:

$$
L(G)=\{a^n b\mid n\geq 0\}
$$

Il linguaggio è descritto dall’espressione regolare:

$$
a^*b
$$

### Attenzione a non mescolare le due forme

Non basta che ogni produzione abbia al massimo un non terminale a destra.

Consideriamo:

$$
S\to aA\mid\varepsilon
$$

$$
A\to Sb
$$

La prima produzione è lineare destra, la seconda lineare sinistra.

La grammatica genera:

$$
L(G)=\{a^n b^n\mid n\geq 0\}
$$

che non è regolare.

> [!warning]
> Una grammatica interamente lineare destra o interamente lineare sinistra genera un linguaggio regolare.
>
> Mescolare liberamente i due orientamenti non garantisce la regolarità.

## 4. Grammatiche context-free — Tipo 2

Una grammatica è **context-free**, o **non contestuale**, se ogni produzione ha la forma:

$$
A\to\beta
$$

dove:

$$
A\in N,\qquad \beta\in(N\cup\Sigma)^*
$$

A sinistra compare un solo non terminale.

La sostituzione di $A$ non dipende dai simboli che lo circondano: da qui il nome “non contestuale”.

A destra possono comparire:

- terminali;
- non terminali;
- una combinazione dei due;
- la stringa vuota.

### Esempio

$$
S\to aSb\mid\varepsilon
$$

genera:

$$
L(G)=\{a^n b^n\mid n\geq 0\}
$$

La grammatica è context-free perché a sinistra di ogni produzione compare soltanto $S$.

Non è regolare nella forma data: nella produzione $S\to aSb$, il non terminale si trova tra due terminali.

In questo caso, anche il linguaggio è non regolare; questa seconda affermazione richiede però una dimostrazione separata.

### Modello equivalente

Un linguaggio è context-free se e solo se può essere riconosciuto da un automa a pila non deterministico.

La pila permette di gestire quantità correlate e strutture annidate, come le parentesi bilanciate.

## 5. Grammatiche context-sensitive — Tipo 1

Nella forma contestuale, le produzioni sono del tipo:

$$
uAv\to u\beta v
$$

dove:

$$
A\in N,\qquad \beta\neq\varepsilon
$$

Il non terminale $A$ viene sostituito con $\beta$ in presenza del contesto $u$ a sinistra e $v$ a destra.

Un esempio di produzione è:

$$
aAb\to acb
$$

Le parti `a` e `b` rappresentano il contesto.

La stessa classe di linguaggi può essere caratterizzata mediante grammatiche **non contraenti**, le cui produzioni rispettano:

$$
|\alpha|\leq|\beta|
\qquad
\text{per ogni produzione }\alpha\to\beta
$$

La lunghezza non diminuisce durante la derivazione.

### Eccezione per ε

Per permettere di generare anche la stringa vuota si può ammettere:

$$
S\to\varepsilon
$$

a condizione che il simbolo iniziale $S$ non compaia nella parte destra di alcuna produzione.

### Esempio di linguaggio

$$
L=\{a^n b^n c^n\mid n\geq 1\}
$$

è context-sensitive, ma non context-free.

> [!note]
> Nel programma le grammatiche più generali sono indicate come argomento da conoscere per cenni.
>
> Qui interessa soprattutto capire che esistono linguaggi oltre la classe context-free.

## 6. Grammatiche generali — Tipo 0

Le produzioni hanno la forma generale:

$$
\alpha\to\beta
$$

con:

$$
\alpha\in V^*NV^*
$$

$$
\beta\in V^*
$$

dove:

$$
V=N\cup\Sigma
$$

La parte sinistra deve contenere almeno un non terminale; non si impongono ulteriori restrizioni generali.

In particolare, la lunghezza può diminuire.

Queste grammatiche generano i linguaggi **ricorsivamente enumerabili**, riconoscibili da macchine di Turing.

“Riconoscibile” significa che:

- se la stringa appartiene al linguaggio, la macchina la accetta dopo un numero finito di passi;
- se non appartiene, la macchina potrebbe rifiutarla oppure non terminare.

Non significa quindi necessariamente che l’appartenenza sia decidibile per ogni ingresso.

## 7. Dove si collocano i linguaggi deterministici

Nel contesto degli automi a pila, un linguaggio è detto **deterministico** se è riconoscibile da un automa a pila deterministico.

La classe si indica con:

$$
\mathrm{DCFL}
$$

e soddisfa:

$$
\mathrm{REG}
\subsetneq
\mathrm{DCFL}
\subsetneq
\mathrm{CFL}
$$

Quindi:

- ogni linguaggio regolare è deterministico;
- alcuni linguaggi deterministici non sono regolari;
- alcuni linguaggi context-free non sono deterministici.

### Esempi

| Linguaggio | Classificazione più precisa tra quelle indicate |
|---|---|
| $\{a^n b^m\mid n,m\geq 0\}$ | Regolare |
| $\{a^n b^n\mid n\geq 0\}$ | Deterministico, ma non regolare |
| $\{ww^R\mid w\in\{a,b\}^*\}$ | Context-free, ma non deterministico |
| $\{a^n b^n c^n\mid n\geq 1\}$ | Context-sensitive, ma non context-free |

La notazione $w^R$ indica la stringa ottenuta leggendo $w$ al contrario.

Il terzo esempio è il linguaggio dei palindromi di lunghezza pari su due simboli, senza un separatore centrale.

> [!important]
> Il non determinismo ha effetti diversi nei due modelli:
>
> - negli automi finiti non aumenta il potere espressivo;
> - negli automi a pila permette di riconoscere più linguaggi rispetto al caso deterministico.

## 8. Dimostrare che ogni linguaggio regolare è deterministico

Supponiamo che $L$ sia regolare.

Esiste quindi un automa finito deterministico $D$ che lo riconosce.

Possiamo costruire un automa a pila deterministico che:

1. abbia gli stessi stati di $D$;
2. legga ogni simbolo eseguendo la stessa transizione di $D$;
3. lasci sempre invariata la pila;
4. accetti per stato finale, dopo aver consumato tutto l’ingresso, esattamente quando accetta $D$.

La pila non è necessaria, ma la sua presenza non impedisce di simulare l’automa finito.

Il nuovo automa riconosce lo stesso linguaggio:

$$
L\in\mathrm{REG}
\implies
L\in\mathrm{DCFL}
$$

Quindi:

$$
\mathrm{REG}\subseteq\mathrm{DCFL}
$$

## 9. Perché l’inclusione è propria

Per dimostrare:

$$
\mathrm{REG}\subsetneq\mathrm{DCFL}
$$

serve anche un linguaggio deterministico che non sia regolare.

Consideriamo:

$$
L=\{a^n b^n\mid n\geq 0\}
$$

### Il linguaggio è deterministico

Un automa a pila deterministico può:

- inserire un simbolo in pila per ogni `a`;
- passare alla fase delle `b` quando incontra la prima `b`;
- togliere un simbolo dalla pila per ogni `b`;
- rifiutare se compare una `a` dopo l’inizio delle `b`;
- verificare alla fine che le quantità coincidano.

La stringa vuota viene gestita come caso ammesso.

La costruzione formale sarà sviluppata nelle note sugli automi a pila.

### Il linguaggio non è regolare

Supponiamo per assurdo che esista un automa finito deterministico con $p$ stati che riconosca $L$.

Consideriamo le $p+1$ stringhe:

$$
\varepsilon,a,a^2,\ldots,a^p
$$

Per il principio dei cassetti, due di esse, $a^i$ e $a^j$ con $i<j$, portano allo stesso stato.

Aggiungendo lo stesso suffisso $b^i$, l’automa deve dare lo stesso risultato su:

$$
a^i b^i
$$

e:

$$
a^j b^i
$$

Ma la prima stringa appartiene a $L$, mentre la seconda no, perché $j\neq i$.

Contraddizione.

Quindi:

$$
L\notin\mathrm{REG}
$$

e pertanto:

$$
\mathrm{REG}\subsetneq\mathrm{DCFL}
$$

> [!tip] Collegamento con l’esame
> Alla domanda sulla relazione tra linguaggi regolari e deterministici:
>
> 1. dimostra che un automa a pila deterministico può simulare un automa finito;
> 2. usa un linguaggio come $\{a^n b^n\mid n\geq 0\}$ per dimostrare che l’inclusione è propria.

## 10. Come affrontare una domanda di classificazione

### Se viene chiesto di classificare una grammatica

Osserva tutte le produzioni e verifica quali vincoli rispettano.

Per esempio:

$$
S\to aS\mid b
$$

è regolare e, di conseguenza, anche context-free. Si indica normalmente la classificazione più restrittiva applicabile.

Una precisazione: una particolare grammatica context-free con produzioni che cancellano simboli potrebbe non essere non contraente. La gerarchia di inclusione presentata riguarda le classi di linguaggi e la possibilità di trovare grammatiche adatte.

### Se viene chiesto di classificare il linguaggio generato

Prima ricava $L(G)$, poi cerca una caratterizzazione appropriata.

Per dimostrare che è regolare puoi fornire:

- un’espressione regolare;
- un automa finito;
- una grammatica regolare equivalente.

Per dimostrare che non è regolare serve un argomento di impossibilità, non basta osservare che la grammatica data non è regolare.

## 11. Errori da evitare

- Confondere la forma della grammatica con la classe del linguaggio.
- Pensare che tutti i linguaggi context-free siano deterministici.
- Pensare che “deterministico” significhi “regolare”.
- Mescolare produzioni lineari destre e sinistre e concludere automaticamente che la grammatica sia regolare.
- Dimostrare soltanto l’inclusione quando viene richiesta un’inclusione propria.
- Confondere riconoscibilità e decidibilità.
- Dedicare alle grammatiche di tipo 0 e 1 lo stesso approfondimento operativo richiesto per automi finiti, grammatiche context-free e analisi LR.