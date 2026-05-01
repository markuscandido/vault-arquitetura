# 20_Cap_17_Padroes_de_Estado_de_Sessao

CapituLo 18 e PaprõEs Básicos

445

Interface Separada (Separated Interface)

Define uma interface em um pacote separado da sua implementação.
domínio

Cliente
«interface»
Unidade

de Trabalho

mapeador de dados

}-------->

Pedido

Implementação
da Unidade
de Trabalho

Implementação da Unidade de Trabalho
À medida que você desenvolve um sistema, você pode melhorar a qualidade do seu
projeto, reduzindo o acoplamento entre as partes dele. Uma boa maneira de fazer isso é agrupar as classes em pacotes e controlar as dependências entre eles. Você pode
então seguir as regras sobre como classes em um pacote podem chamar classes em

outro — por exemplo, uma que diz que classes na camada do domínio não podem

chamar classes no pacote de apresentação.

Entretanto, você poderia precisar chamar métodos que contradizem a estrutura
geral de dependência. Se isso for verdade, use uma Interface Separada para definir
uma interface em um pacote, mas implementá-la em outro. Dessa maneira, um clien-

te que precisa da dependência com a interface pode ficar completamente ignorante a

respeito da implementação. A Interface Separada fornece um bom ponto de conexão

para o Gateway (436).

446

Parte Il e Os PaprOes

Como Funciona
Este padrao é muito simples de empregar. Basicamente, ele tira proveito do fato de
que uma implementação é dependente de sua interface, mas o contrário não é verdadeiro. Isso significa que você pode colocar a interface e a implementação em pacotes
separados, e o pacote da implementação tem uma dependência em relação ao paco-

te da interface. Outros pacotes podem depender do pacote da interface sem depen-

der do pacote da implementação.

É claro que, em tempo de execução, o software não funcionará sem alguma implementação da interface. Isso pode se dar tanto em tempo de compilação, usando
um pacote separado que ligue os dois, quanto em tempo de configuração, usando
um Plugin (465).

Você pode colocar a interface no pacote do cliente (como no esboço) ou em um

terceiro pacote (Figura 18.1). Se houver apenas um cliente para a implementação, ou

todos os clientes estiverem no mesmo pacote, então você poderia também colocar a

interface com o cliente. Uma boa maneira de pensar nisso é que os desenvolvedores

do pacote cliente são responsáveis pela definição da interface. Basicamente o pacote
cliente indica que ele trabalhará com qualquer outro pacote que implemente a inter-

face que ele define. Se você tiver diversos pacotes clientes, um terceiro pacote de i

terface é melhor. Ele também é melhor se você quiser mostrar que a definição da in-

terface não é a responsabilidade dos desenvolvedores do pacote cliente. Este seria o
caso se os desenvolvedores da implementação fossem responsáveis por isso.
Você tem que considerar que característica da linguagem usar para a interface.
Para linguagens que suportam a idéia de interfaces, como Java e C#, a palavra-chave

interface é a escolha óbvia. Entretanto, ela pode não ser a melhor. Uma classe abstra-

ta pode ser uma boa interface, porque nela você pode ter implementações de com-

portamento comuns, mas opcionais.
Uma das coisas complicadas a respeito de interfaces separadas é como instanciar a implementação. Isso geralmente requer conhecimento da classe da implementação. A abordagem comum é usar um objeto fábrica separado, em que novamente

há uma Interface Separada para a fábrica. Você ainda tem que ligar uma implementação à fábrica, e o Plugin (465) é uma boa maneira de fazê-lo. Isso não significa apenas

pedidos

Cliente
Pedido

UtilitáriosDeDados

pos

Unidade de Trabalho

A
=
Mapeador de Dados
ImplementaçãoDaUnidadeDeTrabalho

2
Figura 18.1

Colocando a Interface Separada em um terceiro pacote.

CapituLo 18 e Paproes Básicos

447

que não há dependência, mas também adia a decisão sobre a classe da implementa-

ção para o tempo de configuração.

Se você não quiser ir até o fim com um Plugin (465), uma alternativa mais sim-

ples é deixar que um outro pacote, que conheça tanto a interface quando a imple-

mentação, instancie os objetos corretos no início da aplicação. Quaisquer objetos que

usem uma Interface Separada podem, eles mesmos, serem instanciados ou ter fábricas

instanciadas no início da aplicação.
Quando Usá-la

