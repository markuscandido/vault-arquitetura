# 06_Cap_03_Mapeando_para_Bancos_de_Dados_Relacionais

Capituto 3 e MAPEANDO PARA BANCOS DE DADOS RELACIONAIS

55

problemas, mas ainda lhe deixa com o Modelo de Domínio (126) acoplado ao esquema

do banco de dados. Como resultado, há algumas transformações dos campos do Gateway (436) para os campos dos objetos do domínio, transformações estas que com-

plicam seus objetos de domínio.

Um caminho melhor é isolar completamente o Modelo de Domínio (126) do ban-

co de dados, tornando sua camada de indireção inteiramente responsável pelo ma-

peamento entre os objetos do domínio e as tabelas do banco de dados. Este Mapeador

de Dados (170) (veja a Figura 3.4) lida com toda a carga e armazenamento entre o ban-

co de dados e o Modelo do Domínio (126) e permite a ambos variar independentemen-

te. É a mais complicada das arquiteturas de mapeamento de banco de dados, mas

seu benefício é o completo isolamento das duas camadas.

Não recomendo o uso de um Gateway (436) como mecanismo principal de per-

sistência para um Modelo de Domínio (126). Se a lógica do domínio for simples e você

tiver uma correspondência próxima entre classes e tabelas, o Registro Ativo (165) é a
solução mais simples; se você tiver algo mais complicado, é do Mapeador de Dados
(170) que você precisa.
Esses padrões não são mutuamente excludentes. Em boa parte desta discussão

estamos pensando no mecanismo principal de persistência, isto é, o modo como vo-

cê salva os dados de algum tipo de modelo em memória para o banco de dados. Para tal, escolheremos um destes padrões. Você não irá querer misturá-los, porque isso
acaba ficando muito confuso. Entretanto, mesmo se estiver usando o Mapeador de Da-

dos (170) como mecanismo principal de persistência, você poderá usar um Gateway
(436) de dados para encapsular tabelas ou serviços que estejam sendo tratados como
interfaces externas.

Na minha discussão a respeito dessas idéias, tanto aqui quanto nos próprios pa-

drões, tendo a usar a palavra “tabela”. Todavia, a maioria dessas técnicas pode ser
aplicada igualmente em visões, consultas encapsuladas por meio de procedimentos
armazenados, e consultas dinâmicas freqiientemente usadas. Infelizmente, não há

um termo amplamente usado para tabela / visão /consulta /procedimento armazenado, de modo que uso “tabela” porque representa uma estrutura de dados tabular.
Geralmente penso em visões como tabelas virtuais, que, é claro, é como também SQL

pensa nelas. A mesma sintaxe é usada para pesquisar visões e tabelas.

A atualização é obviamente mais complicada com visões e consultas, já que você

nem sempre pode atualizar uma visão diretamente, mas, em vez disso, tem que mani-

pular as tabelas que dão suporte a ela. Nesse caso, encapsular a visão /consulta com

um padrão apropriado é uma maneira muito boa de implementar a lógica de atualiza-

ção em apenas um lugar, o que torna o uso das visões mais simples e confiável.

Cliente
verificarcrédito
enviarContas

Mapeador de Cliente

criar

