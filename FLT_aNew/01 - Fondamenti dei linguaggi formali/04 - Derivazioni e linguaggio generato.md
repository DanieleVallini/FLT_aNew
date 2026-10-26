# Derivazioni e linguaggio generato

## 1. Obiettivo

Data una grammatica:

$$
G=(N,\Sigma,P,S)
$$

vogliamo capire:

- come si applicano le produzioni;
- quali stringhe si possono ottenere dal simbolo iniziale;
- quale linguaggio viene generato;
- come dimostrare che la descrizione del linguaggio è corretta.

Poniamo:

$$
V=N\cup\Sigma
$$

## 2. Derivazione in un passo

Una stringa $x$ deriva in un passo la stringa $y$, e scriviamo:

$$
x\Rightarrow_G y
$$

se esistono $u,v,\alpha,\beta\in V^*$ tali che:

$$
x=u\alpha v
$$

$$
y=u\beta v
$$

e:

$$
\alpha\to\beta\in P
$$

Si sostituisce quindi **una sola occorrenza** della parte sinistra di una produzione con la corrispondente parte destra.

Le stringhe $u$ e $v$, che circondano l’occorrenza scelta, non cambiano.

### Esempio

Se:

$$
A\to ab
$$

allora:

$$
aAb\Rightarrow aabb
$$

La sostituzione è:

$$
a\underbrace{A}_{\alpha}b
\Rightarrow
a\underbrace{ab}_{\beta}b
$$

### Una produzione per passo

Se abbiamo:

$$
S\to AB,\qquad A\to a,\qquad B\to b
$$

una derivazione è:

$$
S\Rightarrow AB\Rightarrow aB\Rightarrow ab
$$

Non possiamo scrivere:

$$
AB\Rightarrow ab
$$

perché nessuna delle produzioni date permette questo passaggio in un solo passo.

Possiamo invece scrivere:

$$
AB\Rightarrow^* ab
$$

## 3. Derivazione in n passi

La notazione:

$$
x\Rightarrow_G^n y
$$

indica una derivazione di **esattamente $n$ passi**.

Formalmente:

$$
x\Rightarrow_G^0 y
\iff
x=y
$$

e, per $n\geq 0$:

$$
x\Rightarrow_G^{n+1}y
\iff
\exists z\in V^*:
x\Rightarrow_G^n z
\land
z\Rightarrow_G y
$$

Per esempio:

$$
S\Rightarrow AB\Rightarrow aB\Rightarrow ab
$$

mostra che:

$$
S\Rightarrow_G^3 ab
$$

> [!warning]
> Il numero di passi non coincide necessariamente con la lunghezza della stringa ottenuta.
>
> Nell’esempio servono tre passi per generare una stringa di lunghezza due.

## 4. Zero o più passi e uno o più passi

### Chiusura riflessiva e transitiva

La relazione $\Rightarrow_G^*$ è definita da:

$$
x\Rightarrow_G^*y
\iff
\exists n\geq 0:
x\Rightarrow_G^n y
$$

Ammette quindi zero o più passi.

È:

- **riflessiva**, perché $x\Rightarrow_G^*x$;
- **transitiva**, perché due derivazioni consecutive possono essere concatenate.

In particolare:

$$
x\Rightarrow_G^*y
\land
y\Rightarrow_G^*z
\implies
x\Rightarrow_G^*z
$$

### Chiusura transitiva

La relazione $\Rightarrow_G^+$ richiede almeno un passo:

$$
x\Rightarrow_G^+y
\iff
\exists n\geq 1:
x\Rightarrow_G^n y
$$

> [!important]
> Da $x=y$ segue sempre $x\Rightarrow_G^*y$, ma non segue necessariamente $x\Rightarrow_G^+y$.
>
> Una derivazione positiva da una stringa a sé stessa esiste soltanto se le produzioni permettono di tornarvi in almeno un passo.

## 5. Forme sentenziali e linguaggio generato

Una stringa $\gamma$ è una **forma sentenziale** se:

$$
S\Rightarrow_G^*\gamma
$$

Può contenere sia terminali sia non terminali.

Il linguaggio generato comprende soltanto le forme sentenziali costituite interamente da terminali:

$$
L(G)=\{w\in\Sigma^*\mid S\Rightarrow_G^*w\}
$$

Per dimostrare che:

$$
w\in L(G)
$$

basta quindi mostrare una derivazione da $S$ a $w$, verificando che $w$ contenga soltanto terminali.

### Esempio

Con:

$$
S\to aSb\mid\varepsilon
$$

abbiamo:

$$
S\Rightarrow aSb\Rightarrow aaSbb\Rightarrow aabb
$$

Quindi:

$$
aabb\in L(G)
$$

Invece `aaSbb`, pur essendo derivabile, non appartiene a $L(G)$ perché contiene il non terminale $S$.

### Zero passi non significa generare ε

Da:

$$
S\Rightarrow_G^*S
$$

non segue che:

$$
\varepsilon\in L(G)
$$

Per generare la stringa vuota deve esistere una derivazione:

$$
S\Rightarrow_G^*\varepsilon
$$

## 6. Come ricavare il linguaggio di una grammatica

Un procedimento utile consiste in questi passaggi:

1. Individuare le produzioni ricorsive.
2. Capire quali simboli aggiungono e in quali posizioni.
3. Individuare le produzioni che permettono di terminare.
4. Generare alcune stringhe brevi.
5. Formulare una descrizione generale del linguaggio.
6. Verificare che la descrizione includa tutte e sole le stringhe generate.

> [!warning]
> Generare alcuni esempi aiuta a formulare un’ipotesi, ma non dimostra che l’ipotesi sia corretta.

### Esempio: quantità collegate

Consideriamo:

$$
S\to aSb\mid c
$$

Le prime derivazioni sono:

$$
S\Rightarrow c
$$

$$
S\Rightarrow aSb\Rightarrow acb
$$

$$
S\Rightarrow aSb\Rightarrow aaSbb\Rightarrow aacbb
$$

Ogni uso della produzione ricorsiva aggiunge:

- una `a` prima di $S$;
- una `b` dopo $S$.

La produzione $S\to c$ termina la derivazione.

Dopo $n$ applicazioni della produzione ricorsiva:

$$
S\Rightarrow^n a^nSb^n
\Rightarrow a^ncb^n
$$

Quindi:

$$
L(G)=\{a^ncb^n\mid n\geq 0\}
$$

Il parametro può valere zero, perché possiamo scegliere subito $S\to c$.

La stringa minima è quindi `c`, non $\varepsilon$.

## 7. Parametri indipendenti e parametri collegati

Confrontiamo la grammatica precedente con:

$$
S\to AB
$$

$$
A\to aA\mid\varepsilon
$$

$$
B\to bB\mid\varepsilon
$$

Il non terminale $A$ genera un numero arbitrario di `a`.

Il non terminale $B$ genera un numero arbitrario di `b`.

Le due quantità sono indipendenti:

$$
L(G)=\{a^n b^m\mid n,m\geq 0\}
$$

Per esempio:

$$
S\Rightarrow AB
\Rightarrow aAB
\Rightarrow aaAB
\Rightarrow aaB
\Rightarrow aabB
\Rightarrow aab
$$

La grammatica genera `aab`, anche se il numero delle `a` è diverso dal numero delle `b`.

### Confronto

| Grammatica | Linguaggio | Vincolo |
|---|---|---|
| $S\to aSb\mid\varepsilon$ | $\{a^n b^n\mid n\geq 0\}$ | Quantità uguali |
| $S\to AB$, $A\to aA\mid\varepsilon$, $B\to bB\mid\varepsilon$ | $\{a^n b^m\mid n,m\geq 0\}$ | Quantità indipendenti |

> [!important] Collegamento con l’esame
> Quando compaiono più parametri, chiediti quali produzioni li controllano.
>
> Se una stessa produzione aggiunge simboli in due posizioni, può imporre una relazione tra le loro quantità.

## 8. Dimostrare che il linguaggio individuato è esatto

Per dimostrare:

$$
L(G)=L
$$

bisogna dimostrare entrambe le inclusioni:

$$
L(G)\subseteq L
$$

e:

$$
L\subseteq L(G)
$$

### Prima inclusione: nessuna stringa estranea

Dimostrare:

$$
L(G)\subseteq L
$$

