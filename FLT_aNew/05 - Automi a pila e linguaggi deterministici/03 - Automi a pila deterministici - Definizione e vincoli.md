# Automi a pila deterministici - Definizione e vincoli

Un automa a pila deterministico, abbreviato **DPDA**, è un automa a pila nel quale non esistono scelte tra mosse alternative applicabili alla stessa configurazione.

La mossa successiva dipende univocamente da:

- stato corrente;
- cima della pila;
- prossimo simbolo di input, quando viene consumato.

Il punto delicato riguarda le **mosse epsilon**: una mossa che non legge input non deve competere con una mossa che lo legge.

> [!important] Collegamento con l’esame
> Per definire formalmente un DPDA non basta dire che “ogni transizione ha un solo risultato”.
>
> Bisogna anche escludere la competizione tra mosse epsilon e mosse che consumano input.

---

## 1. Definizione formale

Un DPDA può essere descritto dalla tupla:

$$
\boxed{
M=(Q,\Sigma,\Gamma,\delta,q_0,Z_0,F).
}
$$

I componenti hanno lo stesso significato visto per gli NPDA:

| Componente | Significato |
|---|---|
| $Q$ | Insieme finito degli stati |
| $\Sigma$ | Alfabeto di ingresso |
| $\Gamma$ | Alfabeto della pila |
| $q_0$ | Stato iniziale |
| $Z_0$ | Simbolo iniziale della pila |
| $F$ | Insieme degli stati finali |
| $\delta$ | Funzione di transizione |

Possiamo scrivere:

$$
\boxed{
\delta:
Q\times(\Sigma\cup\{\varepsilon\})\times\Gamma
\rightharpoonup
Q\times\Gamma^*.
}
$$

La freccia $\rightharpoonup$ indica una **funzione parziale**: per alcuni argomenti la transizione può non essere definita.

Quando è definita, restituisce una sola coppia:

$$
\delta(q,a,X)=(p,\gamma).
$$

La cima $X$ viene sostituita con $\gamma$, passando nello stato $p$.

Adottiamo ancora la convenzione della cima a sinistra.

---

## 2. I due vincoli di determinismo

### Primo vincolo: un solo risultato per ogni tripla

Fissati stato, simbolo di input e cima, deve esserci al massimo una transizione.

Usando la notazione insiemistica degli NPDA:

$$
\boxed{
|\delta_N(q,a,X)|\leq1.
}
$$

Questo vincolo vale anche per $a=\varepsilon$.

Non sono quindi ammesse due alternative come:

$$
\delta_N(q,a,X)=\{(p,X),(r,YX)\}.
$$

### Secondo vincolo: esclusione tra epsilon e lettura

Per ogni $q\in Q$ e $X\in\Gamma$:

$$
\boxed{
\delta(q,\varepsilon,X)\text{ definita}
\Longrightarrow
\delta(q,a,X)\text{ non definita per ogni }a\in\Sigma.
}
$$

Se, con stato $q$ e cima $X$, è disponibile una mossa epsilon, non deve essere disponibile alcuna mossa che legga input.

> [!important] I due vincoli sono entrambi necessari
> Il primo impedisce più risultati per gli stessi argomenti.
>
> Il secondo impedisce di scegliere tra consumare il prossimo simbolo e applicare una mossa epsilon.

---

## 3. Perché il primo vincolo da solo non basta

Supponiamo di avere:

$$
\delta(q,a,X)=(p,X)
$$

e:

$$
\delta(q,\varepsilon,X)=(r,X).
$$

Ogni transizione ha un solo risultato.

Tuttavia, dalla configurazione:

$$
(q,aw,X\beta)
$$

sono possibili entrambe le mosse:

$$
(q,aw,X\beta)\vdash(p,w,X\beta),
$$

$$
(q,aw,X\beta)\vdash(r,aw,X\beta).
$$

La prima consuma `a`; la seconda non consuma input.

Esiste una scelta, quindi l’automa non è deterministico.

> [!warning] Nessuna precedenza implicita
> La definizione di DPDA non prevede una regola come “prima prova la mossa che legge” oppure “dai precedenza a epsilon”.
>
> Le transizioni in competizione devono essere escluse dalla costruzione.

---

## 4. Quali combinazioni sono consentite?

### Input diversi: consentito

Possiamo avere:

$$
\delta(q,a,X)=(p,YX)
$$

e:

$$
\delta(q,b,X)=(r,\varepsilon).
$$

Non c’è competizione: il prossimo simbolo non può essere contemporaneamente `a` e `b`.

### Cime diverse: consentito

Possiamo avere:

$$
\delta(q,\varepsilon,X)=(p,X)
$$

e:

$$
\delta(q,a,Y)=(r,Y),
\qquad X\neq Y.
$$

Le due mosse richiedono cime differenti, quindi non sono applicabili alla stessa configurazione.

### Stesso stato e stessa cima: controllare epsilon

| Transizioni presenti | Compatibili con il determinismo? |
|---|---|
| Lettura di `a` e lettura di `b`, con la stessa cima | Sì, se ogni risultato è unico |
| Lettura di `a` con cime diverse | Sì, se ogni risultato è unico |
| Epsilon con cima $X$ e lettura con cima $Y\neq X$ | Sì |
| Epsilon e lettura con la stessa cima | No |
| Due risultati per la stessa tripla | No |

> [!tip] Raggruppa per coppia stato-cima
> Per controllare il secondo vincolo, considera separatamente ogni coppia $(q,X)$.
>
> Non basta osservare lo stato senza considerare la pila.

---

## 5. Esempio: il DPDA per aⁿbⁿ

Riprendiamo:

$$
L=\{a^nb^n\mid n\geq1\}.
$$

Usiamo:

$$
Q=\{q_a,q_b,q_f\},
\qquad
\Gamma=\{A,Z_0\},
$$

con stato iniziale $q_a$ e:

$$
F=\{q_f\}.
$$

Le transizioni sono:

$$
\begin{aligned}
\delta(q_a,a,Z_0)&=(q_a,AZ_0)\\
\delta(q_a,a,A)&=(q_a,AA)\\
\delta(q_a,b,A)&=(q_b,\varepsilon)\\
\delta(q_b,b,A)&=(q_b,\varepsilon)\\
\delta(q_b,\varepsilon,Z_0)&=(q_f,Z_0).
\end{aligned}
$$

Tutte le altre transizioni sono non definite.

### Verifica del primo vincolo

Ogni tripla ha un solo risultato.

### Verifica del secondo vincolo

L’unica mossa epsilon parte dalla coppia:

$$
(q_b,Z_0).
$$

Con questa stessa coppia non è definita alcuna transizione che legga input.

La transizione:

$$
\delta(q_b,b,A)=(q_b,\varepsilon)
$$

non crea un conflitto, perché richiede la cima $A$, non $Z_0$.

L’automa è quindi deterministico.

### Perché il cambio di fase non richiede una scelta

Finché legge `a`, l’automa inserisce simboli nella pila.

La prima `b` determina il passaggio da $q_a$ a $q_b$.

Non deve indovinare quando terminano le `a`: il simbolo letto identifica il cambio di fase.

---

## 6. Come aggiungere la parola vuota

Se vogliamo riconoscere:

$$
L=\{a^nb^n\mid n\geq0\},
$$

dobbiamo accettare anche $\varepsilon$.

Non basta rendere finale $q_a$.

Infatti l’automa può terminare la lettura di una parola come `aaa` mentre si trova ancora in $q_a$: rendendo finale quello stato, accetteremmo anche parole sbagliate.

Una soluzione consiste nell’introdurre un nuovo stato iniziale $s$:

- $s$ è finale, per accettare $\varepsilon$;
- la prima `a` porta nella fase di inserimento;
- non esistono transizioni che ritornano in $s$.

Aggiungiamo:

$$
\delta(s,a,Z_0)=(q_a,AZ_0)
$$

e scegliamo:

$$
F=\{s,q_f\}.
$$

Il resto del comportamento resta quello descritto.

> [!important] Controllare il caso vuoto
> Accettare $\varepsilon$ non deve rendere accettanti anche stati raggiunti dopo aver letto soltanto una parte della struttura richiesta.

---

## 7. Determinismo e presenza di separatori

Consideriamo:

$$
L=\{wcw^R\mid w\in\{a,b\}^*\}.
$$

Un DPDA può:

1. leggere $w$ e inserirne i simboli in pila;
2. leggere `c` e cambiare fase;
3. confrontare la parte successiva con i simboli estratti.

Il separatore `c` indica esattamente quando iniziare il confronto.

