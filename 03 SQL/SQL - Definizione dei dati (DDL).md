---
tags: [basi-di-dati, sql, ddl]
fonte: lect4BDsql_I_2627.pdf (+ ripresa in lect4BDsql_II_2627.pdf) — Prof. V. de Nitto Personè
---

# SQL — concetti base e definizione dei dati (DDL)

> [!info] Fonte
> Slides *"SQL: concetti base"* — Prof. Vittoria de Nitto Personè (a.a. 2026/27).
> Collegamenti: [[SQL - Interrogazioni e aggiornamenti (DML)]] · [[Modello relazionale]] · [[Algebra relazionale]] · [[00 Indice|Indice]]

---

## 1. Linguaggi per BD: tipi

| Tipo | Linguaggi |
|---|---|
| **Dichiarativi** | Calcolo relazionale, QBE, **SQL** |
| **Procedurali** | **Algebra relazionale** |

(SQL è dichiarativo, ma con alcune componenti procedurali.)

## 2. SQL
- originariamente "**Structured Query Language**", ora "nome proprio";
- linguaggio con varie funzionalità: **contiene sia il DDL sia il DML**;
- ne esistono **varie versioni**.

### SQL: "storia"
- prima proposta **SEQUEL (1974)**;
- prime implementazioni in **SQL/DS e Oracle (1981)**;
- dal **1983** ca. "**standard di fatto**";
- **standard** (1986, poi 1989, 1992, 1999, 2003, 2006, 2008, 2011 …)
  - recepito **solo in parte** (!! vedi http://troels.arvin.dk/db/rdbms/ per un confronto tra DBMS).

---

## 3. SQL come DDL: `CREATE TABLE`

L'istruzione **`CREATE TABLE`**:
- **definisce uno schema di relazione e ne crea un'istanza vuota**;
- specifica **attributi, domini e vincoli**.

```sql
CREATE TABLE Impiegato(
   Matricola CHAR(6) PRIMARY KEY,
   Nome      CHAR(20) NOT NULL,
   Cognome   CHAR(20) NOT NULL,
   Dipart    CHAR(15),
   Stipendio NUMERIC(9) DEFAULT 0,
   FOREIGN KEY(Dipart) REFERENCES Dipartimento(NomeDip),
   UNIQUE (Cognome, Nome)
)
```

Elementi:
- `PRIMARY KEY` → chiave primaria (vedi [[Modello relazionale#Chiave primaria]]);
- `NOT NULL` → valore nullo non ammesso;
- `DEFAULT 0` → valore di default;
- `FOREIGN KEY (…) REFERENCES Tabella(Attr)` → chiave esterna / vincolo di integrità referenziale;
- `UNIQUE (…)` → chiave (candidata).

> [!warning] **DB2 vuole `NOT NULL` per la chiave primaria.**

### Chiavi su più attributi, attenzione
```sql
Nome    CHAR(20) NOT NULL,
Cognome CHAR(20) NOT NULL,
UNIQUE (Cognome, Nome),
```
```sql
Nome    CHAR(20) NOT NULL UNIQUE,
Cognome CHAR(20) NOT NULL UNIQUE,
```
> [!important] **Non è la stessa cosa!**
> - Il primo dice che la **coppia** (Cognome, Nome) è una chiave: non possono esistere due persone con stesso nome **e** stesso cognome.
> - Il secondo dice che **Nome** è chiave **e** **Cognome** è chiave, separatamente: non possono esistere due persone con lo stesso nome, né due con lo stesso cognome (vincolo molto più restrittivo).

---

## 4. Politiche di reazione (integrità referenziale)

Richiamo (vedi [[Modello relazionale#Azioni compensative]]):
- Esempio: viene eliminata una ennupla causando una violazione.
- Comportamento "standard": **rifiuto dell'operazione** → **`no action`**
- Azioni compensative:
  - **Eliminazione in cascata** → **`cascade`**
  - **Introduzione di valori nulli** → **`set null`**
  - (oppure valore di default) → **`set default`**

Le politiche si specificano separatamente per **`on delete`** e **`on update`** della tabella riferita.

```sql
CREATE TABLE Impiegati(
   Matricola CHAR(6) PRIMARY KEY,
   Cognome   CHAR(20) NOT NULL UNIQUE,
   Progetto  CHAR(6)
             REFERENCES Progetti(Codice)
             on delete set null
             on update cascade
)
```

**Situazione iniziale** — Impiegati / Progetti:

| <u>Matricola</u> | Cognome | Progetto |
|---|---|---|
| 34321 | Rossi | IDEA |
| 53524 | Neri | XYZ |
| 64521 | Verdi | NULL |
| 73032 | Bianchi | IDEA |

| <u>Codice</u> | Inizio | Durata | Costo |
|---|---|---|---|
| IDEA | 01/2000 | 36 | 200 |
| XYZ | 07/2001 | 24 | 120 |
| BOH | 09/2001 | 24 | 150 |

### Introduzione di valori nulli (`on delete set null`)
Si elimina il progetto **XYZ** → l'impiegato Neri resta, ma `Progetto` di Neri diventa **NULL**.

### Eliminazione in cascata (`on delete cascade`)
Si elimina il progetto **XYZ** → viene eliminata anche la riga di **Neri** (53524) in Impiegati.

### Modifica in cascata (`on update cascade`)
Si modifica il codice del progetto **XYZ → ABC** in Progetti → in Impiegati il progetto di Neri diventa automaticamente **ABC**.

### Esempio con chiave esterna su più attributi: Infrazioni
Tabelle **Infrazioni**(Codice, Data, Vigile, Prov, Numero), **Vigili**(Matricola, Cognome, Nome), **Auto**(Prov, Numero, Cognome, Nome) (dati come in [[Modello relazionale#Esempio: Vigili e Infrazioni]]).

```sql
CREATE TABLE Infrazioni(
   Codice    CHAR(6) PRIMARY KEY,
   Data      DATE NOT NULL,
   Vigile    INTEGER NOT NULL
             REFERENCES Vigili(Matricola),
   Provincia CHAR(2),
   Numero    CHAR(6),
   FOREIGN KEY(Provincia, Numero)
             REFERENCES Auto(Provincia, Numero)
)
```
- Vincolo su **un solo attributo**: si può scrivere `REFERENCES` direttamente accanto all'attributo.
- Vincolo su **più attributi**: si usa la clausola separata `FOREIGN KEY (…) REFERENCES …`.

---

## 5. Modifiche degli schemi
```sql
ALTER DOMAIN
ALTER TABLE
DROP DOMAIN
DROP TABLE
...
```

---

## 6. Livelli di progettazione e indici

| Fase | Modello |
|---|---|
| Progettazione **concettuale** | Entity-Relationship → [[Modello Entity-Relationship]] |
| Progettazione **logica** | Modello relazionale → [[Modello relazionale]] |
| Progettazione **fisica** | (strutture fisiche, indici) → [[Organizzazione fisica dei dati]] |

### Definizione degli indici
- è **rilevante dal punto di vista delle prestazioni**;
- ma è a **livello fisico e non logico**;
- in passato era importante perché in alcuni sistemi era **l'unico mezzo per definire chiavi**;
- istruzione **`CREATE INDEX`**.

> [!abstract] Indice
> **File organizzato su alcuni campi di una tabella.** Contiene coppie **(campo indice, id record)**.

Esempio — **Studenti**(<u>Matricola</u>, Nome, Anno, CodiceDip): (1, Joe, 2004, 10), (2, Amy, 2004, 20), (3, Max, 2005, 10), (4, Sue, 2005, 30), …
- `Matricola_IDX`: indice sui valori 1…9 ordinati, ciascuno punta al record corrispondente → **indice primario** (sulla chiave, record ordinati come l'indice);
- `CodiceDip_IDX`: indice sui valori 10, 10, 10, 20, 20, 20, 20, 30, 30 → ciascuno punta al record con quel CodiceDip (puntatori "incrociati": i record non sono ordinati secondo questo campo).

```sql
create index CodiceDip_IDX on Studenti(CodiceDip)
create unique index Matricola_IDX on Studenti(Matricola)
```
(`unique` → non ammette valori duplicati, quindi realizza anche un vincolo di chiave.)

### DDL, in pratica
- In molti sistemi si utilizzano **strumenti diversi dal codice SQL** per definire lo schema della base di dati (interfacce grafiche).

---

## 7. Esecuzione delle query
```mermaid
flowchart LR
  A[query in SQL] --> B[query optimizer]
  B --> C[query in linguaggio procedurale interno al DBMS]
  C --> D[esecuzione]
```

Continua in → [[SQL - Interrogazioni e aggiornamenti (DML)]]
