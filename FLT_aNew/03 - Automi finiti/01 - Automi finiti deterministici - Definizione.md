# Automi finiti deterministici - Definizione

## 1. Idea generale

Un **automa finito deterministico**, abbreviato **DFA**, è un modello di riconoscimento che:

- dispone di un insieme finito di stati;
- legge la stringa di ingresso un simbolo alla volta;
- aggiorna il proprio stato in base al simbolo letto;
- accetta o rifiuta la stringa in base allo stato raggiunto alla fine.

Il termine “deterministico” indica che lo stato corrente e il simbolo letto determinano un’unica destinazione.

> [!important]
> Lo stato rappresenta l’informazione conservata dall’automa sul prefisso già letto.
>
> Non rappresenta necessariamente il numero di simboli letti.

## 2. Definizione formale

Un automa finito deterministico è una quintupla:

$$
M=(Q,\Sigma,\delta,q_0,F)
$$

dove:

- $Q$ è un insieme finito e non vuoto di stati;
- $\Sigma$ è un alfabeto finito;
- $\delta$ è la funzione di transizione;
- $q_0\in Q$ è lo stato iniziale;
- $F\subseteq Q$ è l’insieme degli stati finali, o accettanti.

Adottiamo la definizione di DFA completo, quindi:

$$
\delta:Q\times\Sigma\to Q
$$

è una funzione totale.

Questo significa che, per ogni:

$$
q\in Q,\qquad a\in\Sigma
$$

esiste esattamente uno stato:

$$
\delta(q,a)\in Q
$$

### Osservazioni

- Lo stato iniziale è unico.
- Gli stati finali possono essere più di uno.
- Lo stato iniziale può essere anche finale.
- È ammesso $F=\varnothing$.
- È ammesso $F=Q$.
- Non sono previste epsilon-mosse.

> [!warning]
> $\varepsilon$ è una stringa, non un simbolo dell’alfabeto.
>
> Nel DFA la funzione $\delta$ legge un simbolo alla volta: non è prevista una transizione $\delta(q,\varepsilon)$.

## 3. Funzione di transizione

La scrittura:

$$
\delta(q,a)=p
$$

significa:

> Se l’automa si trova nello stato $q$ e legge il simbolo $a$, passa nello stato $p$.

Graficamente:

$$
q\xrightarrow{a}p
$$

La funzione può essere descritta mediante:

- formule;
- una tabella;
- un diagramma degli stati.

Le tre rappresentazioni devono indicare le stesse transizioni.

### Un arco con più etichette

Un arco etichettato:

$$
a\mid b
$$

rappresenta due transizioni con la stessa destinazione.

Non è una singola lettura di due simboli: l’automa legge `a` oppure `b`.

## 4. Esempio: numero pari di a

Consideriamo il linguaggio:

$$
L=
\{w\in\{a,b\}^*
\mid
w\text{ contiene un numero pari di }a\}
$$

Possiamo riconoscerlo con:

$$
Q=\{q_0,q_1\}
$$

$$
\Sigma=\{a,b\}
$$

$$
F=\{q_0\}
$$

Lo stato iniziale è $q_0$.

La funzione di transizione è:

| Stato | a | b |
|---|---|---|
| $q_0$ | $q_1$ | $q_0$ |
| $q_1$ | $q_0$ | $q_1$ |

Il significato degli stati è:

- $q_0$: il numero di `a` lette finora è pari;
- $q_1$: il numero di `a` lette finora è dispari.

![[Allegati/FLT_07/flt_07_01_automa.svg|900]]

Leggere una `a` cambia la parità.

Leggere una `b` non modifica il numero di `a`, quindi lascia invariato lo stato.

### Lettura di abba

$$
q_0\xrightarrow{a}q_1
\xrightarrow{b}q_1
\xrightarrow{b}q_1
\xrightarrow{a}q_0
$$

L’automa termina in $q_0$, che è finale.

Quindi `abba` è accettata.

## 5. Dalla lettura di un simbolo alla lettura di una stringa

La funzione $\delta$ gestisce un solo simbolo.

Per descrivere l’effetto di un’intera stringa introduciamo la **funzione di transizione estesa**:

$$
\delta^*:Q\times\Sigma^*\to Q
$$

La definizione è:

$$
\delta^*(q,\varepsilon)=q
$$

e:

$$
\delta^*(q,wa)
=
\delta\bigl(\delta^*(q,w),a\bigr)
$$

per:

$$
q\in Q,\qquad w\in\Sigma^*,\qquad a\in\Sigma
$$

La prima regola dice che leggere la stringa vuota non cambia lo stato.

La seconda dice di leggere prima $w$ e poi l’ultimo simbolo $a$.

Per esempio, nell’automa della parità:

$$
\delta^*(q_0,abba)=q_0
$$

La funzione estesa sarà approfondita nella nota dedicata.

## 6. Accettazione e linguaggio riconosciuto

Una stringa $w$ è accettata se, dopo averla letta interamente, l’automa si trova in uno stato finale:

$$
w\text{ è accettata}
\iff
\delta^*(q_0,w)\in F
$$

Il linguaggio riconosciuto da $M$ è:

$$
L(M)=
\{w\in\Sigma^*
\mid
\delta^*(q_0,w)\in F\}
$$

