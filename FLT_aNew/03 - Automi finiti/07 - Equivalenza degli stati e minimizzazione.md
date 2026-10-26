# Equivalenza degli stati e minimizzazione

## 1. Obiettivo della minimizzazione

La **minimizzazione** trasforma un DFA in un DFA equivalente con il minimo numero di stati.

Se l’automa iniziale è $M$, vogliamo ottenere $M_{\min}$ tale che:

$$
L(M_{\min})=L(M)
$$

e nessun DFA completo sullo stesso alfabeto che riconosca quel linguaggio abbia meno stati.

Il procedimento consiste nel:

1. eliminare gli stati irraggiungibili;
2. individuare gli stati con lo stesso comportamento futuro;
3. sostituire ogni gruppo di stati equivalenti con un solo stato.

> [!important]
> L’algoritmo presentato si applica a un DFA completo.
>
> Se l’automa è non deterministico, bisogna prima determinizzarlo.

## 2. Linguaggio riconosciuto a partire da uno stato

Per ogni stato $q$, definiamo:

$$
L_q=
\{w\in\Sigma^*
\mid
\delta^*(q,w)\in F\}
$$

$L_q$ è il linguaggio delle continuazioni che portano all’accettazione partendo da $q$.

Non consideriamo quindi come siamo arrivati a $q$, ma ciò che può accadere da quel momento in avanti.

Due stati possono essere stati raggiunti leggendo prefissi diversi e avere comunque lo stesso comportamento futuro.

## 3. Equivalenza degli stati

Due stati $p$ e $q$ sono **equivalenti** se:

$$
L_p=L_q
$$

Indichiamo questa relazione con:

$$
p\equiv q
$$

Equivalentemente:

$$
p\equiv q
\iff
\forall w\in\Sigma^*:
\left(
\delta^*(p,w)\in F
\iff
\delta^*(q,w)\in F
\right)
$$

Per ogni possibile continuazione, entrambi devono accettare oppure entrambi devono rifiutare.

### Stati distinguibili

Due stati sono distinguibili se esiste almeno un suffisso $w$ che viene accettato da uno e rifiutato dall’altro:

$$
p\not\equiv q
\iff
\exists w\in\Sigma^*:
\left[
\delta^*(p,w)\in F
\right]
\neq
\left[
\delta^*(q,w)\in F
\right]
$$

La stringa $w$ è un **suffisso distinguente**.

### Il ruolo di ε

Uno stato finale e uno non finale sono sempre distinguibili tramite:

$$
w=\varepsilon
$$

Infatti:

$$
\delta^*(q,\varepsilon)=q
$$

Quindi non possono appartenere alla stessa classe di equivalenza.

> [!warning]
> Essere entrambi finali, oppure entrambi non finali, è necessario per l’equivalenza, ma non sufficiente.

## 4. Partizione iniziale

Una **partizione** divide gli stati in blocchi disgiunti, non vuoti, la cui unione è tutto l’insieme degli stati.

Iniziamo separando finali e non finali:

$$
\Pi_0=
\{F,\ Q\setminus F\}
\setminus\{\varnothing\}
$$

Gli eventuali blocchi vuoti vengono omessi.

Questa divisione riconosce le differenze osservabili senza leggere simboli: quelle dovute a $\varepsilon$.

Gli stati nello stesso blocco sono ancora candidati a essere equivalenti.

## 5. Raffinamento della partizione

Supponiamo di avere una partizione corrente $\Pi$.

Due stati $p$ e $q$ dello stesso blocco possono rimanere insieme soltanto se, per ogni simbolo $a$:

$$
\delta(p,a)
$$

e:

$$
\delta(q,a)
$$

appartengono allo stesso blocco di $\Pi$.

Se esiste un simbolo che porta a blocchi diversi, separiamo $p$ e $q$.

### Firma di uno stato

Fissato:

$$
\Sigma=\{a_1,\ldots,a_k\}
$$

possiamo associare a ogni stato la firma:

$$
\operatorname{sig}_{\Pi}(q)
=
\left(
[\delta(q,a_1)]_{\Pi},
\ldots,
[\delta(q,a_k)]_{\Pi}
\right)
$$

La notazione $[r]_{\Pi}$ indica il blocco che contiene $r$.

All’interno di ciascun blocco, raggruppiamo gli stati con la stessa firma.

### Quando fermarsi

Ripetiamo il raffinamento finché la partizione non cambia più.

> [!important]
> Le firme vengono confrontate all’interno dei blocchi già esistenti.
>
> Il raffinamento divide i blocchi: non riunisce stati precedentemente separati.

