# 08_Cap_05_Concorrencia

100

Parte | e As NARRATIVAS

Fatura

Cliente

Entrega

Figura 7.1

Distribuindo uma aplicação, colocando diferentes componentes em

diferentes nós (não recomendado!).

Assim, pressupondo que você não tenha mostrado a porta de saída para este livro, acho que irá querer saber por que esta arquitetura distribuída é ruim. Afinal de
contas, muitos vendedores de ferramentas lhe dirão que o motivo principal do uso

de objetos distribuídos é que você pode pegar um monte de objetos e posicioná-los

como quiser em nós de processamento. Além disso, seu middleware poderoso fornece
transparência. A transparência permite que os objetos chamem uns aos outros den-

tro de um processo ou entre processos sem ter que saber se o objeto chamado está no
mesmo processo, em outro processo ou em outra máquina.

A transparência é valiosa, no entanto, ainda que muitas coisas possam ser tor-

nadas transparentes em objetos distribuídos, o desempenho normalmente não é uma
delas. Embora nosso arquiteto idealizado estivesse distribuindo objetos do modo

que vinha fazendo por motivos de desempenho, na verdade seu projeto irá prejudi-

car o desempenho ou tornar o sistema muito mais difícil de construir e distribuir ou
ainda, o que é mais frequente, terá ambos os defeitos.

Interfaces Locais e Remotas

A principal razão pela qual a distribuição por modelo de classe não funciona tem a

ver com um fato fundamental a respeito de computadores. Uma chamada de procedimento dentro de um mesmo processo é muito, muito rápida. Uma chamada de um

procedimento entre dois processos separados ordens de magnitude mais devagar.

Rode esse processo em uma outra máquina e você pode acrescentar mais uma ou

duas ordens de magnitude, dependendo da topografia de rede envolvida.
O resultado disso é que a interface de um objeto a ser usado remotamente deve

ser diferente daquela de um objeto usado dentro do mesmo processo.

Uma interface local fica melhor como uma interface de granularidade baixa. As-

sim, se eu tiver uma classe endereço, uma boa interface terá métodos separados pa-

Capituto 7 e Estratécias DE Distrisuição

101

ra ler a cidade, o estado, gravar a cidade, gravar o estado, e assim por diante. Uma

interface de granularidade baixa é boa porque segue o princípio OO, que preconiza
muitos pedaços pequenos de software que podem ser combinados e sobrescritos de

várias maneiras para estender o projeto no futuro.

Uma interface de granularidade baixa não funciona bem quando é remota.

Quando as chamadas de métodos são lentas, você quer obter ou atualizar a cidade,

estado e código postal em uma chamada, em vez de em três. A interface resultante
tem granularidade alta, projetada não para maior flexibilidade e extensibilidade mas

para minimizar as chamadas. Aqui você verá uma interface lidar com todos os deta-

lhes de uma leitura ou atualização de endereços. É muito mais complicado de pro-

gramar, mas por motivos de desempenho, você precisará deste tipo de interface.

É claro, que os vendedores irão lhe dizer é que não há overhead no uso do seu
middleware para chamadas locais e remotas. Se for uma chamada local, ela é feita com

a velocidade de uma chamada local. Se for uma chamada remota, ela é feita mais de-

vagar. Assim, você só paga o preço de uma chamada remota quando precisar de uma.
Isso é, até certo ponto, verdadeiro, porém não evita a questão essencial de que qualquer objeto que possa ser usado remotamente deva ter uma interface de granularida-

de alta enquanto que cada objeto que não for usado remotamente deve ter uma interface de granularidade baixa. Sempre que dois objetos se comunicam, você deve esco-

ther qual delas usar. Se o objeto puder, alguma vez, estar em um processo separado,
você tem que usar a interface de granularidade alta e pagar o preço do modelo de programação mais difícil. Obviamente, só faz sentido pagar esse preço quando você precisar, então você deve minimizar a quantidade de colaborações interprocessos.
Por esses motivos você não pode simplesmente pegar um grupo de classes que

projetou para um ambiente de um único processo, atirar CORBA ou algo semelhan-

te nelas e disto resultar um modelo distribuído. Um projeto distribuído é mais do
que isso. Se você basear sua estratégia de distribuição em classes, acabará com um
sistema que executa muitas chamadas remotas e, desta forma, precisa de interfaces

deselegantes, com granularidade alta. No final, mesmo com interfaces de granularidade alta em toda classe remota, você ainda terá como bônus, demasiadas chamadas

remotas e um sistema complicado para modificar.
Assim, chegamos à minha Primeira Lei do Projeto de Objetos Distribuídos:
não distribua seus objetos!

Como, então, usar efetivamente múltiplos processadores? Na maior parte dos

casos, o caminho apropriado é a clusterização (veja a Figura 7.2). Coloque todas as
classes em um único processo e então execute múltiplas cópias desse processo nos diversos nós de processamento. Desta forma, cada processo usa chamadas locais para
executar o trabalho e assim o faz mais rápido. Além disso, você pode usar interfaces
de granularidade baixa em todas as classes dentro do processo e assim obter maior

facilidade de manutenção com um modelo de programação mais simples.

Onde Você Tem que Distribuir
Você quer minimizar as fronteiras da distribuição e utilizar a clusterização de seus

nós de processamento tanto quanto possível. O obstáculo é que há limites nessa

abordagem - isto é, situações onde você precisará separar os processos. Se você for

sensato, lutará como um rato encurralado para eliminar tantas fronteiras de distri-

buição quanto puder, mas não as eliminará por completo.

102

Parte | e As NARRATIVAS

a1: Aplicação

a3: Aplicação

0a2: Aplicação
Pedido

0a4: Aplicação

Pedi

Figura 7.2

Pedido

Pedido

A utilização de clusters envolve a colocação de diversas cópias da mesma

aplicação em diferentes nós.