[S===="Tearegar

[== ===

salvar

Tabela Cliente

Figura 3.4

Um Mapeador de Dados (170) isola os objetos do dominio do banco de dados.

56

Parte | e As NARRATIVAS

Um dos problemas do uso de visões e consultas dessa forma é que ele pode levar a inconsistências que surpreendam os desenvolvedores que não entendem como

uma visão é formada. Eles podem executar atualizações em duas estruturas diferentes, ambas atualizando as mesmas tabelas de suporte em que a segunda atualização

sobrescreve uma atualização feita pela primeira. Estabelecendo que a lógica de atualização execute a validação apropriada, você não deveria obter dados inconsistentes,

mas pode surpreender seus desenvolvedores.

Também devo mencionar a maneira mais simples de realizar persistência mes-

mo com o mais complexo Modelo de Domínio (126). Nos primórdios dos objetos, mui-

tas pessoas perceberam que havia um “desacordo de impedância” fundamental entre objetos e relações. Assim, seguiu-se um grande esforço em bancos de dados orien-

tados a objetos, os quais essencialmente trouxeram o paradigma OO ao armazena-

mento em disco. Com um banco de dados OO, você não tem que se preocupar com
mapeamento. Você trabalha com uma grande estrutura de objetos interconectados, e
o banco de dados descobre quando mover objetos dos discos e para os discos. Além
disso, você pode usar transações para agrupar atualizações e permitir compartilha-

mento dos dados armazenados. Para os programadores isso parece uma quantidade
infinita de memória transacional que é suportada transparentemente pelo armazena-

mento em disco.
A maior vantagem dos bancos de dados OO é que eles aumentam a produtivi-

dade. Embora eu não esteja ciente de testes controlados, observações informais esti-

mam o esforço de mapeamento para um banco de dados relacional em cerca de um
terço do esforço de programação — um custo que continua durante a manutenção.

No entanto, a maior parte dos projetos não usa bancos de dados OO. A razão

principal contra eles é o risco. Bancos de dados relacionais são uma tecnologia bas-

tante conhecida e provada, suportada por vendedores grandes existentes já há bastante tempo. A SQL fornece uma interface relativamente padrão para todos os tipos
de ferramentas. (Se você estiver preocupado com desempenho, tudo que posso dizer

é que não tenho visto quaisquer dados conclusivos de comparações entre o desem-

penho de OO e o de sistemas relacionais.)
Mesmo se você não puder usar um banco de dados OO, deve considerar seria-

mente a compra de uma ferramenta de mapeamento O/R se tiver um Modelo de Dominio (116). Embora os padrões neste livro lhe digam muito sobre como construir um

Mapeador de Dados (170), esta ainda é uma tarefa complicada. Vendedores de ferra-

mentas passaram muitos anos trabalhando sobre esse problema, e ferramentas comerciais de mapeamento O/R

são muito mais sofisticadas do que qualquer coisa que

possa razoavelmente ser feita à mão. Embora as ferramentas não sejam baratas, você

tem que comparar seu preço com o custo considerável de você mesmo escrever e

manter tal camada.
Há ações a realizar para fornecer uma camada estilo banco de dados OO que

possa trabalhar com bancos de dados relacionais. A JDO é uma proposta para a solu-

ção deste problema no mundo Java, mas ainda é muito cedo para dizer se se sairá
bem. Ainda não tive experiência suficiente com ela para chegar a qualquer conclusão

para este livro.

Contudo, mesmo se você comprar uma ferramenta, é uma boa idéia ter ciência

destes padrões. Boas ferramentas O/R lhe dão muitas opções no mapeamento para

um banco de dados, e estes padrões irão lhe ajudar a entender quando usar as dife-

rentes opções. Não parta do princípio de que uma ferramenta acaba com todo o es-

forço. Ela o reduz consideravelmente, mas você descobrirá que usar e ajustar uma
ferramenta O/R demanda um pequeno, porém significativo, trabalho.

CapiTuLo 3 e MAPEANDO PARA BANCOS DE DADOS RELACIONAIS

57

O Problema Comportamental
Quando as pessoas falam sobre mapeamento O/R, normalmente enfocam os aspectos estruturais — como você relaciona tabelas e objetos. Entretanto, descobri que a
parte mais difícil do exercício são seus aspectos arquiteturais e comportamentais. Já
falei sobre as principais abordagens arquiteturais; o próximo passo é pensar no as-

pecto comportamental.
O problema comportamental é como fazer os diversos objetos carregarem e gra-

varem a si próprios no banco de dados. À primeira vista, isto não parece ser um gran-

de problema. Um objeto cliente pode ter métodos para carregar e gravar que executem
essa tarefa. Com certeza, com Registro Ativo (165), este é um caminho óbvio a tomar.
Se você carregar um punhado de objetos para a memória e modificá-los, você

tem que manter registrado quais modificou e se assegurar de gravá-los de volta no

banco de dados. Se você carregar apenas alguns objetos, isso é fácil.

À medida que

carrega mais e mais objetos, o trabalho aumenta, especialmente quando você cria al-

gumas linhas e modifica outras, já que precisará das chaves das linhas criadas antes
que possa modificar as linhas que se referem a elas. Este é um problema um pouco

traiçoeiro a ser resolvido.

Enquanto você lê e modifica objetos, tem que se assegurar de que o estado do

banco de dados com o qual está trabalhando permaneça consistente. Se você ler al-

guns objetos, é importante garantir que a leitura seja isolada, de modo que nenhum

outro processo altere qualquer um dos objetos que leu enquanto estiver trabalhando

com ele. De outra forma, você poderia ter dados inconsistentes e inválidos em seus

objetos. Esta é a questão da concorrência, que é um problema traiçoeiro a ser resolvido; falaremos sobre ele no Capítulo 5.
Um padrão essencial à solução de ambos os problemas é a Unidade de Trabalho

(187). Uma Unidade de Trabalho (187) mantém registro de todos os objetos lidos do

banco de dados, junto com todos os objetos modificados de alguma maneira. Ela
também trata a maneira como as atualizações são feitas no banco de dados. Em vez

do programador da aplicação chamar explicitamente métodos de gravação, ele man-

da a unidade de trabalho confirmar (commit). Essa unidade de trabalho então organi-

za em uma sequência todo o comportamento apropriado para o banco de dados, co-

locando todo o complexo processamento de confirmação em um local. A Unidade de
Trabalho (187) é um padrão essencial sempre que as interações comportamentais com

o banco de dados se tornarem difíceis de manejar.
Uma boa maneira de pensar na Unidade de Trabalho (187) é como um objeto que

age como o controlador do mapeamento do banco de dados. Sem uma Unidade de
Trabalho (187), normalmente a camada do domínio age como o controlador, decidin-

do quando ler e gravar no banco de dados. A Unidade de Trabalho (187) resulta da fa-

toração do comportamento do controlador de mapeamento do banco de dados em

seu próprio objeto.

À medida que você carrega objetos, tem que ser cuidadoso para não carregar o

mesmo objeto duas vezes. Se você fizer isso, terá dois objetos na memória que corres-

pondem a uma única linha do banco de dados. Atualize ambos, e tudo fica muito
confuso. Para lidar com isso, você precisa manter um registro de cada linha que lêem

um Mapa de Identidade (196). Cada vez que você lê algum dado, primeiro deve verifi-

car o Mapa de Identidade (196) para se assegurar de que já não o tenha lido. Se o dado

já estiver carregado, você pode retornar uma segunda referência para ele. Dessa ma-

neira, quaisquer atualizações serão apropriadamente coordenadas. Como benefício,

58

Parte | e As NARRATIVAS

você também pode conseguir evitar uma chamada ao banco de dadosjá que o Mapa

de Identidade (196) também funciona como um cache para o banco de dados. Não esqueça, entretanto, que o propósito principal de um Mapa de Identidade (196) é manter
as identidades corretas, e não aumentar o desempenho.

Se você estiver usando um Modelo de Domínio (126), normalmente irá arranjar as

coisas de modo que objetos conectados sejam carregados juntos de tal maneira que a
leitura de um objeto Pedido carrega também seu objeto Cliente associado. Entretan-

to, com muitos objetos conectados, qualquer leitura de um objeto pode trazer um
enorme grafo de objetos do banco de dados. Para evitar tais ineficiências, você precisa reduzir o que traz de volta, mas manter a porta aberta para pegar mais dados se
precisar deles mais tarde. A Carga Tardia (200) se baseia na existência de um um reci-

piente para uma referência a um objeto. Há diversas variações sobre esse tema, mas

todas elas modificam a referência ao objeto de modo que, em vez de apontar para o

objeto real, elas referenciam o recipiente. Apenas se você tentar seguir, a conexão fa-

rá com que o objeto real seja trazido do banco de dados. Usando Carga Tardia (200)

em pontos convenientes, você pode trazer do banco de dados apenas o suficiente em
cada chamada.

Lendo Dados
Ao ler dados, gosto de pensar nos métodos como buscadores que encapsulam comandos SQL select com uma interface estruturada na forma de um método. Assim,

você poderia ter métodos como buscar(id) ou buscarPorCliente(cliente). Esses métodos

claramente podem se tornar muito difíceis de lidar se você tiver 23 cláusulas diferen-

tes no comando Select; mas felizmente isso é raro.

O lugar onde você coloca seus métodos de busca depende do padrão de inter-

face usado. Se suas classes de interação com o banco de dados forem baseadas em ta-

bela — ou seja, você tem uma instância da classe por tabela no banco de dados — então

pode combinar os métodos de busca com as inserções e atualizações. Se suas classes
de interação forem baseadas em linhas — ou seja, você tem uma classe de interação

por linha no banco de dados — isso não funciona.

Com classes baseadas em linhas você pode tornar estáticas as operações de bus-

ca, mas fazer isso impedirá que você torne as operações de banco de dados substituí-

veis. Isso significa que você não pode, com o propósito de teste, substituir o banco de

dados por um Stub de Serviço (469). Para evitar esse problema a melhor abordagem é

ter objetos de busca separados. Cada classe de busca tem muitos métodos que encap-

sulam uma consulta SQL. Quando você executa a consulta, o objeto de busca retorna

uma coleção dos objetos apropriados baseados em linhas.

Algo a ser observado em métodos de busca é que eles trabalham sobre o estado

do banco de dados, não sobre o estado do objeto. Se você executar uma consulta no

banco de dados para encontrar todas as pessoas em um clube, lembre-se de que
quaisquer objetos pessoa que você tiver acrescentado ao clube na memória não serão
pegos pela consulta. Como resultado disso, normalmente é sensato executar as consultas no início.

Ao ler dados, questões relativas a desempenho podem muitas vezes surgir. Is-

so leva a algumas regras básicas.

Tente trazer diversas linhas de uma vez. De modo especial, nunca execute con-

sultas repetidas sobre a mesma tabela para obter diversas linhas. Quase sempre é

Capituto 3 e MAPEANDO PARA BANCOS DE DADOS RELACIONAIS

59

melhor pegar dados demais do que de menos (embora você tenha que ser cuidado-

so em relação ao bloqueio de linhas demais com controle de concorrência pessimista). Assim, considere uma situação em que você precisa obter 50 pessoas que pode

identificar por uma chave primária no seu modelo de domínio, mas só pode cons-

truir uma pesquisa que obtenha 200 pessoas, sobre a qual irá executar alguma lógica
adicional para isolar as 50 de que você precisa. Normalmente, é melhor usar uma

consulta que traga linhas desnecessárias do que realizar 50 consultas individuais.
Outra forma de evitar ir ao banco de dados mais do que uma vez é usar junções
de modo que você possa trazer diversas tabelas com uma única consulta. O conjun-

to de registros resultante parece estranho, mas pode realmente acelerar as coisas.
Neste caso, você pode ter um Gateway (436) que tenha dados oriundos da junção de

diversas tabelas ou um Mapeador de Dados (170) que carregue diversos objetos de domínio com uma única chamada.

Entretanto, se estiver usando junções, tenha em mente que bancos de dados
são otimizados para lidar com até três ou quatro junções por consulta. Acima dis-

so, o desempenho sofre, embora você possa restaurar uma boa parte dele com vi-

sões em cache.

Muitas otimizações são possíveis no banco de dados. Elas envolvem agrupar

dados freqiientemente referenciados, o uso cuidadoso de índices e a habilidade do
banco de dados de usar cache em memória. Isso está fora do escopo deste livro, mas

dentro do escopo de um bom DBA.

Em todos os casos, você deve moldar sua aplicação com seu banco de dados e

dados específicos. Regras gerais podem guiar seu pensamento, mas suas circunstân-

cias particulares irão sempre ter suas próprias variações. Sistemas de bancos de da-

dos e servidores de aplicação frequentemente têm esquemas de cache sofisticados, e
não há como eu predizer o que acontecerá em sua aplicação. Para cada regra que uso,

tenho visto exceções surpreendentes, então reserve tempo para testes e ajustes de de-

sempenho.

Padrões de Mapeamento

Estrutural

Quando as pessoas falam em mapeamento objeto-relacional, na maioria das vezes se

referem aos padrões de mapeamento estruturais, os quais você usa ao efetuar o mapeamento entre objetos na memória e tabelas do banco de dados. Estes padrões geralmente não são relevantes para o Gateway de Tabelas de Dados (151), mas você pode

usar alguns deles se usar o Gateway de Linhas de Dados (158) ou o Registro Ativo (165).

Você provavelmente precisará usar todos eles para o Mapeador de Dados (170).
Mapeando

Relacionamentos

A questão central aqui é o modo diferente como objetos e relações lidam com associa-

ções, o que leva a dois problemas. Primeiro, há uma diferença na representação. Os

objetos lidam com associações armazenando referências que são mantidas em tempo

de execução ou por ambientes gerenciados por memória ou por endereços de memória. Os bancos de dados relacionais lidam com associações, criando uma chave para

outra tabela. Segundo, os objetos podem usar facilmente coleções para lidar com refe-

rências múltiplas a partir de um único campo, enquanto que a normalização força to-

das as associações da relação a terem um único valor. Isso leva a inversões da estrutu-

60

Parte | e As NARRATIVAS

ra de dados entre objetos e tabelas. Um objeto pedido evidentemente tem uma cole-

ção de objetos Item de pedido que não precisam de qualquer referência de volta ao pe-

dido. Entretanto, a estrutura da tabela é diferente — o Item deve incluir uma chave es-

trangeira para o pedido, já que o pedido não pode ter um campo multivalorado.

A maneira de lidar com o problema da representação é manter a identidade re-

lacional de cada objeto como um Campo Identidade (226) no objeto e buscar estes va-

lores para realizar o mapeamento nos dois sentidos entre as referências dos objetos e

as chaves relacionais. É um processo tedioso, mas não tão difícil, uma vez que você

compreenda a técnica básica. Quando você lê objetos do disco, usa um Mapa de Iden-

tidade (196) como tabela de busca das chaves relacionais para objetos. Cada vez que

você se deparar com uma chave estrangeira na tabela, use Mapeamento de Chave Es-

trangeira (233) (veja a Figura 3.5) para ligar a referência interobjetos apropriada. Se

você não tiver a chave no Mapa de Identidade (196), você precisa ir ao banco de dados

para obtê-la ou usar Carga Tardia (200). Cada vez que você grava um objeto, você o
faz em uma linha com a chave correta. Qualquer referência interobjetos é substituída
pelo campo ID do objeto alvo.
Estabelecidos esses fundamentos, a manipulação da coleção requer uma versão
mais complexa do Mapeamento de Chave Estrangeira (233) (veja a Figura 3.6). Se um

objeto tiver uma coleção, você precisará executar outra busca para encontrar todas as
linhas que referenciam o ID do objeto fonte (ou você pode agora evitar a consulta
com Carga Tardia (200)). Cada objeto que retorna é criado e acrescentado à coleção.
Salvar a coleção envolve salvar cada objeto nela e assegurar de que tenha uma chave
estrangeira para o objeto fonte. Isso se torna confuso, especialmente quando você
tem que detectar objetos adicionados ou removidos da coleção. Pode se tornar repe-

titivo quando você domina o processo, e é por isso que alguma forma de abordagem

baseada em metadados torna-se um procedimento óbvio para sistemas maiores (en-

trarei em mais detalhes sobre isso mais tarde). Se a coleção de objetos não for usada

fora do escopo do dono dessa coleção, você pode usar o Mapeamento Dependente (256)
para simplificar o mapeamento.

Um caso diferente ocorre com um relacionamento muitos-para-muitos, o qual

tem uma coleção em ambos as extremidades. Um exemplo é uma pessoa que tenha

Álbum

título:String

*

Artista

título: String

«tabela»

«tabela»

ID: int
titulo: varchar
IDdoArtista: int

ID: int
nome: varchar

Álbuns

Figura 3.5

1

Artistas

Use um Mapeamento de Chave Estrangeira (233) para mapear um campo

monovalorado.

Capituto 3 e MAPEANDO PARA BANCOS DE DADOS RELACIONAIS

Álbum

1

Figura 3.6

Faixa

título: String

título: String

«tabela»

«tabela»

ID: int
titulo: varchar

ID: int
IDdoÁlbum: int

Álbuns

61

Faixas

título: varchar

Use um Mapeamento de Chave Estrangeira (233) para mapear um campo

coleção.

muitas habilidades e cada habilidade conheça as pessoas que a possuem. Bancos de

dados relacionais não podem lidar com isso diretamente, de modo que você usa um

Mapeamento de Tabela de Associação (244) (veja a Figura 3.7) para criar uma nova tabe-

la relacional apenas para lidar com a associação muitos-para-muitos.

Ao trabalhar com coleções, um erro freqiiente é se basear na ordenação dentro

delas. Em linguagens OO, é comum o uso de coleções ordenadas, como listas e veto-

res — com certeza, isso frequentemente torna os testes mais fáceis. Entretanto, é mui-

to difícil manter uma coleção ordenada arbitrariamente quando salva em um banco

de dados relacional. Por este motivo, vale a pena considerar o uso de conjuntos de-

sordenados para armazenar coleções. Outra opção é escolher uma forma de ordena-

ção sempre que executar uma pesquisa em uma coleção, embora isso possa ser bastante custoso.

Empregado

«tabela»
Empregados
ID

Figura 3.7

Habilidade

«tabela»
habilidade-empregados
IDdoEmpregado

IDdaHabilidade

«tabela»
Habilidades
ID

Use um Mapeamento de Tabela de Associação (244) para mapear uma

associação muitos-para-muitos.

62

Parte | e As NARRATIVAS

Em alguns casos, a integridade referencial pode tornar as atualizações mais

complexas. Os sistemas modernos permitem que você protele a verificação da integridade referencial para o final da transação. Se isso for possível, não há motivo pa-

ra não fazê-lo. De outro modo, o banco de dados irá verificar a cada gravação. Nesse

caso, você tem que ser cuidadoso para realizar sua atualização na ordem correta. Co-

mo fazer isso está fora do escopo deste livro, mas outra técnica é realizar uma orde-

nação topológica de suas atualizações. Outra é codificar explicitamente quais tabelas
são escritas em qual ordem. Isso pode às vezes reduzir problemas de deadlock dentro
do banco de dados que, muito frequentemente, fazem com que as transações sejam

desfeitas (roll back).
Campo Identidade (226) é usado para referências interobjetos que viram chaves

estrangeiras, mas nem todos os relacionamentos de um objeto precisam ser persisti-

dos dessa forma. Pequenos Objetos Valor (453), como intervalos de datas e objetos do

tipo moeda claramente não devem ser representados como sua própria tabela no
banco de dados. Em vez disso, pegue todos os campos do Objeto Valor (453) e colo-

que-os dentro do objeto associado como um Valor Embutido (261). Já que os Objetos

Valor (486) têm semântica de valor, você pode facilmente criá-los cada vez que obti-

ver uma leitura, e não precisa se incomodar com um Mapa de Identidade (196). Gravá-

los também é fácil — simplesmente retire a referência para o objeto e jogue seus cam-

pos para a tabela proprietária.
Você pode fazer esse tipo de coisa em uma escala maior pegando um conjunto
de objetos e gravando-os em uma única coluna em uma tabela como um LOB Serializado (264). LOB quer dizer “Large OBject” (Objeto Grande), que pode ser binário

(BLOB) ou textual (CLOB — Character Large OBject). Serializar um grupo de objetos

como um documento XML é um caminho óbvio para uma estrutura hierárquica de

objetos. Dessa forma você pode pegar um grupo de pequenos objetos associados em
uma única leitura. Muitas vezes os bancos de dados têm desempenho pobre com objetos pequenos altamente interconectados — onde você gasta muito tempo fazendo
muitas chamadas pequenas a banco de dados. Estruturas hierárquicas como organogramas e contas de materiais são no que um LOB Serializado (264) pode economizar
muitas idas e vindas a um banco de dados.

O lado negativo é que a SQL não está ciente do que está acontecendo, de modo

que você não pode fazer consultas portáveis sobre a estrutura do banco de dados.

Mais uma vez, XML pode ser sua salvação aqui, permitindo a você embutir expressões de pesquisa XPath dentro de chamadas SQL, embora embutir não seja de modo
algum padrão no momento. Como resultado, LOB Serializado (264) é melhor usado
quando você não quiser pesquisar as partes da estrutura armazenada.

Geralmente um LOB Serializado (264) é melhor para um grupo relativamente
isolado de objetos que fazem parte de uma aplicação. Se você usá-lo demais, acaba
transformando seu banco de dados em pouco mais do que um sistema de arquivos
transacional.

Herança
Nas hierarquias acima, estou falando sobre hierarquias composicionais, como uma
árvore de partes, o que tradicionalmente não é o forte dos sistemas relacionais. Há

outro tipo de hierarquia que causa dores de cabeça relacionais: uma hierarquia de
classes associadas por herança. Já que não há um modo padrão de tratar herança em

SQL, novamente temos que executar um mapeamento. Para qualquer estrutura de

CapiTuto 3 e MAPEANDO PARA BANCOS DE DADOS RELACIONAIS

63

herança há basicamente três opções. Pode haver uma tabela para todas as classes na

hierarquia: Herança de Tabela Única (269) (veja a Figura 3.8); uma tabela para cada

classe concreta: Herança de Tabela Concreta (283) (veja Figura 3.9); ou uma tabela por
classe na hierarquia: Herança de Tabela de Classes (276) (veja a Figura 3.10).

Os compromissos são todos entre duplicação da estrutura de dados e velocidade de acesso. A Herança de Tabela de Classes (276) é o relacionamento mais simples en-

Jogador
nome

Jogador de Futebol

Jogador de Criquete|

7
E de rebatidas
média
É

clube

«tabela»
Jogadores
nome
clube

de rebatidas
média
média de boliche
tipo

Jogador de Boliche
média de boliche

Figura 3.8

A Herança de Tabela Única (269) usa uma única tabela para armazenar todas

as classes em uma hierarquia.

«tabela»
Jogadores de Futebol
nome
clube

Jogador
nome

Jogador de Futebol
clube

TAS

«tabela»
lJogadores de Criquete|
nome
média de rebatidas

Jogador de Boliche

«tabela»

Jogador de Criquete
média de rebatidas

média de boliche

Jogadores de Boliche

nome

média de rebatidas
média de boliche

Figura 3.9
A Herança de Tabela Concreta (283) usa uma única tabela para armazenar
cada classe concreta em uma hierarquia.

64

Parte | é As NARRATIVAS

«tabela»
Jogadores de Futebol

Jogador
nome

Jogador de Futebol
clube

x

clube

Jogador de Criquete|
média de rebatidas
/\
Jogador de Boliche

«tabela»
Jogadores de Criquete
média de rebatidas
«tabela»
Jogadores de Boliche
média de boliche

«tabela»
Jogadores

média de boliche
nome
Figura 3.10

A Herança de Tabela de Classes (276) usa uma única tabela para cada classe

em uma hierarquia.

tre as classes e as tabelas, mas precisa de junções múltiplas para carregar um único
objeto, o que geralmente reduz o desempenho. A Herança de Tabela Concreta (283) evi-

ta as junções, permitindo que você pegue um único objeto de uma tabela, mas é sen-

sível a alterações. Com qualquer alteração em uma superclasse você tem que se lem-

brar de alterar todas as tabelas (e o código de mapeamento). Alterar a própria hierarquia pode causar alterações ainda maiores. Além disso, a falta de uma tabela da superclasse pode tornar o gerenciamento das chaves difícil e atrapalhar a integridade
referencial, embora reduza a disputa de bloqueios na tabela da superclasse. Em alguns bancos de dados, o maior problema da Herança de Tabela Única (269) é o desper-

dício de espaço, já que cada linha precisa ter colunas para todos os subtipos possiveis, e isso leva a colunas vazias. No entanto, muitos bancos de dados fazem um bom

trabalho ao comprimir o espaço desperdiçado em tabelas. Outro problema com a He-

rança de Tabela Única (269) é seu tamanho, tornando-a um gargalo para os acessos.
Sua grande vantagem é que ela coloca todas as coisas em um único lugar, o que tor-

na a modificação mais fácil e evita junções.

As três opções não são mutuamente exclusivas e, em uma hierarquia, você po-

de misturar padrões. Por exemplo, você poderia ter diversas classes lidas juntas com

Herança de Tabela Única (269) e usar Herança de Tabela de Classes (276) para alguns ca-

sos pouco comuns. É claro que misturar padrões acrescenta complexidade.
Não há um vencedor nítido aqui. Você deve levar em consideração suas próprias circunstâncias e preferências, como com todo o resto desses padrões. Minha
primeira escolha tende a ser a Herança de Tabela Única (269), já que é fácil de realizar e

resiliente a muitas refatorações. Tendo a usar as outras duas quando necessário para
ajudar a resolver os problemas inevitáveis de colunas irrelevantes e desperdiçadas.

Muitas vezes, o melhor é conversar com os DBAs. Eles fregientemente têm bons
conselhos sobre o tipo de acesso que faz mais sentido no banco de dados.

Capituto 3 e MAPEANDO PARA BANCOS DE DADOS RELACIONAIS

65

Todos os exemplos recém-descritos, e os padrões, usam herança única. Embora

herança múltipla esteja ficando menos em uso atualmente e a maioria das linguagens
esteja cada vez mais evitando-a, a questão ainda aparece em mapeamentos O/R

quando você usa interfaces, como em Java e .NET. Os padrões aqui não entram nesse

tópico especificamente, mas na essência você lida com herança múltipla usando variações do trio de padrões de herança. A Herança de Tabela Única (269) coloca todas as

superclasses e interfaces em uma grande tabela, a Herança de Tabela de Classes (276) cria
uma tabela separada para cada interface e superclasse, enquanto que a Herança de Ta-

bela Concreta (283) inclui todas as interfaces e superclasses em cada tabela concreta.

Construindo o Mapeamento
Quando mapeia para um banco de dados relacional, você encontra essencialmente

três situações:

© Você mesmo escolhe o esquema.

+ Você tem que mapear para um esquema preexistente, o qual não pode ser alterado.

* Você tem que mapear para um esquema preexistente, porém alterações nele
são negociáveis.

O caso mais simples ocorre quando você mesmo está criando o esquema e tem

complexidade de pequena a moderada na sua lógica de domínio, resultando em um

Roteiro de Transação (120) ou em Módulo Tabela (134). Nesse caso, você pode projetar as
tabelas em torno dos dados usando técnicas clássicas de projeto de banco de dados.

Use um Gateway de Linha de Dados (158) ou um Gateway de Tabela de Dados (151) para
retirar toda SQL da lógica do domínio.

Se você estiver usando um Modelo de Domínio (126), deve tomar cuidado com

um projeto que se pareça com um projeto de banco de dados. Neste caso, construa

seu Modelo de Domínio (126) sem considerar o banco de dados, de modo que você

possa simplificar da melhor forma a lógica do domínio. Trate o projeto do banco de
dados como uma maneira de persistir os dados dos objetos. O Mapeador de Dados
(170) lhe dá a maior flexibilidade aqui, porém é mais complexo. Se o projeto de um

banco de dados isomórfico ao Modelo de Domínio (126) fizer sentido, você pode considerar um Registro Ativo (165) em vez disso.

Embora criar primeiramente o modelo seja uma forma razoável de pensar nele, o

conselho só se aplica dentro de ciclos interativos pequenos. Passar seis meses criando
um Modelo de Domínio (126) sem bancos de dados e então decidir persisti-lo assim que

tiver terminado é altamente arriscado. O perigo é que o projeto resultante tenha pro-

blemas de desempenho fraco que demandem refatoração demais para serem conserta-

dos. Em vez disso, construa o banco de dados a cada iteração, em não mais de seis se-

manas de duração, preferencialmente menos. Dessa forma, você terá retorno rápido e

contínuo sobre como suas interações de banco de dados funcionam na prática. Dentro

desta tarefa particular, você deve pensar no Modelo de Domínio (126) primeiro, mas integre cada parte do Modelo de Domínio (126) no banco de dados à medida que avança.

Quando o esquema já está lá, suas escolhas são similares, mas o processo é ligei-

ramente diferente. Com lógica de domínio simples você cria classes Gateway de Linhas

66

Parte | e As NARRATIVAS

de Dados (158) ou Gateway de Tabelas de Dados (151) que imitem o banco de dados, e coloca a camada da lógica do domínio sobre elas. Com lógica de domínio mais complexa você precisará de um Modelo de Domínio (126), o qual é altamente improvável de
casar com o desenho do banco de dados. Portanto, construa um Modelo de Domínio

(126) gradualmente e inclua Mapeadores de Dados (170) para persistir os dados no ban-

co de dados preexistente.

Mapeamento Duplo

Ocasionalmente me deparo com situações em que o mesmo tipo de dados precisa ser
trazido de mais de uma fonte. Pode haver múltiplos bancos de dados com os mes-

mos dados, porém com pequenas diferenças no esquema devido a alguma reutiliza-

ção feita com “copiar e colar”. (Nesta situação, o tamanho do aborrecimento é inversamente proporcional ao tamanho da diferença.) Outra possibilidade é usar mecanis-

mos diferentes, armazenando os dados, às vezes, em um banco de dados e, às vezes,

em mensagens. Você pode querer trazer dados similares de uma combinação de
mensagens XML, transações CICS e tabelas relacionais.

A opção mais simples é ter diversas camadas de mapeamento, uma para cada

fonte de dados. Entretanto, se os dados forem muito semelhantes, isso pode levar a

muita duplicação. Nesse caso você poderia considerar um mapeamento em dois pas-

sos. O primeiro passo converte os dados do esquema em memória para um esquema
lógico de armazenamento de dados. O esquema lógico de armazenamento de dados
é projetado para maximizar as similaridades dos formatos das fontes de dados. O segundo passo mapeia do esquema lógico de armazenamento de dados para o esquema

de armazenamento de dados físico real. Este segundo passo contém as diferenças.

O passo extra só vale a pena quando você tem muitas similaridades, de modo

que você somente deve usá-lo quando você tem depósitos físicos de dados similares,

porém irritantemente diferentes. Trate o mapeamento do depósito lógico para o de-

pósito físico dos dados como um Gateway (436) e use qualquer uma das técnicas de
mapeamento para mapear da lógica da aplicação para o depósito lógico dos dados.

Usando Metadados
Neste livro, a maior parte dos meus exemplos usa código escrito à mão. Com mapea-

mento simples e repetitivo isso pode levar a código simples e repetitivo — e código repetitivo é um sinal de algo errado com o projeto. Há muito que você pode fazer fatorando comportamentos comuns com herança e delegação — práticas OO boas e honestas — mas também há uma abordagem mais sofisticada usando Mapeamento em
Metadados (295).

O Mapeamento em Metadados (295) é baseado na condensação do mapeamento em

um arquivo de metadados que detalha como as colunas no banco de dados são mapea-

das em campos nos objetos. O objetivo disto é que, assim que tenha os metadados, vo-

cê pode evitar o código repetitivo usando geração de código ou programação reflexiva.
Com uma pequena quantidade de metadados você obtém uma grande expressividade. Uma linha de metadados pode dizer algo como
<nomeCampo

= cliente

classeAlvo

= "Cliente",

colunaBD

= "IDCliente",

tabelaAlvo

limiteInferior = "1" limiteSuperior = "1" metodoGravacao = "carregarCliente"/>

= "clientes"

CapiTuLo 3 e MAPEANDO PARA BANCOS DE DADOS RELACIONAIS

67

A partir disso, você pode definir o código de leitura e gravação, gerar automa-

ticamente junções para este caso, executar toda SQL, forçar a multiplicidade do rela-

cionamento e até fazer coisas como computar ordens de gravação sob a presença da

integridade referencial. É por isso que ferramentas de mapeamento O/R
usar metadados.

tendem a

Quando usa Mapeamento em Metadados (295), você tem a fundamentação neces-

sária para criar consultas baseadas em objetos em memória. Um Objeto de Pesquisa
(304) permite-lhe criar suas consultas baseadas em objetos e dados em memória de
um modo que os desenvolvedores não precisem conhecer nem SQL nem os detalhes

do esquema relacional. O Objeto de Pesquisa (304) pode então usar o Mapeamento em
Metadados (295) para traduzir expressões baseadas em campos de objetos para a SQL
apropriada.

Leve isso adiante o suficiente e você pode formar um Repositório (309) que es-

conda amplamente o banco de dados da vista. Quaisquer consultas ao banco de dados podem ser feitas em um Repositório (309) pelos Objetos de Pesquisa (304), e os de-

senvolvedores não conseguem perceber se os objetos foram trazidos da memória ou
do banco de dados. O Repositório (309) funciona bem com sistemas de Modelo de Dominio (126) ricos.

Apesar das muitas vantagens dos metadados, neste livro enfoquei exemplos es-

critos à mão porque acredito que são mais fáceis de entender. Assim que você dominar os padrões e conseguir escrevê-los à mão para sua aplicação, poderá descobrir
como usar metadados para tornar as coisas mais simples.

Conexões de Bancos de Dados
A maior parte das interfaces de bancos de dados baseia-se em algum tipo de objeto

de conexão de banco de dados para atuar como um link entre o código da aplicação
eo banco de dados. Normalmente uma conexão deve ser aberta antes de você poder

executar comandos sobre o banco de dados. Em geral, você precisa de uma conexão

explícita para criar e executar um comando. Durante todo o tempo em que você exe-

cuta este comando a conexão deve estar aberta. Buscas retornam um Conjunto de Re-

gistros (473). Algumas interfaces fornecem Conjuntos de Registros (473) desconecta-

dos, os quais podem ser manipulados após a conexão ser fechada. Outras interfaces
fornecem apenas Conjuntos de Registros (473) conectados, o que significa que a cone-

xão deve permanecer aberta enquanto o Conjunto de Registros (473) é manipulado. Se

você estiver executando dentro de uma transação, normalmente ela está conectada

a uma conexão específica e esta deve permanecer aberta enquanto aquela estiver

acontecendo.

Em muitos ambientes, é custoso criar uma conexão, o que faz com que valha

apena criar um pool de conexões. Nesta situação os desenvolvedores solicitam uma

conexão do pool e a liberam quando tiverem terminado, em vez de criar e fechar a

conexão. Atualmente, a maioria das plataformas lhe dá esta possibilidade de pooling, de modo que raramente você mesmo terá que criá-lo. Se tiver, primeiro verifi-

que se o pooling vai realmente ajudar no desempenho. Cada vez mais os ambientes
tornam mais rápido criar uma nova conexão, de modo que não há necessidade de

você usar um pool.

Ambientes que fornecem pooling muitas vezes o colocam por trás de uma inter-

face que se parece com a criação de uma nova conexão. Dessa forma, você não sabe

68

Parte | e As NARRATIVAS

se está obtendo uma conexão nova ou uma alocada de um pool. Esta é uma coisa boa,

já que a escolha de usar um pool ou não está encapsulada. De maneira semelhante, fechar uma conexão pode não fechá-la realmente, mas apenas retorná-la ao pool para

que outra pessoa a use. Nesta discussão usarei “abrir” e “fechar”, que podem ser
substituídos por “obter” do pool e “liberar” de volta para o pool.

Custosas de criar ou não, as conexões precisam de gerenciamento. Já que elas
são recursos custosos de gerenciar, devem ser fechadas assim que você tiver terminado de usá-las. Além disso, se você estiver usando uma transação, normalmente pre-

cisa se assegurar de que cada comando dentro de uma transação específica use a
mesma conexão.

O conselho mais comum é obter uma conexão explicitamente, usando uma cha-

mada para um pool ou um gerente de conexão, e então fornecê-la para cada coman-

do de banco de dados que você quiser executar. Assim que você não precisar mais da

conexão, feche-a. Este conselho leva a algumas questões: assegurar-se de que você te-

nha a conexão e todos os locais onde precisar dela e garantir que não se esquecerá de
fechá-la ao final.
Para se assegurar de que você tenha uma conexão onde precisa, há duas escolhas. Uma é passar a conexão como um parâmetro explícito. O problema nisso é que,
por exemplo, a conexão é adicionada a todas as chamadas de método onde seu úni-

co propósito é ser passada para algum outro método cinco camadas abaixo da pilha

de chamadas. É claro que esta é a situação que demanda um Registro (448). Já que vo-

cê não quer diversas threads usando a mesma conexão, irá querer um Registro (448)
com escopo de thread.
Se você for esquecido como eu, o fechamento explícito não é uma boa idéia. É
muito fácil esquecer de fazê-lo quando deveria. Você também não pode fechar a co-

nexão com cada comando porque pode estar sendo executado dentro de uma transação, e o fechamento irá normalmente fazer com que a transação seja desfeita.
Como uma conexão, a memória é um recurso que precisa ser liberado quan-

do você não estiver usando. Ambientes modernos atualmente fornecem gerencia-

mento automático de memória e coleta de lixo (garbage collection), de modo que
uma maneira de se assegurar de que as conexões sejam fechadas é usar um cole-

tor de lixo. Nesta abordagem, a própria conexão ou um objeto que se refira a ela

fecha a conexão durante a coleta de lixo. O bom disso é que usa o mesmo esque-

ma de gerenciamento usado para a memória sendo dessa forma conveniente e familiar ao mesmo tempo. O problema é que o fechamento da conexão só acontece
quando o coletor realmente reivindica a memória, e isto pode ocorrer um pouco
depois do momento em que a conexão tiver perdido sua última referência. O re-

sultado é que conexões sem referência podem continuar existindo por um certo

tempo antes de serem fechadas. Se isso é um problema ou não depende muito do
seu ambiente específico.
No geral, não gosto de depender da coleta de lixo. Outros esquemas — até o fechamento explícito — são melhores. Ainda assim, a coleta de lixo é um bom backup no

caso do esquema normal falhar. Afinal, é melhor ter as conexões fechadas em algum
momento do que tê-las perdurando para sempre.
Já que as conexões são tão atreladas a transações, uma boa maneira de gerenciálas é vinculando-as a uma transação. Abra uma conexão quando você começar uma

transação e feche-a quando você confirmá-la ou desfazê-la. Faça com que a transação

saiba qual conexão está usando de modo que você possa ignorar a conexão comple-

tamente e lidar apenas com a transação. Já que a conclusão da transação tem um efei-

CapiTuLo 3 e MAPEANDO PARA BANCOS DE DADOS RELACIONAIS

69

to visível, é mais fácil lembrar de confirmá-la e localizá-la se você esquecer. Uma Unidade de Trabalho (184) se encaixa com perfeição para gerenciar tanto a transação quantoa conexão.

Se você fizer coisas fora de uma transação, como uma leitura de dados invariá-

veis, use uma conexão nova para cada comando. Poolings podem lidar com quaisquer questões relacionadas à criação de conexões de curta duração.

Se você estiver usando um Conjunto de Registros (473) desconectado, pode abrir
uma conexão para colocar os dados no conjunto de registros e fechá-la enquanto manipula os dados do Conjunto de Registros (473). A seguir, quando você já tiver feito o

que precisava com os dados, pode abrir uma nova conexão, e transação, para gravar

os dados. Se você fizer isto, precisará se preocupar com os dados alterados durante a
manipulação do Conjunto de Registros (473). Este é um tópico sobre o qual falarei junto com o controle de concorrência.
As especificidades do gerenciamento de conexão são características do seu soft-

ware de interação com o banco de dados, de modo que a estratégia que você usa é
muitas vezes ditada pelo seu ambiente.

Questões Diversas

Você perceberá que alguns dos códigos exemplo usam declarações select na forma

select * from, enquanto que outros usam colunas com nomes. Usar select * pode ter

sérios problemas em alguns drivers de bancos de dados, que podem falhar se uma

nova coluna for acrescentada ou se uma coluna for reordenada. Embora ambientes
mais modernos não sofram isso, não é sábio usar select * se você estiver usando indices posicionais para obter informações das colunas, pois o reordenamento de uma
coluna irá fazer com que o código falhe. Não há problema em usar índices de nomes
de colunas com um select * e de fato estes são mais claros de ler. Entretanto eles podem ser mais lentos, embora isso provavelmente não vá fazer muita diferença dado

o tempo da chamada SQL. Como sempre, meça para se certificar.
Se você usar índices de números de coluna, precisa se assegurar de que os aces-

sos ao conjunto resultante sejam muito próximos à definição da declaração SQL de

modo que não saiam de sincronia caso as colunas sejam reordenadas. Conseqiientemente, se você estiver usando Gateway de Tabelas de Dados (151), deve usar índices de

nomes de colunas, pois o conjunto resultante é usado por todo código que executa
uma operação de busca nesse gateway. O resultado disso é que geralmente vale a pena ter testes de caso simples de criação /leitura /remoção para cada estrutura de mapeamento de banco de dados que você usar. Isso ajudará a pegar casos quando sua
SQL sai de sincronia com seu código.

Sempre vale a pena o esforço de usar SQL estática que possa ser pré-compilada,

em vez de SQL dinâmica que tem que ser compilada a cada vez. A maioria das plataformas lhe dá um mecanismo para pré-compilar SQL. Uma boa regra é evitar o uso
de concatenação de strings para formar pesquisas SQL.

Muitos ambientes lhe dão a habilidade de fazer um lote com diversas pesquisas
SQL em uma única chamada ao banco de dados. Não fiz isso nestes exemplos, mas é
certamente uma tática que você deve usar no código de produção. Como você faz isso varia com a plataforma.
Para conexões nestes exemplos, apenas os invoco com uma chamada a um ob-

jeto “DB”, o qual é um Registro (448). Como você obtém uma conexão dependerá de

70

Parte

e As NARRATIVAS

seu ambiente, de modo que você irá substituir isto pelo que precisar fazer. Não envolvi transações em nenhum dos padrões além daqueles sobre concorrência. Mais
uma vez, você precisará combinar com o que o seu ambiente precisar.

Leitura Adicional
O mapeamento objeto-relacional é um fato para a maioria das pessoas, então não é

surpresa que haja muita coisa escrita sobre isso. A surpresa é que não há um único livro coerente, completo e atualizado, motivo pelo qual dedico tanto deste a este as-

sunto complicado, porém interessante.

O bom do mapeamento de banco de dados é que existem por aí muitas idéias

para serem roubadas. As fontes intelectuais mais vitimadas são [Brown and White-

nack], [Ambler], [Yoder] e [Keller and Coldewey]. Eu certamente o encorajaria a dar

uma boa olhada nesse material para suplementar os padrões deste livro.

CAPÍTULO

Apresentação Web

ma das maiores mudanças nas aplicações corporativas nos últimos anos foi o

surgimento das interfaces com o usuário baseadas em navegadores Web. Elas

trazem muitas vantagens: nenhum software cliente para instalar, uma aborda-

gem comum para a interface com o usuário e um acesso universal fácil. Além disso,
uma série de ambientes tornam fácil criar uma aplicação Web.

A preparação de uma aplicação Web começa com o próprio software servidor.
Normalmente ele tem algum tipo de arquivo de configuração que indica quais
URLs devem ser manipuladas por quais programas. Frequentemente, um único
servidor Web pode lidar com muitos tipos de programas. Estes programas podem
ser dinâmicos e podem ser acrescentados a um servidor colocando-os em um diretório apropriado. O trabalho do servidor Web é interpretar a URL de um pedido e
passar o controle para um programa servidor. Há duas formas principais de estruturar um programa em um servidor Web: como um roteiro ou como uma página

servidora (server page).
O roteiro é um programa, geralmente com funções ou métodos para tratar a

chamada HTTP. Exemplos incluem roteiros CGI e servlets Java. O texto do programa
pode fazer quase tudo o que um programa pode fazer, e o roteiro pode ser divido em
sub-rotinas, além de poder criar e usar outros serviços. Ele obtém dados da página

Web examinando a requisição HTTP, uma string. Em alguns ambientes isso é feito

por meio de uma busca usando expressões regulares na string de requisição — a faci-

lidade oferecida por Perl para fazer isso a torna uma escolha popular para roteiros
CGI. Outras plataformas, como servlets Java, fazem essa busca por programa, o que
permite ao programador acessar as informações da requisição por meio de uma interface com palavras-chave. Isso, no mínimo, significa um número menor de expres-

sões regulares para criar problemas. A saída do servidor Web é outra string — a res-

posta — na qual o roteiro pode escrever usando as operações comuns da linguagem
de gravação em streams.

72

Parte | e As NARRATIVAS

Escrever uma resposta HTML por meio de comandos de streams é incômodo
para os programadores e quase impossível para não-programadores que, no entan-

to, sentiriam-se confortáveis preparando páginas HTML. Isso levou à idéia de páginas servidoras, em que o programa é estruturado em torno da página-texto de re-

torno. Você escreve a página de retorno em HTML e insere scriplets no HTML para
executar código em determinados pontos. Exemplos desta abordagem incluem

PHP, ASP e JSP.

A abordagem da página servidora funciona bem quando há pouco processa-

mento da resposta, como em “Mostre-me os detalhes do álbum # 1234”. As coisas fi-

cam bem mais confusas quando você tem que tomar decisões baseadas na entrada,
como diferentes formatos de apresentação para álbuns clássicos ou de jazz.

Devido ao fato do estilo roteiro funcionar melhor para interpretar a requisição

eo estilo página servidora funcionar melhor para formatar a resposta, existe a opção

óbvia de usar um roteiro para interpretar a requisição e uma página servidora para

formatar a resposta. Esta separação é uma idéia antiga que apareceu primeiro em interfaces com o usuário usando o padrão Modelo Vista Controlador (315). Combine-a

com a noção essencial de que lógica não-relacionada à apresentação deve ser dela ex-

traída, e temos uma ótima base para os conceitos deste padrão.
O Modelo Vista Controlador (315) (veja a Figura 4.1) é um padrão amplamente re-

ferenciado, mas frequentemente mal-compreendido. Antes das aplicações Web entrarem em cena, a maior parte das demonstrações do Modelo Vista Controlador (315)
que examinei o compreendiam erradamente. Uma das principais razões para a confusão era o uso da palavra “controlador.” Controlador é usado em vários contextos
diferentes, e geralmente eu a encontrava usada de uma maneira diferente daquela
descrita no Modelo Vista Controlador (315). Por esse motivo, prefiro usar o termo controlador de entrada para o controlador no Modelo Vista Controlador (315).

Uma requisição chega a um controle de entrada, o qual extrai as informações

dessa requisição. Ele então encaminha a lógica de negócio para um objeto de domínio apropriado. Esse objeto conversa com a fonte de dados e realiza todas as tarefas

indicadas pela requisição, assim como reúne informações para a resposta. Quando ti-

ver terminado, ele devolve o controle para o controlador de entrada, o qual analisa

os resultados e decide qual vista é necessária para exibir a resposta. Ele então passa

o controlador, juntamente com os dados da resposta, para a vista. O repasse do con-

trolador para a vista, muitas vezes, não é uma chamada direta e, frequentemente, en-

volve a colocação dos dados em um local pré-combinado, alguma forma de objeto de

sessão HTTP compartilhado entre o controlador de entrada e a vista.

A primeira e mais importante razão para aplicar o Modelo Vista Controlador (315)
é assegurar que os modelos estejam completamente separados da apresentação Web.

Isso torna mais fácil modificar a apresentação, assim como acrescentar apresentações

adicionais mais tarde. Colocar o processamento em objetos Roteiro de Transação (120)

ou Modelo de Domínio (126) também tornará mais fácil testá-los. Isto é especialmente
importante se você estiver usando uma página servidora como sua vista.
Neste ponto, chegamos a um segundo uso da palavra “controlador”. Muitos
projetos de interface separam os objetos da apresentação dos do domínio com uma

camada intermediária de objetos do tipo Controlador de Aplicação (360). O objetivo de
um Controlador de Aplicação (360) é lidar com o fluxo de uma aplicação, decidindo

quais telas devem aparecer em qual ordem. Ele pode aparecer como parte da cama-

da de apresentação ou você pode pensar nele como uma camada separada que rea-

liza a mediação entre as camadas de apresentação e de domínio. Os Controladores de

73

Capituo 4 e APRESENTAÇÃO Wee

“ojapow ou epeaseq eysodsas en

sopep Ja]

1

i
1
1
1
i1

alJ> eJsIA

!
1

1

!

dny eysodsai

day oeSeroyjos

Lh eunbi

e anb WOd Ze} ogjua a oluItop Op erI69| e a1n22xa ojapoui o anb WOd ze} 'oeSisinhau e e)

