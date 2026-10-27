# Grafi delle dipendenze e ordine di valutazione

## 1. Dalle regole semantiche all’ordine di esecuzione

Una DDS specifica come calcolare gli attributi. Per eseguire quei calcoli dobbiamo conoscere quali valori devono essere disponibili prima di altri.

Il **grafo delle dipendenze** rappresenta questi vincoli.

Per una regola:

$$
b:=f(c_1,\ldots,c_k)
$$

il valore di $b$ richiede tutti gli argomenti $c_1,\ldots,c_k$.

Disegniamo quindi gli archi:

$$
c_1\to b,\quad\ldots,\quad c_k\to b
$$

> [!important] Direzione della freccia
> La freccia va dal valore necessario al valore da calcolare.
> $u\to v$ significa: «$u$ deve essere disponibile prima di calcolare $v$».

Se più archi entrano nello stesso vertice, rappresentano requisiti congiunti: devono essere disponibili tutti gli argomenti.

---

## 2. Vertici e archi del grafo

Dato un albero di derivazione $T$, il suo grafo delle dipendenze è un grafo orientato:

$$
D_T=(V_T,E_T)
$$

Ogni vertice rappresenta un’istanza di attributo, identificabile mediante:

$$
(\text{nodo dell’albero},\text{nome dell’attributo})
$$

Gli archi rappresentano le dipendenze introdotte dalle regole semantiche applicate nell’albero.

### 2.1 Le occorrenze restano distinte

Per:

$$
A\to BCA_1
$$

$A.a$ e $A_1.a$ appartengono a nodi diversi. Nel grafo sono quindi vertici distinti.

Anche due occorrenze dello stesso terminale hanno istanze distinte dei rispettivi attributi lessicali.

### 2.2 Albero e grafo delle dipendenze

| Struttura | Significato dei vertici | Significato dei collegamenti |
|---|---|---|
| Albero di derivazione | Occorrenze dei simboli grammaticali | Relazioni padre-figlio |
| Grafo delle dipendenze | Istanze degli attributi | Precedenze necessarie per il calcolo |

Un arco dell’albero non diventa automaticamente un arco del grafo delle dipendenze.

Le funzioni $f$, $g$ e $h$ specificano i calcoli: in questo modello i vertici rappresentano gli attributi, non le funzioni.

### 2.3 Valori iniziali

Un vertice senza archi entranti è una **fonte**.

Il suo valore può essere:

- una costante definita da una regola;
- un dato fornito dal lexer;
- un ereditato ricevuto dal contesto;
- il risultato di un sottoalbero non dettagliato nel grafo locale.

Essere una fonte non fornisce automaticamente un valore: bisogna sapere come ottenerlo.

---

## 3. Ordinamento topologico

Un **ordinamento topologico** è un elenco dei vertici:

$$
v_1,v_2,\ldots,v_n
$$

nel quale ogni arco:

$$
v_i\to v_j
$$

soddisfa:

$$
i<j
$$

Ogni valore compare quindi dopo tutti quelli necessari al suo calcolo.

Per un grafo finito:

$$
\boxed{
\text{esiste un ordinamento topologico}
\iff
\text{il grafo è aciclico}
}
$$

Un grafo orientato aciclico viene chiamato **DAG**.

### 3.1 Procedura di costruzione

1. Individua i vertici senza archi entranti.
2. Scegline uno e aggiungilo all’ordinamento.
3. Rimuovi quel vertice e i suoi archi uscenti.
4. Aggiorna i vertici diventati privi di archi entranti.
5. Ripeti.

Se vengono rimossi tutti i vertici, l’elenco ottenuto è un ordinamento topologico.

Se rimangono vertici ma nessuno può essere scelto, nel grafo residuo esiste un ciclo.

Quando più vertici sono disponibili contemporaneamente, possono esistere diversi ordinamenti corretti.

> [!warning]
> Avere almeno una fonte all’inizio non dimostra che tutto il grafo sia aciclico.
> Il procedimento deve riuscire a ordinare tutti i vertici.

---

