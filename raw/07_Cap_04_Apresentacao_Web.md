# 07_Cap_04_Apresentacao_Web

Capiruo 5 e Concorrência

89

Isso não significa que você nunca deva usar transações longas. Se o seu banco

de dados tiver somente necessidades moderadas de concorrência, elas podem funcionar para você. E, se elas funcionarem para você, sugerimos usá-las. Entretanto, a

aplicação não será escalável, porque transações longas transformam o banco de da-

dos em um gargalo importante. Além disso, a refatoração de transações longas para

curtas é complexa e mal compreendida.

Por este motivo muitas aplicações corporativas não podem arriscar transações

longas. Nesse caso, você tem que dividir a transação de negócio em uma série de
transações curtas. Isso significa que você terá que dar seu jeito para suportar as propriedades ACID de transações de negócio entre transações de sistema — um proble-

ma que chamamos de concorrência offline. As transações de sistema ainda fazem
parte da cena. Sempre que transações de negócio interagem com um recurso transacional, como um banco de dados, essa interação irá executar dentro de uma transa-

ção de sistema para que se mantenha a integridade desse recurso. Contudo, como

você lerá a seguir, não é suficiente conectar uma série de transações de sistema para

suportar apropriadamente uma transação de negócio. A aplicação de negócio deve
conectar as transações de sistema.

A atomicidade e a durabilidade são as propriedades ACID mais facilmente su-

portadas em transações de negócios. Ambas são suportadas executando-se a fase de
confirmação da transação de negócio, quando o usuário pressiona “Gravar”, dentro

de uma transação de sistema. Antes que a sessão tente gravar todas as alterações pa-

ra o conjunto de registros, ela primeiro abre uma transação de sistema. A transação

de sistema garante que as alterações sejam gravadas como uma unidade e as torna
permanentes. A única parte potencialmente traiçoeira aqui é a manutenção de um

conjunto preciso de alterações durante a vida da transação de negócio. Se a aplicação

usar um Modelo de Domínio (126), uma Unidade de Trabalho (187) pode rastrear as alte-

rações com precisão. Colocar lógica de negócio em um Roteiro de Transação (120) requer um rastreamento manual das alterações, mas isso provavelmente não é um pro-

blema já que o uso de roteiros de transação sugere transações simples de negócios.

A propriedade ACID mais traiçoeira de garantir em transações de negócios é o

isolamento. Falhas de isolamento levam a falhas de consistência. A consistência de-

termina que uma transação de negócio não deixe o conjunto de registros em um estado inválido. Dentro de uma única transação, a responsabilidade da aplicação no

suporte à consistência é forçar o respeito a todas as regras de negócio disponíveis. Ao
longo de múltiplas transações, a responsabilidade da aplicação é assegurar que uma
sessão não atropele as alterações de outra sessão, deixando o conjunto de registros no
estado inválido de ter perdido o trabalho de um usuário.
Assim como o problema óbvio da colisão de atualizações, há problemas mais
sutis de leituras inconsistentes. Quando dados são lidos por diversas transações de
sistema, não há garantia de que estarão consistentes. As diferentes leituras podem

até introduzir dados na memória que estejam suficientemente inconsistentes para

causar falhas na aplicação.

As transações de negócios estão intimamente ligadas a sessões. Na visão do

usuário, cada sessão é uma sequência de transações de negócios (a não ser que eles
estejam apenas lendo dados), de modo que normalmente pressupomos que todas as
transações de negócio executam em uma única sessão cliente. Embora certamente se-

ja possível projetar um sistema que tenha múltiplas sessões para uma transação de

negócio, este é um modo muito bom de se confundir - de maneira que pressupomos
que você não irá fazer isso.

90

Parte | e As NARRATIVAS

Padrões para o Controle de Concorrência Offline
Tanto quanto possível, você deve deixar o seu sistema de transações lidar com problemas de concorrência. Tratar controle de concorrência que atravessa várias transações de sistema faz com que você mergulhe nas águas turvas de ter você mesmo que

lidar com a concorrência. Essas águas estão cheias de tubarões virtuais, águas-vivas,

piranhas e outras criaturas menos amigáveis. Infelizmente, o desacordo entre transa-

ções de negócios e de sistema significa que às vezes você tem que suar a camisa. Os
padrões que fornecemos são algumas técnicas que achamos úteis para lidar com controle de concorrência que atravessa transações de sistema.

