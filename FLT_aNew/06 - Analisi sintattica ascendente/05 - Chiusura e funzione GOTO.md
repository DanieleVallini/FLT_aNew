# Chiusura e funzione GOTO

Le funzioni **CLOSURE** e **GOTO** permettono di costruire gli stati e le transizioni dell’automa LR(0).

Ogni stato è un insieme di item:

- CLOSURE completa l’insieme con le produzioni che possono iniziare nel contesto corrente;
- GOTO descrive il passaggio a un altro insieme dopo il riconoscimento di un simbolo grammaticale.

> [!important] Collegamento con l’esame
> La costruzione dell’intero automa LR(0) richiede di applicare ripetutamente queste due funzioni.
>
> Gli errori nelle chiusure iniziali si propagano agli stati successivi, alle tabelle e alla classificazione della grammatica.

---

## 1. Idea della chiusura

Supponiamo che un insieme contenga:

$$
[A\to\alpha\cdot B\beta].
$$

Il prossimo simbolo da riconoscere è il non terminale $B$.

Dobbiamo quindi considerare tutti i modi in cui può iniziare una struttura generata da $B$.

Per ogni produzione:

$$
B\to\eta,
$$

aggiungiamo:

$$
[B\to\cdot\eta].
$$

Se uno degli item aggiunti ha a sua volta un non terminale dopo il punto, ripetiamo il procedimento.

### Esempio essenziale

Con:

$$
S\to AB,
\qquad
A\to a,
$$

la presenza di:

$$
[S\to\cdot AB]
$$

richiede l’aggiunta di:

$$
[A\to\cdot a].
$$

Non aggiungiamo automaticamente le produzioni di $B$: il simbolo immediatamente dopo il punto è $A$.

---

## 2. Definizione formale di CLOSURE

Dato un insieme di item $I$, la sua chiusura è il più piccolo insieme che:

1. contiene tutti gli item di $I$;
2. soddisfa la regola:

$$
\boxed{
[A\to\alpha\cdot B\beta]\in\mathrm{CLOSURE}(I),
\quad B\to\eta\in P'
\Longrightarrow
[B\to\cdot\eta]\in\mathrm{CLOSURE}(I).
}
$$

Qui $B$ deve essere un non terminale.

### Procedura operativa

1. Copia in un insieme $C$ tutti gli item iniziali.
2. Esamina gli item di $C$.
3. Quando dopo il punto trovi un non terminale, aggiungi tutte le sue produzioni con il punto iniziale.
4. Esamina anche gli item appena aggiunti.
5. Fermati quando non vengono aggiunti nuovi item.

Gli item già presenti non vengono duplicati.

### Perché termina?

La grammatica possiede un numero finito di item.

Una produzione con lato destro lungo $r$ contribuisce con $r+1$ item.

Durante il procedimento l’insieme cresce, ma resta contenuto nell’insieme finito di tutti gli item possibili.

Quindi anche la ricorsione delle produzioni non rende infinita la chiusura.

### Due proprietà utili

$$
I\subseteq\mathrm{CLOSURE}(I),
$$

$$
\mathrm{CLOSURE}(\mathrm{CLOSURE}(I))
=
\mathrm{CLOSURE}(I).
$$

> [!warning] CLOSURE non sposta i punti
> Conserva gli item esistenti e aggiunge item con il punto iniziale.
>
> Non consuma input e non esegue riduzioni.

---

## 3. Definizione di GOTO

Sia $I$ un insieme di item e sia $X$ un simbolo grammaticale, terminale o non terminale.

Per calcolare:

$$
\mathrm{GOTO}(I,X),
$$

eseguiamo due operazioni.

### Prima operazione: avanzamento del punto

Selezioniamo tutti gli item in cui $X$ compare immediatamente dopo il punto:

$$
[A\to\alpha\cdot X\beta].
$$

Li trasformiamo in:

$$
[A\to\alpha X\cdot\beta].
$$

### Seconda operazione: chiusura

Applichiamo CLOSURE all’intero insieme ottenuto:

$$
\boxed{
\mathrm{GOTO}(I,X)
=
\mathrm{CLOSURE}
\left(
\left\{
[A\to\alpha X\cdot\beta]
\mid
[A\to\alpha\cdot X\beta]\in I
\right\}
\right).
}
$$

