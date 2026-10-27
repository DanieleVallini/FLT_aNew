# Configurazioni, mosse e riconoscimento negli automi a pila

Per simulare un automa a pila non basta conoscere lo stato corrente: bisogna sapere anche quale parte dell’input resta da leggere e che cosa contiene la pila.

Queste informazioni formano una **configurazione**.

Una computazione è una sequenza di configurazioni collegate dalle mosse consentite dall’automa.

> [!important] Collegamento con l’esame
> La definizione formale di configurazione e di mossa compare tra le domande teoriche.
>
> Bisogna distinguere le mosse che consumano input dalle mosse epsilon e indicare con precisione come cambia la pila.

---

## 1. Definizione di configurazione

Consideriamo un NPDA:

$$
M=(Q,\Sigma,\Gamma,\delta,q_0,Z_0,F).
$$

Una configurazione è una tripla:

$$
\boxed{(q,w,\alpha)\in Q\times\Sigma^*\times\Gamma^*.}
$$

I componenti rappresentano:

| Componente | Significato |
|---|---|
| $q$ | Stato corrente |
| $w$ | Suffisso dell’input ancora da leggere |
| $\alpha$ | Contenuto completo della pila |

Adottiamo la convenzione:

$$
\boxed{\text{La cima della pila si trova a sinistra.}}
$$

Per esempio:

$$
(q,abb,AAZ_0)
$$

significa che:

- lo stato corrente è $q$;
- resta da leggere `abb`;
- la cima è $A$;
- sotto la cima si trovano un altro $A$ e poi $Z_0$.

### Configurazione iniziale

Per una parola di ingresso $w$, la configurazione iniziale è:

$$
\boxed{(q_0,w,Z_0).}
$$

### Configurazioni con componenti vuote

- $(q,\varepsilon,\alpha)$: l’input è stato consumato completamente.
- $(q,w,\varepsilon)$: la pila è vuota.
- $(q,\varepsilon,\varepsilon)$: input esaurito e pila vuota.

> [!warning] La configurazione contiene l’input rimanente
> In $(q,w,\alpha)$, $w$ non rappresenta necessariamente la parola iniziale né il prefisso già letto.
>
> Durante la computazione il prefisso consumato scompare dalla seconda componente.

---

## 2. Mossa che consuma un simbolo

Supponiamo che:

$$
(p,\gamma)\in\delta(q,a,X),
\qquad a\in\Sigma.
$$

Se:

- l’automa si trova in $q$;
- il prossimo simbolo è $a$;
- la cima è $X$;

può eseguire la mossa:

$$
\boxed{
(q,aw,X\beta)\vdash_M(p,w,\gamma\beta).
}
$$

La mossa:

1. consuma $a$;
2. passa da $q$ a $p$;
3. sostituisce la cima $X$ con $\gamma$;
4. conserva la parte sottostante $\beta$.

### Esempio

Se:

$$
(p,BA)\in\delta(q,a,A),
$$

allora:

$$
(q,abb,AZ_0)
\vdash_M
(p,bb,BAZ_0).
$$

È stato consumato il primo `a`.

La cima $A$ è stata sostituita con $BA$, quindi la nuova cima è $B$.

> [!important] La parte sottostante non cambia
> Una transizione agisce sulla cima.
>
> Il suffisso $\beta$ della pila viene riportato invariato nella configurazione successiva.

---

## 3. Mossa epsilon

Supponiamo che:

$$
(p,\gamma)\in\delta(q,\varepsilon,X).
$$

La mossa corrispondente è:

$$
\boxed{
(q,w,X\beta)\vdash_M(p,w,\gamma\beta).
}
$$

Lo stato e la pila possono cambiare, ma l’input rimanente resta identico.

### Esempio

Se:

$$
(p,\varepsilon)\in\delta(q,\varepsilon,A),
$$

allora:

$$
(q,bb,AAZ_0)
\vdash_M
(p,bb,AZ_0).
$$

La mossa rimuove un $A$ senza leggere nessuna `b`.

### Una mossa epsilon può essere disponibile prima della fine dell’input

Non bisogna aspettare che l’input sia esaurito.

Se la transizione è prevista per lo stato e la cima correnti, può essere applicata anche quando restano simboli da leggere.

---

## 4. Come verificare se una mossa è valida

Per controllare:

$$
(q,u,\alpha)\vdash_M(p,v,\eta),
$$

verifica quattro aspetti:

1. **Stato:** la transizione deve partire da $q$ e arrivare in $p$.
2. **Input:** deve consumare il primo simbolo di $u$, oppure non consumarne nessuno.
3. **Cima:** il simbolo richiesto dalla transizione deve coincidere con la cima effettiva.
4. **Sostituzione:** soltanto la cima viene sostituita; il resto della pila deve restare invariato.

Se la transizione sostituisce $X$ con $\gamma$, la lunghezza della pila cambia secondo:

$$
|\gamma\beta|=|X\beta|-1+|\gamma|.
$$

Quindi:

- se $\gamma=\varepsilon$, diminuisce di uno;
- se $|\gamma|=1$, resta invariata;
- se $|\gamma|>1$, aumenta.

Con la definizione adottata, una transizione richiede un simbolo di cima $X\in\Gamma$. Se la pila è vuota, non ci sono mosse applicabili.

---

## 5. Computazioni e relazione estesa

Una computazione finita è una sequenza:

$$
C_0\vdash_M C_1\vdash_M\cdots\vdash_M C_n,
$$

nella quale ogni passaggio è una mossa valida.

Scriviamo:

$$
\boxed{C\vdash_M^*D}
$$

se $D$ è raggiungibile da $C$ mediante **zero o più mosse**.

Formalmente, esistono $n\geq0$ e configurazioni $C_0,\ldots,C_n$ tali che:

$$
C_0=C,\qquad C_n=D,
$$

e:

$$
C_i\vdash_M C_{i+1}
\qquad\text{per }0\leq i<n.
$$

### Perché si ammettono zero mosse?

La relazione è riflessiva:

$$
C\vdash_M^*C.
$$

Per esempio, con accettazione per stato finale, se $q_0\in F$, la parola vuota può essere accettata senza eseguire alcuna mossa.

### Numero di mosse e lunghezza dell’input

Ogni mossa non epsilon consuma esattamente un simbolo.

Una computazione che consuma completamente una parola $w$ contiene quindi:

$$
|w|
$$

mosse che leggono input, più un eventuale numero di mosse epsilon.

---

## 6. Esempio completo di simulazione

Riprendiamo l’automa per:

$$
L=\{a^nb^n\mid n\geq1\}.
$$

Lo stato iniziale è $q_a$, la pila iniziale contiene $Z_0$ e l’unico stato finale è $q_f$.

Le transizioni non vuote sono:

$$
\begin{aligned}
\delta(q_a,a,Z_0)&=\{(q_a,AZ_0)\}\\
\delta(q_a,a,A)&=\{(q_a,AA)\}\\
\delta(q_a,b,A)&=\{(q_b,\varepsilon)\}\\
\delta(q_b,b,A)&=\{(q_b,\varepsilon)\}\\
\delta(q_b,\varepsilon,Z_0)&=\{(q_f,Z_0)\}.
\end{aligned}
$$

Simuliamo `aabb`.

| Passo | Stato | Input rimanente | Pila | Operazione |
|---|---|---|---|---|
| 0 | $q_a$ | `aabb` | $Z_0$ | Configurazione iniziale |
| 1 | $q_a$ | `abb` | $AZ_0$ | Legge `a` e inserisce $A$ |
| 2 | $q_a$ | `bb` | $AAZ_0$ | Legge `a` e inserisce $A$ |
| 3 | $q_b$ | `b` | $AZ_0$ | Legge `b` e rimuove $A$ |
| 4 | $q_b$ | $\varepsilon$ | $Z_0$ | Legge `b` e rimuove $A$ |
| 5 | $q_f$ | $\varepsilon$ | $Z_0$ | Mossa epsilon |

La parola viene accettata per stato finale.

Abbiamo eseguito cinque mosse su un input di quattro simboli: la mossa aggiuntiva è quella epsilon conclusiva.

---

## 7. Riconoscimento per stato finale

Il linguaggio accettato per stato finale è:

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

Servono entrambe le condizioni:

- input esaurito;
- stato finale.

La pila non deve necessariamente essere vuota.

### Esempio di mancata accettazione: troppe b

Sull’input `abb`, l’automa precedente può raggiungere:

$$
(q_a,abb,Z_0)
\vdash
(q_a,bb,AZ_0)
\vdash
(q_b,b,Z_0)
\vdash
(q_f,b,Z_0).
$$

Lo stato è finale, ma resta una `b`.

La parola non viene accettata.

### Esempio di mancata accettazione: poche b

