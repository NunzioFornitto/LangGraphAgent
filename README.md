# LangGraph-Agent
Studenti: *Angelo Frasca, Nunzio Fornitto, Fernando Riccioli*

## Introduzione
È stato realizzato un agente per la creazione di post per un blog di calcio. L'agente realizza una bozza di articolo con i pronostici per la prossima giornata della Serie A e formula tre idee per post futuri. L'agente è stato realizzato tramite   LangGraph e implementa quattro nodi: caricamento dei dati, generazione della bozza, riflessione e valutazione.   

## Flusso dell'Agente
- **Nel nodo di caricamento dei dati**, l'agente utilizza dei tool per ottenere i dati della Serie A da API. I tool sono implementati tramite decorator e vengono chiamati in maniera statica tramite `nome_tool.invoke("descrizione")`. Tutti i tool comprendono docstring, un parametro di input e il type int per l'output. Si è preferito utilizzare i tool invocati staticamente per rimanere coerenti con il flusso di funzionamento del programma. I dati che si ottengono tramite i tool sono fondamentali per l'esecuzione del programma e quindi si è preferito questo metodo piuttosto dell'utilizzo del binding.


- **Nel nodo di generazione** della bozza vengono anche generate le tre idee per i post futuri. Il modello viene chiamato utilizzando un prompt che implementa alcune tecniche di prompting avanzate come il role-prompting, il chain-of-thought e il constraint-prompting.

- **Nel nodo di reflection**, il modello riceve la bozza generata dal nodo precedente e fornisce una critica dettagliata. Anche questa critica viene generata usando delle tecniche di prompting avanzate che sono commentate esplicitamente nel codice (ad esempio l'adversarial-prompting).  Infine la critica viene inserita nello stato e l'agente torna nuovamente al nodo di generazione. Il nodo di generazione, infatti, comprende anche l'uso di un secondo prompt che viene chiamato durante il ciclo di reflection. In particolare il modello utilizza il commento critico ottenuto per migliorare la bozza precedente. Il numero di iterazioni del ciclo di reflection è modificabile tramite i multipli di due all'interno della funzione di routing `should_continue`. Con un parametro pari a 2, il ciclo viene eseguito una volta. In particolare, dopo che il nodo di generazione è stato utilizzato per migliorare la bozza si passa al nodo di evaluation.   

- **Nel nodo di evaluation** il modello verifica che la bozza sia adatta alla pubblicazione. Il modello controlla se le linee guida specifiche nel prompt di questo nodo vengono rispettate. Se il post supera la valutazione termina il flusso dell'agente. Altrimenti l'agente torna nuovamente al nodo di generation, viene resettato il ciclo di reflection precedente e comincia da capo. Ciò significa che si tornerà nuovamente nel ciclo di reflection.    

## Retrieval-Augmented Generation
È stata utilizzato anche la Retrieval Augmented Generation tramite un database vettoriale. I documenti presi dal web e trasformati in embedding riguardano metodi per generazione accurata di pronostici calcistici.
