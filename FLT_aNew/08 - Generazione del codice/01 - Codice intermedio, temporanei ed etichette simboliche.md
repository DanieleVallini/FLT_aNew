# Codice intermedio, temporanei ed etichette simboliche

## 1. Il ruolo del codice intermedio

Un traduttore può rappresentare il programma mediante un **codice intermedio**, utilizzato fra l’analisi del sorgente e la produzione del codice di destinazione.

Nel corso lavoriamo soprattutto con sequenze di istruzioni elementari che rendono espliciti:

- i calcoli;
- i risultati intermedi;
- gli assegnamenti;
- i salti e le loro destinazioni.

Per esempio, il sorgente:

```text
a := a + b * c
```

può diventare:

```text
t1 := b * c
t2 := a + t1
a := t2
```

La struttura dell’espressione viene tradotta in una sequenza: prima il prodotto, poi la somma, infine l’assegnamento.

> [!important]
> Il traduttore genera istruzioni che calcoleranno il risultato durante l’esecuzione.
> Non deve conoscere già i valori numerici delle variabili del programma.

---

## 2. Codice a tre indirizzi

Una forma comune di codice intermedio è il **codice a tre indirizzi**, o TAC, da *Three-Address Code*.

La forma aritmetica fondamentale è:

$$
x:=y\ \mathrm{op}\ z
$$

dove:

- $y$ e $z$ sono gli operandi;
- $\mathrm{op}$ è un operatore;
- $x$ riceve il risultato.

I tre indirizzi sono quindi la destinazione e i due operandi.

Il termine “indirizzo” comprende qui riferimenti simbolici a variabili, costanti e temporanei.

### 2.1 Forme di istruzione utilizzate

| Forma | Significato |
|---|---|
| `x := y + z` | Operazione binaria |
| `x := -y` | Operazione unaria |
| `x := y` | Copia di un valore |
| `goto L` | Salto incondizionato |
| `if x < y goto L` | Salto condizionato |
| `L:` | Definizione di un punto etichettato |

Non tutte le istruzioni utilizzano tre indirizzi.

Un assegnamento come:

```text
x := a + b * c
```

contiene due operazioni aritmetiche e deve essere scomposto, per esempio:

```text
t1 := b * c
x := a + t1
```

> [!warning]
> “Tre indirizzi” non significa tre istruzioni, tre variabili sorgente o tre caratteri.
> Descrive la struttura delle operazioni elementari.

---

## 3. Temporanei e funzione newtemp

Un **temporaneo** è un nome introdotto dal traduttore per conservare un risultato intermedio.

Per esempio:

```text
t1 := b * c
t2 := a + t1
```

`t1` conserva il prodotto e `t2` conserva il risultato della somma.

Useremo la funzione:

$$
\operatorname{newtemp}()
$$

che restituisce un nome fresco:

$$
t_1,\ t_2,\ t_3,\ldots
$$

Per esempio, una regola del traduttore può eseguire:

```text
E.place := newtemp()
```

ottenendo:

$$
E.place=t_1
$$

Successivamente genera un’istruzione che assegnerà un valore a quel temporaneo:

```text
t1 := a + b
```

### 3.1 Nomi freschi

I temporanei devono essere distinti quando rappresentano risultati che devono essere conservati separatamente.

Riutilizzare un nome troppo presto può sovrascrivere un valore ancora necessario.

Nelle traduzioni iniziali conviene generare un temporaneo nuovo per ciascun risultato intermedio previsto dalle regole.

Questi nomi sono simbolici: non implicano che ogni temporaneo debba occupare un registro fisico diverso.

---

## 4. Etichette simboliche e funzione newlabel

Un’**etichetta simbolica** identifica una posizione nel codice alla quale può essere trasferito il controllo.

Per esempio:

```text
goto L1

L1:
    x := x + 1
```

Le due occorrenze di `L1` hanno ruoli diversi:

- `goto L1` utilizza l’etichetta come destinazione;
- `L1:` definisce la posizione della destinazione.

Useremo:

$$
\operatorname{newlabel}()
$$

