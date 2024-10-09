---
lab:
  title: 'Esercizio - Esplorare, distribuire e chattare con i modelli linguistici in Studio AI della piattaforma Azure.'
---

# Esercizio - Esplorare, distribuire e chattare con i modelli linguistici in Studio AI della piattaforma Azure.

Il catalogo dei modelli in Studio AI della piattaforma Azure funge da archivio centrale in cui è possibile esplorare e usare un'ampia gamma di modelli, semplificando la creazione dello scenario di intelligenza artificiale generativa.

In questo esercizio verrà esaminato il catalogo dei modelli in Studio AI della piattaforma Azure.

Questo esercizio richiederà circa **25** minuti.

## Creare un hub di Azure per intelligenza artificiale

Per ospitare i progetti, la sottoscrizione di Azure dovrà disporre di un hub di Azure per intelligenza artificiale. È possibile creare questa risorsa durante la creazione di un progetto o eseguirne il provisioning in anticipo (ovvero ciò che verrà fatto in questo esercizio).

1. Nella sezione **Gestione**, selezionare **Tutte le risorse** e quindi **+ Nuovo hub**. Creare un nuovo hub con le impostazioni seguenti:
    - **Nome hub**: *un nome univoco*.
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare un nuovo gruppo di risorse con un nome univoco o selezionarne uno esistente*
    - **Posizione**: *effettuare una **scelta casuale** da una delle aree seguenti*\*
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
    - **Connettere i Servizi di Azure AI o OpenAI di Azure**: *selezionare per creare un nuovo servizio di intelligenza artificiale o usarne uno esistente*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote di aree a livello tenant. Le aree elencate includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota negli scenari in cui si condivide un tenant con altri utenti. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa.

    Dopo che l'hub di Azure per intelligenza artificiale è stato creato, il risultato dovrebbe essere simile all'immagine seguente:

    ![Screenshot di un hub di Azure per intelligenza artificiale in Studio AI della piattaforma Azure.](./media/azure-ai-resource.png)

1. Aprire una nuova scheda del browser (lasciando aperta la scheda di Studio AI della piattaforma Azure) e passare al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com?azure-portal=true), effettuando l'accesso con le credenziali di Azure, se richiesto.
1. Passare al gruppo di risorse in cui è stato creato l'hub di Azure per intelligenza artificiale e visualizzare le risorse di Azure create.

    ![Screenshot di un hub di Azure per intelligenza artificiale e delle risorse correlate nel portale di Azure.](./media/azure-portal.png)

1. Tornare alla scheda del browser di Studio AI della piattaforma Azure.
1. Visualizzare ognuna delle pagine nel riquadro a sinistra della pagina per l'hub di Azure per intelligenza artificiale e prendere nota degli artefatti che è possibile creare e gestire. Nella pagina **Connessioni**, osservare che sono già state create le connessioni ai servizi OpenAI di Azure e intelligenza artificiale.

## Creare un progetto

Un hub di Intelligenza artificiale di Azure offre un'area di lavoro collaborativa in cui è possibile definire uno o più *progetti*. Creare un progetto nell'hub di Azure per intelligenza artificiale.

1. In Studio AI della piattaforma Azure assicurarsi di essere nell'hub appena creato (è possibile verificare l'indirizzo controllando il percorso nella parte superiore della schermata).
1. Passare a **Tutti i progetti** usando il menu a sinistra.
1. Selezionare **+ Nuovo progetto**.
1. **Nella procedura guidata Creare un nuovo progetto**, creare un progetto con le impostazioni seguenti:
    - **** Hub corrente: *hub di intelligenza artificiale*
    - **Nome progetto**: *un nome univoco per il progetto*
1. Attendere la creazione del progetto. Il risultato dovrebbe avere un aspetto simile all'immagine seguente.

    ![Screenshot dei dettagli di un progetto in Studio AI della piattaforma Azure.](./media/azure-ai-project.png)

