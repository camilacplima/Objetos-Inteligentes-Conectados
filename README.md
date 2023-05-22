# Objetos-Inteligentes-Conectados

Projeto de Controle de LED e Sensor de Umidade e Temperatura via ESP32

Funcionamento e Uso
O código começa importando as bibliotecas necessárias, como machine, umqtt.simple, ujson, network e dht. Essas bibliotecas fornecem
funcionalidades para trabalhar com o hardware, comunicação MQTT, manipulação de dados JSON, gerenciamento de rede e leitura do sensor DHT22. 
Em seguida, há uma seção de configuração, onde as variáveis são definidas para configurar o sistema. Isso inclui o pino do botão, o ID do 
dispositivo, as configurações de WiFi, as configurações MQTT, os pinos dos LEDs, o número máximo de iterações e a variável de controle do 
estado anterior do botão. Existem também alguns métodos definidos, como o did_receive_callback que é chamado quando uma mensagem MQTT é 
recebida. Nesse método, a mensagem recebida é processada e as ações correspondentes são tomadas, como ligar ou desligar os LEDs. O método 
mqtt_connect é responsável por conectar o dispositivo ao broker MQTT, configurando a conexão e subscrevendo o tópico de controle. O método 
create_json_data é usado para criar uma estrutura JSON com os dados de temperatura e umidade para serem enviados via MQTT. O método 
mqtt_client_publish é responsável por publicar uma mensagem no broker MQTT, enviando os dados para o tópico especificado. O método 
send_led_status é usado para enviar o estado atual dos LEDs para o broker MQTT, para que possam ser monitorados ou exibidos em algum lugar. 
A lógica da aplicação começa conectando-se à rede WiFi e, em seguida, ao broker MQTT. Em seguida, os LEDs são desligados e uma mensagem MQTT 
é publicada para garantir que o estado inicial seja refletido no broker. Um loop é iniciado com um número máximo de iterações definido. 
Dentro desse loop, o cliente MQTT verifica se há mensagens recebidas e acende um LED de sinalização. O sensor DHT22 é lido e os dados de 
temperatura e umidade são obtidos. Os dados são comparados com os dados anteriores e, se houver uma diferença, são publicados no tópico 
MQTT. Em seguida, o estado atual dos LEDs é enviado para o broker MQTT. O estado do botão é verificado e, se for detectada uma borda de 
descida (quando o botão é pressionado), os LEDs são alternados entre ligados e desligados, e a mensagem MQTT correspondente é publicada. O 
loop é pausado por um curto período de tempo e a iteração é incrementada. Essa é uma visão geral detalhada do funcionamento do sistema. Ele 
lê continuamente os valores do sensor DHT22, publica esses valores em um tópico MQTT e também recebe mensagens do broker MQTT para controlar 
os LEDs com base no estado do botão.

O código fonte se encontra na raiz deste repositório.

Simulação do Hardware Utilizado:
Os componentes de hardware utilizados na simulação deste projeto são os seguintes:
- Plataforma Simuladora Wokwi
- Microcontrolador ESP32
- Sensor DHT22
- Resistores
- Botão Interruptor Tátil
- LEDs

Interfaces, Protocolos de Comunicação:
O projeto utiliza os seguintes protocolos de comunicação:

WiFi: Utilizado para conectar o ESP32 a uma rede sem fio, possibilitando a comunicação com a internet e serviços remotos.

MQTT (Message Queuing Telemetry Transport): O protocolo MQTT está sendo usado neste projeto para estabelecer a comunicação entre o 
dispositivo IoT e um servidor/broker MQTT. O dispositivo está configurado para publicar dados (como informações de telemetria e status do 
LED) e também para assinar um tópico para receber comandos de controle.
