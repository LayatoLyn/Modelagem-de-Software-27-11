# Defesa de Arquitetura de Software — Pull Request

## Autor do PR
- **Nome do Estudante:** Rafael Mateus Almeida de Souza
- **Artefato(s) de Responsabilidade:** `Loop-telemetria.mmd` (Diagrama de Sequência – Mermaid)

## O que este PR introduz na Documentação Viva?

 Atualização no loop de telemetria do projeto de Monitoramento IoT de Manutenção Preventiva. O objetivo foi melhorar a representação do fluxo de informações entre os sensores das máquinas e os componentes responsáveis pelo processamento, armazenamento e visualização dos dados.

As principais alterações realizadas foram:
- Organização do fluxo de comunicação entre Sensores IoT, EMQX, Apache Kafka, Analisador Preditivo, TimescaleDB e Grafana.
- Inclusão de uma etapa de validação dos dados recebidos pelo EMQX.
- Inclusão da consulta aos dados históricos armazenados no TimescaleDB para comparação com as informações atuais.
- Melhor separação entre o processamento dos dados e a visualização das informações.
- Inclusão de fluxos alternativos (`alt/else`) para representar situações em que os dados são recebidos corretamente ou apresentam falhas no recebimento.
- Inclusão da identificação de possíveis anomalias e do registro de alertas para visualização pelo técnico.
- Representação do monitoramento como um processo contínuo, utilizando o bloco `loop`.

**Modelos adicionados ou atualizados:**

- [ ] **Visão Funcional / Estrutural:** Não foram realizadas alterações nos diagramas de casos de uso ou de classes.
- [x] **Visão Comportamental:** Atualização do Diagrama de Sequência do fluxo de telemetria, incluindo comunicação assíncrona, condições alternativas e monitoramento contínuo.
- [ ] **Visão Arquitetural Macro:** Não foram realizadas alterações no modelo C4.
- [ ] **Governança:** Não foi criado um novo registro de decisão arquitetural (ADR).

## Defesa das Decisões de Design (Justificativa Técnica)

### 1. Escolha de Abstrações e Desacoplamento

A arquitetura foi organizada para que cada componente tenha uma responsabilidade específica dentro do processo de monitoramento.

- **Sensores IoT:** Responsáveis por coletar os dados de temperatura e vibração das máquinas.
- **EMQX:** Responsável por receber as mensagens enviadas pelos sensores utilizando o protocolo MQTT.
- **Apache Kafka:** Responsável por realizar a distribuição das mensagens entre os componentes do sistema.
- **Analisador Preditivo:** Responsável por validar os dados, consultar informações históricas e executar a análise para identificar possíveis anomalias.
- **TimescaleDB:** Responsável por armazenar os dados atuais, históricos e registros relacionados às análises.
- **Grafana:** Responsável por apresentar os dados e alertas em dashboards para acompanhamento do técnico.

Essa separação permite que os componentes trabalhem de maneira mais independente, evitando que uma única parte do sistema fique responsável por todo o processamento.

Uma alteração importante foi separar o recebimento e a distribuição dos dados da etapa de análise. Assim, o EMQX e o Kafka ficam responsáveis pelo transporte das informações, enquanto o analisador realiza o processamento dos dados recebidos.

### 2. Dinâmica e Resiliência

O fluxo foi atualizado para representar melhor algumas situações que podem acontecer durante o funcionamento do sistema.

Foram adicionadas condições alternativas (`alt/else`) para diferenciar o recebimento correto dos dados de uma possível falha na comunicação.

Quando os dados são recebidos corretamente pelo EMQX, eles são encaminhados ao Kafka e posteriormente ao analisador preditivo. O analisador consulta os dados históricos no TimescaleDB e compara essas informações com os valores atuais dos sensores.

Caso seja identificada alguma anomalia, o sistema registra um alerta no banco de dados e disponibiliza essa informação para o Grafana, permitindo que o técnico acompanhe a situação da máquina.

Caso nenhuma anomalia seja encontrada, os dados e o resultado da análise também são armazenados, mantendo um histórico para consultas futuras.

Também foi mantido o bloco `loop`, representando que a coleta e o processamento das informações acontecem continuamente.

Em caso de falha no recebimento dos dados, o EMQX registra o erro e interrompe o encaminhamento daquela mensagem no fluxo representado.

Essa representação melhora o entendimento do comportamento do sistema em situações normais e alternativas. Entretanto, mecanismos de retentativa automática e recuperação de mensagens não foram definidos nesta versão do modelo.

### 3. Trade-offs

Durante a atualização do diagrama, foram consideradas algumas vantagens e desvantagens das decisões tomadas.

**Vantagens:**

- Melhor organização e separação das responsabilidades dos componentes.
- Possibilidade de comparar informações atuais com dados históricos das máquinas.
- Melhor identificação de possíveis anomalias por meio do analisador preditivo.
- Melhor visualização das informações para o técnico através do Grafana.

**Desvantagens:**

- A utilização de vários componentes aumenta a complexidade da arquitetura.
- A comunicação entre os serviços pode gerar atrasos até que os dados sejam processados e apresentados.
- A consulta frequente ao banco de dados pode aumentar a carga de processamento.
- A implementação de mecanismos de recuperação de mensagens e retentativas poderá exigir configurações adicionais.

## Consistência Cross-Diagramas (Checklist de Autoverificação)

- [ ] Os nomes de métodos chamados no Diagrama de Sequência existem nas interfaces do Diagrama de Classes. **(Pendente: o diagrama de sequência não define métodos específicos e as interfaces precisam ser verificadas.)**
- [x] Os componentes citados correspondem à arquitetura definida no projeto: EMQX, Apache Kafka, TimescaleDB e Grafana. O Analisador Preditivo representa a etapa de análise prevista no escopo.
- [x] O fluxo de coleta, transmissão, análise e armazenamento está alinhado à proposta de monitoramento contínuo e à máquina de estados do sensor.
- [x] A identificação de anomalias está relacionada aos estados de alerta e parada crítica previstos no diagrama de estados da máquina.
- [ ] As transições de estado foram verificadas individualmente com todos os gatilhos do novo fluxo. **(Pendente de revisão conjunta dos diagramas.)**

## Validação da Sintaxe Diagrams-as-Code / Models-as-Code

- **Ferramenta de Compilação/Renderização Utilizada:** Mermaid Live Editor.
- **Linguagem Utilizada:** Mermaid (`sequenceDiagram`).
- **Status da Compilação:** [ ] Código limpo e sem erros de sintaxe ao renderizar.

**Observação:** A renderização e a validação final do código devem ser realizadas antes da aprovação do Pull Request.