# Relazione tra linguaggi regolari, deterministici e context-free

Le tre classi sono legate dalle inclusioni strette:

$$
\boxed{\mathrm{REG}\subsetneq\mathrm{DCFL}\subsetneq\mathrm{CFL}.}
$$

Questo significa che:

- ogni linguaggio regolare è deterministico;
- ogni linguaggio deterministico è context-free;
- esistono linguaggi deterministici non regolari;
- esistono linguaggi context-free non deterministici.

> [!important] Collegamento con l’esame
> Nella prova del 26 gennaio 2026 viene chiesto di descrivere la relazione tra linguaggi regolari e deterministici, con dimostrazione.
>
> Per una risposta completa bisogna provare l’inclusione e mostrare che è stretta.

---

## 1. Che cosa rappresentano le tre classi

| Classe | Definizione mediante riconoscitore | Descrizione mediante grammatica |
|---|---|---|
| REG | DFA o NFA | Grammatica regolare |
| DCFL | DPDA, con accettazione per stato finale | Ammette una grammatica context-free |
| CFL | NPDA | Grammatica context-free |

La riga dei DCFL non fornisce una caratterizzazione mediante una CFG qualsiasi: essere generato da una CFG dimostra soltanto l’appartenenza a CFL.

Per dimostrare direttamente che un linguaggio è deterministico possiamo costruire un DPDA.

### Diagramma delle inclusioni

![[Allegati/FLT_05_07/flt_05_07_classi_linguaggi.svg|1100]]

Nel diagramma ogni elemento è un linguaggio, non una singola parola.

I linguaggi indicati nelle diverse regioni testimoniano che le inclusioni sono strette.

---

## 2. Ogni linguaggio regolare è deterministico

Vogliamo dimostrare:

$$
\boxed{\mathrm{REG}\subseteq\mathrm{DCFL}.}
$$

Sia $L$ un linguaggio regolare.

Esiste quindi un DFA:

$$
D=(Q,\Sigma,\delta_D,q_0,F)
$$

tale che:

$$
L(D)=L.
$$

Costruiamo un automa a pila che simula il DFA mantenendo la pila invariata.

### Costruzione formale

Scegliamo:

$$
M=(Q,\Sigma,\{Z_0\},\delta_M,q_0,Z_0,F).
$$

Per ogni $q\in Q$ e $a\in\Sigma$, definiamo:

$$
\boxed{
\delta_M(q,a,Z_0)=\bigl(\delta_D(q,a),Z_0\bigr).
}
$$

Non introduciamo mosse epsilon.

Il DPDA:

- legge gli stessi simboli del DFA;
- attraversa gli stessi stati;
- conserva sempre $Z_0$ nella pila;
- usa gli stessi stati finali.

### Perché è deterministico?

Ogni tripla ha un unico risultato, perché $\delta_D$ è una funzione.

Inoltre non ci sono mosse epsilon che possano competere con la lettura.

### Perché riconosce lo stesso linguaggio?

Per ogni parola $w$:

$$
(q_0,w,Z_0)
\vdash_M^*
\bigl(\widehat{\delta}_D(q_0,w),\varepsilon,Z_0\bigr),
$$

dove $\widehat{\delta}_D$ è la funzione di transizione estesa del DFA.

La parola viene accettata da $M$ esattamente quando il DFA raggiunge uno stato di $F$.

Quindi:

$$
L_F(M)=L(D)=L.
$$

Abbiamo dimostrato l’inclusione.

> [!warning] Il criterio di accettazione conta
> La costruzione usa l’accettazione per stato finale.
>
> La pila contiene sempre $Z_0$: non viene mai svuotata.

---

## 3. L’inclusione REG ⊆ DCFL è stretta

Serve un linguaggio appartenente a DCFL ma non a REG.

Scegliamo:

$$
H=\{a^nb^n\mid n\geq0\}.
$$

### H è deterministico

Un DPDA può:

1. inserire un simbolo $A$ per ogni lettura di una 'a';
2. passare alla fase di rimozione quando legge la prima 'b';
3. rimuovere un $A$ per ogni 'b';
4. accettare se il confronto termina correttamente e l’input è esaurito.

