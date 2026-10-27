# Analisi a spostamento e riduzione

L’analisi a spostamento e riduzione, o **shift-reduce**, è un metodo di analisi sintattica ascendente.

Il parser legge l’input da sinistra a destra e ricostruisce progressivamente le strutture previste dalla grammatica.

Le operazioni fondamentali sono:

- **spostamento:** leggere il prossimo terminale e inserirlo nella pila;
- **riduzione:** sostituire una struttura già riconosciuta con il non terminale che la genera.

> [!important] Idea centrale
> Nei metodi LR, le riduzioni ricostruiscono una derivazione destra in ordine inverso.
>
> Il problema fondamentale è decidere quando spostare e quale produzione usare per ridurre.

---

## 1. Pila e input residuo

Per descrivere il funzionamento consideriamo:

$$
(\gamma,u),
$$

dove:

- $\gamma$ è il contenuto della pila, escluso il marcatore di fondo;
- $u$ è la parte di input ancora da leggere.

La pila contiene simboli grammaticali, cioè terminali e non terminali.

Rappresenta la parte già letta, eventualmente trasformata dalle riduzioni.

### Convenzione sulla pila

In questa nota:

$$
\boxed{\text{La cima della pila è a destra.}}
$$

Nelle tabelle indichiamo il fondo con $\bot$.

Per esempio:

$$
\bot\,S\,a\,A
$$

ha come cima $A$.

Questa è la convenzione che useremo nelle tabelle di parsing; nelle precedenti definizioni degli automi a pila avevamo invece scritto la cima a sinistra.

> [!warning] Simboli grammaticali e stati
> Qui mostriamo soltanto i simboli grammaticali.
>
> Nelle simulazioni LR complete aggiungeremo gli stati del riconoscitore e useremo le tabelle ACTION e GOTO.

---

## 2. Spostamento: shift

Uno spostamento consuma il prossimo terminale e lo inserisce in cima alla pila.

Formalmente:

$$
\boxed{
(\gamma,au)
\xrightarrow{\mathrm{shift}}
(\gamma a,u).
}
$$

Per esempio:

$$
(S,a c c\downarrow)
\xrightarrow{\mathrm{shift}}
(Sa,c c\downarrow).
$$

È stata letta una `a`.

### Che cosa cambia?

- La pila cresce di un terminale.
- L’input residuo perde il primo simbolo.
- Non viene applicata alcuna produzione.

La concatenazione tra pila e input residuo rimane identica:

$$
\gamma\,au=(\gamma a)u.
$$

Lo spostamento cambia il confine tra la parte letta e quella ancora da leggere.

---

## 3. Riduzione: reduce

Supponiamo che la grammatica contenga:

$$
A\to\beta.
$$

Se la parte da ridurre è in cima alla pila, la riduzione sostituisce $\beta$ con $A$:

$$
\boxed{
(\alpha\beta,u)
\xrightarrow{\mathrm{reduce}\ A\to\beta}
(\alpha A,u).
}
$$

### Operazioni sulla pila

Considerando soltanto i simboli grammaticali:

1. si rimuovono i $|\beta|$ simboli del lato destro;
2. si inserisce il non terminale $A$.

L’input residuo non cambia.

### Esempio

Con:

$$
A\to aA,
$$

possiamo avere:

$$
(SaA,c\downarrow)
\xrightarrow{\mathrm{reduce}\ A\to aA}
(SA,c\downarrow).
$$

La sequenza `aA` viene sostituita da $A$.

> [!important] Una riduzione non legge input
> Il terminale che segue la parte riconosciuta resta disponibile per l’azione successiva.
>
> Per questo possono avvenire più riduzioni consecutive senza alcuno spostamento intermedio.

---

## 4. La maniglia: quale parte deve essere ridotta?

Non basta trovare una sottostringa uguale al lato destro di una produzione.

La riduzione corretta deve invertire un passo della derivazione destra.

Supponiamo che:

$$
S
\Rightarrow_{\mathrm{rm}}^*
\alpha A w
\Rightarrow_{\mathrm{rm}}
\alpha\beta w,
$$

con:

$$
A\to\beta\in P,
\qquad
w\in\Sigma^*.
$$

L’occorrenza di $\beta$ introdotta da questo passo è una **maniglia**, o **handle**, della forma sentenziale destra.

La sua riduzione ricostruisce:

