# p2payments/wallet

Wallet móvil self-custodial para P2Payments, basado en un fork de [Aqua Wallet](https://github.com/AquaWallet/aqua-wallet).

Este repositorio existe porque Aqua ya proporciona una base sólida para la capa de settlement que necesitamos, y al mismo tiempo nos permite extender el producto hacia flujos de pago específicos de P2Payments, gestión de configuración e integraciones de marketplace.

## Why Aqua Wallet

Seleccionamos Aqua Wallet como base de este fork por las siguientes razones:

- Soporte self-custodial nativo para Bitcoin y Liquid, con un único backup de seed phrase.
- Soporte de Lightning con flujos swap-to-Liquid mediante Boltz, reduciendo la necesidad de que los usuarios gestionen directamente la liquidez de canales.
- Soporte para múltiples stablecoins basadas en Liquid, actualmente incluyendo USDT y DePix, que son importantes como activos de final settlement para rieles de pago integrables.
- Capacidades de swap integradas entre las monedas soportadas.
- Un modelo existente de marketplace de apps externas, que puede ser reemplazado por completo por nuestra propia mini app embebida para `/settings`.
- Soporte para múltiples wallets basadas en seed phrases separadas, permitiendo que el mismo usuario se conecte, por ejemplo, a su propia instancia de `/mono` y a una o más cuentas de `/marketplace` al mismo tiempo.
- Soporte para el protocolo Shamrock, que permite una conexión simple con BTCPay Server.
- Fuertes expectativas de mantenimiento a largo plazo, dada la financiación y reputación de Aqua dentro del ecosistema.

## Planned changes

El fork actual está pensado para evolucionar hacia el wallet móvil de settlement y configuración de P2Payments.

Los cambios planificados incluyen:

- Añadir soporte self-custodial para wallet Polygon derivado de la seed phrase existente, con el fin de soportar USDT en Polygon.
- Si fuera necesario para futuros rieles con settlement en USDT sobre otras chains, extender el soporte mediante interoperabilidad basada en USDT0, usando Polygon como red ancla principal.
- Reemplazar la pestaña marketplace de Aqua por una pestaña dedicada de **Payments** impulsada por la mini app Nuxt `/dashboard`.
- Integrar tipos de cambio de yadio.io, que representan mejor los precios reales en mercados emergentes que fuentes basadas únicamente en exchanges como Kraken.
- Seguir mejorando la funcionalidad core mientras se incorporan regularmente cambios upstream de Aqua.
- Eliminar la distinción entre **Saving Account** (BTC) y **Spending Accounts** (otros activos), para ajustarse mejor a nuestro modelo de producto orientado a pagos.
- Añadir ruso y eliminar idiomas innecesarios, alineando el wallet con nuestro conjunto de idiomas por defecto: EN, ES, PT, FR, IT, RU.
- Rebranding de la aplicación como parte de la familia de productos P2Payments.

## USDT on Polygon

Una de las principales incorporaciones planificadas es el soporte self-custodial de USDT en Polygon.

El objetivo es dar a los usuarios acceso a un activo de settlement ampliamente utilizable a través de rieles de pago integrables, preservando al mismo tiempo el mismo modelo de propiedad del wallet ya utilizado para Bitcoin y los activos de Liquid.

La implementación está diseñada en torno a:

- derivar el soporte de wallet Polygon a partir de la misma seed phrase existente, evitando complejidad adicional de backup para el usuario;  
- soportar USDT en Polygon como un activo de settlement de primera clase;  
- utilizar un stack Flutter EVM para derivación de cuentas Polygon, firma e interacción con contratos, con `web3dart` y `onchainlabs_flutter` actualmente en evaluación;  
- minimizar la exposición del usuario a la complejidad operativa del token nativo;  
- habilitar la gestión de fees de red mediante un flujo paymaster ERC-4337, de modo que las fees puedan abstraerse de la experiencia normal del usuario y cobrarse en USDT en lugar de requerir gestión manual de MATIC.  

Esto busca hacer que el settlement en USDT sea operativamente más simple para usuarios que necesitan final settlement basado en stablecoins, sin introducir fragmentación innecesaria del wallet.  

## `/dashboard` mini app integration

La mini app `/dashboard` se integrará en el wallet mediante un enfoque basado en WebView.

El modelo de seguridad es intencionalmente simple:

- Flutter sigue siendo responsable de la custodia de la seed phrase y las claves privadas.
- La app Nuxt embebida no recibe acceso directo a secretos.
- Se expone desde Flutter hacia la capa WebView un bridge mínimo de capacidades.
- Ese bridge se limita a los datos y acciones necesarios para la UI de pagos, como direcciones de recepción, contexto básico del wallet y solicitudes de firma.

Esto nos permite mantener el material sensible de claves dentro de la capa nativa del wallet, mientras seguimos construyendo una interfaz de producto flexible en Nuxt para flujos y configuraciones específicas de P2Payments.

## Scope

Este repositorio debe leerse como la capa de wallet móvil de la arquitectura más amplia de P2Payments.

No es simplemente un fork genérico de wallet. Su propósito es proporcionar:

- settlement self-custodial para Bitcoin, activos de Liquid y futuros rieles soportados;
- conexión segura con BTCPay Server y la infraestructura de pagos relacionada;
- una interfaz embebida para `/dashboard` y flujos relacionados con pagos;
- una base móvil para usuarios que interactúan con instancias de `/mono` y entornos de marketplace conectados.
