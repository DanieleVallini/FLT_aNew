# Conversione da automa a grammatica lineare destra

## 1. Obiettivo

Dato un automa finito $M$, vogliamo costruire una grammatica lineare destra $G$ tale che:

$$
L(G)=L(M)
$$

La corrispondenza fondamentale è:

- stati dell’automa → non terminali;
- transizioni → produzioni;
- stato iniziale → simbolo iniziale;
- stati finali → possibilità di terminare la derivazione.

La costruzione funziona anche su automi non deterministici e con epsilon-mosse.

Non richiede che l’automa sia completo.

> [!important]
> La conversione è meccanica: si può costruire la grammatica direttamente dalla tabella o dal diagramma dell’automa.

## 2. Costruzione formale

Sia:

$$
M=(Q,\Sigma,\delta,q_0,F)
$$

un automa finito, eventualmente con epsilon-mosse.

Costruiamo:

$$
G=(N,\Sigma,P,S)
$$

### Non terminali

A ogni stato $q\in Q$ associamo un non terminale distinto:

$$
A_q
$$

Quindi:

$$
N=\{A_q\mid q\in Q\}
$$

I nomi sono arbitrari: possiamo usare anche $S,A,B,\ldots$, purché manteniamo una corrispondenza chiara.

### Simbolo iniziale

$$
S=A_{q_0}
$$

### Alfabeto terminale

L’alfabeto della grammatica è lo stesso dell’automa:

$$
\Sigma_G=\Sigma_M
$$

$\varepsilon$ non viene inserito nell’alfabeto.

## 3. Regole di conversione

### Transizione su un terminale

Per ogni:

$$
q\xrightarrow{a}p
$$

inseriamo:

$$
A_q\to aA_p
$$

La produzione genera il simbolo letto e continua dal non terminale associato alla destinazione.

### Epsilon-transizione

Per ogni:

$$
q\xrightarrow{\varepsilon}p
$$

inseriamo:

$$
A_q\to A_p
$$

Questa è una **produzione unitaria**: cambia non terminale senza generare terminali.

### Stato finale

Per ogni:

$$
q\in F
$$

inseriamo:

$$
A_q\to\varepsilon
$$

La produzione permette di terminare quando il cammino dell’automa si trova in uno stato accettante.

### Tabella riassuntiva

| Elemento dell’automa | Elemento della grammatica |
|---|---|
| Stato $q$ | Non terminale $A_q$ |
| Stato iniziale $q_0$ | Simbolo iniziale $A_{q_0}$ |
| Arco $q\xrightarrow{a}p$ | $A_q\to aA_p$ |
| Arco $q\xrightarrow{\varepsilon}p$ | $A_q\to A_p$ |
| Stato finale $q$ | $A_q\to\varepsilon$ |

> [!warning]
> Un arco epsilon e uno stato finale producono regole diverse:
>
> - l’arco epsilon permette di cambiare stato senza leggere;
> - lo stato finale permette di terminare.
>
> Non tradurre automaticamente un arco epsilon con una produzione verso $\varepsilon$.

## 4. Non determinismo e archi con più etichette

Se:

$$
\delta(q,a)=\{p,r\}
$$

inseriamo entrambe le produzioni:

$$
A_q\to aA_p
$$

$$
A_q\to aA_r
$$

La grammatica può avere più alternative che iniziano con lo stesso terminale.

Se un arco è etichettato:

$$
a\mid b
$$

inseriamo due produzioni, una per ciascun simbolo.

Se l’etichetta è:

$$
b\mid\varepsilon
$$

inseriamo:

$$
A_q\to bA_p
$$

e:

$$
A_q\to A_p
$$

## 5. Esempio d’esame: febbraio 2011

L’automa ha:

- stato iniziale $q_0$;
- stati finali $q_0$ e $q_2$;
- alfabeto $\Sigma=\{a,b\}$.

La tabella delle transizioni è:

| Stato | a | b | ε |
|---|---|---|---|
| $q_0$ | $\{q_0\}$ | $\{q_1\}$ | $\{q_1\}$ |
| $q_1$ | $\{q_1\}$ | $\{q_2\}$ | $\varnothing$ |
| $q_2$ | $\{q_1,q_2\}$ | $\varnothing$ | $\varnothing$ |

