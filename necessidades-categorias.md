# Necessidades das categorias da ITAndroids

# SSL
A placa principal (Main) é a única que tem um microcontrolador (STM32H753), o qual recebe sinais dos módulos da placa, processa esses sinais por meio de um sistema operacional de tempo real (FreeRTOS) e envia sinais para outros módulos a fim do robô atender o que foi requisitado pelo computador central. O FreeRTOS, em geral, realiza as seguintes tarefas:

- Comunicação com a placa kicker (responsável pelo chute do robô) para controlar descarregamento dos capacitores (hora de carregar, hora de descarregar, força de chute), via protocolo SPI e GPIO.
  - Velocidade de comunicação do SPI de 1,17 MBits/s.
  - Também está implementado na kicker um ADC para ler temperatura da placa e tensão dos capacitores, mas ainda não foi devidamente testado. Os valores do ADC da kicker passam por um conversor digital que envia via SPI para o STM32H753.

- Comunicação GPIO com os sensores infravermelhos (IR) na frente do robô.
  - Par emissor + receptor que detecta se a bola está próxima à dribbler ou não. O circuito é bem simples (sinal alto ou baixo de acordo com a interrupção da bola na linha dos sensores).
  - O STM32H753 recebe o sinal GPIO do receptor IR e usa no código na task de chute do FreeRTOS.

- Comunicação SPI com o rádio.
  - Velocidade de comunicação de 6,25 MBits/s.
  - Atualmente, usa-se apenas 1 rádio (recebe informação do computador da COMP). Futuramente, pensa-se em colocar mais um rádio na placa ou então programar o já existente no robô para fazer telemetria.
  - O rádio é um nRF24L01+ e envia (não sabemos a latência exatamente) duas mensagens de 32 bytes. Cada pacote de 32 bytes contém comandos para 3 robôs. Até não muito tempo atrás, a COMP enviava 34 bytes (2 bytes 0xFF de "confirmação" + 32 bytes de mensagem) para a estação de rádio via UART (a estação é uma placa contendo uma NUCLEO). Como temos 4 robôs, agora a COMP envia 66 bytes (2 bytes 0xFF + 2 pacotes de 32 bytes): a estação verifica os bytes 0xFF, quebra o "bloco" de 64 bytes em 2 pacotes de 32 e envia cada um separadamente. Sobre a latência, o que se sabe é que a COMP envia comandos a 60 Hz. A latência seria a soma USB entre a COMP e a estação + estação + rádio.

- Controle dos motores das rodas.
  - Roda em 200 Hz.

- Comunicação com a IMU por SPI.
  - Acionamento de leitura baseado em interrupção.
  - A ideia é utilizar uma IMU para estimar melhor o ângulo de rotação do robô e não depender apenas da previsão da COMP com base na câmera em cima do campo.
  - Está sendo implementada ainda. Não foi definida a frequência de amostragem. O mínimo necessário é em torno de 60 Hz (taxa de envio de comandos da COMP) e o máximo seria os 200 Hz do controle. A ideia é usar a leitura da IMU como correção da medida da COMP em um Filtro de Kalman 1D.

- Circuito com USB para debug via protocolo UART.
  - Velocidade de comunicação de 115200 Bits/s.

- Filtro de Kalman para os motores e para a IMU.
  - Ainda não foi implementado, mas a ideia é usar a leitura da IMU na etapa de correção de medida no filtro para melhorar a estimativa da velocidade angular vinda da COMP (que é atualizada numa taxa próxima de 60 Hz).
  
- Leitura de encoders dos motores das rodas
  - Pino DIR (GPIO_Output): comanda o sentido de rotação da roda.
  - Pino DIRO (GPIO_Input): mesma ideia do DIR, mas como feedback.
  - Pino TACHO (GPIO_EXT): a cada rotação parcial da roda, é contado um pulso. A contagem (frequência) desses pulsos informa a velocidade das rodas. Em resumo, leitura da velocidade instantânea.
  - Pino PWM (Timer): para ler a velocidade média.
  - Atualmente usamos um sensor HALL, mas estamos migrando para encoders.

- Comunicação GPIO com 3 botões presentes na Main.
  - Devido a erros de pinout, atualmente temos 3 botões de teste na Main (testar motores, chute e dribbler), mas apenas 1 funciona (testar motores). Para testar chute, mudamos o ID do robô (alterando manualmene o valor no componente SW2) e acionamos "testar motores".

- Na próxima versão da Main, a equipe está pensando em adicionar algumas funcionalidades:
  - Display I2C de 7 segmentos para visualizar o ID do robô (para evitar ter que olhar o valor em binário no componente SW2).
  - Usar um buzzer. Tentou-se usar um com comunicação PWM, mas não funcionou. Ainda não se sabe exatamente por que.
  - Adicionar IC externo para leitura da tensão na bateria. Por algum motivo, não se conseguiu até hoje fazer funcionar o ADC do STM32H753.
  - Comunicação com memória FLASH via protocolo Quad SPI. A princípio, a memória está implementada na placa, mas os pinos não foram configurados ainda no microcontrolador e aparentemente o pinout está errado.
  - Adicionar sensores de temperatura para cada um dos 4 módulos de motor. Possivelmente, sensores de corrente também.

Resumo dos periféricos do STM32H753 utilizados:
- UART
- SPI
- I2C
- GPIO
- ADC
- PWM
- Flash QSPI

# VSS

# Humanoid

O único dispositivo que tem firmware embarcado escrito pela ITAndroids é a CMB. Ela precisa fazer as seguintes tarefas, de modo geral:
- Comunicar com a NUC com um protocolo em USB 1.0 ou UART
  - Manter frequência de comunicação fixa em 1kHz.
  - Mantém um compilado de informações dos outros subcircuitos.
- Comunicar com a IMU por SPI ou I2C.
  - Acionamento de leitura baseado em interrupção.
  - Frequência de comunicação por volta de 500Hz.
- Comunicar com a RIB por I2C, lidar com eventos de pressionamento de botão e acionamento de LED.
  - Latência de pressionamento máxima de 100ms.
- Leitura de corrente e tensão por ADC e por I2C.
  - Frequência de leitura a 1kHz.
- Comunicar com o subcircuito dos servos por UART half-duplex, chaveamento do circuito de power com GPIO.
  - Frequência de leitura a 1kHz.
 
Resumo dos periféricos utilizados:
- USB 1.0 Device
- UART
- I2C
- SPI
- ADC
- GPIO