1
i

Jojoutos

eysodsau 12106,

'

[|

!

fi

oe% jos ee :
dny ay oeSespi
[]
A

eqsin e eied sepuew

-21} JOpejo.UOD O “gam JOPINIBS UN WA SOjunf weyjegeu epeigua ap ajoguoo a ejsta “ojpouw op staded so OOD ap [e196 oesin eun

eysiq eun

OlUIWOP OP sopep Wo Jeu>

Ee

‘o1uswop op e2159] seynooxa

et

1
<
(ojapouu o ered yu!) esta e ered sopep Jeuopipe

(I
'

2072190 sunbje

LL

1

sopep 19]

1

jopejonuoswin

1

any osseios

Gam Jopinas um

74

Parte | e As NARRATIVAS

Aplicação (360) podem ser escritos para serem independentes de qualquer apresen-

tação em particular. Nesse caso, eles podem ser reutilizados entre as aplicações. Is-

so funciona bem se você tiver diferentes apresentações com os mesmos fluxo e na-

vegação básicos, embora, muitas vezes, seja melhor prover um fluxo diferente para

cada apresentação.

Nem todos os sistemas precisam de um Controlador de Aplicação (360). Eles são

úteis se o seu sistema tiver muita lógica relacionada à ordem das telas e à navegação

