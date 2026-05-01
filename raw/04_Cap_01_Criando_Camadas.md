# 04_Cap_01_Criando_Camadas

CAPÍTULO

Criando Camadas

criação de camadas é uma das técnicas mais comuns que os projetistas de
software usam para quebrar em pedaços um sistema complexo de software.
Você encontra esta técnica em arquiteturas de máquinas, em que as camadas
descendem de uma linguagem de programação com chamadas do sistema operacional aos drivers de dispositivos e conjuntos de instruções da CPU, e às portas lógicas

dentro de chips. Redes de computadores têm FTP como uma camada sobre TCP, o
qual, por sua vez, está sobre o IP, que está sobre a ethernet.

Ao pensar em um sistema em termos de camadas, você imagina os subsistemas
principais no software dispostos de forma parecida com camadas de um bolo, em que
cada camada repousa sobre uma camada mais baixa. Nesse esquema, a camada mais
alta usa vários serviços definidos pela camada mais baixa, mas a camada mais baixa

ignora a existência da camada mais alta. Além disso, cada camada normalmente es-

conde suas camadas mais baixas das camadas acima, então a camada 4 usa os serviços da camada 3, a qual usa os serviços da camada 2, mas a camada 4 ignora a existência da camada 2. (Nem todas as arquiteturas de camadas são opacas como essa,
mas a maioria é.)
Dividir um sistema em camadas tem uma série de benefícios importantes:

* Você pode compreender uma única camada como um todo coerente sem saber muito sobre as outras camadas. Você pode compreender como construir
um serviço FTP sobre TCP sem conhecer os detalhes de como funciona o protocolo ethernet.

* Você pode substituir camadas por implementações alternativas dos mesmos
serviços básicos. Um serviço FTP pode rodar sem modificações sobre ether-

net, PPP ou seja lá o que o for usado pelo provedor.

* Você minimiza as dependências entre as camadas. Se o provedor alterar o sistema físico de transmissão, não precisamos alterar nosso serviço FTP.

38

Parte!

e As NARRATIVAS

* Camadas são bons lugares para padronização. TCP e IP são padrões porque

eles definem como suas camadas devem operar.

* Uma vez que você tenha construído uma camada, ela pode ser usada por
muitos serviços de nível mais alto. Desta forma, TCP/IP é usado por FTP, tel-

net, SSH e HTTP. De outra maneira, todos esses protocolos de nível mais al-

to teriam que escrever seus próprios protocolos de nível mais baixo.

O uso de camadas é uma técnica importante, mas há aspectos negativos:
* As camadas encapsulam bem algumas coisas, mas não todas. Isso, às vezes,

resulta em alterações em cascata. O exemplo clássico disto em uma aplicação
corporativa em camadas é o acréscimo de um campo que precise ser mostra-

do na interface com o usuário e deva estar no banco de dados e assim deva
também ser acrescentado a cada camada entre elas.

e Camadas extras podem prejudicar o desempenho. Em cada camada os dados precisam, tipicamente, ser transformados de uma representação para
outra. O encapsulamento de uma função subjacente, no entanto, muitas ve-

zes lhe dá ganhos de eficiência que mais do que compensam esse problema.
Uma camada que controla transações pode ser otimizada e então tornará
tudo mais rápido.

Contudo, a parte mais difícil de uma arquitetura em camadas é decidir quais ca-

madas são necessárias e quais as responsabilidades que cada uma deve receber.
A Evolução das Camadas nas Aplicações Corporativas

Embora eu seja jovem demais para ter feito algum trabalho nos velhos tempos dos
sistemas em batch, não sinto que as pessoas pensassem muito em camadas naquele

tempo. Você escrevia um programa que manipulava algum tipo de arquivo (ISAM,

VSAM, etc.) e essa era sua aplicação. Não era necessário aplicar nenhuma camada.

A noção de camadas se tornou mais visível nos anos 1990, com o advento dos

sistemas cliente-servidores. Estes eram sistemas em duas camadas: o cliente manti-

nha a interface com o usuário e um ou outro código da aplicação, e o servidor era
normalmente um banco de dados relacional. Ferramentas comuns para o lado clien-

