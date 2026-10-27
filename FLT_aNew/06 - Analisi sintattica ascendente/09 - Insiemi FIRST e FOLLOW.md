# Insiemi FIRST e FOLLOW

## 1. A cosa servono

FIRST e FOLLOW descrivono proprietà della grammatica utili per costruire un riconoscitore.

- **FIRST** indica quali terminali possono comparire all’inizio di una forma derivabile da un simbolo o da una sequenza.
- **FOLLOW** indica quali terminali possono comparire immediatamente dopo un non terminale nelle forme sentenziali.

Nell’analisi SLR(1), FOLLOW permette di limitare le colonne nelle quali inserire una riduzione.

Per calcolare correttamente questi insiemi dobbiamo prima individuare quali simboli possono generare la stringa vuota.

> [!important] Convenzione
> In questa nota FIRST contiene anche $\varepsilon$ quando la sequenza considerata può annullarsi.
> FOLLOW non contiene mai $\varepsilon$.

---

## 2. Simboli e sequenze annullabili

Un non terminale $A$ è **annullabile**, o *nullable*, quando:

$$
A\Rightarrow^*\varepsilon
$$

Una sequenza:

$$
X_1X_2\cdots X_k
$$

è annullabile se tutti i suoi simboli sono annullabili.

Un terminale non può annullarsi.

### 2.1 Calcolo dei non terminali annullabili

Si procede per aggiunte successive:

1. segna come annullabile ogni $A$ con una produzione $A\to\varepsilon$;
2. se esiste $A\to X_1\cdots X_k$ e tutti gli $X_i$ sono annullabili, segna anche $A$;
3. ripeti finché non compaiono nuovi non terminali annullabili.

Per esempio:

$$
\begin{aligned}
S&\to AB\\
A&\to\varepsilon\\
B&\to\varepsilon
\end{aligned}
$$

Prima riconosciamo che $A$ e $B$ sono annullabili; successivamente riconosciamo che anche $S$ lo è.

> [!warning]
> Non serve una produzione esplicita $S\to\varepsilon$ perché $S$ sia annullabile: basta che possa derivare $\varepsilon$ attraverso altre produzioni.

La sequenza vuota è annullabile per definizione.

---

## 3. Definizione di FIRST

Sia:

$$
V=N\cup\Sigma
$$

Per una sequenza $\alpha\in V^*$ e un terminale $a$:

$$
a\in\operatorname{FIRST}(\alpha)
\iff
\alpha\Rightarrow^*a\beta
$$

per qualche $\beta\in V^*$.

Inoltre:

$$
\varepsilon\in\operatorname{FIRST}(\alpha)
\iff
\alpha\Rightarrow^*\varepsilon
$$

Quindi FIRST raccoglie i possibili terminali iniziali e registra l’eventuale possibilità di annullarsi.

### 3.1 Casi fondamentali

Per un terminale $a$:

$$
\operatorname{FIRST}(a)=\{a\}
$$

Per la sequenza vuota:

$$
\operatorname{FIRST}(\varepsilon)=\{\varepsilon\}
$$

Se un non terminale ha più produzioni, si uniscono i contributi dei loro corpi.

Per esempio:

$$
A\to aB\mid c\mid\varepsilon
$$

implica:

$$
\operatorname{FIRST}(A)=\{a,c,\varepsilon\}
$$

---

## 4. FIRST di una sequenza e algoritmo di calcolo

Per calcolare:

$$
\operatorname{FIRST}(X_1X_2\cdots X_k)
$$

esaminiamo i simboli da sinistra verso destra:

1. aggiungiamo $\operatorname{FIRST}(X_1)\setminus\{\varepsilon\}$;
2. se $X_1$ è annullabile, consideriamo anche $X_2$;
3. continuiamo finché tutti i simboli precedenti sono annullabili;
4. ci fermiamo al primo simbolo non annullabile;
5. aggiungiamo $\varepsilon$ soltanto se l’intera sequenza è annullabile.

### 4.1 Esempio con più simboli annullabili

