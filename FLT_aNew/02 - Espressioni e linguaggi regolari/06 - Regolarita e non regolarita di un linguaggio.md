# Regolarità e non regolarità di un linguaggio

## 1. Che cosa dobbiamo dimostrare

Un linguaggio $L$ è **regolare** se può essere descritto da almeno uno dei seguenti formalismi equivalenti:

- un’espressione regolare;
- una grammatica regolare;
- un automa finito.

Per dimostrare che un linguaggio è regolare basta quindi costruire una di queste descrizioni.

Per dimostrare che non è regolare, invece, bisogna escludere l’esistenza di qualsiasi automa finito equivalente.

> [!important]
> Non riuscire a trovare un’espressione regolare non dimostra che il linguaggio sia non regolare.
>
> Serve un argomento di impossibilità.

## 2. Prima ricavare il linguaggio, poi classificarlo

Se il linguaggio viene fornito attraverso una grammatica, bisogna prima capire quali stringhe genera.

Un procedimento utile è:

1. Individuare le produzioni ricorsive.
2. Contare quali simboli vengono aggiunti a ogni applicazione.
3. Individuare come termina la derivazione.
4. Controllare l’ordine dei blocchi.
5. Determinare i valori minimi dei parametri.
6. Formulare una descrizione generale di $L(G)$.
7. Dimostrare o escludere la regolarità.

Gli esempi brevi servono a controllare l’ipotesi, ma non sostituiscono la descrizione generale.

### La forma della grammatica non basta

La presenza di una produzione come:

$$
S\to aSb
$$

mostra che la grammatica non è lineare destra, ma non costituisce da sola una dimostrazione sulla classe del linguaggio.

Bisogna analizzare l’intera grammatica e il linguaggio generato.

## 3. Esempio di linguaggio regolare da una grammatica non regolare

Consideriamo:

$$
S\to aSa\mid\varepsilon
$$

Ogni applicazione della produzione ricorsiva aggiunge due `a`.

Quindi:

$$
L(G)=\{a^{2n}\mid n\geq 0\}
$$

Il linguaggio è regolare, perché è denotato da:

$$
(aa)^*
$$

Una grammatica lineare destra equivalente è:

$$
S\to aA\mid\varepsilon
$$

$$
A\to aS
$$

> [!warning]
> Una quantità ripetuta in due posizioni non implica automaticamente non regolarità.
>
> Qui i simboli sono tutti uguali e le due parti si fondono in un unico blocco di lunghezza pari.

## 4. L’idea della memoria finita

Un automa finito dispone di un numero finito di stati.

Può ricordare informazioni come:

- se è già comparso un simbolo;
- se ci troviamo prima o dopo un separatore;
- se un conteggio è pari o dispari;
- il resto di un conteggio modulo un intero fissato.

Non può invece conservare arbitrariamente molte informazioni tutte distinguibili tra loro.

### Esempi

| Linguaggio | Regolare? | Idea |
|---|---|---|
| $\{a^{2n}\mid n\geq 0\}$ | Sì | Basta ricordare la parità |
| $\{a^n b^m\mid n,m\geq 0\}$ | Sì | Basta distinguere la fase delle `a` da quella delle `b` |
| $\{a^n b^n\mid n\geq 0\}$ | No | Occorre confrontare due quantità senza limite |
| $\{a^n b^n\mid 0\leq n\leq 10\}$ | Sì | Il linguaggio è finito |

Questa intuizione aiuta a scegliere una dimostrazione, ma da sola non è sempre sufficiente.

## 5. Pumping lemma per i linguaggi regolari

Il **pumping lemma** fornisce una proprietà necessaria di ogni linguaggio regolare.

### Enunciato

Se $L$ è regolare, allora esiste un intero:

$$
p\geq 1
$$

tale che ogni stringa $w\in L$ con:

$$
|w|\geq p
$$

può essere scomposta come:

$$
w=xyz
$$

rispettando:

$$
|xy|\leq p
$$

$$
|y|\geq 1
$$

e:

$$
xy^iz\in L
\qquad
\text{per ogni }i\geq 0
$$

### Interpretazione

La parte $y$ può essere ripetuta un numero arbitrario di volte, anche zero, senza uscire dal linguaggio.

Negli automi finiti questo deriva dalla presenza di un ciclo durante la lettura di una stringa sufficientemente lunga.

### Ruolo delle condizioni

- $|xy|\leq p$: la parte ripetibile si trova entro i primi $p$ simboli.
- $|y|\geq 1$: la parte ripetibile non è vuota.
- $i=0$: possiamo eliminare $y$.
- $i=2$: possiamo duplicare $y$.

