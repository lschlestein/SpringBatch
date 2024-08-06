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

## Configura a etapa de ingestão de arquivos

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

### Configurando um Item Reader
Como o arquivo de entrada é simples, deve-se utilizar FlatFileItemReader<T>. Esse leitor suporta vários tipos de arquivos delimitados simples como (CSV, TSC, etc). Embora seja possível retornar arrays de Strings ou matrizes, para retornar uma classe, que representará cada item. Para o exemplo específico, a classe BillingData.java será escrita como segue:
``` java
package example.billingjob;

public record BillingData (
        int dataYear,
        int dataMonth,
        int accountId,
        String phoneNumber,
        float dataUsage,
        int callDuration,
        int smsCount) {
}
```
O tipo Record representa um record, ou melhor, uma linha do arquivo de entrada. Note que os tipos do Record, são compatíveis com os tipos oriundos do arquivo de entrada.

### Criar o billingDataFileReader
Alterar o BillingJobConfiguration.java, adicionando o seguinte bean:
```java
@Bean
public FlatFileItemReader<BillingData> billingDataFileReader() {
    return new FlatFileItemReaderBuilder<BillingData>()
            .name("billingDataFileReader")
            .resource(new FileSystemResource("staging/billing-2023-01.csv"))
            .delimited()
            .names("dataYear", "dataMonth", "accountId", "phoneNumber", "dataUsage", "callDuration", "smsCount")
            .targetType(BillingData.class)
            .build();
}
```

Neste trecho, definimos um bean denominado *billingDataFileReader* do tipo *FlatFileItemReader<BillingData>*. Usamos *FlatFileItemReaderBuilder* para criar o leitor e especificamos algumas propriedades, como o nome do leitor e o arquivo de entrada staging/billing-2023-01.csv.

Também informamos ao leitor que se espera que o arquivo de entrada seja delimitado (usando o método .delimited()) e que se espera que as colunas sejam definidas em uma ordem específica, que corresponde à lista de campos (.names(... )) no tipo de destino BillingData.

Com isso definido, o leitor de item lerá as linhas uma por uma e criará uma nova instância de BillingData para cada linha.

Observe como, com apenas algumas linhas de código de configuração, o Spring Batch realmente nos economizou muito código padrão para ler o arquivo de entrada (abrir/fechar o arquivo), ler o arquivo linha por linha, analisar campos e mapear dados para tipos correspondentes em Billing Data.

### Configurando um Item Writer
Criar a tabela onde os dados importados serão armazenados:
``` sql
create table BILLING_DATA
(
    DATA_YEAR     INTEGER,
    DATA_MONTH    INTEGER,
    ACCOUNT_ID    INTEGER,
    PHONE_NUMBER  VARCHAR(12),
    DATA_USAGE    FLOAT,
    CALL_DURATION INTEGER,
    SMS_COUNT     INTEGER
);
```

Para verificar se a tabela foi criada corretamente executar no terminal:
``` bash
docker exec postgres psql -U postgres -c 'select count(*) from BILLING_DATA;'
```

### Criar o billingDataTableWriter.
Como utilizaremos uma conexão JDBC para escrever nossos dados, o escritor de item ou, item writer mais adequado a ser utilizado é o *JdbcBatchItemWriter<T>*. Esse item writer escreverá dados em um BD via API JDBC.

Alterar o BillingJobConfiguration.java, adicionando o seguinte bean:

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

### O JdbcBatchItemWriter em Detalhes
O JdbcBatchItemWriter precisa conhecer o banco de dados de destino para gravar dados e a instrução SQL para executar.

No trecho anterior, definimos um bean denominado *billingDataTableWriter* do tipo *JdbcBatchItemWriter<BillingData>*. A fonte de dados foi passada como parâmetro para o método, que representa o banco de dados de destino no qual os dados devem ser armazenados.

Também definimos a instrução SQL insert que o escritor *(writter)* deve invocar para inserir itens. Esta instrução especifica a tabela de destino *BILLING_DATA* criada anteriormente e também a lista de colunas a serem inseridas. Observe como a lista de colunas (:dataYear, :dataMonth, etc) corresponde aos nomes dos campos do tipo *BillingData*.

