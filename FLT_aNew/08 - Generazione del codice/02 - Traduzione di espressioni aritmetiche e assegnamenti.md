# Traduzione di espressioni aritmetiche e assegnamenti

## 1. Obiettivo della traduzione

Per ogni espressione aritmetica vogliamo costruire:

- il codice che ne calcola il valore;
- l’operando mediante il quale utilizzare il risultato.

Useremo gli attributi sintetizzati:

$$
E.code
\qquad
E.place
$$

La proprietà da mantenere è:

> [!important] Proprietà fondamentale
> Dopo l’esecuzione di $E.code$, il risultato dell’espressione è disponibile tramite l’operando indicato da $E.place$.

$E.place$ può essere un identificatore, una costante o un temporaneo.

Consideriamo espressioni scalari ben tipate, senza conversioni implicite da generare negli esempi.

---

## 2. Grammatica, precedenza e associatività

Una grammatica adatta alle operazioni considerate è:

$$
\begin{aligned}
S&\to id:=E\\
E&\to E+T\mid E-T\mid T\\
T&\to T*F\mid T/F\mid F\\
F&\to -F\mid(E)\mid id\mid num
\end{aligned}
$$

La struttura della grammatica stabilisce:

- precedenza di moltiplicazione e divisione rispetto a somma e sottrazione;
- associatività a sinistra degli operatori binari dello stesso livello;
- precedenza del meno unario;
- possibilità di modificare il raggruppamento mediante parentesi.

Per esempio:

$$
a+b*c
$$

viene interpretata come:

$$
a+(b*c)
$$

mentre:

$$
a-b-c
$$

viene interpretata come:

$$
(a-b)-c
$$

La traduzione deve seguire questa struttura.

> [!warning]
> Non basta generare operazioni nell’ordine in cui compaiono i simboli nel testo.
> Bisogna rispettare l’albero sintattico dell’espressione.

---

## 3. Identificatori, costanti e passaggi intermedi

### 3.1 Identificatori

Per:

$$
F\to id
$$

usiamo:

$$
F.place:=id.place
$$

$$
F.code:=\varepsilon
$$

Il nome dell’identificatore è già utilizzabile come operando.

Non serve un’istruzione soltanto per indicare che il risultato del fattore è il valore della variabile.

### 3.2 Costanti numeriche

Per:

$$
F\to num
$$

usiamo:

$$
F.place:=num.lexval
$$

$$
F.code:=\varepsilon
$$

Qui il valore lessicale viene rappresentato come operando costante nel codice intermedio.

Per esempio, se il token rappresenta `7`, il suo risultato può essere utilizzato direttamente come costante `7`.

### 3.3 Produzioni di passaggio

Per:

$$
E\to T
$$

copiamo entrambi gli attributi:

$$
E.place:=T.place
$$

$$
E.code:=T.code
$$

Analogamente, per $T\to F$:

$$
T.place:=F.place
$$

$$
T.code:=F.code
$$

Queste produzioni non aggiungono istruzioni.

> [!important]
> Copiare un attributo del traduttore non significa generare un assegnamento nel programma.
> $E.place:=T.place$ trasferisce un nome fra attributi.

---

## 4. Operazioni binarie

Per una produzione della forma:

$$
X\to Y\ \mathrm{op}\ Z
$$

lo schema generale è:

```text
X.place := newtemp()

X.code := Y.code
          || Z.code
          || gen(X.place, ':=', Y.place, op, Z.place)
```

I passaggi sono:

1. tradurre l’operando sinistro;
2. tradurre l’operando destro;
3. creare un temporaneo per il risultato;
4. aggiungere l’istruzione che applica l’operatore.

Nei nostri esempi assegniamo i nomi dei temporanei procedendo dal basso verso l’alto.

Una numerazione diversa è equivalente se ogni riferimento resta coerente.

### 4.1 Applicazione alla grammatica

| Produzione | Operatore generato |
|---|---|
| $E\to E_1+T$ | `+` |
| $E\to E_1-T$ | `-` |
| $T\to T_1*F$ | `*` |
| $T\to T_1/F$ | `/` |

