# Relazione tra grammatiche context-free e automi a pila

Le grammatiche context-free e gli automi a pila non deterministici descrivono la stessa classe di linguaggi.

La differenza riguarda il punto di vista:

- una grammatica descrive come **generare** una parola;
- un automa descrive come **riconoscere** una parola ricevuta in ingresso.

La pila permette di rappresentare la parte della struttura sintattica che deve ancora essere elaborata.

> [!important] Teorema fondamentale
> Un linguaggio è context-free se e solo se è riconosciuto da un automa a pila non deterministico:
> $$
> \boxed{
> L\in\mathrm{CFL}
> \iff
> \exists M\text{ NPDA tale che }L=L_{\mathrm{pv}}(M).
> }
> $$
> Per gli NPDA, la stessa classe si ottiene usando l’accettazione per stato finale.

---

## 1. Che cosa bisogna dimostrare

L’equivalenza comprende due direzioni.

### Dalla grammatica all’automa

Per ogni grammatica context-free $G$ possiamo costruire un NPDA $M$ tale che:

$$
L(G)=L_{\mathrm{pv}}(M).
$$

### Dall’automa alla grammatica

Per ogni NPDA $M$ possiamo costruire una grammatica context-free $G$ che genera il linguaggio riconosciuto dall’automa.

Se l’automa usa l’accettazione per stato finale, possiamo prima trasformarlo in un NPDA equivalente che accetta per pila vuota.

Le due costruzioni mostrano che grammatiche e automi hanno lo stesso potere espressivo.

---

## 2. Idea della costruzione da grammatica ad automa

Consideriamo:

$$
G=(N,\Sigma,P,S).
$$

La pila contiene una sequenza di terminali e non terminali che devono ancora essere elaborati.

L’automa usa due tipi di mosse.

### Espansione di un non terminale

Se in cima c’è $A$ e la grammatica contiene:

$$
A\to\alpha,
$$

l’automa sostituisce $A$ con $\alpha$ mediante una mossa epsilon.

### Confronto di un terminale

Se in cima c’è il terminale $a$, l’automa deve leggere proprio $a$ dall’input e rimuoverlo dalla pila.

La pila si comporta quindi come una descrizione della parte di parola ancora da riconoscere.

> [!tip] Collegamento con le derivazioni
> Con la cima a sinistra, l’automa espande il primo non terminale ancora da elaborare.
>
> La costruzione simula una derivazione sinistra, alternando espansioni e lettura dei terminali.

---

## 3. Costruzione formale: CFG → NPDA

Costruiamo un automa con:

- un solo stato $q$;
- alfabeto di ingresso $\Sigma$;
- alfabeto della pila $N\cup\Sigma$;
- simbolo iniziale della pila $S$;
- accettazione per pila vuota.

Possiamo scrivere:

$$
\boxed{
M=(\{q\},\Sigma,N\cup\Sigma,\delta,q,S,\varnothing).
}
$$

L’insieme degli stati finali è vuoto perché il criterio adottato usa soltanto lo svuotamento della pila e l’esaurimento dell’input.

### Regole di espansione

Per ogni produzione:

$$
A\to\alpha\in P,
$$

introduciamo:

$$
\boxed{
(q,\alpha)\in\delta(q,\varepsilon,A).
}
$$

L’automa non consuma input e sostituisce $A$ con $\alpha$.

Se esistono più produzioni con lo stesso lato sinistro, la transizione offre più alternative.

### Regole di confronto

Per ogni terminale $a\in\Sigma$:

$$
\boxed{
(q,\varepsilon)\in\delta(q,a,a).
}
$$

Il terminale in cima deve coincidere con il prossimo simbolo dell’input.

### Produzioni epsilon

Una produzione:

$$
A\to\varepsilon
$$

diventa:

$$
(q,\varepsilon)\in\delta(q,\varepsilon,A).
$$

Il non terminale viene rimosso senza leggere input.

> [!warning] Ordine dei simboli
> Con cima a sinistra, $A\to aAb$ sostituisce $A$ con $aAb$.
>
> La nuova cima è `a`. Invertire la stringa cambierebbe il comportamento dell’automa.

---

## 4. Esempio: dalla grammatica di aⁿbⁿ all’automa

Consideriamo:

$$
S\to aSb\mid\varepsilon.
$$