Lembre-se de que essas são técnicas que você só deve usar se tiver que fazê-lo.

Se puder fazer com que todas as suas transações de negócios se ajustem em uma

transação de sistema, assegurando-se de que elas caibam dentro de uma única solicitação, então faça isso. Se você puder se virar com transações longas renunciando à es-

calabilidade, então faça isso. Deixando o controle de concorrência nas mãos do seu
software de transações, você evitará uma grande quantidade de problemas. Estas técnicas são o que você tem que usar quando não puder fazer isso. Devido à natureza
traiçoeira da concorrência, temos que enfatizar novamente que os padrões são um
ponto de partida, não um destino. Descobrimos que eles são úteis, mas não alegamos
ter encontrado uma cura para todos os males da concorrência.

Nossa primeira escolha para lidar com problemas de concorrência offline é o Blo-

queio Offline Otimista (392), que basicamente usa controle de concorrência otimista ao
longo das transações de negócios. Gostamos desta como primeira escolha porque é

uma abordagem mais fácil de programar e fornece a melhor vivacidade. A limitação

do Bloqueio Offline Otimista (392) é que você só descobre que uma transação de negócio irá falhar quando tenta confirmá-la e, em algumas circunstâncias, o custo desta

descoberta tardia é muito alto. Usuários podem ter perdido uma hora de trabalho entrando com detalhes sobre um empréstimo e se você tiver muitas falhas eles perde-

rão sua confiança no sistema. Sua alternativa é o Bloqueio Offline Pessimista (401), com
o qual você descobre cedo se tem problemas, mas sai perdendo porque é mais difícil
de programar e reduz a vivacidade.

Com qualquer dessas abordagens, você pode diminuir consideravelmente a

complexidade se não tentar gerenciar os bloqueios em todos os objetos. Um Bloqueio
de Granularidade Alta (412) permite que você gerencie simultaneamente a concorrên-

cia de um grupo de objetos. Outra maneira de você tornar a vida dos desenvolvedores de aplicações mais fácil é usar Bloqueio Implícito (449), o qual os poupa de ter que

gerenciar os bloqueios diretamente. Isso não apenas poupa trabalho como também
evita falhas quando as pessoas esquecem -— e essas falhas são difíceis de serem encontradas.
Uma declaração comum sobre concorrência é que ela é uma decisão puramen-

te técnica que pode ser tomada após os requisitos estarem completos. Nós discorda-

mos. A escolha de controles otimista ou pessimista afeta toda a experiência do usuá-

rio com o sistema. Um projeto inteligente do Bloqueio Offline Pessimista (401) precisa

de muita informação a respeito do domínio proveniente dos usuários do sistema. De

modo semelhante, conhecimento do domínio é necessário para escolher bons Bloqueios de Granularidade Alta (412).

Brincar com concorrência é uma das tarefas de programação mais difíceis. É

muito difícil testar código concorrente com confiança. Falhas de concorrência são di-

fíceis de serem reproduzidas e muito difíceis de rastrear. Os padrões que descreve-

Capiruo 5 e Concorrência

91

mos funcionaram para nós até agora, mas este é um território particularmente difícil.

Se você precisar ir por esse caminho, vale a pena obter um pouco de ajuda experiente. Pelo menos consulte os livros mencionados no final deste capítulo.

Concorrência em Servidores de Aplicação
Até agora falamos sobre concorrência, principalmente em termos de múltiplas sessões

rodando sobre uma fonte de dados compartilhada. Outra forma de concorrência é a
do processo do próprio servidor de aplicações: como esse servidor lida com diversas
solicitações concorrentemente e como isso afeta o projeto da aplicação no servidor? A

grande diferença das outras questões de concorrência sobre as quais falamos é que a
concorrência do servidor de aplicações não envolve transações, de modo que trabalhar

com elas significa um afastamento do relativamente controlado mundo transacional.
A programação multithread explícita, com bloqueios e blocos de sincronização,

é complicada de ser bem feita. É fácil introduzir defeitos que são muito difíceis de
descobrir — falhas de concorrência são quase impossíveis de reproduzir - resultando

em um sistema que funciona corretamente 99% do tempo, mas falha aleatoriamente.

Tal software é incrivelmente frustrante de usar e depurar, de modo que nossa política
é evitar tanto quanto possível a necessidade de manipulação explícita de sincroniza-