Per esempio:

$$
ab\,c\,ba.
$$

La parte inserita è `ab`; la pila restituisce prima `b` e poi `a`.

Nella strategia non deterministica per parole della forma $ww^R$, invece, il punto di passaggio viene scelto senza un separatore esplicito.

> [!tip] Domanda di progettazione
> Quale informazione permette all’automa di decidere quando cambiare fase?
>
> Può essere un simbolo di input, uno stato o una particolare cima della pila. Se serve una scelta non determinata da queste informazioni, bisogna ripensare la costruzione.

---

## 8. Linguaggi deterministici

Un linguaggio è **context-free deterministico** se esiste un DPDA che lo riconosce per stato finale.

La classe viene indicata con:

$$
\mathrm{DCFL}.
$$

Formalmente:

$$
\boxed{
L\in\mathrm{DCFL}
\iff
\exists M\text{ DPDA tale che }L=L_F(M).
}
$$

Ogni DPDA è anche interpretabile come NPDA, sostituendo ogni risultato con il corrispondente insieme singoletto.

Di conseguenza:

$$
\mathrm{DCFL}\subseteq\mathrm{CFL}.
$$

Il linguaggio:

$$
\{a^nb^n\mid n\geq1\}
$$

mostra che un linguaggio deterministico può essere non regolare.

> [!warning] “Deterministico” non significa “regolare”
> Un DPDA dispone della pila.
>
> Il determinismo limita le scelte tra mosse, ma non elimina questa memoria.

---

## 9. Stato finale e pila vuota: attenzione al criterio

Per gli NPDA abbiamo visto l’equivalenza, a livello di classi di linguaggi, tra accettazione per stato finale e per pila vuota.

Questo risultato non si trasferisce automaticamente ai DPDA.

Nel modello adottato, senza un marcatore finale dell’input, un linguaggio accettato da un DPDA per pila vuota deve essere **libero da prefissi**:

> Nessuna parola del linguaggio può essere prefisso proprio di un’altra parola del linguaggio.

Infatti, una volta svuotata la pila per accettare una parola, non ci sono più mosse applicabili per leggere un’eventuale continuazione.

Le mosse epsilon non permettono di evitare il problema: quando sono disponibili, non possono competere con mosse che leggono la continuazione.

Per esempio:

$$
L=\{a,aa\}
$$

è regolare e riconoscibile da un DPDA per stato finale, ma non soddisfa la proprietà dei prefissi.

### Eventuale marcatore di fine input

Se si usa un simbolo speciale di fine input, per esempio $\dashv$, bisogna dichiararlo esplicitamente.

L’automa può allora riconoscere la fine leggendo quel simbolo e rimandare a quel momento lo svuotamento della pila.

> [!warning] Due marcatori diversi
> $Z_0$ è un simbolo della pila.
>
> $\dashv$, quando previsto, è un marcatore dell’input.
>
> Hanno funzioni differenti.

---

## 10. Determinismo non significa terminazione garantita

Un DPDA può avere un ciclo epsilon.

Per esempio:

$$
\delta(q,\varepsilon,X)=(q,X)
$$

ripete la stessa configurazione senza consumare input.

Il comportamento è deterministico: c’è una sola mossa possibile.

Tuttavia la computazione può proseguire indefinitamente.

L’accettazione resta definita dalla raggiungibilità di una configurazione accettante mediante un numero finito di mosse.

Quindi bisogna distinguere:

- unicità della mossa successiva;
- esistenza di una computazione accettante;
- terminazione della computazione.

---

## 11. Procedura di verifica all’esame

Per controllare che un automa a pila sia deterministico:

1. Fissa una coppia $(q,X)$.
2. Controlla che ogni simbolo di input abbia al massimo un risultato.
3. Controlla che la mossa epsilon abbia al massimo un risultato.
4. Se esiste la mossa epsilon, verifica che non esistano mosse che leggono input con la stessa coppia $(q,X)$.
5. Ripeti per tutte le coppie stato-cima.
6. Specifica il criterio di accettazione.
7. Verifica separatamente l’eventuale parola vuota.

> [!important] Formula da ricordare
> Un DPDA ha:
>
> **al massimo un risultato per ogni tripla**, e
>
> **nessuna competizione tra epsilon e lettura per la stessa coppia stato-cima**.