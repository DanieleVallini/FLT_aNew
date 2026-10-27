# Espressioni booleane e valutazione a corto circuito

## 1. Perché questa nota serve all’esame

Nei compiti forniti compaiono queste condizioni:

- **26 gennaio 2026:** `x>y or x>z`, all’interno di un `if-then-else`;
- **24 giugno 2025:** `a<b or a<c`, come condizione di un `while`.

Per tradurle bisogna stabilire **quale confronto eseguire e dove proseguire in base al risultato**.

Le regole viste qui producono il codice della condizione. Le note successive lo inseriranno nel codice completo di selezioni e cicli.

## 2. Significato del corto circuito

La valutazione a corto circuito procede da sinistra verso destra e interrompe la valutazione appena il risultato complessivo è determinato.

| Espressione | Risultato di `E1` | Valutare `E2`? | Risultato complessivo |
|---|---|---|---|
| `E1 or E2` | vero | No | Vero |
| `E1 or E2` | falso | Sì | Il risultato di `E2` |
| `E1 and E2` | vero | Sì | Il risultato di `E2` |
| `E1 and E2` | falso | No | Falso |

Quindi:

- con **`or`**, un primo operando vero basta per concludere;
- con **`and`**, un primo operando falso basta per concludere.

> [!warning] Fa parte della semantica
> Quando il linguaggio prescrive il corto circuito, valutare sempre entrambi gli operandi può cambiare il comportamento del programma: il secondo può contenere chiamate con effetti oppure operazioni non definite per alcuni valori.

Per esempio, in:

```text
x != 0 and 10/x > 1
```

se `x=0`, il risultato è falso e la divisione non deve essere eseguita.

## 3. Rappresentare il risultato mediante salti

Per le espressioni aritmetiche abbiamo usato `E.place` per indicare dove trovare il risultato.

Per una condizione possiamo invece generare codice che trasferisce direttamente il controllo a una delle due destinazioni:

| Attributo | Significato |
|---|---|
| `E.true` | Etichetta da raggiungere se `E` è vera |
| `E.false` | Etichetta da raggiungere se `E` è falsa |
| `E.code` | Codice che valuta `E` e raggiunge la destinazione appropriata |

`E.true` e `E.false` sono attributi **ereditati**: il contesto stabilisce dove andare. `E.code` è **sintetizzato** a partire dalla struttura dell’espressione.

> [!important] Etichette, non valori booleani
> `E.true=L1` significa «se questa espressione è vera, salta a `L1`». Non significa assegnare a `E` il valore vero.

L’invariante della traduzione è: eseguendo `E.code`, il controllo raggiunge `E.true` oppure `E.false` secondo il risultato della condizione.

![[Allegati/FLT_08_03/flt_08_03_corto_circuito.svg]]

## 4. Caso base: un confronto

La produzione presente nel compito di gennaio è:

$$
E \to id_1\ relop\ id_2
$$

con la regola:

```text
E.code :=
    gen('if', id1.place, relop.op, id2.place, 'goto', E.true)
    || gen('goto', E.false)
```

Ricorda che:

- `relop.op` identifica il confronto, per esempio `<` oppure `>`;
- `gen(...)` costruisce un frammento di codice;
- `||` concatena frammenti: qui non è l’operatore booleano `or`.

Per `x>y`, con `E.true=L1` ed `E.false=L2`:

```text
if x > y goto L1
goto L2
```

Se il confronto è vero, il primo salto porta a `L1` e il secondo non viene eseguito. Se è falso, si passa all’istruzione seguente e si esegue `goto L2`.

Le definizioni di `L1` e `L2` saranno inserite dal contesto che utilizza la condizione.

## 5. Traduzione di OR

Consideriamo:

$$
E \to E_1\ \mathrm{or}\ E_2
$$

Le regole della DDS del compito sono:

```text
E1.true  := E.true
E1.false := newlabel()

E2.true  := E.true
E2.false := E.false

E.code :=
    E1.code
    || gen(E1.false, ':')
    || E2.code
```

La nuova etichetta `E1.false` identifica **l’inizio del codice del secondo operando**.

Il ragionamento è:

1. se `E1` è vera, l’intero `or` è vero: si raggiunge subito `E.true`;
2. se `E1` è falsa, bisogna ancora valutare `E2`;
3. il risultato di `E2` determina allora il risultato complessivo.

> [!tip] Regola da ricordare
> In un `or`, i due operandi condividono la destinazione vera. La destinazione falsa del primo conduce al secondo.

`newlabel()` crea un nome fresco durante la traduzione. Non è un’istruzione eseguita dal programma tradotto.

## 6. Esempio dell’esame: x>y or x>z

Identifichiamo le tre occorrenze:

```text
E  = x>y or x>z
E1 = x>y
E2 = x>z
```

Fissiamo `E.true=L1` ed `E.false=L2`. Per il passaggio al secondo confronto scegliamo l’etichetta fresca `L3`.

| Occorrenza | `true` | `false` |
|---|---|---|
| `E`: `x>y or x>z` | `L1` | `L2` |
| `E1`: `x>y` | `L1` | `L3` |
| `E2`: `x>z` | `L1` | `L2` |

I frammenti sono:

```text
E1.code:
    if x > y goto L1
    goto L3

E2.code:
    if x > z goto L1
    goto L2
```

Le scritte `E1.code:` ed `E2.code:` sono intestazioni esplicative, non etichette del programma.

Concatenando secondo la DDS otteniamo:

```text
if x > y goto L1
goto L3
L3:
if x > z goto L1
goto L2
```

Nel successivo `if-then-else`, `L1` sarà l’ingresso del ramo `then` e `L2` quello del ramo `else`.

### Controllo dei percorsi

| Valori | Primo confronto | Secondo confronto | Destinazione |
|---|---|---|---|
| `x=10, y=3, z=12` | Vero | Non eseguito | `L1` |
| `x=5, y=8, z=2` | Falso | Vero | `L1` |
| `x=2, y=8, z=5` | Falso | Falso | `L2` |

Nel primo caso `x>z` sarebbe falso, ma non serve valutarlo: `x>y` ha già determinato il risultato.

## 7. Esempio dell’esame: a<b or a<c

Per la condizione del `while` di giugno usiamo, in questo esempio separato:

- `L1` per entrare nel corpo;
- `L2` per uscire dal ciclo;
- `L3` per iniziare il secondo confronto.

Il codice della sola condizione è:

```text
if a < b goto L1
goto L3
L3:
if a < c goto L1
goto L2
```

Si entra nel corpo se almeno un confronto è vero. Si esce soltanto se entrambi sono falsi.

Quando il ciclo torna a controllare la condizione, ricomincia dal **primo** confronto, usando i valori correnti delle variabili. L’etichetta di ritorno al test verrà introdotta nella nota sul `while`.

## 8. Traduzione di AND

Per:

$$
E \to E_1\ \mathrm{and}\ E_2
$$

le regole sono:

```text
E1.true  := newlabel()
E1.false := E.false

E2.true  := E.true
E2.false := E.false

E.code :=
    E1.code
    || gen(E1.true, ':')
    || E2.code
```

Questa volta si raggiunge il secondo operando soltanto quando il primo è vero.

> [!tip] Confronto con OR
> In un `and`, i due operandi condividono la destinazione falsa. La destinazione vera del primo conduce al secondo.

Per `x != 0 and 10/x > 1`, con destinazioni finali `L1` e `L2`:

```text
if x != 0 goto L3
goto L2
L3:
t1 := 10 / x
if t1 > 1 goto L1
goto L2
```

La divisione appartiene al codice del secondo operando: si trova **dopo `L3`**. Se `x=0`, il salto a `L2` la evita.

Spostare `t1 := 10/x` prima del primo confronto eliminerebbe questa protezione.

## 9. Negazione, parentesi e costanti

Per:

$$
E \to \mathrm{not}\ E_1
$$

si scambiano le destinazioni:

```text
E1.true  := E.false
E1.false := E.true
E.code   := E1.code
```

Non serve necessariamente un’istruzione che calcoli materialmente la negazione.

Per le parentesi:

```text
E → (E1)

E1.true  := E.true
E1.false := E.false
E.code   := E1.code
```

Per le costanti:

```text
E → true
E.code := gen('goto', E.true)

E → false
E.code := gen('goto', E.false)
```

Questi schemi presuppongono che la struttura dell’espressione sia già stabilita dalla grammatica. Per esempio, se `and` ha precedenza su `or`, `P or Q and R` si interpreta come `P or (Q and R)`.

## 10. Generare codice ed eseguirlo sono momenti diversi

Il traduttore costruisce il codice di entrambi gli operandi, perché il risultato del primo confronto può dipendere dai dati disponibili soltanto durante l’esecuzione.

Il corto circuito riguarda **quali istruzioni vengono effettivamente eseguite**.

Perciò la presenza di `E2.code` nel programma generato è corretta: sono i salti a impedirne l’esecuzione quando non serve.

Analogamente, una condizione tradotta mediante salti non richiede necessariamente un temporaneo contenente `0` o `1`. Se occorre memorizzare il risultato booleano, i percorsi vero e falso possono assegnare i due valori a una variabile e poi ricongiungersi.

## 11. Procedura per gli esercizi

1. **Individua la struttura sintattica**, rispettando parentesi e precedenze.
2. **Assegna le destinazioni finali** della condizione complessiva.
3. **Propaga le etichette** ai figli usando la regola dell’operatore.
4. **Crea etichette fresche** per gli ingressi dei secondi operandi.
5. **Traduci i confronti** e concatena i frammenti nell’ordine prescritto.
6. **Simula i percorsi**: primo operando decisivo, secondo vero, secondo falso.

Se l’esercizio richiede gli attributi delle occorrenze di `E`, presenta anche una tabella: i soli salti finali non mostrano esplicitamente tutti i valori richiesti.

## 12. Errori da evitare

- **Valutare prima entrambi gli operandi e poi applicare `or`:** non realizza il corto circuito.
- **Mandare il falso di `E1` direttamente a `E.false` in un OR:** salta il confronto che potrebbe rendere vera l’espressione.
- **Mandare il vero di `E1` direttamente a `E.true` in un AND:** il secondo operando potrebbe essere falso.
- **Dimenticare l’etichetta prima di `E2.code`:** il salto intermedio resta senza destinazione.
- **Riutilizzare una nuova etichetta per punti diversi:** può collegare erroneamente frammenti distinti.
- **Eliminare salti senza controllare il flusso:** all’esame conviene applicare prima la DDS assegnata e motivare eventuali ottimizzazioni.

## Collegamenti

- [[02 - Traduzione di espressioni aritmetiche e assegnamenti]]
- [[04 - Attributi true, false, next e begin]]
- [[05 - Traduzione di if-then-else]]
- [[06 - Traduzione di while]]
- [[04 - Esercizi su attributi e generazione del codice]]