ção e bloqueios. Os desenvolvedores de aplicações não devem quase nunca ter que
lidar com esses mecanismos explícitos de concorrência.
A maneira mais simples de lidar com isso é usar processo-por-sessão, em que

cada sessão roda no seu próprio processo. Sua grande vantagem é que o estado de
cada processo é completamente isolado dos outros processos, de modo que os programadores de aplicações não têm que se preocupar com multithreading. Até onde o
isolamento de memória abranger, é quase que igualmente eficaz fazer cada solicitação iniciar um novo processo ou ter um processo associado à sessão que estiver ociosa entre as solicitações. Muitos dos primeiros sistemas Web iniciavam um novo pro-

cesso Perl para cada solicitação.

O problema do processo-por-sessão é que ele usa muitos recursos, uma vez que

Os processos são custosos. Para ser mais eficiente, você pode fazer um pool de processos, de modo que cada um lide apenas com uma única solicitação de cada vez mas
possa lidar com múltiplas solicitações de diferentes sessões em uma seqiiéncia. Essa
abordagem de um pool de processos-por-solicitação usará muito menos processos

para suportar um dado número de sessões. Seu isolamento é quase tão bom: você

não tem muitas das complicadas questões de multithreading. O problema principal do

processo por solicitação em relação ao processo-por-sessão é que você tem que assegurar que quaisquer recursos usados para manipular uma solicitação sejam liberados ao final da solicitação. A versão atual do Apache mod-perl usa este esquema, assim como fazem muitos dos sistemas de processamento de transação de larga escala.
Até mesmo o processo-por-solicitação irá precisar de muitos processos rodando para lidar com uma carga razoável. Você pode melhorar ainda mais a quantidade

de dados transferidos fazendo um único processo executar múltiplas treads. Com
esta abordagem thread-por-solicitação, cada solicitação é manipulada por uma única thread dentro de um processo. Já que as threads usam muito menos recursos do ser-

vidor do que um processo, você pode lidar com mais solicitações com menos hardware, de modo que seu servidor fica mais eficiente. O problema com o uso de thread-porsolicitação é que não há isolamento entre as threads, e qualquer thread pode pegar al-

guma parte dos dados a qual tiver acesso.

92

Parte | e As NARRATIVAS

Na nossa visão, há muito a ser dito sobre o uso de processo-por-solicitação. Embora seja menos eficiente do que tliread-por-solicitação, usar processo-por-solicitação

é igualmente escalável. Você também obtém melhor robustez — se uma das threads

sair de controle, ela pode trazer abaixo o processo inteiro, de forma que usar proces-

so-por-solicitação limita o estrago. Especialmente com uma equipe menos experien-

te, a redução dos problemas causados por threads (e o tempo e custo de consertar fa-

lhas) justifica os custos extras de hardware. Acreditamos que poucas pessoas realmen-

te executam algum teste de desempenho para estimar os custos relativos de threadpor-solicitação e processo-por-solicitação para suas aplicações.

Alguns ambientes fornecem um meio-termo permitindo que áreas isoladas de

dados sejam designadas a uma única thread. COM faz isso com apartamentos de uma

única thread, e J2EE faz isso com Enterprise Java Beans (e no futuro fará com isola-

mentos). Se sua plataforma tiver disponível algo parecido com isso, ela pode permitir que você faça e coma seu próprio bolo — seja lá o que isso signifique.

Se você usar thread-por-solicitação, o mais importante é criar e entrar em uma

zona isolada onde os desenvolvedores de aplicações podem na maior parte das vezes, ignorar questões relativas a multithreading. O modo usual de fazer isso é levar a
thread a criar novos objetos quando ela inicia o tratamento da solicitação e assegurar

que esses objetos não sejam colocados em algum lugar (como em uma variável está-

tica) onde outras threads possam vê-los. Dessa maneira, os objetos ficam isolados porque outras threads não têm como referenciá-los.
Muitos desenvolvedores estão preocupados com a criação de novos objetos
porque lhes foi dito que a criação de objetos é um processo custoso. O resultado disso é que eles muitas vezes criam um pool de objetos. O problema com a criação de
pools é que você tem que sincronizar o acesso aos objetos do pool de alguma maneira.
Contudo o custo da criação de objetos depende muito da máquina virtual e das estratégias de gerenciamento de memória. Em ambientes modernos, a criação de objetos é realmente bastante rápida [Peckish]. (Quantos objetos Java para datas você acha

que podemos criar em um segundo no P3 600 Mhz do Martin com Java 1.3? Nós lhe

diremos mais adiante.) Criar objetos novos para cada sessão evita muitas falhas de

