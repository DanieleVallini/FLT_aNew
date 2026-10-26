# Costruzione dell’automa per il complemento

## 1. Obiettivo e alfabeto di riferimento

Dato un linguaggio:

$$
L\subseteq\Sigma^*
$$

il suo complemento rispetto a $\Sigma^*$ è:

$$
\overline L=\Sigma^*\setminus L
$$

Vogliamo costruire un automa che accetti esattamente le stringhe rifiutate dall’automa originale.

Il complemento dipende dall’alfabeto.

Per esempio, se $L=\{a\}$:

- rispetto a $\Sigma=\{a\}$, le stringhe considerate contengono soltanto `a`;
- rispetto a $\Sigma=\{a,b\}$, anche tutte le stringhe contenenti `b` appartengono al complemento.

> [!important]
> Prima di costruire il complemento, fissa sempre l’alfabeto.

## 2. Costruzione per un DFA completo

Sia:

$$
D=(Q,\Sigma,\delta,q_0,F)
$$

un automa deterministico completo.

L’automa del complemento è:

$$
\boxed{
\overline D=
(Q,\Sigma,\delta,q_0,Q\setminus F)
}
$$

Manteniamo:

- gli stessi stati;
- lo stesso alfabeto;
- le stesse transizioni;
- lo stesso stato iniziale.

Cambiamo soltanto l’insieme degli stati finali.

### Regola grafica

- Gli stati finali diventano non finali.
- Gli stati non finali diventano finali.
- La freccia iniziale e gli archi restano invariati.

### Dimostrazione

Per ogni $w\in\Sigma^*$, il determinismo e la completezza garantiscono un unico stato raggiunto:

$$
q=\delta^*(q_0,w)
$$

Nell’automa originale:

$$
w\in L(D)\iff q\in F
$$

Nel complemento:

$$
w\in L(\overline D)\iff q\notin F
$$

Quindi:

$$
w\in L(\overline D)
\iff
w\notin L(D)
$$

e pertanto:

$$
L(\overline D)=\Sigma^*\setminus L(D)
$$

## 3. Se l’automa di partenza non è un DFA completo

Lo scambio dei finali deve essere preceduto dalle trasformazioni necessarie.

### Procedimento generale

1. Gestisci le eventuali epsilon-mosse.
2. Determinizza l’automa, se necessario.
3. Completa le transizioni rispetto all’alfabeto.
4. Scambia finali e non finali.

Puoi eliminare prima le epsilon-mosse oppure determinizzare direttamente usando le epsilon-chiusure.

### Se l’automa è già deterministico

Non serve applicare la costruzione per sottoinsiemi.

Controlla soltanto la completezza prima dello scambio.

### Se manca una transizione

Introduci, quando necessario, un pozzo non finale:

$$
\bot
$$

Indirizza verso di esso le transizioni mancanti e aggiungi:

$$
\delta(\bot,a)=\bot
\qquad
\forall a\in\Sigma
$$

Dopo lo scambio dei finali, il pozzo diventa finale.

> [!warning]
> Se scambi i finali lasciando transizioni mancanti, alcune stringhe prima rifiutate continuano a non avere alcun cammino e non vengono accettate.
>
> Il complemento sarebbe quindi incompleto come descrizione del linguaggio richiesto.

## 4. Esempio d’esame: giugno 2025

L’automa del primo esercizio ha:

$$
Q=\{q_0,q_1,q_2\}
$$

$$
\Sigma=\{a,b,c\}
$$

stato iniziale $q_0$ e:

$$
F=\{q_0,q_2\}
$$

Le transizioni sono:

| Stato | a | b | c |
|---|---|---|---|
| $q_0$ | $q_1$ | $q_1$ | Non definita |
| $q_1$ | Non definita | $q_1$ | $q_2$ |
| $q_2$ | $q_0$ | Non definita | Non definita |

### Passaggio 1: verificare il determinismo

Per ogni coppia stato-simbolo è presente al massimo una destinazione.

Non ci sono epsilon-mosse.

L’automa è deterministico, ma è rappresentato in forma parziale.

Non serve determinizzarlo.

### Passaggio 2: completare

Introduciamo un pozzo non finale $\bot$.

Le transizioni mancanti diventano:

$$
\delta(q_0,c)=\bot
$$

$$
\delta(q_1,a)=\bot
$$

$$
\delta(q_2,b)=\bot
$$

$$
\delta(q_2,c)=\bot
$$

Aggiungiamo inoltre:

$$
\delta(\bot,a)=
\delta(\bot,b)=
\delta(\bot,c)=\bot
$$

Prima dello scambio, gli stati finali rimangono:

$$
F=\{q_0,q_2\}
$$

### Passaggio 3: scambiare i finali

Il nuovo insieme degli stati è:

$$
Q'=\{q_0,q_1,q_2,\bot\}
$$

Quindi:

$$
F_{\mathrm{comp}}
=
Q'\setminus F
=
\{q_1,\bot\}
$$

### Tabella dell’automa del complemento

| Stato | a | b | c | Finale? |
|---|---|---|---|---|
| $q_0$ | $q_1$ | $q_1$ | $\bot$ | No |
| $q_1$ | $\bot$ | $q_1$ | $q_2$ | Sì |
| $q_2$ | $q_0$ | $\bot$ | $\bot$ | No |
| $\bot$ | $\bot$ | $\bot$ | $\bot$ | Sì |

Lo stato iniziale resta $q_0$.

