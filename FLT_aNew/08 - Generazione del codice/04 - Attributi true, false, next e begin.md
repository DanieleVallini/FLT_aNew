# Attributi true, false, next e begin

## 1. A cosa servono questi attributi

Nella traduzione mediante salti, questi attributi collegano il codice delle condizioni con quello delle istruzioni che le utilizzano.

I loro valori sono **etichette simboliche**, cioè nomi di posizioni nel codice generato.

| Attributo | Significato |
|---|---|
| `E.true` | Destinazione da raggiungere quando la condizione `E` è vera |
| `E.false` | Destinazione da raggiungere quando la condizione `E` è falsa |
| `S.next` | Destinazione della continuazione normale dopo il completamento dell’istruzione `S` |
| `S.begin` | Etichetta iniziale del ciclo `S`, dalla quale rivalutare la condizione |

La distinzione fondamentale è:

- `true` e `false` descrivono le due uscite di una **condizione**;
- `next` descrive la continuazione di un’**istruzione**;
- `begin` identifica il punto al quale ritornare per ripetere il controllo di un **ciclo**.

> [!important] Non contengono il risultato dell’esecuzione
> `E.true=L2` non significa che `E` vale vero. Significa che, se durante l’esecuzione `E` risulterà vera, il codice dovrà raggiungere `L2`.

## 2. Creare, usare e definire un’etichetta

Sono tre operazioni diverse.

### Creare un nome

```text
E.true := newlabel()
```

Il traduttore ottiene un’etichetta fresca, per esempio `L2`, e la memorizza nell’attributo.

Questa operazione avviene durante la traduzione.

### Generare un salto

```text
gen('goto', E.true)
```

Se `E.true=L2`, viene costruita l’istruzione:

```text
goto L2
```

### Definire la posizione di destinazione

```text
gen(E.true, ':')
```

Produce:

```text
L2:
```

Questa definizione stabilisce dove si trova la destinazione del salto.

> [!warning] Creare il nome non definisce la posizione
> Una chiamata a `newlabel()` non inserisce automaticamente `L2:` nel codice. La definizione viene emessa da una regola che utilizza `gen(etichetta, ':')`.

Un salto può precedere la definizione della propria destinazione: è proprio uno degli scopi delle etichette simboliche.

## 3. Attributi ereditati e sintetizzati

Negli schemi che stiamo utilizzando:

| Attributo | Classificazione | Motivazione |
|---|---|---|
| `E.true` | Ereditato | Il contesto comunica alla condizione dove andare in caso di verità |
| `E.false` | Ereditato | Il contesto comunica dove andare in caso di falsità |
| `S.next` | Ereditato | Il contesto stabilisce come proseguire dopo `S` |
| `E.code` | Sintetizzato | Il codice viene costruito dalle regole dell’espressione |
| `S.code` | Sintetizzato | Il codice viene costruito dalle regole dell’istruzione |

Nel compito di giugno compare inoltre:

```text
S → while E do S1

S.begin := newlabel()
```

Qui `S.begin` è un attributo **sintetizzato**: viene definito per l’occorrenza di `S` nella testa della produzione.

Non dipendendo da attributi dei figli, può essere calcolato prima di tradurre i sottoalberi.

> [!important] Sintetizzato non significa necessariamente “calcolato alla fine”
> L’ordine di valutazione dipende dalle dipendenze effettive. Per creare `S.begin` non occorre aspettare il codice del corpo.

Alcune presentazioni usano invece una variabile ausiliaria locale chiamata semplicemente `begin`. Per classificare gli attributi bisogna leggere la DDS effettivamente assegnata.

## 4. Che cosa significa davvero next

`S.next` rappresenta la destinazione da raggiungere quando l’esecuzione di quella specifica istruzione è completata normalmente.

Non indica necessariamente la riga fisicamente successiva nel codice.

Consideriamo:

```text
while E do S1
```

Ci sono due istruzioni distinte:

- `S`, cioè l’intero ciclo;
- `S1`, cioè il suo corpo.

Di conseguenza:

```text
S.next  = continuazione dopo l’intero ciclo
S1.next = punto dal quale rivalutare la condizione
```

