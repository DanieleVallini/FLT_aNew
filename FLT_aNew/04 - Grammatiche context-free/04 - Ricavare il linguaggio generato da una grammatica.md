# Ricavare il linguaggio generato da una grammatica

Ricavare il linguaggio di una grammatica significa descrivere **tutte e sole le stringhe terminali** che si possono ottenere dal simbolo iniziale.

Non basta elencare alcune derivazioni: bisogna individuare la struttura comune delle parole, i vincoli sui loro simboli e gli eventuali casi particolari.

> [!important] Obiettivo
> Data una grammatica $G$, dobbiamo trovare una descrizione di:
> $$
> L(G)=\{w\in\Sigma^*\mid S\Rightarrow_G^*w\}.
> $$
> La descrizione proposta deve comprendere ogni parola generata ed escludere ogni parola non generata.

---

## 1. Da dove partire

Prima di scrivere una formula, osserviamo:

1. quali parole può generare ciascun non terminale;
2. quali produzioni introducono ricorsione;
3. quali produzioni permettono di terminare;
4. quali simboli vengono aggiunti a ogni passo;
5. in quale ordine compaiono i terminali.

Per esempio:

$$
A\to aA\mid a
$$

genera una o più `a`:

$$
L_G(A)=\{a^n\mid n\geq1\}.
$$

La produzione $A\to aA$ aggiunge una `a` e lascia ancora un $A$ da espandere. La produzione $A\to a$ conclude la derivazione.

Invece:

$$
A\to aA\mid\varepsilon
$$

genera:

$$
L_G(A)=\{a^n\mid n\geq0\}.
$$

La differenza riguarda il numero minimo di simboli: nel secondo caso è ammessa anche la parola vuota.

---

## 2. Contare le applicazioni delle produzioni ricorsive

Consideriamo:

$$
S\to aSb\mid\varepsilon.
$$

Ogni applicazione di $S\to aSb$ aggiunge:

- una `a` a sinistra;
- una `b` a destra.

Dopo $n$ applicazioni:

$$
S\Rightarrow^*a^nSb^n.
$$

Applicando infine $S\to\varepsilon$:

$$
S\Rightarrow^*a^nb^n.
$$

Quindi:

$$
L(G)=\{a^nb^n\mid n\geq0\}.
$$

Il vincolo di uguaglianza nasce dal fatto che **la stessa applicazione di una produzione aggiunge entrambi i simboli**.

> [!tip] Domanda utile
> Quali quantità vengono incrementate insieme?
>
> Se un passo aggiunge una `a` e due `b`, dopo $n$ passi avremo un contributo di $n$ `a` e $2n$ `b`.

Attenzione, però: altri non terminali potrebbero aggiungere ulteriori simboli. Bisogna considerare l’intera grammatica.

---

## 3. Parametri indipendenti e quantità collegate

Consideriamo:

$$
S\to AB
$$

$$
A\to aA\mid\varepsilon
$$

$$
B\to bB\mid\varepsilon.
$$

Le espansioni di $A$ e $B$ sono indipendenti. Pertanto:

$$
L(G)=\{a^nb^m\mid n,m\geq0\}.
$$

Non possiamo imporre $n=m$: nessuna produzione collega il numero di `a` al numero di `b`.

### Anche due occorrenze dello stesso non terminale sono indipendenti

Se:

$$
S\to AA,
\qquad
A\to aA\mid a,
$$

il primo $A$ può generare una `a` e il secondo tre `a`:

$$
S\Rightarrow AA\Rightarrow aA\Rightarrow aaA
\Rightarrow aaaA\Rightarrow aaaa.
$$

In generale:

$$
L(G)=\{a^{n+m}\mid n,m\geq1\}
=\{a^r\mid r\geq2\}.
$$

Non otteniamo soltanto parole di lunghezza pari.

> [!warning] Errore frequente
> Due occorrenze di $A$ usano le stesse produzioni disponibili, ma non devono scegliere le stesse produzioni né effettuare lo stesso numero di passi.

---

## 4. Esempio d’esame: ricorsione con un non terminale ausiliario

Nella prova del 22 febbraio 2011 compare:

$$
S\to ASbb\mid c
$$

$$
A\to aA\mid a.
$$

Vogliamo ricavare esattamente $L(G)$.

### Passo 1: studiare il non terminale ausiliario

Abbiamo:

$$
L_G(A)=\{a^r\mid r\geq1\}.
$$

Ogni occorrenza di $A$ produce almeno una `a`.

### Passo 2: contare le applicazioni ricorsive di $S$

Applicando $n$ volte $S\to ASbb$, senza espandere ancora gli $A$:

$$
S\Rightarrow^*A^nSb^{2n}.
$$

