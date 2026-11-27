```mermaid

flowchart TD

%% Diagrama geral do processo de manutenção preventiva.
subgraph IoT Preventiva

    %% Etapa de telemetria: coleta, armazenamento e análise dos dados da máquina.
    subgraph Telemetria
        S[Sensor] ## Coleta informações da máquina.
        A[Analise] %% Analisa os dados recebidos e identifica possíveis anormalidades.
        Sis[Sistema] %% Apresenta os dados para acompanhamento.
        B[Banco de dados] %% Armazena os dados coletados para consultas futuras.

        %% Ponto de divisão ou junção dos fluxos de dados.
        F@{ shape: "fork", label: "Fork or Join" }
    end

    %% Etapa de disparo da Ordem de Serviço (OS) e execução da manutenção.
    subgraph Disparo OS
        T[Tecnico] %% Representa o técnico responsável pelo reparo ou manutenção.
        O[OS] %% Ordem de Serviço gerada após a identificação de uma anormalidade.
        M[Maquina] %% Representa a máquina que necessita de manutenção.

        %% Divide o fluxo para direcionar as informações ao técnico e à máquina.
        F2@{ shape: "fork", label: "Fork or Join" }

        %% Reúne os fluxos após a definição do técnico e da máquina.
        F3@{ shape: "fork", label: "Fork or Join" }
    end

end

%% O sensor envia os dados coletados para o ponto de divisão.
S ---> F

%% Uma parte dos dados é armazenada no banco de dados.
F -- Armazena dados --> B

%% Outra parte dos dados é apresentada no sistema.
F -- Mostra dados --> Sis

%% O sistema encaminha os dados atuais para comparação com informações anteriores.
Sis -- Compara com dados antigos --> A

%% O banco de dados fornece informações para comparação com as novas leituras.
B -- Compara com dados novos --> A

%% Se não houver anormalidades, o monitoramento continua com novas leituras.
A -- Sem anormalidades --> S

%% Se uma anormalidade for detectada, é acionada uma Ordem de Serviço.
A -- Anormalidade detectada --> O

%% A Ordem de Serviço direciona o fluxo para a definição dos responsáveis e do equipamento.
O ---> F2

%% Uma saída identifica o técnico que realizará o reparo ou a manutenção.
F2 -- Tecnico que efetuara reparo/manutencao --> T

%% Outra saída identifica a máquina que precisa de reparo ou manutenção.
F2 -- Maquina que necessita de reparo/manutencao --> M

%% Após a definição do técnico, seu fluxo segue para o ponto de junção.
T ---> F3

%% A máquina também segue para o ponto de junção.
M ---> F3

%% Depois que o reparo ou a manutenção é finalizado, o monitoramento é retomado.
F3 -- Reparo/Manutencao finalizado --> S

```
