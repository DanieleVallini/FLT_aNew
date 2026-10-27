# Algoritmi di decisione, automi e complessita

Quando studiamo un problema sui linguaggi dobbiamo distinguere due domande:

1. Esiste un algoritmo che lo risolve sempre?
2. Quanto tempo e quanta memoria richiede?

La prima riguarda la **decidibilità**.

La seconda riguarda la **complessità computazionale**.

> [!important] Collegamento con l’analisi sintattica
> Costruire un riconoscitore e usarlo per analizzare una parola sono operazioni diverse.
>
> Il costo della costruzione dell’automa e delle tabelle non coincide con il costo del riconoscimento di una singola parola.

---

## 1. Che cos’è un algoritmo di decisione

Un problema di decisione richiede una risposta sì/no.

Per esempio:

$$
\text{Data }w,\text{ vale }w\in L(G)?
$$

Un algoritmo decide il problema se:

- restituisce una risposta corretta;
- termina su ogni input ammesso, sia nei casi positivi sia in quelli negativi.

Un problema è **decidibile** se esiste un algoritmo di questo tipo.

È **indecidibile** se non esiste alcun algoritmo che soddisfi queste condizioni per tutte le istanze.

> [!warning] Indecidibile non significa soltanto costoso
> Un algoritmo esponenziale può essere molto lento, ma termina e decide comunque il problema.
>
> L’indecidibilità riguarda l’assenza di un algoritmo generale, non soltanto la sua inefficienza.

L’indecidibilità non impedisce di risolvere singoli casi o particolari sottoclassi del problema.

---

## 2. Una simulazione ingenua può non terminare

Consideriamo:

$$
S\to S\mid a.
$$

Il linguaggio è:

$$
L(G)=\{a\}.
$$

Una procedura che continua a scegliere $S\to S$ può però eseguire:

$$
S\Rightarrow S\Rightarrow S\Rightarrow\cdots
$$

senza mai concludere.

Questo non rende indecidibile l’appartenenza al linguaggio.

Mostra soltanto che quella strategia di simulazione non garantisce la terminazione.

Lo stesso problema può presentarsi simulando automi a pila con cicli epsilon.

> [!important] Distinzione fondamentale
> Il fatto che una particolare computazione possa proseguire indefinitamente non dimostra che il problema di appartenenza sia indecidibile.
>
> Per ogni linguaggio context-free esistono procedure di riconoscimento che terminano.

Anche enumerare tutte le derivazioni per lunghezza crescente non basta, da solo, a ottenere un decisore: se la parola non è generabile, la ricerca potrebbe continuare indefinitamente.

---

## 3. Rispetto a quale dimensione misuriamo il costo?

Introduciamo:

| Parametro | Significato |
|---|---|
| $n$ | Lunghezza della parola, cioè numero di simboli o token |
| $k$ | Numero di stati dell’automa |
| $t$ | Numero di transizioni elementari |
| $|G|$ | Dimensione della descrizione della grammatica |

Dire che un algoritmo costa $O(n)$ ha significato soltanto dopo aver chiarito quali altre quantità consideriamo fisse.

Nel parsing di un linguaggio di programmazione, normalmente:

- la grammatica è fissata;
- il parser viene costruito una volta;
- varia la lunghezza dei programmi da analizzare.

Nella costruzione automatica del parser, invece, anche la grammatica è parte dell’input del problema.

---

## 4. Significato della notazione O grande

Scrivere:

$$
T(n)\in O(f(n))
$$

significa che esistono costanti $c>0$ e $n_0$ tali che:

$$
T(n)\leq c\,f(n)
\qquad\text{per ogni }n\geq n_0.
$$

È un limite superiore asintotico.

Esempi:

| Costo | Crescita |
|---|---|
| $O(n)$ | Lineare |
| $O(n^2)$ | Quadratica |
| $O(n^3)$ | Cubica |
| $O(2^n)$ | Esponenziale |

La notazione non specifica direttamente secondi di esecuzione o numero esatto di operazioni.

Quando non indicato diversamente, in questa nota consideriamo limiti nel caso peggiore.

---

## 5. Riconoscimento con automi finiti