* Uma separação óbvia é entre o lado cliente e o lado servidor nas aplicações
de negócio. Os PCs nas mesas dos usuários são nós de processamento dife-

rentes daqueles onde residem os repositórios de dados compartilhados. Já
que eles são máquinas diferentes, você precisa de processos separados que se
comuniquem. A divisão cliente-sevidor é uma típica divisão interprocessos.

* Uma segunda divisão frequentemente ocorre entre o lado servidor da aplica-

ção (o servidor de aplicações) e o banco de dados. É claro que você não é
obrigado a fazer isso. Você pode rodar todo o lado servidor da aplicação no
próprio processo do banco de dados usando coisas tais como procedimentos
armazenados. Mas, na maioria das vezes, isso não é tão prático, de modo que

você tem que ter processos separados. Eles até podem rodar na mesma má-

quina mas, apenas por trabalhar com processos separados, você terá que pagar a maior parte do custo de chamadas remotas. Felizmente, a linguagem

SQL é projetada como uma interface remota, de modo que você normalmente pode arranjar as coisas de modo a minimizar esse custo.
*

Outra separação em processos pode ocorrer em um sistema Web entre o servi-

dor Web e o servidor de aplicação. Se tudo for igual, é melhor rodar os servidores Web e de aplicações em um único processo, mas nem tudo é sempre igual.

* Você pode ter que separar devido a diferenças entre vendedores. Se você estiver usando um pacote de software, ele muitas vezes rodará no seu próprio
processo, de modo que mais uma vez você estará distribuindo. Um bom pacote terá, no mínimo, uma interface de granularidade alta.

* E, finalmente, pode haver alguma razão genuína para você ter que dividir

seu software servidor de aplicações. Você deve até mesmo vender seus pró-

prios avós para evitar isso, mas algumas vezes há certos casos em que não é

possível evitá-lo. Nesses casos, você tem de tampar o nariz e dividir seu soft-

ware em componentes remotos com granularidade alta.

Carituto 7 e EstratéGiAS DE DistrisuicAo

103

O tema recorrente, segundo a memorável frase de Colleen Roe, é ser “parcimonioso com a distribuição de objetos”. Venda primeiro sua avó favorita, se for possível.

Trabalhando com as Fronteiras da Distribuição
Durante o projeto de seu sistema, você precisa limitar a distribuição tanto quanto

possível, mas onde ela for necessária, você tem de levar em conta as fronteiras de dis-

tribuição. Toda chamada remota é o equivalente cibernético de uma viagem em uma
carruagem puxada a cavalos. Todos os locais no sistema mudarão de formato para
minimizar chamadas remotas. Este é o preço esperado.

Entretanto, dentro de um único processo, você ainda pode projetar usando ob-

jetos de granularidade baixa. A chave é usar esses objetos internamente e colocar ob-

jetos de granularidade alta nas fronteiras de distribuição, cujo único papel é fornecer

uma interface remota para os objetos de granularidade baixa. Os objetos de granularidade alta agem simplesmente como uma fachada para os objetos de granularidade
baixa. A existência desta fachada é motivada apenas por questões de distribuição —
daí o nome de Fachada Remota (368).
O uso de uma Fachada Remota (368) ajuda a minimizar as dificuldades que as in-

terfaces de granularidade alta introduzem. Dessa forma, apenas os objetos que real-

mente precisam de um serviço remoto invocam o método de granularidade alta e fi-

ca óbvio para os desenvolvedores que eles estão pagando esse preço. A transparência tem suas virtudes, mas você não quer ser transparente em relação a uma poten-

cial chamada remota.
Mantendo as interfaces de granularidade alta como meras fachadas, você permite que as pessoas usem os objetos de granularidade baixa sempre que elas souberem que estão rodando no mesmo processo. Isso torna toda a política de distribuição
muito mais explícita. De mãos dadas com a Fachada Remota (368) está o Objeto de
Transferência de Dados (380). Você precisa não apenas de métodos de granularidade al-

ta, mas também transferir objetos de granularidade alta. Quando você solicita um endereço, precisa enviar essa informação em um bloco. Normalmente você não pode
enviar o próprio objeto do domínio, porque ele está preso a uma rede de referências

locais entre objetos de granularidade baixa. Assim, você deve pegar todos os dados

que o cliente precisa e os empacotar em um objeto específico para a transferência —
daí o termo Objeto de Transferência de Dados (380). (Muitas pessoas na comunidade en-

terprise Java usam o termo objeto valor para isso, mas causa um conflito com outros

significados do termo Objeto Valor (453)). O Objeto de Transferência de Dados (380) aparece nos dois lados da conexão, de modo que é importante que ele não faça nenhuma

referência a nada que não seja compartilhado através da conexão. Isto resulta no fato de que um Objeto de Transferência de Dados (380) normalmente apenas referencia

outros Objetos de Transferência de Dados (380) e objetos fundamentais tais como strings.

Outro caminho para a distribuição é ter um intermediário que migre objetos entre os processos. A idéia aqui é usar um esquema de Carga Tardia (200) em que, em
vez de efetuar uma leitura tardia de um banco de dados, você move objetos através

da conexão. A parte complicada é assegurar que você não acabe com demasiadas

chamadas remotas. Ainda não vi ninguém tentar isso em uma aplicação, mas algumas ferramentas de mapeamento O/R

(p. ex., TOPLink) têm esta facilidade e tenho

ouvido alguns relatos interessantes a esse respeito.

104

Parte | é As NARRATIVAS

Interfaces para Distribuição
Tradicionalmente, as interfaces de componentes distribuídos têm sido baseadas em

chamadas a procedimentos remotos, seja por meio de procedimentos globais ou como métodos nos objetos. Nos últimos anos, entretanto, temos começado a ver inter-

faces baseadas em XML sobre HTTP. A forma mais comum dessa interface será provavelmente o SOAP, mas muitas pessoas efetuaram experimentos na área durante
muitos anos.

