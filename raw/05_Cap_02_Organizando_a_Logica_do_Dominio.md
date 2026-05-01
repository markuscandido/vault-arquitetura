# 05_Cap_02_Organizando_a_Logica_do_Dominio

CapítuLO 2 e ORGANIZANDO A LÓGICA DO Domínio

um Contrato

| | um Produto | |

T

]

calcularLangamentos

|
calcularLangamento (um Contrato)

47

uma Estratégia de
Tancamento
|

|
|
|

calcularLançamentos (um Contrato)

!

Figura 2.2

I

T
|

|

|

I

um Lançamento
de Receita

1

Um Modelo de Dominio (126) para o lançamento de receitas.
to um dado contrato se refere, aplicar o algoritmo correto e então criar os objetos lan-

camento de receita para capturar os resultados do cálculo. (Para simplificar estou ig-

norando as questões relativas à interação com o banco de dados).

Na Figura 2.1, o método no Roteiro de Transação (120) faz todo o trabalho. Os ob-

jetos subjacentes são apenas Gateways de Tabela de Dados (151) e tudo o que eles fazem

é passar dados para o roteiro de transação.

Ao contrário, a Figura 2.2 mostra múltiplos objetos, cada um passando parte do
comportamento para outro até que um objeto de estratégia crie os resultados.

O valor de um Modelo de Domínio (126) é que, depois que você se acostuma, há
muitas técnicas que lhe permitem lidar com lógica cada vez mais complexa de uma
forma bem-organizada. À medida que mais e mais algoritmos para calcular o lançamento de receitas vão sendo adicionados, podemos acrescentá-los adicionando no-

vos objetos contendo a estratégia de lançamento. Com o Roteiro de Transação (120), te-

remos de adicionar mais condições à lógica condicional do roteiro. Assim que sua
mente estiver tão voltada para objetos como a minha, você descobrirá que prefere
um Modelo de Domínio (126) mesmo em casos bastante simples.

Os custos de um Modelo de Domínio (126) decorrem da complexidade de usá-lo
e da complexidade da sua camada de dados. Leva tempo para que pessoas novatas
em modelos ricos de objetos se acostumem a um Modelo de Domínio (126) rico. Os de-

senvolvedores frequentemente precisam gastar diversos meses trabalhando em um
projeto que usa este padrão antes que seus paradigmas mudem. No entanto, quando
você se acostuma ao Modelo de Domínio (126), geralmente está contagiado para o res-

to da vida e torna-se fácil trabalhar com ele no futuro — é assim que fanáticos por ob-

jetos, como eu, são criados. No entanto, uma minoria significativa de desenvolvedo-

res parece não ser capaz de dar o salto.

Mesmo depois de ter dado o salto, você ainda tem que lidar com o mapeamen-

to do banco de dados. Quanto mais rico for o seu Modelo de Domínio (126), mais com-

plexo será o mapeamento para um banco de dados relacional (geralmente com um

Mapeador de Dados (170)). Uma sofisticada camada de dados se aproxima bastante de
um custo fixo — obter uma boa camada de dados custa uma quantidade razoável de
dinheiro (se você comprar) ou tempo (se você fizer), mas assim que você tiver uma,

pode fazer muito com ela.

48

Parte | é As NARRATIVAS

Há uma terceira escolha para estruturar a lógica do domínio, o Módulo Tabela

(134). À primeira vista,

o Módulo Tabela (134) se parece com o Modelo de Domínio (126),

já que ambos têm classes para contratos, produtos e lançamento de receitas. A dife-

rença vital é que um Modelo de Domínio (126) tem uma instância de contrato para ca-

da contrato no banco de dados, enquanto um Módulo Tabela (134) tem apenas uma

instância. Um Módulo Tabela é projetado para trabalhar com um Conjunto de Registros

(473). Assim, o cliente de um Módulo Tabela (134) referente a contratos, irá primeiro

fazer consultas ao banco de dados para formar um Conjunto de Registros (473), então

criará um objeto contrato e passará a ele o Conjunto de Registros (473) como parâme-

tro. O cliente pode então invocar as operações no contrato para fazer diversas coisas

(Figura 2.3). Se ele quiser fazer algo com um contrato individual, deve passar o ID

desse contrato.

Um Módulo Tabela (134) é de muitas maneiras um andar intermediário entre um

