```mermaid
flowchart TD
    %% Comentário de Nicolas: O código está apresentando o erro "Unable to render rich display" ao tentar rodar pelo github
    
    %% RAIA 1: representa as atividades executadas pelos sensores IoT.
    %% Os sensores coletam os dados e os encaminham à plataforma.
    subgraph R1["RAIA 1 - SENSORES IoT"]
        A([Inicio]) %% Marca o início do processo de monitoramento.
        B["Coletar temperatura e vibracao"] %% Lê os valores de temperatura e vibração da máquina.
        C["Montar LeituraSensorDTO"] %% Organiza os dados coletados em um objeto de transferência (DTO).
        D["Enviar leitura para plataforma"] %% Encaminha o DTO para a plataforma.
    end

    %% RAIA 2: representa o processamento realizado pelo back-end.
    subgraph R2["RAIA 2 - PLATAFORMA BACK-END"]
        E["Receber leitura"] %% A plataforma recebe os dados enviados pelo sensor.
        F{"Leitura valida?"} %% Verifica se os dados recebidos estão completos e em formato aceitável.
        G["Descartar leitura e registrar erro"] %% Rejeita os dados quando a validação falha.
        H["Armazenar leitura no banco"] %% Persiste a leitura válida para consulta e histórico.
        I["Iniciar analise paralela"] %% Dispara a análise dos dois parâmetros.

        %% As duas análises ocorrem em paralelo: temperatura e vibração.
        J["Analisar temperatura"] %% Compara a temperatura com os limites definidos.
        K["Analisar vibracao"] %% Compara a vibração com os limites definidos.

        L["Consolidar resultados"] %% Reúne os resultados das duas análises.
        M{"Existe indicador critico?"} %% Decide se algum parâmetro atingiu uma condição crítica.
        N["Registrar resultado normal ou atencao"] %% Registra o resultado quando não há indicador crítico.
        O["Criar ocorrencia de manutencao"] %% Abre uma ocorrência quando é identificada uma condição crítica.
        P["Registrar ocorrencia"] %% Salva a ocorrência de manutenção.
        Q["Aguardar confirmacao do alerta"] %% Aguarda a confirmação do envio da notificação.
        R([Fim do processamento]) %% Indica o encerramento deste fluxo de processamento.
    end

    %% RAIA 3: representa o serviço responsável por avisar o responsável pela máquina.
    subgraph R3["RAIA 3 - SERVICO DE NOTIFICACAO"]
        S["Preparar alerta de manutencao"] %% Monta a mensagem com as informações da ocorrência.
        T["Enviar notificacao ao responsavel"] %% Envia o alerta ao responsável pela manutenção.
        U["Registrar confirmacao de envio"] %% Registra que o serviço confirmou o envio da notificação.
    end

    %% Caminho inicial: coleta, estruturação e envio da leitura.
    A --> B --> C --> D --> E --> F

    %% Se a leitura for válida, ela é armazenada.
    F -->|"[temperatura e vibracao validas]"| H
    %% Se qualquer um dos valores for inválido, a leitura é descartada e o fluxo termina.
    F -->|"[temperatura ou vibracao invalidas]"| G
    G --> R

    %% Após salvar, a plataforma inicia as duas análises.
    H --> I
    I --> J
    I --> K

    %% Os resultados das duas análises convergem para a consolidação.
    J --> L
    K --> L

    %% A consolidação alimenta a decisão sobre a existência de um indicador crítico.
    L --> M

    %% Sem indicador crítico, apenas registra o estado normal ou de atenção.
    M -->|"[existe indicador critico]"| O
    M -->|"[nao existe indicador critico]"| N

    N --> R

    %% Com indicador crítico, cria e registra uma ocorrência e solicita a notificação.
    O --> P --> S
    S --> T --> U --> Q --> R
```
