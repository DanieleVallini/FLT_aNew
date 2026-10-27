# Attraversamento dell’albero e valutazione degli attributi

## 1. Dalle dipendenze a una visita dell’albero

Il grafo delle dipendenze stabilisce quali valori devono essere disponibili prima di altri.

Una **visita dell’albero** organizza concretamente l’elaborazione dei nodi e l’esecuzione delle regole semantiche.

Per essere corretta, la visita deve rispettare tutte le dipendenze degli attributi calcolati.

Un ordine topologico descrive precedenze fra istanze di attributo; una visita stabilisce anche quando entrare nei sottoalberi e quando ritornare al padre.

> [!important]
> Non basta scegliere un ordine per le assegnazioni.
> Occorre collocarle rispetto alle visite dei sottoalberi che producono o utilizzano quei valori.

---

## 2. Preordine, postordine e profondità

### 2.1 Visita in preordine

In una visita in **preordine**, l’azione associata a un nodo viene eseguita prima di visitarne i figli.

È adatta a operazioni che utilizzano informazioni già disponibili all’ingresso.

Per esempio, un contesto ricevuto dal padre può essere utilizzato per preparare dati necessari a un figlio.

Un sintetizzato che richiede risultati ancora da ottenere dai figli non può essere calcolato in quel momento.

### 2.2 Visita in postordine

In una visita in **postordine**, si visitano prima i figli e poi si esegue l’azione del padre.

Questo ordine è naturale per i sintetizzati che combinano risultati dei figli.

Per esempio:

$$
E\to E_1+T
$$

$$
E.val:=E_1.val+T.val
$$

richiede prima l’elaborazione di $E_1$ e $T$.

### 2.3 Visita in profondità da sinistra verso destra

Una visita in profondità completa l’elaborazione di un sottoalbero prima di passare al successivo.

Per:

$$
A\to BCA_1
$$

l’ordine dei sottoalberi è:

$$
B,\quad C,\quad A_1
$$

Tra il ritorno da un figlio e la discesa nel successivo si possono eseguire altre azioni.

Questi punti intermedi sono essenziali quando un ereditato di un figlio dipende dal risultato di un fratello a sinistra.

---

## 3. Ereditati in ingresso, sintetizzati al ritorno

Nel caso usuale di una DDS L-attribuita:

- gli ereditati di un figlio dipendono dagli ereditati del padre e dagli attributi dei fratelli a sinistra;
- i risultati sintetizzati vengono ottenuti dopo l’elaborazione del sottoalbero.

Prima di entrare in un figlio, il padre ne prepara quindi gli ereditati.

Quando la visita del figlio termina, i suoi sintetizzati diventano disponibili al padre.

Uno schema generale è:

```text
visita(n):
    # Gli ereditati di n sono già disponibili.

    per ogni figlio c di n, da sinistra a destra:
        calcola gli ereditati di c
        visita(c)

    calcola i sintetizzati di n
```

Lo schema richiede che gli ereditati siano effettivamente calcolabili prima della visita del figlio; eventuali dipendenze locali più generali devono essere controllate separatamente.

Per i terminali, gli attributi lessicali sono forniti dal lexer.

Per una produzione vuota non ci sono sottoalberi da visitare, ma possono esserci regole semantiche da eseguire.

> [!warning] Una visita, con momenti diversi
> La discesa e il ritorno avvengono durante una sola visita in profondità.
> Non si tratta di calcolare prima tutti gli ereditati dell’intero albero e successivamente tutti i sintetizzati.

In particolare, un ereditato che usa il sintetizzato di un fratello deve attendere il ritorno da quel fratello.

---

## 4. Applicazione al frammento d’esame

Consideriamo:

$$
A\to B\,C\,A_1
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

Nella consegna gli attributi indicati con $s$ sono dichiarati sintetizzati.

Dalla classificazione delle altre regole sappiamo che:

- $A.a$ e $A_1.a$ sono sintetizzati;
- $A.b$, $A_1.b$ e $C.c$ sono ereditati.

Supponiamo, come richiesto dall’esercizio, che i sintetizzati dei figli dipendano dai rispettivi ereditati.

In particolare:

$$
C.c\to C.s
$$

$$
A_1.b\to A_1.a
$$

L’ereditato $A.b$ deve essere disponibile all’ingresso nel frammento.

### 4.1 Prima visita: il sottoalbero di B

Per $B$ non sono mostrati ereditati da assegnare.

Visitiamo il suo sottoalbero e otteniamo:

$$
B.s
$$

Ora possiamo utilizzare questo risultato per preparare gli ingressi degli altri figli.

### 4.2 Prima di C

Calcoliamo:

$$
C.c:=h(B.s,A.b)
$$

Entrambi gli argomenti sono disponibili:

- $B.s$ è stato ottenuto dalla visita precedente;
- $A.b$ è ricevuto dal contesto.

### 4.3 Visita del sottoalbero di C

Entriamo in $C$ dopo avergli fornito $C.c$.

La visita rende disponibile:

$$
C.s
$$

### 4.4 Prima di A1

Calcoliamo:

$$
A_1.b:=g(B.s)
$$

Il valore $B.s$ è ancora disponibile nel contesto del padre.

### 4.5 Visita del sottoalbero di A1

Visitiamo $A_1$ con il suo ereditato già assegnato.

Al ritorno otteniamo:

$$
A_1.a
$$

### 4.6 Uscita da A

Ora sono disponibili entrambi gli argomenti di $f$:

$$
C.s
\qquad\text{e}\qquad
A_1.a
$$

Possiamo quindi calcolare:

$$
A.a:=f(C.s,A_1.a)
$$

e restituire il risultato al contesto superiore.

---

## 5. Schema completo della visita

![[Allegati/FLT_07_04/flt_07_04_visita_attributi.svg|1200]]

