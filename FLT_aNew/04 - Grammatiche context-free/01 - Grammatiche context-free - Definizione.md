# Grammatiche context-free - Definizione

## 1. Idea fondamentale

Una grammatica **context-free**, o **non contestuale**, permette di sostituire un non terminale indipendentemente dai simboli che lo circondano.

La caratteristica fondamentale riguarda la parte sinistra delle produzioni:

$$
A\to\alpha
$$

Deve contenere un solo non terminale.

La parte destra può invece contenere una combinazione arbitraria di terminali e non terminali, oppure essere vuota.

Le grammatiche context-free permettono di descrivere strutture ricorsive, annidamenti e relazioni tra quantità di simboli.

## 2. Definizione formale

Una grammatica context-free è una quadrupla:

$$
G=(N,\Sigma,P,S)
$$

dove:

- $N$ è un insieme finito di non terminali;
- $\Sigma$ è un alfabeto finito di terminali;
- $N\cap\Sigma=\varnothing$;
- $S\in N$ è il simbolo iniziale;
- $P$ è un insieme finito di produzioni della forma:

$$
A\to\alpha
$$

con:

$$
A\in N
$$

e:

$$
\alpha\in(N\cup\Sigma)^*
$$

Ponendo:

$$
V=N\cup\Sigma
$$

possiamo esprimere il vincolo sulle produzioni come:

$$
P\subseteq N\times V^*
$$

Più produzioni possono avere lo stesso non terminale a sinistra.

> [!important]
> Il vincolo “un solo non terminale” riguarda la parte sinistra.
>
> A destra possono comparire anche più non terminali.

## 3. Produzioni ammesse e non ammesse

Supponiamo che le maiuscole siano non terminali e le minuscole terminali.

| Produzione | Context-free? | Motivo |
|---|---|---|
| $S\to aSb$ | Sì | A sinistra compare soltanto $S$ |
| $S\to AB$ | Sì | Più non terminali a destra sono ammessi |
| $A\to aBCb$ | Sì | La parte destra può contenere terminali e non terminali |
| $A\to B$ | Sì | È una produzione unitaria |
| $A\to\varepsilon$ | Sì | La parte destra può essere vuota |
| $A\to abc$ | Sì | La parte destra può contenere soltanto terminali |
| $AB\to a$ | No | A sinistra compaiono due non terminali |
| $aA\to ab$ | No | La parte sinistra contiene anche un terminale |
| $\varepsilon\to a$ | No | A sinistra deve esserci un non terminale |

Una produzione può essere valida per una grammatica generale senza essere context-free.

## 4. Perché si chiama “non contestuale”

Se:

$$
A\to\alpha\in P
$$

allora, per qualsiasi:

$$
u,v\in V^*
$$

possiamo applicare:

$$
uAv\Rightarrow_G u\alpha v
$$

La scelta di sostituire $A$ non richiede che $u$ e $v$ abbiano una forma particolare.

Per esempio, con:

$$
A\to a
$$

sono applicazioni valide:

$$
aAb\Rightarrow aab
$$

e:

$$
bAa\Rightarrow baa
$$

La stessa produzione può essere applicata in contesti diversi.

### Che cosa non significa

“Non contestuale” non significa che il linguaggio sia privo di vincoli.

La grammatica:

$$
S\to aSb\mid\varepsilon
$$

impone che il numero delle `a` sia uguale al numero delle `b`.

Il vincolo emerge dalla struttura delle produzioni, anche se ciascuna sostituzione riguarda un singolo non terminale.

## 5. Derivazioni e linguaggio generato

La relazione:

$$
\Rightarrow_G
$$

indica l’applicazione di una produzione.

La relazione:

$$
\Rightarrow_G^*
$$

permette zero o più applicazioni.

Il linguaggio generato è:

$$
L(G)=
\{w\in\Sigma^*
\mid
S\Rightarrow_G^*w\}
$$

Una stringa appartiene a $L(G)$ soltanto se:

1. è derivabile da $S$;
2. contiene esclusivamente terminali.

### Esempio

Con:

$$
S\to aSb\mid\varepsilon
$$

abbiamo:

$$
S\Rightarrow aSb
\Rightarrow aaSbb
\Rightarrow aabb
$$

Quindi:

$$
aabb\in L(G)
$$

La forma intermedia `aaSbb` non appartiene al linguaggio perché contiene ancora $S$.

### Linguaggio generato da un non terminale

Per un qualsiasi $A\in N$, possiamo definire:

$$
L_G(A)=
\{w\in\Sigma^*
\mid
A\Rightarrow_G^*w\}
$$

In particolare:

$$
L(G)=L_G(S)
$$

Questa notazione permette di analizzare separatamente le componenti della grammatica.

## 6. Esempio: quantità collegate

Consideriamo:

$$
S\to aSb\mid\varepsilon
$$

Ogni applicazione ricorsiva aggiunge:

- una `a` a sinistra;
- una `b` a destra.

Dopo $n$ applicazioni:

$$
S\Rightarrow^n a^nSb^n
\Rightarrow a^nb^n
$$

Quindi:

$$
L(G)=\{a^nb^n\mid n\geq 0\}
$$

La scelta immediata di $S\to\varepsilon$ corrisponde a $n=0$.

Questo è un linguaggio context-free non regolare.

La grammatica mostra come una struttura ricorsiva possa mantenere una relazione tra blocchi separati.

## 7. Esempio: combinare componenti indipendenti

