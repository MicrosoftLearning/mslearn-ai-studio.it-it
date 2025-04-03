---
lab:
  title: Creare un'app di IA generativa che usa dati propri
  description: Informazioni su come usare il modello RAG (Retrieval Augmented Generation) per sviluppare un'app di chat in grado di contestualizzare i prompt mediante l'integrazione di propri dati.
---

# Creare un'app di IA generativa che usa dati propri

Generazione ottimizzata per il recupero (RAG) è una tecnica usata per creare applicazioni che integrano dati da origini dati personalizzate in una richiesta di un modello di intelligenza artificiale generativa. RAG è un modello comunemente usato per lo sviluppo di app di IA generativa: applicazioni basate su chat che usano un modello linguistico per interpretare gli input e generare risposte appropriate.

In questo esercizio si userà il portale Fonderia Azure AI e gli SDK di Fonderia Azure AI e Azure OpenAI per integrare dati personalizzati in un'app di IA generativa.

Questo esercizio richiede circa **45** minuti.

> **Nota**: questo esercizio si basa su SDK non definitive, che possono essere soggette a modifiche. Se necessario, abbiamo usato versioni specifiche dei pacchetti; che potrebbero non riflettere le versioni disponibili più recenti.

## Creare un progetto Fonderia Azure AI

Per iniziare, creare un progetto Azure AI Foundry e le risorse del servizio che deve supportare usando i propri dati, inclusa una risorsa di Azure AI Search.

