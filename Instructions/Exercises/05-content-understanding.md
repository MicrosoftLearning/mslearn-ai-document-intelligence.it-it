---
lab:
  title: Analizzare i contenuti con Comprensione del Contenuto di Azure AI
  module: Multimodal analysis with Content Understanding
---

# Analizzare i contenuti con Comprensione del Contenuto di Azure AI

In questo esercizio, verrà utilizzato il portale Fonderia Azure AI per creare un progetto di comprensione dei contenuti in grado di estrarre informazioni dalle fatture. Si testerà quindi l'analizzatore del contenuto nel Portale Fonderia Azure AI e lo si userà tramite l'interfaccia REST di comprensione dei contenuti.

Questo esercizio richiede circa **30** minuti.

## Creare un progetto Fonderia Azure AI

Per iniziare, creare un progetto Fonderia Azure AI.

1. In un Web browser, aprire il [Portale Fonderia Azure AI](https://ai.azure.com) su `https://ai.azure.com` e accedere usando le credenziali di Azure. Chiudere i suggerimenti o i riquadri di avvio rapido aperti la prima volta che si accede e, se necessario, usare il logo **Fonderia Azure AI** in alto a sinistra per passare alla home page, simile all'immagine seguente:

    ![Screenshot del portale di Azure AI Foundry.](../media/ai-foundry-portal.png)

1. Nella home page, selezionare **+ Crea progetto**.
1. Nella procedura guidata **Crea un progetto**, immettere un nome valido per il progetto e, se viene suggerito un hub esistente, selezionare l'opzione per crearne uno nuovo. Successivamente, esaminare le risorse Azure che verranno create automaticamente per supportare l'hub e il progetto.
1. Selezionare **Personalizza** e specificare le impostazioni seguenti per l'hub:
    - **Nome hub**: *un nome valido per l'hub*
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare o selezionare un gruppo di risorse*
    - **Posizione**: selezionare una delle aree seguenti\*
        - Stati Uniti occidentali
        - Svezia centrale
        - Australia orientale
    - **Connettere Servizi di Azure AI o Azure OpenAI**: *Creare una nuova risorsa di Servizi di AI*
    - **Connettere Azure AI Search**: *creare una nuova risorsa di Azure AI Search con un nome univoco*

    > \*Al momento della stesura di questo articolo, la comprensione dei contenuti di Azure AI è disponibile solo in queste aree.

1. Selezionare **Avanti** per esaminare la configurazione. Quindi selezionare **Crea** e attendere il completamento del processo.
1. Quando viene creato il progetto, chiudere tutti i suggerimenti visualizzati e rivedere la pagina del progetto nel portale Fonderia di Azure AI, che dovrebbe essere simile all'immagine seguente:

    ![Screenshot dei dettagli di un progetto di Azure AI nel portale Fonderia di Azure AI.](../media/ai-foundry-project.png)

## Creare un analizzatore di Comprensione dei contenuti

Verrà creato un analizzatore in grado di estrarre informazioni dalle fatture. È possibile iniziare definendo uno schema basato su un esempio di fattura.

1. In una nuova scheda del browser, scaricare il modulo di esempio [invoice-1234.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf) da `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf` e salvarlo in una cartella locale.
1. Tornare alla scheda contenente la pagina iniziale del progetto Fonderia Azure AI e, nel riquadro di spostamento a sinistra, selezionare **Comprensione dei contenuti**.
1. Nella pagina **Comprensione dei contenuti**, selezionare la scheda **Analizzatore personalizzato** in alto.
1. Nella pagina dell'analizzatore personalizzato di Comprensione dei contenuti, selezionare **+ Crea** e creare un'attività con le impostazioni seguenti:
    - **Nome attività**: analisi della fattura
    - **Descrizione**: estrarre dati da una fattura
    - **Connessione ai servizi di Azure AI**: *la risorsa dei servizi di Azure AI nell'hub di Fonderia Azure AI*
    - **Account di archiviazione BLOB di Azure**: *l'account di archiviazione predefinito nell'hub di Fonderia Azure AI*
1. Attendere la creazione dell'attività.

    > **Suggerimento**: se si verifica un errore di accesso alla memoria, attendere un minuto e riprovare.

1. Nella pagina **Definisci schema**, caricare il file **invoice-1234.pdf** appena scaricato.
1. Selezionare il modello **Analisi fattura** e quindi **Crea**.

    Il modello *Analisi delle fatture* include i campi comuni contenuti nelle fatture. È possibile usare l'editor schemi per eliminare i campi suggeriti non necessari e aggiungere quelli personalizzati.

1. Nell'elenco dei campi suggeriti, selezionare **BillingAddress**. Questo campo non è necessario per il formato di fattura caricato, quindi usare l'icona **Elimina campo** (**&#128465;**) visualizzata per eliminarlo.
1. Eliminare ora i seguenti campi suggeriti, che non sono necessari:
    - BillingAddressRecipient
    - CustomerAddressRecipient
    - CustomerId
    - CustomerTaxId
    - DueDate
    - InvoiceTotal
    - PaymentTerm
    - PreviousUnpaidBalance
    - PurchaseOrder
    - RemittanceAddress
    - RemittanceAddressRecipient
    - ServiceAddress
    - ServiceAddressRecipient
    - ShippingAddress
    - ShippingAddressRecipient
    - TotalDiscount
    - VendorAddressRecipient
    - VendorTaxId
    - TaxDetails
1. Usare il pulsante **+ Aggiungi nuovo campo** per aggiungere i campi seguenti:

    | Nome del campo | Descrizione campo | Tipo di valore | metodo |
    |--|--|--|--|
    | `VendorPhone` | `Vendor telephone number` | String | Estrazione |
    | `ShippingFee` | `Fee for shipping` | Numero | Estrazione |

1. Verificare che lo schema completato sia simile al seguente e selezionare **Salva**.
    ![Screenshot di uno schema per una fattura.](../media/invoice-schema.png)

1. Nella pagina **Test dell'analizzatore**, se l'analisi non inizia automaticamente, selezionare **Esegui analisi**. Attendere quindi il completamento dell'analisi ed esaminare i valori di testo nella fattura identificati come corrispondenti ai campi nello schema.
1. Esaminare i risultati dell'analisi, che dovrebbero essere simili ai seguenti:

    ![Screenshot dei risultati del test dell'analizzatore.](../media/analysis-results.png)

1. Visualizzare i dettagli dei campi identificati nel riquadro **Campi**, quindi visualizzare la scheda **Risultato** per visualizzare la rappresentazione JSON.

## Creare e testare un analizzatore

Ora che è stato eseguito il training di un modello per estrarre i campi dalle fatture, è possibile creare un analizzatore da usare con moduli simili.

1. Selezionare la pagina **Crea analizzatore**, quindi selezionare **+ Crea analizzatore** e creare un nuovo analizzatore con le seguenti proprietà (digitate esattamente come illustrato di seguito):
    - **Nome**: `contoso-invoice-analyzer`
    - **Descrizione**: `Contoso invoice analyzer`
1. Attendere che il nuovo analizzatore sia pronto (usare il pulsante **Aggiorna** per controllare).
1. Scaricare il file [invoice-1235.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf) da `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf` e salvarlo in una cartella locale.
1. Tornare alla pagina **Crea analizzatore** e selezionare il collegamento **contoso-invoice-analyzer**. Verranno visualizzati i campi definiti nello schema dell'analizzatore.
1. Nella pagina **contoso-invoice-analyzer**, selezionare la scheda **Test**.
1. Usare il pulsante **+ Carica file di test** per caricare il file **invoice-1235.pdf** ed eseguire l'analisi per estrarre i dati di campo dal modulo di test.
1. Esaminare i risultati del test e verificare che l'analizzatore abbia estratto i campi corretti dalla fattura di test.
1. Chiudere la pagina **contoso-invoice-analyzer***.

## Usare l'API REST Comprensione dei contenuti

Dopo aver creato un analizzatore, è possibile usarlo da un'applicazione client tramite l'API REST di comprensione dei contenuti.

1. Nell'area **Dettagli di progetto** prendere nota della **stringa di connessione del progetto**. Questa stringa di connessione verrà usata per connettersi al progetto in un'applicazione client.
1. Aprire una nuova scheda del browser (mantenendo aperto il Portale Fonderia Azure AI nella scheda esistente). In una nuova scheda del browser, passare al [portale di Azure](https://portal.azure.com) su `https://portal.azure.com`, accedendo con le credenziali di Azure se richiesto.

    Chiudere eventuali notifiche di benvenuto per visualizzare la pagina iniziale del portale di Azure.

1. Usare il pulsante **[\>_]** a destra della barra di ricerca, nella parte superiore della pagina, per aprire una nuova sessione di Cloud Shell nel portale di Azure selezionando un ambiente ***PowerShell*** senza archiviazione nell'abbonamento.

    Cloud Shell fornisce un'interfaccia della riga di comando in un riquadro nella parte inferiore del portale di Azure. È possibile ridimensionare o ingrandire questo riquadro per ottimizzare l'esperienza d'uso.

    > **Nota**: se in precedenza è stata creata una sessione Cloud Shell che usa un ambiente *Bash*, passare a ***PowerShell***.

1. Nella barra degli strumenti di Cloud Shell scegliere **Vai alla versione classica** dal menu **Impostazioni**. Questa operazione è necessaria per usare l'editor di codice.

    **<font color="red">Verificare di passare alla versione classica di Cloud Shell prima di continuare.</font>**

1. Nel riquadro Cloud Shell immettere i comandi seguenti per clonare il repository GitHub contenente i file di codice per questo esercizio (digitare il comando o copiarlo negli Appunti e quindi fare clic con il pulsante destro del mouse nella riga di comando e incollarlo come testo normale):

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-document-intelligence mslearn-ai-doc
    ```

    > **Suggerimento**: quando vengono incollati i comandi in CloudShell, l'ouput può richiedere una grande quantità di buffer dello schermo. È possibile cancellare la schermata immettendo il `cls` comando per rendere più semplice concentrarsi su ogni attività.

1. Dopo aver clonato il repository, passare alla cartella contenente i file di codice dell'applicazione:

    ```
   cd mslearn-ai-doc/Labfiles/05-content-understanding/code
   ls -a -l
    ```

1. Nel riquadro della riga di comando di Cloud Shell, immettere il comando seguente per installare le librerie che verranno utilizzate:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install dotenv azure-identity azure-ai-projects
    ```

1. Immettere il comando seguente per modificare il file di configurazione fornito:

    ```
   code .env
    ```

    Il file viene aperto in un editor di codice.

1. Nel file di codice, sostituire il segnaposto **YOUR_PROJECT_CONNECTION_STRING** con la stringa di connessione del progetto (copiata dalla pagina **Panoramica** del progetto nel portale Fonderia Azure AI) e assicurarsi che **ANALYZER** sia impostato sul nome assegnato all'analizzatore (che dovrebbe essere *contoso-invoice-analyzer*)
1. Dopo aver sostituito i segnaposto, nell'editor di codice, usare il comando **CTRL+S** per salvare le modifiche e quindi usare il comando **CTRL+Q** per chiudere l'editor di codice mantenendo aperta la riga di comando di Cloud Shell.

1. Nella riga di comando di Cloud Shell, immettere il seguente comando per modificare il file di codice Python **analyze_invoice.py** fornito:

    ```
    code analyze_invoice.py
    ```
    Il file di codice Python viene aperto in un editor di codice:

1. Esaminare il codice che:
    - Identifica il file della fattura da analizzare, con un valore predefinito di **invoice-1236.pdf**.
    - Recupera l'endpoint e la chiave della risorsa dei servizi di Azure AI dal progetto.
    - Invia una richiesta HTTP POST all'endpoint di comprensione dei contenuti, indicando di analizzare l'immagine.
    - Controlla la risposta dell'operazione POST per recuperare un ID per l'operazione di analisi.
    - Invia ripetutamente una richiesta HTTP GET al servizio di comprensione dei contenuti fino a quando l'operazione non è più in esecuzione.
    - Se l'operazione ha esito positivo, esegue l'analisi della risposta JSON e visualizza i valori recuperati
1. Usare il comando **CTRL+Q** per chiudere l'editor di codice mantenendo aperta la riga di comando di Cloud Shell.
1. Nel riquadro della riga di comando di Cloud Shell immettere il comando seguente per eseguire il codice Python:

    ```
    python analyze_invoice.py invoice-1236.pdf
    ```

1. Esaminare l'output del programma.
1. Usare il seguente comando per eseguire il programma con una fattura diversa:

    ```
    python analyze_invoice.py invoice-1235.pdf
    ```

    > **Suggerimento**: nella cartella del codice sono presenti tre file di fattura utilizzabili (invoice-1234.pdf, invoice-1235.pdf e invoice-1236.pdf). 

## Eseguire la pulizia

Al termine del lavoro con il servizio di comprensione dei contenuti, è necessario eliminare le risorse create in questo esercizio per evitare di incorrere in costi di Azure non necessari.

1. Nel Portale Fonderia Azure AI passare al progetto **travel-insurance** ed eliminarlo.
1. Nel portale di Azure eliminare il gruppo di risorse creato in questi esercizi.

