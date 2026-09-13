# Sezioni piane e cavità: un metodo nuovo per un vecchio problema di cucina

<blockquote style="font-family: Georgia, 'Times New Roman', serif; font-size: 0.95em; font-style: italic; border-left: 3px solid #999; padding-left: 1em; margin-left: 0; color: #444;">
«La filosofia è scritta in questo grandissimo libro che continuamente ci sta
aperto innanzi a gli occhi (io dico l'universo), ma non si può intendere se
prima non s'impara a intender la lingua, e conoscer i caratteri, ne' quali è
scritto. Egli è scritto in lingua matematica, e i caratteri son triangoli,
cerchi, ed altre figure geometriche, senza i quali mezi è impossibile a
intenderne umanamente parola; senza questi è un aggirarsi vanamente per un
oscuro laberinto.»
<br>— Galileo Galilei, <em>Il Saggiatore</em> (1623)
</blockquote>

**Perché questa idea è pubblica.** Andrea Petrucci, ideatore del metodo:

> «Pubblichiamo: pensiamo di essere i primi a farlo, poiché non abbiamo trovato
> letteratura scientifica al riguardo, ed il sapere rende liberi ed è un bene
> comune.»

*Sapere Aude* — abbi il coraggio di usare la tua ragione. **Nessuno deve poter
guadagnare come privato da un'idea che deve restare pubblica**: la scienza
esiste per essere condivisa, non chiusa in un brevetto.

**Tutti i numeri di questo documento sono verificabili da chiunque** — nessun
codice viene mostrato qui, solo la matematica e i risultati, riproducibili con
gli script citati in fondo a ogni sezione.

---

## 1 · Il problema, in una frase

Per calcolare quanto tempo serve a cuocere o congelare un pezzo di cibo,
bisogna prima sapere **che forma ha**. Per un pezzo irregolare — un pollo
intero, una mezzena, un pesce — l'unica scelta pratica finora era chiuderlo
dentro una **lastra infinita** (prudente, ma sovrastima parecchio) o una
**scatola immaginaria**. Su un cilindro di 8 cm di diametro, la scatola che lo
contiene ha un volume **27,3% più grande** del vero — non un errore di misura,
matematica pura: una scatola attorno a un corpo tondo lo sovrastima sempre di
4/π − 1, attorno a una sfera del **91%**.

## 2 · Il metodo: sezioni piane, non un modello 3D

Invece di una forma unica scelta in anticipo, il pezzo viene immaginato
tagliato a fette sottili e regolari — come un salame affettato — ciascuna
misurata (o, quando non si può, dedotta con cautela dal peso). Il volume si
ottiene sommandole, con il **principio di Cavalieri**: lo stesso integrale che
si usa per un cono o una sfera, applicato sezione per sezione alla forma reale
invece che a una forma assunta prima di guardare il pezzo.

Sullo stesso cilindro da 603,19 cm³ (valore vero, formula esatta):

| Metodo | Volume calcolato | Scarto dal valore vero |
|---|---|---|
| Scatola | 768,00 cm³ | **+27,3%** |
| Sezioni piane | 602,80 cm³ | **−0,06%** |

Oltre **450 volte più preciso**, sulla stessa identica misura. Confermato
anche su un ellissoide oblato (1340,07 contro 1340,41 cm³, −0,03%) e su un
tubo cavo (spessore parete 2,99949 contro 3,00000 cm, −0,02%).

*Riproducibile: `node tests/plane_sections_report.js`.*

## 3 · Quanto cambia un tempo di CONGELAMENTO vero

Confrontando la **lastra infinita** — la geometria che la comunità
scientifica usa e approva da decenni per un pezzo irregolare, tramite il
metodo di Pham (1986) — con le sezioni piane, sulle stesse identiche letture:

| Pezzo | Lastra (metodo standard) | Sezioni piane (nostro metodo) | Differenza |
|---|---|---|---|
| Cilindro 8 cm | 7,09 h | 3,02 h | **−57%** |
| Rollé legato | 11,86 h | 4,76 h | **−60%** |
| Mezzena di manzo | 20,57 h | 8,45 h | **−59%** |
| Pesce intero | 10,59 h | 3,83 h | **−64%** |

Il tempo stimato **si riduce fra il 57% e il 64%** rispetto al metodo
standard. Importante: questo dice che la **geometria** è più fedele, non
prova da sola che il **tempo** lo sia altrettanto — vedi §7.

## 4 · Il problema nuovo: la carcassa CAVA

Un pollo, un tacchino, un coniglio interi non sono pieni: hanno una cavità
interna. Le equazioni di Pham sono state tarate su corpi **pieni** — usarle su
un corpo cavo significherebbe applicare una formula fuori dal suo dominio, e
questa app rifiuta di farlo in silenzio. Serviva un metodo diverso, per due
problemi distinti — la **cottura** (il forno scalda) e il **congelamento** (il
freezer raffredda) — entrambi risolti con lo stesso principio.

### Il principio comune: riscaldamento/raffreddamento ASIMMETRICO

**Dalle misure alle temperature**, il percorso è: si misurano (o si deducono
dal peso) le sezioni della carcassa → si ricava lo spessore di carne fra
l'esterno e la cavità → si risolve la conduzione del calore attraverso quello
spessore, **con due condizioni diverse sulle due facce della stessa parete**
— da un lato il forno o il freezer, dall'altro la cavità interna.

I modelli classici (Cleland, Pham) assumono la **stessa** temperatura e lo
stesso coefficiente di scambio su tutta la superficie del pezzo — è
un'ipotesi di comodo che funziona per un pezzo pieno, ma che una cavità rompe
apposta. Il nostro motore risolve un problema fisico diverso: conduzione con
condizioni al contorno **asimmetriche**, verificato con un metodo numerico
(differenze finite) indipendente dalla formula classica.

- **In cottura**: la cavità si scalda e — se la carcassa lo permette — vi si
  forma una pozza d'acqua che evapora via via che si scalda (mai vapore fisso
  a 100°C dal primo istante: un bilancio energetico vero, corretto due volte
  in diretta da Andrea prima di questa versione).