> [!important] Due controlli
> - Il punto avanza oltre un solo simbolo.
> - Se più item hanno lo stesso simbolo dopo il punto, devono essere avanzati tutti.

Se nessun item permette il movimento:

$$
\mathrm{GOTO}(I,X)=\varnothing.
$$

Nella costruzione canonica ordinaria questo risultato non genera un nuovo stato.

---

## 4. Esempio dell’esame di gennaio 2026

Consideriamo:

$$
S'\to S\downarrow
$$

$$
S\to ABcS\mid d
$$

$$
A\to aA\mid\varepsilon
$$

$$
B\to bB\mid\varepsilon.
$$

Lo stato iniziale è:

$$
I_0=
\mathrm{CLOSURE}
\left(
\{[S'\to\cdot S\downarrow]\}
\right).
$$

### Primo passaggio

Dopo il punto compare $S$, quindi aggiungiamo:

$$
[S\to\cdot ABcS],
$$

$$
[S\to\cdot d].
$$

### Secondo passaggio

Nel primo item aggiunto compare $A$ dopo il punto.

Aggiungiamo:

$$
[A\to\cdot aA],
$$

$$
[A\to\cdot].
$$

Il secondo item rappresenta la produzione $A\to\varepsilon$.

### Risultato

$$
\boxed{
I_0=
\left\{
\begin{array}{l}
[S'\to\cdot S\downarrow]\\
[S\to\cdot ABcS]\\
[S\to\cdot d]\\
[A\to\cdot aA]\\
[A\to\cdot]
\end{array}
\right\}.
}
$$

Non ci sono ulteriori aggiunte.

I punti davanti ad `a` e `d` sono davanti a terminali; l’item epsilon è già completo.

---

## 5. Il punto delicato: perché B non compare in I₀?

Anche se:

$$
A\Rightarrow\varepsilon,
$$

l’item:

$$
[S\to\cdot ABcS]
$$

rimane invariato.

La chiusura non lo trasforma automaticamente in:

$$
[S\to A\cdot BcS].
$$

Perciò non aggiungiamo ancora le produzioni di $B$.

> [!warning] Nullabilità e chiusura sono concetti distinti
> La produzione $A\to\varepsilon$ aggiunge l’item completo:
> $$
> [A\to\cdot].
> $$
> L’eventuale riduzione epsilon sarà un’azione del parser.
>
> CLOSURE non esegue quella riduzione e non salta $A$ negli altri item.

Lo stato che prevede $B$ viene raggiunto tramite:

$$
\mathrm{GOTO}(I_0,A).
$$

---

## 6. Calcolare GOTO(I₀, A)

In $I_0$ l’unico item con $A$ dopo il punto è:

$$
[S\to\cdot ABcS].
$$

Spostiamo il punto:

$$
[S\to A\cdot BcS].
$$

Ora dopo il punto compare $B$, quindi la chiusura aggiunge:

$$
[B\to\cdot bB],
$$

$$
[B\to\cdot].
$$

Otteniamo:

$$
\boxed{
I_A=\mathrm{GOTO}(I_0,A)
=
\left\{
\begin{array}{l}
[S\to A\cdot BcS]\\
[B\to\cdot bB]\\
[B\to\cdot]
\end{array}
\right\}.
}
$$

Da questo insieme possiamo poi calcolare:

$$
\boxed{
I_{AB}=\mathrm{GOTO}(I_A,B)
=
\{[S\to AB\cdot cS]\}.
}
$$

Qui la chiusura non aggiunge nulla, perché dopo il punto compare il terminale `c`.

### Collegamento con le riduzioni epsilon

Per riconoscere una parola che inizia con `c`, il parser deve prima riconoscere gli $A$ e $B$ vuoti.

La sequenza corretta prevede:

1. riduzione $A\to\varepsilon$;
2. passaggio allo stato $I_A$;
3. riduzione $B\to\varepsilon$;
4. passaggio allo stato $I_{AB}$;
5. spostamento di `c`.

Non serve una transizione diretta da $I_0$ su `c`.

---

## 7. Calcolare GOTO(I₀, a)

Il simbolo `a` compare dopo il punto nell’item:

$$
[A\to\cdot aA].
$$

Avanziamo il punto:

$$
[A\to a\cdot A].
$$

La chiusura deve ora aggiungere le produzioni di $A$:

$$
[A\to\cdot aA],
$$

$$
[A\to\cdot].
$$

Quindi:

$$
\boxed{
I_a=\mathrm{GOTO}(I_0,a)
=
\left\{
\begin{array}{l}
[A\to a\cdot A]\\
[A\to\cdot aA]\\
[A\to\cdot]
\end{array}
\right\}.
}
$$

### Un esempio di ciclo

Calcoliamo GOTO sullo stesso terminale:

$$
\mathrm{GOTO}(I_a,a).
$$

L’item che permette il movimento è ancora:

$$
[A\to\cdot aA].
$$

Dopo avanzamento e chiusura otteniamo nuovamente $I_a$:

$$
\boxed{\mathrm{GOTO}(I_a,a)=I_a.}
$$

L’automa avrà quindi un arco su `a` che ritorna nello stesso stato.

Non dobbiamo creare una copia dello stato a ogni ripetizione.

---

## 8. Altri risultati da I₀

| Simbolo $X$ | $\mathrm{GOTO}(I_0,X)$ |
|---|---|
| $S$ | $\{[S'\to S\cdot\downarrow]\}$ |
| $A$ | $I_A$ |
| `a` | $I_a$ |
| `d` | $\{[S\to d\cdot]\}$ |
| `b` | $\varnothing$ |
| `c` | $\varnothing$ |
| $B$ | $\varnothing$ |
| $\downarrow$ | $\varnothing$ |

Per decidere se esiste una transizione, guardiamo il simbolo immediatamente dopo il punto negli item dello stato corrente.

La semplice presenza di un simbolo in una produzione non basta.

> [!tip] Metodo di controllo
> Prima elenca i simboli che compaiono subito dopo un punto.
>
> Sono gli unici simboli per cui GOTO può risultare non vuota.

---

## 9. GOTO nella costruzione e nel parser

La funzione di costruzione:

$$
\mathrm{GOTO}(I,X)
$$

è definita sia per terminali sia per non terminali.

Nel parser, i due casi hanno ruoli diversi.

| Simbolo $X$ | Utilizzo |
|---|---|
| Terminale | La transizione determina lo stato raggiunto con uno shift |
| Non terminale | La transizione determina lo stato raggiunto dopo una riduzione |

Dopo una riduzione, il GOTO sul non terminale si consulta partendo dallo stato rimasto in cima alla pila dopo la rimozione del lato destro.

Nelle tabelle del parser:

- le transizioni sui terminali contribuiscono alle azioni di shift in ACTION;
- quelle sui non terminali vengono riportate nella tabella GOTO.

I dettagli operativi saranno sviluppati nella nota sulle tabelle e sulla simulazione.

---

## 10. Collegamento con gli automi non deterministici

La costruzione ricorda quella già studiata per gli automi con epsilon-mosse.

| Automi finiti | Costruzione LR(0) |
|---|---|
| Stato elementare | Item |
| Insieme di stati | Insieme di item |
| Epsilon-chiusura | CLOSURE |
| Movimento su un simbolo seguito da chiusura | GOTO |

Se un item aspetta un non terminale, le sue produzioni vengono considerate senza leggere input.

Questa è l’analogia che spiega la chiusura.

Non significa però che dobbiamo creare transizioni di GOTO etichettate epsilon: una produzione vuota è rappresentata dall’item completo $[A\to\cdot]$.

---

## 11. Errori da evitare e procedura d’esame

Gli errori più frequenti sono:

- dimenticare gli item di partenza durante la chiusura;
- aggiungere soltanto alcune produzioni del non terminale previsto;
- fermarsi prima di esaminare gli item appena aggiunti;
- espandere un non terminale che non è immediatamente dopo il punto;
- saltare automaticamente un non terminale nullable;
- spostare il punto oltre più simboli;
- dimenticare la chiusura dopo il movimento di GOTO;
- creare più stati con lo stesso insieme di item.

Per ogni GOTO:

1. Evidenzia tutti gli item con $X$ subito dopo il punto.
2. Avanza il punto di una posizione in ciascuno.
3. Riunisci gli item ottenuti.
4. Calcola la chiusura fino alla stabilizzazione.
5. Confronta l’insieme con gli stati già presenti.

> [!important] Non correggere artificialmente gli insiemi
> La presenza di item completi insieme ad altri item può far emergere conflitti.
>
> Gli item corretti devono essere conservati: i conflitti si analizzano nella classificazione della grammatica.