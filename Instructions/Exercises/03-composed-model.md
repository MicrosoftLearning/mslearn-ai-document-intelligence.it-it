---
lab:
  title: Creare un modello di Document Intelligence composto
  module: Module 6 - Document Intelligence
---

# Creare un modello di Document Intelligence composto

In questo esercizio verranno creati e verrà eseguito di due modelli personalizzati che analizzano moduli fiscali diversi. Si creerà quindi un modello composto che include entrambi i modelli personalizzati. Si testerà il modello inviando un modulo e si verificherà che riconosca correttamente il tipo di documento e i campi con etichetta.

## Configurare le risorse

Si userà uno script per creare la risorsa Riconoscimento modulo di Azure, un account di archiviazione con moduli di esempio e un gruppo di risorse:

1. Avviare Visual Studio Code.
1. Aprire il riquadro comandi (MAIUSC+CTRL+P) ed eseguire un comando **Git: Clone** per clonare il repository `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` in una cartella locale. Non è importante usare una cartella specifica.
1. Dopo la clonazione del repository, aprire la cartella in Visual Studio Code.

    > **Nota**: Se Visual Studio Code visualizza un messaggio popup per chiedere se si considera attendibile il codice che si apre, fare clic sull'opzione **Sì, considero attendibili gli autori** nel popup.
    
    > **Nota**: se viene richiesto di aggiungere gli asset necessari per la compilazione e il debug, selezionare **Non adesso**. Se appaiono altri popup di Visual Studio Code, è possibile ignorarli in sicurezza.

1. Espandere la cartella **Labfiles** nel riquadro sinistro e fare clic con il pulsante destro del mouse sulla directory **03-composed-mode**. Selezionare l'opzione per aprire nel terminale integrato ed eseguire lo script seguente:

    ```powershell
    az login --output none
    ```

    > **Nota**: Se viene visualizzato un errore relativo all'assenza di sottoscrizioni attive e all'autenticazione a più fattori abilitata, potrebbe essere necessario accedere prima al portale di Azure all’indirizzo `https://portal.azure.com`, quindi eseguire nuovamente lo `az login`.

1. Quando verrà richiesto, accedere alla sottoscrizione di Azure. Tornare quindi a Visual Studio Code e attendere il completamento del processo di accesso.
1. Nel terminale integrato, eseguire il comando seguente per configurare le risorse:

   ```powershell
   ./setup.ps1
   ```

   > **IMPORTANTE**: L'ultima risorsa creata dallo script è il servizio Document Intelligence di Azure AI. Se il comando ha esito negativo perché è già presente una risorsa di livello F0, usare tale risorsa per questo lab o crearne una manualmente usando il livello S0 nel portale di Azure.

## Creare il modello personalizzato 1040 Forms

Per creare un modello composto, è prima necessario creare due o più modelli personalizzati. Per creare il primo modello personalizzato:

1. In una nuova scheda del browser, passare ad **Azure AI Document Intelligence Studio** all’indirizzo `https://documentintelligence.ai.azure.com/studio`
1. Scorrere verso il basso, quindi, in **Modelli personalizzati**, selezionare **Modello di estrazione personalizzato**.
1. Se viene chiesto di accedere all'account, usare le credenziali di Azure.
1. Se viene chiesto quale risorsa di Informazioni sui documenti di Azure AI usare, selezionare la sottoscrizione e il nome della risorsa usati durante la creazione della risorsa Informazioni sui documenti di Azure AI.
1. In **Progetti personali** selezionare **+ Crea un progetto**.
1. Nella casella di testo **Nome del progetto** digitare **1040 Forms** e quindi selezionare **Continua**.
1. Nella pagina **Configura risorsa servizio** nell'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione di Azure.
1. Nell'elenco a discesa **Gruppo di risorse**, selezionare **DocumentIntelligenceResources&lt;xxxx&gt;** creato automaticamente.
1. Nell'elenco a discesa **Document Intelligence di Azure AI o Risorsa del servizio di Azure AI**, selezionare **DocumentIntelligence&lt;xxxx&gt;**.
1. Nell'elenco a discesa **Versione API**, accertarsi che l'opzione **2023-10-31-preview** sia selezionata, quindi selezionare **Continua**.
1. Nella pagina **Connetti origine dati training**, nell'elenco a discesa **Sottoscrizione**, selezionare la sottoscrizione di Azure.
1. Nell'elenco a discesa **Gruppo di risorse**, selezionare **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Nell'elenco a discesa **Account di archiviazione** selezionare l'unico account di archiviazione elencato. Se nella sottoscrizione sono presenti più account di archiviazione, scegliere quello che inizia con *docintelstorage*
1. Nell'elenco a discesa **Contenitore BLOB** selezionare **1040examples** e quindi selezionare **Continua**.
1. Nella pagina **Rivedi e crea** selezionare **Crea progetto**.
1. Selezionare **Esegui layout** nella finestra popup *Avvia etichettatura ora* e attendere il completamento dell'analisi.

