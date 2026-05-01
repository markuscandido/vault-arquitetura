# 03_Introducao

Introdução

aso você não tenha percebido, criar sistemas computacionais é uma tarefa dificil. À medida que a complexidade do sistema aumenta, a tarefa de construir o
software fica muito mais difícil. Assim como em qualquer profissão, só progre-

dimos aprendendo, tanto com os nossos erros quanto com nossos sucessos. Este livro

descreve parte desse aprendizado de forma que, espero, o ajudará a aprender essas li-

ções mais rapidamente do que aprendi, ou para se comunicar com os outros de modo

mais eficaz do que eu pude fazer antes de descrever estes padrões.

Nesta introdução, quero estabelecer o escopo do livro e fornecer alguns dos
fundamentos que servirão de base para as idéias aqui descritas.

Arquitetura
A industria de software se delicia em pegar palavras e estendê-las em uma miríade de
significados sutilmente contraditórios. Uma das maiores sofredoras é “arquitetura”.

Vejo “arquitetura” com uma daquelas palavras que soam impressionantes, usadas
principalmente para indicar que estamos falando algo importante. Contudo, sou

pragmático o suficiente para não deixar que meu cinismo atrapalhe o desafio de
atrair as pessoas para meu livro. :-)
“ Arquitetura” é um termo que muitas pessoas, com pouca concordância entre

si, tentam definir. Existem dois elementos comuns: um é a decomposição em alto nível de um sistema em suas partes; o outro são decisões difíceis de alterar. Percebe-se
também, cada vez mais, que não há apenas um único modo de especificar a arquite-

tura de um sistema; ao contrário, existem diversas arquiteturas em um sistema, e a

visão do que é significativo em termos de arquitetura pode mudar durante o ciclo de

vida de um sistema.
De tempos em tempos, Ralph Johnson envia uma mensagem verdadeiramente

notável para uma lista de correio, e ele enviou uma dessas quando eu estava termi-

24

— InrrODUÇÃO

nando o esboço deste livro. Nessa mensagem, ele levantou a questão de que a arquitetura é subjetiva, uma compreensão do projeto de um sistema compartilhada pelos

desenvolvedores experientes em um projeto. Esta compreensão compartilhada freqiientemente se apresenta na forma dos componentes mais importantes do sistema e
de como eles interagem. Também diz respeito a decisões, pois os desenvolvedores

gostariam de tomar as decisões certas desde o início, já que elas são vistas como difi-

ceis de alterar. A subjetividade aparece aqui também porque, se você descobrir que
algo é mais fácil de alterar do que você supôs, então isso não é mais arquitetural. No

final, a arquitetura se resume a coisas importantes - seja lá o que isso signifique.

Neste livro, apresento minha percepção das partes mais importantes de uma

aplicação corporativa e das decisões que eu gostaria de poder tomar acertadamente

logo no princípio do projeto. O padrão arquitetural de que mais gosto é o de cama-

das, o qual descrevo em mais detalhes no Capítulo 1. Este livro, é portanto, sobre como decompor uma aplicação corporativa em camadas e como estas camadas trabalham juntas. A maioria das aplicações corporativas não-triviais usa alguma forma de

arquitetura em camadas, mas, em algumas situações, outras abordagens, tais como
canais e filtros, são valiosas. Não discuto essas situações, focalizando em vez disso o

contexto de uma arquitetura em camadas porque esta é a mais amplamente útil.
Alguns dos padrões deste livro podem, de modo aceitável, ser chamados de pa-

drões arquiteturais, visto que representam decisões significativas sobre essas partes.
Outros são mais sobre projeto e o ajudam a conceber essa arquitetura. Não faço nenhuma tentativa decidida de separar os dois, visto que é tão subjetivo o que venha a
ser ou não um padrão arquitetural.

Ap

ações Corporativas
Muitas pessoas escrevem software para computadores, e chamamos a tudo isso de de-

senvolvimento de software. Entretanto, existem diferentes tipos de software, cada qual
com seus próprios desafios e complexidades. Isso torna-se evidente quando converso com alguns dos meus amigos do ramo das telecomunicações. Em alguns aspectos,
as aplicações corporativas são muito mais fáceis do que o software para telecomunicações - não temos problemas de multithreading muito complicados e não temos a in-

tegração de hardware e software. Em outros aspectos, no entanto, elas são muito mais
difíceis. As aplicações corporativas muitas vezes têm dados complexos - e uma

quantidade grande deles — para trabalhar, aliados a regras de negócio que não passam em nenhum teste de raciocínio lógico. Embora algumas técnicas e padrões sejam
relevantes para todos os tipos de software, muitos são relevantes apenas para um ramo específico.
Em minha carreira, tenho me concentrado em aplicações corporativas, de modo que todos os meus padrões aqui são sobre elas. (Outros termos para aplicações

corporativas incluem “sistemas de informação” ou, para aqueles com boa memória,
“processamento de dados”.) Mas o que eu quero dizer com o termo “aplicação cor-