Una distinzione può propagarsi attraverso più passaggi. Per questo una sola scansione non basta necessariamente.

L’algoritmo termina perché ogni divisione aumenta il numero dei blocchi, che non può superare $|Q|$.

## 6. Esempio completo

Riprendiamo il DFA di quattro stati ottenuto nella nota sulla determinizzazione.

Lo stato iniziale è $A$ e:

$$
F=\{A,B,C\}
$$

La tabella è:

| Stato | a | b | Finale? |
|---|---|---|---|
| $A$ | $B$ | $C$ | Sì |
| $B$ | $B$ | $C$ | Sì |
| $C$ | $\bot$ | $C$ | Sì |
| $\bot$ | $\bot$ | $\bot$ | No |

Tutti gli stati sono raggiungibili:

- $A$ con $\varepsilon$;
- $B$ con `a`;
- $C$ con `b`;
- $\bot$ con `ba`.

### Passaggio 1: partizione iniziale

$$
\Pi_0=
\bigl\{
\{A,B,C\},
\{\bot\}
\bigr\}
$$

Chiamiamo temporaneamente:

- $\mathcal F=\{A,B,C\}$;
- $\mathcal N=\{\bot\}$.

Le destinazioni appartengono ai seguenti blocchi:

| Stato | Blocco su a | Blocco su b |
|---|---|---|
| $A$ | $\mathcal F$ | $\mathcal F$ |
| $B$ | $\mathcal F$ | $\mathcal F$ |
| $C$ | $\mathcal N$ | $\mathcal F$ |
| $\bot$ | $\mathcal N$ | $\mathcal N$ |

Nel blocco $\{A,B,C\}$:

- $A$ e $B$ hanno la stessa firma;
- $C$ ha una firma diversa.

Quindi:

$$
\Pi_1=
\bigl\{
\{A,B\},
\{C\},
\{\bot\}
\bigr\}
$$

### Passaggio 2: verificare la stabilità

Gli stati $A$ e $B$:

- su `a` raggiungono entrambi $B$, appartenente a $\{A,B\}$;
- su `b` raggiungono entrambi $C$, appartenente a $\{C\}$.

Non devono essere separati.

Gli altri blocchi contengono un solo stato.

La partizione è stabile:

$$
\Pi_{\mathrm{finale}}
=
\bigl\{
\{A,B\},
\{C\},
\{\bot\}
\bigr\}
$$

### Passaggio 3: costruire i nuovi stati

Assegniamo i nomi:

$$
X=\{A,B\}
$$

$$
Y=\{C\}
$$

$$
Z=\{\bot\}
$$

Lo stato iniziale è $X$, perché contiene il vecchio iniziale $A$.

Gli stati finali sono $X$ e $Y$.

### Tabella del DFA minimo

| Stato | a | b | Finale? |
|---|---|---|---|
| $X$ | $X$ | $Y$ | Sì |
| $Y$ | $Z$ | $Y$ | Sì |
| $Z$ | $Z$ | $Z$ | No |

Il linguaggio rimane:

$$
a^*b^*
$$

### Perché non possiamo unire ulteriormente

- $X$ e $Z$ sono distinguibili con $\varepsilon$.
- $Y$ e $Z$ sono distinguibili con $\varepsilon$.
- $X$ e $Y$ sono distinguibili con `a`:

$$
\delta(X,a)=X\in F_{\min}
$$

$$
\delta(Y,a)=Z\notin F_{\min}
$$

I tre stati sono raggiungibili e a due a due distinguibili.

Il DFA è quindi minimo.

## 7. Costruzione formale dell’automa quoziente

Dopo aver individuato le classi di equivalenza, indichiamo con:

$$
[q]
$$

la classe che contiene $q$.

Il DFA minimo ha:

### Stati

$$
Q_{\min}=Q/{\equiv}
$$

cioè l’insieme delle classi di equivalenza, dopo aver eliminato gli irraggiungibili.

### Stato iniziale

$$
q_{0,\min}=[q_0]
$$

### Stati finali

$$
F_{\min}=
\{[q]\mid q\in F\}
$$

Ogni classe contiene soltanto finali oppure soltanto non finali.

### Transizioni

$$
\delta_{\min}([q],a)
=
[\delta(q,a)]
$$

Possiamo scegliere qualsiasi rappresentante della classe.

La destinazione non cambia perché:

$$
p\equiv q
\implies
\delta(p,a)\equiv\delta(q,a)
$$

Se questa proprietà non valesse, una continuazione dopo `a` potrebbe distinguere $p$ e $q$, contraddicendo la loro equivalenza.

## 8. Perché il raffinamento trova gli stati equivalenti

