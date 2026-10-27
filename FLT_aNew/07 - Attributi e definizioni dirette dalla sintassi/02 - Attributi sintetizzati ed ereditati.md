# Attributi sintetizzati ed ereditati

## 1. Il criterio fondamentale di classificazione

Consideriamo una produzione:

$$
A\to X_1X_2\cdots X_n
$$

Nell’albero di derivazione:

- l’occorrenza di $A$ rappresenta il padre;
- le occorrenze $X_1,\ldots,X_n$ rappresentano i figli.

Una regola semantica associata alla produzione può definire:

1. un attributo **sintetizzato della testa** $A$;
2. un attributo **ereditato di un figlio** $X_i$.

Per classificare un attributo, bisogna quindi individuare **dove viene definito il suo valore**.

> [!important] Procedura essenziale
> Guarda l’attributo a sinistra di $:=$.
> Individua poi la posizione della sua occorrenza nella produzione grammaticale:
>
> - se appartiene alla testa, viene definito come sintetizzato;
> - se appartiene a un simbolo del corpo, viene definito come ereditato.

Il criterio riguarda la produzione grammaticale, non la posizione rispetto a $:=$: ogni attributo definito compare comunque a sinistra dell’assegnamento semantico.

---

## 2. Attributi sintetizzati

Un attributo sintetizzato di un nodo viene definito dalle regole della produzione applicata a quel nodo.

Il valore può dipendere dagli attributi dei figli ed eventualmente da altri attributi dello stesso nodo.

### 2.1 Esempio con informazioni provenienti dai figli

Per:

$$
E\to E_1+T
$$

la regola:

$$
E.val:=E_1.val+T.val
$$

definisce un attributo sintetizzato di $E$.

Il nodo padre combina i risultati delle componenti dell’espressione.

### 2.2 Esempio con una costante

Per:

$$
T\to int
$$

la regola:

$$
T.tipo:=\mathrm{integer}
$$

definisce un attributo sintetizzato.

Il valore è una costante, ma viene comunque definito per la testa della produzione.

### 2.3 Dipendenza da un ereditato dello stesso nodo

Supponiamo che $A$ possieda un attributo ereditato $i$.

Nella produzione:

$$
A\to\varepsilon
$$

possiamo avere:

$$
A.s:=f(A.i)
$$

$A.s$ è sintetizzato, anche se dipende da un attributo ereditato.

> [!warning]
> “Sintetizzato” non significa “indipendente dagli attributi ereditati”.
> La classificazione dipende da come viene definita l’istanza di attributo.

Nel caso più comune, i sintetizzati rappresentano risultati restituiti dal sottoalbero: valori numerici, tipi, nodi di AST o codice generato.

---

## 3. Attributi ereditati

Un attributo ereditato di un nodo viene definito mediante una regola associata alla produzione applicata al padre.

Quella produzione contiene il nodo come occorrenza nel proprio corpo.

L’informazione può provenire:

- dal padre;
- dai fratelli;
- eventualmente da altri attributi dello stesso nodo.

### 3.1 Informazione proveniente dal padre

Per:

$$
A\to BC
$$

la regola:

$$
B.contesto:=A.contesto
$$

definisce un attributo ereditato di $B$.

### 3.2 Informazione proveniente da un fratello

Nella stessa produzione:

$$
C.tipo:=B.tipo
$$

definisce un attributo ereditato di $C$.

Il valore viene trasferito da un fratello, ma la regola che lo assegna a $C$ appartiene alla produzione del padre $A$.

### 3.3 Anche una costante può definire un ereditato

Per:

$$
A\to B
$$

la regola:

$$
B.i:=0
$$

definisce un attributo ereditato di $B$.

Il fatto che l’espressione a destra sia una costante non cambia la classificazione.

> [!important] A cosa servono gli ereditati
> Trasmettono a un sottoalbero informazioni sul contesto nel quale deve essere elaborato.

Esempi importanti sono:

- il tipo dichiarato per una lista di identificatori;
- l’ambiente dei nomi visibili in un blocco;
- un valore accumulato durante una traduzione;
- le etichette da utilizzare nella generazione del codice.

Negli esercizi sulle condizioni, per esempio, $E.true$ ed $E.false$ sono ereditati quando vengono assegnati dalla produzione del costrutto che contiene $E$.

---

## 4. Esempio: propagazione del tipo dichiarato

Consideriamo:

$$
\begin{aligned}
D&\to T\,L\\
T&\to int\mid real\\
L&\to id,L_1\mid id
\end{aligned}
$$

