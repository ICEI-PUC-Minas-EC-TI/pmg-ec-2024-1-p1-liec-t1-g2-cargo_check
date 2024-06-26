
# Materiais

No projeto foram utilizados:
•	1 esp32
•	1 ponte H L298n
•	2 pilhas aa
•	1 protoboard
•	1 célula de carga 
•	1 Módulo Conversor Amplificador Hx711
•	1 motor DC 

# Desenvolvimento

Para chegar no resultado do projeto é necessário passar por algumas etapas:
•	Criar um rascunho do veículo e aplicativo 
•	Montagem de ambos os sistemas de motor e balança 
•	Programação do esp32 e do aplicativo 
•	Testes finais 

## Desenvolvimento do Aplicativo

### Interface

Conexão Bluetooth:
Na tela inicial, os usuários encontrarão uma opção para conectar o Bluetooth.
Essa funcionalidade permite que os usuários ativem ou desativem a conexão com dispositivos externos, como sensores ou outros dispositivos compatíveis.

Seleção de Caminhões:
Abaixo da opção Bluetooth, há uma lista de seleção de caminhões.
Os usuários podem escolher entre três opções diferentes de caminhões.
Cada caminhão possui uma carga máxima específica associada a ele.

Exibição do Resultado:
Logo abaixo da lista de caminhões, está o local onde o resultado será mostrado.
Dependendo das entradas dos usuários (como peso da carga), o aplicativo calculará e exibirá informações relevantes relacionadas à capacidade de carga do caminhão selecionado.

### Código

Inicializações Globais:
Variáveis globais:
caminhões: É um dicionário com três chaves (caminhão1, caminhão2, caminhão3) e valores correspondentes (30, 20, 10).
caminhão: Inicializado com 0.
diferenca: Inicializado com 0.
received_data: Inicializado com 0.

Eventos e Funções:
Marco_do_seu_caminhão.BeforePicking:

Preenche os elementos de Marco_do_seu_caminhão com as chaves do dicionário caminhões.
Bluetooth1.BeforePicking:

Preenche os elementos de Bluetooth1 com os endereços e nomes disponíveis de ClienteBluetooth1.
Bluetooth1.AfterPicking:

Define a seleção de Bluetooth1 como o endereço conectado de ClienteBluetooth1.
Temporizador1.Timer:

Verifica se ClienteBluetooth1 está conectado.
Se sim, obtém o número de bytes disponíveis para receber.
Se houver bytes, define global received_data com os bytes recebidos via ReceiveSignedBytes.
Atualiza o texto de Bluetooth1 com os dados recebidos e chama a função atualizar.
atualizar:

Converte o texto de Marco_do_seu_caminhão para número e define global caminhão no com este valor.
Verifica se global caminhão no está no dicionário caminhões.
Calcula a diferença entre global received_data e global caminhão.

Dependo do global received_data e global caminhão o codigo pode tomar diferentes ações, se global received_data for menor que global caminhão ele faz a diferença de carga atualiza o texto com a mensagem apropriada e envia o numero 7.

No caso de global received_data e global caminhão terem o mesmo valor ele atualiza o texto com a mensagem apropriada e envia o numero 5. 

No caso de global received_data ser igual a 0 ele atualiza o texto com a mensagem apropriada e envia o numero 6. 

Dependendo do valor de global diferenca, realiza diferentes ações:
Se diferenca < 30:
Envia o valor de decimal (1, 2, 3 ou 4) para ClienteBluetooth1 dependendo do valor de diferenca.
Atualiza o texto de Resultado1 com a mensagem adequada.
Chama a função atualizar.

Resumo do Fluxo:
O usuário escolhe um caminhão a partir de Marco_do_seu_caminhão.
O aplicativo tenta se conectar a um dispositivo Bluetooth a partir de Bluetooth1.
Quando os dados são recebidos via Bluetooth, o aplicativo calcula a diferença entre a capacidade do caminhão selecionado e os dados recebidos.
Dependendo da diferença, o aplicativo envia um comando de volta via Bluetooth ou atualiza o texto com a mensagem apropriada.

## Desenvolvimento do Hardware

### Montagem

Durante a montagem do hardware do projeto, foi feita uma pesquisa sobre os diagramas tanto do sensor de peso quanto da ativação do motor, assim que consideramos com informações os suficientes começamos a montagem de ambos os sistemas que seriam integrados através do esp32 e do app 

### Desenvolvimento do Código

O desenvolvimento do código para o Arduino/ESP32 foi um processo cuidadoso, dividido em várias etapas fundamentais:

1. Configuração Inicial
Objetivo: Estabelecer os componentes e pinos essenciais, além de incluir as bibliotecas necessárias.

