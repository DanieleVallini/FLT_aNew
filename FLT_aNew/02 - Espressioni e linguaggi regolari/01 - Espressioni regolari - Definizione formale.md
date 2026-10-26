# Espressioni regolari - Definizione formale

## 1. Che cos’è un’espressione regolare

Un’**espressione regolare** è una descrizione finita di un linguaggio, costruita utilizzando simboli dell’alfabeto e alcuni operatori.

Gli operatori fondamentali sono:

- unione, o alternativa;
- concatenazione;
- stella di Kleene.

Per esempio:

$$
a^*b
$$

descrive le stringhe formate da zero o più `a`, seguite da una sola `b`:

$$
b,\quad ab,\quad aab,\quad aaab,\quad\ldots
$$

> [!important]
> L’espressione regolare è la descrizione.
>
> Il linguaggio denotato è l’insieme delle stringhe descritte.
>
> Un’espressione finita può descrivere un linguaggio infinito.

## 2. Definizione induttiva formale

Sia $\Sigma$ un alfabeto.

L’insieme delle espressioni regolari su $\Sigma$ è il più piccolo insieme costruito attraverso le seguenti regole.

### Casi base

Sono espressioni regolari:

1. $\varnothing$;
2. $\varepsilon$;
3. ogni simbolo $a\in\Sigma$.

### Passi induttivi

Se $r$ e $s$ sono espressioni regolari su $\Sigma$, allora sono espressioni regolari anche:

1. **Unione:**

$$
(r\mid s)
$$

2. **Concatenazione:**

$$
(rs)
$$

3. **Stella di Kleene:**

$$
r^*
$$

Le parentesi vengono utilizzate per rendere esplicita la struttura delle espressioni.

### Clausola di chiusura della definizione

Nient’altro è un’espressione regolare.

Equivalentemente: ogni espressione regolare si ottiene dai casi base mediante un numero finito di applicazioni dei passi induttivi.

> [!warning]
> La definizione formale non è completa se si elencano soltanto gli operatori.
>
> Bisogna specificare:
> - i casi base;
> - le regole di costruzione;
> - che non sono ammesse altre espressioni.

## 3. Il significato di “più piccolo insieme”

L’insieme delle espressioni regolari deve contenere tutti gli elementi imposti dalle regole, senza aggiungere oggetti arbitrari.

Per esempio, se:

$$
\Sigma=\{a,b\}
$$

allora:

- `a` è un’espressione regolare perché è un simbolo dell’alfabeto;
- `b` è un’espressione regolare per lo stesso motivo;
- `ab` è un’espressione regolare per concatenazione;
- `(a|b)` è un’espressione regolare per unione;
- `(a|b)*` è un’espressione regolare per applicazione della stella.

Il simbolo `c`, invece, non è un’espressione regolare su questo alfabeto, perché:

$$
c\notin\Sigma
$$

Non basta che una scrittura “assomigli” a un’espressione: deve poter essere costruita secondo le regole.

## 4. I tre casi base

### Espressione ∅

L’espressione:

$$
\varnothing
$$

denota il linguaggio vuoto, cioè un linguaggio che non contiene alcuna stringa.

### Espressione ε

L’espressione:

$$
\varepsilon
$$

denota il linguaggio che contiene soltanto la stringa vuota:

$$
\{\varepsilon\}
$$

### Espressione a

Per ogni simbolo $a\in\Sigma$, l’espressione:

$$
a
$$

denota il linguaggio contenente la sola stringa di lunghezza uno formata da quel simbolo:

$$
\{a\}
$$

> [!important]
> Non confondere:
>
> $$
> \varnothing
> \qquad\text{e}\qquad
> \{\varepsilon\}
> $$
>
> Il primo linguaggio contiene zero stringhe.
>
> Il secondo contiene una stringa, di lunghezza zero.

## 5. Gli operatori

### Unione

L’espressione:

$$
r\mid s
$$

descrive una scelta: una stringa può appartenere al linguaggio descritto da $r$ oppure a quello descritto da $s$.

Esempio:

$$
a\mid b
$$

descrive:

$$
\{a,b\}
$$

Non descrive la stringa `ab`.

### Concatenazione

L’espressione:

$$
rs
$$

descrive le stringhe ottenute concatenando una stringa descritta da $r$ con una descritta da $s$.

Esempio:

$$
ab
$$

descrive:

$$
\{ab\}
$$

L’ordine è importante:

$$
ab
\quad\text{e}\quad
ba
$$

descrivono linguaggi diversi.

### Stella di Kleene

L’espressione:

$$
r^*
$$

descrive le concatenazioni di zero o più stringhe descritte da $r$.

Esempio:

$$
(ab)^*
$$

descrive:

$$
\{\varepsilon,ab,abab,ababab,\ldots\}
$$

La possibilità di scegliere zero ripetizioni garantisce la presenza di $\varepsilon$.

> [!warning]
> La stella si applica all’espressione immediatamente precedente, rispettando le parentesi.
>
> $$
> ab^*
> $$
>
> significa una `a` seguita da zero o più `b`.
>
> $$
> (ab)^*
> $$
>
> significa zero o più ripetizioni dell’intero blocco `ab`.

## 6. Precedenza degli operatori

Quando si omettono le parentesi, si adotta normalmente questo ordine di precedenza:

1. stella di Kleene;
2. concatenazione;
3. unione.

Quindi:

$$
a\mid bc^*
$$

