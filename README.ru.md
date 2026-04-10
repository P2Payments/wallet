# p2payments/wallet

Мобильный self-custodial wallet для P2Payments, основанный на форке [Aqua Wallet](https://github.com/AquaWallet/aqua-wallet).

Этот репозиторий существует потому, что Aqua уже даёт сильную базу для нужного нам settlement layer, при этом всё ещё позволяя расширять продукт в сторону специфичных для P2Payments платёжных flows, управления настройками и marketplace-интеграций.

## Why Aqua Wallet

Мы выбрали Aqua Wallet в качестве базы для этого форка по следующим причинам:

- Нативная self-custodial поддержка Bitcoin и Liquid с единым backup seed phrase.
- Поддержка Lightning с swap-to-Liquid flows через Boltz, что уменьшает необходимость для пользователей напрямую управлять ликвидностью каналов.
- Поддержка нескольких stablecoins на базе Liquid, включая сейчас USDT и DePix, которые важны как final-settlement assets для интегрируемых payment rails.
- Встроенные возможности swap между поддерживаемыми валютами.
- Уже существующая модель marketplace внешних приложений, которая может быть полностью заменена нашей собственной встроенной mini app для `/settings`.
- Поддержка нескольких wallets на основе отдельных seed phrases, позволяющая одному и тому же пользователю подключаться, например, к собственной инстанции `/mono` и одновременно к одному или нескольким аккаунтам `/marketplace`.
- Поддержка протокола Shamrock, обеспечивающего простое подключение к BTCPay Server.
- Сильные ожидания долгосрочной поддержки благодаря финансированию Aqua и её репутации в экосистеме.

## Planned changes

Текущий форк должен эволюционировать в мобильный wallet для settlement и configuration в рамках P2Payments.

Планируемые изменения включают:

- Добавление self-custodial поддержки Polygon wallet, производного от существующей seed phrase, чтобы поддерживать USDT в Polygon.
- При необходимости для будущих rails с settlement в USDT на других chains — расширение поддержки через interoperability на базе USDT0, используя Polygon как основную anchor network.
- Замену вкладки marketplace в Aqua на отдельную вкладку **Payments**, работающую на основе Nuxt mini app `/dashboard`.
- Интеграцию обменных курсов от yadio.io, которые лучше отражают реальные цены на развивающихся рынках, чем источники только с exchanges вроде Kraken.
- Постоянное улучшение core functionality с регулярным подтягиванием upstream changes из Aqua.
- Удаление различия между **Saving Account** (BTC) и **Spending Accounts** (другие активы), чтобы лучше соответствовать нашему payment-oriented product model.
- Добавление русского языка и удаление лишних языков, чтобы привести wallet в соответствие с нашим набором языков по умолчанию: EN, ES, PT, FR, IT, RU.
- Ребрендинг приложения как части продуктовой семьи P2Payments.

## USDT on Polygon

Одним из главных планируемых дополнений является self-custodial поддержка USDT в Polygon.

Цель состоит в том, чтобы дать пользователям доступ к settlement asset, который широко применим в интегрируемых payment rails, при этом сохранив ту же модель владения wallet, которая уже используется для Bitcoin и Liquid assets.

Реализация строится вокруг следующих принципов:

- deriving Polygon wallet support from the same existing seed phrase, avoiding separate backup complexity for the user;  
- supporting USDT on Polygon as a first-class settlement asset;  
- using a Flutter EVM stack for Polygon account derivation, signing, and contract interaction, with `web3dart` and `onchainlabs_flutter` currently under evaluation;  
- minimizing user exposure to native-token operational complexity;  
- enabling network fee handling through an ERC-4337 paymaster flow, so fees can be abstracted from the normal user experience and charged in USDT instead of requiring manual MATIC management.  

Это должно сделать settlement в USDT операционно более простым для пользователей, которым нужен final settlement на базе stablecoins, без внесения ненужной фрагментации wallet.  

## `/dashboard` mini app integration

Mini app `/dashboard` будет интегрирована в wallet через подход на основе WebView.

Модель безопасности намеренно проста:

- Flutter остаётся ответственным за custody seed phrase и private keys.
- Встроенное Nuxt app не получает прямого доступа к секретам.
- Из Flutter в слой WebView предоставляется минимальный capability bridge.
- Этот bridge ограничен данными и действиями, необходимыми для payment UI, такими как receiving addresses, базовый wallet context и signing requests.

Это позволяет нам сохранять чувствительный key material внутри native wallet layer, одновременно выстраивая гибкий product interface на Nuxt для flows и settings, специфичных для P2Payments.

## Scope

Этот репозиторий следует воспринимать как mobile wallet layer более широкой архитектуры P2Payments.

Это не просто generic wallet fork. Его назначение:

- self-custodial settlement для Bitcoin, Liquid assets и будущих поддерживаемых rails;
- безопасное подключение к BTCPay Server и связанной payment infrastructure;
- встроенный интерфейс для `/dashboard` и payment-related flows;
- мобильная база для пользователей, взаимодействующих с инстансами `/mono` и подключёнными marketplace environments.
