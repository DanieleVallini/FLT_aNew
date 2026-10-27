# Linguaggi deterministici - Proprieta e chiusure

Un linguaggio è context-free deterministico se esiste un automa a pila deterministico che lo riconosce per stato finale.

La classe di questi linguaggi viene indicata con:

$$
\mathrm{DCFL}.
$$

Le proprietà di chiusura permettono di capire quali operazioni preservano l’appartenenza a questa classe.

> [!important] Collegamento con l’esame
> Nella prova del 22 febbraio 2011 viene chiesto di definire i linguaggi deterministici e stabilire se siano chiusi rispetto all’unione.
>
> La risposta è **no**. Per giustificarla possiamo usare la chiusura per complemento e un controesempio relativo all’intersezione.

---

## 1. Definizione di linguaggio deterministico

Formalmente:

$$
\boxed{
L\in\mathrm{DCFL}
\iff
\exists M\text{ DPDA tale che }L=L_F(M).
}
$$

La proprietà riguarda l’esistenza di almeno un riconoscitore deterministico.

Un NPDA non deterministico per $L$ non dimostra che $L$ sia fuori da $\mathrm{DCFL}$: potrebbe esistere anche un DPDA equivalente.

Per esempio:

$$
\{a^nb^n\mid n\geq0\}
$$

è deterministico, anche se la costruzione generale a partire dalla grammatica:

$$
S\to aSb\mid\varepsilon
$$

produce un NPDA con scelte.

---

## 2. Che cosa significa chiusura

Una classe $\mathcal C$ è chiusa rispetto all’unione se:

$$
L_1,L_2\in\mathcal C
\Longrightarrow
L_1\cup L_2\in\mathcal C.
$$

Per dimostrare la chiusura, la proprietà deve valere per ogni coppia di linguaggi della classe.

Per dimostrare la mancata chiusura, basta trovare una coppia per cui non vale.

> [!warning] Significato di “non chiusa”
> Non significa che ogni unione di due linguaggi deterministici sia non deterministica.
>
> Per esempio, $\{a\}\cup\{b\}$ è regolare e quindi deterministico.
>
> Significa che il risultato non è garantito per tutte le coppie.

### Tabella delle chiusure principali

| Operazione | REG | DCFL | CFL |
|---|---|---|---|
| Unione | Sì | **No** | Sì |
| Intersezione | Sì | **No** | No |
| Complemento | Sì | **Sì** | No |
| Concatenazione | Sì | **No** | Sì |
| Stella di Kleene | Sì | **No** | Sì |
| Intersezione con un linguaggio regolare | Sì | **Sì** | Sì |

Per concatenazione e stella, come per l’unione, “no” indica una mancata chiusura generale: singoli risultati possono comunque essere deterministici.

---

## 3. Chiusura per complemento

Fissato un alfabeto $\Sigma$, definiamo:

$$
\overline L=\Sigma^*\setminus L.
$$

Vale il teorema:

$$
\boxed{
L\in\mathrm{DCFL}
\Longrightarrow
\overline L\in\mathrm{DCFL}.
}
$$

Questa proprietà distingue i linguaggi deterministici dai linguaggi context-free generali, che non sono chiusi per complemento.

### Perché serve attenzione nella costruzione

Su un DPDA arbitrario non basta scambiare stati finali e non finali.

Bisogna gestire:

- transizioni mancanti;
- computazioni bloccate prima di consumare tutto l’input;
- eventuali cicli epsilon;
- mosse epsilon dopo l’esaurimento dell’input.

La dimostrazione completa passa attraverso una normalizzazione dell’automa che permette di individuare correttamente l’esito finale.

Nelle presentazioni della costruzione si usa spesso un marcatore esplicito di fine input.

> [!important] Risultato e procedura sono distinti
> La chiusura per complemento è garantita.
>
> Lo scambio diretto degli stati finali richiede invece che l’automa sia stato preparato opportunamente.

---

## 4. Intersezione con un linguaggio regolare

Se:

$$
L\in\mathrm{DCFL}
\qquad\text{e}\qquad
R\in\mathrm{REG},
$$

allora:

$$
\boxed{L\cap R\in\mathrm{DCFL}.}
$$

