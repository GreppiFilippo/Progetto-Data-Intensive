# Progetto Data Intensive

## Contesto e obiettivo

Il dataset utilizzato raccoglie dieci anni di dati clinici, relativi al periodo **1999-2008**, provenienti da **130 ospedali statunitensi** e reti sanitarie integrate. Ogni record rappresenta un singolo **ricovero ospedaliero** di un paziente con diagnosi di diabete, con durata della degenza compresa tra **1 e 14 giorni**. Durante il ricovero il paziente può essere stato sottoposto a esami di laboratorio, procedure cliniche, terapie farmacologiche e monitoraggio medico.

Il problema affrontato è la **predizione della riammissione ospedaliera** dopo la dimissione. Il modello deve stabilire se un paziente non verrà riammesso, verrà riammesso entro 30 giorni, oppure verrà riammesso dopo più di 30 giorni, utilizzando le informazioni demografiche, cliniche, amministrative e terapeutiche disponibili durante il ricovero.

La motivazione è sia clinica sia organizzativa. Una gestione non ottimale del diabete in fase di degenza e di dimissione può tradursi in nuovi ricoveri a breve distanza, con un duplice costo: maggiore spesa per le strutture sanitarie e peggioramento delle condizioni del paziente, con aumento del rischio di complicanze. Un modello capace di stimare in anticipo il rischio di riammissione può quindi supportare le decisioni cliniche, aiutando a individuare i pazienti più fragili prima della dimissione e a orientare interventi mirati come controlli più accurati, modifiche terapeutiche, educazione del paziente o follow-up più ravvicinati.

---

## Fonte e struttura del dataset

Il dataset è disponibile presso l'**UCI Machine Learning Repository**:

[Diabetes 130-US hospitals for years 1999-2008](https://archive.ics.uci.edu/dataset/296/diabetes+130-us+hospitals+for+years+1999-2008)

Ogni riga corrisponde a un episodio di ricovero e descrive le caratteristiche del paziente, il percorso clinico durante la degenza e le terapie ricevute. Le informazioni disponibili includono:

- caratteristiche demografiche del paziente (età, genere, etnia);
- informazioni amministrative e cliniche sul ricovero (tipo di ammissione, provenienza del paziente, durata della degenza, modalità di dimissione);
- diagnosi principali e secondarie, codificate tramite codici ICD-9;
- numero di visite, procedure, farmaci ed esami di laboratorio effettuati;
- risultati di alcuni test clinici, come emoglobina glicata (HbA1c) e glicemia;
- terapie farmacologiche somministrate, con particolare attenzione ai farmaci per il diabete;
- la variabile target relativa alla riammissione del paziente.

Non tutte queste variabili verranno mantenute nel modello finale: l'analisi esplorativa valuterà l'utilità di ciascuna feature e motiverà le eventuali esclusioni.

---

## Variabile target e formulazione del problema

La variabile target è **`readmitted`**, che descrive se e quando un paziente è stato riammesso in ospedale dopo la dimissione. Il problema viene impostato come un task di **classificazione supervisionata multiclasse**: il modello non si limita a distinguere pazienti riammessi e non riammessi, ma assegna ogni ricovero a una delle tre classi disponibili:

- **`NO`** — il paziente non è stato riammesso;
- **`>30`** — il paziente è stato riammesso dopo più di 30 giorni dalla dimissione;
- **`<30`** — il paziente è stato riammesso entro 30 giorni dalla dimissione.

Rispetto a una formulazione binaria, l'impostazione multiclasse offre un'informazione più articolata, perché distingue tra riammissioni precoci, riammissioni tardive e assenza di riammissione. In particolare, la classe **`<30`** è quella di maggiore interesse clinico: una riammissione entro 30 giorni può segnalare una fragilità più marcata del paziente, una gestione non ottimale della dimissione o la necessità di interventi più tempestivi. È anche, come vedremo, la classe più difficile da predire, sia perché clinicamente più sfumata sia per la sua scarsa numerosità.

Le tre classi sono infatti **fortemente sbilanciate**: la maggioranza dei ricoveri non dà luogo a riammissione, mentre la classe `<30` rappresenta solo circa un decimo delle osservazioni. Questo sbilanciamento è un aspetto centrale del problema e condizionerà diverse scelte metodologiche nelle fasi successive, dalla strategia di valutazione (in cui non ci si potrà basare sulla sola accuratezza) alle tecniche di gestione delle classi minoritarie adottate in fase di addestramento.

Coerentemente con questo quadro, il modello predittivo va inteso come uno strumento di **stratificazione del rischio** a supporto delle decisioni cliniche e organizzative, più che come un classificatore definitivo: il suo valore sta nell'aiutare a distinguere diversi livelli di rischio di riammissione e a orientare di conseguenza eventuali azioni preventive.