1. In un Web browser, aprire il [Portale Fonderia Azure AI](https://ai.azure.com) su `https://ai.azure.com` e accedere usando le credenziali di Azure. Chiudere i suggerimenti o i riquadri di avvio rapido aperti la prima volta che si accede e, se necessario, usare il logo **Fonderia Azure AI** in alto a sinistra per passare alla home page, simile all'immagine seguente:

    ![Screenshot del portale di Azure AI Foundry.](./media/ai-foundry-home.png)

1. Nella home page, selezionare **+ Crea progetto**.
1. Nella procedura guidata **Crea un progetto**, immettere un nome appropriato per il progetto (ad esempio, `my-ai-project`). Se viene suggerito un hub esistente, selezionare l'opzione per crearne uno nuovo. Successivamente, esaminare le risorse Azure che verranno create automaticamente per supportare l'hub e il progetto.
1. Selezionare **Personalizza** e specificare le impostazioni seguenti per l'hub:
    - **Nome hub**: *un nome univoco, ad esempio `my-ai-hub`*
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare un nuovo gruppo di risorse con un nome univoco (ad esempio, `my-ai-resources`) o selezionarne uno esistente*
    - **Località**: selezionare **Informazioni su come scegliere** e quindi selezionare sia **gpt-4** sia **text-embedding-ada-002** nella finestra Helper posizione e usare l'area consigliata\*
    - **Connettere i Servizi di Azure AI o Azure OpenAI**: *creare una nuova risorsa di Servizi di intelligenza artificiale con un nome appropriato (ad esempio, `my-ai-services`) o usarne uno esistente*
    - **Connettere Azure AI Search**: *creare una nuova risorsa di Azure AI Search con un nome univoco*

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello tenant. Se viene raggiunto un limite di quota e non è disponibile una regione consigliata per entrambi i modelli, scegliere un solo modello e usare la regione suggerita. Più avanti verrà creata un'altra risorsa in una regione diversa per il secondo modello.

1. Selezionare **Avanti** per esaminare la configurazione. Quindi selezionare **Crea** e attendere il completamento del processo.
1. Quando viene creato il progetto, chiudere tutti i suggerimenti visualizzati e rivedere la pagina **Panoramica** del progetto nel portale Fonderia Azure AI, che dovrebbe essere simile all'immagine seguente:

    ![Screenshot dei dettagli di un progetto di Azure AI nel portale Fonderia di Azure AI.](./media/ai-foundry-project.png)
   
## Distribuire i modelli

Per implementare la soluzione sono necessari due modelli:

- Un modello di *incorporamento* per vettorizzare i dati di testo per un'indicizzazione e un'elaborazione efficienti.
- Modello che può generare risposte in linguaggio naturale alle domande in base ai dati.

1. Nel portale di Azure AI Foundry, nel progetto in uso, nel riquadro di spostamento a sinistra, in **Asset personali**, selezionare la pagina **Modelli + endpoint**.
1. Creare una nuova distribuzione del modello **text-embedding-ada-002** con le impostazioni seguenti, selezionando **Personalizza** nella procedura guidata Distribuisci modello:

    - **Nome distribuzione**: `text-embedding-ada-002`
    - **Tipo di distribuzione**: Standard
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Risorsa di intelligenza artificiale**: *selezionare la risorsa creata in precedenza*
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: predefinitoV2
    - **Abilitare la quota dinamica**: disabilitato

    > **Nota**: se la posizione corrente delle risorse di intelligenza artificiale non dispone di una quota disponibile per il modello che si vuole distribuire, verrà chiesto di scegliere una posizione diversa in cui verrà creata e connessa al progetto una nuova risorsa IA.

1. Ripetere i passaggi precedenti per distribuire un modello **gpt-4** con il nome della distribuzione `gpt-4`.

    > **Nota**: la riduzione dei token al minuto (TPM) consente di evitare di usare eccessivamente la quota disponibile nella sottoscrizione in uso. 5.000 TPM è sufficiente per i dati usati in questo esercizio.

## Aggiungere dati al progetto

I dati per il copilota sono costituiti da una serie di brochure di viaggio in formato PDF dell'agenzia di viaggi fittizia *Margie's Travel*. Aggiungerli al progetto.

1. Scaricare [l'archivio compresso di brochure](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) da `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` ed estrarlo in una cartella denominata **brochure** nel file system locale.
1. Nel portale Azure AI Foundry, nel progetto, nel riquadro di spostamento a sinistra, in **Asset personali**, selezionare la pagina **Dati + indici**.
1. Selezionare **+ Nuovi dati**.
1. Nella procedura guidata **Aggiungi dati** espandere il menu a discesa per selezionare **Carica file/cartelle**.
1. Selezionare **Carica cartella** e selezionare la cartella **brochure**.
1. Selezionare **Avanti** e impostare il nome dei dati su `brochures`.
1. Attendere il caricamento della cartella e notare che contiene diversi file .pdf.

## Creare un indice per i dati

Dopo aver aggiunto un'origine dati al progetto, è possibile usarla per creare un indice nella risorsa di Azure AI Search.

1. Nel portale Azure AI Foundry, nel progetto, nel riquadro di spostamento a sinistra, in **Asset personali**, selezionare la pagina **Dati + indici**.
1. Nella scheda **Indici**, aggiungere un nuovo indice con le impostazioni seguenti:
    - **Posizione di origine**:
        - **Origine dati**: dati nel portale di Azure AI Foundry
            - *Selezionare l'origine dati delle **brochure***
    - **Configurazione dell'indice**:
        - **Selezionare il servizio Azure AI Search**: *selezionare la connessione tra **AzureAISearch** e la risorsa di Azure AI Search in uso*
        - **Indice vettoriale**: `brochures-index`
        - **Macchina virtuale**: selezione automatica
    - **Ricerca impostazioni**:
        - **Impostazioni del vettore**: aggiungere la ricerca vettoriale a questa risorsa di ricerca
        - **Connessione OpenAI di Azure**: *selezionare la risorsa OpenAI di Azure predefinita per l'hub.*

1. Attendere il completamento del processo di indicizzazione, che può richiedere un po' di tempo a seconda delle risorse di calcolo disponibili nell'abbonamento. L'operazione di creazione dell'indice è costituita dai processi seguenti:

    - Spezzare, suddividere e incorporare i token di testo nei dati delle brochure.
    - Creare l'indice di Azure AI Search.
    - Registrare la risorsa dell'indice.

## Testare l'indice nel playground

Prima di usare l'indice in un flusso immediato basato su RAG, verificare che possa essere usato per influire sulle risposte di intelligenza artificiale generativa.

1. Nel riquadro di spostamento a sinistra, selezionare la pagina **Playground** e aprire il playground **Chat**.
1. Nella pagina playground Chat, nel riquadro Configurazione, assicurarsi che sia selezionata la distribuzione modello **gpt-4**. Quindi, nel pannello principale della sessione di chat inviare la richiesta `Where can I stay in New York?`
1. Esaminare la risposta, che deve essere una risposta generica dal modello senza dati dall'indice.
1. Nel pannello di installazione espandere il campo **Aggiungi dati**, quindi aggiungere l'indice di progetto **brochures-index** e selezionare il tipo di ricerca **ibrido (vettore + parola chiave)**.

   > **Suggerimento**: in alcuni casi, gli indici appena creati potrebbero non essere disponibili immediatamente. L'aggiornamento del browser in genere è utile, ma se si verifica ancora il problema per cui non è possibile trovare l'indice, potrebbe essere necessario attendere fino a quando l'indice non viene riconosciuto.

1. Dopo aver aggiunto l'indice e aver riavviato la sessione di chat, inviare di nuovo la richiesta `Where can I stay in New York?`
1. Esaminare la risposta, che deve essere basata sui dati nell'indice.

## Creare un'app client RAG con Fonderia di Azure AI e gli SDK OpenAI di Azure

Ora che si dispone di un indice funzionante, è possibile usare gli SDK Fonderia Azure AI e Azure OpenAI per implementare il pattern RAG in un'applicazione client. Varrà ora esaminato il codice per eseguire questa operazione in un semplice esempio.

> **Suggerimento**: è possibile scegliere di sviluppare la soluzione RAG usando Python o Microsoft C#. Seguire le istruzioni nella sezione appropriata per la lingua scelta.

### Preparare la configurazione dell'applicazione

1. Nel Portale Fonderia Azure AI visualizzare la pagina **Panoramica** per il progetto.
1. Nell'area **Dettagli di progetto** prendere nota della **stringa di connessione del progetto**. Questa stringa di connessione verrà usata per connettersi al progetto in un'applicazione client.
1. Aprire una nuova scheda del browser (mantenendo aperto il Portale Fonderia Azure AI nella scheda esistente). In una nuova scheda del browser, passare al [portale di Azure](https://portal.azure.com) su `https://portal.azure.com`, accedendo con le credenziali di Azure se richiesto.
1. Usare il pulsante **[\>_]** a destra della barra di ricerca, nella parte superiore della pagina, per aprire una nuova sessione di Cloud Shell nel portale di Azure selezionando un ambiente ***PowerShell***. Cloud Shell fornisce un'interfaccia della riga di comando in un riquadro nella parte inferiore del portale di Azure.

    > **Nota**: se in precedenza è stata creata una sessione Cloud Shell che usa un ambiente *Bash*, passare a ***PowerShell***.

1. Nella barra degli strumenti di Cloud Shell scegliere **Vai alla versione classica** dal menu **Impostazioni**. Questa operazione è necessaria per usare l'editor di codice.

    > **Suggerimento**: quando si incollano i comandi in CloudShell, l'ouput può richiedere una grande quantità di buffer dello schermo. È possibile cancellare la schermata immettendo il `cls` comando per rendere più semplice concentrarsi su ogni attività.

1. Nel riquadro PowerShell immettere i comandi seguenti per clonare il repository GitHub per questo esercizio:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

> **Nota**: seguire i passaggi per il linguaggio di programmazione scelto.

1. Dopo aver clonato il repository, passare alla cartella contenente i file di codice dell'applicazione chat:  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. Nel riquadro della riga di comando di Cloud Shell immettere il comando seguente per installare le librerie che si useranno, ovvero:

    **Python**

    ```
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
    ```
    

1. Immettere il comando seguente per modificare il file di configurazione fornito:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Il file viene aperto in un editor di codice.

1. Nel file del codice, sostituire i segnaposto seguenti: 
    - **your_project_endpoint**: sostituire con la stringa di connessione per il progetto (copiato dalla pagina **Panoramica** del progetto nel portale Fonderia di Azure AI)
    - **your_model_deployment** sostituire con il nome assegnato alla distribuzione modello (che deve essere `gpt-4`)
    - **your_index**: sostituire con il nome dell'indice (che deve essere `brochures-index`)
1. Dopo aver sostituito i segnaposto con l'editor di codice, usare il comando **CTRL+S** o **Fare clic con il pulsante destro del mouse > Salva** per salvare le modifiche e quindi usare il comando **CTRL+Q** o **Fare clic con il pulsante destro del mouse > Esci** per chiudere l'editor di codice mantenendo aperta la riga di comando di Cloud Shell.

### Esplorare il codice per implementare il criterio RAG

1. Immettere il comando seguente per modificare il file di codice fornito:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Esaminare il codice nel file, notando che:
    - usa l'SDK Fonderia di Azure AI per connettersi al progetto (usando il progetto stringa di connessione)
    - recupera la connessione predefinita di Azure AI Search dal progetto in modo da poter determinare l'endpoint e la chiave per il servizio di Azure AI Search.
    - Crea un client Azure OpenAI autenticato in base alla connessione predefinita di Servizio OpenAI di Azure nel progetto.
    - Invia un prompt (incluso un messaggio di sistema e utente) al client Azure OpenAI, aggiungendo informazioni aggiuntive sull'indice di Azure AI Search da usare per eseguire il grounding del prompt.
    - Visualizza la risposta dal prompt di cui è stato eseguito il grounding.
1. Usare il comando **CTRL+Q** per chiudere l'editor di codice senza salvare le modifiche, mantenendo aperta la riga di comando di Cloud Shell.

### Eseguire l'applicazione di chat

1. Nel riquadro della riga di comando di Cloud Shell immettere il comando seguente per eseguire l'app:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Quando richiesto, immettere una domanda, ad esempio `Where can I travel to?` ed esaminare la risposta del modello di IA generativa.

    Si noti che la risposta include riferimenti all'origine per indicare i dati indicizzati in cui è stata trovata la risposta.

1. Provare a immettere altre domande, ad esempio `Where should I stay in London?`

    > **Nota**: questa semplice applicazione di esempio non include alcuna logica per conservare la cronologia delle conversazioni, quindi ogni richiesta viene considerata come una nuova conversazione.

1. Al termine, immettere `quit` per uscire dal programma. Quindi chiudere il riquadro Cloud Shell.

## Proposta

Ora che si sono apprese le modalità di integrazione dei dati in un'app di IA generativa realizzata con il portale Azure AI Foundry, è possibile procedere.

Prova ad aggiungere una nuova origine dati tramite il portale Fonderia Azure AI, indicizzarla e integrare i dati indicizzati in un'applicazione client. Alcuni set di dati che è possibile provare sono:

- Una raccolta di articoli (di ricerca) presenti sul computer.
- Una serie di presentazioni delle conferenze precedenti.
- Ognuno dei set di dati disponibili nell'archivio dei [dati di esempio di Ricerca di Azure](https://github.com/Azure-Samples/azure-search-sample-data).

Testare la soluzione inviando richieste che potrebbero trovare risposta solo nel set di dati scelto.

## Eseguire la pulizia

Per evitare i costi e l'utilizzo delle risorse di Azure non necessari, si dovrebbero rimuovere le risorse distribuite in questo esercizio.

1. Se è stata completata l'esplorazione di Azure AI Foundry, tornare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com` e accedere usando le credenziali di Azure, se necessario. Quindi eliminare le risorse nel gruppo di risorse in cui è stato effettuato il provisioning di Azure Ai Search e delle risorse di Azure AI.
