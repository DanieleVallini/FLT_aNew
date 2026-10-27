# Definizioni dirette dalla sintassi e regole semantiche

## 1. Dalla struttura sintattica alle informazioni semantiche

L’analisi sintattica riconosce la struttura di una frase e individua le produzioni utilizzate per costruirla.

Un traduttore deve anche associare informazioni a quella struttura. Per esempio, può dover:

- calcolare il valore di un’espressione;
- determinare il tipo di una variabile;
- costruire un albero di sintassi astratta;
- produrre codice intermedio ed etichette.

Una **definizione diretta dalla sintassi**, abbreviata **DDS** o **SDD**, collega questi calcoli alle produzioni della grammatica.

> [!important]
> La grammatica descrive la struttura delle frasi.
> Gli attributi rappresentano le informazioni associate a quella struttura e le regole semantiche ne definiscono il calcolo.

---

## 2. Definizione di DDS

Una definizione diretta dalla sintassi è costituita da:

1. una grammatica context-free;
2. attributi associati ai simboli della grammatica;
3. regole semantiche associate alle produzioni.

Un attributo ha un nome, un dominio di valori e un significato.

Per esempio:

| Attributo | Possibile significato |
|---|---|
| $E.val$ | Valore numerico di un’espressione |
| $E.type$ | Tipo di un’espressione |
| $E.node$ | Riferimento al nodo radice di un AST |
| $S.code$ | Sequenza di istruzioni generata |
| $E.true$ | Etichetta da raggiungere se una condizione è vera |

Il nome dell’attributo viene scritto dopo il simbolo:

$$
X.nome
$$

Il valore concreto appartiene a una particolare occorrenza di $X$ nell’albero di derivazione.

---

## 3. Simboli, occorrenze e istanze di attributo

Consideriamo la produzione:

$$
E\to E_1+T
$$

$E$ ed $E_1$ rappresentano due occorrenze dello stesso non terminale.

Il pedice distingue:

- $E$, che è la testa della produzione e rappresenta il padre;
- $E_1$, che compare nel corpo e rappresenta un figlio.

Non viene introdotto un nuovo non terminale chiamato $E_1$.

Se associamo l’attributo $val$ a $E$, nell’albero ogni nodo etichettato $E$ possiede la propria **istanza** di quell’attributo.

Per esempio:

$$
E_1.val=3
$$

e:

$$
E.val=23
$$

possono essere contemporaneamente corretti: appartengono a nodi diversi.

> [!warning] Nessuna variabile globale condivisa
> La notazione $E.val$ va interpretata rispetto all’occorrenza considerata.
> Due nodi con lo stesso simbolo grammaticale possono avere valori diversi dello stesso attributo.

La stessa attenzione sarà necessaria per distinguere $A.a$ e $A_1.a$ negli esercizi d’esame.

---

## 4. Regole semantiche e dipendenze

Una regola semantica di calcolo ha tipicamente la forma:

$$
X.a:=f(Y_1.b_1,\ldots,Y_k.b_k)
$$

La regola specifica:

- quale istanza di attributo viene definita: $X.a$;
- quali valori servono per calcolarla;
- quale funzione applicare a quei valori.

Per esempio:

$$
E.val:=E_1.val+T.val
$$

definisce il valore dell’espressione completa mediante i valori delle due componenti.

Prima di eseguire il calcolo devono essere disponibili:

$$
E_1.val
\qquad\text{e}\qquad
T.val
$$

Questa necessità determina le **dipendenze** fra attributi.

### 4.1 Regole senza valori di ingresso

Una regola può assegnare una costante:

$$
T.type:=\mathrm{integer}
$$

Anche una produzione vuota può avere regole semantiche:

$$
N\to\varepsilon
\qquad
N.val:=0
$$

La produzione non genera terminali, ma l’occorrenza di $N$ può comunque possedere attributi.

### 4.2 Ordine di scrittura e ordine di valutazione

Una DDS specifica i calcoli e le loro dipendenze.

