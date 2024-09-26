---
lab:
  title: 'Esplorare, distribuire e chattare con i modelli linguistici in Studio AI della piattaforma Azure'
---

# Esplorare, distribuire e chattare con i modelli linguistici in Studio AI della piattaforma Azure

Il catalogo dei modelli in Studio AI della piattaforma Azure funge da repository centrale in cui è possibile esplorare e usare un'ampia gamma di modelli, semplificando la creazione dello scenario di intelligenza artificiale generativa.

In questo esercizio verrà esaminato il catalogo dei modelli in Studio AI della piattaforma Azure.

Questo esercizio richiederà circa **25** minuti.

## Creare un hub di Azure per intelligenza artificiale

Per ospitare i progetti, la sottoscrizione di Azure dovrà disporre di un hub di Azure per intelligenza artificiale. È possibile creare questa risorsa durante la creazione di un progetto o effettuarne il provisioning in anticipo (ciò che verrà fatto in questo esercizio).

1. Nella sezione **Gestione**, selezionare **Tutti gli hub** e quindi **+ Nuovo hub**. Creare un nuovo hub con le impostazioni seguenti:
    - **Nome hub**: *un nome univoco*.
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare un nuovo gruppo di risorse con un nome univoco o selezionarne uno esistente*
    - **Ubicazione**: *effettuare una scelta **casuale** da una delle aree seguenti*\*
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

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello di tenant. Le aree elencate includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota negli scenari in cui si condivide un tenant con altri utenti. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa.

    Dopo che l'hub di Azure per intelligenza artificiale è stato creato, il risultato dovrebbe essere simile all'immagine seguente.

    ![Screenshot di un hub di Azure per intelligenza artificiale in Studio AI della piattaforma Azure.](./media/azure-ai-resource.png)

1. Aprire una nuova scheda del browser (lasciando aperta la scheda di Studio AI della piattaforma Azure) e passare al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com?azure-portal=true), effettuando l'accesso con le credenziali di Azure, se richiesto.
1. Passare al gruppo di risorse in cui è stato creato l'hub di Azure per intelligenza artificiale e visualizzare le risorse di Azure create.

    ![Screenshot di un hub di Azure per intelligenza artificiale e delle risorse correlate nel portale di Azure.](./media/azure-portal.png)

1. Tornare alla scheda del browser di Studio AI della piattaforma Azure.
1. Visualizzare ognuna delle pagine nel riquadro a sinistra della pagina per l'hub di Azure per intelligenza artificiale e prendere nota degli artefatti che è possibile creare e gestire. Nella pagina **Connessioni**, osservare che sono già state create le connessioni ai servizi OpenAI di Azure e intelligenza artificiale.

## Creare un progetto

Un hub di Azure per intelligenza artificiale offre un'area di lavoro collaborativa in cui è possibile definire uno o più *progetti*. Creare un progetto nell'hub di Azure per intelligenza artificiale.

1. In Studio AI della piattaforma Azure assicurarsi di essere nell'hub appena creato (è possibile verificare l'indirizzo controllando il percorso nella parte superiore della schermata).
1. Passare a **Tutti i progetti** usando il menu a sinistra.
1. Selezionare **+ Nuovo progetto**.
1. Nella procedura guidata **Crea un nuovo progetto** creare un progetto con le impostazioni seguenti:
    - **** Hub corrente: *hub di intelligenza artificiale*
    - **Nome progetto**: *nome univoco per il progetto*
1. Attendere la creazione del progetto. Il risultato dovrebbe avere un aspetto simile all'immagine seguente:

    ![Screenshot dei dettagli di un progetto in Studio AI della piattaforma Azure.](./media/azure-ai-project.png)

1. Visualizzare le pagine nel riquadro a sinistra, espandendo ogni sezione e annotando le attività che è possibile eseguire e le risorse che è possibile gestire in un progetto.

## Scegliere un modello usando i benchmark del modello