1. Visualizzare le pagine nel riquadro a sinistra, espandendo ogni sezione e annotando le attività che è possibile eseguire e le risorse che è possibile gestire in un progetto.

## Scegliere un modello usando i benchmark del modello

Prima di distribuire un modello, è possibile esplorare i benchmark del modello per decidere quello più adatto alle proprie esigenze.

Immaginiamo di voler creare un copilota personalizzato che funge da assistente di viaggio. In particolare, si vuole che il copilota offra supporto per le richieste relative ai viaggi, ad esempio requisiti di visto, previsioni meteo, attrazioni locali e norme culturali.

Il copilota dovrà fornire informazioni effettivamente accurate, quindi l'attendibilità è importante. Accanto a questo, si vuole che le risposte del copilota siano facili da leggere e comprendere. Pertanto, si vuole anche scegliere un modello con tariffe elevate in base alla scorrevolezza e alla coerenza.

1. In Studio AI della piattaforma Azure, passare a **Benchmark del modello** nella sezione**Attività iniziali** usando il menu a sinistra.
    Nella scheda **Benchmark di qualità** è possibile trovare alcuni grafici già visualizzati, confrontando modelli diversi.
1. Filtrare i modelli visualizzati:
    - **Attività**: Risposta alla domanda
    - **Raccolte**: Azure OpenAI
    - **Metriche**: coerenza, scorrevolezza, fondatezza
1. Esplorare i grafici risultanti e la tabella di confronto. Durante l'esplorazione, è possibile provare a rispondere alle domande seguenti:
    - Si nota una differenza nelle prestazioni tra i modelli GPT-3.5 e GPT-4?
    - Esiste una differenza tra le versioni dello stesso modello?
    - In che modo le varianti di 32k differiscono dai modelli di base?

Dalla raccolta OpenAI di Azure è possibile scegliere tra modelli GPT-3.5 e GPT-4. Distribuire questi due modelli ed esaminare il modo in cui vengono confrontati per il caso d'uso.

## Distribuire modelli OpenAI di Azure

Ora che sono state esaminate le opzioni tramite benchmark del modello, è possibile distribuire i modelli linguistici. È possibile esplorare il catalogo dei modelli e distribuire da lì oppure distribuire un modello tramite la pagina **Distribuzioni**. Esaminiamo ora entrambe le opzioni.

### Distribuire un modello dal catalogo dei modelli

Iniziamo distribuendo un modello dal catalogo dei modelli. È possibile preferire questa opzione quando si desidera filtrare tutti i modelli disponibili.

1. Passare alla pagina **Catalogo del modello** nella sezione **Attività iniziali** usando il menu a sinistra.
1. Cercare e distribuire il `gpt-35-turbo` modello, curato da Azure per l'intelligenza artificiale, con le impostazioni seguenti:
    - **Nome distribuzione**: *Nome univoco per la distribuzione del modello, che indica che è un modello GPT-3.5*
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Tipo di distribuzione**: Standard
    - **Risorsa OpenAI di Azure connessa**: *selezionare la connessione predefinita creata al momento della creazione dell'hub*
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: Predefinito

### Distribuire un modello tramite distribuzioni

Se si conosce già esattamente il modello che si vuole distribuire, è consigliabile eseguirlo tramite distribuzioni.

1. Passare alla pagina **Distribuzioni** nella sezione **Componenti** , usando il menu a sinistra.
1. Nella scheda **Distribuzioni del modello**, creare una distribuzione con le impostazioni seguenti:
    - **Modello**: gpt-4
    - **Nome distribuzione**: *Nome univoco per la distribuzione del modello che indica che è un modello GPT-4*
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Tipo di distribuzione**: Standard
    - **Risorsa OpenAI di Azure connessa**: *selezionare la connessione predefinita creata al momento della creazione dell'hub*
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: Predefinito

    > **Nota**: si sarà notato che alcuni modelli mostrano i benchmark del modello, ma non come un'opzione nel catalogo dei modelli. La disponibilità di modelli varia in base alla posizione. La posizione è specificata sul livello di hub IA, in cui è possibile usare l'**helper della posizione** per specificare il modello da distribuire per ottenere un elenco di posizioni in cui è possibile distribuirlo.