te eram, por exemplo, VB, Powerbuilder e Delphi. Essas ferramentas tornaram particularmente fácil criar aplicações que faziam uso intensivo de dados, uma vez que

elas disponibilizavam componentes visuais que trabalhavam com SQL. Assim, você
podia criar uma tela arrastando controles para uma área de desenho e então usando

páginas de propriedades para conectar os controles ao banco de dados.

Se a aplicação tivesse somente de exibir e fazer atualizações simples em dados

relacionais, então esses sistemas cliente-servidor funcionavam muito bem. O problema surgiu com a lógica de domínio: regras de negócio, validações, cálculos, e assim
por diante. Normalmente, as pessoas escreviam essa lógica no cliente, mas isso era

desajeitado e, normalmente, feito embutindo-se a lógica diretamente nas telas da interface com o usuário. À medida que a lógica do domínio se tornava mais complexa,

ficava muito difícil trabalhar com este código. Além disso, embutir lógica nas telas

Capíruto 1 e CRIANDO CAMADAS

39

facilitava a duplicação de código, o que significava que alterações simples resulta-

vam em buscas de código semelhante em muitas telas.

Uma alternativa era colocar a lógica de domínio no banco de dados na forma de

procedimentos armazenados (stored procedures). Estes, no entanto, forneciam meca-

nismos limitados de estruturação, o que mais uma vez levava a código desajeitado.

Além disso, muitas pessoas gostavam de bancos de dados relacionais porque SOL

era um padrão, o que lhes permitia a qualquer tempo mudar o fornecedor do banco

de dados. Ainda que poucas pessoas realmente fizessem isso, muitas gostavam de
ter a opção de mudar de fornecedor sem que isso implicasse em custos de migração

altos demais. Por serem todos proprietários, os procedimentos armazenados eliminavam essa opção.

Ao mesmo tempo em que a arquitetura cliente-servidor estava ganhando popu-

laridade, o mundo orientado a objetos estava ascendendo. A comunidade de objetos
tinha a resposta para o problema da lógica de domínio: migrar para um sistema em
três camadas. Nesta abordagem, você tinha uma camada de apresentação para a sua

interface com o usuário, uma camada de domínio para a sua lógica de domínio e
uma camada de dados. Desta maneira, você poderia tirar toda a complexa lógica de
domínio da interface com o usuário e colocá-la em uma camada na qual você pode-

ria estruturá-la apropriadamente utilizando objetos.

Apesar disso, a popularidade dos objetos fez pouco progresso. A verdade era
que muitos sistemas eram simples, ou pelo menos começavam simples. Embora a
abordagem em três camadas tivesse muitos benefícios, o ferramental para o desenvolvimento cliente-servidor era convincente se o seu problema fosse simples. Além
disso, as ferramentas para o desenvolvimento cliente-servidor eram difíceis, ou mes-

mo impossíveis, de usar em uma configuração com três camadas.

Acho que o abalo sismico aqui foi o advento da Web. De repente as pessoas passaram a querer instalar aplicações cliente-servidor usando um navegador Web. No
entanto, se toda a sua lógica de negócio estivesse enterrada em um cliente rico, então

toda ela precisaria ser refeita para ter uma interface Web. Um sistema bem-projetado,
em três camadas, poderia simplesmente acrescentar uma nova camada de apresentação e estaria pronto. Além disso, com a linguagem Java, vimos uma linguagem

orientada a objetos ocupar a cena sem pudores. As ferramentas que surgiram para
criar páginas Web eram muito menos amarradas à linguagem SQL e, assim, mais
adaptáveis a uma terceira camada.
Quando as pessoas discutem a criação de camadas, frequentemente há confusão entre os termos layer e tier. Muitas vezes os dois são usados como sinônimos, mas

a maioria das pessoas considera que o termo tier implica uma separação física. Os sis-

temas cliente-servidor são frequentemente descritos como sistemas em duas camadas (two-tier systems), e a separação é física: o cliente é um desktop e o servidor é um