Você usa uma Interface Separada quando precisa quebrar a dependência entre duas

partes do sistema. Aqui estão alguns exemplos:

* Você construiu algum código abstrato para casos comuns em um pacote fra-

mework que precisa chamar algum código específico da aplicação.

* Você tem algum código em uma camada que precisa chamar código em outra camada que ele não deveria ver, tal como código de domínio chamando

um Mapeador de Dados (170).

© Você precisa chamar funções desenvolvidas por outro grupo de desenvolvi-

mento, mas não quer uma dependência com suas APIs.

Deparo-me com muitos programadores que têm interfaces separadas para cada

classe que escrevem. Considero que isso é excessivo, especialmente para o desenvol-

vimento de aplicações. Manter separadas interfaces e implementações é um trabalho
extra, especialmente porque, fregientemente, você precisa também de classes fábri-

cas (com interfaces e implementações). Para aplicações, recomendo o uso de uma interface separada apenas se você quiser quebrar um dependência ou quiser ter diver-

sas implementações independentes. Se você colocar a interface e a implementação

juntas e precisar separá-las mais tarde, esta é uma refatoração simples que pode ser

adiada até que você precise fazê-la.

Existe um ponto em que esta forma diligente de gerenciamento de dependên-

cias pode se tornar um pouco tola. Ter apenas uma dependência para criar um objetoe, a partir daí, usar apenas a interface, é normalmente suficiente. O problema vem

quando você quer reforçar regras de dependência, como fazer uma verificação de dependência em tempo de construção. Então, todas as dependências têm que ser remo-

vidas. Para um sistema menor reforçar regras de dependência é um problema menor,
mas para sistemas maiores é uma disciplina que vale muito a pena.

448

Parte Il e Os PaprOes

Registro (Registry)
Um objeto conhecido que os outros objetos podem usar
para encontrar objetos e serviços comuns.
Registro

1

lerPessoa(id)

adicionarPessoa(Pessoa)
Quando você quer encontrar um objeto, você normalmente começa com outro obje-

to que tenha uma associação com ele e usa essa associação para navegar até ele. As-

sim, se você quiser encontrar todos os pedidos de um cliente, comece com o objeto
cliente e use um método nele para chegar aos pedidos. Entretanto, em alguns casos,

você não terá um objeto apropriado com o qual começar. Você pode saber o número

do ID do cliente, mas não ter uma referência para ele. Neste caso, você precisa de al-

gum tipo de método de busca — um buscador — mas a questão permanece: Como vo-

cê chega até o buscador?

O Registro é basicamente um objeto global, ou pelo menos ele se parece com um

— mesmo se ele não for tão global quanto possa parecer.
Como Funciona

Como com qualquer objeto, você tem que pensar no projeto do Registro em termos de
interface e implementação. Como muitos objetos, as duas são bastante diferentes,
embora, muitas vezes, as pessoas cometam o erro de pensar que elas devam ser o

mesmo.

A primeira coisa na qual pensar é a interface e, para os Registros, minha interfa-

ce preferida são os métodos estáticos. Um método estático em uma classe é fácil de

encontrar em qualquer lugar da aplicação. Além disso, você pode encapsular qual-

quer lógica que quiser dentro do método estático, incluindo delegação para outros
métodos, quer sejam estáticos, quer de instâncias.

Entretanto, só porque seus métodos são estáticos, isso não significa que seus da-

dos devam estar em campos estáticos. De fato, quase nunca uso campos estáticos a
menos que eles sejam constantes.

Antes que você decida sobre como armazenar seus dados, pense no escopo

dos mesmos. Os dados de um Registro podem variar com diferentes contextos de
execução. Alguns deles são globais por todo o processo; alguns, globais em uma

thread; e alguns, globais em uma sessão. Escopos diferentes pedem implementações

diferentes, mas não pedem interfaces diferentes. O programador da aplicação não

tem que saber se uma chamada a um método estático produz dados cujo escopo é

o processo ou a thread. Você pode ter diferentes Registros para diferentes escopos,

Registro

mas também pode ter um único Registro no qual diferentes métodos têm diferentes

escopos.

Se os seus dados forem comuns a todo um processo, um campo estático é uma

opção. Entretanto, raramente, uso campos estáticos mutáveis, porque eles não per-

CapituLo 18 e Paproes Básicos

449

mitem a substituição. Pode ser extremamente útil ser capaz de, com alguma finalida-

