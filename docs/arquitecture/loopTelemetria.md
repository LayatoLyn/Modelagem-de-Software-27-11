```mermaid
flowchart LR

flowchart LR
    OP["Operador"]
    MAQ["Máquina Industrial"]
    SEN["Sensor IoT"]
    MB["Message Broker"]
    BD[("Banco de Dados")]
    UI["Interface Gráfica"]

    MAQ -->|"Gera dados operacionais"| SEN
    SEN -->|"Coleta temperatura, vibração, pressão etc."| MB
    MB -->|"Publica mensagens de telemetria"| BD
    BD -->|"Disponibiliza dados históricos"| UI
    UI -->|"Exibe indicadores e alertas"| OP
    OP -->|"Consulta condições da máquina"| UI

    UI -->|"Solicita atualização do monitoramento"| MB
    MB -->|"Mantém fluxo contínuo de telemetria"| SEN

    
```