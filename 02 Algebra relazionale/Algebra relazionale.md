---
tags: [basi-di-dati, algebra-relazionale, interrogazioni]
fonte: lect3BDalgRelaz2627.pdf (Prof. V. de Nitto Personè)
---

# Algebra relazionale

> [!info] Fonte
> Slides *"Algebra relazionale"* — Prof. Vittoria de Nitto Personè (a.a. 2026/27).
> Prerequisiti: [[Modello relazionale]]. Collegamenti: [[SQL - Interrogazioni e aggiornamenti (DML)]] · [[00 Indice|Indice]]

> [!quote] Risultati a.a. 2025/2026 (slide introduttiva)
> Promossi **86 studenti su 175 iscritti** (meno del 50%!); **su 111 attivi → 77,5%**. (Istogramma dei voti da 18 a 30 e lode.)

---

## 1. Contenuti del corso
- modelli per l'organizzazione dei dati
- linguaggi per l'utilizzo dei dati
- sistemi per la gestione dei dati
- metodologie di progettazione di basi di dati

## 2. Linguaggi per basi di dati

| Linguaggio | Obiettivo |
|---|---|
| **DDL** (Data Definition Language) | definire schemi **logici, esterni, fisici** e le **autorizzazioni** agli utenti |
| **DML** (Data Manipulation Language) | **interrogare** e **aggiornare** le istanze di BD |

Il DML comprende:
- **interrogazioni**: funzione che, data un'istanza di BD, **produce una relazione** su un dato schema;
- **aggiornamenti**: funzione che, data un'istanza di BD, **produce un'altra istanza sullo stesso schema**.

### Linguaggi di interrogazione per basi di dati relazionali
- **Dichiarativi**: specificano le **proprietà del risultato** ("**che cosa**") — es. SQL
- **Procedurali**: specificano le **modalità di generazione** del risultato ("**come**") — es. algebra relazionale

## 3. Operatori dell'algebra relazionale
- **operazioni di base**: selezione $\sigma$, proiezione $\pi$, ridenominazione $\rho$
- **operazioni insiemistiche**: unione $\cup$, intersezione $\cap$, differenza $-$
- **operazioni di correlazione**: prodotto cartesiano $\times$, join naturale $\bowtie$, theta-join $\bowtie_\theta$

Gli operatori sono **chiusi**: operano su relazioni e producono relazioni, quindi si possono comporre.

---

## 4. Selezione $\sigma$

$\sigma_{F}(r)$: produce una relazione **sullo stesso schema** dell'operando contenente le ennuple di $r$ che **soddisfano la condizione** $F$ (combinazione booleana di condizioni atomiche con AND, OR, NOT).

**Impiegati**

| Matricola | Cognome | Filiale | Stipendio |
|---|---|---|---|
| 7309 | Rossi | Roma | 55 |
| 5998 | Neri | Milano | 64 |
| 9553 | Milano | Milano | 44 |
| 5698 | Neri | Napoli | 64 |

- impiegati che **guadagnano più di 50**: $\sigma_{\text{Stipendio} > 50}(\text{Impiegati})$ → 7309 Rossi, 5998 Neri, 5698 Neri
- impiegati che **guadagnano più di 50 e lavorano a Milano**: $\sigma_{\text{Stipendio} > 50 \,\land\, \text{Filiale} = \text{'Milano'}}(\text{Impiegati})$ → 5998 Neri Milano 64
- impiegati che **hanno lo stesso nome della filiale** presso cui lavorano: $\sigma_{\text{Cognome} = \text{Filiale}}(\text{Impiegati})$ → 9553 Milano Milano 44

## 5. Proiezione $\pi$

$\pi_{Y}(r)$: produce una relazione su **un sottoinsieme $Y$ degli attributi**, contenente le ennuple di $r$ ristrette a $Y$ (eliminando i duplicati).

**Impiegati**

| Matricola | Cognome | Filiale | Stipendio |
|---|---|---|---|
| 7309 | Neri | Napoli | 55 |
| 5998 | Neri | Milano | 64 |
| 9553 | Rossi | Roma | 44 |
| 5698 | Rossi | Roma | 64 |

- matricola e cognome di tutti gli impiegati: $\pi_{\text{Matricola, Cognome}}(\text{Impiegati})$ → 4 ennuple
- cognome e filiale di tutti gli impiegati: $\pi_{\text{Cognome, Filiale}}(\text{Impiegati})$ → (Neri, Napoli), (Neri, Milano), (Rossi, Roma) → **3 ennuple** (le due ennuple Rossi Roma collassano)