entre elas. Eles são úteis também se você não tiver um mapeamento simples entre
suas páginas e as ações no domínio. No entanto, se as telas puderem ser vistas em

praticamente qualquer ordem, você provavelmente terá pouca necessidade de um

Controlador de Aplicação (360). Um bom teste é este: se a máquina estiver no controle

do fluxo de telas, você precisa de um Controlador de Aplicações (360); se o usuário es-

tiver no controle, você não precisa.

Padrões de Vista
No lado da vista há três padrões a se considerar: Vista de Transformação (343), Vista

Modelo (333) e Vista em Duas Etapas (370). Esses, essencialmente, dão origem a duas

escolhas: usar Vista de Transformação (343) ou Vista Modelo (333) e, em ambos os casos,

se eles usam um único estágio ou uma Vista em Duas Etapas (370). Os padrões básicos

para Vista de Transformação (343) e Vista Modelo (333) são um único estágio. A Vista em

Duas Etapas (370) é uma variação que você pode aplicar em ambos os casos.

Começarei com a escolha entre a Vista de Transformação (343) e a Vista Modelo

(333). A Vista Modelo (333) lhe permite escrever a apresentação na estrutura da página e embutir marcadores na página para indicar onde é necessário o conteúdo dina-

mico. Algumas plataformas populares são baseadas neste padrão, muitas das quais
são tecnologias de páginas servidoras (ASP, JSP, PHP) que lhe permitem colocar uma
linguagem de programação completa na página. Isso claramente fornece muito po-

