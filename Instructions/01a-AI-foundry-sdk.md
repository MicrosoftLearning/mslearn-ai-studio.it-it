---
lab:
  title: Creare un'app di chat con SDK Fonderia Azure AI
---

# Creare un'app di chat con SDK Fonderia Azure AI

In questo esercizio, si usa SDK Fonderia Azure AI per creare una semplice app di chat.

Questo esercizio richiede circa **20** minuti.

## Creare un progetto Fonderia Azure AI

Per iniziare, creare un progetto Fonderia Azure AI.

1. In un Web browser, aprire il [Portale Fonderia Azure AI](https://ai.azure.com) su `https://ai.azure.com` e accedere usando le credenziali di Azure. Chiudere i suggerimenti o i riquadri di avvio rapido aperti la prima volta che si accede e, se necessario, usare il logo **Fonderia Azure AI** in alto a sinistra per passare alla home page, simile all'immagine seguente:

    ![Screenshot del portale di Azure AI Foundry.](./media/ai-foundry-home.png)

1. Nella home page, selezionare **+ Crea progetto**.
1. Nella procedura guidata **Crea un progetto** immettere un nome di progetto appropriato per (ad esempio, `my-ai-project`) e quindi rivedere le risorse di Azure che verranno create automaticamente per supportare il progetto.
1. Selezionare **Personalizza** e specificare le impostazioni seguenti per l'hub:
    - **Nome hub**: *un nome univoco, ad esempio `my-ai-hub`*
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare un nuovo gruppo di risorse con un nome univoco (ad esempio, `my-ai-resources`) o selezionarne uno esistente*
    - **Posizione**: effettuare una scelta casuale dl seguente elenco\*:
        - Stati Uniti orientali
        - Stati Uniti orientali 2
        - Stati Uniti centro-settentrionali
        - Stati Uniti centro-meridionali
        - Svezia centrale
        - Stati Uniti occidentali
        - Stati Uniti occidentali 3
    - **Connettere i Servizi di Azure AI o Azure OpenAI**: *creare una nuova risorsa di Servizi di intelligenza artificiale con un nome appropriato (ad esempio, `my-ai-services`) o usarne uno esistente*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le quote dei modelli sono vincolate dalle quote locali a livello tenant. La scelta di un'area casuale consente di distribuire la disponibilità della quota quando più utenti lavorano nello stesso tenant. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa.

1. Selezionare **Avanti** per esaminare la configurazione. Quindi selezionare **Crea** e attendere il completamento del processo.
1. Quando viene creato il progetto, chiudere tutti i suggerimenti visualizzati e rivedere la pagina del progetto nel portale Fonderia di Azure AI, che dovrebbe essere simile all'immagine seguente:

    ![Screenshot dei dettagli di un progetto di Azure AI nel portale Fonderia di Azure AI.](./media/ai-foundry-project.png)

## Implementare un modello di IA generativa

A questo punto è possibile distribuire un modello linguistico di IA generativa per supportare l'applicazione di chat. In questo esempio si userà il modello Microsoft Phi-4; ma i principi sono gli stessi per qualsiasi modello.

1. Nel riquadro a sinistra del progetto, nella sezione **Risorse personali** selezionare la pagina **Modelli + endpoint**.
1. Nella scheda **Distribuzioni del modello** della pagina **Modelli + endpoint**, nel menu **+ Distribuisci modello** selezionare **Distribuisci modello di base**.
1. Cercare il modello **Phi-4** nell'elenco e quindi selezionarlo e confermarlo.
1. Selezionare l'opzione di distribuzione **API serverless con Sicurezza dei contenuti di Azure AI**.
1. Distribuire il modello con le impostazioni seguenti selezionando **Personalizza** nei dettagli della distribuzione:
    - **Nome distribuzione**: *nome univoco per la distribuzione del modello, ad esempio `phi-4-model` (ricordare il nome assegnato poiché sarà necessario in un secondo momento*)
    - **Filtro contenuto**: abilitato

## Creare un'applicazione client per chattare con il modello

Ora che è stato distribuito un modello, è possibile usare SDK Fonderia Azure AI per sviluppare un'applicazione che chatti con esso.

### Preparare la configurazione dell'applicazione

1. Nel Portale Fonderia Azure AI visualizzare la pagina **Panoramica** per il progetto.
1. Nell'area **Dettagli di progetto** prendere nota della **stringa di connessione del progetto**. Questa stringa di connessione verrà usata per connettersi al progetto in un'applicazione client.
1. Aprire una nuova scheda del browser (mantenendo aperto il Portale Fonderia Azure AI nella scheda esistente). In una nuova scheda del browser, passare al [portale di Azure](https://portal.azure.com) su `https://portal.azure.com`, accedendo con le credenziali di Azure se richiesto.
1. Usare il pulsante **[\>_]** a destra della barra di ricerca, nella parte superiore della pagina, per aprire una nuova sessione di Cloud Shell nel portale di Azure selezionando un ambiente ***PowerShell***. Cloud Shell fornisce un'interfaccia della riga di comando in un riquadro nella parte inferiore del portale di Azure.

    > **Nota**: se in precedenza è stata creata una sessione Cloud Shell che usa un ambiente *Bash*, passare a ***PowerShell***.

1. Nella barra degli strumenti di Cloud Shell scegliere **Vai alla versione classica** dal menu **Impostazioni**. Questa operazione è necessaria per usare l'editor di codice.

1. Nel riquadro PowerShell immettere i comandi seguenti per clonare il repository GitHub per questo esercizio:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

1. Dopo aver clonato il repository, passare alla cartella **mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python**:

    ```
    cd mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python
    ```

1. Nel riquadro della riga di comando di Cloud Shell immettere il comando seguente per installare le librerie Python che si useranno, ovvero:
    - **python-dotenv** : usato per caricare le impostazioni da un file di configurazione dell'applicazione.
    - **azure-identity**: usato per eseguire l'autenticazione con le credenziali Entra ID.
    - **azure-ai-projects**: usato per lavorare con un progetto Fonderia Azure AI.
    - **azure-ai-inference**: usato per chattare con un modello di IA generativa.

    ```
    pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

1. Immettere il comando seguente per modificare il file di configurazione Python **.env** fornito:

    ```
    code .env
    ```

    Il file viene aperto in un editor di codice.

1. Nel file di codice sostituire il segnaposto **your_project_endpoint** con la stringa di connessione per il progetto (copiato dalla pagina **Panoramica** del progetto nel Portale Fonderia Azure AI) e il segnaposto **your_model_deployment** con il nome assegnato alla distribuzione del modello Phi-4.
1. Dopo aver sostituito i segnaposto, usare il comando **CTRL+S** per salvare le modifiche e quindi usare il comando **CTRL+Q** per chiudere l'editor di codice mantenendo aperta la riga di comando di Cloud Shell.

### Scrivere codice per connettersi al progetto e chattare con il modello

> **Suggerimento**: quando si aggiunge codice al file di codice Python, assicurarsi di mantenere il rientro corretto.

1. Immettere il comando seguente per modificare il file di codice Python **chat-app.py** fornito:

    ```
    code chat-app.py
    ```

1. Nel file di codice prendere nota delle istruzioni di **importazione** esistenti aggiunte all'inizio del file. Quindi, sotto il commento **# Aggiungi riferimenti ai progetti di intelligenza artificiale**, aggiungere il codice seguente per fare riferimento alla libreria di Progetti di Azure per intelligenza artificiale:

    ```python
    from azure.ai.projects import AIProjectClient
    ```

1. Nella funzione **principale**, sotto il commento **# Ottieni impostazioni di configurazione**, si noti che il codice carica i valori della stringa di connessione del progetto e del nome della distribuzione del modello definiti nel file **.env**.
1. Nel commento **# Inizializza il client del progetto** aggiungere il codice seguente per connettersi al progetto Fonderia Azure AI usando le credenziali di Azure con cui si è attualmente eseguito l'accesso:

    ```python
    project = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential()
        )
    ```
    
1. Nel commento **# Ottieni un client di chat** aggiungere il codice seguente per creare un oggetto client per la chat con un modello:

    ```python
    chat = project.inference.get_chat_completions_client()
    ```

1. Si noti che il codice include un ciclo per consentire a un utente di immettere un prompt fino a quando non immette "esci". Quindi, nella sezione del ciclo, sotto il commento **# Ottieni un completamento della chat**, aggiungere il codice seguente per inviare il prompt e recuperare il completamento dal modello:

    ```python
    response = chat.complete(
        model=model_deployment,
        messages=[
            {"role": "system", "content": "You are a helpful AI assistant that answers questions."},
            {"role": "user", "content": input_text},
            ],
        )
    print(response.choices[0].message.content)
    ```

1. Usare il comando **CTRL+S** per salvare le modifiche apportate al file di codice e quindi usare il comando **CTRL+Q** per chiudere l'editor di codice mantenendo aperta la riga di comando di Cloud Shell.

### Eseguire l'applicazione di chat

1. Nel riquadro della riga di comando di Cloud Shell immettere il comando seguente per eseguire il codice Python:

    ```
    python chat-app.py
    ```

1. Quando richiesto, immettere una domanda, ad esempio `What is the fastest animal on Earth?` ed esaminare la risposta del modello di IA generativa.
1. Provare a immettere altre domande. Al termine, immettere `quit` per uscire dal programma.

## Riepilogo

In questo esercizio è stato usato SDK Fonderia Azure AI per creare un'applicazione client per un modello di IA generativa distribuito in un progetto Fonderia Azure AI.

## Eseguire la pulizia

Al termine dell'esplorazione del portale Azure AI Foundry, è necessario eliminare le risorse create in questo esercizio per evitare di incorrere in costi di Azure non necessari.

1. Tornare alla scheda del browser che contiene il portale di Azure (o riaprire il [portale di Azure](https://portal.azure.com) su `https://portal.azure.com` in una nuova scheda del browser) e visualizzare il contenuto del gruppo di risorse in cui sono state distribuite le risorse usate in questo esercizio.
1. Sulla barra degli strumenti selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse e confermarne l'eliminazione.
