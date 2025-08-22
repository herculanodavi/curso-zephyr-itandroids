# Necessidades das categorias da ITAndroids

# SSL

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