der e flexibilidade, mas, infelizmente, também leva a código bastante confuso e difí-

cil de manter. Como conseqiiéncia, se você usar tecnologia de páginas servidoras, de-

ve ser muito disciplinado para manter a lógica da programação fora da estrutura da

página, muitas vezes usando um objeto auxiliar.

A Vista de Transformação (343) usa um estilo de programação de transformação.

O exemplo habitual é XSLT. Isso pode ser muito eficaz se você estiver trabalhando

com dados do domínio que estejam no formato XML ou que possam ser facilmente

convertidos para XML. Um controle de entrada pega a folha de estilo XSLT apropriada e a aplica ao XML montado a partir do modelo.

Se você usa roteiros procedurais como sua vista, você pode escrever o código

em ambos os estilos, Vista de Transformação (343) ou Vista Modelo (333), ou mesmo em
alguma mistura interessante dos dois. Percebi que a maioria dos roteiros segue um
destes dois padrões como sua forma principal.

A segunda decisão é sobre usar um estágio único ou usar a Vista em Duas Etapas

(370). Uma vista de um único estágio, na maioria das vezes, tem um componente de

vista para cada tela na aplicação. A vista pega dados orientados ao domínio e os representa em HTML. Digo “na maioria das vezes” porque telas lógicas similares podem compartilhar visões. Mesmo assim, na maior parte do tempo, você pode pensar
em uma vista por tela.

CapituLo 4 e APRESENTAÇÃO Wes

Dados
do Cliente

Figura 4.2

Dados
do Pedido

Vista

do Cliente

do Pedido

iF

y

HTML

HTML

do Cliente

75

1

do Pedido

Uma vista de estágio único.

Uma vista em dois estágios (Figura 4.3) divide esse este processo em dois está-

gios, produzindo uma tela lógica a partir dos dados do domínio e então os representando em HTML. Há uma vista de primeiro estágio para cada tela mas apenas uma

vista de segundo estágio para a aplicação inteira.

A vantagem da Vista em Duas Etapas (370) é que ela coloca a decisão de qual
HTML usar em um único local. Isto facilita as alterações globais no código HTML,
uma vez que só há um objeto para alterar se quisermos alterar todas as telas no site.

É claro que você só obtém esta vantagem se sua apresentação lógica permanecer a
mesma, de modo

que isto funciona melhor com sites onde telas diferentes usam o

mesmo layout básico. Será difícil sugerir uma boa estrutura de tela lógica em sites
com design elaborado.
A Vista em Duas Etapas (370) funciona ainda melhor se você tiver uma aplicação

Web em que os serviços estejam sendo usados por múltiplos clientes, com diferentes

interfaces com o usuário, como diversas companhias aéreas usando o mesmo sistema

de reservas. Dentro dos limites da tela lógica, cada interface com o usuário pode ter
uma aparência diferente usando um segundo estágio diferente. De modo semelhante
você pode usar uma Vista em Duas Etapas (370) para lidar com diferentes dispositivos

de saída, com segundos estágios separados para um navegador Web normal e para
um palmtop. Mais uma vez, a limitação é que você tem de fazer os dois compartilharem uma mesma tela lógica, o que pode não ser possível se as interfaces de usuário forem muito diferentes, como em um navegador e um telefone celular.

Padrões de Controlador de Entrada
Há dois padrões para o controlador de entrada. O mais comum é existir um objeto
Controlador de entrada para cada página no seu site. No caso mais simples, este Con-

trolador de Página (318) pode ser a própria página servidora, combinando os papéis de

vista e controlador de entrada. Em muitas implementações separar o controlador de

76

Parte | é As NARRATIVAS

Dados do
Cliente

Dados do

Primeiro
Estágio
do Cliente

Primeiro
Estágio
do Pedido

i|

1º

Pedido

;
«cria»

Tela Lógica
do Cliente

«criam

Tela Lógica
do Pedido

Segundo
Estágio

Figura 4.3

Uma vista em dois estágios.

entrada em um objeto separado torna as coisas mais fáceis. O controlador de entrada

pode então criar modelos apropriados para executar o processamento e instanciar
uma vista para retornar o resultado. Frequentemente, você perceberá que não há exa-

tamente um relacionamento um-para-um entre Controladores de Página (318) e vistas.
Um pensamento mais preciso é que você tem um Controlador de Página (318) para cada ação, em que uma ação é um botão ou link. Na maior parte do tempo as ações cor-

respondem a páginas, mas ocasionalmente isso não acontece — tal como um link que
pode levar a diferentes páginas, dependendo de alguma condição.
Em qualquer controlador de entrada há duas responsabilidades — tratar a requisição HTTP e decidir o que fazer com ela — e muitas vezes faz sentido separá-las.
Uma página servidora pode tratar a requisição, delegando a um objeto auxiliar separado a decisão do que fazer com ela. O Controlador Frontal (328) aprofunda esta separação tendo apenas um objeto para lidar com todas as solicitações. Esse objeto único
interpreta a URL para descobrir que tipo de requisição ele está tratando e cria um objeto separado para processá-la. Dessa forma você pode centralizar todo o tratamento
HTTP em um único objeto, evitando a necessidade de reconfigurar o servidor Web
toda vez que alterar a estrutura de ação do site.