Usiamo queste regole per propagare il tipo:

| Produzione | Regola |
|---|---|
| $D\to TL$ | $L.tipo:=T.tipo$ |
| $T\to int$ | $T.tipo:=\mathrm{integer}$ |
| $T\to real$ | $T.tipo:=\mathrm{real}$ |
| $L\to id,L_1$ | $L_1.tipo:=L.tipo$ |

Per la dichiarazione:

$$
int\ x,y
$$

il procedimento è:

1. $T\to int$ calcola $T.tipo=\mathrm{integer}$;
2. la produzione di $D$ assegna questo valore a $L.tipo$;
3. la produzione ricorsiva della lista lo passa a $L_1.tipo$.

Ogni porzione della lista dispone così del tipo da associare ai propri identificatori.

La classificazione è:

| Attributo | Tipo | Motivazione |
|---|---|---|
| $T.tipo$ | Sintetizzato | Definito quando $T$ è testa |
| $L.tipo$ | Ereditato | Assegnato al figlio $L$ nella produzione di $D$ |
| $L_1.tipo$ | Ereditato | Assegnato al figlio $L_1$ nella produzione della lista |

$L$ e $L_1$ sono occorrenze dello stesso non terminale.

> [!warning] Il nome non determina la categoria
> $T.tipo$ è sintetizzato e $L.tipo$ è ereditato, pur avendo entrambi il nome $tipo$.

Inoltre, un valore sintetizzato può essere utilizzato per calcolare un ereditato:

$$
L.tipo:=T.tipo
$$

La categoria dell’attributo sorgente non si trasferisce a quello di destinazione.

---

## 5. Classificazione del frammento d’esame

Negli esami di giugno 2025 e gennaio 2026 troviamo:

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

### 5.1 Attributi definiti esplicitamente

La prima regola definisce $A.a$ per la testa:

$$
A.a\quad\text{è sintetizzato}
$$

La seconda definisce $A_1.b$ per un figlio:

$$
A_1.b\quad\text{è ereditato}
$$

La terza definisce $C.c$ per un figlio:

$$
C.c\quad\text{è ereditato}
$$

### 5.2 Propagazione della classificazione fra occorrenze

$A$ e $A_1$ rappresentano lo stesso non terminale in nodi diversi.

Di conseguenza:

- se l’attributo $a$ di $A$ è sintetizzato, anche $A_1.a$ è sintetizzato;
- se l’attributo $b$ di $A$ è ereditato, anche $A.b$ è ereditato.

Le istanze hanno valori distinti, ma appartengono alla stessa categoria di attributo del simbolo.

### 5.3 Attributi forniti dai sottoalberi

Nella consueta lettura completa delle regole del frammento:

- $B.s$ è un risultato sintetizzato del sottoalbero di $B$;
- $C.s$ è un risultato sintetizzato del sottoalbero di $C$.

Non sono assegnati ai figli dalla produzione riportata: vengono ottenuti elaborando i rispettivi sottoalberi.

Questa conclusione presuppone che non siano state omesse altre regole per la stessa produzione. In un frammento arbitrariamente incompleto, il semplice utilizzo di un attributo non ne dimostrerebbe la categoria.

### 5.4 Tabella finale

| Istanza | Categoria | Motivazione |
|---|---|---|
| $A.a$ | Sintetizzato | Definito per la testa $A$ |
| $A_1.a$ | Sintetizzato | Stesso attributo $a$ del non terminale $A$ |
| $A.b$ | Ereditato | Stesso attributo $b$ assegnato al figlio $A_1$ |
| $A_1.b$ | Ereditato | Definito per un’occorrenza nel corpo |
| $C.c$ | Ereditato | Definito per il figlio $C$ |
| $B.s$ | Sintetizzato | Risultato fornito dal sottoalbero di $B$ |
| $C.s$ | Sintetizzato | Risultato fornito dal sottoalbero di $C$ |

> [!important]
> Se $C.s$ dipende dall’ereditato $C.c$, rimane comunque sintetizzato.
> La dipendenza fra attributi non modifica la loro categoria.

Lo stesso ragionamento vale per $A_1.a$ se dipende da $A_1.b$.

---

## 6. DDS S-attribuite e L-attribuite

### 6.1 Definizioni S-attribuite

Una DDS è **S-attribuita** se utilizza soltanto attributi sintetizzati.

Queste definizioni si prestano alla valutazione dal basso verso l’alto: si elaborano i figli e poi si calcolano i risultati del padre, rispettando le dipendenze.