Mas como o criador do item vincularia os dados dos objetos BillingData criados pelo leitor às colunas da tabela usando a sintaxe :fieldName?

É aqui que entra em ação a chamada para o método *.beanMapped()*. Este método instrui o escritor a usar a API Java Reflection para chamar métodos getter para obter o valor de cada campo com o mesmo nome da coluna do banco de dados. Por exemplo, para vincular a coluna :dataYear na consulta SQL, o gravador chamará dataYear() na instância *BillingData* do item atual.

## Configurar a etapa de ingestão de arquivos
Agora, o próximo passo, será definir o passo (step) de ingestão de arquivo.

Alterar o BillingJobConfiguration.java, adicionando o seguinte bean:
``` java
@Bean
public Step step2(
   JobRepository jobRepository, JdbcTransactionManager transactionManager,
   ItemReader<BillingData> billingDataFileReader, ItemWriter<BillingData> billingDataTableWriter) {
    return new StepBuilder("fileIngestion", jobRepository)
            .<BillingData, BillingData>chunk(100, transactionManager)
            .reader(billingDataFileReader)
            .writer(billingDataTableWriter)
            .build();
}
```
### O Step-2 em detalhes (ingestion step)

Como visto anteriormente, todos os tipos de etapas exigem pelo menos o repositório de tarefas e o nome da etapa. Neste caso, o repositório de tarefas é passado como parâmetro para o método de definição do bean e o nome da etapa é fileIngestion.

Como está sendo criada uma etapa orientada a bloco *(chunk-oriented)*, que é um *TaskletStep*, precisamos também passar uma referência para um gerenciador de transações e especificar o tamanho do bloco *(chunk)*, que neste caso é 100. Isso é feito chamando o método .chunk(...).

A sintaxe <BillingData,BillingData>chunk(...) é usada para informar ao Spring Batch que a entrada e a saída da etapa são do tipo *BillingData*, o que significa que o leitor retornará itens do tipo *BillingData* e que o escritor *(writter)* escreverá itens do tipo *BillingData* também.

Ou seja, esta etapa não altera o tipo de itens durante sua execução. Os objetos lidos e escritos são do mesmo tipo. É possível alterar o tipo de item caso os dados devam ser transformados durante o processamento. 

Atenção: O valor do tamanho do bloco depende muito do caso de uso e deve ser definido de forma empírica. O valor 100 geralmente é um bom ponto de partida, mas nem sempre é esse o caso.

Por fim, definimos o item leitor e gravador usando os métodos .reader() e .writer() respectivamente. O leitor e o gravador são passados ​​como parâmetros para o método de definição de bean, o que significa que eles serão conectados automaticamente pelo Spring a partir das definições de bean que configuramos nas seções anteriores.

## Rodando o Job

Antes de rodar o *Job* novamente, é necessário definir a sequência de passos no bean *Job*. Precisamos alterar o *Job* adicionando mais um passo *(step)*, conforme segue:
Alterar a classe BillingJobConfiguration.java
``` java
@Bean
public Job job(JobRepository jobRepository, Step step1, Step step2) {
    return new JobBuilder("BillingJob", jobRepository)
            .start(step1)
            .next(step2)
            .build();
}
```
Em comparação com a versão anterior, adicionamos *step2* ao fluxo sequencial usando a chamada do método .next(step2). Com isso, o Spring Batch executará a etapa *fileIngestion* após a etapa *filePreparation*. Vamos executar o trabalho e verificar isso a seguir:

No terminal, empacotar e rodar a aplicação.
``` bash
./mvnw clean package -Dmaven.test.skip=true
```

Em seguida executar o jar:
``` bash
java -jar target/billing-job-0.0.1-SNAPSHOT.jar input.file=src/main/resources/billing-2023-01.csv
```

No log, as step1 (filePreparation) e step2 (fileIngestion) deverão ser executadas:

Os dados agora devem ter sido inseridos no banco de dados:
``` bash
docker exec postgres psql -U postgres -c 'select count(*) from BILLING_DATA;'
```
Deverão ser retornadas 1000 linhas, ou registros no select acima.

# Processando Dados

Anteriormente configuramos as etapas orientadas a blocos, buscando dados em um arquivo simples .CSV, e os "movemos" para uma banco de dados. Embora essa seja uma operação comum, é raro não haver nenhum tipo de processamento sobre os dados, antes de serem persistidos.

Aboradaremos agora esse processamento é feito, utilizando Spring Batch.

### A API ItemProcessor
O processamento de itens em uma etapa orientada a blocos *(chunk-oriented)* acontece entre a leitura e a gravação de dados. É uma fase opcional do modelo de processamento orientado a bloco, onde os itens retornados pelo leitor são processados ​​e, em seguida, entregues ao escritor.

O processamento de itens no Spring Batch é feito implementando a interface *ItemProcessor*, que é definida da seguinte forma:
``` java
@FunctionalInterface
public interface ItemProcessor<I, O> {

   @Nullable
   O process(@NonNull I item) throws Exception;
}
```
Esta é uma interface funcional com um único método *process*. Este método pega um item do tipo *I* como entrada e retorna um item do tipo *O* como saída. O nome do método *process* é genérico de propósito, pois o processamento de dados é um termo amplo e abrange diferentes casos de uso, como transformar dados, enriquecê-los, validá-los ou filtrar os dados.

### Transformando Dados
O método *process* pega um item do tipo *I* como entrada e retorna um item do tipo *O* como saída. Essa distinção clara entre tipos de entrada e saída é projetada para permitir que os desenvolvedores alterem o tipo do item durante a fase de processamento. Isso é útil ao adaptar os dados de entrada ao formato esperado pelo sistema de destino.

Aqui está um exemplo de um processador de itens que transforma itens de um tipo hipotético *BillingData* para outro tipo *ReportingData*:
``` java
public class BillingDataProcessor implements ItemProcessor<BillingData, ReportingData> {

   public ReportingData process(BillingData item) {
       return new ReportingData(item);
   }
}
```
Embora seja possível alterar o tipo de item durante o processamento, e se não precisarmos disso? Ou seja, e se o tipo de entrada *I* for o mesmo que o tipo de saída *O*? Isso é perfeitamente possível, conforme veremos em seguida.

### Enriquecendo Dados
Transformar dados não é o único caso de uso de um *ItemProcessor*. Na verdade, itens não são necessariamente transformados de um tipo para outro.

Em muitos trabalhos de processamento em lote, o requisito é enriquecer os dados de entrada com detalhes adicionais antes de os persistir no sistema de destino. Nesse caso, os itens não são transformados de um tipo para outro, mas enriquecidos com informações adicionais. Por exemplo, um processador de itens pode solicitar que um sistema externo enriqueça o item atual e, em seguida, retorne o item enriquecido:
``` java
public class EnrichingItemProcessor implements ItemProcessor<Person, Person> {

   private AddressService addressService;

   public EnrichingItemProcessor(AddressService addressService) {
      this.addressService = addressService;
   }

   public Person process(Person person) {
      Address address = this.addressService.getAddress(person);
      person.setAddress(address);
      return person;
   }
}
```
Neste *EnrichingItemProcessor*, itens do tipo *Person* são enriquecidos com o endereço da pessoa usando um *AddressService*. Este é um exemplo típico de um processador de itens que enriquece dados sem transformá-los.

### Validando Dados
O método *process* é projetado para lançar uma exceção em caso de erro de processamento. Erros de processamento podem ser erros técnicos (como falha em chamar um serviço externo) ou erros funcionais (como itens inválidos).

Um dos casos de uso mais comuns de um processador de itens é a validação de dados. Depois que lemos itens de uma fonte de entrada, podemos precisar validar se os dados de entrada são válidos ou não antes de salvá-los no sistema de destino. Em sistemas empresariais típicos, os dados geralmente são consumidos de uma fonte confiável, mas nem sempre é esse o caso. Na verdade, em muitos sistemas de lote corporativos, os dados são consumidos de fontes externas que podem não ser confiáveis, nesse caso, validar os dados é crucial para a segurança e integridade do sistema de destino.