CapituLo 4 e APRESENTAÇÃO Wes

77

Leitura Adicional
A maior parte dos livros sobre tecnologias de servidores Web fornece um capítulo

ou dois sobre bons projetos de servidores, embora estes estejam frequentemente

imersos em descrições tecnológicas. Uma discussão excelente de projeto Web em

Java é o Capítulo 9 de [Brown et al.]. A melhor fonte para outros padrões é [Alur et
al.); a maior parte destes padrões pode ser usada fora do ambiente em situações
não Java. Roubei a terminologia de separar controladores de aplicação e de entra-

da de [Knight and Dai].

CapiTULO

Concorréncia

por Martin Fowler e David Rice

concorrência é um dos aspectos mais traiçoeiros do desenvolvimento de software. Sempre que você tiver múltiplos processos ou threads manipulando os
mesmos dados, depara-se com problemas de concorrência. Pensar a respeito
de concorrência já é difícil, uma vez que não é fácil enumerar os possíveis cenários
que podem lhe trazer problemas. Não importa o que você faça, sempre parece haver
algo que você esqueceu. Além disso, a concorrência é difícil de testar. Somos fãs de
um grande número de testes automatizados atuando como base para o desenvolvimento de software, mas é difícil obter testes que nos dêem a segurança de que preci-

samos em relação a problemas de concorrência.

Uma das grandes ironias do desenvolvimento de aplicações corporativas é que

poucos ramos do desenvolvimento de software estão usando a concorrência, e menos

ainda se preocupando com ela. O motivo pelo qual desenvolvedores deste tipo de

aplicação podem ter esta visão ingênua de concorrência são os gerenciadores de tran-

sação. As transações fornecem um framework que ajuda a evitar muitos dos aspectos

mais traiçoeiros da concorrência em uma aplicação corporativa. Enquanto você exe-

cutar toda sua manipulação de dados dentro de uma transação, nada de muito ruim

irá lhe acontecer.
Infelizmente, isso não significa que possamos ignorar completamente proble-

mas de concorrência, pela razão básica de que muitas das interações com um sistema

não podem ser colocadas dentro de uma única transação com banco de dados. Isso
nos força a gerenciar a concorrência em situações em que os dados atravessam várias
transações. O termo que usamos é concorrência offline, ou seja, controle de concor-

rência para dados que sejam manipulados durante múltiplas transações com bancos

de dados.
A segunda área em que a concorrência mostra seu lado negativo para os desenvolvedores corporativos são os servidores de aplicações — suportar múltiplas threads

em um servidor de aplicações. Gastamos muito menos tempo nisso, porque isso é

Capiruo 5 e CONCORRÊNCIA

79

muito mais simples de lidar. De fato, você pode usar plataformas de servidores que

tomem conta de grande parte disso para você.

Infelizmente, para entender essas questões, você precisa compreender pelo me-

nos alguns dos conceitos gerais de concorrência. Assim, começamos este capítulo
examinando tais questões. Não temos a pretensão de que este capítulo seja um tratamento geral da concorrência no desenvolvimento de software — para tanto precisaría-

mos de pelo menos um livro inteiro. O que este capítulo faz é introduzir questões de

concorrência para o desenvolvimento de aplicações corporativas. Assim que tivermos feito isso, introduziremos os padrões para lidar com concorrência offline e dire-

mos breves palavras sobre concorrência em servidores de aplicações.

Em grande parte deste capítulo ilustraremos as idéias com exemplos de uma
área com a qual esperamos que você esteja familiarizado — os sistemas de controle de

código fonte usados por equipes para coordenar alterações em um código base. Fa-

zemos isto porque eles são relativamente fáceis de entender e também bastante co-

nhecidos. Afinal, se você não estiver familiarizado com sistemas de controle de códi-

go fonte, você não deve estar desenvolvendo aplicações corporativas.
Problemas de Concorrência

Começaremos examinando os problemas básicos da concorrência. Chamamos de básicos porque são os problemas fundamentais que os sistemas de controle de concor-

rência devem tentar impedir. Eles não são os únicos problemas de concorrência, por-

que os mecanismos de controle, muitas vezes, criam um novo conjunto de problemas

nas suas soluções! Todavia, eles enfocam o ponto básico do controle de concorrência.

Atualizações perdidas são a idéia mais simples de entender. Suponha que Mar-

tin edite um arquivo para fazer algumas alterações no método verificarConcorréncia —

uma tarefa que leva alguns minutos. Enquanto ele está fazendo isso, David altera o

método atualizarParâmetroImportante no mesmo arquivo. David começa e termina sua al-

teração muito rapidamente, tão rapidamente que, embora tenha começado depois de

Martin, termina antes deste. Isso é desastroso. Quando Martin leu o arquivo ele não

incluía a atualização do David, de modo que quando Martin gravar o arquivo ele

gravará por cima da versão que David atualizou e essas atualizações serão perdidas
para sempre.

Uma leitura inconsistente ocorre quando você lê duas informações que são

corretas, mas não ao mesmo tempo. Suponha que Martin deseja saber quantas clas-

ses estão no pacote de concorrência, o qual contém dois subpacotes para bloqueio e
multifase. Martin olha o pacote de bloqueio e vê sete classes. Neste momento ele recebe uma ligação do Roy a respeito de alguma questão confusa. Enquanto Martin está falando ao telefone, David termina de consertar aquela falha ridícula no código de
bloqueio em quatro fases e adiciona duas classes ao pacote de bloqueio e três classes

às cinco que estavam no pacote de multifases. Após o término de seu telefonema,
Martin olha o pacote multifase para ver quantas classes há e vê oito, produzindo um

total geral de quinze.

Infelizmente, quinze classes nunca foi a resposta correta. A resposta correta era

doze antes da atualização do David e dezessete depois. Cada uma destas respostas

teria sido correta, mesmo se não fosse a resposta correta correntemente, mas quinze

nunca foi correto. Este problema é chamado de leitura inconsistente, porque os da-

dos que Martin leu estavam inconsistentes.

80

Parte | e As NARRATIVAS

Ambos os problemas causam uma falha de correção (ou segurança) e resultam

em comportamento incorreto que não teria ocorrido sem duas pessoas tentando trabalhar com os mesmos dados ao mesmo tempo. Entretanto, se a correção fosse a úni-

ca questão, esses problemas não seriam tão sérios. Afinal, podemos arranjar as coisas
de modo que apenas um de nós possa trabalhar nos dados de cada vez. Embora isso

ajude a correção, reduz a capacidade de fazer coisas concorrentemente. O problema
essencial de qualquer programação concorrente é que não basta se preocupar com a
correção, você também tem que se preocupar com a vivacidade do sistema: quanta
atividade concorrente pode ser executada. Muitas vezes as pessoas precisam sacrificar um pouco de correção para ganhar mais vivacidade, dependendo da seriedade e
da probabilidade de falhas e da necessidade de as pessoas trabalharem nos seus dados concorrentemente.
Estes não são todos os problemas que você tem com concorrência, mas consideramos estes como sendo os básicos. Para resolvê-los, usamos diversos mecanismos

de controle. Infelizmente, não existe comida de graça. As soluções introduzem seus

próprios problemas, embora estes problemas sejam menos sérios que os originais.

Ainda assim, isso nos traz uma questão importante: se você puder tolerar os problemas, pode evitar qualquer forma de controle de concorrência; é raro, mas ocasionalmente você encontra circunstâncias que o permitam.
Contextos de Execução

Toda vez que ocorre processamento em um sistema, ele ocorre em algum contexto e
normalmente em mais de um. Não há terminologia padrão para contextos de execu-

ção, de modo que aqui iremos definir as que estamos pressupondo neste livro.

Da perspectiva da interação com o mundo externo, dois contextos importantes
são a solicitação e a sessão. Uma solicitação corresponde a uma única chamada do

mundo externo sobre a qual o software trabalha e para a qual opcionalmente retorna
uma resposta. Durante uma solicitação, o processamento ocorre amplamente no lado do servidor e pressupõe-se que o cliente deva esperar por uma resposta. Alguns

protocolos permitem que o cliente interrompa uma solicitação antes de obter uma

resposta, mas isso é bastante raro. Com maior frequência um cliente pode enviar outra solicitação que pode interferir com a que foi recém-enviada. Assim, um cliente

pode solicitar permissão para colocar um pedido e então enviar uma solicitação separada para cancelar aquele pedido. Do ponto de vista do cliente, as duas solicita-

ções podem estar obviamente conectadas, mas, dependendo do seu protocolo, isso

pode não ser tão óbvio para o servidor.

Uma sessão é uma interação longa entre um cliente e um servidor. Pode consis-

tir em uma única solicitação, mas, mais comumente, ela consiste em uma série de so-

licitações que o usuário considera como uma seqiiéncia lógica consistente. É comum
uma sessão começar com um usuário se logando e executando algum trabalho que

pode envolver algumas pesquisas e uma ou mais transações de negócio (a serem dis-

cutidas em breve). No final da sessão, o usuário se desconecta ou pode simplesmente ir embora e assumir que o sistema interprete isso como uma desconexão.
O software servidor em uma aplicação corporativa vê tanto solicitações quanto
sessões de dois ângulos, como o servidor do cliente e como o cliente para outros sistemas. Assim, você muitas vezes verá diversas sessões: sessões HTTP

sessões de banco de dados com diversos bancos de dados.

do cliente e

Capiruo 5 e Concorrência

81

Dois termos muito importantes de sistemas operacionais são os processos e os

threads. O processo é um contexto de execução normalmente pesado que fornece bastante isolamento para os dados internos sobre os quais trabalha. A thread é um agen-

te ativo de menor peso que é configurado de modo que diversas threads possam

operar em um único processo. As pessoas gostam de tlireads porque elas suportam
múltiplas solicitações dentro de um único processo — o que é uma boa utilização de
recursos. As threads entretanto normalmente compartilham memória, e tal compartilhamento leva a problemas de concorrência. Alguns ambientes permitem que você
controle que dados uma thread pode acessar, permitindo que você tenha threads iso-

ladas que não compartilham memória.

A dificuldade com contextos de execução vem quando eles se não alinham tão

bem quanto gostaríamos. Na teoria, cada sessão teria um relacionamento exclusivo

com um processo por todo seu ciclo de vida. Uma vez que processos são apropriadamente isolados entre si, isso ajudaria a reduzir conflitos de concorrência. Atualmen-

te não conhecemos quaisquer ferramentas de servidor que lhe permitam trabalhar
assim. Uma alternativa próxima é começar um novo processo para cada solicitação,

que era o modo comum nos primeiros sistemas Web Perl. As pessoas tendem a evitar isso agora porque iniciar processos retém muitos recursos, mas é muito comum

que sistemas façam um processo lidar com apenas uma solicitação de cada vez — e is-

so pode evitar muitas dores de cabeça causadas por concorrência.
Quando você estiver lidando com bancos de dados, há outro contexto impor-

tante — uma transação. As transações juntam diversas solicitações que o cliente quer

que sejam tratadas como se fossem uma única. Elas podem ocorrer de uma aplicação
para o banco de dados (uma transação de sistema) ou do usuário para uma aplicação
(uma transação de negócio). Iremos nos aprofundar nesses termos mais adiante.

Isolamento e Imutabilidade
Os problemas de concorrência têm ocorrido há um certo tempo, e as pessoas que lidam com software têm encontrado diversas soluções. Para aplicações corporativas,
duas soluções são particularmente importantes: isolamento e imutabilidade.
Problemas de concorrência ocorrem quando mais de um agente ativo, como um

processo ou uma thread, tem acesso a uma mesma parte dos dados. Uma maneira de
lidar com isso é por meio do isolamento: particionar os dados de modo que qualquer
parte deles só possa ser acessada por um agente ativo. Os processos trabalham assim
na memória de sistemas operacionais: o sistema operacional aloca memória exclusi-

vamente para um único processo, e apenas esse processo pode ler ou gravar os da-

dos ligados a ele. De modo similar, você encontra bloqueios de arquivo em muitas

aplicações de produtividade populares. Se Martin abrir um arquivo, ninguém mais
pode abri-lo. Eles podem abrir uma cópia apenas para leitura do arquivo no estado

