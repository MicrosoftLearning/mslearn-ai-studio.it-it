---
lab:
  title: Valutare le prestazioni dell'IA generativa
  description: Informazioni su come valutare i modelli e i flussi di chat per ottimizzare le prestazioni dell'app di chat e la sua capacità di rispondere in modo appropriato.
---

# Valutare le prestazioni dell'IA generativa

In questo esercizio, verranno esaminate le valutazioni predefinite e personalizzate per valutare e confrontare le prestazioni delle applicazioni di intelligenza artificiale con il portale Azure AI Foundry.

Questo esercizio richiederà circa **30** minuti.

## Creare un hub e un progetto di intelligenza artificiale in Azure AI Foundry

Per iniziare, creare un progetto di Azure AI Foundry all'interno di un hub di Azure AI:

1. In un Web browser, aprire [https://ai.azure.com](https://ai.azure.com) e accedere usando le credenziali di Azure.
1. Selezionare la pagina **Home** e quindi **+ Nuovo progetto**.
1. Nella procedura guidata **Crea un progetto** assegnare un nome univoco al progetto, quindi selezionare **Personalizza** e configurare le impostazioni seguenti:
    - **Nome hub**: *un nome univoco*.
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *un nuovo gruppo di risorse*
    - **Località**: selezionare **Informazioni su come scegliere** e quindi selezionare **gpt-4** nella finestra Helper posizione e usare l'area consigliata\*
    - **Connettere Servizi di Azure AI o OpenAI di Azure**: (nuovo) *riempimento automatico con il nome dell'hub selezionato*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello tenant. Le aree elencate nell'helper posizione includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa. Altre informazioni sulla [disponibilità di modelli per area](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#availability)

1. Selezionare **Avanti** per esaminare la configurazione.
1. Selezionare **Crea un progetto** e attendere il completamento del processo.

## Distribuire un modello GTP

Per usare un modello linguistico nel prompt flow, è prima necessario distribuire un modello. Il portale Azure AI Foundry consente all'utente di distribuire modelli OpenAI usabili nei flussi.

1. Passare alla pagina **Modelli + endpoint** nella sezione **Asset personali** usando il menu a sinistra.
1. Selezionare il pulsante **+ Distribuisci modello** e scegliere l'opzione **Distribuisci modello di base**.
1. Creare una nuova distribuzione del modello **gpt-4** con le impostazioni seguenti selezionando **Personalizza** nella procedura guidata **Distribuisci modello**:
    - **Nome distribuzione**: *Nome univoco per la distribuzione del modello*
    - **Tipo di distribuzione**: Standard
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Risorsa di intelligenza artificiale**: *selezionare la risorsa creata in precedenza*
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: predefinitoV2
    - **Abilitare la quota dinamica**: disabilitato

    > **Nota**: se la posizione corrente delle risorse di intelligenza artificiale non dispone di una quota disponibile per il modello che si vuole distribuire, verrà chiesto di scegliere una posizione diversa in cui verrà creata e connessa al progetto una nuova risorsa IA.

1. Attendere la distribuzione del modello. Quando la distribuzione è pronta, selezionare **Apri nel playground**.
1. Nella casella di testo **Fornisci istruzioni e contesto al modello** modificare il contenuto nel modo seguente:

   ```
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Fare clic su **Applica modifiche**.
1. Nella finestra (cronologia) della chat, immettere la query: `What can you do?` per verificare che il modello linguistico si comporti come previsto.

Dopo aver distribuito un modello con un messaggio di sistema aggiornato, è possibile valutare il modello.

## Valutare manualmente un modello linguistico nel portale Azure AI Foundry

È possibile esaminare manualmente le risposte del modello in base ai dati dei test. L'esame manuale consente di testare input diversi uno alla volta per valutare se il modello funziona come previsto.

1. Nel **playground della chat** selezionare l'elenco a discesa **Valuta** nella barra superiore e selezionare **Valutazione manuale**.
1. Modificare il **Messaggio di sistema** con lo stesso messaggio usato in precedenza (incluso di nuovo qui):

   ```
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Nella sezione **Risultato della valutazione manuale** si aggiungeranno cinque input dei quali si esaminerà l'output. Immettere le cinque domande seguenti come cinque **input** separati:

   `Can you provide a list of the top-rated budget hotels in Rome?`

   `I'm looking for a vegan-friendly restaurant in New York City. Can you help?`

   `Can you suggest a 7-day itinerary for a family vacation in Orlando, Florida?`

   `Can you help me plan a surprise honeymoon trip to the Maldives?`

   `Are there any guided tours available for the Great Wall of China?`

1. Selezionare **Esegui** dalla barra superiore per generare output per tutte le domande aggiunte come input.
1. È ora possibile esaminare manualmente gli output per ogni domanda selezionando l'icona del pollice in su o del pollice in giù in basso a destra di una risposta. Valuta ogni risposta, assicurandoti di includere almeno una risposta con un pollice su e un pollice giù nelle tue valutazioni.
1. Selezionare **Salva risultati** dalla barra in alto. Immettere `manual_evaluation_results` come nome per i risultati.
1. Usando il menu a sinistra, passare a **Valutazione**.
1. Selezionare la scheda **Valutazioni manuali** per trovare le valutazioni manuali appena salvate. Si noti che è possibile esplorare le valutazioni manuali create in precedenza, continuare dal punto in cui si era arrivati e salvare le valutazioni aggiornate.

## Valutare l'app di chat con le metriche predefinite

Dopo aver creato un'applicazione di chat con un prompt flow, è possibile valutare il flusso eseguendo un batch e valutando le prestazioni del flusso con le metriche predefinite.

![Diagramma della costruzione del set di dati di input per la valutazione.](./media/diagram-dataset-evaluation.png)

Per valutare un flusso di chat, le query utente e le risposte di chat vengono fornite come input per una valutazione.

Per ridurre i tempi, è stato creato un set di dati di output batch che contiene i risultati di più input elaborati da un prompt flow. Ciascun risultato viene memorizzato nel set di dati che verrà valutato nel passaggio successivo.

1. Selezionare la scheda **Valutazioni automatiche** e creare una **Nuova valutazione** con le impostazioni seguenti: <details>  
      <summary><b>Suggerimento per la risoluzione dei problemi</b>: errore di autorizzazioni</summary>
        <p>Se viene visualizzato un errore di autorizzazioni quando si crea un nuovo prompt flow, provare a risolvere i problemi nel seguente modo:</p>
        <ul>
          <li>Nel portale di Azure, selezionare la risorsa Servizi di intelligenza artificiale.</li>
          <li>Nella scheda Identità, in Gestione risorse, confermare che si tratti dell'identità gestita assegnata dal sistema.</li>
          <li>Passare all'account di archiviazione associato. Nella pagina IAM, aggiungere l'assegnazione di ruolo <em>Lettore dei dati del BLOB di archiviazione</em>.</li>
          <li>In <strong>Assegna accesso a</strong>, scegliere <strong>Identità gestita</strong>, <strong>+ Seleziona membri</strong> e selezionare <strong>Tutte le identità gestite assegnate dal sistema</strong>.</li>
          <li>Rivedere e assegnare per salvare le nuove impostazioni e ripetere il passaggio precedente.</li>
        </ul>
    </details>

    - **Cosa si vuole valutare?**: i set di dati
    - **Nome per la valutazione**: *immettere un nome univoco*
    - Selezionare **Avanti**.
    - **Selezionare i dati da valutare**: aggiungere il set di dati
        - Scaricare il [set di dati di convalida](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl) in `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl`, salvarlo come file JSONL e caricarlo nell'interfaccia utente.

    > **Nota**: per impostazione predefinita, il dispositivo potrebbe salvare il file come file .txt. Selezionare tutti i file e rimuovere il suffisso .txt per assicurarsi di salvare il file come JSONL.

    - Selezionare **Avanti**.
    - **Selezionare le metriche**: coerenza, scorrevolezza
    - **Connessione**: *connessione ai servizi di intelligenza artificiale*
    - **Nome/Modello di distribuzione**: *modello GPT-4 distribuito*
    - **query**: selezionare la **query** come origine dati
    - **risposta**: selezionare la **risposta** come origine dati
      
1. Selezionare **Avanti**, quindi esaminare i dati e **inviare** la nuova valutazione.
1. Attendere il completamento delle valutazioni, potrebbe essere necessario aggiornare.
1. Selezionare l'esecuzione della valutazione appena creata.
1. Esplorare la **dashboard delle metriche** e il **risultato dettagliato delle metriche**.

## Eliminare le risorse di Azure

Al termine dell'esplorazione di Azure AI Foundry, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

- Passare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com`.
- Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
- Selezionare il gruppo di risorse creato per questo esercizio.
- Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
- Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