concorrência e pode realmente aumentar a escalabilidade.
Embora essa tática funcione em muitos casos, ainda há algumas áreas que os de-

senvolvedores precisam evitar. Uma são as variáveis estáticas baseadas em classe ou

variáveis globais, porque qualquer uso delas precisa ser sincronizado. Isso também é

verdade para singletons. Se você precisar de algum tipo de memória global, use um Re-

gistro (448), o qual você pode implementar de tal modo que ele se pareça com uma variável estática, mas que na verdade use armazenamento específico para thread.
Mesmo se você puder criar objetos para a sessão, e assim criar uma zona com-

parativamente segura, alguns objetos são custosos de criar e, portanto, precisam ser
manipulados diferentemente — o exemplo mais comum é uma conexão de banco de
dados. Para lidar com isso, você pode colocar esses objetos em um pool explícito em
que possa obter uma conexão enquanto precisar de uma e retorná-la quando terminar. Estas operações precisarão ser sincronizadas.

Leitura Adicional
De muitas formas, este capítulo apenas arranha a superfície de um tópico muito mais

complexo. Para maior investigação, sugerimos começar com [Bernstein and Newcomer], [Lea] e [Schmidt et al.].

CAPÍTULO

Estado da Sessão

uando falamos sobre concorrência, levantamos a questão da diferença entre

transações de sistema e transações de negócio (Capítulo 5). Além de afetar a

concorrência, essa diferença afeta também a forma de armazenar os dados

usados em uma transação de negócio, mas que ainda não estão prontos para serem

gravados de forma definitiva (commit) no banco de dados.
As diferenças entre transações de negócio e transações de sistema sustentam
muito do debate sobre sessões sem estado contra sessões com estado. Muito tem si-

do escrito sobre esta questão, mas o problema básico muitas vezes está disfarçado

atrás das questões técnicas de sistemas servidores com e sem estado. A questão fun-

damental é perceber que algumas sessões são inerentemente com estado e então decidir o que fazer a respeito do estado.

O Valor de Não Possuir Estado
O que as pessoas querem dizer com um servidor sem estado? O ponto principal a

respeito de objetos, é óbvio, é que eles combinam estado (dados) com comportamen-

to. Um objeto verdadeiramente sem estado é um objeto sem atributos. Tais “animais”

aparecem de tempos em tempos, mas, francamente, eles são bastante raros. Na verdade, você pode argumentar, com razão, que um objeto sem estado caracteriza um
projeto ruim.
Verifica-se, entretanto, que isso não é o que a maioria das pessoas quer dizer

quando fala sobre ausência de estado em uma aplicação corporativa distribuída.
Quando

as pessoas se referem a um servidor sem estado, querem dizer um objeto

que não retém estado entre solicitações. Tal objeto pode muito bem ter atributos, mas

quando você invoca um método em um servidor sem estado os valores dos atributos

são indefinidos.

94

Parte | e As NARRATIVAS

Um exemplo de um objeto servidor sem estado pode ser um servidor que re-

torne uma página Web contendo informações a respeito de um livro. Você faz uma

chamada a este servidor acessando uma URL — o objeto pode ser um documento

ASP ou um servlet. Na URL você fornece um número ISBN que o servidor usa para

gerar a resposta HTTP. Durante a interação, o objeto servidor, antes de gerar a pági-

na HTML, pode armazenar o ISBN, o título e o preço do livro em atributos do obje-

to, quando ele os obtém do banco de dados. Talvez ele execute alguma lógica de ne-

gócio para determinar quais informações complementares mostrar para o usuário.
Assim que tiver terminado seu trabalho, entretanto, estes valores se tornam inúteis.

O próximo ISBN é outra história completamente diferente, e o objeto servidor será
provavelmente reinicializado para limpar quaisquer valores antigos evitando assim

a ocorrência de erros.

Agora imagine que você queira guardar todos os ISBNs visitados por um determinado endereço IP cliente. Você pode guardá-los em uma lista mantida pelo objeto
servidor. Contudo, esta lista precisa persistir entre as solicitações e assim você tem
um objeto servidor com estado. Essa mudança de sem estado para com estado é mui-

