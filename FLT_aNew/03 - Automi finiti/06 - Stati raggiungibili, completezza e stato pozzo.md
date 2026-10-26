# Stati raggiungibili, completezza e stato pozzo

## 1. Tre aspetti da distinguere

Quando analizziamo un automa, dobbiamo distinguere:

| Aspetto | Domanda |
|---|---|
| Raggiungibilità | Lo stato può essere raggiunto partendo dall’iniziale? |
| Completezza | Ogni stato ha una transizione per ogni simbolo dell’alfabeto? |
| Possibilità di accettazione | Dallo stato si può ancora raggiungere un finale? |

Queste proprietà non coincidono.

Uno stato può essere:

- raggiungibile ma incapace di portare all’accettazione;
- irraggiungibile anche se finale;
- non finale ma necessario per riconoscere il linguaggio.

## 2. Stati raggiungibili

Uno stato $q$ è **raggiungibile** se esiste una stringa che permette di arrivarvi partendo dallo stato iniziale.

Per un DFA:

$$
q\text{ è raggiungibile}
\iff
\exists w\in\Sigma^*:
\delta^*(q_0,w)=q
$$

L’insieme degli stati raggiungibili è:

$$
R=
\{\delta^*(q_0,w)\mid w\in\Sigma^*\}
$$

Lo stato iniziale è sempre raggiungibile, perché:

$$
\delta^*(q_0,\varepsilon)=q_0
$$

Per un automa non deterministico, la definizione richiede l’esistenza di almeno un cammino dall’iniziale allo stato considerato.

### Come calcolarli

1. Inserisci lo stato iniziale nell’insieme dei raggiungibili.
2. Segui tutte le transizioni uscenti dagli stati già trovati.
3. Aggiungi le destinazioni non ancora presenti.
4. Continua finché non vengono scoperti nuovi stati.

Puoi utilizzare una visita in ampiezza o in profondità.

Se l’automa ha epsilon-mosse, per la raggiungibilità generale si seguono anche quelle.

### Raggiungibilità ed epsilon-chiusura

| Calcolo | Transizioni da seguire |
|---|---|
| Epsilon-chiusura di uno stato | Soltanto epsilon-mosse |
| Stati raggiungibili dall’iniziale | Tutte le transizioni, comprese le eventuali epsilon-mosse |

> [!warning]
> Uno stato non appartiene necessariamente all’epsilon-chiusura dell’iniziale solo perché è raggiungibile: potrebbe essere necessario leggere dei simboli.

## 3. Eliminazione degli stati irraggiungibili

Gli stati irraggiungibili non possono comparire in alcuna lettura che parta dallo stato iniziale.

Possiamo quindi eliminarli senza modificare il linguaggio riconosciuto.

Per un DFA, restringiamo l’automa agli stati di $R$:

- manteniamo $q_0$;
- manteniamo le transizioni tra stati raggiungibili;
- sostituiamo $F$ con:

$$
F_R=F\cap R
$$

Il linguaggio rimane invariato.

### Perché non perdiamo transizioni utili

Se uno stato $p$ è raggiungibile e:

$$
\delta(p,a)=q
$$

allora anche $q$ è raggiungibile: basta leggere il prefisso che porta a $p$ e poi `a`.

Quindi, in un DFA completo, i raggiungibili sono chiusi rispetto alle transizioni.

Eliminare gli irraggiungibili conserva anche la completezza.

> [!important]
> Si possono eliminare anche stati finali, se irraggiungibili.
>
> Non si possono invece eliminare automaticamente tutti gli stati non finali.

## 4. Completezza

Un automa deterministico è **completo** se per ogni stato e simbolo esiste una transizione.

Formalmente:

$$
\delta:Q\times\Sigma\to Q
$$

è una funzione totale.

Per ogni:

$$
q\in Q,\qquad a\in\Sigma
$$

deve essere definita una destinazione.

### Determinismo e completezza

- Il determinismo esclude destinazioni alternative per la stessa coppia.
- La completezza esclude transizioni mancanti.

Un automa deterministico rappresentato in forma parziale può avere al massimo una destinazione per coppia, ma lasciare alcune coppie senza transizione.

