# Derivazioni sinistre, destre e alberi di derivazione

## 1. Il problema dell’ordine di espansione

In una grammatica context-free, una forma sentenziale può contenere più non terminali.

Per esempio, dopo:

$$
S\Rightarrow AB
$$

possiamo sviluppare prima $A$ oppure prima $B$.

Le derivazioni sinistre e destre fissano una regola per scegliere quale occorrenza espandere.

> [!important]
> Sinistra e destra riguardano il non terminale da sviluppare nella forma sentenziale corrente, non l’ordine finale dei terminali.

## 2. Derivazione sinistra

Una **derivazione sinistra**, o *leftmost derivation*, espande a ogni passo il non terminale più a sinistra.

Indichiamo il passo con:

$$
\Rightarrow_{\mathrm{lm}}
$$

Formalmente, se $A\to\gamma\in P$:

$$
uAv\Rightarrow_{\mathrm{lm}}u\gamma v
$$

con:

$$
u\in\Sigma^*,\qquad v\in(N\cup\Sigma)^*
$$

La condizione $u\in\Sigma^*$ garantisce che prima di $A$ non ci siano altri non terminali.

Le applicazioni ripetute si indicano con:

$$
\Rightarrow_{\mathrm{lm}}^*
$$

## 3. Derivazione destra

Una **derivazione destra**, o *rightmost derivation*, espande a ogni passo il non terminale più a destra.

Indichiamo il passo con:

$$
\Rightarrow_{\mathrm{rm}}
$$

Formalmente, se $A\to\gamma\in P$:

$$
uAv\Rightarrow_{\mathrm{rm}}u\gamma v
$$

con:

$$
u\in(N\cup\Sigma)^*,\qquad v\in\Sigma^*
$$

La condizione $v\in\Sigma^*$ garantisce che dopo $A$ non ci siano altri non terminali.

Le applicazioni ripetute si indicano con:

$$
\Rightarrow_{\mathrm{rm}}^*
$$

## 4. Esempio: stessa frase, due ordini

Consideriamo:

$$
S\to AB
$$

$$
A\to aA\mid a
$$

$$
B\to bB\mid b
$$

Il linguaggio è:

$$
L(G)=\{a^n b^m\mid n,m\geq 1\}
$$

Vogliamo generare `aabb`.

### Derivazione sinistra

$$
S
\Rightarrow_{\mathrm{lm}}AB
\Rightarrow_{\mathrm{lm}}aAB
\Rightarrow_{\mathrm{lm}}aaB
\Rightarrow_{\mathrm{lm}}aabB
\Rightarrow_{\mathrm{lm}}aabb
$$

Prima completiamo lo sviluppo di $A$, poi quello di $B$.

### Derivazione destra

$$
S
\Rightarrow_{\mathrm{rm}}AB
\Rightarrow_{\mathrm{rm}}AbB
\Rightarrow_{\mathrm{rm}}Abb
\Rightarrow_{\mathrm{rm}}aAbb
\Rightarrow_{\mathrm{rm}}aabb
$$

Prima completiamo lo sviluppo di $B$, poi quello di $A$.

> [!warning]
> La derivazione destra non produce `bbaa`: cambia l’ordine delle espansioni, non la posizione delle componenti stabilita dalle produzioni.

## 5. Albero di derivazione

Un **albero di derivazione** è un albero radicato e ordinato che rappresenta la struttura di una frase.

- La radice è il simbolo iniziale.
- Ogni nodo interno è etichettato con un non terminale.
- Se un nodo $A$ ha figli $X_1,\ldots,X_k$, in quest’ordine, deve esistere la produzione:

$$
A\to X_1\cdots X_k
$$

- Le foglie di un albero completo sono terminali oppure marcatori $\varepsilon$ per produzioni vuote.

I figli devono mantenere l’ordine della parte destra della produzione.

### Stesso albero, due ordini di espansione

![[Allegati/FLT_04_02/flt_04_02_derivazioni.svg|1100]]

I numeri sui non terminali indicano il passo in cui viene applicata la loro produzione.

Gli alberi hanno la stessa struttura: cambiano soltanto i numeri, cioè l’ordine di espansione.

Le diverse occorrenze di $A$ sono nodi distinti, anche se portano la stessa etichetta. Lo stesso vale per $B$.

### Frontiera

La **frontiera** si legge dalle foglie, da sinistra a destra.

In entrambi gli alberi:

$$
a\cdot a\cdot b\cdot b=aabb
$$

Non si leggono i nodi per livello e non si usa l’ordine numerato delle espansioni per ottenere la stringa.

## 6. Produzioni verso ε negli alberi

Una produzione:

$$
A\to\varepsilon
$$

può essere rappresentata con una foglia $\varepsilon$ sotto $A$.

Questa foglia documenta la produzione applicata, ma non aggiunge simboli alla stringa.

Consideriamo:

$$
S\to AB
$$

$$
A\to a\mid\varepsilon
$$

$$
B\to b
$$

Per generare `b`, una derivazione sinistra è:

$$
S\Rightarrow_{\mathrm{lm}}AB
\Rightarrow_{\mathrm{lm}}B
\Rightarrow_{\mathrm{lm}}b
$$

![[Allegati/FLT_04_02/flt_04_02_epsilon.svg|800]]

La frontiera dà:

$$
\varepsilon\cdot b=b
$$

