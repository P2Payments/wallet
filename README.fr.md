# p2payments/wallet

Wallet mobile self-custodial pour P2Payments, basé sur un fork de [Aqua Wallet](https://github.com/AquaWallet/aqua-wallet).

Ce dépôt existe parce qu’Aqua fournit déjà une base solide pour la couche de settlement dont nous avons besoin, tout en nous permettant d’étendre le produit vers des flux de paiement spécifiques à P2Payments, la gestion des paramètres et les intégrations marketplace.

## Why Aqua Wallet

Nous avons choisi Aqua Wallet comme base de ce fork pour les raisons suivantes :

- Support self-custodial natif pour Bitcoin et Liquid, avec une sauvegarde unique de seed phrase.
- Support Lightning avec des flux swap-to-Liquid via Boltz, réduisant le besoin pour les utilisateurs de gérer directement la liquidité des canaux.
- Support de plusieurs stablecoins basés sur Liquid, incluant actuellement USDT et DePix, qui sont importants comme actifs de final settlement pour des rails de paiement intégrables.
- Capacités de swap intégrées entre les devises prises en charge.
- Un modèle existant de marketplace d’applications externes, qui peut être entièrement remplacé par notre propre mini app embarquée pour `/settings`.
- Support de plusieurs wallets basés sur des seed phrases séparées, permettant au même utilisateur de se connecter, par exemple, à sa propre instance `/mono` et à un ou plusieurs comptes `/marketplace` en même temps.
- Support du protocole Shamrock, permettant une connexion simple à BTCPay Server.
- Fortes attentes de maintenance à long terme, compte tenu du financement d’Aqua et de sa réputation dans l’écosystème.

## Planned changes

Le fork actuel est destiné à évoluer vers le wallet mobile de settlement et de configuration de P2Payments.

Les changements prévus incluent :

- L’ajout du support d’un wallet Polygon self-custodial dérivé de la seed phrase existante, afin de prendre en charge USDT sur Polygon.
- Si nécessaire pour de futurs rails réglant en USDT sur d’autres chains, l’extension du support via une interopérabilité basée sur USDT0, en utilisant Polygon comme réseau d’ancrage principal.
- Le remplacement de l’onglet marketplace d’Aqua par un onglet **Payments** dédié, alimenté par la mini app Nuxt `/dashboard`.
- L’intégration de taux de change provenant de yadio.io, plus représentatifs des prix réels dans les marchés émergents que des sources uniquement basées sur des exchanges comme Kraken.
- L’amélioration continue des fonctionnalités core tout en intégrant régulièrement les changements upstream d’Aqua.
- La suppression de la distinction entre **Saving Account** (BTC) et **Spending Accounts** (autres actifs), afin de mieux correspondre à notre modèle de produit orienté paiement.
- L’ajout du russe et la suppression des langues inutiles, afin d’aligner le wallet sur notre ensemble de langues par défaut : EN, ES, PT, FR, IT, RU.
- Le rebranding de l’application comme partie de la famille de produits P2Payments.

## USDT on Polygon

L’un des principaux ajouts prévus est le support self-custodial de l’USDT sur Polygon.

L’objectif est de donner aux utilisateurs accès à un actif de settlement largement utilisable à travers des rails de paiement intégrables, tout en préservant le même modèle de propriété du wallet déjà utilisé pour Bitcoin et les actifs Liquid.

L’implémentation est conçue autour des points suivants :

- dériver le support du wallet Polygon à partir de la même seed phrase existante, afin d’éviter une complexité de sauvegarde supplémentaire pour l’utilisateur ;  
- prendre en charge l’USDT sur Polygon comme actif de settlement de première classe ;  
- utiliser une stack Flutter EVM pour la dérivation de comptes Polygon, la signature et l’interaction avec les contrats, avec `web3dart` et `onchainlabs_flutter` actuellement en cours d’évaluation ;  
- minimiser l’exposition de l’utilisateur à la complexité opérationnelle du token natif ;  
- permettre la gestion des frais réseau via un flux paymaster ERC-4337, afin que les frais puissent être abstraits de l’expérience utilisateur normale et facturés en USDT au lieu d’exiger une gestion manuelle de MATIC.  

Cela vise à rendre le settlement en USDT opérationnellement plus simple pour les utilisateurs qui ont besoin d’un final settlement basé sur des stablecoins, sans introduire une fragmentation inutile du wallet.  

## `/dashboard` mini app integration

La mini app `/dashboard` sera intégrée au wallet via une approche basée sur WebView.

Le modèle de sécurité est volontairement simple :

- Flutter reste responsable de la garde de la seed phrase et des clés privées.
- L’app Nuxt embarquée ne reçoit pas d’accès direct aux secrets.
- Un bridge minimal de capacités est exposé depuis Flutter vers la couche WebView.
- Ce bridge est limité aux données et actions nécessaires à l’UI de paiement, comme les adresses de réception, le contexte de base du wallet et les demandes de signature.

Cela nous permet de conserver les éléments sensibles de clé dans la couche native du wallet tout en construisant une interface produit flexible en Nuxt pour les flux et paramètres spécifiques à P2Payments.

## Scope

Ce dépôt doit être lu comme la couche de wallet mobile de l’architecture plus large de P2Payments.

Ce n’est pas simplement un fork générique de wallet. Son objectif est de fournir :

- un settlement self-custodial pour Bitcoin, les actifs Liquid et les futurs rails pris en charge ;
- une connexion sécurisée à BTCPay Server et à l’infrastructure de paiement associée ;
- une interface embarquée pour `/dashboard` et les flux liés aux paiements ;
- une base mobile pour les utilisateurs interagissant avec des instances `/mono` et des environnements marketplace connectés.
