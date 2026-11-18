| Atributo | Efeito | Por quê |
|---|---|---|
| Extensibilidade | Favorecida | A nota fiscal entrou como mais um assinante de `PedidoCriado`, sem editar uma linha da loja |
| Tempo de resposta | Favorecido | O cliente espera só o estoque (cerca de 0,01 s), e não a soma dos quatro serviços (cerca de 0,43 s) |
| Isolamento de falhas | Favorecido | Com o e-mail fora do ar, a venda é concluída e só a mensagem de notificação vai para a dead letter |
| Disponibilidade | Favorecida | Com a fidelidade em manutenção, os eventos esperam na fila e os pontos são creditados quando o serviço volta |
| Consistência | Comprometida | Entre a resposta ao cliente e o processamento dos assinantes, pedido e pontos discordam por um tempo (consistência eventual) |
| Correção dos assinantes | Comprometida | Com entrega "pelo menos uma vez", cada assinante precisa tolerar duplicatas e ordem trocada, com receptor idempotente e versão do evento |
| Rastreabilidade | Comprometida | A sequência de uma venda não está escrita em nenhum trecho de código; só o log do broker permite reconstruí-la |
| Simplicidade operacional | Comprometida | Broker e dead letter viram peças a operar e monitorar, e uma falha deixa de ser visível para quem publicou |