> [!important]
> Applicare una produzione verso $\varepsilon$ elimina l’occorrenza del non terminale. Non inserisce un carattere speciale nella frase.

## 7. Relazione tra alberi e derivazioni

Per una grammatica context-free:

- una derivazione completa determina un albero di derivazione;
- un albero completo determina una sola derivazione sinistra;
- lo stesso albero determina una sola derivazione destra;
- lo stesso albero può corrispondere a più derivazioni con ordine di espansione libero.

Nell’esempio di `aabb`, la derivazione sinistra e quella destra sono diverse, ma descrivono lo stesso albero.

Ogni frase generabile può essere ottenuta sia con una derivazione sinistra sia con una destra:

$$
L(G)
=
\{w\in\Sigma^*\mid S\Rightarrow_{\mathrm{lm}}^*w\}
$$

$$
=
\{w\in\Sigma^*\mid S\Rightarrow_{\mathrm{rm}}^*w\}
$$

Questo non significa che ogni frase abbia necessariamente un solo albero.

## 8. Collegamento con l’ambiguità

Una grammatica è ambigua se esiste una frase con due alberi di derivazione distinti.

Equivalentemente, esiste una frase con:

- due derivazioni sinistre distinte;
- oppure due derivazioni destre distinte.

> [!warning]
> Mostrare una derivazione sinistra e una destra della stessa frase non dimostra l’ambiguità.
>
> Potrebbero essere soltanto due ordini di espansione dello stesso albero, come nell’esempio precedente.

L’ambiguità riguarda strutture diverse, non soltanto l’ordine delle operazioni.

## 9. Derivazione destra e analisi ascendente

L’analisi ascendente ricostruisce una frase attraverso riduzioni: sostituisce una parte destra con il non terminale della produzione corrispondente.

Nell’analisi LR, le riduzioni ricostruiscono una derivazione destra al contrario.

Riprendiamo:

$$
S\Rightarrow_{\mathrm{rm}}AB
\Rightarrow_{\mathrm{rm}}AbB
\Rightarrow_{\mathrm{rm}}Abb
\Rightarrow_{\mathrm{rm}}aAbb
\Rightarrow_{\mathrm{rm}}aabb
$$

La sequenza inversa è:

$$
aabb
\rightsquigarrow aAbb
\rightsquigarrow Abb
\rightsquigarrow AbB
\rightsquigarrow AB
\rightsquigarrow S
$$

Il simbolo $\rightsquigarrow$ indica qui una riduzione.

La prima riduzione sostituisce la seconda `a` con $A$, invertendo l’ultima produzione $A\to a$ della derivazione destra.

Non basta sostituire arbitrariamente una qualsiasi sottostringa che coincida con una parte destra: bisogna individuare quella che permette di ricostruire la derivazione corretta.

### Significato di LR

- **L**: lettura dell’ingresso da sinistra verso destra.
- **R**: ricostruzione di una derivazione destra in ordine inverso.

Il parser non legge quindi la stringa da destra verso sinistra.

La sequenza mostrata descrive le riduzioni sulla frase; un parser LR le realizza durante la lettura mediante pila e operazioni di spostamento e riduzione.

## 10. Derivazione sinistra e ricorsione sinistra

Sono concetti diversi.

| Concetto | Significato |
|---|---|
| Derivazione sinistra | Si espande il non terminale più a sinistra |
| Derivazione destra | Si espande il non terminale più a destra |
| Ricorsione sinistra diretta | Una produzione ha forma $A\to A\alpha$ |
| Ricorsione destra diretta | Una produzione ha forma $A\to\alpha A$ |

L’ordine di espansione e la posizione della ricorsione sono proprietà distinte.

Nell’esempio principale, $A\to aA$ e $B\to bB$ sono ricorsive a destra, ma possiamo comunque effettuare sia derivazioni sinistre sia destre.

## 11. Procedura per gli esercizi

### Per scrivere una derivazione sinistra o destra

1. Parti dal simbolo iniziale.
2. Individua il non terminale da espandere secondo l’ordine richiesto.
3. Scegli una produzione compatibile con la frase obiettivo.
4. Applica una sola produzione per freccia.
5. Continua fino a ottenere soltanto terminali.

### Per costruire l’albero

1. Disegna la radice.
2. Per ogni produzione applicata, aggiungi i figli nell’ordine corretto.
3. Distingui le diverse occorrenze dello stesso non terminale.
4. Rappresenta le produzioni vuote con la convenzione dichiarata.
5. Leggi la frontiera e verifica che coincida con la frase richiesta.

Se compaiono etichette come $A_1$ per distinguere occorrenze, chiarisci che sono etichette dei nodi e non necessariamente nuovi non terminali della grammatica.

## 12. Errori da evitare

- Interpretare “destra” come inversione della stringa.
- Espandere un non terminale qualsiasi durante una derivazione sinistra o destra.
- Applicare più produzioni in un solo passo.
- Riordinare i figli di un nodo.
- Leggere la frontiera per livelli.
- Dimenticare i nodi non terminali che producono singoli terminali.
- Trattare $\varepsilon$ come un carattere della frase.
- Confondere occorrenze diverse dello stesso non terminale.
- Dimostrare l’ambiguità usando soltanto una derivazione sinistra e una destra dello stesso albero.
- Confondere derivazione sinistra e ricorsione sinistra.
- Pensare che LR significhi lettura dell’ingresso da destra.