porativa”? Não consigo dar uma definição precisa, mas posso dar algumas pistas do
meu significado.
Começarei com exemplos. As aplicações corporativas incluem folhas de pagamento, registros de pacientes, rastreamento de remessas, análise de custos, pontuação de crédito, seguro, cadeia de suprimentos, contabilidade, serviço de atendimen-

to ao cliente e comércio internacional. As aplicações corporativas não incluem a inje-

INTRODUÇÃO

25

ção de combustível em automóveis, processadores de texto, controladores de eleva-

dores, controladores de fábricas químicas, chaves telefônicas, sistemas operacionais,
compiladores e jogos.

As aplicações corporativas normalmente envolvem dados persistentes. Os da-

dos são persistentes porque precisam estar disponíveis entre múltiplas execuções do

programa - de fato, eles normalmente precisam persistir por vários anos. Além dis-

so, durante este tempo haverá muitas alterações nos programas que usam estes dados. Eles freqiientemente durarão mais do que o hardware que originalmente os criou

e sobreviverão aos sistemas operacionais e compiladores. Durante esse período, ha-

verá muitas alterações na estrutura dos dados a fim de armazenar novas informações

sem perturbar as já existentes. Ainda que haja uma alteração fundamental, e a com-

panhia instale uma aplicação completamente nova para lidar com o trabalho, os da-

dos têm que ser migrados para a nova aplicação.

Normalmente existe uma grande quantidade de dados — um sistema modera-

do terá mais de 1 GB de dados organizados em dezenas de milhões de registros —
tantos, que a gerência destes dados é uma parte importante do sistema. Os sistemas

mais antigos usavam estruturas de arquivos indexados, tais como ISAM e VSAM da

IBM. Os sistemas modernos normalmente usam bancos de dados, na sua maioria, re-

lacionais. O projeto e carga desses bancos de dados se tornou por si só uma profissão.

Normalmente muitas pessoas acessam os dados concorrentemente. Em muitos
sistemas isso pode ser menos do que uma centena de pessoas, mas para sistemas baseados na Web que conversam pela Internet, isso aumenta em ordens de magnitude.
Com tantas pessoas acessando o sistema, existem questões bem-definidas para asse-

gurar que todas elas possam fazê-lo apropriadamente. Mas mesmo sem tantas pes-

soas, ainda existem problemas para garantir que duas pessoas não acessem os mes-

mos dados ao mesmo tempo de uma forma que possa causar erros. As ferramentas

para o controle de transações absorvem um pouco dessa carga, mas muitas vezes é
impossível esconder esse problema dos desenvolvedores das aplicações.
Com tantos dados, normalmente há uma grande quantidade de telas de inter-

face com o usuário para lidar com eles. Não é raro haver centenas de telas diferentes.

Os usuários das aplicações corporativas vão dos ocasionais àqueles que usam regularmente o sistema e, em geral, eles têm pouco conhecimento técnico. Assim, os da-

dos têm que ser apresentados de várias formas diferentes para atender a diferentes

objetivos. Os sistemas muitas vezes têm uma grande quantidade de processamento
em lote, o que é fácil de esquecer quando enfocamos os casos de uso que enfatizam a

interação com o usuário.

As aplicações corporativas raramente vivem isoladas. Elas fregientemente pre-

cisam falar com outras aplicações corporativas espalhadas pela corporação. Os di-

versos sistemas são construídos em épocas diferentes com diferentes tecnologias e
até mesmo os mecanismos de colaboração são diferentes: arquivos de dados em CO-

BOL, CORBA, sistemas de mensagens, etc. De vez em quando a empresa tenta inte-

grar seus diferentes sistemas usando uma tecnologia comum de comunicação. É claro que o trabalho quase nunca chega ao fim, de modo que diversos esquemas de in-

tegração diferentes coexistem ao mesmo tempo. Isso fica ainda pior quando a empre-

sa tenta se integrar também aos seus parceiros de negócio.
Mesmo se uma companhia unificar a tecnologia para a integração, ela ainda terá problemas com as diferenças nos processos de negócios e com a dissonância con-

ceitual com os dados. Uma divisão da companhia pode achar que um cliente é al-

guém com quem ela tem um contrato vigente; outra divisão também conta aqueles

clientes com os quais a companhia já teve um contrato, mas que não tem mais; uma

26

INTRODUÇÃO

outra conta a venda de produtos mas não a venda de serviços. Pode parecer fácil li-

dar com essas diferenças, mas quando você tem centenas de registros nos quais cada

campo pode ter um significado sutilmente diferente, a grandeza do problema tornase um desafio - mesmo se a única pessoa que saiba exatamente o que o campo significa ainda esteja na companhia. (E, é claro, tudo isso muda sem aviso prévio.) A con-