A comunicação HTTP baseada em XML é útil por diversos motivos. Ela permi-

te facilmente que uma grande quantidade de dados seja enviada de forma estruturada, em uma única viagem de ida e volta. Isso é bom, uma vez que o número de cha-

madas remotas precisa ser minimizado. O fato de XML ser um formato comum, com
parsers disponíveis em muitas plataformas, permite que sistemas construídos em pla-

taformas inteiramente diferentes se comuniquem, acrescido ao fato do protocolo
HTTP ser universal. O fato do XML ser textual torna fácil ver o que está acontecendo

através da conexão. E ainda é fácil passar HTTP através de firewalls quando questões
políticas e de segurança tornam difícil uma alternativa.
Ainda assim, uma interface orientada a objetos de classes e métodos

também

tem valor. Mover todos os dados transferidos para estruturas XML e strings pode

adicionar uma sobrecarga considerável à chamada remota. As aplicações certamen-

te têm visto uma significativa melhora de desempenho substituindo uma interface

baseada em XML por uma chamada remota. Se ambos os lados da conexão usarem o

mesmo mecanismo binário, uma interface XML não traz muito mais do que um conjunto vistoso de acrônimos. Se você tiver dois sistemas construídos com a mesma

plataforma, estará melhor servido se usar o mecanismo de chamadas remotas nessa

plataforma. Serviços Web se tornam úteis quando você quer que plataformas dife-

rentes conversem. Minha posição é usar serviços Web baseados em XML apenas
quando uma abordagem mais direta não for possível.
É claro que você pode ter o melhor dos dois mundos colocando uma camada
HTTP sobre uma interface orientada a objetos. Todas as chamadas para o servidor

Web são traduzidas por esta camada em chamadas para uma interface orientada a
objetos correspondente. Até certo ponto, isso lhe dá o melhor dos dois mundos, mas

aumenta a complexidade, já que irá precisar tanto do servidor Web quanto do meca-

nismo para uma interface remota OO. Portanto, você só deve fazer isso se precisar de
uma API HTTP, bem como de uma API remota OO, ou se as facilidades da APIOO

remota para segurança e gerenciamento de transações tornar mais fácil lidar com essas questões do que usando objetos não-remotos.
Nas minhas discussões aqui, parti do pressuposto de uma interface síncrona
baseada em RPC. Entretanto, embora isso seja o que descrevi, realmente não consi-

dero que seja sempre a melhor maneira de lidar com um sistema distribuído. Cada

vez mais, minha preferência é por uma abordagem inerentemente assincrona, baseada em mensagens. A exploração de padrões para trabalhos baseados em mensagens é um tópico relativamente grande por si só, por isso a evitei neste livro. Espe-

ro que um livro sobre isso apareça em um futuro próximo, mas, por enquanto, tu-

do o que posso fazer é encorajá-lo a considerar abordagens assíncronas, baseadas

em mensagens. Particularmente, acho que elas são o melhor uso dos serviços Web,
ainda que a maior parte dos exemplos publicados até agora sejam síncronos.

CAPÍTULO

Juntando Tudo

té agora estas narrativas olharam um aspecto de um sistema e exploraram as
diversas opções para tratá-lo. Agora é hora de reunir tudo e começar a responder às questões traiçoeiras de quais padrões usar ao projetar uma aplica-

ção corporativa.

O conselho, neste capítulo, é de muitas maneiras uma repetição do conselho da-

do em capítulos anteriores. Devo admitir que fiquei na dúvida se este capítulo seria
necessário. Contudo, pensei que seria bom contextualizar toda a discussão agora
que, espero eu, você tem pelo menos as linhas gerais do escopo completo dos padrões deste livro.
Enquanto escrevo isso, estou inteiramente ciente das limitações do meu conse-

lho. Frodo disse em O Senhor dos Anéis: “Não peça conselhos aos Elfos, porque eles

dirão não e sim.” Embora eu não esteja reivindicando qualquer conhecimento imor-

tal, certamente entendo sua resposta de que conselhos são muitas vezes um presen-

te perigoso. Se estiver lendo isto para tomar decisões referentes à arquitetura do seu

projeto, pense que você sabe muito mais sobre seu projeto do que eu. Uma das maio-

res frustrações em ter bastante conhecimento sobre algo é que as pessoas muitas ve-

zes vêm a mim em uma conferência ou enviam uma mensagem pelo correio eletrô-

nico pedindo conselhos sobre suas decisões de processo ou arquitetura. Não há co-

mo você dar conselhos específicos baseado em uma descrição de cinco minutos. Es-

crevo este capítulo com ainda menos conhecimento do seu problema.

Assim, leia com o espírito com o qual ele é apresentado. Não conheço todas as

respostas e certamente não conheço suas questões. Use este conselho para estimular

sua reflexão, mas não o use em substituição à sua reflexão. No final, você tem que to-

mar suas próprias decisões e viver com elas.

Uma coisa boa é que suas decisões não têm de ficar para sempre gravadas em

pedra. A refatoração arquitetural é difícil, e ainda não conhecemos todos os seus cus-

tos, mas ela não é impossível. Aqui o melhor conselho que posso dar é que, mesmo
se você não gostar de nada da programação extrema [Beck XP], ainda assim deveria

106

Parte | é As NARRATIVAS

considerar seriamente três práticas técnicas: integração continua [Fowler CI], desen-

volvimento conduzido por testes [Beck TDD] e refatoração [Fowler Refactoring]. Essas técnicas não são uma panacéia, mas tornarão muito mais fácil mudar de idéia,

quando você descobrir que precisa fazê-lo. E você irá precisar, a menos que tenha
mais sorte ou mais habilidade do que qualquer pessoa que eu já tenha conhecido.

Começando com a Camada de Domínio
O começo do processo é decidir qual abordagem de lógica de domínio usar. Os três

principais competidores são o Roteiro de Transação (120), o Módulo Tabela (134) e o Modelo de Domínio (126).

