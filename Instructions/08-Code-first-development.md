# Creare un'app di IA generativa usando gli strumenti di sviluppo code-first

In questo esercizio, verrà clonato e distribuito un modello di Azure Developer CLI che effettua il provisioning e [distribuisce il progetto di intelligenza artificiale in un endpoint online](https://learn.microsoft.com/azure/developer/azure-developer-cli/azure-ai-ml-endpoints?WT.mc_id=academic-140829-cacaste) su Azure AI Foundry. Verrà quindi usato come punto di partenza per creare un copilota personalizzato con Azure per l'intelligenza artificiale e un'esperienza code-first.

Questo esercizio richiederà circa **90** minuti.

## Prima di iniziare

Per completare questo esercizio, sarà necessario disporre di:

- Un account GitHub per creare una copia tramite fork del repository del progetto e testarlo in un ambiente GitHub Codespaces. Creare un account gratuito  [in GitHub](https://github.com/).
- Livello di base di Azure AI Search per attivare il classificatore semantico. Altre informazioni sui [prezzi di AI Search](https://azure.microsoft.com/pricing/details/search/).
- Per distribuire tre modelli OpenAI (`gpt-35-turbo`, `gpt-4`, `text-embedding-ada-002`). Per poter distribuire i modelli, è necessario creare l'hub di intelligenza artificiale in un'area con una quota sufficiente. Altre informazioni sulla [disponibilità dell'area del modello](https://learn.microsoft.com/azure/ai-services/openai/concepts/models?WT.mc_id=academic-140829-cacaste#model-summary-table-and-region-availability).

## Informazioni sullo scenario

Per iniziare a usare il modello di progetto di intelligenza artificiale di Azure Developer CLI, passare ai [modelli di Azure per l'intelligenza artificiale con la raccolta di Azure Developer CLI ](https://learn.microsoft.com/collections/5pq0uompdgje8d/?WT.mc_id=academic-140829-cacaste). Esplorando la raccolta, è possibile trovare diversi progetti raggruppati per tecnologia e caso d'uso, tra cui esempi di progetti multi-modale e multi-agente, progetti simili a copilota ed esempi che integrano framework e servizi di Azure diversi.

Per questo esercizio verrà usato come punto di partenza il modello di progetto **[Copilot di Contoso Chat Retail con Azure AI Foundry e PromptFlow (Python)](https://aka.ms/contoso-retail-sample)**. Questo modello di progetto è un'esperienza code-first che usa Prompty e PromptFlow per creare un copilota personalizzato (Intelligenza artificiale di chat) che può essere integrato nel sito Web di vendita al dettaglio (interfaccia utente di chat) di una società fittizia denominata Contoso Outdoors.

![Interfaccia utente/esperienza utente di chat Contoso](./media/contoso_outdoors_website.png)

La soluzione di copilota per la vendita al dettaglio usa un modello di generazione aumentata di recupero (RAG) per mettere a punto le risposte nei dati dei prodotti e dei clienti dell'azienda. I clienti possono porre domande al chatbot per la vendita al dettaglio sul catalogo dei prodotti dell'azienda e ricevere consigli anche in base agli acquisti precedenti.

Selezionando il collegamento al progetto incluso nella raccolta, si verrà reindirizzati al repository GitHub che ospita il codice del modello. Il file [README.md](https://github.com/Azure-Samples/contoso-chat/blob/main/README.md) nel repository fornisce una descrizione dettagliata del progetto, tra cui l'architettura, i prerequisiti e i passaggi per distribuire il progetto.

![Architettura chat di Contoso](./media/contoso_chat_architecture.png)

## Configurare GitHub Codespaces

In questo esercizio si userà [GitHub Codespaces](https://github.com/features/codespaces), una funzionalità di GitHub che consente di avviare un [ contenitore di sviluppo](https://docs.github.com/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers) ospitato nel cloud preconfigurato direttamente dal repository, con un solo clic. In questo modo, è possibile iniziare rapidamente a scrivere codice senza dover configurare l'ambiente di sviluppo locale, poiché Codespaces ha già tutti i necessari strumenti e dipendenze preinstallati.

Per inizializzare l'ambiente di sviluppo, seguire questa procedura:

1. Passare al repository della **chat di Contoso**: `https://github.com/Azure-Samples/contoso-chat`
1. Selezionare il pulsante **Fork** nell'angolo in alto a destra della pagina del repository GitHub della chat di Contoso per creare una copia del repository nell'account GitHub.
1. Dopo aver un repository di cui è stata creata una copia tramite fork, selezionare il pulsante **Codice** e selezionare **Codespaces**.
1. Selezionare il **+** pulsante per creare un nuovo codespace nel ramo principale del repository di cui è stata creata una copia tramite fork.

    ![Crea GitHub Codespaces](./media/create_codespaces.png)

1. In pochi secondi, si verrà reindirizzati a una nuova scheda del browser in cui l'ambiente Codespaces è configurato con un [editor di Visual Studio Code collegato](https://code.visualstudio.com/docs/devcontainers/containers) per impostazione predefinita.

## Connettere l'ambiente VS Code ad Azure

Il passaggio successivo consiste nel connettere l'ambiente di sviluppo alla sottoscrizione di Azure in cui si vuole distribuire il progetto. Per iniziare, aprire un nuovo terminale nell'ambiente VS Code in esecuzione in Codespaces.

1. Verificare prima di tutto che sia installata la [versione più recente](https://github.com/Azure/azure-dev/releases/tag/azure-dev-cli_1.9.3) di Azure Developer CLI.
    ```bash
    azd version
    ```

1. Accedere quindi all'account di Azure dal terminale di VS Code.

    ```bash
    azd auth login 
    ```

## Effettuare il provisioning delle risorse di Azure per il progetto

Dopo aver eseguito l'accesso, è possibile iniziare a effettuare il provisioning delle risorse di Azure per il progetto nella sottoscrizione. È possibile eseguire questa operazione nello stesso terminale di VS Code usato per l'accesso.

1. Effettuare il provisioning *e distribuire* l'applicazione di intelligenza artificiale usando azd.

    ```bash
    azd up
    ```

1. Verranno ora visualizzati i prompt seguenti. Rispondere usando le indicazioni seguenti:
    - **Immettere un nuovo nome di ambiente:***usato per creare il nome del gruppo di risorse*.
    - **Selezionare una sottoscrizione di Azure per usare**: *Selezionare una sottoscrizione che ha accesso ai modelli OpenAI di Azure*.
    - **Selezionare una località di Azure da usare**: *selezionare una località con quota di modello disponibile*.

    > Usare la [tabella di riepilogo del modello e la disponibilità dell'area](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models?WT.mc_id=academic-140829-cacaste#model-summary-table-and-region-availability) per trovare l'area necessaria. Ad esempio, è possibile usare `sweden central` come località di Azure poiché si tratta dell'area in cui sono disponibili la maggior parte dei modelli OpenAI di Azure.

## Convalidare il provisioning tramite il portale di Azure

Il provisioning e la distribuzione di un'applicazione di intelligenza artificiale con azd possono richiedere almeno 10 minuti o più per il completamento. È possibile tenere traccia dello stato di avanzamento:

- Visualizzando l'avanzamento dettagliato nel [portale di Azure](https://ms.portal.azure.com/). Cercare il gruppo di risorse corrispondente al nome dell'ambiente. Selezionare l'opzione **Distribuzioni** nella barra laterale, quindi monitorare lo stato di distribuzione delle risorse create.
- Visitare il portale di [Azure AI Foundry](https://ai.azure.com). Accedere tramite l'account di Azure personale. Cercare l'hub di intelligenza artificiale corrispondente al gruppo di risorse precedente (potrebbe essere necessario aggiornare alcune volte). Selezionare il progetto di intelligenza artificiale elencato, quindi selezionare **Distribuzioni** nella relativa barra laterale per tenere traccia dello stato per i modelli e le distribuzioni delle applicazioni di chat.

Si esaminerà ora come convalidare il provisioning delle risorse usando il portale di Azure.

1. Passare al [portale di Azure](https://ms.portal.azure.com/) nel browser.
1. Accedere e trovare il gruppo di risorse corrispondente alla sottoscrizione e al nome dell'ambiente immessi in precedenza. Il pannello **Panoramica** dovrebbe essere simile al seguente:

    ![Panoramica del gruppo di risorse del portale di Azure](./media/azure-portal-resource-group.png)

1. Per iniziare, verificare che siano state create le risorse principali dell'[architettura di Azure AI Foundry](https://learn.microsoft.com/azure/ai-studio/concepts/architecture). La figura seguente fornisce altri dettagli su ciò che ognuna di queste risorse fornisce all'applicazione di intelligenza artificiale.

    - **Hub di Azure per l'intelligenza artificiale**: risorsa di Azure di primo livello. Fornisce un ambiente di collaborazione per i team.
    - **Progetto di Azure per l'intelligenza artificiale**: figlio dell'hub. Raggruppa i componenti dell'app per orchestrazione, personalizzazione.
    - **Servizi di Azure per l'intelligenza artificiale**: gestisce gli endpoint del modello.

    ![Architettura di Azure AI Foundry](./media/resource-provider-connected-resources.svg)

1. Successivamente, si verificherà che sia stato effettuato il provisioning di due risorse chiave per implementare il modello di progettazione [Generazione aumentata di recupero](https://learn.microsoft.com/azure/ai-studio/concepts/retrieval-augmented-generation) archiviando il prodotto e i dati dei clienti per il recupero basato su query.

    - **Servizio di ricerca**: per gestire gli indici di ricerca per i dati del catalogo prodotti.
    - **Account Azure Cosmos DB**: per creare un database per i dati degli ordini dei clienti.

1. Successivamente, è possibile verificare che siano disponibili risorse di supporto per la gestione delle esigenze dell'applicazione di intelligenza artificiale:

    - **Application Insights**: per supportare il monitoraggio e la telemetria per l'applicazione distribuita.
    - **Registro contenitori**: per archiviare e gestire le immagini Docker usate nel progetto, privatamente.
    - **Key Vault**: per archiviare in modo sicuro i segreti del progetto (chiavi, credenziali).
    - **Account di archiviazione**: per archiviare i dati correlati alla gestione dei progetti di intelligenza artificiale (inclusi i log).
    - **Regola di avviso del rilevatore intelligente**: rilevamento anomalie di Application Insights (per le richieste).

1. Infine, si noterà una nuova risorsa con tipo **distribuzione online di Machine Learning**. Questa è la risorsa corrispondente all'endpoint del progetto distribuito di Azure per intelligenza artificiale (per il copilota di chat).

## Convalidare la distribuzione con Azure AI Foundry

Il portale di Azure consente di gestire le risorse di Azure sottostanti per il progetto. Il portale di Azure AI Foundry consente di *compilare e gestire* i progetti di intelligenza artificiale stessi, end-to-end, dalla selezione del modello alla distribuzione dell'applicazione. Il comando `azd up` deve aver completato l'intero processo: dal provisioning dei modelli necessari, alla distribuzione e all'hosting dell'endpoint API copilot per l'utilizzo. Verificare che l'applicazione funzioni come previsto.

1. Visitare la pagina **Gestione** in [Azure AI Foundry](https://ai.azure.com/manage) per visualizzare tutti gli hub di Azure per intelligenza artificiale nella sottoscrizione.
1. Selezionare l'hub per il gruppo di risorse per visualizzare tutti i progetti di Azure per Intelligenza artificiale al suo interno.
1. Selezionare il progetto di intelligenza artificiale predefinito nell'hub, quindi selezionare **Distribuzioni** nel menu a sinistra.
1. In **Distribuzioni di modelli** verificare di avere una connessione ad OpenAI di Azure, incluse le distribuzioni di:
    - **gpt-35-turbo**: usato per il completamento di chat, forma il motore principale della chat.
    - **gpt-4**: usato per la valutazione della chat, in particolare per i flussi assistiti dall'intelligenza artificiale.
    - **text-embedding-ada-002**: usato per la vettorializzazione e la ricerca di query.
1. Verificare di avere un endpoint online di Machine Learning con:
    - **modello di chat**: distribuzione di chat basati su intelligenza artificiale con la risorsa dell'endpoint *mloe-xxx*.

    ![Distribuzioni di progetti di Azure per Intelligenza artificiale](./media/azure-ai-project-deployment.png)

## Testare la distribuzione (nel cloud) usando Azure AI Foundry

Per verificare che il copilota distribuito funzioni, usare la funzionalità predefinita del playground di test nel portale di Azure AI Foundry.

![Dettagli della distribuzione della chat](./media/chat-deployment-details.png)

1. In Azure AI Foundry, nell'elenco **Distribuzioni di app** selezionare la distribuzione **distribuzione-chat-xxxx**.
1. Nella pagina **Dettagli** dell'applicazione chat distribuita, selezionare la scheda **Test** per ottenere l'interfaccia di test.

    Si noti che la scheda **Dettagli** include anche `Target URI` e i valori `Key` che è possibile usare con altre applicazioni front-end (ad esempio il sito Web Contoso Outdoor) per integrare questo assistente di chat per le interazioni di utenti reali.

1. Per il momento, testare la distribuzione del copilota con il seguente test di **Input**:

    ```bash
    {"question": "tell me about your hiking shoes", "customerId": "2", "chat_history": []}
    ```

Si dovrebbe ottenere una risposta JSON valida nel componente di output, come illustrato.

![Test di distribuzione chat](./media/chat-deployment-test.png)

## Testare la distribuzione (localmente) usando Visual Studio Code

Il comando **azd up** non solo effettua il provisioning e distribuisce l'applicazione in Azure, ma *configura anche l'ambiente locale* in Visual Studio Code per supportare lo sviluppo locale, il test e l'iterazione. Verifichiamolo.

1. Prima di tutto, verificare che l'ambiente VS Code sia stato configurato correttamente. Cercare un file **config.json** nella cartella radice e verificare che abbia le tre proprietà definite di seguito, con valori validi.

    ```json
    {
        "subscription_id": "xxxxxxxxxxxxxxxx",
        "resource_group": "rg-xxxxxx",
        "workspace_name": "ai-project-xxxxxxx"
    }

    ```

1. Verificare che nella cartella radice sia stato creato un file con estensione **.env**. Deve contenere un elenco di variabili di ambiente *con valori compilati*.

    ```bash
    AZUREAI_HUB_NAME=
    AZUREAI_PROJECT_NAME=
    AZURE_CONTAINER_REGISTRY_ENDPOINT=
    AZURE_CONTAINER_REGISTRY_NAME=
    AZURE_COSMOS_NAME=
    AZURE_ENV_NAME=
    AZURE_KEY_VAULT_ENDPOINT=
    AZURE_KEY_VAULT_NAME=
    AZURE_LOCATION=
    AZURE_OPENAI_API_VERSION=
    AZURE_OPENAI_CHAT_DEPLOYMENT=
    AZURE_OPENAI_ENDPOINT=
    AZURE_OPENAI_NAME=
    AZURE_RESOURCE_GROUP=
    AZURE_SEARCH_ENDPOINT=
    AZURE_SEARCH_NAME=
    AZURE_SUBSCRIPTION_ID=
    AZURE_TENANT_ID=
    COSMOS_ENDPOINT=
    ```

1. Verificare di avere installato gli **strumenti Promptflow** nell'ambiente di sviluppo.

    ```bash
    pf version
    ```

1. Usare lo strumento di **test del flusso pf** per testare l'applicazione del flusso flessibile di **contoso_chat** in locale, con la domanda di esempio seguente. Si noti la sintassi del comando per l'introduzione degli input:

    ```bash
    pf flow test --flow ./contoso_chat --inputs question="tell me about your jackets" customerId="3" chat_history=[]
    ```

Si riceverà una risposta come la seguente:

![Esempio di output dell'app](./media/example_app_output.png)

### Visualizzare le tracce (localmente) con Visual Studio Code

1. È possibile tracciare i dettagli dell'esecuzione con il flag `--ui` come illustrato di seguito.

    ```bash
    pf flow test --flow ./contoso_chat --inputs question="tell me about your jackets" customerId="3" chat_history=[] --ui
    ```

Questo comando deve avviare una **visualizzazione della traccia** nel browser (in una nuova scheda) con una tabella che fornisce dettagli generali sull'esecuzione di tale test, tra cui la latenza e l'utilizzo dei token.

![riga del test pf](./media/pf-flow-test-row.png)

1. Selezionare il record da espandere in una visualizzazione della traccia più dettagliata che consente di esaminare i dettagli più specifici del flusso: dai dati non elaborati (input, output) ai singoli passaggi del flusso e ai componenti pertinenti (ad esempio, i modelli di prompt usati per LLM).

![dettaglio del test pf](./media/pf-flow-test-detail.png)

## Informazioni sulla codebase della Chat di Contoso

È stato effettuato il provisioning del back-end di Azure che è pronto. L'ambiente di sviluppo locale è installato e configurato per l'uso con il back-end di Azure. A questo punto, è sufficiente iniziare a modificare il contenuto per personalizzare e ridistribuire la propria versione dell'applicazione. Di seguito viene esaminato rapidamente come è strutturata la codebase.

> Si tratta di un **elenco semplificato** dell'archivio, eliminando alcuni file e cartelle per maggiore chiarezza.

```bash
data/
    customer_info/  
        create-cosmos-db.ipynb      # Run notebook to upload data to Cosmos DB
        customer_info_1.json        # Example Customer info and orders file
        customer_info_2.json 
        ...
        ...
    product_info/   
        create-azure-search.ipynb   # Run notebook to index product data in AI Search
        products.csv                # Example Products data file

contoso_chat/                       # Main folder for application content
    ai_search.py                    # Search retrieval tool (for RAG design)
    chat.json                       # Example chat file (for Prompty template)
    chat.prompty                    # Chat asset (using Prompty format)
    chat_request.py                 # LLM request tool (for chat completion)
    flow.flex.yaml                  # Promptflow flex flow (define entry point)
    requirements.txt                # App dependencies (define runtime environment)

azure.yaml                          # Main configuration file for Azure Developer CLI  
infra/      
    ai.yaml                         # Define AI model deployments
    app/                            # Infrastructure-as-code config specific to app
    core/                           # Infrastructure-as-code config for core resources
    hooks/                          # Contains post-provisioning scripts
    main.bicep                      # Entry point for Bicep template used by azd
deployment/                         # ai.endpoint config files (named in azure.yaml)
    chat-deployment.yaml 
    chat-model.yaml  
    environment.yaml  

requirements.txt
```

Quando si desidera personalizzare il codice:

- Se si apportano modifiche all'app (in `contoso_chat/`) è sufficiente eseguire `azd deploy` per ridistribuire l'applicazione nel back-end di cui è stato eseguito il provisioning in precedenza. Non sono necessari altri passaggi di provisioning o interventi manuali.
- Se si apportano modifiche alle risorse (nella cartella `infra/`), eseguire `azd up` per eseguire di nuovo il provisioning e ridistribuire l'applicazione. Deve selezionare automaticamente i valori di configurazione precedenti da `.azure/` e modificarli.

## Facoltativo: personalizzare e ridistribuire il copilota

È il momento di creare un copilota personalizzato. Ecco alcune cose che è possibile esplorare, per fare una prova.

Tenere presente per ognuna di queste opzioni:

- Usare `azd deploy` per ridistribuire l'applicazione se è stato modificato solo il codice dell'app.
- Usare `azd up` per eseguire nuovamente il provisioning e la ridistribuzione dell'applicazione se è stata modificata la configurazione delle risorse.

### Personalizzare i dati della cronologia cliente e degli ordini

1. Esaminare i dati di esempio in **dati/customer_info** per dare un senso allo schema predefinito.
1. Esplorare il notebook **data/create-cosmos-db.ipynb** per un approccio code-first agli aggiornamenti dei dati.
1. **Modificare** i dati di esempio ed **eseguire** il notebook per modificare il database predefinito di Azure CosmosDB.
1. **Ridistribuire** l'app. Provare una domanda di test per verificare che vengano restituiti nuovi dati del cliente.

### Personalizzare i dati del catalogo prodotti

1. Esaminare i dati di esempio in **data/product_info/** per un'idea dello schema predefinito.
1. Esplorare il notebook **create-azure-search.ipynb** per un approccio code-first agli aggiornamenti degli indici.
1. **Modificare** i dati di esempio ed **eseguire** il notebook per modificare gli indici predefiniti di Azure AI Search.
1. **Ridistribuire** l'app. Provare una domanda di test per verificare che vengano restituiti nuovi dati del prodotto.

### Personalizzare il modello di richiesta

1. Esaminare il file **contoso_chat/chat.prompty** per un'idea del modello di richiesta predefinito.
1. Esaminare **contoso_chat/chat.json** per comprendere lo schema dei dati di esempio per il test.
1. **Modificare** il modello (messaggio di sistema, sicurezza, documentazione o istruzioni).
1. **Modificare** i dati di esempio, se necessario.
1. **Usare** l'interfaccia della riga di comando di promptflow per testare il flusso in locale con il nuovo modello di richiesta.
1. **Installare e usare** l'estensione Prompty per creare un nuovo modello di richiesta da zero.

### Esplorare l'automazione della valutazione e della pipeline

Sostituire il set di dati di test usato per eseguire la pipeline di valutazione dell'app tramite GitHub Actions con i propri dati. Il set di dati di test si trova nella cartella **dati** del progetto ed è in formato **.jsonl**.

1. Sostituire il file del set di dati di test con i propri dati.
1. Avviare, quindi, la pipeline di valutazione eseguendo il push delle modifiche nel ramo principale del repository di cui è stata creata una copia con fork.

    La pipeline di valutazione verrà eseguita automaticamente ed è possibile controllare i risultati nella scheda GitHub Actions del repository.

1. È possibile personalizzare la pipeline di valutazione modificando il file **evaluate.yaml** nella cartella **.github/workflows** del progetto e lo script **evaluations_chat.py** nella cartella **valutazioni**.

## Eseguire la pulizia ed eliminare le risorse di Azure

Questo progetto usa modelli e servizi (ad esempio Azure AI Search) che possono comportare costi più complessi se lasciati in esecuzione per lungo tempo. Quando si finisce di esplorare il modello di Azure Developer di Azure AI, è necessario eliminare le risorse create per evitare costi di Azure non necessari. È possibile farlo eseguendo il comando seguente nel terminale di VS Code:

```bash
azd down
```

Ciò non solo inverte i passaggi eseguiti per effettuare il provisioning e la distribuzione dell'applicazione, ma effettua anche passaggi aggiuntivi per *eliminare* le risorse che altrimenti possono essere mantenute nello stato di "eliminazione temporanea", con effetti sulla possibilità di riutilizzare i nomi delle risorse o recuperare la quota del modello. **Questo comando richiederà informazioni su queste azioni durante l'arresto, quindi assicurarsi di rispondere correttamente**.