### Selezione e proiezione
- operatori "**ortogonali**":
  - **selezione** → **decomposizione orizzontale** (sceglie righe)
  - **proiezione** → **decomposizione verticale** (sceglie colonne)

### Cardinalità delle proiezioni
- una proiezione **contiene al più tante ennuple quante l'operando**; **può contenerne di meno**;
- se $Y$ è una **superchiave** di $R$, allora $\pi_Y(r)$ contiene **esattamente** tante ennuple quante $r$ ($r$ istanza di $R$).

### Selezione + proiezione
Matricola e cognome degli impiegati che guadagnano più di 50:
$$\pi_{\text{Matricola, Cognome}}\big(\sigma_{\text{Stipendio} > 50}(\text{Impiegati})\big)$$

---

## 6. Operatori insiemistici: $\cup$, $\cap$, $-$
Le relazioni sono insiemi → si possono usare gli operatori insiemistici, **ma solo tra relazioni definite sugli stessi attributi**.

**Laureati**: (7274, Rossi, 42), (7432, Neri, 54), (9824, Verdi, 45)
**Specialisti**: (9297, Neri, 33), (7432, Neri, 54), (9824, Verdi, 45)

| Operazione | Risultato |
|---|---|
| Laureati $\cup$ Specialisti | 7274 Rossi 42; 7432 Neri 54; 9824 Verdi 45; 9297 Neri 33 |
| Laureati $\cap$ Specialisti | 7432 Neri 54; 9824 Verdi 45 |
| Laureati $-$ Specialisti | 7274 Rossi 42 |

### Un'unione sensata ma impossibile
**Paternità**(Padre, Figlio): (Adamo, Abele), (Adamo, Caino), (Abramo, Isacco)
**Maternità**(Madre, Figlio): (Eva, Abele), (Eva, Set), (Sara, Isacco)

Paternità $\cup$ Maternità → **??** (attributi diversi: Padre ≠ Madre)

---

## 7. Ridenominazione $\rho$
$\rho_{\text{NuovoNome} \leftarrow \text{VecchioNome}}(r)$: operatore **monadico** che **modifica lo schema** cambiando il nome agli attributi, lasciando inalterata l'istanza. Serve per rendere possibili unioni/join.

$$\rho_{\text{Genitore} \leftarrow \text{Padre}}(\text{Paternità}) \;\cup\; \rho_{\text{Genitore} \leftarrow \text{Madre}}(\text{Maternità})$$

| Genitore | Figlio |
|---|---|
| Adamo | Abele |
| Adamo | Caino |
| Abramo | Isacco |
| Eva | Abele |
| Eva | Set |
| Sara | Isacco |

Esempio con più attributi:
- **Impiegati**(Cognome, Ufficio, Stipendio): (Rossi, Roma, 55), (Neri, Milano, 64)
- **Operai**(Cognome, Fabbrica, Salario): (Bruni, Monza, 45), (Verdi, Latina, 55)

$$\rho_{\text{Sede, Retribuzione} \leftarrow \text{Ufficio, Stipendio}}(\text{Impiegati}) \;\cup\; \rho_{\text{Sede, Retribuzione} \leftarrow \text{Fabbrica, Salario}}(\text{Operai})$$

| Cognome | Sede | Retribuzione |
|---|---|---|
| Rossi | Roma | 55 |
| Neri | Milano | 64 |
| Bruni | Monza | 45 |
| Verdi | Latina | 55 |

---

## 8. Join naturale $\bowtie$

### Motivazione: prove scritte in un concorso pubblico
- I compiti sono **anonimi** e ad ognuno è associata una **busta chiusa** con il nome del candidato.
- Ciascun compito e la relativa busta vengono contrassegnati con **uno stesso numero**.

| Numero | Voto |
|---|---|
| 1 | 25 |
| 2 | 13 |
| 3 | 27 |
| 4 | 28 |

| Numero | Candidato |
|---|---|
| 1 | Mario Rossi |
| 2 | Nicola Russo |
| 3 | Mario Bianchi |
| 4 | Remo Neri |

Il join "combina" le ennuple con lo stesso Numero:

| Numero | Candidato | Voto |
|---|---|---|
| 1 | Mario Rossi | 25 |
| 2 | Nicola Russo | 13 |
| 3 | Mario Bianchi | 27 |
| 4 | Remo Neri | 28 |