Poi applichiamo $S\to c$:

$$
S\Rightarrow^*A^ncb^{2n}.
$$

Questo mostra che:

- compare esattamente una `c`;
- tutte le `a` precedono la `c`;
- tutte le `b` seguono la `c`;
- il numero di `b` è pari;
- ci sono $n$ occorrenze di $A$.

### Passo 3: espandere gli $A$

Se $n\geq1$, ciascuna occorrenza produce:

$$
A\Rightarrow^*a^{r_i},
\qquad r_i\geq1.
$$

Il numero totale di `a` è:

$$
m=r_1+\cdots+r_n.
$$

Quindi:

$$
m\geq n.
$$

Non è necessario che $m=n$, perché ogni $A$ può produrre più di una `a`.

### Passo 4: separare il caso senza ricorsione

Se $n=0$, non viene introdotto alcun $A$:

$$
S\Rightarrow c.
$$

In questo caso non possiamo aggiungere `a`.

La descrizione corretta è:

$$
\boxed{
L(G)=\{c\}\cup
\{a^mcb^{2n}\mid n\geq1,\ m\geq n\}.
}
$$

> [!warning] Il caso limite cambia il linguaggio
> Scrivere soltanto:
> $$
> \{a^mcb^{2n}\mid n\geq0,\ m\geq n\}
> $$
> sarebbe sbagliato.
>
> Con $n=0$ questa formula ammetterebbe anche `ac`, `aac` e altre parole che la grammatica non genera.

---

## 5. Dimostrare che la descrizione è esatta

Per giustificare un linguaggio proposto servono due direzioni.

### Ogni parola generata appartiene al linguaggio descritto

Una derivazione terminale usa $S\to ASbb$ un certo numero $n$ di volte e conclude con $S\to c$.

Se $n=0$, produce `c`.

Se $n\geq1$, introduce:

- esattamente $2n$ `b`;
- esattamente $n$ occorrenze di $A$;
- almeno $n$ `a`, perché ogni $A$ ne produce almeno una.

Quindi ogni parola generata ha la forma indicata.

### Ogni parola descritta è generabile

La parola `c` si ottiene direttamente.

Consideriamo ora:

$$
a^mcb^{2n},
\qquad n\geq1,\quad m\geq n.
$$

Applichiamo $n$ volte la produzione ricorsiva e poi quella conclusiva:

$$
S\Rightarrow^*A^ncb^{2n}.
$$

Facciamo generare:

- al primo $A$, $m-n+1$ simboli `a`;
- a ciascuno degli altri $n-1$ non terminali, una sola `a`.

È possibile perché:

$$
m-n+1\geq1.
$$

Il totale è:

$$
(m-n+1)+(n-1)=m.
$$

Otteniamo quindi esattamente la parola desiderata.

> [!important] Perché servono entrambe le direzioni
> La prima esclude che la grammatica generi parole aggiuntive.
> La seconda esclude che la formula includa parole impossibili da generare.

---

## 6. Controllare la formula con parole brevi

Per la grammatica precedente:

| Parola | Appartiene? | Motivo |
|---|---|---|
| `c` | Sì | Nessuna applicazione ricorsiva |
| `acbb` | Sì | $n=1$, $m=1$ |
| `aaacbb` | Sì | $n=1$, $m=3$ |
| `aaacbbbb` | Sì | $n=2$, $m=3$ |
| `cbb` | No | Con due `b` serve almeno una `a` |
| `acbbbb` | No | Con quattro `b` servono almeno due `a` |
| `aac` | No | Senza `b` si può generare soltanto `c` |
| `aacbbb` | No | Il numero di `b` deve essere pari |

Questi controlli aiutano a trovare errori, ma non sostituiscono la dimostrazione generale.

---

## 7. Esempio d’esame: eliminare un parametro ausiliario

Nella prova del 26 gennaio 2026 compare:

$$
S\to ASB\mid\varepsilon
$$

$$
A\to a\mid aA
$$

$$
B\to b\mid bb.
$$

Indichiamo con $k$ il numero di applicazioni di $S\to ASB$.

Prima di espandere gli altri non terminali:

$$
S\Rightarrow^*A^kSB^k
\Rightarrow A^kB^k.
$$

### Vincoli introdotti dagli $A$

Ogni $A$ produce almeno una `a`.

Se $n$ è il numero totale di `a`:

$$
n\geq k.
$$

### Vincoli introdotti dai $B$

Ogni $B$ produce una oppure due `b`.

Se $m$ è il numero totale di `b`:

$$
k\leq m\leq2k.
$$

Per $k\geq1$ otteniamo quindi:

$$
n\geq k,
\qquad
k\leq m\leq2k.
$$

