---
tags: [basi-di-dati, sql, dml, interrogazioni]
fonte: lect4BDsql_I_2627.pdf, lect4BDsql_II_2627.pdf — Prof. V. de Nitto Personè
---

# SQL come DML — interrogazioni e aggiornamenti

> [!info] Fonte
> Slides *"SQL: concetti base"* (parte DML) e *"SQL"* (parte II) — Prof. Vittoria de Nitto Personè (a.a. 2026/27).
> Collegamenti: [[SQL - Definizione dei dati (DDL)]] · [[Algebra relazionale]] · [[00 Indice|Indice]]

## 0. SQL, operazioni sui dati
- **interrogazione**: `SELECT`
- **modifica**: `INSERT`, `DELETE`, `UPDATE`

---

## 1. Istruzione SELECT (versione base)

```sql
SELECT ListaAttributi
FROM ListaTabelle
[ WHERE Condizione ]
```
- clausola **SELECT** (chiamata **target list**) → **cosa** si vuole come risultato
- clausola **FROM** → **da dove** si prende
- clausola **WHERE** → **che condizioni** deve soddisfare

### Base dati di esempio

**Persone**

| Nome | Età | Reddito |
|---|---|---|
| Andrea | 27 | 21 |
| Aldo | 25 | 15 |
| Maria | 55 | 42 |
| Anna | 50 | 35 |
| Filippo | 26 | 30 |
| Luigi | 50 | 40 |
| Franco | 60 | 20 |
| Olga | 30 | 41 |
| Sergio | 85 | 35 |
| Luisa | 75 | 87 |

**Maternità**(Madre, Figlio): (Luisa, Maria), (Luisa, Luigi), (Anna, Olga), (Anna, Filippo), (Maria, Andrea), (Maria, Aldo)
**Paternità**(Padre, Figlio): (Sergio, Franco), (Luigi, Olga), (Luigi, Filippo), (Franco, Andrea), (Franco, Aldo)

### Selezione e proiezione
Nome e reddito delle persone con meno di trenta anni:
```sql
SELECT Nome, Reddito
FROM Persone
WHERE Età < 30
```
1. WHERE seleziona: (Andrea, 27, 21), (Aldo, 25, 15), (Filippo, 26, 30)
2. SELECT proietta: (Andrea, 21), (Aldo, 15), (Filippo, 30)

### SELECT, abbreviazioni
```sql
SELECT Nome, Reddito          -- "as" opzionale
FROM Persone
WHERE Età < 30
```
equivale a
```sql
SELECT p.Nome as nome, p.Reddito as reddito
FROM Persone as p
WHERE p.Età < 30
```
(`as` introduce un alias per tabelle e attributi; è **opzionale**.)

### Selezione, senza proiezione
Nome, età e reddito delle persone con meno di trenta anni:
```sql
SELECT *
FROM Persone
WHERE Età < 30
```
`SELECT *` è un'abbreviazione di `SELECT Nome, Età, Reddito` (tutti gli attributi).

### Proiezione, senza selezione
Nome e reddito di tutte le persone:
```sql
SELECT Nome, Reddito
FROM Persone
```
(senza WHERE)

### Espressioni nella target list
```sql
SELECT Reddito/2 as redditoSemestrale
FROM Persone
WHERE Nome = 'Luigi'
```
→ si seleziona (Luigi, 50, 40) → risultato: `redditoSemestrale = 20`.

### Condizione complessa
```sql
SELECT *
FROM Persone
WHERE Reddito > 25
  and (Età < 30 or Età > 60)
```

### Condizione `LIKE`
Le persone che hanno un nome che **inizia per 'A'** e ha una **'d' come terza lettera**:
```sql
SELECT *
FROM Persone
WHERE Nome like 'A_d%'
```
- `LIKE` → confronto di stringhe
- `_` → un carattere arbitrario
- `%` → una stringa di qualsiasi lunghezza (anche 0)

### Gestione dei valori nulli
**Impiegati**(Matricola, Cognome, Filiale, Età): (7309, Rossi, Roma, 32), (5998, Neri, Milano, 45), (9553, Bruni, Milano, NULL)

