# Traduzione di while

## 1. Comportamento del ciclo

L’istruzione:

```text
while E do S1
```

controlla la condizione `E` prima di ogni eventuale esecuzione del corpo:

1. se `E` è falsa, esce dal ciclo;
2. se `E` è vera, esegue `S1`;
3. al termine del corpo, torna a controllare `E`.

Il corpo può quindi essere eseguito **zero, una o più volte**. Per alcuni programmi e valori iniziali, il ciclo può anche non terminare.

> [!important] Il controllo precede il corpo
> Una condizione inizialmente falsa deve impedire qualsiasi esecuzione del corpo. Una traduzione che esegue prima il corpo cambia la semantica del `while`.

Il compito del **24 giugno 2025** richiede la traduzione di:

```text
while a<b or a<c do a:=a+b*c
```

## 2. Definizione diretta dalla sintassi

La produzione è:

$$
S \to \mathrm{while}\ E\ \mathrm{do}\ S_1
$$

Le regole complete sono:

```text
S.begin := newlabel()
E.true  := newlabel()
E.false := S.next
S1.next := S.begin

S.code :=
    gen(S.begin, ':')
    || E.code
    || gen(E.true, ':')
    || S1.code
    || gen('goto', S.begin)
```

Nel testo d’esame le azioni da completare sono:

```text
E.true  := newlabel()
E.false := S.next
```

La prima crea l’ingresso del corpo. La seconda collega la falsità della condizione alla continuazione dopo il ciclo.

`gen(...)` costruisce codice; `||` concatena frammenti. Le chiamate a `newlabel()` avvengono durante la traduzione.

## 3. Le destinazioni da distinguere

| Attributo | Destinazione |
|---|---|
| `S.begin` | Inizio del codice che valuta la condizione |
| `E.true` | Ingresso del corpo |
| `E.false` | Uscita dal ciclo, coincidente con `S.next` |
| `S1.next` | Ritorno al test, coincidente con `S.begin` |
| `S.next` | Continuazione dell’intera istruzione `while` |

Le due uguaglianze fondamentali sono:

$$
E.false = S.next
$$

$$
S_1.next = S.begin
$$

> [!warning] Corpo e ciclo hanno continuazioni diverse
> Dopo il corpo bisogna rivalutare la condizione. Dopo il ciclo bisogna proseguire con il contesto successivo.

Nel caso di un semplice assegnamento, `S1.code` non deve necessariamente usare esplicitamente `S1.next`: il salto di ritorno viene comunque aggiunto dalla regola del `while`.

## 4. Svolgimento completo dell’esercizio di giugno

### 4.1 Separare condizione e corpo

```text
E  = a<b or a<c
E1 = a<b
E2 = a<c

S1 = a:=a+b*c
```

La condizione richiede una valutazione a corto circuito:

- se `a<b` è vero, si entra subito nel corpo;
- se `a<b` è falso, si controlla `a<c`;
- si esce soltanto quando entrambi i confronti sono falsi.

### 4.2 Assegnare le etichette

Scegliamo questi nomi:

| Etichetta | Significato |
|---|---|
| `L1` | Inizio del test del ciclo |
| `L2` | Ingresso del corpo |
| `L3` | Ingresso del secondo confronto dell’`or` |
| `L4` | Continuazione dopo il ciclo |

Ponendo `S.next=L4`:

```text
S.begin = L1
E.true  = L2
E.false = L4
S1.next = L1
```

Dalla regola dell’`or` segue:

| Occorrenza | `true` | `false` |
|---|---|---|
| `E`: `a<b or a<c` | `L2` | `L4` |
| `E1`: `a<b` | `L2` | `L3` |
| `E2`: `a<c` | `L2` | `L4` |

Questa tabella risponde alla richiesta sui valori degli attributi delle diverse occorrenze di `E`.

### 4.3 Tradurre la condizione

Il codice della sola condizione è:

```text
if a < b goto L2
goto L3
L3:
if a < c goto L2
goto L4
```

`L3` viene definita dalla regola dell’`or`, immediatamente prima del codice del secondo operando.

Il falso del primo confronto non porta all’uscita: resta ancora da controllare il secondo.

### 4.4 Tradurre l’assegnamento

Nel corpo:

```text
a := a + b*c
```

la moltiplicazione precede l’addizione.

Usando un temporaneo fresco per ogni operazione:

```text
t1 := b * c
t2 := a + t1
a := t2
```

Il valore di `a` usato nell’addizione è quello precedente all’assegnamento finale.

Il compito non richiede di specificare tutte le produzioni aritmetiche: è sufficiente indicare il codice del corpo.

### 4.5 Comporre il codice finale

Applicando la regola del `while`:

```text
L1:
    if a < b goto L2
    goto L3

L3:
    if a < c goto L2
    goto L4

L2:
    t1 := b * c
    t2 := a + t1
    a := t2
    goto L1

L4:
```

Il percorso di ripetizione è determinato da `goto L1`.

La definizione finale `L4:` è mostrata per rendere esplicita la continuazione: non viene emessa dalla formula di `S.code` del compito.

![[Allegati/FLT_08_06/flt_08_06_while.svg]]

## 5. Simulazione con più iterazioni

Consideriamo valori interi iniziali:

```text
a = -1
b = 1
c = 2
```

Nel corpo si calcola ogni volta:

$$
a := a + 1\cdot 2 = a+2
$$

