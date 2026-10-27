# Analisi LR(1) - Cenni

## 1. Dal metodo SLR(1) al metodo LR(1)

Nel metodo SLR(1), un candidato completo:

$$
[A\to\alpha\cdot]
$$

produce una riduzione nelle colonne appartenenti a:

$$
\operatorname{FOLLOW}(A)
$$

FOLLOW è però un insieme globale: raccoglie ciò che può seguire $A$ in tutti i contesti della grammatica.

In un particolare stato, alcuni di quei terminali potrebbero non essere compatibili con il contesto effettivamente riconosciuto.

Il metodo **LR(1) canonico** conserva informazioni più precise, associando un lookahead a ogni candidato.

> [!important]
> SLR(1) e LR(1) utilizzano entrambi un simbolo di lookahead.
> LR(1) distingue più precisamente i contesti mediante le informazioni contenute nei candidati e negli stati.

Nel programma LR(1) è indicato a livello di cenni: sono centrali il significato dei candidati, la differenza rispetto a SLR(1) e il principio delle operazioni di costruzione.

---

## 2. Candidati LR(1)

Un candidato LR(1) ha la forma:

$$
[A\to\alpha\cdot\beta,\ a]
$$

Contiene:

- una produzione $A\to\alpha\beta$;
- un punto che indica l’avanzamento nel corpo;
- un terminale di lookahead $a$.

La parte $\alpha$ è già stata riconosciuta nel contesto rappresentato dal candidato.

La parte $\beta$ deve ancora essere riconosciuta.

Il lookahead $a$ descrive un terminale che può seguire il corpo completo della produzione in quel contesto.

### 2.1 Candidato completo

Per:

$$
[A\to\alpha\cdot,\ a]
$$

la produzione è completa.

Il candidato autorizza la riduzione mediante $A\to\alpha$ quando il prossimo simbolo di input è $a$.

Per esempio:

$$
[A\to bc\cdot,\ d]
$$

autorizza la riduzione:

$$
bc\longmapsto A
$$

quando il prossimo simbolo è `d`.

La riduzione lascia `d` nell’input.

### 2.2 Significato del lookahead nei candidati incompleti

Consideriamo:

$$
[A\to\alpha\cdot Bc,\ d]
$$

Il lookahead `d` riguarda ciò che può seguire il completamento dell’intero corpo $\alpha Bc$.

Prima devono ancora essere riconosciuti $B$ e il terminale `c`.

> [!warning]
> Il lookahead del candidato non indica necessariamente il simbolo immediatamente successivo al punto.
> Coincide con il prossimo simbolo richiesto per una riduzione quando il candidato è completo.

Talvolta si scrive:

$$
[A\to\alpha\cdot\beta,\ \{b,c\}]
$$

come abbreviazione dei due candidati:

$$
[A\to\alpha\cdot\beta,b]
$$

$$
[A\to\alpha\cdot\beta,c]
$$

I due terminali sono alternative: il metodo continua a utilizzare un solo simbolo di lookahead alla volta.

---

## 3. Chiusura LR(1)

Supponiamo che uno stato contenga:

$$
[A\to\alpha\cdot B\beta,\ a]
$$

Per ogni produzione:

$$
B\to\gamma
$$

aggiungiamo:

$$
[B\to\cdot\gamma,\ b]
$$

per ogni:

$$
b\in\operatorname{FIRST}(\beta a)
$$

Ripetiamo la regola fino a quando non vengono aggiunti nuovi candidati.

### 3.1 Perché si usa FIRST di beta seguito da a

Dopo ciò che verrà riconosciuto come $B$ devono comparire:

1. una stringa derivabile da $\beta$;
2. successivamente, il lookahead $a$ del candidato iniziale.

I possibili simboli che seguono il completamento di $B$ sono quindi gli elementi di:

$$
\operatorname{FIRST}(\beta a)
$$

Se $\beta$ è annullabile, può essere propagato anche $a$.

Se $\beta=\varepsilon$:

$$
\operatorname{FIRST}(\beta a)
=
\operatorname{FIRST}(a)
=
\{a\}
$$

### 3.2 Esempio

Supponiamo di avere:

$$
[A\to\alpha\cdot BC,\ d]
$$