to mais do que uma mudança de palavras. Para muitas pessoas, servidores com es-

tado são nada menos do que desastrosos. Por que isso?

A questão básica diz respeito a recursos do servidor. Qualquer objeto servidor

com estado precisa manter todo o seu estado enquanto espera que o usuário medite
sobre a página Web enviada. Um objeto servidor sem estado, contudo, pode processar outras solicitações de outras sessões. Considere um exemplo completamente irreal, porém ilustrativo. Suponha que tenhamos uma centena de pessoas que queiram
obter informações a respeito de livros e que processar uma requisição sobre um livro

leve um segundo. Cada pessoa faz uma solicitação a cada dez segundos, e todas as
solicitações são perfeitamente balanceadas. Se quisermos guardar as solicitações de
um usuário em um objeto servidor com estado, temos que ter um objeto servidor por
usuário: cem objetos. Entretanto, durante 90% do tempo, estes objetos ficam parados

sem nada para fazer. Se desistirmos de rastrear o ISBN e apenas usarmos objetos ser-

vidores sem estado para responder às solicitações, podemos nos sair bem com ape-

nas dez objetos servidores, integralmente utilizados, durante todo o tempo.
A questão é que, se não tivermos estados entre chamadas de métodos, não im-

porta que objeto responde à solicitação, mas, se armazenarmos o estado, então preci-

samos sempre pegar o mesmo objeto. A ausência de estado nos permite fazer um pool
com nossos objetos de modo que precisemos de menos objetos para lidar com mais
usuários. Quanto mais usuários inativos tivermos, mais valiosos serão os servidores

sem estado. Como você pode imaginar, servidores sem estado são muito úteis em

Web sites com muito tráfego. A ausência de estado também é apropriada para a Web,

já que o HTTP é um protocolo sem estado.

Então, tudo deveria ser sem estado, certo? Bem, deveria, se isso fosse possível.

O problema é que muitas interações de clientes são inerentemente com estado.
Considere a metáfora do carrinho de compras presente em milhares de aplicações
de comércio eletrônico. A interação do usuário consiste em navegar pelas páginas

de diversos livros e escolher os que deseja comprar. O conteúdo do carrinho de

compras precisa ser lembrado durante toda a sessão do cliente. Basicamente, temos

uma transação de negócio com estado, o que significa que a sessão tem que ter es-

tado. Se eu apenas procuro livros e não compro nada, minha sessão não tem esta-

do, mas se eu comprar, ela tem. Não podemos evitar o estado a não ser que queira-

mos permanecer na pobreza. Em vez disso, temos que decidir o que fazer com ele.

Carítuto 6 e EstaDO DA SESSÃO

95

A boa notícia é que podemos usar um servidor sem estado para implementar uma

sessão com estado. A novidade interessante é que, ainda que isso seja possível, podemos não querê-lo.

Estado da Sessão
Os itens no carrinho de compras são o estado da sessão, o que quer dizer que os dados
no carrinho são relevantes apenas para aquela sessão em particular. Este estado está
dentro de uma transação de negócio, o que significa que está separado de outras sessões e suas transações de negócio. (Continuarei a pressupor, nesta discussão, que cada

transação de negócio roda em uma sessão apenas e que cada sessão executa apenas
uma transação de negócio de cada vez). O estado de sessão é diferente do que chamo

de registro de dados, que são os dados persistentes, de longo alcance, armazenados no
banco de dados e visíveis para todas as sessões. Para se tornar um registro de dados, o
estado da sessão precisa ser armazenado de forma persistente (commited).
Uma vez que o estado da sessão está dentro de uma transação de negócio, ele
possui muitas das propriedades que as pessoas normalmente associam a transações,
tais como as propriedades ACID (atomicidade, consistência, isolamento e durabili-

dade). As conseqiiéncias disso nem sempre são compreendidas.
Uma conseqiiéncia interessante é o efeito sobre a consistência. Enquanto o clien-

te está editando uma apólice de seguros, o estado corrente da apólice pode não ser
válido. O cliente altera um valor, usa uma solicitação para enviá-lo para o sistema e

o sistema responde indicando valores inválidos. Esses valores são parte de um esta-

do de sessão, mas não são válidos. Este fato muitas vezes ocorre com o estado da ses-

são — ele não satisfaz as regras de validação enquanto está sendo trabalhado. Isso irá

