# Automi finiti non deterministici e con epsilon-mosse

## 1. Idea del non determinismo

Un **automa finito non deterministico**, abbreviato **NFA**, può ammettere più destinazioni per la stessa coppia formata da:

- stato corrente;
- simbolo letto.

Per esempio:

$$
\delta(q,a)=\{p,r\}
$$

significa che, leggendo `a` nello stato $q$, sono possibili sia il passaggio a $p$ sia quello a $r$.

Una stringa è accettata se esiste almeno un cammino che consuma tutto l’ingresso e termina in uno stato finale.

> [!important]
> Non devono essere accettanti tutti i cammini.
>
> Ne basta uno, purché abbia consumato l’intera stringa.

## 2. Definizione formale di NFA

Un automa finito non deterministico è una quintupla:

$$
M=(Q,\Sigma,\delta,q_0,F)
$$

dove:

- $Q$ è un insieme finito e non vuoto di stati;
- $\Sigma$ è l’alfabeto;
- $q_0\in Q$ è lo stato iniziale;
- $F\subseteq Q$ è l’insieme degli stati finali;
- la funzione di transizione è:

$$
\delta:Q\times\Sigma\to\mathcal P(Q)
$$

$\mathcal P(Q)$ indica l’insieme delle parti di $Q$, cioè l’insieme di tutti i suoi sottoinsiemi.

### Possibili risultati di una transizione

| Valore | Significato |
|---|---|
| $\delta(q,a)=\varnothing$ | Nessuna transizione possibile |
| $\delta(q,a)=\{p\}$ | Una sola destinazione |
| $\delta(q,a)=\{p,r\}$ | Due destinazioni possibili |

La funzione può essere definita su ogni coppia restituendo anche l’insieme vuoto.

> [!warning]
> $\varnothing$ non è uno stato.
>
> Significa che da quel ramo non è possibile consumare il simbolo considerato.

Un NFA può avere soltanto transizioni con una destinazione: il modello permette il non determinismo, ma non obbliga a utilizzarlo.

## 3. Esempio: stringhe che terminano con ab

Consideriamo:

$$
L=\{wab\mid w\in\{a,b\}^*\}
$$

Un NFA che lo riconosce ha:

$$
Q=\{q_0,q_1,q_2\}
$$

stato iniziale $q_0$ e:

$$
F=\{q_2\}
$$

La funzione di transizione è:

| Stato | a | b |
|---|---|---|
| $q_0$ | $\{q_0,q_1\}$ | $\{q_0\}$ |
| $q_1$ | $\varnothing$ | $\{q_2\}$ |
| $q_2$ | $\varnothing$ | $\varnothing$ |

### Significato degli stati

- In $q_0$ possiamo continuare a leggere il prefisso arbitrario.
- Il passaggio da $q_0$ a $q_1$ su `a` considera quella `a` come inizio del suffisso finale.
- Da $q_1$ bisogna leggere `b` per raggiungere $q_2$.
- $q_2$ è finale e non permette di consumare altri simboli.

### Lettura di aab

Un cammino accettante è:

$$
q_0\xrightarrow{a}q_0
\xrightarrow{a}q_1
\xrightarrow{b}q_2
$$

Esiste anche un cammino che rimane sempre in $q_0$, ma questo non impedisce l’accettazione.

### Lettura di aba

Dopo `ab` è possibile essere in $q_2$, ma resta ancora una `a`.

Da $q_2$ non possiamo consumarla. Gli altri cammini non terminano in uno stato finale.

Quindi `aba` è rifiutata.

> [!warning]
> Raggiungere uno stato finale prima di aver consumato tutto l’ingresso non basta.

## 4. Simulare un NFA mediante insiemi di stati

Possiamo simulare un NFA conservando tutti gli stati raggiungibili dopo ogni prefisso.

Se l’insieme corrente è $R$ e leggiamo $a$, il nuovo insieme è:

$$
\operatorname{Move}(R,a)
=
\bigcup_{q\in R}\delta(q,a)
$$

Si uniscono tutte le destinazioni possibili.

### Esempio con aab

Per l’automa precedente:

| Prefisso letto | Stati raggiungibili |
|---|---|
| $\varepsilon$ | $\{q_0\}$ |
| `a` | $\{q_0,q_1\}$ |
| `aa` | $\{q_0,q_1\}$ |
| `aab` | $\{q_0,q_2\}$ |

Dopo la seconda `a`, il ramo che si trovava in $q_1$ non può proseguire. Rimangono però i rami generati da $q_0$.

Alla fine è presente $q_2$, quindi la stringa è accettata.

> [!important]
> Se uno stato non ha transizioni sul simbolo letto, quel ramo scompare.
>
> Non rimane automaticamente fermo nello stato precedente.

## 5. Automi con epsilon-mosse

Un automa con **epsilon-mosse**, o **ε-NFA**, permette anche transizioni che non consumano simboli dell’ingresso.

La funzione di transizione diventa:

$$
\delta:
Q\times(\Sigma\cup\{\varepsilon\})
\to\mathcal P(Q)
$$

con:

$$
\varepsilon\notin\Sigma
$$

Una transizione:

$$
q\xrightarrow{\varepsilon}p
$$

permette di passare da $q$ a $p$ lasciando invariato l’ingresso ancora da leggere.

Le epsilon-mosse possono essere eseguite:

- prima del primo simbolo;
- tra due simboli;
- dopo l’ultimo simbolo.

> [!warning]
> Una epsilon-mossa non legge un carattere “vuoto”.
>
> Non consuma alcun simbolo.

## 6. Epsilon-chiusura

L’**epsilon-chiusura** di uno stato $q$ è l’insieme degli stati raggiungibili da $q$ mediante zero o più epsilon-mosse.

La indichiamo con:

$$
E(q)
$$

Poiché sono ammessi zero passi:

$$
q\in E(q)
$$

anche se da $q$ non parte nessuna epsilon-transizione.

### Esempio

Se:

$$
q_0\xrightarrow{\varepsilon}q_1
\xrightarrow{\varepsilon}q_2
$$

e non ci sono altri collegamenti epsilon:

$$
E(q_0)=\{q_0,q_1,q_2\}
$$

$$
E(q_1)=\{q_1,q_2\}
$$

$$
E(q_2)=\{q_2\}
$$

### Epsilon-chiusura di un insieme

Per un insieme $R\subseteq Q$:

$$
E(R)=\bigcup_{q\in R}E(q)
$$

In particolare:

$$
E(\varnothing)=\varnothing
$$

Se ci sono cicli di epsilon-mosse, ogni stato viene comunque inserito una sola volta: la chiusura è un insieme finito.

## 7. Simulare un ε-NFA

Per leggere:

$$
w=a_1a_2\cdots a_n
$$

bisogna considerare le epsilon-mosse sia prima della lettura sia dopo ogni simbolo.

### Insieme iniziale

$$
R_0=E(q_0)
$$

### Passo di lettura

Per $0\leq i<n$:

$$
R_{i+1}
=
E\left(
\bigcup_{q\in R_i}\delta(q,a_{i+1})
\right)
$$

Il procedimento è:

1. partire dall’insieme degli stati attualmente raggiungibili;
2. consumare il simbolo da tutti questi stati;
3. unire le destinazioni;
4. calcolarne l’epsilon-chiusura.

### Accettazione

La stringa è accettata se:

$$
R_n\cap F\neq\varnothing
$$

Se l’insieme raggiunto è vuoto, non esiste più alcun cammino capace di proseguire.

## 8. Esempio con epsilon-mosse prima e dopo la lettura

Consideriamo:

$$
Q=\{p_0,p_1,p_2,p_3\}
$$

con stato iniziale $p_0$ e:

$$
F=\{p_3\}
$$

Le transizioni sono:

| Stato | a | b | ε |
|---|---|---|---|
| $p_0$ | $\varnothing$ | $\varnothing$ | $\{p_1\}$ |
| $p_1$ | $\{p_1\}$ | $\{p_2\}$ | $\varnothing$ |
| $p_2$ | $\varnothing$ | $\varnothing$ | $\{p_3\}$ |
| $p_3$ | $\varnothing$ | $\varnothing$ | $\varnothing$ |

Il linguaggio riconosciuto è:

$$
a^*b
$$

### Epsilon-chiusure

$$
E(p_0)=\{p_0,p_1\}
$$

