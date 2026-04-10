# p2payments/wallet

Wallet mobile self-custodial per P2Payments, basato su un fork di [Aqua Wallet](https://github.com/AquaWallet/aqua-wallet).

Questo repository esiste perché Aqua fornisce già una base solida per il layer di settlement di cui abbiamo bisogno, consentendoci allo stesso tempo di estendere il prodotto verso flussi di pagamento specifici di P2Payments, gestione delle impostazioni e integrazioni marketplace.

## Why Aqua Wallet

Abbiamo scelto Aqua Wallet come base di questo fork per i seguenti motivi:

- Supporto self-custodial nativo per Bitcoin e Liquid, con un unico backup della seed phrase.
- Supporto Lightning con flussi swap-to-Liquid tramite Boltz, riducendo la necessità per gli utenti di gestire direttamente la liquidità dei canali.
- Supporto per multiple stablecoin basate su Liquid, attualmente incluse USDT e DePix, importanti come asset di final settlement per rail di pagamento integrabili.
- Funzionalità di swap integrate tra le valute supportate.
- Un modello esistente di marketplace per app esterne, che può essere completamente sostituito dalla nostra mini app embeddeda per `/settings`.
- Supporto per wallet multipli basati su seed phrase separate, permettendo allo stesso utente di collegarsi, per esempio, alla propria istanza di `/mono` e a uno o più account `/marketplace` allo stesso tempo.
- Supporto per il protocollo Shamrock, che consente una connessione semplice a BTCPay Server.
- Forti aspettative di manutenzione a lungo termine, data la reputazione e il finanziamento di Aqua all’interno dell’ecosistema.

## Planned changes

L’attuale fork è pensato per evolversi nel wallet mobile di settlement e configurazione di P2Payments.

Le modifiche pianificate includono:

- Aggiunta del supporto self-custodial per wallet Polygon derivato dalla seed phrase esistente, per supportare USDT su Polygon.
- Se necessario per futuri rail con settlement in USDT su altre chain, estensione del supporto tramite interoperabilità basata su USDT0, usando Polygon come rete ancora principale.
- Sostituzione della tab marketplace di Aqua con una tab dedicata **Payments** alimentata dalla mini app Nuxt `/dashboard`.
- Integrazione dei tassi di cambio di yadio.io, più rappresentativi dei prezzi reali nei mercati emergenti rispetto a fonti basate solo su exchange come Kraken.
- Miglioramento continuo delle funzionalità core integrando regolarmente i cambiamenti upstream di Aqua.
- Rimozione della distinzione tra **Saving Account** (BTC) e **Spending Accounts** (altri asset), per adattarsi meglio al nostro modello di prodotto orientato ai pagamenti.
- Aggiunta del russo e rimozione delle lingue non necessarie, allineando il wallet al nostro set linguistico predefinito: EN, ES, PT, FR, IT, RU.
- Rebranding dell’applicazione come parte della famiglia di prodotti P2Payments.

## USDT on Polygon

Una delle principali aggiunte pianificate è il supporto self-custodial a USDT su Polygon.

L’obiettivo è dare agli utenti accesso a un asset di settlement ampiamente utilizzabile attraverso rail di pagamento integrabili, preservando allo stesso tempo lo stesso modello di possesso del wallet già usato per Bitcoin e gli asset Liquid.

L’implementazione è progettata attorno a:

- derivare il supporto del wallet Polygon dalla stessa seed phrase esistente, evitando ulteriore complessità di backup per l’utente;  
- supportare USDT su Polygon come asset di settlement di prima classe;  
- utilizzare uno stack Flutter EVM per derivazione degli account Polygon, firma e interazione con smart contract, con `web3dart` e `onchainlabs_flutter` attualmente in valutazione;  
- minimizzare l’esposizione dell’utente alla complessità operativa del token nativo;  
- abilitare la gestione delle fee di rete tramite un flusso paymaster ERC-4337, così che le fee possano essere astratte dalla normale esperienza utente e addebitate in USDT invece di richiedere gestione manuale di MATIC.  

Questo ha l’obiettivo di rendere il settlement in USDT operativamente più semplice per utenti che hanno bisogno di final settlement basato su stablecoin, senza introdurre frammentazione non necessaria del wallet.  

## `/dashboard` mini app integration

La mini app `/dashboard` verrà integrata nel wallet tramite un approccio basato su WebView.

Il modello di sicurezza è intenzionalmente semplice:

- Flutter resta responsabile della custodia della seed phrase e delle chiavi private.
- L’app Nuxt embeddeda non riceve accesso diretto ai segreti.
- Un bridge minimo di capability viene esposto da Flutter al layer WebView.
- Quel bridge è limitato ai dati e alle azioni necessari per la UI di pagamento, come indirizzi di ricezione, contesto base del wallet e richieste di firma.

Questo ci consente di mantenere il materiale sensibile delle chiavi all’interno del layer nativo del wallet, continuando allo stesso tempo a costruire un’interfaccia di prodotto flessibile in Nuxt per flussi e impostazioni specifici di P2Payments.

## Scope

Questo repository va letto come il layer wallet mobile della più ampia architettura P2Payments.

Non è solo un fork generico di wallet. Il suo scopo è fornire:

- settlement self-custodial per Bitcoin, asset Liquid e futuri rail supportati;
- connessione sicura a BTCPay Server e alla relativa infrastruttura di pagamento;
- un’interfaccia embeddeda per `/dashboard` e flussi correlati ai pagamenti;
- una base mobile per utenti che interagiscono con istanze `/mono` e ambienti marketplace connessi.