La simulazione è:

| Controllo | `a` prima del test | `a<b` | `a<c` | Azione | `a` dopo l’azione |
|---|---|---|---|---|---|
| 1 | `-1` | Vero | Non valutato | Esegue il corpo | `1` |
| 2 | `1` | Falso | Vero | Esegue il corpo | `3` |
| 3 | `3` | Falso | Falso | Esce dal ciclo | `3` |

Il corpo viene eseguito **due volte**, mentre la condizione viene controllata **tre volte**.

Al secondo controllo, `a<b` è falso perché `1<1` è falso. Il secondo confronto consente comunque di entrare nel corpo.

> [!tip] Controllo utile
> Per un’esecuzione che termina normalmente dopo `n` iterazioni, il test del `while` viene raggiunto `n+1` volte: l’ultimo controllo determina l’uscita.

Il numero dei singoli confronti può variare per effetto del corto circuito.

### Caso con zero iterazioni

Se inizialmente:

```text
a = 5
b = 2
c = 3
```

entrambi i confronti sono falsi. Il codice raggiunge `L4` senza entrare in `L2`, e `a` resta `5`.

## 6. Perché il salto deve tornare al primo confronto

Dopo il corpo bisogna rivalutare l’intera condizione con i valori correnti delle variabili.

Per questo:

```text
goto L1
```

è corretto.

Sarebbero invece errati:

- `goto L2`: ripeterebbe il corpo senza controllare la condizione;
- `goto L3`: rivaluterebbe soltanto il secondo confronto;
- `goto L4`: uscirebbe dopo il corpo, senza consentire ulteriori iterazioni.

Se manca il salto di ritorno, nel codice mostrato l’esecuzione prosegue verso `L4`: il ciclo non viene ripetuto.

La corretta collocazione di `L1` **prima di `E.code`** garantisce che il controllo riparta sempre dal punto giusto.

## 7. Codice generato una volta, eseguito più volte

Il traduttore costruisce una sola copia del codice del ciclo.

Durante l’esecuzione, i salti possono far percorrere quella stessa copia molte volte.

Quindi:

- `newlabel()` non viene richiamato a ogni iterazione;
- `newtemp()` assegna i nomi dei temporanei durante la traduzione;
- le istruzioni che scrivono `t1` e `t2` vengono invece rieseguite ogni volta che si entra nel corpo.

Non bisogna generare un numero di copie del corpo pari alle iterazioni: tale numero può dipendere dai dati e può non essere limitato.

Una traduzione corretta conserva anche le eventuali esecuzioni non terminanti del programma sorgente.

Per esempio, con `a=0, b=0, c=1`, la condizione è vera ma il corpo non modifica `a`: il ciclo continua a ripetersi.

## 8. Etichette interne e continuazione esterna

Nel nostro svolgimento, le etichette interne sono:

- `L1`, creata per `S.begin`;
- `L2`, creata per `E.true`;
- `L3`, creata per il passaggio al secondo operando dell’`or`.

`L4` è invece il valore dell’attributo ereditato `S.next`.

> [!important] Risposta alla richiesta sulle etichette esterne
> `S.next` rappresenta la continuazione dopo il completamento del ciclo. Il suo valore viene fornito dal contesto e la relativa posizione viene definita nel codice complessivo.

La continuazione non coincide necessariamente con la fine dell’intero programma.

Se il `while` è inserito in un costrutto più grande, `S.next` può identificare un’altra istruzione oppure un punto di controllo del costrutto contenitore.

I numeri delle etichette sono arbitrari: conta mantenere coerenti riferimenti e definizioni.

## 9. Procedura da seguire all’esame

1. **Individua condizione e corpo.**
2. **Completa le regole mancanti:** ingresso fresco del corpo e uscita falsa verso `S.next`.
3. **Assegna i nomi alle etichette**, distinguendo test, corpo, secondo operando e uscita.
4. **Compila la tabella `true/false`** per tutte le occorrenze booleane.
5. **Traduci il corpo**, rispettando precedenze e assegnamenti.
6. **Componi il codice**, mettendo il test prima del corpo e il ritorno dopo.
7. **Verifica almeno zero iterazioni e più iterazioni.**

Per il compito di giugno, ricorda anche di spiegare il significato dell’etichetta ereditata dall’esterno.

## 10. Errori da evitare

- **Mettere il corpo prima del test:** consentirebbe un’esecuzione anche con condizione inizialmente falsa.
- **Impostare `E.false := S.begin`:** una condizione falsa deve far uscire dal ciclo.
- **Impostare `S1.next := S.next`:** il corpo deve tornare al test, non uscire dall’intero ciclo.
- **Tornare al secondo operando dell’`or`:** bisogna ricontrollare l’intera condizione.
- **Dimenticare il salto di ritorno:** il codice può eseguire il corpo una sola volta.
- **Generare i confronti una sola volta fuori dal percorso di ripetizione:** si perderebbe il controllo sui valori aggiornati.

## Collegamenti

- [[02 - Traduzione di espressioni aritmetiche e assegnamenti]]
- [[03 - Espressioni booleane e valutazione a corto circuito]]
- [[04 - Attributi true, false, next e begin]]
- [[05 - Traduzione di if-then-else]]
- [[04 - Esercizi su attributi e generazione del codice]]
- [[05 - Esame 24 giugno 2025]]
