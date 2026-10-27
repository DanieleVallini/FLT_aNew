# Automi a pila non deterministici - Definizione

Un automa a pila è un automa dotato di una memoria aggiuntiva organizzata come una **pila**.

Il controllo dell’automa ha un numero finito di stati, ma la pila può contenere una quantità arbitrariamente grande di simboli.

Questa memoria permette, per esempio, di confrontare il numero di `a` iniziali con quello delle `b` successive.

> [!important] Collegamento con l’esame
> Le prove richiedono sia la definizione formale degli automi a pila sia la costruzione di automi per linguaggi specifici.
>
> Per dare una definizione completa bisogna indicare la tupla, il tipo della funzione di transizione e il significato delle mosse.

---

## 1. Come funziona la pila

La pila segue la disciplina **LIFO**:

> L’ultimo simbolo inserito è il primo a essere rimosso.

L’automa può osservare e modificare la cima della pila.

Non può accedere direttamente a un simbolo che si trova nel mezzo della pila.

Per esempio, possiamo usare la pila per riconoscere:

$$
L=\{a^nb^n\mid n\geq1\}.
$$

L’idea è:

1. leggere le `a`, inserendo un simbolo per ciascuna;
2. leggere le `b`, rimuovendo un simbolo per ciascuna;
3. accettare quando l’input è terminato e il confronto è riuscito.

La pila conserva quindi l’informazione necessaria a confrontare due quantità che possono essere arbitrariamente grandi.

---

## 2. Definizione formale

Un **automa a pila non deterministico**, abbreviato NPDA, è una tupla:

$$
\boxed{
M=(Q,\Sigma,\Gamma,\delta,q_0,Z_0,F).
}
$$

I componenti sono:

| Componente | Significato |
|---|---|
| $Q$ | Insieme finito degli stati |
| $\Sigma$ | Alfabeto finito di ingresso |
| $\Gamma$ | Alfabeto finito della pila |
| $\delta$ | Funzione di transizione |
| $q_0\in Q$ | Stato iniziale |
| $Z_0\in\Gamma$ | Simbolo iniziale della pila |
| $F\subseteq Q$ | Insieme degli stati finali |

All’inizio:

- il controllo si trova in $q_0$;
- l’intera parola deve ancora essere letta;
- la pila contiene soltanto $Z_0$.

Il simbolo $Z_0$ viene spesso usato come marcatore di fondo della pila.

### Tipo della funzione di transizione

Adottiamo:

$$
\boxed{
\delta:
Q\times(\Sigma\cup\{\varepsilon\})\times\Gamma
\longrightarrow
\mathcal{P}_{\mathrm{fin}}(Q\times\Gamma^*).
}
$$

$\mathcal{P}_{\mathrm{fin}}$ indica l’insieme dei sottoinsiemi finiti.

Quindi, fissati:

- lo stato corrente;
- il simbolo da leggere, oppure $\varepsilon$;
- il simbolo in cima alla pila;

la funzione restituisce un insieme finito di possibili risultati.

In molti testi si usa semplicemente la notazione $\mathcal P$, sottintendendo che la descrizione delle transizioni sia finita.

> [!warning] Due alfabeti con ruoli diversi
> $\Sigma$ contiene i simboli dell’input.
>
> $\Gamma$ contiene i simboli memorizzabili nella pila.
>
> Non è necessario che coincidano. Inoltre $\varepsilon$ non è un simbolo dell’alfabeto di ingresso.

---

## 3. Significato di una transizione

Se:

$$
(p,\gamma)\in\delta(q,a,X),
$$

l’automa può:

1. partire dallo stato $q$;
2. leggere $a$, se $a\in\Sigma$;
3. trovare $X$ in cima alla pila;
4. passare nello stato $p$;
5. sostituire $X$ con la stringa $\gamma$.

Se il secondo argomento è $\varepsilon$, la transizione non consuma input.

### Convenzione sulla cima

In queste note scriviamo la cima della pila **a sinistra**.

Se la pila è:

$$
X\beta,
$$

e la transizione sostituisce $X$ con $\gamma$, la nuova pila è:

$$
\gamma\beta.
$$

Per esempio:

$$
(p,YZ)\in\delta(q,a,X)
$$

trasforma:

$$
X\beta
\quad\text{in}\quad
YZ\beta.
$$

La nuova cima è $Y$.

### Operazioni ottenibili

| Sostituzione | Effetto |
|---|---|
| $X\mapsto\varepsilon$ | Rimuove la cima |
| $X\mapsto X$ | Lascia invariata la pila |
| $X\mapsto YX$ | Inserisce $Y$ sopra $X$ |
| $X\mapsto Y$ | Sostituisce la cima |
| $X\mapsto YZ$ | Sostituisce la cima con due simboli |

