# Proprietà di chiusura dei linguaggi regolari

## 1. Che cosa significa “chiusura”

Una classe di linguaggi è **chiusa rispetto a un’operazione** se applicando quell’operazione a linguaggi della classe si ottiene ancora un linguaggio della stessa classe.

Per esempio, la chiusura dei linguaggi regolari rispetto all’unione significa:

$$
L_1,L_2\in\mathrm{REG}
\implies
L_1\cup L_2\in\mathrm{REG}
$$

La proprietà riguarda tutti i linguaggi regolari, non soltanto alcuni esempi.

Per dimostrarla possiamo fornire una costruzione che, partendo da descrizioni regolari degli operandi, produca una descrizione regolare del risultato.

In questa nota fissiamo un alfabeto comune $\Sigma$. I complementi sono sempre riferiti a $\Sigma^*$.

## 2. Le principali proprietà

I linguaggi regolari sono chiusi rispetto alle seguenti operazioni:

| Operazione | Linguaggio risultante |
|---|---|
| Unione | $L_1\cup L_2$ |
| Intersezione | $L_1\cap L_2$ |
| Complemento | $\Sigma^*\setminus L$ |
| Differenza | $L_1\setminus L_2$ |
| Concatenazione | $L_1L_2$ |
| Stella di Kleene | $L^*$ |
| Chiusura positiva | $L^+$ |
| Potenza finita | $L^n$, per ogni intero fissato $n\geq 0$ |
| Rovesciamento | $L^R$ |

Le costruzioni più comode dipendono dall’operazione:

- per unione, concatenazione e stella sono immediate le espressioni regolari;
- per complemento e intersezione sono particolarmente utili gli automi.

## 3. Unione, concatenazione e stella

Supponiamo:

$$
L_1=L(r_1)
\qquad
L_2=L(r_2)
$$

con $r_1$ e $r_2$ espressioni regolari.

### Unione

L’espressione:

$$
r_1\mid r_2
$$

denota:

$$
L_1\cup L_2
$$

Quindi l’unione è regolare.

### Concatenazione

L’espressione:

$$
r_1r_2
$$

denota:

$$
L_1L_2
$$

Quindi la concatenazione è regolare.

### Stella di Kleene

L’espressione:

$$
r_1^*
$$

denota:

$$
L_1^*
$$

Quindi la stella conserva la regolarità.

### Chiusura positiva e potenze

Poiché:

$$
L^+=LL^*
$$

la chiusura positiva è regolare per chiusura rispetto a concatenazione e stella.

Anche ogni potenza finita è regolare:

$$
L^0=\{\varepsilon\}
$$

$$
L^{n+1}=L^nL
$$

## 4. Complemento: costruzione e dimostrazione

Il complemento di $L$ rispetto all’alfabeto $\Sigma$ è:

$$
\overline L=\Sigma^*\setminus L
$$

Sia:

$$
D=(Q,\Sigma,\delta,q_0,F)
$$

un automa finito **deterministico completo** che riconosce $L$.

Costruiamo:

$$
\overline D=
(Q,\Sigma,\delta,q_0,Q\setminus F)
$$

Manteniamo stati, transizioni e stato iniziale, ma scambiamo stati finali e non finali.

### Perché funziona

Essendo deterministico e completo, l’automa raggiunge esattamente uno stato dopo aver letto qualsiasi stringa $w\in\Sigma^*$.

Indichiamo questo stato con:

$$
\delta^*(q_0,w)
$$

Allora:

$$
w\in L(\overline D)
\iff
\delta^*(q_0,w)\notin F
$$

$$
\iff
w\notin L(D)
$$

Quindi:

$$
L(\overline D)=\Sigma^*\setminus L(D)
$$

Il complemento di un linguaggio regolare è dunque regolare.

> [!important] Collegamento con l’esame
> Scambiare finali e non finali è corretto su un automa deterministico completo.
>
> Non è necessario che l’automa sia minimo.

### Se l’automa è incompleto

Se manca una transizione, alcune stringhe non conducono ad alcuno stato.

Scambiare soltanto i finali non rende accettanti questi percorsi mancanti.

Bisogna prima completare l’automa introducendo, quando necessario, uno stato pozzo.

Per esempio, supponiamo che su $\Sigma=\{a,b\}$ siano presenti soltanto:

- uno stato iniziale $q_0$;
- uno stato finale $q_1$;
- la transizione $q_0\xrightarrow{a}q_1$.