Roteiro de Transação (120) e um Modelo de Domínio (126). Organizar a lógica do domínio ao redor de tabelas em vez de procedimentos segienciais fornece mais estrutura

e torna mais fácil encontrar e remover duplicação. No entanto, você não pode usar
várias das técnicas que um Modelo de Domínio (126) usa para estruturar a lógica em
granularidade mais fina, tais como herança, estratégias e outros padrões OO.

A maior vantagem de um Módulo Tabela (134) é como ele se adapta ao resto da
arquitetura. Muitos ambientes usando uma interface gráfica com o usuário (GUI) são
criados para trabalhar sobre os resultados de uma consulta SQL organizados em um

Conjunto de Registros (473). Uma vez que um Módulo Tabela (134) também trabalha sobre um Conjunto de Registros (473), você pode facilmente executar uma consulta, ma-

nipular os resultados no Módulo Tabela (134) e passar os dados manipulados para
apresentação na interface gráfica. Você também pode usar o Módulo Tabela (134) no
sentido contrário para realizar mais validações e cálculos. Várias plataformas, particularmente Microsoft COM e .NET, usam esse estilo de desenvolvimento.

novo(oConjuntoDeDados)

um Contrato
T

calcularLançamentos (IDdoContrato) fl

novo(oConjuntoDeDados)

um

Produto

novo(oConjuntoDeDados)

T
|

obterTipoDoProduto (IDdoProduto)
*inserir

Figura 2.3

]

Calculando lançamentos de receitas com um Módulo Tabela (134).

um Lançamento
De Receita

|

Capituto 2 e ORGANIZANDO A LÓGica DO Domínio

49

Fazendo uma Escolha
Então, como você escolhe entre os três padrões? Não é uma escolha fácil e depende

muito do quão complexa é sua lógica de domínio. A Figura 2.4 é um daqueles gráfi-

cos não-científicos que realmente me irritam em apresentações PowerPoint porque

têm eixos sem quantificação alguma. No entanto, o gráfico ajuda a visualizar minha
percepção de como comparar os três. O Modelo de Domínio (126) é menos atrativo com
lógica de domínio simples porque o custo de compreendê-lo e a complexidade da camada de dados adicionam muito esforço ao desenvolvimento, esforço esse que não

terá retorno. No entanto, à medida que a complexidade da lógica do domínio au-

menta, as outras abordagens tendem a atingir uma barreira em que acrescentar mais

características se torna muito mais difícil.

Seu problema, claro, é descobrir onde nesse eixo X sua aplicação se encontra.

boa notícia é que posso dizer que você deveria usar um Modelo de Domínio (126) sem-

A

pre que a complexidade da sua lógica de domínio for maior que 7.42. A má notícia é

que ninguém sabe como medir a complexidade da lógica do domínio. Na prática, en-

tão, tudo que o você pode fazer é encontrar pessoas experientes que possam fazer

uma análise inicial dos requisitos e fazer um julgamento.

Há alguns fatores que alteram um pouco as curvas. Uma equipe familiarizada

com o Modelo de Domínio (126) diminuirá o custo inicial do uso deste padrão. Isso não

irá rebaixá-lo ao mesmo ponto inicial dos outros padrões devido à complexidade da
camada de dados. Ainda assim, quanto melhor for a equipe, maior minha inclinação
a usar o Modelo de Domínio (126).

A atratividade de um Módulo de Tabela (134) depende muito do suporte existen-

te em seu ambiente a uma estrutura do tipo Conjunto de Registros (473) comum. Se vo-

cê tiver um ambiente como .NET ou Visual Studio, onde várias ferramentas traba-

Roteiro de Transação

Esforço para

Módulo Tabela

Modelo de Domínio

Melhorar

Complexidade da Lógica do Domínio

Figura 2.4

Um sentimento sobre o relacionamento entre complexidade e esforço para

diferentes estilos de lógica de domínio.

50

Parte | e As NARRATIVAS

lham ao redor de um Conjunto de Registros (473), então isso torna um Módulo Tabela

(134) muito mais atrativo. De fato, não vejo uma boa razão para usar um Roteiro de
Transação (120) em um ambiente .NET. Assim, se não houver ferramentas especiais

para Conjuntos de Registros (473), eu não perderia tempo com o Módulo Tabela (134).

Sua decisão, uma vez que você a tenha tomado, não estará completamente gra-