Per $k=0$ otteniamo soltanto $\varepsilon$.

### Eliminazione di $k$

Dai vincoli segue:

$$
m\leq2k\leq2n.
$$

Quindi ogni parola non vuota soddisfa:

$$
n,m\geq1,
\qquad
m\leq2n.
$$

Bisogna verificare anche il contrario.

Dati $n,m\geq1$ con $m\leq2n$, scegliamo:

$$
k=\left\lceil\frac m2\right\rceil.
$$

Allora:

$$
1\leq k\leq n,
\qquad
k\leq m\leq2k.
$$

Possiamo distribuire le $n$ `a` tra i $k$ non terminali $A$.

Per ottenere esattamente $m$ `b`, facciamo produrre:

- `bb` a $m-k$ occorrenze di $B$;
- `b` alle altre $2k-m$ occorrenze.

I numeri sono non negativi e la loro somma è $k$.

Pertanto:

$$
\boxed{
L(G)=
\{\varepsilon\}\cup
\{a^nb^m\mid n,m\geq1,\ m\leq2n\}.
}
$$

> [!warning] Il parametro $k$ non coincide con $n$
> $k$ conta le applicazioni ricorsive di $S$.
> $n$ conta le `a` prodotte complessivamente.
>
> Siccome ogni $A$ può produrre molte `a`, non possiamo sostituire automaticamente $k$ con $n$ in tutti i vincoli.
>
> Per esempio, `aaaaab` è generabile: basta introdurre un solo $A$ e un solo $B$.

---

## 8. Contare i simboli non basta: serve il loro ordine

Confrontiamo:

$$
S\to aSb\mid\varepsilon
$$

e:

$$
S\to aS\mid bS\mid\varepsilon.
$$

La prima grammatica genera:

$$
\{a^nb^n\mid n\geq0\}.
$$

La seconda genera:

$$
\{a,b\}^*.
$$

Nella seconda si possono scegliere liberamente `a` e `b` a ogni passo, ottenendo anche parole come `baba`.

Scrivere soltanto quanti simboli compaiono perderebbe questa informazione.

> [!tip] Controlla sempre due aspetti
> - **Quantità:** uguaglianze, disuguaglianze, parità, valori minimi.
> - **Posizione:** blocchi consecutivi, alternanze, separatori, annidamenti.

---

## 9. Casi particolari da controllare

### Ricorsione senza una conclusione terminale

La grammatica:

$$
S\to aS
$$

non genera alcuna parola terminale:

$$
L(G)=\varnothing.
$$

Ogni forma ottenuta contiene ancora $S$.

### Presenza della parola vuota

Bisogna verificare se:

$$
S\Rightarrow^*\varepsilon.
$$

Non è indispensabile trovare una produzione diretta $S\to\varepsilon$.

Per esempio:

$$
S\to AB,
\qquad
A\to\varepsilon,
\qquad
B\to\varepsilon
$$

permette di generare $\varepsilon$.

### Grammatica non regolare e linguaggio regolare

Consideriamo:

$$
S\to aSa\mid\varepsilon.
$$

La grammatica genera:

$$
L(G)=\{a^{2n}\mid n\geq0\},
$$

che è regolare ed è denotato da:

$$
(aa)^*.
$$

La forma delle produzioni non basta per concludere che il linguaggio non sia regolare.

---

## 10. Procedura da usare all’esame

1. **Studia i non terminali ausiliari.**  
   Determina quali parole possono generare e con quali lunghezze minime.

2. **Introduci un parametro per le ricorsioni rilevanti.**  
   Specifica che cosa conta ogni parametro.

3. **Scrivi una forma intermedia generale.**  
   Per esempio:
   $$
   A^ncb^{2n}.
   $$

4. **Ricava ordine e vincoli sui terminali.**  
   Distingui le quantità indipendenti da quelle collegate.

5. **Controlla i casi limite.**  
   In particolare: zero ricorsioni, parola vuota e numero minimo di simboli.

6. **Scrivi il linguaggio completo.**  
   Usa un’unione se il caso senza ricorsione ha un comportamento diverso.

7. **Giustifica entrambe le inclusioni.**  
   Ogni parola generata rispetta la formula; ogni parola della formula è generabile.

8. **Se richiesto, studia poi la regolarità.**  
   Prima identifica correttamente il linguaggio, poi scegli la dimostrazione appropriata.

> [!important] Collegamento con gli esercizi d’esame
> Una descrizione sbagliata del linguaggio compromette anche la successiva risposta sulla regolarità.
>
> Prima di applicare il pumping lemma o cercare un’espressione regolare, controlla soprattutto i casi limite e il significato dei parametri.