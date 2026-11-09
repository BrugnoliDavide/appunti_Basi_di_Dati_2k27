---
tags: [basi-di-dati, modello-relazionale]
fonte: lect2BDmodRelaz2627.pdf (Prof. V. de Nitto Personè)
---

# Il modello relazionale

> [!info] Fonte
> Slides *"Il modello relazionale"* — Prof. Vittoria de Nitto Personè (a.a. 2026/27).
> Collegamenti: [[Algebra relazionale]] · [[SQL - Definizione dei dati (DDL)]] · [[Modello Entity-Relationship]] · [[00 Indice|Indice]]

---

## 1. I modelli logici dei dati

**Tre modelli logici tradizionali:**
- **gerarchico**
- **reticolare**
- **relazionale**

**Più recenti:**
- **a oggetti** (poco diffuso)
- **basato su XML** ("complementare" a quello relazionale)

### Modello gerarchico
I dati sono organizzati ad **albero**: i record sono collegati tramite **puntatori** da un record "padre" ai record "figli".
Esempio delle slides: una radice punta ai record di `CONTI` (numero conto, saldo: 9000/120345, 7000/500, 4500/3500, 5100/58500, 8000/6320, 4310/37), e ogni conto punta ai record di `CLIENTI` che lo intestano (Bianchi Mazzini Bergamo 24127, Neri Garibaldi Napoli 80120, Rossi Rosmini Milano 20125, Gialli Cavour Bari 70122, Verdi Dante Roma 00128, Rosa Crispi Torino 10137, Moro Colombo Milano 20143).

### Modello reticolare
Generalizza il gerarchico: i record sono collegati da puntatori formando un **grafo** (un record può avere più "padri").
Esempio: `CLIENTI` ↔ `CONTI` ↔ `MOVIMENTI` (data, tipo Vers/Prel, importo), collegati tramite puntatori in entrambe le direzioni.

### Modello relazionale (esempio)
I dati sono organizzati in **tabelle**; i collegamenti tra tabelle avvengono **tramite valori** (non puntatori).

**studenti**

| Matricola | Cognome | Nome | Data di nascita |
|---|---|---|---|
| 6554 | Rossi | Mario | 05/12/1978 |
| 8765 | Neri | Paolo | 03/11/1976 |
| 9283 | Verdi | Luisa | 12/11/1979 |
| 3456 | Rossi | Maria | 01/02/1978 |

**esami**

| Studente | Voto | Corso |
|---|---|---|
| 3456 | 30 | 04 |
| 3456 | 24 | 02 |
| 9283 | 28 | 01 |
| 6554 | 26 | 01 |

**corsi**

| Codice | Titolo | Docente |
|---|---|---|
| 01 | Analisi | Mario |
| 02 | Chimica | Bruni |
| 04 | Chimica | Verdi |

> [!note] Le corrispondenze tra tabelle sono basate sui **valori**: `esami.Studente` corrisponde a `studenti.Matricola`, `esami.Corso` a `corsi.Codice`.

---

## 2. Il modello relazionale: origini