sequência disso é que os dados têm de ser constantemente lidos, alterados e gravados em todos os tipos de formatos sintática e semanticamente diferentes.
A seguir, temos a questão do que está por trás do termo “lógica de negócio.”

Acho este um termo curioso porque existem poucas coisas que são menos lógicas do
que a lógica de negócio. Quando você cria um sistema operacional, esforça-se para

manter toda a coisa lógica. No entanto, as regras de negócio são simplesmente im-

postas a você e, sem um grande esforço político, não há nada que você possa fazer

para alterá-las. Você tem que lidar com um conjunto aleatório de condições estranhas

que muitas vezes interagem umas com as outras de formas surpreendentes. É claro
que elas são assim por alguma razão: algum vendedor negociou receber uma prestação anual dois dias mais tarde do que o usual porque isso se ajustava ao fluxo de caixa do cliente e, assim, ele conseguiu ganhar alguns milhões de dólares no negócio.

Alguns poucos milhares desses casos especiais é o que leva à complexa “ilógica” de
negócio, que torna tão difícil o software de negócios. Nesta situação você tem que organizar a lógica de negócio tão eficazmente quanto puder, porque a única coisa certa é que ela mudará com o decorrer do tempo.
Para algumas pessoas, o termo “aplicação corporativa” sugere um sistema

grande. Entretanto, é importante lembrar que nem todas as aplicações corporativas
são grandes, ainda que elas possam agregar muito valor ao negócio. Muitas pessoas
entendem que não vale a pena se preocupar com sistemas pequenos, e, até certo,
ponto elas tem razão. Se um sistema pequeno falhar, ele normalmente faz menos barulho do que um sistema grande. Ainda assim, acredito que essa forma de pensar
tende a não dar tanta atenção ao efeito cumulativo da coexistência de vários projetos

pequenos. Se você puder fazer coisas que melhorem os projetos pequenos, o efeito

cumulativo pode ser muito significativo para o negócio, especialmente porque pro-

jetos pequenos muitas vezes têm um valor desproporcional. Uma das melhores coi-

sas que você pode fazer é transformar um projeto grande em um pequeno simplificando a arquitetura e o processo.

Tipos de Aplicações Corporativas
Quando discutimos como projetar aplicações corporativas e quais padrões usar, é
importante perceber que as aplicações corporativas são inteiramente diferentes umas

das outras e que problemas diferentes levam a maneiras diferentes de fazer as coisas.

Tenho um conjunto de campainhas de alarme que disparam quando as pessoas dizem “Sempre faça isso”. Para mim, muito do desafio (e interesse) do projeto está em

conhecer as alternativas e julgar os compromissos envolvidos em usar esta ou aquela. Há muitas alternativas, mas escolherei aqui três pontos nesta área enorme.
Considere um varejista online B2C (negócio direto com o cliente): as pessoas navegam e — com sorte e um carrinho de compras - compram. Em um sistema como esse, precisamos ser capazes de lidar com um grande volume de usuários, então nossa

solução precisa não apenas ser razoavelmente eficiente em termos de recursos usados, mas também escalável, de modo que você possa aumentar a carga adicionando

InrrODUÇÃO

27

mais hardware. A lógica de domínio para tal aplicação pode ser bastante direta: obter

pedido, alguns cálculos relativamente simples de preço e remessa e notificação de
envio. Queremos que qualquer um consiga acessar o sistema facilmente, então isto

sugere uma apresentação Web bastante genérica que possa ser usada pela maior faixa possível de navegadores. A fonte de dados inclui um banco de dados para arma-

zenar pedidos e talvez alguma comunicação com um sistema de estoque para ajudar

com informações sobre disponibilidade e entrega.

Compare isso com um sistema que automatize o processo de contratos de leasing. De certo modo, este é um sistema muito mais simples do que o do varejista B2C

porque há muito menos usuários — não mais do que em torno de uma centena de cada vez. Ele é mais complicado na lógica de negócio. Calcular contas mensais em um

lease, tratar eventos tais como retornos antecipados e pagamentos atrasados e validar

dados quando um lease é reservado com antecedência, são todas tarefas complicadas,
que muito da competição na indústria de leasing vem na forma de pequenas variações sobre negócios feitos no passado. Um domínio de negócio complexo como esse
é desafiador porque as regras são arbitrárias.

Tal sistema apresenta ainda uma interface com o usuário mais complexa. No

mínimo, isso significa uma interface HTML muito mais elaborada com telas mais

complicadas e em maior número. Com fregiiência esses sistemas têm demandas de
interface com o usuário que levam os usuários a querer uma apresentação mais so-

fisticada do que o permitido por um front end HTML, assim é necessária uma interfa-