de específica, substituir um Registro, especialmente para testes (o Plugin (465) é uma
boa maneira de fazer isso).

Dessa forma, para um Registro com escopo de processo, a opção usual é um sin-

gleton [Gang of Four]. A classe Registro contém um único campo estático que armaze-

na uma instância do Registro. Quando as pessoas usam um singleton, elas fregiente-

mente obrigam o cliente a explicitamente acessar os dados subjacentes (Registro. instânciaúnica( ).lerBobo( |), mas eu prefiro um método estático que esconda o objeto
singleton de mim (Registro. lerBobo( )). Isso funciona especialmente bem, já que as lin-

guagens baseadas em C permitem aos métodos estáticos acessar dados privados de
instâncias.

Os Singletons são largamente utilizados em aplicações com uma única thread,
mas podem ser um problema em aplicações com diversas delas. Isso ocorre porque é

muito fácil acontecer de diversas threads manipularem o mesmo objeto de formas imprevisíveis. Você pode conseguir resolver o problema com sincronização, mas a difi-

culdade de escrever o código de sincronização provavelmente o levará a um hospi-

cio antes que você consiga eliminar todos os erros. Por esta razão, não recomendo o

uso de um singleton para dados mutáveis em um ambiente de multi-thread. Isso funciona bem com dados imutáveis, já que qualquer coisa que não possa ser alterada
não terá problemas causados pelo choque de threads. Assim, algo como uma lista de

todos os estados dos Estados Unidos é uma boa candidata para um Registro com es-

copo de processo. Tais dados podem ser carregados, quando um processo inicia, e
nunca precisam de alteração, ou podem ser atualizados raramente com algum tipo

de interrupção do processo.
Um tipo comum de dados de Registro são dados com escopo de thread. Um bom

exemplo é uma conexão a um banco de dados. Neste caso, muitos ambientes lhe dão

algum tipo de armazenamento específico de thread, como a thread local de Java. Ou-

tra técnica é um dicionário no qual chave é uma thread cujo valor é um objeto de da-

dos apropriado. Uma solicitação de uma conexão resulta em uma busca nesse dicio-

nario pela thread corrente.
O importante a lembrar sobre dados com escopo de thread é que eles não pare-

cem diferentes dos dados com escopo de processo. Ainda posso usar um método como Registro. lerConexãoBD( ), que é a mesma forma de quando estou acessando dados
com escopo de processo.
Uma busca no dicionário também é uma técnica que você pode usar para dados
com escopo de sessão. Aqui você precisa de uma identificação de sessão, mas ela pode ser colocada em um registro com escopo de thread quando uma solicitação tem início. Quaisquer acessos subseqiientes aos dados da sessão podem procurar os dados
em um mapa cujas chaves são sessões usando a identificação da sessão mantida no
armazenamento específico de thread.
Se você estiver usando um Registro com escopo de thread com métodos estáticos,
pode ter problemas de desempenho com diversas threads passando por estes métodos. Neste caso, o acesso direto à instância da thread evitará o gargalo.

Algumas aplicações podem ter um único Registro; outras podem ter vários. Os
Registros são normalmente classificados pela camada do sistema ou pelo contexto de
execução. Minha preferência é classificá-los pelo modo como são usados, e não pela
sua implementação.

450

Parte Il e Os PaprOes

Quando

Usa-lo

Apesar do encapsulamento de um método, um Registro consiste ainda de dados globais e, como tal, é algo com o que não me sinto confortável. Quase sempre vejo alguma forma de Registro em uma aplicação, mas sempre tento acessar os objetos por
meio de referências normais inter-objetos. Basicamente, você só deve usar um Registro como último recurso.

Existem alternativas ao uso de um Registro. Uma é passar por parâmetros quais-

quer dados necessários em vários pontos da aplicação. O problema é que os parâme-

tros têm de ser adicionados a chamadas de métodos em que eles não são necessários
ao método invocado, mas a algum outro método que é chamado diversas camadas

abaixo na árvore de chamadas. Passar um parâmetro adiante quando, durante 90%
do tempo ele não é necessário, é o que me leva a usar um Registro.

Uma alternativa que tenho visto ao uso do Registro é adicionar aos objetos, no

momento em que eles são criados, uma referência aos dados comuns. Embora isso le-

ve a um parâmetro extra em um construtor, ele pelo menos é usado apenas por esse

construtor. Ainda assim, é mais trabalho do que muitas vezes vale a pena, mas se vo-

cê tiver dados que são usados apenas por um subconjunto das classes, esta técnica

permite a você restringir as coisas deste modo.

Um dos problemas com um Registro é que ele tem que ser modificado cada vez

que você adiciona novos dados. É por isso que algumas pessoas preferem usar um

mapa como seu armazenador de dados globais. Prefiro a classe explícita porque ela

mantém os métodos explícitos, de modo que não há confusão sobre qual chave você
usa para encontrar alguma coisa. Com uma classe explícita, você pode apenas olhar
o código fonte ou a documentação gerada para ver o que está disponível. Com um
mapa você tem que encontrar lugares no sistema onde os dados são lidos ou grava-

dos no mapa para descobrir qual chave é usada, ou depender da documentação que

rapidamente se torna desatualizada. Uma classe explícita também permite a você

manter a segurança de tipos em uma linguagem estaticamente tipada, assim como

encapsular a estrutura do Registro, de modo que você possa refatorá-lo à medida que
o sistema cresce. Além disso, um mapa simples não é encapsulado, o que torna mais
difícil esconder a implementação. Isso é especialmente complicado se você tiver que

alterar o escopo de execução dos dados.

Assim, existem situações em que é correto usar um Registro, mas lembre-se de

que quaisquer dados globais são sempre culpados, até que se prove o contrário.

Exempl

: Um Registro Singleton (Java)

Considere uma aplicação que leia dados de um banco de dados e, a seguir, faça mo-

dificações nestes dados para transformá-los em informações. Bem, imagine um sistema razoavelmente simples que use Gateways de Linhas de Dados (158) para o acesso a
dados. Este sistema tem objetos de busca para encapsular as consultas ao banco de

dados. Os buscadores ficam melhor em instâncias porque, desta forma, podemos

Registro

substituí-los para criar um Stub de Serviço (469) com o propósito de testes. Precisamos
de um lugar para colocá-los. Um Registro é a escolha óbvia.
Um registro singleton é um exemplo muito simples do padrão Singleton [Gang
of Four]. Você tem uma variável estática para a instância única.

class Registro. .
private static Registro lerInstância () {

Caríruto 18 e PaprÕEs BÁsicos

451

return únicaInstância;

}

private static Registro únicaInstância = new Registro

( };

Tudo que é armazenado no registro é armazenado na instância.
class Registro...

protected BuscadorDePessoa buscadorDePessoa = new BuscadorDePessoa ( );
No entanto, para tornar o acesso mais fácil, torno os métodos públicos estáticos:

class Registro...
public static BuscadorDePessoa buscadorDePessoa

return lerInstancia( ) .buscadorDePessoa;

}

