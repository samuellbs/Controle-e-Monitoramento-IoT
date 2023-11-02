# Sistema de controle-e-monitoramento-IoT 

Esse trabalho aborda o desenvolvimento de um sistema de controle e monitoramento IoT com aplicação para acionamentos de baixa potência.
Autores: Samuel Barros Souza           (RA:20.01044-3);
         Lucas Granja Bernardo         (RA:19.00305-6);
         Lucas Bacich Martins          (RA:19.02421-5);
         Johannes Mattheus Krouwel     (RA:20.01248-9);

# 🚀 Introdução

A proposta desse projeto é o desenvolvimento de um sistema IoT utilizando MQTT para o acionamento e monitoramento remoto de variáveis.

# 💻 Componentes Eletrônicos e Esquema Elétrico 

Utilizou-se o Altium Designer para desenvolvimento do esquema elétrico e PCB. É possível visualizar os resultados nas imagens abaixo. 

[T3_SCH.pdf](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/files/13232578/T3_SCH.pdf)

![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/3e4179ea-695d-4172-8e80-6bad7860c981)

A tabela abaixo indica os componentes utilizados, bem como os preços deles (01/11/2023) como forma de realizar um levantamento de custo.

![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/14538ad9-6f4f-44d7-8b4b-d9bb8af0be96)



#  ⚙️ Funcionamento do sistema

O principal objetivo do projeto é o acionamento remoto via MQTT de relés de sinal fraco (biestáveis e latch). Dessa maneira, desenvolveu-se o hardware que atendesse as necessidades, isto é, o display OLED I2C é utilizado para visualização local dos status dos relés (on ou off) e os leds verde e vermelho, indicam conexão com o Wi-Fi e com o MQTT broker, respectivamente. O diagrama de blocos abaixo indica o funciomanento do sistema:

![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/7c5200fd-3a30-49be-9b93-34a8e63fd5f2)

A plataforma MQTT escolhida para desenvolvimento da infraestrutura necessária para acionamento e monitoramento online foi o Ubidots. Dessa maneira, o hardware está conectado com a internet, enviando dados (status dos relés) para a plataforma, bem como recebendo os dados da plataforma. Um ponto interessante a se destacar é o tempo de envio das variáveis para a plataforma. O autor determinou que a cada um minuto haja o envio dos status dos relés, todavia o recebimento de comando da plataforma, ou seja, acionar o relé, ocorre no mesmo tempo em que o botão foi acionado, porém há o intervalo de um minuto para atualização no dashboard online e no display físico da PCB. É possível visualizar o dashboard no link: https://stem.ubidots.com/app/dashboards/public/dashboard/w4wUidUygCJmY1CnEOmew5zm_JMcHxWhb3yMTTkWDmA

As imagens e o vídeo abaixo são dedicadas para o funcionamento real do projeto, incluindo a PCB final, dashboard online e exemplos do funcionamento descrito acima.

![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/49a1c66f-f324-40eb-9020-9797776d1169)
![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/479fa14b-0d01-4574-bb2f-cf38ccf0cfc5)
![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/6c0e07a6-2af1-4f0b-a92e-10b674309328)
Vídeo de funcionamento: https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/4b611a5f-120c-4a5c-bae1-b58c73cc2766

# ⌨️ Software Versão 0