Il linguaggio è $\{a\}$.

Scambiando soltanto i finali, la stringa `b` continua a non avere un percorso. Ma `b` dovrebbe appartenere al complemento.

### Se l’automa è non deterministico

Nel caso non deterministico una stringa è accettata se esiste almeno un cammino accettante.

Scambiare i finali non nega questa condizione.

Consideriamo, su $\Sigma=\{a\}$:

- $q_0$ iniziale;
- $q_1$ finale;
- da $q_0$, leggendo `a`, possiamo andare sia in $q_0$ sia in $q_1$;
- da $q_1$, leggendo `a`, rimaniamo in $q_1$.

La stringa `a` è accettata tramite il cammino che termina in $q_1$.

Dopo lo scambio dei finali è ancora accettata, tramite il cammino che termina in $q_0$.

Non può quindi essere il complemento.

### Procedimento generale

Per costruire l’automa del complemento:

1. ottenere un automa deterministico equivalente, gestendo le eventuali epsilon-mosse;
2. completarlo rispetto all’alfabeto considerato;
3. scambiare stati finali e non finali.

## 5. Intersezione mediante automa prodotto

Siano:

$$
D_1=(Q_1,\Sigma,\delta_1,s_1,F_1)
$$

$$
D_2=(Q_2,\Sigma,\delta_2,s_2,F_2)
$$

due automi deterministici completi.

Costruiamo un automa che simuli entrambi contemporaneamente.

### Stati

Gli stati sono coppie:

$$
Q=Q_1\times Q_2
$$

La coppia $(p,q)$ registra lo stato corrente del primo e del secondo automa.

### Stato iniziale

$$
s=(s_1,s_2)
$$

### Transizioni

Per ogni $a\in\Sigma$:

$$
\delta((p,q),a)=
\bigl(\delta_1(p,a),\delta_2(q,a)\bigr)
$$

Entrambi gli automi leggono lo stesso simbolo.

### Stati finali per l’intersezione

Una coppia è finale se entrambi i suoi componenti sono finali:

$$
F=F_1\times F_2
$$

Quindi:

$$
w\in L(D)
\iff
w\in L(D_1)\land w\in L(D_2)
$$

Otteniamo:

$$
L(D)=L_1\cap L_2
$$

Il numero degli stati è finito, al massimo:

$$
|Q_1|\cdot|Q_2|
$$

Perciò l’intersezione è regolare.

## 6. Unione e differenza con lo stesso prodotto

La struttura dell’automa prodotto può essere usata anche per altre operazioni: basta cambiare il criterio di accettazione.

| Operazione | Quando la coppia $(p,q)$ è finale |
|---|---|
| $L_1\cap L_2$ | $p\in F_1$ e $q\in F_2$ |
| $L_1\cup L_2$ | $p\in F_1$ oppure $q\in F_2$ |
| $L_1\setminus L_2$ | $p\in F_1$ e $q\notin F_2$ |

Per la differenza possiamo anche usare:

$$
L_1\setminus L_2=L_1\cap\overline{L_2}
$$

La regolarità segue quindi dalla chiusura rispetto a complemento e intersezione.

> [!warning]
> Prima del prodotto, gli automi devono essere considerati sullo stesso alfabeto e completati rispetto a esso.
>
> Una lettera non gestita da uno dei due automi deve condurre al suo stato pozzo.

## 7. Rovesciamento

Se:

$$
w=a_1a_2\cdots a_n
$$

il suo rovesciamento è:

$$
w^R=a_n\cdots a_2a_1
$$

Inoltre:

$$
\varepsilon^R=\varepsilon
$$

Per un linguaggio:

$$
L^R=\{w^R\mid w\in L\}
$$

### Costruzione dell’automa

Partendo da un automa per $L$:

1. invertiamo la direzione di tutte le transizioni, mantenendo le etichette;
2. introduciamo un nuovo stato iniziale;
3. dal nuovo iniziale aggiungiamo epsilon-transizioni verso tutti i vecchi stati finali;
4. rendiamo il vecchio stato iniziale l’unico stato finale.

Il risultato è un automa non deterministico con epsilon-mosse che riconosce $L^R$.

Quindi anche il rovesciamento conserva la regolarità.

### Esempio

Se:

$$
L=\{a^n b\mid n\geq 0\}
$$

allora:

$$
L^R=\{ba^n\mid n\geq 0\}
$$