### Idea della costruzione

Simuliamo contemporaneamente:

- un DPDA per $L$;
- un DFA per $R$.

Lo stato del nuovo automa è una coppia:

$$
(q,p),
$$

dove $q$ appartiene al DPDA e $p$ al DFA.

La pila resta quella del DPDA: il DFA non richiede memoria aggiuntiva.

Se il DPDA legge un simbolo, entrambi i controlli avanzano.

Se il DPDA esegue una mossa epsilon, lo stato del DFA resta invariato.

### Forma delle transizioni

Se:

$$
\delta_M(q,a,X)=(q',\gamma)
$$

e il DFA ha funzione $\eta$, introduciamo:

$$
\delta'((q,p),a,X)
=
((q',\eta(p,a)),\gamma).
$$

Per una mossa epsilon:

$$
\delta_M(q,\varepsilon,X)=(q',\gamma),
$$

introduciamo:

$$
\delta'((q,p),\varepsilon,X)
=
((q',p),\gamma).
$$

L’accettazione richiede che entrambi i controlli siano in uno stato finale.

La costruzione conserva il determinismo.

### Esempio

Consideriamo:

$$
L=\{a^nb^n\mid n\geq0\}
$$

e il linguaggio regolare:

$$
R=(aa)^*b^*.
$$

L’intersezione seleziona le parole con un numero pari di `a`:

$$
L\cap R=
\{a^{2k}b^{2k}\mid k\geq0\}.
$$

Il risultato è deterministico.

---

## 5. Mancata chiusura per intersezione

Consideriamo, sull’alfabeto $\Sigma=\{a,b,c\}$:

$$
A=\{a^nb^nc^m\mid n,m\geq1\}
$$

e:

$$
B=\{a^mb^nc^n\mid m,n\geq1\}.
$$

### Perché A è deterministico

Un DPDA può:

1. controllare l’ordine dei blocchi;
2. contare le `a` nella pila;
3. confrontarle con le `b`;
4. verificare che seguano una o più `c`.

Il numero di `c` è indipendente dal confronto.

### Perché B è deterministico

Un DPDA può:

1. leggere una o più `a`;
2. contare le `b` nella pila;
3. confrontarle con le `c`.

Quindi:

$$
A,B\in\mathrm{DCFL}.
$$

### Calcolare l’intersezione

Per appartenere ad $A$ deve valere:

$$
\#a=\#b.
$$

Per appartenere a $B$ deve valere:

$$
\#b=\#c.
$$

Entrambi i linguaggi impongono l’ordine dei blocchi. Pertanto:

$$
\boxed{
A\cap B=\{a^nb^nc^n\mid n\geq1\}.
}
$$

Questo linguaggio non è context-free.

Poiché ogni linguaggio deterministico è context-free, l’intersezione non può essere deterministica.

Concludiamo:

$$
\boxed{\mathrm{DCFL}\text{ non è chiusa per intersezione}.}
$$

> [!info]- Perché aⁿbⁿcⁿ non è context-free
> Supponiamo che lo sia e applichiamo il pumping lemma per i linguaggi context-free.
>
> Sia $p$ la lunghezza di pompaggio e scegliamo $w=a^pb^pc^p$.
>
> Il lemma richiederebbe una decomposizione $w=uvxyz$ con $|vxy|\leq p$, $|vy|>0$ e $uv^txy^tz$ nel linguaggio per ogni $t\geq0$.
>
> Il segmento $vxy$ non può contenere contemporaneamente `a` e `c`: dovrebbe attraversare l’intero blocco di $p$ simboli `b`, superando la lunghezza consentita.
>
> Pompando con $t=0$, almeno un conteggio diminuisce, mentre almeno uno dei tre resta uguale a $p$.
>
> I tre conteggi non sono più tutti uguali: contraddizione.
>
> Questo è il pumping lemma per i CFL, distinto da quello usato per dimostrare la non regolarità.

---

## 6. Mancata chiusura per unione

Usiamo i linguaggi $A$ e $B$ della sezione precedente.

Sappiamo che:

$$
A,B\in\mathrm{DCFL}
$$

ma:

$$
A\cap B\notin\mathrm{DCFL}.
$$

Supponiamo, per assurdo, che i DCFL siano chiusi per unione.

Essendo chiusi per complemento:

$$
\overline A,\overline B\in\mathrm{DCFL}.
$$

Per l’ipotetica chiusura rispetto all’unione:

$$
\overline A\cup\overline B\in\mathrm{DCFL}.
$$

Applicando ancora il complemento:

$$
\overline{\overline A\cup\overline B}
\in\mathrm{DCFL}.
$$

Ma per De Morgan:

$$
\overline{\overline A\cup\overline B}=A\cap B,
$$

che sappiamo non essere deterministico.

Otteniamo una contraddizione.

Quindi:

$$
\boxed{\mathrm{DCFL}\text{ non è chiusa per unione}.}
$$

### Una coppia concreta che testimonia la mancata chiusura

I due linguaggi:

$$
C=\overline A,
\qquad
D=\overline B
$$

sono deterministici, ma:

$$
C\cup D
=
\Sigma^*\setminus\{a^nb^nc^n\mid n\geq1\}
$$

non è deterministico.

Se lo fosse, anche il suo complemento sarebbe deterministico, mentre non è neppure context-free.

> [!warning] Tenere distinta la coppia usata
> Per l’intersezione il controesempio usa $A$ e $B$.
>
> Per l’unione il ragionamento appena svolto usa i loro complementi $\overline A$ e $\overline B$.

---

## 7. L’unione resta comunque context-free

Se:

$$
L_1,L_2\in\mathrm{DCFL},
$$

allora entrambi appartengono a $\mathrm{CFL}$.

Poiché i CFL sono chiusi per unione:

$$
\boxed{L_1\cup L_2\in\mathrm{CFL}.}
$$

Il risultato può quindi richiedere un NPDA, pur essendo i due linguaggi iniziali riconoscibili da DPDA.

Una costruzione generale per l’unione consiste nel scegliere inizialmente quale automa simulare.

Questa scelta è ammessa in un NPDA; non garantisce un DPDA.

> [!important] Confronto tra unione e intersezione
> L’unione di due DCFL è sempre context-free.
>
> L’intersezione di due DCFL può essere addirittura non context-free.

---

## 8. Conseguenze quando uno dei linguaggi è regolare

Dalla chiusura per complemento e per intersezione con un regolare ricaviamo altre proprietà.

Se:

$$
L\in\mathrm{DCFL},
\qquad R\in\mathrm{REG},
$$

allora:

$$
L\setminus R=L\cap\overline R
\in\mathrm{DCFL},
$$

perché $\overline R$ è regolare.

Anche:

$$
R\setminus L=R\cap\overline L
\in\mathrm{DCFL}.
$$

Infine:

$$
L\cup R=
\overline{\overline L\cap\overline R}
\in\mathrm{DCFL}.
$$

Quindi l’unione con un linguaggio regolare preserva il determinismo.

La mancata chiusura per unione riguarda il caso generale di due linguaggi deterministici.

---

## 9. Risposta sintetica alla domanda d’esame

**Definizione:**

Un linguaggio è deterministico se esiste un DPDA che lo riconosce per stato finale.

**Chiusura rispetto all’unione:**

I linguaggi deterministici non sono chiusi per unione.

Infatti sono chiusi per complemento. Se fossero chiusi anche per unione, per De Morgan sarebbero chiusi per intersezione.

Ma i linguaggi deterministici:

$$
A=\{a^nb^nc^m\mid n,m\geq1\},
$$

$$
B=\{a^mb^nc^n\mid m,n\geq1\}
$$

hanno intersezione:

$$
\{a^nb^nc^n\mid n\geq1\},
$$

che non è context-free. Quindi la chiusura per unione è impossibile.

> [!important] Errori da evitare
> - Trasferire ai DCFL tutte le chiusure dei linguaggi regolari.
> - Confondere “non regolare” con “non context-free”.
> - Usare il solo fallimento di una costruzione come prova di mancata chiusura.
> - Dimenticare di fissare $\Sigma^*$ quando si prendono complementi.
> - Affermare che l’unione di due DCFL possa essere non context-free.