## Assegnare un'etichetta al modello personalizzato 1040 Forms

A questo punto, assegnare un'etichetta ai campi nei moduli di esempio:

1. Nella pagina **Dati etichetta**, in alto a destra nella pagina, selezionare **+ Aggiungi un campo**, quindi selezionare **Campo**.
1. Digitare **FirstName** e quindi premere *INVIO*.
1. Selezionare il documento denominato **f1040_1.pdf** nell'elenco a sinistra, selezionare **John**, quindi selezionare **FirstName**.
1. In alto a destra nella pagina, selezionare **+ Aggiungi un campo**, quindi selezionare **Campo**.
1. Digitare **LastName** e quindi premere *INVIO*.
1. Nel documento selezionare **Doe** e quindi **LastName**.
1. In alto a destra nella pagina, selezionare **+ Aggiungi un campo**, quindi selezionare **Campo**.
1. Digitare **City** e quindi premere *INVIO*.
1. Nel documento selezionare **Los Angeles** e quindi **City**.
1. In alto a destra nella pagina, selezionare **+ Aggiungi un campo**, quindi selezionare **Campo**.
1. Digitare **State** e quindi premere *INVIO*.
1. Nel documento selezionare **CA** e quindi **State**.
1. Ripetere il processo di etichettatura per i moduli rimanenti nell'elenco a sinistra usando le etichette create. Assegnare un'etichetta agli stessi quattro campi: *FirstName*, *LastName*, *City* e *State*.

> **IMPORTANTE** Per gli scopi di questo esercizio vengono usati solo cinque moduli di esempio e vengono etichettati solo quattro campi. Nei modelli reali è consigliabile usare il maggior numero possibile di esempi per ottimizzare l'accuratezza e l'attendibilità delle previsioni. È anche consigliabile assegnare un'etichetta a tutti i campi disponibili nei moduli, anziché solo a quattro campi.

## Eseguire il training del modello personalizzato 1040 Forms

Dopo aver assegnato un'etichetta ai moduli di esempio, è possibile eseguire il training del primo modello personalizzato:

1. In Informazioni sui documenti di Azure AI Studio, selezionare **Esegui training**.
1. Nella finestra di dialogo **Esegui il training di un modello** nella casella di testo **ID modello** digitare **1040FormsModel**.
1. Nell'elenco a discesa **Modalità di compilazione** selezionare **Modello** e quindi selezionare **Eseguire il training**. 
1. Nella finestra di dialogo **Training in corso** selezionare **Vai a Modelli**.

## Creare il modello personalizzato 1099 Forms

A questo punto, è necessario creare un secondo modello di cui verrà eseguito il training nei moduli fiscali 1099 di esempio:

1. In Azure AI Document Intelligence Studio, selezionare **Modello di estrazione personalizzato**.
1. In **Progetti personali** selezionare **+ Crea un progetto**.
1. Nella casella di testo **Nome del progetto** digitare **1099 Forms** e quindi selezionare **Continua**.
1. Nella pagina **Configura risorsa servizio** nell'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione di Azure.
1. Nell'elenco a discesa **Gruppo di risorse**, selezionare **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Nell'elenco a discesa **Document Intelligence di Azure AI o Risorsa del servizio di Azure AI**, selezionare **DocumentIntelligence&lt;xxxx&gt;**.
1. Nell'elenco a discesa **Versione API**, accertarsi che l'opzione **20223-10-31-preview** sia selezionata, quindi selezionare **Continua**.
1. Nella pagina **Connetti origine dati training**, nell'elenco a discesa **Sottoscrizione**, selezionare la sottoscrizione di Azure.
1. Nell'elenco a discesa **Gruppo di risorse**, selezionare **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Nell'elenco a discesa **Account di archiviazione** selezionare l'unico account di archiviazione elencato.
1. Nell'elenco a discesa **Contenitore BLOB** selezionare **1099examples** e quindi selezionare **Continua**.
1. Nella pagina **Rivedi e crea** selezionare **Crea progetto**.
1. Selezionare **Esegui layout** nella finestra popup *Avvia etichettatura ora* e attendere il completamento dell'analisi.

## Assegnare un'etichetta al modello personalizzato 1099 Forms