servidor. Uso layer para enfatizar o fato de que você não tem que rodar as camadas
em máquinas diferentes. Uma camada distinta de lógica de domínio muitas vezes
pode rodar tanto em um desktop como no servidor de banco de dados. Nessa situação
você tem dois nodos, mas três camadas distintas. Com um banco de dados local, pos-

so executar todas as três camadas em um único laptop, mas ainda assim haverá três
camadas distintas.

40

Parte!

e As NARRATIVAS

As Trés Camadas Principais
Neste livro estou centralizando minha discussão em torno da arquitetura contendo

três camadas principais: apresentação, domínio e fonte de dados. (Estou seguindo os

nomes usados em [Brown et al.]). A Tabela 1.1 resume estas camadas.

A lógica de apresentação diz respeito a como tratar a interação entre o usuário

e o software. Isso pode ser tão simples quanto uma linha de comando ou um menu

baseado em texto, porém, hoje é mais provável que seja uma interface gráfica em um

cliente rico ou um navegador com interface baseada em HTML. (Neste livro, uso
cliente rico significando uma interface com o usuário ao estilo Windows ou Swing,

em vez de um navegador HTML.) As responsabilidades primárias da camada de
apresentação são exibir informações para o usuário e traduzir comandos do usuário
em ações sobre o domínio e a camada de dados.
A lógica da camada de dados diz respeito à comunicação com outros sistemas
que executam tarefas no interesse da aplicação. Estes podem ser monitores de transações, outras aplicações, sistemas de mensagens e assim por diante. Para a maioria
das aplicações corporativas, a maior parte da lógica de dados é um banco de dados

responsável, antes de mais nada, pelo armazenamento de dados persistentes.
O

resto é a lógica de domínio, também chamada de lógica de negócio. Este é

o trabalho que esta aplicação tem de fazer para o domínio com o qual você está tra-

balhando. Envolve cálculos baseados nas entradas e em dados armazenados, validação de quaisquer dados provenientes da camada de apresentação e a compreensão exata de qual lógica de dados executar, dependendo dos comandos recebidos
da apresentação.

Às vezes as camadas são organizadas de modo que a camada de domínio es-

conda completamente a camada de dados da camada de apresentação. Com maior

freqiiéncia, contudo, a apresentação acessa diretamente o armazenamento de dados.

Embora isso seja menos puro, tende a funcionar melhor na prática. A apresentação
pode interpretar um comando do usuário, usar a camada de dados para trazer os da-

dos relevantes do banco de dados e então deixar a lógica de domínio manipular esses dados antes de apresentá-los na tela.

Uma única aplicação frequentemente pode ter múltiplos pacotes de cada uma

dessas três áreas. Uma aplicação, projetada para ser manipulada não apenas por
usuários finais por meio de uma interface de cliente rico, mas também por meio de

uma linha de comando teria duas apresentações: uma para a interface de cliente rico
e uma para a linha de comando. Múltiplos componentes de dados podem estar presentes para bancos de dados diferentes, mas, supostamente, estariam presentes par-

Tabela 1.1

Três Camadas Principais

Camada

Responsabilidades

Apresentação

Fornecimento de serviços, exibição de informações (p. ex., em Windows ou
HTML, tratamento de solicitações do usuário (cliques com o mouse,
pressionamento de teclas), requisições HTTP, chamadas em linhas de
comando, API em lotes)

Domínio
Fonte de Dados

Lógica que é o real propósito do sistema
Comunicação com os bancos de dados, sistemas de mensagens,
gerenciadores de transações, outros pacotes

CapiTuLo 1 e CRIANDO CAMADAS

41

ticularmente para a comunicação com os pacotes existentes. Mesmo o domínio pode

ser quebrado em áreas distintas relativamente separadas umas das outras. Certos pacotes de dados só podem ser usados por determinados pacotes do domínio.

Até agora falei sobre um único usuário. Isso naturalmente levanta a questão do

que acontece quando não há um ser humano guiando o software. Pode ser algo novo
e moderno como um serviço Web ou algo banal e útil como um processo em lote.
Neste último caso, o usuário é o programa cliente. Neste ponto torna-se evidente que

há muita semelhança entre as camadas de apresentação e de dados visto que ambas

