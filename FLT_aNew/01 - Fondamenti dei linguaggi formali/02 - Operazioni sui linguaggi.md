# Operazioni sui linguaggi

Un linguaggio è un insieme di stringhe. Possiamo quindi applicare sia le operazioni sugli insiemi, sia operazioni basate sulla concatenazione delle stringhe.

In questa nota consideriamo linguaggi su uno stesso alfabeto $\Sigma$.

## 1. Unione

L’unione di due linguaggi contiene le stringhe che appartengono ad almeno uno dei due:

$$
L_1\cup L_2
=
\{w\in\Sigma^*\mid w\in L_1 \lor w\in L_2\}
$$

Esempio:

$$
L_1=\{a,ab\},\qquad L_2=\{ab,b\}
$$

$$
L_1\cup L_2=\{a,ab,b\}
$$

Essendo un insieme, il risultato non contiene duplicati.

## 2. Intersezione

L’intersezione contiene le stringhe che appartengono a entrambi i linguaggi:

$$
L_1\cap L_2
=
\{w\in\Sigma^*\mid w\in L_1 \land w\in L_2\}
$$

Nell’esempio precedente:

$$
L_1\cap L_2=\{ab\}
$$

## 3. Differenza

La differenza contiene le stringhe che appartengono al primo linguaggio ma non al secondo:

$$
L_1\setminus L_2
=
\{w\in\Sigma^*\mid w\in L_1 \land w\notin L_2\}
$$

Per esempio:

$$
\{a,ab\}\setminus\{ab,b\}=\{a\}
$$

La differenza non è commutativa:

$$
L_1\setminus L_2
\neq
L_2\setminus L_1
$$

in generale.

## 4. Complemento

Il complemento di un linguaggio $L$, rispetto a $\Sigma^*$, contiene tutte le stringhe su $\Sigma$ che non appartengono a $L$:

$$
\overline{L}=\Sigma^*\setminus L
$$

Quindi:

$$
w\in\overline{L}
\iff
w\in\Sigma^* \land w\notin L
$$

### Esempio

Siano:

$$
\Sigma=\{a,b\}
$$

$$
L=\{w\in\Sigma^*\mid w\text{ termina con }a\}
$$

Allora:

$$
\overline{L}
=
\{\varepsilon\}
\cup
\{w\in\Sigma^*\mid w\text{ termina con }b\}
$$

La stringa vuota appartiene al complemento perché non termina con `a`.

> [!warning]
> Il complemento dipende dall’alfabeto di riferimento.
>
> Se cambia $\Sigma$, cambia anche $\Sigma^*$ e quindi può cambiare il complemento di $L$.

> [!important] Collegamento con l’esame
> Per ottenere l’automa del complemento scambiando stati finali e non finali, bisogna partire da un **automa deterministico completo** sull’alfabeto considerato.
>
> La costruzione sarà approfondita nella nota sugli automi per il complemento.

## 5. Concatenazione di linguaggi

La concatenazione di $L_1$ e $L_2$ contiene tutte le stringhe ottenute concatenando una stringa del primo linguaggio con una del secondo:

$$
L_1L_2
=
\{uv\mid u\in L_1,\ v\in L_2\}
$$

### Esempio

$$
L_1=\{a,ab\},\qquad L_2=\{b,bb\}
$$

Le concatenazioni sono:

| Stringa di L₁ | Stringa di L₂ | Risultato |
|---|---|---|
| `a` | `b` | `ab` |
| `a` | `bb` | `abb` |
| `ab` | `b` | `abb` |
| `ab` | `bb` | `abbb` |

Quindi:

$$
L_1L_2=\{ab,abb,abbb\}
$$

La stringa `abb` compare una sola volta nel risultato.

### Proprietà

La concatenazione è associativa:

$$
(L_1L_2)L_3=L_1(L_2L_3)
$$

In generale, non è commutativa:

$$
L_1L_2\neq L_2L_1
$$

Per esempio:

$$
\{a\}\{b\}=\{ab\}
$$

$$
\{b\}\{a\}=\{ba\}
$$

### Concatenazione con il linguaggio vuoto

$$
L\varnothing=\varnothing L=\varnothing
$$

Non possiamo scegliere una stringa da $\varnothing$, quindi non possiamo formare alcuna concatenazione.

### Concatenazione con il linguaggio contenente ε

$$
L\{\varepsilon\}=\{\varepsilon\}L=L
$$

Infatti, concatenare una stringa con $\varepsilon$ non la modifica.

> [!warning]
> $\varnothing$ e $\{\varepsilon\}$ hanno effetti completamente diversi:
>
> - $\varnothing$ annulla la concatenazione;
> - $\{\varepsilon\}$ è l’elemento neutro.

## 6. Potenza di un linguaggio

La potenza $L^n$ rappresenta la concatenazione di $n$ copie del linguaggio $L$.

La definizione è:

$$
L^0=\{\varepsilon\}
$$

$$
L^{n+1}=L^nL
\qquad n\geq 0
$$

### Esempio

Se:

$$
L=\{a,b\}
$$

allora:

$$
L^0=\{\varepsilon\}
$$

$$
L^1=\{a,b\}
$$

$$
L^2=\{aa,ab,ba,bb\}
$$

Le stringhe scelte nelle diverse copie di $L$ possono essere differenti.

> [!warning]
> In generale:
>
> $$
> L^n\neq\{w^n\mid w\in L\}
> $$
>
> Per esempio, se $L=\{a,b\}$, allora $ab\in L^2$, anche se `ab` non è il quadrato di una stringa di $L$.