### Passaggio 1: associare i non terminali

Scegliamo:

| Stato | Non terminale |
|---|---|
| $q_0$ | $S$ |
| $q_1$ | $A$ |
| $q_2$ | $B$ |

Il simbolo iniziale è $S$.

### Passaggio 2: tradurre le transizioni di q₀

Dall’autoanello su `a`:

$$
S\to aS
$$

Dalla transizione su `b` verso $q_1$:

$$
S\to bA
$$

Dalla epsilon-transizione verso $q_1$:

$$
S\to A
$$

### Passaggio 3: tradurre le transizioni di q₁

Dall’autoanello su `a`:

$$
A\to aA
$$

Dalla transizione su `b` verso $q_2$:

$$
A\to bB
$$

### Passaggio 4: tradurre le transizioni di q₂

Su `a` sono possibili due destinazioni:

$$
B\to aA
$$

$$
B\to aB
$$

### Passaggio 5: aggiungere la terminazione

Poiché $q_0$ e $q_2$ sono finali:

$$
S\to\varepsilon
$$

$$
B\to\varepsilon
$$

### Grammatica risultante

$$
G=(\{S,A,B\},\{a,b\},P,S)
$$

con:

$$
\boxed{
S\to aS\mid bA\mid A\mid\varepsilon
}
$$

$$
\boxed{
A\to aA\mid bB
}
$$

$$
\boxed{
B\to aA\mid aB\mid\varepsilon
}
$$

È una grammatica lineare destra nella forma estesa, che ammette anche produzioni unitarie.

## 6. Verifica della conversione

### Stringa vuota

L’automa accetta $\varepsilon$ perché lo stato iniziale è finale.

La grammatica permette:

$$
S\Rightarrow\varepsilon
$$

### Stringa b

Nell’automa esiste il cammino:

$$
q_0\xrightarrow{\varepsilon}q_1
\xrightarrow{b}q_2
$$

La grammatica lo rappresenta con:

$$
S\Rightarrow A
\Rightarrow bB
\Rightarrow b
$$

Questo esempio mostra perché la produzione $S\to A$ è necessaria.

### Stringa bb

Nell’automa:

$$
q_0\xrightarrow{b}q_1
\xrightarrow{b}q_2
$$

Nella grammatica:

$$
S\Rightarrow bA
\Rightarrow bbB
\Rightarrow bb
$$

### Non eliminare semplicemente le produzioni unitarie

Se cancellassimo $S\to A$ senza sostituirne il comportamento, perderemmo la possibilità di generare `b`.

Eliminare una produzione unitaria richiede una trasformazione che conservi il linguaggio.

## 7. Ottenere una grammatica senza produzioni unitarie

Se viene richiesta la forma base:

$$
A\to aB
\qquad
A\to a
\qquad
A\to\varepsilon
$$

possiamo eliminare le produzioni unitarie dopo la conversione.

### Chiusura unitaria

Per ogni non terminale $X$, consideriamo i non terminali raggiungibili da $X$ usando soltanto produzioni unitarie.

Indichiamo questo insieme con:

$$
U(X)
$$

Include sempre $X$ stesso.

Per ogni $Y\in U(X)$, copiamo in $X$ tutte le produzioni non unitarie di $Y$, comprese quelle verso $\varepsilon$.

Poi eliminiamo le produzioni unitarie.

### Applicazione all’esempio

L’unica produzione unitaria è:

$$
S\to A
$$

Quindi:

$$
U(S)=\{S,A\}
$$

Copiamo in $S$ le produzioni:

$$
A\to aA\mid bB
$$

ed eliminiamo $S\to A$.

Otteniamo:

$$
\boxed{
S\to aS\mid bA\mid aA\mid bB\mid\varepsilon
}
$$

$$
\boxed{
A\to aA\mid bB
}
$$

$$
\boxed{
B\to aA\mid aB\mid\varepsilon
}
$$

Il linguaggio è invariato.

