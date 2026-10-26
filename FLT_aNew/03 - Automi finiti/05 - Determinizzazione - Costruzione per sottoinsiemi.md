# Determinizzazione - Costruzione per sottoinsiemi

## 1. Obiettivo

La **determinizzazione** trasforma un automa finito non deterministico in un automa deterministico equivalente.

Dato un NFA $M$, vogliamo costruire un DFA $D$ tale che:

$$
L(D)=L(M)
$$

L’idea è conservare in un solo stato del DFA tutti gli stati in cui l’NFA potrebbe trovarsi dopo aver letto un prefisso.

Per questo il procedimento si chiama **costruzione per sottoinsiemi**.

> [!important]
> Uno stato del nuovo DFA rappresenta un insieme di stati dell’NFA.
>
> L’insieme viene trattato come un unico stato, non come una scelta tra più destinazioni.

## 2. L’idea fondamentale

Supponiamo che dopo un prefisso l’NFA possa trovarsi negli stati:

$$
R=\{q_1,q_2\}
$$

Leggendo `a`, dobbiamo considerare le transizioni da entrambi:

$$
\delta_N(q_1,a)\cup\delta_N(q_2,a)
$$

Se il risultato è:

$$
T=\{q_0,q_2\}
$$

nel DFA inseriamo una sola transizione:

$$
R\xrightarrow{a}T
$$

Il fatto che $T$ contenga due stati originali non rende non deterministico il nuovo automa: la destinazione è un unico sottoinsieme ben determinato.

## 3. Costruzione formale per un NFA senza epsilon-mosse

Sia:

$$
M=(Q,\Sigma,\delta_N,q_0,F)
$$

con:

$$
\delta_N:Q\times\Sigma\to\mathcal P(Q)
$$

Costruiamo:

$$
D=(Q_D,\Sigma,\delta_D,R_0,F_D)
$$

### Stati

Ogni stato del DFA è un sottoinsieme di $Q$:

$$
Q_D\subseteq\mathcal P(Q)
$$

Possiamo utilizzare tutto $\mathcal P(Q)$, ma nella costruzione pratica generiamo soltanto i sottoinsiemi raggiungibili.

### Stato iniziale

$$
R_0=\{q_0\}
$$

Prima della lettura, l’NFA senza epsilon-mosse si trova soltanto nel proprio stato iniziale.

### Funzione di transizione

Per ogni sottoinsieme $R$ e simbolo $a$:

$$
\boxed{
\delta_D(R,a)
=
\bigcup_{q\in R}\delta_N(q,a)
}
$$

Il risultato è il sottoinsieme che rappresenta la destinazione del DFA.

### Stati finali

$$
\boxed{
F_D=
\{R\in Q_D\mid R\cap F\neq\varnothing\}
}
$$

Un sottoinsieme è finale se contiene almeno uno stato finale dell’NFA.

Per esempio, se:

$$
F=\{q_2\}
$$

allora:

- $\{q_0,q_2\}$ è finale;
- $\{q_0,q_1\}$ non è finale.

Non è necessario che tutti gli elementi del sottoinsieme siano finali.

## 4. Il sottoinsieme vuoto

Può accadere che:

$$
\delta_D(R,a)=\varnothing
$$

Questo significa che l’NFA non ha alcun cammino capace di proseguire su quel simbolo.

Nel DFA, però, $\varnothing$ può essere utilizzato come uno stato.

Per ogni $a\in\Sigma$:

$$
\delta_D(\varnothing,a)
=
\bigcup_{q\in\varnothing}\delta_N(q,a)
=
\varnothing
$$

Quindi lo stato vuoto:

- non è finale;
- torna su se stesso per ogni simbolo;
- svolge il ruolo di stato pozzo.

Possiamo indicarlo anche con:

$$
\bot
$$

mantenendo la corrispondenza:

$$
\bot=\varnothing
$$

> [!important]
> Nell’NFA, $\varnothing$ come risultato di una transizione significa “nessuna destinazione”.
>
> Nel DFA costruito per sottoinsiemi, $\varnothing$ può diventare il nome di uno stato che rappresenta proprio questa situazione.

Se il sottoinsieme vuoto non viene mai raggiunto, non è necessario inserirlo nella costruzione dei soli stati raggiungibili.

## 5. Procedura pratica

Per determinizzare un NFA senza epsilon-mosse:

1. Inserisci il sottoinsieme iniziale $\{q_0\}$.
2. Scegli un sottoinsieme non ancora elaborato.
3. Per ogni simbolo dell’alfabeto, unisci le transizioni degli stati che contiene.
4. Se compare un sottoinsieme nuovo, aggiungilo alla tabella.
5. Continua finché tutti i sottoinsiemi scoperti hanno una transizione per ogni simbolo.
6. Segna come finali quelli che contengono almeno un finale dell’NFA.
7. Se vuoi, assegna nomi brevi ai sottoinsiemi e disegna il DFA.

### Riconoscere insiemi già scoperti

L’ordine degli elementi non conta:

$$
\{q_1,q_2\}=\{q_2,q_1\}
$$

Le ripetizioni non contano:

$$
\{q_1,q_1,q_2\}=\{q_1,q_2\}
$$

Due sottoinsiemi sono lo stesso stato se contengono esattamente gli stessi elementi.

## 6. Esempio completo

Riprendiamo l’NFA ottenuto nella nota sull’eliminazione delle epsilon-mosse.

Lo stato iniziale è $q_0$ e tutti gli stati sono finali:

$$
F=\{q_0,q_1,q_2\}
$$

La tabella è:

| Stato | a | b |
|---|---|---|
| $q_0$ | $\{q_0,q_1,q_2\}$ | $\{q_1,q_2\}$ |
| $q_1$ | $\varnothing$ | $\{q_1,q_2\}$ |
| $q_2$ | $\varnothing$ | $\varnothing$ |

Il linguaggio è:

$$
a^*b^*
$$

### Passaggio 1: stato iniziale

Partiamo da:

$$
A=\{q_0\}
$$

Su `a`:

$$
\delta_D(A,a)=\{q_0,q_1,q_2\}
$$

Chiamiamo questo nuovo stato:

$$
B=\{q_0,q_1,q_2\}
$$

Su `b`:

$$
\delta_D(A,b)=\{q_1,q_2\}
$$

Chiamiamo:

$$
C=\{q_1,q_2\}
$$

### Passaggio 2: elaborare B

Su `a`:

$$
\delta_D(B,a)
=
\delta_N(q_0,a)
\cup
\delta_N(q_1,a)
\cup
\delta_N(q_2,a)
$$

$$
=
\{q_0,q_1,q_2\}
=
B
$$

Su `b`:

$$
\delta_D(B,b)
=
\{q_1,q_2\}
=
C
$$

### Passaggio 3: elaborare C

Su `a`:

$$
\delta_D(C,a)
=
\delta_N(q_1,a)\cup\delta_N(q_2,a)
=
\varnothing
$$

Introduciamo:

$$
\bot=\varnothing
$$

Su `b`:

$$
\delta_D(C,b)
=
\{q_1,q_2\}
=
C
$$

### Passaggio 4: elaborare il pozzo

$$
\delta_D(\bot,a)=\bot
$$

$$
\delta_D(\bot,b)=\bot
$$

Non vengono scoperti altri sottoinsiemi.

### Tabella finale

| Stato DFA | Sottoinsieme rappresentato | a | b | Finale? |
|---|---|---|---|---|
| $A$ | $\{q_0\}$ | $B$ | $C$ | Sì |
| $B$ | $\{q_0,q_1,q_2\}$ | $B$ | $C$ | Sì |
| $C$ | $\{q_1,q_2\}$ | $\bot$ | $C$ | Sì |
| $\bot$ | $\varnothing$ | $\bot$ | $\bot$ | No |

Lo stato iniziale è $A$.

### Verifiche

La stringa vuota è accettata perché $A$ è finale.

La stringa `aab` segue:

$$
A\xrightarrow{a}B
\xrightarrow{a}B
\xrightarrow{b}C
$$

ed è accettata.

La stringa `ba` segue:

$$
A\xrightarrow{b}C
\xrightarrow{a}\bot
$$

ed è rifiutata.

## 7. Determinizzazione diretta di un ε-NFA

È possibile determinizzare anche senza eliminare prima le epsilon-mosse, incorporando le epsilon-chiusure nella costruzione.

### Stato iniziale

$$
\boxed{
R_0=E(q_0)
}
$$

### Transizioni

$$
\boxed{
\delta_D(R,a)
=
E\left(
\bigcup_{q\in R}\delta_N(q,a)
\right)
}
$$

### Stati finali

$$
F_D=
\{R\in Q_D\mid R\cap F\neq\varnothing\}
$$

I sottoinsiemi raggiunti sono già epsilon-chiusi.

### Confronto tra i due casi