em que este se encontrava no momento em que Martin o abriu, mas não podem alterá-lo e não conseguem ver o arquivo entre suas alterações.

O isolamento é uma técnica vital porque reduz a chance de erros. Temos visto

com muita frequência pessoas se envolverem em problemas, porque usam uma téc-

nica que força todos a se preocuparem com concorrência durante o tempo todo. Com

isolamento, você deixa as coisas de um modo que os programas entram em uma área

isolada, dentro da qual você não tem que se preocupar com concorrência. Um bom

projeto de concorrência é, desta forma, encontrar modos de criar tais áreas e garantir
que tanta programação quanto possível seja feita em uma delas.

82

Parte | e As NARRATIVAS

Você só terá problemas de concorrência se os dados que estiver compartilhan-

do puderem ser modificados. Dessa forma, uma maneira de evitar conflitos de concorrência é reconhecer dados imutáveis. Obviamente, não podemos tornar todos os

dados imutáveis, já que o objetivo de muitos sistemas é a modificação de dados.

Contudo, identificando alguns dados como imutáveis, ou pelo menos imutáveis qua-

se todo o tempo, podemos relaxar nossas preocupações com concorrência em relação
a eles e compartilhá-los amplamente. Outra opção é separar aplicações que apenas
lêem dados e fazê-las usar fontes de dados copiados, nas quais podemos relaxar todos os controles de concorrência.

Controles de Concorrência Otimista e Pessimista
O que acontece quando temos dados mutáveis que não podemos isolar? De um mo-

do geral, há duas formas de controle de concorrência que podemos usar: otimista e
pessimista.
Suponhamos que Martin e David queiram editar o arquivo Clientes ao mesmo

tempo. Com bloqueio otimista, ambos podem fazer uma cópia do arquivo e editá-la

livremente. Se David for o primeiro a terminar, ele pode gravar seu trabalho sem problema. O controle de concorrência entra quando Martin tentar perpetrar suas alterações. Nesse momento, o sistema de controle de código fonte detecta um conflito entre as alterações de Martin e as de David. A confirmação de Martin é rejeitada e é responsabilidade dele descobrir como lidar com a situação. Com bloqueio pessimista,
qualquer pessoa que pegue o arquivo antes evita que outra possa editá-lo. Dessa maneira, se Martin for o primeiro a pegar o arquivo, David não poderá trabalhar com

esse arquivo até que Martin tenha terminado e perpetrado suas alterações.
Uma boa maneira de pensar sobre isso é que um bloqueio otimista se preocupa

com a detecção de conflitos enquanto que um bloqueio pessimista se preocupa com

a prevenção de conflitos. Os sistemas de controle de código fonte podem usar ambos
os tipos, embora atualmente mais desenvolvedores de código fonte prefiram trabalhar com bloqueios otimistas. (Há um argumento razoável, segundo o qual o bloqueio otimista não é realmente um bloqueio, mas consideramos a terminologia bas-

tante conveniente e difundida demais para ser ignorada.)

Ambas as abordagens têm prós e contras. O problema com o bloqueio pessimista é que ele diminui a concorrência. Enquanto Martin está trabalhando em um arqui-

vo ele o bloqueia, de modo que as outras pessoas têm de esperar. Se você já tiver tra-

balhado com sistemas de controle de código fonte pessimistas, sabe o quão frustrante isso pode ser. Com dados corporativos, é freqiientemente, pior porque, se alguém
estiver editando dados, ninguém mais pode lê-los, muito menos editá-los.

Os bloqueios otimistas permitem às pessoas fazerem mais progresso, porque o

bloqueio só acontece durante a confirmação. O problema com eles é o que acontece

quando você tem um conflito. Basicamente, todas as pessoas após a confirmação do

David têm que verificar a versão do arquivo que David gravou, descobrir como juntar

suas alterações com as do David e então gravar a nova versão. Com código fonte isso
não é muito difícil. De fato, em muitos casos o sistema de controle de código fonte pode fazer a junção automaticamente para você e, mesmo quando ele não puder fazê-lo,

ferramentas podem tornar muito mais fácil ver as diferenças. Contudo, dados de negó-

cio são normalmente difíceis demais de sofrerem junção automaticamente, de modo

que frequentemente o que você poderá fazer é jogar tudo fora e começar de novo.

Capiruo 5 e CONCORRÊNCIA

83

A essência da escolha entre bloqueios otimista e pessimista é a frequência e se-

veridade dos conflitos. Se estes forem suficientemente raros ou se as consequências
não forem graves, você deve normalmente escolher bloqueios otimistas porque eles

lhe dão maior concorrência e são geralmente mais fáceis de implementar. Entretan-

to, se os resultados de um conflito forem dolorosos para os usuários, você precisará

usar uma técnica de bloqueio pessimista.
Nenhuma dessas abordagens está livre de problemas. Usando-as, você pode facilmente introduzir problemas que causem quase tanto prejuízo quanto os problemas básicos de concorrência que você está tentando resolver. Deixaremos uma discussão detalhada dessas ramificações para um livro apropriado sobre concorrência,

mas aqui estão alguns fatos importantes a serem lembrados.

Evitando Leituras Inconsistentes
Considere esta situação. Martin edita a classe Clientes, a qual efetua chamadas para

a classe Pedidos. Enquanto isso, David edita a classe Pedidos e altera a interface. Da-

vid compila e grava. Martin então compila e grava. Agora o código compartilhado

está estragado porque Martin não percebeu que a classe Pedidos foi alterada. Alguns

sistemas de controle de código fonte irão localizar esta leitura inconsistente, mas ou-

tros requerem algum tipo de disciplina manual para impor consistência, como atualizar seus arquivos antes de gravá-los.
Na essência, este é o problema da inconsistência de leitura e, muitas, vezes é fácil nao vê-lo, porque a maioria das pessoas tende a focar as atualizações perdidas como o problema essencial na concorrência. Os bloqueios pessimistas têm um jeito

muito usado de lidar com este problema por meio de bloqueios de leitura e de gra-

vação. Para ler dados você precisa de um bloqueio de leitura (ou bloqueio comparti-

lhado). Para gravar dados você precisa de um bloqueio de gravação (ou bloqueio ex-

clusivo). Muitas pessoas podem ter bloqueios de leitura sobre um mesmo dado ao
mesmo tempo, mas, se alguém tiver um bloqueio de leitura, ninguém pode obter um
bloqueio de gravação. Inversamente, assim que alguém tiver um bloqueio de grava-

ção, ninguém mais poderá ter qualquer tipo de bloqueio. Com esse sistema, você

consegue evitar leituras inconsistentes com bloqueios pessimistas.
Os bloqueios otimistas normalmente baseiam sua detecção de conflitos em al-

gum tipo de marcador de versão sobre os dados. Este pode ser um timestamp ou um
contador seqiiencial. Para detectar atualizações perdidas, o sistema compara o marcador de versão da sua atualização com o marcador de versão dos dados comparti-

lhados. Se eles forem o mesmo, o sistema permite a atualização e atualiza o marcador

de versão.

Detectar uma leitura inconsistente é semelhante: neste caso cada parte dos dados que foi lida precisa ter seu marcador de versão comparado com os dados compartilhados. Qualquer diferença indica um conflito.

Controlar o acesso a cada parte dos dados que é lida, muitas vezes, causa proble-

mas desnecessários devido a conflitos ou esperas dos dados que não importam tanto.

Você pode reduzir esse fardo separando dados que usou dos dados que meramente

leu. Em uma lista de escolha de produtos, não importa se um novo produto aparecer

após você começar suas alterações. Entretanto, uma lista de pagamentos que você es-

teja resumindo para uma conta pode ser mais importante. A dificuldade é que isso re-

quer uma análise cuidadosa do motivo pelo qual é usado. Um código postal em um

endereço de um cliente pode parecer inócuo, porém se um cálculo de taxa for basea-

84

Parte]

e As NARRATIVAS

do em onde alguém mora, esse endereço tem que ser controlado quanto à concorrência. Como você pode ver, descobrir o que é e o que não é preciso controlar é um exercício intrincado, não importa qual forma de controle de concorrência você use.
Outra maneira de lidar com problemas de leitura inconsistente é usar Leituras
Temporais. Elas assinalam cada leitura de dados com algum tipo de timestamp ou rótulo imutável, e o banco de dados retorna os dados como

eles estavam de acordo

com aquele tempo ou rótulo. Muito poucos bancos de dados têm algo assim, mas os
desenvolvedores muitas vezes se deparam com isso em sistemas de controle de código fonte. O problema é que a fonte de dados precisa fornecer uma história temporal completa das alterações, o que gasta tempo e espaço para processar. Isso é razoável para código fonte, porém é mais difícil e custoso para bancos de dados. Você po-

de precisar fornecer essa capacidade para áreas específicas da sua lógica de domínio:
veja em [Snodgrass] e [Fowler TP] idéias de como fazer isso.

Deadlocks
Um problema particular com técnicas pessimistas é o deadlock. Suponha que Martin
comece a editar o arquivo Clientes, e David comece a editar o arquivo Pedidos. Da-

vid percebe que, para completar sua tarefa, precisa editar o arquivo Clientes também, porém Martin tem o bloqueio deste arquivo, de forma que ele tem que esperar.
Martin então percebe que tem que editar o arquivo Pedidos, o qual foi bloqueado por
David. Eles estão agora em deadlock - nenhum dos dois pode avançar até que o outro
termine. Descrito assim, os deadlocks parecem fáceis de evitar, mas eles podem ocor-

rer com muitas pessoas envolvidas em uma cadeia complexa, e isso os torna mais

traiçoeiro.

Há várias técnicas usadas para lidar com deadlocks. Uma é ter software que consiga detectar um deadlock quando ele ocorrer. Nesse caso você escolhe uma vítima,

que tem que jogar fora seu trabalho e seus bloqueios de modo que os outros possam
avançar. A detecção de deadlocks é muito difícil e custosa para as vítimas. Uma abordagem similar é dar a cada bloqueio um limite de tempo. Assim que você chegar nesse limite, perde seus bloqueios e seu trabalho — basicamente se tornando uma vítima.

Mecanismos de tempo limite são mais fáceis de implementar do que um mecanismo
de detecção de deadlocks, porém, se alguém segurar um bloqueio por certo tempo, algumas pessoas serão vitimadas quando não houver realmente deadlocks.
Os tempos limite e detecção de deadlocks lidam com um deadlock quando ele
ocorre. Outras abordagens tentam evitar que os deadlocks ocorram. Deadlocks ocorrem

basicamente quando pessoas que já têm bloqueios tentam obter mais (ou passar de
bloqueios de leitura para de gravação.) Assim, um modo de preveni-los é forçar as

pessoas a obter todos os seus bloqueios de uma só vez, no início do seu trabalho e,
então, evitar que elas obtenham mais.

Você pode forçar uma ordem pela qual todos obtenham seus bloqueios. Um
exemplo seria ser obter sempre os bloqueios sobre os arquivos em ordem alfabética.
Desta maneira, já que David tinha um bloqueio sobre o arquivo Pedidos, ele não po-

de tentar obter um bloqueio sobre o arquivo Clientes, porque este é anterior na seqiiéncia. Nesse momento, ele basicamente se torna uma vítima.

Você também pode fazer com que, se Martin tentar obter um bloqueio e David

já tiver um, Martin automaticamente se torne uma vítima. É uma técnica drástica,
mas é simples de implementar, e, em muitos casos, tal esquema funciona bem.

Capiruo 5 e CONCORRÊNCIA

85

Se você for muito conservador, pode usar múltiplos esquemas. Por exemplo,

você força todos a obter todos os seus bloqueios no início, mas acrescenta um tempo

limite para o caso de algo dar errado. Isso pode parecer como usar um cinto e sus-

pensórios, mas tal conservadorismo é muitas vezes sábio com relação a deadlocks,

porque estes são coisas desagradáveis que facilmente dão errado.

É muito fácil pensar que você tem um esquema à prova de deadlocks e depois

descobrir alguma cadeia de eventos que não considerou. O resultado disso é que preferimos esquemas muito simples e conservadores para o desenvolvimento de aplica-

ções corporativas. Elas podem causar vítimas desnecessárias, mas é geralmente muito melhor do que as conseqiiéncias de esquecer algum cenário de deadlock.