Sono particolarmente adatte all’analisi bottom-up, perché al momento della riduzione i risultati relativi al corpo possono essere già disponibili.

Il frammento d’esame contiene attributi ereditati, quindi non è un frammento con soli sintetizzati.

### 6.2 Definizioni L-attribuite

Le DDS **L-attribuite** ammettono attributi ereditati con restrizioni che favoriscono l’elaborazione dei figli da sinistra verso destra.

Per:

$$
A\to X_1X_2\cdots X_n
$$

un ereditato di $X_i$ può dipendere:

- dagli attributi ereditati di $A$;
- dagli attributi dei fratelli $X_1,\ldots,X_{i-1}$;
- da attributi dello stesso $X_i$, purché le relative dipendenze non introducano cicli.

Il vincolo fondamentale esclude dipendenze da fratelli a destra per preparare gli ereditati di un figlio.

Ogni DDS S-attribuita è anche L-attribuita: non contiene ereditati che possano violare questi vincoli.

### 6.3 Verifica locale del frammento d’esame

Per:

$$
A_1.b:=g(B.s)
$$

$B$ è a sinistra di $A_1$.

Per:

$$
C.c:=h(B.s,A.b)
$$

$B$ è a sinistra di $C$ e $A.b$ è un ereditato del padre.

Entrambe le regole rispettano quindi i vincoli L-attribuiti.

Questo verifica il frammento. Per classificare l’intera DDS bisogna controllare anche le altre produzioni.

> [!warning]
> La classificazione S-attribuita o L-attribuita riguarda le regole semantiche.
> La classificazione LR(0) o SLR(1) riguarda l’analisi sintattica: sono verifiche distinte.

---

## 7. Categoria dell’attributo e momento di esecuzione

Sapere che un attributo è ereditato non determina automaticamente il punto preciso nel quale calcolarlo.

Bisogna anche controllare quando diventano disponibili i suoi argomenti.

Nel frammento d’esame:

$$
A_1.b:=g(B.s)
$$

richiede prima $B.s$.

Analogamente:

$$
C.c:=h(B.s,A.b)
$$

richiede sia $B.s$ sia l’ereditato $A.b$ del padre.

Una volta disponibili questi dati, gli ereditati possono essere preparati prima di elaborare i sottoalberi che li utilizzano.

Questa osservazione diventa decisiva quando gli attributi sintetizzati dei figli dipendono dai loro ereditati.

In particolare, calcolare tutti gli ereditati soltanto alla riduzione finale della produzione potrebbe essere troppo tardi.

Le note successive approfondiranno:

- l’ordine imposto dalle dipendenze;
- la visita dell’albero;
- l’inserimento delle azioni durante il parsing bottom-up.

---

## 8. Procedura da applicare all’esame

Per ogni regola semantica:

1. individua l’attributo definito, a sinistra di $:=$;
2. identifica la sua occorrenza nella produzione;
3. classificalo come sintetizzato della testa oppure ereditato di un figlio;
4. estendi la classificazione alle altre occorrenze dello stesso attributo del medesimo simbolo;
5. per gli attributi soltanto letti, cerca dove vengono definiti;
6. separa la classificazione dal problema dell’ordine di valutazione.

Una risposta adatta alla domanda «Come si riconosce un attributo ereditato e a cosa serve?» è:

> Un attributo è ereditato quando il valore di una sua istanza viene definito mediante una regola della produzione applicata al padre, nella quale il simbolo interessato compare nel corpo. Serve a trasmettere al sottoalbero informazioni sul contesto, come tipi, ambienti ed etichette, utilizzabili durante la sua elaborazione.

Gli errori principali da evitare sono:

- classificare un attributo soltanto dal nome;
- confondere la testa della produzione con la sinistra dell’assegnamento;
- considerare $A$ e $A_1$ non terminali diversi;
- ritenere ereditato un risultato solo perché dipende da un ereditato;
- supporre che ogni ereditato provenga esclusivamente dal padre;
- confondere il tipo dell’attributo con il momento in cui viene calcolato.

## Collegamenti

- [[01 - Definizioni dirette dalla sintassi e regole semantiche]]
- [[03 - Grafi delle dipendenze e ordine di valutazione]]
- [[04 - Attraversamento dell albero e valutazione degli attributi]]
- [[05 - Esecuzione delle azioni durante l analisi bottom-up]]
- [[06 - Trasformazione delle produzioni per le azioni semantiche]]