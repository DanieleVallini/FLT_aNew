# Grammatiche generative

## 1. A cosa serve una grammatica

Una **grammatica generativa** descrive un linguaggio attraverso un insieme finito di regole.

Si parte da un simbolo iniziale e si applicano le regole per ottenere stringhe del linguaggio.

Per esempio, per generare:

$$
L=\{a^n b^n\mid n\geq 0\}
$$

possiamo utilizzare:

$$
S\to aSb
\qquad
S\to\varepsilon
$$

La prima regola aggiunge una `a` a sinistra e una `b` a destra; la seconda termina la costruzione.

Una possibile generazione è:

$$
S\Rightarrow aSb\Rightarrow aaSbb\Rightarrow aabb
$$

La grammatica non elenca tutte le stringhe: descrive un procedimento per costruirle.

## 2. Definizione formale

Adottiamo la convenzione:

$$
G=(N,\Sigma,P,S)
$$

dove:

- $N$ è un insieme finito di **simboli non terminali**;
- $\Sigma$ è un alfabeto di **simboli terminali**, disgiunto da $N$;
- $P$ è un insieme finito di **produzioni**;
- $S\in N$ è il **simbolo iniziale**, detto anche **assioma**.

Vale quindi:

$$
N\cap\Sigma=\varnothing
$$

Indichiamo con:

$$
V=N\cup\Sigma
$$

l’insieme di tutti i simboli della grammatica.

> [!note]
> Alcuni testi indicano i non terminali con $V_N$ e i terminali con $V_T$, oppure scrivono le componenti della quadrupla in un ordine diverso.
>
> L’importante è dichiarare chiaramente la convenzione utilizzata.

## 3. Terminali e non terminali

### Simboli terminali

I terminali sono i simboli che possono comparire nelle stringhe del linguaggio generato.

Esempi:

$$
a,\quad b,\quad c
$$

Una stringa del linguaggio deve appartenere a $\Sigma^*$: non può contenere non terminali.

### Simboli non terminali

I non terminali rappresentano strutture ancora da sviluppare mediante le produzioni.

Per convenzione si indicano spesso con lettere maiuscole:

$$
S,\quad A,\quad B
$$

Per esempio:

$$
S\Rightarrow aSb
$$

La stringa `aSb` contiene ancora il non terminale $S$: è una tappa intermedia, non una stringa del linguaggio generato.

> [!warning]
> La distinzione dipende dagli insiemi $N$ e $\Sigma$, non dalla forma grafica dei simboli.
>
> Usare maiuscole per i non terminali e minuscole per i terminali è una convenzione.

## 4. Produzioni

Una produzione si scrive:

$$
\alpha\to\beta
$$

e consente di sostituire un’occorrenza di $\alpha$ con $\beta$.

In una grammatica generativa generale:

$$
\alpha\in V^*NV^*
$$

$$
\beta\in V^*
$$

Questo significa che:

- la parte sinistra $\alpha$ contiene almeno un non terminale;
- la parte destra $\beta$ è una stringa di terminali e non terminali, eventualmente vuota.

Sono quindi possibili, in una grammatica generale, produzioni come:

$$
S\to aSb
$$

$$
AB\to a
$$

$$
aA\to ab
$$

$$
A\to\varepsilon
$$

Le diverse classi di grammatiche impongono ulteriori restrizioni alla forma delle produzioni.

Per esempio, nelle grammatiche **context-free**, a sinistra compare un solo non terminale:

$$
A\to\beta
$$

### Produzioni alternative

La scrittura:

$$
S\to aSb\mid\varepsilon
$$

è un’abbreviazione delle due produzioni:

$$
S\to aSb
$$

$$
S\to\varepsilon
$$

Il simbolo `|` separa alternative: non è un simbolo da inserire nella stringa generata.

## 5. Applicazione di una produzione

Se:

$$
\alpha\to\beta\in P
$$

allora possiamo effettuare la sostituzione:

$$
u\alpha v\Rightarrow_G u\beta v
$$

con:

$$
u,v\in V^*
$$

Le parti $u$ e $v$ restano invariate: viene sostituita soltanto l’occorrenza scelta di $\alpha$.

Formalmente:

$$
x\Rightarrow_G y
$$

se e solo se esistono $u,v,\alpha,\beta$ tali che:

$$
x=u\alpha v,\qquad
y=u\beta v,\qquad
\alpha\to\beta\in P
$$

La relazione $\Rightarrow_G$ è la **derivazione in un passo**.

Quando la grammatica è chiara dal contesto, si omette il pedice $G$.

### Esempio

Data la produzione:

$$
S\to aSb
$$

nella stringa `aaSbb` possiamo sostituire $S$ con `aSb`:

$$
aaSbb\Rightarrow aaaSbbb
$$

Qui:

$$
u=aa,\qquad \alpha=S,\qquad v=bb
$$

## 6. Derivazione in più passi

Una derivazione è una sequenza di applicazioni di produzioni.

La notazione:

$$
x\Rightarrow_G^* y
$$

significa che da $x$ si può ottenere $y$ in **zero o più passi**.

In particolare:

$$
x\Rightarrow_G^* x
$$

è sempre vero, perché sono ammessi zero passi.

La notazione:

$$
x\Rightarrow_G^+ y
$$

richiede invece **uno o più passi**.

Esempio:

$$
S\Rightarrow aSb\Rightarrow aaSbb\Rightarrow aabb
$$

Quindi:

$$
S\Rightarrow^* aabb
$$

e anche:

$$
S\Rightarrow^+ aabb
$$