### DFA

Per riconoscere una parola con un DFA basta:

1. partire dallo stato iniziale;
2. eseguire una transizione per ogni simbolo;
3. controllare se lo stato raggiunto è finale.

Con accesso costante alla tabella delle transizioni:

$$
\boxed{T(n)=O(n).}
$$

La memoria ausiliaria è costante rispetto a $n$: basta conservare lo stato corrente.

### NFA e automi con epsilon-mosse

Non serve esplorare separatamente ogni percorso.

Manteniamo l’insieme degli stati attualmente possibili:

$$
R_0=E(\{q_0\}),
$$

$$
R_{i+1}
=
E\left(
\bigcup_{q\in R_i}\delta(q,a_{i+1})
\right).
$$

Alla fine accettiamo se:

$$
R_n\cap F\neq\varnothing.
$$

Le epsilon-chiusure si calcolano visitando il grafo e segnando gli stati già incontrati.

Un ciclo epsilon non provoca una visita infinita, perché ciascuno stato viene elaborato al massimo una volta nella singola visita.

Con liste di adiacenza, un limite per questa simulazione è:

$$
O\bigl((n+1)(k+t)\bigr),
$$

con memoria ausiliaria $O(k)$.

Per un automa fissato, il costo è lineare rispetto alla lunghezza della parola.

---

## 6. Determinizzazione: costo di costruzione e costo d’uso

Un NFA con $k$ stati può produrre, mediante costruzione per sottoinsiemi, un DFA con fino a:

$$
2^k
$$

stati.

Quindi la costruzione del DFA può comportare una crescita esponenziale nel numero degli stati iniziali.

Una volta costruito il DFA, il riconoscimento di una parola resta lineare in $n$.

> [!important] Due costi distinti
> - **Costruzione:** trasformare l’NFA nel DFA.
> - **Utilizzo:** eseguire il DFA su una parola.
>
> Il secondo costo non include automaticamente il primo.

Inoltre la determinizzazione non è obbligatoria per decidere l’appartenenza: possiamo simulare direttamente l’NFA mediante insiemi di stati.

---

## 7. Appartenenza ai linguaggi context-free

Il problema:

$$
\text{Dati una CFG }G\text{ e una parola }w,\quad w\in L(G)?
$$

è decidibile.

Un algoritmo generale di riferimento è **CYK**, basato sulla programmazione dinamica.

### Idea di CYK

La grammatica viene preparata in forma normale di Chomsky, con produzioni del tipo:

$$
A\to BC
\qquad\text{oppure}\qquad
A\to a.
$$

La parola vuota viene gestita separatamente verificando se il simbolo iniziale può generarla.

Per ogni sottoparola:

$$
w[i,j]=a_i\cdots a_j,
$$

si memorizzano i non terminali che possono generarla.

Un non terminale $A$ genera $w[i,j]$ se esiste una produzione:

$$
A\to BC
$$

e una separazione della sottoparola in due parti generate rispettivamente da $B$ e $C$.

### Perché il costo è cubico?

Ci sono:

- $O(n^2)$ sottoparole;
- fino a $O(n)$ punti di separazione per ciascuna.

Per una grammatica fissata:

$$
\boxed{T(n)=O(n^3),\qquad S(n)=O(n^2).}
$$

Questi sono i costi dell’algoritmo descritto, non una dimostrazione che ogni algoritmo generale debba necessariamente avere costo cubico.

Negli esercizi d’esame lavoreremo soprattutto con LR(0) e SLR(1); CYK fornisce qui un confronto con il riconoscimento generale delle CFG.

---

## 8. Complessità del parsing LR

Per una grammatica fissata e un parser LR correttamente costruito, il riconoscimento ha costo:

$$
\boxed{T(n)=O(n).}
$$

Nel modello standard:

- ogni token viene spostato una volta;
- il numero complessivo delle riduzioni è lineare;
- la consultazione delle tabelle richiede tempo costante;
- la lunghezza massima delle produzioni è una costante della grammatica.

La pila può richiedere:

$$
O(n)
$$

spazio nel caso peggiore.

### Costi esclusi da questo conteggio