per ottenere un nome fresco:

$$
L_1,\ L_2,\ L_3,\ldots
$$

### 4.1 Creazione del nome, definizione e utilizzo

Sono tre operazioni distinte:

| Operazione | Esempio |
|---|---|
| Crea un nome di etichetta | `L := newlabel()` |
| Genera un salto verso quel nome | `goto L` |
| Colloca l’etichetta nel codice | `L:` |

La chiamata a `newlabel()` non inserisce automaticamente una definizione nel codice e non esegue un salto.

> [!important] Riutilizzare la stessa destinazione
> Una volta creato un nome, i salti e la definizione devono riferirsi a quello stesso nome.
> Una nuova chiamata a `newlabel()` produce una destinazione diversa.

Nella sequenza completa, ogni destinazione usata deve essere definita correttamente. Nei singoli frammenti alcune etichette possono essere fornite e collocate dal contesto esterno.

---

## 5. Temporanei ed etichette hanno ruoli diversi

| Elemento | Esempio | Che cosa rappresenta |
|---|---|---|
| Variabile sorgente | `a` | Un dato del programma originale |
| Costante | `5` | Un valore utilizzabile come operando |
| Temporaneo | `t1` | Un risultato intermedio |
| Etichetta | `L1` | Una posizione del flusso di controllo |

Un temporaneo viene utilizzato nei calcoli:

```text
t1 := a + b
```

Un’etichetta viene utilizzata per organizzare il controllo:

```text
if a < b goto L1
```

### 5.1 Traduzione ed esecuzione

Bisogna distinguere due momenti:

| Durante la traduzione | Durante l’esecuzione del codice prodotto |
|---|---|
| Si creano i nomi dei temporanei | Si calcolano e memorizzano i loro valori |
| Si creano i nomi delle etichette | I salti trasferiscono il controllo |
| Si costruiscono le istruzioni | Le istruzioni modificano i dati del programma |

Per un ciclo, per esempio, i nomi delle etichette vengono scelti durante la traduzione. Durante l’esecuzione, il programma può tornare molte volte alla stessa etichetta.

---

## 6. Gli attributi code e place

Le DDS collegano i frammenti di codice ai costrutti grammaticali.

Per un’espressione aritmetica $E$ useremo soprattutto:

$$
E.code
$$

$$
E.place
$$

### 6.1 E.code

$E.code$ contiene la sequenza di istruzioni generata per l’espressione.

Può essere una stringa oppure una rappresentazione strutturata di istruzioni: nelle regole ragioneremo come su una sequenza.

### 6.2 E.place

$E.place$ indica l’operando che rappresenta il risultato dell’espressione.

Può essere:

- un identificatore;
- una costante;
- un temporaneo.

Per esempio, dopo aver tradotto:

$$
a+b*c
$$

possiamo avere:

$$
E.place=t_2
$$

e:

```text
E.code:
    t1 := b * c
    t2 := a + t1
```

$E.place$ contiene il nome `t2`, non il valore numerico che `t2` assumerà durante l’esecuzione.

Per un’istruzione $S$, invece, $S.code$ rappresenta il codice dell’intera istruzione.

---

## 7. gen e concatenazione dei frammenti

Nelle DDS useremo:

$$
\operatorname{gen}(\ldots)
$$

per costruire un frammento di codice.

Per esempio:

```text
gen(t1, ':=', 'b', '*', 'c')
```

rappresenta la costruzione dell’istruzione:

```text
t1 := b * c
```

La funzione costruisce il codice; non esegue il prodotto fra i valori di `b` e `c`.

### 7.1 Concatenazione mediante ||

L’operatore:

$$
\|
\|
$$

indicherà la concatenazione ordinata di frammenti.

Per esempio:

$$
C=C_1\mathbin{\| \|}C_2
$$

significa che le istruzioni di $C_1$ precedono quelle di $C_2$ nella sequenza risultante.

Una regola per un assegnamento è:

$$
S.code
:=
E.code
\mathbin{\| \|}
\operatorname{gen}(id.place,\text{":="},E.place)
$$