Nel disegno:

- $q_1$ e $\bot$ hanno il doppio cerchio;
- $q_0$ e $q_2$ hanno il cerchio semplice;
- il pozzo ha un autoanello con etichetta $a\mid b\mid c$.

## 5. Controllare il risultato dell’esempio

Confrontiamo il linguaggio originale e il complemento.

| Stringa | Stato raggiunto nell’automa completato | Originale | Complemento |
|---|---|---|---|
| $\varepsilon$ | $q_0$ | Accetta | Rifiuta |
| `a` | $q_1$ | Rifiuta | Accetta |
| `c` | $\bot$ | Rifiuta | Accetta |
| `ac` | $q_2$ | Accetta | Rifiuta |
| `acb` | $\bot$ | Rifiuta | Accetta |

### Perché c è un controllo importante

Nell’automa originale mancava la transizione da $q_0$ su `c`.

La stringa veniva quindi rifiutata.

Nel complemento deve essere accettata:

$$
q_0\xrightarrow{c}\bot
$$

con $\bot$ finale.

Questo controllo permette di accorgersi subito dell’errore di scambiare i finali senza completare.

### Stringa vuota

L’originale accetta $\varepsilon$ perché $q_0$ è finale.

Nel complemento $q_0$ non è finale, quindi $\varepsilon$ viene correttamente rifiutata.

## 6. Perché non si scambiano direttamente i finali di un NFA

Un NFA accetta una stringa se esiste almeno un cammino accettante.

Scambiare i finali può lasciare accettata una stringa che era già accettata.

### Controesempio

Consideriamo:

- $q_0$ iniziale e non finale;
- $q_1$ finale;
- $\delta(q_0,a)=\{q_0,q_1\}$;
- $\delta(q_1,a)=\{q_1\}$.

La stringa `a` è accettata tramite:

$$
q_0\xrightarrow{a}q_1
$$

Se scambiamo i finali, $q_0$ diventa finale.

La stessa stringa è ancora accettata tramite:

$$
q_0\xrightarrow{a}q_0
$$

Ma una stringa non può appartenere sia a un linguaggio sia al suo complemento.

### Il problema logico

L’accettazione originale richiede:

> Esiste un cammino che termina in un finale.

Il rifiuto richiede che nessun cammino sia accettante.

Scambiando i finali otteniamo invece:

> Esiste un cammino che termina in un vecchio stato non finale.

Questa non è la negazione della condizione originale.

La determinizzazione permette di rappresentare tutti i cammini possibili in un solo stato, rendendo corretto lo scambio.

## 7. Complemento e minimizzazione

Non è necessario minimizzare un DFA per costruirne il complemento.

Servono determinismo e completezza.

### Se il DFA è già minimo

Se un DFA completo è minimo, lo scambio dei finali produce ancora un DFA minimo.

Infatti un suffisso che distingueva due stati continua a distinguerli: si scambiano soltanto gli esiti di accettazione.

Le equivalenze tra stati non cambiano, e nemmeno la raggiungibilità.

### Verifica nell’esempio d’esame

I quattro stati sono raggiungibili:

- $q_0$ con $\varepsilon$;
- $q_1$ con `a`;
- $q_2$ con `ac`;
- $\bot$ con `c`.

Nel complemento:

- $q_0$ e $q_2$ sono distinguibili con `a`;
- $q_1$ e $\bot$ sono distinguibili con `c`;
- ogni coppia formata da un finale e un non finale è distinguibile con $\varepsilon$.

Il risultato è quindi già minimo.

Questa verifica è aggiuntiva: per costruire il complemento non era necessario svolgere prima una minimizzazione.

## 8. Casi limite e controlli generali

Rispetto allo stesso alfabeto:

$$
\overline{\varnothing}=\Sigma^*
$$

$$
\overline{\Sigma^*}=\varnothing
$$

$$
\overline{\overline L}=L
$$

Inoltre:

$$
L\cap\overline L=\varnothing
$$

$$
L\cup\overline L=\Sigma^*
$$

Per ogni stringa, esattamente uno tra originale e complemento deve accettare.

> [!tip]
> Per controllare un esercizio, prova:
>
> - la stringa vuota;
> - una stringa accettata dall’originale;
> - una stringa rifiutata;
> - una stringa che nell’automa parziale incontrava una transizione mancante.

## 9. Procedura da presentare all’esame

Una soluzione chiara deve indicare:

1. l’alfabeto;
2. se l’automa è deterministico;
3. le eventuali trasformazioni preliminari;
4. il completamento delle transizioni;
5. il nuovo insieme degli stati finali;
6. il diagramma o la tabella conclusiva.

La motivazione essenziale è:

> In un DFA completo ogni stringa conduce a un unico stato. Scambiando finali e non finali vengono accettate esattamente le stringhe prima rifiutate.

## 10. Errori da evitare

- Scambiare direttamente i finali di un NFA.
- Ignorare le epsilon-mosse.
- Dimenticare il completamento.
- Lasciare non finale il pozzo dopo lo scambio.
- Cambiare lo stato iniziale.
- Invertire gli archi: questa operazione riguarda il rovesciamento, non il complemento.
- Calcolare il complemento senza dichiarare l’alfabeto.
- Confondere “non finale” con “irraggiungibile”.
- Aggiungere una minimizzazione come requisito per lo scambio dei finali.
- Controllare soltanto stringhe che avevano già un cammino completo nell’automa originale.