Il costo lineare riguarda l’analisi mediante tabelle già costruite.

Vanno considerati separatamente:

- costruzione dell’automa;
- calcolo degli insiemi necessari;
- costruzione delle tabelle;
- eventuali operazioni semantiche costose.

Un parser con analisi sintattica lineare può eseguire azioni semantiche che richiedono tempo aggiuntivo.

> [!warning] Determinismo e tempo lineare
> Il fatto che un automa a pila sia deterministico non basta, da solo, a garantire una simulazione lineare: possono esistere cicli epsilon.
>
> Il risultato precedente riguarda il funzionamento standard dei parser LR.

---

## 9. Decidere se un linguaggio è vuoto

### Automa finito

Per un automa finito:

$$
L(M)=\varnothing
$$

se e solo se nessuno stato finale è raggiungibile dallo stato iniziale.

Basta una visita del grafo, considerando anche gli archi epsilon quando presenti.

Con liste di adiacenza il costo è:

$$
O(k+t).
$$

### Grammatica context-free

Per una CFG calcoliamo i non terminali **produttivi**, cioè capaci di generare almeno una parola terminale.

Partiamo da:

$$
\mathrm{Prod}_0=\varnothing.
$$

Poi aggiungiamo un non terminale $A$ quando esiste una produzione $A\to\alpha$ i cui non terminali sono già produttivi:

$$
\mathrm{Prod}_{i+1}
=
\mathrm{Prod}_i
\cup
\left\{
A\mid
A\to\alpha\in P,\ 
\alpha\in(\Sigma\cup\mathrm{Prod}_i)^*
\right\}.
$$

Il procedimento termina perché i non terminali sono finiti e l’insieme cresce senza perdere elementi.

Alla stabilizzazione:

$$
\boxed{
L(G)\neq\varnothing
\iff
S\in\mathrm{Prod}.
}
$$

Per esempio, con la sola produzione:

$$
S\to aS,
$$

$S$ non diventa mai produttivo e il linguaggio è vuoto.

---

## 10. Non tutti i problemi sulle CFG sono decidibili

Bisogna distinguere il problema specifico che stiamo ponendo.

| Problema | Decidibile? |
|---|---|
| Una parola appartiene al linguaggio di una CFG? | Sì |
| Una CFG genera il linguaggio vuoto? | Sì |
| Due CFG arbitrarie generano lo stesso linguaggio? | No |
| Una CFG arbitraria è ambigua? | No |
| Una grammatica è LR(0)? | Sì |
| Una grammatica è SLR(1)? | Sì |

Per verificare LR(0) o SLR(1) si costruiscono strutture finite e si controllano gli eventuali conflitti.

### Perché questo non decide l’ambiguità?

Essere LR(0) o SLR(1) è una condizione sufficiente per la non ambiguità.

Se la grammatica non appartiene a queste classi, non possiamo concludere automaticamente che sia ambigua.

Quindi il test non risolve il problema generale dell’ambiguità.

Analogamente, l’indecidibilità dell’equivalenza di CFG arbitrarie non impedisce di dimostrare l’equivalenza di due grammatiche specifiche.

---

## 11. Quadro da ricordare

| Metodo | Tempo di riconoscimento |
|---|---|
| DFA fissato | $O(n)$ |
| NFA fissato, simulato per insiemi | $O(n)$ rispetto a $n$ |
| CYK, grammatica fissata | $O(n^3)$ |
| Parser LR, grammatica e tabelle fissate | $O(n)$ |

Questi costi riguardano algoritmi specifici e ipotesi dichiarate.

Non significano che:

- ogni implementazione raggiunga automaticamente quel costo;
- costruire il riconoscitore abbia lo stesso costo;
- tutte le domande sulla grammatica siano decidibili.

> [!important] Tre controlli prima di parlare di complessità
> 1. Qual è il problema da risolvere?
> 2. Quali quantità fanno parte dell’input e quali sono fissate?
> 3. Stiamo misurando la costruzione del riconoscitore oppure il suo utilizzo?
>
> Queste distinzioni permettono di interpretare correttamente i costi degli automi e dei parser.