### Ogni separazione è giustificata

La separazione iniziale tra finali e non finali è giustificata dal suffisso $\varepsilon$.

Se due stati vengono separati perché su `a` raggiungono stati distinguibili tramite $w$, allora sono distinguibili tramite:

$$
aw
$$

Le differenze si propagano quindi all’indietro.

### I blocchi stabili contengono stati equivalenti

Quando la partizione è stabile, due stati dello stesso blocco rimangono nello stesso blocco dopo ogni simbolo.

Ripetendo il ragionamento, dopo qualsiasi stringa raggiungono stati dello stesso blocco.

Poiché ogni blocco contiene soltanto finali oppure soltanto non finali, l’esito dell’accettazione coincide.

La partizione finale coincide quindi con le classi di equivalenza.

## 9. Metodo alternativo: tabella delle coppie distinguibili

Un altro procedimento lavora sulle coppie di stati.

### Procedura

1. Considera tutte le coppie di stati distinti.
2. Marca subito le coppie con uno stato finale e uno non finale.
3. Cerca coppie $(p,q)$ per cui esiste un simbolo $a$ tale che la coppia:

$$
\bigl(\delta(p,a),\delta(q,a)\bigr)
$$

sia già distinguibile.

4. Marca anche $(p,q)$.
5. Ripeti finché non compaiono nuove marcature.
6. Gli stati delle coppie rimaste non marcate sono equivalenti.

Se le due destinazioni coincidono, non forniscono una distinzione.

### Suffissi distinguenti

Se le destinazioni sono distinguibili tramite $w$, la coppia di partenza è distinguibile tramite:

$$
aw
$$

Nell’esempio, $A$ e $C$ sono distinguibili con `a`, perché raggiungono rispettivamente $B$ e $\bot$, già distinguibili con $\varepsilon$.

La coppia $(A,B)$ rimane invece non marcata.

I due metodi, raffinamento e tabella delle coppie, individuano la stessa equivalenza.

## 10. Casi particolari

### Nessuno stato finale raggiungibile

Il linguaggio è vuoto.

Il DFA minimo completo ha un solo stato non finale con un autoanello per ogni simbolo.

### Tutti gli stati raggiungibili sono finali

Se il DFA è completo:

$$
L(M)=\Sigma^*
$$

Il DFA minimo ha un solo stato finale con un autoanello per ogni simbolo.

### Più stati morti

Gli stati morti non accettano alcuna continuazione:

$$
L_q=\varnothing
$$

Sono quindi equivalenti e, se raggiungibili, vengono riuniti in un unico stato.

## 11. Determinizzazione e minimizzazione: insiemi con significati diversi

Entrambe le costruzioni possono utilizzare nomi scritti come insiemi, ma il significato cambia.

| Operazione | Che cosa rappresenta un nuovo stato |
|---|---|
| Determinizzazione | Stati dell’NFA simultaneamente possibili dopo un prefisso |
| Minimizzazione | Stati del DFA con lo stesso linguaggio delle continuazioni |

Nella determinizzazione due sottoinsiemi diversi restano distinti.

La minimizzazione può successivamente riconoscere che i corrispondenti stati hanno lo stesso comportamento futuro.

## 12. Procedura da applicare all’esame

1. Verifica che l’automa sia deterministico.
2. Controlla la completezza rispetto all’alfabeto.
3. Elimina gli stati irraggiungibili.
4. Separa finali e non finali.
5. Raffina i blocchi fino alla stabilità, oppure usa la tabella delle coppie.
6. Costruisci uno stato per ogni classe.
7. Trasferisci correttamente iniziale, finali e transizioni.
8. Controlla che tutte le classi siano raggiungibili.

Per motivare la minimalità puoi indicare suffissi che distinguano le classi rimaste.

A parità di alfabeto, il DFA minimo completo è unico a meno dei nomi assegnati agli stati.

## 13. Errori da evitare

- Applicare direttamente questo algoritmo a un NFA.
- Confondere eliminazione degli irraggiungibili e minimizzazione.
- Unire tutti gli stati finali senza controllare le continuazioni.
- Unire stati finali e non finali.
- Confrontare soltanto i nomi delle destinazioni invece dei blocchi.
- Fermarsi dopo una sola scansione senza verificare la stabilità.
- Riunire blocchi già separati durante il raffinamento.
- Dimenticare il pozzo quando serve per un DFA completo.
- Eliminare lo stato iniziale anziché trasferirlo alla sua classe.
- Confrontare il numero di stati di un DFA completo con quello di una rappresentazione parziale senza considerare il pozzo implicito.