ce mais convencional, do tipo cliente rico. Uma interação mais complexa com o ustário também leva a um comportamento transacional mais complicado: Reservar com
antecedência um lease pode levar uma hora ou duas, período durante o qual o usuá-

rio está em uma transação lógica. Também observamos um esquema de banco de da-

dos complexo com talvez duas centenas de tabelas e conexões com pacotes para ava-

liação de bens e determinação de preços.

Um terceiro exemplo é um sistema simples de registro de gastos de uma pequena companhia. Tal sistema tem poucos usuáriose lógica simples, podendo ser dispo-

nibilizado facilmente para a companhia com uma apresentação HTML. A única fonte de dados são algumas poucas tabelas em um banco de dados. Tão simples quanto
possa parecer, um sistema como este não é desprovido de desafios. Você tem que

construí-lo muito rapidamente e tem que ter em mente que ele pode crescer à medi-

da que as pessoas queiram calcular reembolsos, incluí-los na folha de pagamento, entender implicações de impostos, fornecer relatórios para o diretor financeiro, conectar o sistema aos serviços Web de reserva de passagens aéreas, e assim por diante. A
tentativa de usar a arquitetura de qualquer um dos dois outros exemplos irá retardar

o desenvolvimento deste. Se um sistema traz benefícios para o negócio (como todas
as aplicações corporativas deveriam trazer), atrasar esses benefícios custa dinheiro.

Entretanto, você não quer tomar decisões agora que dificultem uma futura expansão
do sistema. Contudo, se você acrescentar flexibilidade agora e o fizer de forma equivocada, a complexidade adicionada objetivando a flexibilidade pode, em verdade,

tornar mais difícil estender o sistema no futuro e pode atrasar o desenvolvimento

atual retardando assim os benefícios. Embora

tais sistemas sejam pequenos, a

maioria das empresas têm muitos deles, de modo que o efeito cumulativo de uma arquitetura inapropriada pode ser significativo.
Cada um desses três exemplos de aplicações corporativas tem suas dificuldades
e todas elas são diferentes. Como conseqiiéncia, é difícil propor uma arquitetura úni-

ca que seja apropriada para as três situações. Escolher uma arquitetura significa entender os problemas específicos do seu sistema e definir um projeto apropriado ba-

28

INTRODUÇÃO

seado nesse entendimento. Eis por que, neste livro, não dou uma solução única para

suas necessidades corporativas. Em vez disso, muitos dos padrões se referem a escolhas e alternativas. Mesmo quando você escolher um padrão específico, terá que mo-

dificá-lo para satisfazer suas demandas. Você não pode criar software corporativo sem

pensar, e tudo que qualquer livro pode fazer é lhe dar mais informações sobre as

quais basear suas decisões.
Se isso se aplica a padrões, também se aplica a ferramentas. Embora obviamen-

te faça sentido escolher um conjunto tão pequeno quanto possível de ferramentas pa-

ra desenvolver aplicações, você também tem que reconhecer que cada ferramenta diferente é melhor para uma tarefa diferente. Tome cuidado para não usar uma ferra-

menta que seja apropriada para um tipo diferente de aplicação — pode atrapalhar
mais do que ajudar.

Pensando em Desempenho
Muitas decisões relacionadas à arquitetura dizem respeito ao desempenho. Para a
maioria das questões relacionadas ao desempenho, prefiro obter um sistema no ar e
rodando, instrumentá-lo e então usar um processo disciplinado de otimização basea-

do em medições. Entretanto, algumas decisões de arquitetura afetam o desempenho
de um modo tal que é difícil consertá-lo com otimizações posteriores. E mesmo
quando é fácil consertá-lo mais tarde, as pessoas envolvidas no projeto se preocupam
desde cedo com tais decisões.

É sempre difícil falar sobre desempenho em um livro como este. A dificuldade
advém do fato de que qualquer conselho sobre desempenho não deve ser tratado como verdade absoluta até que seja avaliado na sua própria configuração. Tenho visto
muitas vezes projetos aproveitados ou rejeitados devido a considerações de desempenho que se tornam falsas assim que alguém faz algumas medidas na configuração

real usada para a aplicação.

Dou algumas diretrizes neste livro, incluindo minimizar o número de chama-

das remotas, o qual tem sido um bom conselho relativo a desempenho há bastante
tempo. Mesmo assim, você deve verificar cada dica avaliando-o em sua própria

aplicação. De modo semelhante, há diversas ocasiões em que os exemplos de códi-

go neste livro sacrificam o desempenho para tornar mais fácil a compreensão do

que se quer demonstrar. Mais uma vez, é sua tarefa aplicar as otimizações no seu

ambiente. No entanto, toda vez que você fizer uma otimização de desempenho, de-

ve medir o ganho obtido, caso contrário pode apenas estar tornando seu código
mais difícil de ler.

Há uma conseqiiéncia importante disso: uma alteração significativa na configu-

