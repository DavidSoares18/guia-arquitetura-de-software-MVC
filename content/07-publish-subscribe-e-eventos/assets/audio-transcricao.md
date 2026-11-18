# Transcrição: Desacoplamento com arquitetura baseada em eventos

Resumo em Áudio (formato "Análise detalhada") gerado no NotebookLM a partir do texto deste tópico, em português do Brasil, com 14 min 38 s de duração. A transcrição foi feita pelo próprio NotebookLM, e os termos técnicos que o reconhecimento de fala errou foram corrigidos (por exemplo, "Rabq ou Cafica" para "RabbitMQ ou Kafka", "Oer" para "Eugster" e "idem potente" para "idempotente"). Cada linha é uma fala; os dois apresentadores se alternam.

---

Hoje o nosso mergulho vai ser um pouco diferente. A gente vai direto ao ponto no tópico 07 do Guia Aberto de Arquitetura de Software.

Uhum. É um material super rico demais. E a nossa missão de hoje é traduzir uns conceitos meio densos, sabe? Para quem é estudante de graduação e tá tendo o primeiro contato com esse assunto.

Nossa, essencial. É aquele momento que a teoria bate de frente com a prática, né?

Exatamente. E para isso a gente vai usar um exemplo bem clássico e prático que tá lá no texto. Imagina uma loja virtual, tá? O cenário padrão de comércio.

Isso. O cliente vai lá e clica em finalizar compra. Nesse momento, o sistema precisa fazer quatro coisas básicas. Ele precisa baixar o estoque, mandar um e-mail de confirmação, creditar pontos no sistema de fidelidade e, por fim, atualizar a tela do painel de Meus pedidos.

É o que parece simples na superfície, mas por baixo do capô é outra história.

Pois é, porque a abordagem mais tradicional, aquela que a gente aprende no começo, é criar uma função gigante de venda que chama esses quatro serviços em sequência, um depois do outro.

O famoso monolito acoplado.

Exato. É tipo um garçom de restaurante que precisa cozinhar o prato, depois servir. Aí ele mesmo cobra a conta e ainda limpa a mesa no final, tudo sozinho.

E se ele tropeçar numa dessas etapas, a operação inteira trava.

É aí que tá. Se o servidor de e-mail cair, a venda inteira falha. O cliente fica lá esperando o tempo somado de todos esses serviços e no fim toma um erro na cara.

O que é péssimo pros negócios. Nenhuma loja quer perder venda porque o e-mail não enviou.

Com certeza, não. Então a grande questão é: se essa chamada direta, sequencial, não escala e quebra tão fácil, qual é a alternativa?

Então, aí a gente entra na mudança de paradigma pra comunicação dos sistemas. A gente começa a falar de arquitetura baseada em eventos, tá? Mas, ó, tem uma confusão muito comum aqui que a gente precisa limpar logo de cara. Eu vejo muita gente usando os termos publish-subscribe, ou pub/sub, e arquitetura baseada em eventos como se fossem a mesma coisa.

Nossa, sim, o tempo todo. E é muito importante esclarecer isso. Publish-subscribe é só o mecanismo de comunicação. É a tecnologia bruta, tipo o encanamento da casa.

Perfeito. É o cano. Significa que um componente publica uma mensagem e quem assinou aquele canal recebe. Só isso. Já a arquitetura baseada em eventos, a EDA, é uma decisão muito maior.

É a planta da casa inteira.

Exato. É você decidir organizar todo o sistema em torno desse mecanismo. É um estilo arquitetural completo. E esse modelo tem quatro peças principais.

Quais são? Primeiro, o evento em si, que é o fato ocorrido. Segundo, o publicador, que no nosso exemplo seria a própria loja entregando a mensagem, certo?

Isso mesmo. Terceiro, a gente tem o broker, ou o canal, que é o intermediário. Pensa num RabbitMQ ou num Kafka da vida, que recebe essa mensagem e distribui. E quarto, os assinantes, os serviços de notificação, o sistema de fidelidade e tal.

Perfeitamente.

Perfeito.

Eles ficam lá só escutando.