( ) (

Posso reinicializar o registro simplesmente criando uma nova instância única.

class Registro...
public static void inicializar (1) (
únicaInstância = new Registro( );
}
Se eu quiser usar Stubs de Serviço (469) para testes, uso em vez disso uma sub-

classe.

class StubDoRegistro extends Registro...
public StubDoRegistro { ) {

}

buscadorDePessoa = new StubDoBuscadorDePessoa| |;

O buscador do Stub de Serviço (469) apenas retorna instâncias codificadas expli-

citamente do Gateway de Linhas de Dados (158) da pessoa.

class StubDoBuscadorDePessoa...

public Pessoa buscar (long id) (
if (id == 1) {

}

return new Pessoa("Fowler",

"Martin", 10);

throw new IllegalArgumentException
("Não foi possível encontrar id: " + String.valueOf (id));

}

Coloco um método no registro para inicializá-lo no modo stub, mas, mantendo

todo o comportamento do stub na subclasse, posso separar todo o código necessário
para testes.

class Registro...
public static void inicializarstub () {

únicaInstância = new StubDoRegistro ( );

452

Parte ll e Os PanrõEs

Exemplo: Registro à Prova de Thread (Java)
Matt Foemmel e Martin Fowler

O simples exemplo anterior não funcionará em uma aplicação multi-thread na qual as
diferentes threads precisam de seu próprio registro. Java fornece o mecanismo de variáveis de armazenamento específicas de thread [Schmidt], que são locais a uma

thread, apropriadamente, chamadas de variáveis locais de threads. Você pode usá-las

para criar um registro que seja único para uma thread.

class RegistroDeThreadiocal...
private static ThreadLocal instâncias = new Threadtocal ( );
public static RegistroDeThreadLocal lerInstancia () {

}

return (RegistroDeThreadiocal) instâncias.get ( );

O Registro precisa ser configurado com métodos para a obtenção e liberação

deste registro. Normalmente, você faz isso no contexto de uma transação ou chama-

da de sessão.

class RegistroDeThreadiocal...
public static void início( ) {

Assert. istrue (instâncias.get ( ) == null);
instâncias.
set (new RegistroDeThreadhocal

)
public static void fim () (

( ));

Assert .notNull (lerInstância ( ));
instâncias. set (null);

Você pode então, como antes, armazenar buscadores de pessoas.

class RegistroDeThreadiocal...
private

BuscadorDePessoa

buscadorDePessoa

= new BuscadorDePessoa(

public static BuscadorDePessoa buscadorDePessoa ( | {

);

return lerinstância( ) .buscadorDePessoa;

As chamadas de fora encapsulam o seu uso de um registro nos métodos início e fim.
RegistroDeThreadLocal.inicio(

);

BuscadorDePessoa bl = RegistroDeThreadLocal .buscadorDePessoa( ];

Pessoa martin = bl.buscar(1);

assertEquals("Fowler",

martin. lerSobrenone(

) finally (RegistroDeThreadLocal.fim(

2
E

}

};

) );

CapituLo 18 e Paproes BAsicos

453

Objeto Valor (Value Object)
Um objeto pequeno e simples, como dinheiro ou uma faixa de datas,
cuja igualdade não é baseada na identidade.
Com sistemas de objetos de vários tipos, descobri que é útil distinguir entre objetos

referência e Objetos Valor. Dos dois, um Objeto Valor é geralmente o menor. Ele é semelhante aos tipos primitivos encontrados em muitas linguagens que não são puramente orientadas a objetos.

Como Funciona
Definir a diferença entre um objeto referência e um Objeto Valor pode ser um pouco
complicado. De maneira geral, gostamos de pensar que Objetos Valor são pequenos
objetos, tais como um objeto dinheiro ou uma data, enquanto que objetos referência

são grandes, tais como um pedido ou um cliente. Tal definição é útil, mas irritantemente informal.

A principal diferença entre objetos de referência e objetos valor reside em como

eles lidam com a igualdade. Um objeto referência usa a identidade como base para a
igualdade — talvez a identidade dentro do sistema de programação, tal como a identidade embutida das linguagens de programação OO, ou talvez algum tipo de núme-

ro de identificação, como a chave primária em um banco de dados relacional. Um

Objeto Valor baseia sua noção de igualdade em valores de campos dentro da classe.

Assim, dois objetos data podem ser o mesmo objeto se seus valores de dia, mês e ano

forem os mesmos.

Essa diferença se expressa na maneira como você lida com elas. Uma vez que

Objetos Valor são pequenos e facilmente criados, eles freqiientemente são passados
por valor em vez de por referência. Você não se importa realmente com quantos ob-

jetos 18 de março de 2001 existem no seu sistema. Tampouco se importa se dois objetos compartilham o mesmo objeto data físico ou se eles têm cópias diferentes, porém
iguais.

A maioria das linguagens não têm facilidades especiais para objetos valor. Para que os objetos valor funcionem apropriadamente nestes casos, é uma boa idéia
torná-los imutáveis — ou seja, uma vez criados nenhum de seus campos muda. A razão para isso é evitar problemas com apelidos (aliasing). Tais problemas podem
ocorrer quando dois objetos compartilham o mesmo objeto valor e um dos donos altera os valores nele. Assim, se Martin foi contratado no dia 18 de março e sabemos

que Cindy foi contratada no mesmo dia, podemos estabelecer a data de contratação

de Cindy como sendo a mesma de Martin. Se, a seguir, Martin altera o mês da sua
data de contratação para maio, a data de contratação de Cindy muda também. Quer

seja correto, quer não, não é o que as pessoas esperam. Normalmente, com valores

pequenos como este as pessoas esperam alterar uma data de contratação substituindo o objeto data existente por um novo. Tornar os Objeto Valor imutáveis satisfaz es-

ta expectativa.

Os Objetos Valor não devem ser persistidos como registros completos. Em vez

disso, use um Valor Embutido (261) ou um LOB Serializado (264). Uma vez que os Ob-

jetos Valor são pequenos, um Valor Embutido (261) é geralmente a melhor escolha porque ele também permite consultas SQL usando os dados em um Objeto Valor.

454

Parte ll e Os Parões

Se você estiver executando uma quantidade grande de serialização binária, po-

de descobrir que otimizar a serialização dos Objetos Valor pode melhorar o desempenho, especialmente em linguagens como Java que não tratam Objetos Valor de modo
especial.

Para um exemplo de um Objeto Valor, veja Dinheiro (455).
Implementação .NET

.NET tem um tratamento de primeira classe para Objetos Valor. Em C# um objeto é
marcado como um Objeto Valor declarando-o como uma struct em vez de como
uma classe. O ambiente então o trata com semântica de valor.

Quando

Usá-lo

Trate algo como um Objeto Valor quando estiver baseando as igualdades em algo que
não seja uma identidade. Vale a pena considerar isso para qualquer objeto pequeno
que seja fácil de criar.

Colisões de Nomes Tenho visto o termo Objeto Valor usado para este padrão já há al-

gum tempo. Infelizmente, vi recentemente a comunidade J2EE [Alur et al.) usar o tero “objeto valor” para significar um Objeto de Transferência de Dados (380), o que causou uma tempestade em um copo d'água na comunidade de padrões. Esta é apenas
uma daquelas colisões sobre nomes que acontecem o tempo todo neste negócio. Recentemente [Alur et al.] decidiram usar o termo objeto de transferência.

Continuo usando Objeto Valor desta maneira, neste texto. Senão por nenhum

outro motivo, ele me permite ser consistente com meus escritos anteriores!

Capíruio 18 e Paproes BÁsicos

455

Dinheiro (Money)
Representa um valor monetário.

Dinheiro

quantia
moeda
pe

alocar

><, <5,

Uma grande proporção dos computadores no mundo manipulam dinheiro, então

sempre me intrigou que dinheiro não seja realmente um tipo de dados de primeira
classe em nenhuma das principais linguagens de programação. A falta de um tipo

causa problemas, os mais óbvios envolvendo moedas. Se todos os seus cálculos fo-

rem feitos em uma única moeda corrente, este não é um grande problema, mas uma

vez que você envolva diversas moedas, você quer evitar adicionar seus dólares aos

seus ienes sem levar as diferenças de moeda em consideração. O problema mais su-

tilé com o arredondamento. Cálculos monetários são muitas vezes arredondados pa-

ra a menor unidade da moeda. Quando você faz isso, é fácil perder alguns centavos
(ou o seu equivalente local) devido aos erros de arredondamento.

O bom a respeito da programação orientada a objetos é que você pode consertar estes problemas criando uma classe Dinheiro que lide com eles. É claro que ainda

é surpreendente que, em verdade, nenhuma das bibliotecas principais de classes bá-

sicas faça isso.

Como Funciona
A idéia básica é ter uma classe Dinheiro com campos para a quantidade numérica e
a moeda corrente. Você pode armazenar a quantidade como um tipo inteiro ou um
tipo decimal fixo. O tipo decimal é mais fácil para algumas manipulações, o integral
para outras. Você deve evitar completamente qualquer tipo de ponto flutuante, pois

isso introduzirá o tipo de problemas de arredondamento cuja finalidade de Dinheiro

é evitar. Na maior parte do tempo, as pessoas querem valores monetários arredonda-

dos para a menor unidade da moeda, como os centavos no dólar. Entretanto, há ve-

zes em que unidades fracionárias são necessárias. É importante deixar claro com que
tipo de dinheiro você está trabalhando, especialmente em uma aplicação que usa am-

bos os tipos. Faz sentido ter diferentes tipos para os dois casos, pois eles se compor-

tam de forma bastante diferente no que diz respeito à aritmética.
O dinheiro é um Objeto Valor (453), então deve ter suas operações de igualdade

e código hash sobrescritas para serem baseadas na moeda corrente e na quantia.

O dinheiro precisa de operações aritméticas, de modo que você possa usar ob-

jetos desse tipo tão facilmente quanto usa números. Contudo, operações aritméticas
com dinheiro têm algumas diferenças importantes em relação às operações com di-

nheiro em números. A mais óbvia, qualquer adição ou subtração precisa ter ciência

da moeda, de modo que você possa reagir se tentar adicionar diferentes tipos de

moedas. A resposta mais simples, e mais comum, é tratar a adição de moedas incom-

g

