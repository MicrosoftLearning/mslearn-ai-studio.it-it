---
lab:
  title: Ottimizzare un modello linguistico
  description: Informazioni su come usare dati di training aggiuntivi per ottimizzare un modello e personalizzarne il comportamento.
---

# Ottimizzare un modello linguistico

Se si desidera che un modello linguistico si comporti in modo specifico, è possibile usare la progettazione dei prompt per definire il comportamento desiderato. Se si desidera migliorare la coerenza del comportamento desiderato, è possibile scegliere di ottimizzare un modello, confrontandolo con il proprio approccio di progettazione dei prompt per valutare il metodo più adatto alle esigenze.

In questo esercizio, verrà ottimizzato un modello linguistico con Azure AI Foundry che si desidera usare per uno scenario di applicazione di chat personalizzato. Il modello ottimizzato verrà confrontato con un modello di base per valutare se il modello ottimizzato è più adatto alle proprie esigenze.

Si supponga di lavorare per un'agenzia di viaggi per la quale si sta sviluppando un'applicazione di chat per aiutare le persone a pianificare le proprie vacanze. L'obiettivo è creare una chat semplice e accattivante che suggerisca destinazioni e attività. Poiché la chat non è connessa ad alcuna origine dati, **non** deve fornire raccomandazioni specifiche per hotel, voli o ristoranti per essere considerata attendibile dai clienti.

Questo esercizio richiederà circa **60** minuti\*.

> \***Nota**: questa tempistica è una stima basata sull'esperienza media. L'ottimizzazione dipende dalle risorse dell'infrastruttura cloud, che possono richiedere una quantità variabile di tempo per il provisioning a seconda della capacità del data center e della domanda simultanea. Alcune attività in questo esercizio possono impiegare <u>molto</u> tempo per essere completate e richiedono pazienza. Se la procedura richiede diverso tempo, è consigliabile esaminare la [documentazione sull'ottimizzazione di Fonderia Azure AI](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) o fare una pausa.

## Creare un hub e un progetto di intelligenza artificiale nel portale Azure AI Foundry

Per iniziare, è necessario creare il progetto del portale Azure AI Foundry all'interno di un hub Azure AI:

1. In un Web browser, aprire [https://ai.azure.com](https://ai.azure.com) e accedere usando le credenziali di Azure.
1. Dalla home page, selezionare **+ Crea progetto**.
1. Nella procedura guidata **Crea un nuovo progetto**, creare un progetto con le impostazioni seguenti:
    - **Nome progetto**: *un nome univoco per il progetto*
    - Selezionare **Personalizza**
        - **Hub**: *riempimento automatico con nome predefinito*
        - **Sottoscrizione**: *riempimento automatico con l'account con cui è stato effettuato l'accesso*
        - **Gruppo di risorse**: (nuovo) *riempimento automatico con il nome del progetto*
        -  **Posizione**: selezionare **Informazioni su come scegliere** e quindi selezionare **gpt-4-finetune** nella finestra Helper posizione e usare l'area consigliata\*
        - **Connettere Servizi di Azure AI o OpenAI di Azure**: (nuovo) *riempimento automatico con il nome dell'hub selezionato*
        - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello tenant. Le aree elencate nell'helper posizione includono la quota predefinita per i tipi di modello usati in questo esercizio. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa. Altre informazioni sull'[Ottimizzazione delle aree del modello](https://learn.microsoft.com/azure/ai-services/openai/concepts/models?tabs=python-secure%2Cglobal-standard%2Cstandard-chat-completions#fine-tuning-models)

1. Esaminare la configurazione e creare il progetto.
1. Attendere la creazione del progetto.

## Ottimizzare un modello GPT-4

Poiché l'ottimizzazione di un modello richiede del tempo, è opportuno iniziare subito il processo di ottimizzazione e ritornarci dopo aver esplorato un modello di base che non è stato ottimizzato a scopo di confronto.

1. Scaricare il [set di dati di training](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) in `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl` e salvarlo come file JSONL in locale.

    > **Nota**: per impostazione predefinita, il dispositivo potrebbe salvare il file come file .txt. Selezionare tutti i file e rimuovere il suffisso .txt per assicurarsi di salvare il file come JSONL.

1. Passare alla pagina **Ottimizzazione** nella sezione **Crea e personalizza**, usando il menu a sinistra.
1. Selezionare il pulsante per aggiungere un nuovo modello di ottimizzazione, selezionare il modello `gpt-4`, selezionare **Avanti** e quindi **Conferma**.
1. **Ottimizzare** il modello usando la configurazione seguente:
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Suffisso del modello**: `ft-travel`
    - **Risorsa AI connessa**: *selezionare la connessione predefinita creata al momento della creazione dell'hub. Dovrebbe essere selezionata per impostazione predefinita.*
    - **Dati di training**: caricare i file

    <details>  
    <summary><b>Suggerimento per la risoluzione dei problemi</b>: errore di autorizzazioni</summary>
    <p>Se viene visualizzato un errore di autorizzazione, provare i seguenti passaggi per la risoluzione dei problemi:</p>
    <ul>
        <li>Nel portale di Azure, selezionare la risorsa Servizi di intelligenza artificiale.</li>
        <li>Nella scheda Identità, in Gestione risorse, confermare che si tratti dell'identità gestita assegnata dal sistema.</li>
        <li>Passare all'account di archiviazione associato. Nella pagina IAM, aggiungere l'assegnazione di ruolo <em>Proprietario dei dati del BLOB di archiviazione</em>.</li>
        <li>In <strong>Assegna accesso a</strong>, scegliere <strong>Identità gestita</strong>, <strong>+Seleziona membri</strong>, selezionare <strong>Tutte le identità gestite assegnate dal sistema</strong> e selezionare la risorsa dei Servizi di Azure AI.</li>
        <li>Rivedere e assegnare per salvare le nuove impostazioni e ripetere il passaggio precedente.</li>
    </ul>
    </details>

    - **Caricare il file**: selezionare il file JSONL scaricato nel passaggio precedente.
    - **Dati di convalida**: nessuno
    - **Parametri attività**: *mantenere le impostazioni predefinite*
1. Verrà avviata l'ottimizzazione, che potrebbe richiedere del tempo per il completamento.

> **Nota**: l'ottimizzazione e la distribuzione possono richiedere una notevole quantità di tempo (30 minuti o più), quindi potrebbe essere necessario eseguire periodicamente il controllo. È già possibile continuare con il passaggio successivo durante l'attesa.

## Chat con un modello di base

Mentre si attende il completamento del processo di ottimizzazione, è possibile chattare con un modello GPT 4 di base per valutare le prestazioni.

1. Passare alla pagina **Modelli + endpoint** nella sezione **Asset personali** usando il menu a sinistra.
1. Selezionare il pulsante **+ Distribuisci modello** e scegliere l'opzione **Distribuisci modello di base**.
1. Distribuire un`gpt-4` modello con le impostazioni seguenti:
    - **Nome distribuzione**: *un nome univoco per il modello, è possibile usare il valore predefinito*
    - **Tipo di distribuzione**: Standard
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: Predefinito

> **Nota**: se la posizione corrente delle risorse di intelligenza artificiale non dispone di una quota disponibile per il modello che si vuole distribuire, verrà chiesto di scegliere una posizione diversa in cui verrà creata e connessa al progetto una nuova risorsa IA.

1. Quando la distribuzione è completa, selezionare il pulsante **Apri nel playground**.
1. Verificare che il modello di base distribuito `gpt-4` sia selezionato nel riquadro di configurazione.
1. Nella finestra della chat immettere la query `What can you do?` e visualizzare la risposta.
    Le risposte sono molto generiche. Si ricorda che l'obiettivo è creare un'applicazione di chat che susciti il desiderio di viaggiare.
1. Aggiornare il messaggio di sistema nel riquadro di configurazione con la seguente richiesta:

    ```md
    You are an AI assistant that helps people plan their holidays.
    ```

1. Selezionare **Applica modifiche**, quindi selezionare **Cancella chat** e chiedere nuovamente `What can you do?` Come risposta, l'assistente potrebbe comunicare all'utente che può aiutare a prenotare voli, hotel e auto a noleggio per il viaggio. Si intende evitare questo comportamento.
1. Aggiornare di nuovo il messaggio di sistema con un nuovo prompt:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Selezionare **Applica modifiche** e **Cancella chat**.
1. Continuare a testare l'applicazione di chat per verificare che non fornisca informazioni non basate sui dati recuperati. Ad esempio, porre le domande seguenti ed esaminare le risposte del modello, prestando particolare attenzione al tono e allo stile di scrittura usato dal modello per rispondere:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

## Esaminare il file di training

Il modello di base sembra funzionare abbastanza bene, ma potrebbe essere necessario cercare uno stile di conversazione specifico dall'app di IA generativa. I dati di training usati per l'ottimizzazione offrono la possibilità di creare esempi espliciti dei tipi di risposta desiderati.

1. Aprire il file JSONL scaricato in precedenza (è possibile aprirlo in qualsiasi editor di testo)
1. Esaminare l'elenco dei documenti JSON nel file di dati di training. Il primo dovrebbe essere simile a questo (formattato per la leggibilità):

    ```json
    {"messages": [
        {"role": "system", "content": "You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms. You should not provide any hotel, flight, rental car or restaurant recommendations. Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday."},
        {"role": "user", "content": "What's a must-see in Paris?"},
        {"role": "assistant", "content": "Oh la la! You simply must twirl around the Eiffel Tower and snap a chic selfie! After that, consider visiting the Louvre Museum to see the Mona Lisa and other masterpieces. What type of attractions are you most interested in?"}
        ]}
    ```

    Ogni interazione di esempio nell'elenco include lo stesso messaggio di sistema testato con il modello di base, una richiesta dell'utente correlata a una query di viaggio e una risposta. Lo stile delle risposte nei dati di training consentirà al modello ottimizzato di apprendere come deve rispondere.

## Distribuire il modello ottimizzato

Al termine dell'ottimizzazione, è possibile distribuire il modello ottimizzato.

1. Passare alla pagina **Ottimizzazione** in **Crea e personalizza** per trovare il processo di ottimizzazione e il relativo stato. Se è ancora in esecuzione, è possibile scegliere di continuare a chattare con il modello di base distribuito o fare una pausa. Se l'operazione è stata completata, è possibile continuare.
1. Selezionare il modello ottimizzato. Selezionare la scheda **Metriche** ed esplorare le metriche ottimizzate.
1. Distribuire il modello ottimizzato con le configurazioni seguenti:
    - **Nome distribuzione**: *un nome univoco per il modello, è possibile usare il valore predefinito*
    - **Tipo di distribuzione**: Standard
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: Predefinito
1. Attendere il completamento della distribuzione prima di sottoporla a test. L'operazione potrebbe richiedere alcuni minuti. Controllare lo **stato provisioning** fino a quando non è stato completato (potrebbe essere necessario aggiornare il browser per visualizzare lo stato aggiornato).

## Test del modello ottimizzato

Dopo aver distribuito il modello ottimizzato, è possibile testarlo come si è testato il modello di base distribuito.

1. Quando la distribuzione è pronta, passare al modello ottimizzato e selezionare **Apri nel playground**.
1. Verificare che il messaggio di sistema includa queste istruzioni:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Testare il modello ottimizzato per valutare se ora il comportamento è più coerente. Ad esempio, porre di nuovo le domande seguenti ed esplorare le risposte del modello:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

1. Dopo aver esaminato le risposte, come vengono confrontate con quelle del modello di base?

## Eseguire la pulizia

Al termine dell'esplorazione di Azure AI Foundry, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

- Passare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com`.
- Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
- Selezionare il gruppo di risorse creato per questo esercizio.
- Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
- Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