e le produzioni:

$$
C\to c\mid\varepsilon
$$

$$
B\to b
$$

Poiché:

$$
\operatorname{FIRST}(Cd)=\{c,d\}
$$

la chiusura aggiunge:

$$
[B\to\cdot b,c]
$$

$$
[B\to\cdot b,d]
$$

`c` può seguire $B$ quando $C$ produce `c`; `d` può seguirlo quando $C$ si annulla.

> [!important]
> I lookahead della chiusura vengono calcolati mediante FIRST$(\beta a)$.
> Non vengono sostituiti automaticamente con l’intero FOLLOW di $B$.

---

## 4. GOTO e costruzione degli stati

La funzione GOTO avanza il punto mantenendo il lookahead:

$$
\operatorname{GOTO}(I,X)=
\operatorname{CLOSURE}
\left(
\left\{
[A\to\alpha X\cdot\beta,a]
\ \middle|\
[A\to\alpha\cdot X\beta,a]\in I
\right\}
\right)
$$

Per costruire la collezione canonica:

1. si calcola lo stato iniziale mediante la chiusura;
2. si applica GOTO sui simboli possibili;
3. si aggiungono gli stati nuovi;
4. si ripete fino alla stabilizzazione.

Due stati vengono identificati soltanto quando coincidono tutti i candidati, compresi i lookahead.

Per esempio:

$$
I=\{[A\to b\cdot,c]\}
$$

e:

$$
J=\{[A\to b\cdot,d]\}
$$

sono stati diversi nell’automa LR(1) canonico.

La possibilità di distinguere questi contesti può comportare un numero maggiore di stati rispetto all’automa LR(0).

### 4.1 Convenzione sul marcatore finale

Manteniamo:

$$
S'\to S\downarrow
$$

Per la produzione aumentata utilizziamo il valore convenzionale **fine**, che indica l’input esaurito.

Lo stato iniziale è:

$$
I_0=
\operatorname{CLOSURE}
\left(
\{
[S'\to\cdot S\downarrow,\mathrm{fine}]
\}
\right)
$$

Nel calcolo dei lookahead, `fine` viene trattato come un marcatore non annullabile.

Poiché:

$$
\operatorname{FIRST}(\downarrow\,\mathrm{fine})
=
\{\downarrow\}
$$

i candidati delle produzioni di $S$ introdotti inizialmente ricevono lookahead $\downarrow$.

Il valore `fine` non viene spostato.

---

## 5. Costruzione della tabella LR(1)

Il funzionamento della pila e le operazioni del riconoscitore restano quelli già studiati.

### 5.1 Spostamento

Se:

$$
[A\to\alpha\cdot t\beta,a]\in I_i
$$

con $t$ terminale, e:

$$
\operatorname{GOTO}(I_i,t)=I_j
$$

inseriamo:

$$
\operatorname{ACTION}[i,t]=s_j
$$

Lo spostamento riguarda il terminale davanti al punto, cioè $t$.

### 5.2 Riduzione

Se:

$$
[A\to\alpha\cdot,a]\in I_i
$$

con $A\ne S'$, inseriamo la riduzione mediante $A\to\alpha$ nella colonna $a$.

Se la stessa produzione completa compare con più lookahead, si inserisce la riduzione nelle rispettive colonne.

### 5.3 Accettazione

Nella convenzione adottata, il candidato:

$$
[S'\to S\downarrow\cdot,\mathrm{fine}]
$$

produce l’accettazione a input esaurito.

### 5.4 Conflitti

Un conflitto shift/reduce si presenta quando, nello stesso stato, una riduzione è autorizzata proprio sul terminale che può essere spostato.

Un conflitto reduce/reduce si presenta quando due produzioni complete distinte autorizzano una riduzione sullo stesso lookahead.

La grammatica è LR(1) se ogni cella ACTION contiene al massimo un’azione.

---

## 6. Esempio di grammatica LR(1) ma non SLR(1)

Consideriamo:

$$
\begin{aligned}
S'&\to S\downarrow\\
S&\to L=R\mid R\\
L&\to *R\mid id\\
R&\to L
\end{aligned}
$$

Qui `id`, `*` e `=` sono terminali. In particolare, `*` è un simbolo della grammatica.

### 6.1 Il problema nella costruzione SLR(1)

L’automa LR(0) contiene uno stato:

$$
I=
\{
[S\to L\cdot=R],
[R\to L\cdot]
\}
$$

Il primo candidato permette uno spostamento su `=`.

Il secondo propone la riduzione:

$$
R\to L
$$

In questa grammatica:

$$
\operatorname{FOLLOW}(R)=\{=,\downarrow\}
$$

Il terminale `=` può seguire $R$, per esempio nella forma sentenziale ottenuta mediante:

$$
S\Rightarrow L=R\Rightarrow *R=R
$$

Quindi SLR(1) inserisce la riduzione anche nella colonna `=`, producendo un conflitto shift/reduce.

La grammatica non è SLR(1).

### 6.2 L’informazione conservata da LR(1)

Nella costruzione LR(1), lo stato raggiunto dal contesto iniziale dopo $L$ contiene:

$$
J=
\{
[S\to L\cdot=R,\downarrow],
[R\to L\cdot,\downarrow]
\}
$$

La riduzione mediante $R\to L$ è autorizzata soltanto su $\downarrow$.

Quindi:

- con prossimo simbolo `=`, si esegue lo spostamento;
- con prossimo simbolo $\downarrow$, si riduce mediante $R\to L$.

Il conflitto scompare.

Per esempio, dopo aver riconosciuto il primo identificatore come $L$ nell’input `id=id↓`, occorre spostare `=` per proseguire con $S\to L=R$.

L’informazione globale FOLLOW$(R)$ include `=` per altri contesti; il lookahead del candidato completo conserva il contesto specifico di questo stato.

La costruzione canonica completa della grammatica è priva di conflitti: la grammatica è LR(1).

---

## 7. Confronto fra i tre metodi

| Aspetto | LR(0) | SLR(1) | LR(1) canonico |
|---|---|---|---|
| Candidati | Produzione con punto | Produzione con punto | Produzione con punto e lookahead |
| Stati utilizzati | Stati LR(0) | Stati LR(0) | Stati LR(1) |
| Colonne di una riduzione | Tutte le colonne ACTION | FOLLOW del non terminale | Lookahead del candidato completo |
| Informazione per limitare le riduzioni | Nessuna distinzione sul prossimo simbolo | Insieme globale FOLLOW | Contesto conservato nel candidato |

Per le classi di grammatiche valgono le inclusioni strette:

$$
\mathrm{LR}(0)
\subsetneq
\mathrm{SLR}(1)
\subsetneq
\mathrm{LR}(1)
$$

La grammatica di gennaio 2026 mostra che una grammatica può essere SLR(1) senza essere LR(0).

L’esempio con $S\to L=R\mid R$ mostra che una grammatica può essere LR(1) senza essere SLR(1).

Anche LR(1) ha dei limiti: non tutte le grammatiche context-free sono LR(1). Inoltre, una grammatica LR(1) è non ambigua, ma la sola assenza di ambiguità non garantisce la classificazione LR(1).

---

## 8. Punti da ricordare per l’esame

Per il livello di cenni indicato nel programma, devi saper:

1. leggere un candidato $[A\to\alpha\cdot\beta,a]$;
2. spiegare che il lookahead riguarda il completamento della produzione;
3. applicare la regola di chiusura mediante FIRST$(\beta a)$;
4. ricordare che GOTO mantiene il lookahead;
5. distinguere FOLLOW globale e lookahead del singolo candidato;
6. riconoscere i conflitti nella tabella;
7. confrontare LR(0), SLR(1) e LR(1).

Errori da evitare:

- usare sempre FOLLOW al posto dei lookahead LR(1);
- interpretare il lookahead come il simbolo subito dopo il punto;
- usare il lookahead del candidato per scegliere quale terminale spostare;
- unire stati canonici ignorando differenze nei lookahead;
- pensare che LR(1) utilizzi due simboli di input perché è più potente di SLR(1).

## Collegamenti

- [[05 - Chiusura e funzione GOTO]]
- [[09 - Insiemi FIRST e FOLLOW]]
- [[10 - Analisi SLR1 e risoluzione dei conflitti]]
- [[01 - Definizioni dirette dalla sintassi e regole semantiche]]