L’elenco delle regole non impone automaticamente di eseguirle nell’ordine in cui sono stampate.

> [!important] Regola di disponibilità
> Un attributo può essere calcolato quando sono disponibili tutti i valori dai quali dipende.
> Regole indipendenti possono ammettere più ordini di valutazione corretti.

Studieremo gli ordini di valutazione mediante i grafi delle dipendenze.

---

## 5. Attributi sintetizzati ed ereditati: prima distinzione

Gli attributi si distinguono principalmente in due categorie.

Un attributo **sintetizzato** di un nodo viene definito mediante la produzione applicata a quel nodo, utilizzando attributi dei figli ed eventualmente altri attributi del nodo stesso.

Un attributo **ereditato** di un figlio viene definito nel contesto della produzione del padre, utilizzando informazioni del padre, dei fratelli ed eventualmente del figlio stesso.

Per una produzione:

$$
A\to BC
$$

due esempi sono:

$$
A.val:=B.val+C.val
$$

$$
C.tipo:=B.tipo
$$

Nel primo caso si definisce un attributo sintetizzato della testa $A$.

Nel secondo si definisce un attributo ereditato del figlio $C$, trasferendogli un’informazione dal fratello $B$.

I terminali possono inoltre avere attributi forniti dall’analizzatore lessicale. Per un token numerico, per esempio:

$$
num.lexval
$$

contiene il valore del numero riconosciuto.

La classificazione dettagliata degli attributi sarà sviluppata nella prossima nota.

---

## 6. Esempio completo: valutazione di un’espressione

Consideriamo questa DDS:

| Produzione | Regola semantica |
|---|---|
| $E\to E_1+T$ | $E.val:=E_1.val+T.val$ |
| $E\to T$ | $E.val:=T.val$ |
| $T\to T_1*F$ | $T.val:=T_1.val\cdot F.val$ |
| $T\to F$ | $T.val:=F.val$ |
| $F\to(E)$ | $F.val:=E.val$ |
| $F\to num$ | $F.val:=num.lexval$ |

Gli attributi $val$ di $E$, $T$ e $F$ sono sintetizzati.

Il valore $num.lexval$ viene fornito dal lexer per ciascuna occorrenza di token numerico.

### 6.1 Calcolo di 3 + 4 * 5

La grammatica distingue somme, prodotti e fattori. L’espressione viene strutturata come:

$$
3+(4*5)
$$

Per i tre token numerici il lexer fornisce rispettivamente i valori:

$$
3,\quad 4,\quad 5
$$

Applicando $F\to num$, i fattori ricevono gli stessi valori.

Per il prodotto si applica:

$$
T\to T_1*F
$$

con:

$$
T_1.val=4
\qquad
F.val=5
$$

quindi:

$$
T.val=4\cdot5=20
$$

Per la somma si applica:

$$
E\to E_1+T
$$

con:

$$
E_1.val=3
\qquad
T.val=20
$$

ottenendo:

$$
\boxed{E.val=23}
$$

Le produzioni $E\to T$ e $T\to F$ copiano i valori lungo i passaggi intermedi.

### 6.2 Albero di derivazione annotato

Un **albero annotato** è un albero di derivazione nel quale sono riportati i valori degli attributi delle singole occorrenze.

![[Allegati/FLT_07_01/flt_07_01_albero_annotato.svg|1200]]

Nell’immagine:

- ogni nodo non terminale mostra il proprio $val$;
- i token numerici mostrano il valore lessicale $lexval$;
- il valore della radice è il risultato dell’intera espressione.

Le linee rappresentano i legami padre-figlio dell’albero di derivazione. Le dipendenze fra attributi sono una relazione distinta, che studieremo con un apposito grafo.

---

## 7. Lettura del frammento presente negli esami

Negli esami di giugno 2025 e gennaio 2026 compare:

$$
A\to B\,C\,A_1
$$

con le regole:

$$
A.a:=f(C.s,A_1.a)
$$

$$
A_1.b:=g(B.s)
$$

$$
C.c:=h(B.s,A.b)
$$

