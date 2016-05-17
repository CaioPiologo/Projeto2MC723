#MC713 - Projeto 2
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
* Hazard de controle: ocorre quando a próxima instrução não é conhecida
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
Para o pipeline de 5 estágios hazards de WAW e WAR são resolvidos pelo fowarding ou não ocorrem, logo, o único hazard que devemos considerar é o RAW, normalmente são necessários dois stalls para resolvê-lo, no entanto devido ao fowarding um *stall* é suficiente.
Para detectar o hazard, o **hazard detector** verifica se a instrução entre os estágios de **instruction decoding (ID)** e **execute (EX)** faz um acesso a memória e se o registrador onde o valor é armazenado vai ser lido entre  o estágio de **instruction fetch (IF)** e **ID**, se isto ocorre, uma bolha é inserida.

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


##Referências
[1] http://www.hardware.com.br/dicas/entendendo-cache.html 