## 4. Costruzione del grafo del frammento d’esame

Riprendiamo:

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

### 4.1 Dipendenze esplicite

Ogni argomento genera un arco verso l’attributo definito.

| Regola | Archi introdotti |
|---|---|
| $A.a:=f(C.s,A_1.a)$ | $C.s\to A.a$; $A_1.a\to A.a$ |
| $A_1.b:=g(B.s)$ | $B.s\to A_1.b$ |
| $C.c:=h(B.s,A.b)$ | $B.s\to C.c$; $A.b\to C.c$ |

Le tre regole introducono quindi cinque archi.

### 4.2 Dipendenze aggiunte dall’ipotesi della consegna

La consegna chiede di supporre che gli attributi sintetizzati dei simboli nel corpo dipendano dai loro attributi ereditati.

Per i simboli che possiedono ereditati nel frammento aggiungiamo:

$$
C.c\to C.s
$$

$$
A_1.b\to A_1.a
$$

Questi archi riassumono le dipendenze fra gli ingressi e i risultati dei rispettivi sottoalberi. Il calcolo concreto può comprendere più passaggi interni.

Per $B$ non sono mostrati ereditati: nel grafo locale $B.s$ rappresenta il risultato ottenuto elaborando il sottoalbero di $B$.

### 4.3 Grafo complessivo

I vertici sono:

$$
V=
\{A.b,\ B.s,\ C.c,\ C.s,\ A_1.b,\ A_1.a,\ A.a\}
$$

Il grafo contiene sette vertici e sette archi.

![[Allegati/FLT_07_03/flt_07_03_grafo_esame.svg|1200]]

Nell’immagine:

- gli archi continui derivano dalle regole esplicite;
- gli archi tratteggiati derivano dall’ipotesi sui sottoalberi;
- il colore dei vertici distingue ereditati e sintetizzati.

> [!important]
> Considerare $C.s$ e $A_1.a$ immediatamente disponibili eliminerebbe proprio le dipendenze aggiunte dall’ipotesi dell’esame.
> Prima devono essere preparati $C.c$ e $A_1.b$.

---

## 5. Un ordine corretto per il frammento

Un ordinamento compatibile è:

$$
(
A.b,\ B.s,\ C.c,\ C.s,\ A_1.b,\ A_1.a,\ A.a
)
$$

Lo possiamo leggere così:

| Passo | Attributo | Come diventa disponibile |
|---|---|---|
| 1 | $A.b$ | Ricevuto dal contesto del frammento |
| 2 | $B.s$ | Ottenuto elaborando il sottoalbero di $B$ |
| 3 | $C.c$ | Calcolato mediante $h(B.s,A.b)$ |
| 4 | $C.s$ | Ottenuto dal sottoalbero di $C$, dopo avergli fornito $C.c$ |
| 5 | $A_1.b$ | Calcolato mediante $g(B.s)$ |
| 6 | $A_1.a$ | Ottenuto dal sottoalbero di $A_1$, dopo avergli fornito $A_1.b$ |
| 7 | $A.a$ | Calcolato mediante $f(C.s,A_1.a)$ |

Questa sequenza descrive l’ordine dei risultati visibili nel grafo locale. Non espande tutte le operazioni interne ai sottoalberi.

Il risultato $A.a$ viene calcolato soltanto quando sono disponibili entrambi gli argomenti di $f$.

### 5.1 Un altro ordine possibile

Anche:

$$
(
A.b,\ B.s,\ A_1.b,\ A_1.a,\ C.c,\ C.s,\ A.a
)
$$

rispetta tutti gli archi del grafo locale.

Dopo aver ottenuto $A.b$ e $B.s$, i due rami:

$$
C.c\to C.s
$$

e:

$$
A_1.b\to A_1.a
$$

non impongono precedenze reciproche.

Entrambi devono però essere completati prima di $A.a$.

> [!tip] Verificare un ordine proposto
> Assegna a ogni vertice la sua posizione nell’elenco.
> Per ciascun arco $u\to v$, controlla che la posizione di $u$ sia minore di quella di $v$.

