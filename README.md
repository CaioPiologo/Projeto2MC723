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
* Branch Estático: Supor que todos os branchs ocorrem, e calcular penalidade de ciclos, caso não tenha ocorrido o branch.
* Branch Dinâmico: Utilizar uma tabela de 2 bits que contabiliza se houve ou não recentemente um branch, e armazenar essas informações para criar uma máquina de estados para gerar a previsão de branch ou não. 

### Configurações da cache:

As configurações da cache L1 e L2 serão as seguintes:
* Size: 8KB / Block: 64b
* Size: 8KB / Block: 512b
* Size: 64KB / Block: 64b
* Size: 64KB / Block: 512b

### Roteiro dos experimentos :

O roteiro vai partir de uma configuração inicial, para os processador MIPS, pipeline de 5 estágios, escalar, branch estático e melhor configuração de cache. A partir dessa configuração, usaremos os simuladores do MIPS e o software Dinero IV para variar estes parâmetros para avaliar a influência destes no desempenho do simulador, para isso faremos uma contagem de certos eventos.
Faremos 11 configurações de processador: 
* Faremos 8 configurações com pipeline de 5 estágios, gerando a permutação entre todos os branchs e todas as configurações das caches L1 e L2, e com o processador sendo escalar; 
* Faremos uma configurão superescalar de pipeline de 5 estágios, branch estático, e configuração de cache Size: 8KB / Block: 64b; 
* Uma configuração com pipeline de 7 estágios, processador escalar, branch escalar e configuração de cache igual a acima;
* Uma configuração com pipeline de 13 estágios, processador escalar, branch escalar e configuração de cache igual a acima. 

Os eventos que iremos coletar em cada benchmark são:
* Miss rate de L1;
* Miss rate de L2;
* Stalls por branchs;
* Stall por dados;
* Número de ciclos total gerado pela execução do programa;
* Tempo total de execução do programa;