vada em pedra, mas será mais complicado alterá-la posteriormente. Dessa forma, va-

le a pena realizar alguma reflexão inicial para decidir qual caminho tomar. Se você

descobrir que tomou o caminho errado, então, se você começou com um Roteiro de
Transação (120), não hesite em refatorar em direção a um Modelo de Domínio (126). No

entanto, se você começou com um Modelo de Domínio (126), geralmente vale menos a

pena ir para um Roteiro de Transação (120), a não ser que você possa simplificar sua camada de dados.

Estes três padrões não são escolhas mutuamente excludentes. Na verdade, é
muito comum usar um Roteiro de Transação (120) para uma parte da lógica do domínio e um Módulo Tabela (134) ou Modelo de Domínio (126) para o resto.

Camada de Serviço
Uma abordagem comum para lidar com a lógica do domínio é dividir a camada de

domínio em duas. Uma Camada de Serviço (141) é colocada sobre um Modelo de Do-

minio (126) ou Módulo Tabela (134) subjacente. Normalmente, você só consegue isso

com um Modelo de Domínio (126) ou Módulo Tabela (134), uma vez que uma camada

de domínio que use apenas um Roteiro de Transação (120) não é complexa o suficien-

te para justificar uma camada separada. A lógica da apresentação interage com a de

domínio puramente por meio da Camada de Serviço (141), a qual age como uma API

para a aplicação.

Além de fornecer uma API clara, a Camada de Serviço (141) é também um bom

local para colocar coisas como controle de transação e segurança. Isso lhe dá um mo-

delo simples para pegar cada método na Camada de Serviço (141) e descrever suas características transacionais e de segurança. Uma escolha comum para isso consiste em

um arquivo separado de propriedades, mas os atributos .NET fornecem uma manei-

ra elegante de fazer isso diretamente no código.
Quando você tenta uma Camada de Serviço (141), uma decisão-chave é quanto

comportamento colocar nela. O caso mínimo é tornar a Camada de Serviço (141) uma

fachada, de modo que todo o comportamento real esteja em objetos subjacentes. Des-

sa forma, tudo o que a Camada de Serviço (141) faz é encaminhar as chamadas à facha-

da para os objetos de mais baixo nível. Neste caso, a Camada de Serviços (141) fornece

uma API mais fácil de usar porque, tipicamente, ela é orientada aos casos de uso. Ela
também é um ponto conveniente para acrescentar invólucros transacionais e verificações de segurança.

No outro extremo, a maior parte da lógica de negócio é colocada nos Roteiros de

Transação (120), dentro da Camada de Serviço (141). Os objetos de domínio subjacentes

são muito simples; se for um Modelo de Domínio (126), eles terão uma correspondência um-para-um com o banco de dados e, assim, você pode usar uma camada de dados mais simples, tal como um Registro Ativo (165).

A meio caminho entre essas alternativas está uma combinação mais regular de

comportamento: o estilo controle-entidade. Este nome vem de uma prática comum,

muito influenciada por [Jacobson et al.]. O principal aqui é colocar a lógica que seja

CapítuLo 2 e ORGANIZANDO A LÓGICA DO Domínio

51

particular a uma única transação ou caso de uso em Roteiros de Transação (120), os
quais comumente são referidos como controles ou serviços. Estes são controles diferentes do controle de entrada no Modelo Vista Controle (315) ou Controle de Aplicação
(360) que encontraremos mais adiante, por isso uso o termo controle do caso de uso.

O comportamento usado em mais de um caso de uso vai para os objetos de domínio,
os quais são chamados de entidades.

Ainda que a abordagem controle-entidade seja comum, nunca gostei muito de-

la. Os controles de casos de uso, como qualquer Roteiro de Transação (120), tendem a

encorajar o código duplicado. Minha opinião é que, se você decidir usar um Modelo

de Dominio (126), realmente deveria ir fundo e fazê-lo dominante. A exceção a isso é
se você começou com um projeto que usa o Roteiro de Transação (120) com um Gateway de Linha de Dados (134). Neste caso, faz sentido mover o comportamento duplica-

do para os Gateways de Linhas de Dados (158), o que os tornará em um Modelo de Domínio (126) simples usando Registro Ativo (165). No entanto, eu não começaria dessa for-

ma. Eu faria isso apenas para melhorar um projeto que esteja mostrando falhas.

Não estou dizendo que você nunca deva ter objetos de serviço que contenham