Perciò la DDS assegna:

```text
S1.next := S.begin
```

> [!warning] Il corpo e il ciclo hanno continuazioni diverse
> Terminare il corpo significa prepararsi all’iterazione successiva. Terminare il ciclo significa proseguire con ciò che viene dopo il ciclo.

Inoltre, avere un attributo `next` non impone di generare un salto alla fine di ogni istruzione.

Un semplice assegnamento può terminare per prosecuzione sequenziale; il codice del costrutto che lo contiene inserirà i salti necessari.

## 5. Come true e false si propagano nelle condizioni

Per:

$$
E \to E_1\ \mathrm{or}\ E_2
$$

le etichette si distribuiscono così:

| Attributo | Valore |
|---|---|
| `E1.true` | `E.true` |
| `E1.false` | Nuova etichetta all’ingresso di `E2.code` |
| `E2.true` | `E.true` |
| `E2.false` | `E.false` |

La destinazione falsa del primo operando è **interna** alla traduzione dell’`or`: serve a raggiungere il secondo operando.

Non coincide, in generale, con la destinazione falsa dell’intera espressione.

Per un `and`, invece:

```text
E1.true  := nuova etichetta all’ingresso di E2.code
E1.false := E.false
E2.true  := E.true
E2.false := E.false
```

Le etichette devono quindi essere assegnate alle **singole occorrenze** dei simboli nell’albero.

Due nodi chiamati entrambi `E` possono avere destinazioni diverse.

## 6. Attributi nell’if-then-else dell’esame

Il compito di gennaio propone:

```text
if x>y or x>z then x:=x-w else x:=x+w
```

La produzione è:

$$
S \to \mathrm{if}\ E\ \mathrm{then}\ S_1\ \mathrm{else}\ S_2
$$

Le regole per le destinazioni sono:

```text
E.true  := newlabel()
E.false := newlabel()

S1.next := S.next
S2.next := S.next
```

Entrambi i rami hanno la stessa continuazione: quella dell’intero `if`.

Scegliamo questi nomi:

| Etichetta | Posizione rappresentata |
|---|---|
| `L1` | Inizio del ramo `then` |
| `L2` | Inizio del ramo `else` |
| `L3` | Inizio del secondo confronto dell’`or` |
| `L4` | Continuazione dopo l’intero `if` |

Otteniamo:

| Occorrenza o attributo | Valore |
|---|---|
| `S.next` | `L4` |
| `S1.next` | `L4` |
| `S2.next` | `L4` |
| `E.true` | `L1` |
| `E.false` | `L2` |
| `E1.true`, per `x>y` | `L1` |
| `E1.false`, per `x>y` | `L3` |
| `E2.true`, per `x>z` | `L1` |
| `E2.false`, per `x>z` | `L2` |

Il salto emesso dopo il ramo `then` è:

```text
goto S.next
```

Quindi, con questi valori:

```text
goto L4
```

Serve a raggiungere la continuazione senza proseguire nel codice del ramo `else`.

## 7. Attributi nel while dell’esame

Il compito di giugno propone:

```text
while a<b or a<c do a:=a+b*c
```

La produzione è:

$$
S \to \mathrm{while}\ E\ \mathrm{do}\ S_1
$$

Le regole complete per le etichette sono:

```text
S.begin := newlabel()
E.true  := newlabel()
E.false := S.next
S1.next := S.begin
```

Le due azioni da completare nel testo sono dunque:

```text
E.true  := newlabel()
E.false := S.next
```

La prima introduce l’ingresso del corpo. La seconda fa coincidere l’esito falso della condizione con l’uscita dal ciclo.

Per questo esempio chiamiamo:

| Etichetta | Posizione rappresentata |
|---|---|
| `L1` | Inizio del test del ciclo |
| `L2` | Inizio del corpo |
| `L3` | Inizio del secondo confronto dell’`or` |
| `L4` | Continuazione dopo il ciclo |

La tabella degli attributi diventa:

| Occorrenza o attributo | Valore |
|---|---|
| `S.begin` | `L1` |
| `S.next` | `L4` |
| `S1.next` | `L1` |
| `E.true` | `L2` |
| `E.false` | `L4` |
| `E1.true`, per `a<b` | `L2` |
| `E1.false`, per `a<b` | `L3` |
| `E2.true`, per `a<c` | `L2` |
| `E2.false`, per `a<c` | `L4` |

La differenza da fissare è:

```text
S1.next = L1: ripeti il controllo
S.next  = L4: prosegui dopo il ciclo
```

## 8. Chi inserisce le definizioni delle etichette

Nel compito di giugno:

```text
S.code :=
    gen(S.begin, ':')
    || E.code
    || gen(E.true, ':')
    || S1.code
    || gen('goto', S.begin)
```

Questo frammento definisce:

- `S.begin`, prima del test;
- `E.true`, prima del corpo.

La regola dell’`or` definisce inoltre l’etichetta intermedia prima del secondo confronto.

La definizione di `S.next` non compare in questa formula: viene fornita dal **contesto che contiene il ciclo**.

> [!important] Un frammento può usare un’etichetta definita all’esterno
> `goto S.next` è corretto anche se `S.next:` non viene emesso dentro `S.code`. Nel codice completo, la destinazione deve essere definita coerentemente.

Questo risponde alla richiesta dell’esame sulle etichette non generate durante la traduzione del frammento: la destinazione ereditata `S.next` rappresenta la continuazione dopo il ciclo.

## 9. Esempio di contesto: una sequenza di istruzioni

Consideriamo:

$$
S \to S_1\ S_2
$$

Un possibile schema è:

```text
S1.next := newlabel()
S2.next := S.next

S.code :=
    S1.code
    || gen(S1.next, ':')
    || S2.code
```

La continuazione di `S1` è l’ingresso di `S2`.

La continuazione di `S2` coincide con quella dell’intera sequenza.

È il padre `S` a definire l’etichetta `S1.next`, collocandola fra i due frammenti.

Questo mostra concretamente come un attributo ereditato possa essere utilizzato dal figlio e definito nel codice composto dal padre.

## 10. Costrutti annidati e condivisione delle etichette

Consideriamo:

```text
while P do
    if Q then A else B
```

L’istruzione `if` costituisce il corpo del ciclo. La sua continuazione è quindi l’inizio del test di `P`.

Di conseguenza, anche le continuazioni dei rami `A` e `B` vengono impostate su quel punto.

Ogni occorrenza mantiene i propri attributi: non esiste un unico `next` globale valido per tutte le istruzioni.

Attributi diversi possono però contenere la stessa etichetta intenzionalmente:

```text
S1.next := S.begin
```

Questa assegnazione condivide una destinazione già esistente e non richiede `newlabel()`.

I nomi `L1`, `L2` e così via sono arbitrari. Due traduzioni con numerazioni diverse sono equivalenti se collegano gli stessi punti del flusso di controllo.

## 11. Procedura ed errori da evitare

Per risolvere un esercizio:

1. individua l’istruzione completa e le sue sottoistruzioni;
2. stabilisci la continuazione ricevuta dal contesto;
3. crea soltanto le etichette interne necessarie;
4. propaga `true`, `false` e `next` alle occorrenze corrette;
5. indica dove ogni etichetta viene definita;
6. verifica i percorsi di ingresso, uscita e ripetizione.

Gli errori più frequenti sono:

- **Confondere `E.false` con `E1.false` in un OR:** la prima è l’uscita falsa complessiva, la seconda conduce al secondo operando.
- **Assegnare `S1.next := S.next` nel while:** il corpo deve tornare al test.
- **Assegnare `E.false := S.begin` nel while:** una condizione falsa deve far uscire dal ciclo.
- **Creare una nuova etichetta invece di copiarne una richiesta:** si perde il collegamento con la destinazione corretta.
- **Confondere `newlabel()` con l’emissione di `L:`:** ottenere un nome e definirne la posizione sono operazioni distinte.

## Collegamenti

- [[01 - Codice intermedio, temporanei ed etichette simboliche]]
- [[03 - Espressioni booleane e valutazione a corto circuito]]
- [[05 - Traduzione di if-then-else]]
- [[06 - Traduzione di while]]
- [[02 - Attributi sintetizzati ed ereditati]]