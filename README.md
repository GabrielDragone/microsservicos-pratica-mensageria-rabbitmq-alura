# microsservicos-pratica-mensageria-rabbitmq-alura
Curso de Microsserviços na prática: mensageria com RabbitMQ.

* Link: https://cursos.alura.com.br/course/microsservicos-pratica-mensageria-rabbitmq/

#### 01 - Requisições assíncronas:
* 01 - Apresentação:
  * Fazer comunicação entre sistemas distribuidos que não necessariamente foram desenvolvidos na mesma linguagem.
  * Trabalharemos como mensageria, formas de roteamento, publicando, consumindo, lidando com falhas, etc.
* 02 - Por que utilizar mensageria:
  * Diferenças entre comunicação síncrona (resposta imediata) x assícrona (resposta posterior, após processamento de outras coisas).
  * No projeto desenvolvido anteriormente nos cursos, foi desenvolvido um circuit Breaker, sistema de fallback, para tratar falhas.
    * Quando o ms estava fora, era pego o pagamento e atualizava o status como CONFIRMADO_SEM_INTEGRACAO.
    * Mas essa responsabilidade não era do ms de pedidos.
  * Outro problema era comunicação direta entre ms's, gerando acoplamento.
  * Para desacoplar, utilizaremos o RabbitMQ e para os demais sistemas que vão consumir e publicar mensagem, pouco importa qual a linguagem que utilizarão, desde que estejam publicando mensagens no padrão do contrato.
* 03 - Conhecendo o RabbitMQ:
  * Message broker: é uma aplicação que funciona como intermediário na gestão de envio e recebimentos de mensagens de forma rápida e confiável.
  * Open Source.
  * Suporte e plugins para várias linguagens.
  * Protocolo principal é AMPQ, mas ele também tem suporte para MQTT, STOMP, HTTP.
    * Advanced Messaging Queue Protocol: Protocolo avançado de enfileramento de mensagens, baseado no TCP.
  * Utiliza padrão de Pub/Sub. Publicador e Consumidor.
  * Trabalharemos com Queues, que podem ser:
    * Duráveis: Se o RabbitMQ cair, as mensagens não são perdidas.
    * Não duráveis: Se o RabbitMQ cair, as mensagens são perdidas.
    * Tamanho máximo de mensagem.
    * Transbordo de mensagens.
    * FIFO: First In, First Out. Primeira mensagem que entra será a primeira a ser entregue. Da pra trabalhar com prioridades.
  * Geralmente quando publicamos uma mensagem, a gente trabalha com exchanges, que é o que chamamos de troca. Ela que é responsável por pegar a mensagem publicada, analisar, processar e descobrir pra qual fila ou quais filas ela tem que direcionar essa mensagem. Existem vários tipos:
    * Default: É a exchange padrão do RabbitMQ. Quando uma mensagem é publicada sem especificar uma exchange, ela é automaticamente enviada para a default exchange. Essa exchange faz o roteamento das mensagens diretamente para as filas com o mesmo nome que a chave de roteamento (routing key).
    * Direct: Nesse tipo de exchange, as mensagens são roteadas com base na chave de roteamento (routing key) exata. A mensagem é entregue apenas para as filas que têm uma binding key (chave de ligação) correspondente à routing key e são entregues de forma balanceada utilizando round-robin.
    * Fanout: Nesse caso, a exchange envia a mensagem para todas as filas vinculadas a ela, independentemente da chave de roteamento. Ou seja, a mensagem é enviada para todas as filas associadas a essa exchange.
    * Topic: Essa exchange usa padrões de correspondência (wildcards) na chave de roteamento para determinar para quais filas a mensagem será enviada. Isso permite um roteamento mais flexível e granular das mensagens.
    * Header: Uma das menos utilizadas que envia baseado no cabeçalho.
* 04 - Para saber mais: protocolos de comunicação:
  * Os protocolos de comunicação são os responsável pela operação no message broker de como as mensagens são enviadas e recebidas.
  * O RabbitMQ possui vários protocolos, entre eles:
    * AMPQ: Advanced Message Queuing Protocol. É o protocolo padrão do RabbitMQ. 
      * Possui inumeras vantagens como:
        * Enfileramento confíavel.
        * Roteamento flexível.
        * Transações.
        * Segurança.
        * Interoperabilidade.
      * Publisher -> Exchange -> Queue -> Consumer.
      * Uma das características desse protocolo é que um publisher nunca publica diretamente na fila.
    * MQTT: Message Queuing Telemetry Transport. É um protocolo de mensagens leve, projetado para situações em que a largura de banda é limitada.
      * É muito utilizado em IoT.
      * Objetivo ser mais simples que o AMQP.
    * STOMP: Simple Text Oriented Messaging Protocol. Protocolo baseado em texto, construído para trabalhar com middlewares orientados à mensagem.
      * Similiar ao AMQP, com cabeçalho, propriedades e corpo da mensagem, porém não lida com tópicos ou filas.
      * Usa semântica de string de destino.
