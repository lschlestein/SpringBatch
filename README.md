# SpringBatch

O Spring Batch, é um poderoso framework, integrado ao Spring Boot. Ele serve para processamento em lote de informações.
A seguir teremos uma visão geral sobre o framework.

### Processamento em lote

O processamento em lote é um método de processamento de grandes volumes de dados simultaneamente, em vez de processá-los individualmente. Essa abordagem é amplamente usada em muitos setores, incluindo finanças, manufatura e telecomunicações. O processamento em lote é frequentemente usado para tarefas que exigem o processamento de grandes quantidades de dados, como processamento de folha de pagamento ou faturamento, bem como tarefas que exigem cálculos ou análises demoradas. Os aplicativos em lote são efêmeros, o que significa que, uma vez concluídos, eles "morrem".

Esse tipo de processamento traz consigo uma série de desafios, incluindo, mas não se limitando a:

- Manipulando grandes quantidades de dados com eficiência
- Tolerância a erros humanos e deficiências de hardware
- Escalabilidade

Quando é hora de fornecer um aplicativo baseado em lote para processar grandes quantidades de dados de forma estruturada, o Spring Batch fornece uma solução robusta e eficiente.

### Spring Batch Framework
O Spring Batch é um framework leve e abrangente, projetada para permitir o desenvolvimento de aplicativos em lote robustos que são vitais para as operações diárias de sistemas empresariais.

Ele fornece todos os recursos necessários que são essenciais para processar grandes volumes de dados, incluindo gerenciamento de transações, status de processamento de trabalho, estatísticas e recursos de tolerância a falhas. Ele também fornece recursos avançados de escalabilidade que permitem trabalhos em lote de alto desempenho por meio de técnicas de processamento multithread e particionamento de dados. 
Você pode usar o Spring Batch em casos de uso simples (como carregar um arquivo em um banco de dados) e em casos de uso complexos e de alto volume (como mover dados entre bancos de dados, transformá-los e assim por diante).

O Spring Batch integra-se perfeitamente com outras tecnologias Spring, o que o torna uma excelente escolha para escrever aplicativos em lote com Spring.

### Liguagem de domínio em lote

Os conceitos chave do modelo de domínio do Spring Batch, são representados a seguir:
![image](https://github.com/user-attachments/assets/e6410737-079b-4bfd-8ef0-c0bb6607193f)

A *Job* é uma entidade que encapsula um processo em lote inteiro, que é executado do início ao fim sem interrupção. A *Job* tem uma ou mais etapas. A *Step* é uma unidade de trabalho que pode ser uma tarefa simples (como copiar um arquivo ou criar um arquivo), ou uma tarefa orientada a itens (como exportar registros de uma tabela de banco de dados relacional para um arquivo), nesse caso, teria um *ItemReader*, um *ItemProcessor* (opcional) e um *ItemWriter*.

Um *Job* precisa ser iniciado com um *JobLauncher* e pode ser iniciado com um conjunto de *JobParameters*. Os metadados de execução sobre o que está sendo executado no momento *Job* são armazenados em um *JobRepository*.

### Modelo de domínio em lote
O Spring Batch usa um modelo robusto e bem projetado para o domínio de processamento em lote. Ele fornece um rico conjunto de APIs Java com interfaces e classes que representam todos os principais conceitos de processamento em lote, como *Job*, *Step*, *JobLauncher*, *JobRepository*, os quais serão aprofudandos em seguida.

Embora o modelo de domínio em lote possa ser implementado com qualquer tecnologia de persistência (como um banco de dados relacional, um banco de dados não relacional, um banco de dados gráfico, etc.), o Spring Batch fornece um modelo relacional dos conceitos de domínio em lote com tabelas de metadados que correspondem de perto às classes e interfaces na API Java.

O diagrama de entidade-relacionamento a seguir apresenta as principais tabelas de metadados:

![image](https://github.com/user-attachments/assets/be300a5e-e433-40da-b48c-8bfde16e39cf)

**Job_Instance:** Esta tabela contém todas as informações relevantes para uma definição de trabalho, como o nome do trabalho e sua chave de identificação.
**Job_Execution:** Esta tabela contém todas as informações relevantes para a execução de um job, como o horário de início, horário de término e status. Toda vez que um job é executado, uma nova linha é inserida nesta tabela.
**Job_Execution_Context:** Esta tabela contém o contexto de execução de um job. Um contexto de execução é um conjunto de pares de chave/valor de informações de tempo de execução que normalmente representa o estado que deve ser recuperado após uma falha.
**Step_Execution:** Esta tabela contém todas as informações relevantes para a execução de uma etapa, como hora de início, hora de término, contagem de itens lidos e contagem de itens gravados. Toda vez que uma etapa é executada, uma nova linha é inserida nesta tabela.
**Step_Execution_Context:** Esta tabela contém o contexto de execução de uma etapa. É semelhante à tabela que contém o contexto de execução de um job, mas, em vez disso, armazena o contexto de execução de uma etapa.
**Job_Execution_Params:** Esta tabela contém os parâmetros de tempo de execução de uma tarefa.

### Arquitetura do Spring Batch
O Spring Batch é projetado de forma modular e extensível. O diagrama a seguir mostra a arquitetura em camadas que suporta a facilidade de uso do framework para usuários finais:

``` mermaid
flowchart TB
    id1([Application]) --> id2([Batch Core])
    id2 --> id3([Batch Infrastructure])
    id1 --> id3
```

Esta arquitetura em camadas destaca três principais componentes de alto nível:

A camada *Application*: contém o trabalho em lote e o código personalizado escrito pelos desenvolvedores do aplicativo em lote.
A camada *Batch Core*: contém as classes de tempo de execução principais fornecidas pelo Spring Batch que são necessárias para criar e controlar trabalhos em lote. Ela inclui implementações para *Job* e *Step*, bem como serviços comuns como *JobLauncher* e *JobRepository*.
A camda *Batch Infrastructure*: contém readers e writters de itens comuns fornecidos pelo Spring Batch, além de serviços básicos, como mecanismos de repetição e nova tentativa, que são usados ​​tanto por desenvolvedores de aplicativos quanto pela própria estrutura principal.
Como desenvolvedor do Spring Batch, você normalmente usa APIs fornecidas pelo Spring Batch nos módulos *Batch Infrastructure* e *Batch Core* para definir seus trabalhos e etapas na camada *Application*. O Spring Batch fornece uma rica biblioteca de componentes de lote que você pode usar imediatamente (como itens readers, itens writters, particionadores de dados e dentre outros).