```
/*    Programa: Acionamento e monitoramento remoto via MQTT
      Autor: Samuel Barros Souza   (RA: 20.01044-3)

      Versão: 0
      Concluído em: 21/10/2023
      Breve Descrição:
          Este programa permite o monitoramento e acionamento remoto de relés
          de sinal fraco (biestáveis e latch) via MQTT.

*/


// definição de bibliotecas
#include <Wire.h>
#include <WiFi.h>
#include <PubSubClient.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>


#define WIFISSID "NOME_WIFI"                                 //Coloque seu SSID de WiFi aqui
#define PASSWORD "SENHA_WIFI"                               //Coloque seu password de WiFi aqui
#define TOKEN "BBFF-xHc30n4EJalYtrj4L3lWvQN2VT9Dl9"        //Coloque seu TOKEN do Ubidots aqui
#define VARIABLE_LABEL_RELE3DW "rele3_teste"              //Label referente a variável criada no ubidots
#define VARIABLE_LABEL_RELE3UP "rele3_uplink"            //Label referente a variável criada no ubidots
#define DEVICE_ID "64ffb1cea863f81075ae6ef8"            //ID do dispositivo (Device id, também chamado de client name)
#define SERVER "industrial.api.ubidots.com"            //Servidor do Ubidots (broker)
#define DEVICE_LABEL "esp32-teste"

//Porta padrão
#define PORT 1883

//Tópico de publish,         DEVICE_LABEL
#define TOPIC "/v1.6/devices/esp32-teste"                    //Tópico de publish
#define TOPIC1 "/v1.6/devices/esp32-teste/rele3_teste/lv"   //Tópico de subscribe

#define SCREEN_WIDTH 128 // OLED display width, in pixels
#define SCREEN_HEIGHT 64 // OLED display height, in pixels
// Declaration for an SSD1306 display connected to I2C (SDA, SCL pins)
#define OLED_RESET     -1 // Reset pin # (or -1 if sharing Arduino resetpin)
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);


//Ubidots
WiFiClient ubidots;                //Objeto WiFiClient usado para a conexão wifi
PubSubClient client(ubidots);     //Objeto PubSubClient usado para publish–subscribe


//definição de pinos
#define PIN_GREEN         33
#define PIN_RED           32
#define PIN_FEED3         13
#define PIN_POS3          12
#define PIN_NEG3          14


String deviceName   = "T3_REV_00";    //nome do dispositivo

//criacao das flags
char fs_bt1   = 0;
char fs_100ms = 0;   //flags de estado para cada x segundos
char fs_60s  = 0;
char fs_1s    = 0;
char fs_30s = 0;



// criacao dos timers
int millis_atual_100ms;
int millis_atual_60s;
int millis_atual_30s;
int millis_atual_1s;




// definição dos argumentos para subscribe MQTT

char payload[100];
char topic[150];
char topicSubscribe[100];


int greenState = LOW;
int redState = LOW;
int countSEND = 0;
int countRECEIVED0 = 0;
int countRECEIVED1 = 0;
int recebido;

void setup() {

  Serial.begin(115200);

  if (inicializa_pinos() < 0)
  {
    Serial.println("erro em inicializa_pinos");
  }

  if (testa_display() < 0)

  {
    Serial.println("erro em testa_display");
  }


  Serial.println("Starting" +  deviceName );
  Serial.println("inicializa_pinos ok");
  Serial.println("inicializa_display ok");
  inicializa_display();
  mqtt_init();
  client.subscribe(TOPIC1);
  client.setCallback(callback);

}

void loop() {


  trata_timers();
  if ( fs_60s) // 1 minuto
  {
    fs_60s = 0;
    atualiza_display();
    sendValues();
    if (sendValues)
    {
      countSEND = 0;
      while (countSEND < 1000000)
      {
        digitalWrite(PIN_RED, LOW);
        countSEND ++;
      }
      digitalWrite(PIN_RED, HIGH);
    }
  }

  if (fs_100ms)
  {
    fs_100ms = 0;
    internet_off();
    mqtt_off();
    client.loop(); //permite o envio de mensagens publish (DEVE SER CHAMADA REGULARMENTE)
  }

  if (fs_1s)         // 1 segundo

  {
    fs_1s = 0;
  }
}


char inicializa_pinos(void)
{


  /*      Descrição de funcionamento:

           Inicialização de pinos e retorno 0 ou -1 que será verificado no setup
  */

  char ret;

  ret = -1;
  pinMode(PIN_GREEN, OUTPUT);
  pinMode(PIN_RED, OUTPUT);
  pinMode(PIN_FEED3, INPUT);
  pinMode(PIN_POS3, OUTPUT);
  pinMode(PIN_NEG3, OUTPUT);
  ret = 0;

  return ret; // inicializa_pinos = valor de ret
}

char testa_display(void)
{

  /*      Descrição de funcionamento:

          Inicialização de display e retorno 0 ou -1 que será verificado no setup
  */

  char ret;
  ret = -1;
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  ret = 0;

}

void inicializa_display(void)
{
  /*      Descrição de funcionamento:

          Definição de tela para o primeiro minuto enquanto há inicialização do sistema
  */

  display.clearDisplay();
  // Display Text
  display.setTextSize(2);             // definição tamanho de letra para display
  display.setTextColor(WHITE);        // definição de cor de letra para display
  display.setRotation(2);
  display.setCursor(6, 25);
  display.print("SB PROJECT");
  display.display();
}

void atualiza_display(void)

{
  /*      Descrição de funcionamento:

        Definição de tela de acordo com status dos relés
  */

  display.clearDisplay();
  // Display Text
  display.setTextSize(1.5);             // definição tamanho de letra para display
  display.setTextColor(WHITE);        // definição de cor de letra para display
  display.setRotation(2);
  display.setCursor(20, 0);
  display.print("STATUS DO RELE");
  display.setCursor(0, 26);
  display.print("RELE 3:");
  if (digitalRead(PIN_FEED3) == 0)
  {
    display.println("OFF");
  }
  else
  {
    display.println("ON");
  }
  display.display();

}

void trata_timers(void)
{
  /*      Descrição de funcionamento:

         Definição de tempo de timers
  */

  // função milis retorna o tempo que o microcontrolador está ligado

  if (millis() > 1000 + millis_atual_1s) //1s
  {
    millis_atual_1s = millis();
    fs_1s = 1;
  }

  if (millis() > 60000 + millis_atual_60s) //60s
  {
    millis_atual_60s = millis();
    fs_60s = 1;
  }

  if (millis() > 100 + millis_atual_100ms) //100ms
  {
    millis_atual_100ms = millis();
    fs_100ms = 1;
  }
  if (millis() > 30000 + millis_atual_30s) //30s
  {
    millis_atual_30s = millis();
    fs_30s = 1;
  }
}

bool mqtt_init(void)
{
  /*      Descrição de funcionamento:

          Conexão com WiFi e MQTT
  */

  WiFi.begin(WIFISSID, PASSWORD); //Inicia WiFi com o SSID e a senha

  while (WiFi.status() != WL_CONNECTED)   //Loop até que o WiFi esteja conectado
  {
    Serial.print(".");
  }

  //Exibe no monitor serial
  Serial.println("\nConnected to network");
  digitalWrite(PIN_RED, LOW);
  digitalWrite(PIN_GREEN, HIGH);

  //Seta servidor com o broker e a porta
  client.setServer(SERVER, PORT);

  //Conecta no ubidots com o Device id e o token, o password é informado como vazio
  while (!client.connect(DEVICE_ID, TOKEN, ""))
  {
    Serial.println("MQTT - Connect error");
    return false;
  }

  Serial.println("MQTT - Connect ok");
  digitalWrite(PIN_RED, HIGH);
  return true;
}

void internet_off (void)

/*      Descrição de funcionamento:

        Rotina de verificação de conexão com o Wifi, caso haja desconexão
        o led verde mudará de estado.
*/

{
  if (WiFi.status() != WL_CONNECTED)
  {

    if (greenState == LOW)
    {
      greenState = HIGH;
    }

    else
    {
      greenState = LOW;
    }

    digitalWrite(PIN_GREEN, greenState);

  }
}

void mqtt_off (void)
{
  /*      Descrição de funcionamento:

        Rotina de verificação de conexão com o MQTT, caso haja desconexão
        o led vermelho irá mudar de estado.
  */

  if (!client.connect(DEVICE_ID, TOKEN, ""))
  {
    if (redState == LOW)
    {
      redState = HIGH;
    }

    else
    {
      redState = LOW;
    }

    digitalWrite(PIN_RED, redState);

  }
}

bool sendValues(void)
{
  /*      Descrição de funcionamento:

      Função para envio do estado da variável para o MQTT broker (dashboard online)
  */

  char json[250];
  float feedback3 = digitalRead(PIN_FEED3);
  //Serial.println(feedback3);

  //Atribui para a cadeia de caracteres "json" os valores e os envia para a variável do ubidots correspondente
  sprintf(json,  "{\"%s\":{\"value\":%02.02f}}", VARIABLE_LABEL_RELE3UP, feedback3);

  if (!client.publish(TOPIC, json))
    return false;

  //Se tudo der certo retorna true
  return true;
}

void callback(char* topic, byte* message, unsigned int length)
{
  /*      Descrição de funcionamento:

      Função para recepção do comando do MQTT broker (dashboard online)
  */

  Serial.println("chegou");
  Serial.println(topic);
  Serial.println(length);
  for (int i = 0; i < length; i++)
  {
    Serial.print((char)message[i]);
    //recebido += (char)message[i];
    //recebido = (char)message[i];
  }
  recebido = btof(message, length); //variável inteira
  trata_rele3();

}

int btof(byte * payload, unsigned int length)
{
  /*      Descrição de funcionamento:

      Função para transformar mensagem reebida do MQTT broker em variável do tipo inteira
  */
  char * demo_ = (char *) malloc(sizeof(char) * 10);
  for (int i = 0; i < length; i++) {
    demo_[i] = payload[i];
  }
  return atof(demo_);
}


void trata_rele3 (void)
{
  /*      Descrição de funcionamento:

    Rotina para acionar os relés, de acordo com o comando remoto recebido
  */

  if (recebido == 1)
  {
    countRECEIVED1 = 0;
    while (countRECEIVED1 < 1000000)
    {
      digitalWrite(PIN_POS3, HIGH);
      digitalWrite(PIN_NEG3, LOW);
      countRECEIVED1 ++;
    }
    digitalWrite(PIN_POS3, LOW);
    digitalWrite(PIN_NEG3, LOW);
  }

  if (recebido == 0)
  {
    countRECEIVED0 = 0;
    while (countRECEIVED0 < 1000000)
    {
      digitalWrite(PIN_POS3, LOW);
      digitalWrite(PIN_NEG3, HIGH);
      countRECEIVED0 ++;
    }
    digitalWrite(PIN_POS3, LOW);
    digitalWrite(PIN_NEG3, LOW);
  }
}
```


