# Funzione di transizione estesa e riconoscimento

## 1. Perché serve una funzione estesa

La funzione di transizione $\delta$ descrive l’effetto della lettura di un singolo simbolo.

Per stabilire che cosa accade leggendo un’intera stringa utilizziamo la **funzione di transizione estesa**, indicata con:

$$
\delta^*
$$

oppure, in alcuni testi, con:

$$
\widehat{\delta}
$$

La definizione dipende dal modello:

- in un DFA il risultato è uno stato;
- in un NFA o ε-NFA il risultato è un insieme di stati.

> [!important]
> La funzione estesa descrive gli stati raggiungibili dopo aver consumato esattamente la stringa indicata.

## 2. Funzione estesa per un DFA

Sia:

$$
M=(Q,\Sigma,\delta,q_0,F)
$$

un automa finito deterministico completo, con:

$$
\delta:Q\times\Sigma\to Q
$$

La funzione estesa è:

$$
\delta^*:Q\times\Sigma^*\to Q
$$

### Caso base

$$
\delta^*(q,\varepsilon)=q
$$

Leggere la stringa vuota non cambia lo stato.

### Passo induttivo

Per ogni $w\in\Sigma^*$ e $a\in\Sigma$:

$$
\delta^*(q,wa)
=
\delta\bigl(\delta^*(q,w),a\bigr)
$$

Prima si legge il prefisso $w$, poi l’ultimo simbolo $a$.

In particolare:

$$
\delta^*(q,a)=\delta(q,a)
$$

### Lettura di una stringa

Se:

$$
w=a_1a_2\cdots a_n
$$

possiamo calcolare:

$$
r_0=q
$$

$$
r_{i+1}=\delta(r_i,a_{i+1})
$$

Alla fine:

$$
\delta^*(q,w)=r_n
$$

## 3. Esempio su un DFA

Consideriamo l’automa che riconosce un numero pari di `a`.

| Stato | a | b |
|---|---|---|
| $q_0$ | $q_1$ | $q_0$ |
| $q_1$ | $q_0$ | $q_1$ |

Lo stato iniziale è $q_0$ e:

$$
F=\{q_0\}
$$

Calcoliamo:

$$
\delta^*(q_0,abba)
$$

| Prefisso letto | Stato raggiunto |
|---|---|
| $\varepsilon$ | $q_0$ |
| `a` | $q_1$ |
| `ab` | $q_1$ |
| `abb` | $q_1$ |
| `abba` | $q_0$ |

Quindi:

$$
\delta^*(q_0,abba)=q_0
$$

La stessa lettura può essere scritta come applicazione annidata:

$$
\delta^*(q_0,abba)
=
\delta\bigl(
\delta\bigl(
\delta\bigl(
\delta(q_0,a),b
\bigr),b
\bigr),a
\bigr)
$$

Il risultato appartiene a $F$, quindi la stringa è accettata.

## 4. Funzione estesa per un NFA senza epsilon-mosse

Per un NFA:

$$
\delta:Q\times\Sigma\to\mathcal P(Q)
$$

La funzione estesa restituisce un insieme:

$$
\delta^*:Q\times\Sigma^*\to\mathcal P(Q)
$$

### Caso base

$$
\delta^*(q,\varepsilon)=\{q\}
$$

Il risultato è il singoletto contenente lo stato, non lo stato da solo.

### Passo induttivo

$$
\delta^*(q,wa)
=
\bigcup_{p\in\delta^*(q,w)}\delta(p,a)
$$

Bisogna:

1. trovare tutti gli stati raggiungibili leggendo $w$;
2. leggere $a$ da ciascuno;
3. unire tutte le destinazioni.

### Esempio

Consideriamo:

| Stato | a | b |
|---|---|---|
| $q_0$ | $\{q_0,q_1\}$ | $\{q_0\}$ |
| $q_1$ | $\varnothing$ | $\{q_2\}$ |
| $q_2$ | $\varnothing$ | $\varnothing$ |

Lo stato iniziale è $q_0$ e $F=\{q_2\}$.

Dopo `a`:

$$
\delta^*(q_0,a)=\{q_0,q_1\}
$$

Dopo `ab`:

$$
\delta^*(q_0,ab)
=
\delta(q_0,b)\cup\delta(q_1,b)
$$

$$
=
\{q_0\}\cup\{q_2\}
=
\{q_0,q_2\}
$$

La stringa è accettata perché tra gli stati raggiunti compare $q_2$.

Non è necessario che tutti gli stati raggiunti siano finali.

## 5. Epsilon-chiusura e transizione diretta su ε

Per un ε-NFA bisogna distinguere:

$$
\delta(q,\varepsilon)
$$

da:

$$
E(q)
$$

### Transizione diretta

$\delta(q,\varepsilon)$ contiene le destinazioni raggiungibili con una sola epsilon-mossa.

### Epsilon-chiusura

$E(q)$ contiene gli stati raggiungibili con zero o più epsilon-mosse.

Per esempio, se:

$$
q_0\xrightarrow{\varepsilon}q_1
\xrightarrow{\varepsilon}q_2
$$

senza altri archi epsilon:

$$
\delta(q_0,\varepsilon)=\{q_1\}
$$

ma:

$$
E(q_0)=\{q_0,q_1,q_2\}
$$

Per un insieme $R\subseteq Q$ definiamo:

$$
E(R)=\bigcup_{q\in R}E(q)
$$

> [!warning]
> La transizione diretta su $\varepsilon$ non coincide, in generale, con l’epsilon-chiusura.

## 6. Funzione estesa per un ε-NFA

La funzione di transizione è:

$$
\delta:
Q\times(\Sigma\cup\{\varepsilon\})
\to\mathcal P(Q)
$$

La funzione estesa è:

$$
\delta^*:Q\times\Sigma^*\to\mathcal P(Q)
$$

### Caso base

$$
\delta^*(q,\varepsilon)=E(q)
$$

Anche senza leggere simboli possiamo eseguire epsilon-mosse.

### Passo induttivo

$$
\delta^*(q,wa)
=
E\left(
\bigcup_{p\in\delta^*(q,w)}\delta(p,a)
\right)
$$

La lettura di $w$ ha già considerato le epsilon-mosse possibili fino a quel momento.

Per leggere il nuovo simbolo:

1. si eseguono le transizioni su $a$;
2. si uniscono le destinazioni;
3. si applica nuovamente l’epsilon-chiusura.

### Caso di un solo simbolo

Sostituendo $w=\varepsilon$:

$$
\delta^*(q,a)
=
E\left(
\bigcup_{p\in E(q)}\delta(p,a)
\right)
$$

Questa formula considera le epsilon-mosse sia prima sia dopo il simbolo.

> [!important]
> In un ε-NFA, calcolare soltanto $E(\delta(q,a))$ può essere sbagliato: mancherebbero le epsilon-mosse prima della lettura di `a`.

## 7. Esempio completo con epsilon-mosse

Consideriamo:

| Stato | a | b | ε |
|---|---|---|---|
| $p_0$ | $\varnothing$ | $\varnothing$ | $\{p_1\}$ |
| $p_1$ | $\{p_1\}$ | $\{p_2\}$ | $\varnothing$ |
| $p_2$ | $\varnothing$ | $\varnothing$ | $\{p_3\}$ |
| $p_3$ | $\varnothing$ | $\varnothing$ | $\varnothing$ |

Lo stato iniziale è $p_0$ e:

$$
F=\{p_3\}
$$

Vogliamo calcolare:

$$
\delta^*(p_0,ab)
$$

### Passaggio iniziale

$$
R_0=E(p_0)=\{p_0,p_1\}
$$

### Lettura di a

Prima uniamo le transizioni su `a`:

$$
\delta(p_0,a)\cup\delta(p_1,a)
=
\varnothing\cup\{p_1\}
=
\{p_1\}
$$

Poi applichiamo la chiusura:

$$
R_1=E(\{p_1\})=\{p_1\}
$$

### Lettura di b

$$
\bigcup_{p\in R_1}\delta(p,b)
=
\delta(p_1,b)
=
\{p_2\}
$$

Applicando la chiusura:

$$
R_2=E(\{p_2\})=\{p_2,p_3\}
$$

Quindi:

$$
\delta^*(p_0,ab)=\{p_2,p_3\}
$$