### Definizione (informale)
$r_1 \bowtie r_2$, con $r_1$ su $X_1$ e $r_2$ su $X_2$: relazione su $X_1 \cup X_2$ che contiene le ennuple ottenute **combinando** ennuple di $r_1$ e $r_2$ con **valori uguali sugli attributi comuni**.

### Join completo
$r_1$(Impiegato, Reparto) = (Rossi, A), (Neri, B), (Bianchi, B); $r_2$(Reparto, Capo) = (A, Mori), (B, Bruni)

$r_1 \bowtie r_2$ = (Rossi, A, Mori), (Neri, B, Bruni), (Bianchi, B, Bruni)
→ **ogni ennupla contribuisce al risultato**: join **completo**.

### Un join non completo
$r_1$ = (Rossi, A), (Neri, B), (Bianchi, B); $r_2$ = (B, Mori), (C, Bruni)
$r_1 \bowtie r_2$ = (Neri, B, Mori), (Bianchi, B, Mori)

### Un join vuoto
$r_1$ = (Rossi, A), (Neri, B), (Bianchi, B); $r_2$ = (D, Mori), (C, Bruni)
$r_1 \bowtie r_2$ = **∅** (nessuna ennupla)

### Un join completo, con $n \times m$ ennuple
$r_1$ = (Rossi, B), (Neri, B); $r_2$ = (B, Mori), (B, Bruni)
$r_1 \bowtie r_2$ = (Rossi, B, Mori), (Rossi, B, Bruni), (Neri, B, Mori), (Neri, B, Bruni)

> [!note] Cardinalità del join *(integrazione: riassume quanto mostrato dagli esempi)*
> Il join di $r_1$ e $r_2$ contiene un numero di ennuple compreso tra **0** e $|r_1| \times |r_2|$.
> - Se il join coinvolge una **chiave** di $r_2$, il numero di ennuple è tra 0 e $|r_1|$.
> - Se coinvolge una chiave di $r_2$ **e** c'è un vincolo di integrità referenziale (FK), il numero di ennuple è **pari a** $|r_1|$.

**Esempio senza FK** ("NO FK!"): Impiegati = (Rossi, A), (Neri, B), (Bianchi, **C**), (Verdi, B); Reparti = (A, Mori), (B, Bruni)
→ join = (Rossi, A, Mori), (Neri, B, Bruni), (Verdi, B, Bruni): Bianchi si perde perché il reparto C non esiste.

**Esempio con FK** (Reparto in Impiegati è FK verso Reparti): Impiegati = (Rossi, A), (Neri, B), (Verdi, B), (Bianchi, C); Reparti = (A, Mori), (B, Bruni), (C, Gialli)
→ join = (Rossi, A, Mori), (Neri, B, Bruni), (Verdi, B, Bruni), (Bianchi, C, Gialli): **tutte** le ennuple di Impiegati compaiono una volta.

### Join, una difficoltà
$r_1$ = (Rossi, A), (Neri, B), (Bianchi, B); $r_2$ = (B, Mori), (C, Bruni) → join = (Neri, B, Mori), (Bianchi, B, Mori)
- alcune ennuple **non contribuiscono al risultato**: vengono "**tagliate fuori**" (dangling).

---

## 9. Join esterno
- Il join esterno **estende, con valori nulli**, le ennuple che verrebbero tagliate fuori da un join (interno).
- Esiste in **tre versioni**: **sinistro**, **destro**, **completo**.

Impiegati = (Rossi, A), (Neri, B), (Bianchi, B); Reparti = (B, Mori), (C, Bruni)

| Operazione | Risultato |
|---|---|
| Impiegati $\text{JOIN}_{\text{LEFT}}$ Reparti | (Neri, B, Mori), (Bianchi, B, Mori), (Rossi, A, **NULL**) |
| Impiegati $\text{JOIN}_{\text{RIGHT}}$ Reparti | (Neri, B, Mori), (Bianchi, B, Mori), (**NULL**, C, Bruni) |
| Impiegati $\text{JOIN}_{\text{FULL}}$ Reparti | (Neri, B, Mori), (Bianchi, B, Mori), (Rossi, A, **NULL**), (**NULL**, C, Bruni) |

- **sinistro**: mantiene tutte le ennuple del primo operando (completandole con NULL);
- **destro**: mantiene tutte le ennuple del secondo operando;
- **completo**: mantiene tutte le ennuple di entrambi.

---

## 10. Join e proiezioni

