---
lab:
  title: Estrarre dati dai moduli
  module: Module 6 - Document Intelligence
---

# Estrarre dati dai moduli

Si supponga che un'azienda richieda attualmente ai dipendenti di compilare manualmente i fogli degli ordini e di immettere i dati in un database. L'azienda ha richiesto di usare i servizi di intelligenza artificiale per migliorare il processo di immissione dei dati. Si decide di creare un modello di Machine Learning per leggere il modulo e produrre dati strutturati che possano essere usati per aggiornare automaticamente un database.

**Informazioni sui documenti di Azure AI** è un servizio di intelligenza artificiale di Azure che consente agli utenti di creare software di elaborazione dati automatizzato. Questo software può estrarre testo, coppie chiave/valore e tabelle dai documenti modulo usando il riconoscimento ottico dei caratteri (OCR). Informazioni sui documenti di Azure AI include modelli predefiniti per il riconoscimento di fatture, ricevute e biglietti da visita. Il servizio offre anche la possibilità di eseguire il training di modelli personalizzati. Questo esercizio si concentrerà sulla creazione di modelli personalizzati.

## Prepararsi a sviluppare un’app in Visual Studio Code

A questo punto si userà l’SDK del servizio per sviluppare un’app usando Visual Studio Code. I file di codice per l’app sono stati forniti in un repository GitHub.

> **Suggerimento**: Se il repository **mslearn-ai-document-intelligence** è già stato clonato, aprirlo in Visual Studio Code. In caso contrario, eseguire i passaggi seguenti per clonarlo nell'ambiente di sviluppo.

1. Avviare Visual Studio Code.
1. Aprire il riquadro comandi (MAIUSC+CTRL+P) ed eseguire un comando **Git: Clone** per clonare il repository `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` in una cartella locale. Non è importante usare una cartella specifica.
1. Dopo la clonazione del repository, aprire la cartella in Visual Studio Code.

    > **Nota**: Se Visual Studio Code visualizza un messaggio popup per chiedere se si considera attendibile il codice che si apre, fare clic sull'opzione **Sì, considero attendibili gli autori** nel popup.

1. Attendere il completamento dell'installazione di file aggiuntivi per supportare i progetti in codice C# nel repository.

    > **Nota**: se viene richiesto di aggiungere gli asset necessari per la compilazione e il debug, selezionare **Non adesso**. Se viene presentato il messaggio *È stato rilevato un progetto di Funzioni di Azure nella cartella*, è possibile chiudere il messaggio in modo sicuro.

## Creare una risorsa di Informazioni sui documenti di Azure AI

Per usare il servizio Informazioni sui documenti di Azure AI, è necessaria una risorsa di Informazioni sui documenti di Azure AI o di Servizi Azure AI nella sottoscrizione di Azure. Si userà il portale di Azure per creare una risorsa.

1. In una scheda del browser aprire il portale di Azure in `https://portal.azure.com`, accedere con l’account Microsoft associato alla sottoscrizione di Azure.
1. Nella pagina iniziale del portale di Azure, passare alla casella di ricerca superiore e digitare **Document Intelligence**, quindi premere **Invio**.
1. Nella pagina **Informazioni sui documenti** selezionare **Crea**.
1. Nella pagina **Crea Document Intelligence**, configurare la risorsa usando gli elementi seguenti:
    - **Sottoscrizione**: sottoscrizione di Azure.
    - **Gruppo di risorse**: Selezionare o creare un gruppo di risorse con un nome univoco, ad esempio *DocIntelligenceResources*.
    - **Area**: selezionare un’area vicina.
    - **Nome**: Immettere un nome globalmente univoco.
    - **Piano tariffario**: selezionare **Gratuito F0** (se non è disponibile un livello gratuito, selezionare **Standard S0**).
1. Selezionare, quindi, **Rivedi + crea** e **Crea**. Attendere mentre Azure crea la risorsa Informazioni sui documenti di Azure AI.
1. Al termine della distribuzione, selezionare **Vai alla risorsa** per visualizzare la pagina **Informazioni generali** della risorsa. 

## Raccogliere documenti per il training

Si useranno i moduli di esempio come questo per eseguire il training di un modello di test: 

