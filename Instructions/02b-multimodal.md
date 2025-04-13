---
lab:
  title: Sviluppare un'app di IA generativa multimodale
  description: 'Informazioni su come usare Fonderia Azure AI per sviluppare un''applicazione di IA generativa che supporta input di testo, immagini e audio.'
---

# Sviluppare un'app di IA generativa multimodale

In questo esercizio, verrà usato il modello di IA generativa *Phi-4-multimodal-instruct* per generare risposte a richieste che includono testo, immagini e audio. Verrà sviluppata un'applicazione che fornisce assistenza AI per i prodotti freschi in un negozio di alimentari usando Fonderia Azure AI e il servizio dell'inferenza del modello di Azure per intelligenza artificiale.

Questo esercizio richiede circa **30** minuti.

> **Nota**: questo esercizio si basa su SDK non definitive, che possono essere soggette a modifiche. Se necessario, abbiamo usato versioni specifiche dei pacchetti; che potrebbero non riflettere le versioni disponibili più recenti.

## Creare un progetto Fonderia Azure AI

Per iniziare, creare un progetto Fonderia Azure AI.

1. In un Web browser, aprire il [Portale Fonderia Azure AI](https://ai.azure.com) su `https://ai.azure.com` e accedere usando le credenziali di Azure. Chiudere i suggerimenti o i riquadri di avvio rapido aperti la prima volta che si accede e, se necessario, usare il logo **Fonderia Azure AI** in alto a sinistra per passare alla home page, simile all'immagine seguente:

    ![Screenshot del portale di Azure AI Foundry.](./media/ai-foundry-home.png)

2. Nella home page, selezionare **+ Crea progetto**.
3. Nella procedura guidata **Crea un progetto**, immettere un nome appropriato per il progetto (ad esempio, `my-ai-project`). Se viene suggerito un hub esistente, selezionare l'opzione per crearne uno nuovo. Successivamente, esaminare le risorse Azure che verranno create automaticamente per supportare l'hub e il progetto.
4. Selezionare **Personalizza** e specificare le impostazioni seguenti per l'hub:
    - **Nome hub**: *un nome univoco, ad esempio `my-ai-hub`*
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare un nuovo gruppo di risorse con un nome univoco (ad esempio, `my-ai-resources`) o selezionarne uno esistente*
    - **Posizione**: selezionare una delle aree seguenti\*:
        - Stati Uniti orientali
        - Stati Uniti orientali 2
        - Stati Uniti centro-settentrionali
        - Stati Uniti centro-meridionali
        - Svezia centrale
        - Stati Uniti occidentali
        - Stati Uniti occidentali 3
    - **Connettere i Servizi di Azure AI o Azure OpenAI**: *creare una nuova risorsa di Servizi di intelligenza artificiale con un nome appropriato (ad esempio, `my-ai-services`) o usarne uno esistente*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Al momento della stesura del presente documento, il modello Microsoft *Phi-4-multimodal-instruct* che verrà usato in questo esercizio è disponibile in queste regioni. È possibile controllare la disponibilità a livello di area più recente per modelli specifici nella documentazione di [Fonderia Azure AI ](https://learn.microsoft.com/azure/ai-foundry/how-to/deploy-models-serverless-availability#region-availability). In caso di raggiungimento di un limite di quota di area più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa.

5. Selezionare **Avanti** per esaminare la configurazione. Quindi selezionare **Crea** e attendere il completamento del processo.
6. Quando viene creato il progetto, chiudere tutti i suggerimenti visualizzati e rivedere la pagina del progetto nel portale Fonderia di Azure AI, che dovrebbe essere simile all'immagine seguente:

    ![Screenshot dei dettagli di un progetto di Azure AI nel portale Fonderia di Azure AI.](./media/ai-foundry-project.png)

## Distribuire un modello

Ora è possibile distribuire un modello *Phi-4-multimodal-instruct* per supportare prompt multimodali.

1. Nella barra degli strumenti nella parte superiore destra della pagina del progetto Fonderia Azure AI, usare l'icona **Funzionalità di anteprima** per abilitare la funzionalità **Distribuisci modelli nel servizio di inferenza del modello di Azure per intelligenza artificiale**. Questa funzionalità garantisce che la distribuzione del modello sia disponibile per il servizio di inferenza di Azure AI, che verrà usato nel codice dell'applicazione.
2. Nel riquadro a sinistra del progetto, nella sezione **Risorse personali** selezionare la pagina **Modelli + endpoint**.
3. Nella scheda **Distribuzioni del modello** della pagina **Modelli + endpoint**, nel menu **+ Distribuisci modello** selezionare **Distribuisci modello di base**.
4. Cercare il modello **Phi-4-multimodal-instruct** nell'elenco, quindi selezionarlo e confermarlo.
5. Accettare il contratto di licenza se richiesto e quindi distribuire il modello con le impostazioni seguenti selezionando **Personalizza** nei dettagli della distribuzione:
    - **Nome distribuzione**: *nome univoco per la distribuzione del modello, ad esempio `Phi-4-multimodal` (ricordare il nome assegnato poiché sarà necessario in un secondo momento*)
    - **Tipo di distribuzione**: standard globale
    - **Dettagli della distribuzione**: *usare le impostazioni predefinite*
6. Attendere che lo stato di provisioning della distribuzione sia **completato**.

## Creare un'applicazione client

Dopo aver distribuito il modello, è possibile usare la distribuzione in un'applicazione client.

> **Suggerimento**: è possibile scegliere di sviluppare la propria soluzione usando Python o Microsoft C#  *(presto disponibile)*. Seguire le istruzioni nella sezione appropriata per la lingua scelta.

### Preparare la configurazione dell'applicazione

1. Nel Portale Fonderia Azure AI visualizzare la pagina **Panoramica** per il progetto.
2. Nell'area **Dettagli di progetto** prendere nota della **stringa di connessione del progetto**. Questa stringa di connessione verrà usata per connettersi al progetto in un'applicazione client.
3. Aprire una nuova scheda del browser (mantenendo aperto il Portale Fonderia Azure AI nella scheda esistente). In una nuova scheda del browser, passare al [portale di Azure](https://portal.azure.com) su `https://portal.azure.com`, accedendo con le credenziali di Azure se richiesto.
4. Usare il pulsante **[\>_]** a destra della barra di ricerca, nella parte superiore della pagina, per aprire una nuova sessione di Cloud Shell nel portale di Azure selezionando un ambiente ***PowerShell***. Cloud Shell fornisce un'interfaccia della riga di comando in un riquadro nella parte inferiore del portale di Azure.

    > **Nota**: se in precedenza è stata creata una sessione Cloud Shell che usa un ambiente *Bash*, passare a ***PowerShell***.

5. Nella barra degli strumenti di Cloud Shell scegliere **Vai alla versione classica** dal menu **Impostazioni**. Questa operazione è necessaria per usare l'editor di codice.

6. Nel riquadro PowerShell, immettere i comandi seguenti per clonare il repository GitHub contenente i file di codice per questo esercizio:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Suggerimento**: quando si incollano i comandi in CloudShell, l'ouput può richiedere una grande quantità di buffer dello schermo. È possibile cancellare la schermata immettendo il `cls` comando per rendere più semplice concentrarsi su ogni attività.

7. Dopo aver clonato il repository, passare alla cartella contenente i file di codice dell'applicazione:  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/c-sharp
    ```

8. Nel riquadro della riga di comando di Cloud Shell immettere il comando seguente per installare le librerie che si useranno, ovvero:

    **Python**

    ```
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.3
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.3
    ```

9. Immettere il comando seguente per modificare il file di configurazione fornito:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Il file viene aperto in un editor di codice.

10. Nel file di codice, sostituire il segnaposto **your_project_connection_string** con la stringa di connessione per il progetto (copiata dalla pagina **Panoramica** del Portale Fonderia Azure AI) e il segnaposto **your_model_deployment** con il nome assegnato alla distribuzione modello Phi-4-multimodal-instruct.
11. Dopo aver sostituito i segnaposto con l'editor di codice, usare il comando **CTRL+S** o **Fare clic con il pulsante destro del mouse > Salva** per salvare le modifiche e quindi usare il comando **CTRL+Q** o **Fare clic con il pulsante destro del mouse > Esci** per chiudere l'editor di codice mantenendo aperta la riga di comando di Cloud Shell.

### Scrivere il codice per connettersi al progetto e ottenere un client di chat per il modello

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

2. Nel file di codice prendere nota delle istruzioni esistenti aggiunte all'inizio del file per importare i namespaces (spazi dei nomi) SDK necessari. Quindi, sotto il commento **Aggiungi riferimenti**, aggiungere il codice seguente per fare riferimento ai namespaces (spazi dei nomi) installati in precedenza:

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import (
       SystemMessage,
       UserMessage,
       TextContentItem,
       ImageContentItem,
       ImageUrl,
       AudioContentItem,
       InputAudio,
       AudioContentFormat,
   )
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

3. Nella funzione **principale**, sotto il commento **Ottieni impostazioni di configurazione**, notare che il codice carica i valori della stringa di connessione del progetto e del nome della distribuzione modello definiti nel file di configurazione.
4. Nel commento **Inizializza il client del progetto**, aggiungere il codice seguente per connettersi al progetto Fonderia Azure AI usando le credenziali di Azure con cui si è attualmente eseguito l'accesso:

    **Python**

    ```
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

5. Sotto il commento **Ottieni un client di chat**, aggiungere il seguente codice per creare un oggetto client per chattare con il modello:

    **Python**

    ```
   chat_client = project_client.inference.get_chat_completions_client(model=model_deployment)
    ```

    **C#**

    ```
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```


### Scrivere codice per usare un prompt basato su testo

1. Si noti che il codice include un ciclo per consentire a un utente di immettere un prompt fino a quando non immette "esci". Quindi, nella sezione del ciclo, sotto il commento **Ottieni una risposta a un input di testo**, aggiungere il seguente codice per inviare una richiesta basata sul testo e recuperare la risposta dal modello:

    **Python**

    ```python
   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[TextContentItem(text= prompt)])
       ])
   print(response.choices[0].message.content)
    ```

    **C#**

    ```
   var requestOptions = new ChatCompletionsOptions()
   {
   Model = model_deployment,
   Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(prompt),
       }
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Usare il comando **CTRL+S** per salvare le modifiche al file di codice, senza chiuderlo.

3. Nel riquadro della riga di comando di Cloud Shell, sotto l'editor di codice, inserire il seguente comando per eseguire l'applicazione:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Quando richiesto, immettere `1` per usare un prompt basato sul testo e quindi immettere il prompt `I want to make an apple pie. What kind of apple should I use?`.
5. Rivedere la risposta. Immettere quindi `quit` per uscire dal programma.

### Scrivere codice per usare un prompt basato su immagine

1. Nell'editor di codice del file **chat-app.py**, nella sezione del ciclo, sotto il commento **Ottieni una risposta all'input immagine**, aggiungere il seguente codice per inviare un prompt che includa la seguente immagine:

    ![Una foto di un'arancia.](../labfiles/multimodal/orange.jpg)

    **Python**

    ```python
   image_url = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg"
   image_format = "jpeg"
   request = Request(image_url, headers={"User-Agent": "Mozilla/5.0"})
   image_data = base64.b64encode(urlopen(request).read()).decode("utf-8")
   data_url = f"data:image/{image_format};base64,{image_data}"

   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[
               TextContentItem(text=prompt),
               ImageContentItem(image_url=ImageUrl(url=data_url))
           ]),
       ]
   )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
  string imageUrl = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg";
   ChatCompletionsOptions requestOptions = new ChatCompletionsOptions()
   {
       Messages = {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage([
               new ChatMessageTextContentItem(prompt),
               new ChatMessageImageContentItem(new Uri(imageUrl))
           ]),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Usare il comando **CTRL+S** per salvare le modifiche al file di codice, senza chiuderlo.

3. Nel riquadro della riga di comando di Cloud Shell, sotto l'editor di codice, inserire il seguente comando per eseguire l'applicazione:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Quando richiesto, immettere `2` per usare un prompt basato su immagine e quindi immettere il prompt `I don't know what kind of fruit this is. Can you identify it, and tell me what kinds of food I could make with it?`
5. Rivedere la risposta. Immettere quindi `quit` per uscire dal programma.

### Scrivere codice per usare un prompt basato su audio

1. Nell'editor di codice del file **chat-app.py**, nella sezione del ciclo, sotto il commento **Ottieni una risposta all'input audio**, aggiungere il seguente codice per inviare un prompt che includa il seguente audio:

    <video controls src="./media/manzanas.mp4" title="Sono le 2:15" width="150"></video>

    **Python**

    ```python
   file_path="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3"
   response = chat_client.complete(
           messages=[
               SystemMessage(system_message),
               UserMessage(
                   [
                       TextContentItem(text=prompt),
                       {
                           "type": "audio_url",
                           "audio_url": {"url": file_path}
                       }
                   ]
               )
           ]
       )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
   string audioUrl="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3";
   var requestOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(
               new ChatMessageTextContentItem(prompt),
               new ChatMessageAudioContentItem(new Uri(audioUrl))),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```


2. Usare il comando **CTRL+S** per salvare le modifiche apportate al file di codice. È anche possibile chiudere l'editor di codice (**CTRL+Q**) se si desidera.

3. Nel riquadro della riga di comando di Cloud Shell, sotto l'editor di codice, inserire il seguente comando per eseguire l'applicazione:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Quando richiesto, immettere `3` per usare un prompt basato su audio e quindi immettere il prompt `What is this customer saying in English?`
5. Rivedere la risposta.
6. È possibile continuare a eseguire l'applicazione, selezionando e testando diversi tipi di prompt. Al termine, immettere `quit` per uscire dal programma.

    Se si dispone di tempo, è possibile modificare il codice per usare un prompt di sistema differente e integrare propri file immagine e audio accessibili via Internet.

    > **Nota**: in questa semplice app non è stata implementata la logica per conservare la cronologia delle conversazioni, quindi il modello considererà ogni prompt come una nuova richiesta, senza il contesto del prompt precedente.

## Riepilogo

In questo esercizio sono stati usati SDK Fonderia Azure AI e inferenza di Azure per intelligenza artificiale per sviluppare un'applicazione client basata su un modello multimodale, in grado di generare risposte a testo, immagini e audio.

## Eseguire la pulizia

Al termine dell'esplorazione di Fonderia Azure AI, è importante eliminare le risorse create durante l'esercizio per evitare costi di Azure non necessari.

1. Tornare alla scheda del browser che contiene il portale di Azure (o riaprire il [portale di Azure](https://portal.azure.com) su `https://portal.azure.com` in una nuova scheda del browser) e visualizzare il contenuto del gruppo di risorse in cui sono state distribuite le risorse usate in questo esercizio.
1. Sulla barra degli strumenti selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse e confermarne l'eliminazione.
