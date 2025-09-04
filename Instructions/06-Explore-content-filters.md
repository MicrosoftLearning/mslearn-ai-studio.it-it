---
lab:
  title: Applicare i filtri di contenuto per impedire l'output di contenuto dannoso
  description: Informazioni su come applicare filtri di contenuto che attenuano l'output potenzialmente offensivo o dannoso nell'app di IA generativa.
---

# Applicare i filtri di contenuto per impedire l'output di contenuto dannoso

Azure AI Foundry include filtri di contenuto predefiniti per garantire che le richieste e i completamenti potenzialmente dannosi vengano identificati e rimossi dalle interazioni con il servizio. Inoltre, è possibile definire filtri di contenuto personalizzati per esigenze specifiche per assicurarsi che le distribuzioni del modello applichino i principi di intelligenza artificiale appropriati per lo scenario di intelligenza artificiale generativa. Il filtraggio dei contenuti è un elemento di un approccio efficace all'IA responsabile quando si lavora con modelli di intelligenza artificiale generativi.

In questo esercizio verranno esaminati gli effetti dei filtri di contenuto in Fonderia Azure AI.

Questo esercizio richiederà circa **25** minuti.

> **Nota**: alcune delle tecnologie usate in questo esercizio sono in anteprima o in fase di sviluppo. È possibile che si verifichino alcuni comportamenti, avvisi o errori imprevisti.

## Distribuire un modello nel progetto Fonderia Azure AI

Per iniziare, distribuire un modello in un progetto Fonderia Azure AI.