La prima operazione da fare è distinguere gli attributi definiti da quelli utilizzati.

| Regola | Attributo definito | Valori necessari |
|---|---|---|
| $A.a:=f(C.s,A_1.a)$ | $A.a$ | $C.s$, $A_1.a$ |
| $A_1.b:=g(B.s)$ | $A_1.b$ | $B.s$ |
| $C.c:=h(B.s,A.b)$ | $C.c$ | $B.s$, $A.b$ |

$A$ e $A_1$ sono occorrenze diverse dello stesso non terminale.

Di conseguenza:

$$
A.a
\qquad\text{e}\qquad
A_1.a
$$

sono istanze distinte dello stesso attributo.

Lo stesso vale per:

$$
A.b
\qquad\text{e}\qquad
A_1.b
$$

### 7.1 Disponibilità dei valori

Se sono disponibili $B.s$ e $A.b$, possiamo calcolare:

$$
A_1.b=g(B.s)
$$

e:

$$
C.c=h(B.s,A.b)
$$

Per calcolare $A.a$ devono invece essere disponibili anche:

$$
C.s
\qquad\text{e}\qquad
A_1.a
$$

Il frammento non specifica da solo tutti i calcoli interni ai sottoalberi di $B$, $C$ e $A_1$.

Nella consegna, le informazioni aggiuntive sulle dipendenze degli attributi sintetizzati servono proprio a completare il ragionamento sull’ordine di valutazione.

> [!warning]
> La prima regola scritta non è necessariamente la prima eseguibile.
> In particolare, $A.a$ deve attendere i valori utilizzati da $f$.

Le funzioni $f$, $g$ e $h$ sono astratte: l’esercizio richiede di ragionare sulle dipendenze e sulla disponibilità degli argomenti, senza inventare risultati numerici.

---

## 8. DDS e schema di traduzione

Una DDS associa regole semantiche alle produzioni e specifica quali valori devono essere calcolati.

Uno **schema di traduzione** inserisce azioni semantiche in posizioni precise dei corpi delle produzioni.

Per esempio:

$$
E\to E_1+T\
\{E.val:=E_1.val+T.val\}
$$

Il frammento fra graffe rappresenta un’azione del traduttore.

La sua posizione indica quando eseguirla durante il riconoscimento.

Un’implementazione corretta deve collocare l’azione in un punto nel quale i valori necessari siano disponibili.

Questa distinzione è fondamentale quando l’esame chiede di trasformare una DDS per eseguire le azioni durante l’analisi bottom-up.

> [!important]
> Una dipendenza semanticamente corretta può richiedere un momento di esecuzione diverso da quello immediatamente disponibile nel parser.
> Bisogna verificare sia quali valori servono sia quando vengono ottenuti.

---

## 9. Metodo di lettura da applicare all’esame

Per ogni produzione con regole semantiche:

1. identifica la testa e le singole occorrenze nel corpo;
2. elenca gli attributi definiti da ciascuna regola;
3. elenca gli attributi letti per eseguire quei calcoli;
4. distingui le istanze associate a occorrenze diverse;
5. individua i valori iniziali o forniti dal contesto;
6. ricava le dipendenze prima di scegliere un ordine;
7. verifica quando i dati risultano disponibili durante il riconoscimento.

Gli errori più frequenti sono:

- confondere un simbolo con una sua particolare occorrenza;
- trattare $A.a$ e $A_1.a$ come la stessa variabile;
- eseguire le regole nell’ordine stampato senza controllare gli argomenti;
- dedurre il tipo di un attributo soltanto dal nome;
- supporre che ogni regola possa essere eseguita immediatamente alla riduzione.

## Collegamenti

- [[02 - Attributi sintetizzati ed ereditati]]
- [[03 - Grafi delle dipendenze e ordine di valutazione]]
- [[04 - Attraversamento dell albero e valutazione degli attributi]]
- [[05 - Esecuzione delle azioni durante l analisi bottom-up]]
- [[06 - Trasformazione delle produzioni per le azioni semantiche]]