ração pode invalidar quaisquer fatos referentes ao desempenho. Assim, se você atua-

lizar sua máquina virtual, seu hardware, seu banco de dados ou praticamente qual-

quer outra coisa, você deve refazer suas otimizações de desempenho e assegurar-se
de que elas ainda estão ajudando. Em muitos casos, uma nova configuração pode al-

terar as coisas. Você pode descobrir que uma otimização que fez no passado para melhorar o desempenho, traz prejuízos no novo ambiente.
Um outro problema ao falar sobre desempenho é o fato de que muitos termos
são usados de forma inconsistente. A mais famosa vítima é a “escalabilidade,” usada
regularmente com meia dúzia de significados diferentes. Aqui estão os termos que
eu uso.

INTRODUÇÃO

29

O tempo de resposta é a quantidade de tempo que o sistema leva para proces-

sar uma solicitação externa. Pode ser uma ação na interface com o usuário, como o
pressionamento de um botão, ou uma chamada de API do servidor.

A agilidade de resposta diz respeito ao quão rapidamente o sistema reconhece

uma solicitação (em oposição ao tempo que leva para processá-la). Isso é importante
em muitos sistemas porque os usuários podem ficar frustrados se um sistema demorar a responder a uma solicitação, ainda que seu tempo de resposta seja bom. Se o seu
sistema esperar durante toda a solicitação, então sua agilidade de resposta e seu tempo de resposta são os mesmos. Entretanto, se você indicar que recebeu a solicitação
antes de completá-la, então sua agilidade de resposta é melhor. Fornecer uma barra
de progresso durante uma cópia de arquivo melhora a agilidade de resposta da sua
interface com o usuário, embora não melhore o tempo de resposta.

A latência é o tempo mínimo requerido para obter qualquer forma de resposta,
mesmo se o trabalho a ser feito for inexistente. Geralmente é a grande questão em sistemas remotos. Se eu pedir a um programa para não fazer nada, mas para me avisar
quando tiver terminado de fazer nada, então devo receber uma resposta quase instantânea se o programa rodar no meu laptop. Entretanto, se o programa rodar em um
computador remoto, pode demorar alguns segundos devido ao tempo gasto para que
a solicitação e a resposta cheguem aos seus destinos através da conexão. Como desen-

volvedor de aplicações, geralmente, nada posso fazer para melhorar a latência. A latência é também o motivo pelo qual você deve minimizar chamadas remotas.
O throughput é a quantidade de coisas que você pode fazer em uma dada quantidade de tempo. Se você estiver contabilizando o tempo gasto na cópia de um arquivo, o throughput poderia ser medido em bytes por segundo. Para aplicações corporativas, uma medida típica é o número de transações por segundo (tps), mas o proble-

ma é que isso depende da complexidade da sua transação. Para seu sistema específi-

co, você deve escolher um conjunto usual de transações.

Nesta terminologia, desempenho pode significar tanto throughtput quanto tem-

po de resposta - o que for mais importante para você. Às vezes, pode ser difícil falar
sobre desempenho quando uma técnica melhora o throughput, mas piora o tempo de
resposta. Assim, é melhor usar o termo mais preciso. Da perspectiva de um usuário,
a agilidade de resposta pode ser mais importante do que o tempo de resposta, então

melhorar a agilidade de resposta em detrimento do tempo de resposta ou do through

put aumentará o desempenho.

A carga é uma medida da pressão a que o sistema está submetido, que poderia

ser medida pelo número de usuários a ele conectados em um determinado instante
de tempo. A carga é geralmente um contexto para alguma outra medida, como um

tempo de resposta. Assim, você pode dizer que o tempo de resposta para alguma so-

licitação é de 0,5 segundo com 10 usuários e de 2 segundos com 20 usuários.

A sensibilidade de carga é uma medida de como o tempo de resposta varia

com a carga. Digamos que o sistema A tenha um tempo de resposta de 0,5 segundo
para um número de usuários entre 10 e 20, e o sistema B tenha um tempo de resposta de 0,2 segundo para 10 usuários que aumenta para 2 segundos com 20 usuários.

Neste caso o sistema A tem uma sensibilidade de carga menor do que o sistema B.
Poderíamos também usar o termo degradação para dizer que o sistema B degrada

mais do que o sistema A.

A eficiência é o desempenho dividido pelos recursos. Um sistema que obtenha
30 tps com duas CPUs é mais eficiente que um que obtenha 40 tps com quatro CPUs

idênticas.

30

INTRODUÇÃO

A capacidade de um sistema é uma indicação do máximo throughput efetivo ou

máxima carga efetiva. Este poderia ser um máximo absoluto ou um ponto a partir do
qual o desempenho caia abaixo de um limite aceitável.
A escalabilidade é uma medida de como o acréscimo de recursos (normalmen-

te hardware) afeta o desempenho. Um sistema escalável é aquele que lhe permite adi-