Consideriamo:

$$
S\to AB
$$

$$
A\to aAb\mid\varepsilon
$$

$$
B\to cB\mid\varepsilon
$$

Il non terminale $A$ genera:

$$
L_G(A)=\{a^nb^n\mid n\geq 0\}
$$

Il non terminale $B$ genera:

$$
L_G(B)=\{c^m\mid m\geq 0\}
$$

Poiché $S\to AB$, otteniamo:

$$
L(G)=L_G(A)L_G(B)
$$

e quindi:

$$
L(G)=
\{a^nb^nc^m\mid n,m\geq 0\}
$$

### Derivazione di abc

$$
S\Rightarrow AB
\Rightarrow aAbB
\Rightarrow abB
\Rightarrow abcB
\Rightarrow abc
$$

### Significato dei parametri

- $n$ è controllato dalla ricorsione di $A$;
- $m$ è controllato dalla ricorsione di $B$.

Le due quantità sono indipendenti.

> [!tip] Collegamento con l’esame
> Per costruire grammatiche di linguaggi con più vincoli, è spesso utile assegnare a non terminali diversi componenti con responsabilità distinte.

## 8. Ricorsione e terminazione

Una produzione è direttamente ricorsiva quando il non terminale a sinistra compare anche a destra.

Per esempio:

$$
A\to aAb
$$

La ricorsione è indiretta quando si ritorna al non terminale attraverso altre produzioni.

Per esempio:

$$
A\to aB
$$

$$
B\to bA\mid c
$$

### La ricorsione non garantisce un linguaggio infinito

Consideriamo:

$$
S\to S\mid a
$$

La grammatica è ricorsiva, ma genera soltanto:

$$
L(G)=\{a\}
$$

La produzione $S\to S$ non aggiunge terminali.

### La ricorsione deve poter terminare

Con la sola produzione:

$$
S\to aS
$$

possiamo ottenere infinite forme sentenziali:

$$
S,\quad aS,\quad aaS,\quad aaaS,\ldots
$$

ma nessuna frase terminale.

Quindi:

$$
L(G)=\varnothing
$$

> [!warning]
> Una produzione verso $\varepsilon$ permette di terminare senza aggiungere simboli.
>
> L’assenza di produzioni non equivale a una produzione verso $\varepsilon$: significa che non possiamo proseguire da quel non terminale.

## 9. Rapporto con le grammatiche regolari

Ogni grammatica regolare è anche context-free, perché le sue produzioni hanno un solo non terminale a sinistra.

Quindi:

$$
\mathrm{REG}\subsetneq\mathrm{CFL}
$$

dove $\mathrm{CFL}$ è la classe dei linguaggi context-free.

L’inclusione è propria perché:

$$
\{a^nb^n\mid n\geq 0\}
$$

è context-free ma non regolare.

### Una grammatica context-free può generare un linguaggio regolare

Per esempio:

$$
S\to AB
$$

$$
A\to a
$$

$$
B\to b
$$

genera:

$$
L(G)=\{ab\}
$$

Il linguaggio è regolare, anche se la grammatica fornita non è lineare destra.

Un linguaggio è context-free se esiste almeno una grammatica context-free che lo genera.

## 10. Collegamento con gli automi a pila

Vale l’equivalenza:

$$
L\text{ è context-free}
$$

se e solo se:

$$
L\text{ è riconoscibile da un automa a pila non deterministico}
$$

La pila permette di conservare informazioni sulle strutture ancora da completare.

Questo modello sarà approfondito nella cartella sugli automi a pila.

Non tutti i linguaggi context-free sono riconoscibili da un automa a pila deterministico.

## 11. Proprietà diverse da non confondere

| Proprietà | Che cosa riguarda |
|---|---|
| Essere context-free | La forma delle produzioni |
| Essere ambigua | La presenza di più alberi di derivazione per una stessa frase |
| Essere LR(0) o SLR(1) | La possibilità di usare uno specifico metodo di analisi deterministica |
| Generare un linguaggio regolare | Il linguaggio generato, non soltanto la forma della grammatica |

Una grammatica context-free non è automaticamente non ambigua o LR(0).

Una grammatica che non è LR(0) può comunque essere context-free.

## 12. Definizione da saper dare all’esame

Una grammatica context-free è una quadrupla:

$$
G=(N,\Sigma,P,S)
$$

con insiemi finiti e disgiunti di non terminali e terminali, simbolo iniziale $S\in N$ e un insieme finito di produzioni:

$$
A\to\alpha
$$

dove:

$$
A\in N,\qquad
\alpha\in(N\cup\Sigma)^*
$$

Il linguaggio generato è:

$$
L(G)=
\{w\in\Sigma^*\mid S\Rightarrow_G^*w\}
$$

Il punto caratterizzante è la presenza di un solo non terminale nella parte sinistra di ogni produzione.

## 13. Errori da evitare

- Imporre a destra lo stesso vincolo previsto a sinistra.
- Escludere produzioni come $S\to AB$.
- Considerare context-free una produzione con più simboli a sinistra.
- Pensare che “non contestuale” significhi “senza relazioni tra quantità”.
- Confondere una forma sentenziale con una frase terminale.
- Confondere una produzione verso $\varepsilon$ con l’assenza di produzioni.
- Concludere che la ricorsione implichi sempre un linguaggio infinito.
- Considerare ogni linguaggio context-free non regolare.
- Confondere context-free, non ambigua e LR(0).