---
tags: [basi-di-dati, modello-relazionale, vincoli, feedback, ripasso]
fonte: feedback2_2627.pdf (Prof. V. de Nitto Personè, 04/11/26)
---

# Feedback 2 — modello relazionale e vincoli (ripasso)

> [!info] Fonte
> Slides *"Feedback 2"* — Prof. Vittoria de Nitto Personè (classe a.a. 26/27).
> Approfondisce: [[Modello relazionale]] · [[SQL - Definizione dei dati (DDL)]] · [[00 Indice|Indice]]

## 1. Le domande poste alla classe
1. Considerando il modello relazionale, spiegare in modo chiaro e sintetico
   a. **cosa è** (NON una sua rappresentazione)
   b. **quale obiettivo** ha
   c. **in quale fase del progetto** si colloca
2. Indicare una richiesta di chiarimento sul modello relazionale.

**Risposte**: 49 (feedback 1: 52 risposte).
Esito della domanda 1 (definizione, obiettivi, livello progettuale): **NO: 9 — SI: 16 — NI: 24** (totale 49 studenti).

## 2. Risposta corretta: il modello relazionale

> [!success] Modello relazionale
> - **Cosa è**: è un **modello per l'organizzazione dei dati**; in particolare, i dati sono organizzati in **relazioni**.
> - **Obiettivo**: **facilitare l'accesso efficiente ai dati**, consentendo l'**indipendenza dall'organizzazione fisica**.
> - **Fase del progetto**: dopo la **raccolta dei requisiti** e il **progetto concettuale** viene la fase di **progettazione logica**, in cui si utilizza il modello relazionale.

