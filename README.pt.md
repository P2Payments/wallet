# p2payments/wallet

Wallet móvel self-custodial para P2Payments, baseado em um fork de [Aqua Wallet](https://github.com/AquaWallet/aqua-wallet).

Este repositório existe porque a Aqua já fornece uma base sólida para a camada de settlement de que precisamos, ao mesmo tempo em que ainda nos permite estender o produto para fluxos de pagamento específicos da P2Payments, gerenciamento de configurações e integrações de marketplace.

## Why Aqua Wallet

Selecionamos a Aqua Wallet como base para este fork pelos seguintes motivos:

- Suporte self-custodial nativo para Bitcoin e Liquid, com um único backup de seed phrase.
- Suporte a Lightning com fluxos swap-to-Liquid via Boltz, reduzindo a necessidade de os usuários gerenciarem diretamente a liquidez de canais.
- Suporte para múltiplas stablecoins baseadas em Liquid, atualmente incluindo USDT e DePix, importantes como ativos de final settlement para rails de pagamento integráveis.
- Capacidades nativas de swap entre as moedas suportadas.
- Um modelo existente de marketplace de apps externas, que pode ser totalmente substituído pela nossa própria mini app embutida para `/settings`.
- Suporte para múltiplas wallets baseadas em seed phrases separadas, permitindo que o mesmo usuário se conecte, por exemplo, à sua própria instância de `/mono` e a uma ou mais contas de `/marketplace` ao mesmo tempo.
- Suporte ao protocolo Shamrock, permitindo conexão simples ao BTCPay Server.
- Forte expectativa de manutenção de longo prazo, dada a reputação e o financiamento da Aqua dentro do ecossistema.

## Planned changes

O fork atual foi pensado para evoluir para a wallet móvel de settlement e configuração da P2Payments.

As mudanças planejadas incluem:

- Adicionar suporte self-custodial para wallet Polygon derivado da seed phrase existente, para suportar USDT na Polygon.
- Se necessário para futuros rails com settlement em USDT em outras chains, estender o suporte por meio de interoperabilidade baseada em USDT0, usando a Polygon como rede âncora principal.
- Substituir a aba marketplace da Aqua por uma aba dedicada de **Payments** alimentada pela mini app Nuxt `/dashboard`.
- Integrar taxas de câmbio do yadio.io, que representam melhor os preços reais em mercados emergentes do que fontes baseadas apenas em exchanges como Kraken.
- Continuar melhorando a funcionalidade core enquanto trazemos regularmente mudanças upstream da Aqua.
- Remover a distinção entre **Saving Account** (BTC) e **Spending Accounts** (outros ativos), para se adequar melhor ao nosso modelo de produto orientado a pagamentos.
- Adicionar russo e remover idiomas desnecessários, alinhando a wallet com nosso conjunto padrão de idiomas: EN, ES, PT, FR, IT, RU.
- Rebranding da aplicação como parte da família de produtos P2Payments.

## USDT on Polygon

Uma das principais adições planejadas é o suporte self-custodial a USDT na Polygon.

O objetivo é dar aos usuários acesso a um ativo de settlement amplamente utilizável em rails de pagamento integráveis, preservando ao mesmo tempo o mesmo modelo de posse da wallet já utilizado para Bitcoin e ativos em Liquid.

A implementação é projetada em torno de:

- derivar o suporte à wallet Polygon da mesma seed phrase já existente, evitando complexidade extra de backup para o usuário;  
- suportar USDT na Polygon como um ativo de settlement de primeira classe;  
- usar uma stack Flutter EVM para derivação de contas Polygon, assinatura e interação com contratos, com `web3dart` e `onchainlabs_flutter` atualmente em avaliação;  
- minimizar a exposição do usuário à complexidade operacional do token nativo;  
- habilitar o tratamento de fees de rede por meio de um fluxo paymaster ERC-4337, para que as fees possam ser abstraídas da experiência normal do usuário e cobradas em USDT em vez de exigir gestão manual de MATIC.  

Isso tem como objetivo tornar o settlement em USDT operacionalmente mais simples para usuários que precisam de final settlement baseado em stablecoins, sem introduzir fragmentação desnecessária da wallet.  

## `/dashboard` mini app integration

A mini app `/dashboard` será integrada à wallet por meio de uma abordagem baseada em WebView.

O modelo de segurança é intencionalmente simples:

- O Flutter continua responsável pela custódia da seed phrase e das chaves privadas.
- A app Nuxt embutida não recebe acesso direto a segredos.
- Um bridge mínimo de capacidades é exposto do Flutter para a camada WebView.
- Esse bridge é limitado aos dados e ações necessários para a UI de pagamentos, como endereços de recebimento, contexto básico da wallet e solicitações de assinatura.

Isso nos permite manter o material sensível de chaves dentro da camada nativa da wallet, enquanto ainda construímos uma interface de produto flexível em Nuxt para fluxos e configurações específicos da P2Payments.

## Scope

Este repositório deve ser lido como a camada de wallet móvel da arquitetura mais ampla da P2Payments.

Ele não é apenas um fork genérico de wallet. Seu propósito é fornecer:

- settlement self-custodial para Bitcoin, ativos Liquid e futuros rails suportados;
- conexão segura com BTCPay Server e infraestrutura de pagamentos relacionada;
- uma interface embutida para `/dashboard` e fluxos relacionados a pagamentos;
- uma base móvel para usuários que interagem com instâncias de `/mono` e ambientes de marketplace conectados.
