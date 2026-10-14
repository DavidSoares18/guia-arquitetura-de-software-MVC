| Componente | Responsabilidade | O que não pode fazer |
|---|---|---|
| Model | Guarda o estado e as regras de negócio; avisa quem depende dele quando muda | Conhecer telas, importar biblioteca gráfica, chamar `print` ou montar HTML |
| View | Apresenta o estado do Model ao usuário; lê, não altera | Calcular regra de negócio ou escrever diretamente no Model |
| Controller | Recebe a ação do usuário, aciona o Model e escolhe a View | Conter regra de negócio, formatar a saída ou acessar a persistência direto |