(Vedi anche [[Modello Entity-Relationship#4. Metodologia di progetto]].)

## 3. Richieste di chiarimento emerse
- differenza tra vincoli **intra/interrelazionali**
- **organizzazione del progetto**
- **query nidificate** → [[SQL - Interrogazioni e aggiornamenti (DML)#7. Interrogazioni nidificate]]
- **doppia negazione** → [[Algebra relazionale#12. Esempi di interrogazioni|Algebra relazionale (esempio e)]] e [[SQL - Interrogazioni e aggiornamenti (DML)#8. Quantificazione esistenziale EXISTS|SQL: EXISTS]]
- definizione e distinzione tra **K, SK, FK e K primaria**
- **aggiornamento delle tabelle con vincoli** in caso di operazioni non permesse → [[SQL - Definizione dei dati (DDL)#4. Politiche di reazione (integrità referenziale)]]

---

## 4. Differenza tra vincoli intra/interrelazionali

### Vincolo di integrità
> [!abstract] Definizione
> È una **condizione che si esprime a livello di schema** e che si intende debba essere **soddisfatta da quelle istanze** della base di dati che rappresentano **situazioni corrette** per l'applicazione.

- Ogni vincolo può essere visto come una **funzione booleana (o un predicato)** che associa ad ogni istanza della base di dati il valore **vero** (nel caso in cui il vincolo sia soddisfatto) o **falso** (altrimenti).
- Ad uno schema associamo un **insieme di vincoli** e consideriamo **corrette** (o anche *lecite, legali, valide, ammissibili*) **solo le istanze che soddisfano tutti i vincoli**.
- I vincoli **scaturiscono dalle regole di vincolo** nella documentazione ottenuta dalla **fase concettuale** (vedi [[Modello ER esteso#6. Vincoli non esprimibili]]).

### Vincoli di ennupla
**Stipendi**: (Rossi, 55.000, 12.500, 42.500), (Neri, 45.000, 10.000, 35.000), (Bruni, **50.000**, 11.000, 36.000) — vincolo `Lordo = (Ritenute + Netto)` (violato da Bruni).

```sql
CREATE TABLE Stipendi(
   Impiegato CHAR(20) PRIMARY KEY,
   Ritenute  NUMERIC(9),
   Netto     NUMERIC(9),
   Lordo     NUMERIC(9),
   CHECK (Lordo = Ritenute + Netto)
)
```

### Vincolo di dominio
**Esami**: (276545, **32**, , 01), (276545, 30, e lode, 02), (787643, **27, e lode**, 03), (739430, 24, , 04)

- vincolo di **dominio**: $(\text{Voto} \ge 18) \text{ AND } (\text{Voto} \le 30)$
- vincolo di **ennupla**: $(\text{Voto} = 30) \text{ OR NOT } (\text{Lode} = \text{"e lode"})$

```sql
CREATE TABLE Esami(
   ....
   CHECK ( Voto >= 18 AND Voto <= 30 ),
   Lode CHAR(6),
   CHECK (Voto = 30 OR NOT Lode = 'e lode')   -- è un vincolo di ennupla
)
```

### Vincolo di integrità referenziale
Dallo schema E-R MUSICISTA (CF, Nome, Cognome) —(1,n)— *Suona* (Anno inizio) —(0,n)— STRUMENTO (Nome, Tipo) si ottiene:
- **Musicisti**(<u>CF</u>, Nome, Cognome)
- **Strumenti**(<u>Nome</u>, Tipo)
- **Suona**(<u>Musicista, Strumento</u>, Anno inizio) — `Musicista` → FK verso Musicisti.CF; `Strumento` → FK verso Strumenti.Nome

### Differenza
> [!important] Intra vs inter
> - **vincoli intrarelazionali**: proprietà che devono essere soddisfatte dai dati di **ogni istanza di una relazione** (dominio, ennupla, chiave, superchiave, chiave primaria);
> - **vincoli interrelazionali**: proprietà «**relative**» dei dati di **relazioni diverse** (integrità referenziale / chiave esterna).

---

## 5. Definizione e distinzione tra K, SK, FK e K primaria

### Vincolo di chiave (intrarelazionale)
È un'**asserzione** che specifica che un insieme di attributi forma una **chiave** per una relazione.
Se in una relazione $R(A,B,C,D)$ dichiaro un vincolo di chiave su $\{A,B\}$, sto asserendo che:
- in **tutte le istanze** della base di dati, **non esistono due tuple** della relazione R che **coincidono negli attributi A e B**;
- **nessun sottoinsieme proprio** di $\{A,B\}$ è una chiave (minimalità).

### Vincolo di superchiave (intrarelazionale)
È un'asserzione che specifica che un insieme di attributi forma una **superchiave** per una relazione.
Se in $R(A,B,C,D)$ dichiaro un vincolo di superchiave su $\{A,B\}$, sto asserendo che in tutte le istanze della base di dati **non esistono due tuple** della relazione R che **coincidono negli attributi A e B**.

> [!warning] Nota
> La slide sulla superchiave ripete anche la frase "e sto anche asserendo che nessun sottoinsieme proprio di {A,B} è una chiave": per la **superchiave** però **non è richiesta la minimalità** (vedi la definizione in [[Modello relazionale#8. Superchiavi e chiavi]]: la chiave è una *superchiave minimale*). La differenza tra chiave e superchiave sta proprio nella minimalità.

### Vincolo di chiave primaria (intrarelazionale)
È un'asserzione che specifica che:
- un insieme di attributi **formano una chiave** per una relazione **e**
- **non si ammettono valori nulli** per tali attributi.

> [!important] **Un solo vincolo di chiave primaria è ammesso per una relazione.**

### Vincolo di chiave esterna e integrità referenziale (interrelazionale)
- Informazioni in relazioni diverse sono **correlate attraverso valori comuni**.
- Le correlazioni debbono essere "**coerenti**".

**Chiave esterna** di uno schema $R_1$: **non è una chiave per $R_1$**, ma è **chiave (primaria) in un altro schema $R_2$**; quindi ha la funzione, quando definita in una tupla di $R_1$, di **collegare quei dati con i dati della tupla identificata da quella chiave in $R_2$**.

Il **vincolo di integrità referenziale (v.i.r.)** riguarda i valori della chiave esterna: **non è possibile utilizzare valori della FK se non esiste la tupla in $R_2$ con quella chiave (primaria)**, come dire: non esistono i dati «principali».

**Esempio** — Infrazioni(Codice, Data, Vigile, Prov, Numero), Vigili(Matricola, Cognome, Nome), Auto(Prov, Numero, Cognome, Nome): `Vigile` è FK verso Vigili, `(Prov, Numero)` è FK verso Auto (dati in [[Modello relazionale#Esempio: Vigili e Infrazioni]]).

### Riepilogo
| Concetto | Tipo di vincolo | Unicità | Minimalità | NULL ammessi | Quanti per relazione |
|---|---|---|---|---|---|
| **Superchiave (SK)** | intrarelazionale | sì | no | — | molte |
| **Chiave (K)** | intrarelazionale | sì | **sì** | sì (da limitare) | una o più |
| **Chiave primaria** | intrarelazionale | sì | sì | **no** | **una sola** |
| **Chiave esterna (FK)** | **interrelazionale** | no (non è chiave in $R_1$) | — | sì | zero o più |