- Proposto da **E. F. Codd nel 1970** per favorire l'**indipendenza dei dati**.
- Disponibile in DBMS reali nel **1981** (non è facile implementare l'indipendenza con efficienza e affidabilità!).
- Si basa sul concetto matematico di **relazione**.
- Le relazioni hanno naturale rappresentazione per mezzo di **tabelle**.

### Relazione: tre accezioni
1. **relazione matematica**: come nella teoria degli insiemi;
2. **relazione** (dall'inglese *relationship*) che rappresenta una **classe di fatti**, nel modello **Entity-Relationship**; tradotto anche con *associazione* o *correlazione* (es. MUSICISTA —*Suona*— STRUMENTO, vedi [[Modello Entity-Relationship]]);
3. **relazione secondo il modello relazionale dei dati**.

---

## 3. Relazione matematica

Esempio:
- $D_1 = \{a, b\}$, $D_2 = \{x, y, z\}$
- **prodotto cartesiano** $D_1 \times D_2 = \{ax, ay, az, bx, by, bz\}$
- una **relazione** $r \subseteq D_1 \times D_2$, ad es. $r = \{ax, az, bx, by\}$

### Proprietà
- una relazione matematica è un **insieme di n-uple ordinate** $(d_1, \dots, d_n)$ tali che $d_1 \in D_1, \dots, d_n \in D_n$
- una relazione è un **insieme**: $r = \{ax, az, bx, by\}$
  - **non c'è ordinamento fra le n-uple**: $\{ax, az, bx, by\} = \{bx, ax, by, az\}$
  - le n-uple sono **distinte**
  - **ciascuna n-upla è ordinata**: l'$i$-esimo valore proviene dall'$i$-esimo dominio

### Esempio
- $M = \{\text{Maria, Alberto, Alessandra}\}$, $S = \{\text{Piano, Sax, Chitarra}\}$
- $M \times S = \{(\text{Maria,Piano}), (\text{Maria,Sax}), (\text{Maria,Chitarra}), \dots, (\text{Alessandra,Chitarra})\}$
- $r = \{(\text{Maria,Piano}), (\text{Alberto,Sax}), (\text{Alessandra,Chitarra})\}$

> [!abstract] Definizioni
> - **Grado di una relazione**: è il numero di domini che partecipano alla relazione.
> - **Cardinalità di un'istanza di relazione**: è il numero di tuple che compongono l'istanza (ad un dato istante di tempo).

Altro esempio:
- Insegnamenti = {Chimica, …}, Docenti = {Luigi Neri, …}, Aule = {N3, …}, Ore = {8:00, …}
- $I \times D \times A \times O = \{(\text{Basi di dati, Luigi Neri, N3, 8:00}), \dots\}$
- orario $= \{(\text{Basi di dati, Piero Rossi, N2, 9:45}), \dots\}$

> [!abstract] Def. Tupla (n-upla)
> È una **sequenza ordinata di valori di attributi**.

**Orario**

| Insegnamento | Docente | Aula | Ora |
|---|---|---|---|
| Analisi matem. I | Luigi Neri | N1 | 8:00 |
| Basi di dati | Piero Rossi | N2 | 9:45 |
| Chimica | Nicola Mori | N1 | 9:45 |
| Fisica I | Mario Bruni | N1 | 11:45 |
| Fisica II | Mario Bruni | N3 | 9:45 |
| Sistemi inform. | Piero Rossi | N3 | 8:00 |

→ istanza di relazione di **cardinalità 6**: orario $= \{t_1, \dots, t_6\}$.

---

## 4. Attributo, schema, istanza

> [!abstract] Def. Attributo
> È una coppia **(nome, tipo di dato)**.

```sql
CREATE TABLE orario (
  insegnamento CHAR(20),
  docente      CHAR(20),
  aula         CHAR(4),
  ora          CHAR(5) )
```
es. l'attributo `(insegnamento, CHAR(20))`.

> [!abstract] Def. Schema di relazione
> $R(A_1, A_2, \dots, A_m)$, dove $X = \{A_1, \dots, A_m\}$ è l'insieme degli attributi: si scrive $R(X)$.
> Esempi: `ORARIO(Insegnamento, Docente, Aula, Ora)`, `MUSICISTA(Nome, Strumento)`.

> [!abstract] Def. Istanza di relazione (su $R(X)$)
> È un **insieme di tuple** (su $X$):
> $$r = \{t_1, \dots, t_i, \dots, t_n\}, \qquad t_i = \langle v_1, \dots, v_j, \dots, v_m \rangle, \quad v_j \in dom(A_j)$$
> Es.: orario $= \{t_1, \dots, t_6\}$, $t_3 = \langle \text{Chimica, Nicola Mori, N1, 9:45} \rangle$, $v_4 \in dom(A_4) = \text{CHAR(5)}$.

> [!abstract] Def. Istanza di una base di dati
> Su un insieme di schemi $\mathbf{R} = \{R_1(X_1), \dots, R_p(X_p)\}$ è un **insieme di istanze di relazioni** $\mathbf{r} = \{r_1, \dots, r_p\}$, con $r_i$ istanza di relazione su $R_i$.

> [!abstract] Def. Base di dati
> Una base di dati (DB) rappresenta **un aspetto del mondo reale**, di interesse per qualche specifico scopo, ed è un **insieme di dati coerenti e correlati**¹, con un significato preciso per un particolare insieme di utenti.
> ¹ sono le *relazioni* nel modello relazionale.

**Esempio** — istanza (ad un istante $t$) di una base di dati su un insieme di schemi:
- `BDUNIVERSITA = {STUDENTI(X1), ESAMI(X2), CORSI(X3), ORARIO(X4)}` (ad es. `ORARIO(Insegnamento, Docente, Aula, Ora)`)
- è un insieme di istanze di relazioni (all'istante $t$): `bduniversita = {studenti, esami, corsi, orario}` (ad es. `orario = {(Basi di dati, Piero Rossi, N2, 9:45), …}`)

---

## 5. Struttura posizionale vs non posizionale

**Relazione matematica** — $\text{Partite} \subseteq \text{string} \times \text{string} \times \text{int} \times \text{int}$:

| | | | |
|---|---|---|---|
| Juve | Lazio | 3 | 1 |
| Lazio | Milan | 2 | 0 |
| Juve | Roma | 0 | 2 |
| Roma | Milan | 0 | 1 |

- Ciascuno dei domini ha **due ruoli diversi**, distinguibili attraverso la **posizione**: la struttura è **posizionale**.

**Struttura non posizionale**: a ciascun dominio si associa un **nome unico** nella tabella (il **nome dell'attributo**), che ne descrive il "**ruolo**".

| Casa | Fuori | RetiCasa | RetiFuori |
|---|---|---|---|
| Juve | Lazio | 3 | 1 |
| Lazio | Milan | 2 | 0 |
| Juve | Roma | 0 | 2 |
| Roma | Milan | 0 | 1 |

(L'ordine delle colonne può essere permutato senza cambiare il significato.)

### Tabelle e relazioni
In una tabella che rappresenta una relazione:
- l'**ordinamento tra le righe è irrilevante** → l'istanza è un insieme;
- l'**ordinamento tra le colonne è irrilevante** → lo schema è un insieme.

Una tabella rappresenta una relazione **se**:
- le **righe sono diverse** fra loro;
- le **intestazioni delle colonne sono diverse** tra loro;
  → sono **insiemi e non multinsiemi**;
- i **valori di ogni colonna sono fra loro omogenei**: $v_j \in dom(A_j)$.

---

## 6. Informazione incompleta e valori nulli

| Nome | SecondoNome | Cognome |
|---|---|---|
| Franklin | Delano | Roosevelt |
| Winston | | Churchill |
| Charles | | De Gaulle |
| Josip | | Stalin |

### Soluzioni?
**Non conviene** (anche se spesso si fa) usare **valori del dominio** (0, stringa nulla, "99", …):
- potrebbero **non esistere** valori "non utilizzati";
- valori "non utilizzati" potrebbero **diventare significativi**;
- in fase di utilizzo (nei programmi) sarebbe necessario ogni volta tener conto del "significato" di questi valori.

→ Si usa il **valore nullo `NULL`**, un valore speciale (non appartenente al dominio) che denota assenza di informazione.

Tipi di valore nullo (esempio `MUSICISTA(Nome, Strumento)`):

| MUSICISTA | Nome | Strumento |
|---|---|---|
| $t_1$ | Mario | NULL ← **sconosciuto** (esiste ma non lo conosciamo) |
| $t_2$ | Alberto | Sax |
| $t_3$ | Alessandro | Chitarra |
| $t_4$ | NULL ← **senza informazione** (non sappiamo nemmeno se esiste) | Violino |

### Troppi valori nulli
Un uso eccessivo di NULL rende la base di dati inutilizzabile:

**studenti**: (6554, Rossi, Mario, 05/12/1978), (9283, Verdi, Luisa, 12/11/1979), (**NULL**, Rossi, Maria, 01/02/1978)
**esami**: (**NULL**, 30, **NULL**), (**NULL**, 24, 02), (9283, 28, 01)
**corsi**: (01, Analisi, Mario), (02, **NULL**, **NULL**), (04, Chimica, Verdi)

→ non si sa a chi/quale corso si riferiscono gli esami, non si può identificare lo studente Rossi Maria, ecc.

---

## 7. Vincoli di integrità

> [!abstract] Def. Vincolo di integrità
> **Proprietà** che deve essere soddisfatta dalle istanze che rappresentano informazioni **corrette** per l'applicazione.
> Un vincolo è una **funzione booleana (un predicato)**: associa ad ogni istanza il valore **vero** o **falso**.

### Classificazione dei vincoli
- **Vincoli inerenti al modello relazionale** (es. righe distinte, valori omogenei)
- **Vincoli dello schema**
  - **Vincoli intra-relazione** (su una sola relazione):
    - vincolo di **dominio**
    - vincolo di **ennupla**
    - vincolo di **superchiave**
    - vincolo di **chiave primaria**
  - **Vincoli inter-relazione** (tra più relazioni):
    - vincolo di **integrità referenziale** (chiave esterna)

### Vincoli di ennupla
Esprimono condizioni sui valori di ciascuna ennupla, indipendentemente dalle altre.

**Stipendi** — vincolo: `Lordo = (Ritenute + Netto)`

| Impiegato | Lordo | Ritenute | Netto |
|---|---|---|---|
| Rossi | 55.000 | 12.500 | 42.500 |
| Neri | 45.000 | 10.000 | 35.000 |
| Bruni | 47.000 | 11.000 | 36.000 |

**Violazione**: se Bruni avesse Lordo = 50.000 (con Ritenute 11.000 e Netto 36.000) il vincolo sarebbe violato.

### Una base di dati "scorretta"

**Esami**

| Studente | Voto | Lode | Corso |
|---|---|---|---|
| 276545 | **32** | | 01 |
| 276545 | 30 | e lode | 02 |
| 787643 | **27** | **e lode** | 03 |
| **739430** | 24 | | 04 |

**Studenti**

| Matricola | Cognome | Nome |
|---|---|---|
| 276545 | Rossi | Mario |
| **787643** | Neri | Piero |
| **787643** | Bianchi | Luca |

Errori evidenziati:
- voto 32 → viola il **vincolo di dominio** (voto tra 18 e 30);
- 27 e lode → viola un **vincolo di ennupla** (la lode è ammessa solo con voto 30);
- matricola 787643 ripetuta → viola il **vincolo di chiave**;
- studente 739430 non presente in Studenti → viola il **vincolo di integrità referenziale**.

> [!note] Vincoli di integrità e DBMS
> - alcuni tipi di vincoli (ma non tutti) sono **"supportati" dai DBMS**: possiamo quindi specificare vincoli di tali tipi nella nostra base di dati e il DBMS ne **impedisce la violazione**;
> - per i vincoli **"non supportati"**, la responsabilità della verifica è dell'**utente** o del **programmatore**.

---

## 8. Superchiavi e chiavi

> [!abstract] Def. Superchiave SK di uno schema $R(X)$
> È un sottoinsieme di attributi $SK \subseteq X$ tale che
> $$\forall\, t_i, t_l \quad i \neq l, \quad i,l \in [1, \dots, n]: \qquad t_i[SK] \neq t_l[SK]$$
> (non esistono due tuple distinte con gli stessi valori su SK).

> [!abstract] Def. Chiave K di uno schema $R(X)$
> È una **superchiave minimale** (nessun sottoinsieme proprio è a sua volta superchiave).

Esempio **MUSICISTA**:

| | Nome | Cognome | Strumento |
|---|---|---|---|
| $t_1$ | Mario | Rossi | Piano |
| $t_2$ | Alberto | Bianchi | Sax |
| $t_3$ | Alessandro | Verdi | Chitarra |
| $t_4$ | Stefano | Neri | Violino |

In questa istanza anche {Cognome} o {Strumento} distinguono le tuple, ma basta un'istanza diversa (es. $t_3$ = Alessandro **Rossi** **Piano**, uguale a $t_1$ su Cognome e Strumento) per mostrare che non sono superchiavi.

> [!important] È importante trovare una SK **significativa rispetto allo schema** e **non alla particolare istanza**.

### Esempio: Studenti(Matricola, Cognome, Nome, Corso, Nascita)
- chiavi: **{Matricola}** e **{Cognome, Nome, Nascita}**

| Matricola | Cognome | Nome | Corso | Nascita |
|---|---|---|---|---|
| 27655 | Rossi | Mario | Ing Inf | 5/12/78 |
| 78763 | Rossi | Mario | Ing Civile | 3/11/76 |
| 65432 | Neri | Piero | Ing Mecc | 10/7/79 |
| 87654 | Neri | Mario | Ing Inf | 3/11/76 |
| 67653 | Rossi | Piero | Ing Mecc | 5/12/78 |

- L'istanza è **corretta**: soddisfa i vincoli di SK.
- Ne soddisfa anche altri ("**per caso**"): {Cognome, Corso} è chiave **in questa istanza**, ma non è una chiave dello schema.

### Importanza delle chiavi
- L'esistenza delle chiavi garantisce l'**accessibilità a ciascun dato** della base di dati.
- Le chiavi permettono di **correlare i dati in relazioni diverse**: il modello relazionale è **basato su valori**.

> [!tip] Ogni relazione ha almeno una chiave
> Poiché le tuple di una relazione sono distinte, l'insieme di tutti gli attributi $X$ è sempre una superchiave → esiste sempre almeno una chiave.

### Chiavi e valori nulli
In presenza di valori nulli, i valori della chiave **non permettono**:
- di **identificare** le ennuple;
- di realizzare facilmente i **riferimenti** da altre relazioni.

Esempio (chiavi: Matricola; Cognome, Nome, Nascita):

| Matricola | Cognome | Nome | Corso | Nascita |
|---|---|---|---|---|
| NULL | NULL | Mario | Ing Inf | 5/12/78 |
| 78763 | Rossi | Mario | Ing Civile | 3/11/76 |
| 65432 | Neri | Piero | Ing Mecc | 10/7/79 |
| 87654 | Neri | Mario | Ing Inf | NULL |
| NULL | Neri | Mario | NULL | 5/12/78 |

→ la prima tupla non è identificabile; le ultime due potrebbero riferirsi allo stesso studente.
**La presenza di valori nulli nelle chiavi deve essere limitata.**

### Chiave primaria
> [!abstract] Def. Chiave primaria
> Chiave su cui **non sono ammessi valori nulli**. Notazione: attributi **sottolineati**.

| <u>Matricola</u> | Cognome | Nome | Corso | Nascita |
|---|---|---|---|---|
| 86765 | NULL | Mario | Ing Inf | 5/12/78 |
| 78763 | Rossi | Mario | Ing Civile | 3/11/76 |
| 65432 | Neri | Piero | Ing Mecc | 10/7/79 |
| 87654 | Neri | Mario | Ing Inf | NULL |
| 43289 | Neri | Mario | NULL | 5/12/78 |

(I NULL sono ammessi negli altri attributi, ma non in Matricola.)

---

## 9. Chiave esterna e integrità referenziale

> [!abstract] Def. Chiave esterna
> Una **FK di $R_1(X_1)$ che fa riferimento a $R_2(X_2)$** è un insieme di attributi $FK \subset X_1$ tale che:
> - $|FK| = |K|$, dove $K$ è la **chiave primaria** di $R_2(X_2)$
> - $dom(FK) = dom(K)$
> - $t_1[FK] = t_2[K]$ **oppure** $t_1[FK] = null$, dove $t_1 \in r_1(R_1)$ e $t_2 \in r_2(R_2)$

**Terminologia** (es. `MUSICISTA(CF, Nome, Cognome, Strumento)` → `STRUMENTO(Nome, Tipo)`):
- **relazione referente**: quella che contiene la FK (MUSICISTA);
- **relazione riferita**: quella a cui la FK fa riferimento (STRUMENTO);
- il vincolo è detto **vincolo di integrità referenziale** (vincolo **inter-relazione**).

Esempio studenti/esami/corsi: `esami.Studente` è FK verso `studenti.Matricola` ($R_1$ = esami, $R_2$ = studenti).

### Esempio: Vigili e Infrazioni
**Infrazioni** (relazione referente) — `Vigile` è FK in Infrazioni

| <u>Codice</u> | Data | Vigile | Prov | Numero |
|---|---|---|---|---|
| 34321 | 1/2/95 | 3987 | MI | 39548K |
| 53524 | 4/3/95 | 3295 | TO | E39548 |
| 64521 | 5/4/96 | 3295 | PR | 839548 |
| 73321 | 5/2/98 | 9345 | PR | 839548 |

**Vigili** (relazione riferita)

| <u>Matricola</u> | Cognome | Nome |
|---|---|---|
| 3987 | Rossi | Luca |
| 3295 | Neri | Piero |
| 9345 | Neri | Mario |
| 7543 | Mori | Gino |

Anche **(Prov, Numero)** è FK in Infrazioni verso **Auto(<u>Prov, Numero</u>, Cognome, Nome)**: FK composta da più attributi.

| Prov | Numero | Cognome | Nome |
|---|---|---|---|
| MI | 39548K | Rossi | Mario |
| TO | E39548 | Rossi | Mario |
| PR | 839548 | Neri | Luca |

**Violazione del vincolo di integrità referenziale**: se Auto contenesse (MI, E39548), (TO, F34268), (PR, 839548), le infrazioni con (MI, 39548K) e (TO, E39548) farebbero riferimento a auto inesistenti (la coppia va confrontata **nel suo insieme**, non attributo per attributo).

### Integrità referenziale e valori nulli
`Progetto` è FK in **Impiegati** verso **Progetti**:

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

Il valore NULL nella FK è ammesso (Verdi non lavora a nessun progetto).

### Azioni compensative
Esempio: viene **eliminata una ennupla** della relazione riferita causando una violazione (es. si elimina il progetto XYZ).
- **Comportamento "standard"**: **rifiuto dell'operazione**.
- **Azioni compensative**:
  - **Eliminazione in cascata**: si eliminano anche le ennuple referenti (viene eliminato l'impiegato Neri che lavorava a XYZ);
  - **Introduzione di valori nulli**: la FK delle ennuple referenti viene posta a NULL (Neri → Progetto = NULL).

Vedi anche la realizzazione in SQL: [[SQL - Definizione dei dati (DDL)#4. Politiche di reazione (integrità referenziale)]].