I riquadri di $B$, $C$ e $A_1$ rappresentano interi sottoalberi: le loro produzioni interne non sono mostrate.

La sequenza è:

| Momento | Visita o azione | Risultato disponibile |
|---|---|---|
| Ingresso in $A$ | Ricevi $A.b$ | $A.b$ |
| 1 | Visita $B$ | $B.s$ |
| 2 | $C.c:=h(B.s,A.b)$ | $C.c$ |
| 3 | Visita $C$ | $C.s$ |
| 4 | $A_1.b:=g(B.s)$ | $A_1.b$ |
| 5 | Visita $A_1$ | $A_1.a$ |
| 6 | $A.a:=f(C.s,A_1.a)$ | $A.a$ |

In pseudocodice, per questa produzione:

```text
# A.b è già disponibile.

visita(B)

C.c := h(B.s, A.b)
visita(C)

A1.b := g(B.s)
visita(A1)

A.a := f(C.s, A1.a)
```

Le chiamate visita elaborano i rispettivi sottoalberi e ne rendono disponibili i sintetizzati.

Questo è lo schema relativo al frammento: un valutatore completo deve gestire anche tutte le altre produzioni.

> [!tip] Un’azione può essere anticipata quando i dati sono pronti
> $A_1.b$ potrebbe essere calcolato già subito dopo la visita di $B$.
> Nello schema proposto lo calcoliamo immediatamente prima della visita di $A_1$: entrambe le scelte rispettano le dipendenze.

---

## 6. Perché non eseguire tutte le azioni nello stesso momento

### 6.1 Tutte le azioni all’ingresso in A

All’ingresso non sono ancora stati ottenuti i risultati dei sottoalberi.

In particolare:

- $B.s$ serve per calcolare $C.c$ e $A_1.b$;
- $C.s$ e $A_1.a$ servono per calcolare $A.a$.

Eseguire tutte le regole prima di visitare i figli non rispetta queste dipendenze.

### 6.2 Tutte le azioni all’uscita da A

Se rimandassimo anche $C.c$ e $A_1.b$ fino all’uscita:

- visiteremmo $C$ senza avergli fornito $C.c$;
- visiteremmo $A_1$ senza avergli fornito $A_1.b$.

L’ipotesi dell’esame stabilisce che quei dati sono necessari per ottenere i sintetizzati.

Quindi anche questa collocazione sarebbe scorretta.

> [!important]
> Nel frammento d’esame servono azioni prima della discesa in alcuni figli e un’azione al ritorno da tutti i figli.
> Una sola visita in profondità può includere entrambi i momenti.

---

## 7. Un grafo aciclico può richiedere un’altra visita

Consideriamo un altro frammento:

$$
A\to BC
$$

con:

$$
B.i:=C.s
$$

Supponiamo che $C.s$ venga ottenuto al termine della visita di $C$.

Una visita che tenti di completare prima $B$ trova un problema: $B.i$ richiede il risultato di un fratello ancora da visitare.

Il grafo potrebbe comunque essere aciclico.

Su un albero già costruito, una strategia possibile potrebbe elaborare prima $C$ e poi $B$, se le altre dipendenze lo consentono.

Cambiare l’ordine di elaborazione dei sottoalberi non cambia l’ordine dei simboli nella frase generata.

Questo esempio distingue:

- l’esistenza di qualche ordine di valutazione;
- la compatibilità con una specifica visita da sinistra verso destra.

Nel frammento dell’esame, gli ereditati richiesti utilizzano soltanto il contesto del padre e il risultato del fratello $B$ già elaborato: la visita proposta è quindi compatibile con le dipendenze mostrate.

---

## 8. Collegamento con l’analisi bottom-up

Durante una riduzione:

$$
A\to X_1\cdots X_n
$$

un parser bottom-up ha già riconosciuto il corpo della produzione.

Questo è un momento naturale per calcolare un sintetizzato di $A$ usando i risultati dei simboli del corpo.

Gli ereditati necessari durante l’elaborazione dei figli possono invece richiedere azioni precedenti alla riduzione finale.

Perciò la corretta visita dell’albero individua i momenti necessari dei calcoli, mentre l’esecuzione durante il parsing richiede di realizzare concretamente quei momenti.

Le prossime note studieranno come inserire le azioni e trasformare le produzioni.

---

## 9. Risposta sintetica da dare all’esame

Per il frammento considerato, una risposta completa è:

> È possibile valutare il frammento mediante una visita in profondità dei sottoalberi da sinistra verso destra, assumendo disponibile $A.b$ all’ingresso. Si visita prima $B$ per ottenere $B.s$; si calcola $C.c$ e si visita $C$ per ottenere $C.s$; si calcola $A_1.b$ e si visita $A_1$ per ottenere $A_1.a$. Infine si calcola $A.a$. Ogni attributo ereditato richiesto è quindi disponibile prima dell’elaborazione del relativo sottoalbero e il sintetizzato del padre viene calcolato dopo i risultati dei figli.

Per controllare una soluzione:

1. scrivi quali valori sono disponibili all’ingresso;
2. prima di ogni visita, verifica gli ereditati necessari;
3. al ritorno, aggiungi i sintetizzati ottenuti;
4. esegui ogni azione soltanto quando tutti gli argomenti sono disponibili;
5. verifica anche l’ordine richiesto per i sottoalberi.

## Collegamenti

- [[01 - Definizioni dirette dalla sintassi e regole semantiche]]
- [[02 - Attributi sintetizzati ed ereditati]]
- [[03 - Grafi delle dipendenze e ordine di valutazione]]
- [[05 - Esecuzione delle azioni durante l analisi bottom-up]]
- [[06 - Trasformazione delle produzioni per le azioni semantiche]]