Supponiamo:

$$
A\to a\mid\varepsilon
$$

$$
B\to b\mid\varepsilon
$$

Allora:

$$
\operatorname{FIRST}(AB)=\{a,b,\varepsilon\}
$$

Infatti:

- `a` può essere generata da $A$;
- se $A$ si annulla, `b` può essere generata da $B$;
- se entrambi si annullano, si ottiene $\varepsilon$.

Aggiungendo il terminale `c`:

$$
\operatorname{FIRST}(ABc)=\{a,b,c\}
$$

La sequenza non è annullabile perché `c` deve essere generata.

> [!warning] FIRST non è una semplice unione
> Si può passare al simbolo successivo soltanto quando tutti quelli precedenti possono annullarsi.
> Inoltre, $\varepsilon$ appartiene al risultato soltanto se può annullarsi tutta la sequenza.

### 4.2 Calcolo per l’intera grammatica

Per ottenere FIRST di tutti i non terminali:

1. inizializza i loro insiemi a $\varnothing$;
2. per ogni produzione $A\to\alpha$, aggiungi a FIRST$(A)$ i contributi di FIRST$(\alpha)$;
3. ripeti usando gli insiemi aggiornati;
4. termina quando un’intera scansione delle produzioni non modifica alcun insieme.

Gli insiemi crescono soltanto per aggiunta e possono contenere un numero finito di elementi. Il procedimento quindi termina.

La ricorsione, anche sinistra, viene gestita da questa iterazione: non richiede di eliminare la ricorsione dalla grammatica.

---

## 5. Definizione e calcolo di FOLLOW

FOLLOW si calcola per i non terminali.

Un terminale $t$ appartiene a FOLLOW$(A)$ se può comparire immediatamente dopo $A$ in una forma sentenziale derivabile dal simbolo iniziale.

Utilizzando la grammatica aumentata:

$$
t\in\operatorname{FOLLOW}(A)
\iff
S'\Rightarrow^*\mu A t\nu
$$

per opportune sequenze $\mu,\nu$.

Qui $t$ è un terminale, compreso l’eventuale marcatore finale.

### 5.1 Il marcatore nella convenzione dell’esame

Manteniamo:

$$
S'\to S\downarrow
$$

Questa produzione impone:

$$
\downarrow\in\operatorname{FOLLOW}(S)
$$

Il marcatore compare esplicitamente dopo $S$.

> [!important] Marcatore e fine dell’input
> Nei FOLLOW dei non terminali originali utilizziamo $\downarrow$.
> La colonna **fine**, introdotta per indicare l’input esaurito dopo lo spostamento di $\downarrow$, non è un ulteriore terminale da aggiungere a questi insiemi.

### 5.2 Regola del suffisso

Per ogni produzione:

$$
A\to\alpha B\beta
$$

aggiungiamo a FOLLOW$(B)$:

$$
\operatorname{FIRST}(\beta)\setminus\{\varepsilon\}
$$

In forma di aggiornamento:

$$
\operatorname{FOLLOW}(B)
\leftarrow
\operatorname{FOLLOW}(B)
\cup
\left(
\operatorname{FIRST}(\beta)\setminus\{\varepsilon\}
\right)
$$

Questi terminali possono comparire dopo $B$ perché possono iniziare ciò che viene generato dal suffisso $\beta$.

### 5.3 Regola della propagazione

Se il suffisso può annullarsi:

$$
\beta\Rightarrow^*\varepsilon
$$

aggiungiamo anche:

$$
\operatorname{FOLLOW}(A)
\subseteq
\operatorname{FOLLOW}(B)
$$

Infatti, quando $\beta$ scompare, ciò che può seguire $A$ può seguire anche $B$.

La regola vale anche quando $B$ è l’ultimo simbolo del corpo: in quel caso $\beta=\varepsilon$.

### 5.4 Procedura iterativa

1. Inizializza gli insiemi FOLLOW dei non terminali originali a $\varnothing$.
2. Aggiungi $\downarrow$ a FOLLOW$(S)$.
3. Applica le due regole a ogni occorrenza di non terminale nei corpi.
4. Ripeti fino a quando nessun insieme cambia.

