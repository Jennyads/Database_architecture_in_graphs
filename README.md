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

Quando se inicia um novo serviço de GRPC, trata-se da comunicação entre duas partes (servidor e cliente).Durante a inicialização, é necessário passar o caminho desse arquivo protobuf para o GRPC que por sua vez vai ler o arquivo, fazer um "parsing", ou seja, decodificar o arquivo para poder entender de dentro e trazer a definição da API como um objeto que pode ser lido pelo motor de execução dele. 
```
//javascript, grpc tem uma biblioteca proprio
const grpc = require('grpc')
const path = require('path')
const DefinicaoTarefas = grpc.load(path.resolve('./tarefas.proto'))
```

o GRPC leva como parâmetro um objeto que vai ter uma lista das implementações reais de cada ação que foi definida no arquivo protobuf. Então é necessário ter que ter três funções, uma para listar, marcar como concluído, adicionar tarefa.

```
const grpc = require('grpc')
const path = require('path')
const DefinicaoTarefas = grpc.load(path.resolve('./tarefas.proto'))

const server = new grpc.Server()
server.addService(
  DefinicaoTarefas.APIListaDeTarefas.service,
  { /* implementação */ }
}
```
Listar todas as tarefas: 
Cria-se uma lista de tarefas que já vai ter uma tarefa dentro, assim cria-se uma função com o mesmo nome
do RPC no arquivo protobuf, a função de GRPC leva dois parâmetros, sendo o primeiro requisição feita pelo cliente, por meio dela vai buscar todos os parâmetros que foram passados para o servidor, nesse caso o parâmetro é vazio. O segundo parâmetro é um callback que vai responder para o cliente quando for chamado, o callback também uma função, e o primeiro parâmetro dela é a mensagem de erro se passar alguma coisa que não seja nula, o GRPC vai retornar um erro para o cliente. já o segundo parâmetro é odado que vamos retornar que precisa ser do mesmo tipo que foi definido lá no arquivo protobuf. Depois é só adicionar 
adicionar essa função no objeto de implementação GRPC, fazendo o mesmo para as outras duas funções.

```
const tarefas = [
  {
    id:1,
    descricao: 'Lavar a louça'
    data: '12/03/2022'
    responsavel: 'Gabriel',
    feita: false
   }
]
function ListaTodas(requisicao, callback) {
  return callback(null, tarefas)
}
````
Adicionando a  função no objeto de implentação no GRPC, fazendo o mesmo para as duas outras.

```
const grpc = require('grpc')
const path = require('path')
const DefinicaoTarefas = grpc.load(path.resolve('./tarefas.proto'))

const tarefas = [
  {
    id:1,
    descricao: 'Lavar a louça'
    data: '12/03/2022'
    responsavel: 'Gabriel',
    feita: false
   }
]

function ListaTodas(requisicao, callback) {
  return callback(null, tarefas)
}
const server = new grpc.Server()
server.addService(
  DefinicaoTarefas.APIListaDeTarefas.service,
  { ListarTodas }
}
```

Marcar como concluida:

````
function MarcarComoConcluida (requisicao, callback) {
  const {indice, tarefaConcluida} = tarefas.find((tarefa, indice) => {
    if(tarefa.id === requisicao.request.id) {
      return {
        indice,
        tarefa
       }
      }
    })
    if (!tarefaConcluida) {
      return callback(new Error('A tarefa não existe'), null)
     }
     
     tarefaConcluida.feito = true
     tarefas[indice] = tarefaConcluida
     return callback(null, tarefaConcluida)
    }
    
````