## Testare i modelli nel playground della chat

Ora che sono disponibili due modelli da confrontare, vediamo come si comportano i modelli in un’interazione conversazionale.

1. Passare alla pagina **Chat** nella sezione **Playground del progetto** usando il menu a sinistra.
1. Nel **playground della chat**, selezionare la distribuzione GPT-3.5.
1. Nella finestra della chat immettere la query `What can you do?` e visualizzare la risposta.
    Le risposte sono molto generiche. Ricorda che vogliamo creare un copilota personalizzato che funga da assistente di viaggio. È possibile specificare il tipo di aiuto desiderato nella domanda che si pone.
1. Nella finestra della chat immettere la query `Imagine you're a travel assistant, what can you help me with?`. Le risposte sono già più specifiche. Potrebbe non essere necessario che gli utenti finali forniscano il contesto necessario ogni volta che interagiscono con il copilota. Per aggiungere istruzioni globali, è possibile modificare il messaggio di sistema.
1. Aggiornare il messaggio di sistema con il prompt seguente:

   ```
   You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
   ```

1. Selezionare **Applica modifiche** e **Cancella chat**.
1. Nella finestra della chat immettere la query `What can you do?` e visualizzare la nuova risposta. Osservare come è diverso dalla risposta ricevuta in precedenza. La risposta è specifica per viaggiare ora.
1. Continuare la conversazione chiedendo: `I'm planning a trip to London, what can I do there?` Il copilota offre molte informazioni correlate ai viaggi. È possibile migliorare ancora l'output. Ad esempio, è possibile che la risposta sia più concisa.
1. Aggiornare il messaggio di sistema aggiungendo `Answer with a maximum of two sentences.` alla fine del messaggio. Applicare la modifica, cancellare la chat e testare di nuovo la chat chiedendo: `I'm planning a trip to London, what can I do there?` È anche possibile che il copilota continui la conversazione invece di rispondere semplicemente alla domanda.
1. Aggiornare il messaggio di sistema aggiungendo `End your answer with a follow-up question.` alla fine del messaggio. Applicare la modifica, cancellare la chat e testare di nuovo la chat chiedendo: `I'm planning a trip to London, what can I do there?`
1. Modificare la **distribuzione** nel modello GPT-4 e ripetere tutti i passaggi in questa sezione. Si noti che i modelli possono variare nei relativi output.
1. Infine, testare entrambi i modelli nella query `Who is the prime minister of the UK?`. Le prestazioni di questa domanda sono correlate al radicamento (se la risposta è effettivamente accurata) dei modelli. Le prestazioni sono correlate alle conclusioni dei benchmark del modello?

Ora che sono stati esaminati entrambi i modelli, considerare il modello da scegliere ora per il caso d'uso. All'inizio, gli output dei modelli possono differire e potrebbe essere preferibile un modello rispetto all'altro. Tuttavia, dopo l'aggiornamento del messaggio di sistema, è possibile notare che la differenza è minima. Dal punto di vista dell'ottimizzazione dei costi, è quindi possibile scegliere il modello GPT-3.5 rispetto al modello GPT-4, perché le prestazioni sono molto simili.

## Eseguire la pulizia

Al termine dell'esplorazione di Studio AI dalla piattaforma Azure, è necessario eliminare le risorse create in questo esercizio per evitare di incorrere in costi di Azure non necessari.

1. Tornare alla scheda del browser contenente il portale di Azure (o riaprire il [portale di Azure](https://portal.azure.com?azure-portal=true) in una nuova scheda del browser) e visualizzare il contenuto del gruppo di risorse in cui sono state distribuite le risorse usate in questo esercizio.
1. Sulla barra degli strumenti selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse e confermarne l'eliminazione.