> [!warning]
> Non confondere:
>
> - $\to$: una produzione appartenente alla grammatica;
> - $\Rightarrow$: l’applicazione di una produzione all’interno di una stringa;
> - $\Rightarrow^*$: una derivazione di zero o più passi.

## 7. Forme sentenziali e frasi

Una **forma sentenziale** è una stringa di terminali e non terminali derivabile dal simbolo iniziale:

$$
\gamma\in V^*
\quad\text{tale che}\quad
S\Rightarrow^*\gamma
$$

Una **frase** è una forma sentenziale costituita soltanto da terminali.

Nella derivazione:

$$
S\Rightarrow aSb\Rightarrow aaSbb\Rightarrow aabb
$$

abbiamo:

| Stringa | Forma sentenziale? | Frase? |
|---|---|---|
| $S$ | Sì | No |
| $aSb$ | Sì | No |
| $aaSbb$ | Sì | No |
| $aabb$ | Sì | Sì |

Anche $S$ è una forma sentenziale, perché:

$$
S\Rightarrow^* S
$$

in zero passi.

## 8. Linguaggio generato

Il linguaggio generato da una grammatica $G$ è:

$$
L(G)=\{w\in\Sigma^*\mid S\Rightarrow_G^*w\}
$$

Questa definizione richiede contemporaneamente che:

1. $w$ sia derivabile dal simbolo iniziale;
2. $w$ contenga soltanto terminali.

> [!important]
> Essere derivabile da $S$ non basta: una stringa contenente ancora non terminali non appartiene a $L(G)$.

### Presenza della stringa vuota

La stringa vuota appartiene al linguaggio se e solo se:

$$
S\Rightarrow_G^*\varepsilon
$$

Non è necessario che esista direttamente la produzione $S\to\varepsilon$.

Per esempio:

$$
S\to A
\qquad
A\to\varepsilon
$$

permettono la derivazione:

$$
S\Rightarrow A\Rightarrow\varepsilon
$$

## 9. Esempio completo

Consideriamo:

$$
N=\{S\}
$$

$$
\Sigma=\{a,b\}
$$

$$
P=\{S\to aSb,\ S\to\varepsilon\}
$$

Il simbolo iniziale è $S$.

Le prime stringhe generate sono:

| Applicazioni di S → aSb | Derivazione conclusiva | Stringa |
|---:|---|---|
| 0 | $S\Rightarrow\varepsilon$ | $\varepsilon$ |
| 1 | $S\Rightarrow aSb\Rightarrow ab$ | `ab` |
| 2 | $S\Rightarrow aSb\Rightarrow aaSbb\Rightarrow aabb$ | `aabb` |
| 3 | $S\Rightarrow aSb\Rightarrow aaSbb\Rightarrow aaaSbbb\Rightarrow aaabbb$ | `aaabbb` |

Quindi:

$$
L(G)=\{a^n b^n\mid n\geq 0\}
$$

Il motivo è che:

- ogni applicazione di $S\to aSb$ aggiunge esattamente una `a` e una `b`;
- tutte le `a` precedono tutte le `b`;
- per ottenere una stringa di soli terminali bisogna infine applicare $S\to\varepsilon$;
- qualsiasi numero $n\geq 0$ di applicazioni della prima produzione è possibile.

Questi argomenti spiegano sia perché le stringhe generate rispettano il vincolo, sia perché ogni stringa che lo rispetta può essere generata.

## 10. Grammatica e linguaggio sono oggetti diversi

La grammatica è il sistema di regole; il linguaggio è l’insieme delle stringhe generate.

Due grammatiche sono **equivalenti** se generano lo stesso linguaggio:

$$
L(G_1)=L(G_2)
$$

Possono essere equivalenti anche se hanno produzioni e non terminali diversi.

### Attenzione alla regolarità

Dalla presenza di una produzione non regolare non possiamo concludere che il linguaggio generato non sia regolare.

Per esempio:

$$
S\to AB
\qquad
A\to a
\qquad
B\to b
$$

La produzione $S\to AB$ non ha la forma di una produzione regolare, ma:

$$
L(G)=\{ab\}
$$

è un linguaggio regolare.

> [!important] Collegamento con l’esame
> Alla domanda «Il linguaggio generato è regolare?» bisogna analizzare il linguaggio.
>
> Osservare soltanto la forma della grammatica fornita può non essere sufficiente.

## 11. Definizioni da saper dare all’esame

**Grammatica generativa:** una quadrupla $G=(N,\Sigma,P,S)$, con insiemi finiti e disgiunti di non terminali e terminali, un insieme finito di produzioni e un simbolo iniziale $S\in N$.

**Produzione generale:** una regola $\alpha\to\beta$, con $\alpha\in V^*NV^*$ e $\beta\in V^*$, dove $V=N\cup\Sigma$.

**Derivazione in un passo:** $u\alpha v\Rightarrow_G u\beta v$ quando $\alpha\to\beta$ è una produzione di $G$.

**Linguaggio generato:**

$$
L(G)=\{w\in\Sigma^*\mid S\Rightarrow_G^*w\}
$$

## 12. Errori da evitare

- Confondere la grammatica con il linguaggio generato.
- Considerare una forma sentenziale con non terminali come una stringa di $L(G)$.
- Pensare che ogni grammatica debba avere un solo non terminale a sinistra delle produzioni: questo vincolo caratterizza le grammatiche context-free.
- Confondere $\to$, $\Rightarrow$ e $\Rightarrow^*$.
- Dimenticare che $\Rightarrow^*$ ammette zero passi.
- Escludere $\varepsilon$ senza controllare se sia derivabile.
- Concludere che un linguaggio non sia regolare soltanto perché la grammatica fornita non è regolare.