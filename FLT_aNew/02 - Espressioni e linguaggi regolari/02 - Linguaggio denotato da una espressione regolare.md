# Linguaggio denotato da una espressione regolare

## 1. Dalla sintassi al significato

La definizione di espressione regolare stabilisce quali espressioni possiamo costruire.

La definizione del **linguaggio denotato** stabilisce invece quali stringhe descrive ciascuna espressione.

Indichiamo con:

$$
L(r)
$$

il linguaggio denotato dall’espressione regolare $r$.

Formalmente, fissato l’alfabeto $\Sigma$, la funzione di interpretazione associa a ogni espressione regolare un sottoinsieme di $\Sigma^*$:

$$
L:\mathrm{ER}(\Sigma)\to\mathcal P(\Sigma^*)
$$

dove:

- $\mathrm{ER}(\Sigma)$ è l’insieme delle espressioni regolari su $\Sigma$;
- $\mathcal P(\Sigma^*)$ è l’insieme di tutti i sottoinsiemi di $\Sigma^*$.

> [!important]
> $r$ è un’espressione.
>
> $L(r)$ è un insieme di stringhe.
>
> Per esempio, $ab$ è un’espressione regolare, mentre $L(ab)=\{ab\}$ è il suo linguaggio.

## 2. Definizione induttiva: casi base

La definizione del linguaggio segue la struttura dell’espressione.

### Linguaggio vuoto

$$
L(\varnothing)=\varnothing
$$

L’espressione $\varnothing$ non descrive alcuna stringa.

### Stringa vuota

$$
L(\varepsilon)=\{\varepsilon\}
$$

L’espressione $\varepsilon$ descrive una sola stringa: quella di lunghezza zero.

### Simbolo dell’alfabeto

Per ogni $a\in\Sigma$:

$$
L(a)=\{a\}
$$

Il linguaggio contiene la sola stringa di lunghezza uno costituita dal simbolo $a$.

> [!warning]
> Scrivere $L(a)=a$ non è formalmente corretto: il risultato deve essere un insieme.
>
> Analogamente:
>
> $$
> L(\varepsilon)=\{\varepsilon\}
> $$
>
> e non semplicemente $\varepsilon$.

## 3. Definizione induttiva: operatori

Siano $r$ e $s$ espressioni regolari su $\Sigma$.

### Unione

$$
L(r\mid s)=L(r)\cup L(s)
$$

Equivalentemente:

$$
w\in L(r\mid s)
\iff
w\in L(r)\lor w\in L(s)
$$

Per esempio:

$$
L(a\mid b)=\{a\}\cup\{b\}=\{a,b\}
$$

### Concatenazione

$$
L(rs)=L(r)L(s)
$$

La concatenazione tra linguaggi è definita da:

$$
L(r)L(s)
=
\{uv\mid u\in L(r),\ v\in L(s)\}
$$

Quindi:

$$
w\in L(rs)
\iff
\exists u,v:
w=uv,\quad u\in L(r),\quad v\in L(s)
$$

Per esempio:

$$
L((a\mid b)c)
=
\{a,b\}\{c\}
=
\{ac,bc\}
$$

### Stella di Kleene

$$
L(r^*)=(L(r))^*
$$

Esplicitando la stella applicata al linguaggio:

$$
L(r^*)=\bigcup_{n\geq 0}(L(r))^n
$$

dove:

$$
(L(r))^0=\{\varepsilon\}
$$

e:

$$
(L(r))^{n+1}=(L(r))^nL(r)
$$

> [!important]
> La stella a sinistra è un operatore sulle espressioni.
>
> La stella a destra è l’operazione sui linguaggi che ne definisce il significato.

## 4. Che cosa viene ripetuto nella stella

Una stringa appartiene a $L(r^*)$ se si può ottenere concatenando un numero finito, anche zero, di stringhe di $L(r)$.

Le stringhe scelte possono essere diverse tra loro.

Per esempio:

$$
r=a\mid bb
$$

ha linguaggio:

$$
L(r)=\{a,bb\}
$$

Allora:

$$
L(r^*)=
\{\varepsilon,a,bb,aa,abb,bba,bbbb,\ldots\}
$$

La stringa `abb` appartiene al linguaggio perché:

$$
abb=a\cdot bb
$$

con entrambi i fattori appartenenti a $L(r)$.