Le parti $x$ e $z$ possono essere vuote.

> [!important]
> Il pumping lemma è una condizione necessaria, non una caratterizzazione completa della regolarità.
>
> Si usa soprattutto per dimostrare la non regolarità.

## 6. Come usare correttamente il pumping lemma

Per dimostrare che $L$ non è regolare:

1. Supponi per assurdo che $L$ sia regolare.
2. Considera la lunghezza di pompaggio $p$ garantita dal lemma.
3. Scegli una stringa $w\in L$ con $|w|\geq p$.
4. Considera una scomposizione arbitraria $w=xyz$ che soddisfi i vincoli.
5. Mostra che esiste un valore $i\geq 0$ per cui $xy^iz\notin L$.
6. Concludi che il lemma è violato e quindi $L$ non è regolare.

### L’ordine delle scelte è fondamentale

Tu scegli la stringa $w$, dopo aver considerato $p$.

Non puoi scegliere una sola scomposizione conveniente e ignorare le altre: il ragionamento deve funzionare per tutte le scomposizioni ammesse.

Puoi poi scegliere il valore di $i$ che produce la contraddizione.

> [!warning]
> Dimostrare che una particolare scomposizione non funziona non basta.
>
> Il lemma richiede l’esistenza di una scomposizione valida: devi escluderle tutte.

## 7. Esempio fondamentale: aⁿbⁿ

Consideriamo:

$$
L=\{a^n b^n\mid n\geq 0\}
$$

Supponiamo per assurdo che sia regolare e sia $p$ la lunghezza di pompaggio.

Scegliamo:

$$
w=a^p b^p
$$

La stringa appartiene a $L$ e ha lunghezza almeno $p$.

Per ogni scomposizione:

$$
w=xyz
$$

con:

$$
|xy|\leq p,\qquad |y|\geq 1
$$

la parte $y$ contiene soltanto `a`, perché i primi $p$ simboli sono tutti `a`.

Quindi:

$$
y=a^t
\qquad
1\leq t\leq p
$$

Scegliendo $i=0$ otteniamo:

$$
xz=a^{p-t}b^p
$$

Il numero delle `a` è diminuito, mentre quello delle `b` è rimasto invariato.

Dunque:

$$
xz\notin L
$$

Contraddizione con il pumping lemma.

Pertanto:

$$
L\notin\mathrm{REG}
$$

## 8. Esempio d’esame: gennaio 2026

La grammatica è:

$$
S\to ASB\mid\varepsilon
$$

$$
A\to a\mid aA
$$

$$
B\to b\mid bb
$$

La richiesta è stabilire se il linguaggio sia regolare e, in caso positivo, fornire un’espressione regolare e una grammatica lineare destra.

### Passaggio 1: capire A e B

Il non terminale $A$ genera una o più `a`:

$$
L(A)=\{a^n\mid n\geq 1\}
$$

Ogni occorrenza di $B$ genera una oppure due `b`.

### Passaggio 2: contare i livelli ricorsivi

Dopo $k$ applicazioni di:

$$
S\to ASB
$$

e la successiva terminazione con $S\to\varepsilon$, otteniamo:

$$
A^kB^k
$$

Se $k=0$, viene generata soltanto $\varepsilon$.

Se $k\geq 1$:

- i $k$ non terminali $A$ producono complessivamente almeno $k$ `a`;
- i $k$ non terminali $B$ producono da $k$ a $2k$ `b`.

Scrivendo la stringa come:

$$
a^n b^m
$$

abbiamo quindi:

$$
n\geq k
$$

$$
k\leq m\leq 2k
$$

In particolare:

$$
m\leq 2n
$$

### Passaggio 3: descrivere esattamente il linguaggio

Otteniamo:

$$
L(G)=
\{\varepsilon\}
\cup
\{a^n b^m\mid n,m\geq 1,\ m\leq 2n\}
$$

Il vincolo è anche sufficiente.

Infatti, dati $n,m\geq 1$ con $m\leq 2n$, possiamo scegliere:

$$
k=\left\lceil\frac m2\right\rceil
$$

dove $\lceil x\rceil$ indica il più piccolo intero maggiore o uguale a $x$.

Allora $k\leq n$ e $k\leq m\leq 2k$:

- i $k$ simboli $A$ possono produrre esattamente $n$ `a`;
- i $k$ simboli $B$ possono produrre esattamente $m$ `b`.

### Controllo con alcune stringhe