> [!important] Operazione fondamentale
> La definizione generale usa la sostituzione della cima con una stringa.
>
> Inserimento, rimozione e conservazione della cima sono casi particolari della stessa operazione.

---

## 4. Configurazioni e mosse

Una configurazione descrive la situazione completa dell’automa:

$$
(q,w,\alpha),
$$

dove:

- $q$ è lo stato corrente;
- $w$ è la parte di input ancora da leggere;
- $\alpha$ è il contenuto della pila, con cima a sinistra.

### Mossa che consuma input

Se:

$$
(p,\gamma)\in\delta(q,a,X),
\qquad a\in\Sigma,
$$

allora:

$$
\boxed{
(q,aw,X\beta)\vdash_M(p,w,\gamma\beta).
}
$$

Il simbolo $a$ viene consumato.

### Mossa epsilon

Se:

$$
(p,\gamma)\in\delta(q,\varepsilon,X),
$$

allora:

$$
\boxed{
(q,w,X\beta)\vdash_M(p,w,\gamma\beta).
}
$$

L’input rimanente resta $w$.

Scriviamo $\vdash_M^*$ per indicare una sequenza di zero o più mosse.

> [!warning] Due usi distinti di epsilon
> In $\delta(q,\varepsilon,X)$, $\varepsilon$ significa che non viene consumato input.
>
> In un risultato $(p,\varepsilon)$, significa che la cima viene rimossa.
>
> Una mossa epsilon può modificare la pila; una mossa che legge un simbolo può lasciare la pila invariata.

---

## 5. Che cosa significa “non deterministico”

Da una stessa configurazione possono essere possibili più mosse.

Questo può accadere perché:

- una transizione restituisce più coppie;
- è disponibile una mossa epsilon insieme a una mossa che legge il prossimo simbolo.

Per esempio:

$$
\delta(q,a,X)=\{(p,X),(r,YX)\}
$$

offre due alternative:

- passare in $p$ mantenendo la pila;
- passare in $r$ inserendo $Y$ sopra $X$.

Una parola viene accettata se **esiste almeno una computazione accettante**.

Non è necessario che tutte le scelte conducano all’accettazione.

### Esempio intuitivo: individuare il centro di una parola

Per riconoscere:

$$
\{ww^R\mid w\in\{a,b\}^*\},
$$

un NPDA può:

1. inserire in pila i simboli di una prima parte;
2. scegliere con una mossa epsilon di iniziare il confronto;
3. confrontare la parte restante con i simboli estratti.

La pila restituisce i simboli nell’ordine inverso.

Per `abba`, la scelta corretta separa:

$$
ab\mid ba.
$$

Le scelte che individuano un punto sbagliato possono fallire: basta che ne esista una corretta.

> [!tip] Non deterministico non significa casuale
> La definizione considera l’esistenza di una sequenza di scelte accettante.
>
> Inoltre un NPDA può avere, in un particolare esempio, una sola mossa possibile per ogni configurazione.

---

## 6. Accettazione per stato finale

Una parola è accettata per stato finale se esiste una computazione che:

- consuma tutto l’input;
- termina in uno stato appartenente a $F$.

Il contenuto finale della pila può essere qualsiasi.

Formalmente:

$$
\boxed{
L_F(M)=
\left\{
w\in\Sigma^*
\;\middle|\;
\exists q_f\in F,\ \exists\alpha\in\Gamma^*:
(q_0,w,Z_0)\vdash_M^*(q_f,\varepsilon,\alpha)
\right\}.
}
$$

> [!warning] Raggiungere uno stato finale non basta
> Anche l’input deve essere completamente consumato.
>
> Una configurazione $(q_f,u,\alpha)$ con $u\neq\varepsilon$ non soddisfa il criterio di accettazione.

La computazione accettante deve essere finita. Un eventuale ramo infinito non è accettante, ma non impedisce che un altro ramo accetti.

---

## 7. Esempio completo: riconoscere aⁿbⁿ con n ≥ 1

Consideriamo:

$$
L=\{a^nb^n\mid n\geq1\}.
$$

Definiamo:

$$
Q=\{q_a,q_b,q_f\},
$$

$$
\Sigma=\{a,b\},
\qquad
\Gamma=\{A,Z_0\}.
$$

Lo stato iniziale è $q_a$ e:

$$
F=\{q_f\}.
$$

### Ruolo degli stati

- $q_a$: lettura delle `a` e inserimento dei simboli $A$;
- $q_b$: lettura delle `b` e rimozione dei simboli $A$;
- $q_f$: stato finale.