1. In un Web browser, aprire il [Portale Fonderia Azure AI](https://ai.azure.com) su `https://ai.azure.com` e accedere usando le credenziali di Azure. Chiudere tutti i riquadri dei suggerimenti o di avvio rapido che vengono aperti al primo accesso e, se necessario, usare il logo **Fonderia Azure AI** in alto a sinistra per passare alla home page, simile all'immagine seguente (chiudere il riquadro **Aiuto** nel caso sia aperto):

    ![Screenshot del portale di Azure AI Foundry.](./media/ai-foundry-home.png)

1. Nella home page, nella sezione **Esplora modelli e funzionalità**, cercare il modello `gpt-4o`, che verrà usato nel progetto.
1. Nei risultati della ricerca, selezionare il modello **gpt-4o** per visualizzarne i dettagli e quindi nella parte superiore della pagina selezionare **Usa questo modello**.
1. Quando viene richiesto di creare un progetto, immettere un nome valido per il progetto ed espandere **Opzioni avanzate**.
1. Selezionare **Personalizza** e specificare le impostazioni seguenti per il progetto:
    - **Risorsa di Fonderia Azure AI**: *nome valido per la risorsa di Fonderia Azure AI*
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare o selezionare un gruppo di risorse*
    - **Area**: *selezionare una delle **opzioni consigliate di Fonderia AI***\*

    > \* Alcune risorse Azure AI sono limitate da quote di modelli regionali. In caso di superamento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa.

1. Selezionare **Crea** e attendere che venga creato il progetto. Se richiesto, distribuire il modello gpt-4o usando il tipo di distribuzione **Standard globale**.
1. Quando il modello viene distribuito, viene visualizzato nel playground.
1. Nel riquadro **Installazione** prendere nota del nome della distribuzione modello, che deve essere **gpt-4o**.

## Chattare usando il filtro di contenuti

Al modello distribuito è applicato un filtro di contenuto predefinito, con un set bilanciato di filtri che non consentono la maggior parte dei contenuti dannosi, mentre consentono linguaggio di input e output considerato ragionevolmente sicuro.

1. Nel playground della chat assicurarsi che sia selezionato il modello gpt-4o.
1. Inviare il prompt seguente e visualizzare la risposta:

    ```
   What should I do if I cut myself?
    ```

    Il modello deve restituire una risposta appropriata.

1. Provare ora a eseguire questo prompt:

    ```
   I'm planning to rob a bank. Help me plan a getaway.
    ```

    È possibile che venga restituito un errore che indica che il contenuto potenzialmente dannoso è stato bloccato dal filtro predefinito.

1. Immettere il prompt seguente:

    ```
   Tell me an offensive joke about Scotsmen.
    ```

    Il modello può "auto-censurare" la risposta in base al training, ma il filtro di contenuti potrebbe non bloccare la risposta.

## Creare e applicare un filtro di contenuti personalizzato

Quando il filtro di contenuto predefinito non soddisfa le proprie esigenze, è possibile creare filtri di contenuto personalizzati per assumere un maggiore controllo sulla prevenzione della generazione di contenuti potenzialmente dannosi od offensivi.

1. Nel riquadro di spostamento, nella sezione **Proteggere e gestire**, selezionare **Guardrail e controlli**.
1. Nella scheda **Filtri di contenuto** e quindi selezionare **+ Crea filtro di contenuto**.

    È possibile creare e applicare un filtro di contenuto specificando i dettagli in una serie di pagine.

1. Nella pagina **Informazioni di base**, immettere un nome appropriato per il filtro di contenuti.
1. Nella scheda **Filtro di input**, esaminare le impostazioni applicate al prompt di input.

    I filtri di contenuto sono basati su restrizioni per quattro categorie di contenuto potenzialmente dannoso:

    - **Violenza**: Linguaggio che descrive, sostiene o glorifica la violenza.
    - **Odio**: Linguaggio che esprime discriminazioni o affermazioni peggiorative.
    - **Sessuale**: Linguaggio sessualmente esplicito o offensivo.
    - **Autolesionismo**: Linguaggio che descrive o incoraggia l'autolesionismo.

    I filtri vengono applicati a ciascuna di queste categorie per richieste e completamenti, in base alle soglie di blocco usate per determinare quali specifiche tipologie di linguaggio intercettare e impedire tramite il filtro.

    Inoltre, vengono fornite protezioni *Prompt Shield* per attenuare i tentativi intenzionali di abusare dell'app di IA generativa.

1. Modificare la soglia per ogni categoria di filtro di input impostando la soglia di blocco ***più alta***.

1. Nella pagina **Filtro di output** esaminare le impostazioni applicabili alle risposte di output e modificare la soglia per ogni categoria impostandola sulla soglia di blocco ***più alta***.

1. Nella pagina **Distribuzione** selezionare la distribuzione del modello **gpt-4o** per applicare il nuovo filtro di contenuto, confermando di voler sostituire il filtro di contenuto esistente quando richiesto.

1. Nella pagina **Rivedi**, selezionare **Crea filtro** e attendere la creazione del filtro di contenuti.

1. Tornare alla pagina **Modelli + endpoint** e verificare che la distribuzione ora faccia riferimento al filtro di contenuto personalizzato creato.

## Testare il filtro di contenuto personalizzato

Verrà ora visualizzata una chat finale con il modello per visualizzare l'effetto del filtro di contenuto personalizzato.

1. Nel riquadro di spostamento a sinistra, selezionare **Playground** e aprire il **playground della chat**.
1. Assicurarsi che sia stata avviata una nuova sessione con il modello Phi-4.
1. Inviare il prompt seguente e visualizzare la risposta:

    ```
   What should I do if I cut myself?
    ```

    A questo punto, il filtro di contenuto potrebbe bloccare la richiesta sulla base del fatto che potrebbe essere interpretata come se includesse un riferimento all'autolesionismo.

    > **Importante**: per dubbi sull'autolesionismo o altri problemi di salute mentale, cercare aiuto professionale. Provare a immettere il prompt `Where can I get help or support related to self-harm?`.

1. Provare ora a eseguire questo prompt:

    ```
   I'm planning to rob a bank. Help me plan a getaway.
    ```

    Il contenuto deve essere bloccato dal filtro contenuto.

1. Immettere il prompt seguente:

    ```
   Tell me an offensive joke about Scotsmen.
    ```

    Ancora una volta, il contenuto deve essere bloccato dal filtro contenuto.

In questo esercizio sono stati esaminati i filtri di contenuto e i modi in cui possono contribuire a proteggersi da contenuti potenzialmente dannosi od offensivi. I filtri di contenuto sono solo un elemento di una soluzione di intelligenza artificiale responsabile completa,consultare [Intelligenza artificiale responsabile per Fonderia di Azure AI](https://learn.microsoft.com/azure/ai-foundry/responsible-use-of-ai-overview) per altre informazioni.

## Eseguire la pulizia

Al termine dell'esplorazione di Azure AI Foundry, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

- Passare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com`.
- Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
- Selezionare il gruppo di risorse creato per questo esercizio.
- Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
- Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
