# Grammatiche regolari e lineari destre

## 1. Definizione

Una grammatica:

$$
G=(N,\Sigma,P,S)
$$

è **lineare destra** se ogni produzione ha la forma:

$$
A\to uB
$$

oppure:

$$
A\to u
$$

dove:

$$
A,B\in N,\qquad u\in\Sigma^*
$$

Quindi:

- a sinistra compare un solo non terminale;
- a destra compare al massimo un non terminale;
- se presente, il non terminale si trova all’estremità destra.

Le grammatiche lineari destre generano esattamente i linguaggi regolari.

> [!important]
> “Al massimo un non terminale” non basta.
>
> Conta anche la sua posizione: nella forma lineare destra deve trovarsi dopo tutti i terminali della produzione.

## 2. Forma base delle produzioni

Una forma particolarmente utile, soprattutto per il collegamento con gli automi, utilizza soltanto:

$$
A\to aB
$$

$$
A\to a
$$

$$
A\to\varepsilon
$$

con:

$$
A,B\in N,\qquad a\in\Sigma
$$

Ogni produzione:

- emette un terminale e continua con un non terminale;
- oppure emette un terminale e termina;
- oppure termina senza emettere simboli.

### Forma estesa

La definizione iniziale ammette anche intere stringhe di terminali:

$$
A\to abcB
$$

Questa produzione può essere trasformata nella forma base introducendo non terminali nuovi:

$$
A\to aC
$$

$$
C\to bD
$$

$$
D\to cB
$$

Anche:

$$
A\to B
$$

è ammessa nella forma estesa, scegliendo $u=\varepsilon$.

Questa regola cambia non terminale senza generare terminali.

> [!note]
> Forma base e forma estesa hanno lo stesso potere espressivo: generano la stessa classe di linguaggi.

## 3. Riconoscere le produzioni lineari destre

Supponiamo che le lettere maiuscole siano non terminali e quelle minuscole terminali.

| Produzione | Lineare destra? | Motivo |
|---|---|---|
| $S\to aA$ | Sì | Un non terminale all’estremità destra |
| $A\to b$ | Sì | Soltanto terminali |
| $A\to\varepsilon$ | Sì | Stringa vuota |
| $S\to abA$ | Sì, in forma estesa | Stringa di terminali seguita da un non terminale |
| $S\to A$ | Sì, in forma estesa | Nessun terminale prima del non terminale |
| $S\to Aa$ | No | Il non terminale si trova a sinistra |
| $S\to aAb$ | No | Il non terminale si trova in posizione interna |
| $S\to AB$ | No | Due non terminali nella parte destra |
| $AB\to a$ | No | Due non terminali nella parte sinistra |

La classificazione riguarda qui la **forma delle produzioni**, non direttamente il linguaggio generato.

## 4. Come si sviluppano le derivazioni

In una grammatica lineare destra, ogni forma sentenziale raggiungibile dal simbolo iniziale ha una delle forme:

$$
wA
$$

oppure:

$$
w
$$

con:

$$
w\in\Sigma^*,\qquad A\in N
$$

Nel primo caso la generazione deve ancora terminare; nel secondo è stata ottenuta una stringa di soli terminali.

### Perché il non terminale resta a destra

All’inizio abbiamo:

$$
S=\varepsilon S
$$

Se la forma corrente è $wA$ e applichiamo:

$$
A\to aB
$$

otteniamo:

$$
wA\Rightarrow waB
$$

Il nuovo non terminale è ancora all’estremità destra.

Se invece applichiamo:

$$
A\to a
$$

otteniamo la frase:

$$
wa
$$

Con:

$$
A\to\varepsilon
$$

otteniamo:

$$
w
$$

> [!important]
> Durante una derivazione lineare destra c’è al massimo un non terminale da sviluppare.
>
> Possono comunque esserci più produzioni alternative applicabili a quel non terminale.

## 5. Esempio: tutte le a prima delle b

Vogliamo generare:

$$
L=\{a^n b^m\mid n,m\geq 0\}
$$

L’espressione regolare corrispondente è:

$$
a^*b^*
$$

Una grammatica lineare destra è:

$$
G=(\{S,B\},\{a,b\},P,S)
$$

con:

$$
S\to aS\mid bB\mid\varepsilon
$$

$$
B\to bB\mid\varepsilon
$$

### Significato dei non terminali

- $S$: possiamo ancora generare `a`, iniziare le `b` oppure terminare.
- $B$: abbiamo iniziato la fase delle `b` e possiamo generare soltanto altre `b` oppure terminare.

### Derivazione di aab

$$
S\Rightarrow aS
\Rightarrow aaS
\Rightarrow aabB
\Rightarrow aab
$$

### Casi limite

La stringa vuota è generata da:

$$
S\Rightarrow\varepsilon
$$

Una stringa di sole `a` è generata, per esempio, da:

$$
S\Rightarrow aS\Rightarrow aaS\Rightarrow aa
$$

Una stringa di sole `b` è generata, per esempio, da:

$$
S\Rightarrow bB\Rightarrow bbB\Rightarrow bb
$$

La stringa `ba` non può essere generata: dopo aver prodotto la prima `b`, il non terminale $B$ non permette più di produrre `a`.

> [!tip]
> Separare i non terminali in “fasi” è un metodo utile per imporre l’ordine dei blocchi.

## 6. Esempio: stringhe che terminano con ab

Consideriamo:

$$
L=\{w\,ab\mid w\in\{a,b\}^*\}
$$

Un’espressione regolare è:

$$
(a\mid b)^*ab
$$

Una grammatica lineare destra equivalente è:

$$
S\to aS\mid bS\mid aA
$$

$$
A\to b
$$

Le produzioni:

$$
S\to aS\mid bS
$$

generano un prefisso arbitrario.

La produzione:

$$
S\to aA
$$

inizia il suffisso obbligatorio `ab`, completato da:

$$
A\to b
$$

Per esempio:

$$
S\Rightarrow bS
\Rightarrow baS
\Rightarrow baaA
\Rightarrow baab
$$

### Scelte con lo stesso terminale iniziale

Da $S$ possiamo scegliere sia:

$$
S\to aS
$$

sia:

$$
S\to aA
$$

Entrambe iniziano con `a`, ma svolgono ruoli diversi.

Questo non impedisce alla grammatica di essere regolare.

> [!warning]
> Una grammatica regolare non deve necessariamente offrire una sola produzione per ogni terminale iniziale.
>
> Il linguaggio generato resta riconoscibile da un automa finito deterministico equivalente.

## 7. Grammatiche lineari sinistre

Una grammatica è **lineare sinistra** se le produzioni hanno la forma:

$$
A\to Bu
$$

oppure:

$$
A\to u
$$

con:

$$
u\in\Sigma^*
$$

Il non terminale, quando presente a destra, si trova all’estremità sinistra.

Anche queste grammatiche generano esattamente i linguaggi regolari.

### Esempio

$$
S\to Sa\mid b
$$

genera:

$$
L(G)=\{ba^n\mid n\geq 0\}
$$

Infatti:

$$
S\Rightarrow Sa
\Rightarrow Saa
\Rightarrow baa
$$

Attenzione: non genera $a^*b$.

Una grammatica lineare destra per lo stesso linguaggio è:

$$
S\to bA
$$

$$
A\to aA\mid\varepsilon
$$

> [!important]
> Grammatiche lineari destre e sinistre descrivono la stessa classe di linguaggi.
>
> Questo non significa che basti spostare il non terminale da un’estremità all’altra di ogni produzione per conservare il linguaggio.

## 8. Mescolare i due orientamenti

Consideriamo:

$$
S\to aA\mid\varepsilon
$$

$$
A\to Sb
$$

La produzione $S\to aA$ è lineare destra.

La produzione $A\to Sb$ è lineare sinistra.

Insieme permettono:

$$
S\Rightarrow aA
\Rightarrow aSb
\Rightarrow aaAb
\Rightarrow aaSbb
\Rightarrow aabb
$$

Il linguaggio generato è:

$$
L(G)=\{a^n b^n\mid n\geq 0\}
$$

che non è regolare.

Quindi la possibilità di avere un solo non terminale a destra di ogni produzione, senza imporre un orientamento uniforme, non garantisce la regolarità.

## 9. Collegamento con gli automi finiti

Il legame fondamentale è:

$$
\text{linguaggio generato da una grammatica regolare}
$$

$$
\iff
$$

$$
\text{linguaggio riconosciuto da un automa finito}
$$

Nella conversione usuale da grammatica lineare destra ad automa, i non terminali vengono associati agli stati.

| Produzione | Interpretazione nell’automa |
|---|---|
| $A\to aB$ | Transizione da $A$ a $B$ leggendo `a` |
| $A\to a$ | Transizione su `a` verso un nuovo stato finale |
| $A\to\varepsilon$ | Lo stato associato ad $A$ è finale |
| $A\to B$ | Epsilon-transizione da $A$ a $B$ |

Il simbolo iniziale della grammatica corrisponde allo stato iniziale.

> [!warning]
> In questa costruzione:
>
> - $A\to\varepsilon$ consente di terminare la generazione;
> - $A\to B$ cambia non terminale senza emettere simboli.
>
> Sono regole diverse e hanno ruoli diversi.

Le costruzioni complete tra grammatiche, espressioni e automi saranno approfondite nelle note dedicate.

## 10. Come affrontare la richiesta d’esame

Se viene chiesto:

> Dare una grammatica lineare destra per un linguaggio.

Un procedimento utile è:

1. Descrivere con precisione il linguaggio.
2. Individuare eventuali fasi, prefissi o suffissi obbligatori.
3. Assegnare un non terminale a ciascuna situazione necessaria.
4. Scrivere produzioni che generino i terminali nell’ordine corretto.
5. Consentire la terminazione soltanto dove il linguaggio lo permette.
6. Verificare stringhe brevi ammesse e stringhe escluse.
7. Controllare la forma lineare destra di tutte le produzioni.

Presenta chiaramente:

- non terminali;
- terminali;
- simbolo iniziale;
- produzioni.

Se la richiesta è:

> Stabilire se il linguaggio generato è regolare e, se lo è, fornire un’espressione regolare e una grammatica lineare destra.

devi prima analizzare il linguaggio. Non ogni grammatica context-free può essere sostituita da una grammatica regolare equivalente.

## 11. Controllo rapido

### Domanda 1

La produzione:

$$
S\to abA
$$

è lineare destra?

**Sì**, nella forma estesa. Può essere scomposta introducendo un nuovo non terminale.

### Domanda 2

La produzione:

$$
S\to aAb
$$

è lineare destra?

**No:** il non terminale non è all’estremità destra.

### Domanda 3

Una grammatica lineare destra può generare un linguaggio infinito?

**Sì.** Per esempio:

$$
S\to aS\mid\varepsilon
$$

genera $a^*$.

### Domanda 4

Una grammatica context-free non lineare destra può generare un linguaggio regolare?

**Sì.** La forma della grammatica fornita non determina da sola la classificazione più precisa del linguaggio.

## 12. Errori da evitare

- Controllare soltanto il numero dei non terminali senza verificarne la posizione.
- Confondere “lineare” con “lineare destra”.
- Mescolare liberamente produzioni lineari destre e sinistre.
- Inserire una produzione con $\varepsilon$ quando non è ancora possibile terminare.
- Dimenticare casi limite come la stringa vuota o un blocco di lunghezza zero.
- Consentire il ritorno a una fase precedente quando l’ordine dei simboli lo vieta.
- Pensare che più produzioni con lo stesso terminale iniziale rendano il linguaggio non regolare.
- Tentare di costruire una grammatica regolare per un linguaggio che non è regolare.