# Traduzione di if-then-else

## 1. Comportamento da preservare

L’istruzione:

```text
if E then S1 else S2
```

valuta la condizione `E` e seleziona un ramo:

- se `E` è vera, esegue `S1`;
- se `E` è falsa, esegue `S2`.

Dopo il ramo selezionato, l’esecuzione prosegue con la continuazione dell’intero `if`.

> [!important] I rami sono alternativi
> Il codice generato deve impedire che, dopo aver eseguito il ramo `then`, si prosegua anche nel ramo `else`.

Nel compito del **26 gennaio 2026** viene chiesto di motivare proprio il salto che garantisce questa separazione e di tradurre:

```text
if x>y or x>z then x:=x-w else x:=x+w
```

## 2. Definizione diretta dalla sintassi

La produzione è:

$$
S \to \mathrm{if}\ E\ \mathrm{then}\ S_1\ \mathrm{else}\ S_2
$$

Le regole della DDS assegnata sono:

```text
E.true  := newlabel()
E.false := newlabel()

S1.next := S.next
S2.next := S.next

S.code :=
    E.code
    || gen(E.true, ':')
    || S1.code
    || gen('goto', S.next)
    || gen(E.false, ':')
    || S2.code
```

Ricordiamo i significati:

| Attributo | Ruolo |
|---|---|
| `E.true` | Ingresso del ramo `then` |
| `E.false` | Ingresso del ramo `else` |
| `S.next` | Continuazione dopo l’intero `if` |
| `S1.next` | Continuazione del ramo `then` |
| `S2.next` | Continuazione del ramo `else` |

`S.next` è fornito dal contesto. Entrambi i rami ricevono questa stessa destinazione.

`newlabel()` crea nomi freschi durante la traduzione; `gen(...)` costruisce codice e `||` concatena frammenti.

## 3. Struttura del codice risultante

Se chiamiamo `L_then`, `L_else` e `L_fine` le tre destinazioni principali, la disposizione è:

```text
codice della condizione:
    raggiunge L_then oppure L_else

L_then:
    codice di S1
    goto L_fine

L_else:
    codice di S2

L_fine:
    continuazione
```

Questo è uno schema esplicativo: le righe descrittive vanno sostituite con il codice effettivo.

La definizione di `L_fine` è mostrata per rendere visibile la continuazione. Nella DDS originale, la sua emissione è compito del contesto che contiene `S`.

Le etichette individuano posizioni: non fermano l’esecuzione. Se non incontra un salto, il programma prosegue con l’istruzione successiva.

## 4. Perché serve il salto dopo then

Nella disposizione scelta, il codice del ramo `else` si trova immediatamente dopo quello del ramo `then`.

Se il ramo `then` termina per prosecuzione sequenziale, senza un salto si entrerebbe anche nell’`else`.

Consideriamo questo frammento errato:

```text
L1:
    t1 := x - w
    x := t1
L2:
    t2 := x + w
    x := t2
```

Supponiamo che la condizione sia vera, `x=10` e `w=2`.

Il programma entra in `L1`:

1. calcola `t1=8`;
2. assegna `x=8`;
3. prosegue oltre l’etichetta `L2`;
4. calcola `t2=10`;
5. assegna `x=10`.

Il risultato atteso era invece `x=8`.

Inserendo:

```text
goto L4
```

dopo il ramo `then`, si evita il codice del ramo `else` e si raggiunge la continuazione.

> [!tip] Motivazione pronta per l’esame
> Il salto a `S.next` impedisce che l’esecuzione del ramo `then` prosegua sequenzialmente nel codice del ramo `else`, collocato subito dopo, e trasferisce il controllo alla continuazione dell’intera istruzione.

Questa necessità riguarda lo schema assegnato. Se un ramo termina già con un trasferimento di controllo, una successiva ottimizzazione può rendere il salto aggiunto ridondante.

## 5. Svolgimento completo dell’esercizio di gennaio

L’istruzione è:

```text
if x>y or x>z then x:=x-w else x:=x+w
```

### 5.1 Individuare le componenti

```text
E  = x>y or x>z
E1 = x>y
E2 = x>z

S1 = x:=x-w
S2 = x:=x+w
```

La condizione usa un `or` a corto circuito: se `x>y` è vero, non occorre eseguire il secondo confronto.

### 5.2 Assegnare le etichette

Scegliamo:

| Etichetta | Significato |
|---|---|
| `L1` | Ingresso del ramo `then` |
| `L2` | Ingresso del ramo `else` |
| `L3` | Ingresso del secondo confronto |
| `L4` | Continuazione dopo l’`if` |

Poniamo `S.next=L4`. Dalla DDS dell’`if` segue:

```text
E.true  = L1
E.false = L2

S1.next = L4
S2.next = L4
```

Per la produzione:

$$
E \to E_1\ \mathrm{or}\ E_2
$$

si ottiene la tabella richiesta dall’esame:

| Occorrenza | `true` | `false` |
|---|---|---|
| `E`: `x>y or x>z` | `L1` | `L2` |
| `E1`: `x>y` | `L1` | `L3` |
| `E2`: `x>z` | `L1` | `L2` |

`L3` è una destinazione interna alla condizione: viene raggiunta quando il primo confronto è falso.

### 5.3 Tradurre la condizione

Applicando le regole dei confronti e dell’`or`:

```text
if x > y goto L1
goto L3
L3:
if x > z goto L1
goto L2
```

I percorsi sono:

- primo confronto vero: ingresso immediato nel `then`;
- primo falso e secondo vero: ingresso nel `then`;
- entrambi falsi: ingresso nell’`else`.

### 5.4 Tradurre i due assegnamenti

Usando un temporaneo fresco per ogni operazione aritmetica:

```text
S1.code:
    t1 := x - w
    x := t1

S2.code:
    t2 := x + w
    x := t2
```

Le scritte `S1.code:` e `S2.code:` sono intestazioni esplicative.

Entrambi i frammenti vengono costruiti dal traduttore. Sarà il controllo del programma generato a selezionare quale eseguire.

### 5.5 Comporre il codice finale

Sostituendo i frammenti nella regola di `S.code`:

```text
if x > y goto L1
goto L3

L3:
    if x > z goto L1
    goto L2

L1:
    t1 := x - w
    x := t1
    goto L4

L2:
    t2 := x + w
    x := t2

L4:
```

La definizione finale `L4:` è mostrata come collegamento al contesto successivo: non è aggiunta dalla formula di `S.code` riportata nel compito.

Il ramo `else`, formato qui da un semplice assegnamento, raggiunge `L4` per prosecuzione sequenziale. Non serve aggiungere un altro salto prima di questa etichetta.

![[Allegati/FLT_08_05/flt_08_05_if_then_else.svg]]

## 6. Verificare il risultato sui percorsi

| Valori iniziali | `x>y` | `x>z` | Ramo eseguito | `x` finale |
|---|---|---|---|---|
| `x=10, y=3, z=12, w=2` | Vero | Non valutato | `then` | `8` |
| `x=5, y=8, z=2, w=2` | Falso | Vero | `then` | `3` |
| `x=2, y=8, z=5, w=2` | Falso | Falso | `else` | `4` |
| `x=5, y=5, z=5, w=2` | Falso | Falso | `else` | `7` |

Il primo caso controlla il corto circuito. Il secondo verifica il collegamento attraverso `L3`. Gli ultimi due controllano l’uscita falsa, compreso il caso di uguaglianza.

La condizione viene valutata prima dell’assegnamento selezionato. Dopo aver modificato `x` nel ramo, non si rivaluta automaticamente la condizione dell’`if`.

## 7. Variante: if senza else

Per:

$$
S \to \mathrm{if}\ E\ \mathrm{then}\ S_1
$$

se la condizione è falsa bisogna raggiungere direttamente la continuazione.

Uno schema coerente è:

```text
E.true  := newlabel()
E.false := S.next
S1.next := S.next

S.code :=
    E.code
    || gen(E.true, ':')
    || S1.code
```

Il contesto assicura il collegamento alla continuazione, eventualmente mediante un salto già previsto dal costrutto contenitore.

Qui non c’è un ramo `else` da scavalcare: se `S1` termina sequenzialmente, prosegue verso la continuazione senza il salto aggiuntivo dello schema `if-then-else`.

## 8. Istruzioni annidate

I rami possono contenere altre selezioni o cicli.

Per esempio:

```text
if P then
    (if Q then A else B)
else
    C
```

L’`if` interno costituisce il ramo `then` di quello esterno. Riceve quindi come `next` la continuazione dell’`if` esterno.

Le etichette d’ingresso dei suoi rami devono essere fresche; la continuazione può invece essere condivisa intenzionalmente.

Bisogna distinguere:

- le etichette che identificano punti diversi del codice;
- gli attributi che indicano lo stesso punto di continuazione.

Condividere una continuazione è corretto. Usare accidentalmente la stessa etichetta per ingressi diversi può collegare i rami in modo errato.

## 9. Procedura da seguire all’esame

1. **Scrivi la produzione** e riconosci condizione e rami.
2. **Fissa `S.next`**, spiegando che rappresenta la continuazione.
3. **Assegna le etichette** d’ingresso di `then` ed `else`.
4. **Propaga `true` e `false`** alle occorrenze della condizione.
5. **Traduci separatamente** condizione e assegnamenti.
6. **Concatena secondo la DDS**, inserendo il salto dopo `then`.
7. **Presenta la tabella degli attributi** e controlla i percorsi.

Se vengono richieste anche spiegazioni, il solo elenco di istruzioni non basta: motiva il salto e indica il significato delle etichette.

## 10. Errori da evitare

- **Omettere il salto dopo `then`:** può provocare l’esecuzione anche dell’`else`.
- **Saltare all’ingresso dell’`else` dopo `then`:** la destinazione corretta è `S.next`.
- **Usare `E.false` per il falso del primo operando di un OR:** bisogna prima valutare il secondo operando.
- **Inserire gli assegnamenti prima della condizione:** si modificherebbero i valori usati dai confronti.
- **Confondere generazione ed esecuzione:** costruire entrambi i frammenti non significa eseguire entrambi i rami.
- **Ottimizzare prima di applicare la DDS:** conviene mostrare prima il codice richiesto dallo schema.

## Collegamenti

- [[02 - Traduzione di espressioni aritmetiche e assegnamenti]]
- [[03 - Espressioni booleane e valutazione a corto circuito]]
- [[04 - Attributi true, false, next e begin]]
- [[06 - Traduzione di while]]
- [[06 - Esame 26 gennaio 2026]]
