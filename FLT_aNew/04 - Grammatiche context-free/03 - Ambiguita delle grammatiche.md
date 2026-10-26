# Ambiguità delle grammatiche

## 1. Definizione

Una grammatica context-free $G$ è **ambigua** se esiste almeno una frase:

$$
w\in L(G)
$$

che ammette due alberi di derivazione distinti.

È invece **non ambigua** se ogni frase del linguaggio ammette esattamente un albero di derivazione.

> [!important]
> Per dimostrare l’ambiguità basta una sola frase con due strutture diverse.
>
> Per dimostrare la non ambiguità bisogna giustificare l’unicità della struttura per tutte le frasi.

Gli alberi devono differire nella struttura o nelle produzioni utilizzate. Disegnare lo stesso albero in due modi diversi non dimostra nulla.

## 2. Definizioni equivalenti mediante derivazioni

Per una grammatica context-free sono equivalenti:

1. esiste una frase con due alberi di derivazione distinti;
2. esiste una frase con due derivazioni sinistre distinte;
3. esiste una frase con due derivazioni destre distinte.

Quindi possiamo dimostrare l’ambiguità costruendo:

- due alberi;
- oppure due derivazioni sinistre;
- oppure due derivazioni destre.

### Attenzione alle derivazioni qualsiasi

Due derivazioni con ordine di espansione libero possono descrivere lo stesso albero.

Per esempio:

$$
S\to AB,\qquad A\to a,\qquad B\to b
$$

permette:

$$
S\Rightarrow AB\Rightarrow aB\Rightarrow ab
$$

e:

$$
S\Rightarrow AB\Rightarrow Ab\Rightarrow ab
$$

Cambia soltanto quale non terminale viene sviluppato prima.

Questa grammatica non è ambigua.

> [!warning]
> Non basta mostrare una derivazione sinistra e una destra della stessa frase: potrebbero corrispondere allo stesso albero.

## 3. Esempio: precedenza non specificata

Consideriamo:

$$
E\to E+E\mid E*E\mid\mathtt{id}
$$

Il simbolo $\mathtt{id}$ rappresenta un unico terminale, cioè il token identificatore.

La frase:

$$
\mathtt{id}+\mathtt{id}*\mathtt{id}
$$

ammette due strutture:

$$
\mathtt{id}+(\mathtt{id}*\mathtt{id})
$$

e:

$$
(\mathtt{id}+\mathtt{id})*\mathtt{id}
$$

Le parentesi indicano qui il raggruppamento: non sono token aggiunti alla frase.

![[Allegati/FLT_07/flt_07_04_ambiguita.svg|1100]]

### Prima derivazione sinistra

La radice rappresenta un’addizione:

$$
E
\Rightarrow_{\mathrm{lm}}E+E
\Rightarrow_{\mathrm{lm}}\mathtt{id}+E
$$

$$
\Rightarrow_{\mathrm{lm}}\mathtt{id}+E*E
\Rightarrow_{\mathrm{lm}}\mathtt{id}+\mathtt{id}*E
\Rightarrow_{\mathrm{lm}}\mathtt{id}+\mathtt{id}*\mathtt{id}
$$

### Seconda derivazione sinistra

La radice rappresenta una moltiplicazione:

$$
E
\Rightarrow_{\mathrm{lm}}E*E
\Rightarrow_{\mathrm{lm}}E+E*E
$$

$$
\Rightarrow_{\mathrm{lm}}\mathtt{id}+E*E
\Rightarrow_{\mathrm{lm}}\mathtt{id}+\mathtt{id}*E
\Rightarrow_{\mathrm{lm}}\mathtt{id}+\mathtt{id}*\mathtt{id}
$$

Le due derivazioni sono entrambe sinistre, ma iniziano con produzioni diverse e costruiscono alberi diversi.

La grammatica è quindi ambigua.

## 4. Eliminare l’ambiguità attraverso la precedenza

Per imporre la precedenza della moltiplicazione sull’addizione possiamo separare i livelli:

$$
E\to E+T\mid T
$$

$$
T\to T*F\mid F
$$

$$
F\to\mathtt{id}
$$