são relacionadas à conexão com o mundo externo. Esta é a lógica por trás do padrão

Arquitetura Hexagonal [wiki] de Alistair Cockburn, que enxerga qualquer sistema como um núcleo cercado de interfaces para sistemas externos. Na Arquitetura Hexago-

nal, tudo que é externo ao sistema é tratado essencialmente como uma interface externa. Consiste, portanto, em uma visão simétrica ao invés do meu esquema assimé-

trico de camadas.

No entanto, considero essa assimetria útil, porque há uma boa distinção a ser

feita entre uma interface que você fornece como um serviço para outros e o serviço
que você utiliza de outra pessoa qualquer. No fundo, esta é a distinção real que faço
entre a apresentação e a camada de dados. A apresentação é uma interface externa

para um serviço que o seu sistema oferece a outra pessoa, seja um humano comple-

xo ou um programa remoto simples. A camada de dados é a interface para coisas que

estão provendo um serviço para você. É benéfico pensar nelas de forma diferente,
porque a diferença nos clientes altera a maneira de pensar sobre o serviço.

Embora possamos identificar as três camadas usuais, apresentação, domínio e

fonte de dados, em cada aplicação corporativa, a maneira como você as separa depende do quão complexa é a aplicação. Um roteiro simples para extrair dados de um
banco de dados e exibi-los em uma página Web pode, todo ele, consistir de um úni-

co procedimento. Eu ainda me esforçaria para separar as três camadas, mas, neste ca-

so, poderia fazê-lo simplesmente colocando o comportamento de cada camada em

sub-rotinas separadas. Se o sistema se tornasse mais complexo, quebraria as três camadas em classes separadas. Se a complexidade aumentasse ainda mais, dividiria as
classes em pacotes separados. Meu conselho geral é escolher a forma mais apropriada de separação para o seu problema mas estar seguro de que você tenha algum tipo

de separação — ao menos ao nível de sub-rotinas.

Junto com a separação, há também uma regra estabelecida sobre dependén-

cias: o domínio e a camada de dados nunca devem ser dependentes da apresenta-

ção, isto é, não deve haver chamadas de sub-rotinas da apresentação a partir do có-

digo do domínio ou da camada de dados. Essa regra torna mais fácil utilizar apresentações diferentes sobre a mesma base e torna mais fácil modificar a apresenta-

ção sem ramificações sérias mais abaixo. O relacionamento entre o domínio e a camada de dados é mais complexo e depende dos padrões arquiteturais usados para

a fonte de dados.

Uma das partes mais difíceis de trabalhar com lógica de domínio parece ser que

as pessoas frequentemente acham difícil reconhecer o que é lógica de domínio e o

que são outras formas de lógica. Um teste informal que gosto de aplicar é imaginar a
adição de uma camada radicalmente diferente a uma aplicação, como uma interface
em linha de comando a uma aplicação Web. Se, para fazer isso, você tiver que duplicar alguma funcionalidade, é um sinal de que a lógica de domínio vazou para a apresentação. De maneira semelhante, você tem que duplicar lógica para substituir um

banco de dados relacional por um arquivo XML?

42

Parte | e As NARRATIVAS

Falaram-me certa vez sobre um sistema que era um bom exemplo disso: ele

continha uma lista de produtos em que todos que tivessem um acréscimo de vendas

de 10% ou mais, em relação ao més anterior, eram coloridos de vermelho. Para fazer

isso, os desenvolvedores colocaram lógica na camada de apresentação que comparava as vendas do mês atual com as do mês anterior e, se a diferença fosse maior do
que 10%, eles a coloriam de vermelho.

O problema é que essa solução coloca lógica de domínio na apresentação. Para
separar corretamente as camadas, você precisa de um método na camada de domí-

nio para indicar se um produto aumentou suas vendas. Este método faz a compara-

ção entre os dois meses e retorna um valor booleano. A camada de apresentação então, simplesmente chama este método booleano e, se o valor retornado é verdadeiro,

destaca o produto em vermelho. Desta forma o processo é dividido em suas duas
partes: decidir se há algo a ser destacado e decidir como destacá-lo.
Preocupo-me a estar sendo demasiadamente categórico a esse respeito. Ao rever este livro, Alan Knight comentou que ele estava “em dúvida se colocar lógica do

