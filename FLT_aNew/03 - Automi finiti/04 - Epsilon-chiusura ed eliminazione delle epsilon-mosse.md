# Epsilon-chiusura ed eliminazione delle epsilon-mosse

## 1. Obiettivo

Dato un ε-NFA:

$$
M=(Q,\Sigma,\delta,q_0,F)
$$

vogliamo costruire un automa senza epsilon-mosse:

$$
M'=(Q,\Sigma,\delta',q_0,F')
$$

tale che:

$$
L(M')=L(M)
$$

Nella costruzione presentata manteniamo:

- gli stessi stati;
- lo stesso alfabeto;
- lo stesso stato iniziale.

Modifichiamo le transizioni e, quando necessario, gli stati finali.

> [!important]
> Eliminare le epsilon-mosse non significa semplicemente cancellare gli archi etichettati con $\varepsilon$.
>
> Bisogna conservare le possibilità di lettura e accettazione che quegli archi rendevano disponibili.

Il risultato può essere ancora non deterministico.

## 2. Epsilon-chiusura

L’epsilon-chiusura di uno stato $q$ è:

$$
E(q)=
\{p\in Q\mid
p\text{ è raggiungibile da }q
\text{ con zero o più epsilon-mosse}\}
$$

Poiché sono ammessi zero passi:

$$
q\in E(q)
$$

Per un insieme $R\subseteq Q$:

$$
E(R)=\bigcup_{q\in R}E(q)
$$

### Come calcolarla

Per ogni stato $q$:

1. inserisci $q$ nella chiusura;
2. segui tutte le epsilon-transizioni uscenti dagli stati trovati;
3. aggiungi le destinazioni non ancora presenti;
4. continua finché non vengono aggiunti nuovi stati.

Durante questo calcolo si seguono soltanto archi epsilon.

Le transizioni su simboli dell’alfabeto non contribuiscono alla chiusura.

### Cicli di epsilon-mosse

Un ciclo non rende infinita la chiusura: gli stati sono finiti e ciascuno viene inserito una sola volta.

Per esempio:

$$
q_0\xrightarrow{\varepsilon}q_1
\xrightarrow{\varepsilon}q_2
\xrightarrow{\varepsilon}q_1
$$

produce:

$$
E(q_0)=\{q_0,q_1,q_2\}
$$

## 3. Costruzione delle nuove transizioni

La nuova funzione è:

$$
\delta':Q\times\Sigma\to\mathcal P(Q)
$$

Per ogni stato $q$ e simbolo $a$ definiamo:

$$
\boxed{
\delta'(q,a)
=
E\left(
\bigcup_{p\in E(q)}\delta(p,a)
\right)
}
$$

Una nuova transizione su `a` riassume quindi un percorso formato da:

1. zero o più epsilon-mosse;
2. una transizione su `a`;
3. zero o più epsilon-mosse.

Simbolicamente:

$$
q
\xrightarrow{\varepsilon^*}
p
\xrightarrow{a}
r
\xrightarrow{\varepsilon^*}
s
$$

Nella notazione del percorso, $\varepsilon^*$ indica una sequenza di zero o più epsilon-mosse.

La destinazione $s$ viene inserita in $\delta'(q,a)$.

### Tre passaggi operativi

Per riempire una casella della nuova tabella:

1. prendi $E(q)$;
2. unisci le transizioni su `a` da tutti gli stati della chiusura;
3. calcola l’epsilon-chiusura delle destinazioni ottenute.

> [!warning]
> Usare soltanto $\delta(q,a)$ perderebbe i percorsi che richiedono epsilon-mosse prima del simbolo.
>
> Dimenticare la chiusura finale perderebbe alcune destinazioni raggiungibili dopo il simbolo, rispetto alla costruzione qui adottata.

## 4. Costruzione dei nuovi stati finali

Definiamo:

$$
\boxed{
F'=
\{q\in Q\mid E(q)\cap F\neq\varnothing\}
}
$$

Uno stato diventa finale se nell’automa originale poteva raggiungere un finale senza leggere altri simboli.

In particolare:

$$
F\subseteq F'
$$

perché ogni stato appartiene alla propria epsilon-chiusura.

### Perché è importante

Se:

$$
q_0\xrightarrow{\varepsilon}q_f
$$

con $q_f\in F$, l’automa originale accetta $\varepsilon$.

Dopo aver eliminato le epsilon-mosse, questa possibilità viene conservata rendendo finale $q_0$.

Infatti:

$$
\varepsilon\in L(M)
\iff
E(q_0)\cap F\neq\varnothing
$$

e:

$$
\varepsilon\in L(M')
\iff
q_0\in F'
$$

## 5. Esempio completo

Consideriamo un automa su:

$$
\Sigma=\{a,b\}
$$

con stato iniziale $q_0$ e:

$$
F=\{q_2\}
$$

La tabella originale è:

| Stato | a | b | ε |
|---|---|---|---|
| $q_0$ | $\{q_0\}$ | $\varnothing$ | $\{q_1\}$ |
| $q_1$ | $\varnothing$ | $\{q_1\}$ | $\{q_2\}$ |
| $q_2$ | $\varnothing$ | $\varnothing$ | $\varnothing$ |

L’automa può:

- leggere alcune `a` in $q_0$;
- passare con epsilon in $q_1$;
- leggere alcune `b`;
- passare con epsilon nello stato finale $q_2$.

Il linguaggio è:

$$
L(M)=\{a^n b^m\mid n,m\geq 0\}
$$

cioè:

$$
a^*b^*
$$

### Passaggio 1: epsilon-chiusure

| Stato | Epsilon-chiusura |
|---|---|
| $q_0$ | $\{q_0,q_1,q_2\}$ |
| $q_1$ | $\{q_1,q_2\}$ |
| $q_2$ | $\{q_2\}$ |

### Passaggio 2: transizione da q₀ su a

Partiamo da:

$$
E(q_0)=\{q_0,q_1,q_2\}
$$

Leggendo `a`:

$$
\delta(q_0,a)
\cup
\delta(q_1,a)
\cup
\delta(q_2,a)
=
\{q_0\}
$$

Applichiamo la chiusura:

$$
\delta'(q_0,a)
=
E(\{q_0\})
=
\{q_0,q_1,q_2\}
$$

### Passaggio 3: transizione da q₀ su b

Leggendo `b` dagli stati di $E(q_0)$:

$$
\delta(q_0,b)
\cup
\delta(q_1,b)
\cup
\delta(q_2,b)
=
\{q_1\}
$$

Quindi:

$$
\delta'(q_0,b)
=
E(\{q_1\})
=
\{q_1,q_2\}
$$

La transizione esiste anche se dall’originale $q_0$ non partiva direttamente alcun arco su `b`.

### Passaggio 4: completare la tabella

Otteniamo:

| Stato | a | b |
|---|---|---|
| $q_0$ | $\{q_0,q_1,q_2\}$ | $\{q_1,q_2\}$ |
| $q_1$ | $\varnothing$ | $\{q_1,q_2\}$ |
| $q_2$ | $\varnothing$ | $\varnothing$ |

Non compare più la colonna delle epsilon-mosse.

### Passaggio 5: nuovi stati finali

Ogni epsilon-chiusura contiene il vecchio finale $q_2$.

Quindi:

$$
F'=\{q_0,q_1,q_2\}
$$

Lo stato iniziale rimane $q_0$.

### Controllo della stringa vuota

Nel vecchio automa:

$$
q_0\xrightarrow{\varepsilon}q_1
\xrightarrow{\varepsilon}q_2
$$

Nel nuovo automa, invece, $q_0$ è direttamente finale.

Entrambi accettano $\varepsilon$.

### Controllo di ab

Nel nuovo automa:

$$
\{q_0\}
\xrightarrow{a}
\{q_0,q_1,q_2\}
\xrightarrow{b}
\{q_1,q_2\}
$$

L’insieme finale contiene stati accettanti, quindi `ab` è accettata.

### Controllo di ba

$$
\{q_0\}
\xrightarrow{b}
\{q_1,q_2\}
\xrightarrow{a}
\varnothing
$$

La stringa è rifiutata.

> [!important]
> Tutti gli stati sono finali, ma questo NFA non accetta tutte le stringhe.
>
> Alcune letture non hanno alcun cammino completo, come accade per `ba`.
>
> La proprietà “tutti gli stati finali implica linguaggio $\Sigma^*$” vale per un DFA completo.

## 6. Perché il linguaggio viene conservato

### Dal nuovo automa all’originale

Ogni transizione del nuovo automa corrisponde nell’originale a:

- epsilon-mosse iniziali;
- lettura dello stesso simbolo;
- epsilon-mosse finali.

Se il nuovo cammino termina in uno stato di $F'$, nell’originale è possibile raggiungere un vecchio finale con ulteriori epsilon-mosse.

Quindi ogni stringa accettata da $M'$ è accettata da $M$.

### Dall’originale al nuovo automa

Un cammino dell’automa originale può essere suddiviso in segmenti che consumano un simbolo ciascuno, includendo le epsilon-mosse circostanti.

Ogni segmento è rappresentato da una transizione di $\delta'$.

Per la stringa vuota, l’accettazione è conservata dalla definizione di $F'$.

Quindi ogni stringa accettata da $M$ è accettata da $M'$.

Concludiamo:

$$
L(M')=L(M)
$$

## 7. Una variante equivalente

È possibile incontrare anche la costruzione:

$$
\widetilde{\delta}(q,a)
=
\bigcup_{p\in E(q)}\delta(p,a)
$$

mantenendo:

$$
\widetilde F=
\{q\in Q\mid E(q)\cap F\neq\varnothing\}
$$

Questa variante considera le epsilon-mosse:

- prima di ogni simbolo, attraverso $E(q)$;
- dopo l’ultimo simbolo, attraverso i nuovi stati finali.

Produce ancora un automa equivalente, ma le tabelle possono essere diverse da quelle ottenute con la chiusura anche dopo il simbolo.

> [!note]
> Nelle costruzioni svolte in questa nota utilizziamo la formula completa:
>
> $$
> \delta'(q,a)
> =
> E\left(
> \bigcup_{p\in E(q)}\delta(p,a)
> \right)
> $$

## 8. Procedura da applicare all’esame

Per eliminare le epsilon-mosse:

1. Trascrivi correttamente stati, alfabeto, iniziale e finali.
2. Calcola l’epsilon-chiusura di ogni stato.
3. Costruisci la tabella delle nuove transizioni sui soli simboli dell’alfabeto.
4. Calcola i nuovi stati finali.
5. Mantieni lo stato iniziale.
6. Disegna l’automa senza archi epsilon.
7. Controlla l’accettazione di $\varepsilon$ e alcune stringhe brevi.

Se la traccia richiede anche determinizzazione e minimizzazione, queste sono le fasi successive.

### Distinguere le operazioni

| Operazione | Risultato |
|---|---|
| Eliminazione delle epsilon-mosse | Automa senza epsilon-mosse, eventualmente non deterministico |
| Determinizzazione | Automa con un’unica destinazione per stato e simbolo |
| Minimizzazione | DFA equivalente con il minimo numero di stati |

Nella costruzione qui adottata, lo stato iniziale resta $q_0$.

L’insieme $E(q_0)$ verrà invece utilizzato come stato iniziale nella determinizzazione diretta di un ε-NFA.

## 9. Controlli utili

Prima di considerare concluso l’esercizio, verifica che:

- ogni $E(q)$ contenga $q$;
- durante le chiusure siano stati seguiti soltanto archi epsilon;
- tutti i vecchi finali appartengano anche a $F'$;
- la nuova tabella non contenga epsilon-transizioni;
- le destinazioni siano insiemi di stati di $Q$;
- l’accettazione della stringa vuota sia conservata.

Con la formula completa, ogni insieme $\delta'(q,a)$ è già chiuso rispetto alle epsilon-mosse dell’automa originale.

## 10. Errori da evitare

- Cancellare gli archi epsilon senza ricostruire le transizioni.
- Dimenticare lo stato stesso nella sua chiusura.
- Seguire archi su terminali durante il calcolo di una epsilon-chiusura.
- Calcolare soltanto le epsilon-mosse immediatamente uscenti.
- Dimenticare l’aggiornamento degli stati finali.
- Inserire $\varepsilon$ come elemento di una chiusura: le chiusure contengono stati.
- Sostituire automaticamente lo stato iniziale con un insieme durante questa costruzione.
- Pensare che l’automa ottenuto sia necessariamente deterministico.
- Confondere conservazione del linguaggio con identità delle tabelle.