Gli impiegati la cui età **è o potrebbe essere** maggiore di 40:
```sql
SELECT *
FROM Impiegati
WHERE Età > 40 or Età is null
```
(vedi [[Algebra relazionale#14. Selezione con valori nulli]])

### Proiezione, attenzione: duplicati
Cognome e filiale di tutti gli impiegati — Impiegati: (7309, Neri, Napoli, 55), (5998, Neri, Milano, 64), (9553, Rossi, Roma, 44), (5698, Rossi, Roma, 64)

```sql
SELECT Cognome, Filiale        SELECT distinct Cognome, Filiale
FROM Impiegati                 FROM Impiegati
```

| senza distinct | con distinct |
|---|---|
| Neri, Napoli | Neri, Napoli |
| Neri, Milano | Neri, Milano |
| Rossi, Roma | Rossi, Roma |
| Rossi, Roma | |

In algebra $\pi_{\text{Cognome, Filiale}}(\text{Impiegati})$ elimina sempre i duplicati: **SQL ≠ AlgRel** (SQL **non** elimina i duplicati, a meno di `distinct`).

---

## 2. SQL e algebra relazionale

$R_1(A_1, A_2)$, $R_2(A_3, A_4)$
```sql
SELECT distinct R1.A1, R2.A4
FROM R1, R2
WHERE R1.A2 = R2.A3
```
Semantica:
1. **prodotto cartesiano** (FROM)
2. **selezione** (WHERE)
3. **proiezione** (SELECT)

$$\pi_{A_1, A_4}\big(\sigma_{A_2 = A_3}(R_1 \bowtie R_2)\big)$$
(qui $R_1 \bowtie R_2$ è un prodotto cartesiano perché non ci sono attributi comuni)

- possono essere necessarie **ridenominazioni**:
  - nel **prodotto cartesiano** (variabili/alias di tabella, es. la stessa tabella usata più volte)
  - nella **target list**
```sql
SELECT X.A1 AS B1, ...
FROM R1 X, R2 Y, R1 Z
WHERE X.A2 = Y.A3 AND ...
```

### SQL: esecuzione delle interrogazioni
- Le espressioni SQL sono **dichiarative** e noi ne stiamo vedendo la **semantica**.
- In pratica, i DBMS eseguono le operazioni in modo **efficiente**, ad esempio:
  - eseguono le **selezioni al più presto**;
  - se possibile, eseguono **join e non prodotti cartesiani**.

### SQL: specifica delle interrogazioni
- La capacità dei DBMS di "**ottimizzare**" le interrogazioni rende (di solito) **non necessario preoccuparsi dell'efficienza** quando si specifica un'interrogazione.
- È perciò più importante preoccuparsi della **chiarezza** (anche perché così è più difficile sbagliare…).

---

## 3. Selezione, proiezione e join

### I padri di persone che guadagnano più di 20
$$\pi_{\text{Padre}}\big(\text{Paternità} \bowtie_{\text{Figlio}=\text{Nome}} \sigma_{\text{Reddito}>20}(\text{Persone})\big)$$
```sql
SELECT distinct Padre
FROM Persone, Paternita
WHERE Figlio = Nome and Reddito > 20
```

### Le persone che guadagnano più dei rispettivi padri (nome, reddito e reddito del padre)
$$\pi_{\text{Nome, Reddito, RP}}\Big(\sigma_{\text{Reddito}>\text{RP}}\big(\rho_{\text{NP,EP,RP} \leftarrow \text{Nome,Età,Reddito}}(\text{Persone}) \bowtie_{\text{NP}=\text{Padre}} (\text{Paternità} \bowtie_{\text{Figlio}=\text{Nome}} \text{Persone})\big)\Big)$$
```sql
SELECT f.Nome, f.Reddito, p.Reddito
FROM Persone p, Paternita, Persone f
WHERE p.Nome = Padre and
      Figlio = f.Nome and
      f.Reddito > p.Reddito
```

### SELECT, con ridenominazione del risultato
```sql
SELECT Figlio, f.Reddito as reddito,
       p.Reddito as redditoPadre
FROM Persone p, Paternita, Persone f
WHERE p.Nome = Padre and Figlio = f.Nome
      and f.Reddito > p.Reddito
```

### Impiegati che guadagnano più del proprio capo
(Stesse tabelle Impiegati/Supervisione di [[Algebra relazionale#12. Esempi di interrogazioni]], esempio d.)
$$\pi_{\text{Matr,Nome,Stip,MatrC,NomeC,StipC}}\Big(\sigma_{\text{Stipendio}>\text{StipC}}\big(\rho_{\text{MatrC,NomeC,EtàC,StipC} \leftarrow \text{Matr,Nome,Età,Stip}}(\text{Impiegati}) \bowtie_{\text{MatrC}=\text{Capo}} (\text{Supervisione} \bowtie_{\text{Impiegato}=\text{Matricola}} \text{Impiegati})\big)\Big)$$
```sql
SELECT ImpC.Matricola AS MatrC,
       ImpC.Nome      AS NomeC,
       ImpC.Stipendio AS StipC,
       Matricola      AS MatrI,
       Nome           AS NomeI,
       Stipendio      AS StipI
FROM Impiegati ImpC, Supervisione, Impiegati
WHERE Impiegato = Matricola AND Capo = ImpC.Matricola
      AND Stipendio > ImpC.Stipendio
```
| MatrC | NomeC | StipC | MatrI | NomeI | StipI |
|---|---|---|---|---|---|
| 5698 | Bruni | 42 | 7309 | Rossi | 45 |

---

## 4. Ordinamento del risultato
Nome e reddito delle persone con meno di trenta anni **in ordine alfabetico**:
```sql
SELECT Nome, Reddito
FROM Persone
WHERE Età < 30
order by Nome
```
| senza order by | con order by Nome |
|---|---|
| Andrea 21 | Aldo 15 |
| Aldo 15 | Andrea 21 |
| Filippo 30 | Filippo 30 |

---

## 5. Join esplicito

### Join naturale dell'algebra: Paternità ⋈ Maternità
```sql
SELECT Madre, Paternita.Figlio, Padre
FROM Maternita join Paternita
WHERE Paternita.Figlio = Maternita.Figlio
```

### Madre e padre di ogni persona
```sql
SELECT Maternita.Figlio, Madre, Padre
FROM Maternita, Paternita
WHERE Maternita.Figlio = Paternita.Figlio
```
oppure, con **join esplicito** (la condizione di join va nella clausola `on`):
```sql
SELECT Maternita.Figlio, Madre, Padre
FROM Maternita join Paternita on Maternita.Figlio = Paternita.Figlio
```

### Ulteriore estensione: join naturale (meno diffuso)
```sql
SELECT Madre, Paternita.Figlio, Padre
FROM Maternita join Paternita on Paternita.Figlio = Maternita.Figlio
```
```sql
SELECT Madre, Figlio, Padre
FROM Maternita natural join Paternita
```
> [!warning] `natural join` è **sconsigliabile**. Supporto: **mimer OK, DB2 no**.

### Join esterno: "outer join"
Padre e, **se nota**, madre di ogni persona:
```sql
SELECT Paternita.Figlio, Padre, Madre
FROM Paternita left join Maternita
     on Paternita.Figlio = Maternita.Figlio
```
```sql
SELECT Paternita.Figlio, Padre, Madre
FROM Paternita left outer join Maternita
     on Paternita.Figlio = Maternita.Figlio
```
- `outer` è **opzionale**.

### Outer join: che cosa produce?
```sql
SELECT Paternita.Figlio, Padre, Madre
FROM Maternita join Paternita on Maternita.Figlio = Paternita.Figlio

SELECT Paternita.Figlio, Padre, Madre
FROM Maternita left outer join Paternita on Maternita.Figlio = Paternita.Figlio

SELECT Paternita.Figlio, Padre, Madre
FROM Maternita full outer join Paternita on Maternita.Figlio = Paternita.Figlio
```

**join (interno)**:

| Paternita.Figlio | Padre | Madre |
|---|---|---|
| Olga | Luigi | Anna |
| Filippo | Luigi | Anna |
| Andrea | Franco | Maria |
| Aldo | Franco | Maria |

**full outer join**:

| Paternita.Figlio | Padre | Madre |
|---|---|---|
| Maria | null | Luisa |
| Luigi | null | Luisa |
| Olga | Luigi | Anna |
| Filippo | Luigi | Anna |
| Andrea | Franco | Maria |
| Aldo | Franco | Maria |
| Franco | Sergio | null |

(Nelle slide le righe Maria e Luigi mostrano il figlio, anche se, a rigore, `Paternita.Figlio` per quelle righe sarebbe NULL.)
Il **left outer join** (Maternita a sinistra) produce le righe del join più quelle con Madre nota e Padre NULL (Maria, Luigi).

---

## 6. Operatori insiemistici

### Unione
```sql
SELECT A, B          SELECT A, B
FROM R               FROM R
union                union all
SELECT A, B          SELECT A, B
FROM S               FROM S
```
- `union` **elimina i duplicati**!
- `union all` li mantiene.

### Notazione posizionale!
```sql
select madre, figlio
from maternita
union
select padre, figlio
from paternita
```
- quali nomi per gli attributi del risultato?
  - inventati o nessuno
  - quelli del primo operando
  - …

Risultato (attributi Madre, Figlio — quelli del primo operando): (Luisa, Maria), (Luisa, Luigi), (Anna, Olga), (Anna, Filippo), (Maria, Andrea), (Maria, Aldo), (Sergio, Franco), (Luigi, Olga), (Luigi, Filippo), (Franco, Andrea), (Franco, Aldo).

### Notazione posizionale, 2
```sql
select madre, figlio      select madre, figlio
from maternita            from maternita
union                     union
select figlio, padre      select padre, figlio
from paternita            from paternita
```
Il primo **è sbagliato**: le colonne vengono accoppiate **per posizione**, non per nome (figlio finirebbe sotto madre).

### Notazione posizionale, 3
Anche con le ridenominazioni **non cambia niente**:
```sql
select madre as genitore, figlio
from maternita
union
select figlio, padre as genitore
from paternita
```
**Corretta**:
```sql
select madre as genitore, figlio
from maternita
union
select padre as genitore, figlio
from paternita
```

### Differenza
```sql
select Nome
from Impiegato
except
select Cognome as Nome      -- "as Nome" non è necessaria!
from Impiegato
```
- (supportato **solo DB2** tra i sistemi considerati)
- vedremo che si può esprimere con **select nidificate**.

### Intersezione
```sql
select Nome
from Impiegato
intersect
select Cognome as Nome
from Impiegato
```
- (solo DB2) — equivale a
```sql
select I.Nome
from Impiegato I, Impiegato J
where I.Nome = J.Cognome
```

---

## 7. Interrogazioni nidificate

Quando la condizione (WHERE) **contiene a sua volta una query**, si ha la **nidificazione**.

### Nome e reddito del padre di Franco
```sql
select Nome, Reddito
from Persone, Paternita
where Nome = Padre and Figlio = 'Franco'
```
```sql
select Nome, Reddito
from Persone
where Nome = ( select Padre
               from Paternita
               where Figlio = 'Franco')
```

### Commenti
- La forma nidificata è "**meno dichiarativa**", ma talvolta **più leggibile** (richiede meno variabili).
- La forma piana e quella nidificata possono essere **combinate**.
- Le sottointerrogazioni **non possono contenere operatori insiemistici** ("l'unione si fa solo al livello esterno"); la limitazione non è significativa.

### Nome e reddito dei padri di persone che guadagnano più di 20
Forma piana:
```sql
select distinct P.Nome, P.Reddito
from Persone P, Paternita, Persone F
where P.Nome = Padre and Figlio = F.Nome
      and F.Reddito > 20
```
Forma nidificata:
```sql
select Nome, Reddito
from Persone
where Nome in (select Padre
               from Paternita
               where Figlio = any (select Nome
                                   from Persone
                                   where Reddito > 20))
```
Passo per passo:
1. sottoquery più interna → persone con reddito > 20: Andrea, Maria, Anna, Filippo, Luigi, Olga, Sergio, Luisa
2. padri di queste persone (Figlio = any …) → Luigi (Olga, Filippo), Franco (Andrea)
3. nome e reddito → Luigi 40, Franco 20

- **notare la `distinct`** nella forma piana (un padre con più figli comparirebbe più volte);
- **`in` ≅ `= any`**; **`not in` ≅ `<> all`**;
- **disomogeneità**: nella stessa query si usano `in` e `= any` per esprimere la stessa cosa.

**Forma combinata**:
```sql
select Nome, Reddito
from Persone
where Nome in (select Padre
               from Paternita, Persone
               where Figlio = Nome
                 and Reddito > 20)
```

### … con indicazione del reddito del figlio
```sql
select distinct P.Nome, P.Reddito, F.Reddito
from Persone P, Paternita, Persone F
where P.Nome = Padre and Figlio = F.Nome
      and F.Reddito > 20
```
```sql
select Nome, Reddito, ????
from Persone
where Nome in (select Padre
               from Paternita
               where Figlio = any (select Nome
                                   from Persone
                                   where Reddito > 20))
```
→ con la forma nidificata **non è possibile**: gli attributi delle sottoquery non sono visibili nella target list esterna.

### Regole di visibilità
- **non è possibile** fare riferimenti a variabili definite in **blocchi più interni**;
- se un nome di variabile è omesso, si assume riferimento alla variabile più "**vicina**";
- in un blocco si **può** fare riferimento a variabili definite in **blocchi più esterni**; in tal caso la semantica base (prodotto cartesiano, selezione, proiezione) **non funziona più** (→ interrogazioni correlate).

Esempio (con `Impiegato(Matricola, Nome, Cognome, Dipart, Stipendio)` e `Dipartimento(NomeDip, Citta, …)`):
```sql
select *
from Impiegato
where Dipart in (select NomeDip
                 from Dipartimento D1
                 where NomeDip = 'Produzione') or
      Dipart in (select NomeDip
                 from Dipartimento D2
                 where D2.Citta = D1.Citta)
```
> [!danger] **Visibilità**: la query è **scorretta**: `D1` è definita nella prima sottoquery e **non è visibile** nella seconda (che è un blocco "fratello", non interno).

---

## 8. Quantificazione esistenziale EXISTS
Ulteriore tipo di condizione:
- **`EXISTS (Sottoespressione)`** = **true** se il risultato della sottoespressione è **non vuoto**.

### Le persone che hanno almeno un figlio
```sql
select *
from Persone
where exists ( select *
               from Maternità
               where Madre = Nome)
   or
      exists ( select *
               from Paternità
               where Padre = Nome)
```

### I padri i cui figli guadagnano **tutti** più di 20
```sql
select distinct Padre
from Paternita Z
where not exists (
      select *
      from Paternita W, Persone
      where W.Padre = Z.Padre
        and W.Figlio = Nome
        and Reddito <= 20)
```
(doppia negazione: *non esiste* un figlio di Z.Padre che guadagna ≤ 20)

> [!danger] Versione sbagliata — **NO!!!**
> ```sql
> select distinct Padre
> from Paternita
> where not exists (
>       select *
>       from Persone
>       where Figlio = Nome
>         and Reddito <= 20)
> ```
> Qui la sottoquery è valutata per ogni **riga** (Padre, Figlio) di Paternità, cioè per ogni singolo figlio: si ottengono i padri che hanno **almeno un** figlio con reddito > 20, non quelli con **tutti** i figli sopra 20.

### Semantica delle espressioni "correlate"
- L'interrogazione **interna viene eseguita una volta per ciascuna ennupla** dell'interrogazione esterna.

**Interpretazione semplificata**:
- l'interrogazione interna venga eseguita **prima** della esterna (possibile **solo se non vi è passaggio di binding**);
- **passaggio di binding**:
  1. esegui il prodotto cartesiano delle tabelle;
  2. a ciascuna riga risultante dal prodotto applica la condizione della clausola where;
     - se questa è una nuova select, **va valutata per ogni riga** della tabella prodotta in 1.

### Disgiunzione e unione (ma non sempre)
```sql
select * from Persone where Reddito > 30
union
select F.*
from Persone F, Paternita, Persone P
where F.Nome = Figlio and Padre = P.Nome
  and P.Reddito > 30
```
equivale a
```sql
select *
from Persone F
where Reddito > 30 or
      exists (select *
              from Paternita, Persone P
              where F.Nome = Figlio and Padre = P.Nome
                and P.Reddito > 30)
```

### Differenza e nidificazione
`Impiegato(Matricola, Nome, Cognome, Dipart, Stipendio)`
```sql
select Nome from Impiegato
except
select Cognome as Nome from Impiegato
```
equivale a
```sql
select Nome
from Impiegato I
where not exists (select *
                  from Impiegato
                  where Cognome = I.Nome)
```

---

## 9. Operatori aggregati
- **importante estensione dell'algebra relazionale**;
- si applicano su **insiemi di tuple**.

### COUNT
Il numero di figli di Franco:
```sql
select count(*) as NumFigliDiFranco
from Paternita
where Padre = 'Franco'
```
- l'operatore aggregato (count) viene **applicato al risultato** dell'interrogazione `select * from Paternita where Padre = 'Franco'` → (Franco, Andrea), (Franco, Aldo) → **NumFigliDiFranco = 2**.

### COUNT: DISTINCT e valori NULL

| Istruzione | Significato |
|---|---|
| `select count(*) from R` | conta le tuple di R |
| `select count(A) from R` | conta le tuple con A **not Null** |
| `select count(distinct A) from R` | conta le tuple con **diverso valore** di A (non nulli) |

Esempio 1 — Persone: (Andrea, 27, 21), (Aldo, 25, 35), (Maria, 55, 21), (Anna, 50, 35)
- `count(*)` = 4; `count(reddito)` = 4; `count(distinct reddito)` = 2

Esempio 2 — Persone: (Andrea, 27, 21), (Aldo, 25, **NULL**), (Maria, 55, 21), (Anna, 50, 35)
- `count(*)` = 4; `count(reddito)` = 3; `count(distinct reddito)` = 2

### Altri operatori aggregati: SUM, AVG, MAX, MIN
Media dei redditi dei figli di Franco:
```sql
select avg(reddito)
from persone join paternita on nome = figlio
where padre = 'Franco'
```

### Operatori aggregati e valori nulli
```sql
select avg(reddito) as redditomedio
from persone
```
Persone: (Andrea, 27, 30), (Aldo, 25, NULL), (Maria, 55, 36), (Anna, 50, 36)
→ i NULL vengono **ignorati**: media = (30 + 36 + 36) / 3 = **34**.

### Operatori aggregati e target list
Vogliamo trovare la/le persona/e di reddito massimo. Un'interrogazione **scorretta**:
```sql
select nome, max(reddito)
from persone
```
- **disomogeneità**: di chi sarebbe il nome? **La target list deve essere omogenea**.

Corretta (solo aggregati):
```sql
select min(eta), avg(reddito)
from persone
```

### Massimo e nidificazione
La persona (o le persone) con il reddito massimo:
```sql
select *
from persone
where reddito = ( select max(reddito)
                  from persone)
```

---

## 10. Raggruppamenti: `GROUP BY`
- Le funzioni aggregate possono essere applicate a **partizioni** delle relazioni.
- Clausola **`GROUP BY listaAttributi`**.

### Il numero di figli di ciascun padre
```sql
select Padre, count(*) AS NumFigli
from paternita
group by Padre
```
1. `select * from paternita` → tutte le righe
2. `group by Padre` → partizioni: {Sergio–Franco}, {Luigi–Olga, Luigi–Filippo}, {Franco–Andrea, Franco–Aldo}
3. conteggio per gruppo:

| Padre | NumFigli |
|---|---|
| Sergio | 1 |
| Luigi | 2 |
| Franco | 2 |

### Group by e valori nulli
R(A, B) = (1, 11), (2, 11), (3, null), (4, null)

| Query | Risultato |
|---|---|
| `select B, count(*) from R group by B` | (11, 2), (null, 2) |
| `select A, count(*) from R group by A` | (1,1), (2,1), (3,1), (4,1) |
| `select A, count(B) from R group by A` | (1,1), (2,1), (3,0), (4,0) |
| `select B, count(B) from R group by B` | **?** → (11, 2), (null, 0) |

(I NULL formano un **gruppo** a sé; `count(B)` non conta i NULL.)

### Raggruppamenti e target list
Vogliamo calcolare la **media dei redditi dei figli** (per padre, mostrando anche il reddito del padre).

**Scorretta**:
```sql
select padre, avg(f.reddito), p.reddito
from persone f join paternita on figlio = f.nome
     join persone p on padre = p.nome
group by padre
```
**Corretta**:
```sql
select padre, avg(f.reddito), p.reddito
from persone f join paternita on figlio = f.nome
     join persone p on padre = p.nome
group by padre, p.reddito
```
> [!important] Nella target list di una query con GROUP BY possono comparire **solo** attributi presenti nel GROUP BY e funzioni aggregate.

Passaggi: senza raggruppamento `select padre, f.reddito, p.reddito …` dà una riga per figlio; raggruppando per (padre, p.reddito) si ottiene una riga per padre con la media dei redditi dei figli.

### Condizioni sui gruppi: `HAVING`
I padri i cui figli hanno un **reddito medio maggiore di 25**; mostrare padre e reddito medio dei figli:
```sql
select padre, avg(f.reddito)
from persone f join paternita on figlio = nome
group by padre
having avg(f.reddito) > 25
```

**Come viene eseguita?**
1. `select padre, f.reddito from persone f join paternita on figlio = nome` (join)
2. `group by padre` (partizionamento)
3. calcolo di `avg(f.reddito)` per ogni gruppo e filtro `having avg(f.reddito) > 25`

### WHERE o HAVING?
I padri i cui **figli sotto i 30 anni** hanno un reddito medio maggiore di 20:
```sql
select padre, avg(f.reddito)
from persone f join paternita on figlio = nome
where eta < 30
group by padre
having avg(f.reddito) > 20
```
- **WHERE**: condizioni sulle **singole ennuple** (prima del raggruppamento);
- **HAVING**: condizioni sui **gruppi** (con funzioni aggregate).

---

## 11. Operazioni di aggiornamento
- operazioni di
  - **inserimento**: `insert`
  - **eliminazione**: `delete`
  - **modifica**: `update`
- di **una o più ennuple** di una relazione
- sulla base di una **condizione** che può coinvolgere anche **altre relazioni**.

### Inserimento
```sql
INSERT INTO Tabella [ ( Attributi ) ]
       VALUES ( Valori )
```
oppure
```sql
INSERT INTO Tabella [ ( Attributi ) ]
       SELECT ...
```
Esempi su Persone(Nome, Età, Reddito):
```sql
INSERT INTO Persone VALUES ('Mario', 25, 52)

INSERT INTO Persone(Nome, Eta, Reddito)
       VALUES ('Pino', 25, 52)

INSERT INTO Persone(Nome, Reddito)
       VALUES ('Lino', 55)            -- Età = NULL (o default)

INSERT INTO Persone ( Nome )
SELECT Padre
FROM Paternita
WHERE Padre NOT IN (SELECT Nome
                    FROM Persone)
```

### Eliminazione di ennuple
```sql
DELETE FROM Tabella
  [ WHERE Condizione ]
```
```sql
DELETE FROM Persone
WHERE Eta < 35

DELETE FROM Paternita
WHERE Figlio NOT in ( SELECT Nome
                      FROM Persone)

DELETE FROM Paternita      -- elimina TUTTE le ennuple
```

**Commenti**:
- l'eliminazione di tuple **può causare eliminazioni da altre relazioni** (se i vincoli di integrità referenziale sono definiti con politiche di reazione **cascade**):
```sql
CREATE TABLE Impiegati(
   Matricola CHAR(6) PRIMARY KEY,
   Cognome   CHAR(20) NOT NULL UNIQUE,
   Progetto  CHAR(6)
             REFERENCES Progetti(Codice)
             on delete cascade
             on update cascade
)
```
- ricordare: **se la where viene omessa, si intende `where true`**.

### Modifica di ennuple
```sql
UPDATE NomeTabella
SET Attributo = < Espressione | SELECT … | NULL | DEFAULT >
  [ WHERE Condizione ]
```
```sql
UPDATE Persone SET Reddito = 45
  WHERE Nome = 'Piero'

UPDATE Persone
  SET Reddito = Reddito * 1.1
  WHERE Eta < 30
```
Persone prima: (Andrea, 27, 21), (Piero, 25, NULL), (Maria, 55, 21), (Anna, 50, 35)
Dopo i due update: Andrea **23,1**; Piero **49,5** (45 × 1,1); Maria 21; Anna 35.
