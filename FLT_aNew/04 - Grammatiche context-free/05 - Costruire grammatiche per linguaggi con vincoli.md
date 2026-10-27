# Costruire grammatiche per linguaggi con vincoli

Costruire una grammatica per un linguaggio significa scegliere produzioni che generino **tutte e sole le parole richieste**.

Il problema è inverso rispetto a quello della nota precedente:

- prima partivamo dalle produzioni e cercavamo il linguaggio;
- ora partiamo dal linguaggio e dobbiamo progettare le produzioni.

> [!important] Obiettivo
> Dato un linguaggio $L$, dobbiamo costruire una grammatica $G$ tale che:
> $$
> L(G)=L.
> $$
> Generare alcune parole corrette non basta: le produzioni devono anche impedire la generazione di parole estranee a $L$.

---

## 1. Tradurre i vincoli in scelte grammaticali

Prima di scrivere le produzioni, individuiamo:

1. **l’ordine dei blocchi:** per esempio, prima tutte le `a`, poi tutte le `b`;
2. **i parametri indipendenti:** quantità che possono essere scelte separatamente;
3. **le quantità collegate:** uguaglianze, multipli o somme;
4. **i valori minimi:** zero, uno oppure un numero maggiore;
5. **i simboli fissi:** separatori o terminali che devono comparire esattamente una volta.

Per esempio:

$$
L=\{a^nb^{2n}\mid n\geq1\}
$$

richiede:

- un blocco di `a` seguito da un blocco di `b`;
- due `b` per ogni `a`;
- almeno una `a`.

La produzione ricorsiva naturale è:

$$
A\to aAbb.
$$

Ogni applicazione aggiunge una `a` a sinistra e due `b` a destra.

Per imporre almeno un gruppo, scegliamo come caso base:

$$
A\to abb.
$$

Otteniamo:

$$
\boxed{A\to aAbb\mid abb.}
$$

---

## 2. Schemi fondamentali da riconoscere

Nella tabella, $n$ è un intero non negativo, salvo diversa indicazione.

| Linguaggio desiderato | Produzioni possibili |
|---|---|
| $\{a^n\mid n\geq0\}$ | $A\to aA\mid\varepsilon$ |
| $\{a^n\mid n\geq1\}$ | $A\to aA\mid a$ |
| $\{a^nb^n\mid n\geq0\}$ | $A\to aAb\mid\varepsilon$ |
| $\{a^nb^n\mid n\geq1\}$ | $A\to aAb\mid ab$ |
| $\{a^nb^{2n}\mid n\geq0\}$ | $A\to aAbb\mid\varepsilon$ |
| $\{a^nb^{2n}\mid n\geq1\}$ | $A\to aAbb\mid abb$ |
| $\{a^{n+1}b^n\mid n\geq0\}$ | $A\to aAb\mid a$ |

Questi schemi funzionano perché ogni passo ricorsivo aggiunge un contributo preciso ai due lati.

> [!tip] Il caso base determina il valore minimo
> La ricorsione descrive come aumentano i conteggi.
> Il caso base stabilisce da dove iniziano.
>
> Per questo sostituire `abb` con $\varepsilon$ cambia il vincolo da $n\geq1$ a $n\geq0$.

---

## 3. Usare la concatenazione per scelte indipendenti

Se un linguaggio è formato concatenando una parola di $L_1$ con una parola di $L_2$, possiamo introdurre:

$$
S\to AB,
$$

dove $A$ genera $L_1$ e $B$ genera $L_2$.

Per esempio:

$$
L=\{a^nb^{2n}c^m\mid n\geq1,\ m\geq0\}.
$$

Possiamo costruire:

$$
S\to AB
$$

$$
A\to aAbb\mid abb
$$

$$
B\to cB\mid\varepsilon.
$$

Il parametro $n$ viene scelto attraverso le espansioni di $A$, mentre $m$ viene scelto attraverso quelle di $B$.

Non esiste alcun vincolo tra i due.

> [!warning] Separare troppo può perdere un vincolo
> Per generare $a^nb^n$ non basta assegnare le `a` a un non terminale e le `b` a un altro, entrambi liberi.
>
> Le due quantità devono essere collegate dalle produzioni.

---

## 4. Gestire un esponente che contiene una somma

Consideriamo:

$$
L=\{a^nb^{n+m}c^m\mid n,m\geq0\}.
$$

Il blocco centrale contiene due contributi:

$$
b^{n+m}=b^nb^m.
$$

Possiamo quindi riscrivere ogni parola come:

$$
\underbrace{a^nb^n}_{\text{primo vincolo}}
\underbrace{b^mc^m}_{\text{secondo vincolo}}.
$$

Una grammatica è:

$$
S\to AB
$$

$$
A\to aAb\mid\varepsilon
$$

$$
B\to bBc\mid\varepsilon.
$$

Infatti:

$$
S\Rightarrow AB
\Rightarrow^*a^nb^nb^mc^m
=a^nb^{n+m}c^m.
$$

