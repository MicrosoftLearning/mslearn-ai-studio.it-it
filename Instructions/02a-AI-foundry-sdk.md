---
lab:
  title: Creare un'app di chat di IA generativa
  description: Informazioni su come usare SDK Fonderia Azure AI per compilare un'app che si connetta al progetto e che chatti con un modello linguistico.
---

# Creare un'app di chat di IA generativa

In questo esercizio, verrà usato SDK Fonderia Azure AI per creare una semplice app di chat che si connette a un progetto e chatta con un modello linguistico.

Questo esercizio richiede circa **40** minuti.

> **Nota**: questo esercizio si basa su SDK non definitive, che possono essere soggette a modifiche. Se necessario, abbiamo usato versioni specifiche dei pacchetti; che potrebbero non riflettere le versioni disponibili più recenti.

## Creare un progetto Fonderia Azure AI

Per iniziare, creare un progetto Fonderia Azure AI.

1. In un Web browser, aprire il [Portale Fonderia Azure AI](https://ai.azure.com) su `https://ai.azure.com` e accedere usando le credenziali di Azure. Chiudere tutti i riquadri dei suggerimenti o di avvio rapido che vengono aperti al primo accesso e, se necessario, usare il logo **Fonderia Azure AI** in alto a sinistra per passare alla home page, simile all'immagine seguente (chiudere il riquadro **Aiuto** nel caso sia aperto):

    ![Screenshot del portale di Azure AI Foundry.](./media/ai-foundry-home.png)

1. Nella home page, selezionare **+ Crea progetto**.
1. Nella procedura guidata **Crea un progetto**, immettere un nome appropriato per il progetto. Se viene suggerito un hub esistente, selezionare l'opzione per crearne uno nuovo. Successivamente, esaminare le risorse Azure che verranno create automaticamente per supportare l'hub e il progetto.
1. Selezionare **Personalizza** e specificare le impostazioni seguenti per l'hub:
    - **Nome hub**: *un nome valido per l'hub*
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare o selezionare un gruppo di risorse*
    - **Posizione**: selezionare **Informazioni su come scegliere** e quindi selezionare **gpt-4o** nella finestra Helper posizione e usare l'area consigliata\*
    - **Connettere Servizi di Azure AI o Azure OpenAI**: *Creare una nuova risorsa di Servizi di AI*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse Azure OpenAI sono limitate da quote di modelli regionali. In caso di superamento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa.

1. Selezionare **Avanti** per esaminare la configurazione. Quindi selezionare **Crea** e attendere il completamento del processo.
1. Quando viene creato il progetto, chiudere tutti i suggerimenti visualizzati e rivedere la pagina del progetto nel portale Fonderia di Azure AI, che dovrebbe essere simile all'immagine seguente:

    ![Screenshot dei dettagli di un progetto di Azure AI nel portale Fonderia di Azure AI.](./media/ai-foundry-project.png)

## Implementare un modello di IA generativa

A questo punto è possibile distribuire un modello linguistico di IA generativa per supportare l'applicazione di chat. In questo esempio, verrà utilizzato il modello OpenAI gpt-4o; ma i principi sono gli stessi per qualsiasi modello.

1. Nella barra degli strumenti nella parte superiore destra della pagina del progetto Fonderia Azure AI, usare l'icona **Funzionalità di anteprima** (**&#9215;**) per assicurarsi che la funzionalità **Distribuisci modelli nel servizio di inferenza del modello di Azure per intelligenza artificiale** sia abilitata. Questa funzionalità garantisce che la distribuzione del modello sia disponibile per il servizio di inferenza di Azure AI, che verrà usato nel codice dell'applicazione.
1. Nel riquadro a sinistra del progetto, nella sezione **Risorse personali** selezionare la pagina **Modelli + endpoint**.
1. Nella scheda **Distribuzioni del modello** della pagina **Modelli + endpoint**, nel menu **+ Distribuisci modello** selezionare **Distribuisci modello di base**.
1. Cercare il modello **gpt-4o** nell'elenco e quindi selezionarlo e confermarlo.
1. Distribuire il modello con le impostazioni seguenti selezionando **Personalizza** nei dettagli della distribuzione:
    - **Nome distribuzione**: *nome univoco per la distribuzione del modello*
    - **Tipo di distribuzione**: standard globale
    - **Aggiornamento automatico della versione**: abilitato
    - **Versione del modello**: *selezionare la versione più recente disponibile*
    - **Risorsa di intelligenza artificiale connessa**: *selezionare la connessione alla risorsa Azure OpenAI*
    - **Token al limite di velocità al minuto (migliaia)**: 50.000 *(o il valore massimo disponibile nella sottoscrizione se inferiore a 50.000)*
    - **Filtro contenuto**: predefinitoV2

    > **Nota**: la riduzione del TPM consente di evitare l'eccessivo utilizzo della quota disponibile nella sottoscrizione in uso. 50.000 TPM dovrebbero essere sufficienti per i dati usati in questo esercizio. Se la quota disponibile è inferiore a questa, sarà possibile completare l'esercizio, ma potrebbero verificarsi errori se viene superato il limite di velocità.

1. Attendere il completamento della distribuzione.

## Creare un'applicazione client per chattare con il modello

Ora che è stato distribuito un modello, è possibile usare SDK Fonderia Azure AI e Inferenza del modello di Azure AI per sviluppare un'applicazione che chatti con esso.

> **Suggerimento**: è possibile scegliere di sviluppare la soluzione usando Python o Microsoft C#. Seguire le istruzioni nella sezione appropriata per la lingua scelta.

### Preparare la configurazione dell'applicazione

1. Nel Portale Fonderia Azure AI visualizzare la pagina **Panoramica** per il progetto.
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
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Suggerimento**: quando vengono incollati i comandi in CloudShell, l'ouput può richiedere una grande quantità di buffer dello schermo. È possibile cancellare la schermata immettendo il `cls` comando per rendere più semplice concentrarsi su ogni attività.

1. Dopo aver clonato il repository, passare alla cartella contenente i file di codice dell'applicazione chat:

    Usare il comando seguente a seconda del linguaggio di programmazione scelto.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/c-sharp
    ```

1. Nel riquadro della riga di comando di Cloud Shell, immettere il comando seguente per installare le librerie che verranno utilizzate:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.3
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.3
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

1. Nel file di codice, sostituire il segnaposto **your_project_connection_string** con la stringa di connessione per il progetto (copiata dalla pagina **Panoramica** del Portale Fonderia Azure AI) e il segnaposto **your_model_deployment** con il nome assegnato alla distribuzione modello gpt-4.
1. Dopo aver sostituito i segnaposto con l'editor di codice, usare il comando **CTRL+S** o **Fare clic con il pulsante destro del mouse > Salva** per salvare le modifiche e quindi usare il comando **CTRL+Q** o **Fare clic con il pulsante destro del mouse > Esci** per chiudere l'editor di codice mantenendo aperta la riga di comando di Cloud Shell.

### Scrivere codice per connettersi al progetto e chattare con il modello

> **Suggerimento**: quando si aggiunge codice, assicurarsi di mantenere il rientro corretto.

1. Immettere il comando seguente per modificare il file di codice fornito:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Nel file di codice prendere nota delle istruzioni esistenti aggiunte all'inizio del file per importare i namespaces (spazi dei nomi) SDK necessari. Quindi, trovare il commento **Aggiungi riferimenti** e aggiungere il codice seguente per fare riferimento ai namespaces (spazi dei nomi) installati in precedenza:

    **Python**

    ```python
   # Add references
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import SystemMessage, UserMessage, AssistantMessage
    ```

    **C#**

    ```csharp
   // Add references
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

1. Nella funzione **principale**, sotto il commento **Ottieni impostazioni di configurazione**, notare che il codice carica i valori della stringa di connessione del progetto e del nome della distribuzione modello definiti nel file di configurazione.
1. Trovare il commento **Inizializza il client del progetto** e aggiungere il codice seguente per connettersi al progetto Fonderia Azure AI usando le credenziali di Azure con cui si è attualmente eseguito l'accesso:

    > **Suggerimento**: prestare attenzione a mantenere il livello di rientro corretto per il codice.

    **Python**

    ```python
   # Initialize the project client
   projectClient = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```csharp
   // Initialize the project client
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. Trovare il commento **Ottieni un client di chat** e aggiungere il codice seguente per creare un oggetto client per la chat con un modello:

    **Python**

    ```python
   # Get a chat client
   chat = projectClient.inference.get_chat_completions_client()
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```

    > **Nota**: questo codice usa il client del progetto Fonderia Azure AI per creare una connessione sicura all'endpoint di servizio di inferenza del modello di Azure AI predefinito associato al progetto. È anche possibile connettersi *direttamente* all'endpoint usando l'SDK dell'inferenza del modello di Azure AI, specificando l'URI dell'endpoint visualizzato per la connessione del servizio nel portale Fonderia Azure AI o nella corrispondente pagina delle risorse di Servizi di Azure AI nel portale di Azure e usando una chiave di autenticazione o un token di credenziali Entra. Per altre informazioni sulla connessione al servizio di inferenza del modello di Azure AI, consultare [API di inferenza del modello di Azure AI](https://learn.microsoft.com/azure/machine-learning/reference-model-inference-api).

1. Trovare il commento **Inizializza richiesta con il messaggio di sistema** e aggiungere il codice seguente per inizializzare una raccolta di messaggi con una richiesta di sistema.

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
            SystemMessage("You are a helpful AI assistant that answers questions.")
        ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
   var prompt = new List<ChatRequestMessage>(){
                    new ChatRequestSystemMessage("You are a helpful AI assistant that answers questions.")
                };
    ```

1. Si noti che il codice include un ciclo per consentire a un utente di immettere un prompt fino a quando non immette "esci". Quindi, nella sezione ciclo, individuare il commento **Ottieni un completamento della chat** e aggiungere il codice seguente per aggiungere l'input dell'utente al prompt, recuperare il completamento dal modello e aggiungere il completamento al prompt (in modo da mantenere la cronologia delle chat per le iterazioni future):

    **Python**

    ```python
   # Get a chat completion
   prompt.append(UserMessage(input_text))
   response = chat.complete(
        model=model_deployment,
        messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append(AssistantMessage(completion))
    ```

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new ChatRequestUserMessage(input_text));
   var requestOptions = new ChatCompletionsOptions()
   {
       Model = model_deployment,
       Messages = prompt
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   var completion = response.Value.Content;
   Console.WriteLine(completion);
   prompt.Add(new ChatRequestAssistantMessage(completion));
    ```

1. Usare il comando **CTRL+S** per salvare le modifiche apportate al file di codice.

### Eseguire l'applicazione di chat

1. Nel riquadro della riga di comando di Cloud Shell, sotto l'editor di codice, immettere il seguente comando per eseguire l'applicazione:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Quando richiesto, immettere una domanda, ad esempio `What is the fastest animal on Earth?` ed esaminare la risposta del modello di IA generativa.
1. Provare alcune domande di completamento, ad esempio `Where can I see one?` o `Are they endangered?`. La conversazione dovrebbe continuare, usando la cronologia delle chat come contesto per ogni iterazione.
1. Al termine, immettere `quit` per uscire dal programma.

> **Suggerimento**: se l'app non riesce perché viene superato il limite di velocità. Attendere alcuni secondi e riprovare. Se nella sottoscrizione è disponibile una quota insufficiente, il modello potrebbe non essere in grado di rispondere.

## Usare OpenAI SDK

L'app client viene compilata usando Azure AI Model Inference SDK, il che significa che può essere usato con qualsiasi modello distribuito nel servizio di inferenza del modello di Azure per intelligenza artificiale. Il modello distribuito è un modello OpenAI GPT, che è possibile usare anche con OpenAI SDK.

Verranno ora apportate alcune modifiche al codice per vedere come implementare un'applicazione di chat usando OpenAI SDK.

1. Nella riga di comando di Cloud Shell per la cartella del codice (*python* o *c-sharp*) immettere il comando seguente per installare il pacchetto richiesto:

    **Python**

    ```
   pip install openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.6
   dotnet add package Azure.AI.OpenAI --prerelease
    ```

> **Nota**: è necessaria una versione non definitiva diversa del pacchetto Azure.AI.Projects come soluzione alternativa temporanea per alcune incompatibilità con Azure AI Model Inference SDK.

1. Se il file di codice (*chat-app.py* o *Program.cs*) non è già aperto, immettere il comando seguente per aprirlo nell'editor di codice:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Nella parte superiore del file, aggiungere il/i riferimento/i seguente/i:

    **Python**

    ```python
   import openai
    ```

    **C#**

    ```csharp
   using OpenAI.Chat;
   using Azure.AI.OpenAI;
    ```

1. Trovare il commento **Ottieni un client di chat** e modificare il codice usato per creare un oggetto client come indicato di seguito:

    **Python**

    ```python
   # Get a chat client 
   openai_client = projectClient.inference.get_azure_openai_client(api_version="2024-10-21")
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatClient openaiClient = projectClient.GetAzureOpenAIChatClient(model_deployment);
    ```

    > **Nota**: questo codice usa il client del progetto Fonderia Azure AI per creare una connessione sicura all'endpoint di Servizio OpenAI di Azure predefinito associato al progetto. È anche possibile connettersi *direttamente* all'endpoint usando l'SDK di Azure OpenAI, specificando l'URI dell'endpoint visualizzato per la connessione del servizio nel portale Fonderia Azure AI o nella corrispondente pagina delle risorse di Servizi di intelligenza artificiale o OpenAI di Azure nel portale di Azure e usando una chiave di autenticazione o un token di credenziali Entra. Per altre informazioni sulla connessione al Servizio OpenAI di Azure, vedere [Linguaggi di programmazione supportati da Azure OpenAI](https://learn.microsoft.com/azure/ai-services/openai/supported-languages).

1. Trovare il commento **Inizializza richiesta con il messaggio di sistema** e modificare il codice per inizializzare una raccolta di messaggi con una richiesta di sistema come indicato di seguito:

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
        {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
    ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
    var prompt = new List<ChatMessage>(){
        new SystemChatMessage("You are a helpful AI assistant that answers questions.")
    };
    ```

1. Trovare il commento **Ottieni un completamento della chat** e modificare il codice per aggiungere l'input dell'utente al prompt, recuperare il completamento dal modello e aggiungere il completamento al prompt come indicato di seguito:

    **Python**

    ```python
   # Get a chat completion
   prompt.append({"role": "user", "content": input_text})
   response = openai_client.chat.completions.create(
        model=model_deployment,
        messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append({"role": "assistant", "content": completion})
    ```

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new UserChatMessage(input_text));
   ChatCompletion completion = openaiClient.CompleteChat(prompt);
   var completionText = completion.Content[0].Text;
   Console.WriteLine(completionText);
   prompt.Add(new AssistantChatMessage(completionText));
    ```

1. Usare il comando **CTRL+S** per salvare le modifiche apportate al file di codice.

1. Nel riquadro della riga di comando di Cloud Shell, sotto l'editor di codice, immettere il seguente comando per eseguire l'applicazione:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Testare l'app inviando domande come in precedenza. Al termine, immettere `quit` per uscire dal programma.

    > **Nota**: l'SDK di inferenza del modello di Azure AI e gli SDK di OpenAI usano classi e costrutti di codice simili, quindi il codice richiede modifiche minime. È possibile usare l'SDK di inferenza del modello di Azure AI con *qualsiasi* modello distribuito in un endpoint del servizio di inferenza del modello di Azure AI. SDK di OpenAI funziona solo con i modelli OpenAI, ma è possibile usarlo per i modelli distribuiti in un endpoint del servizio di inferenza del modello di Azure AI o in un endpoint di Azure OpenAI.  

## Riepilogo

In questo esercizio sono stati usati SDK di Fonderia Azure AI, di inferenza del modello Azure AI e di Azure OpenAI per creare un'applicazione client per un modello di IA generativa distribuito in un progetto Fonderia Azure AI.

## Eseguire la pulizia

Al termine dell'esplorazione del portale Azure AI Foundry, è necessario eliminare le risorse create in questo esercizio per evitare di incorrere in costi di Azure non necessari.

1. Tornare alla scheda del browser che contiene il portale di Azure (o riaprire il [portale di Azure](https://portal.azure.com) su `https://portal.azure.com` in una nuova scheda del browser) e visualizzare il contenuto del gruppo di risorse in cui sono state distribuite le risorse usate in questo esercizio.
1. Sulla barra degli strumenti selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse e confermarne l'eliminazione.
