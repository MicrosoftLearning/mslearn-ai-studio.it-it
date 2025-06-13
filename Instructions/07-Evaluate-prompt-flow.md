---
lab:
  title: Valutare le prestazioni del modello dell'IA generativa
  description: Informazioni su come valutare i modelli e le richieste per ottimizzare le prestazioni dell'app di chat e la sua capacità di rispondere in modo appropriato.
---

# Valutare le prestazioni del modello dell'IA generativa

In questo esercizio, verranno usate valutazioni manuali e automatizzate per valutare le prestazioni di un modello nel portale Fonderia Azure AI.

Questo esercizio richiederà circa **30** minuti.

> **Nota**: alcune delle tecnologie usate in questo esercizio sono in anteprima o in fase di sviluppo. È possibile che si verifichino alcuni comportamenti, avvisi o errori imprevisti.

## Creare un hub e un progetto Fonderia Azure AI

Le funzionalità di Fonderia Azure AI che verranno usate in questo esercizio richiedono un progetto basato su una risorsa *hub* di Fonderia Azure AI.

1. In un Web browser, aprire il [Portale Fonderia Azure AI](https://ai.azure.com) su `https://ai.azure.com` e accedere usando le credenziali di Azure. Chiudere tutti i riquadri dei suggerimenti o di avvio rapido che vengono aperti al primo accesso e, se necessario, usare il logo **Fonderia Azure AI** in alto a sinistra per passare alla home page, simile all'immagine seguente (chiudere il riquadro **Aiuto** nel caso sia aperto):

    ![Screenshot del portale di Azure AI Foundry.](./media/ai-foundry-home.png)

1. Nel browser, passare a `https://ai.azure.com/managementCenter/allResources` e selezionare **Crea**. Scegliere quindi l'opzione per creare una nuova **risorsa Hub IA**.
1. Nella procedura guidata **Crea un progetto**, immettere un nome valido per il progetto e, se è suggerito un hub esistente, selezionare l'opzione per crearne uno nuovo ed espandere **Opzioni avanzate** per specificare le impostazioni seguenti per il progetto:
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: *creare o selezionare un gruppo di risorse*
    - **Nome hub**: un nome valido per l'hub
    - **Posizione**: Selezionare una delle seguenti posizioni (*In caso di superamento di un limite di quota più avanti nell'esercizio, potrebbe essere necessario creare un'altra risorsa in un'area diversa*):
        - Stati Uniti orientali 2
        - Francia centrale
        - Regno Unito meridionale
        - Svezia centrale

    > **Nota**: se si usa una sottoscrizione di Azure in cui i criteri vengono usati per limitare i nomi di risorse consentiti, potrebbe essere necessario usare il collegamento nella parte inferiore della finestra di dialogo **Crea un nuovo progetto** per creare l'hub usando il portale di Azure.

    > **Suggerimento**: se il pulsante **Crea** è ancora disabilitato, assicurarsi di rinominare l'hub in un valore alfanumerico univoco.

1. Attendere la creazione del progetto.

## Distribuire i modelli

In questo esercizio, verranno valutate le prestazioni di un modello gpt-4o-mini. Verrà usato anche un modello gpt-4o per generare metriche di valutazione assistita dall'intelligenza artificiale.

1. Nel riquadro di spostamento a sinistra del progetto, nella sezione **Asset personali** selezionare la pagina **Modelli + endpoint**.
1. Nella scheda **Distribuzioni del modello** della pagina **Modelli + endpoint**, nel menu **+ Distribuisci modello** selezionare **Distribuisci modello di base**.
1. Cercare il modello **gpt-4o** nell'elenco e quindi selezionarlo e confermarlo.
1. Distribuire il modello con le impostazioni seguenti selezionando **Personalizza** nei dettagli della distribuzione:
    - **Nome distribuzione**: *nome univoco per la distribuzione del modello*
    - **Tipo di distribuzione**: standard globale
    - **Aggiornamento automatico della versione**: abilitato
    - **Versione del modello**: *selezionare la versione più recente disponibile*
    - **Risorsa di intelligenza artificiale connessa**: *selezionare la connessione alla risorsa Azure OpenAI*
    - **Token al limite di velocità al minuto (migliaia)**: 50.000 *(o il valore massimo disponibile nella sottoscrizione se inferiore a 50.000)*
    - **Filtro contenuto**: predefinitoV2

    > **Nota**: la riduzione del TPM consente di evitare l'eccessivo utilizzo della quota disponibile nella sottoscrizione in uso. 50.000 TPM dovrebbero essere sufficienti per i dati usati in questo esercizio. Se la quota disponibile è inferiore a questa, sarà possibile completare l'esercizio, ma potrebbero verificarsi errori se viene superato il limite di velocità.

1. Attendere il completamento della distribuzione.
1. Tornare alla pagina **Modelli + endpoint** e ripetere i passaggi precedenti per distribuire un modello **gpt-4o-mini** con le stesse impostazioni.

## Valutare manualmente un modello

È possibile esaminare manualmente le risposte del modello in base ai dati dei test. L'esame manuale consente di testare input diversi per valutare se il modello funziona come previsto.

1. In una nuova scheda del browser, scaricare il file [travel_evaluation_data.jsonl](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl) da `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl` e salvarlo in una cartella locale come **travel_evaluation_data.jsonl** (assicurarsi di salvarlo come file JSONL, non come file TXT).
1. Tornare alla scheda del portale Fonderia Azure AI, nel riquadro di spostamento, nella sezione **Proteggere e gestire**, selezionare **Valutazione**.
1. Se si apre automaticamente il riquadro **Crea una nuova valutazione** , selezionare **Annulla** per chiuderlo.
1. Nella pagina **Valutazione**, visualizzare la scheda **Valutazioni manuali** e selezionare **+ Nuova valutazione manuale**.
1. Nella sezione **Configurazioni**, nell'elenco **Modello**, selezionare la distribuzione modello **gpt-4o**.
1. Modificare il **messaggio di sistema** con le istruzioni seguenti per un assistente viaggio di intelligenza artificiale:

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. Nella sezione **Risultato della valutazione manuale**, selezionare **Importa dati di test** e caricare il file **travel_evaluation_data.jsonl** scaricato in precedenza. Scorrere verso il basso per eseguire il mapping dei campi del set di dati come indicato di seguito:
    - **Input**: domanda
    - **Risposta prevista**: ExpectedResponse
1. Esaminare le domande e le risposte previste nel file di test: verranno usate per valutare le risposte generate dal modello.
1. Selezionare **Esegui** dalla barra superiore per generare output per tutte le domande aggiunte come input. Dopo alcuni minuti, le risposte del modello devono essere visualizzate in una nuova colonna **Output**, come illustrato di seguito:

    ![Screenshot della pagina di valutazioni manuali nel portale Fonderia di Azure AI.](./media/manual-evaluation.png)

1. Esaminare gli output per ogni domanda, confrontando l'output del modello con la risposta prevista e "assegnando dei punteggi" ai risultati selezionando l'icona pollice verso l'alto o verso il basso in basso a destra di ogni risposta.
1. Dopo aver ottenuto il punteggio delle risposte, esaminare i riquadri di riepilogo sopra l'elenco. Nella barra degli strumenti, selezionare **Salva risultati** e assegnare un nome appropriato. Il salvataggio dei risultati consente di recuperarli in un secondo momento per un'ulteriore valutazione o un confronto con un modello diverso.

## Usare valutazioni automatizzate

Mentre il confronto manuale dell'output del modello con le risposte previste può essere un modo utile per valutare le prestazioni di un modello, è un approccio dispendioso in termini di tempo in scenari in cui è attesa un'ampia gamma di domande e risposte; e fornisce poco nel modo delle metriche standardizzate che è possibile usare per confrontare diverse combinazioni di modelli e prompt.

La valutazione automatizzata è un approccio che tenta di risolvere queste carenze calcolando le metriche e usando l'IA per valutare le risposte per coerenza, pertinenza e altri fattori.

1. Usare la freccia indietro (**&larr;**) accanto al titolo pagina **Valutazione manuale** per tornare alla pagina **Valutazione**.
1. Visualizzare la scheda **Valutazioni automatiche**.
1. Selezionare **Crea una nuova valutazione**, e quando richiesto, selezionare l'opzione per valutare **Valuta un modello** e selezionare **Avanti**.
1. Nella pagina **Seleziona origine dati** selezionare **Usa il set di dati** e selezionare il set di dati **travel_evaluation_data_jsonl_*xxxx...*** in base al file caricato in precedenza e selezionare **Avanti**.
1. Nella pagina **Testa il modello** selezionare il modello **gpt-4o-mini** e modificare il **messaggio di sistema** con le stesse istruzioni per un assistente viaggio di intelligenza artificiale usato in precedenza:

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. Per il campo **query**, selezionare **\{\{item.question\}\}**
1. Selezionare **Avanti** per passare alla pagina successiva.
1. Nella pagina **Configura valutatori**, usare il pulsante **+Aggiungi** per aggiungere i valutatori seguenti, configurandoli nel modo seguente:
    - **Strumento di assegnazione dei punteggi del modello**:
        - **Nome dei criteri**: Semantic_similarity
        - **Voto con**: *selezionare il modello **gpt-4o***
        - Impostazioni **utente** (nella parte inferiore):


            Output: \{\{sample.output_text\}\}<br>
            Verità di riferimento: \{\{item.ExpectedResponse\}\}<br>
            <br>
        
    - **Analizzatore di scala likert**:
        - **Nome criteri**: pertinenza
        - **Voto con**: *selezionare il modello **gpt-4o***
        - **Query**: \{\{item.question\}\}

    - **Somiglianza del testo**:
        - **Nome criteri**: F1_Score
        - **Verità di riferimento**: \{\{item. ExpectedResponse\}\}

    - **Contenuto odioso e fazioso**:
        - **Nome dei criteri**: Hate_and_unfairness
        - **Query**: \{\{item.question\}\}

1. Selezionare **Avanti** per rivedere le impostazioni di valutazione. È necessario aver configurato la valutazione per usare il set di dati di valutazione del viaggio per valutare il modello **gpt-4o-mini** per la somiglianza semantica, la pertinenza, il punteggio F1 e il linguaggio odioso e fazioso.
1. Assegnare alla valutazione un nome appropriato e **inviarla** per avviare il processo di valutazione e attendere il completamento. L'operazione potrebbe richiedere alcuni minuti. È possibile usare il pulsante **Aggiorna** della barra degli strumenti per controllare lo stato.

1. Al termine della valutazione, scorrere verso il basso, se necessario per esaminare i risultati.

    ![Screenshot delle metriche di valutazione.](./media/ai-quality-metrics.png)

1. Nella parte superiore della pagina, selezionare la scheda **Dati** per visualizzare i dati non elaborati dalla valutazione. I dati includono le metriche per ogni input e le spiegazioni del ragionamento che il modello gpt-4o ha applicato durante la valutazione delle risposte.

## Eseguire la pulizia

Al termine dell'esplorazione di Azure AI Foundry, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

- Passare al [portale di Azure](https://portal.azure.com) all'indirizzo `https://portal.azure.com`.
- Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
- Selezionare il gruppo di risorse creato per questo esercizio.
- Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
- Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
