---
lab:
  title: Esplorare i componenti e gli strumenti di Azure AI Foundry
---

# Esplorare i componenti e gli strumenti di Azure AI Foundry

In questo esercizio, Azure AI Foundry viene usato per creare un progetto ed esplorare un modello di intelligenza artificiale generativa.

Questo esercizio richiede circa **30** minuti.

## Aprire il portale di Azure AI Foundry

Per iniziare, esplorare il portale di Azure AI Foundry.

1. In un Web browser, aprire [https://ai.azure.com](https://ai.azure.com) e accedere usando le credenziali di Azure. La home page del portale di Azure AI Foundry si presenta come l'immagine seguente:

    ![Screenshot del portale di Azure AI Foundry.](./media/azure-ai-studio-home.png)

1. Esaminare le informazioni nella home page e visualizzare ciascuna scheda, osservando le opzioni per esplorare modelli e funzionalità, creare progetti e gestire le risorse.

## Creare un progetto e un hub di Azure per intelligenza artificiale

Un hub di Intelligenza artificiale di Azure offre un'area di lavoro collaborativa in cui è possibile definire uno o più *progetti*. Creare un progetto un hub di Azure per intelligenza artificiale.

1. Nella home page, selezionare **+ Crea progetto**. Nella procedura guidata **Creare un progetto** è possibile visualizzare tutte le risorse di Azure che verranno create automaticamente con il progetto, oppure personalizzare le impostazioni seguenti selezionando **Personalizza** prima di selezionare **Crea**:
   
    - **Nome hub**: *un nome univoco*.
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare un nuovo gruppo di risorse con un nome univoco o selezionarne uno esistente*
    - **Località**: selezionare **Informazioni su come scegliere** e quindi selezionare **gpt-35-turbo** nella finestra Helper posizione e usare l'area consigliata\*
    - **Connettere i Servizi di Azure AI o OpenAI di Azure**: *selezionare per creare un nuovo servizio di intelligenza artificiale o usarne uno esistente*
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello tenant. Le aree elencate includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota negli scenari in cui si condivide un tenant con altri utenti. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa.

1. Se si seleziona **Personalizza**, selezionare **Avanti** ed esaminare la configurazione.
1. Selezionare **Crea** e attendere il completamento del processo.
   
    Dopo che l'hub di Azure per intelligenza artificiale e il progetto sono stati creati, il risultato dovrebbe essere simile all'immagine seguente:

    ![Screenshot dei dettagli di un hub di Azure per intelligenza artificiale nel portale di Azure AI Foundry.](./media/azure-ai-resource.png)

1. Aprire una nuova scheda del browser (lasciando aperta la scheda di Azure AI Foundry) e passare al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com?azure-portal=true), effettuando l'accesso con le credenziali di Azure, se richiesto.
1. Passare al gruppo di risorse in cui è stato creato l'hub di Azure per intelligenza artificiale e visualizzare le risorse di Azure create.

    ![Screenshot di un hub di Azure per intelligenza artificiale e delle risorse correlate nel portale di Azure.](./media/azure-portal.png)

1. Tornare alla scheda del browser del portale di Azure AI Foundry.
1. Visualizzare ognuna delle pagine nel riquadro a sinistra della pagina per l'hub di Azure per intelligenza artificiale e prendere nota degli artefatti che è possibile creare e gestire. Nella pagina **Centro gestione** è possibile selezionare le **Risorse connesse**, nell'hub o nel progetto, e osservare che sono già state create le connessioni ai servizi OpenAI e IA di Azure.
1. Nella pagina del Centro gestione, selezionare **Vai al progetto**.

## Distribuire e testare il modello

È possibile usare un progetto per creare soluzioni di intelligenza artificiale complesse basate su modelli di intelligenza artificiale generativa. Un'esplorazione completa di tutte le opzioni di sviluppo disponibili nel portale Azure AI Foundry esula dall'ambito di questo esercizio, ma verranno esaminati alcuni semplici modi in cui è possibile usare i modelli in un progetto.

1. Nel riquadro a sinistra del progetto, nella sezione **Risorse personali** selezionare la pagina **Modelli + endpoint**.
1. Nella pagina **Modelli + endpoint**, nella scheda **Distribuzioni del modello**, selezionare **+ Distribuisci modello**.
1. Cercare il modello **gpt-35-turbo** dall'elenco, selezionarlo e confermare.
1. Distribuire il modello con le impostazioni seguenti selezionando **Personalizza** nei dettagli della distribuzione:
    - **Nome distribuzione**: *Nome univoco per la distribuzione del modello*
    - **Tipo di distribuzione**: Standard
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Risorsa di intelligenza artificiale**: *selezionare la risorsa creata in precedenza*
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: predefinitoV2
    - **Abilitare la quota dinamica**: disabilitato
      
    > **Nota**: la riduzione del TPM consente di evitare l'eccessivo utilizzo della quota disponibile nella sottoscrizione in uso. 5.000 TPM è sufficiente per i dati usati in questo esercizio.

1. Dopo aver distribuito il modello, nella pagina di panoramica della distribuzione selezionare **Apri nel playground**.
1. Nella pagina **playground della chat** assicurarsi che la distribuzione del modello sia selezionata nella sezione **Distribuzione**.
1. Nella finestra della chat immettere una query, ad esempio *Che cos'è l'intelligenza artificiale?* e visualizzare la risposta:

    ![Screenshot del playground nel portale Azure AI Foundry.](./media/playground.png)

## Eseguire la pulizia

Al termine dell'esplorazione del portale Azure AI Foundry, è necessario eliminare le risorse create in questo esercizio per evitare di incorrere in costi di Azure non necessari.

1. Tornare alla scheda del browser che contiene il portale di Azure (o riaprire il [portale di Azure](https://portal.azure.com?azure-portal=true) in una nuova scheda del browser) e visualizzare il contenuto del gruppo di risorse in cui sono state distribuite le risorse usate in questo esercizio.
1. Sulla barra degli strumenti selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse e confermarne l'eliminazione.
