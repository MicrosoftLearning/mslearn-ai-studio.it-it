---
lab:
  title: Ottimizzare un modello linguistico per il completamento della chat in Studio AI della piattaforma Azure
---

# Ottimizzare un modello linguistico per il completamento della chat in Studio AI della piattaforma Azure

In questo esercizio, verrà ottimizzato un modello linguistico che si vuole usare per uno scenario di copilota personalizzato con Studio AI della piattaforma Azure.

Questo esercizio richiederà circa **45** minuti.

## Creare un hub e un progetto di intelligenza artificiale in Studio AI della piattaforma Azure

Per iniziare, creare un progetto di Studio AI della piattaforma Azure all'interno di un hub di Azure per intelligenza artificiale:

1. In un Web browser aprire [https://ai.azure.com](https://ai.azure.com) e accedere usando le credenziali di Azure.
1. Selezionare la **home** page e quindi **+ Nuovo progetto**.
1. Nella procedura guidata **Crea un nuovo progetto** creare un progetto con le impostazioni seguenti:
    - **Nome progetto**: *nome univoco per il progetto*
    - **Hub**: *creare un nuovo hub con le impostazioni seguenti:*
        - **Nome hub**: *un nome univoco*.
        - **Sottoscrizione**: *la sottoscrizione di Azure usata*
        - **Gruppo di risorse**: *un nuovo gruppo di risorse*
        - **Ubicazione**: *effettuare una scelta **casuale** da una delle aree seguenti*\*
        - Stati Uniti orientali 2
        - Stati Uniti centro-settentrionali
        - Svezia centrale
        - Svizzera settentrionale
    - **Connettere Servizi di Azure AI o OpenAI di Azure**: *creare una nuova connessione*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello di tenant. Le aree elencate includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa. Altre informazioni sulla [disponibilità di modelli per area](https://learn.microsoft.com/en-us/azure/ai-studio/concepts/fine-tuning-overview#azure-openai-models)

1. Esaminare la configurazione e creare il progetto.
1. Attendere la creazione del progetto.

## Ottimizzare un modello GPT-3.5

Prima di poter ottimizzare un modello, è necessario un set di dati.

1. Salvare il set di dati di training come file JSONL in locale: https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-finetune.jsonl
1. Passare alla pagina **Ottimizzazione** nella sezione **Strumenti**, usando il menu a sinistra.
1. Selezionare il pulsante per aggiungere un nuovo modello di ottimizzazione, selezionare il modello `gpt-35-turbo` e selezionare **Conferma**.
1. **Ottimizzare** il modello usando la configurazione seguente:
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Suffisso del modello**: `ft-travel`
    - **Connessione di OpenAI di Azure**: *selezionare la connessione creata al momento della creazione dell'hub*
    - **Dati di training**: caricare i file
    - **Caricare il file**: selezionare il file JSONL scaricato nel passaggio precedente.

    > **Suggerimento**: non è necessario attendere il completamento dell'elaborazione dei dati per continuare con il passaggio successivo.

    - **Dati di convalida**: nessuno
    - **Parametri attività**: *mantenere le impostazioni predefinite*
1. L'ottimizzazione verrà avviata e potrebbe richiedere un po' di tempo per il completamento.

> **Nota**: l'ottimizzazione e la distribuzione possono richiedere del tempo, quindi potrebbe essere necessario eseguire periodicamente il controllo per completare il passaggio successivo.

## Distribuire il modello ottimizzato

Al termine dell'ottimizzazione, è possibile distribuire il modello.

1. Selezionare il modello ottimizzato. Selezionare la scheda **Metriche** ed esplorare le metriche ottimizzate.
1. Distribuire il modello ottimizzato con le configurazioni seguenti:
    - **Nome distribuzione**: *un nome univoco per il modello, è possibile usare il valore predefinito*
    - **Tipo di distribuzione**: Standard
    - **Limite di velocità dei token al minuto**: 5K
    - **Filtro contenuto**: Predefinito

## Testare il modello ottimizzato

Dopo aver distribuito il modello ottimizzato, è possibile testare il modello allo stesso modo in cui è possibile testare qualsiasi altro modello distribuito.

1. Quando la distribuzione è pronta, passare al modello ottimizzato e selezionare **Apri in playground**.
1. Nella finestra della chat immettere la query `What can you do?` Tenere presente che, anche se non è stato specificato il messaggio di sistema per indicare al modello di rispondere alle domande relative ai viaggi, il modello riconosce già su cosa deve concentrarsi.
1. Provare con un'altra query, ad esempio `Where should I go on holiday for my 30th birthday?`

## Eliminare le risorse di Azure

Quando si finisce di esplorare Studio AI della piattaforma Azure, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

- Passare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com`.
- Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
- Selezionare il gruppo di risorse creato per questo esercizio.
- Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
- Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
