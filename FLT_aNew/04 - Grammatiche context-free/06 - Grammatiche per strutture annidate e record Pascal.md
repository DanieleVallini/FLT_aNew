# Grammatiche per strutture annidate e record Pascal

Le grammatiche context-free permettono di descrivere strutture che possono contenerne altre dello stesso tipo.

Un esempio è un record il cui campo può avere, come tipo, un altro record.

In questi esercizi dobbiamo gestire contemporaneamente:

- liste di elementi;
- separatori;
- delimitatori di apertura e chiusura;
- annidamento ricorsivo.

> [!important] Obiettivo d’esame
> Costruire una grammatica per il frammento richiesto e mostrare come genera una struttura concreta.
>
> Nella prova del 22 febbraio 2011 viene richiesto proprio un linguaggio di record Pascal con tipi `integer`, `real` e record annidati.

---

## 1. Liste e annidamento sono due strutture diverse

Consideriamo:

```pascal
record
  x: integer;
  y: real;
  z: integer
end
```

Qui troviamo una **lista di dichiarazioni**.

Consideriamo invece:

```pascal
record
  x: record
    y: integer
  end
end
```

Qui troviamo un **record annidato**.

Le due strutture richiedono ruoli grammaticali distinti:

- una produzione ricorsiva permette di aggiungere dichiarazioni alla lista;
- un’altra permette a un tipo di essere nuovamente un record.

> [!tip] Domanda utile
> La ricorsione serve ad aggiungere un elemento allo stesso livello oppure a scendere dentro una struttura?
>
> Questa distinzione aiuta a scegliere i non terminali.

### Esempio essenziale: parentesi bilanciate

La grammatica:

$$
P\to (P)P\mid\varepsilon
$$

genera sequenze di parentesi bilanciate, come:

- `()`;
- `(())`;
- `()()`;
- `(()())`.

Nella produzione $(P)P$:

- il primo $P$ genera ciò che sta dentro la coppia di parentesi;
- il secondo $P$ genera ciò che viene dopo.

Un’idea simile permette di distinguere il contenuto di un record dalle dichiarazioni successive.

---

## 2. Identificare i componenti del record

Per il frammento dell’esercizio consideriamo strutture della forma:

```pascal
record
  lista_di_dichiarazioni
end
```

Ogni dichiarazione ha la forma:

```pascal
lista_di_identificatori: tipo
```

Un tipo può essere:

- `integer`;
- `real`;
- un altro record.

Per esempio:

```pascal
x, y, z: integer
```

è una sola dichiarazione che introduce tre campi.

Invece:

```pascal
x: integer;
y: real
```

contiene due dichiarazioni separate da `;`.

### Terminali e non terminali

Usiamo questi non terminali:

| Non terminale | Significato |
|---|---|
| $R$ | Record completo |
| $L$ | Lista non vuota di dichiarazioni |
| $D$ | Singola dichiarazione |
| $I$ | Lista non vuota di identificatori |
| $T$ | Tipo |

I terminali comprendono le parole chiave `record`, `end`, `integer`, `real`, il token `id` e i simboli `:`, `,`, `;`.

### Il significato del token id

`id` rappresenta un identificatore riconosciuto dall’analizzatore lessicale.

Per esempio, `r1`, `temperatura` e `coordinata` possono essere lessèmi di token `id`.

La grammatica sintattica lavora quindi con:

```text
id : integer
```

mentre nel programma concreto possiamo avere:

```pascal
temperatura: integer
```

Spazi, rientri e ritorni a capo servono a rendere leggibile il codice; qui non sono terminali della grammatica.

---

## 3. Una grammatica per il frammento richiesto

Scegliamo $R$ come simbolo iniziale:

$$
\boxed{
\begin{aligned}
R&\to \mathtt{record}\ L\ \mathtt{end}\\
L&\to D\mid D\ \mathtt{;}\ L\\
D&\to I\ \mathtt{:}\ T\\
I&\to \mathtt{id}\mid \mathtt{id}\ \mathtt{,}\ I\\
T&\to \mathtt{integer}\mid\mathtt{real}\mid R.
\end{aligned}
}
$$

La grammatica assume:

- almeno una dichiarazione per record;
- almeno un identificatore per dichiarazione;
- `;` tra dichiarazioni;
- nessun `;` dopo l’ultima dichiarazione.

Queste scelte riproducono la struttura dell’esempio d’esame.

