# ProjetoEmbarcado
Monitoramento Solução Nutritiva
#include <PubSubClient.h>
#include <WiFi.h>
#include <freertos/FreeRTOS.h>
#include <freertos/task.h>


#define pot1 36 //simulação de um sensor
#define pot2 39
#define pot3 34
const char* ssid = "TP-LINK_BF1DCE";
const char* password = "zelia@252";
const char* mqttServer = "projetoembarcado.duckdns.org";
const int mqttPort = 1883;
const char* mqttUser = "admin";
const char* mqttPassword = "raquel";

WiFiClient espClient;
PubSubClient client(espClient);
PubSubClient client2(espClient);
PubSubClient client3(espClient);
int analog_value = 0;
int analog_value2 = 0;
int analog_value3 = 0;
int Sensor=0;
int Sensor2=0;
int Sensor3=0;
int contador = 1;
char mensagem[30];
char mensagem2[30];
char mensagem3[30];
void Temperatura_task(void *p);
void reconectabroker2();
void reconectabroker();
void reconectabroker3();
void ph_task(void *p);
void condutividade_task(void *p);
void setup()
{
 pinMode( pot1,INPUT);
 pinMode( pot2,INPUT);
 pinMode( pot3,INPUT);
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED)
  {
     delay(500);
     Serial.println("Iniciando conexao com a rede WiFi..");
  }
  Serial.println("Conectado na rede WiFi!");
   xTaskCreate(Temperatura_task, "Temperatura", 5000, NULL, 3, NULL);
   xTaskCreate(condutividade_task, "condutividade", 5000, NULL, 3, NULL);
   xTaskCreate(ph_task, "ph", 5000, NULL, 3, NULL);
}
void loop()
{
  //analog_value = analogRead(pot1);
 // Serial.print(analogRead(pot1));
  //Faz a conexao com o broker MQTT
  reconectabroker();
  reconectabroker2();
  reconectabroker3();
  sprintf(mensagem, "%d", Sensor);
  sprintf(mensagem2, "%d", Sensor2);
  sprintf(mensagem3, "%d", Sensor3);
  Serial.print("Mensagem enviada: ");
  Serial.println(mensagem);
  Serial.print("Será q tá dando erro?: ");
  Serial.println(analog_value);
  //Envia a mensagem ao broker
  client.publish("teste1", mensagem);
  client2.publish("teste2",mensagem2);
  client3.publish("teste3",mensagem3);
  Serial.println("Mensagem enviada com sucesso...");
  
  //Incrementa o contador
  contador++;
  //Aguarda 1 segundo para enviar uma nova mensagem
  delay(1000);
}
void reconectabroker()
{
  //Conexao ao broker MQTT
  client.setServer(mqttServer, mqttPort);
  while (!client.connected())
  {
    Serial.println("Conectando ao broker MQTT...");
    if (client.connect("ESP32Client", mqttUser, mqttPassword ))
    {
      Serial.println("Conectado ao broker!");
    }
    else
    {
      Serial.print("Falha na conexao ao broker - Estado: ");
      Serial.print(client.state());
      delay(1000);
    }
  }
}
void reconectabroker2()
{
  //Conexao ao broker MQTT
  client2.setServer(mqttServer, mqttPort);
  while (!client2.connected())
  {
    Serial.println("Conectando ao broker MQTT...");
    if (client2.connect("ESP32Client", mqttUser, mqttPassword ))
    {
      Serial.println("Conectado ao broker!");
    }
    else
    {
      Serial.print("Falha na conexao ao broker - Estado: ");
      Serial.print(client2.state());
      delay(1000);
    }
  }
  Serial.println("Conectado ao broker!");
}
void reconectabroker3()
{
  //Conexao ao broker MQTT
  client3.setServer(mqttServer, mqttPort);
  while (!client3.connected())
  {
    Serial.println("Conectando ao broker MQTT...");
    if (client3.connect("ESP32Client", mqttUser, mqttPassword ))
    {
      Serial.println("Conectado ao broker!");
    }
    else
    {
      Serial.print("Falha na conexao ao broker - Estado: ");
      Serial.print(client3.state());
      delay(1000);
    }
  }
  Serial.println("Conectado ao broker!");
}
void Temperatura_task(void *p)
{
    while (1)    {
       delay(1);
       analog_value = analogRead(pot1);
       Sensor= analog_value;
       Sensor= map(Sensor,0,4095,0,40); 
    }
}
void condutividade_task(void *p)
{
    while (1)    {
       delay(1);
       analog_value3 = analogRead(pot2);
       Sensor3= analog_value3;
       Sensor3= map(Sensor3,0,4095,0,1500); 
    }
}
void ph_task(void *p)
{
    while (1)    {
       delay(1);
       analog_value2 = analogRead(pot3);
       Sensor2= analog_value2;
       Sensor2= map(Sensor2,0,4095,0,14); 
    }
}