L’espressione regolare passa da:

$$
a^*b
$$

a:

$$
ba^*
$$

> [!important]
> Rovesciamento e complemento sono operazioni diverse:
>
> - il rovesciamento cambia l’ordine dei simboli;
> - il complemento seleziona le stringhe che non appartengono al linguaggio.

## 8. Chiusura finita e unioni infinite

La chiusura rispetto all’unione di due linguaggi implica la chiusura rispetto a qualsiasi unione finita:

$$
L_1\cup\cdots\cup L_k
$$

con $k$ finito.

Non implica la chiusura rispetto a unioni infinite arbitrarie.

### Controesempio

Per ogni $n\geq 0$, definiamo:

$$
L_n=\{a^n b^n\}
$$

Ogni $L_n$ è regolare: contiene una sola stringa.

Ma:

$$
\bigcup_{n\geq 0}L_n
=
\{a^n b^n\mid n\geq 0\}
$$

non è regolare.

> [!warning]
> La stella è regolare anche se:
>
> $$
> L^*=\bigcup_{n\geq 0}L^n
> $$
>
> Questo è un caso specifico, con una struttura ripetitiva descritta da un solo operatore. Non dimostra la chiusura rispetto a qualsiasi unione infinita.

## 9. Usare le chiusure per dimostrare la non regolarità

Le proprietà di chiusura possono essere utilizzate anche per ottenere una contraddizione.

Un metodo comune è:

1. supporre che il linguaggio $L$ sia regolare;
2. combinarlo con un linguaggio sicuramente regolare;
3. ottenere un linguaggio noto come non regolare;
4. concludere che l’ipotesi era falsa.

### Esempio con intersezione

Consideriamo, su $\Sigma=\{a,b,c\}$:

$$
L=
\{a^n b^n\mid n\geq 0\}
\cup
\{c^m\mid m\geq 0\}
$$

Scegliamo il linguaggio regolare:

$$
R=L(a^*b^*)
$$

Allora:

$$
L\cap R=
\{a^n b^n\mid n\geq 0\}
$$

Se $L$ fosse regolare, anche $L\cap R$ dovrebbe esserlo.

Ma il risultato non è regolare. Quindi:

$$
L\notin\mathrm{REG}
$$

### Attenzione al verso del ragionamento

Sapere che un linguaggio contiene un sottoinsieme non regolare non basta per dimostrarne la non regolarità.

Per esempio:

$$
\{a^n b^n\mid n\geq 0\}
\subseteq
\{a,b\}^*
$$

ma $\{a,b\}^*$ è regolare.

Serve un’operazione di chiusura che isoli il linguaggio non regolare, non una semplice inclusione.

## 10. Attenzione alla classe considerata

Queste proprietà riguardano i linguaggi regolari.

Non devono essere trasferite automaticamente ad altre classi.

Per esempio:

- i linguaggi regolari sono chiusi rispetto all’unione;
- i linguaggi context-free deterministici non sono chiusi rispetto all’unione.

La chiusura di una sottoclasse non garantisce quella della classe più ampia.

> [!important] Collegamento con l’esame
> Se la domanda parla di “linguaggi deterministici”, nel contesto degli automi a pila si riferisce alla classe DCFL.
>
> Non basta rispondere usando le proprietà dei linguaggi regolari.

## 11. Cosa saper dimostrare

Per una preparazione adeguata all’esame, devi saper spiegare:

- unione, concatenazione e stella mediante espressioni regolari;
- complemento mediante un automa deterministico completo;
- intersezione mediante automa prodotto;
- differenza come intersezione con un complemento;
- rovesciamento mediante inversione delle transizioni e gestione degli stati iniziali e finali.

La costruzione è la parte centrale della dimostrazione: deve funzionare per linguaggi arbitrari della classe, non soltanto per un esempio.

## 12. Errori da evitare

- Scambiare finali e non finali in un automa non deterministico.
- Dimenticare di completare l’automa prima del complemento.
- Calcolare il complemento senza fissare l’alfabeto.
- Confondere determinismo e minimalità.
- Accettare nel prodotto per l’intersezione quando è finale un solo componente.
- Generalizzare dalle unioni finite alle unioni infinite.
- Concludere che un linguaggio sia non regolare solo perché contiene un sottoinsieme non regolare.
- Applicare le chiusure dei regolari ai linguaggi deterministici senza verificare la classe.