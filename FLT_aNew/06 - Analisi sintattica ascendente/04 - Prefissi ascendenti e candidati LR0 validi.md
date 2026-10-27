# Prefissi ascendenti e candidati LR0 validi

Gli stati di un parser LR descrivono le produzioni che possono essere in corso di riconoscimento.

Per rappresentarle usiamo gli **item LR(0)**, chiamati anche **candidati LR(0)**.

La nozione di item valido collega queste informazioni al contenuto della pila, cioè a un prefisso ascendente.

> [!important] Collegamento con l’esame
> Nella prova del 26 gennaio 2026 viene richiesta la definizione formale di candidato LR(0) valido per un prefisso ascendente $\gamma$.
>
> Il punto centrale è:
> $$
> \gamma=\alpha\beta_1,
> $$
> dove $\alpha$ è il contesto a sinistra e $\beta_1$ è la parte della produzione prima del punto.

---

## 1. Notazione e forme sentenziali destre

Consideriamo una grammatica aumentata:

$$
G'=(N',\Sigma',P',S').
$$

Negli esercizi adottiamo la produzione:

$$
S'\to S\downarrow.
$$

Il marcatore $\downarrow$ appartiene all’alfabeto della grammatica aumentata.

Indichiamo con:

$$
V=N'\cup\Sigma'
$$

l’insieme dei simboli grammaticali.

Una **forma sentenziale destra** è una sequenza ottenuta mediante una derivazione destra:

$$
S'\Rightarrow_{\mathrm{rm}}^*\eta.
$$

A ogni passo viene espanso il non terminale più a destra.

Le forme intermedie possono contenere sia terminali sia non terminali.

---

## 2. Maniglia e prefisso ascendente

Supponiamo che esista il passo:

$$
S'
\Rightarrow_{\mathrm{rm}}^*
\alpha A w
\Rightarrow_{\mathrm{rm}}
\alpha\beta w,
$$

dove:

$$
A\to\beta\in P',
\qquad
\alpha,\beta\in V^*,
\qquad
w\in\Sigma'^*.
$$

L’occorrenza di $\beta$ introdotta dal passo finale è una maniglia della forma sentenziale.

Riducendola si ricostruisce:

$$
\alpha\beta w\rightsquigarrow\alpha A w.
$$

### Definizione di prefisso ascendente

Un **prefisso ascendente**, o *viable prefix*, è un prefisso di una forma sentenziale destra che non oltrepassa l’estremità destra della sua maniglia.

Formalmente, $\gamma$ è un prefisso ascendente se esiste una derivazione:

$$
S'
\Rightarrow_{\mathrm{rm}}^*
\alpha A w
\Rightarrow_{\mathrm{rm}}
\alpha\beta w
$$

tale che:

$$
\boxed{\gamma\text{ è un prefisso di }\alpha\beta.}
$$

Il prefisso può fermarsi:

- prima della maniglia;
- all’interno della maniglia;
- esattamente alla fine della maniglia.

Non deve proseguire oltre quel confine.

> [!important] Collegamento con la pila
> Durante un’analisi ascendente corretta, i simboli grammaticali sulla pila formano un prefisso ascendente.
>
> Il parser deve riconoscere la maniglia prima di spostare simboli che la oltrepasserebbero.

---

## 3. Non è semplicemente un prefisso dell’input

Riprendiamo:

$$
S'\to S\downarrow
$$

$$
S\to SA\mid b
$$

$$
A\to aA\mid c.
$$

Una derivazione destra di `bacc↓` è:

$$
\begin{aligned}
S'
&\Rightarrow S\downarrow\\
&\Rightarrow SA\downarrow\\
&\Rightarrow Sc\downarrow\\
&\Rightarrow SAc\downarrow\\
&\Rightarrow SaAc\downarrow\\
&\Rightarrow Sacc\downarrow\\
&\Rightarrow bacc\downarrow.
\end{aligned}
$$

Nell’ultimo passo la maniglia è `b`.

Il parser deve quindi ridurre:

$$
b\rightsquigarrow S
$$

prima di spostare la successiva `a`.

Dopo aver letto il prefisso terminale `ba`, la pila corretta contiene:

$$
Sa,
$$

perché `b` è già stata ridotta a $S$.

### Due differenze da ricordare

- Un prefisso ascendente può contenere non terminali.
- Essere prefisso di una parola valida non basta per essere un prefisso ascendente.

Per questa grammatica, `ba` non è un prefisso ascendente: ogni forma sentenziale destra che inizia con `b` è ottenuta applicando $S\to b$ come ultimo passo, quindi la sua maniglia è proprio `b`.

Il prefisso `ba` ne oltrepassa la fine.