Le transizioni non vuote sono:

| Stato | Input | Cima | Risultati |
|---|---|---|---|
| $q_a$ | $a$ | $Z_0$ | $\{(q_a,AZ_0)\}$ |
| $q_a$ | $a$ | $A$ | $\{(q_a,AA)\}$ |
| $q_a$ | $b$ | $A$ | $\{(q_b,\varepsilon)\}$ |
| $q_b$ | $b$ | $A$ | $\{(q_b,\varepsilon)\}$ |
| $q_b$ | $\varepsilon$ | $Z_0$ | $\{(q_f,Z_0)\}$ |

Tutti gli altri valori di $\delta$ sono l’insieme vuoto.

### Computazione su aabb

$$
\begin{aligned}
(q_a,aabb,Z_0)
&\vdash(q_a,abb,AZ_0)\\
&\vdash(q_a,bb,AAZ_0)\\
&\vdash(q_b,b,AZ_0)\\
&\vdash(q_b,\varepsilon,Z_0)\\
&\vdash(q_f,\varepsilon,Z_0).
\end{aligned}
$$

L’input è esaurito e lo stato finale è $q_f$, quindi `aabb` viene accettata.

### Perché il linguaggio è quello richiesto

- Ogni `a` inserisce esattamente un $A$.
- Ogni `b` rimuove esattamente un $A$.
- Dopo la prima `b` non è possibile leggere altre `a`.
- Si raggiunge $q_f$ soltanto quando tutti gli $A$ sono stati rimossi.
- Per entrare in $q_b$ bisogna aver letto almeno una `a` e una `b`.

La parola vuota non è accettata, coerentemente con $n\geq1$.

Questo automa ha un comportamento deterministico; rientra comunque nella definizione generale di NPDA.

---

## 8. Accettazione per pila vuota

Un altro criterio richiede di:

- consumare tutto l’input;
- svuotare completamente la pila.

Lo stato raggiunto può essere qualsiasi.

Formalmente:

$$
\boxed{
L_{\mathrm{pv}}(M)=
\left\{
w\in\Sigma^*
\;\middle|\;
\exists q\in Q:
(q_0,w,Z_0)\vdash_M^*(q,\varepsilon,\varepsilon)
\right\}.
}
$$

Con questo criterio gli stati finali non hanno un ruolo nell’accettazione.

### Pila vuota significa davvero vuota

Una pila contenente soltanto $Z_0$ non è vuota.

Per applicare questo criterio bisogna poter rimuovere anche il simbolo iniziale.

Nell’esempio precedente possiamo sostituire l’ultima transizione con:

$$
\delta(q_b,\varepsilon,Z_0)=\{(q_f,\varepsilon)\}
$$

e adottare l’accettazione per pila vuota.

---

## 9. Equivalenza dei criteri e relazione con le grammatiche

Per gli NPDA, accettazione per stato finale e accettazione per pila vuota riconoscono la stessa classe di linguaggi.

Questo significa che si possono costruire automi equivalenti passando da un criterio all’altro.

Non significa che, per uno stesso automa senza modifiche:

$$
L_F(M)=L_{\mathrm{pv}}(M).
$$

Il risultato fondamentale è:

$$
\boxed{
L\text{ è context-free}
\iff
L\text{ è riconosciuto da un NPDA}.
}
$$

Le grammatiche context-free descrivono come generare le parole; gli NPDA descrivono come riconoscerle.

La corrispondenza sarà approfondita nella nota dedicata alla relazione tra grammatiche e automi a pila.

---

## 10. Cosa ricordare per la definizione d’esame

Una risposta completa deve contenere:

1. La tupla:
   $$
   M=(Q,\Sigma,\Gamma,\delta,q_0,Z_0,F).
   $$

2. Il significato dei componenti.

3. Il tipo della funzione:
   $$
   \delta:
   Q\times(\Sigma\cup\{\varepsilon\})\times\Gamma
   \to
   \mathcal P_{\mathrm{fin}}(Q\times\Gamma^*).
   $$

4. Il significato di:
   $$
   (p,\gamma)\in\delta(q,a,X).
   $$

5. Il criterio di accettazione adottato.

6. Il ruolo del non determinismo: basta una computazione accettante.

> [!important] Errori da evitare
> - Confondere alfabeto di ingresso e alfabeto della pila.
> - Dimenticare le mosse epsilon.
> - Pensare che ogni mossa epsilon lasci invariata la pila.
> - Accettare con input ancora da leggere.
> - Considerare vuota una pila contenente $Z_0$.
> - Richiedere che tutti i rami accettino.
> - Cambiare convenzione sulla posizione della cima durante una simulazione.