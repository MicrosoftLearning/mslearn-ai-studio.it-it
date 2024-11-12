---
lab:
  title: Usare il prompt flow per il Riconoscimento entità denominata (NER) in Studio AI della piattaforma Azure
---

# Usare il prompt flow per il Riconoscimento entità denominata (NER) in Studio AI della piattaforma Azure

L'estrazione di informazioni preziose dal testo è nota come Riconoscimento entità denominata (NER). Le entità sono parole chiave di interesse per l'utente in un determinato testo.

![Estrazione entità](./media/get-started-prompt-flow-use-case.gif)

I modelli linguistici di grandi dimensioni (LLM) possono essere utilizzati per eseguire il NER. Per creare un'applicazione che accetti un testo come entità di input e output, è possibile creare un flusso che usa un nodo LLM con prompt flow.

In questo esercizio verrà usato il prompt flow di Studio AI della piattaforma Azure per creare un'applicazione LLM che prevede un tipo di entità e un testo come input. L'applicazione chiama un modello GPT da Azure OpenAI tramite un nodo LLM per estrarre l'entità richiesta da un determinato testo, pulisce il risultato e restituisce le entità estratte.

![Panoramica dell'esercizio](./media/get-started-lab.png)

Prima, è necessario creare un progetto in Studio AI della piattaforma Azure per creare le risorse di Azure necessarie. È quindi possibile distribuire un modello GPT con il Servizio OpenAI di Azure. Dopo aver creato le risorse necessarie, è possibile creare il flusso. Infine, il flusso verrà eseguito per testarlo e visualizzare l'output di esempio.

## Creare un progetto in Studio AI della piattaforma Azure

Per iniziare, creare un progetto di Studio AI della piattaforma Azure e un hub di Azure per intelligenza per supportarlo.

1. In un Web browser, aprire [https://ai.azure.com](https://ai.azure.com) e accedere usando le credenziali di Azure.
1. Selezionare la pagina **Home** e quindi **+ Nuovo progetto**.
1. Nella procedura guidata **Crea un nuovo progetto**, creare un progetto con le impostazioni seguenti:
    - **Nome progetto**: *un nome univoco per il progetto*
    - **Hub**: *creare un nuovo hub con le impostazioni seguenti:*
    - **Nome hub**: *un nome univoco*.
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *un nuovo gruppo di risorse*
    - **Località**: selezionare **Informazioni su come scegliere** e quindi selezionare **gpt-35-turbo** nella finestra Helper posizione e usare l'area consigliata\*
    - **Connettere Servizi di Azure AI o OpenAI di Azure**: *creare una nuova connessione*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello tenant. Le aree elencate nell'helper posizione includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa. Altre informazioni sulla [disponibilità di modelli per area](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Esaminare la configurazione e creare il progetto.
1. Attendere la creazione del progetto.

## Distribuire un modello GTP

Per usare un modello LLM nel prompt flow, prima è necessario distribuire un modello. Studio AI della piattaforma Azure consente all'utente di distribuire modelli OpenAI utilizzabili nei flussi.

1. Nel riquadro di spostamento a sinistra, in **Componenti**, selezionare la pagina **Distribuzioni**.
1. Creare una nuova distribuzione del modello **gpt-35-turbo** con le impostazioni seguenti:
    - **Nome distribuzione**: *Nome univoco per la distribuzione del modello*
    - **Tipo di distribuzione**: Standard
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Risorsa di intelligenza artificiale**: *selezionare la risorsa creata in precedenza*
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: predefinitoV2
    - **Abilitare la quota dinamica**: disabilitato
   
Dopo aver distribuito il modello LLM, è possibile creare un flusso in Studio AI della piattaforma Azure che richiama il modello distribuito.

## Creare e gestire un flusso in Studio AI della piattaforma Azure

Ora che è stato effettuato il provisioning di tutte le risorse necessarie, è possibile creare un flusso.

### Crea un nuovo flusso

Per creare un nuovo flusso con un modello, è possibile selezionare uno dei tipi di flussi da sviluppare.

1. Nel riquadro di spostamento a sinistra, in **Strumenti**, selezionare **Prompt flow**.
1. Selezionare **+ Creare** per creare un nuovo flusso.
1. Creare un nuovo **flusso standard** e immettere `entity-recognition` come nome della cartella.

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

Vengono creati un flusso standard con un input, due nodi e un output. Il flusso verrà aggiornato in modo da accettare due input, estrarre entità, pulire l'output dal nodo LLM e restituire le entità come output.

### Avviare il runtime automatico

Per testare il flusso, è necessario effettuare un calcolo. Il calcolo necessario viene reso disponibile tramite il runtime.

1. Dopo aver creato il nuovo flusso denominato `entity-recognition`, il flusso dovrebbe essere aperto in Studio.
1. Selezionare **Avvia sessione di calcolo** dalla barra in alto.
1. L'avvio della sessione di calcolo richiederà 1-3 minuti.

### Configurare gli input

Il flusso creato richiederà due input: un testo e il tipo di entità da estrarre dal testo.

1. In **Input**, viene configurato un input denominato `topic` di tipo `string`. Modificare l'input esistente e aggiornare con le impostazioni seguenti:
    - **Nome**: `entity_type`
    - **Tipo**: `string`
    - **Valore**: `job title`
1. Selezionare **Aggiungi input**.
1. Configurare il secondo input per avere le impostazioni seguenti:
    - **Nome**: `text`
    - **Tipo**: `string`
    - **Valore**: `The software engineer is working on a new update for the application.`

### Configurare il nodo LLM

Il flusso standard include già un nodo che usa lo strumento LLM. È possibile trovare il nodo nella panoramica del flusso. La richiesta predefinita chiede una barzelletta. Il nodo LLM verrà aggiornato per estrarre le entità in base ai due input specificati nella sezione precedente.

1. Passare al **nodo LLM** denominato `joke`.
1. Sostituire il nome con `NER_LLM`
1. In **Connessione**, selezionare la connessione `Default_AzureOpenAI`.
1. Per **deployment_name**, selezionare il modello `gpt-35-turbo` distribuito.
1. Sostituire il campo richiesta con il codice seguente.

   ```yml
   {% raw %}
   system:

   Your task is to find entities of a certain type from the given text content.
   If there're multiple entities, please return them all with comma separated, e.g. "entity1, entity2, entity3".
   You should only return the entity list, nothing else.
   If there's no such entity, please return "None".

   user:
   
   Entity type: {{entity_type}}
   Text content: {{text}}
   Entities:
   {% endraw %}
   ```

1. Selezionare **Convalida e analizza input**.
1. All'interno del nodo LLM, nella sezione **Input**, configurare quanto segue:
    - Per `entity_type`, selezionare il valore `${inputs.entity_type}`.
    - Per `text`, selezionare il valore `${inputs.text}`.

Il nodo LLM accetta ora il tipo di entità e il testo come input, includerlo nella richiesta specificata e inviare la richiesta al modello distribuito.

### Configurare il nodo Python

Per estrarre solo le informazioni chiave dal risultato del modello, è possibile usare lo strumento Python per pulire l'output del nodo LLM.

1. Passare al nodo Python denominato `echo`.
1. Sostituire il nome con `cleansing`.
1. Sostituire il codice con il codice seguente:

   ```python
   from typing import List
   from promptflow import tool
    
    
   @tool
   def cleansing(entities_str: str) -> List[str]:
       # Split, remove leading and trailing spaces/tabs/dots
       parts = entities_str.split(",")
       cleaned_parts = [part.strip(" \t.\"") for part in parts]
       entities = [part for part in cleaned_parts if len(part) > 0]
       return entities
    
   ```

1. Selezionare **Convalida e analizza l'input**.
1. All'interno del nodo Python, nella sezione **Input** , impostare il valore di `entities_str` su `${NER_LLM.output}`.

### Configurare l'output

Infine, è possibile configurare l'output dell'intero flusso. Si vuole un solo output per il flusso, che deve essere l'entità estratta.

1. Passare agli **output** del flusso.
1. Per **Nome** inserisci `entities`.
1. Per **Valore**, selezionare `${cleansing.output}`.

### Esecuzione del flusso

Dopo aver sviluppato il flusso, è possibile eseguirlo per testarlo. Poiché sono stati aggiunti valori predefiniti agli input, è possibile testare facilmente il flusso in studio.

1. Selezionare **Esegui** per testare il flusso.
1. Attendere che l'esecuzione venga completata.
1. Selezionare **Visualizza output**. Verrà visualizzata un popup che mostra l'output per gli input predefiniti. Facoltativamente, è anche possibile esaminare i log.

## Eliminare le risorse di Azure

Quando si finisce di esplorare Studio AI della piattaforma Azure, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

- Passare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com`.
- Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
- Selezionare il gruppo di risorse creato per questo esercizio.
- Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
- Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
