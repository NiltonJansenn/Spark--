# Spark--
Algumas Questões e Respostas sobre Spark 


Nilton Jansenn  - perguntas e respostas para engenheiro de dados  17/02/2019


Qual o objetivo do comando cache em Spark?

O Objetivo do uso do comando cache e que ele ajuda a melhorar a eficiência do código, pois permite que resultados intermediários de operações lazy sejam armazenados e reutilizados repetidamente.


O mesmo código implementado em Spark é normalmente mais rápido que a implementação equivalente em MapReduce. Por quê?

No MapReduce roda vários Jobs de MapReduce em serie no HD ao invés de um único, o que provoca lentidão. Já o Spark passa os resultados das operações a serem executadas em um mesmo conjunto de dados em cache o que reduz muito a leitura e escrita em HD. 
No que tange a JVM o Spark inicia um thread que é um muito rápido, já o MapReduce inicializa um novo thread na JVM a cada job o que torna o processo mais lento.  


Qual é a função do SparkContext ?

O SparkContext executa a função principal do usuário e as várias operações paralelas nos nós de trabalho. Em seguida, o SparkContext coleta os resultados das operações. Os nós de trabalho leem e gravam dados do Sistema de Arquivos Distribuído do Hadoop. Os nós de trabalho também armazenam dados transformados na memória em cache como RDDs (Conjuntos de Dados Distribuído Resiliente).
O SparkContext conecta-se ao Spark mestre e é responsável por converter um aplicativo em um DAG (grafo direcionado) das tarefas individuais, que por sua vez é executado em um processo de executor nos nós de trabalho. Cada aplicativo obtém seus próprios processos de executor, que se mantêm pela duração do aplicativo e executa tarefas em vários threads.


Explique com suas palavras o que é Resilient Distributed Datasets (RDD).

Um RDD (Resilient Distributed Dataset) é a primeira abstração fornecida pelo Spark para a manipulação de dados. É uma representação de um dado distribuído pelos nodos do cluster que pode ser operado em paralelo. RDDs podem ser criados a partir de arquivos no HDFS (Hadoop Distributed File System) ou de coleções da linguagem Scala. são uma estrutura de dados fundamental do Spark. É uma coleção distribuída imutável de objetos. Cada conjunto de dados no RDD é dividido em partições lógicas, que podem ser calculadas em diferentes nós do cluster. Os RDDs podem conter alguns  tipos de objetos com exemplos o  Python, Java ou Scala, incluindo classes definidas pelo usuário.

Formalmente, um RDD é uma coleção de registros particionados somente para leitura. Os RDDs podem ser criados por meio de operações determinísticas em dados sobre armazenamento estável ou outros RDDs. O RDD é uma coleção tolerante a falhas de elementos que podem ser operados em paralelo , Há duas maneiras de criar RDDs - paralelizar uma coleção existente em seu programa de driver ou fazer referência a um conjunto de dados em um sistema de armazenamento externo, como um sistema de arquivos compartilhado, HDFS, HBase ou qualquer fonte de dados que ofereça um formato de entrada Hadoop .O Spark utiliza o conceito de RDD para obter operações MapReduce mais rápidas e eficientes.

GroupByKey é menos eficiente que reduceByKey em grandes dataset. Por quê?

Quando um groupByKey é chamado em um par RDD, os dados nas partições são embaralhados na rede para formar uma chave e uma lista de valores. Esta é uma operação dispendiosa, particularmente quando se trabalha em um grande conjunto de dados. Isso também pode causar problemas quando a lista de valores combinados é enorme para ocupar em uma partição. Nesse caso, um derramamento de disco ocorrerá. groupByKey () opera em Pair RDDs e é usado para agrupar todos os valores relacionados a uma determinada chave.groupByKey () sempre resulta em RDDs particionados por hash
reduceByKey (function) - Quando chamado em um conjunto de dados de pares (K, V), retorna um conjunto de dados de pares (K, V) onde os valores de cada chave são agregados usando a função de redução fornecida. A função deve ser capaz de receber argumentos de algum tipo e retornar o mesmo tipo de dados de resultado. Como em groupByKey, o número de tarefas de redução é configurável por meio de um segundo argumento opcional.
Ao contrário de groupByKey, reduceByKey não mistura dados no início. Como sabe que a operação de redução pode ser aplicada na mesma partição primeiro, somente o resultado da função de redução é embaralhado na rede. Isso causa uma redução significativa no tráfego pela rede. O único problema é que os valores de cada chave devem ser do mesmo tipo de dados. Se forem tipos de dados diferentes, ele deve ser explicitamente convertido. Essa desvantagem pode ser resolvida usando o combineByKey.

Explique o que o código Scala abaixo faz.

Linha 1). val textFile = sc . textFile ( "hdfs://..." )

Linha 2). val counts = textFile . flatMap ( line => line . split ( " " ))

Linha 3).           . map ( word => ( word , 1 ))

Linha 4).           . reduceByKey ( _ + _ )

Linha 5). counts . saveAsTextFile ( "hdfs://..." )

respostas : 

Linha 1) um arquivo-texto é lido;

Linha 2) cada linha é dividida em uma série de palavras e as sequencias correspondentes a cada linha são transformadas em uma única coleção de palavras;

Linha 3) palavra é então transformada em um mapeamento de chave/valor, com chave igual à própria palavra e valor 1;

Linha 4) esses valores são agregados por chave, através da operação de soma;

Linha 5) por último o RDD com a contagem de cada palavra é salvo em um arquivo texto.