Sull’input `aab` raggiunge:

$$
(q_b,\varepsilon,AZ_0).
$$

L’input è esaurito, ma lo stato non è finale e non ci sono mosse applicabili.

Anche questa parola viene rifiutata.

---

## 8. Riconoscimento per pila vuota

Il linguaggio accettato per pila vuota è:

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

Servono:

- input esaurito;
- pila completamente vuota.

Lo stato raggiunto non deve necessariamente appartenere a $F$.

Supponendo che le configurazioni indicate siano state raggiunte:

| Configurazione | Accetta per stato finale? | Accetta per pila vuota? |
|---|---|---|
| $(q_f,\varepsilon,AZ_0)$, con $q_f\in F$ | Sì | No |
| $(q,\varepsilon,\varepsilon)$, con $q\notin F$ | No | Sì |
| $(q_f,a,\varepsilon)$, con $q_f\in F$ | No | No |
| $(q_f,\varepsilon,\varepsilon)$, con $q_f\in F$ | Sì | Sì |

Nell’automa dell’esempio, $Z_0$ non viene mai rimosso.

Di conseguenza:

$$
L_{\mathrm{pv}}(M)=\varnothing,
$$

anche se $L_F(M)$ contiene tutte le parole $a^nb^n$ con $n\geq1$.

> [!warning] Cambiare criterio può cambiare il linguaggio
> L’equivalenza dei criteri per gli NPDA riguarda la possibilità di costruire automi equivalenti.
>
> Non permette di cambiare criterio su un automa qualsiasi mantenendo automaticamente lo stesso linguaggio.

---

## 9. Riconoscimento non deterministico

In un NPDA una configurazione può avere più configurazioni successive.

Consideriamo un piccolo automa con stato iniziale $q_0$, unico stato finale $f$ e transizioni:

$$
\delta(q_0,a,Z_0)=\{(p,Z_0),(r,Z_0)\},
$$

$$
\delta(p,\varepsilon,Z_0)=\{(f,Z_0)\}.
$$

Tutte le altre transizioni sono vuote.

Sull’input `a` esistono due rami.

Il primo accetta:

$$
(q_0,a,Z_0)
\vdash(p,\varepsilon,Z_0)
\vdash(f,\varepsilon,Z_0).
$$

Il secondo si blocca in uno stato non finale:

$$
(q_0,a,Z_0)
\vdash(r,\varepsilon,Z_0).
$$

La parola è comunque accettata.

> [!important] Criterio esistenziale
> Per dimostrare che una parola è accettata basta esibire una computazione accettante.
>
> Per dimostrare che non è accettata bisogna escludere ogni possibile computazione accettante.

Un singolo ramo fallito non dimostra il rifiuto dell’intera parola.

---

## 10. Arresto e accettazione sono concetti distinti

Una configurazione è bloccata quando non ci sono mosse applicabili.

Essere bloccati non significa automaticamente accettare: bisogna verificare il criterio adottato.

Viceversa, l’accettazione non richiede necessariamente che l’automa sia bloccato.

Se viene raggiunta una configurazione con input esaurito e stato finale, esiste già una computazione accettante, anche se sarebbero disponibili altre mosse epsilon.

Un automa può anche avere computazioni infinite, per esempio a causa di un ciclo epsilon.

Una computazione infinita non costituisce una computazione accettante. Tuttavia un altro ramo finito può accettare la stessa parola.

---

## 11. Schema di risposta per l’esame

Per definire configurazioni e mosse:

1. Indica la configurazione:
   $$
   (q,w,\alpha).
   $$

2. Specifica il significato delle tre componenti e la convenzione sulla cima.

3. Scrivi la mossa che legge input:
   $$
   (q,aw,X\beta)\vdash_M(p,w,\gamma\beta)
   $$
   se $(p,\gamma)\in\delta(q,a,X)$.

4. Scrivi la mossa epsilon:
   $$
   (q,w,X\beta)\vdash_M(p,w,\gamma\beta)
   $$
   se $(p,\gamma)\in\delta(q,\varepsilon,X)$.

5. Definisci $\vdash_M^*$ come zero o più mosse.

6. Se richiesto, aggiungi la condizione formale di accettazione.

> [!tip] Controllo durante una simulazione
> A ogni riga verifica: stato, prossimo simbolo, cima e sostituzione.
>
> Alla fine verifica separatamente input, stato e pila. Questo evita di confondere una computazione terminata con una computazione accettante.