ocorrer apenas quando a transação de negócio for armazenada de maneira persistente no banco (commited).
A questão maior no que diz respeito ao estado da sessão é lidar com o isolamen-

to. Devido ao grande número de aspectos envolvidos, diversas coisas podem ocorrer

enquanto o cliente edita uma apólice. A mais óbvia consiste em duas pessoas editando

a mesma apólice ao mesmo tempo. No entanto, o problema não são apenas as altera-

ções. Considere a existência de dois registros, a própria apólice e o registro do cliente.

A apólice tem um valor de risco que depende parcialmente do código postal no registro do cliente. O cliente começa a editar a apólice e, após dez minutos, faz algo que abre

o registro do cliente de modo que ele possa ver o código postal. Entretanto, durante es-

se período, outra pessoa alterou o código postal e o valor do risco — levando a uma lei-

tura inconsistente. Veja, na página 89-90, uma estratégia para lidar com isso.

Nem todos os dados armazenados pela sessão contam como estado da sessão.
A sessão pode armazenar em cache alguns dados que, em verdade, não precisariam
ser persistidos entre solicitações, mas que são armazenados para aumentar o desempenho. Uma vez que você pode perder o cache sem perder o comportamento correto, isto é diferente do estado da sessão, que deve ser armazenado entre solicitações

para que o comportamento seja o correto.

Modos de Armazenar o Estado da Sessão
Como você pode armazenar o estado da sessão uma vez que descobriu que precisará dele? Divido as opções em três escolhas não muito distintas, porém básicas.

96

Parte | e As NARRATIVAS

O Estado da Sessão no Cliente (427) armazena os dados no cliente. Há diversos
modos de fazer isso: codificando os dados em uma URL para uma apresentação Web,
usando cookies, serializando os dados em algum campo escondido em um formulá-

rio Web ou armazenando os dados em objetos em um cliente rico.

O Estado da Sessão no Servidor (429) pode ser tão simples quanto armazenar os

dados na memória entre solicitações. Normalmente, entretanto, há um mecanismo

para armazenar o estado da sessão em algum lugar mais durável, como um objeto

serializado. O objeto pode ser armazenado no sistema de arquivos local do servidor

de aplicações ou colocado em uma fonte de dados compartilhada. Esta poderia ser
uma simples tabela em um banco de dados com um ID da sessão como chave e um
objeto serializado como valor.
O

Estado da Sessão no Banco de Dados (432) também é armazenamento no lado

servidor, porém envolve a separação dos dados em tabelas e campos e o armazena-

mento deles no banco de dados, de modo semelhante ao que você faria com dados

mais duráveis.

Há algumas questões envolvidas na escolha da opção. Primeiro falarei sobre as
necessidades de largura da banda entre o cliente e o servidor. Usar Estado da Sessão no
Cliente (427) significa que os dados da sessão precisam ser transferidos pela rede em

cada solicitação. Se estivermos falando de apenas alguns campos, isso não é grande
coisa, mas quantidades maiores de dados resultam em transferências maiores. Em

uma aplicação, estes dados chegaram a quase um megabyte ou, como um dos mem-

bros da nossa equipe colocou, o tamanho de três peças de Shakespeare. É verdade
que estávamos usando XML entre os dois, o que não é a forma mais compacta de
transmissão de dados, porém mesmo assim havia muitos dados para se trabalhar.

É claro que alguns dados precisarão ser transferidos porque eles têm que ser

vistos na apresentação, porém, usar Estado da Sessão no Cliente (427) significa que, em

cada solicitação, você tem que transferir todos os dados que o servidor usa, mesmo

que o cliente não precise deles para fins de exibição. Tudo isso significa que você não

irá querer usar Estado da Sessão no Cliente (427) a não ser que a quantidade de estado

de sessão que você precisa armazenar seja pequena. Você também tem que se preo-

cupar com segurança e integridade. A menos que você criptografe os dados, deve ter
em mente que qualquer usuário mal-intencionado pode editar seus dados de sessão,

o que poderia levá-lo a uma versão inteiramente nova do conceito de “diga o seu
próprio preço.”

Os dados da sessão têm que ser isolados. Na maior parte dos casos o que está

acontecendo em uma sessão não deve afetar o que está acontecendo em outra. Se
reservarmos um itinerário de vôo, isso não deve ter nenhum efeito sobre nenhum