cionar hardware e obter uma melhora de desempenho proporcional, como dobrar o
número de servidores disponíveis para dobrar o throughput. O escalabilidade verti-

cal, ou escalar para cima, significa adicionar mais poder a um único servidor (por
exemplo, acrescentar memória). O escalabilidade horizontal, ou escalar para fora,

significa adicionar mais servidores.
O problema aqui é que as decisões de projeto não afetam todos esses fatores de
desempenho de forma igual. Digamos que temos dois sistemas de software rodando
em um servidor: a capacidade do Peixe-Espada é de 20 tps, enquanto que a do Came-

lo é de 40 tps. Qual dos dois tem melhor desempenho? Qual é mais escalável? Não po-

demos responder a questão da escalabilidade com estes dados, e a única coisa que podemos dizer é que o Camelo é mais eficiente em um único servidor. Se adicionarmos

outro servidor, percebemos que o Peixe-Espada agora trata 35 tps, e o Camelo 50 tps.

A capacidade do Camelo ainda é melhor, mas parece que o Peixe-Espada pode ter me-

lhor escalabilidade. Se continuarmos adicionando servidores, descobriremos que o

Peixe-Espada obtém 15 tps por servidor adicional e que o Camelo obtém 10. Com esses dados, podemos dizer que o Peixe-Espada tem uma melhor escalabilidade horizontal, ainda que o Camelo seja mais eficiente com menos de cinco servidores.

Ao criar aplicações corporativas, muitas vezes faz sentido visar à escalabilida-

de de hardware em vez da capacidade ou mesmo eficiência. A escalabilidade lhe dá a

opção de melhor desempenho, se você precisar dela. A escalabilidade pode também

ser mais fácil de obter. Os projetistas muitas vezes fazem coisas complicadas que au-

mentam a capacidade de uma plataforma específica de hardware quando, em verda-

de, poderia ser mais barato comprar mais hardwares. Se o Camelo tem um custo
maior do que o Peixe-Espada, e esse custo maior é equivalente a um par de servido-

res, então o Peixe-Espada acaba sendo mais barato, mesmo se você precisar de ape-

nas 40tps. Está na moda reclamar por ter que depender de hardware melhor para fa-

zer nosso software rodar apropriadamente, e me junto a este coro sempre que tenho
que atualizar meu laptop apenas para poder rodar a versão mais nova do Word. No
entanto, adquirir hardware mais novo é com frequência mais barato do que fazer o
software rodar em sistemas menos poderosos. De forma semelhante, adicionar mais

servidores é freqiientemente mais barato do que adicionar mais programadores desde que um sistema seja escalável.

Padrões
Os padrões estão em cena há bastante tempo, então parte de mim não quer ficar re-

petindo sua história mais uma vez. No entanto, esta é uma oportunidade para dar a

minha visão de padrões e o que os torna uma abordagem valiosa para descrever pro-

jetos.

Não existe uma definição universalmente aceita de padrão, mas talvez o melhor

seja começar por Cristopher Alexander, uma inspiração para muitos entusiastas de

padrões: “Cada padrão descreve um problema que ocorre repetidamente no nosso

ambiente e então descreve a essência da solução desse problema, de tal forma que
você possa usar essa solução um milhão de vezes, sem jamais fazê-lo exatamente da

inrroDução

31

mesma forma” [Alexander et al.]. Alexander é um arquiteto, de modo que ele estava

falando de edifícios, mas a definição funciona muito bem para software também. O

foco do padrão é uma solução específica, uma que seja, ao mesmo tempo, usual e efetiva na abordagem de um ou mais problemas recorrentes. Uma outra maneira de ver

é que um padrão é um conjunto de conselhos, e a arte de criar padrões consiste em

separar os conselhos em grupos relativamente independentes de modo que você

possa se referir a eles e discuti-los mais ou menos independentemente.
Um elemento-chave dos padrões é que eles estão enraizados na prática. Você
descobre padrões vendo o que as pessoas fazem, observando as coisas que funcio-

nam e então buscando a “essência da solução.” Não é um processo fácil, mas uma

vez que você tenha encontrado alguns bons padrões, eles se tornam algo valioso. Pa-

ra mim, o seu valor reside no fato de me possibilitarem criar um livro que serve co-

mo uma referência. Você não precisa ler este livro, ou qualquer outro livro sobre padrões, do início ao fim, para achá-lo útil. Você só precisa ler o suficiente para ter uma
noção do que os padrões são, quais problemas eles resolvem e como eles os resolvem. Você não precisa conhecer todos os detalhes, mas apenas o suficiente, de modo

que, se você se deparar com um dos problemas, possa encontrar o padrão no livro.
Somente então você precisa realmente entender o padrão em profundidade.

Uma vez que você precise do padrão, tem que descobrir como aplicá-lo ao seu