Transações

A principal ferramenta para lidar com concorrência em aplicações corporativas é a
transação. A palavra “transação” muitas vezes traz à mente uma troca de dinheiro ou

de bens. Ir até um caixa eletrônico, digitar seu código e retirar dinheiro é uma tran-

sação. Pagar $3 de taxa na Golden Gate Bridge é uma transação. Comprar uma cer-

veja no bar local é uma transação.

Olhar para negócios financeiros típicos como esses fornece uma boa definição

para o termo. Primeiro, uma transação é uma sequência limitada de trabalho, com

pontos de início e fim bem definidos. Uma transação em um caixa eletrônico come-

ça quando o cartão é inserido e termina quando o dinheiro é entregue ou uma falta de fundos é descoberta. Em segundo lugar, todos os recursos participantes estão

em estado consistente tanto quando a transação começa como quando ela termina.

Um homem comprando uma cerveja tem algum dinheiro a menos na sua carteira,

mas tem uma bela cerveja na sua frente. O somatório dos seus bens não mudou. O
mesmo vale para o bar — servir cerveja de graça não seria uma maneira de conduzir um negócio.
Além disso, cada transação deve ser completada em uma base de tudo ou nada.
O banco não pode subtrair do saldo de uma conta a não ser que o caixa eletrônico

realmente entregue o dinheiro. Embora o elemento humano pudesse tornar esta úl-

tima propriedade opcional durante as transações acima, não há razão para que o soft-

ware não possa criar uma garantia.
ACID

As transações de software são fregientemente descritas em termos de propriedades
ACID:
* Atomicidade: Cada passo na seqiiéncia de ações executadas no contexto de
uma transação deve ser completado com sucesso ou então todo o trabalho
deve ser desfeito. A conclusão parcial não é um conceito transacional. Assim,

se Martin estiver transferindo algum dinheiro de sua conta poupança para

sua conta corrente, e o servidor sofrer uma pane após ter retirado o dinheiro
da conta poupança, o sistema se comporta com se nunca tivesse executado

essa retirada. Confirmar (committing) diz que ambas as coisas ocorreram.

Desfazer (roll back) significa que nenhuma aconteceu. Tem que ser as duas
coisas ou nenhuma.

86

Parte | e As NARRATIVAS

* Consisténcia: Os recursos de um sistema devem estar em um estado consistente e não corrompido tanto no início quanto no final de uma transação.
* Isolamento: O resultado de uma transação individual não deve ser visível
para outras transações abertas até que a transação confirme sua execução

com sucesso.
*

Durabilidade: Qualquer resultado de uma transação confirmada deve ser

permanente. Isso é traduzido para “deve sobreviver a qualquer tipo de falha”.

Recursos Transacionais
A maior parte das aplicações corporativas se depara com transações no uso de bancos de dados. Porém há muitas outras coisas que podem ser controladas pelo uso de

transações, como filas de mensagens, impressoras e ATMs. O resultado disso é que

discussões técnicas sobre transações usam o termo “recurso transacional” para qual-

quer coisa que seja transacional - ou seja, que use transações para controle de concor-

rência. “Recurso transacional” é um termo um pouco longo, então usamos apenas
“banco de dados”, já que esse é o caso mais comum. Contudo, quando dizemos

“banco de dados”, o mesmo se aplica para qualquer outro recurso transacional.

Para obter a maior transferência de dados possível, sistemas transacionais mo-

dernos são projetados para manter as transações tão curtas quanto possível. Como

conseqiiéncia disso, o conselho geral é nunca fazer uma transação durar por múlti-

plas solicitações. Uma transação que dura por múltiplas solicitações é geralmente conhecida como uma transação longa.

Por essa razão, uma abordagem comum é começar uma transação no início de
uma solicitação e completá-la ao final. Esta transação de solicitação é um ótimo mo-

delo simples, e vários ambientes tornam fácil fazê-la declarativamente, simplesmen-

te identificando métodos como transacionais.

Uma variação é abrir uma transação o mais tarde possível. Com uma transação

tardia, você pode executar todas as leituras fora dela e apenas abri-la quando executar as atualizações. Isso tem a vantagem de minimizar o tempo gasto em uma transação. Se houver um tempo longo entre a abertura da transação e a primeira escrita, is-

so pode melhorar a vivacidade. Entretanto, significa que você não tem nenhum con-

trole de concorrência até que comece a transação, o que lhe deixa sujeito a leituras in-

consistentes. Como conseqiiéncia, normalmente não vale a pena fazer isso, a menos

que você tenha uma disputa muito grande ou o esteja fazendo devido a transações

de negócio que atravessam múltiplas solicitações (o que é o próximo tópico).
Quando você usa transações, precisa de alguma forma estar ciente do que exatamente está bloqueado. Para muitas ações do banco de dados, o sistema de transações bloqueia as linhas envolvidas, o que permite que múltiplas transações acessem
a mesma tabela. Entretanto, se uma transação bloquear muitas linhas de uma tabela,

o banco de dados tem mais bloqueios do que consegue lidar e amplia o bloqueio pa-

ra a tabela inteira — bloqueando outras transações. Esta ampliação de bloqueio pode
ter efeitos sérios sobre a concorrência, e é exatamente por isso que você não deve ter

algumas tabelas “objeto” para dados no nível Camada Supertipo (444) do domínio. Tal
tabela é uma forte candidata à ampliação do bloqueio, e bloquear esta tabela impede

o acesso de todos os outros ao banco de dados.

Capiruo 5 e CONCORRÊNCIA

87

Reduzindo o Isolamento Transacional para Aumentar a Vivacidade
É comum restringir a proteção completa das transações, de modo que você possa ob-

ter mais vivacidade. Isso é particularmente o caso ao lidar com isolamento. Se você
tiver isolamento integral, obtém transações serializáveis. As transações são serializá-

veis se puderem ser executadas concorrentemente e você obtiver um resultado igual

ao que obteria da execução serial dessas transações. Assim, se pegarmos nosso exem-

plo anterior do Martin contando seus arquivos, a capacidade de serializar garante

que ele obtenha um resultado que corresponde a concluir sua transação completa-

mente antes que a transação do David comece (doze) ou completamente após ela ter-

minar (dezessete). Capacidade de serializar não pode garantir o resultado, como nes-

te caso, mas pelo menos garante um resultado correto.

A maior parte dos sistemas transacionais usa o padrão SQL, o qual define qua-

tro níveis de isolamento. O serializável é o nível mais forte, e cada nível abaixo per-

mite que um tipo particular de leitura inconsistente entre em cena. Nós os exploraremos com o exemplo do Martin contando arquivos enquanto David os modifica. Há

dois pacotes: bloqueio e multifase. Antes do David atualizá-los há sete arquivos no
pacote de bloqueio e cinco no de multifase. Após sua atualização, há nove no de bloqueio e oito no multifase. Martin olha o pacote de bloqueio e David então atualiza

ambos. A seguir, Martin olha o pacote multifase.

Se o nível de isolamento for serializável, o sistema garante que a resposta do
Martin seja doze ou dezessete, ambas corretas. A capacidade de serializar não pode

garantir que toda execução deste cenário dê o mesmo resultado, mas este sempre ob-

tém o número antes da atualização do David ou o número depois.
O primeiro nível de isolamento abaixo da serialização é a leitura repetível, que
permite fantasmas. Os fantasmas ocorrem quando você adiciona alguns elementos a

uma coleção, e o leitor vê apenas alguns deles. O caso aqui é que Martin vê os arqui-

vos no pacote de bloqueio e enxerga sete. David então confirma sua transação, após
o que, Martin vê o pacote multifase e enxerga oito. Conseqiientemente, Martin obtém

um resultado incorreto. Os fantasmas ocorrem porque eles são válidos para uma parte da transação de Martin, mas não para toda ela, e eles são sempre coisas que foram
inseridas.
A seguir, na lista, está o nível de isolamento leitura confirmada, que permite

leituras não repetiveis. Imagine que Martin olhe um total em vez dos arquivos reais.
Uma leitura não-repetível permite a ele ler um total de sete para bloqueio. A seguir,

David confirma a gravação e ele então lê um total de oito para multifase. É chamada
leitura não-repetível porque, se Martin fosse reler o total do pacote de bloqueio após
David ter confirmado a gravação, ele obteria o novo número nove. Sua leitura original do número sete não pode ser repetida após a atualização de David. É mais fácil

para bancos de dados localizar leituras não-repetíveis do que fantasmas, de modo

que a leitura repetível lhe dá mais correção do que leituras confirmadas, mas menos

concorrência.
O menor nível de isolamento é a leitura não-confirmada, que permite leitu-

ras sujas. Na leitura não-confirmada, você pode ler dados que outra transação

ainda não confirmou. Isso causa dois tipos de erros. Martin poderia ver o pacote

de bloqueio enquanto David adicionava o primeiro dos seus arquivos, mas antes
que ele acrescentasse o segundo. O resultado disso é que ele vê oito arquivos no

pacote de bloqueio. O segundo tipo de erro ocorre se David adiciona seus arqui-

vos, mas desfaz sua transação — neste caso Martin vê arquivos que nunca estiveram realmente lá.

88

Parte | e As NARRATIVAS

A Tabela 5.1 lista os erros de leitura causados por cada nivel de isolamento.

Para estar seguro da correção, você deve sempre usar o nível de isolamento se-

rializável. O problema é que escolher o serializável atrapalha a vivacidade de um sis-

tema, tanto que muitas vezes você tem que reduzir a capacidade de serializar para

aumentar a quantidade de dados transferidos. Você tem que decidir que riscos quer
tomar e fazer seu próprio balanço entre erros e desempenho.

Você não tem que usar o mesmo nível de isolamento para todas as transações,

de modo que deve ver cada transação e decidir como balancear vivacidade versus

correção.

Transações de Negócio e de Sistema
O que nós falamos até agora, e a maior parte do que as pessoas falam, é o que chama-

mos de transações de sistema, ou transações suportadas por sistemas de gerencia-

mento de bancos de dados relacionais (RDBMS)

e monitores de transações. Uma

transação de banco de dados é um grupo de comandos SQL delimitados por instru-

ções para começar e terminá-lo. Se a quarta declaração na transação resultar em uma

violação de restrição de integridade, o banco de dados deve desfazer os efeitos das

primeiras três declarações e notificar o solicitante de que a transação falhou. Se todas

as quatro declarações tivessem sido completadas com sucesso, todas teriam sido tornadas visíveis para os outros usuários ao mesmo tempo, e não uma de cada vez. Ge-

renciadores de transações em sistemas RDBMS e em servidores de aplicações são tão
comuns que podem não receber tanta atenção. Eles funcionam bem e são bem com-

preendidos pelos desenvolvedores de aplicações.

Todavia, uma transação de sistema não tem significado para o usuário de um

sistema de negócios. Para o usuário de um sistema de banco online uma transação
consiste em se logar, selecionar uma conta, configurar alguns pagamentos e finalmente clicar no botão OK para pagar as contas. Isso é o que chamamos de uma transação de negócio, e parece uma expectativa razoável que ela apresente as mesmas

propriedades ACID como uma transação de sistema. Se o usuário cancelar antes de

pagar as contas, quaisquer alterações feitas nas telas anteriores devem ser canceladas. Fazer pagamentos não deve resultar em uma alteração de saldo visível para o
sistema até que o botão OK seja pressionado.
Aresposta óbvia para suportar as propriedades ACID de uma transação de negócios é executar essa transação inteira dentro de uma única transação de sistema.

Infelizmente as transações de negócios muitas vezes precisam de diversas solicitações para serem completadas, de modo que usar uma única transação de sistema para implementá-la resulta em uma transação de sistema longa. A maioria dos sistemas
de transação não funciona muito eficientemente com transações longas.

Tabela 5.1

Níveis de Isolamento e os Erros de Leitura Inconsistente que Permitem

Nível de Isolamento

Leitura Suja

Leitura Não-Repetível

Fantasmas

Leitura Não-Confirmada

Sim

Sim

Sim

Leitura Confirmada

Não

Sim

Sim

Leitura Repetível

Não

Não

Sim

Serializável

Não

Não

Não