$$
E(p_1)=\{p_1\}
$$

$$
E(p_2)=\{p_2,p_3\}
$$

$$
E(p_3)=\{p_3\}
$$

### Lettura di aab

| Prefisso letto | Stati raggiungibili, incluse le epsilon-mosse |
|---|---|
| $\varepsilon$ | $\{p_0,p_1\}$ |
| `a` | $\{p_1\}$ |
| `aa` | $\{p_1\}$ |
| `aab` | $\{p_2,p_3\}$ |

La stringa è accettata perché dopo l’ultimo simbolo possiamo eseguire:

$$
p_2\xrightarrow{\varepsilon}p_3
$$

e raggiungere uno stato finale.

La stringa vuota, invece, non è accettata:

$$
E(p_0)\cap F=\varnothing
$$

## 9. Linguaggio riconosciuto e funzione estesa

Indichiamo con:

$$
\delta^*(q,w)
$$

l’insieme degli stati raggiungibili dopo aver letto interamente $w$ a partire da $q$, includendo le eventuali epsilon-mosse.

Per NFA ed ε-NFA, il risultato è quindi un insieme di stati.

Il linguaggio riconosciuto è:

$$
L(M)=
\{w\in\Sigma^*
\mid
\delta^*(q_0,w)\cap F\neq\varnothing\}
$$

### Caso della stringa vuota

Per un NFA senza epsilon-mosse:

$$
\delta^*(q,\varepsilon)=\{q\}
$$

Quindi:

$$
\varepsilon\in L(M)
\iff
q_0\in F
$$

Per un ε-NFA:

$$
\delta^*(q,\varepsilon)=E(q)
$$

Quindi:

$$
\varepsilon\in L(M)
\iff
E(q_0)\cap F\neq\varnothing
$$

Lo stato iniziale non deve necessariamente essere finale: può raggiungere un finale tramite epsilon-mosse.

## 10. Confronto tra i modelli

| Aspetto | DFA completo | NFA | ε-NFA |
|---|---|---|---|
| Risultato di una transizione | Un singolo stato | Un insieme di stati | Un insieme di stati |
| Più destinazioni sullo stesso simbolo | No | Ammesse | Ammesse |
| Nessuna destinazione su un simbolo | No | Ammessa | Ammessa |
| Epsilon-mosse | No | No | Ammesse |
| Accettazione | L’unico cammino termina in un finale | Esiste un cammino accettante | Esiste un cammino accettante, eventualmente con epsilon-mosse |
| Classe di linguaggi riconosciuti | Regolari | Regolari | Regolari |

Un ε-NFA non riconosce più linguaggi di un DFA.

Gli stati possibili durante la simulazione sono sottoinsiemi di $Q$, e i sottoinsiemi sono in numero finito:

$$
|\mathcal P(Q)|=2^{|Q|}
$$

Questa è l’idea alla base della determinizzazione.

## 11. Collegamenti con l’esame

Gli esercizi richiedono spesso:

1. eliminare le epsilon-mosse;
2. eliminare il non determinismo;
3. minimizzare.

Per affrontarli devi distinguere:

- una transizione su un simbolo;
- una epsilon-transizione;
- l’insieme degli stati raggiungibili;
- il criterio di accettazione.

L’eliminazione delle epsilon-mosse deve conservare anche l’eventuale accettazione di $\varepsilon$.

La determinizzazione deve conservare tutti i cammini possibili attraverso opportuni insiemi di stati.

## 12. Errori da evitare

- Pensare che debbano accettare tutti i cammini.
- Accettare con un cammino che non ha consumato tutta la stringa.
- Confondere $\varnothing$ con uno stato.
- Lasciare fermo un ramo quando manca una transizione.
- Dimenticare lo stato stesso nella sua epsilon-chiusura.
- Considerare soltanto le epsilon-mosse iniziali, ignorando quelle dopo i simboli.
- Consumare un simbolo durante una epsilon-mossa.
- Dimenticare l’epsilon-chiusura dopo l’ultimo simbolo.
- Ritenere necessaria la finalità dello stato iniziale per accettare $\varepsilon$ in un ε-NFA.
- Pensare che il non determinismo aumenti il potere espressivo degli automi finiti.