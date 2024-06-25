
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
Verifica se global caminhão no está no dicionário caminhõinhos.
Calcula a diferença entre global received_data e global caminhão no.
Dependendo do valor de global diferenca, realiza diferentes ações:
Se diferenca >= 0:
Atualiza o texto de Resultado1 com a mensagem adequada.
Se diferenca < 0:
Envia o valor de decimal (1, 2 ou 3) para ClienteBluetooth1 dependendo do valor de diferenca.
Atualiza o texto de Resultado1 com a mensagem adequada.
Marco_do_seu_caminhão.AfterPicking:

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

Descreva como foi o desenvolvimento do código do arduino/ESP.

## Comunicação entre App e Hardware

Descreva como foi o processo de comunicação entre App e arduino/ESP.
