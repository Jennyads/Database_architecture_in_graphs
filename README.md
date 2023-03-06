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