> [!warning] Direzione della propagazione
> Da $A\to\alpha B\beta$, con $\beta$ annullabile, si trasferisce FOLLOW$(A)$ dentro FOLLOW$(B)$.
> Il trasferimento va dal non terminale a sinistra della produzione al non terminale considerato nel corpo.

---

## 6. Esempio con suffisso annullabile

Completiamo l’esempio precedente:

$$
\begin{aligned}
S'&\to S\downarrow\\
S&\to AB\\
A&\to a\mid\varepsilon\\
B&\to b\mid\varepsilon
\end{aligned}
$$

Tutti e tre i non terminali originali sono annullabili.

I FIRST sono:

$$
\begin{aligned}
\operatorname{FIRST}(A)&=\{a,\varepsilon\}\\
\operatorname{FIRST}(B)&=\{b,\varepsilon\}\\
\operatorname{FIRST}(S)&=\{a,b,\varepsilon\}
\end{aligned}
$$

Per FOLLOW partiamo da:

$$
\operatorname{FOLLOW}(S)=\{\downarrow\}
$$

Nella produzione $S\to AB$:

- dopo $A$ si trova $B$, quindi aggiungiamo `b` a FOLLOW$(A)$;
- $B$ è annullabile, quindi aggiungiamo anche FOLLOW$(S)$ a FOLLOW$(A)$;
- $B$ è l’ultimo simbolo, quindi riceve FOLLOW$(S)$.

Otteniamo:

| Non terminale | FIRST | FOLLOW |
|---|---|---|
| $S$ | $\{a,b,\varepsilon\}$ | $\{\downarrow\}$ |
| $A$ | $\{a,\varepsilon\}$ | $\{b,\downarrow\}$ |
| $B$ | $\{b,\varepsilon\}$ | $\{\downarrow\}$ |

Questo esempio mostra perché bisogna controllare se **l’intero suffisso** può annullarsi.

---

## 7. Calcolo completo per l’esame del 26 gennaio 2026

La grammatica è:

$$
\begin{aligned}
(0)\quad&S'\to S\downarrow\\
(1)\quad&S\to ABcS\\
(2)\quad&S\to d\\
(3)\quad&A\to aA\\
(4)\quad&A\to\varepsilon\\
(5)\quad&B\to bB\\
(6)\quad&B\to\varepsilon
\end{aligned}
$$

### 7.1 Annullabilità

$A$ e $B$ sono annullabili grazie alle produzioni vuote.

$S$ non è annullabile:

- $S\to d$ introduce un terminale;
- $S\to ABcS$ introduce necessariamente almeno il terminale `c`.

### 7.2 FIRST di A e B

Da $A\to aA\mid\varepsilon$:

$$
\operatorname{FIRST}(A)=\{a,\varepsilon\}
$$

Da $B\to bB\mid\varepsilon$:

$$
\operatorname{FIRST}(B)=\{b,\varepsilon\}
$$

### 7.3 FIRST di S

Per il corpo $ABcS$:

- se $A$ produce una stringa non vuota, il primo terminale è `a`;
- se $A$ si annulla e $B$ produce una stringa non vuota, il primo terminale è `b`;
- se entrambi si annullano, il primo terminale è `c`.

Quindi:

$$
\operatorname{FIRST}(ABcS)=\{a,b,c\}
$$

Il terminale `c` interrompe l’esame della sequenza: il successivo $S$ non contribuisce al FIRST di questo corpo.

La produzione $S\to d$ aggiunge `d`:

$$
\boxed{\operatorname{FIRST}(S)=\{a,b,c,d\}}
$$

### 7.4 FOLLOW di S

Da $S'\to S\downarrow$:

$$
\downarrow\in\operatorname{FOLLOW}(S)
$$

Nella produzione $S\to ABcS$, l’ultimo $S$ riceve FOLLOW$(S)$ stesso. Non vengono aggiunti nuovi elementi.

