---
lab:
  title: Creare un copilota personalizzato che usa dati propri
---

# Creare un copilota personalizzato che usa dati propri

Generazione aumentata di recupero (RAG) è una tecnica usata per creare applicazioni che integrano dati da origini dati personalizzate in un prompt di un modello di intelligenza artificiale generativa. RAG è un modello comunemente usato per lo sviluppo di *copiloti* personalizzati: applicazioni basate su chat che usano un modello linguistico per interpretare gli input e generare risposte appropriate.

In questo esercizio si userà Studio AI della piattaforma Azure per integrare dati personalizzati in un prompt flow dell'intelligenza artificiale generativa.

Questo esercizio richiede circa **45** minuti.

## Creare una risorsa di Azure AI Search

La soluzione copilota integra dati personalizzati in un prompt flow. Per supportare questa integrazione, è necessaria una risorsa di Azure AI Search con cui indicizzare i dati.

1. In un Web browser, aprire il [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com` e accedere usando le credenziali di Azure.
1. Nella pagina iniziale, selezionare **+ Crea una risorsa** e cercare `Azure AI Search`. Quindi creare una nuova risorsa di Azure AI Search con le impostazioni seguenti:

    - **Sottoscrizione**: *selezionare la sottoscrizione di Azure*
    - **Gruppo di risorse**: *Selezionare o creare un gruppo di risorse*
    - **Nome del servizio**: *immettere un nome univoco*
    - **Ubicazione**: *effettuare una scelta **casuale** da una delle aree seguenti*\*
        - Australia orientale
        - Canada orientale
        - Stati Uniti orientali
        - Stati Uniti orientali 2
        - Francia centrale
        - Giappone orientale
        - Stati Uniti centro-settentrionali
        - Svezia centrale
        - Svizzera 
    - **Piano tariffario**: Standard

    > \* Successivamente si creerà un hub di Azure per intelligenza artificiale (che include un servizio OpenAI di Azure) nella stessa area della risorsa di Azure AI Search. Le risorse OpenAI di Azure sono vincolate dalle quote a livello di area. Le aree elencate includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota negli scenari in cui si condivide un tenant con altri utenti. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un altro hub di Azure per intelligenza artificiale in un'area diversa.

1. Attendere il completamento della distribuzione a opera della risorsa di Azure AI Search.

## Creare un progetto di Intelligenza artificiale di Azure

A questo punto si è pronti per creare un progetto di Studio AI della piattaforma Azure e le risorse di Azure per intelligenza artificiale per supportarlo.

1. In un Web browser aprire [Studio AI della piattaforma Azure](https://ai.azure.com) all'indirizzo `https://ai.azure.com` e accedere usando le credenziali di Azure.
1. Nella pagina **Home** di Studio AI della piattaforma Azure selezionare **+ Nuovo progetto**. Quindi, nella creazione guidata **Crea un progetto**, creare un progetto con le impostazioni seguenti:

    - **Nome progetto**: *nome univoco per il progetto*
    - **Hub**: *creare una nuova risorsa con le impostazioni seguenti:*

        - **Nome hub**: *un nome univoco*.
        - **Sottoscrizione di Azure**: *la sottoscrizione di Azure usata*
        - **Gruppo di risorse**: *selezionare il gruppo di risorse contenente la risorsa di Azure AI Search*
        - **Posizione**: *la stessa posizione della risorsa di Azure AI Search*
        - **OpenAI di Azure**: (nuovo) *riempimento automatico con il nome dell'hub selezionato*
        - **Azure AI Search**: *scegliere la risorsa di Azure AI Search*.

1. Attendere la creazione del progetto.

## Distribuire i modelli

Per implementare la soluzione sono necessari due modelli:

- Un modello *di incorporamento* per vettorizzare i dati di testo per un'indicizzazione e un'elaborazione efficienti.
- Modello che può generare risposte in linguaggio naturale alle domande in base ai dati.

1. In Studio AI della piattaforma Azure, nel progetto, nel riquadro di spostamento a sinistra, in **Componenti** selezionare la **pagina Distribuzioni**.
1. Creare una nuova distribuzione del modello **text-embedding-ada-002** con le impostazioni seguenti:

    - **Nome distribuzione**: `text-embedding-ada-002`
    - **Versione del modello**: *predefinita*
    - **Opzioni avanzate**:
        - **Filtro contenuto**: *Predefinito*
        - **Limite di velocità dei token al minuto**: `5K`
1. Ripetere i passaggi precedenti per distribuire un modello **gpt-35-turbo-16k** con il nome della distribuzione `gpt-35-turbo-16k`.

    > **Nota**: la riduzione dei token al minuto (TPM) consente di evitare di usare eccessivamente la quota disponibile nella sottoscrizione in uso. 5.000 TPM sono sufficienti per i dati usati in questo esercizio.

## Aggiungere dati al progetto

I dati per il copilota sono costituiti da una serie di brochure di viaggio in formato PDF dell'agenzia di viaggi fittizia *Margie's Travel*. Aggiungerli al progetto.

1. Scaricare [l'archivio compresso di brochure](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) da `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` ed estrarlo in una cartella denominata **brochure** nel file system locale.
1. In Studio AI della piattaforma Azure, nel progetto, nel riquadro di spostamento a sinistra, in **Componenti** selezionare la pagina **Dati**.
1. Selezionare **+ Nuovi dati**.
1. Nella procedura guidata **Aggiungi dati** espandere il menu a discesa per selezionare **Carica file/cartelle**.
1. Selezionare **Carica cartella** e segliere la cartella ** brochure**.
1. Impostare il nome dei dati su `brochures`.
1. Attendere il caricamento della cartella e notare che contiene diversi file .pdf.

## Creare un indice per i dati

Dopo aver aggiunto un'origine dati al progetto, è possibile usarla per creare un indice nella risorsa di Azure AI Search.

1. In Studio AI della piattaforma Azure, nel progetto, nel riquadro di spostamento a sinistra, in **Componenti**, selezionare la pagina **Indici**.
1. Aggiungere un nuovo indice con le impostazioni seguenti:
    - **Dati di origine**:
        - **Origine dati**: dati in Studio AI della piattaforma Azure
            - *Selezionare l'origine dati delle ** brochure***
    - **Impostazioni dell'indice**:
        - **Selezionare il servizio Azure AI Search**: *selezionare la connessione tra **AzureAISearch** e la risorsa di Azure AI Search in uso*
        - **Nome dell'indice**: `brochures-index`
        - **Macchina virtuale**: selezione automatica
    - **Ricerca impostazioni**:
        - **Impostazioni del vettore**: aggiungere la ricerca vettoriale a questa risorsa di ricerca
        - **Selezionare un modello di incorporamento**: *selezionare la risorsa OpenAI di Azure predefinita per l'hub.*
        
1. Attendere il completamento del processo di indicizzazione, che può richiedere alcuni minuti. L'operazione di creazione dell'indice è costituita dai processi seguenti:

    - Spezzare, suddividere e incorporare i token di testo nei dati delle brochure.
    - Creare l'indice di Azure AI Search.
    - Registrare la risorsa dell'indice.

## Testare l'indice

Prima di usare l'indice in un prompt flow basato su RAG, verificare che possa essere usato per influire sulle risposte di intelligenza artificiale generativa.

1. Nel riquadro di spostamento a sinistra, in **Playground progetto**, selezionare la pagina **Chat**.
1. In tale pagina, nel pannello Opzioni, verificare che sia selezionata la distribuzione del modello **gpt-35-turbo-16k**. Quindi, nel pannello principale della sessione di chat inviare il prompt `Where can I stay in New York?`
1. Esaminare la risposta, che deve essere una risposta generica dal modello senza dati dall'indice.
1. Nel pannello di installazione selezionare la scheda **Aggiungi dati**, quindi aggiungere l'indice di progetto **brochures-index** e selezionare il tipo di ricerca **ibrido (vettore + parola chiave)**.

   > **Nota**: alcuni utenti potrebbero riscontrare che gli indici appena creati non sono immediatamente disponibili. L'aggiornamento del browser in genere è utile, ma se si verifica ancora il problema per in quale non è possibile trovare l'indice, potrebbe essere necessario attendere fino a quando l'indice non viene riconosciuto.
   
1. Dopo aver aggiunto l'indice e aver riavviato la sessione di chat, inviare nuovamente il prompt `Where can I stay in New York?`
1. Esaminare la risposta, che deve essere basata sui dati dell'indice.

## Usare l'indice di un prompt flow

L'indice vettoriale è stato salvato nel progetto Studio AI della piattaforma Azure, consentendone il facile utilizzo in un flusso immediato.

1. In Studio AI della piattaforma Azure, nel progetto, nel pannello di navigazione a sinistra, in **Strumenti**, selezionare la pagina **Flusso immediato**.
1. Creare un nuovo flusso di richiesta clonando l'esempio **di domande e risposte multiple sui dati del profilo** nella raccolta. Salvare il clone di questo esempio in una cartella denominata `brochure-flow`.
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

1. Quando si apre la pagina della finestra di progettazione del flusso immediato, esaminare il **flusso della brochure**. Il grafico dovrebbe avere l'aspetto mostrato nell'immagine seguente:

    ![Screenshot di un grafico di flusso immediato](./media/chat-flow.png)

    L'esempio di flusso immediato usato implementa la logica di richiesta per un'applicazione chat in cui l'utente può inviare in modo iterativo l'input di testo all'interfaccia della chat. La cronologia conversazionale viene mantenuta e inclusa nel contesto per ogni iterazione. Il flusso immediato orchestra una sequenza di *strumenti* per:

    - Aggiungere la cronologia all'input della chat per definire un prompt sotto forma di domanda contestualizzata.
    - Recuperare il contesto usando l'indice e un tipo di query di propria scelta in base alla domanda.
    - Generare il contesto del prompt usando i dati recuperati dall'indice per aumentare la domanda.
    - Creare varianti di prompt aggiungendo un messaggio di sistema e strutturando la cronologia della chat.
    - Inviare il prompt a un modello linguistico per generare una risposta in un linguaggio naturale.

1. Usare il pulsante **Avviare sessione di calcolo** per avviare il calcolo di runtime per il flusso.

    Attendere l'avvio del runtime. In questo modo viene fornito un contesto di calcolo per il prompt flow. Durante l'attesa, nella scheda **Flusso** esaminare le sezioni relative agli strumenti nel flusso.

1. Nella sezione **Input** verificare che gli input includano:
    - **chat_history**
    - **chat_input**

    La cronologia predefinita della chat in questo esempio include alcune conversazioni sull'intelligenza artificiale.

1. Nella sezione **Output** verificare che l'output includa:

    - **chat_output** con valore ${chat_with_context.output}

1. Nella sezione **modify_query_with_history** selezionare le impostazioni seguenti (lasciando le altre invariate):

    - **Connessione**: *la risorsa OpenAI di Azure predefinita per l'hub di intelligenza artificiale*
    - **API**: chat
    - **deployment_name**: gpt-35-turbo-16k
    - **response_format**: {"type":"text"}

1. Nella sezione **ricerca** impostare i valori dei parametri seguenti:

    - **** mlindex_content: *selezionare il campo vuoto per aprire il riquadro Genera*
        - **index_type**: indice registrato
        - **mlindex_asset_id**: brochures-index:1
    - **queries**: ${modify_query_with_history.output}
    - **query_type**: ibrido (vettore + parola chiave)
    - **top_k**: 2

1. Nella sezione **generate_prompt_context** esaminare lo script Python e assicurarsi che gli **input** per questo strumento includano il parametro seguente:

    - **search_result***(oggetto)*: ${lookup.output}

1. Nella sezione **Prompt_variants** esaminare lo script Python e assicurarsi che gli **input** per questo strumento includano i parametri seguenti:

    - **contexts***(stringa)*: ${generate_prompt_context.output}
    - **chat_history***(stringa)*: ${inputs.chat_history}
    - **chat_input***(stringa)*: ${inputs.chat_input}

1. Nella sezione **chat_with_context** selezionare le impostazioni seguenti (lasciando le altre invariate):

    - **Connessione**: Default_AzureOpenAI
    - **API**: chat
    - **deployment_name**: gpt-35-turbo-16k
    - **response_format**: {"type":"text"}

    Assicurarsi quindi che gli **input** per questo strumento includano i parametri seguenti:
    - **prompt_text***(stringa)*: ${Prompt_variants.output}

1. Sulla barra degli strumenti usare il pulsante **Salva** per salvare le modifiche apportate agli strumenti nel prompt flow.
1. Nella barra degli strumenti, selezionare **Chat**. Viene aperto un riquadro della chat con la cronologia della conversazione di esempio e l'input già compilato in base ai valori di esempio, che possono essere tuttavia ignorati.
1. Nel riquadro della chat sostituire l'input predefinito con la domanda `Where can I stay in London?` e inviarla.
1. Esaminare la risposta, che deve essere basata sui dati dell'indice.
1. Esaminare gli output per ogni strumento nel flusso.
1. Nel riquadro della chat immettere la domanda `What can I do there?`
1. Esaminare la risposta, che deve essere basata sui dati nell'indice e prendere in considerazione la cronologia della chat (quindi "là" significa "a Londra").
1. Esaminare gli output per ogni strumento nel flusso, osservando il modo in cui ogni strumento del flusso ha agito sui relativi input per preparare un prompt contestualizzato e ottenere una risposta appropriata.

## Distribuire il flusso

Ora che si dispone di un flusso di lavoro che usa i dati indicizzati, è possibile distribuirlo come servizio da utilizzare da parte di un'applicazione copilota.

> **Nota**: a seconda dell'area e del carico del data center, le distribuzioni possono richiedere qualche minuto. È possibile passare alla sezione di esercizio seguente durante la distribuzione o ignorare i test della distribuzione se si dispone di poco tempo.

1. Sulla barra degli strumenti, seleziona **Distribuisci**.
1. Creare una distribuzione con le impostazioni seguenti:
    - **Impostazioni di base**:
        - **Endpoint**: Nuovo
        - **Nome endpoint**: *usare il nome dell'endpoint univoco predefinito*
        - **Nome distribuzione**: *usare il nome dell'endpoint di distribuzione predefinito*
        - **Macchina virtuale**: Standard_DS3_v2
        - **Numero di istanze**: 3
        - **Raccolta di dati di inferenza**: selezionato
    - **Impostazioni avanzate**:
        - *usare le impostazioni predefinite*
1. In Studio AI della piattaforma Azure, nel progetto, nel riquadro di spostamento a sinistra, in **Componenti** selezionare la pagina **Distribuzioni**.
1. Mantenere aggiornata la visualizzazione fino a quando la distribuzione di **brochure-endpoint-1** non viene visualizzata come *completata* nell'endpoint di **brochure-endpoint** (ciò potrebbe richiedere un periodo di tempo significativo).
1. Al termine della distribuzione, selezionarla. Quindi, nella pagina **Test** immettere il prompt `What is there to do in San Francisco?` e rivedere la risposta.
1. Immettere il prompt `Where else could I go?` ed esaminare la risposta.
1. Visualizzare la pagina **Consumo** relativa all'endpoint e notare che contiene informazioni sulla connessione e codice di esempio, usabili per sviluppare un'applicazione client per l'endpoint. Questo consente di integrare la soluzione di flusso immediato in un'applicazione come copilot personalizzato.

## Esercizio 

Si è appreso come integrare i propri dati in un copilota creato con Studio AI della piattaforma Azure; si vedano ora le fasi successive.

Provare ad aggiungere una nuova origine dati tramite Studio AI della piattaforma Azure, indicizzarla e integrare i dati indicizzati in un flusso immediato. Alcuni set di dati che è possibile provare sono:

- Una raccolta di articoli (ricerca) sul computer.
- Una serie di presentazioni delle conferenze precedenti.
- Uno dei set di dati disponibili nel repository di [dati di esempio di Ricerca di Azure](https://github.com/Azure-Samples/azure-search-sample-data).

Sfruttare tutte le risorse disponibili per creare l'origine dati e integrarla nel prompt flow. Provare il nuovo prompt flow e inviare richieste che potrebbero trovare risposta solo nel set di dati scelto.

## Eseguire la pulizia

Per evitare i costi e l'utilizzo delle risorse di Azure non necessari, è necessario rimuovere le risorse distribuite in questo esercizio.

1. Se è stata completata l'esplorazione di Studio AI della piattaforma Azure, tornare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com` e accedere usando le credenziali di Azure, se necessario. Eliminare quindi le risorse nel gruppo di risorse in cui è stato effettuato il provisioning delle risorse di Ricerca di Azure per intelligenza artificiale e di Azure per intelligenza artificiale.
