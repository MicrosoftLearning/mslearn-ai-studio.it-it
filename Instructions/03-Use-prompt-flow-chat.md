---
lab:
  title: Usare un prompt flow per gestire la conversazione in un'app di chat
  description: Informazioni su come usare i prompt flow per gestire i dialoghi di conversazione e assicurarsi che i prompt vengano sviluppati e orchestrati per ottenere risultati ottimali.
---

# Usare un prompt flow per gestire la conversazione in un'app di chat

In questo esercizio, verrà usato il prompt flow del Portale Fonderia Azure AI per creare un'app di chat personalizzata che impiega un prompt dell'utente e la cronologia della chat come input e usa un modello GPT di Azure OpenAI per generare un output.

Questo esercizio richiederà circa **30** minuti.

## Creare un hub e un progetto di intelligenza artificiale nel portale Azure AI Foundry

Per iniziare, è necessario creare il progetto del portale Azure AI Foundry all'interno di un hub Azure AI:

1. In un Web browser, aprire [https://ai.azure.com](https://ai.azure.com) e accedere usando le credenziali di Azure.
1. Nella home page, selezionare **+ Crea progetto**.
1. Nella procedura guidata **Creare un progetto** è possibile visualizzare tutte le risorse di Azure che verranno create automaticamente con il progetto, oppure personalizzare le impostazioni seguenti selezionando **Personalizza** prima di selezionare **Crea**:

    - **Nome hub**: *un nome univoco*.
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *un nuovo gruppo di risorse*
    - **Località**: selezionare **Informazioni su come scegliere** e quindi selezionare **gpt-35-turbo** nella finestra Helper posizione e usare l'area consigliata\*
    - **Connettere Servizi di Azure AI o OpenAI di Azure**: (nuovo) *riempimento automatico con il nome dell'hub selezionato*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello tenant. Le aree elencate nell'helper posizione includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa. Altre informazioni sulla [disponibilità di modelli per area](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Se si seleziona **Personalizza**, selezionare **Avanti** ed esaminare la configurazione.
1. Selezionare **Crea** e attendere il completamento del processo.

## Distribuire un modello GTP

Per usare un modello linguistico nel prompt flow, è prima necessario distribuire un modello. Il portale Azure AI Foundry consente all'utente di distribuire modelli OpenAI usabili nei flussi.

1. Nel riquadro di spostamento a sinistra, in **Risorse personali**, selezionare la pagina **Modelli + endpoint**.
1. Creare una nuova distribuzione del modello **gpt-35-turbo** con le impostazioni seguenti:
    - **Nome distribuzione**: *Nome univoco per la distribuzione del modello*
    - **Tipo di distribuzione**: Standard
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Risorsa di intelligenza artificiale**: *selezionare la risorsa creata in precedenza*
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: predefinitoV2
    - **Abilitare la quota dinamica**: disabilitato

    > **Nota**: se la posizione corrente delle risorse di intelligenza artificiale non dispone di una quota disponibile per il modello che si vuole distribuire, verrà chiesto di scegliere una posizione diversa in cui verrà creata e connessa al progetto una nuova risorsa IA.

1. Attendere la distribuzione del modello. Quando la distribuzione è pronta, selezionare **Apri nel playground**.
1. Nella finestra della chat immettere la query `What can you do?`.

    Tenere presente che la risposta è generica perché non sono disponibili istruzioni specifiche per l'assistente. Per concentrarsi su un'attività, è possibile modificare la richiesta di sistema.

1. Modificare il messaggio **Fornisci istruzioni e contesto al modello** nel modo seguente:

   ```md
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
1. Nella finestra della chat immettere la stessa query di prima: `What can you do?` Tenere presente il cambiamento della risposta.

Ora che è stato sperimentato il messaggio di sistema per il modello GPT distribuito, è possibile personalizzare ulteriormente l'applicazione usando il prompt flow.

## Creare ed eseguire un flusso di chat nel portale Azure AI Foundry

È possibile creare un nuovo flusso da un modello o crearne uno in base alle configurazioni nel playground. Poiché si stava già sperimentando nel playground, questa opzione verrà utilizzata per creare un nuovo flusso.

<details>  
    <summary><b>Suggerimento per la risoluzione dei problemi</b>: errore di autorizzazioni</summary>
    <p>Se viene visualizzato un errore di autorizzazioni quando si crea un nuovo prompt flow, provare a risolvere i problemi nel seguente modo:</p>
    <ul>
        <li>Nel portale di Azure, selezionare la risorsa Servizi di intelligenza artificiale.</li>
        <li>Nella scheda Identità, in Gestione risorse, confermare che si tratti dell'identità gestita assegnata dal sistema.</li>
        <li>Passare all'account di archiviazione associato. Nella pagina IAM, aggiungere l'assegnazione di ruolo <em>Lettore dei dati del BLOB di archiviazione</em>.</li>
        <li>In <strong>Assegna accesso a</strong>, scegliere <strong>Identità gestita</strong>, <strong>+ Seleziona membri</strong>, selezionare <strong>Tutte le identità gestite assegnate dal sistema</strong> e selezionare la risorsa dei servizi di intelligenza artificiale di Azure.</li>
        <li>Rivedere e assegnare per salvare le nuove impostazioni e ripetere il passaggio precedente.</li>
    </ul>
</details>

1. Nel **playground della chat**, selezionare **Prompt flow** nella barra superiore.
1. Immettere `Travel-Chat` come nome cartella.

    Viene creato un flusso di chat semplice. Notare che sono presenti due input (cronologia chat e domanda dell'utente), un nodo LLM che si connetterà al modello linguistico distribuito e un output per riflettere la risposta nella chat.

    Per poter testare il flusso, è necessario effettuare un calcolo.

1. Selezionare **Avvia sessione di calcolo** dalla barra in alto.
1. L'avvio della sessione di calcolo richiederà 1-3 minuti.
1. Trovare il nodo LLM denominato **chat**. Tenere presente che il prompt include già il prompt di sistema specificato nel playground della chat.

    È comunque necessario connettere il nodo LLM al modello distribuito.

1. Nella sezione del nodo LLM, per **Connessione**, selezionare la connessione creata al momento della creazione dell'hub IA.
1. Per **API** selezionare **Chat**.
1. Per **deployment_name** selezionare il modello **gpt-35-turbo** distribuito.
1. Per **response_format**, selezionare **{"type":"text"}**.
1. Esaminare il campo della richiesta e verificare che sia simile al seguente:

   ```yml
   system:
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

   {% for item in chat_history %}
   user:
   {{item.inputs.question}}
   assistant:
   {{item.outputs.answer}}
   {% endfor %}

   user:
   {{question}}
   ```

### Testare e distribuire il flusso

Dopo aver sviluppato il flusso, è possibile usare la finestra di chat per testarlo.

1. Verificare che la sessione di calcolo sia in esecuzione.
1. Seleziona **Salva**.
1. Selezionare **Chat** per testare il flusso.
1. Immettere la query `I have one day in London, what should I do?` ed esaminare l'output.

    Quando si è soddisfatti del comportamento del flusso creato, è possibile distribuirlo.

1. Selezionare **Distribuisci** per distribuire il flusso con le impostazioni seguenti:
    - **Impostazioni di base**:
        - **Endpoint**: Nuovo
        - **Nome endpoint**: *Immettere un nome univoco*
        - **Nome distribuzione**: *Immettere un nome univoco*
        - **Macchina virtuale**: Standard_DS3_v2
        - **Numero di istanze**: 3
        - **Raccolta di dati di inferenza**: abilitata
    - **Impostazioni avanzate**:
        - *Usa le impostazioni predefinite*
1. Nel portale Azure AI Foundry, nel progetto, nel riquadro di navigazione a sinistra, in **Asset personali**, selezionare la pagina **Modelli + endpoint**.
1. Tenere presente che per impostazione predefinita sono elencate le **Distribuzioni del modello**, compresi il modello linguistico e il flusso distribuiti. Potrebbe essere necessario del tempo prima che la distribuzione venga elencata e creata correttamente.
1. Al termine della distribuzione, selezionarla. Quindi, nella sua pagina **Test**, immettere il prompt `What is there to do in San Francisco?` e rivedere la risposta.
1. Immettere il prompt `Where else could I go?` e rivedere la risposta.
1. Visualizzare la pagina **Utilizzo** per l'endpoint e osservare che contiene informazioni di connessione e codice di esempio che è possibile usare per compilare un'applicazione client per l'endpoint, che consente di integrare la soluzione prompt flow in un'applicazione come copilota personalizzato.

## Eliminare le risorse di Azure

Al termine dell'esplorazione del portale Azure AI Foundry, è necessario eliminare le risorse create per evitare costi di Azure inutili.

- Passare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com`.
- Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
- Selezionare il gruppo di risorse creato per questo esercizio.
- Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
- Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