**Join e poi proiezioni**: $r_1$ = (Rossi, A), (Neri, B), (Bianchi, B); $r_2$ = (B, Mori), (C, Bruni)
- $r = r_1 \bowtie r_2$ = (Neri, B, Mori), (Bianchi, B, Mori)
- $\pi_{\text{Impiegato, Reparto}}(r)$ = (Neri, B), (Bianchi, B) ≠ $r_1$ (Rossi è perso)
- $\pi_{\text{Reparto, Capo}}(r)$ = (B, Mori) ≠ $r_2$

$$\pi_{X_1}(r_1 \bowtie r_2) \subseteq r_1$$

**Proiezioni e poi join**: $r$(Impiegato, Reparto, Capo) = (Neri, B, Mori), (Bianchi, B, Bruni), (Verdi, A, Bini)
- $\pi_{\text{Impiegato, Reparto}}(r)$ = (Neri, B), (Bianchi, B), (Verdi, A)
- $\pi_{\text{Reparto, Capo}}(r)$ = (B, Mori), (B, Bruni), (A, Bini)
- join delle proiezioni = (Neri, B, Mori), (Neri, B, Bruni), (Bianchi, B, Mori), (Bianchi, B, Bruni), (Verdi, A, Bini) → **più ennuple** dell'originale (ennuple "spurie")

$$\pi_{X_1}(r) \bowtie \pi_{X_2}(r) \supseteq r$$

---

## 11. Prodotto cartesiano e theta-join

### Prodotto cartesiano $\times$
- è un **join naturale su relazioni senza attributi in comune**;
- contiene **sempre** un numero di ennuple pari al **prodotto delle cardinalità** degli operandi (le ennuple sono tutte combinabili).

Impiegati(Impiegato, Reparto) = (Rossi, A), (Neri, B), (Bianchi, B); Reparti(Codice, Capo) = (A, Mori), (B, Bruni)

Impiegati JOIN Reparti (= prodotto cartesiano):

| Impiegato | Reparto | Codice | Capo |
|---|---|---|---|
| Rossi | A | A | Mori |
| Rossi | A | B | Bruni |
| Neri | B | A | Mori |
| Neri | B | B | Bruni |
| Bianchi | B | A | Mori |
| Bianchi | B | B | Bruni |

### Theta-join $\bowtie_F$
*(La definizione formale è un'integrazione; le slides mostrano l'esempio.)* Il prodotto cartesiano ha senso (quasi sempre) solo se seguito da una **selezione**:
$$r_1 \bowtie_F r_2 = \sigma_F(r_1 \times r_2)$$
Se $F$ è una congiunzione di uguaglianze si parla di **equi-join**.

$\text{Impiegati} \bowtie_{\text{Reparto} = \text{Codice}} \text{Reparti}$:

| Impiegato | Reparto | Codice | Capo |
|---|---|---|---|
| Rossi | A | A | Mori |
| Neri | B | B | Bruni |
| Bianchi | B | B | Bruni |

---

## 12. Esempi di interrogazioni

**Impiegati**(Matricola, Nome, Età, Stipendio)

| Matricola | Nome | Età | Stipendio |
|---|---|---|---|
| 7309 | Rossi | 34 | 45 |
| 5998 | Bianchi | 37 | 38 |
| 9553 | Neri | 42 | 35 |
| 5698 | Bruni | 43 | 42 |
| 4076 | Mori | 45 | 50 |
| 8123 | Lupi | 46 | 60 |

**Supervisione**(Impiegato, Capo)

| Impiegato | Capo |
|---|---|
| 7309 | 5698 |
| 5998 | 5698 |
| 9553 | 4076 |
| 5698 | 4076 |
| 4076 | 8123 |

### a) Matricola, nome, età e stipendio degli impiegati che guadagnano più di 40
$$\sigma_{\text{Stipendio} > 40}(\text{Impiegati})$$
→ 7309 Rossi, 5698 Bruni, 4076 Mori, 8123 Lupi

### b) Matricola, nome, età degli impiegati che guadagnano più di 40
$$\pi_{\text{Matricola, Nome, Età}}\big(\sigma_{\text{Stipendio} > 40}(\text{Impiegati})\big)$$
→ (7309, Rossi, 34), (5698, Bruni, 43), (4076, Mori, 45), (8123, Lupi, 46)

### c) I capi degli impiegati che guadagnano più di 40
$$\pi_{\text{Capo}}\big(\text{Supervisione} \bowtie_{\text{Impiegato} = \text{Matricola}} \sigma_{\text{Stipendio} > 40}(\text{Impiegati})\big)$$
→ Capo: 5698, 4076, 8123