A primeira tarefa foi incluir as bibliotecas apropriadas para o sensor de carga (HX711) e a comunicação Bluetooth. Em seguida, definimos quais pinos do ESP32 seriam utilizados para conectar o sensor de carga e o driver do motor.

2. Inicialização no Setup
Objetivo: Inicializar a comunicação serial e Bluetooth, configurar o sensor de carga, e definir os pinos para o controle do motor.

No método setup(), começamos inicializando a comunicação serial para monitoramento e a comunicação Bluetooth para permitir a conexão com um dispositivo externo. Em seguida, configuramos o sensor de carga, calibrando-o e ajustando a escala. Também configuramos os pinos de controle do motor como saídas.

3. Leitura e Envio de Dados no Loop
Objetivo: Ler dados do sensor de carga e enviá-los via Bluetooth em intervalos regulares, além de responder a comandos para controlar o motor.

No método loop(), implementamos a lógica principal do programa. Primeiramente, verificamos se o sensor de carga está pronto para fornecer uma leitura. Se estiver, lemos os dados, calculamos o peso e os enviamos via Bluetooth se houver um cliente conectado.

Adicionalmente, configuramos um temporizador para garantir que os dados fossem enviados em intervalos regulares, evitando sobrecarga de comunicação. Também monitoramos a porta serial para verificar se havia comandos recebidos para ajustar a calibração do sensor de carga.

4. Controle do Motor
Objetivo: Implementar a lógica de controle do motor com base nos dados recebidos e comandos do usuário.

Finalmente, configuramos a lógica para controlar um motor usando o driver L298N. Dependendo dos dados recebidos via Bluetooth, ajustamos a velocidade do motor. A lógica foi projetada para ajustar a velocidade do motor proporcionalmente ao peso detectado pelo sensor de carga, garantindo que o motor respondesse de maneira adequada às mudanças na carga.

## Comunicação entre App e Hardware

Parte do App (App Inventor)
Configuração Inicial:

O app cria um dicionário (caminhões) com diferentes caminhões e suas capacidades (por exemplo, caminhão1 = 10, caminhão2 = 20).
O app define variáveis globais (global_caminhão, global_receved_data, etc.) para armazenar dados temporários.
Escolha do Caminhão:

Quando você seleciona um caminhão no app, ele salva a capacidade desse caminhão na variável global_caminhão.
Conexão Bluetooth:

O app escaneia os dispositivos Bluetooth disponíveis.
Quando você seleciona um dispositivo da lista, o app se conecta a ele.
Recepção de Dados via Bluetooth:

O app usa um temporizador para verificar se há dados recebidos do ESP32 via Bluetooth.
Quando recebe dados, ele armazena esses dados na variável global_receved_data e chama uma função para atualizar a interface do usuário.
Atualização e Envio de Comandos:

A função Atualizar compara os dados recebidos (peso) com a capacidade do caminhão escolhido.
Dependendo da diferença, o app mostra diferentes mensagens e envia comandos ao ESP32 para controlar o motor.
Parte do ESP32 (Código Arduino)
Configuração Inicial:

O código configura os pinos para o sensor de carga (HX711) e para o motor.
Inicializa a comunicação serial e a comunicação Bluetooth.
Configura a célula de carga e calibra ela.
Leitura do Peso:

No loop principal, o ESP32 lê o peso usando a célula de carga.
Se o peso for válido, ele envia os dados via Bluetooth para o app.
Recepção de Comandos:

O ESP32 espera por comandos do app via Bluetooth.
Dependendo do comando recebido, ele controla o motor (por exemplo, parar, mover para frente com diferentes velocidades).
Resumo
App: O app escolhe um caminhão, se conecta ao ESP32 via Bluetooth, recebe dados de peso do ESP32, e envia comandos para controlar o motor baseado nos dados recebidos.
ESP32: O ESP32 lê o peso da célula de carga, envia os dados de peso para o app via Bluetooth, e controla o motor de acordo com os comandos recebidos do app.
Fluxo de Comunicação
Inicialização:
O app e o ESP32 são inicializados. O app se conecta ao ESP32 via Bluetooth.
Escolha do Caminhão:
O usuário escolhe um caminhão no app.
Leitura e Envio de Peso:
O ESP32 lê o peso usando a célula de carga e envia os dados para o app.
Processamento de Dados no App:
O app recebe os dados de peso e decide qual comando enviar ao ESP32.
Controle do Motor:
O ESP32 recebe o comando do app e controla o motor de acordo.
Este é o processo de comunicação simplificado entre o app e o ESP32 usando o código fornecido, considerando agora que estamos falando de "caminhões".
