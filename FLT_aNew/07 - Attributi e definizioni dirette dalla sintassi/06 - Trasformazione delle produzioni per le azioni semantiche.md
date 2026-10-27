# Trasformazione delle produzioni per le azioni semantiche

## 1. Obiettivo della trasformazione

Una regola semantica può richiedere un calcolo prima che sia stato riconosciuto l’intero corpo di una produzione.

Per esempio:

$$
A\to BC
$$

può avere un’azione da eseguire dopo il riconoscimento di $B$ e prima dell’elaborazione di $C$.

La trasformazione introduce punti nei quali il parser possa eseguire queste azioni mediante normali riduzioni.

Per essere corretta, deve preservare:

- il linguaggio delle frasi riconosciute;
- i risultati e le dipendenze della traduzione;
- la disponibilità dei valori letti dalle azioni.

Inoltre, la grammatica trasformata deve essere analizzabile con il metodo richiesto.

> [!important]
> Preservare il linguaggio non basta.
> Bisogna verificare anche gli attributi, l’ordine delle azioni e l’assenza di nuovi conflitti nel parser.

---

## 2. Sostituire un’azione intermedia con un marcatore

Consideriamo uno schema di traduzione:

$$
A\to\alpha\ \{\mathcal A\}\ \beta
$$

L’azione $\mathcal A$ deve essere eseguita dopo aver riconosciuto $\alpha$ e prima di elaborare $\beta$.

Introduciamo un non terminale fresco $M$:

$$
A\to\alpha M\beta
$$

$$
M\to\varepsilon\ \{\mathcal A\}
$$

L’azione viene associata alla riduzione:

$$
M\to\varepsilon
$$

Il parser dispone così di una produzione alla quale collegare l’esecuzione dell’azione.

Per più azioni intermedie, si possono introdurre marcatori distinti nei rispettivi punti.

> [!warning] Marcatore e simboli di input
> $M$ è un non terminale e genera la stringa vuota.
> Non viene aggiunto un token da leggere nell’input.

Il marcatore non va confuso con $\downarrow$, che nelle nostre grammatiche aumentate è il marcatore finale effettivamente spostato.

---

## 3. Che cosa accade sulla pila

Supponiamo che il prefisso $\alpha$ sia stato riconosciuto.

Alla riduzione di:

$$
M\to\varepsilon
$$

il parser:

1. esegue l’azione usando i dati disponibili;
2. non elimina record relativi al corpo, perché il corpo è vuoto;
3. inserisce il record semantico di $M$;
4. inserisce lo stato determinato da GOTO.

Il record del marcatore può conservare un risultato destinato a operazioni successive.

La trasformazione deve specificare anche come quei risultati verranno recuperati.

> [!important]
> Un marcatore risolve un problema di collocazione temporale e di conservazione dei dati.
> Non rende disponibili argomenti che non sono stati ottenuti.

La riduzione del marcatore deve inoltre essere prescritta senza conflitti dalla tabella del parser trasformato.

---

## 4. Esempio completo con un ereditato

Consideriamo:

$$
\begin{aligned}
S&\to BC\\
B&\to b\\
C&\to c
\end{aligned}
$$

con le regole:

| Produzione | Regole semantiche |
|---|---|
| $S\to BC$ | $C.i:=3\cdot B.s$; $S.s:=B.s+C.s$ |
| $B\to b$ | $B.s:=2$ |
| $C\to c$ | $C.s:=C.i+1$ |

$C.i$ è ereditato: viene assegnato al figlio $C$ nella produzione di $S$.

Per la parola `bc`:

$$
B.s=2
$$

$$
C.i=3\cdot2=6
$$

$$
C.s=6+1=7
$$

$$
S.s=2+7=9
$$

Vogliamo conservare questi risultati mediante una possibile implementazione bottom-up con marcatore.

### 4.1 Grammatica trasformata

Introduciamo $M$:

$$
\begin{aligned}
S&\to BMC\\
B&\to b\\
M&\to\varepsilon\\
C&\to c
\end{aligned}
$$

Alla riduzione di $M$ calcoliamo il valore destinato a svolgere il ruolo dell’ereditato $C.i$ e lo conserviamo nel campo $M.v$.