- **In congelamento**: la cavità è **vuota** (aria, non pozza) — nessuna
  evaporazione. La incertezza vera non è quanto bene l'aria si scambia con la
  parete (irrilevante: l'aria ha così poca massa che si equilibra in secondi,
  contro le ore del problema), ma se la fessura naturale che ogni animale ha
  (e che varia con la sua taglia) lascia entrare l'aria del freezer o no.

## 5 · Il nuovo motore di congelamento — validato in tre modi indipendenti

| Controllo | Cosa verifica | Risultato |
|---|---|---|
| **Contro Pham** (caso simmetrico) | Con le stesse condizioni sui due lati, il nuovo metodo deve dare lo stesso tempo del metodo standard già in uso | **9,5% di differenza** (soglia 20%: sono due metodi radicalmente diversi — una formula chiusa contro una simulazione numerica) |
| **Conservazione dell'energia** | Il calore che entra ed esce, integrato nel tempo, deve corrispondere all'energia realmente guadagnata o persa (calore sensibile + calore latente del ghiaccio che si forma) | **1,49% di differenza** (soglia 2%) |
| **Convergenza numerica** | Raffinando la griglia di calcolo, l'errore deve ridursi in modo prevedibile | **Esattamente 4 volte più piccolo** ad ogni raffinamento — la convergenza teorica perfetta |

*Riproducibile: `python docs/asymmetric_wall_freezing_conduction.py`.*

## 6 · Due esempi, stesse misure — una carcassa piena, una cava

Parete di **6 cm** di spessore in entrambi i casi, stessa temperatura iniziale
(4°C), stesso freezer (−30°C), stesso target (−18°C):

| Carcassa | Tempo per raggiungere −18°C |
|---|---|
| **Piena** (Pham, metodo standard) | **6,15 h** |
| **Cava, fessura sigillata** (il nostro caso prudente) | **12,61 h** |
| **Cava, con una vera fessura verso il freezer** | **7,17 h** |

**Perché la cava impiega più tempo, a parità di spessore.** In un pezzo pieno
il freddo arriva **da entrambi i lati** verso il centro, dimezzando di fatto
il cammino. In un pezzo cavo, se la cavità è sigillata, il freddo arriva **da
un solo lato**: la faccia interna deve aspettare che tutto lo spessore si sia
raffreddato, senza aiuto dall'altro lato. Se invece la carcassa ha una vera
fessura che lascia entrare aria fredda, il tempo si avvicina a quello di un
pezzo pieno. **Questa è oggi la domanda aperta più importante**: quanto è
grande, in un animale vero, quella fessura?

## 7 · L'onestà che conta di più

**Sapere che la geometria è più precisa non vuol dire sapere che il tempo lo
sia altrettanto.** Le formule che trasformano una forma in un tempo sono
tarate su solidi idealizzati — dargli una forma più fedele non garantisce
automaticamente un tempo più fedele. Questa app è già stata scottata da
esattamente questo: **in agosto una coppia di fattori di forma più realistici
aveva reso i tempi di congelamento fino al 31% troppo corti**, prima che
l'errore venisse trovato — quella volta nel verso pericoloso.

Quindi: la geometria è **misurabilmente** migliore (§2), i numeri sono
verificabili con una calcolatrice. Se anche il tempo lo sia, **non lo sappiamo
ancora** — serve una sonda in un vero arrosto o in una vera carcassa
congelata, e questa verifica non l'abbiamo fatta.

## 8 · Le fonti consultate — anche quelle scartate

Per costruire il modello della cavità abbiamo scritto, l'**11 settembre
2026**, alla Biblioteca Civica Bertoliana di Vicenza, chiedendo **13 fonti**
fra articoli e manuali. Esito, onestamente riportato:

- **Trovata e consultabile gratis, in sede**: Singh & Heldman, *Introduction
  to Food Engineering* (Biblioteca La Vigna, Vicenza).
- **Trovata ma solo a pagamento** (prestito interbibliotecario, €10): Datta,
  *Biological and Bioenvironmental Heat and Mass Transfer* — **scartata**, per
  principio dichiarato: solo fonti gratuite.
- **Irreperibile per principio**: ASHRAE Handbook – Refrigeration completo,
  acquistabile solo direttamente dall'editore — **scartato**.
- **Irreperibile per circuito**: Polley, Snyder & Kotnour (1980), consultabile
  solo alla sede dell'International Institute of Refrigeration — **scartato**.
- **Nessuna risposta dalla biblioteca** su altre 7 voci (Cleland 1987, Cleland
  & Earle anni '80, Hossain et al. 1992, Siripon/Tansakul/Mittal 2007,
  Incropera & DeWitt, Sun, Rahman).

**In parallelo**, tre richieste dirette agli autori stessi:
- **G. S. Mittal** (30 luglio 2026) e **Ampawan Tansakul** (28 agosto 2026),
  co-autori dell'unico precedente pubblicato che tratta un pollo intero come
  pila di sezioni trasversali (Siripon, Tansakul & Mittal, 2007) — **nessuna
  risposta da nessuno dei due**.
- **Ors Petnehazy** (11 settembre 2026), autore delle uniche tomografie
  pubbliche di una cavità di tacchino — email inviata, recapito non
  confermato. Il suo **dataset 2022 (CC BY, gratuito) è già stato scaricato e
  usato**, senza bisogno di permesso: è l'unica fonte di morfometria animale
  realmente in uso in questo lavoro, oggi.

**Totale onesto: 16 fonti cercate per questo lavoro specifico (13 tramite
biblioteca + 3 dirette agli autori), 2 realmente disponibili gratis, 1
dataset pubblico in uso, tutto il resto assente o scartato.** La cavità di una
carcassa reale resta, per ora, un'assunzione dichiarata — non una misura.

## 9 · I motori di controllo — cosa sono, quanti ne abbiamo

**Un motore di controllo non ripete un calcolo: lo verifica per un'altra
strada.** Un secondo calcolo con lo stesso metodo non è un controllo, è un
eco. Gli esempi di questo documento (§5) sono un caso da manuale: il nuovo
motore di congelamento è stato verificato contro (a) una formula
completamente diversa (Pham), (b) una legge di conservazione che non sa nulla
del nostro modello (l'energia), (c) il comportamento noto di uno schema
numerico sotto raffinamento della griglia.

**Quanti ne abbiamo**: l'intera app è coperta da **82 suite di test
automatici, 20.150 asserzioni**, rilanciate ad ogni modifica e confrontate
meccanicamente contro i numeri dichiarati nei documenti del progetto (un
controllo di controlli). Fra queste, un nucleo di oltre **26 motori di
controllo veri e propri** — catalogati con una loro storia, spesso nata da un
bug reale trovato e corretto — copre geometria, termica, congelamento,
letalità/sicurezza e le affermazioni stesse che l'app fa su di sé.

*Riproducibile: `node tests/run_all.js` dalla radice del repository.*

## 10 · Perché pubblichiamo ora

**Per quanto ne sappiamo, nessuna fonte pubblica applica il metodo a sezioni
piane ripetute — con cavità dedotta o misurata, per riscaldamento e per
raffreddamento — a un tempo di cottura o congelamento in cucina.** Non
chiediamo credito su un'idea vecchia (Cavalieri, XVII secolo): chiediamo che
risulti, con data certa, che siamo stati i primi a scriverla e pubblicarla
applicata a questo problema specifico. Nessun brevetto: il sapere è un bene
comune, e nessuno deve poterne fare un guadagno privato.

## Come citare questo lavoro

KitchenMetrics è **gratuita**, senza pubblicità: non c'è alcun secondo fine
commerciale in questa richiesta. Se usi il metodo delle sezioni piane ripetute
con cavità (dedotta o misurata) — o questo testo — in un tuo lavoro, un
progetto, un articolo, un'altra app, citare **Andrea Petrucci** come ideatore
sarebbe semplicemente **un gesto di onestà intellettuale**: non un obbligo che
possiamo imporre legalmente su un'idea matematica (il copyright protegge
l'espressione, non il metodo — vedi `LICENSE-SCOPE.md`), solo quello che ci
si aspetterebbe fra persone corrette.

> Petrucci, A. (2026). *Repeated plane sections with a deduced or measured
> cavity, applied to cooking and freezing time.* KitchenMetrics.
> https://github.com/apetrucci90-rgb/KitchenMetrics

---

*Documento pubblicato il 13 settembre 2026. Ogni numero è riproducibile dagli
script citati in questo repository pubblico. Versione inglese:
`POST-GITHUB-SEZIONI-PIANE-EN.md`.*
