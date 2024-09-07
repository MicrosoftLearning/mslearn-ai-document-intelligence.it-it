---
lab:
  title: Usare modelli predefiniti di Informazioni sui documenti
  module: Module 6 - Document Intelligence
---

# Usare modelli predefiniti di Informazioni sui documenti

In questo esercizio, si configurerà una risorsa di Intelligence sui documenti di Intelligenza artificiale di Azure nella sottoscrizione di Azure. Si userà sia Azure AI Document Intelligence Studio sia C# o Python per inviare moduli a tale risorsa per l'analisi.

## Creare una risorsa di Informazioni sui documenti di Azure AI

Prima di poter chiamare il servizio Informazioni sui documenti di Azure AI, è necessario creare una risorsa per ospitare tale servizio in Azure:

1. In una scheda del browser, aprire il portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com?azure-portal=true) e accedere usando l'account Microsoft associato alla sottoscrizione di Azure.
1. Nella pagina iniziale del portale di Azure, passare alla casella di ricerca superiore e digitare **Document Intelligence**, quindi premere **Invio**.
1. Nella pagina **Informazioni sui documenti** selezionare **Crea Informazioni sui documenti**.
1. Nella pagina **Crea Document Intelligence**, configurare la risorsa usando gli elementi seguenti:
    - **Sottoscrizione**: sottoscrizione di Azure.
    - **Gruppo di risorse**: Selezionare o creare un gruppo di risorse con un nome univoco, ad esempio *DocIntelligenceResources*.
    - **Area**: selezionare un’area vicina.
    - **Nome**: Immettere un nome globalmente univoco.
    - **Piano tariffario**: selezionare **Gratuito F0** (se non è disponibile un livello gratuito, selezionare **Standard S0**).
1. Selezionare, quindi, **Rivedi + crea** e **Crea**. Attendere che Azure crei la risorsa Document Intelligence di Azure AI.
1. Al termine della distribuzione, seleziona **Vai alla risorsa**. Mantenere questa pagina aperta per il resto di questo esercizio.

## Usare il modello Lettura

Iniziamo usando **Azure AI Document Intelligence Studio** e il modello di lettura per analizzare un documento in più lingue. Si connetterà Informazioni sui documenti di Azure AI Studio alla risorsa appena creata per eseguire l'analisi:

1. Aprire una nuova scheda del browser e passare ad **Azure AI Document Intelligence Studio** all’indirizzo [https://documentintelligence.ai.azure.com/studio](https://documentintelligence.ai.azure.com/studio).
1. In **Analisi documento**, selezionare il riquadro **Lettura**.
1. Se viene chiesto di accedere all'account, usare le credenziali di Azure.
1. Se viene chiesto quale risorsa di Informazioni sui documenti di Azure AI usare, selezionare la sottoscrizione e il nome della risorsa usati durante la creazione della risorsa Informazioni sui documenti di Azure AI.
1. Nell'elenco dei documenti a sinistra, selezionare **read-german.pdf**.

    ![Screenshot che mostra la pagina Lettura in Informazioni sui documenti di Azure AI Studio.](../media/read-german-sample.png#lightbox)

1. In alto a sinistra, selezionare **Opzioni di analisi**, quindi abilitare la casella di controllo **Lingua** (in **Rilevamento facoltativo**) nel riquadro **Opzioni di analisi** e fare clic su **Salva**. 
1. In alto a sinistra, selezionare **Esegui analisi**.
1. Al termine dell'analisi, il testo estratto dall'immagine viene visualizzato a destra nella scheda **Content**. Esaminare questo testo e confrontarlo con il testo nell'immagine originale per valutarne l'accuratezza.
1. Selezionare la scheda **Result**. Questa scheda visualizza il codice JSON estratto. 
1. Scorrere fino alla fine del codice JSON nella scheda **Result**. Notare che il modello di lettura ha rilevato la lingua di ogni intervallo indicato da `locale`. La maggior parte degli intervalli è in tedesco (codice lingua `de`) ma è possibile trovare altri codici lingua negli intervalli (ad esempio Inglese - codice lingua `en` - in uno degli ultimi intervalli).

    ![Screenshot che mostra il rilevamento della lingua per due intervalli di risultati dal modello di lettura in Informazioni sui documenti di Azure AI Studio.](../media/language-detection.png#lightbox)

## Prepararsi allo sviluppo di un'app in Visual Studio Code

Ora verrà esaminata l'app che usa l’SDK del servizio Azure Document Intelligence. L'app verrà sviluppata usando Visual Studio Code. I file di codice per l'app sono stati forniti in un repository GitHub.

> **Suggerimento**: Se il repository **mslearn-ai-document-intelligence** è già stato clonato, aprirlo in Visual Studio Code. In caso contrario, eseguire i passaggi seguenti per clonarlo nell'ambiente di sviluppo.

1. Avviare Visual Studio Code.
1. Aprire il riquadro comandi (MAIUSC+CTRL+P) ed eseguire un comando **Git: Clone** per clonare il repository `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` in una cartella locale. Non è importante usare una cartella specifica.
1. Dopo la clonazione del repository, aprire la cartella in Visual Studio Code.

    > **Nota**: Se Visual Studio Code visualizza un messaggio popup per chiedere se si considera attendibile il codice che si apre, fare clic sull'opzione **Sì, considero attendibili gli autori** nel popup.

1. Attendere il completamento dell'installazione di file aggiuntivi per supportare i progetti in codice C# nel repository.

    > **Nota**: se viene richiesto di aggiungere gli asset necessari per la compilazione e il debug, selezionare **Non adesso**. Se viene visualizzato il messaggio *È stato rilevato un progetto di funzione di Azure nella cartella*, è possibile chiuderlo in sicurezza.

## Configurare l'applicazione

Sono state fornite applicazioni sia per C# che per Python, oltre a un file PDF di esempio che verrà usato per testare Document Intelligence. Entrambe le app presentano la stessa funzionalità. Prima di tutto, verranno completate alcune parti chiave dell'applicazione per consentire l'uso della risorsa Azure Document Intelligence.

1. Esaminare la fattura seguente e prendere nota di alcuni dei relativi campi e valori. Questa è la fattura che verrà analizzata dal codice.

    ![Screenshot che mostra un documento di fattura di esempio.](../media/sample-invoice.png#lightbox)

1. Nel riquadro **Explorer** di Visual Studio Code, passare alla cartella **Labfiles/01-prebuild-models** ed espandere la cartella **CSharp** o **Python** a seconda del linguaggio scelto. Ogni cartella contiene i file specifici della lingua per un'app in cui si intende integrare la funzionalità Azure Document Intelligence.

1. Fare clic con il pulsante destro del mouse sulla cartella **CSharp** o **Python** contenente i file di codice, quindi selezionare **Apri un terminale integrato**. Installare, quindi, il pacchetto SDK Riconoscimento modulo di Azure (il nome precedente per Document Intelligence) eseguendo il comando appropriato per il linguaggio scelto:

    **C#:**

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**:

    ```powershell
    pip install azure-ai-formrecognizer==3.3.3
    ```

## Aggiungere il codice per usare il servizio Azure Document Intelligence

A questo punto, è possibile usare l'SDK per valutare il file pdf.

1. Passare alla scheda del browser che visualizza la panoramica di Informazioni sui documenti di Azure AI nel portale di Azure. Nel riquadro a sinistra, in *Gestione risorse*, selezionare **Chiavi ed endpoint**. A destra del valore **Endpoint** fare clic sul pulsante **Copia negli Appunti**.
1. Nel riquadro **Explorer**, nella cartella **CSharp** o **Python**, aprire il file di codice per il linguaggio scelto e sostituire `<Endpoint URL>` con la stringa appena copiata:

    **C#**: ***Program.cs***

    ```csharp
    string endpoint = "<Endpoint URL>";
    ```

    **Python**: ***document-analysis.py***

    ```python
    endpoint = "<Endpoint URL>"
    ```

1. Passare alla scheda del browser che visualizza **Chiavi ed endpoint** di Document Intelligence di Azure AI nel portale di Azure. A destra del valore **KEY 1** fare clic sul pulsante *Copia negli Appunti**.
1. Nell'editor di codice in Visual Studio Code, individuare questa riga e sostituire `<API Key>` con la stringa appena copiata:

    **C#**

    ```csharp
    string apiKey = "<API Key>";
    ```

    **Python**

    ```python
    key = "<API Key>"
    ```

1. Individuare il commento `Create the client`. Dopo il commento, nelle nuove righe immettere il codice seguente:

    **C#**

    ```csharp
    var cred = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), cred);
    ```

    **Python**

    ```python
    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )
    ```

1. Individuare il commento `Analyze the invoice`. Dopo il commento, nelle nuove righe immettere il codice seguente:

    **C#**

    ```csharp
    AnalyzeDocumentOperation operation = await client.AnalyzeDocumentFromUriAsync(WaitUntil.Completed, "prebuilt-invoice", fileUri);
    ```

    **Python**

    ```python
    poller = document_analysis_client.begin_analyze_document_from_url(
        fileModelId, fileUri, locale=fileLocale
    )
    ```

1. Individuare il commento `Display invoice information to the user`. Dopo il commento, nelle nuove righe immettere il codice seguente:

    **C#**

    ```csharp
    AnalyzeResult result = operation.Value;
    
    foreach (AnalyzedDocument invoice in result.Documents)
    {
        if (invoice.Fields.TryGetValue("VendorName", out DocumentField? vendorNameField))
        {
            if (vendorNameField.FieldType == DocumentFieldType.String)
            {
                string vendorName = vendorNameField.Value.AsString();
                Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}.");
            }
        }
    ```

    **Python**

    ```python
    receipts = poller.result()
    
    for idx, receipt in enumerate(receipts.documents):
    
        vendor_name = receipt.fields.get("VendorName")
        if vendor_name:
            print(f"\nVendor Name: {vendor_name.value}, with confidence {vendor_name.confidence}.")
    ```

    > [!NOTE]
    > È stato aggiunto codice per visualizzare il nome del fornitore. Il progetto iniziale include anche il codice per visualizzare il *nome del cliente* e il *totale della fattura*.

1. Salvare le modifiche apportate al file di codice.

1. Nel riquadro del terminale interattivo, accertarsi che il contesto della cartella sia la cartella per la lingua dell’interfaccia utente. Immettere, quindi, il comando seguente per eseguire l'applicazione.

1. ***Solo per C#***, per compilare il progetto, immettere questo comando:

    **C#:**

    ```powershell
    dotnet build
    ```

1. Per eseguire il codice, immettere questo comando:

    **C#:**

    ```powershell
    dotnet run
    ```

    **Python**:

    ```powershell
    python document-analysis.py
    ```

Il programma visualizza il nome del fornitore, il nome del cliente e il totale della fattura con i livelli di confidenza. Confrontare i valori restituiti con la fattura di esempio aperta all'inizio di questa sezione.

## Eseguire la pulizia

Dopo aver finito con la risorsa Azure, non dimenticare di eliminarla nel [portale di Azure](https://portal.azure.com/?azure-portal=true) per evitare ulteriori modifiche.

## Ulteriori informazioni

Per altre informazioni sul servizio Document Intelligence, vedere la [documentazione di Document Intelligence](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true).