## 7. Stella di Kleene

La stella di Kleene di $L$ è l’unione di tutte le sue potenze, compresa quella di esponente zero:

$$
L^*=\bigcup_{n\geq 0}L^n
$$

Contiene tutte le stringhe ottenute concatenando **zero o più stringhe di $L$**.

Poiché:

$$
L^0=\{\varepsilon\}
$$

si ha sempre:

$$
\varepsilon\in L^*
$$

### Esempio

Se:

$$
L=\{ab\}
$$

allora:

$$
L^*=\{\varepsilon,ab,abab,ababab,\ldots\}
$$

Se invece:

$$
L=\{a,b\}
$$

allora $L^*$ contiene tutte le stringhe finite formate da `a` e `b`, compresa $\varepsilon$.

## 8. Chiusura positiva

La chiusura positiva di $L$ è:

$$
L^+=\bigcup_{n\geq 1}L^n
$$

Contiene tutte le stringhe ottenute concatenando **una o più stringhe di $L$**.

Valgono:

$$
L^+=LL^*=L^*L
$$

$$
L^*=\{\varepsilon\}\cup L^+
$$

### Attenzione alla stringa vuota

Non è sempre vero che:

$$
L^+=L^*\setminus\{\varepsilon\}
$$

Questa uguaglianza vale se $\varepsilon\notin L$.

Se invece $\varepsilon\in L$, allora:

$$
\varepsilon\in L^+
$$

Infatti basta scegliere una sola stringa di $L$, precisamente $\varepsilon$.

Più in generale:

$$
\varepsilon\in L^+
\iff
\varepsilon\in L
$$

> [!important]
> “Una o più stringhe” non significa necessariamente “uno o più simboli”: una delle stringhe scelte potrebbe essere vuota.

## 9. Casi particolari fondamentali

| Linguaggio L | L⁰ | L* | L⁺ |
|---|---|---|---|
| $\varnothing$ | $\{\varepsilon\}$ | $\{\varepsilon\}$ | $\varnothing$ |
| $\{\varepsilon\}$ | $\{\varepsilon\}$ | $\{\varepsilon\}$ | $\{\varepsilon\}$ |
| $\{a\}$ | $\{\varepsilon\}$ | $\{\varepsilon,a,aa,\ldots\}$ | $\{a,aa,aaa,\ldots\}$ |

In particolare:

$$
\varnothing^*=\{\varepsilon\}
$$

perché la stella comprende anche la potenza di esponente zero.

## 10. Proprietà utili

### Unione e intersezione

Sono commutative e associative:

$$
L_1\cup L_2=L_2\cup L_1
$$

$$
L_1\cap L_2=L_2\cap L_1
$$

$$
(L_1\cup L_2)\cup L_3=L_1\cup(L_2\cup L_3)
$$

$$
(L_1\cap L_2)\cap L_3=L_1\cap(L_2\cap L_3)
$$

### Distributività della concatenazione rispetto all’unione

$$
L_1(L_2\cup L_3)=L_1L_2\cup L_1L_3
$$

$$
(L_1\cup L_2)L_3=L_1L_3\cup L_2L_3
$$

### Leggi di De Morgan

I complementi sono tutti riferiti allo stesso $\Sigma^*$:

$$
\overline{L_1\cup L_2}
=
\overline{L_1}\cap\overline{L_2}
$$

$$
\overline{L_1\cap L_2}
=
\overline{L_1}\cup\overline{L_2}
$$

## 11. Operazioni e proprietà di chiusura

Un’**operazione sui linguaggi** costruisce un linguaggio a partire da altri linguaggi.

Una **proprietà di chiusura** riguarda invece una classe di linguaggi.

Una classe $\mathcal C$ è chiusa rispetto all’unione se:

$$
L_1,L_2\in\mathcal C
\implies
L_1\cup L_2\in\mathcal C
$$

Per esempio, chiedere se i linguaggi deterministici siano chiusi rispetto all’unione significa chiedere se l’unione di due linguaggi deterministici sia sempre deterministica.

> [!tip] Collegamento con l’esame
> Questa distinzione è necessaria per affrontare le domande sulle proprietà delle classi di linguaggi.
>
> Per dimostrare una chiusura serve un argomento valido in generale. Per smentirla basta un controesempio corretto.

## 12. Controllo rapido

### Domanda 1

Se $L=\{a,b\}$, la stringa `ab` appartiene a $L^2$?

**Sì:** si ottiene scegliendo `a` dalla prima copia di $L$ e `b` dalla seconda.

### Domanda 2

La stringa vuota appartiene sempre a $L^*$?

**Sì**, perché $L^0=\{\varepsilon\}$.

### Domanda 3

La stringa vuota appartiene sempre a $L^+$?

**No:** vi appartiene se e solo se $\varepsilon\in L$.

### Domanda 4

Quanto vale $\varnothing\{a\}$?

$$
\varnothing\{a\}=\varnothing
$$

### Domanda 5

Quanto vale $\{\varepsilon\}\{a\}$?

$$
\{\varepsilon\}\{a\}=\{a\}
$$

## 13. Errori da evitare

- Confondere l’unione con la concatenazione.
- Considerare la concatenazione commutativa.
- Scrivere $L^0=\varnothing$ invece di $L^0=\{\varepsilon\}$.
- Confondere $\varnothing^*$ con $\varnothing$.
- Affermare che $L^+$ escluda sempre $\varepsilon$.
- Calcolare un complemento senza fissare l’alfabeto.
- Confondere un’operazione con una proprietà di chiusura.