si interpreta come:

$$
a\mid\bigl(b(c^*)\bigr)
$$

Non come:

$$
(a\mid b)c^*
$$

### Esempio

L’espressione:

$$
a\mid bc^*
$$

descrive:

- la stringa `a`;
- oppure una `b` seguita da zero o più `c`.

Per esempio:

$$
a,\quad b,\quad bc,\quad bcc
$$

L’espressione:

$$
(a\mid b)c^*
$$

ammette invece anche:

$$
ac,\quad acc,\quad accc
$$

Le parentesi cambiano la struttura e possono cambiare il linguaggio.

## 7. Costruire un’espressione secondo la definizione

Consideriamo:

$$
(a\mid b)^*ab
$$

su:

$$
\Sigma=\{a,b\}
$$

La sua costruzione può essere giustificata così:

1. $a$ e $b$ sono espressioni regolari per i casi base.
2. $(a\mid b)$ è un’espressione regolare per unione.
3. $(a\mid b)^*$ è un’espressione regolare per applicazione della stella.
4. $(a\mid b)^*a$ è un’espressione regolare per concatenazione.
5. $(a\mid b)^*ab$ è un’espressione regolare per un’ulteriore concatenazione.

L’espressione descrive tutte le stringhe su $\{a,b\}$ che terminano con `ab`.

Per esempio:

$$
ab,\quad aab,\quad bab,\quad aaab,\quad bbab
$$

> [!tip]
> Per capire un’espressione composta, separa:
>
> - la struttura esterna;
> - le sottoespressioni;
> - l’operatore che combina ciascuna coppia o modifica una sottoespressione.

## 8. Espressioni diverse possono descrivere lo stesso linguaggio

Le espressioni:

$$
a\mid b
$$

e:

$$
b\mid a
$$

sono scritte in modo diverso, ma descrivono lo stesso linguaggio:

$$
\{a,b\}
$$

Indichiamo con $L(r)$ il linguaggio denotato da $r$.

Due espressioni sono **equivalenti** se:

$$
L(r)=L(s)
$$

Possiamo scrivere:

$$
r\equiv s
$$

Per esempio:

$$
a\mid b\equiv b\mid a
$$

Invece:

$$
ab\not\equiv ba
$$

La definizione completa di $L(r)$ sarà sviluppata nella prossima nota.

## 9. Abbreviazioni utili

Gli operatori fondamentali della definizione sono unione, concatenazione e stella.

Altre notazioni possono essere introdotte come abbreviazioni.

### Chiusura positiva

$$
r^+=rr^*
$$

Descrive una o più concatenazioni di stringhe del linguaggio di $r$.

Attenzione: se $r$ descrive anche $\varepsilon$, allora anche $r^+$ descrive $\varepsilon$.

### Opzionalità

Possiamo definire:

$$
r?=(\varepsilon\mid r)
$$

L’espressione consente di scegliere una stringa descritta da $r$ oppure la stringa vuota.

### Potenza con esponente fissato

Se $n$ è un intero fissato:

$$
r^n=
\underbrace{rr\cdots r}_{n\text{ copie}}
$$

con:

$$
r^0=\varepsilon
$$

> [!warning]
> Un esponente numerico fissato abbrevia una concatenazione finita.
>
> Una scrittura come $a^n b^n$, con $n$ variabile, non è automaticamente un’espressione regolare del formalismo base: introduce un vincolo tra quantità.
>
> Il linguaggio $\{a^n b^n\mid n\geq 0\}$ non è regolare.

## 10. Controllo rapido

Supponiamo:

$$
\Sigma=\{a,b\}
$$

| Scrittura | È un’espressione regolare? | Motivo |
|---|---|---|
| $\varepsilon$ | Sì | Caso base |
| $\varnothing$ | Sì | Caso base |
| $aa$ | Sì | Concatenazione |
| $(a\mid b)^*$ | Sì | Unione e stella |
| $(a^*)^*$ | Sì | La stella può essere applicata a un’espressione già costruita |
| $c$ | No | Il simbolo non appartiene all’alfabeto |
| $a\mid$ | No | Manca un operando dell’unione |
| $*a$ | No | La stella è un operatore postfisso |
| $(a\mid b$ | No | Le parentesi non sono bilanciate |

## 11. Cosa serve per l’esame

Alla richiesta «Definire formalmente le espressioni regolari» bisogna presentare la definizione induttiva completa:

1. fissare l’alfabeto $\Sigma$;
2. indicare $\varnothing$, $\varepsilon$ e i simboli dell’alfabeto come casi base;
3. introdurre unione, concatenazione e stella;
4. precisare che non esistono altre espressioni oltre a quelle costruite mediante queste regole.

Se viene chiesto anche il **linguaggio denotato**, questa definizione non basta: occorre aggiungere il significato formale di ciascun caso, come nella prossima nota.

## 12. Errori da evitare

- Confondere un’espressione con il linguaggio che descrive.
- Dimenticare $\varnothing$ o $\varepsilon$ nei casi base.
- Omettere la clausola “nient’altro è un’espressione regolare”.
- Confondere unione e concatenazione.
- Applicare la stella a una parte diversa da quella indicata dalle parentesi.
- Pensare che un’espressione finita descriva necessariamente un linguaggio finito.
- Usare simboli non appartenenti all’alfabeto dichiarato.
- Considerare qualsiasi formula con esponenti variabili come un’espressione regolare.