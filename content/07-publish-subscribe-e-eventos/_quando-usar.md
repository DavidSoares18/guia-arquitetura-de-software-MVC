**Considere usar quando:**

- Vários componentes precisam reagir ao mesmo fato, e essa lista de interessados muda com o tempo
- A reação pode acontecer alguns instantes depois, sem que o usuário precise esperar por ela
- Uma falha em quem reage não deve impedir a operação principal
- Sistemas de equipes diferentes precisam se integrar sem que uma equipe dependa do calendário da outra

**Evite quando:**

- Quem chama precisa da resposta para continuar, como conferir o estoque antes de confirmar a venda
- A regra exige que todos os dados estejam corretos no mesmo instante
- O sistema é pequeno, mantido por uma só equipe, e as reações são poucas e estáveis
- Ninguém vai operar o broker nem acompanhar a dead letter

**Sinais de que a escolha foi errada:**

- Eventos com nome de ordem, como `EnviarEmail`, em vez de fatos, como `PedidoCriado`
- O publicador fica esperando o assinante responder antes de seguir
- Ninguém na equipe consegue explicar a cadeia completa de eventos de uma operação
- A dead letter acumula mensagens que ninguém olha
