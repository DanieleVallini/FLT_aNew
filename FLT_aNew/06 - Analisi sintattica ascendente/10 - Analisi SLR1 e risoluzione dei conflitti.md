# Analisi SLR(1) e risoluzione dei conflitti

## 1. Idea del metodo SLR(1)

Il metodo **SLR(1)**, cioè *Simple LR con un simbolo di lookahead*, utilizza:

- lo stesso automa di candidati LR(0);
- gli insiemi FOLLOW;
- il prossimo simbolo di input per limitare le riduzioni.

La differenza fondamentale rispetto a LR(0) riguarda il candidato completo:

$$
[A\to\alpha\cdot]
$$

Nel metodo LR(0), la riduzione viene inserita in tutte le colonne ACTION.

Nel metodo SLR(1), viene inserita soltanto nelle colonne:

$$
t\in\operatorname{FOLLOW}(A)
$$

L’idea è che, dopo aver riconosciuto una stringa derivabile da $A$, il prossimo terminale debba essere compatibile con ciò che può seguire $A$.

> [!important]
> SLR(1) usa candidati LR(0).
> Non aggiunge lookahead ai candidati e non richiede di ricostruire l’automa.

---

## 2. Costruzione delle tabelle SLR(1)

Supponiamo di avere già costruito la collezione canonica:

$$
\mathcal C=\{I_0,I_1,\ldots,I_n\}
$$

e di conoscere FOLLOW di tutti i non terminali.

### 2.1 Spostamenti

Se:

$$
[A\to\alpha\cdot t\beta]\in I_i
$$

con $t$ terminale, e:

$$
\operatorname{GOTO}(I_i,t)=I_j
$$

inseriamo:

$$
\operatorname{ACTION}[i,t]=s_j
$$

Gli spostamenti vengono costruiti come nel metodo LR(0).

### 2.2 Riduzioni

Se:

$$
[A\to\alpha\cdot]\in I_i
$$

con $A\ne S'$, inseriamo la riduzione mediante $A\to\alpha$ soltanto per:

$$
t\in\operatorname{FOLLOW}(A)
$$

Quindi:

$$
\operatorname{ACTION}[i,t]=r_{A\to\alpha}
$$

per ogni $t$ ammesso da FOLLOW$(A)$.

> [!warning] Servono entrambe le condizioni
> Per inserire una riduzione occorre che il candidato sia completo nello stato considerato e che il terminale appartenga al FOLLOW appropriato.
> La sola appartenenza a FOLLOW non autorizza una riduzione.

### 2.3 GOTO

Per ogni non terminale $A$, se:

$$
\operatorname{GOTO}(I_i,A)=I_j
$$

inseriamo:

$$
\operatorname{GOTO}[i,A]=j
$$

Anche questa parte resta identica alla costruzione LR(0).

### 2.4 Accettazione

Manteniamo la convenzione dell’esame:

$$
S'\to S\downarrow
$$

Se uno stato contiene:

$$
[S'\to S\downarrow\cdot]
$$

inseriamo `acc` nella colonna **fine**, che indica l’input esaurito.

Il marcatore $\downarrow$ viene quindi spostato prima dell’accettazione.

Le celle non definite rappresentano errori.

---

## 3. Condizioni per eliminare i conflitti

### 3.1 Conflitto shift/reduce

Supponiamo che uno stato contenga:

$$
[A\to\alpha\cdot]
$$

e:

$$
[B\to\beta\cdot t\gamma]
$$

Lo spostamento su $t$ resta presente.

La riduzione compete con esso soltanto se:

$$
t\in\operatorname{FOLLOW}(A)
$$

Se:

$$
t\notin\operatorname{FOLLOW}(A)
$$

la riduzione non viene inserita in quella colonna e il conflitto scompare.

### 3.2 Conflitto reduce/reduce

Se nello stesso stato sono presenti due candidati completi distinti:

$$
[A\to\alpha\cdot]
\qquad
[B\to\beta\cdot]
$$

le riduzioni competono nelle colonne appartenenti a:

$$
\operatorname{FOLLOW}(A)
\cap
\operatorname{FOLLOW}(B)
$$

Se l’intersezione è vuota, quelle due riduzioni non producono un conflitto SLR(1).

> [!important] Il controllo è locale
> Due FOLLOW possono avere elementi in comune senza causare conflitti.
> Le riduzioni devono comparire nello stesso stato e nella stessa colonna ACTION.

Una grammatica è SLR(1) se, dopo la costruzione, ogni cella ACTION contiene al massimo un’azione.

---

## 4. Grammatica dell’esame del 26 gennaio 2026

Riprendiamo la numerazione già utilizzata:

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

Gli insiemi FOLLOW sono:

$$
\begin{aligned}
\operatorname{FOLLOW}(S)&=\{\downarrow\}\\
\operatorname{FOLLOW}(A)&=\{b,c\}\\
\operatorname{FOLLOW}(B)&=\{c\}
\end{aligned}
$$

Usiamo esattamente gli stati $I_0,\ldots,I_{11}$ della nota sulla costruzione dell’automa LR(0).

Le riduzioni vengono quindi distribuite così:

| Riduzione | Produzione | Colonne ammesse |
|---|---|---|
| $r_1$ | $S\to ABcS$ | $\downarrow$ |
| $r_2$ | $S\to d$ | $\downarrow$ |
| $r_3$ | $A\to aA$ | `b`, `c` |
| $r_4$ | $A\to\varepsilon$ | `b`, `c` |
| $r_5$ | $B\to bB$ | `c` |
| $r_6$ | $B\to\varepsilon$ | `c` |

La restrizione mediante FOLLOW riguarda tutte le riduzioni ordinarie, comprese quelle di produzioni non vuote.

---

## 5. Eliminazione dei conflitti LR(0)

Nella costruzione LR(0) avevamo trovato conflitti negli stati:

$$
I_0,\ I_2,\ I_4,\ I_7,\ I_9
$$

Con SLR(1):

| Stati | Riduzione | Colonne della riduzione | Terminali di spostamento |
|---|---|---|---|
| $I_0$, $I_9$ | $r_4$ | `b`, `c` | `a`, `d` |
| $I_4$ | $r_4$ | `b`, `c` | `a` |
| $I_2$, $I_7$ | $r_6$ | `c` | `b` |

Le colonne delle riduzioni sono separate da quelle degli spostamenti concorrenti.

Per esempio, nello stato $I_0$:

$$
\operatorname{ACTION}[0,a]=s_4
$$

perché:

$$
a\notin\operatorname{FOLLOW}(A)
$$

Invece:

$$
\operatorname{ACTION}[0,b]=r_4
$$

perché:

$$
b\in\operatorname{FOLLOW}(A)
$$

Tutti i conflitti shift/reduce scompaiono. Non sono presenti conflitti reduce/reduce negli altri stati.

Possiamo quindi concludere:

$$
\boxed{\text{La grammatica è SLR(1), ma non è LR(0).}}
$$

---

## 6. Tabella ACTION e GOTO completa

Nelle celle utilizziamo direttamente il numero dello stato.

| Stato | ACTION `a` | ACTION `b` | ACTION `c` | ACTION `d` | ACTION $\downarrow$ | ACTION fine | GOTO $S$ | GOTO $A$ | GOTO $B$ |
|---|---|---|---|---|---|---|---|---|---|
| 0 | $s_4$ | $r_4$ | $r_4$ | $s_3$ | — | — | 1 | 2 | — |
| 1 | — | — | — | — | $s_5$ | — | — | — | — |
| 2 | — | $s_7$ | $r_6$ | — | — | — | — | — | 6 |
| 3 | — | — | — | — | $r_2$ | — | — | — | — |
| 4 | $s_4$ | $r_4$ | $r_4$ | — | — | — | — | 8 | — |
| 5 | — | — | — | — | — | `acc` | — | — | — |
| 6 | — | — | $s_9$ | — | — | — | — | — | — |
| 7 | — | $s_7$ | $r_6$ | — | — | — | — | — | 10 |
| 8 | — | $r_3$ | $r_3$ | — | — | — | — | — | — |
| 9 | $s_4$ | $r_4$ | $r_4$ | $s_3$ | — | — | 11 | 2 | — |
| 10 | — | — | $r_5$ | — | — | — | — | — | — |
| 11 | — | — | — | — | $r_1$ | — | — | — | — |

Il simbolo `—` indica una cella vuota.

> [!warning]
> Una cella vuota non va completata scegliendo un’azione “plausibile”.
> Se viene consultata durante il riconoscimento, il parser segnala un errore.

Per esempio:

$$
\operatorname{ACTION}[7,d]=\text{errore}
$$

---

## 7. Simulazione di `cbcd↓`

La pila contiene alternativamente stati e simboli, con la cima a destra.

Ogni riga mostra la configurazione prima dell’azione indicata.

| Passo | Pila | Input residuo | Azione |
|---|---|---|---|
| 0 | `0` | `cbcd↓` | $r_4$: $A\to\varepsilon$ |
| 1 | `0 A 2` | `cbcd↓` | $r_6$: $B\to\varepsilon$ |
| 2 | `0 A 2 B 6` | `cbcd↓` | $s_9$: sposta `c` |
| 3 | `0 A 2 B 6 c 9` | `bcd↓` | $r_4$: $A\to\varepsilon$ |
| 4 | `0 A 2 B 6 c 9 A 2` | `bcd↓` | $s_7$: sposta `b` |
| 5 | `0 A 2 B 6 c 9 A 2 b 7` | `cd↓` | $r_6$: $B\to\varepsilon$ |
| 6 | `0 A 2 B 6 c 9 A 2 b 7 B 10` | `cd↓` | $r_5$: $B\to bB$ |
| 7 | `0 A 2 B 6 c 9 A 2 B 6` | `cd↓` | $s_9$: sposta `c` |
| 8 | `0 A 2 B 6 c 9 A 2 B 6 c 9` | `d↓` | $s_3$: sposta `d` |
| 9 | `0 A 2 B 6 c 9 A 2 B 6 c 9 d 3` | `↓` | $r_2$: $S\to d$ |
| 10 | `0 A 2 B 6 c 9 A 2 B 6 c 9 S 11` | `↓` | $r_1$: $S\to ABcS$ |
| 11 | `0 A 2 B 6 c 9 S 11` | `↓` | $r_1$: $S\to ABcS$ |
| 12 | `0 S 1` | `↓` | $s_5$: sposta `↓` |
| 13 | `0 S 1 ↓ 5` | $\varepsilon$ | `acc` |

La parola viene accettata.

Le due applicazioni di $S\to ABcS$ corrispondono ai blocchi `c` e `bc`; la produzione $S\to d$ fornisce il terminale conclusivo.

---

## 8. I passaggi da capire nella simulazione

### 8.1 Riduzioni epsilon prima del primo spostamento

All’inizio:

$$
\operatorname{ACTION}[0,c]=r_4
$$

Riducendo mediante $A\to\varepsilon$, non eliminiamo elementi dalla pila.

Consultiamo:

$$
\operatorname{GOTO}[0,A]=2
$$

e otteniamo:

$$
0\,A\,2
$$

L’input è ancora `cbcd↓`.

Poi:

$$
\operatorname{ACTION}[2,c]=r_6
$$

Con $B\to\varepsilon$ non eliminiamo elementi e usiamo:

$$
\operatorname{GOTO}[2,B]=6
$$

ottenendo:

$$
0\,A\,2\,B\,6
$$

Soltanto adesso spostiamo la prima `c`.

> [!important]
> Una riduzione epsilon può aggiungere un non terminale e uno stato senza consumare input.
> Non viene inserito né spostato un simbolo $\varepsilon$.

### 8.2 Due riduzioni uguali con destinazioni diverse

Ai passi 10 e 11 applichiamo:

$$
S\to ABcS
$$

Il corpo contiene quattro simboli: eliminiamo quattro coppie simbolo-stato, cioè otto elementi.

Alla prima riduzione rimane:

$$
0\,A\,2\,B\,6\,c\,9
$$

Quindi consultiamo:

$$
\operatorname{GOTO}[9,S]=11
$$

e otteniamo la pila del passo 11.

Alla seconda riduzione rimane soltanto:

$$
0
$$

Quindi consultiamo:

$$
\operatorname{GOTO}[0,S]=1
$$

ottenendo:

$$
0\,S\,1
$$

> [!warning] GOTO dipende dalla pila rimasta
> La stessa produzione, applicata dallo stesso stato corrente 11, può condurre a stati diversi.
> Si usa lo stato scoperto dopo aver eliminato il corpo della produzione.

---

## 9. Limiti del metodo e procedura d’esame

SLR(1) non elimina necessariamente tutti i conflitti LR(0).

FOLLOW raccoglie informazioni globali sui possibili contesti di un non terminale. Alcuni terminali presenti in FOLLOW possono essere incompatibili con il particolare contesto rappresentato da uno stato.

Se rimangono celle con più azioni, la grammatica non è SLR(1). Questo risultato, da solo, non dimostra ambiguità e non esclude che la grammatica sia LR(1).

Ogni grammatica LR(0) è anche SLR(1): restringere le colonne delle riduzioni non può creare conflitti nuovi. L’esempio di gennaio dimostra che il contrario non vale.

All’esame:

1. numera le produzioni e costruisci l’automa LR(0);
2. calcola FIRST e FOLLOW;
3. conserva spostamenti e GOTO;
4. inserisci ogni riduzione soltanto nelle colonne FOLLOW appropriate;
5. controlla tutte le celle ACTION;
6. formula la classificazione;
7. simula la stringa soltanto dopo aver verificato il determinismo della tabella.

Durante la simulazione ricorda:

- gli spostamenti consumano input;
- le riduzioni lasciano invariato l’input;
- una produzione epsilon elimina zero coppie;
- GOTO si consulta dopo la rimozione del corpo;
- l’accettazione segue lo spostamento di $\downarrow$ e richiede l’input esaurito.

## Collegamenti

- [[06 - Costruzione dell automa LR0]]
- [[07 - Tabelle ACTION e GOTO e simulazione del riconoscimento]]
- [[08 - Conflitti e classificazione LR0]]
- [[09 - Insiemi FIRST e FOLLOW]]
- [[11 - Analisi LR1 - Cenni]]