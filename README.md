# Trabalho SE 16

## 🚶 Integrantes do Projeto

- Jadson de Jesus Santos
- João Pedro Soares Raccolto
- Mariana Farias da Silva
- Matheus Pereira Alves
- Rian de Sena Mendes
- Samuel Guedes Canário

# 📡 Receptor LoRa com Display OLED na RP2040

<p align="center">Este projeto implementa um sistema de recepção de dados via rádio LoRa, utilizando a placa RP2040. O sistema é projetado para capturar pacotes de uma estação de sensores remota, decodificar os dados de temperatura, umidade e pressão, e exibi-los em tempo real em um display OLED e no console serial para monitoramento.</p>

## 📋 Apresentação da tarefa

A tarefa consiste em desenvolver um firmware robusto para um nó receptor LoRa. O sistema deve ser capaz de operar continuamente, aguardando pacotes de dados de um transmissor configurado com os mesmos parâmetros de rádio. Ao receber um pacote, ele deve validar sua integridade, extrair as informações úteis e apresentá-las ao usuário de forma clara através de múltiplas interfaces (visual e serial), além de fornecer feedback sobre a qualidade do sinal e o status da operação.

## 🎯 Objetivos

- Implementar um receptor LoRa funcional na frequência de 915 MHz.

- Decodificar um payload de dados customizado com informações de múltiplos sensores.

- Validar a integridade dos pacotes recebidos através de um checksum.

- Exibir os dados dos sensores (temperatura, umidade, pressão) em um display OLED.

- Apresentar métricas de qualidade do sinal, como RSSI e SNR, para diagnóstico da comunicação.

- Fornecer feedback de status instantâneo utilizando um LED RGB (pronto, recebendo, erro, timeout).

- Monitorar a recepção de pacotes e alertar visualmente caso ocorra perda de sinal por um período prolongado.

- Imprimir um relatório detalhado de cada pacote recebido no console serial para depuração e registro.

## 📚 Descrição do Projeto

Utilizando uma placa com microcontrolador RP2040, este projeto estabelece um link de comunicação sem fio via LoRa. O sistema utiliza um módulo de rádio conectado via SPI para escutar em modo de recepção contínua. Um display OLED SSD1306, conectado via I2C, serve como interface gráfica principal, mostrando os dados mais recentes de forma organizada.

O firmware implementa um protocolo de comunicação customizado, onde cada pacote de 10 bytes contém um cabeçalho de tipo, dados de sensores codificados, um contador de mensagens e um checksum para verificação de erros. O sistema não apenas decodifica esses valores, mas também calcula a intensidade do sinal (RSSI) e a relação sinal-ruído (SNR) de cada pacote, oferecendo um diagnóstico completo da saúde do link de rádio. O gerenciamento de status é reforçado por um LED RGB, que muda de cor para indicar se o sistema está aguardando, se recebeu um pacote com sucesso ou se perdeu a comunicação com o transmissor.

## 📑 Funcionamento do Projeto

O sistema opera com base em um loop principal eficiente que gerencia a recepção e o processamento de dados:

Inicialização: Configura as interfaces de hardware: SPI para o módulo LoRa e I2C para o display OLED. Os pinos GPIO para controle do rádio (CS, RESET) e para o LED RGB são inicializados.

Configuração LoRa: O rádio é configurado com parâmetros específicos (frequência de 915 MHz, Spreading Factor, Bandwidth) e colocado em modo de recepção contínua (MODE_RX_CONTINUOUS).

Loop Principal: O sistema verifica continuamente as flags de interrupção do módulo LoRa.

Processamento de Pacote: Ao detectar a flag de pacote recebido (IRQ_RX_DONE), o firmware lê os dados do buffer FIFO do rádio.

Validação e Decodificação: O sistema primeiro verifica o CRC do hardware LoRa. Em seguida, calcula o checksum do payload e o compara com o valor recebido. Se tudo estiver correto, os bytes são convertidos para seus respectivos valores (temperatura, umidade, etc.).

Interface e Feedback: Os dados decodificados são formatados e enviados para o display OLED. Simultaneamente, um relatório detalhado é impresso no console serial. O LED verde pisca para sinalizar o sucesso da recepção.

Monitoramento de Timeout: Um temporizador interno verifica se um pacote foi recebido nos últimos 10 segundos. Se não, o LED vermelho começa a piscar, e uma mensagem de "TIMEOUT" é exibida no display, alertando para a perda de sinal.

<br>

# 🔵 Comunicação BLE com Sensor de Temperatura na Pico W

<p align="center">Este projeto demonstra uma comunicação completa via Bluetooth Low Energy (BLE) entre duas placas Raspberry Pi Pico W. Uma placa atua como "Servidor", lendo a temperatura de seu sensor interno e a transmitindo sem fio. A segunda placa atua como "Cliente", escaneando, conectando-se ao servidor e recebendo os dados de temperatura em tempo real.</p>

## 📋 Apresentação da tarefa