negócio na interface com o usuário é o primeiro passo em direção a uma rampa escorregadia para o inferno ou uma coisa perfeitamente razoável a fazer a que apenas
um purista categórico faria objeção”. A razão pela qual nos sentimos desconfortáveis
é porque ambos são verdadeiros!

Escolhendo Onde Rodar suas Camadas
Na maior parte deste livro estarei falando sobre camadas lógicas — isto é, a divisão de
um sistema em pedaços separados para reduzir o acoplamento entre as diferentes

partes do sistema. A separação entre camadas é útil mesmo se todas as camadas estiverem rodando na mesma máquina física. Entretanto, há lugares onde a estrutura física de um sistema faz diferença.

Na maioria das aplicações de sistemas de informação, a decisão diz respeito a rodar
o processamento em um cliente, em uma máquina desktop, ou em um servidor.

Muitas vezes, a solução mais simples é rodar tudo em servidores. Um front end*

HTML usando um navegador Web é uma boa maneira de fazer isso. A grande vanta-

gem de rodar no servidor é que, por estar em uma quantidade limitada de lugares, tudo é fácil de atualizar e reparar. Você não tem que se preocupar com a distribuição para muitos desktops e em mantê-los todos em sincronia com o servidor. Você também não
tem que se preocupar com compatibilidades com outros softwares de desktop.
O argumento geral a favor da execução em um cliente gira em torno da capaci-

dade de resposta ou operação desconectada. Qualquer lógica que rode no servidor
precisa de uma viagem de ida e volta ao servidor para responder a qualquer coisa

que o usuário faça. Se esse usuário quiser brincar com alguma coisa e receber retor-

no imediato, essa ida e volta atrapalha. Ela também precisa de uma conexão de rede
para rodar. A rede pode estar por toda parte, mas, enquanto digito isso, ela não está

a 10.000 metros. Em breve, poderá estar em todo lugar, mas há pessoas que querem

trabalhar agora, sem ter de esperar que a cobertura sem fios chegue a lugares muito
distantes. As operações desconectadas trazem desafios específicos, e lamento tê-los

colocado fora do escopo deste livro.

* N.deR.T: Parte do programa responsável pela interface com o usuário.

Capiruto 1 e CRIANDO CAMADAS

43

Com essas forças gerais posicionadas, podemos considerar as opções, camada

por camada. A camada de dados quase sempre roda em servidores. A exceção são as
situações em que você poderia duplicar a funcionalidade do servidor em um cliente
convenientemente poderoso, geralmente quando você quer uma operação desconectada. Neste caso, as alterações na fonte de dados do cliente desconectado precisam

ser sincronizadas com o servidor. Como mencionei anteriormente, decidi deixar essas questões para outro dia — ou outro autor.

A decisão sobre onde rodar a apresentação depende, em sua maior parte, do tipo de interface de usuário que você quer. Um cliente rico implica, quase certamente,
em rodar a apresentação no cliente. Uma interface Web implica, quase certamente,
em rodá-la no servidor. Existem exceções — por exemplo, a operação remota de um

software cliente (tais como servidores X no mundo Unix) rodando um servidor Web
no desktop — mas estas exceções são raras.
Se você estiver criando um sistema B2C, você não tem escolha. Qualquer usuário, Tom, Dick ou Harriet, pode estar se conectando aos seus servidores, e você não

quer mandar ninguém embora porque eles insistem em fazer suas compras online
com um TRS-80. Neste caso, você faz todo o processamento no servidor e devolve

HTML para o browser manipular. A limitação da opção HTML é que cada pedaço de
uma tomada de decisão precisa de uma viagem de ida e volta do cliente para o servi-

dor, e isso pode diminuir a capacidade de resposta. Você pode reduzir parte do atra-

so com scripts no navegador e applets, mas eles reduzem a compatibilidade do seu na-

vegador e tendem a acrescentar outras dores de cabeça. Quanto mais puro for seu

HTML, mas fácil será sua vida.