$$
\alpha\beta w
\rightsquigarrow
\alpha A w.
$$

La maniglia comprende quindi:

- la produzione da usare;
- la posizione dell’occorrenza del suo lato destro.

### Collegamento con la pila

Quando il parser deve ridurre la maniglia:

- $\alpha\beta$ è sulla pila;
- $w$ è ancora da leggere;
- $\beta$ si trova in cima alla pila.

> [!warning] La posizione conta
> Se lo stesso lato destro compare più volte nella forma sentenziale, le diverse occorrenze non sono intercambiabili.
>
> Le riduzioni LR devono ricostruire la derivazione destra nell’ordine corretto.

---

## 5. Esempio d’esame: grammatica per bacc

Riprendiamo la grammatica della prova del 22 febbraio 2011:

$$
S\to SA\mid b
$$

$$
A\to aA\mid c.
$$

La parola richiesta è:

$$
bacc.
$$

Una sua derivazione destra è:

$$
\begin{aligned}
S
&\Rightarrow SA\\
&\Rightarrow Sc\\
&\Rightarrow SAc\\
&\Rightarrow SaAc\\
&\Rightarrow Sacc\\
&\Rightarrow bacc.
\end{aligned}
$$

Le riduzioni devono quindi ricostruire, nell’ordine:

$$
\begin{aligned}
bacc
&\rightsquigarrow Sacc\\
&\rightsquigarrow SaAc\\
&\rightsquigarrow SAc\\
&\rightsquigarrow Sc\\
&\rightsquigarrow SA\\
&\rightsquigarrow S.
\end{aligned}
$$

Questa sequenza individua le riduzioni. Per eseguirle durante la lettura dobbiamo intercalare gli spostamenti necessari.

---

## 6. Simulazione con pila e input residuo

Usiamo la produzione aumentata:

$$
S'\to S\downarrow.
$$

Con questa convenzione, anche il marcatore viene spostato sulla pila; l’accettazione riconosce il completamento della produzione aumentata.

La tabella mostra una sequenza corretta di azioni, senza riportare ancora gli stati LR.

La colonna finale indica l’azione appena eseguita.

| Passo | Pila | Input residuo | Azione |
|---|---|---|---|
| 0 | $\bot$ | `bacc↓` | Inizio |
| 1 | $\bot\,b$ | `acc↓` | Shift di `b` |
| 2 | $\bot\,S$ | `acc↓` | Reduce $S\to b$ |
| 3 | $\bot\,S\,a$ | `cc↓` | Shift di `a` |
| 4 | $\bot\,S\,a\,c$ | `c↓` | Shift di `c` |
| 5 | $\bot\,S\,a\,A$ | `c↓` | Reduce $A\to c$ |
| 6 | $\bot\,S\,A$ | `c↓` | Reduce $A\to aA$ |
| 7 | $\bot\,S$ | `c↓` | Reduce $S\to SA$ |
| 8 | $\bot\,S\,c$ | `↓` | Shift di `c` |
| 9 | $\bot\,S\,A$ | `↓` | Reduce $A\to c$ |
| 10 | $\bot\,S$ | `↓` | Reduce $S\to SA$ |
| 11 | $\bot\,S\,\downarrow$ | $\varepsilon$ | Shift del marcatore |
| 12 | $\bot\,S\,\downarrow$ | $\varepsilon$ | Accettazione |

### Osservazioni importanti

Al passo 2 abbiamo già $S$ sulla pila, ma resta da leggere `acc↓`.

Quindi non possiamo accettare.

Ai passi 5, 6 e 7 avvengono tre riduzioni consecutive, tutte con lo stesso input residuo.

Al passo 5 viene ridotta la prima `c` della parola: la seconda è ancora nell’input.

> [!important] Il parser alterna lettura e ricostruzione
> Non deve necessariamente leggere tutta la parola prima di iniziare a ridurre.
>
> La riduzione $S\to b$ avviene subito dopo aver letto il primo terminale.

---

## 7. Un invariante utile per controllare la simulazione

Consideriamo la concatenazione:

$$
\text{pila senza fondo}\;\cdot\;\text{input residuo}.
$$

Uno spostamento lascia invariata questa sequenza.

Una riduzione la trasforma mediante l’inverso di una produzione.

Per esempio, nella simulazione:

$$
(Sa,cc\downarrow)
\xrightarrow{\mathrm{shift}}
(Sac,c\downarrow)
$$

rappresenta, prima e dopo:

$$
Sacc\downarrow.
$$

La successiva riduzione:

$$
(Sac,c\downarrow)
\xrightarrow{A\to c}
(SaA,c\downarrow)
$$

trasforma invece:

$$
Sacc\downarrow
\rightsquigarrow
SaAc\downarrow.
$$

Questo controllo permette di collegare la tabella di parsing alla derivazione destra inversa.

---

## 8. Riduzioni con produzioni epsilon

Se la produzione è:

$$
A\to\varepsilon,
$$

il lato destro ha lunghezza zero.

La riduzione:

- non rimuove simboli grammaticali;
- inserisce $A$;
- non consuma input.

Formalmente:

$$
\boxed{
(\alpha,u)
\xrightarrow{\mathrm{reduce}\ A\to\varepsilon}
(\alpha A,u).
}
$$

### Esempio

Consideriamo:

$$
S\to Ab,
\qquad
A\to\varepsilon.
$$

Per riconoscere `b`, le prime operazioni sono:

$$
(\varepsilon,b\downarrow)
\xrightarrow{\mathrm{reduce}\ A\to\varepsilon}
(A,b\downarrow),
$$

$$
(A,b\downarrow)
\xrightarrow{\mathrm{shift}}
(Ab,\downarrow),
$$

$$
(Ab,\downarrow)
\xrightarrow{\mathrm{reduce}\ S\to Ab}
(S,\downarrow).
$$

La prima azione è quindi una riduzione, anche se non è stato ancora letto alcun terminale.

> [!warning] Epsilon non viene spostato
> Una produzione epsilon provoca una riduzione con rimozione di zero simboli.
>
> Non esiste un token epsilon da leggere dall’input.

---

## 9. Come si sceglie l’azione?

Le operazioni sulla pila sono semplici. La parte delicata è decidere:

- quando continuare a leggere;
- quando ridurre;
- quale produzione usare.

Una strategia come “riduci appena trovi un lato destro” non è sufficiente.

I parser LR usano gli stati dell’automa per rappresentare il contesto della parte già riconosciuta.

Le tabelle indicano poi l’azione da eseguire.

Nei metodi che usano il lookahead, la decisione considera anche il prossimo token.

### Conflitto shift/reduce

Le informazioni disponibili suggeriscono sia uno spostamento sia una riduzione.

### Conflitto reduce/reduce

Le informazioni disponibili suggeriscono due riduzioni diverse.

> [!important] Non inventare una priorità
> Dare arbitrariamente precedenza allo shift o alla reduce non dimostra la correttezza del parser.
>
> Nei prossimi argomenti stabiliremo quando le tabelle LR(0) e SLR(1) permettono una decisione univoca.

---

## 10. Accettazione ed errore

Con la convenzione:

$$
S'\to S\downarrow,
$$

l’accettazione conclude il riconoscimento della produzione aumentata, con input completamente consumato.

La semplice presenza di $S$ sulla pila non basta.

Un errore viene segnalato quando il metodo non prevede alcuna azione per la configurazione corrente.

Occorre distinguere:

- **conflitto nella costruzione:** più azioni proposte per la stessa situazione;
- **errore durante l’analisi:** nessuna azione prevista sulla parola corrente.

Il primo riguarda la costruzione del parser; il secondo il suo utilizzo.

---

## 11. Procedura pratica per gli esercizi

1. Copia la grammatica e la produzione aumentata.
2. Dichiara la convenzione della pila.
3. Inizia con la pila contenente soltanto il fondo.
4. Riporta tutto l’input, compreso il marcatore.
5. A ogni shift, sposta esattamente il prossimo terminale.
6. A ogni reduce, controlla il suffisso della pila e la produzione.
7. Durante una riduzione lascia invariato l’input residuo.
8. Per una produzione epsilon rimuovi zero simboli.
9. Accetta soltanto nella configurazione conclusiva prevista.

> [!tip] Controlli rapidi
> - Uno shift consuma un simbolo.
> - Una reduce non consuma input.
> - Si riduce soltanto in cima alla pila.
> - La sequenza delle riduzioni deve corrispondere a una derivazione destra inversa.
> - Nelle simulazioni LR complete, ogni azione deve essere giustificata dalle tabelle.