Il linguaggio è:

$$
L(G)=\{a^nb^n\mid n\geq0\}.
$$

La costruzione produce:

$$
\delta(q,\varepsilon,S)
=
\{(q,aSb),(q,\varepsilon)\},
$$

$$
\delta(q,a,a)=\{(q,\varepsilon)\},
$$

$$
\delta(q,b,b)=\{(q,\varepsilon)\}.
$$

La pila iniziale contiene $S$.

### Computazione accettante su aabb

$$
\begin{aligned}
(q,aabb,S)
&\vdash(q,aabb,aSb)\\
&\vdash(q,abb,Sb)\\
&\vdash(q,abb,aSbb)\\
&\vdash(q,bb,Sbb)\\
&\vdash(q,bb,bb)\\
&\vdash(q,b,b)\\
&\vdash(q,\varepsilon,\varepsilon).
\end{aligned}
$$

Le espansioni scelgono due volte $S\to aSb$ e poi $S\to\varepsilon$.

Le altre mosse confrontano i terminali con l’input.

La parola viene accettata perché input e pila sono entrambi vuoti.

Anche $\varepsilon$ viene accettata:

$$
(q,\varepsilon,S)
\vdash
(q,\varepsilon,\varepsilon).
$$

---

## 5. Perché la costruzione è corretta

L’informazione fondamentale è questa:

> La parte già letta, seguita dal contenuto della pila, corrisponde a una forma sentenziale derivabile dalla grammatica.

Se l’input iniziale è $w=uv$, abbiamo già consumato $u$ e la configurazione corrente è:

$$
(q,v,\alpha),
$$

allora:

$$
\boxed{
S\Rightarrow_{\mathrm{lm}}^*u\alpha.
}
$$

### Quando espandiamo un non terminale

La pila passa da $A\beta$ a $\gamma\beta$, usando una produzione $A\to\gamma$.

Alla forma sentenziale corrisponde il passo:

$$
uA\beta\Rightarrow_{\mathrm{lm}}u\gamma\beta.
$$

### Quando leggiamo un terminale

La pila passa da $a\beta$ a $\beta$ e il prefisso consumato passa da $u$ a $ua$.

La forma complessiva resta la stessa:

$$
u\,a\beta=(ua)\beta.
$$

### Alla fine

Se l’automa accetta, ha consumato tutta la parola e svuotato la pila:

$$
S\Rightarrow^*w.
$$

Viceversa, una derivazione sinistra di $w$ può essere simulata scegliendo le corrispondenti espansioni e confrontando i terminali.

Quindi:

$$
\boxed{L(G)=L_{\mathrm{pv}}(M).}
$$

---

## 6. Idea della costruzione inversa: NPDA → CFG

Nella direzione inversa dobbiamo rappresentare grammaticalmente sia la pila sia i cambiamenti di stato.

Introduciamo non terminali della forma:

$$
[pXq],
$$

con:

$$
p,q\in Q,
\qquad X\in\Gamma.
$$

$[pXq]$ è un singolo non terminale.

Il suo significato è:

> Generare le stringhe che portano l’automa dallo stato $p$, con $X$ in cima, allo stato $q$, dopo aver rimosso $X$ e tutto ciò che viene inserito al suo posto, senza modificare la parte sottostante della pila.

Se sotto $X$ si trova $\beta$, il segmento di computazione ha la forma:

$$
(p,u,X\beta)\vdash^*(q,\varepsilon,\beta),
$$

senza operare sui simboli di $\beta$.

### Perché servono due stati nel nome?

Il primo indica dove inizia l’elaborazione del simbolo $X$.

Il secondo indica dove termina.

Questa informazione permette di concatenare segmenti di computazione compatibili.

---

## 7. Produzioni della costruzione inversa

Supponiamo che l’automa accetti per pila vuota.

### Simbolo iniziale

Introduciamo un nuovo simbolo iniziale $S_G$ e le produzioni:

$$
\boxed{
S_G\to[q_0Z_0q]
\qquad\text{per ogni }q\in Q.
}
$$

Lo stato conclusivo può essere qualsiasi, perché il criterio richiede soltanto input esaurito e pila vuota.

### Caso 1: una transizione rimuove la cima

Se:

$$
(r,\varepsilon)\in\delta(p,a,X),
$$