Per esempio, per:

$$
E\to E_1+T
$$

otteniamo:

$$
E.place:=\operatorname{newtemp}()
$$

$$
E.code:=
E_1.code
\mathbin{\| \|}
T.code
\mathbin{\| \|}
\operatorname{gen}
(E.place,\text{":="},E_1.place,\text{"+"},T.place)
$$

> [!warning] Conservare il codice degli operandi
> Generare soltanto l’istruzione finale non basta quando un operando richiede altri calcoli.
> Il codice dei due figli deve comparire prima dell’istruzione che ne usa i risultati.

---

## 5. Parentesi e meno unario

### 5.1 Parentesi

Per:

$$
F\to(E)
$$

usiamo:

$$
F.place:=E.place
$$

$$
F.code:=E.code
$$

Le parentesi stabiliscono il raggruppamento, ma non richiedono un’istruzione aritmetica aggiuntiva.

Per esempio:

```text
x := (a + b) * (c - d)
```

diventa:

```text
t1 := a + b
t2 := c - d
t3 := t1 * t2
x := t3
```

I tre temporanei corrispondono alle tre operazioni, non alle parentesi.

### 5.2 Meno unario

Per:

$$
F\to-F_1
$$

usiamo:

```text
F.place := newtemp()

F.code := F1.code
          || gen(F.place, ':=', '-', F1.place)
```

Per esempio:

```text
x := -(a + b)
```

diventa:

```text
t1 := a + b
t2 := -t1
x := t2
```

Prima viene calcolata la somma, poi la sua negazione.

---

## 6. Traduzione dell’assegnamento

Per:

$$
S\to id:=E
$$

la regola è:

$$
S.code:=
E.code
\mathbin{\| \|}
\operatorname{gen}(id.place,\text{":="},E.place)
$$

Il procedimento è:

1. genera il codice dell’espressione a destra;
2. ottieni il suo operando risultato;
3. aggiungi la copia nella variabile destinazione.

In questo schema l’assegnamento non richiede un nuovo temporaneo proprio.

Se $E.place=t_2$, l’ultima istruzione sarà:

```text
id := t2
```

L’identificatore concreto viene ricavato da $id.place$.

### 6.1 Preservare i valori precedenti

Per:

```text
a := a + 1
```

una traduzione sistematica è:

```text
t1 := a + 1
a := t1
```

Il valore precedente di `a` viene letto prima dell’aggiornamento.

Questa attenzione è necessaria anche quando la destinazione compare nell’espressione a destra.

---

## 7. Esempio completo dall’esame di giugno 2025

Nel corpo del `while` compare:

```text
a := a + b * c
```

### 7.1 Traduzione del prodotto

Per la sottoespressione:

$$
b*c
$$

gli identificatori hanno codice vuoto e sono utilizzabili direttamente come operandi.

Creiamo:

$$
T.place=t_1
$$

e generiamo:

```text
t1 := b * c
```

### 7.2 Traduzione della somma

Per:

$$
a+(b*c)
$$

il risultato dell’operando destro si trova nel temporaneo `t1`.

Creiamo:

$$
E.place=t_2
$$

e aggiungiamo:

```text
t2 := a + t1
```

Il codice dell’espressione completa è quindi:

```text
t1 := b * c
t2 := a + t1
```

### 7.3 Assegnamento finale

Aggiungiamo la copia nella destinazione:

```text
a := t2
```

Il risultato completo è:

```text
t1 := b * c
t2 := a + t1
a := t2
```

### 7.4 Attributi e istruzioni aggiunte

| Costrutto | Operando risultato | Istruzione aggiunta al codice dei figli |
|---|---|---|
| Identificatore `a` | `a` | Nessuna |
| Identificatore `b` | `b` | Nessuna |
| Identificatore `c` | `c` | Nessuna |
| `b * c` | `t1` | `t1 := b * c` |
| `a + b * c` | `t2` | `t2 := a + t1` |
| Assegnamento | — | `a := t2` |

Come controllo manuale, se durante l’esecuzione:

$$
a=2,\quad b=3,\quad c=4
$$

si ottiene prima `t1 = 12`, poi `t2 = 14`, infine `a = 14`.

Nel ciclo questo blocco verrà eseguito a ogni iterazione, utilizzando il valore corrente di `a`.

---

## 8. Gli assegnamenti nei rami dell’esame di gennaio 2026

Nel ramo vero compare:

```text
x := x - w
```

Con le regole descritte:

```text
t1 := x - w
x := t1
```

Nel ramo falso compare:

```text
x := x + w
```

Continuando a generare temporanei freschi nella stessa traduzione:

```text
t2 := x + w
x := t2
```

Questi sono i codici dei singoli rami.

Il codice della condizione e i salti del costrutto `if` stabiliranno quale ramo viene effettivamente eseguito.

Generare entrambi i frammenti durante la traduzione non significa eseguirli entrambi durante l’esecuzione del programma.

---

## 9. Aderenza alla DDS e semplificazioni

Per l’assegnamento:

```text
a := a + b * c
```

anche:

```text
t1 := b * c
a := a + t1
```

è un codice a tre indirizzi semanticamente equivalente nel caso considerato.

Tuttavia, la DDS presentata crea un temporaneo per la somma e poi genera una copia finale.

> [!important] All’esame segui le regole richieste
> Se la consegna chiede di applicare una determinata DDS, mostra le istruzioni prodotte da quelle regole.
> Una semplificazione equivalente può non corrispondere alla loro applicazione meccanica.

Analogamente, due occorrenze della stessa sottoespressione vengono tradotte separatamente se le regole non prevedono il riutilizzo dei risultati.

Per esempio, in:

$$
(a+b)*(a+b)
$$

questa DDS genera normalmente due calcoli della somma.

L’eliminazione delle sottoespressioni comuni richiede un’ulteriore scelta di ottimizzazione.

---

## 10. Collegamento con la valutazione degli attributi

Nelle regole presentate, `code` e `place` sono risultati sintetizzati.

Il padre combina:

- il codice dei figli;
- gli operandi nei quali si trovano i loro risultati;
- la nuova istruzione relativa alla propria operazione.

Questa DDS è quindi S-attribuita e si presta all’esecuzione delle azioni durante le riduzioni di un parser bottom-up appropriato.

L’ordine di costruzione del codice deve rispettare le dipendenze:

$$
\text{codice degli operandi}
\quad\text{prima di}\quad
\text{istruzione che ne usa i risultati}
$$

Con le regole descritte:

- ogni operazione binaria crea un temporaneo;
- il meno unario crea un temporaneo;
- parentesi e produzioni di passaggio non ne creano;
- identificatori e costanti non ne creano;
- l’assegnamento finale aggiunge una copia.

---

## 11. Procedura da applicare all’esame

1. Individua la struttura dell’espressione rispettando parentesi, precedenza e associatività.
2. Associa agli identificatori e alle costanti i rispettivi operandi.
3. Traduci le sottoespressioni dal basso verso l’alto.
4. Per ogni operazione, crea il temporaneo richiesto.
5. Concatena il codice dei figli prima della nuova istruzione.
6. Propaga `code` e `place` nelle produzioni di passaggio.
7. Aggiungi l’assegnamento alla destinazione.
8. Controlla che ogni temporaneo sia definito prima dell’utilizzo.

Errori da evitare:

- generare la somma prima del prodotto in `a + b * c`;
- trasformare `a - b - c` in `a - (b - c)`;
- creare istruzioni per le sole parentesi;
- dimenticare il codice di una sottoespressione;
- sovrascrivere troppo presto una variabile sorgente;
- confondere $E.place$ con il valore numerico dell’espressione;
- applicare ottimizzazioni non previste dalla DDS richiesta.

## Collegamenti

- [[01 - Codice intermedio, temporanei ed etichette simboliche]]
- [[03 - Espressioni booleane e valutazione a corto circuito]]
- [[04 - Attributi true, false, next e begin]]
- [[05 - Traduzione di if-then-else]]
- [[06 - Traduzione di while]]