Tutte le produzioni hanno un solo non terminale a sinistra: la grammatica è context-free.

---

## 4. Come vengono generate le liste

### Lista di identificatori

Le produzioni:

$$
I\to\mathtt{id}\mid\mathtt{id}\ \mathtt{,}\ I
$$

permettono di generare:

```text
id
id , id
id , id , id
```

Per esempio:

$$
I
\Rightarrow \mathtt{id},I
\Rightarrow \mathtt{id},\mathtt{id},I
\Rightarrow \mathtt{id},\mathtt{id},\mathtt{id}.
$$

Ogni applicazione ricorsiva introduce un identificatore seguito da una virgola.

Il caso base introduce l’ultimo identificatore, senza virgola finale.

### Lista di dichiarazioni

Le produzioni:

$$
L\to D\mid D\ \mathtt{;}\ L
$$

permettono di generare:

```text
D
D ; D
D ; D ; D
```

Per esempio:

$$
L\Rightarrow D;L
\Rightarrow D;D;L
\Rightarrow D;D;D.
$$

Il separatore compare soltanto tra due dichiarazioni.

> [!warning] Attenzione alla posizione del separatore
> La produzione $L\to D;L\mid D$ gestisce una lista separata da `;`.
>
> La produzione $L\to D;L\mid\varepsilon$ genera invece dichiarazioni tutte terminate da `;` e ammette anche la lista vuota.
>
> Le due grammatiche descrivono convenzioni diverse.

---

## 5. Come viene introdotto l’annidamento

La produzione decisiva è:

$$
T\to R.
$$

Essa permette a un tipo di essere un record completo.

Esiste quindi il ciclo:

$$
R\Rightarrow^*
\mathtt{record}\ I:T\ \mathtt{end}
\Rightarrow
\mathtt{record}\ I:R\ \mathtt{end}.
$$

Il nuovo $R$ può contenere un altro campo il cui tipo è ancora $R$, e così via.

Per esempio:

```pascal
record
  x: record
    y: record
      z: real
    end
  end
end
```

La grammatica non impone una profondità massima.

Ogni parola generata deriva comunque da un numero finito di applicazioni delle produzioni.

> [!important] Non basta elencare alcuni livelli
> Per consentire annidamento arbitrario serve una regola che richiami nuovamente la struttura record.
>
> La ricorsione $T\to R$ svolge questo ruolo senza duplicare tutte le produzioni.

---

## 6. Esempio della prova del 22 febbraio 2011

La struttura richiesta è:

```pascal
record
  r1, r2: integer;
  r3: record
    r4: real
  end;
  r5: real
end
```

Nel record esterno ci sono tre dichiarazioni:

1. `r1, r2: integer`;
2. `r3: record ... end`;
3. `r5: real`.

La prima dichiarazione introduce due campi.

Il campo `r3` ha come tipo un record contenente il campo `r4`.

### Passo 1: predisporre tre dichiarazioni esterne

Possiamo derivare:

```text
R
⇒ record L end
⇒ record D ; L end
⇒ record D ; D ; L end
⇒ record D ; D ; D end
```

Le tre occorrenze di $D$ saranno espanse separatamente.

### Passo 2: generare la prima dichiarazione

Per la prima occorrenza:

```text
D
⇒ I : T
⇒ id , I : T
⇒ id , id : T
⇒ id , id : integer
```

Ai due token `id` associamo i lessèmi `r1` e `r2`.

### Passo 3: generare il campo con tipo record

Per la seconda occorrenza:

```text
D
⇒ I : T
⇒ id : T
⇒ id : R
⇒ id : record L end
⇒ id : record D end
⇒* id : record id : real end
```

Il primo `id` corrisponde a `r3`; quello interno corrisponde a `r4`.

Il simbolo $\Rightarrow^*$ nell’ultimo passaggio abbrevia l’espansione della dichiarazione interna attraverso $D$, $I$ e $T$.

### Passo 4: generare l’ultima dichiarazione

Per la terza occorrenza:

```text
D
⇒ I : T
⇒ id : T
⇒ id : real
```

Il token `id` corrisponde a `r5`.

Combinando le espansioni otteniamo la sequenza terminale:

```text
record
  id , id : integer ;
  id : record id : real end ;
  id : real
end
```

Associando i lessèmi agli identificatori si ottiene il record richiesto.

