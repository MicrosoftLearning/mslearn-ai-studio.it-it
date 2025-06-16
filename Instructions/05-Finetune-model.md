---
lab:
  title: Ottimizzare un modello linguistico
  description: Informazioni su come usare dati di training per ottimizzare un modello e personalizzarne il comportamento.
---

# Ottimizzare un modello linguistico

Se si desidera che un modello linguistico si comporti in modo specifico, è possibile usare la progettazione dei prompt per definire il comportamento desiderato. Se si desidera migliorare la coerenza del comportamento desiderato, è possibile scegliere di ottimizzare un modello, confrontandolo con il proprio approccio di progettazione dei prompt per valutare il metodo più adatto alle esigenze.

In questo esercizio, verrà ottimizzato un modello linguistico con Azure AI Foundry che si desidera usare per uno scenario di applicazione di chat personalizzato. Il modello ottimizzato verrà confrontato con un modello di base per valutare se il modello ottimizzato è più adatto alle proprie esigenze.

Si supponga di lavorare per un'agenzia di viaggi per la quale si sta sviluppando un'applicazione di chat per aiutare le persone a pianificare le proprie vacanze. L'obiettivo è creare una chat semplice e accattivante che suggerisca destinazioni e attività con un tono coerente e informale.

Questo esercizio richiederà circa **60** minuti\*.

> \***Nota**: questa tempistica è una stima basata sull'esperienza media. L'ottimizzazione dipende dalle risorse dell'infrastruttura cloud, che possono richiedere una quantità variabile di tempo per il provisioning a seconda della capacità del data center e della domanda simultanea. Alcune attività in questo esercizio possono impiegare <u>molto</u> tempo per essere completate e richiedono pazienza. Se la procedura richiede diverso tempo, è consigliabile esaminare la [documentazione sull'ottimizzazione di Fonderia Azure AI](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) o fare una pausa. È possibile che alcuni processi possano dare un errore di timeout o essere eseguiti in modo indefinito. Alcune delle tecnologie usate in questo esercizio sono in anteprima o in fase di sviluppo. È possibile che si verifichino alcuni comportamenti, avvisi o errori imprevisti.

## Distribuire un modello nel progetto Fonderia Azure AI

Per iniziare, distribuire un modello in un progetto Fonderia Azure AI.