### Esempi per l’automa della parità

| Stringa | Stato finale della lettura | Accettata? |
|---|---|---|
| $\varepsilon$ | $q_0$ | Sì |
| `b` | $q_0$ | Sì |
| `a` | $q_1$ | No |
| `abba` | $q_0$ | Sì |
| `abb` | $q_1$ | No |

### Stringa vuota

Poiché:

$$
\delta^*(q_0,\varepsilon)=q_0
$$

si ha:

$$
\varepsilon\in L(M)
\iff
q_0\in F
$$

Questo criterio vale per un DFA, che non dispone di epsilon-mosse.

### Stati finali e fine dell’ingresso

Uno stato finale non impone l’arresto immediato.

Se restano simboli da leggere, l’automa continua a eseguire transizioni.

Nell’esempio, $q_0$ è iniziale e finale, ma la stringa `a` viene rifiutata perché la lettura termina in $q_1$.

> [!important]
> Conta lo stato raggiunto dopo aver consumato tutta la stringa, non il fatto di essere passati prima attraverso uno stato finale.

## 7. Determinismo e completezza

Sono due aspetti da distinguere.

### Determinismo

Per ogni stato e simbolo non ci sono destinazioni alternative.

### Completezza

Per ogni stato e simbolo la transizione è definita.

Nella definizione adottata:

$$
\delta:Q\times\Sigma\to Q
$$

la funzione è totale, quindi ogni coppia ha esattamente una destinazione.

Un disegno con transizioni mancanti può rappresentare un automa deterministico parziale. Le transizioni mancanti non lo rendono automaticamente non deterministico.

### Completamento con stato pozzo

Per completare un automa parziale, interpretando una transizione mancante come rifiuto:

1. introduciamo un nuovo stato non finale $q_{\bot}$;
2. indirizziamo verso $q_{\bot}$ tutte le transizioni mancanti;
3. aggiungiamo:

$$
\delta(q_{\bot},a)=q_{\bot}
\qquad
\text{per ogni }a\in\Sigma
$$

Una volta raggiunto il pozzo, l’automa non può più accettare.

> [!tip] Collegamento con l’esame
> Prima di costruire il complemento scambiando finali e non finali, bisogna avere un automa deterministico completo.

## 8. Come dimostrare che un DFA riconosce il linguaggio richiesto

Un metodo efficace consiste nell’assegnare agli stati un significato preciso e dimostrare che viene mantenuto durante la lettura.

Questa proprietà è un **invariante**.

### Esempio della parità

L’invariante è:

- dopo un prefisso con un numero pari di `a`, lo stato è $q_0$;
- dopo un prefisso con un numero dispari di `a`, lo stato è $q_1$.

### Caso iniziale

Prima di leggere simboli:

- il numero di `a` è zero, quindi pari;
- lo stato è $q_0$.

### Passo di lettura

Se leggiamo `b`, la parità non cambia e l’automa rimane nello stesso stato.

Se leggiamo `a`, la parità cambia e l’automa passa all’altro stato.

L’invariante è quindi mantenuto.

Poiché l’unico stato finale è $q_0$, l’automa accetta esattamente le stringhe con un numero pari di `a`.

> [!important]
> Controllare alcune stringhe è utile per trovare errori.
>
> L’invariante spiega invece perché la costruzione funziona per tutte le stringhe.

## 9. Un numero finito di stati non limita la lunghezza delle stringhe

Un DFA può riconoscere stringhe arbitrariamente lunghe e linguaggi infiniti.

Può farlo perché durante la lettura torna più volte negli stessi stati.

Nell’automa della parità non serve ricordare il numero esatto delle `a`: basta ricordare se è pari o dispari.

Questo distingue:

- la quantità di informazione conservata;
- la lunghezza dell’ingresso.

La prima è limitata dagli stati; la seconda non deve essere limitata.

## 10. Definizione da saper dare all’esame

Un automa finito deterministico completo è una quintupla:

$$
M=(Q,\Sigma,\delta,q_0,F)
$$

dove:

- $Q$ è un insieme finito e non vuoto di stati;
- $\Sigma$ è un alfabeto finito;
- $\delta:Q\times\Sigma\to Q$ è una funzione totale;
- $q_0\in Q$ è lo stato iniziale;
- $F\subseteq Q$ è l’insieme degli stati finali.

Il linguaggio riconosciuto è:

$$
L(M)=
\{w\in\Sigma^*
\mid
\delta^*(q_0,w)\in F\}
$$

Devi inoltre saper spiegare che il determinismo garantisce una sola destinazione per ogni stato e simbolo.

## 11. Errori da evitare

- Confondere uno stato con un insieme di possibili stati.
- Ammettere più destinazioni sullo stesso simbolo in un DFA.
- Considerare una transizione mancante come una scelta non deterministica.
- Dimenticare che nella definizione completa $\delta$ è totale.
- Inserire epsilon-mosse nel modello deterministico qui definito.
- Pensare che gli stati finali non possano avere transizioni uscenti.
- Accettare prima di aver letto tutta la stringa.
- Confondere lo stato iniziale con uno stato necessariamente finale.
- Pensare che un automa con pochi stati possa leggere soltanto stringhe brevi.