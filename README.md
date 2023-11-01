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

![image](https://github.com/samuellbs/Alimentador_PET/assets/103770785/cd5911a6-a360-4e6c-b6f2-1e72f2a021fb)



#  ⚙️ Funcionamento do sistema

O projeto apresenta um display OLED I2C que indica o horário atual. Dessa forma, quando o botão é apertado, ocorrem duas situações simultâneas. A primeira é a saturação do transistor BC548 permitindo que o LED acenda. Como também, o pino D18 do ESP32 está "monitorando" o acionamento do botão, para que exiba no display "Hora da comida" e acione o motor para liberação da comida. É importante ressaltar que a proposta do botão é permitir que o usuário despeje a comida quando quiser.

Por outro lado, como a proposta do sistema é ser um alimentador pet automático, através do código é possível definir horários para que a comida seja despejada no pote. No caso do software original, estão definidos os horários 07:30:00 e 19:30:00. As imagens abaixo são dedicadas para o funcionamento real do projeto, incluindo a placa final soldada, estrutura física e exemplos do funcionamento descrito acima.

![image](https://github.com/samuellbs/Alimentador_PET/assets/103770785/db1bf6cf-6dea-44c6-ac03-a2b49627749c)

https://github.com/samuellbs/Alimentador_PET/assets/103770785/105a38ab-1e81-4e2a-8d41-1488f6c5eada

https://github.com/samuellbs/Alimentador_PET/assets/103770785/1b54e7d9-1f1e-4d01-a8e0-5c319d7db13d

É importante ressaltar que a estrutura física escolhida, não foi desenvolvida pelos autores desse projeto. A escolha foi por uma estrutura da UsinaINFO, que atendia as necessidades.