introduciamo:

$$
\boxed{
[pXr]\to a.
}
$$

Qui $a$ può essere un terminale oppure $\varepsilon$.

Se la transizione non legge input, la produzione diventa:

$$
[pXr]\to\varepsilon.
$$

### Caso 2: una transizione inserisce più simboli

Supponiamo che:

$$
(r,Y_1Y_2\cdots Y_k)\in\delta(p,a,X),
\qquad k\geq1.
$$

Dopo la transizione, l’automa deve elaborare prima $Y_1$, poi $Y_2$ e così via.

Per ogni scelta degli stati intermedi introduciamo:

$$
\boxed{
[pXq]\to
a\,[r_0Y_1r_1]\,[r_1Y_2r_2]\cdots[r_{k-1}Y_kr_k],
}
$$

dove:

$$
r_0=r,
\qquad
r_k=q.
$$

Gli stati intermedi $r_1,\ldots,r_{k-1}$ variano in $Q$.

Se $a=\varepsilon$, il prefisso $a$ non aggiunge alcun terminale.

### Esempio con due simboli

Se:

$$
(r,YZ)\in\delta(p,a,X),
$$

aggiungiamo, per ogni $s,q\in Q$:

$$
\boxed{
[pXq]\to a\,[rYs]\,[sZq].
}
$$

Il primo non terminale descrive l’elaborazione di $Y$.

Il secondo descrive quella di $Z$, iniziando dallo stato in cui è terminata la prima.

---

## 8. Perché la costruzione inversa funziona

La proprietà decisiva è l’ordine LIFO.

Dopo aver sostituito $X$ con:

$$
Y_1Y_2\cdots Y_k,
$$

l’automa deve rimuovere $Y_1$ e i simboli inseriti durante la sua elaborazione prima di poter operare su $Y_2$.

La computazione si divide quindi in segmenti ordinati.

I non terminali della grammatica descrivono questi segmenti; gli stati intermedi garantiscono che la fine di un segmento coincida con l’inizio del successivo.

La grammatica è finita perché:

- $Q$ e $\Gamma$ sono finiti;
- le transizioni hanno una descrizione finita;
- ogni transizione inserisce una stringa finita.

È context-free perché ogni produzione ha un solo non terminale a sinistra.

---

## 9. L’equivalenza riguarda gli NPDA

La costruzione da grammatica ad automa può introdurre non determinismo anche quando il linguaggio ammette un DPDA.

Nell’esempio:

$$
S\to aSb\mid\varepsilon,
$$

la transizione:

$$
\delta(q,\varepsilon,S)
=
\{(q,aSb),(q,\varepsilon)\}
$$

offre due risultati.

L’automa costruito non è deterministico.

Tuttavia:

$$
\{a^nb^n\mid n\geq0\}
$$

è riconoscibile anche da un DPDA, come abbiamo visto nelle note precedenti.

> [!warning] Due conclusioni da evitare
> - Un NPDA ottenuto dalla grammatica non dimostra che il linguaggio richieda il non determinismo.
> - L’equivalenza CFG–NPDA non implica che ogni linguaggio context-free sia riconoscibile da un DPDA.

Anche la non ambiguità di una grammatica, da sola, non permette di concludere che il linguaggio sia deterministico.

---

## 10. Cosa ricordare all’esame

Il risultato centrale è:

$$
\boxed{
\text{Grammatiche context-free}
\quad\equiv\quad
\text{Automi a pila non deterministici}.
}
$$

Per spiegare la direzione CFG → NPDA:

1. La pila parte dal simbolo iniziale della grammatica.
2. Un non terminale viene sostituito con il lato destro di una produzione.
3. Un terminale in cima viene confrontato con l’input.
4. L’accettazione richiede input e pila vuoti.

Per spiegare la direzione NPDA → CFG:

1. Si usa l’accettazione per pila vuota.
2. Si introducono non terminali $[pXq]$.
3. Ogni non terminale descrive l’elaborazione completa di un simbolo di pila.
4. Le produzioni collegano segmenti di computazione mediante stati intermedi.

> [!important] Idea unificante
> La ricorsione della grammatica e il funzionamento LIFO della pila rappresentano la stessa organizzazione:
>
> prima si completa una struttura interna, poi si riprende quella che la contiene.