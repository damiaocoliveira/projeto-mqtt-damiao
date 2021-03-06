# Objetivo
O projeto tem por objetivo, utilizar um Arduino Uno e Sensor Magnético para monitorar a porta de um Rack de Rede, demonstrando se ela está ABERTO ou FECHADO; posteriormente, deve-se enviar essa informação via Internet utilizando o protocolo MQTT (Message Queuing Telemetry Transport) para um servidor MQTT hospedado na Amazon Web Service (AWS) e exibir a informação em um cliente MQTT (MQTT Dash) instalado em um Smartphone, conforme imagem abaixo.


![imagem2](https://camo.githubusercontent.com/7beef2d4780d87a603d7de49b2da0467c8537dff96575b628a04bd4010ebb1cc/68747470733a2f2f692e696d6775722e636f6d2f4d576870586b562e706e67)

Foram utilizadas as seguintes bibliotecas:
UIPEthernet (conexão do ENC28J60 com o Arduino)
PubSubClient (cliente MQTT para o Arduino)

* [UIPEthernet](https://www.arduino.cc/reference/en/libraries/uipethernet/)

* [PubsubClient](https://www.arduino.cc/reference/en/libraries/pubsubclient/) 

# Materiais

* [Arduino Uno](https://www.arduino.cc/)

* [Módulo Ethernet (ENC28J60)](https://www.arduino.cc/search?q=M%C3%B3dulo%20Ethernet%20%28ENC28J60%29)

* [Sensor Magnético (MC-38)](https://blogmasterwalkershop.com.br/arduino/como-usar-com-arduino-sensor-magnetico-com-fio-para-alarme-mc-38/)

* [Jumpers](https://www.arduino.cc/search?q=jumpers&tab=store)

# Circuitos
![imagem1](https://camo.githubusercontent.com/ad1da211b35b60b23fb095a64e76dc6504d0c3229e853bd82a69a4d5d27bbb88/68747470733a2f2f692e696d6775722e636f6d2f594947477453472e706e67) 

Autor: Damião Costa de Oliveira

[![Linkedin Badge](https://img.shields.io/badge/-LinkedIn-black?style=flat-square&logo=Linkedin&logoColor=white&link=https://www.linkedin.com/in/rafael-costa-c-honesto-3b46b2205/)](https://www.linkedin.com/feed/)

# Código

int pino2 = 2;      //Cria uma variável para o pino 2
bool estado_sensor; //Cria uma variável para armazenar o estado do sensor

#include <UIPEthernet.h>
#include <utility/logging.h>
#include <PubSubClient.h>

#include <SPI.h>
boolean mensagem;

//Define o endereço MAC que será utilizado
byte mac[] = {0XCA, 0X2E, 0XBF, 0XEC, 0XF0, 0X21};

//Inicia o cliente Ethernet
EthernetClient client;

PubSubClient mqqtClient(client); // Inicia o cliente PubSubClient

void setup()
{
    //Inicia o controlador Ethernet e solicita um IP para o servidor de DHCP
    Ethernet.begin(mac);

    //Inicia o monitor Serial
    Serial.begin(9600);

    pinMode(pino2, INPUT_PULLUP);

    mqqtClient.setServer("54.173.148.114", 1883);

    //Exibe no Monitor Serial as informações sobre o IP do Arduino
    Serial.print("O IP do Arduino e: ");
    Serial.println(Ethernet.localIP());

    //Exibe no Monitor Serial as informações sobre a Máscara de Rede do Arduino
    Serial.print("A Mascara de Rede do Arduino e: ");
    Serial.println(Ethernet.subnetMask());

    //Exibe no Monitor Serial as informações sobre o Gateway do Arduino
    Serial.print("O Gateway do Arduino e: ");
    Serial.println(Ethernet.gatewayIP());

    //Exibe uma linha em branco
    Serial.println("");
}

void loop()
{
    mqqtClient.connect("damiao"); //Define o nome do cliente MQTT

    estado_sensor = digitalRead(pino2);

    if (estado_sensor == 1)
    {
        mensagem = mqqtClient.publish("damiao-t", " RACK ABERTO ! ");
        Serial.println ("Aberto");
    }   
    else
    {
        mensagem = mqqtClient.publish("damiao-t", " RACK FECHADO ! ");
        Serial.println ("Fechado");
        }   
     
    mqqtClient.loop();

    Serial.println(mensagem);

  }