An *ItemProcessor* é o lugar ideal para implementar regras de validação de dados. Veja o exemplo:
``` java
public class ValidatingItemProcessor implements ItemProcessor<Person, Person> {

   private EmailService emailService;

   public ValidatingItemProcessor(EmailService emailService) {
      this.emailService = emailService;
   }

   public Person process(Person person) {
      if (!this.emailService.isValid(person.getEmail()) {
         throw new InvalidEmailException("Invalid email for " + person);
      }
      return person;
   }
}
```
A classe *ValidatingItemProcessor* foi escrita para validar o e-mail da pessoa usando um *EmailService* hipotético, e rejeitar itens inválidos lançando um *InvalidEmailException*. Por outro lado, se o e-mail da pessoa for válido, o item é retornado como está.

### Filtrando dados

O último resultado do método *process* que ainda não abordamos é quando o método retorna *null*. Como o Spring Batch interpreta tal resultado do processador de itens?

O retorno *null* do método *process* diz ao Spring Batch para filtrar o item atual *Stream#filter* . Filtrar o item atual significa simplesmente não deixá-lo continuar no pipeline de processamento. Portanto, ele será removido da escrita como parte do pedaço atual.

A filtragem de dados é outro caso de uso típico para um processador de itens. A última etapa do nosso *BillingJob* é gerar um relatório de faturamento para clientes que gastaram mais de US$ 150 por mês. Isso significa que devemos filtrar todos os clientes que gastaram menos do que esse valor. Veja o exemplo a seguir:
``` java
public class FilteringItemProcessor implements ItemProcessor<BillingData, BillingData> {

   public BillingData process(BillingData item) }
      if (item.getMonthlySpending() < 150) {
         return null; // filter customers spending less than $150
      }
      return item;
   }
}
```
Neste exemplo, itens com o valor *monthlySpending* inferior a US$ 150 são filtrados, ou melhor, definidos como *null* e não serão incluídos no relatório final.

# Prática - 6
O objetivo dessa prática, é processar as informações adquiridas do arquivo *billing-2023-01.csv*. Será gerado um novo relatório de faturamento, filtrando clientes que tiveram um gasto maior de U$ 150/mês.
O formato da saída, será igual ao da entrada, somente será adicionada uma coluna com o valor total do faturamento do mês.
* Leremos os dados da tabela *BILLING_DATA*, que populamos anteriormente com o *step fileIngestion*. Agora será calculado o gasto mensal de cada cliente, e serão apresentados os que tiveram gasto superior a U$ 150/mês.

## Lendo dados de faturamento do banco de dados.
Como, nesta prática, leremos dados de uma fonte de dados JDBC, utilizaremos o leitor *JdbcCursorItemReader* do Spring Batch. Os dados são retornados através de um *ResultSet* por esse leitor.

### Criando o bean billingDataTableReader
Adicione o bean na classe BillingJobConfiguration.java conforme segue:
``` java
@Bean
public JdbcCursorItemReader<BillingData> billingDataTableReader(DataSource dataSource) {
    String sql = "select * from BILLING_DATA";
    return new JdbcCursorItemReaderBuilder<BillingData>()
            .name("billingDataTableReader")
            .dataSource(dataSource)
            .sql(sql)
            .rowMapper(new DataClassRowMapper<>(BillingData.class))
            .build();
}
```

### Compreendendo o reader
Neste método de definição de bean, usamos *JdbcCursorItemReaderBuilder* para criar um *JdbcCursorItemReader*. As principais propriedades de configuração deste leitor são a fonte de dados, a consulta SQL para buscar dados e o tipo de item de destino para o qual os dados devem ser mapeados.

No nosso caso, o tipo de item de destino é *BillingData*, que é um Java record.

Agora a questão é: como mapear os valores das colunas das linhas do banco de dados para campos do tipo *BillingData*? Para isso podemos usar o *DataClassRowMapper* do Spring Framework.

