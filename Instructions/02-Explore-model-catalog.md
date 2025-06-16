---
lab:
  title: Scegliere e distribuire un modello linguistico
  description: Le applicazioni di IA generativa si basano su uno o più modelli linguistici. Informazioni su come trovare e selezionare i modelli appropriati per il progetto di IA generativa.
---

# Scegliere e distribuire un modello linguistico

Il catalogo dei modelli in Azure AI Foundry funge da repository centrale in cui è possibile esplorare e usare un'ampia gamma di modelli, semplificando la creazione dello scenario di intelligenza artificiale generativa.

In questo esercizio verrà esaminerà il catalogo dei modelli nel portale AI Azure Foundry e verranno confrontati i modelli potenziali per un'applicazione di IA generativa che consente di risolvere i problemi.

Questo esercizio richiederà circa **25** minuti.

> **Nota**: alcune delle tecnologie usate in questo esercizio sono in anteprima o in fase di sviluppo. È possibile che si verifichino alcuni comportamenti, avvisi o errori imprevisti.

## Esplorare i modelli

Per iniziare, accedere al portale Fonderia Azure AI ed esplorare alcuni dei modelli disponibili.

1. In un Web browser, aprire il [Portale Fonderia Azure AI](https://ai.azure.com) su `https://ai.azure.com` e accedere usando le credenziali di Azure. Chiudere tutti i riquadri dei suggerimenti o di avvio rapido che vengono aperti al primo accesso e, se necessario, usare il logo **Fonderia Azure AI** in alto a sinistra per passare alla home page, simile all'immagine seguente (chiudere il riquadro **Aiuto** nel caso sia aperto):

    ![Screenshot del portale di Azure AI Foundry.](./media/ai-foundry-home.png)

1. Esaminare le informazioni nella home page.
1. Nella home page, nella sezione **Esplora modelli e funzionalità**, cercare il modello `gpt-4o`, che verrà usato nel progetto.
1. Nei risultati della ricerca, selezionare il modello **gpt-4o** per visualizzarne i dettagli.
1. Leggere la descrizione ed esaminare le altre informazioni disponibili nella scheda **Dettagli**.

    ![Screenshot della pagina dei dettagli del modello gpt-4o.](./media/gpt4-details.png)

1. Nella pagina **gpt-4o**, visualizzare la scheda **Benchmark** per vedere in che modo il modello si confronta con altri modelli usati in scenari simili, in base ad alcuni indicatori delle prestazioni standard.

    ![Screenshot della pagina degli indicatori di modello di gpt-4o.](./media/gpt4-benchmarks.png)

1. Usare la freccia indietro (**&larr;**) accanto al titolo pagina **gpt-4o** per tornare al catalogo dei modelli.
1. Cercare `Phi-3.5-mini-instruct` e visualizzare i dettagli e i benchmark per il modello **Phi-3.5-mini-instruct**.

## Confrontare i modelli

Sono stati esaminati due modelli diversi, entrambi utilizzabili per implementare un'applicazione di chat di IA generativa. Ora si confrontano visivamente le metriche per questi due modelli.

1. Usare la freccia indietro (**&larr;**) per tornare al catalogo dei modelli.
1. Selezionare **Confronta modelli**. Viene visualizzato un grafico visivo per il confronto tra modelli con una selezione di modelli comuni.

    ![Screenshot della pagina di confronto modelli.](./media/compare-models.png)

1. Nel riquadro **Modelli da confrontare**, notare che è possibile selezionare le attività più diffuse, ad esempio la *risposta alla domanda*, per selezionare automaticamente i modelli di uso comune per attività specifiche.
1. Usare l'icona **Cancella tutti i modelli** (&#128465;) per rimuovere tutti i modelli pre-selezionati.
1. Usare il pulsante **+ Modello da confrontare** per aggiungere il modello **GPT-4o** all'elenco. Usare quindi lo stesso pulsante per aggiungere il modello **Phi-3.5-mini-instruct** all'elenco.
1. Esaminare il grafico, che confronta i modelli in base all'**indice di qualità** (un punteggio standardizzato che indica la qualità del modello) e al **costo**. È possibile visualizzare i valori specifici per un modello tenendo il mouse sul punto che lo rappresenta nel grafico.

    ![Screenshot del grafico di confronto tra modelli per GPT-4o e Phi-3.5-mini-instruct.](./media/comparison-chart.png)

1. Nel menu a discesa **Asse X**, in **Qualità**, selezionare le metriche seguenti e osservare ogni grafico risultante prima di passare al successivo:
    - Precisione
    - Coerenza
    - Scorrevolezza
    - Pertinenza

    In base ai benchmark, il modello gpt-4o sembra offrire le migliori prestazioni complessive, ma a un costo più elevato.

1. Nell'elenco dei modelli da confrontare, selezionare il modello **gpt-4o** per riaprire la pagina dei benchmark.
1. Nella pagina del modello **gpt-4o** selezionare la scheda **Panoramica** per visualizzare i dettagli del modello.

## Creare un progetto Fonderia Azure AI

Per usare un modello, è necessario creare un *progetto* Fonderia Azure AI.

1. Nella parte superiore della pagina di panoramica del modello **gpt-4o** selezionare **Usa questo modello**.
1. Quando viene richiesto di creare un progetto, immettere un nome valido per il progetto ed espandere **Opzioni avanzate**.
1. Nella sezione **Opzioni avanzate**, specificare le impostazioni seguenti per il progetto:
    - **Risorsa di Fonderia Azure AI**: *nome valido per la risorsa di Fonderia Azure AI*
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare o selezionare un gruppo di risorse*
    - **Area geografica**: *selezionare qualsiasi **località supportata per i servizi di intelligenza artificiale**\*

    > \* Alcune risorse Azure AI sono limitate da quote di modelli regionali. In caso di superamento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa.

1. Selezionare **Crea** e attendere la creazione del progetto, inclusa la distribuzione del modello gpt-4 selezionato.
1. Quando viene creato il progetto, il playground della chat verrà aperto automaticamente in modo da poter testare il modello:

    ![Screenshot del playground della chat di Fonderia Azure AI.](./media/ai-foundry-chat-playground.png)

## Chattare con il modello *gpt-4o*

Ora che è disponibile una distribuzione del modello, è possibile usare il playground per testarlo.

1. Nel playground della chat, nel riquadro **Impostazione**, assicurarsi che il modello **gpt-4o** sia selezionato e nel campo **Assegnare le istruzioni e il contesto del modello** impostare il prompt di sistema su `You are an AI assistant that helps solve problems.`
1. Selezionare **Applica modifiche** per aggiornare il prompt di sistema.

1. Nella finestra della chat, immettere la query seguente

    ```
   I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Visualizzare la risposta. Immettere quindi la query di completamento seguente:

    ```
   Explain your reasoning.
    ```

## Distribuire un altro modello

Dopo aver creato il progetto, il modello **gpt-4o** selezionato è stato distribuito automaticamente. Distribuiamo ora anche il modello ***Phi-3.5-mini-instruct** considerato.

1. Nella barra di spostamento a sinistra, nella sezione **Risorse personali**, selezionare **Modelli + endpoint**.
1. Nell'elenco a discesa **+ Distribuisci modello** della scheda **Distribuzioni modelli**, selezionare **Distribuisci modello di base**. Quindi cercare `Phi-3.5-mini-instruct` e confermare la selezione.
1. Accettare la licenza del modello.
1. Distribuire un modello **Phi-3.5-mini-instruct** con le impostazioni seguenti:
    - **Nome distribuzione**: *nome univoco per la distribuzione del modello*
    - **Tipo di distribuzione**: standard globale
    - **Dettagli della distribuzione**: *usare le impostazioni predefinite*

1. Attendere il completamento della distribuzione.

## Chattare con il modello *Phi-3.5*

A questo punto è possibile chattare con il nuovo modello nel playground.

1. Nella barra di spostamento selezionare **Playground**. Quindi selezionare il **playground chat**.
1. Nel playground della chat, nel riquadro **Installazione** assicurarsi che il modello **Phi-3.5-mini-instruct** sia selezionato e nel campo **Assegna istruzioni e contesto**, impostare il prompt di sistema su `You are an AI assistant that helps solve problems.` (lo stesso prompt di sistema usato per testare il modello gpt-4o).
1. Selezionare **Applica modifiche** per aggiornare il prompt di sistema.
1. Assicurarsi che venga avviata una nuova sessione di chat prima di ripetere le stesse richieste usate in precedenza per testare il modello GPT-4.
1. Nella finestra della chat, immettere la query seguente

    ```
   I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Visualizzare la risposta. Immettere quindi la query di completamento seguente:

    ```
   Explain your reasoning.
    ```

## Eseguire un ulteriore confronto

1. Usare l'elenco a discesa nel riquadro **Installazione** per passare da un modello all'altro, testando entrambi i modelli con il puzzle seguente (la risposta corretta è 40!):

    ```
   I have 53 socks in my drawer: 21 identical blue, 15 identical black and 17 identical red. The lights are out, and it is completely dark. How many socks must I take out to make 100 percent certain I have at least one pair of black socks?
    ```

## Riflettere sui modelli

Sono stati confrontati due modelli, che possono variare sia in termini di capacità di generare risposte appropriate sia in termini costo. In qualsiasi scenario generativo, è necessario trovare un modello con il giusto equilibrio tra l'idoneità all'attività da eseguire e il costo di utilizzo del modello in base al numero di richieste che si prevede debba gestire.

I dettagli e i benchmark forniti nel catalogo dei modelli, insieme alla possibilità di confrontare visivamente i modelli, offrono un punto di partenza utile nell'identificazione dei modelli candidati per una soluzione di IA generativa. È quindi possibile testare i modelli candidati con un'ampia gamma di richieste di sistema e utente nel playground della chat.

## Eseguire la pulizia

Al termine dell'esplorazione del portale Azure AI Foundry, è necessario eliminare le risorse create in questo esercizio per evitare di incorrere in costi di Azure non necessari.

1. Aprire il [portale di Azure](https://portal.azure.com) e visualizzare il contenuto del gruppo di risorse in cui sono state distribuite le risorse usate in questo esercizio.
1. Sulla barra degli strumenti selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse e confermarne l'eliminazione.