| Stringa | Appartiene? | Motivo |
|---|---|---|
| $\varepsilon$ | Sì | $S\to\varepsilon$ |
| `abb` | Sì | Una `a` e due `b` |
| `aab` | Sì | Un solo $A$ può produrre due `a` |
| `aabbbb` | Sì | Due livelli, ciascun $B$ produce `bb` |
| `abbb` | No | Tre `b` superano il doppio delle `a` |
| `a` | No | Ogni livello ricorsivo introduce anche un $B$ |

### Passaggio 4: dimostrare la non regolarità

Supponiamo per assurdo che il linguaggio sia regolare e sia $p$ la lunghezza di pompaggio.

Scegliamo:

$$
w=a^p b^{2p}
$$

La stringa appartiene al linguaggio: rispetta il vincolo con uguaglianza.

Ogni scomposizione ammessa ha:

$$
y=a^t
\qquad
t\geq 1
$$

perché $|xy|\leq p$.

Pompando con $i=0$ otteniamo:

$$
xz=a^{p-t}b^{2p}
$$

Ma:

$$
2p>2(p-t)
$$

Il numero delle `b` supera il doppio del numero delle `a`.

La stringa non appartiene al linguaggio e non è nemmeno $\varepsilon$, perché contiene ancora tutte le `b`.

Abbiamo una contraddizione.

Quindi:

$$
L(G)\notin\mathrm{REG}
$$

Non esistono dunque un’espressione regolare o una grammatica lineare destra equivalenti.

> [!important]
> Un vincolo di disuguaglianza può rendere un linguaggio non regolare.
>
> Non bisogna cercare soltanto uguaglianze come “stesso numero di `a` e `b`”.

## 9. Metodo alternativo: prefissi distinguibili

Un altro metodo consiste nel mostrare che un automa dovrebbe distinguere infinite situazioni.

Due prefissi $u$ e $v$ sono distinguibili se esiste un suffisso $z$ tale che:

- $uz$ appartiene al linguaggio;
- $vz$ non appartiene, o viceversa.

Se un automa deterministico raggiungesse lo stesso stato dopo $u$ e $v$, leggendo lo stesso suffisso $z$ dovrebbe dare lo stesso risultato.

Quindi prefissi distinguibili devono condurre a stati diversi.

Se esistono infiniti prefissi a due a due distinguibili, nessun automa finito può riconoscere il linguaggio.

### Esempio

Per:

$$
L=\{a^n b^n\mid n\geq 0\}
$$

consideriamo i prefissi:

$$
\varepsilon,a,a^2,a^3,\ldots
$$

Per $i<j$, il suffisso $b^i$ distingue $a^i$ da $a^j$:

$$
a^ib^i\in L
$$

$$
a^jb^i\notin L
$$

Servirebbero quindi infiniti stati.

## 10. Usare le proprietà di chiusura

Un’ulteriore tecnica è supporre che $L$ sia regolare e applicare un’operazione che conserva la regolarità.

Se il risultato è un linguaggio noto come non regolare, si ottiene una contraddizione.

Per esempio:

$$
L\in\mathrm{REG}
\land
R\in\mathrm{REG}
\implies
L\cap R\in\mathrm{REG}
$$

Scegliendo opportunamente $R$, possiamo isolare una parte difficile di $L`.

Il procedimento è approfondito nella nota sulle proprietà di chiusura.

## 11. Come organizzare una risposta d’esame

### Se il linguaggio è regolare

1. Descrivi il linguaggio.
2. Motiva la descrizione.
3. Fornisci una costruzione che dimostri la regolarità.
4. Se richiesto, scrivi sia l’espressione regolare sia la grammatica lineare destra.

### Se il linguaggio non è regolare

1. Descrivi il linguaggio e i suoi vincoli.
2. Scegli un metodo di dimostrazione.
3. Presenta la contraddizione in modo esplicito.
4. Concludi che non può esistere una descrizione regolare equivalente.

Per una domanda da pochi punti, una dimostrazione breve ma completa è preferibile a molti esempi senza un argomento generale.

## 12. Errori da evitare

- Confondere una grammatica non regolare con un linguaggio non regolare.
- Considerare l’infinità del linguaggio come prova di non regolarità.
- Affermare genericamente “serve memoria” senza giustificare il vincolo.
- Scegliere soltanto una scomposizione nel pumping lemma.
- Dimenticare che $y$ deve essere non vuota.
- Usare soltanto $|y|\leq p$ invece del vincolo completo $|xy|\leq p$.
- Scegliere una stringa che non appartiene al linguaggio.
- Dimenticare i casi limite, soprattutto $\varepsilon$.
- Pensare che soddisfare alcuni esempi di pompaggio dimostri la regolarità.
- Tentare di fornire una grammatica lineare destra dopo aver dimostrato che il linguaggio non è regolare.