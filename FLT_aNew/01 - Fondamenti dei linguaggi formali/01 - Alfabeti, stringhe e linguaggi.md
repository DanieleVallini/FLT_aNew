# Alfabeti, stringhe e linguaggi

## 1. Alfabeto

Un **alfabeto** è un insieme finito e non vuoto di simboli, generalmente indicato con $\Sigma$.

Esempi:

- $\Sigma = \{a,b\}$
- $\Sigma = \{0,1\}$
- $\Sigma = \{a,b,c,d\}$

I simboli sono gli elementi di base con cui si costruiscono le stringhe.

> [!important]
> Un simbolo non deve essere necessariamente una lettera: può rappresentare anche un token, come `id`, `if` o `while`. Conta come viene definito l’alfabeto.

## 2. Stringhe e lunghezza

Una **stringa**, o **parola**, su un alfabeto $\Sigma$ è una sequenza finita di simboli appartenenti a $\Sigma$.

Se $\Sigma = \{a,b\}$, sono stringhe su $\Sigma$:

$$
a,\quad b,\quad ab,\quad aab,\quad bbaa
$$

La **lunghezza** di una stringa $w$, indicata con $|w|$, è il numero di simboli che la compongono.

Per esempio:

$$
|aab|=3
$$

L’ordine dei simboli è importante:

$$
ab \neq ba
$$

Anche le ripetizioni sono importanti:

$$
ab \neq aab
$$

### Stringa vuota

La **stringa vuota**, indicata con $\varepsilon$, è l’unica stringa che non contiene simboli.

$$
|\varepsilon|=0
$$

> [!warning]
> $\varepsilon$ indica una stringa di lunghezza zero, non un simbolo dell’alfabeto.

## 3. Concatenazione di stringhe

La **concatenazione** di due stringhe $u$ e $v$ si ottiene scrivendo prima i simboli di $u$ e poi quelli di $v$.

Si indica con $uv$ oppure $u\cdot v$.

Esempio:

$$
u=ab,\qquad v=baa
$$

$$
uv=abbaa
$$

Valgono le seguenti proprietà:

- **Associatività:** $(uv)z=u(vz)$.
- **Elemento neutro:** $w\varepsilon=\varepsilon w=w$.
- **Lunghezza:** $|uv|=|u|+|v|$.

La concatenazione, in generale, **non è commutativa**:

$$
uv \neq vu
$$

### Potenza di una stringa

La notazione $w^n$ indica la concatenazione di $n$ copie di $w$.

Formalmente:

$$
w^0=\varepsilon
$$

$$
w^{n+1}=w^n w \qquad n\geq 0
$$

Esempi:

$$
a^3=aaa
$$

$$
(ab)^3=ababab
$$

Attenzione alla differenza:

$$
(ab)^2=abab
\qquad\text{mentre}\qquad
a^2b^2=aabb
$$

## 4. Gli insiemi Σⁿ, Σ* e Σ⁺

### Stringhe di lunghezza n

$\Sigma^n$ è l’insieme di tutte le stringhe di lunghezza $n$ sull’alfabeto $\Sigma$.

Se $\Sigma=\{a,b\}$:

$$
\Sigma^0=\{\varepsilon\}
$$

$$
\Sigma^1=\{a,b\}
$$

$$
\Sigma^2=\{aa,ab,ba,bb\}
$$

Se l’alfabeto contiene $k$ simboli, allora:

$$
|\Sigma^n|=k^n
$$

### Tutte le stringhe finite

$\Sigma^*$ è l’insieme di tutte le stringhe finite su $\Sigma$, compresa la stringa vuota:

$$
\Sigma^*=\bigcup_{n\geq 0}\Sigma^n
$$

$\Sigma^+$ contiene invece tutte le stringhe finite non vuote:

$$
\Sigma^+=\bigcup_{n\geq 1}\Sigma^n
=\Sigma^*\setminus\{\varepsilon\}
$$

Quindi:

$$
\varepsilon\in\Sigma^*
\qquad\text{e}\qquad
\varepsilon\notin\Sigma^+
$$

## 5. Linguaggio formale

Un **linguaggio formale** su un alfabeto $\Sigma$ è un qualsiasi sottoinsieme di $\Sigma^*$:

$$
L\subseteq\Sigma^*
$$

Un linguaggio è dunque un **insieme di stringhe**.

Può essere:

- **vuoto**, se non contiene stringhe;
- **finito**, se contiene un numero finito di stringhe;
- **infinito**, se contiene infinite stringhe.

Esempi su $\Sigma=\{a,b\}$:

$$
L_1=\{ab,baa\}
$$

$$
L_2=\{a^n b^n\mid n\geq 0\}
=\{\varepsilon,ab,aabb,aaabbb,\ldots\}
$$

Ogni stringa è finita anche quando il linguaggio contiene infinite stringhe.

### Appartenenza e inclusione

Per una stringa si usa il simbolo di appartenenza:

$$
ab\in L_2
$$

Per un linguaggio si usa il simbolo di inclusione:

$$
L_2\subseteq\Sigma^*
$$

## 6. Linguaggio vuoto e linguaggio contenente ε

Questi due linguaggi sono diversi:

$$
\varnothing \neq \{\varepsilon\}
$$

| Linguaggio | Contenuto | Numero di stringhe |
|---|---|---:|
| $\varnothing$ | Nessuna stringa | 0 |
| $\{\varepsilon\}$ | La sola stringa vuota | 1 |

In particolare:

$$
\varepsilon\notin\varnothing
$$

$$
\varepsilon\in\{\varepsilon\}
$$

## 7. Leggere i linguaggi descritti da una formula

Consideriamo:

$$
L=\{a^n b^{2n}\mid n\geq 1\}
$$

Una stringa appartiene a $L$ se:

1. contiene prima soltanto `a`, poi soltanto `b`;
2. contiene almeno una `a`;
3. il numero delle `b` è il doppio del numero delle `a`.

Esempi:

| Stringa | Appartiene a L? | Motivo |
|---|---|---|
| `abb` | Sì | $n=1$ |
| `aabbbb` | Sì | $n=2$ |
| `aabb` | No | Il numero delle `b` non è doppio |
| `bba` | No | L’ordine dei blocchi è sbagliato |
| $\varepsilon$ | No | È richiesto $n\geq 1$ |

> [!tip] Collegamento con l’esame
> Prima di costruire una grammatica o un automa, individua sempre:
> - l’ordine dei blocchi;
> - i vincoli sul numero dei simboli;
> - i valori minimi dei parametri;
> - l’eventuale presenza della stringa vuota.
>
> Scrivi poi alcune stringhe ammesse e alcune escluse: saranno utili per controllare la costruzione.

## 8. Definizioni da saper dare all’esame

**Alfabeto:** un insieme finito e non vuoto di simboli.

**Stringa su un alfabeto:** una sequenza finita di simboli dell’alfabeto.

**Stringa vuota:** l’unica stringa di lunghezza zero, indicata con $\varepsilon$.

**Linguaggio su un alfabeto $\Sigma$:** un sottoinsieme di $\Sigma^*$, dove $\Sigma^*$ è l’insieme di tutte le stringhe finite su $\Sigma$.

## 9. Errori da evitare

- Confondere un simbolo, una stringa e un insieme di stringhe.
- Confondere $\varnothing$ con $\{\varepsilon\}$.
- Dimenticare che $\Sigma^*$ contiene $\varepsilon$.
- Confondere $(ab)^n$ con $a^n b^n$.
- Controllare il numero dei simboli senza controllarne l’ordine.
- Ignorare la differenza tra i vincoli $n\geq 0$ e $n\geq 1$.