Agora tem um ponto do texto que eu acho fantástico e que muda o jogo. O evento tem que ser um fato, não uma ordem.

Isso é crucial.

Um evento descreve algo que já aconteceu, passado, né?

Por isso que a mensagem se chama PedidoCriado, no particípio passado, e não EnviarEmail ou AtualizarFidelidade.

Uhum. Se você usa o evento como se fosse um comando disfarçado, mandando alguém fazer algo, você traz de volta exatamente aquele problema de acoplamento que a gente estava tentando evitar.

É, o Martin Fowler até cita isso, né? Se a loja manda um EnviarEmail, ela tem que saber que o sistema de e-mail existe. Ela volta a ser o garçom sobrecarregado, de certa forma.

Com certeza.

A loja só tem que gritar pro mundo: "Olha, um PedidoCriado aconteceu. Quem tiver interesse, que reaja". Mas pensando aqui com a cabeça de quem tá estudando padrões de projeto, se a loja anuncia algo e quem tem interesse reage, isso não é exatamente o padrão Observer que a galera estuda no MVC?

Parece muito, mas tem diferenças fundamentais. E para explicar isso, a gente usa as três dimensões de desacoplamento que o Eugster e os coautores dele definem, tá? Espaço, tempo e sincronização, certo?

Exatamente. No padrão Observer clássico, quem quer ser avisado se registra diretamente no objeto que tá sendo observado.

Ou seja, eles estão acoplados no espaço, porque um guarda a referência de memória do outro.

Sim. E acoplados no tempo também, porque ambos precisam existir e estar rodando simultaneamente para a notificação acontecer. Se um cai, o outro não consegue avisar.

Ah, entendi.

E no pub/sub, com o broker no meio, a coisa muda de figura totalmente.

Essa lista de interessados sai lá do publicador e vai morar no broker.

Então, na dimensão do espaço, a loja virtual não faz a menor ideia de que o serviço de fidelidade sequer existe.

Zero ideia.

E no tempo, se o sistema de fidelidade tiver fora do ar por manutenção, a loja publica o evento do mesmo jeito. O evento fica ali seguro, aguardando na fila do broker.

E é a sincronização.

A loja joga a mensagem pro broker e imediatamente já responde pro cliente na tela. Ela não fica travada esperando o e-mail ser enviado ou o ponto ser creditado.

É muito elegante. Mas lendo o exemplo da loja virtual no texto, eu reparei numa coisa que parece uma contradição.

Pode falar.

Se a regra de ouro é desacoplar tudo através de eventos no broker, por que o serviço de estoque não virou um assinante? No código de exemplo, a loja ainda chama o estoque diretamente.

Ah, essa é a exceção que prova a regra. Existe uma premissa básica nesse estilo arquitetural. Se a resposta ao cliente depende daquele resultado para fazer sentido, a chamada tem que ser direta, tá? Porque a loja não pode dizer pro cliente "compra confirmada" sem ter certeza se aquela caneca realmente existe lá no galpão, né?

Imagina a dor de cabeça. Se o estoque fosse um evento assíncrono, a loja confirmaria a venda. Aí dois clientes poderiam acabar comprando a última caneca ao mesmo tempo.

Ia ser um desastre.

O sistema ia ter que rodar um processo de estorno, mandar um e-mail de desculpas, frustrar o cliente.

Exato.

Então, se é uma verificação vital pro negócio acontecer, é chamada direta. Se é apenas uma reação a algo que já aconteceu de fato, aí sim é evento.

Muito bom. E para quem acha que isso é só teoria, o texto traz umas medições reais de um notebook Python, e eu confesso que eu fiquei impressionado com os números.

Os dados são bem reveladores mesmo.

Sim. Falando de dependências, a loja acoplada original conhecia quatro serviços. Aí imagina que o setor fiscal pediu para incluir a emissão de nota fiscal.

O que acontece na versão acoplada?

O código da loja teve que ser alterado, testado de novo, e passou a conhecer cinco serviços. Um pesadelo de manutenção.

E na versão com eventos, a loja continua conhecendo só dois, o estoque e o broker. A nota fiscal entrou no sistema apenas assinando o tópico PedidoCriado. Ninguém tocou numa linha de código da loja.