O objetivo é construir um sistema funcional de sensor sem fio usando a tecnologia BLE. Para isso, foi necessário desenvolver dois firmwares distintos: um para o dispositivo periférico (Servidor), que coleta e anuncia os dados, e outro para o dispositivo central (Cliente), que descobre e consome esses dados. O projeto serve como um exemplo prático de implementação do perfil GATT (Generic Attribute Profile) e do ciclo de vida de uma conexão BLE, desde a publicidade até a notificação de dados.

## 🎯 Objetivos

- Servidor BLE (Periférico)
  - Acessar e ler o sensor de temperatura interno do microcontrolador RP2040.

  - Configurar um servidor GATT com o serviço padrão de "Environmental Sensing" (Sensoriamento Ambiental).

  - Anunciar (advertise) sua presença e serviços para que clientes possam descobri-lo.

  - Gerenciar conexões e desconexões de clientes de forma estável.

  - Transmitir os valores de temperatura para clientes inscritos usando o mecanismo de notificações BLE.

- Cliente BLE (Central)
  - Escanear ativamente por dispositivos BLE que estejam anunciando o serviço de "Environmental Sensing".

  - Conectar-se automaticamente ao servidor de temperatura assim que ele for identificado.

  - Realizar o processo de descoberta de serviços e características para encontrar o atributo de temperatura.

  - Inscrever-se (subscribe) para receber as notificações de temperatura enviadas pelo servidor.

  - Exibir os dados de temperatura recebidos no console serial para verificação.

  - Gerenciar o estado da conexão e tentar se reconectar caso a conexão seja perdida.

## 📚 Descrição do Projeto

Este projeto é dividido em duas partes que trabalham em conjunto, ambas utilizando o chip CYW43 da Raspberry Pi Pico W e a biblioteca BTstack para a gestão da pilha Bluetooth.

O Servidor BLE é o dispositivo sensor. Seu firmware inicializa o conversor analógico-digital (ADC) para ler a tensão do sensor de temperatura interno e a converte para graus Celsius. Em seguida, ele estabelece um perfil GATT, que define como seus dados são estruturados e acessados. Ele começa a anunciar sua presença, tornando-se visível para dispositivos próximos. Quando um cliente se conecta e se inscreve em sua característica de temperatura, o servidor começa a enviar os dados periodicamente através de notificações BLE.

O Cliente BLE é o dispositivo monitor. Seu firmware opera como uma máquina de estados que gerencia todo o processo de conexão. Ele primeiro escaneia o ambiente, filtrando os pacotes de anúncio para encontrar o servidor. Ao localizá-lo, ele estabelece uma conexão e, em seguida, interroga o servidor para descobrir os "endereços" (handles) de seus serviços e características. Por fim, ele habilita as notificações e entra em um modo de escuta, imprimindo cada novo valor de temperatura que recebe no console. O LED de cada placa fornece feedback visual sobre seu estado atual (procurando, conectado, enviando dados).

## 📑 Funcionamento do Projeto

Servidor BLE
Inicialização: Configura o hardware (CYW43, ADC) e a pilha Bluetooth (BTstack). O perfil GATT com o serviço de temperatura é carregado.

Publicidade (Advertising): O dispositivo começa a transmitir pacotes de anúncio, informando seu nome local e o UUID do serviço de sensoriamento ambiental.

Gerenciamento de Eventos: O sistema opera em um loop de eventos, aguardando ações como uma solicitação de conexão de um cliente.

Envio de Dados: Um timer periódico (heartbeat_handler) é acionado a cada segundo. Ele lê a temperatura e, se houver um cliente conectado com notificações habilitadas, envia o novo valor. O LED on-board pisca para indicar atividade.

Cliente BLE
Inicialização: Configura o hardware (CYW43) e a pilha BTstack.

Máquina de Estados de Conexão: O cliente transita por vários estados:

TC_W4_SCAN_RESULT: Escaneia ativamente por dispositivos.

TC_W4_CONNECT: Ao encontrar o servidor, para de escanear e inicia a conexão.

TC_W4_SERVICE_RESULT: Após conectar, busca pelo serviço de sensoriamento.

TC_W4_CHARACTERISTIC_RESULT: Busca pela característica de temperatura dentro do serviço.

TC_W4_ENABLE_NOTIFICATIONS_COMPLETE: Envia um comando para o servidor para começar a receber as notificações.

TC_W4_READY: Estado final, onde o cliente está pronto e apenas aguarda para processar os dados recebidos.

Recepção e Exibição: No estado READY, qualquer notificação recebida aciona o packet_handler, que extrai o valor da temperatura do pacote e o imprime no console serial. O LED on-board pisca rapidamente para indicar que está conectado.

## ▶️ Vídeo no youtube mostrando o funcionamento do programa na placa Raspberry Pi Pico
<p align="center">
    <a href="https://youtu.be/iMRuBZcHfCY">Clique aqui para acessar o vídeo</a>
</p>