outro usuário até que o vôo seja confirmado. De fato, parte do significado de dados

de sessão é que eles são invisíveis fora da sessão. Isso se torna uma questão com-

plicada se você usar Estado da Sessão no Banco de Dados (432), porque você tem que
trabalhar duro para isolar os dados da sessão dos registros de dados armazenados

no banco de dados.

Se você tiver muitos usuários, deve considerar a utilização de clusters para me-

lhorar a taxa de transferência de dados (throughput). Neste caso, você deve considerar se você precisa de migração de sessão. A migração de sessão permite que uma
sessão se mova de um servidor a outro quando um servidor trata uma solicitação da
sessão e outros servidores tratam as demais. Seu oposto é a afinidade de servidor,

que força um servidor a tratar todas as solicitações para uma sessão em particular.

migração de servidor leva a um melhor balanceamento dos seus servidores, especial-

A

Carítuto 6 e EstaDO DA SEssÃo

97

mente se suas sessões forem longas. Entretanto, isso pode ser complicado se você estiver usando Estado da Sessão no Servidor (429), porque, muitas vezes, apenas a máqui-

na que lida com a sessão pode encontrar esse estado facilmente. Há maneiras de contornar isso, mas estas maneiras obscurecem a distinção entre o Estado da Sessão no

Banco de Dados (432) e o Estado da Sessão no Servidor (429).

A afinidade com o servidor pode levar a problemas maiores do que você pode-

ria inicialmente imaginar. Na tentativa de garantir a afinidade com o servidor, o clus-

ter de máquinas não pode a todo momento inspecionar as chamadas para ver de qual
sessão elas fazem parte. Como resultado, ele irá aumentar a afinidade de modo que

todas as chamadas de um cliente sejam encaminhadas para o mesmo servidor de

aplicações. Muitas vezes, isso é feito pelo endereço IP do cliente. Se o cliente estiver

atrás de um proxy, isso poderia significar que muitos clientes estejam usando o mes-

mo endereço IP e assim associados a um servidor em particular. Isso pode ser muito

ruim se você chegar a uma situação em que a maior parte do seu tráfego é manipu-

lado por um único servidor responsável pelo endereço IP da AOL!

Se o servidor for usar o estado da sessão, precisa obtê-lo em um formato que
possa ser usado rapidamente. Se você usar o Estado da Sessão no Servidor (429), o esta-

do da sessão já está lá. Se você usar o Estado da Sessão no Cliente (427), ele está lá, mas

frequentemente precisa antes ser colocado na forma apropriada. Se você usar o Esta-

do da Sessão no Banco de Dados (432), precisa ir ao banco de dados para obtê-lo (e, tal-

vez, também executar alguma transformação). Isso significa que cada abordagem po-

de ter diferentes efeitos sobre a resposta do sistema. O tamanho e a complexidade
dos dados terão efeito nesta hora.
Se você tiver um sistema público de venda a varejo, provavelmente não terá

tantos dados em cada sessão, mas terá muitos usuários na maior parte do tempo

inativos. Por este motivo, o Estado da Sessão no Banco de Dados (432) pode funcionar

bem em termos de desempenho. Em um sistema de leasing, você corre o risco de arrastar uma grande quantidade de dados de e para o banco de dados em cada solici-

tação. É nessa situação que o Estado da Sessão no Servidor (429) pode Ihe dar um de-

sempenho melhor.

Um dos grandes bichos-papões, em muitos sistemas, é quando o usuário cance-

la uma sessão e diz “pode deixar, esqueça”. Isso é especialmente complicado com

aplicações B2C, porque o usuário normalmente não diz “pode deixar, esqueça”, ele
simplesmente desaparece e não volta mais. O Estado da Sessão no Cliente (427) certamente ganha aqui, porque você pode facilmente esquecer esse usuário. Nas outras

abordagens, você precisa limpar o estado da sessão quando percebe que ela foi can-

celada, assim como configurar um sistema que lhe permita cancelar a sessão após a

passagem de um certo período de tempo (timeout). Boas implementações do Estado

da Sessão no Servidor (429) permitem um timeout automático.

Assim como o cancelamento pelo usuário, considere o que acontece quando um
sistema cancela: um cliente pode falhar, um servidor pode ter problemas e uma conexão de rede pode desaparecer. O Estado da Sessão no Banco de Dados (432) normalmente lida muito bem com as três situações. O Estado da Sessão no Servidor (429) pode

