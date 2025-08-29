# Curso de Zephyr OS para ITAndroids

O escopo do curso é habilitar os alunos a desenvolver os software embarcados das diferentes categorias da ITAndrdoids usando Zephyr.

Ao fim do curso, os alunos devem saber:
- De Zephyr especificamente:
    - Instalar ambiente e gerenciar dependências
    - Escrever devicetrees, drivers e bindings
    - Utilizar APIs de periféricos (GPIO, ADC, I2C, SPI, UART, USB 1.0)
    - Utilizar as principais funções do kernel
- De sistemas embarcados em geral:
    - Noções básicas do que acontece dentro de um microcontrolador
    - Noções básicas de DMA e RTOS
    - Software embarcado orientado a eventos
    - Máquinas de estados
    - Documentação de design de software embarcado

O código embarcado será desenvolvido exclusivamente em C. Zephyr pode ser programado em C++, mas isso está fora do escopo do curso.

## **Aula 1: Fundamentos do Zephyr OS + GPIO**

### 1.1 Introdução ao Zephyr
- O que é Zephyr e suas vantagens
  - Zephyr vs FreeRTOS (comparação direta)
  - Arquitetura modular e escalável
  - Suporte nativo a múltiplos microcontroladores
  - Ecosistema e comunidade

### 1.2 Ambiente de Desenvolvimento
- Configuração do ambiente
  - Instalação do `west` e do SDK
  - Configuração no VSCode

- Primeiro projeto
  - Hello World em Zephyr
  - Build system (CMake + West)
  - Logs com console serial
  - Flash e debug com VSCode
  - Execução do código em `native_sim`

**Projeto Prático:** Implementar blink LED com thread e k_msleep.

---

## **Aula 2: Gerenciamento de Threads + GPIO ISR**

### 2.1 Sistema de Threads
- Gerenciamento de memória (heap vs stack, dinâmico vs estático)
- Criação de threads
- Scheduling com prioridade preemptiva e cooperativa
- Primitivos de sincronização
  - Semáforo
  - Mutex
  - Fila

### 2.2 Leitura de GPIO por interrupção
- API de GPIO por interrupção
- Devicetree
- Comunicação ISR -> thread

**Projeto Prático:** Pressionar botão + blinky -> frequência variável

---

## **Aula 3: Devicetree + ADC + PWM**

### 3.1 Devicetree ###
- Estrutura de uma Devicetree
- Bindings, compatibles, drivers
- Sintaxe de uma devicetree

### 3.2 Leitura de tensão com ADC ###
- Configuração de Devicetree
- API do ADC -- o que é uma sequência?
- Leitura de potenciômetro

### 3.3 Geração e captura de PWM ###
- Geração de PWM
- Captura de PWM (exemplo de loopback)

**Projeto Prático:** Potenciômetro controla intensidade do blinky, botão controla frequência. 

---

## **Aula 4: Comunicação I2C + SPI**

### 4.1 Protocolo I2C ###

- Revisão do protocolo (master/slave, endereçamento, clock stretching)
- API I2C do Zephyr
- Configuração no devicetree
- Tratamento de erros de comunicação

### 4.2 Protocolo SPI ### 

- Revisão do protocolo (MOSI, MISO, SCK, CS, modos de operação)
- API SPI do Zephyr
- Configuração no devicetree

### 4.3 Integração com sensores reais ###

- Leitura de IMU via I2C/SPI (fundamental para todas as categorias)
- Chamadas síncronas e assíncronas
- Display I2C (útil para VSS e futuras implementações SSL)

Projeto Prático: Ler dados de uma IMU (giroscópio/acelerômetro) via I2C ou SPI e exibir valores filtrados no console. Bonus: exibir em display I2C.

--- 

## **Aula 5: Comunicação UART + Telemetria** ##

### 5.1 Protocolo UART ###

- Revisão do protocolo (baud rate, bits de dados, paridade, stop bits)
- API UART do Zephyr (polling, interrupt-driven, async)
- Configuração no devicetree

### 5.2 Implementação de protocolos customizados ###

- Design de protocolo de comunicação robusto
- Checksums e validação de dados
- Buffers circulares para recepção contínua
- State machines para parsing de mensagens

### 5.3 Debug e telemetria ###

- Logging estruturado com diferentes níveis
- Envio de telemetria via UART
- Debug remoto de sistemas embarcados

--- 

## **Aula 6: Máquinas de Estado + Design Patterns para Sistemas Embarcados** ##

### 6.1 Fundamentos de Máquinas de Estado Finitas (FSM)

- O que são FSMs e por que usar em sistemas embarcados
- Estados, transições, eventos e ações
- Representação gráfica (diagramas de estado)
- FSM vs programação linear/procedural

### 6.2 Implementação de FSMs em C

- Padrão Switch-Case básico
  - Estrutura simples com enum de estados
  - Vantagens e limitações
- Padrão Function Pointer (State Pattern)
  - Tabela de ponteiros para funções
  - Estados como funções independentes
  - Escalabilidade e manutenibilidade
- FSM Hierárquicas (HFSM)
  - Super-estados e sub-estados
  - Quando usar hierarquia


### 6.3 Design Patterns para Embarcados

- Event-Driven Programming
  - Filas de eventos
  - Publisher-Subscriber pattern (zbus)
  - Integração com Zephyr work queues
- Diagramas de sequência


### 6.4 Debugging e Visualização de Estados

- Logging de transições de estado
- Debug de FSMs em tempo real
- Ferramentas para visualização

Projeto Prático: Implementar um sistema de controle de robô com FSM robusta:
  - Estados: BOOT → IDLE → MOVING → FALLING → RECOVERY
  - Eventos: button_press, sensor_data, timeout, error_detected
  - Logging detalhado de transições