Conforme ressaltei no Capítulo 2 (página 45-46), a maior força que o conduz

por este trio é a complexidade da lógica do domínio, algo totalmente impossível de
quantificar, ou até mesmo de qualificar, com algum grau de precisão. Contudo outros
fatores também influem na decisão, em particular a dificuldade de conexão com um

banco de dados.
O mais simples dos três padrões é o Roteiro de Transação (120). Ele se ajusta ao
modelo procedural com o qual a maioria das pessoas ainda se sente confortável.

Eficientemente encapsula a lógica de cada transação do sistema em um roteiro fa-

cilmente compreensível. Além disso, ele é fácil para construir um Roteiro de Transação sobre um banco de dados relacional. Sua maior falha é não tratar adequada-

mente a lógica de negócio complexa, sendo especialmente suscetível a código du-

plicado. Se você tiver apenas uma aplicação simples de catálogo, com pouco mais
do que um carrinho de compras rodando sobre uma estrutura básica de preços, então o Roteiro de Transação (120) será perfeitamente suficiente. Entretanto, à medida
que sua lógica ficar mais complicada, suas dificuldades se multiplicam.

No outro lado da escala está o Modelo de Domínio (126). Fanáticos radicais por

objetos, assim como eu, não enxergam uma aplicação de outra forma. Afinal, se uma

aplicação é simples o suficiente para que possa ser escrita com o Roteiro de Transação

(120), por que nossos imensos intelectos deveriam se incomodar com um problema
tão sem valor? Além disso, minha experiência me leva a não ter dúvidas de que na-

da pode lidar melhor com o inferno da lógica de domínio realmente complexa do

que um rico Modelo de Domínio (126). Uma vez que você tenha se acostumado a tra-

balhar com um Modelo de Dominio (126), até mesmo problemas simples podem ser
tratados com facilidade.
Ainda assim, o Modelo de Domínio (126) tem suas falhas. Nos primeiros lugares
da lista está a dificuldade de aprender a como usar um modelo de domínio. Fanáticos por objetos muitas vezes torcem seus narizes para pessoas que simplesmente não

conseguem entender objetos, mas a verdade é que, se for para ser bem feito, um Modelo de Dominio (126) requer habilidade - mal feito ele é um desastre. A segunda grande dificuldade de um Modelo de Domínio (126) é a sua conexão a um banco de dados

relacional. É claro que um verdadeiro fanático por objetos usa de artimanhas para re-

solver este problema com um empurrãozinho de um banco de dados orientado a ob-

jetos. Mas, por muitas razões, a maioria

delas não-técnicas,

um banco de dados

orientado a objetos não é uma escolha possível para aplicações corporativas. O resultado é uma conexão confusa com um banco de dados relacional. Vamos falar a verdade: modelos de objetos e modelos relacionais não se encaixam. O resultado é a

complexidade de muitos dos padrões de mapeamento O/R

que descrevo.

Capíruto 8 e JuntaNDO Tubo

107

O Módulo Tabela (134) representa um meio-termo interessante entre esses dois

pólos. Ele pode tratar melhor a lógica de domínio do que os Roteiros de Transação

(120). Além disso, embora não possa se comparar a um verdadeiro Modelo de Domi-

nio (126) para tratar lógica de domínio complexa, ele se encaixa verdadeiramente

bem com um banco de dados relacional — e muitas outras coisas também. Se você tiver um ambiente como o .NET, onde muitas ferramentas orbitam em torno do todo
poderoso Conjunto de Registros (473), então o Módulo Tabela (134) funciona muito bem,

aproveitando os pontos fortes dos bancos de dados relacionais e ainda representando uma razoável fatoração da lógica do domínio.

Analisando esse argumento, vemos que as ferramentas que você tem também

afetam sua arquitetura. Às vezes você pode escolher as ferramentas baseado na arquitetura e, em teoria, é isso o que você tem de fazer. Na prática, entretanto, muitas
vezes você tem que adaptar sua arquitetura às suas ferramentas. Dos três padrões, o
Módulo Tabela (134) é aquele cuja estrela mais brilha quando você tem ferramentas
que se adaptam a ele. É uma escolha particularmente apropriada para ambientes
-NET, já que tanto da plataforma é centrado ao redor do Conjunto de Registros (473).

Se você leu a discussão sobre a lógica do domínio no Capítulo 2, muito disso parecerá familiar. Ainda assim, vale a pena repetir essa discussão aqui, porque realmente penso que esta é a decisão central. Daqui, prosseguimos para a camada do banco

de dados, mas agora as decisões são moldadas pelo contexto da sua escolha da lógica do domínio.

Descendo para a Camada de Dados
Uma vez que você tenha escolhido sua camada de domínio, tem que descobrir como

conectá-la às suas fontes de dados. Suas decisões são baseadas na sua escolha da camada de domínio. Por esse motivo, abordarei essa questão em seções separadas, dirigidas por essa escolha.

Camada

de Dados para o Roteiro de Transação (120)
Os Roteiros de Transação (120) mais simples contêm sua própria lógica de banco de dados, mas eu evito isso mesmo nos casos mais simples. Separar o banco de dados de-

limita duas partes que fazem sentido separadas, de modo que faço a separação mes-

mo nas aplicações mais simples. Os padrões de bancos de dados a serem escolhidos
aqui são o Gateway de Linhas de Dados (158), e o Gateway de Tabela de Dados (151).

A escolha entre os dois depende muito dos recursos da sua plataforma de imple-

mentação e para onde você espera que a aplicação vá no futuro. Com um Gateway de

Linhas de Dados (158), cada registro é lido para um objeto com uma interface clara e explícita. Com um Gateway de Tabela de Dados (151), você pode ter menos código para escrever, uma vez que você não precisa de todo o código de acesso para chegar aos dados, mas você acaba com uma interface muito mais implícita que se baseia no acesso