Un ordinamento topologico non è necessariamente unico. Per l’esame basta proporne uno corretto e motivarlo, salvo richieste diverse.

---

## 6. Dipendenze circolari

Consideriamo un diverso esempio:

| Produzione | Regole semantiche |
|---|---|
| $P\to BC$ | $B.i:=C.s$; $C.i:=B.s$ |
| $B\to b$ | $B.s:=B.i+1$ |
| $C\to c$ | $C.s:=C.i+1$ |

Le dipendenze formano il ciclo:

$$
B.i\to B.s\to C.i\to C.s\to B.i
$$

![[Allegati/FLT_07_03/flt_07_03_ciclo.svg|1100]]

Ogni attributo del ciclo attende un altro attributo dello stesso ciclo.

Non esiste quindi un ordinamento topologico.

Nel modello di valutazione diretta studiato nel corso, questi calcoli non possono essere eseguiti rispettando tutte le precedenze.

> [!warning]
> Cambiare l’ordine con cui si leggono le regole non elimina una dipendenza circolare.
> Occorre modificare le dipendenze della definizione.

Un ciclo può coinvolgere attributi di nodi diversi, passando fra padre, figli o fratelli. Non deve essere necessariamente contenuto in una singola regola.

---

## 7. Ordine di valutazione, visita e parsing

Il grafo stabilisce quali precedenze devono essere rispettate.

Una particolare visita dell’albero o un parser possono imporre ulteriori vincoli sull’ordine operativo.

Per esempio:

- una visita dei figli da sinistra verso destra elaborerà $C$ prima di $A_1$;
- un parser bottom-up ottiene i risultati dei sottoalberi secondo le proprie riduzioni;
- un ereditato necessario durante l’elaborazione di un figlio deve essere preparato in tempo.

L’esistenza di un ordinamento topologico dimostra quindi la compatibilità delle dipendenze, mentre l’esecuzione durante il parsing richiede anche di verificare quando i dati sono disponibili.

Per il frammento d’esame, il primo ordine proposto è compatibile con l’elaborazione dei figli $B$, $C$, $A_1$ da sinistra verso destra.

Questo collegamento sarà approfondito nella nota sull’attraversamento dell’albero.

### 7.1 Portata della verifica

Un grafo completo riguarda un particolare albero di derivazione.

Un grafo locale, come quello del frammento d’esame, rappresenta soltanto le dipendenze mostrate o riassunte dalle ipotesi.

Dimostrare che quel grafo è aciclico non verifica automaticamente tutte le altre produzioni o tutti i possibili alberi della DDS.

---

## 8. Procedura da applicare all’esame

1. Distingui tutte le occorrenze dei simboli.
2. Crea un vertice per ogni istanza di attributo interessata.
3. Per ogni regola, traccia un arco da ciascun argomento al risultato.
4. Aggiungi le dipendenze richieste dalle ipotesi della consegna.
5. Identifica i dati ricevuti dal contesto o dai sottoalberi.
6. Cerca un ordinamento topologico.
7. Se non è possibile completarlo, individua un ciclo.
8. Verifica infine la compatibilità con la visita o con il parser richiesto.

Errori da evitare:

- invertire le frecce;
- confondere le istanze $A.a$ e $A_1.a$;
- interpretare più argomenti come alternative;
- dimenticare le dipendenze fra ereditati e sintetizzati dello stesso simbolo;
- considerare disponibili valori che devono ancora essere calcolati;
- scambiare un ordine possibile per l’unico ordine possibile;
- confondere l’assenza di cicli con l’immediata eseguibilità di tutte le azioni durante una riduzione.

## Collegamenti

- [[01 - Definizioni dirette dalla sintassi e regole semantiche]]
- [[02 - Attributi sintetizzati ed ereditati]]
- [[04 - Attraversamento dell albero e valutazione degli attributi]]
- [[05 - Esecuzione delle azioni durante l analisi bottom-up]]
- [[06 - Trasformazione delle produzioni per le azioni semantiche]]
