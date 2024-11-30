---
lab:
  title: Valutare le app del linguaggio in Studio AI della piattaforma Azure
---

# Valutare le app del linguaggio in Studio AI della piattaforma Azure

In questo esercizio, verranno esaminate le valutazioni predefinite e personalizzate per valutare e confrontare le prestazioni delle applicazioni di intelligenza artificiale con Studio AI della piattaforma Azure.

> Per completare questo esercizio, la sottoscrizione di Azure dovrà disporre dell'approvazione per l'accesso al servizio OpenAI di Azure. Compilare il [modulo di registrazione](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) per richiedere l'accesso ai modelli OpenAI di Azure.

Per compilare un copilota con prompt flow, è necessario:

- Creare un hub di intelligenza artificiale e un progetto all'interno di Studio AI della piattaforma Azure.
- Distribuire un modello GPT.
- Valutare un set di dati di test con metriche predefinite.
- Definire una metrica di valutazione personalizzata ed eseguirla in base al set di dati di test.

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
        - **Posizione**: *effettuare una scelta **casuale** da una delle aree seguenti*\*
        - Australia orientale
        - Canada orientale
        - Stati Uniti orientali
        - Stati Uniti orientali 2
        - Francia centrale
        - Giappone orientale
        - Stati Uniti centro-settentrionali
        - Svezia centrale
        - Svizzera settentrionale
        - Regno Unito meridionale
    - **Connettere Servizi di Azure AI o OpenAI di Azure**: *creare una nuova connessione*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello tenant. Le aree elencate includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa. Altre informazioni sulla [disponibilità di modelli per area](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Esaminare la configurazione e creare il progetto.
1. Attendere 5-10 minuti per la creazione del progetto.

## Distribuire un modello GPT

Per usare un modello linguistico nel prompt flow, è prima necessario distribuire un modello. Studio AI della piattaforma Azure consente all'utente di distribuire modelli OpenAI utilizzabili nei flussi.

1. Nel riquadro di spostamento a sinistra, in **Componenti**, selezionare la pagina **Distribuzioni**.
1. Creare una nuova distribuzione del modello **gpt-35-turbo** con le impostazioni seguenti:
    - **Nome distribuzione**: *Nome univoco per la distribuzione del modello*
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Tipo di distribuzione**: Standard
    - **Risorsa OpenAI di Azure connessa**: *selezionare la connessione predefinita*
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: Predefinito
1. Attendere la distribuzione del modello. Quando la distribuzione è pronta, selezionare **Apri nel playground**.
1. Modificare il **messaggio di sistema** nel modo seguente:

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
1. Nella finestra della chat immettere la query: `What can you do?` per verificare che il modello linguistico si comporti come previsto.

Dopo aver distribuito un modello con un messaggio di sistema aggiornato, è possibile valutarlo.

## Valutare un modello linguistico in Studio AI della piattaforma Azure

È possibile esaminare manualmente le risposte del modello in base ai dati di test. La revisione manuale consente di testare input diversi uno alla volta per valutare se il modello viene eseguito come previsto.

1. Nel **playground di chat**, selezionare **Valuta** nella barra superiore.
1. Verrà visualizzata una nuova finestra con il messaggio di sistema precedente già popolato e il modello distribuito selezionato.
1. Nella sezione relativa al **risultato della valutazione manuale** si aggiungeranno cinque input per i quali si esaminerà l'output. Immettere le cinque domande seguenti come cinque **input** separati:

   `Can you provide a list of the top-rated budget hotels in Rome?`

   `I'm looking for a vegan-friendly restaurant in New York City. Can you help?`

   `Can you suggest a 7-day itinerary for a family vacation in Orlando, Florida?`

   `Can you help me plan a surprise honeymoon trip to the Maldives?`

   `Are there any guided tours available for the Great Wall of China?`

1. Selezionare **Esegui** dalla barra superiore per generare output per tutte le domande aggiunte come input.
1. È ora possibile esaminare manualmente gli output per ogni domanda selezionando l'icona del pollice in su o del pollice in giù in basso a destra di una risposta. Valuta ogni risposta, assicurandoti di includere almeno una risposta con un pollice su e un pollice giù nelle tue valutazioni.
1. Selezionare **Salva risultati** dalla barra in alto. Immettere `manual_evaluation_results` come nome per i risultati.
1. Usando il menu a sinistra, passare a **Valutazioni**.
1. Selezionare la scheda **Valutazioni manuali** per trovare le valutazioni manuali appena salvate. Si noti che è possibile esplorare le valutazioni manuali create in precedenza, continuare dal punto in cui si era arrivati e salvare le valutazioni aggiornate.
1. Selezionare la scheda **Valutazioni delle metriche** e creare una nuova valutazione con le impostazioni seguenti:
    - **Nome valutazione**: *immettere un nome univoco*
    - **Quale tipo di scenario si sta valutando?**: domanda e risposta senza contesto
    - **Selezionare i dati da valutare**: aggiungere il set di dati
        - Scaricare il file JSONL https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl e caricarlo nell'interfaccia utente.
    - **Selezionare le metriche**: coerenza, scorrevolezza
    - **Connessione**: *connessione ai servizi di intelligenza artificiale*
    - **Nome/Modello di distribuzione**: *modello GPT-3.5 distribuito*
1. Attendere il completamento delle valutazioni, potrebbe essere necessario aggiornare.
1. Selezionare l'esecuzione della valutazione appena creata.
1. Esplorare la **dashboard delle metriche** e il **risultato dettagliato delle metriche**.

## Eliminare le risorse di Azure

Quando si finisce di esplorare Studio AI della piattaforma Azure, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

- Passare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com`.
- Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
- Selezionare il gruppo di risorse creato per questo esercizio.
- Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
- Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