Prima di distribuire un modello, è possibile esplorare i benchmark del modello per decidere quello più adatto alle proprie esigenze.

Immaginare di voler creare un copilota personalizzato come assistente di viaggio. In particolare, è necessario che il copilota offra assistenza per le richieste di informazioni relative al viaggio, come l'obbligo del visto, le previsioni del tempo, le attrazioni locali e le norme culturali.

Il copilota dovrà fornire informazioni effettivamente accurate, quindi l'attendibilità è importante. Inoltre, è richiesto che le risposte del copilota siano chiare e di facile comprensione. Pertanto, è opportuno scegliere un modello che abbia un alto tasso di efficienza e di coerenza.

1. In Studio AI della piattaforma Azure, passare a **Benchmark del modello** nella sezione **Attività iniziali** usando il menu a sinistra.
    Nella scheda **Benchmark di qualità** è possibile trovare alcuni grafici già visualizzati, confrontando modelli diversi.
1. Filtrare i modelli visualizzati:
    - **Attività**: risposta alle domande
    - **Raccolte**: OpenAI di Azure
    - **Metriche**: coerenza, scorrevolezza, fondatezza
1. Esplorare i grafici risultanti e la tabella di confronto. Durante l'esplorazione, è possibile provare a rispondere alle domande seguenti:
    - È presente una differenza di prestazioni tra i modelli GPT-3.5 e GPT-4?
    - Esiste una differenza tra le versioni dello stesso modello?
    - In che modo le 32 mila varianti differiscono dai modelli base?

Dalla raccolta OpenAI di Azure, è possibile scegliere tra modelli GPT-3.5 e GPT-4. Distribuire questi due modelli ed esaminare il modo in cui vengono confrontati per il caso d'uso.

## Distribuire modelli OpenAI di Azure

Ora che sono state esaminate le opzioni tramite benchmark del modello, è possibile distribuire i modelli linguistici. È possibile esplorare il catalogo dei modelli e avviare la distribuzione, oppure distribuire un modello dalla pagina **Distribuzioni**. Verranno ora esaminate entrambe le opzioni.

### Distribuire un modello dal catalogo dei modelli

Iniziare a distribuire un modello dal catalogo dei modelli. Questa opzione può essere preferibile per filtrare tutti i modelli disponibili.

1. Passare alla pagina **Catalogo del modello** nella sezione **Attività iniziali** usando il menu a sinistra.
1. Cercare e distribuire il modello `gpt-35-turbo`, gestito da Azure per intelligenza artificiale, con le seguenti impostazioni:
    - **Nome distribuzione**: *un nome univoco per la distribuzione del modello, che indica che è un modello GPT-3.5*
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Tipo di distribuzione**: Standard
    - **Risorsa OpenAI di Azure connessa**: *selezionare la connessione predefinita creata al momento della creazione dell'hub*
    - **Limite di velocità dei token al minuto**: 5K
    - **Filtro contenuto**: Predefinito

### Distribuire un modello tramite distribuzioni

Se il modello che si desidera distribuire è già noto, è preferibile eseguire l'operazione tramite Distribuzioni.

1. Passare alla pagina **Distribuzioni** nella sezione **Componenti**, usando il menu a sinistra.
1. Nella scheda **Distribuzioni del modello**, creare una distribuzione con le impostazioni seguenti:
    - **Modello**: gpt-4
    - **Nome distribuzione**: *nome univoco per la distribuzione del modello che indica che è un modello GPT-4*
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Tipo di distribuzione**: Standard
    - **Risorsa OpenAI di Azure connessa**: *selezionare la connessione predefinita creata al momento della creazione dell'hub*
    - **Limite di velocità dei token al minuto**: 5K
    - **Filtro contenuto**: Predefinito

    > **Nota**: è possibile che alcuni modelli mostrino i parametri di riferimento del modello, ma non come opzioni nel catalogo dei modelli. La disponibilità di modelli varia in base alla posizione. La posizione è specificata a livello di hub AI, dove è possibile usare l'**helper della posizione** per specificare il modello che si desidera distribuire e ottenere un elenco di posizioni in cui è possibile distribuirlo.