Isso é o sonho de qualquer equipe de engenharia. Extensibilidade limpa.

E o tempo de resposta caiu de cerca de 0,43 segundo para absurdos 0,01 segundo.

Porque o cliente só fica esperando a validação do estoque e o aceno do broker. O resto acontece nos bastidores. E a resiliência também brilha aqui. Eles simularam o servidor de e-mail caindo na venda acoplada. Mesmo com a caneca já separada no estoque, a venda falhava.

Dinheiro jogado fora.

Com os eventos, a venda foi concluída bonitinha em 0,01 segundo. O e-mail tentou ser enviado, falhou três vezes e foi parar na dead letter, que é aquela fila de mensagens mortas para alguém olhar depois.

E o sistema de fidelidade?

Esse foi melhor ainda. Deixaram ele em manutenção durante três vendas. O sistema acoplado simplesmente recusou as três vendas. Um prejuízo.

Uhum.

Na versão baseada em eventos, as três vendas passaram liso, e quando o serviço de fidelidade voltou ao ar, ele sugou a fila e 150 pontos foram creditados de uma vez. Só retroativamente.

É o cenário perfeito.

Parece mágica, né? Mas a gente que é de tecnologia sabe que não existe almoço grátis na arquitetura de software. O que acontece quando esse sistema assíncrono maravilhoso sai dos trilhos?

É aí que entra o pedágio do assincronismo. Tem uma terceira regra no estilo pub/sub que é amarga de engolir. O assinante não pode assumir entrega única e nem ordem de chegada.

Nossa, como assim?

A maioria dos brokers comerciais garante a entrega pelo menos uma vez. Eles podem e vão repetir mensagens. Garantir que uma mensagem chegue exatamente uma única vez de forma distribuída custa uma latência muito alta, tá? E aí a gente tem a primeira violação, que é a entrega duplicada. O texto detalha isso super bem. Imagina que o serviço de fidelidade recebe o evento, credita os 50 pontos do cliente no banco de dados...

E aí a rede pisca.

Exato. A confirmação de recebimento, o tal do ACK, se perde antes de chegar de volta no broker.

O broker não recebe a resposta e pensa: "Opa, falhou, vou mandar de novo".

E aí ele manda de novo.

O serviço de fidelidade processa a mesma mensagem e credita mais 50 pontos. Um pedido de R$ 50 gerou 100 pontos por engano.

Um caos financeiro silencioso.

E a correção para isso é aplicar o padrão do receptor idempotente, o Idempotent Receiver.

Que é jogar a responsabilidade pro assinante, né?

Isso. O serviço que recebe a mensagem precisa guardar o ID dos eventos que ele já tratou. Quando chega a mensagem duplicada, ele olha no banco, vê o ID, ignora o processamento e avisa o broker que tá tudo certo.

Resolvido. Os mesmos R$ 50 geram os 50 pontos corretos, mesmo se o broker entregar a mensagem dez vezes.

Mas tem outro fantasma aí.

A ordem trocada.

Ih, verdade.

A segunda violação. Imagina a cena. Uma cliente compra um produto, gera o evento PedidoCriado. Dois segundos depois ela se arrepende e cancela. Gera o evento PedidoCancelado.

Ambos vão pro broker.

Sim. Aí o serviço que atualiza o painel de Meus pedidos puxa o PedidoCriado. Só que o banco de dados dele dá uma travada bem na hora e não responde.

O que acontece? Essa mensagem falha, volta pro fim da fila e fica atrás da mensagem de PedidoCancelado.

Exatamente. Aí o painel processa o cancelamento primeiro. O pedido não existe ainda, mas ele tenta cancelar. Depois ele puxa a criação e processa.

E a tela do usuário mostra "confirmado" para um pedido que a cliente acabou de cancelar.

O suporte ao cliente chora numa hora dessas. E como eles corrigiram isso lá na fonte?

A solução é incluir um número de versão do pedido dentro do evento. Assim o painel compara. Se o evento que chegou for mais antigo, com uma versão menor do que a última que ele já aplicou, ele simplesmente ignora.