La stringa `bab`, invece, non appartiene al linguaggio: non può essere suddivisa in blocchi `a` e `bb`.

> [!warning]
> Nella formula:
>
> $$
> L(r^*)=\bigcup_{n\geq 0}(L(r))^n
> $$
>
> $n$ conta il numero dei fattori concatenati, non necessariamente il numero dei simboli della stringa.

## 5. Esempio completo di calcolo

Consideriamo:

$$
r=(a\mid\varepsilon)b^*
$$

Applichiamo le definizioni dall’esterno verso le sottoespressioni:

$$
L(r)=L(a\mid\varepsilon)L(b^*)
$$

$$
=
\bigl(L(a)\cup L(\varepsilon)\bigr)(L(b))^*
$$

$$
=
\bigl(\{a\}\cup\{\varepsilon\}\bigr)\{b\}^*
$$

Poiché:

$$
\{b\}^*=\{b^n\mid n\geq 0\}
$$

otteniamo:

$$
L(r)
=
\{ab^n\mid n\geq 0\}
\cup
\{b^n\mid n\geq 0\}
$$

In parole: zero o una `a`, seguita da un numero arbitrario di `b`.

Esempi:

| Stringa | Appartiene al linguaggio? | Scelta |
|---|---|---|
| $\varepsilon$ | Sì | Alternativa $\varepsilon$ e zero `b` |
| `a` | Sì | Alternativa `a` e zero `b` |
| `bbb` | Sì | Alternativa $\varepsilon$ e tre `b` |
| `abb` | Sì | Alternativa `a` e due `b` |
| `aab` | No | Non sono ammesse due `a` |
| `ba` | No | L’eventuale `a` deve precedere tutte le `b` |

## 6. Collegamento con implicazione e derivazione

La definizione precedente può essere collegata formalmente alle regole di generazione di una grammatica.

Un modo per farlo consiste nell’associare a ogni sottoespressione $t$ un non terminale $A_t$.

Il non terminale iniziale è $A_r$, dove $r$ è l’espressione da interpretare.

### Produzioni associate alle sottoespressioni

| Sottoespressione | Produzioni associate |
|---|---|
| $\varnothing$ | Nessuna produzione per $A_{\varnothing}$ |
| $\varepsilon$ | $A_{\varepsilon}\to\varepsilon$ |
| $a$ | $A_a\to a$ |
| $s\mid t$ | $A_{s\mid t}\to A_s\mid A_t$ |
| $st$ | $A_{st}\to A_sA_t$ |
| $s^*$ | $A_{s^*}\to\varepsilon\mid A_sA_{s^*}$ |

Indichiamo con $G_r$ la grammatica così ottenuta.

L’assenza di produzioni per $A_{\varnothing}$ impedisce di trasformarlo in una stringa terminale.

La produzione per la stella permette invece di scegliere zero ripetizioni oppure di generare una stringa del linguaggio di $s$ e continuare.

### Implicazione in un passo

Una produzione:

$$
A_t\to\beta
$$

permette la riscrittura:

$$
xA_ty\Rightarrow_{G_r}x\beta y
$$

Si sostituisce una sola occorrenza del non terminale, lasciando invariato ciò che la circonda.

### Derivazione

La relazione:

$$
\Rightarrow_{G_r}^*
$$

è la chiusura riflessiva e transitiva della relazione in un passo: permette zero o più applicazioni di produzioni.

Il linguaggio denotato può quindi essere espresso anche come:

$$
L(r)=
\{w\in\Sigma^*\mid A_r\Rightarrow_{G_r}^*w\}
$$

Questa costruzione rende esplicito il collegamento tra espressioni regolari, produzioni e derivazioni.

La grammatica ottenuta è context-free e genera un linguaggio regolare, ma non è necessariamente già in forma lineare destra.

### Esempio

Per:

$$
r=(a\mid b)b
$$

possiamo usare i nomi abbreviati:

$$
A_r\to UB
$$

$$
U\to A\mid B
$$

$$
A\to a
\qquad
B\to b
$$

Una derivazione è:

$$
A_r\Rightarrow UB
\Rightarrow AB
\Rightarrow aB
\Rightarrow ab
$$

Quindi:

$$
ab\in L(r)
$$

L’altra stringa generata è `bb`:

$$
L(r)=\{ab,bb\}
$$

## 7. Quando il linguaggio contiene ε

Dalle definizioni seguono criteri utili per controllare rapidamente la presenza della stringa vuota.

### Unione

$$
\varepsilon\in L(r\mid s)
\iff
\varepsilon\in L(r)
\lor
\varepsilon\in L(s)
$$

Basta che una delle alternative generi la stringa vuota.

### Concatenazione

$$
\varepsilon\in L(rs)
\iff
\varepsilon\in L(r)
\land
\varepsilon\in L(s)
$$

Entrambi i fattori devono poter essere vuoti: concatenare stringhe non vuote non può produrre una stringa vuota.

### Stella

$$
\varepsilon\in L(r^*)
$$

per qualsiasi espressione regolare $r$, grazie alla possibilità di scegliere zero fattori.

> [!tip]
> Negli esercizi, controllare subito la presenza di $\varepsilon$ aiuta a individuare errori nell’espressione o negli stati finali dell’automa equivalente.

## 8. Casi particolari fondamentali

| Espressione | Linguaggio denotato |
|---|---|
| $\varnothing$ | $\varnothing$ |
| $\varepsilon$ | $\{\varepsilon\}$ |
| $a\varepsilon$ | $\{a\}$ |
| $a\varnothing$ | $\varnothing$ |
| $\varnothing^*$ | $\{\varepsilon\}$ |
| $\varepsilon^*$ | $\{\varepsilon\}$ |
| $a^*$ | $\{a^n\mid n\geq 0\}$ |

### Perché ∅* contiene ε

$$
L(\varnothing^*)
=
\bigcup_{n\geq 0}\varnothing^n
$$

Per definizione:

$$
\varnothing^0=\{\varepsilon\}
$$

mentre:

$$
\varnothing^n=\varnothing
\qquad n\geq 1
$$

Quindi:

$$
L(\varnothing^*)=\{\varepsilon\}
$$

Non viene scelta alcuna stringa dal linguaggio vuoto: si sceglie una concatenazione di zero fattori.

## 9. Linguaggi regolari ed equivalenza

Un linguaggio $L\subseteq\Sigma^*$ è regolare se e solo se esiste un’espressione regolare $r$ su $\Sigma$ tale che:

$$
L=L(r)
$$

Due espressioni regolari $r$ e $s$ sono equivalenti quando:

$$
L(r)=L(s)
$$

Per esempio:

$$
a(b\mid c)\equiv ab\mid ac
$$

perché entrambe denotano:

$$
\{ab,ac\}
$$

La diversa struttura delle espressioni non impedisce che il linguaggio descritto sia identico.

## 10. Come rispondere alla domanda teorica d’esame

Se viene chiesto di definire formalmente i linguaggi denotati dalle espressioni regolari, una risposta completa deve:

1. fissare l’alfabeto $\Sigma$;
2. ricordare la definizione induttiva delle espressioni regolari;
3. definire i linguaggi dei tre casi base;
4. definire il significato di unione, concatenazione e stella;
5. esplicitare che la stella include la potenza di esponente zero.

Le sei equazioni centrali sono:

$$
L(\varnothing)=\varnothing
$$

$$
L(\varepsilon)=\{\varepsilon\}
$$

$$
L(a)=\{a\}
\qquad a\in\Sigma
$$

$$
L(r\mid s)=L(r)\cup L(s)
$$

$$
L(rs)=L(r)L(s)
$$

$$
L(r^*)=
\bigcup_{n\geq 0}(L(r))^n
$$

Se la richiesta richiama anche implicazione e derivazione, bisogna esplicitare la relazione di riscrittura in un passo e la sua chiusura, collegandole alla generazione delle stringhe, come nella sezione 6.

## 11. Errori da evitare

- Scrivere una stringa al posto di un insieme nel risultato di $L$.
- Confondere $L(\varnothing)$ con $L(\varepsilon)$.
- Dimenticare il caso $n=0$ nella stella.
- Pensare che nella stella debba essere ripetuta sempre la stessa stringa.
- Confondere il numero dei fattori con la lunghezza del risultato.
- Applicare gli operatori senza rispettare parentesi e precedenze.
- Considerare una forma con non terminali come una stringa terminale generata.
- Dare soltanto esempi quando viene richiesta una definizione formale.