Esta vida fácil é atraente mesmo se cada uma das suas áreas de trabalho for

construída à mão, com todo o carinho, pelo seu departamento de sistemas de infor-

mação. Manter clientes atualizados e evitar erros de compatibilidade com outros soft-

wares são problemas que mesmo os mais simples sistemas baseados em clientes ricos
têm.

A razão principal pela qual as pessoas desejam uma apresentação baseada em
clientes ricos é que algumas tarefas são complicadas para os usuários realizarem e,
nesses casos, para ter uma aplicação amigável, eles precisarão de mais do que uma
interface Web pode dar. Cada vez mais, entretanto, as pessoas estão se acostumando

às técnicas para construir front ends Web mais amigáveis, e isso reduz a necessidade

de uma apresentação em um cliente rico. Hoje, enquanto escrevo este livro, sou muito favorável à apresentação Web, se você puder; e ao cliente rico, se você precisar.

Isso nos deixa com a lógica do domínio. Você pode rodar toda a lógica de negócio no servidor, toda ela no cliente, ou você pode dividi-la. Uma vez mais, toda a ló-

gica no servidor é a melhor escolha para facilitar a manutenção. A motivação em mo-

vê-la para o cliente visa a melhorar a capacidade de resposta ou possibilitar o uso

desconectado.
Se você tiver que rodar alguma lógica no cliente, deve considerar a possibilidade de executá-la toda lá — pelo menos assim ela fica toda em um único lugar.
Normalmente esse cenário ocorre de mãos dadas com um cliente rico — rodar um

servidor Web em uma máquina cliente não irá ajudar muito a capacidade de res-

posta, embora possa ser um modo de tratar operações desconectadas. Neste caso,

você ainda pode manter sua lógica do domínio em módulos separados da apresen-

tação, com um Roteiro de Transação (110) ou um Modelo do Domínio (116). O proble-

ma em colocar toda a lógica do domínio no cliente é que você tem mais coisas para
atualizar e manter.

44

Parte | é As NARRATIVAS

Dividir a lógica de domínio entre o desktop e o servidor parece ser o pior de dois

mundos, porque você não sabe onde cada parte da lógica vai estar. O motivo princi-

pal para fazer isso é se apenas uma pequena parte da lógica precisar rodar no clien-

te. O truque então é isolar esta parte da lógica em um módulo que não seja depen-

dente de nenhuma outra parte do sistema. Dessa forma, você pode rodar esse módu-

lo no cliente ou no servidor. Isso irá requerer uma boa quantidade de código malicio-

so, mas é um bom modo de realizar esse trabalho.

Uma vez que você tenha escolhido seus nós de processamento, você deve ten-

tar manter todo o código em um único processo, seja em um único nó ou copiado em

diversos nós em um cluster. Não tente separar as camadas em processos discretos a

menos que seja absolutamente necessário. Fazer isso irá degradar o desempenho e
acrescentar complexidade ao sistema, uma vez que você terá que acrescentar coisas
como Fachadas Remotas (368) e Objetos de Transferência de Dados (380).

É importante lembrar que muitas dessas coisas são o que Jens Coldewey se refere como impulsionadores de complexidade — distribuição, multithreading explicito, mudanças de paradigma (como objeto
/ relacional), desenvolvimento multiplata-

forma e requisitos extremos de desempenho (como mais de 100 transações por segundo). Tudo isso carrega um alto custo. Certamente há ocasiões em que você tem

que fazê-lo, mas nunca se esqueça de que cada um deles carrega um custo tanto no
desenvolvimento quanto na manutenção.

CAPÍTULO

2

Organizando a Lógica do Domínio

D:

modo a organizar a lógica do dominio, eu a separei em três padrões prin-

cipais: Roteiro de Transação (120), Modelo do Domínio (126) e Módulo Tabela

(134).

A abordagem mais simples para armazenar lógica de domínio é o Roteiro de

Transação (120). Um Roteiro de Transação (120) é, essencialmente, um procedimento

que recebe os dados de entrada da camada de apresentação, efetua o processamento

com validações e cálculos, armazena dados no banco de dados e invoca quaisquer

