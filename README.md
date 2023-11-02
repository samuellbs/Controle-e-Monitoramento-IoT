# Sistema de controle-e-monitoramento-IoT 

Esse trabalho aborda o desenvolvimento de um sistema de controle e monitoramento IoT com aplicação para acionamentos de baixa potência.
Autores: Samuel Barros Souza           (RA:20.01044-3);
         Lucas Granja Bernardo         (RA:19.00305-6);
         Lucas Bacich Martins          (RA:19.02421-5);
         Johannes Mattheus Krouwel     (RA:20.01248-9);

# 🚀 Introdução

A proposta desse projeto é o desenvolvimento de um sistema IoT utilizando MQTT para o acionamento e monitoramento remoto de variáveis.

# 💻 Componentes Eletrônicos e Esquema Elétrico 

Utilizou-se o Altium Designer para desenvolvimento do esquema elétrico e pcb. É possível visualizar os resultados nas imagens abaixo. 

[T3_SCH.pdf](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/files/13232578/T3_SCH.pdf)

![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/3e4179ea-695d-4172-8e80-6bad7860c981)

A tabela abaixo indica os componentes utilizados, bem como os preços deles (01/11/2023) como forma de realizar um levantamento de custo.

![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/14538ad9-6f4f-44d7-8b4b-d9bb8af0be96)



#  ⚙️ Funcionamento do sistema

O principal objetivo do projeto é o acionamento remoto via MQTT de relés de sinal fraco (biestáveis e latch). Dessa maneira, desenvolveu-se o hardware que atendesse as necessidades, isto é, o display OLED I2C é utilizado para visualização local dos status dos relés (on ou off) e os leds verde e vermelho, indicam conexão com o Wi-Fi e com o MQTT broker, respectivamente. 

A plataforma MQTT escolhida para desenvolvimento da infraestrutura necessária para acionamento e monitoramento online foi o Ubidots. Dessa maneira, o hardware está conectado com a internet, enviando dados (status dos relés) para a plataforma, bem como recebendo os dados da plataforma. Um ponto interessante a se destacar é o tempo de envio das variáveis para a plataforma. O autor determinou que a cada um minuto haja o envio dos status dos relés, todavia o recebimento de comando da plataforma, ou seja, acionar o relé, ocorre no mesmo tempo em que o botão foi acionado, porém há o intervalo de um minuto para atualização no dashboard online e no display físico da PCB. É possível visualizar o dashboard no link: https://stem.ubidots.com/app/dashboards/public/dashboard/w4wUidUygCJmY1CnEOmew5zm_JMcHxWhb3yMTTkWDmA

As imagens e o vídeo abaixo são dedicadas para o funcionamento real do projeto, incluindo a PCB final, dashboard online e exemplos do funcionamento descrito acima.

![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/49a1c66f-f324-40eb-9020-9797776d1169)
![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/479fa14b-0d01-4574-bb2f-cf38ccf0cfc5)
![image](https://github.com/samuellbs/Controle-e-Monitoramento-IoT/assets/103770785/6c0e07a6-2af1-4f0b-a92e-10b674309328)





https://github.com/samuellbs/Alimentador_PET/assets/103770785/105a38ab-1e81-4e2a-8d41-1488f6c5eada

https://github.com/samuellbs/Alimentador_PET/assets/103770785/1b54e7d9-1f1e-4d01-a8e0-5c319d7db13d

É importante ressaltar que a estrutura física escolhida, não foi desenvolvida pelos autores desse projeto. A escolha foi por uma estrutura da UsinaINFO, que atendia as necessidades.
