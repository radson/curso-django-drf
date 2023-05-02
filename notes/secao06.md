# Seção 06 - Django Staticfiles: Arquivos estáticos (imagens, css, javascript, etc)

## 37. Sobre arquivos estáticos

Arquivos estáticos são arquivos que são entregues exatamente como estão salvos. Esses arquivos quase não sofrem alterações, por isso, o navegador pode salvá-los em cache para que o conteúdo da página carregue mais rapidamente para o usuário final. Alguns provedores de Internet também adicionam servidores no meio da rede que também podem fazer cache desse arquivos, assim o próprio provedor economiza sua banda de Internet externa.

Frequentemente, sites adicionam seu conteúdo estático em CDNs (Content Delivery Network) que são servidores otimizados para entrega de arquivos estáticos tanto em velocidade de entrega quando em manter os arquivos na localização mais próxima do usuário final.

O Django consegue lidar com arquivos estáticos no seu servidor ou em CDNs.