lógica de domínio, mas sim que você não deve, necessariamente, fazer deles uma ca-

mada fixa. Objetos de serviço procedurais podem, às vezes, ser uma maneira muito

útil de fatorar lógica, mas tendo a usá-los quando necessário, em vez de usá-los co-

mo uma camada da arquitetura.
Minha preferência é, portanto, ter a mais fina Camada de Serviço (141) que você
puder, se realmente necessitar de uma. Minha abordagem normal é supor que eu não
preciso de uma e apenas acrescentá-la se parecer que a aplicação precisa dela. Entretanto, conheço muitos bons projetistas que sempre usam uma Camada de Serviço (141)

com razoável quantidade de lógica, então sinta-se à vontade para não me dar aten-

ção a esse respeito. Randy Stafford tem tido muito sucesso com uma Camada de Ser-

viço (141) rica. Por este motivo pedi a ele que escrevesse o padrão Camada de Serviço
(141) para este livro.

CapiTULO

3

Mapeando para Bancos de Dados
Relacionais

Ore
da camada de fonte de dados é a comunicação com as diversas partes da infra-estrutura que uma aplicação precisa para executar sua tarefa.
Uma parte proeminente deste problema é se comunicar com o banco de dados, o que, na maioria dos sistemas criados hoje, significa um banco de dados relacional. Certamente ainda há muitos dados em formatos de armazenamento mais
antigos, como arquivos ISAM e VSAM de mainframes, mas a maior parte das pes-

soas que está construindo sistemas hoje se preocupa em trabalhar com um banco
de dados relacional.

Uma das maiores razões para o sucesso dos bancos de dados relacionais é a presença da SQL, a linguagem mais padronizada para comunicação com bancos de da-

dos. Embora a SQL seja cheia de acréscimos aborrecidos e complicados específicos de
vendedores, o núcleo da sua sintaxe é comum e bem compreendido.

Padrões de Arquitetura
O primeiro conjunto de padrões abrange os padrões de arquitetura, que direcionam

o modo pelo qual a lógica de domínio se comunica com o banco de dados. A escolha

que você faz aqui tem amplas consegiiências sobre o seu projeto e, desta forma, dificulta a refatoração, de modo que é uma decisão na qual você deve prestar certa aten-

ção. É também uma escolha fortemente afetada pelo modo como você projeta sua lógica de domínio.

Apesar de toda a difusão do uso da SQL em software corporativo, ainda há ar-

madilhas. Muitos desenvolvedores de aplicação não entendem bem SQL e, como re-

sultado, têm problemas para definir consultas e comandos eficazes. Embora existam
várias técnicas para embutir SQL em uma linguagem de programação, elas são todas

um pouco deselegantes. Seria melhor acessar os dados usando mecanismos que se

encaixem com a linguagem de desenvolvimento da aplicação. Administradores de

Capituto 3 e MAPEANDO PARA BANCOS DE DADOS RELACIONAIS

53

bancos de dados (DBAs) também gostam de acessar a SQL que acessa uma tabela de

modo que possam compreender como melhor ajustá-la e como organizar os indices.
Por essas razões, é sábio separar o acesso SQL da lógica de domínio e colocá-la

em classes separadas. Uma boa maneira de organizar essas classes é baseá-las na estrutura da tabela do banco de dados de modo que você tenha uma classe por tabela

do banco de dados. Essas classes então formam um Gateway (436) para a tabela. O
resto da aplicação não precisa saber nada da SQL, e é fácil de encontrar toda a SQL

que acessa o banco de dados. Os desenvolvedores que se especializam no banco de

dados têm um claro lugar aonde ir.

Há duas maneiras principais por meio das quais você pode usar um Gateway

(136). A mais óbvia é ter uma instância dela para cada linha retornada por uma con-

sulta (Figura 3.1). Este Gateway de Linhas de Dados (158) é uma abordagem que natu-

ralmente se ajusta em um modo orientado a objetos de pensar sobre os dados.

Muitos ambientes fornecem um Conjunto de Registros (473) — ou seja, uma estru-

tura de dados genérica de tabelas e linhas que simula a natureza tabular de um ban-

co de dados. Devido ao fato de um Conjunto de Registros (473) ser uma estrutura de
dados genérica, os ambientes podem usá-la em muitas partes de uma aplicação.

bastante comum que ferramentas de interfaces gráficas com o usuário (GUI) tenham