### d) Impiegati che guadagnano più del proprio capo (matricola, nome e stipendio dell'impiegato e del capo)
Serve un **self-join** su Impiegati con ridenominazione:
$$\pi_{\text{Matr, Nome, Stip, MatrC, NomeC, StipC}}\Big(\sigma_{\text{Stipendio} > \text{StipendioC}}\big(\rho_{\text{MatricolaC, NomeC, EtàC, StipendioC} \leftarrow \text{Matricola, Nome, Età, Stipendio}}(\text{Impiegati}) \bowtie_{\text{MatricolaC} = \text{Capo}} (\text{Supervisione} \bowtie_{\text{Impiegato} = \text{Matricola}} \text{Impiegati})\big)\Big)$$

Passaggi:
1. $\text{Supervisione} \bowtie_{\text{Impiegato}=\text{Matricola}} \text{Impiegati}$ → (7309, 5698, Rossi, 34, 45), (5998, 5698, Bianchi, 37, 38), (9553, 4076, Neri, 42, 35), (5698, 4076, Bruni, 43, 42), (4076, 8123, Mori, 45, 50)
2. join con la copia ridenominata (MatricolaC, NomeC, EtàC, StipendioC) su MatricolaC = Capo → aggiunge i dati del capo (5698 Bruni 43 42; 5698 Bruni 43 42; 4076 Mori 45 50; 4076 Mori 45 50; 8123 Lupi 46 60)
3. selezione Stipendio > StipendioC → solo Rossi (45) con capo Bruni (42)

| MatricolaC | NomeC | StipendioC | Matricola | Nome | Stipendio |
|---|---|---|---|---|---|
| 5698 | Bruni | 42 | 7309 | Rossi | 45 |

### e) Matricole dei capi i cui impiegati guadagnano **tutti** più di 40
Non si può esprimere direttamente il "per tutti": si usa la **doppia negazione** → *capi* **meno** *capi che hanno almeno un impiegato che guadagna ≤ 40*.
$$\pi_{\text{Capo}}(\text{Supervisione}) \;-\; \pi_{\text{Capo}}\big(\text{Supervisione} \bowtie_{\text{Impiegato}=\text{Matricola}} \sigma_{\text{Stipendio} \leq 40}(\text{Impiegati})\big)$$

Passaggi:
1. $\sigma_{\text{Stipendio} \le 40}(\text{Impiegati})$ = (5998, Bianchi, 37, 38), (9553, Neri, 42, 35)
2. join con Supervisione → (5998, 5698, …), (9553, 4076, …) → $\pi_{\text{Capo}}$ = {5698, 4076}
3. $\pi_{\text{Capo}}(\text{Supervisione})$ = {5698, 4076, 8123}
4. differenza → **{8123}**

---

## 13. Equivalenza di espressioni
- Due espressioni sono **equivalenti** se producono **lo stesso risultato qualunque sia l'istanza attuale** della base di dati.
- L'equivalenza è importante in pratica perché i **DBMS cercano di eseguire espressioni equivalenti a quelle date, ma meno "costose"** (ottimizzazione).

---

## 14. Selezione con valori nulli

**Impiegati**: (7309, Rossi, Roma, 28), (5998, Neri, Milano, 45), (9553, Bruni, Milano, **NULL**)

$\sigma_{\text{Età} > 40}(\text{Impiegati})$ → solo Neri.
- la **condizione atomica è vera solo per valori non nulli**.

### Un risultato non desiderabile
$$\sigma_{\text{Età}>30}(\text{Persone}) \cup \sigma_{\text{Età}\le 30}(\text{Persone}) \neq \text{Persone}$$
$$\sigma_{\text{Età}>30 \,\lor\, \text{Età}\le 30}(\text{Persone}) \neq \text{Persone}$$
(le persone con età NULL non compaiono in nessuno dei due casi)

### Soluzione
- per riferirsi ai valori nulli esistono **forme apposite di condizioni**: **`IS NULL`**, **`IS NOT NULL`**.

$$\sigma_{\text{Età}>30}(\text{P}) \cup \sigma_{\text{Età}\le 30}(\text{P}) \cup \sigma_{\text{Età IS NULL}}(\text{P}) = \sigma_{\text{Età}>30 \,\lor\, \text{Età}\le 30 \,\lor\, \text{Età IS NULL}}(\text{P}) = \text{Persone}$$