contexto. Uma coisa importante sobre padrões é que você não pode simplesmente

aplicar cegamente a solução, que é o motivo pelo qual as ferramentas de padrões têm
sido um fracasso total. Gosto de dizer que padrões são “meio assados,” significando
que você tem sempre que terminar de assá-los no forno do seu próprio projeto. Sem-

pre que uso um padrão, adapto-o aqui e ali. Você vê a mesma solução muitas vezes,

mas ela nunca é exatamente a mesma.
Cada padrão é relativamente independente, mas padrões não isolados uns dos
outros. Frequentemente, um padrão leva a outro ou um ocorre apenas se outro estiver presente. Assim, você normalmente só verá uma Herança de Tabela de Classes (276)

se houver um Modelo de Domínio (126) no seu projeto. As fronteiras entre os padrões

são naturalmente pouco claras, mas tentei tornar cada padrão tão autocontido quan-

to possível. Se alguém disser “Use uma Unidade de Trabalho (187),” você pode dar

uma olhada e ver como aplicá-la sem ter que ler o livro inteiro.

Se você for um projetista experiente de aplicações corporativas, provavelmente

descobrirá que a maioria destes padrões lhe é familiar. Espero que você não fique
muito desapontado (tentei avisá-lo no Prefácio). Os padrões não são idéias originais;

eles são, em grande medida, observações das coisas que ocorrem na prática. Em conseqiiéncia, nós, autores de padrões, não dizemos que “inventamos” um padrão, mas,

que “descobrimos” um. Nosso papel é perceber a solução usual, procurar sua essên-

cia e então escrever o padrão resultante. Para um projetista experiente, o valor do padrão não é lhe dar uma idéia nova; seu valor reside no fato de ele lhe ajudar a comunicar sua idéia. Se você e todos os seus colegas sabem o que é uma Fachada Remota
(368), você pode dizer muito simplesmente afirmando “Esta classe é uma Fachada Remota.” Isso também lhe permite dizer a alguém mais novo, “Use um Objeto de Trans-

ferência de Dados (380) para isso” e eles podem vir a este livro fazer a consulta. O resultado é que os padrões criam um vocabulário sobre projeto, que é o motivo pelo
qual a atribuição de nomes é uma questão tão importante.
Embora a maioria destes padrões seja verdadeiramente para aplicações corpo-

rativas, aqueles no capítulo de padrões básicos (Capítulo 18) são mais gerais e locali-

zados. Eu os incluo porque me refiro a eles em discussões sobre os padrões de apli-

cações corporativas.

32

INTRODUÇÃO

A Estrutura dos Padrões

Cada autor tem que escolher seu formato de descrição de padrões. Alguns baseiam
seus formatos em um livro clássico de padrões como

[Alexander et al.], [Gang of

Four] ou [POSA]. Outros criam seus próprios formatos. Tenho lutado há tempos pa-

ra descobrir o melhor formato. Por um lado, não quero algo tão pequeno quanto o
formato do GOF; por outro, preciso ter seções que dêem suporte a um livro de referência. Assim, isso é o que usei neste livro.

O primeiro item é o nome do padrão. Nomes de padrões são cruciais, porque
parte do propósito do padrão é criar um vocabulário que permita aos projetistas se
comunicarem mais eficazmente. Assim, se eu lhe disser que meu

construído em torno de um Controlador Frontal (328) e uma

servidor Web

é

Vista de Transformação

(343) e você conhecer esses padrões, você terá uma idéia clara da arquitetura do meu
servidor Web.
A seguir vêm dois itens que estão sempre juntos: a intenção e o esboço. A intenção resume o padrão em uma frase ou duas. O esboço é uma representação visual do

padrão, muitas vezes, mas nem sempre, um diagrama da UML. A idéia é criar um
breve lembrete do que se trata o padrão de modo que você possa rapidamente relembrá-lo. Se você já “tem o padrão”, significando que você conhece a solução ainda que
não saiba o seu nome, então a intenção e o esboço devem ser tudo o que você precisa para saber o que é o padrão.

A próxima seção descreve um problema que motiva o padrão. Este pode não ser

o único problema que o padrão resolve, mas acredito que seja o que melhor justifica
o padrão.
Como Funciona descreve a solução. Aqui coloco a discussão de questões de im-

plementação e variações com as quais me deparei. A discussão é tão independente

quanto possível de qualquer plataforma específica - onde houver seções específicas

para alguma plataforma, distanciei-as das margens de modo que você possa pulá-las

facilmente. Onde julguei útil, coloquei diagramas da UML para ajudar a explicar os

padrões.

Quando Usá-lo descreve quando o padrão deve ser usado. Aqui falo os compro-

missos que o levam a selecionar uma solução em comparação com outras. Muitos

dos padrões deste livro são alternativas, como o Controlador de Página (318) e o Con-