![Immagine di una fattura usata in questo progetto.](../media/Form_1.jpg)

1. Tornare a **Visual Studio Code**. Nel riquadro *Explorer*, aprire la cartella **Labfiles/02-custom-document-intelligence** ed espandere la cartella **sample-forms**. Si noti che sono presenti file che terminano con **.json**e **.jpg** nella cartella.

    Si useranno i file con estensione **jpg** per eseguire il training del modello.  

    I **file .json** sono stati generati automaticamente e contengono informazioni sull'etichetta. I file verranno caricati nel contenitore di archiviazione BLOB insieme ai moduli.

    È possibile visualizzare le immagini in uso nella cartella *sample-forms* selezionandole in Visual Studio Code.

1. Tornare al **portale di Azure** e passare alla pagina **Informazioni generali** della risorsa, se non è già presente. Nella sezione *Essentials*, visualizzare il **gruppo di risorse** in cui è stata creata la risorsa di Informazioni sui documenti.

1. Nella pagina **Panoramica** per il gruppo di risorse prendere nota dei valori di **ID sottoscrizione** e **Località**. Questi valori saranno necessari nei passaggi successivi, insieme al nome del **gruppo di risorse**.

    ![Esempio della pagina del gruppo di risorse.](../media/resource_group_variables.png)

1. Nel riquadro *Explorer* di Visual Studio Code, passare alla cartella **Labfiles/02-custom-document-intelligence** ed espandere la cartella **CSharp** o **Python** in base alle preferenze del linguaggio. Ogni cartella contiene i file specifici del linguaggio per un’app in cui si intende integrare la funzionalità OpenAI di Azure.

1. Fare clic con il pulsante destro del mouse sulla cartella **CSharp** o **Python** contenente i file di codice e selezionare **Aprire un terminale integrato**. 

1. Nel terminale, eseguire il comando seguente per accedere ad Azure. Il comando **az login** aprirà il browser Microsoft Edge: accedere con lo stesso account usato per creare la risorsa di Informazioni sui documenti di Azure AI. Dopo aver eseguito l’accesso, chiudere la finestra del browser.

    ```powershell
    az login
    ```

1. Tornare a Visual Studio Code. Nel riquadro del terminale, eseguire il comando seguente per elencare le posizioni di Azure.

    ```powershell
    az account list-locations -o table
    ```

1. Nell'output individuare il valore **Name** corrispondente alla località del gruppo di risorse (ad esempio per *Stati Uniti orientali* il nome corrispondente è *eastus*).

    > **Importante**: Registrare il valore **Nome** e usarlo nel passaggio 11.

1. In Visual Studio Code, nella cartella **Labfiles/02-custom-document-intelligence**, selezionare **setup.cmd**. Questo script verrà usato per eseguire i comandi dell’interfaccia della riga di comando di Azure necessari per creare le altre risorse di Azure necessarie.

1. Nello script **setup.cmd**, riesaminare i comandi. Il programma:
    - Creerà un account di archiviazione nel gruppo di risorse di Azure
    - Caricherà file dalla cartella *sampleforms* locale in un contenitore denominato *sampleforms* nell'account di archiviazione
    - Stamperà un URI di firma di accesso condiviso

1. Modificare le dichiarazioni delle variabili **subscription_id**, **resource_group** e **posizione** con i valori appropriati per la sottoscrizione, il gruppo di risorse e il nome della posizione in cui è stata distribuita la risorsa di Informazioni sui documenti.
**Salvare** quindi le modifiche.

    Lasciare la variabile **expiry_date** così come è per l'esercizio. Questa variabile viene usata durante la generazione dell'URI della firma di accesso condiviso. In pratica è necessario impostare una data di scadenza appropriata per la firma di accesso condiviso. Per altre informazioni sulla firma di accesso condiviso, fare clic [qui](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works).  

1. Nel terminale, per la cartella **Labfiles/02-custom-document-intelligence**, immettere il comando seguente per eseguire lo script:

    ```PowerShell
    ./setup.cmd
    ```

1. Al termine dello script, riesaminare l’output visualizzato.