> [!tip] Derivazione per componenti
> Questa è una presentazione della derivazione suddivisa per parti.
>
> Se il testo richiede espressamente una derivazione sinistra o destra, bisogna espandere le occorrenze nell’ordine corrispondente.

---

## 7. Perché la grammatica descrive la struttura corretta

Possiamo giustificarla partendo dai componenti più semplici.

### Identificatori

$I$ genera una lista non vuota di token `id`, con virgole soltanto tra elementi consecutivi.

### Dichiarazioni

$D$ genera una lista di identificatori, un solo `:` e un tipo.

### Tipi

$T$ genera `integer`, `real` oppure un record.

### Liste di dichiarazioni

$L$ genera una o più dichiarazioni, con `;` soltanto tra dichiarazioni consecutive.

### Record

$R$ racchiude la lista tra `record` ed `end`.

Ogni applicazione di:

$$
R\to\mathtt{record}\ L\ \mathtt{end}
$$

introduce insieme il delimitatore di apertura e quello di chiusura.

La ricorsione interna completa il record annidato prima di proseguire con la struttura che lo contiene.

Per dimostrare che ogni struttura ammessa è generabile, si ragiona sulla profondità:

- i record senza annidamento usano soltanto tipi elementari;
- un record più profondo usa $T\to R$ per i campi che contengono record;
- ciascun record interno viene costruito nello stesso modo.

---

## 8. Separare sintassi e vincoli semantici

La grammatica controlla la forma delle dichiarazioni, ma non verifica automaticamente tutte le condizioni sui nomi.

Per esempio:

```pascal
record
  x: integer;
  x: real
end
```

è generabile dalla grammatica.

Se il linguaggio vieta due campi con lo stesso nome nello stesso record, serve un controllo semantico aggiuntivo.

Tra i controlli semantici possono rientrare:

- unicità dei nomi nello stesso ambito;
- validità dell’accesso a un campo;
- compatibilità dei tipi nelle operazioni.

> [!important] Collegamento con gli attributi
> La grammatica descrive la struttura.
>
> Informazioni come nomi dichiarati, tipi e ambiti possono essere gestite durante l’analisi semantica, anche mediante attributi e tabelle dei simboli.

---

## 9. Variante equivalente con code di lista

Le produzioni delle liste possono essere riscritte introducendo una coda opzionale.

Per le dichiarazioni:

$$
L\to D\,L_1
$$

$$
L_1\to \mathtt{;}\ D\,L_1\mid\varepsilon.
$$

Per gli identificatori:

$$
I\to\mathtt{id}\,I_1
$$

$$
I_1\to\mathtt{,}\ \mathtt{id}\,I_1\mid\varepsilon.
$$

Le altre produzioni restano quelle già definite.

Questa riscrittura rende esplicito che:

- il primo elemento è obbligatorio;
- gli elementi successivi sono facoltativi;
- ogni elemento successivo è preceduto dal separatore.

La presenza di $\varepsilon$ nelle code non rende vuote le liste: $L$ deve comunque generare un primo $D$ e $I$ un primo `id`.

---

## 10. Errori frequenti e procedura d’esame

### Errori da evitare

- **Dimenticare $T\to R$.**  
  Si ottengono soltanto record con tipi elementari.

- **Usare lo stesso non terminale per identificatori e dichiarazioni.**  
  Le due liste hanno elementi e separatori differenti.

- **Inserire sempre `;` alla fine di una dichiarazione.**  
  Questo cambia la convenzione richiesta per l’ultima dichiarazione.

- **Ammettere una lista vuota senza verificarne la richiesta.**  
  Nel modello adottato ogni record contiene almeno un campo.

- **Trattare `r1`, `r2`, ecc. come gli unici nomi possibili.**  
  La grammatica usa il token generale `id`.

### Procedura da seguire

1. Individua parole chiave, separatori e delimitatori.
2. Distingui le liste dalle strutture annidate.
3. Assegna un non terminale a ogni componente.
4. Scrivi i casi base delle liste.
5. Introduci la ricorsione per aggiungere elementi.
6. Collega il tipo alla struttura record per consentire annidamento.
7. Verifica l’esempio richiesto con una derivazione.
8. Controlla liste minime e posizione dei separatori.

> [!important] Idea da ricordare
> La ricorsione nelle liste aggiunge elementi allo stesso livello.
> La ricorsione attraverso il tipo introduce un nuovo livello di annidamento.
>
> Nei record servono entrambe.