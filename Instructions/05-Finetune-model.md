---
lab:
  title: Ottimizzare un modello linguistico per il completamento della chat in Studio AI della piattaforma Azure
---

# Ottimizzare un modello linguistico per il completamento della chat in Studio AI della piattaforma Azure

Se si desidera che un modello linguistico si comporti in modo specifico, è possibile usare la progettazione dei prompt per definire il comportamento desiderato. Quando si intende migliorare la coerenza del comportamento desiderato, è possibile scegliere di ottimizzare un modello, confrontandolo con l'approccio di progettazione dei prompt per valutare il metodo più adatto alle proprie esigenze.

In questo esercizio, verrà ottimizzato un modello linguistico con Studio AI della piattaforma Azure che si desidera usare per uno scenario di applicazione di chat personalizzato. Il modello ottimizzato verrà confrontato con un modello di base per valutare se il modello ottimizzato è più adatto alle proprie esigenze.

Si supponga di lavorare per un'agenzia di viaggi per la quale si sta sviluppando un'applicazione di chat per aiutare le persone a pianificare le loro vacanze. L'obiettivo è creare una chat semplice e accattivante che suggerisca destinazioni e attività. Poiché la chat non è connessa ad alcuna origine dati, **non ** deve fornire raccomandazioni specifiche per hotel, voli o ristoranti per essere considerata attendibile dai clienti.

Questo esercizio richiederà circa **60** minuti.

## Creare un hub e un progetto di intelligenza artificiale in Studio AI della piattaforma Azure

Per iniziare, creare un progetto di Studio AI della piattaforma Azure all'interno di un hub di Azure per intelligenza artificiale:

1. In un Web browser, aprire [https://ai.azure.com](https://ai.azure.com) e accedere usando le credenziali di Azure.
1. Selezionare la pagina **Home** e quindi **+ Nuovo progetto**.
1. Nella procedura guidata **Crea un nuovo progetto**, creare un progetto con le impostazioni seguenti:
    - **Nome progetto**: *un nome univoco per il progetto*
    - **Hub**: *creare un nuovo hub con le impostazioni seguenti:*
    - **Nome hub**: *un nome univoco*.
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *un nuovo gruppo di risorse*
    - **Località**: selezionare **Informazioni su come scegliere** e quindi selezionare **gpt-35-turbo** nella finestra Helper posizione e usare l'area consigliata\*
    - **Connettere Servizi di Azure AI o OpenAI di Azure**: (nuovo) *riempimento automatico con il nome dell'hub selezionato*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello tenant. Le aree elencate nell'helper posizione includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa. Altre informazioni sulla [disponibilità di modelli per area](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Esaminare la configurazione e creare il progetto.
1. Attendere la creazione del progetto.

## Ottimizzare un modello GPT-3.5

Poiché l'ottimizzazione di un modello richiede del tempo, è opportuno iniziare prima di tutto con il processo di ottimizzazione. Prima di poter ottimizzare un modello, è necessario un set di dati.

1. Salvare il set di dati di training come file JSONL in locale: [https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-finetune.jsonl](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl)

    > **Nota**: per impostazione predefinita, il dispositivo potrebbe salvare il file come file .txt. Selezionare tutti i file e rimuovere il suffisso .txt per assicurarsi di salvare il file come JSONL.

1. Passare alla pagina **Ottimizzazione** nella sezione **Strumenti**, usando il menu a sinistra.
1. Selezionare il pulsante per aggiungere un nuovo modello di ottimizzazione, selezionare il `gpt-35-turbo` modello e selezionare **Conferma**.
1. **Ottimizzare** il modello usando la configurazione seguente:
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Suffisso del modello**: `ft-travel`
    - **Connessione di OpenAI di Azure**: *selezionare la connessione creata al momento della creazione dell'hub*
    - **Dati di training**: caricare i file

    <details>  
    <summary><b>Suggerimento per la risoluzione dei problemi</b>: errore di autorizzazioni</summary>
    <p>Se viene visualizzato un errore di autorizzazioni quando si crea un nuovo prompt flow, provare a risolvere i problemi nel seguente modo:</p>
    <ul>
        <li>Nel portale di Azure, selezionare la risorsa Servizi di intelligenza artificiale.</li>
        <li>Nella pagina IAM, nella scheda Identità, verificare che si tratti dell'identità gestita assegnata dal sistema.</li>
        <li>Passare all'account di archiviazione associato. Nella pagina IAM, aggiungere l'assegnazione di ruolo <em>Lettore dei dati del BLOB di archiviazione</em>.</li>
        <li>In <strong>Assegna accesso a</strong>, scegliere <strong>Identità gestita</strong>, <strong>+ Seleziona membri</strong> e selezionare <strong>Tutte le identità gestite assegnate dal sistema</strong>.</li>
        <li>Rivedere e assegnare per salvare le nuove impostazioni e ripetere il passaggio precedente.</li>
    </ul>
    </details>

    - **Caricare il file**: selezionare il file JSONL scaricato nel passaggio precedente.
    - **Dati di convalida**: nessuno
    - **Parametri attività**: *mantenere le impostazioni predefinite*
1. L'ottimizzazione inizierà e potrebbe richiedere del tempo per il completamento.

> **Nota**: l'ottimizzazione e la distribuzione possono richiedere del tempo, quindi potrebbe essere necessario eseguire periodicamente il controllo. È già possibile continuare con il passaggio successivo durante l'attesa.

## Chat con un modello di base

Mentre si attende il completamento del processo di ottimizzazione, è possibile chattare con un modello GPT 3.5 di base per valutare le prestazioni.

1. Passare alla pagina **Distribuzioni** nella sezione **Componenti** , usando il menu a sinistra.
1. Selezionare il pulsante **+ Distribuisci modello** e selezionare l'opzione **Distribuisci modello di base**.
1. Distribuire un modello `gpt-35-turbo`, che corrisponde allo stesso tipo di modello usato durante l'ottimizzazione.
1. Al termine della distribuzione, passare alla pagina **Chat** nella sezione **Playground progetto**.
1. Selezionare il modello di base `gpt-35-model` distribuito nella distribuzione di installazione.
1. Nella finestra della chat immettere la query `What can you do?` e visualizzare la risposta.
    Le risposte sono molto generiche. Si ricorda che l'obiettivo è creare un'applicazione di chat che susciti il desiderio di viaggiare.
1. Aggiornare il messaggio di sistema con il prompt seguente:
    ```md
    You are an AI assistant that helps people plan their holidays.
    ```
1. Selezionare **Salva**, quindi selezionare **Cancella chat** e chiedere nuovamente `What can you do?`. In risposta, l'assistente potrebbe indicare che può essere d'aiuto per la prenotazione di voli, hotel e auto a noleggio per un viaggio. Si intende evitare questo comportamento.
1. Aggiornare di nuovo il messaggio di sistema con un nuovo prompt:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Selezionare **Salva** e **Cancella chat**.
1. Continuare a testare l'applicazione di chat per verificare che non fornisca informazioni non aggiornate nei dati recuperati Ad esempio, porre le domande seguenti ed esplorare le risposte del modello:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

    Il modello può fornire un elenco di hotel, anche qualora sia stato richiesto di non fornire suggerimenti sugli hotel. Questo è un esempio di comportamento incoerente. Si vedrà ora se il modello ottimizzato offre prestazioni migliori in questi casi.

1. Passare alla pagina **Ottimizzazione** in **Strumenti** per trovare il processo di ottimizzazione e il relativo stato. Se è ancora in esecuzione, è possibile scegliere di continuare a valutare manualmente il modello di base distribuito. Se l'operazione è stata completata, è possibile continuare con la sezione successiva.

## Distribuire il modello ottimizzato

Al termine dell'ottimizzazione, è possibile distribuire il modello ottimizzato.

1. Selezionare il modello ottimizzato. Selezionare la scheda **Metriche** ed esplorare le metriche ottimizzate.
1. Distribuire il modello ottimizzato con le configurazioni seguenti:
    - **Nome distribuzione**: *un nome univoco per il modello, è possibile usare il valore predefinito*
    - **Tipo di distribuzione**: Standard
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: Predefinito
1. Attendere il completamento della distribuzione prima di poterla sottoporre a test. L'operazione potrebbe richiedere alcuni minuti.

## Test del modello ottimizzato

Dopo aver distribuito il modello ottimizzato, è possibile testare il modello come è possibile testare il modello di base distribuito.

1. Quando la distribuzione è pronta, passare al modello ottimizzato e selezionare **Apri nel playground**.
1. Aggiornare il messaggio di sistema con le istruzioni seguenti:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Testare il modello ottimizzato per valutare se ora il comportamento è più coerente. Ad esempio, porre di nuovo le domande seguenti ed esplorare le risposte del modello:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

## Eseguire la pulizia

Al termine dell’esplorazione di Studio AI della piattaforma Azure, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

- Passare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com`.
- Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
- Selezionare il gruppo di risorse creato per questo esercizio.
- Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
- Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
