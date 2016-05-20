#MC723 - Projeto 2
##Membros do grupo :
- Pedro Elias Lucas Ramos Meireles      RA: 148914
- Andre Tsuyoshi Sakiyama               RA: 150547
- Caio Vinicius Piologo Véras Fernandes RA: 145574
- Luiz Rodolfo Felet Sekijima           RA: 117842

##Configurações Cache:
  Dada as expecificações determinadas no roteiro do projeto 2, foram feitas as seguintes etapas para obtenção do *miss rate* das configurações de cache expecificadas pelo grupo:
  1. Geração dos *traces* dos benchmarks selecionados;
  2. Execução do programa Dinero IV, para as configurações de cache expecificadas no experimento. 

Para a geração dos *traces* dos benchmarks selecionados, foi necessário fazer modificações breves no código do mips_isa.cpp. Este código é responsável pela execução da simulação dos programas compilados utilizando o simulador do mips, e a função inicial dele é imprimir na tela as imagens de debug do sistema, para o caso do usuário do simulador queira acompanhar a execução dos comandos do mips, para o código compilado pelo mips, na máquina local. 
  
No entanto, para realizar as configurações da cache através do software Dinero IV, é necessário criar um *trace* que o mesmo seja capaz de compreender. Para isso, foram feitas uma série de modificações no arquivo [mips_isa.cpp](../traceManager/mips_isa_mod.cpp "mips_isa traces")

Logo após essas modificações no código mips_isa.cpp, foram executado todos os benchmarks no mips (devidamente compilado para admitir essas novas alterações).
  
Após gerar todos os arquivos, eles foram executados no software Dinero IV, de forma que todos os arquivos com os traces gerados sejam executados para cada configuração de cache. As configurações de cache utilizadas por nós foram as seguintes: 
  1. Cache L1 de dados e Cache L1 de instruções com tamanho de 8KB e blocos de tamanho 64b; Cache L2 unificada de tamanho 128KB e bloco de 64b. 
  2. Cache L1 de dados e Cache L1 de instruções com tamanho de 64KB e blocos de tamanho 512b; Cache L2 unificada de tamanho 128KB e bloco de 64b. 
  3. Cache L1 de dados e Cache L1 de instruções com tamanho de 8KB e blocos de tamanho 64b; Cache L2 unificada de tamanho 256KB e bloco de 512b. 
  4. Cache L1 de dados e Cache L1 de instruções com tamanho de 64KB e blocos de tamanho 512b; Cache L2 unificada de tamanho 256KB e bloco de 512b

