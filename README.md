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