Per esempio, `b` viene ora generata direttamente attraverso:

$$
S\Rightarrow bB\Rightarrow b
$$

Un’altra possibilità è eliminare prima le epsilon-mosse dall’automa e poi convertirlo.

## 8. Perché la costruzione conserva il linguaggio

### Da cammino a derivazione

Ogni transizione:

$$
q\xrightarrow{a}p
$$

corrisponde a:

$$
A_q\Rightarrow aA_p
$$

Ogni epsilon-transizione corrisponde a una produzione unitaria.

Quindi un cammino dall’iniziale a uno stato $q$, con etichetta complessiva $w$, permette una derivazione:

$$
S\Rightarrow^*wA_q
$$

Se $q$ è finale, possiamo terminare:

$$
wA_q\Rightarrow w
$$

Ogni stringa accettata dall’automa viene dunque generata.

### Da derivazione a cammino

Ogni produzione che genera un terminale deriva da una transizione dell’automa.

Le produzioni unitarie derivano da epsilon-transizioni.

La terminazione è possibile soltanto attraverso un non terminale associato a uno stato finale.

Ogni frase generata corrisponde quindi a un cammino accettante.

Concludiamo:

$$
L(G)=L(M)
$$

## 9. Stati finali con transizioni uscenti

Uno stato finale può sia terminare sia continuare la lettura.

Per esempio, un unico stato iniziale e finale con autoanello su `a` produce:

$$
S\to aS\mid\varepsilon
$$

e genera:

$$
a^*
$$

Non bisogna sostituire ogni transizione verso un finale con una produzione che termina immediatamente.

La regola generale resta:

$$
A_q\to aA_p
$$

insieme a:

$$
A_p\to\varepsilon
$$

quando $p$ è finale.

In questo modo rimangono possibili anche le continuazioni da $p$.

## 10. Transizioni mancanti e stato pozzo

Una transizione mancante non produce alcuna regola.

Non è necessario completare l’automa per convertirlo in grammatica.

### Pozzo non finale

Se è presente un pozzo non finale $p$ su $\Sigma=\{a,b\}$, otteniamo:

$$
P\to aP\mid bP
$$

Senza una possibilità di terminare, da $P$ non viene generata alcuna stringa terminale.

Questo rappresenta correttamente il rifiuto.

### Pozzo finale

Se il pozzo è finale, come può accadere dopo il complemento, aggiungiamo:

$$
P\to\varepsilon
$$

Da quel non terminale vengono allora generate tutte le continuazioni su $\{a,b\}$.

> [!warning]
> Non aggiungere una produzione verso $\varepsilon$ soltanto perché un non terminale sembra non riuscire a terminare.
>
> La terminazione deve corrispondere a un’accettazione ammessa dall’automa.

## 11. Procedura da applicare all’esame

1. Individua alfabeto, iniziale e finali.
2. Associa un non terminale a ogni stato.
3. Indica il simbolo iniziale.
4. Traduci ogni transizione su terminale.
5. Traduci ogni epsilon-transizione.
6. Aggiungi una produzione verso $\varepsilon$ per ogni finale.
7. Raggruppa le alternative con la stessa parte sinistra.
8. Se necessario, elimina le produzioni unitarie.
9. Verifica $\varepsilon$ e alcune stringhe brevi.

Presenta chiaramente:

$$
G=(N,\Sigma,P,S)
$$

La corrispondenza tra stati e non terminali rende la costruzione controllabile.

## 12. Errori da evitare

- Invertire sorgente e destinazione di una transizione.
- Dimenticare una destinazione non deterministica.
- Tradurre un arco con più etichette usando una sola produzione.
- Confondere epsilon-transizioni e stati finali.
- Dimenticare la produzione verso $\varepsilon$ per un finale.
- Aggiungere terminazioni a stati non finali senza una trasformazione giustificata.
- Cancellare produzioni unitarie senza copiarne le possibilità di generazione.
- Forzare la terminazione appena si raggiunge un finale.
- Completare o determinizzare come requisito obbligatorio per questa conversione.
- Confondere il nome di uno stato con un terminale del linguaggio.