A classe *DataClassRowMapper* usa a mesma técnica de reflexão em *JdbcBatchItemWriter.beanMapped* que vimos anteriormente para mapear dados de colunas de banco de dados para campos no tipo de destino *BillingData*. Usamos uma instância de *DataClassRowMapper* chamando o método .rowMapper(new DataClassRowMapper<>(BillingData.class)).
Agora será preciso definir a lógica de processamento do nosso *step*.

### Calculando os dados do relatório
O cálculo dos gastos mensais e a filtragem dos clientes podem ser feitos em um processador de itens.

Para calcular o gasto mensal, precisamos dos preços de uso de dados, chamadas e mensagens de texto. Utilizaremos os seguintes valalores:

Preço das chamadas: US$ 0,50 por minuto
Preço de texto/SMS: US$ 0,10 cada
Preço de dados: US$ 0,01 por MB
Esta configuração de preços pode variar e deve ser externalizada nas propriedades de configuração. Por esse motivo, iremos implementá-los como propriedades de configuração do Spring com os detalhes de preços anteriores como valores padrão. Por fim, criaremos um novo Java record que representa os dados do relatório, que inclui todos os detalhes do *BillingData* com um novo campo para o valor total do faturamento.

### Criando o novo tipo ReportingData
Crie um novo Java record de nome ReportingData conforme segue:
```java
package example.billingjob;

public record ReportingData(BillingData billingData, double billingTotal) {
}
```
Este record contém as informações de faturamento de cada cliente, bem como o total gasto no campo *billingTotal*, que mostra o valor total gasto por cada cliente. Esse campo será calculado e preenchido posteriormente por um processador de itens.

### Criando o novo processador de itens BillingDataProcessor
Crie um novo arquivo Java, de nome BillingDataProcessor.java, conforme segue:
``` java
package example.billingjob;

import org.springframework.batch.item.ItemProcessor;
import org.springframework.beans.factory.annotation.Value;

public class BillingDataProcessor implements ItemProcessor<BillingData, ReportingData> {

    @Value("${spring.cellular.pricing.data:0.01}")
    private float dataPricing;

    @Value("${spring.cellular.pricing.call:0.5}")
    private float callPricing;

    @Value("${spring.cellular.pricing.sms:0.1}")
    private float smsPricing;

    @Value("${spring.cellular.spending.threshold:150}")
    private float spendingThreshold;

   @Override
   public ReportingData process(BillingData item) {
      double billingTotal = item.dataUsage() * dataPricing + item.callDuration() * callPricing + item.smsCount() * smsPricing;
      if (billingTotal < spendingThreshold) {
         return null;
      }
      return new ReportingData(item, billingTotal);
   }
}
```
Compreendendo o código criado acima. Nossa nova classe de processador implementa a interface *ItemProcessor<BillingData,ReportingData>*.

Nesta classe, calculamos o gasto mensal do cliente atual e filtramos os clientes que gastaram menos de US$ 150 (retornando null nesse caso).

Os detalhes de preços são declarados como propriedades Spring no namespace spring.celluar.* e podem ser configurados externamente via application.properties. Essas propriedades possuem valores padrão que adotaremos nesse exemplo.

Feito o cálculo e a filtragem, criamos um objeto *ReportingData* com as informações de faturamento e também os gastos mensais.

Este processador de itens é uma combinação de diferentes tipos de processamento: ele não apenas enriquece o item atual com novos dados, mas também filtra itens que não são necessários para os requisitos comerciais atuais.

### Declarando o bean BillingDataProcessor
Na classe BillingJobConfiguration.java, adicione um novo bean, conforme segue:
``` java
@Bean
public BillingDataProcessor billingDataProcessor() {
    return new BillingDataProcessor();
}
```
É necessário declarar o *BillingDataProcessor* como bean para que ele seja gerenciado pelo Spring e configurado com as propriedades que declaramos nele.
Com nossa nova implementação de processador e definição de bean em vigor, somente os clientes que gastaram mais de US$ 150 serão repassados ​​ao escritor de itens e incluídos no relatório final. Em seguida, configuraremos o escritor.
