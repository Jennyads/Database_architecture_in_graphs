<h3> Arquitetura de Banco de Dados em Grafos </h3>

Os bancos de grafos permitem que além das entidades, sejam também armazenados os relacionamentos entre elas.
Os elementos básicos são:

- Nó: instância de um objeto.
- Aresta: relacionamento (pode ter propriedades). As arestas sempre serão direcionadas e existe um nó inicial e final. É possível acrescentar mais propriedades às arestas, por exemplo, em um relacionamento de amizadade, pode-se adicionar um data. 
- As consultas em um banco de grafo são chamadas de percurso (processo de andar pelos nós). Os percursos não alteram os nós.
- Percorrer um grafo é mais rápido que realizar junções nos bancos relacionais.
- Bancos: neo4j, orientDB, infinitegraph.

Há dois modelos populares de bancos de dados de grafos: grafos de propriedades e grafos RDF. O grafo de propriedades foca análises e consultas, enquanto o grafo RDF enfatiza a integração de dados. Ambos os tipos de grafos consistem em uma coleção de pontos (vértices) e nas conexões entre esses pontos (bordas). Mas também existem diferenças.

* Grafos de propriedades:
Os grafos de propriedades são usados para modelar relacionamentos entre dados e permitem a consulta e a análise de dados com base nesses relacionamentos. Um grafo de propriedades tem vértices que podem conter informações detalhadas sobre um assunto e bordas que denotam a relação entre os vértices. Os vértices e bordas podem ter atributos, chamados propriedades, com os quais estão associados.


<h3>GRPC</h3>
Para enviar esses dados via HTTP teria que gastar espaço com caracteres desnecessários, como colchetes e chaves.

````
const tarefa = {
  id:1,
  descricao: 'Lavar a louça'
  data: '12/03/2022'
  responsavel: 'Gabriel',
  feita: false
  
}
const listaDeTarefas = [tarefas]
````

````
syntax "proto3"

message Tarefa {
  int32 id = 1;
  string descricao = 2;
  string data = 3;
  string responsavel = 4;
  bool feito = 5;
}

message ListaDeTarefas {
  repeated Tarefa lista = 1;
 }
 ````
 
No protobuf por outro lado, chama-se cada tipo de dado de mensagem, e essa mesma lista seria definida rápidamente. Os números do lado direito são índices da mensagem, eles existem para dizer qual campo é o que
quando a mensagem for enviada, assim isso elimina a necessidade de codificar caracteres desnecessário, considerando-se que pode passar só um número para dizer qual é o próximo campo e o tipo desse campo. 

Se há dois sistemas,  sendo um cliente e um servidor, os dois lados vão precisar ter a mesma versão de um arquivo protobuf que termina com a extensão proto, quando se lê esse arquivo e se passa a configuração para o GRPC, ele automaticamente sabe qual o tipo de ação foi chamada e qual é a mensagem que está sendo recebida.
O protobuf não define só a mensagem, mas sim toda API, dessa forma, vai ter toda a comunicação do sistema definida nesse arquivo e vai ser com base nela que o GRPC vai trabalhar. 

Primeiro é necessário definir os outros tipos que vão existir na nossa API, além dos dois que a gente já criou, o tipo vazio que significa que o valor será em branco e o tipo tarefaID de que vai ser somente o ID de uma tarefa. Depois é necessário definir em tudo que o API (ou como o protobuf chama, o serviço vai ter).
````

syntax "proto3";

message Vazio {}

message TarefaID {
    int32 id =1;
}
````

Nessa API,pode ter três ações listar todas as tarefas, adicionar uma tarefa e marca a tarefa como concluída. Para isso, será necessário criar a representação do serviço que vai conter várias chamadas RPC. Cada chamada RPC só pode conter uma mensagem de entrada e uma mensagem de saída, por isso que teve que definir tipos diferentes para tarefa e o ID da tarefa, se a
juntar tudo que se tem, têm-se o arquivo de definição completo da API. 

```
syntax "proto3";

service APIListaDeTarefas {
  rpc ListarTodas (Vazio) returns (ListadeTarefas);
  rpc AdicionarTarefa (Tarefa) returns (Tarefa);
  rpc MarcarComoConcluida (TarefaID) returns (Tarefa);
}
````