I due blocchi di `b`, essendo adiacenti, formano un unico blocco.

> [!important] Strategia per gli esponenti con somme
> Cerca di assegnare ogni addendo al vincolo che lo produce.
>
> Per esempio, in $b^{2n+2m}$ un gruppo di produzioni può generare $b^{2n}$ e un altro $b^{2m}$.

---

## 5. Esercizio d’esame: analizzare il linguaggio richiesto

Nella prova del 24 giugno 2025 viene chiesta una grammatica per:

$$
L=
\left\{
a^n b^{2n+2m} c d^{k+1} e^k a^m
\;\middle|\;
n,m\geq1,\ k\geq0
\right\}.
$$

Analizziamo separatamente i vincoli.

| Parte della parola | Vincolo |
|---|---|
| $a^n$ iniziale | Almeno una `a` |
| $b^{2n+2m}$ | Due `b` per ogni `a` iniziale e due per ogni `a` finale |
| $c$ | Esattamente una |
| $d^{k+1}e^k$ | Una `d` in più rispetto alle `e` |
| $a^m$ finale | Almeno una `a` |

I parametri $n$, $m$ e $k$ sono indipendenti, ma ciascuno collega alcune parti della parola.

### Scomposizione utile

Separiamo il contributo $2n$ dal contributo $2m$:

$$
a^n b^{2n+2m} c d^{k+1} e^k a^m
=
\underbrace{a^nb^{2n}}_{\text{generato da }A}
\underbrace{b^{2m}c\,d^{k+1}e^ka^m}_{\text{generato da }B}.
$$

Dentro la parte generata da $B$ troviamo:

$$
\underbrace{b^{2m}}_{\text{legato ad }a^m}
\quad
c
\quad
\underbrace{d^{k+1}e^k}_{\text{generato da }C}
\quad
\underbrace{a^m}_{\text{legato a }b^{2m}}.
$$

La struttura suggerisce:

- concatenazione tra $A$ e $B$;
- ricorsione di $B$ attorno alla parte centrale;
- un non terminale $C$ per gestire `d` ed `e`.

---

## 6. Costruire i tre componenti

### Componente A: collegare le `a` iniziali a una parte delle `b`

Vogliamo:

$$
L_G(A)=\{a^nb^{2n}\mid n\geq1\}.
$$

Usiamo:

$$
\boxed{A\to aAbb\mid abb.}
$$

Il caso base corrisponde a $n=1$.

Ogni applicazione ricorsiva incrementa $n$ di uno.

### Componente C: generare una `d` in più delle `e`

Vogliamo:

$$
L_G(C)=\{d^{k+1}e^k\mid k\geq0\}.
$$

Usiamo:

$$
\boxed{C\to dCe\mid d.}
$$

Il caso base genera:

$$
d=d^{0+1}e^0.
$$

Ogni passo ricorsivo aggiunge una `d` e una `e`, conservando la differenza di uno:

$$
C\Rightarrow dCe\Rightarrow ddCee\Rightarrow dddee.
$$

L’ultima parola corrisponde a $k=2$.

### Componente B: collegare le altre `b` alle `a` finali

Vogliamo:

$$
L_G(B)=
\{b^{2m}c\,d^{k+1}e^ka^m
\mid m\geq1,\ k\geq0\}.
$$

La produzione ricorsiva deve aggiungere:

- due `b` a sinistra;
- una `a` a destra.

Quindi:

$$
B\to bbBa.
$$

Per imporre $m\geq1$, il caso base deve già contenere due `b` e una `a`:

$$
B\to bb\,c\,C\,a.
$$

Otteniamo:

$$
\boxed{B\to bbBa\mid bb\,c\,C\,a.}
$$

---

## 7. Grammatica completa

Una soluzione è:

$$
G=(N,\Sigma,P,S),
$$

dove:

$$
N=\{S,A,B,C\},
$$

$$
\Sigma=\{a,b,c,d,e\},
$$

e le produzioni sono:

$$
\boxed{
\begin{aligned}
S&\to AB\\
A&\to aAbb\mid abb\\
B&\to bbBa\mid bb\,c\,C\,a\\
C&\to dCe\mid d.
\end{aligned}
}
$$

Tutte le produzioni hanno un solo non terminale a sinistra: la grammatica è context-free.

> [!tip] Ogni non terminale ha un compito preciso
> - $A$ collega le `a` iniziali a $2n$ delle `b`.
> - $B$ collega le `a` finali alle altre $2m$ `b`.
> - $C$ impone una `d` in più delle `e`.
> - $S$ concatena i componenti nell’ordine richiesto.

---

## 8. Verificare una derivazione concreta

Scegliamo:

$$
n=2,\qquad m=1,\qquad k=1.
$$

La parola desiderata è:

$$
a^2b^6cd^2ea.
$$

Una derivazione è:

$$
\begin{aligned}
S
&\Rightarrow AB\\
&\Rightarrow aAbbB\\
&\Rightarrow a^2b^4B\\
&\Rightarrow a^2b^6cCa\\
&\Rightarrow a^2b^6cdCea\\
&\Rightarrow a^2b^6cd^2ea.
\end{aligned}
$$

Abbiamo usato:

- una volta la ricorsione di $A$, poi il suo caso base;
- direttamente il caso base di $B$;
- una volta la ricorsione di $C$, poi il suo caso base.

La parola terminale è `aabbbbbbcddea`.

Le potenze nella derivazione sono soltanto abbreviazioni delle sequenze di terminali.

---

## 9. Giustificare che la grammatica genera esattamente L

### Ogni parola generata rispetta i vincoli

Le produzioni di $A$ generano esattamente:

$$
a^nb^{2n},
\qquad n\geq1.
$$

Per $B$, applicando $m-1$ volte la produzione ricorsiva e poi il caso base:

$$
B\Rightarrow^*
b^{2(m-1)}Ba^{m-1}
\Rightarrow
b^{2m}cCa^m.
$$

Infine:

$$
C\Rightarrow^*d^{k+1}e^k,
\qquad k\geq0.
$$

Di conseguenza:

$$
S\Rightarrow AB
\Rightarrow^*
a^nb^{2n}b^{2m}cd^{k+1}e^ka^m.
$$

Unendo i blocchi adiacenti di `b`:

$$
a^nb^{2n+2m}cd^{k+1}e^ka^m.
$$

Quindi:

$$
L(G)\subseteq L.
$$

### Ogni parola richiesta può essere generata

Fissiamo arbitrariamente:

$$
n,m\geq1,
\qquad k\geq0.
$$

Possiamo scegliere:

- $n-1$ applicazioni ricorsive di $A$, poi il caso base;
- $m-1$ applicazioni ricorsive di $B$, poi il caso base;
- $k$ applicazioni ricorsive di $C$, poi il caso base.

Otteniamo la parola corrispondente a quei parametri.

Quindi:

$$
L\subseteq L(G).
$$

Concludiamo:

$$
\boxed{L(G)=L.}
$$

---

## 10. Controllare i valori minimi

Il caso minimo ammesso è:

$$
n=1,\qquad m=1,\qquad k=0.
$$

La parola risultante è:

$$
ab^4cda,
$$

cioè `abbbbcda`.

La grammatica la genera con:

$$
S\Rightarrow AB
\Rightarrow abbB
\Rightarrow abbbbcCa
\Rightarrow abbbbcda.
$$

Questo controllo verifica contemporaneamente che:

- ci sia almeno una `a` iniziale;
- ci sia almeno una `a` finale;
- le `b` siano almeno quattro;
- il blocco centrale possa essere soltanto `d`, senza `e`.

> [!warning] Non tutti i parametri devono partire dallo stesso valore
> Qui $n,m\geq1$, mentre $k\geq0$.
>
> I casi base devono rispettare questa differenza.

---

## 11. Errori frequenti

### Usare ε per un parametro che deve essere positivo

Se scrivessimo:

$$
A\to aAbb\mid\varepsilon,
$$

ammetteremmo anche $n=0$.

Se scrivessimo:

$$
B\to bbBa\mid cC,
$$

ammetteremmo anche $m=0$.

Entrambe le modifiche allargherebbero il linguaggio.

### Dimenticare lo scarto costante

La grammatica:

$$
C\to dCe\mid\varepsilon
$$

genera $d^ke^k$, mentre serve $d^{k+1}e^k$.

La `d` aggiuntiva deve essere presente nel caso base.

### Controllare i conteggi senza controllare l’ordine

Una produzione ricorsiva aggiunge terminali in posizioni precise.

Per esempio:

$$
X\to aXbb
$$

colloca le nuove `b` dopo tutto ciò che sarà generato da $X$.

Se dentro $X$ vengono generate anche le `a` finali, quelle `b` finiranno dopo di esse, violando l’ordine richiesto.

### Collegare parametri che devono restare indipendenti

Il linguaggio non richiede $n=m$.

La scelta del numero di `a` iniziali deve poter essere diversa dalla scelta del numero di `a` finali.

---

## 12. Procedura da ricordare all’esame

1. Scrivi l’ordine dei blocchi terminali.
2. Evidenzia quali blocchi dipendono dallo stesso parametro.
3. Scomponi gli esponenti che contengono somme.
4. Usa la concatenazione per parti indipendenti.
5. Usa produzioni ricorsive per collegare quantità.
6. Scegli casi base coerenti con i valori minimi.
7. Verifica una parola minima e una parola con ricorsione.
8. Spiega perché la grammatica genera tutte e sole le parole richieste.

> [!important] Idea centrale
> Ogni produzione dovrebbe avere un significato riconoscibile:
> aggiungere un gruppo di simboli, conservare un vincolo oppure terminare la derivazione.
>
> Se sai spiegare il ruolo di ogni produzione, puoi controllare la correttezza della grammatica e presentarla con chiarezza all’esame.