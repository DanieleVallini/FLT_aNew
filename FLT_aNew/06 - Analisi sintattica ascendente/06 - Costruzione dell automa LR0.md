# Costruzione dell’automa LR(0)

L’automa canonico LR(0) si costruisce applicando ripetutamente CLOSURE e GOTO fino a ottenere tutti gli insiemi di item raggiungibili.

Ogni insieme distinto diventa uno stato.

Ogni GOTO non vuota diventa una transizione.

> [!important] Obiettivo d’esame
> Se viene richiesto l’intero automa, bisogna riportare tutti gli stati e tutti gli archi, anche quando un conflitto permette già di concludere che la grammatica non è LR(0).
>
> La costruzione dell’automa e la classificazione della grammatica sono due passaggi distinti.

---

## 1. Algoritmo della collezione canonica

Data la grammatica aumentata, partiamo da:

$$
I_0=\mathrm{CLOSURE}(\{[S'\to\cdot S\downarrow]\}).
$$

La collezione iniziale è:

$$
\mathcal C=\{I_0\}.
$$

Poi:

1. scegli uno stato $I\in\mathcal C$ ancora da esaminare;
2. individua i simboli che compaiono immediatamente dopo un punto;
3. per ciascuno di essi calcola $J=\mathrm{GOTO}(I,X)$;
4. se $J$ è nuovo, aggiungilo alla collezione;
5. se $J$ esiste già, riutilizza quello stato;
6. registra la transizione $I\xrightarrow{X}J$;
7. continua finché tutti gli stati sono stati esaminati.

La costruzione termina perché esiste soltanto un numero finito di insiemi di item.

> [!tip] Quando due stati sono uguali?
> Quando contengono esattamente gli stessi item.
>
> L’ordine degli item dentro l’insieme non conta.

---

## 2. Grammatica dell’esame di gennaio 2026

Usiamo la grammatica:

$$
\begin{array}{rcl}
(0)&S'&\to S\downarrow\\
(1)&S&\to ABcS\\
(2)&S&\to d\\
(3)&A&\to aA\\
(4)&A&\to\varepsilon\\
(5)&B&\to bB\\
(6)&B&\to\varepsilon.
\end{array}
$$

La produzione (0) è quella aumentata.

I numeri delle altre produzioni potranno essere usati per indicare le riduzioni nelle tabelle.

La numerazione degli stati scelta qui segue un particolare ordine di scoperta. Un ordine diverso può produrre numeri differenti, mantenendo identici insiemi e collegamenti.

### Diagramma completo

![[Allegati/FLT_06_06/flt_06_06_automa_lr0_gennaio_2026.svg|1400]]

Il diagramma contiene tutti gli item degli stati.

Gli archi viola corrispondono a non terminali; quelli blu a terminali, compreso il marcatore finale.

---

## 3. Stato iniziale I₀

Partiamo dall’item aumentato e calcoliamo la chiusura:

$$
\boxed{
I_0=
\left\{
\begin{array}{l}
[S'\to\cdot S\downarrow]\\
[S\to\cdot ABcS]\\
[S\to\cdot d]\\
[A\to\cdot aA]\\
[A\to\cdot]
\end{array}
\right\}.
}
$$

Le transizioni possibili partono sui simboli:

$$
S,\ A,\ d,\ a.
$$

Le produzioni di $B$ non vengono ancora aggiunte: nell’item di $S$ il punto si trova prima di $A$.

La nullabilità di $A$ non permette di saltarlo durante la chiusura.

---

## 4. Stati ottenuti direttamente da I₀

### Stato I₁: riconoscimento di S

$$
\boxed{
I_1=\mathrm{GOTO}(I_0,S)
=
\{[S'\to S\cdot\downarrow]\}.
}
$$

È ancora necessario riconoscere il marcatore.

### Stato I₂: riconoscimento di A

$$
\boxed{
I_2=\mathrm{GOTO}(I_0,A)
=
\left\{
\begin{array}{l}
[S\to A\cdot BcS]\\
[B\to\cdot bB]\\
[B\to\cdot]
\end{array}
\right\}.
}
$$

Il punto davanti a $B$ richiede la chiusura sulle sue produzioni.

### Stato I₃: lettura di d

$$
\boxed{
I_3=\mathrm{GOTO}(I_0,d)
=
\{[S\to d\cdot]\}.
}
$$

Il lato destro della produzione (2) è completo.

### Stato I₄: lettura di a

$$
\boxed{
I_4=\mathrm{GOTO}(I_0,a)
=
\left\{
\begin{array}{l}
[A\to a\cdot A]\\
[A\to\cdot aA]\\
[A\to\cdot]
\end{array}
\right\}.
}
$$

Dopo la prima $a$ dobbiamo riconoscere un altro $A$, quindi aggiungiamo nuovamente le sue produzioni con il punto iniziale.

---

## 5. Stati successivi

### Stato I₅: produzione aumentata completa

$$
\boxed{
I_5=\mathrm{GOTO}(I_1,\downarrow)
=
\{[S'\to S\downarrow\cdot]\}.
}
$$

Con la convenzione adottata, questo è lo stato in cui il parser segnala l’accettazione, dopo aver consumato l’intero input.

### Stato I₆: riconoscimento di B

$$
\boxed{
I_6=\mathrm{GOTO}(I_2,B)
=
\{[S\to AB\cdot cS]\}.
}
$$

Il prossimo simbolo è il terminale $c$.

### Stato I₇: lettura di b

$$
\boxed{
I_7=\mathrm{GOTO}(I_2,b)
=
\left\{
\begin{array}{l}
[B\to b\cdot B]\\
[B\to\cdot bB]\\
[B\to\cdot]
\end{array}
\right\}.
}
$$

Il punto davanti a $B$ richiede ancora la chiusura sulle produzioni di $B$.

### Stato I₈: produzione A → aA completa

$$
\boxed{
I_8=\mathrm{GOTO}(I_4,A)
=
\{[A\to aA\cdot]\}.
}
$$

### Due cicli da registrare

Calcolando GOTO sui terminali ricorsivi otteniamo:

$$
\boxed{\mathrm{GOTO}(I_4,a)=I_4,}
$$

$$
\boxed{\mathrm{GOTO}(I_7,b)=I_7.}
$$

Sono archi che ritornano sullo stesso stato.

Non generano nuovi insiemi.

---

## 6. Lo stato I₉ e la ripresa della ricorsione

Da $I_6$ leggiamo $c$:

$$
[S\to AB\cdot cS]
\quad\longmapsto\quad
[S\to ABc\cdot S].
$$

Dobbiamo poi calcolare la chiusura su $S$ e, successivamente, su $A$.

Otteniamo:

$$
\boxed{
I_9=\mathrm{GOTO}(I_6,c)
=
\left\{
\begin{array}{l}
[S\to ABc\cdot S]\\
[S\to\cdot ABcS]\\
[S\to\cdot d]\\
[A\to\cdot aA]\\
[A\to\cdot]
\end{array}
\right\}.
}
$$

### Perché I₉ è diverso da I₀?

I due stati condividono quattro item, ma il rimanente è diverso.

In $I_0$ troviamo:

$$
[S'\to\cdot S\downarrow].
$$

In $I_9$ troviamo:

$$
[S\to ABc\cdot S].
$$

Quindi:

$$
I_9\neq I_0.
$$

Da $I_9$, però, alcune transizioni raggiungono stati già costruiti:

$$
\boxed{
\begin{aligned}
\mathrm{GOTO}(I_9,A)&=I_2,\\
\mathrm{GOTO}(I_9,d)&=I_3,\\
\mathrm{GOTO}(I_9,a)&=I_4.
\end{aligned}
}
$$

Il GOTO su $S$ genera invece un nuovo stato.

---

## 7. Ultimi stati

### Stato I₁₀: produzione B → bB completa

$$
\boxed{
I_{10}=\mathrm{GOTO}(I_7,B)
=
\{[B\to bB\cdot]\}.
}
$$

### Stato I₁₁: produzione S → ABcS completa

$$
\boxed{
I_{11}=\mathrm{GOTO}(I_9,S)
=
\{[S\to ABcS\cdot]\}.
}
$$

Esaminati anche questi stati, non emergono ulteriori transizioni.

La collezione canonica completa è:

$$
\boxed{
\mathcal C=\{I_0,I_1,\ldots,I_{11}\}.
}
$$

Abbiamo quindi **12 stati**.

---

## 8. Tabella completa delle transizioni

Nella tabella, $X\mapsto I_j$ significa:

$$
\mathrm{GOTO}(I_i,X)=I_j.
$$

| Stato | Transizioni non vuote |
|---|---|
| $I_0$ | $S\mapsto I_1$; $A\mapsto I_2$; $d\mapsto I_3$; $a\mapsto I_4$ |
| $I_1$ | $\downarrow\mapsto I_5$ |
| $I_2$ | $B\mapsto I_6$; $b\mapsto I_7$ |
| $I_3$ | Nessuna |
| $I_4$ | $A\mapsto I_8$; $a\mapsto I_4$ |
| $I_5$ | Nessuna |
| $I_6$ | $c\mapsto I_9$ |
| $I_7$ | $B\mapsto I_{10}$; $b\mapsto I_7$ |
| $I_8$ | Nessuna |
| $I_9$ | $S\mapsto I_{11}$; $A\mapsto I_2$; $d\mapsto I_3$; $a\mapsto I_4$ |
| $I_{10}$ | Nessuna |
| $I_{11}$ | Nessuna |

Tutte le GOTO non elencate sono vuote.

Il numero complessivo delle transizioni è:

$$
\boxed{16.}
$$

> [!tip] Controllo di completezza
> Per ogni stato, verifica tutti i simboli immediatamente dopo un punto.
>
> Ognuno deve avere la propria transizione, anche quando lo stato di arrivo esiste già.

---

## 9. L’automa esiste anche se la grammatica non è LR(0)

La costruzione della collezione canonica può essere completata anche per una grammatica che non è LR(0).

L’automa ha una sola destinazione per ogni coppia stato-simbolo, ma questo non garantisce che le azioni del parser siano prive di conflitti.

### Il conflitto è già visibile in I₀

Lo stato contiene:

$$
[A\to\cdot],
$$

che propone la riduzione:

$$
A\to\varepsilon.
$$

Contiene anche:

$$
[A\to\cdot aA]
$$

e:

$$
[S\to\cdot d],
$$

che permettono spostamenti rispettivamente su $a$ e $d$.

Nel metodo LR(0), la riduzione non viene selezionata mediante lookahead e compete con questi spostamenti.

La grammatica non è quindi LR(0).

> [!important] Conseguenza per l’esercizio
> Il conflitto non autorizza a eliminare item o a fermare la costruzione quando viene richiesto l’intero automa.
>
> Le proprietà SLR(1) saranno verificate usando lo stesso automa e gli insiemi FOLLOW.

Non essere LR(0) non dimostra, da solo, che la grammatica sia ambigua.

---

## 10. Procedura da seguire all’esame

1. Riporta la produzione aumentata esatta.
2. Calcola completamente $I_0$.
3. Elenca i simboli immediatamente dopo i punti.
4. Calcola ogni GOTO con la relativa chiusura.
5. Confronta l’intero insieme ottenuto con gli stati esistenti.
6. Registra anche cicli e ritorni.
7. Continua finché tutti gli stati sono stati esaminati.
8. Controlla separatamente i conflitti richiesti dalla classificazione.

Gli errori principali sono:

- saltare i non terminali nullable durante la chiusura;
- disegnare archi epsilon per le produzioni vuote;
- dimenticare il marcatore nella produzione aumentata;
- creare copie di stati già presenti;
- fondere stati simili ma con item differenti;
- interrompere la costruzione al primo conflitto.

> [!important] Risultato da conservare
> Per questa grammatica la costruzione produce 12 stati e 16 transizioni.
>
> Questi stessi stati saranno la base per le tabelle e per la simulazione richiesta sulla parola $cbcd\downarrow$.