Genial. Mas parando para pensar, nenhuma dessas falhas complexas existia na versão de chamadas diretas. Elas são puramente o preço da entrega assíncrona.

É o trade-off clássico.

Então, pra gente fazer uma balança de decisão pra nossa audiência de estudantes que vai ter que tomar essa decisão de arquitetura no mundo real, quais são os prós e os contras, de forma bem resumida?

Bom, os benefícios são claros: extensibilidade gigante, tipo o caso de colocar a nota fiscal sem alterar nada; tempo de resposta muito menor pro usuário final; e o isolamento de falhas, onde um erro no e-mail não derruba a venda principal. E a fatura que chega, os custos disso?

O primeiro é a consistência eventual.

Tem uma janela de tempo, que podem ser milissegundos ou horas, em que a tela diz que tá confirmado, mas os pontos da fidelidade ainda não caíram. Os sistemas discordam temporariamente.

E as falhas silenciosas, né?

Sim. Se ninguém da equipe fica monitorando ativamente aquela dead letter, o e-mail do cliente simplesmente nunca chega e ninguém da empresa percebe que tem algo quebrado.

E para mim, acho que a pior parte pro desenvolvedor no dia a dia é o fluxo implícito.

É verdade. Dificulta muito o raciocínio.

Ninguém consegue mais abrir o código da função finalizar_pedido e ler uma historinha de tudo o que o sistema faz. A lógica espalhou. Para debugar um erro, você tem que virar um detetive de infraestrutura.

Além disso, tem o fato de que eventos viram contratos. Se você precisa mudar o formato do evento PedidoCriado no futuro, adicionar ou tirar um campo, você tem que coordenar isso com as cinco equipes diferentes que assinam esse evento.

Sem contar o custo financeiro e operacional de manter e monitorar um cluster de broker rodando em produção o tempo todo.

É, e não é para qualquer projeto.

Tem alguma regra de ouro? Um teste rápido na hora de decidir?

Tem sim. Uma dica super prática. Bateu a dúvida se algo deve ser um evento? Escreve o nome da ação no quadro. Se sai naturalmente no passado, tipo um fato consumado, e interessar a mais de um componente, manda ver, é um evento.

Mas e se soar como uma ordem?

Se soar como uma ordem, ou se quem tá publicando precisa desesperadamente da resposta imediatamente para poder continuar trabalhando...

Tipo o caso do estoque.

Exato. Aí é uma chamada direta disfarçada. Foge do evento, que vai dar dor de cabeça.

Sensacional. Olha, foi uma jornada e tanto hoje. A gente pegou aquele sistema que era um garçom sobrecarregado, com chamadas diretas que quebravam por qualquer coisinha.

Uhum. A esteira frágil.

Isso. E transformamos num sistema altamente desacoplado, super rápido na resposta, resiliente a quedas, mas que, claro, exige lidar com a complexidade de tempo, repetições, ordem trocada e essas filas todas nos bastidores.

É o preço da engenharia de grande porte. E eu queria deixar uma provocação sobre isso.

Manda.

A gente falou que eventos viram contratos, né? E que, por definição técnica, um evento é um fato imutável do passado.

Sim. O que aconteceu, aconteceu.

Exato. Mas as regras de negócio das empresas, os formatos de dados, estão sempre mudando. Então, como é que uma arquitetura consegue reconciliar um passado de dados que não pode ser alterado com o futuro, que exige formatos de informação completamente novos?

Nossa, excelente ponto. Principalmente para quem trabalha com event sourcing, onde o banco de dados é literalmente o histórico de todos os eventos desde o dia zero. Fica aí o convite para quem tá escutando dar uma pesquisada e refletir sobre como evoluir sistemas baseados no passado. Tem um baita desafio para se aprofundar. Bom, foi muito bom mergulhar nesse tópico arquitetural hoje. Agradeço a companhia nessa discussão cheia de nuances técnicas.

Foi ótimo. Um grande abraço para todo mundo que acompanhou a gente até aqui.

É isso aí. Até o nosso próximo mergulho.