---

## 4. Definizione di item LR(0)

Un item LR(0) è una produzione nella quale viene inserito un punto in una posizione del lato destro.

Da:

$$
A\to\beta_1\beta_2
$$

otteniamo:

$$
\boxed{[A\to\beta_1\cdot\beta_2].}
$$

L’interpretazione è:

- $\beta_1$ è la parte già riconosciuta della produzione;
- $\beta_2$ è la parte che resta da riconoscere.

### Esempio

La produzione:

$$
A\to aA
$$

produce tre item:

$$
[A\to\cdot aA],
$$

$$
[A\to a\cdot A],
$$

$$
[A\to aA\cdot].
$$

Una produzione con lato destro lungo $r$ produce:

$$
r+1
$$

item.

> [!warning] Il punto non è un simbolo della grammatica
> È un’informazione del riconoscitore.
>
> Non viene letto dall’input e non compare nelle parole generate.

---

## 5. Definizione formale di item valido

L’item:

$$
[A\to\beta_1\cdot\beta_2]
$$

è **valido per il prefisso ascendente $\gamma$** se esistono:

$$
\alpha\in V^*,
\qquad
w\in\Sigma'^*
$$

tali che:

$$
\boxed{
S'
\Rightarrow_{\mathrm{rm}}^*
\alpha A w
\Rightarrow_{\mathrm{rm}}
\alpha\beta_1\beta_2w
\quad\text{e}\quad
\gamma=\alpha\beta_1.
}
$$

Il passo finale usa precisamente la produzione:

$$
A\to\beta_1\beta_2.
$$

### Significato delle parti

| Parte | Significato |
|---|---|
| $\alpha$ | Contesto a sinistra della produzione |
| $\beta_1$ | Parte riconosciuta del lato destro |
| $\beta_2$ | Parte ancora da riconoscere |
| $w$ | Suffisso terminale a destra |
| $\gamma=\alpha\beta_1$ | Prefisso rappresentato dalla pila |

La maniglia completa è:

$$
\alpha\,
\underbrace{\beta_1\beta_2}_{\text{maniglia}}
\,w.
$$

Il prefisso $\gamma$ termina nella posizione indicata dal punto.

> [!important] Errore da evitare
> In generale:
> $$
> \gamma\neq\beta_1.
> $$
> Bisogna includere anche il contesto $\alpha$.

La validità è una proprietà dell’item **rispetto a un prefisso**, non una proprietà isolata della produzione puntata.

---

## 6. Esempio completo di validità

Consideriamo:

$$
[A\to a\cdot A]
$$

e il prefisso:

$$
\gamma=Sa.
$$

Nella grammatica precedente esiste:

$$
S'
\Rightarrow_{\mathrm{rm}}^*
SAc\downarrow
\Rightarrow_{\mathrm{rm}}
SaAc\downarrow.
$$

Confrontiamo con la definizione:

$$
\alpha=S,
\qquad
\beta_1=a,
\qquad
\beta_2=A,
\qquad
w=c\downarrow.
$$

Quindi:

$$
\alpha\beta_1=Sa=\gamma.
$$

L’item è valido per $Sa$.

### Esempio di item completo

Consideriamo:

$$
[A\to c\cdot].
$$

Dalla derivazione:

$$
S'
\Rightarrow_{\mathrm{rm}}^*
SaAc\downarrow
\Rightarrow_{\mathrm{rm}}
Sacc\downarrow
$$

otteniamo:

$$
\alpha=Sa,
\qquad
\beta_1=c,
\qquad
\beta_2=\varepsilon.
$$

Pertanto l’item è valido per:

$$
\gamma=Sac.
$$

La maniglia è la prima `c`, quella che termina il prefisso $Sac$.

### Un caso non valido

L’item:

$$
[A\to a\cdot A]
$$

non è valido per $\gamma=S$.

La definizione richiederebbe:

$$
S=\alpha a,
$$

ma il simbolo $S$ non termina con il terminale `a`.

---

## 7. Più item possono essere validi insieme

Per il prefisso:

$$
Sa
$$

sono contemporaneamente validi:

$$
[A\to a\cdot A],
$$

$$
[A\to\cdot aA],
$$

$$
[A\to\cdot c].
$$

Il primo descrive la produzione esterna: abbiamo riconosciuto `a` e aspettiamo un $A$.

Gli altri descrivono come può iniziare il riconoscimento di quell’$A$:

$$
A\to aA
\qquad\text{oppure}\qquad
A\to c.
$$

### Un item iniziale può essere valido per un prefisso non vuoto

Per esempio:

$$
[A\to\cdot c]
$$