### L’alfabeto conta

La completezza deve essere verificata rispetto all’alfabeto dichiarato.

Se:

$$
\Sigma=\{a,b,c\}
$$

non basta controllare le transizioni su `a` e `b`: devono essere presenti anche quelle su `c`.

## 5. Stato pozzo di rifiuto

Per completare un automa parziale possiamo introdurre uno stato:

$$
q_{\bot}
$$

con le seguenti proprietà:

1. non è finale;
2. riceve tutte le transizioni mancanti;
3. per ogni simbolo torna su se stesso:

$$
\delta(q_{\bot},a)=q_{\bot}
\qquad
\forall a\in\Sigma
$$

È uno **stato assorbente non finale**, utilizzato come stato pozzo di rifiuto.

Una volta raggiunto, nessuna continuazione può portare all’accettazione.

### Perché il completamento conserva il linguaggio

Interpretiamo una transizione mancante nell’automa parziale come impossibilità di proseguire, quindi rifiuto.

Nel completamento:

- le letture già definite restano uguali;
- una lettura che prima si bloccava entra nel pozzo;
- dal pozzo non si raggiungono finali.

Le stringhe accettate restano quindi le stesse.

### È sempre necessario aggiungerlo?

No.

Se tutte le transizioni sono già presenti, l’automa è completo.

Per esempio, il DFA della parità delle `a` è completo senza avere uno stato assorbente: su `a` passa continuamente da uno stato all’altro.

> [!important]
> Un DFA completo non deve necessariamente contenere un pozzo.
>
> Il pozzo serve quando occorre rappresentare esplicitamente letture che devono essere definitivamente rifiutate.

## 6. Esempio completo

Consideriamo un automa deterministico parziale su:

$$
\Sigma=\{a,b\}
$$

con stato iniziale $A$ e:

$$
F=\{A,B,U\}
$$

La tabella è:

| Stato | a | b |
|---|---|---|
| $A$ | $A$ | $B$ |
| $B$ | Non definita | $B$ |
| $U$ | $U$ | $U$ |

### Passaggio 1: raggiungibilità

Partiamo da:

$$
R_0=\{A\}
$$

Da $A$ possiamo raggiungere $A$ e $B$:

$$
R_1=\{A,B\}
$$

Da $B$ non raggiungiamo altri stati.

Quindi:

$$
R=\{A,B\}
$$

Lo stato $U$ è irraggiungibile, anche se finale.

Possiamo eliminarlo.

### Passaggio 2: controllo della completezza

Manca la transizione:

$$
\delta(B,a)
$$

Introduciamo un nuovo stato non finale $\bot$.

### Passaggio 3: tabella completa

| Stato | a | b | Finale? |
|---|---|---|---|
| $A$ | $A$ | $B$ | Sì |
| $B$ | $\bot$ | $B$ | Sì |
| $\bot$ | $\bot$ | $\bot$ | No |

Lo stato iniziale resta $A$.

### Linguaggio riconosciuto

In $A$ possiamo leggere qualsiasi numero di `a`.

Dopo la prima `b`, ci troviamo in $B$:

- altre `b` sono ammesse;
- una `a` conduce al pozzo.

Quindi:

$$
L=\{a^n b^m\mid n,m\geq 0\}
$$

cioè:

$$
a^*b^*
$$

### Controlli

La stringa `aab` è accettata:

$$
A\xrightarrow{a}A
\xrightarrow{a}A
\xrightarrow{b}B
$$

La stringa `ba` è rifiutata:

$$
A\xrightarrow{b}B
\xrightarrow{a}\bot
$$

Anche $\varepsilon$ è accettata, perché $A$ è finale.

> [!warning]
> Nell’automa parziale iniziale tutti gli stati erano finali, ma il linguaggio non era $\Sigma^*$.
>
> La lettura di `ba` si bloccava.
>
> Dopo il completamento questa situazione è rappresentata dal pozzo non finale.

## 7. Stati irraggiungibili e stati morti

È utile distinguere due concetti.

### Stato irraggiungibile

Non esiste un cammino dallo stato iniziale allo stato considerato.

La domanda riguarda il passato della lettura:

> Possiamo arrivare qui?

