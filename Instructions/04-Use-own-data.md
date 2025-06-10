---
lab:
  title: Creare un'app di IA generativa che usa dati propri
  description: Informazioni su come usare il modello RAG (Retrieval Augmented Generation) per sviluppare un'app di chat in grado di contestualizzare i prompt mediante l'integrazione di propri dati.
---

# Creare un'app di IA generativa che usa dati propri

Generazione ottimizzata per il recupero (RAG) è una tecnica usata per creare applicazioni che integrano dati da origini dati personalizzate in una richiesta di un modello di intelligenza artificiale generativa. RAG è un modello comunemente usato per lo sviluppo di app di IA generativa: applicazioni basate su chat che usano un modello linguistico per interpretare gli input e generare risposte appropriate.

In questo esercizio, verrà utilizzato il portale Fonderia Azure AI per integrare dati personalizzati in una soluzione di IA generativa.

Questo esercizio richiede circa **45** minuti.

> **Nota**: questo esercizio si basa sui servizi delle versioni non definitive, che possono essere soggette a modifiche.

## Creare una risorsa di Fonderia Azure AI

Per iniziare, creare una risorsa di Fonderia Azure AI.

1. Nel web browser, aprire il [portale di Azure](https://portal.azure.com) su `https://portal.azure` e accedere usando le credenziali di Azure. Chiudere eventuali suggerimenti o riquadri di avvio rapido che vengono aperti al primo accesso.
1. Hub: creare una nuova risorsa `Azure AI Foundry` con le impostazioni seguenti:
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare o selezionare un gruppo di risorse*
    - **Nome**: *un nome valido per la risorsa di Fonderia Azure AI*
    - **Area**: scegliere tra una delle seguenti aree:
        - Stati Uniti orientali 2
        - Svezia centrale
    - **Nome progetto predefinito**: *un nome valido per il progetto*

1. Attendere che la risorsa venga creata, quindi passare alla relativa pagina nel portale di Azure.
1. Nella pagina della risorsa di Fonderia Azure AI, selezionare **Passare al portale di Fonderia Azure AI**.

## Distribuire i modelli

Per implementare la soluzione sono necessari due modelli:

- Un modello di *incorporamento* per vettorizzare i dati di testo per un'indicizzazione e un'elaborazione efficienti.
- Modello che può generare risposte in linguaggio naturale alle domande in base ai dati.

1. Nel portale di Azure AI Foundry, nel progetto in uso, nel riquadro di spostamento a sinistra, in **Asset personali**, selezionare la pagina **Modelli + endpoint**.
1. Creare una nuova distribuzione del modello **text-embedding-ada-002** con le impostazioni seguenti, selezionando **Personalizza** nella procedura guidata Distribuisci modello:

    - **Nome distribuzione**: *nome univoco per la distribuzione del modello*
    - **Tipo di distribuzione**: standard globale
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Risorsa di intelligenza artificiale connessa**: *selezionare la risorsa creata in precedenza*
    - **Token al limite di velocità al minuto (migliaia)**: 50.000 *(o il valore massimo disponibile nella sottoscrizione se inferiore a 50.000)*
    - **Filtro contenuto**: predefinitoV2

    > **Nota**: se la posizione corrente delle risorse di intelligenza artificiale non dispone di una quota disponibile per il modello che si vuole distribuire, verrà chiesto di scegliere una posizione diversa in cui verrà creata e connessa al progetto una nuova risorsa IA.

1. Tornare alla pagina **Modelli + endpoint** e ripetere i passaggi precedenti per distribuire un modello **gpt-4o** usando una distribuzione **Standard globale** della versione più recente con un limite di velocità TPM di **50.000** (o il massimo disponibile nella sottoscrizione se inferiore a 50.000).

    > **Nota**: la riduzione dei token al minuto (TPM) consente di evitare di usare eccessivamente la quota disponibile nella sottoscrizione in uso. 50.000 TPM sono sufficienti per i dati usati in questo esercizio.

## Aggiungere dati al progetto

I dati per l'app sono costituiti da una serie di brochure di viaggio in formato PDF dell'agenzia di viaggi fittizia *Margie's Travel*. Aggiungerli al progetto.

1. In una nuova scheda del browser, scaricare l'[archivio compresso delle brochure](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) da `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` ed estrarlo in una cartella denominata **brochures** sul file system locale.
1. Nel portale di Fonderia Azure AI, nel progetto, nel riquadro di spostamento a sinistra, selezionare **Playgrounds**, quindi selezionare **Prova il playground della chat**.
1. Nel riquadro **Configurazione** dell'area di lavoro, espandere la sezione **Aggiungi dati** e selezionare **Aggiungi un'origine dati**.
1. Nella procedura guidata **Aggiungi dati**, espandere il menu a discesa per selezionare **Carica file**.
1. Creare una nuova risorsa di archiviazione BLOB di Azure con le seguenti impostazioni:
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *lo stesso gruppo di risorse della risorsa di Fonderia Azure AI*
    - **Nome dell'account di archiviazione**: *un nome valido per la risorsa dell'account di archiviazione*
    - **Area**: *la stessa area della risorsa di Fonderia Azure AI*
    - **Prestazioni**: standard
    - **Ridondanza:** archiviazione con ridondanza locale
1. Creare la risorsa e attendere il completamento della distribuzione.
1. Tornare alla scheda Fonderia Azure AI, aggiornare l'elenco delle risorse di archiviazione BLOB di Azure e selezionare l'account appena creato.

    > **Nota**: se viene visualizzato un avviso che Azure OpenAI richiede l'autorizzazione per accedere alla risorsa, selezionare **Attiva CORS**.

1. Creare una nuova risorsa di Azure AI Search con le impostazioni seguenti:
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *lo stesso gruppo di risorse della risorsa di Fonderia Azure AI*
    - **Nome del servizio**: *un nome valido per la risorsa di Azure AI Search*
    - **Area**: *la stessa area della risorsa di Fonderia Azure AI*
    - **Piano tariffario**: Basic.

1. Creare la risorsa e attendere il completamento della distribuzione.
1. Tornare alla scheda Fonderia Azure AI, aggiornare l'elenco delle risorse di Azure AI Search e selezionare l'account appena creato.
1. Assegnare il nome all'indice `brochures-index`.
1. Abilitare l'opzione **Aggiungi ricerca vettoriale a questa risorsa di ricerca** e selezionare il modello di incorporamento distribuito in precedenza. Selezionare **Avanti**.

   >**Nota**: la procedura guidata **Aggiungi dati** potrebbe impiegare un po' di tempo prima di riconoscere il modello di incorporamento distribuito, quindi se non è possibile abilitare l'opzione di ricerca vettoriale, annullare la procedura guidata, attendere qualche minuto e riprovare.

1. Caricare tutti i file .pdf dalla cartella **brochure** estratta in precedenza e quindi selezionare **Avanti**.
1. Nel passaggio **Gestione dati**, selezionare il tipo di ricerca **Ibrido (vettore + parola chiave)** e le dimensioni blocco pari **a 1024**. Selezionare **Avanti**.
1. Nel passaggio **Connessione dati**, selezionare **Chiave API** come tipo di autenticazione. Selezionare **Avanti**.
1. Esaminare tutti i passaggi di configurazione e quindi selezionare **Salva e chiudi**.
1. Attendere il completamento del processo di indicizzazione, che può richiedere un po' di tempo a seconda delle risorse di calcolo disponibili nell'abbonamento.

    > **Suggerimento**: in attesa della generazione dell'indice, è possibile consultare le brochure scaricate per approfondirne i contenuti.

## Testare l'indice nel playground

Prima di usare l'indice in un flusso immediato basato su RAG, verificare che possa essere usato per influire sulle risposte di intelligenza artificiale generativa.

1. Nella pagina Chat playground, nel riquadro Configurazione, verificare che sia selezionata la distribuzione modello **gpt-4o**. Quindi, nel pannello principale della sessione di chat inviare la richiesta `Where can I stay in New York?`
1. Esaminare la risposta, che deve essere basata sui dati nell'indice.

## Creare un'app client RAG

Ora che si dispone di un indice funzionante, è possibile usare l'SDK di Azure OpenAI per implementare il pattern RAG in un'applicazione client. Varrà ora esaminato il codice per eseguire questa operazione in un semplice esempio.

> **Suggerimento**: è possibile scegliere di sviluppare la soluzione RAG usando Python o Microsoft C#. Seguire le istruzioni nella sezione appropriata per la lingua scelta.

### Preparare la configurazione dell'applicazione

1. Tornare alla scheda del browser contenente il portale di Azure (mantenendo aperto il portale Fonderia Azure AI nella scheda esistente).
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

    > **Suggerimento**: quando si incollano i comandi in CloudShell, l'ouput può richiedere una grande quantità di buffer dello schermo. È possibile cancellare la schermata immettendo il `cls` comando per rendere più semplice concentrarsi su ogni attività.

1. Dopo aver clonato il repository, passare alla cartella contenente i file di codice dell'applicazione chat:

    > **Nota**: seguire i passaggi per il linguaggio di programmazione scelto.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. Nel riquadro della riga di comando di Cloud Shell, immettere il comando seguente per installare la libreria dell'SDK di OpenAI:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.OpenAI
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

1. Nel file del codice, sostituire i segnaposto seguenti: 
    - **your_openai_endpoint**: l'endpoint Open AI dalla pagina **Panoramica** del progetto nel portale Fonderia Azure AI (selezionare la scheda di funzionalità **Azure OpenAI**, non l'inferenza di Azure AI o la funzionalità di Servizi di Azure AI).
    - **your_openai_api_key** La chiave API Open AI dalla pagina **Panoramica** del progetto nel portale Fonderia Azure AI (selezionare la scheda delle funzionalità **Azure OpenAI**, non l'inferenza di Azure AI o la funzionalità Servizi di Azure AI).
    - **your_chat_model**: il nome assegnato alla distribuzione del modello **gpt-4o**, dalla pagina **Modelli ed endpoint** nel portale Fonderia Azure AI (il nome predefinito è `gpt-4o`).
    - **your_embedding_model**: il nome assegnato alla distribuzione modello **text-embedding-ada-002**, dalla pagina **Modelli ed endpoint** nel portale Fonderia Azure AI (il nome predefinito è `text-embedding-ada-002`).
    - **your_search_endpoint**: URL della risorsa Azure AI Search. Questo è disponibile nel **Centro di gestione** nel portale Fonderia Azure AI.
    - **your_search_api_key**: chiave API per la risorsa Azure AI Search. Questo è disponibile nel **Centro di gestione** nel portale Fonderia Azure AI.
    - **your_index**: sostituire con il nome dell'indice dalla pagina **Dati e indici** per il progetto nel portale Fonderia Azure AI (deve essere `brochures-index`).
1. Dopo aver sostituito i segnaposto con l'editor di codice, usare il comando **CTRL+S** o **Fare clic con il pulsante destro del mouse > Salva** per salvare le modifiche e quindi usare il comando **CTRL+Q** o **Fare clic con il pulsante destro del mouse > Esci** per chiudere l'editor di codice mantenendo aperta la riga di comando di Cloud Shell.

### Esplorare il codice per implementare il criterio RAG

1. Immettere il comando seguente per modificare il file di codice fornito:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Esaminare il codice nel file, notando che:
    - Crea un client Azure OpenAI usando l'endpoint, la chiave e il modello di chat.
    - Crea un messaggio di sistema appropriato per una soluzione di chat correlata ai viaggi.
    - Invia una richiesta al client Azure OpenAI (contenente le istruzioni di sistema e il messaggio dell'utente basato sull'input dell'utente), aggiungendo:
        - Dettagli della connessione per l'indice Azure AI Search su cui eseguire query.
        - Dettagli del modello di incorporamento da usare per vettorizzare la query\*.
    - Visualizza la risposta dal prompt di cui è stato eseguito il grounding.
    - Aggiunge la risposta alla cronologia della chat.

    \**La query per l'indice di ricerca è basata sul prompt ed è usata per individuare contenuti pertinenti nei documenti indicizzati. È possibile usare una ricerca basata su parole chiave, che trasmette la query come testo semplice. Tuttavia, l'approccio con ricerca vettoriale risulta spesso più efficiente, motivo per cui viene usato un modello di incorporamento per vettorizzare il testo della query prima di inviarlo.*

1. Usare il comando **CTRL+Q** per chiudere l'editor di codice senza salvare le modifiche, mantenendo aperta la riga di comando di Cloud Shell.

### Eseguire l'applicazione di chat

1. Nel riquadro della riga di comando di Cloud Shell immettere il comando seguente per eseguire l'app:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Quando richiesto, immettere una domanda, ad esempio `Where should I go on vacation to see architecture?` ed esaminare la risposta del modello di IA generativa.

    Si noti che la risposta include riferimenti all'origine per indicare i dati indicizzati in cui è stata trovata la risposta.

1. Provare una domanda di completamento, ad esempio `Where can I stay there?`

1. Al termine, immettere `quit` per uscire dal programma. Quindi chiudere il riquadro Cloud Shell.

## Eseguire la pulizia

Per evitare i costi e l'utilizzo delle risorse di Azure non necessari, si dovrebbero rimuovere le risorse distribuite in questo esercizio.

1. Se è stata completata l'esplorazione di Azure AI Foundry, tornare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com` e accedere usando le credenziali di Azure, se necessario. Quindi eliminare le risorse nel gruppo di risorse in cui è stato effettuato il provisioning di Azure Ai Search e delle risorse di Azure AI.