Prima viene generato il codice che ottiene il risultato dell’espressione, poi l’istruzione che lo copia nella variabile destinazione.

> [!warning]
> Nelle formule delle DDS, `||` concatena codice.
> Non rappresenta l’operatore booleano `or` del programma sorgente.

La sequenza vuota di istruzioni può essere indicata con $\varepsilon$ oppure con una stringa vuota.

Qui $\varepsilon$ riguarda il codice generato: un identificatore può avere codice vuoto perché il suo nome è già utilizzabile come operando.

---

## 8. Esempio completo: assegnamento aritmetico

Per:

```text
a := a + b * c
```

una traduzione semplice è:

```text
t1 := b * c
t2 := a + t1
a := t2
```

I passaggi sono:

1. crea `t1` per il prodotto;
2. genera `t1 := b * c`;
3. crea `t2` per la somma;
4. genera `t2 := a + t1`;
5. genera `a := t2`.

L’espressione a destra usa il valore di `a` precedente all’assegnamento finale.

Non bisogna modificarlo prima di aver ottenuto il risultato richiesto.

In questa traduzione:

| Informazione | Valore |
|---|---|
| Risultato del prodotto | `t1` |
| Risultato dell’intera espressione | `t2` |
| $E.code$ | Le prime due istruzioni |
| $E.place$ | `t2` |
| $S.code$ | Tutte e tre le istruzioni |

La traduzione sistematica delle espressioni sarà sviluppata nella prossima nota.

---

## 9. Esempio introduttivo con salti

Consideriamo:

```text
if x > y then
    x := x - w
else
    x := x + w
```

Una possibile traduzione è:

```text
if x > y goto L1
goto L2

L1:
    t1 := x - w
    x := t1
    goto L3

L2:
    t2 := x + w
    x := t2

L3:
```

Le etichette hanno questi significati:

| Etichetta | Ruolo |
|---|---|
| `L1` | Inizio del ramo vero |
| `L2` | Inizio del ramo falso |
| `L3` | Continuazione dopo il condizionale |

Se la condizione è vera, si salta a `L1`.

Se è falsa, il salto condizionato non viene preso e si esegue l’istruzione successiva:

```text
goto L2
```

Dopo il ramo vero:

```text
goto L3
```

evita che l’esecuzione prosegua anche nel ramo falso.

Dopo il ramo falso si raggiunge naturalmente `L3`.

> [!important] Proseguimento naturale
> In assenza di un salto, l’esecuzione continua con l’istruzione successiva.
> La presenza di un’etichetta non interrompe il flusso.

Negli schemi delle prossime note, attributi come $E.true$, $E.false$ e $S.next$ conterranno proprio destinazioni di controllo di questo tipo.

---

## 10. Controlli da applicare all’esame

Quando produci o leggi codice intermedio, verifica che:

1. ogni operazione utilizzi valori già disponibili;
2. la sequenza rispetti la struttura dell’espressione;
3. i temporanei conservino i risultati necessari;
4. i salti utilizzino le etichette corrette;
5. le destinazioni siano definite nel codice completo o fornite dal contesto;
6. il proseguimento naturale non faccia eseguire istruzioni indesiderate;
7. le concatenazioni mantengano l’ordine richiesto dalle dipendenze.

Gli errori più frequenti sono:

- confondere il nome di un temporaneo con il suo valore;
- confondere un temporaneo con un’etichetta;
- generare una nuova etichetta invece di riutilizzare quella già assegnata;
- dimenticare la definizione di una destinazione;
- trattare `gen` come esecuzione del codice prodotto;
- interpretare `||` come un’operazione booleana;
- aggiornare una variabile sorgente prima di aver completato il calcolo che ne usa il valore precedente.

## Collegamenti

- [[01 - Definizioni dirette dalla sintassi e regole semantiche]]
- [[02 - Traduzione di espressioni aritmetiche e assegnamenti]]
- [[03 - Espressioni booleane e valutazione a corto circuito]]
- [[04 - Attributi true, false, next e begin]]
- [[05 - Traduzione di if-then-else]]
- [[06 - Traduzione di while]]