### 4.2 Convenzione per gli accessi

Usiamo una pila logica $V$ con un record per simbolo.

$top$ indica la posizione della cima **prima delle rimozioni e dell’inserimento della testa della riduzione corrente**.

Gli accessi seguenti sono operazioni dell’implementazione del traduttore.

In particolare, leggere il prefisso della pila durante $M\to\varepsilon$ è un accesso al contesto: non significa che $B$ appartenga al corpo vuoto di quella produzione.

### 4.3 Azioni implementate

```text
B -> b:
    B.s := 2

M -> ε:
    M.v := 3 * V[top].s

C -> c:
    C.s := V[top - 1].v + 1

S -> B M C:
    S.s := V[top - 2].s + V[top].s
```

Vediamo perché gli indici sono corretti.

---

## 5. Controllo degli indici e dei valori

### 5.1 Riduzione del marcatore

Prima di ridurre $M\to\varepsilon$, la cima contiene $B$:

$$
\cdots\ B[s=2]
$$

Quindi:

$$
V[top].s=B.s
$$

L’azione calcola:

$$
M.v=6
$$

e il parser inserisce il record di $M$:

$$
\cdots\ B[s=2]\ M[v=6]
$$

### 5.2 Riduzione di C

Dopo lo spostamento di `c`, la pila contiene:

$$
\cdots\ B[s=2]\ M[v=6]\ c
$$

Alla riduzione $C\to c$:

- $c$ è in posizione $top$;
- $M$ è in posizione $top-1$.

Quindi:

$$
C.s=V[top-1].v+1=7
$$

Si elimina soltanto il record di `c` e si inserisce quello di $C$:

$$
\cdots\ B[s=2]\ M[v=6]\ C[s=7]
$$

Il marcatore rimane sulla pila.

### 5.3 Riduzione finale

Per:

$$
S\to BMC
$$

le posizioni sono:

| Posizione | Simbolo |
|---|---|
| $top-2$ | $B$ |
| $top-1$ | $M$ |
| $top$ | $C$ |

Calcoliamo:

$$
S.s=2+7=9
$$

Poi eliminiamo i tre record del corpo e inseriamo $S$.

> [!warning] Anche i marcatori contano
> Il corpo trasformato contiene tre simboli, quindi la riduzione elimina tre record logici.
> Il fatto che $M$ generi $\varepsilon$ non elimina il suo record dalla pila.

In questo esempio, $C$ compare soltanto nel contesto mostrato e ha la sola produzione $C\to c$.

Con altre produzioni o altri contesti, gli accessi devono essere verificati nuovamente: non si possono riutilizzare automaticamente gli stessi indici.

---

## 6. Preservazione del linguaggio e della traduzione

Sostituire un’azione intermedia con un non terminale fresco avente la sola produzione:

$$
M\to\varepsilon
$$

non aggiunge terminali alle parole generate.

Ignorando le azioni, ogni derivazione trasformata può essere ricondotta a quella originale eliminando i marcatori.

Viceversa, nelle derivazioni originali si possono inserire i nuovi marcatori nelle posizioni previste e poi annullarli.

Quindi, per questa trasformazione:

$$
L(G_{\mathrm{trasformata}})
=
L(G_{\mathrm{originale}})
$$

Per preservare la traduzione bisogna anche verificare che:

- le azioni vengano eseguite nei punti previsti;
- leggano i valori delle occorrenze corrette;
- i dati restino disponibili fino all’ultimo utilizzo;
- il risultato finale coincida con quello della DDS originale.

Nell’esempio, entrambe le versioni producono:

$$
S.s=9
$$

La grammatica trasformata dell’esempio è inoltre priva di conflitti LR(0).

---

## 7. I marcatori possono introdurre conflitti

Consideriamo la grammatica:

$$
S\to ab\mid ac
$$

che è LR(0).

Supponiamo di voler eseguire due azioni diverse all’inizio delle rispettive alternative:

$$
S\to\{\mathcal A_1\}\,ab
\mid
\{\mathcal A_2\}\,ac
$$

La sostituzione meccanica produce:

$$
\begin{aligned}
S&\to M_1ab\mid M_2ac\\
M_1&\to\varepsilon\\
M_2&\to\varepsilon
\end{aligned}
$$

con le azioni associate alle due produzioni vuote.

Lo stato iniziale contiene entrambi i candidati completi:

$$
[M_1\to\cdot]
$$

$$
[M_2\to\cdot]
$$

Inoltre:

$$
\operatorname{FOLLOW}(M_1)
=
\operatorname{FOLLOW}(M_2)
=
\{a\}
$$

Nella cella ACTION relativa ad `a`, SLR(1) inserisce quindi entrambe le riduzioni.

Si ottiene un conflitto reduce/reduce.

Il linguaggio resta:

$$
\{ab,ac\}
$$

ma la grammatica trasformata non è SLR(1).

> [!important]
> Inserire marcatori può richiedere al parser una scelta anticipata che prima non era necessaria.
> Dopo la trasformazione bisogna ricostruire o verificare automa e tabella del metodo richiesto.

La sola equivalenza dei linguaggi non preserva la classificazione LR.

---

## 8. Applicazione al frammento degli esami

Riprendiamo:

$$
A\to BCA_1
$$

con:

$$
A.a:=f(C.s,A_1.a)
$$

$$
A_1.b:=g(B.s)
$$

$$
C.c:=h(B.s,A.b)
$$

Le esigenze temporali sono:

| Regola | Momento richiesto |
|---|---|
| $C.c:=h(B.s,A.b)$ | Prima dell’elaborazione di $C$ |
| $A_1.b:=g(B.s)$ | Prima dell’elaborazione di $A_1$ |
| $A.a:=f(C.s,A_1.a)$ | Dopo aver ottenuto i risultati di $C$ e $A_1$ |

Per la regola di $A_1.b$, il valore sorgente $B.s$ proviene da un simbolo già riconosciuto a sinistra. Occorre conservarlo e predisporre il passaggio al destinatario.

Per $C.c$, invece, serve anche:

$$
A.b
$$

che è un ereditato del padre.

Come discusso nella nota precedente, il suo valore non è automaticamente presente nel prefisso riconosciuto.

> [!warning] Non completare la trasformazione inventando la memoria
> Scrivere un marcatore con un’azione che cita $A.b$ non dimostra che quel valore sia accessibile.
> Bisogna indicare dove è conservato e come viene recuperato per l’occorrenza corretta.

Nel metodo locale basato soltanto sui valori dei simboli già riconosciuti a sinistra, questa dipendenza impedisce una trasformazione autonoma dell’intero frammento.

Un’implementazione più generale potrebbe predisporre un record di contesto per $A.b$, ma dovrebbe specificarne inizializzazione, accesso e conservazione nelle elaborazioni annidate.

La visita dell’albero resta possibile con $A.b$ disponibile all’ingresso, come mostrato nella nota dedicata.

---

## 9. Procedura da applicare all’esame

1. Individua quando deve essere eseguita ciascuna azione.
2. Controlla quali argomenti sono disponibili in quel punto.
3. Lascia alla riduzione finale le azioni che possono attendere il completamento del corpo.
4. Per un’azione intermedia introduci un marcatore fresco nella posizione appropriata.
5. Associa l’azione alla produzione vuota del marcatore.
6. Specifica dove vengono conservati i risultati anticipati.
7. Aggiorna le azioni che leggono quei risultati.
8. Ricalcola gli indici contando anche i marcatori.
9. Verifica linguaggio, risultati semantici e classificazione del parser.

Una trasformazione completa deve rispondere a tre domande:

- **Quando** viene eseguita l’azione?
- **Dove** si trovano tutti i valori necessari?
- **Come** vengono recuperati i risultati dalle azioni successive?

Se manca una di queste informazioni, la sola riscrittura delle produzioni non basta.

## Collegamenti

- [[03 - Grafi delle dipendenze e ordine di valutazione]]
- [[04 - Attraversamento dell albero e valutazione degli attributi]]
- [[05 - Esecuzione delle azioni durante l analisi bottom-up]]
- [[01 - Codice intermedio, temporanei ed etichette simboliche]]