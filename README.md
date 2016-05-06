# Projeto2MC723
## Mc 723 - Laboratório de Projeto de Sistemas Computacionais.
## Projeto 2: Desempenho do processador.

### Integrantes do grupo:
* Pedro Elias Lucas Ramos Meireles,         RA: 148914 
* Andre Tsuyoshi Sakiyama                   RA: 150547
* Caio Vinicius Piologo Véras Fernandes     RA: 145574
* Luiz Rodolfo Felet Sekijima               RA: 117842

### Escolha dos benchmarks:

Todos os benchmarks estão inclusos no Mibench. Os benchmarcks selecionados foram os larges do seguintes programas:
* Bitcount;
* Dijkstra;
* Rijindael;
* JPEG.


### Estratégia para critérios básicos:
* Modificar o arquivo mips_isa.cpp para contar certas instruções seguidas e avaliar as bolhas geradas em diferentes tamanhos de pipeline. 
* Modificar no arquivo mips_isa.cpp para que este analise a simultaneidade de execução das instruções. 


### Configurações branch:
* Sem Branch predction: Não utilizar a estragtégia de branch prediction, para uso de comparação.
* Branch Estático: Supor que todos os branchs ocorrem, e calcular penalidade de ciclos, caso não tenha ocorrido o branch.
* Branch Dinâmico: Utilizar uma tabela de 2 bits que contabiliza se houve ou não recentemente um branch, e armazenar essas informações para criar uma máquina de estados para gerar a previsão de branch ou não. 

### Configurações da cache:

As configurações da cache L1 serão as seguintes:
* Size: 8KB / Block: 64b
* Size: 64KB / Block: 512b

As configurações da cache L2 serão as seguintes:
* Size: 128KB / Block: 64b
* Size: 256KB / Block: 512b

### Hazards:
Hazards de pipeline se refere a uma situação na qual um programa para de funcionar corretamente por causa da implementação em um processador com pipeline. Tratar os harzards aumentam a complexidade do pipeline e reduzem a sua performance e o resolução eficiente desses hazards é torna o design de processadores difícil.


Há três possíveis tipos de hazards:

* Hazard estrutural: um mesmo componente de hardware é requisitado por diferentes estágios ao mesmo tempo
* Hazard de controle: ocorre quando a próxima instrução não é conhecida
* Hazards de dados: ocorre quando o input de uma instrução não está disponível para uso no ciclos que é necessário

A solução para o hazard estrutural é adicionar mais componentes de hardware para poder atender todas as requisições ao mesmo tempo. Por exemplo, adicionar duas unidades de memória para atender pedidos de fetch de *instrução* e executar instruções de *lw* ou *sw* que fazem acesso a memória ao mesmo tempo.

O problema de hazard de controle pode ser resolvidos com *branch prediction*, ele fará uma previsão de qual será a próxima instrução e indica para a próxima instrução para a unidade de *instruction fetch* se errar, há uma penalidade para buscar a instrução correta.

O problema de hazard de dados se divide em três tipos:

* Read After Write (RAW): um dado é escrito e logo em seguida ele é requerido para uma leitura desse dado, por exemplo add R1,X,X seguido de sub X,R1,X. Neste caso a operação de sub estára usando um dado incorreto
* Write After Read (WAR): um dado é lido e em seguida é escrito, pode ocorrer da escrita do dado ocorrer antes da sua leitura 
* Write After Write (WAW): duas escritas em sequência podem inverter a ordem dos dados armazenados.


#### Pipeline de 5 estágios

No pipeline de 5 estágios com que vamos trabalhar, vamos supor que os hazard estrutural não ocorre. O hazards de controle vão ocorrer no caso de branchs, sendo resolvidos pelos *branch predictors* utilizados e o dos hazards de dados, apenas os casos de RAW, escrita seguido de leitura irá ocorrer. Também iremos considerar que o pipeline usa forwarding, logo o único hazards de dados que contabiliza *stalls* será um load seguido de operação aritimética.

### Roteiro dos experimentos :

O roteiro vai partir de uma configuração inicial, para os processador MIPS, pipeline de 5 estágios, escalar, branch estático e melhor configuração de cache. A partir dessa configuração, usaremos os simuladores do MIPS e o software Dinero IV para variar estes parâmetros para avaliar a influência destes no desempenho do simulador, para isso faremos uma contagem de certos eventos.
Faremos 11 configurações de processador: 
* Faremos 4 configurações com pipeline de 5 estágios, inicialmente sem branch prediction e permutando todas as configurações das caches L1 e L2, e com o processador sendo escalar; 
* Faremos mais duas configurações, uma com branch estático e outra com branch dinâmico e configuração de cache L1 Size: 64KB / Block: 512b e L2 Size: 256KB / Block: 512b;
* Faremos uma configurão superescalar de pipeline de 5 estágios, sem branch prediciton, e configuração de cache L1 Size: 64KB / Block: 512b e L2 Size: 256KB / Block: 512b;
* Uma configuração com pipeline de 7 estágios, processador escalar, sem branch prediction e configuração de cache igual a acima;
* Uma configuração com pipeline de 13 estágios, processador escalar, sem branch prediction e configuração de cache igual a acima. 

Os eventos que iremos coletar em cada benchmark são:
* Miss rate de L1;
* Miss rate de L2;
* Stalls por branchs;
* Stall por dados;
* Número de ciclos total gerado pela execução do programa;
* Tempo total de execução do programa;