operações necessárias em outros sistemas. Ele então responde com mais dados para

a apresentação, talvez executando mais cálculos para ajudar a organizar e formatar a
resposta. A organização fundamental é a de um único procedimento para cada ação

que o usuário possa querer executar. Assim, podemos pensar neste padrão como sendo um roteiro para uma ação ou transação de negócio. Ele não tem de ser constituí-

do, necessariamente, de um único procedimento. As partes podem ser separadas em
sub-rotinas, e estas podem ser compartilhadas por diferentes Roteiros de Transação
(120). Entretanto, a motivação ainda é a de um procedimento para cada ação, de mo-

do que um sistema de varejo pode ter Roteiros de Transação (120) para a checagem de
saída, para acrescentar algo ao carrinho de compras, para mostrar a situação atual da

entrega e assim por diante.

Um Roteiro de Transação (120) oferece diversas vantagens:

* É um modelo procedural simples que a maioria dos desenvolvedores com-

preende.

* Funciona bem com uma camada de dados simples usando o Gateway de Linha
de Dados (158) ou o Gateway de Tabelas de Dados (151).
* Omodo de estabelecer as fronteiras da transação é uma tarefa óbvia: comece

abrindo uma transação e termine fechando-a. É fácil para as ferramentas fazer isso por trás dos panos.

46

Parte | e As NARRATIVAS

Infelizmente, existem também muitas desvantagens, que tendem a aparecer à

medida que a complexidade da lógica do domínio aumenta. Frequentemente, have-

rá código duplicado, uma vez que diversas transações precisam fazer coisas parecidas. Este problema pode, em parte, ser tratado fatorando-se o código comum em
sub-rotinas, mas, mesmo assim, muito da duplicação é complicada de ser removida

e mais dificil ainda de ser localizada. A aplicação resultante pode acabar sendo uma

teia confusa de sub-rotinas sem uma estrutura clara.
É claro que a lógica complexa é o cenário ideal para os objetos, e a maneira pela qual a orientação a objetos lida com este problema é por meio de um Modelo de Do-

minio (126). Com um Modelo de Domínio (126), construímos um modelo de nosso do-

mínio o qual, pelo menos em uma primeira aproximação, é organizado primaria-

mente em torno dos nomes do domínio. Assim, um sistema de leasing teria classes

para o arrendamento, propriedades, e assim por diante. A lógica para lidar com validações e cálculos seria colocada neste modelo de domínio, de modo que um objeto
remessa poderia conter a lógica para calcular o preço da remessa para uma entrega.
Poderia ainda haver rotinas para calcular uma fatura, mas tal procedimento logo de-

legaria para um método do Modelo de Domínio (126).
Usar um Modelo de Domínio (126), em oposição a um Roteiro de Transação (120), é

a essência da mudança de paradigma que as pessoas de orientação a objetos falam

tanto. Em vez de uma rotina contendo toda a lógica para uma ação do usuário, cada

objeto realiza uma parte da lógica que seja relevante para ele. Se você não estiver
acostumado com um Modelo de Domínio (126), aprender a trabalhar com um pode ser
muito frustrante quando você começa a correr de objeto em objeto tentando desco-

brir em qual está o comportamento.

É difícil capturar a essência da diferença entre os dois padrões com um exemplo
simples, mas nas discussões sobre os padrões tentei fazer isso criando um pedaço

simples de lógica de domínio de ambas as maneiras. A maneira mais fácil de ver a di-

ferença é olhar os diagramas de seqiiéncia das duas abordagens (Figuras 2.1 e 2.2). O
problema essencial é que diferentes tipos de produtos têm algoritmos diferentes para o lançamento de receitas em um dado contrato (veja o Capítulo 9, para uma melhor fundamentação). O método de cálculo tem que determinar a que tipo de produ-

um Serviço de

um Gateway

Lançamento
T
calcularLançamentos (IDdoContrato)

de Ds

T;

encontrarContrato(IDdoContrato)

um conjunto de
contratos

ler dados

!

* inserir lançamento de receita

[|
Figura 2.1

Um Roteiro de Transação (120) para o lançamento de receitas.

T

[à
I
I
I