A seguir, segue um link com as tabelas que contem os dados de demand fetches e miss rate em porcentagem, para cada Cache e cada Programa: 
Os dados correspondentes à *demand fethces* e *miss rate* para cada benchmark executado em cada cache podem ser encontrados na seguinte [tabela](https://docs.google.com/spreadsheets/d/1JgQaRzANXwcKrzuqRanqKzjbjIpdOShRg3fVvYLbbK4/edit?usp=sharing "Cache Tests Data")

A partir dessas informações, podemos tirar as seguintes conclusões, para cada benchmark:
- **Jpeg Decode**: A melhor configuração de cache foi a configuração 4, visto que ela foi a que possuiu o menor miss rate global, para todas as caches. A segunda melhor foi a configuração 1.
- **Jpeg Encode**: A melhor configuração de cache foi a configuração 4, visto que ela foi a que possuiu o menor miss rate global para todas as caches. A segunda melhor foi a configuração 3. 
* **Dijkstra Large**: A melhor configuração de cache foi a configuração 4, visto que ela foi a que possuiu o menor miss rate global, para todas as caches. A segunda foi a configuração 2.
* **Rijindael Decode**: A melhor configuração de cache foi a configuração 4, visto que ela foi a que possuiu o menor miss rate global, para todas as caches. A segunda melhor foi a configuração 1.
* **Rijindael Encode**: A melhor configuração de cache foi a configuração 4, visto que ela foi a que possuiu o menor miss rate global, para todas as caches. A segunda melhor foi a configuraçaõ 1. 
* **Bitcounts Large**: A melhor configuração de cache foi a configuração 4, visto que ela possuiu o menor número de instruções erradas, para as demais configurações, para todas as caches. A segunda melhor foi a configuração 3.

Para as partes seguintes do relatório, iremos supor que o custo de ciclos para um miss, para as respectivas caches, serão:
* L1 de dados e de instruções: 3 ciclos de clock.
* L2 unificada: 10 ciclos de clock. 

##Hazards
Hazards ocorrem no pipeline devido a divisão em estágios do processamento de instruções. Apesar da separação em estágios melhorar a performance do processador ela deixa o design de processadores mais complexa criando dependências entre os estágios.
Há quatro tipos de hazards:

* Hazard estrutural: um mesmo componente de hardware é requisitado por diferentes estágios ao mesmo tempo
* Hazard de controle: ocorre quando a próxima instrução não é conhecida ou há desvio de fluxo
* Hazards de dados: ocorre quando o input de uma instrução não está disponível para uso no ciclo em que o dado é necessário
* Hazards de memória: ocorrem quando é necessário buscar informação na memória, operação muito custosa em relação a tempo


A solução para o hazard estrutural é adicionar mais componentes de hardware para poder atender todas as requisições ao mesmo tempo. Por exemplo, adicionar duas unidades de memória para atender pedidos de *fetch* de instrução e executar instruções de *lw* ou *sw* que fazem acesso a memória ao mesmo tempo.

O problema de hazard de controle pode ser resolvidos com *branch prediction*, ele fará uma previsão de qual será a próxima instrução e indica para a próxima instrução para a unidade de *instruction fetch* se errar, há uma penalidade para buscar a instrução correta.

O problema de hazard de dados se divide em três tipos:

* Read After Write (RAW): um dado é escrito e logo em seguida ele é requerido para uma leitura desse dado, por exemplo add R1,X,X seguido de sub X,R1,X. Neste caso a operação de sub estára usando um dado incorreto
* Write After Read (WAR): um dado é lido e em seguida é escrito, pode ocorrer da escrita do dado ocorrer antes da sua leitura 
* Write After Write (WAW): duas escritas em sequência podem inverter a ordem dos dados armazenados.

Os hazards de memória são resolvidos com a implementação de caches, que são unidades de memória mais rápidas que reduzem o tempo médio para acessar dados na memória principal. As caches estão organizadas em uma hierarquia de níveis, quanto menor a cache, mais rápido é o seu acesso e quanto maior a sua capacidade, mais lento é o acesso.

Para as simulações realizados no projeto, consideramos que hazard estrutural não ocorre, visto que há duas unidades de memória para acessar instruções e dados separadamente. Hazard de controle são influênciados pelo *branch predictor* escolhido na configuração da simulação. O hazards de dados dependem do tamanho do pipeline ou se é supereescalar ou escalar, mas sempre consideramos que há fowarding. Hazard de dados vão depender da configuração de cache escolhida.


#####Pipeline Escalar de 5 estágios
Para o pipeline de 5 estágios hazards de WAW e WAR não ocorrem, logo, o único hazard que devemos considerar é o RAW, que pode ser resolvido por *stalling* e *fowarding*. Normalmente são necessários dois stalls para resolvê-lo, no entanto devido ao fowarding um *stall* é suficiente.
Para detectar o hazard, o **hazard detector** verifica se a instrução entre os estágios de **instruction decoding (ID)** e **execute (EX)** faz um acesso a memória e se o registrador onde o valor é armazenado vai ser lido entre  o estágio de **instruction fetch (IF)** e **ID**, se isto ocorre, uma bolha é inserida. O hazard detector foi implementado em [mips_isa_escalar.cpp](../data_hazard/mips_isa_escalar.cpp).

Os resultados para os benchmarks estão apresentados a seguir:

| Benchmark     | Ciclos    | Data Stalls         |
| -------------------|:----------------:|--------------------:|
| Bitcount	 | 511004870	| 8170	  |
| Dijkstra	 | 181060823	| 30155599	  |
| Rijndael (encode) | 420492338	| 21916061	  |
| Rijndael (decode) | 441097079	| 29800097	  |
| JPEG (encode) | 102672414	| 5610850	  |
| JPEG (decode) | 32161693	| 2892153	  |


#####Pipeline Superescalar
Para aumentar o desempenho do processador, é possível duplicar os estágios do pipeline para que o processador execute mais instruções em paralelo. Em um processador superescalar ideal, com uma replicação de pipeline o CPI de um processador cai pela metade, pois há o dobro de instruções sendo executadas por ciclo. No entanto, o pipeline se torna mais complexo e outros problemas de hazards ocorrem.
 O pipeline superescalar implementado em [mips_isa_superescalar.cpp](../data_hazard/mips_isa_superescalar.cpp) é o descrito no livro do Patterson e Hennessy[2], onde há dois pipelines, podendo então duas instruções serem executadas no mesmo estágio. Mas em um pipeline, apenas são aceitas instruções aritiméticas e de branch e no outro são executadas instruções de load e store. O novo pipeline pode ser construido a partir da modificação do pipeline de cinco estágio usual, a cada ciclo, duas instruções são buscadas e decodificadas na memória nos estágios do *fetch* e *decoding*, então mais uma ALU é adicionada no estágio EX para que seja possível realizar operações aritiméticas e ao mesmo tempo realizar calculos de endereço para instruções de load e store.

A nova configuração do processador faz com que novos hazards apareçam, o hazard causado pelo load se mantem, mas agora a dependência do load pode ocorrer com uma instrução que está no mesmo estágio que o load, então deve ser gerado um stall de duas bubbles para resolver o hazard. Outro problema ocorre com a dependência do resultado de uma operação aritimética, se uma operação de load ou store precisa deste resultado para calcular o endereço, é necessário gerar um stall para que o endereço seja calculado corretamente. 

Os resultados para os benchmarks estão apresentados a seguir:

| Benchmark     | Ciclos    | Data Stalls         |
| -------------------|:----------------:|--------------------:|
| Bitcount	 | 482864335	| 15778283	  |
| Dijkstra	 | 129641625	| 70925848	  |
| Rijndael (encode) | 342272216	| 75048164	  |
| Rijndael (decode) | 111294148	| 105984811	  |
| JPEG (encode) | 73682627	| 34104664	  |
| JPEG (decode) | 21784657	| 14492166	  |

Podemos ver que o número de ciclos cai em relação ao pipeline escalar, no melhor dos casos a contagem de ciclos é a metade no pipeline superescalar. No entanto, o número de stalls aumentou muito, na maioria das vezes o número de stalls dobrou e aumento de stalls é causado pelo aumento de hazards, pois o pipeline ficou mais complexo. Outra razão é o fato do pipeline superescalar ser estático, então as instruções são inseridas no pipeline na ordem em que estão na memória, um pipeline dinámico que alterá a ordem das instruções teria um número de stalls mais reduzido.


##Branch Prediction

As ações de *branch* em códigos tendem a sempre criar bolhas no pipeline de execução se não tratadas, uma vez que o pipeline acredita que irá apenas mover seu *PC* para a próxima posição e por conta disso carrega as instruções de *PCs* seguintes nos estágios iniciais do pipeline, até analisar a instrução atual e verificar que se trata de um *branch*, tendo que descartar as instruções carregadas nos estágios anteriores.
Dessa forma, o *branch prediction* foi desenvolvido de forma a atenuar as perdas de cada branch de forma a tentar prever para qual *PC* o fluxo será desviado. Para o projeto foram usados os dois seguintes tipos:
- **Branch Estático**: Assume que a posição de "chute" para o próximo *PC* estará sempre correta, e assim carrega no pipeline as posições referentes a este *PC*. Caso acerte, o fluxo se dará normalmente sem bolhas, caso contrário irá descartar as instruções carregadas pelo chute e transferir o fluxo como se tivesse apenas executado um *branch* sem *branch prediction*
- **Branch Dinâmico**: Assim como o *branch estático*, ele irá assumir um *PC* para o qual o fluxo será transferido, porém este valor irá depender de como o fluxo do programa está sendo realizado e será modificado ao longo do mesmo.
  - Por exemplo, o *branch predictor* de 1 bit age similarmente ao *branch estático*, no entanto, ao errar um desvio de fluxo ele irá assumir que os próximos *branchs* também irão errar, ou seja, o fluxo não será desviado para o *PC* do chute mas sim para o próximo *PC*. E da mesma forma, quando acertar ele irá assumir que irá continuar acertando o desvio da predição. 

####Branch Estático

O código [mips_isa.cpp](../branchPrediction/mips_isa_estatico.cpp "Static Branch Predictor") foi modificado de forma a armazenar o pc da última posição de branch e sempre assumir que o próximo branch será realizado para esta posição, como descrito anteriormente. Caso não seja, será considerado que um hazard ocorrerá no pipeline e uma nova posição será armazenada.
Executando os benchmarks a seguir, obtivemos os seguintes resultados:

| Benchmark         | Instruções  | Branches    | Branch Mispredictions | Misses Percentage |
| ------------------|:-----------:|------------:| ---------------------:| -----------------:|
| Bitcount	        | 536894266	  | 117341166	  | 28130876		          | 24.0%		          |
| Dijkstra	        | 223690619	  | 51329314	  | 18271877		          | 35.6%		          |
| Rijndael (encode) | 453556705	  | 15040294	  | 10336543	            | 68.7% 		        |
| Rijndael (decode) | 483868969	  | 16654333	  | 9924102	              | 59.6% 		        |
| JPEG (encode)     | 111543858	  | 13528023	  | 6910492		            | 51.1%		          |
| JPEG (decode)     | 35847190	  | 1816631	    | 222811		            | 12.3%		          |

Como pode-se perceber, os resultados possuiram uma média de erros de predição entre 10~70%. A alta variação entre número de misses pode ter ocorrido devido ao fato de o código possuir muitos saltos diferentes, raramente repetindo a mesma posição saltada.

####Branch Dinâmico
Dentre os branchs dinâmicos existentes foi selecionado para o caso o branch prediction de 1 bit. Dessa forma, foi modificado o código [mips_isa.cpp](../branchPrediction/mips_isa_dinamico.cpp "Dynamic Branch Prediction") de forma a fazer o descrito anteriormente, obtendo os seguintes valores para os benchmarks:

| Benchmark     	| Instruções    | Branches        | Branch Mispredictions | Misses Percentag	|
| ----------------------|:-------------:|----------------:| ---------------------:| -------------------:|
| Bitcount		| 536894310	| 117341166	  | 6750164		  | 5.8%		|
| Dijkstra		| 223691867	| 51329314	  | 153864		  | 0.3%		|
| Rijndael (encode) 	| 453563104	| 15040294	  | 412354	     	  | 2.7% 		|
| Rijndael (decode) 	| 483868969	| 16654333	  | 405998	     	  | 2.4% 		|
| JPEG (encode) 	| 111543858	| 13528023	  | 287842		  | 2.1%		|
| JPEG (decode) 	| 35848023	| 1816631	  | 58818		  | 3.2%		|

Percebe-se que os resultados possuem uma média de erros de predição entre 0~10%, como esperado de um branch predictor dinâmico. 


### Clock

Para a parte final do projeto, fizemos a contagem total de ciclos de cada programa nas diferentes configurações propostas e é necessário achar o clock de cada configuração para calcular o tempo execução dos programas. Para isso foi feita uma análise de processadores MIPS existentes de configuração semelhante com as propostas aqui. O [R4300i](https://en.wikipedia.org/wiki/R4200#R4300i) é um processador de 5 estágios escalar anunciado em 1995, que possuia frequência de 133 MHz, que equivale a um clock de 7.5 ns. O [R5000](https://en.wikipedia.org/wiki/R5000) é um processador de 5 estágios superescalar com duas linhas de pipeline, a configuração é um pouco diferente da proposta aqui, o tipo de instruções que podem rodar simultaneamente são diferentes, mas este é o processador que mais se aproxima ao nosso. Possui frequência de 200MHz, que equivale a um clock de 5 ns.

##Tabelas finais


####Cofiguração 1:

 - Pipeline de 5 estágios
 - Processador escalar
 - Tamanho L1 dados: 8KB
 - Tamanho L1 instrução: 8KB
 - Tamanho L2 unificada: 128KB
 - Bloco L1 dados: 64B
 - Bloco L1 instruções: 64B
 - Bloco L2 unificada: 64B
 - Sem branch prediction

| 		| Bitcount	| Dijkstra	| Rijndael(encode)| Rijndael(decode)| JPEG(encode)| JPEG(decode)	|
| :------------ |:------------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| Total instruções | 536894239	| 223690592	| 453556707	| 483862590	| 111543017	| 35847192	|
| Miss rate L1 dados| 0.00%	| 2.92%		| 23.91%	| 22.35%	| 2.23%		| 2.33%		|
| Miss rate L1 instruções| 0.00%| 0.20%		| 3.88%		| 4.14%		| 0.04%		| 0.09%		|
| Miss rate L2     | 17.30%	| 0.80%		| 17.97%	| 13.89%	| 19.10%		| 3.04%		|
| Stalls por branch| 100.0%	| 100.0%	| 100.0%	| 100.0%	| 100.0%		| 100.0%	|
| Stalls por dados |  0.03%	| 13.48%	| 16.94%	| 20.88%	| 15.21%		| 22.19%	|
| Total ciclos	   |  771588925 | 361294184	| 682386397	| 717429446	| 147800555	| 43171316	|
| Tempo de execução| 5.79 s	| 2.71 s	| 5.12 s	| 5.38 s	| 1.11 s	| 0.32 s	|


----------


####Cofiguração 2:

 - Pipeline de 5 estágios
 - Processador escalar
 - Tamanho L1 dados: 64KB
 - Tamanho L1 instrução: 64KB
 - Tamanho L2 unificada: 128KB
 - Bloco L1 dados: 512B
 - Bloco L1 instruções: 512B
 - Bloco L2 unificada: 64B
 - Sem branch prediction

| 		| Bitcount	| Dijkstra	| Rijndael(encode)| Rijndael(decode)| JPEG(encode)	| JPEG(decode)	|
| :------------ |:------------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| Total instruções | 536894239	| 223690592	| 453556707	| 483862590	| 111543017	| 35847192	|
| Miss rate L1 dados| 0.00%	| 0.03%		| 18.12%	| 16.32%	| 0.75%		| 0.38%		|
| Miss rate L1 instruções| 0.00%| 0.00%		| 0.36%		| 0.00%		| 0.00%		| 0.01%		|
| Miss rate L2     | 63.50%	| 17.46%	| 82.22%	| 74.51%	| 74.19%		| 62.18%	|
| Stalls por branch| 100.0%	| 100.0%	| 100.0%	| 100.0%	| 100.0%		| 100.0%	|
| Stalls por dados | 0.03%	| 13.48%	| 16.94%	| 20.88%	| 15.21%		| 22.19%	|
| Total ciclos	   | 771596792	| 356883288	| 1824438875	| 2106034464	| 161167477	| 44893624	|
| Tempo de execução| 5.79 s |  2.68 s |  13.68 s |  15.80 s |  1.21 s |  0.34 s |


----------


####Cofiguração 3:

 - Pipeline de 5 estágios
 - Processador escalar
 - Tamanho L1 dados: 8KB
 - Tamanho L1 instrução: 8KB
 - Tamanho L2 unificada: 256KB
 - Bloco L1 dados: 64B
 - Bloco L1 instruções: 64B
 - Bloco L2 unificada: 512B
 - Sem branch prediction

| 		| Bitcount	| Dijkstra	| Rijndael(encode)| Rijndael(decode)| JPEG(encode)	|JPEG(decode)	|
| :------------ |:------------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| Total instruções | 536894239	| 223690592	| 453556707	| 483862590	| 111543017	| 35847192	|
| Miss rate L1 dados| 0.00%	| 2.92%		| 23.91%	| 22.35%	| 2.23%		| 2.33%		|
| Miss rate L1 instruções| 0.00%| 0.20%		| 3.88%		| 4.14%		| 0.04%		| 0.09%		|
| Miss rate L2     | 6.44%	| 0.11%		| 30.35%	| 22.96%	| 13.97%		| 3.46%		|
| Stalls por branch| 100.0%	| 100.0%	| 100.0%	| 100.0%	| 100.0%		| 100.0%	|
| Stalls por dados | 0.03%	| 13.48%	| 16.94%	| 20.88%	| 15.21%		| 22.19%	|
| Total ciclos	   | 771586316	| 361125356	| 716980968	| 781119124	| 147302282	| 43188828	|
| Tempo de execução| 5.79 s |  2.71 s |  5.38 s |  5.86 s |  1.10 s |  0.32 s |


----------


####Cofiguração 4:

 - Pipeline de 5 estágios
 - Processador escalar
 - Tamanho L1 dados: 64KB
 - Tamanho L1 instrução: 64KB
 - Tamanho L2 unificada: 256KB
 - Bloco L1 dados: 512B
 - Bloco L1 instruções: 512B
 - Bloco L2 unificada: 512B
 - Sem branch prediction

| 		| Bitcount	| Dijkstra	| Rijndael(encode)| Rijndael(decode)| JPEG(encode)	|JPEG(decode)	|
| :------------ |:------------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| Total instruções | 536894239	| 223690592	| 453556707	| 483862590	| 111543017	| 35847192	|
| Miss rate L1 dados| 0.00%	| 0.03%		| 18.12%	| 16.32%	| 0.75%		| 0.38%		|
| Miss rate L1 instruções| 0.00%| 0.00%		| 0.36%		| 0.00%		| 0.00%		| 0.01%		|
| Miss rate L2     | 61.60%	| 9.36%		| 56.74%	| 47.72%	| 57.98%		| 36.99%		|
| Stalls por branch| 100.0%	| 100.0%	| 100.0%	| 100.0%	| 100.0%		| 100.0%	|
| Stalls por dados | 0.03%	| 13.48%	| 16.94%	| 20.88%	| 15.21%		| 22.19%	|
| Total ciclos	   | 771586226	| 356565661	| 653990644	| 727489633	| 146366555	| 42658406	|
| Tempo de execução| 5.79 s |  2.67 s |  4.90 s |  5.46 s |  1.10 s |  0.32 s |


----------


####Configuração 5:

 - Pipeline de 5 estágios
 - Processador escalar
 - Tamanho L1 dados: 64KB
 - Tamanho L1 instrução: 64KB
 - Tamanho L2 unificada: 256KB
 - Bloco L1 dados: 512B
 - Bloco L1 instruções: 512B
 - Bloco L2 unificada: 512B
 - Branch prediction estático

| 		| Bitcount	| Dijkstra	| Rijndael(encode)| Rijndael(decode)| JPEG(encode)	|JPEG(decode)	|
| :------------ |:------------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| Total instruções | 536894266	| 223690619	| 453556705	| 483868969	| 111543858	| 35847190	|
| Miss rate L1 dados| 0.00%	| 0.03%		| 18.12%	| 16.32%	| 0.75%		| 0.38%		|
| Miss rate L1 instruções| 0.00%| 0.00%		| 0.36%		| 0.00%		| 0.00%		| 0.01%		|
| Miss rate L2     | 61.60%	| 9.36%		| 56.74%	| 47.72%	| 57.98%		| 36.99%		|
| Stalls por branch| 24.0% 	| 35.6%		| 68.7%		| 59.6%		| 51.1%		| 12.3%		|
| Stalls por dados | 0.03%	| 13.48%	| 16.94%	| 20.88%	| 15.21%		| 22.19%	|
| Total ciclos	   | 593165646	| 290450787	| 644583142	| 714029171	| 133131493	| 39470766	|
| Tempo de execução| 4.45 s |  2.18 s |  4.83 s |  5.36 s |  1.00 s |  0.30 s |


----------


####Configuração 6:

 - Pipeline de 5 estágios
 - Processador escalar
 - Tamanho L1 dados: 64KB
 - Tamanho L1 instrução: 64KB
 - Tamanho L2 unificada: 256KB
 - Bloco L1 dados: 512B
 - Bloco L1 instruções: 512B
 - Bloco L2 unificada: 512B
 - Branch prediction dinâmico

| 		| Bitcount	| Dijkstra	| Rijndael(encode)| Rijndael(decode)| JPEG(encode)	|JPEG(decode)	|
| :------------ |:------------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| Total instruções | 536894310	| 223691867	| 453563104	| 483868969	| 111543858	| 35848023	|
| Miss rate L1 dados| 0.00%	| 0.03%		| 18.12%	| 16.32%	| 0.75%		| 0.38%		|
| Miss rate L1 instruções| 0.00%| 0.00%		| 0.36%		| 0.00%		| 0.00%		| 0.01%		|
| Miss rate L2     | 61.60%	| 9.36%		| 56.74%	| 47.72%	| 57.98%		| 36.99%		|
| Stalls por branch| 5.8%	| 0.3%		| 2.7%		| 2.4%		| 2.1%		| 3.2%		|
| Stalls por dados | 0.03%	| 13.48%	| 16.94%	| 20.88%	| 15.21%		| 22.19%	|
| Total ciclos	   | 550404352	| 254216016	| 624734764	| 694992963	| 119886193	| 39142780	|
| Tempo de execução| 4.13 s |  1.91 s |  4.69 s |  5.21 s |  0.90 s |  0.29 s |


----------


####Configuração 7:

 - Pipeline de 5 estágios
 - Processador superescalar
 - Tamanho L1 dados: 64KB
 - Tamanho L1 instrução: 64KB
 - Tamanho L2 unificada: 256KB
 - Bloco L1 dados: 512B
 - Bloco L1 instruções: 512B
 - Bloco L2 unificada: 512B
 - Branch prediction dinâmico

| 		| Bitcount	| Dijkstra	| Rijndael(encode)| Rijndael(decode)| JPEG(encode)	|JPEG(decode)	|
| :------------ |:------------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| Total instruções | 536894310	| 223691867	| 453563104	| 483868969	| 111543858	| 35848023	|
| Miss rate L1 dados| 0.00%	| 0.03%		| 18.12%	| 16.32%	| 0.75%		| 0.38%		|
| Miss rate L1 instruções| 0.00%| 0.00%		| 0.36%		| 0.00%		| 0.00%		| 0.01%		|
| Miss rate L2     | 61.60%	| 9.36%		| 56.74%	| 47.72%	| 57.98%		| 36.99%		|
| Stalls por branch| 5.8%	| 0.3%		| 2.7%		| 2.4%		| 2.1%		| 3.2%		|
| Stalls por dados |  0.03% | 13.69% | 17.81% | 22.55% | 16.95% | 28.53% |
| Total ciclos	   | 566174465 | 294986265 | 677866867 | 771177677 | 148380007 | 50742793 |
| Tempo de execução| 2.83s | 1.47s | 3.39s | 3.86s | 0.74s | 0.25s |

###Análise de desempenho

Fazendo uma comparação entre as configurações 1 e 2 vemos que aumentar o tamanho e bloco apenas da cache L1 pode afetar negativamente o miss rate na cache L2, para o programa **Rijndael**, o miss rate foi de 17% para 82%, dobrando o tempo de execução do programa. Nas configurações 1 e 3 a diferença é o tamanho e bloco da cache L2, o programa Rijndael novamente apresentou pior desempenho quando comparado com a configuração 1, o miss rate na L2 foi de 17% para 30%. Nestas duas comparações vemos a importância do equilíbrio entre as caches L1 e L2, apenas aumentar uma das duas não necessariamente significa melhorar o miss rate das caches, as caches devem crescer em tamanho e tamanho de bloco proporcionalmente para obter o melhor desempenho. Na configuração 4 as duas caches foram aumentadas e foi a configuração que mostrou melhor desempenho entre 1,2,3 e 4.

Com a melhor configuração de cache partimos para a análise do *brach predictor*, na configuração 5 usamos um método  estático que sempre considera que o branch ocorreu, melhorando o desempenho do processador quando comparado com uma configuração sem *branch predictor*, mas ele apresenta desempenho inferior quando comparado com um método dinâmico (configuração 6), no caso do programa JPEG (encoding), os stalls causados por branch cairam de 51% para 2%, um grande aumento na performance. 

Por fim, com a melhor configuração de processador escalar, foi feita uma comparação com um processador superescalar. A configuração 7 usou mais ciclos para processar os programas, isso devido ao aumento de stalls causados por hazards de dados. No entanto, o processador superescalar possui um clock 33% menor que o escalar e como o aumento de ciclos foi na ordem de 20%, houve uma melhora no desempenho do processador superescalar. Assim temos que a configuração 7 apresentou o melhor desempenho, executando os programas do benchmark com maior velocidade.


##Conclusão

Com este projeto vimos como as modificações na configuração vão se acumulando e melhorando o desempenho do processador. A ideia é que quando se melhora uma parte do processador, surge um gargalo em outro ponto, assim é necessário fazer uma nova modificação neste ponto para melhorar o desempenho. Este projeto foi uma versão simplificada dos processadores atuais, que passaram por centenas de modificações para atingir altos níveis de desempenho e há muitas outras modificações que podem ser analisadas (mudar estágios do processador). A análise do desempenho de um processador foi bem sucedida neste projeto.


##Referências
[1] http://www.hardware.com.br/dicas/entendendo-cache.html 
[2] David A. Patterson and John L. Hennessy. Computer Organization Design, The Hardware/Software Interface. Morgan Kaufmann