significa mostrare che ogni stringa generata rispetta la descrizione proposta.

Per:

$$
S\to aSb\mid c
$$

ogni forma sentenziale contenente $S$ ha la forma:

$$
a^nSb^n
$$

Infatti:

- all’inizio abbiamo $S=a^0Sb^0$;
- ogni applicazione di $S\to aSb$ aumenta di uno entrambi gli esponenti;
- l’unica produzione che elimina $S$ lo sostituisce con `c`.

Ogni stringa terminale generata ha quindi la forma:

$$
a^ncb^n
$$

### Seconda inclusione: nessuna stringa mancante

Dimostrare:

$$
L\subseteq L(G)
$$

significa mostrare che ogni stringa descritta può essere generata.

Per qualsiasi $n\geq 0$, basta applicare:

- $n$ volte $S\to aSb$;
- una volta $S\to c$.

Otteniamo:

$$
S\Rightarrow^n a^nSb^n
\Rightarrow a^ncb^n
$$

Le due inclusioni dimostrano l’uguaglianza.

> [!tip]
> Lo stesso metodo serve quando costruisci una grammatica richiesta dall’esame:
>
> - non deve generare stringhe vietate;
> - deve poter generare tutte quelle ammesse.

## 9. Ordine di applicazione delle produzioni

Quando una forma sentenziale contiene più non terminali, può essere possibile scegliere quale sviluppare per primo.

Con:

$$
S\to AB,\qquad A\to a,\qquad B\to b
$$

possiamo avere:

$$
S\Rightarrow AB\Rightarrow aB\Rightarrow ab
$$

oppure:

$$
S\Rightarrow AB\Rightarrow Ab\Rightarrow ab
$$

La stringa finale è la stessa.

> [!warning]
> Due derivazioni diverse della stessa stringa non dimostrano, da sole, che una grammatica sia ambigua.
>
> Potrebbero differire soltanto nell’ordine in cui vengono sviluppati non terminali indipendenti. L’ambiguità sarà definita attraverso gli alberi di derivazione, oppure le derivazioni sinistre o destre.

## 10. Derivazioni che non producono frasi

Non tutte le sequenze di applicazioni portano a una stringa di soli terminali.

Consideriamo:

$$
S\to aS
$$

Possiamo continuare:

$$
S\Rightarrow aS\Rightarrow aaS\Rightarrow aaaS\Rightarrow\cdots
$$

ma non possiamo eliminare $S$.

Quindi:

$$
L(G)=\varnothing
$$

Esistono infinite forme sentenziali, ma nessuna frase.

Una sequenza infinita di applicazioni non genera una parola infinita appartenente al linguaggio: i linguaggi considerati qui contengono stringhe finite ottenute mediante derivazioni finite.

## 11. Controllo rapido

### Domanda 1

Se $S\Rightarrow^*aAb$, possiamo concludere che $aAb\in L(G)$?

**No**, se $A$ è un non terminale.

### Domanda 2

Se $S\to aS\mid b$, qual è il linguaggio?

$$
L(G)=\{a^n b\mid n\geq 0\}
$$

Ogni uso della prima produzione aggiunge una `a`; la seconda termina con `b`.

### Domanda 3

Se $S\to AB$, $A\to\varepsilon$ e $B\to\varepsilon$, viene generata ε?

**Sì:**

$$
S\Rightarrow AB\Rightarrow B\Rightarrow\varepsilon
$$

### Domanda 4

Per dimostrare $L(G)=L$ basta verificare alcune stringhe?

**No.** Bisogna giustificare entrambe le inclusioni.

## 12. Errori da evitare

- Applicare più produzioni contemporaneamente usando una sola freccia $\Rightarrow$.
- Eliminare un non terminale senza una produzione che lo permetta.
- Confondere il numero di passi con la lunghezza della stringa.
- Scambiare quantità indipendenti per quantità necessariamente uguali.
- Controllare le quantità dei simboli senza verificarne l’ordine.
- Dimenticare i casi con parametro zero.
- Considerare una derivazione infinita come una stringa generata.
- Descrivere il linguaggio usando soltanto esempi, senza una regola generale.
- Dimostrare una sola inclusione quando si afferma un’uguaglianza tra linguaggi.