Esempio: $\sigma_{(\text{Età} > 40) \text{ OR } (\text{Età IS NULL})}(\text{Impiegati})$ → Neri (45) e Bruni (NULL).

---

## 15. Architettura ANSI/SPARC e viste

### Architettura standard (ANSI/SPARC) a tre livelli per DBMS
```
utente  utente   utente   utente  utente
   \      |        |        |      /
 Schema esterno  Schema esterno  Schema esterno
            \        |        /
              Schema logico
                    |
              Schema interno
                    |
                   BD
```

### Viste (relazioni derivate)
- **Rappresentazioni diverse per gli stessi dati** (schema esterno).
- **Relazioni di base**: contenuto **autonomo**.
- **Relazioni derivate**: relazioni il cui contenuto è **funzione del contenuto di altre relazioni** (definito per mezzo di interrogazioni).

### Esempio
**Afferenza**(Impiegato, Reparto): (Rossi, A), (Neri, B), (Bianchi, B)
**Direzione**(Reparto, Capo): (A, Mori), (B, Bruni)

Una vista:
$$\text{Supervisione} = \pi_{\text{Impiegato, Capo}}(\text{Afferenza} \bowtie \text{Direzione})$$
- Afferenza $\bowtie$ Direzione = (Rossi, A, Mori), (Neri, B, Bruni), (Bianchi, B, Bruni)
- Supervisione = (Rossi, Mori), (Neri, Bruni), (Bianchi, Bruni)

### Interrogazioni sulle viste
Sono eseguite **sostituendo alla vista la sua definizione**:
$$\sigma_{\text{Capo}=\text{'Leoni'}}(\text{Supervisione}) \;\Rightarrow\; \sigma_{\text{Capo}=\text{'Leoni'}}\big(\pi_{\text{Impiegato, Capo}}(\text{Afferenza} \bowtie \text{Direzione})\big)$$

### Viste, motivazioni
- **Schema esterno**: ogni utente vede solo
  - ciò che gli interessa e nel modo in cui gli interessa, senza essere distratto dal resto;
  - ciò che è autorizzato a vedere (**autorizzazioni**).
- **Strumento di programmazione**: si può **semplificare la scrittura di interrogazioni**: espressioni complesse e sottoespressioni ripetute.
- **Utilizzo di programmi esistenti su schemi ristrutturati**.

Invece:
- L'utilizzo di viste **non influisce sull'efficienza** delle interrogazioni.

### Esempio, senza la vista
Per trovare gli impiegati che hanno lo stesso capo di Rossi occorre ripetere il join Afferenza ⋈ Direzione due volte (una copia ridenominata ImpR, RepR):
- (Afferenza ⋈ Direzione) = (Rossi, A, Mori), (Neri, B, Bruni), (Bianchi, B, Bruni)
- copia ridenominata e ristretta a Rossi: (ImpR = Rossi, RepR = A, Capo = Mori)
- join sul Capo → (Rossi, A, Mori, Rossi, A)

### Esempio, con la vista
Con la vista **Supervisione**(Impiegato, Capo) = (Rossi, Mori), (Neri, Bruni), (Bianchi, Bruni) basta un join della vista con sé stessa sul Capo: l'espressione è molto più semplice.

### Viste e aggiornamenti, attenzione
Afferenza = (Rossi, A), (Neri, B), (Verdi, A); Direzione = (A, Mori), (B, Bruni), (C, Bruni)
Supervisione = (Rossi, Mori), (Neri, Bruni), (Verdi, Mori)

- Vogliamo inserire, **nella vista**, il fatto che **Lupi ha come capo Bruni**; oppure che **Belli ha come capo Falchi**: come facciamo?
- Per Lupi/Bruni: in Afferenza si potrebbe inserire (Lupi, **B**) **oppure** (Lupi, **C**) → entrambe le scelte producono la stessa vista: **ambiguità** (**?**).
- Per Belli/Falchi non esiste alcun reparto diretto da Falchi.

### Viste e aggiornamenti
- "**Aggiornare una vista**": modificare le relazioni di base in modo che la vista, "ricalcolata", rispecchi l'aggiornamento.
- L'aggiornamento sulle relazioni di base corrispondente a quello specificato sulla vista **deve essere univoco**.
- In generale però **non è univoco**!
- **Ben pochi aggiornamenti sono ammissibili sulle viste**.

(Le viste in SQL — `CREATE VIEW` — non sono trattate nelle slides 2k27.)
