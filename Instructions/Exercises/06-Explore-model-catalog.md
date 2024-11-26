---
lab:
  title: Esplorare il catalogo dei modelli in Studio AI della piattaforma Azure
---

# Esplorare il catalogo dei modelli in Studio AI della piattaforma Azure

Il catalogo dei modelli in Studio AI della piattaforma Azure funge da repository centrale in cui è possibile esplorare e usare un'ampia gamma di modelli, semplificando la creazione dello scenario di intelligenza artificiale generativa.

In questo esercizio verrà esaminato il catalogo dei modelli in Studio AI della piattaforma Azure.

> **Nota**: Studio AI della piattaforma Azure è in fase di anteprima nel momento in cui viene scritto questo testo ed è in corso di sviluppo attivo. Alcuni elementi del servizio potrebbero non essere esattamente come descritto e alcune funzionalità potrebbero non dare gli effetti previsti.

> Per completare questo esercizio, la sottoscrizione di Azure dovrà disporre dell'approvazione per l'accesso al servizio OpenAI di Azure.

Questo esercizio richiederà circa **25** minuti.

## Creare un hub di Azure per intelligenza artificiale

Per ospitare i progetti, la sottoscrizione di Azure dovrà disporre di un hub di Azure per intelligenza artificiale. È possibile creare questa risorsa durante la creazione di un progetto o eseguirne il provisioning in anticipo (ovvero ciò che verrà fatto in questo esercizio).

1. In un Web browser, aprire [https://ai.azure.com](https://ai.azure.com) e accedere usando le credenziali di Azure.

1. Nella sezione Gestione, selezionare Tutti gli hub e quindi **+ Nuovo hub**. Creare un nuovo hub con le impostazioni seguenti:
    - **Nome hub**: *un nome univoco*.
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare un nuovo gruppo di risorse con un nome univoco o selezionarne uno esistente*
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
    - **Connettere Servizi di Azure AI o OpenAI di Azure**: selezionare per creare un nuovo servizio di intelligenza artificiale o usarne uno esistente
    - **Connettere Azure AI Search**: ignorare la connessione

    > \* Le risorse OpenAI di Azure sono vincolate dalle quote regionali a livello tenant. Le aree elencate includono la quota predefinita per i tipi di modello usati in questo esercizio. La scelta casuale di un'area riduce il rischio che una singola area raggiunga il limite di quota negli scenari in cui si condivide un tenant con altri utenti. In caso di raggiungimento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa.

1. Seleziona **Crea**. Il completamento della creazione del primo hub può richiedere alcuni minuti. Durante la creazione dell'hub, verranno create automaticamente anche le risorse di intelligenza artificiale seguenti: 
    - Servizi di intelligenza artificiale
    - Account di archiviazione
    - Key vault

1. Dopo che l'hub di Azure per intelligenza artificiale è stato creato, il risultato dovrebbe essere simile all'immagine seguente:

    ![Screenshot di un hub di Azure per intelligenza artificiale in Studio AI della piattaforma Azure.](./media/azure-ai-overview.png)

## Creare un progetto

Un hub di Intelligenza artificiale di Azure offre un'area di lavoro collaborativa in cui è possibile definire uno o più *progetti*. Creare un progetto nell'hub di Azure per intelligenza artificiale.

1. Nella pagina **Compilazione** di Studio AI della piattaforma Azure, selezionare **+ Nuovo progetto**. Quindi, nella procedura guidata **Creazione di un nuovo progetto**, creare un progetto con le impostazioni seguenti:

    - **Nome progetto**: *un nome univoco per il progetto*
    - **Hub**: *l'hub di intelligenza artificiale in uso*

1. Attendere la creazione del progetto. Il risultato dovrebbe avere un aspetto simile all'immagine seguente.

    ![Screenshot dei dettagli di un progetto in Studio AI della piattaforma Azure.](./media/azure-ai-project.png)

1. Visualizzare le pagine nel riquadro a sinistra, espandendo ogni sezione e annotando le attività che è possibile eseguire e le risorse che è possibile gestire in un progetto.

## Distribuire un modello

A questo punto è possibile distribuire un modello da usare tramite **Studio AI della piattaforma Azure**. Dopo la distribuzione, si userà il modello per generare contenuto in linguaggio naturale.

1. In Studio AI della piattaforma Azure creare una nuova distribuzione con le impostazioni seguenti:

    - **Modello**: gpt-35-turbo
    - **Tipo di distribuzione**: Standard
    - **Risorsa OpenAI di Azure connessa**: *connessione OpenAI di Azure*
    - **Versione modello**: Aggiornamento automatico per impostazione predefinita
    - **Nome distribuzione**: *nome univoco di propria scelta*
    - **Opzioni avanzate**
        - **Filtro contenuto**: Predefinito
        - **Limite di velocità dei token al minuto**: 5K

> **Nota**: ogni modello di Studio AI della piattaforma Azure è ottimizzato per un equilibrio diverso tra funzionalità e prestazioni. In questo esercizio si userà il modello **GPT 3.5 Turbo**, che è altamente in grado di generare e chattare in linguaggio naturale.

## Testare il modello selezionato nel playground di chat

Vediamo come si comporta il modello in un'interazione conversazionale.

1. Passare a **Playground** nel riquadro di sinistra.

1. In modalità **Chat**, immettere il prompt seguente nella sezione **Sessione di chat**.

    ```
   <Placeholder>
    ```

1. Il modello risponderà probabilmente con un testo ...

## Modificare il messaggio di prompt del sistema

Nel playground di chat è possibile modificare il prompt di sistema per modificare il comportamento del modello. Il prompt di sistema è il messaggio iniziale ricevuto dal modello prima di iniziare a generare risposte.

1. Nella sezione **Messaggio di sistema** modificare il messaggio di sistema nel testo seguente:

    ```
    <Placeholder>
    ```

1. Applicare le modifiche apportate al messaggio di sistema.

1. Nella sezione **Sessione di chat** immettere nuovamente il prompt seguente.

    ```
   <Placeholder>
    ```

8. Osservare l'output, che dovrebbe indicare che ...


## Aggiungere i dati al playground di chat

<Placeholder>

## Eseguire la pulizia

Terminato l’utilizzo della risorsa OpenAI di Azure, ricordarsi di eliminare la distribuzione o l'intera risorsa nel [portale di Azure](https://portal.azure.com/?azure-portal=true).