La stringa è accettata perché:

$$
\{p_2,p_3\}\cap\{p_3\}\neq\varnothing
$$

Il calcolo deve includere l’epsilon-mossa finale da $p_2$ a $p_3$.

## 8. Definizione formale del linguaggio riconosciuto

### DFA

$$
L(M)=
\{w\in\Sigma^*
\mid
\delta^*(q_0,w)\in F\}
$$

Il risultato della funzione estesa è uno stato.

### NFA ed ε-NFA

$$
L(M)=
\{w\in\Sigma^*
\mid
\delta^*(q_0,w)\cap F\neq\varnothing\}
$$

Il risultato è un insieme: deve contenere almeno uno stato finale.

### Confronto

| Modello | $\delta^*(q,\varepsilon)$ | Criterio di accettazione |
|---|---|---|
| DFA | $q$ | Lo stato raggiunto appartiene a $F$ |
| NFA senza epsilon-mosse | $\{q\}$ | L’insieme raggiunto interseca $F$ |
| ε-NFA | $E(q)$ | L’insieme raggiunto interseca $F$ |

> [!warning]
> Per un NFA non si richiede:
>
> $$
> \delta^*(q_0,w)\subseteq F
> $$
>
> Questa condizione pretenderebbe che tutti gli stati raggiunti fossero finali. Il criterio corretto richiede soltanto un’intersezione non vuota.

## 9. Riconoscimento della stringa vuota

### DFA

$$
\varepsilon\in L(M)
\iff
q_0\in F
$$

### NFA senza epsilon-mosse

Vale lo stesso criterio:

$$
\varepsilon\in L(M)
\iff
q_0\in F
$$

### ε-NFA

$$
\varepsilon\in L(M)
\iff
E(q_0)\cap F\neq\varnothing
$$

Lo stato iniziale può non essere finale, purché raggiunga un finale senza consumare simboli.

## 10. Proprietà di composizione

Leggere una concatenazione $uv$ equivale a leggere prima $u$ e poi $v$.

### DFA

$$
\delta^*(q,uv)
=
\delta^*\bigl(\delta^*(q,u),v\bigr)
$$

Lo stato raggiunto dopo $u$ diventa il punto di partenza per leggere $v$.

### NFA ed ε-NFA

$$
\delta^*(q,uv)
=
\bigcup_{p\in\delta^*(q,u)}
\delta^*(p,v)
$$

Dopo $u$ possono esserci più stati: bisogna leggere $v$ da ciascuno e unire i risultati.

La proprietà vale anche in presenza di epsilon-mosse, perché sono già comprese nella definizione della funzione estesa.

### Se l’insieme raggiunto diventa vuoto

Se:

$$
\delta^*(q,u)=\varnothing
$$

allora, per ogni $v\in\Sigma^*$:

$$
\delta^*(q,uv)=\varnothing
$$

Non esistono più cammini da cui continuare.

## 11. Che cosa saper scrivere all’esame

Se viene richiesta la definizione formale della funzione estesa, devi indicare:

1. il modello di automa;
2. il dominio e il codominio;
3. il caso base sulla stringa vuota;
4. il passo induttivo sulla stringa $wa$.

Nel caso di un ε-NFA devi inoltre definire l’epsilon-chiusura e utilizzarla:

- nel caso base;
- dopo ogni lettura di simbolo.

Se viene richiesto il riconoscimento, aggiungi il criterio corretto sugli stati finali.

## 12. Errori da evitare

- Confondere $\delta$, che legge un simbolo, con $\delta^*$, che legge una stringa.
- Restituire uno stato invece di un insieme nel caso non deterministico.
- Scrivere $\delta^*(q,\varepsilon)=q$ per un NFA invece di $\{q\}$.
- Usare $\delta(q,\varepsilon)$ al posto dell’epsilon-chiusura.
- Dimenticare le epsilon-mosse prima del primo simbolo.
- Dimenticare la chiusura dopo un simbolo, soprattutto dopo l’ultimo.
- Controllare un solo cammino di un NFA.
- Richiedere che tutti gli stati raggiunti siano finali.
- Accettare senza aver consumato l’intera stringa.
- Considerare l’insieme vuoto come un ramo che può proseguire.