è valido per $Sa$ perché:

$$
S'
\Rightarrow_{\mathrm{rm}}^*
SaAc\downarrow
\Rightarrow_{\mathrm{rm}}
Sacc\downarrow.
$$

Qui:

$$
\alpha=Sa,
\qquad
\beta_1=\varepsilon,
\qquad
\beta_2=c.
$$

Il punto iniziale significa che non è stata riconosciuta alcuna parte di questa produzione, non che la pila sia vuota.

> [!warning] Più item non significano automaticamente conflitto
> Un insieme di item rappresenta più informazioni compatibili.
>
> Il conflitto nasce quando queste informazioni richiedono azioni incompatibili nella stessa situazione.

---

## 8. Interpretare la posizione del punto

| Forma dell’item | Informazione |
|---|---|
| $[A\to\cdot\beta]$ | La produzione deve ancora essere riconosciuta |
| $[A\to\beta_1\cdot a\eta]$ | Il prossimo simbolo della produzione è il terminale $a$ |
| $[A\to\beta_1\cdot B\eta]$ | Deve essere riconosciuta una struttura generata da $B$ |
| $[A\to\beta\cdot]$ | Il lato destro è completo: candidato a una riduzione |

Un non terminale dopo il punto non viene letto dall’input: viene riconosciuto attraverso le sue produzioni.

Un item completo ordinario propone una riduzione; non indica automaticamente l’accettazione.

Con la nostra grammatica aumentata, l’item conclusivo speciale è:

$$
[S'\to S\downarrow\cdot].
$$

---

## 9. Produzioni epsilon

La produzione:

$$
A\to\varepsilon
$$

ha un unico item:

$$
\boxed{[A\to\cdot].}
$$

Il lato destro contiene zero simboli, quindi il punto è contemporaneamente all’inizio e alla fine.

La validità per $\gamma$ richiede:

$$
S'
\Rightarrow_{\mathrm{rm}}^*
\alpha A w
\Rightarrow_{\mathrm{rm}}
\alpha w,
$$

con:

$$
\gamma=\alpha.
$$

La corrispondente riduzione:

- rimuove zero simboli grammaticali;
- inserisce $A$;
- non consuma input.

> [!warning] Epsilon non occupa una posizione
> Non dobbiamo costruire due item distinti mettendo il punto prima e dopo un presunto simbolo epsilon.

---

## 10. Collegamento con l’automa LR(0)

Indichiamo con:

$$
\mathrm{Val}(\gamma)
$$

l’insieme degli item validi per $\gamma$.

Nel consueto caso di grammatica senza simboli inutili, la costruzione canonica LR(0) permette di calcolare questi insiemi mediante un automa finito.

Leggendo un prefisso ascendente, l’automa raggiunge lo stato che raccoglie gli item validi per quel prefisso.

L’alfabeto dell’automa comprende simboli grammaticali:

$$
N'\cup\Sigma'.
$$

Le transizioni sui non terminali vengono utilizzate dal parser dopo le riduzioni.

Le funzioni che costruiscono stati e transizioni sono CLOSURE e GOTO, argomento della prossima nota.

### Perché non c’è contraddizione con i linguaggi context-free?

Il linguaggio dei prefissi ascendenti è regolare e può essere riconosciuto da un automa finito.

Il parser completo usa però anche la pila e le riduzioni.

Quindi:

- l’automa finito descrive il contesto della pila;
- il riconoscitore completo può analizzare linguaggi non regolari.

> [!important] Prefisso possibile e input effettivo
> Un item valido descrive un contesto possibile della grammatica.
>
> Non garantisce che il particolare input ancora da leggere completi correttamente la parola.

---

## 11. Risposta sintetica per l’esame

Un candidato LR(0) è una produzione puntata:

$$
[A\to\beta_1\cdot\beta_2].
$$

È valido per un prefisso ascendente $\gamma$ se esistono $\alpha\in V^*$ e $w\in\Sigma'^*$ tali che:

$$
\boxed{
S'
\Rightarrow_{\mathrm{rm}}^*
\alpha A w
\Rightarrow_{\mathrm{rm}}
\alpha\beta_1\beta_2w,
\qquad
\gamma=\alpha\beta_1.
}
$$

La parte $\beta_1$ è già riconosciuta; $\beta_2$ resta da riconoscere.

> [!tip] Controlli prima di consegnare
> - La derivazione deve essere destra.
> - Il suffisso $w$ deve essere terminale.
> - Il passo finale deve usare la produzione dell’item.
> - Il prefisso è $\alpha\beta_1$, non soltanto $\beta_1$.
> - Un item completo ordinario indica una possibile riduzione, non l’accettazione.