La parola vuota viene gestita con uno stato iniziale finale apposito, come nella nota sulla definizione dei DPDA.

Il cambio di fase è determinato dal simbolo letto.

Quindi:

$$
H\in\mathrm{DCFL}.
$$

### H non è regolare

Supponiamo per assurdo che sia regolare.

Sia $p$ una lunghezza di pompaggio e scegliamo:

$$
w=a^pb^p.
$$

Per ogni decomposizione:

$$
w=xyz,
\qquad |xy|\leq p,
\qquad |y|>0,
$$

il segmento $y$ contiene soltanto 'a':

$$
y=a^t,
\qquad 1\leq t\leq p.
$$

Pompando con esponente zero otteniamo:

$$
xz=a^{p-t}b^p,
$$

che non appartiene a $H$, perché il numero di 'a' è inferiore al numero di 'b'.

Questo contraddice il pumping lemma.

Pertanto:

$$
H\notin\mathrm{REG}.
$$

Concludiamo:

$$
\boxed{\mathrm{REG}\subsetneq\mathrm{DCFL}.}
$$

> [!important] Struttura della dimostrazione
> La simulazione del DFA prova l’inclusione.
>
> Il linguaggio $H$ prova che l’inclusione è stretta.
>
> Sono due passaggi distinti.

---

## 4. Ogni linguaggio deterministico è context-free

Vogliamo dimostrare:

$$
\boxed{\mathrm{DCFL}\subseteq\mathrm{CFL}.}
$$

Se $L\in\mathrm{DCFL}$, esiste un DPDA $M$ che riconosce $L$.

Possiamo interpretarlo come NPDA sostituendo ogni risultato con un insieme singoletto:

$$
\delta_D(q,a,X)=(p,\gamma)
$$

diventa:

$$
\delta_N(q,a,X)=\{(p,\gamma)\}.
$$

Quando la transizione deterministica è non definita, il corrispondente insieme di risultati è vuoto.

Le computazioni e il linguaggio riconosciuto restano gli stessi.

Poiché i linguaggi riconosciuti dagli NPDA sono esattamente i CFL:

$$
L\in\mathrm{CFL}.
$$

> [!tip] Non determinismo come possibilità
> Un NPDA può avere più scelte, ma non è obbligato ad averle.
>
> Il comportamento di un DPDA è quindi un caso particolare del comportamento ammesso per un NPDA.

---

## 5. L’inclusione DCFL ⊆ CFL è stretta

Per dimostrarlo serve un linguaggio context-free che non sia deterministico.

Riprendiamo il risultato della nota:

[[06 - Linguaggi deterministici - Proprieta e chiusure]].

Fissiamo:

$$
\Sigma=\{a,b,c\}
$$

e definiamo:

$$
A=\{a^nb^nc^m\mid n,m\geq1\},
$$

$$
B=\{a^mb^nc^n\mid m,n\geq1\}.
$$

Entrambi sono deterministici:

- in $A$ confrontiamo il numero di 'a' con quello delle 'b';
- in $B$ confrontiamo il numero di 'b' con quello delle 'c'.

La loro intersezione è:

$$
T=A\cap B=\{a^nb^nc^n\mid n\geq1\}.
$$

Sappiamo che:

$$
T\notin\mathrm{CFL}.
$$

La dimostrazione mediante pumping lemma per i CFL è riportata nella nota precedente.

### Costruiamo il testimone K

Definiamo:

$$
K=\Sigma^*\setminus T.
$$

Per De Morgan:

$$
K=\overline{A\cap B}
=\overline A\cup\overline B.
$$

### K è context-free

Poiché i DCFL sono chiusi per complemento:

$$
\overline A,\overline B\in\mathrm{DCFL}
\subseteq\mathrm{CFL}.
$$

I CFL sono chiusi per unione, quindi:

$$
K=\overline A\cup\overline B\in\mathrm{CFL}.
$$

### K non è deterministico

Supponiamo per assurdo:

$$
K\in\mathrm{DCFL}.
$$

La chiusura dei DCFL per complemento darebbe:

$$
\overline K=T\in\mathrm{DCFL}.
$$

Ma ogni DCFL è context-free, mentre $T$ non lo è.

Contraddizione.

Quindi:

$$
K\in\mathrm{CFL}\setminus\mathrm{DCFL}.
$$

Concludiamo:

$$
\boxed{\mathrm{DCFL}\subsetneq\mathrm{CFL}.}
$$

> [!warning] Il complemento è rispetto a tutto Σ*
> $K$ comprende anche le parole i cui simboli non sono nell’ordine $a^*b^*c^*$.
>
> Non stiamo prendendo il complemento soltanto dentro il linguaggio $a^*b^*c^*$.

---

## 6. Esempi nelle diverse regioni

| Linguaggio | REG | DCFL | CFL |
|---|---|---|---|
| $\{a,b\}^*$ | Sì | Sì | Sì |
| $H=\{a^nb^n\mid n\geq0\}$ | No | Sì | Sì |
| $K=\{a,b,c\}^*\setminus\{a^nb^nc^n\mid n\geq1\}$ | No | No | Sì |
| $T=\{a^nb^nc^n\mid n\geq1\}$ | No | No | No |

Per $K$, la non regolarità segue già dal fatto che non è deterministico: ogni regolare sarebbe deterministico.

Per $T$, la non appartenenza a CFL esclude anche l’appartenenza alle due classi più piccole.

La colonna “No” per CFL non significa che il linguaggio sia privo di qualsiasi riconoscitore: riguarda soltanto i modelli studiati in questa parte del corso.

---

## 7. Quali conclusioni possiamo trarre?

Dalla catena:

$$
\mathrm{REG}\subsetneq\mathrm{DCFL}\subsetneq\mathrm{CFL}
$$

seguono queste implicazioni:

| Informazione disponibile | Conclusione certa |
|---|---|
| $L$ è regolare | È anche deterministico e context-free |
| $L$ è deterministico | È anche context-free |
| $L$ non è context-free | Non è deterministico né regolare |
| $L$ non è deterministico | Non è regolare |

Invece:

- non regolare non implica non deterministico;
- context-free non implica deterministico;
- deterministico non implica regolare.

Gli esempi $H$ e $K$ permettono di verificare subito queste distinzioni.

---

## 8. Linguaggio e rappresentazione non coincidono

La classificazione riguarda il linguaggio, non soltanto la forma del dispositivo o della grammatica usati per descriverlo.

### Una CFG può generare un linguaggio regolare

Per esempio:

$$
S\to aSa\mid\varepsilon
$$

genera:

$$
\{a^{2n}\mid n\geq0\},
$$

che è regolare ed è denotato da $(aa)^*$.

### Un NPDA può riconoscere un linguaggio deterministico

La costruzione generale dalla grammatica di $a^nb^n$ produce scelte, ma esiste anche un DPDA per lo stesso linguaggio.

### Una grammatica ambigua può generare un linguaggio regolare

Consideriamo:

$$
S\to aA\mid aB,
\qquad
A\to b,
\qquad
B\to b.
$$

La grammatica è ambigua, ma il linguaggio è soltanto:

$$
\{ab\}.
$$

> [!important] Domanda corretta
> Qual è il modello più semplice che può descrivere il linguaggio?
>
> La presenza di una rappresentazione più generale non esclude l’esistenza di una rappresentazione più semplice.

---

## 9. Risposta sintetica per l’esame di gennaio 2026

Ogni linguaggio regolare è deterministico.

Infatti, dato un DFA per $L$, costruiamo un DPDA con gli stessi stati e gli stessi stati finali, che simula le transizioni del DFA lasciando invariata la pila:

$$
\delta_M(q,a,Z_0)
=
(\delta_D(q,a),Z_0).
$$

Il DPDA è deterministico e riconosce esattamente $L$ per stato finale.

L’inclusione è stretta perché:

$$
\{a^nb^n\mid n\geq0\}
$$

è riconoscibile da un DPDA, ma non è regolare, come si dimostra mediante pumping lemma.

Quindi:

$$
\boxed{\mathrm{REG}\subsetneq\mathrm{DCFL}.}
$$

> [!tip] Cosa deve comparire nella risposta
> - Costruzione che vale per ogni linguaggio regolare.
> - Spiegazione del determinismo e dell’equivalenza.
> - Un linguaggio deterministico non regolare.
> - Giustificazione della sua non regolarità.