ou não sobreviver, dependendo de o objeto da sessão ter backup em armazenamento

não-volátil e onde esse armazenamento é mantido. O Estado da Sessão no Cliente (427)

não irá sobreviver a uma falha no cliente, mas deve sobreviver à queda dos demais.
Não se esqueça do esforço de desenvolvimento envolvido nestes padrões. Nesse sentido, o Estado da Sessão no Servidor (429) é, normalmente, o mais fácil de imple-

mentar, especialmente se você não tiver que persistir o estado da sessão entre requi-

98

Parte | é As NARRATIVAS

sições. O Estado da Sessão no Banco de Dados (432) e o Estado da Sessão no Cliente (427)

normalmente demandarão código para transformar o formato interno de um banco

de dados ou um formato de transporte em um formato apropriado para os objetos de

sessão. Esse tempo extra significa que você não conseguirá efetuar o mesmo trabalho

tão rapidamente quanto o faria com o Estado da Sessão no Servidor (429), especialmen-

te se os dados forem complexos. À primeira vista, o Estado da Sessão no Banco de Dados (432) poderia não parecer tão complexo se você já estiver efetuando o mapeamento para tabelas no banco de dados, porém o esforço extra de desenvolvimento vem

da necessidade de manter os dados da sessão isolados de todas as outras utilizações
do banco de dados.
As três abordagens não são mutuamente excludentes. Você pode usar uma mis-

tura de duas ou três delas para armazenar diferentes partes do estado da sessão.

Contudo, isso normalmente torna as coisas mais complicadas, já que você nunca tem
certeza de que parte do estado vai em que parte do sistema. Se, apesar disso, você usar algo em conjunto com o Estado da Sessão no Cliente (427), terá que manter pelo menos um identificador de sessão no Estado da Sessão no Cliente (427) mesmo se o resto

do estado for mantido usando os outros padrões.
Minha preferência é pelo Estado da Sessão no Servidor (429), especialmente se o

estado da sessão for armazenado remotamente, de modo que possa sobreviver a

uma falha no servidor. Também gosto do Estado da Sessão no Cliente (427) para IDs e
dados de sessões que sejam muito pequenos. Não gosto do Estado da Sessão no Banco de Dados (432), a menos que você precise de failover e clusters e você não possa armazenar mementos remotos ou se o isolamento entre sessões não for um problema
para você.

CAPÍTULO

7

Estratégias de Distribuição

O:

objetos têm sido usados já há um certo tempo e às vezes parece que, desde
sua criação, as pessoas têm querido distribuí-los. Entretanto, a distribuição
de objetos tem muito mais armadilhas do que muitas pessoas percebem

[Waldo et al.], especialmente quando elas estão sob a influência dos livretos dos ven-

dedores. Este capítulo é sobre algumas dessas duras lições — lições que tenho visto
muitos dos meus clientes aprenderem de modo difícil.

O Fascínio dos Objetos Distribuídos
Há uma apresentação recorrente que eu costumava ver duas ou três vezes por ano
durante revisões de projetos. Orgulhosamente, o arquiteto de sistemas de um novo

sistema OO mostra seu plano para um novo sistema de objetos distribuídos — vamos

fazer de conta que é algum sistema de pedidos. Ele me mostra um projeto que se pa-

rece com a Figura 7.1, com objetos remotos separados para clientes, pedidos, produ-

tos e entregas. Cada um deles é um componente separado que pode ser colocado em
um nó de processamento separado.
Pergunto “Por que você faz isso?”
“Desempenho, é claro”, o arquiteto retruca, olhando para mim de um modo um

pouco estranho. “Podemos rodar cada componente em uma caixa separada. Se um com-

ponente ficar ocupado demais, acrescentamos caixas extras para ele, de modo que pos-

samos balancear a carga da nossa aplicação”. O olhar agora é curioso como se ele estivesse tentando imaginar se eu realmente conheço alguma coisa sobre objetos distribuídos.
Enquanto isso, deparo-me com um dilema interessante. Será que eu digo que

este projeto é ruim e me mostram a porta de saída imediatamente? Ou tento aos poucos

esclarecer o meu cliente? Essa última opção é mais lucrativa, mas bem mais difi-

cil, já que o cliente geralmente está bastante satisfeito com sua arquitetura, e é duro

desistir de um sonho do qual se gosta muito.