a uma estrutura do tipo conjunto de registros que é pouco mais que um mapa.
A decisão-chave, entretanto, recai sobre o resto da sua plataforma. Se você tem
uma plataforma que fornece várias ferramentas que trabalham bem com um Conjunto de Registros (473), especialmente ferramentas de interface com o usuário ou conjuntos de registros transacionais desconectados, faz com que você se incline decididamente na direção de um Gateway de Tabela de Dados (151).

108

Parte | e As NARRATIVAS

Você normalmente não precisa de nenhum dos outros padrões de mapeamen-

to O/R neste contexto. As questões referentes ao mapeamento estrutural estão pra-

ticamente ausentes aqui uma vez que a estrutura em memória mapeia tão bem para a estrutura do banco de dados. Você poderia considerar a utilização de uma Uni-

dade de Trabalho (187), mas normalmente é fácil rastrear o que mudou no roteiro. Você não precisa se preocupar com a maior parte das questões relacionadas à concorrência, porque o roteiro freqiientemente corresponde quase que exatamente a uma

transação do sistema.Você pode, portanto, simplesmente encapsular todo o roteiro
em uma única transação. A exceção usual é quando uma requisição extrai dados
para editá-los, e a próxima requisição tenta gravar as alterações. Neste caso, o Bloqueio Offline Otimista (392) é quase sempre a melhor escolha. Ele não apenas é mais

fácil de implementar, como também geralmente satisfaz as expectativas dos usuários e evita o problema de uma sessão pendurada deixar todo tipo de recurso bloqueado.
Camada

de Dados para o Módulo Tabela (134)

A principal razão para se escolher um Módulo Tabela (134) é a presença de um bom
framework baseado em Conjuntos de Registros (473). Neste caso, você irá querer um pa-

drão de mapeamento de banco de dados que funcione bem com um Conjunto de Registros (473), e isso o leva inexoravelmente na direção de um Gateway de Tabela de Dados (151). Esses dois padrões se ajustam tão bem que parecem ter sido feitos um para
o outro.

De fato, com este padrão, não há mais nada que você precise acrescentar no la-

do da fonte de dados. Nos melhores casos, o Conjunto de Registros (473) tem algum tipo de mecanismo de controle de concorrência embutido, o que efetivamente o torna
uma Unidade de Trabalho (187), reduzindo ainda mais a perda de cabelos.

Camada

de Dados para o Modelo de Domínio (126)
Agora as coisas ficam interessantes. Por muitos motivos, a grande fraqueza do Modelo de Domínio (126) é que a conexão ao banco de dados é complicada. O grau de com-

plicação depende da complexidade deste padrão.
Se o seu Modelo de Domínio (126) for simples, digamos uma ou duas dúzias de

classes que sejam bastante semelhantes às tabelas no banco de dados, então um Registro Ativo (165) faz sentido. Se você quiser desacoplar um pouco as coisas, você po-

de usar para isso tanto um Gateway de Tabela de Dados (151) quanto um Gateway de Linha de Dados (158). Quer você separe, quer não, qualquer das alternativas não é mui-

to complicada.

Como as coisas se tornam mais complicadas, você precisará considerar a utilização de um Mapeador de Dados (170). Esta é a abordagem que promete manter seu
Modelo de Domínio (126) tão independente quanto possível de todas as outras cama-

das. Mas o Mapeador de Dados (170) é também o mais complicado padrão para implementar. A menos que você tenha uma equipe forte ou consiga encontrar algumas

simplificações que tornem o mapeamento mais fácil, eu recomendaria fortemente o
uso de uma ferramenta de mapeamento.

Uma vez que você tenha escolhido o Mapeador de Dados (170), a maioria dos pa-

drões na seção de mapeamento O/R

se aplicam. Em particular, recomendo de todo o

coração a Unidade de Trabalho (187), que funciona como um ponto central para o controle da concorrência.

Capiruo 8 e JuNTANDO Tubo

109

A Camada de Apresentação
Por diversos motivos, a apresentação é relativamente independente da escolha

das camadas mais baixas. Sua primeira questão é entre fornecer uma interface rica de cliente ou uma interface HTML em um browser. Um cliente rico lhe dará uma
interface com

o usuário mais bonita, mas, neste caso, você precisa de um

certo

controle sobre a distribuição de seus clientes. Minha preferência é usar um navegador HTML se for possível, e um cliente rico em caso contrário. Clientes ricos

normalmente demandarão um esforço maior de programação, mas isso ocorre

porque eles tendem a ser mais sofisticados, nem tanto devido às complexidades
inerentes à tecnologia.
Não explorei nenhum padrão de cliente rico neste livro, de modo que se você
escolher um, não tenho realmente nada mais a dizer.

Se você escolher o caminho HTML, tem que decidir como estruturar sua aplica-

ção. Certamente, recomendo o padrão Modelo Vista Controlador (315) como a fundação do seu projeto. Feito isso, você ainda tem duas decisões a tomar, uma para o con-

trolador e outra para a vista.
Suas ferramentas podem muito bem fazer a escolha para você. Se você usar o
Visual Studio, a maneira mais fácil é o Controlador de Página (318) e a Vista Padrão

(333). Se você usar Java, tem uma variedade de frameworks Web a considerar. No mo-

mento, o Struts é uma escolha popular, o que o levará a um Controlador Frontal (328)

ea uma Vista Padrão (333).

Se for possível uma maior liberdade na escolha, eu recomendaria um Controla-

dor de Página (318) se o seu site for mais orientado a documentos, especialmente se você tiver uma mistura de páginas estáticas e dinâmicas. A navegação e interface com
o usuário mais complexas levam à direção de um Controlador Frontal (328).

No lado da vista, a escolha entre a Vista Padrão (333) e a Vista de Transformação

(343) depende de se sua equipe usa páginas servidoras ou XSLT na programação. As

Vistas Padrão (333) têm no momento a dianteira, embora eu goste da facilidade de testes da Vista de Transformação (343). Se você tiver necessidade de mostrar um mesmo