| Automa di partenza | Stato iniziale del DFA | Transizione |
|---|---|---|
| NFA senza epsilon-mosse | $\{q_0\}$ | Unione delle transizioni |
| ε-NFA | $E(q_0)$ | Unione delle transizioni e chiusura finale |

> [!warning]
> Se hai già eliminato le epsilon-mosse, devi determinizzare l’automa risultante usando le sue nuove transizioni e i suoi nuovi stati finali.
>
> Non usare le transizioni dell’automa modificato insieme ai finali dell’automa originale.

Se la traccia richiede esplicitamente le fasi separate, presenta prima l’eliminazione delle epsilon-mosse e poi la determinizzazione.

## 8. Perché la costruzione funziona

L’invariante fondamentale è:

> Dopo ogni prefisso, lo stato del DFA è esattamente l’insieme degli stati raggiungibili nell’NFA dopo quel prefisso.

Per un NFA senza epsilon-mosse:

$$
\delta_D^*(\{q_0\},w)
=
\delta_N^*(q_0,w)
$$

A sinistra compare un unico stato del DFA, il cui valore è un sottoinsieme di $Q$.

A destra compare l’insieme degli stati raggiungibili nell’NFA.

### Caso iniziale

Per $w=\varepsilon$, entrambi corrispondono a:

$$
\{q_0\}
$$

### Passo di lettura

La funzione $\delta_D$ unisce esattamente le destinazioni possibili dell’NFA sul nuovo simbolo.

L’invariante viene quindi mantenuto.

### Accettazione

Il DFA accetta quando il sottoinsieme raggiunto contiene almeno un finale dell’NFA.

Questo è precisamente il criterio di accettazione non deterministico.

Quindi:

$$
L(D)=L(M)
$$

Nel caso con epsilon-mosse, lo stesso ragionamento utilizza le epsilon-chiusure.

## 9. Numero degli stati

Se l’NFA ha $n$ stati, i sottoinsiemi possibili sono:

$$
2^n
$$

Il DFA ottenuto ha quindi al massimo:

$$
|Q_D|\leq 2^n
$$

Non tutti i sottoinsiemi devono essere raggiungibili.

Nell’esempio, con tre stati originali avremmo otto sottoinsiemi possibili, ma ne vengono raggiunti soltanto quattro.

Non è necessario elencare in anticipo tutti gli elementi dell’insieme delle parti.

## 10. Determinizzazione e minimizzazione

La determinizzazione non garantisce il minimo numero di stati.

Nell’esempio:

$$
A=\{q_0\}
$$

e:

$$
B=\{q_0,q_1,q_2\}
$$

sono sottoinsiemi diversi e vengono quindi mantenuti distinti durante la costruzione.

Tuttavia:

- entrambi sono finali;
- entrambi vanno in $B$ su `a`;
- entrambi vanno in $C$ su `b`.

La minimizzazione potrà riconoscerli come equivalenti e unirli.

> [!important]
> Durante la costruzione per sottoinsiemi identifichi stati con lo stesso insieme di elementi.
>
> Durante la minimizzazione identifichi stati con lo stesso comportamento rispetto alle letture future.

## 11. Controlli da fare all’esame

- Ogni stato del DFA deve avere una corrispondenza chiara con un sottoinsieme.
- Ogni riga deve avere una destinazione per ciascun simbolo dell’alfabeto.
- Ogni sottoinsieme nuovo deve essere elaborato.
- I finali devono essere scelti con il criterio dell’intersezione non vuota.
- Il sottoinsieme vuoto, se raggiunto, deve essere gestito.
- Il criterio iniziale deve essere coerente con la presenza o assenza di epsilon-mosse.
- La stringa vuota deve mantenere lo stesso esito.

Non fermarti quando trovi il primo stato finale: la costruzione termina quando tutte le righe scoperte sono complete.

## 12. Errori da evitare

- Scegliere una sola destinazione dell’NFA.
- Trattare gli elementi di un sottoinsieme come più destinazioni del DFA.
- Richiedere che tutti gli elementi di uno stato siano finali.
- Dimenticare di elaborare un sottoinsieme appena scoperto.
- Creare due stati per insiemi che differiscono soltanto nell’ordine.
- Ignorare il sottoinsieme vuoto lasciando transizioni mancanti.
- Usare $\{q_0\}$ come iniziale nella determinizzazione diretta di un ε-NFA.
- Usare $E(q_0)$ automaticamente dopo aver già eliminato le epsilon-mosse.
- Mescolare transizioni e finali appartenenti a fasi diverse.
- Unire sottoinsiemi diversi senza aver verificato l’equivalenza tramite minimizzazione.