controles que trabalhem com um Conjunto de Registros (473). Se você usar um Conjun-

É

to de Registros (473), só precisa de um objeto para cada tabela no banco de dados. Este Gateway de Tabelas de Dados (151) (veja a Figura 3.2) fornece métodos para pesquisar o banco de dados que retornam um Conjunto de Registros (473).

Gateway Pessoa
sobrenome
prenome
numeroDeDependentes
inserir
atualizar

apagar

encontrar (id)

encontrarPelaCompanhia(idDaCompanhia)

Figura 3.1 Um Gateway de Linhas de Dados (158) tem uma instância por linha retornada
por uma consulta.

Gateway Pessoa
encontrar (id): ConjuntoDeRegistros

encontrarPeloSobrenome (String): ConjuntoDeRegistros
atualizar (id, sobrenome, prenome, numeroDeDependentes)
inserir (sobrenome, prenome, numeroDeDependentes)
apagar (id)
Figura 3.2

Um Gateway de Tabelas de Dados (151) tem uma instância por tabela.

54

Parte | e As NARRATIVAS

Até mesmo em aplicações simples, tendo a usar um dos padrões Gateway. Uma

olhada rápida nos meus roteiros Ruby e Python confirmará isso. Considero muito
útil a separação clara entre SQL e lógica do domínio.

O fato do Gateway de Tabelas de Dados (151) se ajustar tão bem ao Conjunto de Re-

gistros (473) torna-o a escolha óbvia se você estiver usando Módulo Tabela (134). Tam-

bém é um padrão que você pode usar para pensar em organizar procedures armaze-

nadas (stored procedures). Muitos projetistas gostam de executar todo seu acesso de
banco de dados por meio de procedimentos armazenados em vez de SQL explícita.
Neste caso, você pode pensar na coleção de procedimentos armazenados como definindo um Gateway de Tabelas de Dados (151) para uma tabela. Eu ainda teria um Gate-

way de Tabelas de Dados (151) em memória para encapsular as chamadas aos procedi-

mentos armazenados, uma vez que isso mantém encapsulados os mecanismos de

chamada ao procedimento armazenado.

Se você estiver usando um Modelo de Domínio (126), há mais algumas opções.

Você certamente pode usar um Gateway de Linhas de Dados (158) ou Gateway de Tabelas
de Dados (151) com um Modelo de Domínio (126). Para o meu gosto, entretanto, isso po-

de ser ou indireção demais, ou não o suficiente.

Em aplicações simples, o Modelo de Domínio (126) é uma estrutura sem compli-

cação que em verdade corresponde bastante à estrutura do banco de dados, com

uma classe de domínio por tabela do banco de dados. Tais objetos de domínio muitas vezes têm lógica de negócio apenas moderadamente complexa. Nesse caso faz
sentido deixar cada objeto do domínio ser responsável por carregar e salvar no ban-

co de dados, o que caracteriza o Registro Ativo (165) (veja a Figura 3.3). Outra forma

de pensar no Registro Ativo (165) é que você começa com um Gateway de Linhas de Da-

dos (158) e então acrescenta lógica de domínio à classe, especialmente quando vê código repetido em diversos Roteiros de Transação (120).

Neste tipo de situação, a indireção adicionada de um Gateway (436) não fornece

grande valor. À medida que a lógica de domínio ficar mais complicada e você come-

gar a ir na direção de um Modelo de Domínio (126) rico, a abordagem simples de um

Registro Ativo (165) começa a não funcionar. A associação um-para-um das classes do

domínio com as tabelas começa a falhar quando você fatora a lógica de domínio em

classes menores. Bancos de dados relacionais não lidam com herança, de modo que
se torna dificil usar estratégias [Gang of Four] e outros padrões elegantes OO. À medida que a lógica de domínio fica pior, você irá querer testá-la sem ter que se comunicar com o banco de dados todo o tempo.

Todas essas forças lhe empurram para a indireção à medida que seu Modelo de

Domínio (126) se torna mais rico. Nesse, caso o Gateway (436) pode resolver alguns

Cliente

carregar(ConjuntoDeRegistros)

apagar
inserir
atualizar
verificarCrédito
enviarCobrança

[o
Tabela

Figura 3.3 No Registro Ativo (165), um objeto de dominio cliente sabe como interagir
com tabelas do banco de dados.