site com múltiplas aparências e funcionalidades, deve considerar a utilização de uma
Vista em Duas Etapas (347).

O modo como você se comunica com as camadas inferiores depende do tipo de

camada e se elas estarão sempre no mesmo processo. Minha preferência é fazer com

que tudo rode em um único processo, se possível - desta maneira, você não tem que

se preocupar com as lentas chamadas interprocessos. Se você não puder fazer isso,
deve encapsular sua camada de domínio com uma Fachada Remota (368) e usar um

Objeto de Transferência de Dados (380) para se comunicar com o servidor Web.

Alguns Conselhos Específicos para Determinadas Tecnologias
Na

maior parte deste livro, tenho tentado trazer a experiência comum de criar proje-

tos em várias plataformas diferentes. A experiência com Forte, CORBA e Smalltalk se
traduz muito efetivamente no desenvolvimento com Java e .NET. A única razão pela

qual tenho me concentrado em ambientes Java e .NET é que eles parecem ser as plataformas mais comuns para o desenvolvimento de aplicações corporativas no futuro. (Ainda que eu fosse gostar se as linguagens de script dinamicamente tipadas, em

especial Python e Ruby, tivessem uma chance.)

110

Parte!

o As Narrativas

Nesta seção, quero aplicar os conselhos acima nestas duas plataformas. Tão lo-

go tenha feito isso, contudo, corro o risco de atribuir a mim mesmo um prazo de validade. As tecnologias mudam

muito mais rapidamente do que estes padrões, de

modo que enquanto você lê lembre-se de que estou escrevendo no início de 2002,

quando todos estão dizendo que a recuperação econômica chegará logo.
Java e J2EE

Atualmente o grande debate no mundo Java é exatamente o quão valiosos são os
Enterprise Java Beans. Depois de tantas “versões preliminares” finais quanto concertos de despedida do The Who, a especificação EJB 2.0 finalmente apareceu. Mas
você não precisa de EJB para criar uma boa aplicação J2EE, apesar do que os fornecedores EJB lhe dizem. Você pode fazer bastante com os velhos e bons objetos Java

(POJOs)* e JDBC.

As alternativas de projeto para J2EE variam em função dos padrões que você
estiver usando e, mais uma vez, começam pela lógica do domínio.

Se você usar um Roteiro de Transação (120) sobre alguma das formas de um Gate-

way (436), a abordagem usual com EJBs, atualmente, é usar session beans como um Ro-

teiro de Transação (120) e entity beans como um Gateway de Linha de Dados (158). Esta é

uma arquitetura bastante razoável se a sua lógica de domínio for suficientemente
modesta. Entretanto, um problema com tal abordagem usando beans é que é difícil se
livrar do servidor EJB se você descobrir que não precisa dele e não quiser gastar com
taxas de licenças.

A abordagem não-EJB consiste de um POJO para o Roteiro de Tran-

sação (120) sobre um Gateway de Linha de Dados (158) ou um Gateway de Tabela de Da-

dos (151). Se os conjuntos de linhas do JDBC 2.0 obtiverem maior aceitação, esta será
uma razão para usá-los como um Conjunto de Dados (473) e isso leva a um Gateway de
Tabela de Dados (151). Se você não estiver seguro sobre a utilização de EJBs, pode us-

ar a abordagem não-EJB e encapsular os entity beans com os session beans atuando como fachadas remotas (368).
Se você estiver usando um Modelo de Domínio (126), a ortodoxia corrente é usar

entity beans. Se o seu Modelo de Domínio (126) for bastante simples e tiver uma boa cor-

respondência com as tabelas no banco de dados, isso faz bastante sentido e seus en-

tity beans serão então Registros Ativos (165). Ainda é uma boa prática encapsular seus

entity beans com session beans agindo como Fachadas Remotas (368) (embora você tam-

bém possa pensar na Persistência Gerenciada pelo Conteiner (CMP**) como um Mapeador de Dados (170)). Entretanto, se o seu Modelo de Domínio (126) for mais complexo,

você irá querer que ele seja inteiramente independente da estrutura do EJB de modo

que você possa gravar, executar e testar sua lógica de domínio sem ter que lidar com

os caprichos do container EJB. Nesse contexto, eu usaria POJOs para o Modelo do Dominio (126) e os encapsularia com session beans agindo como Fachadas Remotas (368).
Se você escolher não usar EJBs, eu executaria toda a aplicação no servidor Web e evi-

taria chamadas remotas entre a apresentação e o domínio. Se você estiver usando um

Modelo de Domínio (126) POJO, eu também usaria POJOs para os Mapeadores de Dados

(170) - seja usando uma ferramenta para o mapeamento O/R, seja fazendo eu mesmo, se eu achasse que conseguiria.

* N.deR:

lain and Old Java Objects.

** N. de R.T.: Container Managed Persistence.

Capiruo 8 e JuntaNDO Tubo

111

Se você usar entity beans em qualquer contexto, evite dar a eles uma interface re-

mota. Nunca entendi o motivo de dar a um entity bean uma interface remota. Os entity beans são normalmente usados como Modelos de Domínio (126) ou como Gateways
de Linhas de Dados (158). Em ambos os casos, eles precisam de uma interface de granularidade baixa para executar bem esses papéis. No entanto, como espero ter intro-

duzido em sua mente, uma interface remota deve sempre ter uma granularidade alta. Por esse motivo, mantenha seus entity beans apenas como locais. (A exceção a isso
é o padrão Entity Composto de [Alur et al.], o qual é uma forma diferente de usar entity beans e que, ainda assim, não me parece muito útil.)

No presente momento, o Módulo Tabela (134) não é muito comum no mundo Ja-

va. Será interessante ver se mais ferramentas surgirão em torno do conjunto de linhas
(row set) JDBC — se isso acontecer, este padrão pode se tornar uma abordagem viável.

