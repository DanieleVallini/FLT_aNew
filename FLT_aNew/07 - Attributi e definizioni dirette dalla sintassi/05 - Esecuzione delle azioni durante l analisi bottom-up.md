# Esecuzione delle azioni durante l’analisi bottom-up

## 1. Unire riconoscimento e valutazione

Un parser bottom-up costruisce implicitamente l’albero di derivazione mediante spostamenti e riduzioni.

Durante questo processo possiamo anche eseguire azioni semantiche:

- calcolare valori;
- costruire nodi di un AST;
- produrre frammenti di codice;
- conservare informazioni necessarie ai calcoli successivi.

Per farlo, associamo ai simboli presenti sulla pila anche i loro attributi.

> [!important] Criterio di eseguibilità
> Un’azione può essere eseguita in un certo momento del parsing soltanto se tutti i valori che legge sono disponibili in quel momento.

L’esistenza di un ordine di valutazione corretto sull’albero non garantisce automaticamente che lo stesso ordine sia realizzabile usando soltanto i dati già presenti sulla pila LR.

---

## 2. Pila sintattica e pila semantica

Nelle note sul parsing abbiamo rappresentato la pila alternando simboli e stati:

$$
s_0\,X_1\,s_1\cdots X_k\,s_k
$$

A ogni occorrenza di simbolo $X_i$ possiamo associare un record contenente i suoi attributi.

Concettualmente distinguiamo:

- le informazioni sintattiche, necessarie al riconoscimento;
- le informazioni semantiche, necessarie alla traduzione.

Queste informazioni possono essere memorizzate insieme oppure in strutture parallele.

Per ragionare sui valori, useremo una **pila semantica logica con un record per simbolo**, omettendo temporaneamente gli stati.

Per esempio:

$$
E(3)\quad+\quad T(20)
$$

indica che le occorrenze di $E$ e $T$ hanno rispettivamente:

$$
E.val=3
\qquad
T.val=20
$$

Anche il simbolo `+` occupa una posizione, pur non avendo necessariamente un attributo numerico.

> [!warning]
> Le distanze nella pila logica contano tutti i simboli della grammatica, compresi operatori e separatori.
> Non si contano soltanto i simboli che possiedono un attributo interessante.

La cima della pila resta a destra.

---

## 3. Azioni durante spostamento e riduzione

### 3.1 Spostamento

Quando viene spostato un token, vengono conservate anche le informazioni fornite dal lexer.

Per esempio, spostando il token numerico corrispondente a `5`, possiamo memorizzare:

$$
num.lexval=5
$$

Non vengono ancora calcolati tutti gli attributi dei costrutti che conterranno quel token.

### 3.2 Riduzione

Supponiamo che il parser prescriva:

$$
A\to X_1X_2\cdots X_m
$$

Il corpo riconosciuto occupa la parte finale della pila.

Per calcolare un sintetizzato di $A$ possiamo:

1. recuperare gli attributi necessari dei simboli del corpo;
2. eseguire il calcolo;
3. rimuovere i record del corpo;
4. inserire il record di $A$ con il risultato;
5. aggiornare lo stato mediante GOTO.

Nella pila semantica logica si rimuovono $m$ record.

Nella rappresentazione LR alternata si rimuovono $m$ coppie simbolo-stato, cioè $2m$ elementi.

> [!important]
> I dati necessari devono essere letti o conservati prima di eliminare i record che li contengono.

La riduzione non consuma input: l’azione semantica accompagna la riduzione sintattica.

---

## 4. Perché i sintetizzati si prestano al bottom-up

Consideriamo:

$$
E\to E_1+T
$$

con:

$$
E.val:=E_1.val+T.val
$$

Quando il parser riduce questa produzione, ha già riconosciuto $E_1$, `+` e $T$.

I valori dei due operandi possono quindi essere letti dai rispettivi record.

Se $top$ indica la posizione del record più a destra:

| Posizione | Simbolo | Valore utile |
|---|---|---|
| $top-2$ | $E_1$ | $E_1.val$ |
| $top-1$ | `+` | Nessun valore numerico necessario |
| $top$ | $T$ | $T.val$ |

Un’implementazione astratta è:

```text
risultato := pila[top - 2].val + pila[top].val

rimuovi 3 record
inserisci il record di E con E.val := risultato
```

Le operazioni sugli stati LR vengono eseguite secondo il normale algoritmo di riduzione e GOTO.

Una DDS con soli attributi sintetizzati è detta **S-attribuita**. Questa organizzazione rende naturale calcolare i risultati della testa durante le riduzioni.

---

## 5. Esempio: le riduzioni finali di 3 + 4 * 5

Consideriamo la grammatica:

$$
\begin{aligned}
E&\to E+T\mid T\\
T&\to T*F\mid F\\
F&\to(E)\mid num
\end{aligned}
$$

con le consuete regole per il valore numerico.

Mostriamo soltanto le due riduzioni semantiche finali.

### 5.1 Riduzione del prodotto

La pila logica contiene:

$$
E(3)\quad+\quad T(4)\quad*\quad F(5)
$$

Per:

$$
T\to T_1*F
$$

calcoliamo:

$$
T.val:=4\cdot5=20
$$

Rimuoviamo i tre record:

$$
T(4),\quad *,\quad F(5)
$$

e inseriamo il risultato:

$$
E(3)\quad+\quad T(20)
$$

### 5.2 Riduzione della somma

Per:

$$
E\to E_1+T
$$

calcoliamo:

$$
E.val:=3+20=23
$$

La pila logica diventa:

$$
E(23)
$$

> [!warning]
> È la tabella del parser a stabilire quando ridurre.
> Le regole semantiche calcolano i risultati delle riduzioni prescritte; non autorizzano a scegliere arbitrariamente quale produzione applicare.

In entrambi i casi il risultato sostituisce i valori del corpo riconosciuto.

---

## 6. La difficoltà degli attributi ereditati

Un attributo ereditato può essere necessario prima che il sottoalbero del simbolo sia stato riconosciuto completamente.

Consideriamo:

$$
D\to TL
$$

con:

$$
L.tipo:=T.tipo
$$

Il risultato $T.tipo$ è disponibile dopo il riconoscimento di $T$.

Se il tipo serve durante l’elaborazione della lista $L$, deve essere conservato e reso accessibile alle azioni che elaborano quella lista.

Aspettare la riduzione finale di $D\to TL$ potrebbe essere troppo tardi.

Inoltre, il record definitivo del simbolo $L$ viene normalmente inserito sulla pila quando $L$ viene ridotto: prima di quel momento non possiamo presumere che esista già un record completo del futuro $L$.

> [!important]
> Bisogna distinguere la disponibilità del valore da passare dalla disponibilità del record del simbolo destinatario.
> Il valore può essere pronto prima che il destinatario sia stato riconosciuto.

Servono quindi posizioni di memoria o azioni intermedie che conservino il contesto.

---

## 7. Azioni intermedie e marcatori

Un’azione può dover essere eseguita dopo un prefisso del corpo, prima che sia riconosciuta l’intera produzione.

Un meccanismo tipico introduce un non terminale marcatore:

$$
D\to TML
$$

$$
M\to\varepsilon
$$

Alla riduzione di $M\to\varepsilon$ viene eseguita un’azione:

- $T$ è già stato riconosciuto;
- $L$ deve ancora essere elaborato;
- l’azione può leggere informazioni del prefisso già presente sulla pila;
- il record di $M$ può conservare dati utili alle operazioni successive.

La produzione vuota non consuma input e non rimuove simboli del corpo.

> [!warning] Un marcatore non crea dati mancanti
> Può conservare o rielaborare valori già disponibili.
> Se un argomento necessario non è stato calcolato o reso accessibile, inserire un marcatore non risolve da solo il problema.

L’accesso al prefisso della pila deve essere specificato nell’implementazione: non basta scrivere il nome di un attributo esterno alla produzione del marcatore.

La prossima nota approfondirà la trasformazione delle produzioni e le verifiche richieste dopo l’inserimento dei marcatori.

---

## 8. Analisi del frammento d’esame

Riprendiamo:

$$
A\to BCA_1
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

### 8.1 Il sintetizzato della testa

Alla riduzione finale di:

$$
A\to BCA_1
$$

i simboli del corpo sono stati riconosciuti.

I loro record possono contenere:

$$
C.s
\qquad\text{e}\qquad
A_1.a
$$

La regola:

$$
A.a:=f(C.s,A_1.a)
$$

è quindi collocabile alla riduzione finale, assumendo correttamente valutati i sottoalberi.

### 8.2 L’ereditato di A1

La regola:

$$
A_1.b:=g(B.s)
$$

richiede il sintetizzato di un fratello a sinistra.

Il valore $g(B.s)$ può essere calcolato dopo il riconoscimento di $B$.

Per utilizzarlo durante l’elaborazione di $A_1$, occorre però conservarlo e renderlo accessibile prima che quel sottoalbero venga completato.

### 8.3 Il punto critico: A.b

La regola:

$$
C.c:=h(B.s,A.b)
$$

richiede due valori diversi:

- $B.s$, risultato di un simbolo già riconosciuto a sinistra;
- $A.b$, ereditato dell’occorrenza del padre.

$A.b$ non è automaticamente contenuto nei record di $B$, $C$ o $A_1$.

Inoltre, il record finale di $A$ non è ancora stato inserito tramite la riduzione della produzione.

Per eseguire questa azione prima di $C$, serve quindi un meccanismo esplicito che renda disponibile $A.b$, per esempio un record di contesto predisposto precedentemente.

> [!important] Applicabilità del metodo locale
> Nel metodo che ammette, per le azioni anticipate, soltanto valori dei simboli già riconosciuti a sinistra, questa regola non soddisfa il requisito: utilizza l’ereditato del padre $A.b$.
> Un marcatore inserito dopo $B$ non può recuperarlo senza una gestione esplicita del contesto.

Questo non dimostra l’impossibilità di qualsiasi implementazione bottom-up più generale. Individua il limite del metodo locale basato sui dati già presenti nel prefisso riconosciuto.

### 8.4 Confronto con la visita dell’albero

Nella visita dell’albero della nota precedente, $A.b$ era assunto disponibile all’ingresso nella procedura che elaborava $A$.

Durante il parsing LR, quella disponibilità deve essere realizzata concretamente.

Perciò la sola affermazione «il frammento rispetta i vincoli L-attribuiti» non basta a giustificare un’implementazione locale sulla pila.

---

## 9. Preservare le diverse occorrenze

La produzione contiene un’altra occorrenza dello stesso non terminale:

$$
A_1
$$

Il suo ereditato:

$$
A_1.b
$$

è distinto da:

$$
A.b
$$

Una gestione del contesto deve conservare correttamente questi valori per le rispettive occorrenze.

Se vengono utilizzate strutture esterne alla pila sintattica, devono rispettare anche l’annidamento delle elaborazioni. Una singola variabile condivisa non distingue automaticamente contesti ricorsivi diversi.

L’implementazione deve mantenere l’associazione:

$$
\text{occorrenza del simbolo}
\longleftrightarrow
\text{suoi valori di attributo}
$$

---

## 10. Procedura da applicare all’esame

Per ogni azione:

1. individua gli attributi letti e quello definito;
2. stabilisci quando il risultato deve essere disponibile;
3. individua il prefisso già riconosciuto in quel momento;
4. verifica quali valori sono presenti nei suoi record;
5. controlla gli eventuali dati provenienti dal contesto del padre;
6. scegli dove conservare i risultati destinati a simboli non ancora riconosciuti;
7. verifica se basta la riduzione finale oppure serve un’azione intermedia;
8. dopo una trasformazione, controlla anche il funzionamento del parser.

Gli errori principali sono:

- leggere attributi dopo aver eliminato i record che li contenevano;
- sbagliare gli offset ignorando operatori e separatori;
- aspettare la riduzione finale per un ereditato necessario prima;
- presumere che il record del padre sia già presente;
- introdurre marcatori senza verificarne gli argomenti;
- dedurre l’eseguibilità sulla pila dalla sola esistenza di un ordine topologico.

## Collegamenti

- [[02 - Attributi sintetizzati ed ereditati]]
- [[03 - Grafi delle dipendenze e ordine di valutazione]]
- [[04 - Attraversamento dell albero e valutazione degli attributi]]
- [[06 - Trasformazione delle produzioni per le azioni semantiche]]