### Stato morto, o non produttivo

Non esiste alcuna continuazione che permetta di raggiungere uno stato finale.

Per un DFA completo:

$$
q\text{ è morto}
\iff
\forall v\in\Sigma^*:
\delta^*(q,v)\notin F
$$

La domanda riguarda il futuro:

> Da qui possiamo ancora accettare qualcosa?

### Non sono la stessa proprietà

Nell’esempio:

- $U$ è irraggiungibile, ma essendo finale potrebbe accettare la continuazione vuota se ci trovassimo in quello stato;
- $\bot$ è raggiungibile, per esempio leggendo `ba`, ma è morto.

Un normale stato non finale, invece, può non essere morto.

Nell’automa della parità, lo stato dispari è non finale, ma leggendo un’altra `a` raggiunge quello pari e finale.

### Stato morto e stato assorbente

Uno stato morto non deve necessariamente avere soltanto autoanelli.

Può portare ad altri stati morti, senza mai raggiungere finali.

Durante la minimizzazione, gli stati morti raggiungibili risultano equivalenti: da tutti loro il linguaggio delle continuazioni accettate è vuoto.

## 8. Collegamento con la minimizzazione

Prima di minimizzare, bisogna considerare gli stati raggiungibili.

Gli irraggiungibili non contribuiscono al riconoscimento del linguaggio e non devono comparire nel risultato minimo.

Gli stati morti raggiungibili, invece, non vanno semplicemente cancellati se vogliamo mantenere un DFA completo.

Possono essere riuniti in un unico stato pozzo.

### Caso del linguaggio vuoto

Se nessun finale è raggiungibile dall’iniziale:

$$
L(M)=\varnothing
$$

Il DFA minimo completo per il linguaggio vuoto ha un solo stato:

- iniziale;
- non finale;
- con un autoanello per ogni simbolo.

> [!important]
> Per minimizzare non basta eliminare gli irraggiungibili.
>
> Possono rimanere stati raggiungibili con lo stesso comportamento futuro, che devono essere riconosciuti come equivalenti.

## 9. Collegamento con il complemento

Per costruire il complemento attraverso lo scambio dei finali serve un DFA completo.

L’eventuale pozzo deve quindi essere presente prima dello scambio.

Dopo lo scambio, il vecchio pozzo non finale diventa finale.

Rimane assorbente, ma non è più uno stato di rifiuto.

### Nell’esempio a*b*

Prima del complemento:

$$
F=\{A,B\}
$$

Dopo lo scambio:

$$
F_{\mathrm{comp}}=\{\bot\}
$$

Il complemento accetta le stringhe che hanno letto una `a` dopo essere entrate nella fase delle `b`.

Il passaggio al pozzo segnala proprio questa violazione dell’ordine.

## 10. Procedura pratica negli esercizi

Una sequenza utile è:

1. fissare l’alfabeto;
2. individuare gli stati raggiungibili;
3. eliminare quelli irraggiungibili;
4. controllare tutte le coppie stato-simbolo;
5. completare le transizioni mancanti;
6. procedere con minimizzazione o complemento, secondo la richiesta.

Se nella determinizzazione hai già:

- generato soltanto i sottoinsiemi raggiungibili;
- elaborato ogni simbolo per ciascuno;
- inserito $\varnothing$ quando necessario;

il DFA risultante è già raggiungibile e completo.

Non serve aggiungere un secondo pozzo.

## 11. Errori da evitare

- Confondere “non finale” con “inutile”.
- Considerare irraggiungibile uno stato solo perché non riceve archi diretti dall’iniziale.
- Dimenticare che lo stato iniziale è raggiungibile con $\varepsilon$.
- Seguire soltanto epsilon-mosse quando si cercano tutti i raggiungibili.
- Eliminare un pozzo raggiungibile lasciando incompleto un DFA che deve essere completo.
- Aggiungere un nuovo pozzo quando non manca alcuna transizione.
- Dimenticare gli autoanelli del pozzo.
- Rendere finale il pozzo aggiunto per completare l’automa originale.
- Calcolare il complemento prima del completamento.
- Pensare che tutti gli stati raggiungibili siano necessari nel DFA minimo.