Neste caso, a abordagem POJO se adapta melhor, embora você também possa encap-

sular o Módulo Tabela (134) com session beans atuando como Fachadas Remotas (368) e

retornando Conjuntos de Registros (473).

«NET
Observando o .NET, o Visual Studio e a história do desenvolvimento de aplicações

no mundo Microsoft, o padrão dominante é o Módulo Tabela (134). Ainda que os faná-

ticos por objetos costumem dizer que isso significa apenas que os seguidores da Mi-

crosoft não conhecem orientação a objetos, o fato é que o Módulo Tabela (134) oferece
um meio-termo valioso entre um Roteiro de Transação (120) e um Modelo de Domínio

(126), com um impressionante conjunto de ferramentas que tiram proveito do onipresente conjunto de dados atuando como um Conjunto de Registros (473).
Aconseqiiéncia disso é que o Módulo Tabela (134) tem que ser a escolha padrão

para esta plataforma. Em verdade, não vejo sentido algum em usar Roteiros de Tran-

sação (120), exceto nos casos mais simples e, mesmo assim, eles deveriam ser bem-

comportados e retornar conjuntos de dados.

Isso não significa que você não possa usar um Modelo de Domínio (126). Com cer-

teza, você pode construir um Modelo de Domínio (126) tão facilmente em .NET quan-

to em qualquer outro ambiente OO. Todavia, as ferramentas não lhe dão a ajuda extra que elas dão em Módulos Tabela (134), de modo que eu toleraria uma complexida-

de maior antes de sentir a necessidade de mudar para um Modelo de Domínio (126).

Existe atualmente uma publicidade excessiva referente ao uso de serviços Web

em .NET. No entanto, eu não usaria serviços Web dentro de uma aplicação. Eu os
usaria, assim como em Java, na camada de apresentação para permitir a integração

de aplicações. Não existe nenhuma boa razão em uma aplicação .NET para separar o
servidor Web e a lógica do domínio em processos distintos. Desse modo, a Fachada
Remota (368) é menos útil aqui.

Procedimentos Armazenados
Ocorrem normalmente uma quantidade razoável de discussões a respeito de procedimentos armazenados. Freqiientemente, eles são o modo mais rápido de fazer as
coisas, uma vez que eles rodam no mesmo processo do seu banco de dados e, desta
forma, reduzem as preguiçosas chamadas remotas. Entretanto, a maioria dos ambientes de procedimentos armazenados não lhe fornece bons mecanismos para a es-

truturação de seus procedimentos armazenados. Além disso, os procedimentos ar-

112

Parte | é As NARRATIVAS

mazenados o manterão preso a um vendedor de banco de dados específico. (Uma

boa maneira de evitar estes problemas é a abordagem Oracle de permitir que você rode aplicações Java dentro do processo do seu banco de dados. Isso é o equivalente a
colocar toda sua camada de lógica do domínio dentro do banco de dados. Por en-

quanto, isso ainda o deixa um pouco amarrado a um vendedor, mas pelo menos re-

duz os custos de portar o banco de dados para um outro gerenciador.)

Por questões de modularidade e portabilidade, muitas pessoas evitam usar pro-

cedimentos armazenados para a lógica de negócio. Tendo a me alinhar com esta vi-

são a menos que haja um grande ganho de desempenho a ser obtido, o que, para ser
sincero, frequentemente ocorre. Nesse caso, pego um método da camada do domínio

e, alegremente, o transformo em um procedimento armazenado. Faço isso apenas em
áreas com claros problemas de desempenho, tratando-o como um passo da otimiza-

ção, e não como um princípio arquitetural. ([Nilsson] apresenta um bom argumento

a favor do uso de procedimentos armazenados mais extensamente.)
Um modo comum de usar procedimentos armazenados é para controlar o acesso a um banco de dados, segundo as diretrizes de um Gateway de Tabelas de Dados

(151). Não tenho nenhuma opinião fechada quanto a fazer isso ou não, e, pelo que te-

nho visto, não há fortes argumentos de nenhum dos lados. De todo modo, prefiro us-

ar os mesmos padrões para isolar o acesso ao banco de dados, quer este seja contro-

lado por procedimentos armazenados ou por SQL normal.

Serviços Web
Quando escrevo isso, o consenso entre os especialistas é o de que os serviços Web
farão da reutização uma realidade e tirarão os integradores de sistemas do mercado, mas não estou assim tão entusiasmado. Os serviços Web não desempenham um

papel muito importante na utilização destes padrões, porque sua finalidade é a in-

tegração de aplicações, e não a construção das mesmas. Você não deveria tentar separar uma aplicação única em serviços Web que conversem entre si, a menos que

você realmente precise fazê-lo. Em vez disso, construa sua aplicação e exponha as
diversas partes dela como serviços Web, tratando esses serviços como Fachadas Re-

motas (368). Acima de tudo, não deixe que todo o burburinho sobre o quão fácil é

criar serviços Web faça você esquecer a Primeira Lei do Projeto de Objetos Distri-

buidos (página 100-101).

Embora a maioria dos exemplos publicados que eu tenha visto use serviços

Web de forma síncrona, em vez de usá-los como uma chamada RPC XML, prefiro

usá-los de forma assíncrona e baseados em mensagens. Embora não apresente aqui

nenhum padrão para isso (este livro já é grande o suficiente do jeito que está), espe-

ro que possamos ver nos próximos anos alguns padrões para a troca assincrona de
mensagens.

Outros Esquemas de Camadas
Construí minha discussão em torno de três camadas principais, mas a minha abordagem para a criação de camadas não é a única que faz sentido. Outros bons livros sobre arquitetura têm esquemas de camadas, e todos eles têm valor. Vale a pena olhar
esses outros esquemas e compará-los àqueles que apresento aqui. Você pode achar

que eles fazem mais sentido para a sua aplicação.

Capiruo 8 e JuntaNDO Tubo

113