## Testare i modelli nel playground della chat

Ora che sono disponibili due modelli da confrontare, di seguito viene illustrato come si comportano i modelli in un’interazione conversazionale.

1. Passare alla pagina **Chat** nella sezione **Playground del progetto** usando il menu a sinistra.
1. Nel **playground della chat**, selezionare la distribuzione GPT-3.5.
1. Nella finestra della chat, immettere la query `What can you do?` e visualizzare la risposta.
    Le risposte sono molto generiche. Tenere a mente che si desidera creare un copilota personalizzato che funga da assistente di viaggio. È possibile specificare il tipo di aiuto desiderato nella domanda che si pone.
1. Nella finestra della chat, immettere la query `Imagine you're a travel assistant, what can you help me with?`. In questo modo, le risposte sono già più specifiche. È possibile che non si desideri che gli utenti finali forniscano il contesto necessario ogni volta che interagiscono con il copilota. Per aggiungere istruzioni globali, è possibile modificare il messaggio di sistema.
1. Aggiornare il messaggio di sistema con il prompt seguente:

   ```
   You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
   ```

1. Selezionare **Applica modifiche** e **Cancella chat**.
1. Nella finestra della chat, immettere la query `What can you do?` e visualizzare la nuova risposta. Osservare come è diversa dalla risposta ricevuta in precedenza. La risposta ora è specifica per i viaggi.
1. Continuare la conversazione chiedendo: `I'm planning a trip to London, what can I do there?` Il copilota offre molte informazioni correlate ai viaggi. È possibile migliorare ancora l'output. Ad esempio, è possibile fare in modo che la risposta sia più concisa.
1. Aggiornare il messaggio di sistema aggiungendo `Answer with a maximum of two sentences.` alla fine. Applicare la modifica, cancellare la chat e testarla di nuovo chiedendo: `I'm planning a trip to London, what can I do there?` È anche possibile che il copilota continui la conversazione invece di rispondere semplicemente alla domanda.
1. Aggiornare il messaggio di sistema aggiungendo `End your answer with a follow-up question.` alla fine. Applicare la modifica, cancellare la chat e testarla di nuovo chiedendo: `I'm planning a trip to London, what can I do there?`
1. Modificare la **distribuzione** nel modello GPT-4 e ripetere tutti i passaggi in questa sezione. Notare che i modelli possono variare nei relativi output.
1. Infine, testare entrambi i modelli nella query `Who is the prime minister of the UK?`. Le prestazioni di questa domanda sono correlate all'allineamento (se la risposta è effettivamente accurata) dei modelli. Le prestazioni sono correlate alle conclusioni dei benchmark del modello?

Ora che sono stati esaminati entrambi i modelli, prendere in considerazione il modello da scegliere ora per il caso d'uso. All'inizio, gli output dei modelli possono differire e potrebbe essere preferibile un modello rispetto all'altro. Tuttavia, dopo l'aggiornamento del messaggio di sistema, è possibile notare che la differenza è minima. Dal punto di vista dell'ottimizzazione dei costi, è quindi possibile scegliere il modello GPT-3.5 rispetto al modello GPT-4, perché le prestazioni sono molto simili.

## Eseguire la pulizia

Al termine dell'esplorazione di Studio AI della piattaforma Azure, è necessario eliminare le risorse create in questo esercizio per evitare costi di Azure non necessari.

1. Tornare alla scheda del browser contenente il portale di Azure (o riaprire il [portale di Azure](https://portal.azure.com?azure-portal=true) in una nuova scheda del browser) e visualizzare il contenuto del gruppo di risorse in cui sono state distribuite le risorse usate in questo esercizio.
1. Sulla barra degli strumenti selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse e confermarne l'eliminazione.
