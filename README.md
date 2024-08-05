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
A camada *Batch Infrastructure*: contém readers e writters de itens comuns fornecidos pelo Spring Batch, além de serviços básicos, como mecanismos de repetição e nova tentativa, que são usados ​​tanto por desenvolvedores de aplicativos quanto pela própria estrutura principal.
Como desenvolvedor do Spring Batch, você normalmente usa APIs fornecidas pelo Spring Batch nos módulos *Batch Infrastructure* e *Batch Core* para definir seus trabalhos e etapas na camada *Application*. O Spring Batch fornece uma rica biblioteca de componentes de lote que você pode usar imediatamente (como itens readers, itens writters, particionadores de dados e dentre outros).

# Prática - 1
Criaremos um novo projeto, com as dependências **Spring Batch** e  **PostgresSQL Driver**
![image](https://github.com/user-attachments/assets/6f480050-850f-450c-bad3-65630bc8268b)

Será necessário criar um container docker, ou uma nova database no Postgres, para rodarmos nosso primeiro Job com Spring Batch.
Para instruções de como criar um container, veja aqui [Link para aulas Docker]().

Com o projeto criado, crie uma nova classe chamada **BillingJobConfiguration.java** conforme abaixo:
``` java
package example.billingjob;

import org.springframework.context.annotation.Configuration;

@Configuration
public class BillingJobConfiguration {
  // TODO add job definition here
}
```
This class is a placeholder for Spring Batch related beans (Jobs, Steps, etc) and contains a TODO that you will implement in a future Lab.

Agora precisaremos configurar a database no Postgres.
Crie o seguinte esquema conforme segue abaixo. O esquema do banco de dados pode ser criado via terminal, caso esteja utilizando o Docker, ou o PgAdmin.
Se estiver utilizando o Docker, verifique se o container está inicializado e rodando através do comando:
``` bash
docker -ps

CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS          PORTS                      NAMES
c711e7873371   postgres:14.1-alpine   "docker-entrypoint.s…"   20 minutes ago   Up 20 minutes   127.0.0.1:5432->5432/tcp   postgres
```
Em seguida, acesse o terminal do postgres
``` bash
docker exec -it postgres psql -U postgres
```
Conectado ao terminal, criei o seguinte esquema de tabelas conforme abaixo:
``` bash
CREATE TABLE BATCH_JOB_INSTANCE  (
    JOB_INSTANCE_ID BIGINT  NOT NULL PRIMARY KEY ,
    VERSION BIGINT ,
    JOB_NAME VARCHAR(100) NOT NULL,
    JOB_KEY VARCHAR(32) NOT NULL,
    constraint JOB_INST_UN unique (JOB_NAME, JOB_KEY)
) ;
CREATE TABLE BATCH_JOB_EXECUTION  (
    JOB_EXECUTION_ID BIGINT  NOT NULL PRIMARY KEY ,
    VERSION BIGINT  ,
    JOB_INSTANCE_ID BIGINT NOT NULL,
    CREATE_TIME TIMESTAMP NOT NULL,
    START_TIME TIMESTAMP DEFAULT NULL ,
    END_TIME TIMESTAMP DEFAULT NULL ,
    STATUS VARCHAR(10) ,
    EXIT_CODE VARCHAR(2500) ,
    EXIT_MESSAGE VARCHAR(2500) ,
    LAST_UPDATED TIMESTAMP,
    constraint JOB_INST_EXEC_FK foreign key (JOB_INSTANCE_ID)
    references BATCH_JOB_INSTANCE(JOB_INSTANCE_ID)
) ;
CREATE TABLE BATCH_JOB_EXECUTION_PARAMS  (
    JOB_EXECUTION_ID BIGINT NOT NULL ,
    PARAMETER_NAME VARCHAR(100) NOT NULL ,
    PARAMETER_TYPE VARCHAR(100) NOT NULL ,
    PARAMETER_VALUE VARCHAR(2500) ,
    IDENTIFYING CHAR(1) NOT NULL ,
    constraint JOB_EXEC_PARAMS_FK foreign key (JOB_EXECUTION_ID)
    references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ;
CREATE TABLE BATCH_STEP_EXECUTION  (
    STEP_EXECUTION_ID BIGINT  NOT NULL PRIMARY KEY ,
    VERSION BIGINT NOT NULL,
    STEP_NAME VARCHAR(100) NOT NULL,
    JOB_EXECUTION_ID BIGINT NOT NULL,
    CREATE_TIME TIMESTAMP NOT NULL,
    START_TIME TIMESTAMP DEFAULT NULL ,
    END_TIME TIMESTAMP DEFAULT NULL ,
    STATUS VARCHAR(10) ,
    COMMIT_COUNT BIGINT ,
    READ_COUNT BIGINT ,
    FILTER_COUNT BIGINT ,
    WRITE_COUNT BIGINT ,
    READ_SKIP_COUNT BIGINT ,
    WRITE_SKIP_COUNT BIGINT ,
    PROCESS_SKIP_COUNT BIGINT ,
    ROLLBACK_COUNT BIGINT ,
    EXIT_CODE VARCHAR(2500) ,
    EXIT_MESSAGE VARCHAR(2500) ,
    LAST_UPDATED TIMESTAMP,
    constraint JOB_EXEC_STEP_FK foreign key (JOB_EXECUTION_ID)
    references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ;
CREATE TABLE BATCH_STEP_EXECUTION_CONTEXT  (
    STEP_EXECUTION_ID BIGINT NOT NULL PRIMARY KEY,
    SHORT_CONTEXT VARCHAR(2500) NOT NULL,
    SERIALIZED_CONTEXT TEXT ,
    constraint STEP_EXEC_CTX_FK foreign key (STEP_EXECUTION_ID)
    references BATCH_STEP_EXECUTION(STEP_EXECUTION_ID)
) ;
CREATE TABLE BATCH_JOB_EXECUTION_CONTEXT  (
    JOB_EXECUTION_ID BIGINT NOT NULL PRIMARY KEY,
    SHORT_CONTEXT VARCHAR(2500) NOT NULL,
    SERIALIZED_CONTEXT TEXT ,
    constraint JOB_EXEC_CTX_FK foreign key (JOB_EXECUTION_ID)
    references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ;
CREATE SEQUENCE BATCH_STEP_EXECUTION_SEQ MAXVALUE 9223372036854775807 NO CYCLE;
CREATE SEQUENCE BATCH_JOB_EXECUTION_SEQ MAXVALUE 9223372036854775807 NO CYCLE;
CREATE SEQUENCE BATCH_JOB_SEQ MAXVALUE 9223372036854775807 NO CYCLE;
```
Para conferir se as tabelas foram criadas corretamente, digite \d no terminal do postgres:
``` bash
postgres=# \d
                      List of relations
 Schema |             Name             |   Type   |  Owner
--------+------------------------------+----------+----------
 public | batch_job_execution          | table    | postgres
 public | batch_job_execution_context  | table    | postgres
 public | batch_job_execution_params   | table    | postgres
 public | batch_job_execution_seq      | sequence | postgres
 public | batch_job_instance           | table    | postgres
 public | batch_job_seq                | sequence | postgres
 public | batch_step_execution         | table    | postgres
 public | batch_step_execution_context | table    | postgres
 public | batch_step_execution_seq     | sequence | postgres
(9 rows)
postgres=#
```

Em seguida, configure a conexão da aplicação Spring Batch, com o Postgres, no arquivo application.properties do projeto Spring Boot. Confira se o usuário e senha configurados no banco de dados, correspondem aos configurados no application.properties
``` bash
spring.datasource.url=jdbc:postgresql://localhost:5432/postgres
spring.datasource.username=postgres
spring.datasource.password=postgres
```
Nesse ponto, a aplicação deve iniciar normalmente se tudo estiver configurado corretamente.

### 2-Compreendendo os jobs e como executá-los
Como já visto anteriomente a *Job* é uma entidade que encapsula um processo em lote inteiro que é executado do início ao fim sem interação ou interrupção. Nesta lição, você aprenderá como Jobs são representados internamente no Spring Batch, entenderá como eles são iniciados e entenderá como seus metadados de execução são persistidos.

**O que é um Job?**
A *Job* é uma entidade que encapsula um processo em lote inteiro que é executado do início ao fim. Consiste em um conjunto de etapas que são executadas em uma ordem específica.

Um trabalho em lote no Spring Batch é representado pela interface Job fornecida pela dependência *spring-batch-core*:
``` java
public interface Job {
    String getName();
    void execute(JobExecution execution);
}
```
Em um nível fundamental, a interface Job requer que as implementações especifiquem o *Jobname* (o método getName()) e o que ela deve fazer (o método execute).

O método *execute* fornece uma referência a um objeto *JobExecution*. O *JobExecution* representa a execução real do *Job* em tempo de execução. Ele contém uma série de detalhes de tempo de execução, como o horário de início, o horário de término, o status da execução e assim por diante. Essas informações de tempo de execução são armazenadas pelo Spring Batch em um repositório de metadados, conforme configurado anteriormente.

Observe que o método *execute* não lanca nenhuma exceção. Exceções de tempo de execução devem ser manipuladas por implementações e adicionadas no objeto *JobExecution*. Os clientes podem inspecionar o o status da *JobExecution* para determinar se Job foi executada com sucesso ou falha.

### Compreendendo os metadados do job
Um dos principais conceitos do Spring Batch é o *JobRepository*. O *JobRepository* é onde todos os metadados sobre trabalhos (jobs) e etapas são armazenados. Um *JobRepository* pode ser um armazenamento persistente ou um armazenamento na memória. Um armazenamento persistente tem a vantagem de fornecer metadados mesmo após a *Job* ser concluída, o que pode ser usado para pós-análise ou para reiniciar um *Job* no caso de uma falha.

O Spring Batch fornece uma implementação JDBC do *JobRepository*, que armazena metadados de lote em um banco de dados relacional. Em um sistema de nível de produção, você precisa criar algumas tabelas que o Spring Batch usa para armazenar seus metadados de execução, conforme visto anteriormente.

O *JobRepository* é o que cria um objeto *JobExecution* quando a *Job* é iniciado pela primeira vez. Veremos como iniciar um Job a seguir:

### Lançando um Job

Para iniciar uma *job* no Spring Batch utiliza-se o conceito do *JobLauncher*, que é representado pela seguinte interface:

``` java
public interface JobLauncher {

   JobExecution run(Job job, JobParameters jobParameters)
          throws
             JobExecutionAlreadyRunningException,
             JobRestartException,
             JobInstanceAlreadyCompleteException,
             JobParametersInvalidException;
}
```
O método *run* é projetado para iniciar um dado *Job* com um conjunto de *JobParameters*. Abordaremos os *JobParameters* em seguida. Por enquanto, você pode pensar neles como uma coleção de pares de chave/valor que são passados ​​para o *Job* em tempo de execução.

A interface *JobLauncher* raramente será implementada sozinha, porque o Spring Batch fornece uma implementação pronta para uso dela. O diagrama a seguir mostra como o *JobLauncher*, o *JobRepository* e o *Job* interagem entre si.
![image](https://github.com/user-attachments/assets/0502249b-b275-4e11-a337-ab6588039609)

As *Jobs* podem ser iniciadas ou por linha de comando ou via web container. Veremos somente através da linha comando.

# Prática - 2
Com base no projeto anterior, criaremos nosso primeiro Job.
No projeto anteriormente criado crie a classe BillingJob.java no diretório src/main/java/example/billingjob conforme segue:

``` java
ackage example.billingjob;

import org.springframework.batch.core.Job;
import org.springframework.batch.core.JobExecution;

public class BillingJob implements Job {

    @Override
    public String getName() {
        return BillingJob;
    }

    @Override
    public void execute(JobExecution execution) {
       System.out.println("processing billing information");
    }
}
```

Logo em seguida precisamos configura a Bean do Job, criado anteriormente.
``` java
package example.billingjob;

import org.springframework.batch.core.Job;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class BillingJobConfiguration {
    @Bean
    public Job job() {
        return new BillingJob();
    }
}
```

Para saber se o Job foi configurado e executado corretament, a informação *processing billing information* deverá ser exibida no console.
É possível verificar se o *Job* foi executado junto ao Postgres conforme segue:

```bash
docker exec postgres psql -U postgres -c 'select * from BATCH_JOB_EXECUTION;'
```


Dessa forma, o *Job* está pronto para ser executado.

Provavelmente haverá o registro do *Job* junto a tabela BATCH_JOB_EXECUTION. Para adicionarmos os status corretamente a essa tabela precisamos reconfigurar a classe BillingJob.java

``` java
package example.billingjob;

import org.springframework.batch.core.BatchStatus;
import org.springframework.batch.core.ExitStatus;
import org.springframework.batch.core.Job;
import org.springframework.batch.core.JobExecution;
import org.springframework.batch.core.repository.JobRepository;

public class BillingJob implements Job {

    private JobRepository jobRepository;

    public BillingJob(JobRepository jobRepository) {
        this.jobRepository = jobRepository;
    }

    @Override
    public String getName() {
        return "BillingJob";
    }

    @Override
    public void execute(JobExecution execution) {
        System.out.println("processing billing information");
        execution.setStatus(BatchStatus.COMPLETED);
        execution.setExitStatus(ExitStatus.COMPLETED);
        this.jobRepository.update(execution);
    }
}
```

Como visto acima, primeiramente é passado o referência do *JobRepository* via construtor da classe.
Em seguida o código do método execute, foi alterado para indicar os status corretos e em seguida é feito o update na jobRepository.
O status do *Job* indicada os possiveís estados da execução do trabalho em si. Por exemplo, se o *Job* iniciou o status é *BatchStatus.STARTED*. Caso falhe *BatchStatus.FAILED*, equando concluído *BatchStatus.COMPLETED*.
No código acima, admitimos que nosso trabalho terminou com sucesso *BatchStatus.COMPLETED* e também que terminou com sucesso *ExitStatus.COMPLETED*.
Como a implementação do *Job* agora faz o uso de um *JobRepository* é preciso forncer um *JobRepository* em nossa definição da bean *Job*. Devemos agora alterar a nossa classe BillingJobConfiguration.java

``` java
package example.billingjob;

import org.springframework.batch.core.Job;
import org.springframework.batch.core.repository.JobRepository;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class BillingJobConfiguration {

    @Bean
    public Job job(JobRepository jobRepository) {
        return new BillingJob(jobRepository);
    }
}
```
É necessário limpar a base de dados e rodar novamento o Job.
Execute no terminal o seguinte comando
``` bash
scripts/drop-create-database.sh
```
Arquivo drop-create-database.sh
``` bash
#!/bin/bash
set -e

# This script is used to drop and recreate the meta-data tables

docker exec postgres psql -f /mnt/exercises/src/sql/schema-drop-postgresql.sql -U postgres
docker exec postgres psql -f /mnt/exercises/src/sql/schema-postgresql.sql -U postgres
```

Para que o script seja executado corretamente, os seguintes arquivos devem ser criados no diretório /src/sql

schema-drop-postgresql.sql
``` sql
DROP TABLE  IF EXISTS BATCH_STEP_EXECUTION_CONTEXT;
DROP TABLE  IF EXISTS BATCH_JOB_EXECUTION_CONTEXT;
DROP TABLE  IF EXISTS BATCH_STEP_EXECUTION;
DROP TABLE  IF EXISTS BATCH_JOB_EXECUTION_PARAMS;
DROP TABLE  IF EXISTS BATCH_JOB_EXECUTION;
DROP TABLE  IF EXISTS BATCH_JOB_INSTANCE;

DROP SEQUENCE  IF EXISTS BATCH_STEP_EXECUTION_SEQ;
DROP SEQUENCE  IF EXISTS BATCH_JOB_EXECUTION_SEQ;
DROP SEQUENCE  IF EXISTS BATCH_JOB_SEQ;
```

schema-postgresql.sql
```sql
CREATE TABLE BATCH_JOB_INSTANCE  (
                                     JOB_INSTANCE_ID BIGINT  NOT NULL PRIMARY KEY ,
                                     VERSION BIGINT ,
                                     JOB_NAME VARCHAR(100) NOT NULL,
                                     JOB_KEY VARCHAR(32) NOT NULL,
                                     constraint JOB_INST_UN unique (JOB_NAME, JOB_KEY)
) ;

CREATE TABLE BATCH_JOB_EXECUTION  (
                                      JOB_EXECUTION_ID BIGINT  NOT NULL PRIMARY KEY ,
                                      VERSION BIGINT  ,
                                      JOB_INSTANCE_ID BIGINT NOT NULL,
                                      CREATE_TIME TIMESTAMP NOT NULL,
                                      START_TIME TIMESTAMP DEFAULT NULL ,
                                      END_TIME TIMESTAMP DEFAULT NULL ,
                                      STATUS VARCHAR(10) ,
                                      EXIT_CODE VARCHAR(2500) ,
                                      EXIT_MESSAGE VARCHAR(2500) ,
                                      LAST_UPDATED TIMESTAMP,
                                      constraint JOB_INST_EXEC_FK foreign key (JOB_INSTANCE_ID)
                                          references BATCH_JOB_INSTANCE(JOB_INSTANCE_ID)
) ;

CREATE TABLE BATCH_JOB_EXECUTION_PARAMS  (
                                             JOB_EXECUTION_ID BIGINT NOT NULL ,
                                             PARAMETER_NAME VARCHAR(100) NOT NULL ,
                                             PARAMETER_TYPE VARCHAR(100) NOT NULL ,
                                             PARAMETER_VALUE VARCHAR(2500) ,
                                             IDENTIFYING CHAR(1) NOT NULL ,
                                             constraint JOB_EXEC_PARAMS_FK foreign key (JOB_EXECUTION_ID)
                                                 references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ;

CREATE TABLE BATCH_STEP_EXECUTION  (
                                       STEP_EXECUTION_ID BIGINT  NOT NULL PRIMARY KEY ,
                                       VERSION BIGINT NOT NULL,
                                       STEP_NAME VARCHAR(100) NOT NULL,
                                       JOB_EXECUTION_ID BIGINT NOT NULL,
                                       CREATE_TIME TIMESTAMP NOT NULL,
                                       START_TIME TIMESTAMP DEFAULT NULL ,
                                       END_TIME TIMESTAMP DEFAULT NULL ,
                                       STATUS VARCHAR(10) ,
                                       COMMIT_COUNT BIGINT ,
                                       READ_COUNT BIGINT ,
                                       FILTER_COUNT BIGINT ,
                                       WRITE_COUNT BIGINT ,
                                       READ_SKIP_COUNT BIGINT ,
                                       WRITE_SKIP_COUNT BIGINT ,
                                       PROCESS_SKIP_COUNT BIGINT ,
                                       ROLLBACK_COUNT BIGINT ,
                                       EXIT_CODE VARCHAR(2500) ,
                                       EXIT_MESSAGE VARCHAR(2500) ,
                                       LAST_UPDATED TIMESTAMP,
                                       constraint JOB_EXEC_STEP_FK foreign key (JOB_EXECUTION_ID)
                                           references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ;

CREATE TABLE BATCH_STEP_EXECUTION_CONTEXT  (
                                               STEP_EXECUTION_ID BIGINT NOT NULL PRIMARY KEY,
                                               SHORT_CONTEXT VARCHAR(2500) NOT NULL,
                                               SERIALIZED_CONTEXT TEXT ,
                                               constraint STEP_EXEC_CTX_FK foreign key (STEP_EXECUTION_ID)
                                                   references BATCH_STEP_EXECUTION(STEP_EXECUTION_ID)
) ;

CREATE TABLE BATCH_JOB_EXECUTION_CONTEXT  (
                                              JOB_EXECUTION_ID BIGINT NOT NULL PRIMARY KEY,
                                              SHORT_CONTEXT VARCHAR(2500) NOT NULL,
                                              SERIALIZED_CONTEXT TEXT ,
                                              constraint JOB_EXEC_CTX_FK foreign key (JOB_EXECUTION_ID)
                                                  references BATCH_JOB_EXECUTION(JOB_EXECUTION_ID)
) ;

CREATE SEQUENCE BATCH_STEP_EXECUTION_SEQ MAXVALUE 9223372036854775807 NO CYCLE;
CREATE SEQUENCE BATCH_JOB_EXECUTION_SEQ MAXVALUE 9223372036854775807 NO CYCLE;
CREATE SEQUENCE BATCH_JOB_SEQ MAXVALUE 9223372036854775807 NO CYCLE;
```
Agora o status COMPLETED deverá ser mostrado.
``` bash
2023-05-03T21:21:07.939Z  INFO 7458 --- [           main] o.s.b.c.l.support.SimpleJobLauncher      : Job: [example.billingjob.BillingJob@66f0548d] completed with the following parameters: [{}] and the following status: [COMPLETED]
```

# Instâncias de Jobs

### O Que São Instâncias de Jobs

Um *Job* pode ser definido uma vez, mas provavelmente será executado diversas vezes, geralmente em um intervalo definido. No Spring Batch, a *Job* é a definição genérica de um processo em lote especificado por um desenvolvedor. Essa definição genérica deve ser parametrizada para criar instâncias reais de um *Job*, que são chamadas *JobInstances*.

A *JobInstance* é uma parametrização única de um determinado *Job*. Por exemplo, imagine um processo em lote que precisa ser executado uma vez no final de cada dia, ou quando um determinado arquivo estiver presente. No cenário de uma vez por dia, podemos usar o Spring Batch para criar um *Job EndOfDay* nesse caso. Haveria uma única definição *Job EndOfDay*, mas várias instâncias do mesmo *Job*, uma por dia. Cada instância processaria os dados de um dia específico e poderia ter um resultado diferente (sucesso ou falha) de outras instâncias. Portanto, cada instância individual do *Job* deve ser rastreada separadamente.

A *JobInstance* é distinta de outras *JobInstances* por um parâmetro específico, ou um conjunto de parâmetros. Por exemplo, um parâmetro chamado *schedule.date* especificaria um dia específico. Tal parâmetro é chamado de *JobParameter*. *JobParameters* são o que distingue uma *JobInstance* da outra. O diagrama a seguir mostra como *JobParameters*  define *JobInstances*:
![image](https://github.com/user-attachments/assets/2d3cf090-a91f-4e62-9ccf-ca98f8962f41)

### O que as Instâcias dos Jobs e os seus Parâmetros representam?

*JobInstances* são distintos entre si por *JobParameters* distintos. Esses parâmetros geralmente representam os dados que se pretende processar por um dado *JobInstance*. Por exemplo, no caso do *Job EndOfDay*, o *JobParameter* *schedule.date* para 1º de janeiro define a *JobInstance* que processará os dados de 1º de janeiro. O *JobParameter* *schedule.date* para 2 de janeiro define o *JobInstance* que processará os dados de 2 de janeiro, e assim por diante.

Embora não seja necessário que os *Job Parameters* representem os dados a serem processados, é uma boa prática - para projetar *JobInstances* corretamente. Projetar *JobInstances* para representar os dados a serem processados acaba sendo mais fácil de configurar, testar e pensar, em caso de falha.

A definição de a *JobInstance* em si não tem absolutamente nenhuma relação com os dados a serem carregados. Depende inteiramente da implementação do *Job* em determinar como os dados são carregados, com base em *JobParameters*. Aqui estão alguns exemplos de *JobParameters* e como eles representam os dados a serem processados ​​pela *JobInstance*:

Uma data específica: Neste caso, teríamos uma *JobInstance* data específica.
Um arquivo específico: Neste caso, teríamos um *JobInstance* por arquivo.
Um intervalo específico de registros em uma tabela de banco de dados relacional: Nesse caso, teríamos um *JobInstance* por intervalo.

### Como as Jobs Instances se relacionam com as Execuções das Jobs?
A *JobExecution* se refere ao conceito técnico de uma única tentativa de executar um *JobInstance*. Como visto na anteriormente, a *JobExecution* pode terminar em sucesso ou falha. No caso do *Job EndOfDay*, se a execução de 1º de janeiro falhar na primeira vez e for executada novamente no dia seguinte, ainda será a execução de 1º de janeiro. Portanto, cada *JobInstance* pode ter vários *JobExecutions*.

A relação entre os conceitos de *Job*, *JobInstance*, *JobParameters*, e *JobExecution* é apresentada a seguir:
![image](https://github.com/user-attachments/assets/8636792b-d227-42bc-b56e-58a434e0f8d0)

Abaixo temos um diagrama do ciclo de vida de uma *JobInstance* no caso de um  *Job EndOfDay*:
![image](https://github.com/user-attachments/assets/82a728cb-1410-410c-90ba-c3e4660961a1)

No Spring Batch, uma *JobInstance* não é considerada completa a menos que a *JobExecution* seja concluída com sucesso. A *JobInstance* que é concluída não pode ser reiniciada novamente. Esta é uma escolha de design para evitar o reprocessamento acidental dos mesmos dados para batch *Jobs* que não são idempotentes.

### Os diferentes tipos de parâmetros de um Job
*JobParameters* são normalmente usados ​​para distinguir uma *JobInstance* de outra. Em outras palavras, eles são usados ​​para identificar um *JobInstance*.

Nem todos os parâmetros podem ser usados ​​para identificar *JobInstances*. Por exemplo, se o *Job EndOfDay* pega outro parâmetro - digamos, file.format) - que representa o formato do arquivo de saída (CSV, XML e outros), esse parâmetro não representa realmente os dados a serem processados, então, ele pode ser excluído do processo de identificação das *Jobinstâncias*.

É aqui que os *JobParameter* não identificadores entram em cena. No Spring Batch, *JobParameters* pode ser identificador ou não identificador. Um *JobParameter* identificador contribui para a identificação de uma *JobInstance*, enquanto um não identificador não. Por padrão, *JobParameters* são identificadores, e o Spring Batch fornece APIs para especificar se o *JobParameter* é identificador ou não.

No exemplo do *Job EndOfDay* , os parâmetros podem ser definidos na tabela a seguir:

|Job parameter|Identificador?|Exemplo   |
|-------------|--------------|----------|
|schedule.date|	Yes          |2023-01-01|
|file.format  |	No	         |csv       |

Agora a questão é: Por que isso é importante e como é usado no Spring Batch? Identificar *JobParameters* desempenha um papel crucial no caso de falha. Em um ambiente de produção, onde centenas de *JobInstances* estão em execução e uma delas falha, precisamos de uma maneira de identificar qual instância falhou. É aqui que os parâmetros de identificação dos *Jobs* são essenciais. Quando um *JobExecution* para uma dada *JobInstance*, iniciar o mesmo trabalho com o mesmo conjunto de JobParameters de identificação criará um novo *JobExecution* (ou seja, uma nova tentativa) para o mesmo *JobInstance*.

# Prática - 3

Veremos nessa prática a forma de passarmos parâmetros para um *Job* e também, como criar uma *JobInstance*.
Nesse exemplo, iremos processar o faturamento mensal de uma empresa hipotética. Os dados serão lidos de um arquivo .CSV. O nome do arquivo será utilizado com identificador do *Job*. Dessa forma, teremos uma *JobInstance* distinta para cada mês.

Os arquivos estão no diretório src/resources do projeto.
Modificaremos o método *execute* da classe BillingJob.java conforme segue:
``` java
 @Override
 public void execute(JobExecution execution) {
     JobParameters jobParameters = execution.getJobParameters();
     String inputFile = jobParameters.getString("input.file");
     System.out.println("processing billing information from file " + inputFile);
     execution.setStatus(BatchStatus.COMPLETED);
     execution.setExitStatus(ExitStatus.COMPLETED);
     this.jobRepository.update(execution);
 }
```
Logo em seguida empacotar o projeto com o maven
``` bash
./mvnw package -Dmaven.test.skip=true
```

Em seguida rodar o Jar criado, como segue:
``` bash
java -jar target/billing-job-0.0.1-SNAPSHOT.jar input.file=src/main/resources/billing-2023-01.csv
```

Deverá ser apresentada a seguinte saída.
``` terminal
processing billing information from file src/main/resources/billing-2023-01.csv
```

Caso deseje, tente executar novamente o "Job" com o mesmo arquivo, e um erro ocorrerá, pois, essa instância já foi executada anteriormente.

Para confirmar as execuções anteriormente executadas, verifique no postgres:
``` sql
docker exec postgres psql -U postgres -c 'select * from BATCH_JOB_INSTANCE;'
```

Verifique as execuções em sua respectiva tabela:
``` sql
docker exec postgres psql -U postgres -c 'select * from BATCH_JOB_EXECUTION;'
```
É possível também verificar os parâmetros utlizados:
``` sql
docker exec postgres psql -U postgres -c 'select * from BATCH_JOB_EXECUTION_PARAMS;'
```
Para cada Execução, os parâmetros são gravados na tabela.

# Compreendendo os Steps

### O que é um Step
Na vida cotidiana, frequentemente falamos sobre dar passos. Um passo no caminho. Um passo na direção certa.

O Spring Batch também tem etapas. A *Step* é um objeto de domínio que encapsula uma fase independente e sequencial de um *batch Job*. Ele contém todas as informações necessárias para definir uma unidade de trabalho em um *batch Job*.

A *Step* no Spring Batch é representado pela interface *Step*e fornecida pela dependência *spring-batch-core*:
``` java
public interface Step {

  String getName();

  void execute(StepExecution stepExecution) throws JobInterruptedException;
}
```
Semelhante à interface *Job*, a interface *Step* requer, em um nível fundamental, uma implementação para especificar o nome da etapa (o método getName()) e o que a etapa deve fazer (o método execute).

O método *execute* fornece uma referência a um objeto *StepExecution*. O *StepExecution* representa a execução real da etapa no tempo de execução. Ele contém vários detalhes do tempo de execução, como o horário de início, o horário de término, o status da execução e assim por diante. Essas informações de tempo de execução são armazenadas pelo Spring Batch no repositório de metadados, semelhante ao *JobExecution*, como vimos anteriormente.

O método *execute* foi projetado para gerar uma exceção *JobInterruptedException* caso o trabalho seja interrompido naquela etapa específica.

### Quais são os diferentes tipos de passos (steps)?

Embora seja possível implementar a interface *Step* manualmente para definir a lógica de uma etapa, o Spring Batch fornece implementações diferentes para casos de uso comuns. Todas essas implementações derivam da classe AbstractStep base que fornece os requisitos comuns, como definir o horário de início, horário de término de uma etapa, atualizar o status de saída da etapa, persistir os metadados da etapa no repositório de tarefas, etc.

Os tipos de *Step​​* mais usados são os seguintes:

**TaskletStep:** Projetado para tarefas simples (como copiar um arquivo ou criar um arquivo) ou tarefas orientadas a itens (como ler um arquivo ou uma tabela de banco de dados).

**PartitionedStep:** Projetado para processar o conjunto de dados de entrada em partições.

**FlowStep:** Útil para agrupar logicamente etapas em fluxos.

**JobStep:** Semelhante a um, *FlowStep* mas na verdade cria e inicia uma execução de trabalho separada para as etapas no fluxo especificado. Isso é útil para criar um fluxo complexo de trabalhos e subtrabalhos.

O diagrama a seguir explica a hierarquia e a relação entre esses diferentes tipos de Steps.
![image](https://github.com/user-attachments/assets/19760155-5122-4525-b7fa-29d5020e80dd)

Nos aprofundaremos no TaskletStep

### O TakskletStep
O *TaskletStep* é uma implementação da interface *Step* baseada no conceito da *Tasklet*. A *Tasklet* representa uma unidade de trabalho que o *Step* deve fazer quando invocado. A interface *Tasklet* é definida como segue:

``` java
@FunctionalInterface
public interface Tasklet {

  @Nullable
  RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception;
}
```

O método *execute* desta interface funcional é projetado para conter uma iteração da lógica de negócios de um *TaskletStep*. Há alguns elementos-chave para entender:

* O tipo de retorno do método *execute* é do tipo *RepeatStatus*. Esta é uma enumeração que é usada para sinalizar ao framework que o trabalho foi concluído (RepeatStatus.FINISHED), ou não concluído ainda (RepeatStatus.CONTINUABLE). Neste último caso, o *TaskletStep* invoca *Tasklet* novamente.
* Cada iteração do *Tasklet* é executada no escopo de uma transação de banco de dados. Dessa forma, o Spring Batch salva o trabalho que foi feito durante a iteração no repositório de tarefas persistentes. Dessa forma, a etapa pode continuar de onde parou, em caso de falha. Por esse motivo, o *TaskletStep* requer um *PlatformTransactionManager* para gerenciar a transação do *Tasklet*.
* O método execute fornece uma referência a um objeto *StepContribution*, que representa a contribuição deste *Tasklet* para a etapa (por exemplo, quantos itens foram lidos, gravados ou processados ​​de outra forma) e uma referência a um objeto *ChunkContext*, que é um conjunto de pares chave/valor que fornecem detalhes sobre o contexto de execução do *Tasklet*.
O método *execute* foi projetado para lançar uma exceção se ocorrer algum erro durante o processamento. Nesse caso, a etapa será marcada como falha.


O Spring Batch fornece diversas implementações da interface *Tasklet* para casos de uso comuns:

**ChunkOrientedTasklet:** Projetado para conjuntos de dados orientados a itens, como um arquivo simples ou tabela de banco de dados.

**SystemCommandTasklet:** Permite que você invoque um comando do Sistema Operacional dentro do Tasklet.

**Outros:**. Veja a [documentação](https://docs.spring.io/spring-batch/reference/index.html) de referência do Spring Batch para mais detalhes.

## Utilizando Steps
### Utilizando Steps para definir o fluxo de execução de um Job
Como visto anteriormente, raramente será necessário implementar a interface *Job* manualmente, como fizemos. Na verdade, o Spring Batch fornece a classe  *AbstractJob* que permite que você defina seu "Job" como um fluxo de "Steps". Esta classe tem duas variações:

**SimpleJob:** Para execução sequencial de etapas.

**FlowJob:** Para fluxos de etapas complexos, incluindo ramificação condicional e execução paralela. Veja a documentação aqui [Fluxos Condicionais](https://docs.spring.io/spring-batch/docs/5.0.4/reference/html/step.html#conditionalFlow) e [Fluxos Paralelos](https://docs.spring.io/spring-batch/docs/5.0.4/reference/html/step.html#split-flows)

### O SimpleJob
A classe *SimpleJob* é projetada para compor um trabalho como uma sequência de etapas. Uma etapa deve ser concluída com sucesso para que a próxima etapa na sequência seja iniciada. Se uma etapa falhar, o trabalho será imediatamente encerrado e as etapas subsequentes não serão executadas. É possível criar um fluxo sequencial de etapas usando a API *JobBuilder*. Aqui está um exemplo com duas etapas:

``` java
@Bean
public Job myJob(JobRepository jobRepository, Step step1, Step step2) {
  return new JobBuilder("job", jobRepository)
    .start(step1)
    .next(step2)
    .build();
}
```

Acima, o job (myJob) é definido como uma sequência de duas etapas, step1 e step2. O job começa com step1 e move para step2 somente se step1 for concluído com sucesso. Se step1 falhar, o job será encerrado e step2 não será executado.

### Como criar steps?
Semelhante à API *JobBuilder*, o Spring Batch fornece a API *StepBuilder* para permitir que você crie diferentes tipos de etapas. Todos os tipos de etapas compartilham algumas propriedades comuns (como o nome da etapa, o repositório de tarefas para relatar metadados e outros), mas cada tipo de etapa tem suas próprias propriedades específicas. Por esse motivo, o Spring Batch fornece um construtor específico para cada tipo de etapa (TaskletStepBuilder, PartitionedStepBuilder, e outros).

Aqui está um exemplo que cria um *TaskletStep*:

``` java
@Bean
public Step taskletStep(JobRepository jobRepository, Tasklet tasklet, PlatformTransactionManager transactionManager) {
  return new StepBuilder("step1", jobRepository)
    .tasklet(tasklet, transactionManager)
    .build();
}
```
Neste exemplo, definimos o step como um Spring bean. A *StepBuilder* aceita o nome do passo e o repositório de trabalho, pois eles são comuns a todos os tipos de step.

Depois disso, chamamos o método *StepBuilder.tasklet*, que usará a *TaskletStepBuilder* para definir ainda mais propriedades específicas do *TaskletStep*, principalmente o *Tasklet* para executar como parte da etapa e o gerenciador de transações para usar para transações. Observe como não usamos o *TaskletStepBuilder* diretamente.

O padrão é similar se, por exemplo, você quiser criar um passo particionado. Aqui está um exemplo para criar um *PartitionedStep*:
``` java
@Bean
public Step partitionedtStep(JobRepository jobRepository, Partitioner partitioner) {
  return new StepBuilder("step1", jobRepository)
    .partitioner("worker", partitioner)
    .build();
}
```

### Compreendendo os metadados de etapas
Semelhante aos metadados de nível de trabalho, que são armazenados na tabela *BATCH_JOB_EXECUTION*, o Spring Batch armazena metadados de nível de etapa na tabela *BATCH_STEP_EXECUTION*. Isso inclui o horário de início da etapa, seu horário de término, seu status de execução e outros detalhes.

Outra similaridade com o nível de trabalho é o contexto de execução. Cada etapa tem um contexto de execução, que nada mais é do que um conjunto de pares de chave/valor para armazenar informações de tempo de execução sobre a execução da etapa. Isso inclui o tipo de etapa, o tipo de tasklet se a etapa for um *TaskletStep* e outros detalhes. O contexto também pode registrar o progresso de uma etapa, como contagem de leitura de itens, contagem de gravação de itens e outras métricas. Esses pares de chave/valor podem ser usados ​​para reiniciar uma etapa de onde parou, em caso de falha.

Por padrão, uma etapa bem-sucedida não é re-executada ao reiniciar uma instância de trabalho com falha. No entanto, em algumas situações, mesmo uma etapa bem-sucedida deve ser re-executada ao tentar novamente uma instância de trabalho. O Spring Batch torna isso possível por meio do parâmetro *StepBuilder.allowStartIfComplete*. Você também pode limitar o número de vezes que uma etapa é reiniciada usando o parâmetro *StepBuilder.startLimit*.

# Prática - 4

Veremos nessa prática como se dá a implementação do primeiro step, com Spring Batch.
Precisaremos copiar os arquivos contidos na /src/main/resources/billing-2023-01.csv para um novo diretório /staging.

### Implementar a FilePreparationTasklet
Criar a classe FilePreparationTaksklet.java na src/main/java/example/billingjob conforme segue:
```java
package example.billingjob;

import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.nio.file.StandardCopyOption;

import org.springframework.batch.core.JobParameters;
import org.springframework.batch.core.StepContribution;
import org.springframework.batch.core.scope.context.ChunkContext;
import org.springframework.batch.core.step.tasklet.Tasklet;
import org.springframework.batch.repeat.RepeatStatus;

public class FilePreparationTasklet implements Tasklet {

    @Override
    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
        JobParameters jobParameters = contribution.getStepExecution().getJobParameters();
        String inputFile = jobParameters.getString("input.file");
        Path source = Paths.get(inputFile);
        Path target = Paths.get("staging", source.toFile().getName());
        Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
        return RepeatStatus.FINISHED;
    }
}
```
Nessa clase a interface *Tasklet* é implementada para adquirir o arquivo de entrada dos parâmetros do *job*, e fazer uma cópia desses arquivos para o diretório de staging.
Observe como é usado a opção StandardCopyOption.REPLACE_EXISTING, para cópia do arquivo, configurando para que caso haja algum arquivo no destino, que o mesmo seja substituído. Isso é útil caso o *step* seja re-executado e seja desejado que a operação seja concluída com sucesso, ao invés de falha devido a já existir um arquivo existente.

Agora podemos definir a *TaskletStep*.

Alterar o src/main/java/example/billingjob/BillingJobConfiguration.java e adicionar a seguinte *Bean* conforme segue:
``` java
@Bean
public Step step1(JobRepository jobRepository, JdbcTransactionManager transactionManager) {
    return new StepBuilder("filePreparation", jobRepository)
            .tasklet(new FilePreparationTasklet(), transactionManager)
            .build();
}
```
Nessa porção de código, foi definida uma *bean* chamada step1, do tipo *Step*
Foi passada a referência de um *JobRepository* para a step, e uma *JdbcTransactionManager* para a tasklet. Essa transacition manager é auto-configurada pelo Spring Boot, podendo assim ser usada para define a *TaskletStep*.

Vale lembrar que uma *TaskletStep* precisa de uma gerenciador de transações para orquestrar a transação de cada iteração da *Tasklet*. A *TaskletStep* é definida pela chamada ao *StepBuilder.tasklet* para o qual é passada uma instância do *FilePreparationTasklet* e o gerenciador de transações para gerenciar as transações.

Em seguida modificar a configuração do Job.
Alterar a BillingJobConfiguration.java, substituindo a bean job, existente nessa classe.

``` java
public Job job(JobRepository jobRepository, Step step1) {
    return new JobBuilder("BillingJob", jobRepository)
            .start(step1)
            .build();
```
Neste trecho, substituímos a criação de uma instância *BillingJob* pelo uso da API *JobBuilder* para criar o trabalho.
Passamos o nome do trabalho e uma referência a um *JobRepository*.
Depois disso, chamamos o método *JobBuilder.start* que cria um fluxo de trabalho sequencial e espera a primeira etapa da sequência.
No nosso caso, a primeira etapa é a etapa *filePreparation* que definimos como *step 1* anteriormente.

Feito isso, podemos agora remover a classe BillingJob.java, implementada anteriormente.

Feito isso é possível empacator e rodar a aplicação novamente.
Empacotar o projeto com o maven
``` bash
./mvnw package -Dmaven.test.skip=true
```

Em seguida rodar o Jar criado, como segue:
``` bash
java -jar target/billing-job-0.0.1-SNAPSHOT.jar input.file=src/main/resources/billing-2023-01.csv
```
Após a execução da aplicação, o arquivo billing-2023-01.csv deverá ter sido copiado para o staging, indicando que o processo configurado funcionou corretamente.

É possível verificar os metadados salvos pela aplicação, na tabela *BATCH_STEP_EXECUTION*.

Executar no terminal:
``` java
docker exec postgres psql -U postgres -c 'select * from BATCH_STEP_EXECUTION;'
```
# Lendo e Escrevendo Dados
### Compreendendo o modelo de processamento orientado a blocos (Chunk-Oriented)
Ingerir um arquivo em uma tabela de banco de dados parece uma tarefa simples e fácil à primeira vista, mas essa tarefa simples é, na verdade, bem desafiadora! E se o arquivo de entrada for grande o suficiente para não caber na memória? E se o processo que ingere o arquivo for encerrado abruptamente no meio do caminho? Como lidamos com essas situações de forma eficiente e tolerante a falhas?

O Spring Batch vem com um modelo de processamento que é projetado e implementado para lidar com esses desafios. Ele é chamado de modelo de processamento orientado a pedaços (Chunk-Oriented). A ideia desse modelo é processar a fonte de dados em blocos (Chunks) de um tamanho configurável.

Um chunk é uma coleção de "itens" da fonte de dados. Um item pode ser uma linha em um arquivo simples, um registro em uma tabela de banco de dados, etc.

Um pedaço de itens é representado pela API *Chunk<T>*, que é um wrapper em torno de uma lista de objetos do tipo "T". O tipo genérico "T" representa o tipo de itens. Isso significa que os itens podem ser de qualquer tipo.
```java
public class Chunk<T> implements Iterable<T> {

   private List<T> items;

   // methods to add and get items
}
```

### Transações
Cada pedaço de itens é lido e escrito dentro do escopo de uma transação. Dessa forma, os pedaços são confirmados juntos ou revertidos juntos, o que significa que todos eles são bem-sucedidos ou falham juntos. Você pode pensar nisso como uma abordagem "tudo ou nada", mas apenas para o pedaço específico que está sendo processado.

Se a transação for confirmada, o Spring Batch registra, como parte da transação, o progresso da execução (contagem de leituras, contagem de gravações, etc.) em seu repositório de metadados e usa essas informações para reiniciar de onde parou em caso de falha.

Se ocorrer um erro durante o processamento de um bloco de itens, a transação será revertida pelo framework. Portanto, o estado de execução não será atualizado e o Spring Batch reiniciará do último ponto de salvamento bem-sucedido em caso de falha.

O número de itens a serem incluídos em um bloco é chamado de intervalo de confirmação , que é o tamanho configurável de um bloco que deve ser processado dentro do escopo de uma única transação.

## Lendo Dados
Ler dados de um arquivo simples é diferente de ler dados de uma tabela de banco de dados ou de uma fila de broker de mensagens. Por esse motivo, o Spring Batch fornece uma interface de estratégia para ler itens de forma consistente e independente de implementação.

### A interface ItemReader
A leitura de dados no Spring Batch é feita por meio da interface ItemReader, que é definida da seguinte forma:

``` java
@FunctionalInterface
public interface ItemReader<T> {
   @Nullable
   T read() throws Exception;
}
```
Esta interface funcional fornece um único método chamado read para ler um pedaço de dados, ou um item. Cada chamada para este método deve retornar um único item, um de cada vez.

O Spring Batch chamará read conforme necessário para criar blocos de itens. Dessa forma, o Spring Batch nunca carrega a fonte de dados inteira na memória, apenas blocos de dados.

O tipo de item T é genérico, então cabe à implementação decidir qual tipo de item será retornado.

### Manipulação de erros
Se ocorrer um erro durante a leitura de um item, espera-se que as implementações gerem uma exceção para sinalizar o erro ao framework.

Observe como o método read é anotado com *@Nullable*, o que significa que ele pode retornar null.

``` java
@Nullable
T read() throws Exception;
```

Um retorno null desse método é a maneira de sinalizar para o framework que a fonte de dados está esgotada. Lembre-se, o processamento em lote é sobre processar conjuntos de dados finitos, não fluxos infinitos de dados.

### A biblioteca ItemReader
O Spring Batch vem com uma grande biblioteca de implementações ItemReader para ler dados de uma variedade de fontes de dados, como arquivos, bancos de dados, corretores de mensagens, etc. Como desenvolvedor do Spring Batch, normalmente você só precisará configurar um desses leitores e usá-lo em uma etapa.

Em seguida um exemplo para configurar o FlatFileItemReader:
``` java
@Bean
public FlatFileItemReader<BillingData> billingDataFileReader() {
    return new FlatFileItemReaderBuilder<BillingData>()
            .name("billingDataFileReader")
            .resource(new FileSystemResource("staging/billing-data.csv"))
            .delimited()
            .names("dataYear", "dataMonth", "accountId", "phoneNumber", "dataUsage", "callDuration", "smsCount")
            .targetType(BillingData.class)
            .build();
}
```
Neste exemplo, um bean do tipo *FlatFileItemReader* que retorna itens do tipo *BillingData*. Este tipo representa um item do arquivo de dados de cobrança. Para construir um FlatFileItemReader, usaremos a API *FlatFileItemReaderBuilder* para especificar o caminho do arquivo de entrada, os campos esperados naquele arquivo e o tipo de destino para mapear os dados.

A menos que você tenha um requisito muito específico para implementar um leitor de item personalizado, você deve ser capaz de aproveitar um dos leitores de item fornecidos pelo Spring Batch prontos para uso, conforme mostrado acima. Para outros tipos de leitores de itens veja a [documentação](https://docs.spring.io/spring-batch/docs/5.0.4/reference/html/appendix.html#itemReadersAppendix)

## Escrevendo Dados
Semelhante à interface *ItemReader*, a gravação de dados com o Spring Batch é feita por meio da interface *ItemWriter*, que é definida da seguinte forma:

``` java
@FunctionalInterface
public interface ItemWriter<T> {

   void write(Chunk<? extends T> chunk) throws Exception;
}
```

### Escrevendo pedaços (chunks)
O método *write* espera um pedaço de itens. Ao contrário da leitura de itens, que é feita um item por vez, a escrita de itens é feita em pedaços. O motivo para isso é que gravações em massa são tipicamente mais eficientes do que gravações únicas.

Por exemplo, o *JdbcBatchItemWriter*, que é projetado para gravar itens em uma tabela de banco de dados relacional, dispara a API JDBC Batch  para inserir itens no modo batch. Isso não seria possível se a interface *ItemWriter* fosse projetada para gravar um item por vez.

O Spring Batch fornece várias implementações da interface *ItemWriter* para gravar dados em uma variedade de alvos, como arquivos, bancos de dados e corretores de mensagens. Para documentação de outros escritores veja a [documentação](https://docs.spring.io/spring-batch/docs/5.0.4/reference/html/appendix.html#itemWritersAppendix)

Observação: se ocorrer um erro ao escrever itens, espera-se que as implementações desta interface gerem uma exceção para sinalizar o problema ao framework.

### Exemplo ItemWriter
Para a primeira etapa do nosso trabalho de *Billing*, precisamos gravar dados em uma tabela de banco de dados relacional, então usaremos um *JdbcBatchItemWriter* para isso. Aqui está um exemplo de como configurar tal gravador:

``` java
@Bean
public JdbcBatchItemWriter<BillingData> billingDataTableWriter(DataSource dataSource) {
    String sql = "insert into BILLING_DATA values (:dataYear, :dataMonth, :accountId, :phoneNumber, :dataUsage, :callDuration, :smsCount)";
    return new JdbcBatchItemWriterBuilder<BillingData>()
            .dataSource(dataSource)
            .sql(sql)
            .beanMapped()
            .build();
}
```
Acima note como o  *JdbcBatchItemWriter* é construído usando construções específicas de banco de dados, como uma instrução SQL e um DataSource. Considere que outros escritores, como *FlatFileItemWriter*, seriam construídos usando construções que são específicas para essas implementações.

### Configurando etapas de tasklet orientadas a blocos
Uma etapa orientada a bloco (chunk) no Spring Batch é uma *TaskletStep* configurada com um tipo de *Tasklet* específico, o *ChunkOrientedTasklet*. Essa "Tasklet" que implementa o modelo de processamento orientado a blocos (chunk), usando um leitor de itens (item reader) e um escritor de itens (item writter).

Semelhante à configuração de um *TaskletStep* com uma *tasklet* personalizada, a configuração de um *tasklet* orientado a blocos também é feita por meio da API *StepBuilder*, exceto que precisaremos chamar o método *chunk* ao invés do método *tasklet*.

### Ingestão de arquivo
A etapa de ingestão de arquivo tem como objetivo ler dados de um arquivo simples e gravá-los em um banco de dados relacional. Portanto, supondo que o *FlatFileItemReader<BillingData>* e *JdbcBatchItemWriter<BillingData>* mostrados anteriormente, veja como configurar a etapa (step):

``` java
@Bean
public Step ingestFile(
              JobRepository jobRepository,
              PlatformTransactionManager transactionManager,
              FlatFileItemReader<BillingData> billingDataFileReader,
              JdbcBatchItemWriter<BillingData> billingDataTableWriter) {

    return new StepBuilder("fileIngestion", jobRepository)
            .<BillingData, BillingData>chunk(100, transactionManager)
            .reader(billingDataFileReader)
            .writer(billingDataTableWriter)
            .build();
}
```
Como visto anteriormente, precisaremos especificar o nome do passo e o repositório de trabalho. Isso é comum para todos os tipos de passo (step). Como estamos criando uma *TaskletStep* orientada a bloco (chunk-oriented), precisaremos fornecer dois parâmetros:

O primeiro parâmetro é o tamanho do bloco *(chunk-size)*, ou o *commit-interval*. Neste caso, ele é definido como 100, o que significa que o Spring Batch lerá e gravará 100 itens como uma unidade em cada transação.
O segundo parâmetro é uma referência a a *PlatformTransactionManager* para gerenciar as transações do *tasklet*. Lembre-se, cada chamada a *Tasklet.execute* é feita dentro do escopo de uma transação.
Por fim, a API *StepBuilder.chunk*  orienta o usuário a configurar melhor o *tasklet* orientado a blocos, especificando o leitor e o gravador de itens a serem usados ​​por meio dos métodos *reader* e *writer* respectivamente.

# Pratica - 5

Antes de começar, é importante explanar sobre o arquivo a ser manipulado nessa prática.
Compreendendo o arquivo *billing-2023-01.csv*

|Column	            |Type	|Description
|-------------------|-------|--------------------------------------------------------
|Year	            |int	|The year during which the data was captured for the customer
|Month	            |int	|The month during which the data was captured for the customer
|Account Identifier	|int	|The account identifier associated with the customer
|Phone Number	    |String	|The phone number that the usage is associated with
|Data Usage	        |float	|The sum of data used for the month in MB
|Call Duration	    |int	|The sum of calls used for the month in minutes
|SMS Count	        |int	|The number of text messages sent during the month

Exemplo de dados contidos no arquivo *billing-2023-01.csv*
|Year	|Month	|Account N. |Phone Number	|Data Usage	|Call Duration	|SMS Count
|-------|-------|-----------|---------------|-----------|---------------|---------
|2023	|1	    |100	    |404-555-1234	|12.5	    |0	            |3
|2023	|1	    |101	    |404-555-5678	|7.8	    |5	            |1
|2023	|1	    |102	    |404-555-9101	|45.2	    |2	            |0
|2023	|1	    |104	    |404-555-2345	|22.1	    |0            	|1
|2023	|1	    |105	    |404-555-6789	|8.9	    |10	            |0
|2023	|1	    |106	    |404-555-1112	|3.2	    |3	            |2
|2023	|1	    |107	    |404-555-1314	|17.6	    |0	            |4
|2023	|1	    |108	    |404-555-1516	|28.9	    |7	            |1
|2023	|1	    |109	    |404-555-1718	|11.3	    |2	            |3