1. Nel portale di Azure, aggiornare il gruppo di risorse e verificare che contenga l’account di archiviazione di Azure appena creato. Aprire l’account di archiviazione e nel riquadro a sinistra selezionare **Browser di archiviazione**. Quindi, in Browser di archiviazione, espandere i **contenitori BLOB** e selezionare il contenitore **sampleforms** per verificare che i file siano stati caricati dalla cartella locale **02-custom-document-intelligence/sample-forms**.

## Eseguire il training del modello usando Document Intelligence Studio

Ora si eseguirà il training del modello usando i file caricati nell’account di archiviazione.

1. Nel browser, passare a Document Intelligence Studio in `https://documentintelligence.ai.azure.com/studio`.
1. Scorrere verso il basso fino alla sezione **Modelli** personalizzati e selezionare il riquadro **Modello di estrazione personalizzato**.
1. Se viene chiesto di accedere all'account, usare le credenziali di Azure.
1. Se viene chiesto quale risorsa di Informazioni sui documenti di Azure AI usare, selezionare la sottoscrizione e il nome della risorsa usati durante la creazione della risorsa Informazioni sui documenti di Azure AI.
1. In **Progetti personali** selezionare **Crea un progetto**. Usare le configurazioni seguenti:

    - **Nome progetto**: Immettere un nome univoco.
        - Selezionare *Continua*.
    - **Configurare la risorsa del servizio**: Selezionare la sottoscrizione, il gruppo di risorse e la risorsa Informazioni sui documenti creata in precedenza in questo lab. Selezionare la casella *Imposta come predefinito*. Mantenere la versione dell’API predefinita. 
        - Selezionare *Continua*.
    - **Connettere l’origine dati di training**: Selezionare la sottoscrizione, il gruppo di risorse e l’account di archiviazione creati dallo script di installazione. Selezionare la casella *Imposta come predefinito*. Selezionare il contenitore BLOB `sampleforms` e lasciare vuoto il percorso della cartella.
        - Selezionare *Continua*.
    - Selezionare *Crea progetto*.

1. Dopo aver creato il progetto, in alto a destra nella schermata selezionare **Eseguire il training** per eseguire il training del modello. Usare le configurazioni seguenti:
    - **ID modello**: *Specificare un nome univoco globale: è necessario il nome dell’ID modello nel passaggio successivo*. 
    - **Modalità di compilazione**: Modello.
1. Selezionare **Vai a Modelli**.
1. Il training può richiedere del tempo. Al termine della procedura verrà visualizzata una notifica.

## Testare il modello personalizzato di Informazioni sui documenti

1. Tornare a Visual Studio Code. Nel terminale, installare il pacchetto di Informazioni sui documenti eseguendo il comando appropriato per le preferenze linguistiche:

    **C#:**

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**:

    ```powershell
    pip install azure-ai-formrecognizer==3.3.3
    ```

1. In Visual Studio Code, nella cartella **Labfiles/02-custom-document-intelligence**, selezionare la lingua in uso. Modificare il file di configurazione, **appsettings.json** o **.env**, a seconda della preferenza di lingua, con i valori seguenti:
    - Endpoint di Informazioni sui documenti.
    - Chiave di Informazioni sui documenti.
    - ID modello generato durante il training del modello. È possibile trovarlo nella pagina **Modelli** di Document Intelligence Studio. Selezionare **Salva** per salvare le modifiche.

1. In Visual Studio Code, aprire il file di codice per l’applicazione client: *Program.cs* per C#* o test-model.py* per Python ed esaminare il codice che contiene, in particolare che l’immagine nell’URL faccia riferimento al file in questo repository GitHub sul Web.

1. Tornare al terminale e immettere il comando seguente per eseguire il programma:

    **C#**

    ```powershell
    dotnet build
    dotnet run
    ```

    **Python**

    ```powershell
    python test-model.py
    ```

1. Visualizzare l’output e osservare come fornisca nomi di campi come `Merchant` e `CompanyPhoneNumber` per il modello.

## Eseguire la pulizia

Dopo aver finito con la risorsa Azure, non dimenticare di eliminarla nel [portale di Azure](https://portal.azure.com/?azure-portal=true) per evitare ulteriori modifiche.

## Ulteriori informazioni

Per altre informazioni sul servizio Document Intelligence, vedere la [documentazione di Document Intelligence](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true).