I ruoli sono:

- $E$: espressioni contenenti addizioni;
- $T$: termini contenenti moltiplicazioni;
- $F$: fattori.

La frase:

$$
\mathtt{id}+\mathtt{id}*\mathtt{id}
$$

ha ora soltanto la struttura:

$$
\mathtt{id}+(\mathtt{id}*\mathtt{id})
$$

### Perché la struttura è unica

Se un’espressione contiene `+`, la produzione esterna deve essere:

$$
E\to E+T
$$

Poiché $T$ non genera `+`, la separazione avviene sull’ultimo `+`.

All’interno di un termine, la produzione:

$$
T\to T*F
$$

separa analogamente sull’ultimo `*`, e $F$ genera soltanto `id`.

Il raggruppamento viene quindi determinato dalla grammatica.

### Eventuali parentesi nel linguaggio

Se vogliamo ammettere anche parentesi esplicite, possiamo aggiungere:

$$
F\to\mathtt{(}E\mathtt{)}
$$

Per confrontare due grammatiche equivalenti, però, devono essere ammesse le stesse stringhe terminali: aggiungere parentesi soltanto a una grammatica cambia il linguaggio.

> [!important]
> Eliminare l’ambiguità significa trovare una grammatica non ambigua per lo stesso linguaggio.
>
> Non basta eliminare alcune frasi problematiche o aggiungere nuovi token.

## 5. Ambiguità dovuta all’associatività

Consideriamo:

$$
E\to E-E\mid\mathtt{id}
$$

La frase:

$$
\mathtt{id}-\mathtt{id}-\mathtt{id}
$$

può essere raggruppata come:

$$
(\mathtt{id}-\mathtt{id})-\mathtt{id}
$$

oppure:

$$
\mathtt{id}-(\mathtt{id}-\mathtt{id})
$$

Con operandi di valore $10$, $3$ e $2$, le interpretazioni danno rispettivamente:

$$
(10-3)-2=5
$$

$$
10-(3-2)=9
$$

### Associatività a sinistra

Una grammatica che impone il raggruppamento a sinistra è:

$$
E\to E-\mathtt{id}\mid\mathtt{id}
$$

### Associatività a destra

Per imporre il raggruppamento a destra:

$$
E\to\mathtt{id}-E\mid\mathtt{id}
$$

### Ambiguità e risultato numerico

L’ambiguità è una proprietà sintattica.

Anche quando due raggruppamenti producono lo stesso valore, la presenza di due alberi distinti rende comunque ambigua la grammatica.

## 6. Una grammatica regolare può essere ambigua

Consideriamo:

$$
S\to aA\mid aB
$$

$$
A\to b
$$

$$
B\to b
$$

La stringa `ab` ha due derivazioni sinistre:

$$
S\Rightarrow_{\mathrm{lm}}aA
\Rightarrow_{\mathrm{lm}}ab
$$

e:

$$
S\Rightarrow_{\mathrm{lm}}aB
\Rightarrow_{\mathrm{lm}}ab
$$

Gli alberi differiscono per il non terminale utilizzato.

La grammatica è regolare e ambigua, mentre il linguaggio è semplicemente:

$$
L(G)=\{ab\}
$$

Una grammatica non ambigua equivalente è:

$$
S\to aA
$$

$$
A\to b
$$

### Alternative con lo stesso prefisso

Avere due produzioni che iniziano con lo stesso terminale non dimostra da solo l’ambiguità.

Se nell’esempio sostituissimo $B\to b$ con:

$$
B\to c
$$

il linguaggio diventerebbe:

$$
\{ab,ac\}
$$

e ciascuna frase avrebbe una sola derivazione.

## 7. Anche ε può dimostrare l’ambiguità

Consideriamo:

$$
S\to A\mid B
$$

$$
A\to\varepsilon
$$

$$
B\to\varepsilon
$$

La stringa vuota ha due derivazioni sinistre:

$$
S\Rightarrow_{\mathrm{lm}}A
\Rightarrow_{\mathrm{lm}}\varepsilon
$$

e:

$$
S\Rightarrow_{\mathrm{lm}}B
\Rightarrow_{\mathrm{lm}}\varepsilon
$$

La grammatica è ambigua anche se genera un linguaggio con una sola stringa.

> [!tip]
> Quando una grammatica contiene produzioni vuote, controlla anche se esistono più strutture per generare $\varepsilon$.

## 8. Grammatica ambigua e linguaggio intrinsecamente ambiguo

Sono concetti diversi.

### Grammatica ambigua

La particolare grammatica considerata assegna più strutture ad almeno una frase.

Potrebbe esistere una grammatica equivalente non ambigua.

### Linguaggio intrinsecamente ambiguo

Un linguaggio context-free è **intrinsecamente ambiguo** se ogni grammatica context-free che lo genera è ambigua.

In questo caso non è possibile eliminare l’ambiguità conservando il linguaggio attraverso una diversa grammatica context-free.

L’esempio:

$$
L=\{ab\}
$$

non è intrinsecamente ambiguo, perché abbiamo già costruito una grammatica non ambigua che lo genera.

I linguaggi regolari ammettono sempre una grammatica non ambigua: possiamo ottenerla da un DFA, nel quale ogni stringa ha un unico cammino.

> [!warning]
> Dimostrare che una grammatica è ambigua non dimostra che il suo linguaggio sia intrinsecamente ambiguo.

## 9. Collegamento con LR e SLR

Le grammatiche LR(0) e SLR(1) sono non ambigue.

Quindi:

$$
G\text{ ambigua}
\implies
G\text{ non è LR(0) né SLR(1)}
$$

Il contrario non vale:

$$
G\text{ non LR(0)}
$$

non implica:

$$
G\text{ ambigua}
$$

Una grammatica può essere non ambigua e richiedere un metodo più potente di LR(0).

Per esempio:

$$
S\to aA\mid a
$$

$$
A\to b
$$

genera soltanto:

$$
\{a,ab\}
$$

con una sola struttura per ciascuna frase.

La grammatica non è LR(0), ma è SLR(1).

> [!important] Collegamento con l’esame
> Un conflitto LR(0) dimostra che la grammatica non è LR(0).
>
> Da solo non dimostra l’ambiguità: bisogna verificare le ulteriori proprietà richieste, come SLR(1).

## 10. Come dimostrare l’ambiguità

Una soluzione completa deve:

1. scegliere una frase $w$ del linguaggio;
2. mostrare due strutture realmente ammesse dalla grammatica;
3. usare due alberi oppure due derivazioni dello stesso tipo;
4. indicare dove le strutture differiscono;
5. concludere che la grammatica è ambigua.

Non basta proporre due raggruppamenti immaginabili: entrambi devono essere ottenibili con le produzioni date.

## 11. Come motivare la non ambiguità

Controllare alcune frasi non basta.

Occorre un argomento generale, per esempio:

- ogni frase ha una separazione unica nelle sue componenti;
- gli operatori sono organizzati in livelli di precedenza;
- l’associatività determina un solo raggruppamento;
- un separatore o delimitatore individua senza alternative le parti della costruzione;
- la grammatica appartiene a una classe deterministica, come LR(0) o SLR(1), verificata correttamente.

Una dimostrazione può procedere ricorsivamente: si individua un’unica produzione alla radice e si dimostra l’unicità dei sottoalberi.

## 12. Errori da evitare

- Usare una derivazione sinistra e una destra dello stesso albero come prova di ambiguità.
- Confondere un diverso ordine di espansione con una diversa struttura.
- Disegnare alberi che non rispettano le produzioni.
- Considerare due disposizioni grafiche dello stesso albero come alberi distinti.
- Confondere ambiguità grammaticale e risultato delle operazioni.
- Pensare che una grammatica regolare sia necessariamente non ambigua.
- Concludere l’ambiguità dalla sola presenza di alternative con lo stesso prefisso.
- Dimenticare la stringa vuota.
- Modificare il linguaggio mentre si cerca di eliminare l’ambiguità.
- Confondere grammatica ambigua e linguaggio intrinsecamente ambiguo.
- Interpretare qualsiasi conflitto LR(0) come prova di ambiguità.