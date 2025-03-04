---
lab:
  title: Creare un'app di IA generativa che usa dati propri
  description: Informazioni su come usare il modello RAG (Retrieval Augmented Generation) per sviluppare un'app di chat in grado di contestualizzare i prompt mediante l'integrazione di propri dati.
---

# Creare un'app di IA generativa che usa dati propri

Generazione ottimizzata per il recupero (RAG) è una tecnica usata per creare applicazioni che integrano dati da origini dati personalizzate in una richiesta di un modello di intelligenza artificiale generativa. RAG è un modello comunemente usato per lo sviluppo di app di IA generativa: applicazioni basate su chat che usano un modello linguistico per interpretare gli input e generare risposte appropriate.

In questo esercizio si userà il portale di Azure AI Foundry per integrare dati personalizzati in un flusso immediato di intelligenza artificiale generativa.

Questo esercizio richiede circa **45** minuti.

## Creare un progetto Fonderia Azure AI

Per iniziare, creare un progetto Azure AI Foundry e le risorse del servizio che deve supportare usando i propri dati, inclusa una risorsa di Azure AI Search.

1. In un Web browser, aprire il [Portale Fonderia Azure AI](https://ai.azure.com) su `https://ai.azure.com` e accedere usando le credenziali di Azure. Chiudere i suggerimenti o i riquadri di avvio rapido aperti la prima volta che si accede e, se necessario, usare il logo **Fonderia Azure AI** in alto a sinistra per passare alla home page, simile all'immagine seguente:

    ![Screenshot del portale di Azure AI Foundry.](./media/ai-foundry-home.png)

1. Nella home page, selezionare **+ Crea progetto**.
1. Nella procedura guidata **Crea un progetto** immettere un nome di progetto appropriato per (ad esempio, `my-ai-project`) e quindi rivedere le risorse di Azure che verranno create automaticamente per supportare il progetto.
1. Selezionare **Personalizza** e specificare le impostazioni seguenti per l'hub:
    - **Nome hub**: *un nome univoco, ad esempio `my-ai-hub`*
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare un nuovo gruppo di risorse con un nome univoco (ad esempio, `my-ai-resources`) o selezionarne uno esistente*
    - **Località**: selezionare **Informazioni su come scegliere** e quindi selezionare sia **gpt-4** sia **text-embedding-ada-002** nella finestra Helper posizione e usare l'area consigliata\*
    - **Connettere i Servizi di Azure AI o Azure OpenAI**: *creare una nuova risorsa di Servizi di intelligenza artificiale con un nome appropriato (ad esempio, `my-ai-services`) o usarne uno esistente*
    - **Connettere Azure AI Search**: *creare una nuova risorsa di Azure AI Search con un nome univoco*

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello tenant. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa.

1. Selezionare **Avanti** per esaminare la configurazione. Quindi selezionare **Crea** e attendere il completamento del processo.
1. Quando viene creato il progetto, chiudere tutti i suggerimenti visualizzati e rivedere la pagina **Panoramica** del progetto nel portale Fonderia Azure AI, che dovrebbe essere simile all'immagine seguente:

    ![Screenshot dei dettagli di un progetto di Azure AI nel portale Fonderia di Azure AI.](./media/ai-foundry-project.png)
   
## Distribuire i modelli

Per implementare la soluzione sono necessari due modelli:

- Un modello di *incorporamento* per vettorizzare i dati di testo per un'indicizzazione e un'elaborazione efficienti.
- Modello che può generare risposte in linguaggio naturale alle domande in base ai dati.

1. Nel portale di Azure AI Foundry, nel progetto in uso, nel riquadro di spostamento a sinistra, in **Asset personali**, selezionare la pagina **Modelli + endpoint**.
1. Creare una nuova distribuzione del modello **text-embedding-ada-002** con le impostazioni seguenti, selezionando **Personalizza** nella procedura guidata Distribuisci modello:

    - **Nome distribuzione**: `text-embedding-ada-002`
    - **Tipo di distribuzione**: Standard
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Risorsa di intelligenza artificiale**: *selezionare la risorsa creata in precedenza*
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: predefinitoV2
    - **Abilitare la quota dinamica**: disabilitato

    > **Nota**: se la posizione corrente delle risorse di intelligenza artificiale non dispone di una quota disponibile per il modello che si vuole distribuire, verrà chiesto di scegliere una posizione diversa in cui verrà creata e connessa al progetto una nuova risorsa IA.

1. Ripetere i passaggi precedenti per distribuire un modello **gpt-4** con il nome della distribuzione `gpt-4`.

    > **Nota**: la riduzione dei token al minuto (TPM) consente di evitare di usare eccessivamente la quota disponibile nella sottoscrizione in uso. 5.000 TPM è sufficiente per i dati usati in questo esercizio.

## Aggiungere dati al progetto

I dati per il copilota sono costituiti da una serie di brochure di viaggio in formato PDF dell'agenzia di viaggi fittizia *Margie's Travel*. Aggiungerli al progetto.

1. Scaricare [l'archivio compresso di brochure](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) da `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` ed estrarlo in una cartella denominata **brochure** nel file system locale.
1. Nel portale Azure AI Foundry, nel progetto, nel riquadro di spostamento a sinistra, in **Asset personali**, selezionare la pagina **Dati + indici**.
1. Selezionare **+ Nuovi dati**.
1. Nella procedura guidata **Aggiungi dati** espandere il menu a discesa per selezionare **Carica file/cartelle**.
1. Selezionare **Carica cartella** e selezionare la cartella **brochure**.
1. Selezionare **Avanti** e impostare il nome dei dati su `brochures`.
1. Attendere il caricamento della cartella e notare che contiene diversi file .pdf.

## Creare un indice per i dati

Dopo aver aggiunto un'origine dati al progetto, è possibile usarla per creare un indice nella risorsa di Azure AI Search.

1. Nel portale Azure AI Foundry, nel progetto, nel riquadro di spostamento a sinistra, in **Asset personali**, selezionare la pagina **Dati + indici**.
1. Nella scheda **Indici**, aggiungere un nuovo indice con le impostazioni seguenti:
    - **Posizione di origine**:
        - **Origine dati**: dati nel portale di Azure AI Foundry
            - *Selezionare l'origine dati delle **brochure***
    - **Configurazione dell'indice**:
        - **Selezionare il servizio Azure AI Search**: *selezionare la connessione tra **AzureAISearch** e la risorsa di Azure AI Search in uso*
        - **Indice vettoriale**: `brochures-index`
        - **Macchina virtuale**: selezione automatica
    - **Ricerca impostazioni**:
        - **Impostazioni del vettore**: aggiungere la ricerca vettoriale a questa risorsa di ricerca
        - **Connessione OpenAI di Azure**: *selezionare la risorsa OpenAI di Azure predefinita per l'hub.*
        
1. Attendere il completamento del processo di indicizzazione, che può richiedere alcuni minuti. L'operazione di creazione dell'indice è costituita dai processi seguenti:

    - Spezzare, suddividere e incorporare i token di testo nei dati delle brochure.
    - Creare l'indice di Azure AI Search.
    - Registrare la risorsa dell'indice.

## Testare l'indice

Prima di usare l'indice in un flusso immediato basato su RAG, verificare che possa essere usato per influire sulle risposte di intelligenza artificiale generativa.

1. Selezionare la pagina **Playground** nel pannello di navigazione a sinistra.
1. In tale pagina, nel pannello di installazione, verificare che sia selezionata la distribuzione modello **gpt-4**. Quindi, nel pannello principale della sessione di chat inviare la richiesta `Where can I stay in New York?`
1. Esaminare la risposta, che deve essere una risposta generica dal modello senza dati dall'indice.
1. Nel pannello di installazione espandere il campo **Aggiungi dati**, quindi aggiungere l'indice di progetto **brochures-index** e selezionare il tipo di ricerca **ibrido (vettore + parola chiave)**.

   > **Nota**: alcuni utenti trovano immediatamente gli indici appena creati non disponibili. L'aggiornamento del browser in genere è utile, ma se si verifica ancora il problema per cui non è possibile trovare l'indice, potrebbe essere necessario attendere fino a quando l'indice non viene riconosciuto.

1. Dopo aver aggiunto l'indice e aver riavviato la sessione di chat, inviare di nuovo la richiesta `Where can I stay in New York?`
1. Esaminare la risposta, che deve essere basata sui dati nell'indice.

## Usare l'indice in un prompt flow

L'indice vettoriale è stato salvato nel progetto Azure AI Foundry, consentendo di usarlo facilmente in un flusso immediato.

1. Nel portale di Azure AI Foundry, all'interno del progetto, nel riquadro di spostamento a sinistra, in **Crea e personalizza**, selezionare la pagina **Pompt flow**.
1. Creare un nuovo prompt flow clonando l'esempio **Domande e risposte multiple sui dati del profilo** nella raccolta. Salvare il clone di questo esempio in una cartella denominata `brochure-flow`.
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

1. Quando si apre la pagina della finestra di progettazione del prompt flow, esaminare il **flusso della brochure**. Il relativo grafo dovrebbe avere l'aspetto dell'immagine seguente:

    ![Screenshot di un grafo del prompt flow](./media/chat-flow.png)

    Il prompt flow di esempio usato implementa la logica di richiesta per un'applicazione di chat in cui l'utente può inviare in modo iterativo l'input di testo all'interfaccia di chat. La cronologia conversazionale viene mantenuta e inclusa nel contesto per ogni iterazione. Il prompt flow orchestra una sequenza di *strumenti* per:

    - Aggiungere la cronologia all'input della chat per definire un prompt sotto forma di domanda contestualizzata.
    - Recuperare il contesto usando l'indice e un tipo di query di propria scelta in base alla domanda.
    - Generare il contesto della richiesta usando i dati recuperati dall'indice per aumentare la domanda.
    - Creare varianti di prompt aggiungendo un messaggio di sistema e strutturando la cronologia della chat.
    - Inviare la richiesta a un modello linguistico che genera una risposta in un linguaggio naturale.

1. Usare il pulsante **Avvia sessione di calcolo** per avviare il calcolo di runtime per il flusso.

    Attendere l'avvio del runtime. In questo modo viene fornito un contesto di calcolo relativo al prompt flow. Durante l'attesa, nella scheda **Flusso**, esaminare le sezioni relative agli strumenti nel flusso.

1. Nella sezione **Input**, assicurarsi che gli input includano:
    - **chat_history**
    - **chat_input**

    La cronologia di chat predefinita in questo campione include alcune conversazioni sull'intelligenza artificiale.

1. Nella sezione **Output**, assicurarsi che l'output includa:

    - **chat_output** con valore ${chat_with_context.output}

1. Nella sezione **modify_query_with_history**, selezionare le impostazioni seguenti (lasciando le altre così come sono):

    - **Connessione**: *la risorsa OpenAI di Azure predefinita per l'hub IA*
    - **API**: chat
    - **deployment_name**: gpt-4
    - **response_format**: {"type":"text"}

1. Attendere l'avvio della sessione di calcolo, quindi nella sezione di **ricerca** impostare i valori dei parametri seguenti:

    - **mlindex_content**: *selezionare il campo vuoto per aprire il riquadro Genera*
        - **index_type**: indice registrato
        - **mlindex_asset_id**: brochures-index:1
    - **query**: ${modify_query_with_history.output}
    - **query_type**: ibrido (vettore + parola chiave)
    - **top_k**: 2

1. Nella sezione **generate_prompt_context**, esaminare lo script Python e assicurarsi che gli **input** per questo strumento includano il parametro seguente:

    - **search_result** *(oggetto)*: ${lookup.output}

1. Nella sezione **Prompt_variants** esaminare lo script Python e assicurarsi che gli **input** per questo strumento includano i parametri seguenti:

    - **contexts***(stringa)*: ${generate_prompt_context.output}
    - **chat_history***(stringa)*: ${inputs.chat_history}
    - **chat_input***(stringa)*: ${inputs.chat_input}

1. Nella sezione **chat_with_context** selezionare le impostazioni seguenti (lasciando gli altri così come sono):

    - **Connessione**: *la risorsa OpenAI di Azure predefinita per l'hub IA*
    - **API**: Chat
    - **deployment_name**: gpt-4
    - **response_format**: {"type":"text"}

    Assicurarsi quindi che gli **input** per questo strumento includano i parametri seguenti:
    - **prompt_text***(stringa)*: ${Prompt_variants.output}

1. Sulla barra degli strumenti usare il pulsante **Salva** per salvare le modifiche apportate agli strumenti nel prompt flow.
1. Nella barra degli strumenti, selezionare **Chat**. Viene aperto un riquadro chat con la cronologia di conversazione di esempio e l'input già compilato in base ai valori di esempio. che possono essere tuttavia ignorati.
1. Nel riquadro della chat sostituire l'input predefinito con la domanda `Where can I stay in London?` e inviarla.
1. Esaminare la risposta, che deve essere basata sui dati nell'indice.
1. Esaminare gli output per ogni strumento nel flusso.
1. Nel riquadro della chat immettere la domanda `What can I do there?`
1. Esaminare la risposta, che deve essere basata sui dati nell'indice e prendere in considerazione la cronologia delle chat (quindi "there" è compresa come "a Londra").
1. Esaminare gli output per ogni strumento nel flusso, notando il modo in cui ogni strumento nel flusso ha eseguito i relativi input per preparare una richiesta contestualizzata e ottenere una risposta appropriata.

## Distribuire il flusso

Ora che si dispone di un flusso di lavoro che usa i dati indicizzati, è possibile distribuirlo come un servizio da utilizzare da un'applicazione copilota.

> **Nota**: a seconda dell'area e del carico del data center, le distribuzioni possono richiedere qualche minuto e talvolta generano un errore durante l'interazione con la distribuzione. È possibile passare alla sezione di richiesta di verifica di seguito durante la distribuzione o ignorare i test di distribuzione se non si dispone di molto tempo.

1. Sulla barra degli strumenti, selezionare **Distribuisci**.
1. Creare una distribuzione con le impostazioni seguenti:
    - **Impostazioni di base**:
        - **Endpoint**: Nuovo
        - **Nome endpoint**: *usare il nome dell'endpoint univoco predefinito*
        - **Nome distribuzione**: *usare il nome dell'endpoint di distribuzione predefinito*
        - **Macchina virtuale**: Standard_DS3_v2
        - **Numero di istanze**: 3
        - **Raccolta di dati di inferenza**: selezionato
    - **Impostazioni avanzate**:
        - *Usa le impostazioni predefinite*
1. Nel portale Azure AI Foundry, nel progetto, nel riquadro di navigazione a sinistra, in **Asset personali**, selezionare la pagina **Modelli + endpoint**.
1. Mantenere aggiornata la visualizzazione fino a quando la distribuzione di **brochure-endpoint-1** non viene mostrata come *completata* nell'endpoint di **brochure-endpoint** (questo potrebbe richiedere un periodo di tempo significativo).
1. Al termine della distribuzione, selezionarla. Quindi, nella sua pagina **Test**, immettere il prompt `What is there to do in San Francisco?` e rivedere la risposta.
1. Immettere il prompt `Where else could I go?` e rivedere la risposta.
1. Visualizzare la pagina **Utilizzo** per l'endpoint e osservare che contiene informazioni di connessione e codice di esempio che è possibile usare per compilare un'applicazione client per l'endpoint, che consente di integrare la soluzione prompt flow in un'applicazione come copilota personalizzato.

## Proposta 

Ora che si sono apprese le modalità di integrazione dei dati in un'app di IA generativa realizzata con il portale Azure AI Foundry, è possibile procedere.

Provare ad aggiungere una nuova origine dati tramite il portale Azure AI Foundry, indicizzarla e integrare i dati indicizzati in un prompt flow. Alcuni set di dati che è possibile provare sono:

- Una raccolta di articoli (di ricerca) presenti sul computer.
- Una serie di presentazioni delle conferenze precedenti.
- Ognuno dei set di dati disponibili nell'archivio dei [dati di esempio di Ricerca di Azure](https://github.com/Azure-Samples/azure-search-sample-data).

Attingere da tutte le risorse disponibili per creare l'origine dati e integrarla nel prompt flow. Provare il nuovo prompt flow e inviare dei prompt ai quali potrebbe rispondere solo il set di dati scelto.

## Eseguire la pulizia

Per evitare i costi e l'utilizzo delle risorse di Azure non necessari, si dovrebbero rimuovere le risorse distribuite in questo esercizio.

1. Se è stata completata l'esplorazione di Azure AI Foundry, tornare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com` e accedere usando le credenziali di Azure, se necessario. Quindi eliminare le risorse nel gruppo di risorse in cui è stato effettuato il provisioning di Azure Ai Search e delle risorse di Azure AI.