trolador Frontal (328). Poucos padrões são sempre a escolha certa, então toda vez que
encontro um padrão eu sempre me pergunto “Quando eu não o usaria?” Essa ques-

tão frequentemente me leva a padrões alternativos.

A seção Leitura Adicional mostra-lhe outras discussões sobre o padrão sendo

apresentado. Esta não é uma bibliografia completa. Limitei minhas referências a textos que acho importantes para ajudá-lo a entender o padrão, por isso eliminei qual-

quer discussão que, segundo meu entendimento, não acrescentasse muito ao que escrevi. Além disso eliminei, é claro, discussões sobre padrões que não li. Também não
mencionei itens que acho que serão difíceis de encontrar, ou links Web instáveis que,

temo, possam já ter desaparecido quando você estiver lendo este livro.
Gosto de acrescentar um ou mais exemplos. Cada um é um exemplo simples do
padrão em uso, ilustrado com algum código em Java ou C#. Escolhi essas linguagens
porque elas parecem ser as que o maior número de programadores profissionais sabe ler. É absolutamente essencial compreender que o exemplo não é o padrão. Quando você usar o padrão, ele não se parecerá exatamente com o exemplo, então não o
trate como algum tipo de macro completa. Deliberadamente mantive o exemplo tão

simples quanto possível, de modo que você possa ver o padrão da forma mais clara

inrrODUÇÃO

33

que consegui imaginar. Várias questões que foram ignoradas, se tornarão, no entan-

to, importantes quando você for usar o padrão, mas elas são específicas do seu pró-

prio ambiente. Eis por que você sempre tem que adaptar o padrão.

Uma das conseqiiéncias disso é que tive de trabalhar duro para manter cada

exemplo tão simples quanto pude e, no entanto, ainda transmitindo sua mensagem
essencial. Assim, muitas vezes escolhi um exemplo que fosse simples e explícito, em
vez de um que demonstrasse como um padrão funciona com os muitos ajustes re-

queridos em um sistema de produção. É um equilíbrio complicado entre o simples e
o simplista, mas também é verdade que muitas questões por demais realistas porém
periféricas, podem tornar mais difícil entender o pontos-chave de um padrão.

Foi também por este motivo que usei exemplos simples e independentes em

vez de exemplos executáveis conectados. Exemplos independentes são mais fáceis

de entender separadamente, mas dão menos orientação sobre como juntá-los. Um

exemplo conectado mostra como as coisas se ajustam, mas é difícil entender um pa-

drão sem entender todos os outros envolvidos no exemplo. Embora na teoria seja
possível produzir exemplos que sejam conectados e, ainda assim, compreensíveis independentemente, fazer isso é muito difícil - ou pelo menos, muito difícil para mim
— então escolhi o caminho independente.
O código nos exemplos é escrito com foco em tornar as idéias compreensíveis.

Em conseqiiéncia, muitas coisas ficaram de fora — em particular o tratamento de erros, ao qual não presto muita atenção uma vez que ainda não desenvolvi nenhum
padrão nessa área. Eles estão lá meramente para ilustrar o padrão. Eles não se propõem a mostrar como modelar qualquer problema específico de negócio.

Por esses motivos, o código não está disponível para download no meu site Web.

Cada exemplo de código neste livro é cercado com tantos andaimes para simplificar
as idéias básicas, que eles não têm valor algum em um ambiente de produção.

Nem todas as seções aparecem em todos os padrões. Para o que não consegui

pensar em um bom exemplo ou texto de motivação, deixei-os de fora.
Limitações Destes Padrões
Como

mencionei no Prefácio, esta coleção de padrões não é, de forma alguma, um

guia completo para o desenvolvimento de aplicações corporativas. Meu teste para
este livro não é se ele é completo, mas simplesmente se é útil. O campo é muito extenso para uma única mente, que dirá para um único livro.
Os padrões aqui são todos padrões com os quais me deparei na prática, mas
não vou dizer que entendo completamente todas as suas ramificações e inter-relacio-

namentos. Este livro reflete minha compreensão atual, e essa compreensão se desen-

volveu à medida que eu escrevia o livro. Espero que ela continue a se desenvolver

muito após este livro ter sido impresso. Uma certeza sobre o desenvolvimento de
software e suas técnicas é que ele nunca estaciona.
Quando você considerar a utilização dos padrões, nunca se esqueça de que

são um ponto de partida, não um destino final. Não existe como um autor descrever todas as variações que os projetos de software podem ter. Escrevi estes padrões
para ajudar a fornecer um início, de modo que você possa ler sobre as lições que eu
e as pessoas que observei aprendemos fazendo e lutando. Além dessas, você terá
suas próprias lutas. Lembre-se sempre de que cada padrão está incompleto e que
você tem a responsabilidade, e a diversão, de completá-lo no contexto do seu próprio sistema.

PARTE

|

As NARRATIVAS

