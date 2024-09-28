---
lab:
  title: Esplorare i componenti e gli strumenti di Studio AI dalla piattaforma Azure
---

# Esplorare i componenti e gli strumenti di Studio AI dalla piattaforma Azure

In questo esercizio Studio AI della piattaforma Azure viene usato per creare un progetto ed esplorare un modello di intelligenza artificiale generativa.

Questo esercizio richiede circa **30** minuti.

## Prima di iniziare

Per completare questo esercizio, la sottoscrizione di Azure deve essere approvata per l'accesso al Servizio OpenAI di Azure. Compilare il [modulo di registrazione](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) per richiedere l'accesso ai modelli OpenAI di Azure.

## Aprire Studio AI della piattaforma Azure

Per iniziare, si esaminerà Studio AI della piattaforma Azure.

1. In un Web browser, aprire [https://ai.azure.com](https://ai.azure.com) e accedere usando le credenziali di Azure. La home page di Studio AI della piattaforma Azure si presenta come l'immagine seguente:

    ![Screenshot di Studio AI della piattaforma Azure](./media/azure-ai-studio-home.png)

1. Esaminare le informazioni nella home page e visualizzare ognuna delle schede, osservando le opzioni per esplorare modelli e funzionalità, creare progetti e gestire le risorse.

## Creare un hub di Azure per intelligenza artificiale

Per ospitare i progetti, la sottoscrizione di Azure dovrà disporre di un hub di Azure per intelligenza artificiale. È possibile creare questa risorsa durante la creazione di un progetto o eseguirne il provisioning in anticipo (ovvero ciò che verrà fatto in questo esercizio).

1. Nella sezione **Gestione**, selezionare **Tutti gli hub** e quindi **+ Nuovo hub**. Creare un nuovo hub con le impostazioni seguenti:
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

## Distribuire e testare il modello

È possibile usare un progetto per creare soluzioni di intelligenza artificiale complesse basate su modelli di intelligenza artificiale generativa. Un'esplorazione completa di tutte le opzioni di sviluppo disponibili in Studio AI della piattaforma Azure esula dall'ambito di questo esercizio, ma verranno esaminati alcuni semplici modi in cui è possibile usare i modelli in un progetto.

1. Nel riquadro a sinistra del progetto selezionare la pagina **Distribuzioni** nella sezione **Componenti**.
1. Nella pagina **Distribuzioni**, nella scheda **Distribuzioni del modello**, selezionare ** + Crea distribuzione**.
1. Cercare il modello **gpt-35-turbo** dall'elenco, selezionare e confermare.
1. Distribuire il modello con le impostazioni seguenti:
    - **Nome distribuzione**: *Nome univoco per la distribuzione del modello*
    - **Versione del modello**: *selezionare la versione predefinita*
    - **Tipo di distribuzione**: Standard
    - **Risorsa OpenAI di Azure connessa**: *selezionare la connessione predefinita creata al momento della creazione dell'hub*
    - **Limite di velocità dei token al minuto (migliaia)**: 5K
    - **Filtro contenuto**: Predefinito

    > **Nota**: la riduzione del TPM consente di evitare l'over-using della quota disponibile nella sottoscrizione in uso. 5.000 TPM è sufficiente per i dati usati in questo esercizio.

1. Dopo aver distribuito il modello, nella pagina di panoramica della distribuzione selezionare **Apri nel playground**.
1. Nella pagina **playground della chat** assicurarsi che la distribuzione del modello sia selezionata nella sezione **Distribuzione**.
1. Nella finestra della chat immettere una query, ad esempio *Che cos'è l'intelligenza artificiale?* e visualizzare la risposta:

    ![Screenshot del playground in Studio AI della piattaforma Azure.](./media/playground.png)

## Eseguire la pulizia

Al termine dell'esplorazione di Studio AI dalla piattaforma Azure, è necessario eliminare le risorse create in questo esercizio per evitare di incorrere in costi di Azure non necessari.

1. Tornare alla scheda del browser contenente il portale di Azure (o riaprire il [portale di Azure](https://portal.azure.com?azure-portal=true) in una nuova scheda del browser) e visualizzare il contenuto del gruppo di risorse in cui sono state distribuite le risorse usate in questo esercizio.
1. Sulla barra degli strumenti selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse e confermarne l'eliminazione.