Pertanto:

$$
\boxed{\operatorname{FOLLOW}(S)=\{\downarrow\}}
$$

### 7.5 FOLLOW di A

Nella produzione:

$$
S\to A\,BcS
$$

il suffisso dopo $A$ è $BcS$.

Poiché $B$ può annullarsi:

$$
\operatorname{FIRST}(BcS)=\{b,c\}
$$

Il suffisso non è annullabile, perché contiene il terminale `c`.

Quindi aggiungiamo `b` e `c`, senza propagare FOLLOW$(S)$.

La produzione $A\to aA$ trasferisce FOLLOW$(A)$ a se stesso e non aggiunge elementi.

Otteniamo:

$$
\boxed{\operatorname{FOLLOW}(A)=\{b,c\}}
$$

### 7.6 FOLLOW di B

Il suffisso dopo $B$ è $cS$:

$$
\operatorname{FIRST}(cS)=\{c\}
$$

Anche questo suffisso non è annullabile.

La produzione $B\to bB$ non aggiunge ulteriori elementi.

Quindi:

$$
\boxed{\operatorname{FOLLOW}(B)=\{c\}}
$$

### 7.7 Risultato complessivo

| Non terminale | Annullabile | FIRST | FOLLOW |
|---|---|---|---|
| $S$ | No | $\{a,b,c,d\}$ | $\{\downarrow\}$ |
| $A$ | Sì | $\{a,\varepsilon\}$ | $\{b,c\}$ |
| $B$ | Sì | $\{b,\varepsilon\}$ | $\{c\}$ |

> [!important] Il ruolo di `c`
> In $S\to ABcS$, il terminale `c` impedisce ai suffissi dopo $A$ e $B$ di annullarsi.
> Per questo $\downarrow$ non entra nei loro FOLLOW.

---

## 8. Collegamento con le riduzioni SLR(1)

Se uno stato contiene il candidato completo:

$$
[A\to\alpha\cdot]
$$

SLR(1) inserisce la riduzione soltanto nelle colonne:

$$
t\in\operatorname{FOLLOW}(A)
$$

Per la grammatica di gennaio:

- le riduzioni delle produzioni di $A$ vengono inserite su `b` e `c`;
- quelle delle produzioni di $B$ su `c`;
- quelle delle produzioni ordinarie di $S$ su $\downarrow$.

La regola vale sia per le produzioni vuote sia per quelle non vuote.

> [!warning] FIRST e FOLLOW hanno ruoli diversi
> Per stabilire le colonne di una riduzione SLR si usa FOLLOW del non terminale a sinistra della produzione.
> FIRST serve, fra le altre cose, a calcolare correttamente quei FOLLOW.

---

## 9. Procedura ed errori da evitare all’esame

Procedura consigliata:

1. individua i non terminali annullabili;
2. calcola FIRST fino alla stabilizzazione;
3. inizializza FOLLOW$(S)$ con $\downarrow$;
4. esamina ogni occorrenza nei corpi, considerando tutto il suffisso successivo;
5. aggiungi FIRST del suffisso senza $\varepsilon$;
6. se il suffisso è annullabile, propaga FOLLOW del non terminale a sinistra;
7. ripeti fino alla stabilizzazione e presenta una tabella finale.

Controlli essenziali:

- FIRST contiene soltanto terminali ed eventualmente $\varepsilon$;
- FOLLOW contiene soltanto terminali, incluso il marcatore;
- $\varepsilon$ non entra mai in FOLLOW;
- FIRST$(\varepsilon)=\{\varepsilon\}$, che è diverso da $\varnothing$;
- per proseguire nel calcolo di FIRST devono annullarsi tutti i simboli precedenti;
- FOLLOW si propaga soltanto quando può annullarsi tutto il suffisso;
- una sola scansione delle produzioni può non bastare.

## Collegamenti

- [[08 - Conflitti e classificazione LR0]]
- [[10 - Analisi SLR1 e risoluzione dei conflitti]]
- [[11 - Analisi LR1 - Cenni]]