Assegnare ora un'etichetta ai moduli di esempio con alcuni campi:

1. Nella pagina **Dati etichetta**, in alto a destra nella pagina, selezionare **+ Aggiungi un campo**, quindi selezionare **Campo**.
1. Digitare **FirstName** e quindi premere *INVIO*.
1. Selezionare il documento denominato **f1099msc_payer.pdf** nell'elenco a sinistra, selezionare **John**, quindi selezionare **FirstName**.
1. In alto a destra nella pagina, selezionare **+ Aggiungi un campo**, quindi selezionare **Campo**.
1. Digitare **LastName** e quindi premere *INVIO*.
1. Nel documento selezionare **Doe** e quindi **LastName**.
1. In alto a destra nella pagina, selezionare **+ Aggiungi un campo**, quindi selezionare **Campo**.
1. Digitare **City** e quindi premere *INVIO*.
1. Nel documento selezionare **New Haven** e quindi **City**.
1. In alto a destra nella pagina, selezionare **+ Aggiungi un campo**, quindi selezionare **Campo**.
1. Digitare **State** e quindi premere *INVIO*.
1. Nel documento selezionare **CT** e quindi **State**.
1. Ripetere il processo di assegnazione dell'etichetta per i moduli rimanenti nell'elenco a sinistra. Assegnare un'etichetta agli stessi quattro campi: *FirstName*, *LastName*, *City* e *State*.

## Eseguire il training del modello personalizzato 1099 Forms

È ora possibile eseguire il training del secondo modello personalizzato:

1. In Informazioni sui documenti di Azure AI Studio, selezionare **Esegui training**.
1. Nella finestra di dialogo **Esegui il training di un modello** nella casella di testo **ID modello** digitare **1099FormsModel**.
1. Nell'elenco a discesa **Modalità di compilazione** selezionare **Modello** e quindi selezionare **Eseguire il training**.
1. Nella finestra di dialogo **Training in corso** selezionare **Vai a Modelli**.
1. Il processo di training potrebbe richiedere alcuni minuti. Aggiornare occasionalmente il browser fino a quando entrambi i modelli non visualizzano lo stato **riuscito**.

## Creare e assemblare un modello composto

I due modelli personalizzati, che analizzano i moduli fiscali 1040 e 1099, sono ora completi. È possibile procedere con la creazione del modello composto:

1. Nella pagina Modelli di Informazioni sui documenti di Azure AI selezionare **sia 1040FormsModel** che **1099FormsModel**.
1. Nella parte superiore dell'elenco dei modelli selezionare **Componi**.
1. Nella finestra di dialogo **Componi un nuovo modello** nella casella di testo **ID modello** digitare **TaxFormsModel** e quindi selezionare **Componi**. Informazioni sui documenti di Azure AI crea il modello composto e lo visualizza nell'elenco dei modelli personalizzati:

## Usare il modello composto

Ora che il modello composto è completo, è possibile testarlo con un modulo di esempio:

1. In Azure AI Document Intelligence Studio, selezionare la pagina **Test**, quindi selezionare il modulo **TaxFormsModel** dal menu a discesa.
1. Selezionare **Cerca file**, quindi passare al percorso in cui è stato clonato il repository.
1. Selezionare **03-composed-model/trainingdata/TestDoc/f1040_7.pdf**, quindi selezionare **Apri**.
1. Selezionare **Esegui analisi**. Informazioni sui documenti di Azure AI analizza il modulo usando il modello composto.
1. Il documento analizzato è un esempio del modulo fiscale 1040. Controllare la proprietà **DocType** per verificare se è stato usato il modello personalizzato corretto. Controllare anche i valori **FirstName**, **LastName**, **City** e **State** identificati dal modello.

## Pulire le risorse

Dopo aver esaminato il funzionamento dei modelli composti, rimuovere le risorse create nella sottoscrizione di Azure.

1. Nel **portale di Azure** all’indirizzo `https://portal.azure.com/`, selezionare **Gruppi di risorse**.
1. Nell’elenco di **Gruppi di risorse**, selezionare il gruppo **DocumentIntelligenceResources&lt;xxxx&gt;** creato, quindi selezionare **Elimina gruppo di risorse**.
1. Nella casella di testo **DIGITARE IL NOME DEL GRUPPO DI RISORSE**, digitare il nome del gruppo di risorse, quindi selezionare **Elimina** per eliminare la risorsa di Document Intelligence e l'account di archiviazione.

## Altre informazioni

- [Comporre modelli personalizzati v3.0](/azure/ai-services/document-intelligence/concept-composed-models)