1. In un Web browser, aprire il [Portale Fonderia Azure AI](https://ai.azure.com) su `https://ai.azure.com` e accedere usando le credenziali di Azure. Chiudere tutti i riquadri dei suggerimenti o di avvio rapido che vengono aperti al primo accesso e, se necessario, usare il logo **Fonderia Azure AI** in alto a sinistra per passare alla home page, simile all'immagine seguente (chiudere il riquadro **Aiuto** nel caso sia aperto):

    ![Screenshot del portale di Azure AI Foundry.](./media/ai-foundry-home.png)

1. Nella home page, nella sezione **Esplora modelli e funzionalità**, cercare il modello `gpt-4o`, che verrà usato nel progetto.
1. Nei risultati della ricerca, selezionare il modello **gpt-4o** per visualizzarne i dettagli e quindi nella parte superiore della pagina selezionare **Usa questo modello**.
1. Quando viene richiesto di creare un progetto, immettere un nome valido per il progetto ed espandere **Opzioni avanzate**.
1. Selezionare **Personalizza** e specificare le impostazioni seguenti per il progetto:
    - **Risorsa di Fonderia Azure AI**: *nome valido per la risorsa di Fonderia Azure AI*
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare o selezionare un gruppo di risorse*
    - **Area geografica**: *selezionare una delle aree geografiche seguenti*:\*
        - Stati Uniti orientali 2
        - Stati Uniti centro-settentrionali
        - Svezia centrale

    > \* Al momento della stesura di questo documento, queste aree geografiche supportano l'ottimizzazione per i modelli gpt-4o.

1. Selezionare **Crea** e attendere la creazione del progetto, inclusa la distribuzione del modello gpt-4 selezionato.
1. Quando il progetto viene creato, il playground della chat verrà aperto automaticamente.
1. Nel riquadro **Installazione** prendere nota del nome della distribuzione modello, che deve essere **gpt-4o**. È possibile confermarlo visualizzando la distribuzione nella pagina **Modelli ed endpoint** (è sufficiente aprire tale pagina nel riquadro di spostamento a sinistra).
1. Nel riquadro di spostamento a sinistra, selezionare **Panoramica** per visualizzare la pagina principale del progetto, che avrà questo aspetto:

    > **Nota**: se viene visualizzato un errore *Autorizzazioni insufficienti**, usare il pulsante **Correggi** per risolverlo.

    ![Screenshot della pagina di panoramica del progetto Fonderia Azure AI.](./media/ai-foundry-project.png)

## Ottimizzare un modello

Poiché il completamento dell'ottimizzazione di un modello richiede del tempo, è opportuno iniziare subito il processo di ottimizzazione e ritornarci dopo aver esplorato un modello gpt-4o di base che non è stato già distribuito.

1. Scaricare il [set di dati di training](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) in `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl` e salvarlo come file JSONL in locale.

    > **Nota**: per impostazione predefinita, il dispositivo potrebbe salvare il file come file .txt. Selezionare tutti i file e rimuovere il suffisso .txt per assicurarsi di salvare il file come JSONL.

1. Passare alla pagina **Ottimizzazione** nella sezione **Crea e personalizza**, usando il menu a sinistra.
1. Selezionare il pulsante per aggiungere un nuovo modello di ottimizzazione, selezionare il modello **gpt-4o** e quindi **Avanti**.
1. **Ottimizzare** il modello usando la configurazione seguente:
    - **Metodo di personalizzazione**: supervisione
    - **** Modello di base: *selezionare la versione per impostazione predefinita di **gpt-4o***
    - **Dati di training**: *selezionare l'opzione **Aggiungi dati di training**, caricare e applicare il file .jsonl scaricato in precedenza*
    - **Suffisso del modello**: `ft-travel`
    - **Valore di inizializzazione**: *casuale
1. Inviare i dettagli di ottimizzazione e il processo verrà avviato. Il completamento può richiedere del tempo. È possibile continuare con la sezione successiva dell'esercizio mentre si attende.

> **Nota**: l'ottimizzazione e la distribuzione possono richiedere una notevole quantità di tempo (30 minuti o più), quindi potrebbe essere necessario eseguire periodicamente il controllo. Per visualizzare altri dettagli sullo stato di avanzamento finora, selezionare il processo del modello di ottimizzazione e visualizzarne la scheda **Log**.

## Chat con un modello di base

Durante l'attesa per il completamento del processo di ottimizzazione, è possibile chattare con un modello GPT 4o di base per valutare le prestazioni.

1. Nel riquadro di spostamento a sinistra, selezionare **Playground** e aprire la**chat di playground**.
1. Verificare che il modello di base distribuito **gpt-4o** sia selezionato nel riquadro di configurazione.
1. Nella finestra della chat immettere la query `What can you do?` e visualizzare la risposta.

    Le risposte possono essere piuttosto generiche. Si ricorda che l'obiettivo è creare un'applicazione di chat che susciti il desiderio di viaggiare.

1. Aggiornare il messaggio di sistema nel riquadro di configurazione con la seguente richiesta:

    ```
    You are an AI assistant that helps people plan their travel.
    ```

1. Selezionare **Applica modifiche** per aggiornare il messaggio di sistema.
1. Nella finestra chat immettere nuovamente la query `What can you do?` e visualizzare la risposta.
1 In risposta, l'assistente potrebbe indicare che può essere d'aiuto per la prenotazione di voli, hotel e auto a noleggio per un viaggio. Si intende evitare questo comportamento.

1. Aggiornare di nuovo il messaggio di sistema con un nuovo prompt:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```
.
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

    > **Suggerimento**: usare il pulsante **Aggiorna** nella pagina di ottimizzazione per aggiornare la visualizzazione. Se il processo di ottimizzazione scompare completamente, aggiornare la pagina nel browser.

1. Selezionare il collegamento al processo di ottimizzazione per aprire la relativa pagina dei dettagli. Selezionare la scheda **Metriche** ed esplorare le metriche ottimizzate.
1. Distribuire il modello ottimizzato con le configurazioni seguenti:
    - **Nome distribuzione**: *nome univoco per la distribuzione del modello*
    - **Tipo di distribuzione**: Standard
    - **Token al limite di velocità al minuto (migliaia)**: 50.000 *(o il valore massimo disponibile nella sottoscrizione se inferiore a 50.000)*
    - **Filtro contenuto**: Predefinito
1. Attendere il completamento della distribuzione prima di sottoporla a test. L'operazione potrebbe richiedere alcuni minuti. Controllare lo **stato provisioning** fino a quando non è stato completato (potrebbe essere necessario aggiornare il browser per visualizzare lo stato aggiornato).

## Test del modello ottimizzato

Dopo aver distribuito il modello ottimizzato, è possibile testarlo come si è testato il modello di base distribuito.

1. Quando la distribuzione è pronta, passare al modello ottimizzato e selezionare **Apri nel playground**.
1. Verificare che il messaggio di sistema includa queste istruzioni:

    ```
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