O primeiro destes padrões é o que chamarei de modelo de Brown, discutido em

[Brown et al.] (veja a Tabela 8.1). Esse modelo tem cinco camadas: apresentação, controlador/mediador, domínio, mapeamento de dados e fonte de dados. Basicamente

ele coloca camadas adicionais de mediação entre as três camadas básicas. O contro-

lador/mediador realiza a mediação entre as camadas de apresentação e domínio, en-

quanto que a camada de mapeamento de dados realiza a mediação entre a camada

de domínio e a camada da fonte de dados.

Acho que as camadas de mediação são úteis durante parte do tempo mas não
durante todo o tempo, de forma que as descrevo em termos de padrões. O Controla-

dor de Aplicação (360) é o mediador entre a apresentação e o domínio, e o Mapeador de

Dados (170) é o mediador entre a camada de dados e o domínio. Para organizar este

livro, descrevi o Controlador de Aplicação (360) na seção da apresentação (Capítulo 14)
e o Mapeador de Dados (170) na seção da camada de dados (Capitulo 10).
Para mim então, a adição das fregientemente (mas nem sempre) úteis camadas

de mediação, representa um suplemento opcional no projeto. Minha abordagem é
sempre pensar nas três camadas básicas, verificar se alguma delas está se tornando
muito complexa e, em caso afirmativo, acrescentar a camada de mediação para sepa-

rar a funcionalidade.

Outro bom esquema de camadas para J2EE aparece nos padrões CoreJ2EE

[Alur et al.) (veja a Tabela 8.2). Aqui as camadas são: cliente, apresentação, negócio,

integração e recursos. Correspondências simples existem para as camadas de negó-

cio e integração. A camada de recursos compreende os serviços externos aos quais a
camada de integração se conecta. A principal diferença é que eles separam a camada
de apresentação entre a parte que roda no cliente (cliente) e a parte que roda em um

servidor (apresentação). Essa separação é muitas vezes útil, mas, novamente, ela não
é necessária durante todo o tempo.

A arquitetura Microsoft DNA

[Kirtland] define três camadas: apresentação,

negócio e acesso a dados, que correspondem quase que diretamente às três cama-

das que uso aqui (veja a Tabela 8.3). A maior mudança ocorre no modo pelo qual os

dados são passados das camadas de acesso a dados. No Microsoft DNA, todas as
camadas operam sobre conjuntos de registros que resultam de consultas SQL exe-

cutadas pela camada de acesso a dados. Isso introduz um aparente acoplamento,

uma vez que, tanto a camada de negócio quanto a de apresentação conhecem o

banco de dados.

O modo como vejo isso é que no DNA o conjunto de registros age como um
Objeto de Transferência de Dados (380) entre as camadas. A camada de negócio pode
modificar o conjunto de registros durante o trajeto deste para a camada de apresen-

tação ou mesmo ela própria criar um conjunto de registros (o que é raro). Embora

Tabela 8.1

As Camadas de Brown

Brown

Fowler

Apresentação

Apresentação

Fonte de dados

Fonte de dados

Controlador/mediador
Domínio
Mapeamento de dados

Apresentação (Controlador de Aplicação (260))
Domínio
Fonte de dados (Mapeador de Dados (170))

114

Parte | é As NARRATIVAS
Tabela 8.2

Camadas CoreJ2EE

CoreJ2EE

Fowler

Cliente

Apresentação que roda no cliente (p.ex, sistemas de clientes ricos)

Apresentação

Apresentação que roda no servidor (p.ex., processos que tratam

requisições HTTP, páginas servidoras)
Domínio
Fonte de Dados
Recursos externos com os quais a fonte de dados se comunica

Negócio
Integração
Recursos

Tabela 8.3

Camadas Microsoft DNA

Microsoft DNA
Apresentação
Negócio
Acesso a dados

Fowler
Apresentação
Domínio
Fonte de Dados

esta forma de comunicação seja, por muitos motivos, incômoda, ela tem a grande
vantagem de permitir que a apresentação use controles ligados aos dados (data

aware) na interface com o usuário, mesmo com dados que tenham sido modificados

pela camada de negócio.

Neste caso, a camada

de domínio

é estruturada na forma

de Módulos

Tabela

(134), e a camada de dados usa Gateways de Tabelas de Dados (151).

[Marinescu] tem cinco camadas (veja a Tabela 8.4). A apresentação é dividida

em duas camadas, refletindo a separação de um Controlador de Aplicação (380). O domínio é também dividido, com uma Camada de Serviço (141) construída sobre um Modelo de Dominio (126), refletindo o senso comum de dividir uma camada de domínio
em duas partes. Esta é uma abordagem comum, reforçada pelas limitações de EJB co-

mo um Modelo de Domínio (126) (veja a página 127-128).

A idéia de separar uma camada de serviços de uma camada de domínio é baseada na separação da lógica do fluxo de trabalho da lógica de domínio pura. A ca-

mada de serviços tipicamente inclui lógica específica de um único caso de uso e também alguma comunicação com outras infra-estruturas, como a de mensagens. Ter ou
não camadas separadas de serviço e de domínio é motivo de algum debate. Tendo a

encarar isso como algo ocasionalmente útil, em vez de obrigatório, mas vários projetistas que respeito não concordam comigo.

[Nilsson] usa um dos esquemas de camadas mais complexos (veja a Tabela 8.5).

O mapeamento para este esquema de camadas se torna um pouco mais complexo

pelo fato de Nilsson usar extensivamente procedimentos armazenados e encorajar,
por motivos de desempenho, a inclusão de lógica de domínio neles. Não me sinto
confortável em colocar lógica de domínio em procedimentos armazenados, porque
isso pode tornar uma aplicação muito mais difícil de manter. Ocasionalmente, entre-

tanto, essa é uma técnica de otimização valiosa. As camadas de procedimentos armazenados de Nilsson contêm tanto a fonte de dados quanto a lógica do domínio.

