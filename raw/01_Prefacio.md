# 01_Prefacio

PrerAcio

ix

Alista de coisas sobre as quais não falo é longa. Eu realmente gostaria de escre-

ver sobre a organização de validações, incorporação de mensagens e comunicação

assíncrona, segurança, manipulação de erros, clusterização, integração de aplicações, refatoração de arquitetura, estruturação de interfaces de usuário do tipo clien-

terico, entre outros tópicos. Entretanto, devido a restrições de espaço e tempo e falta de reflexão suficiente, você não irá encontrá-los neste livro. Espero ver alguns pa-

drões para este trabalho em um futuro próximo. Talvez eu escreva um segundo vo-

lume deste livro e trate esses tópicos, ou talvez alguma outra pessoa preencha esta

e outras lacunas.

Destas, a comunicação baseada em mensagens é uma questão particularmente

importante. As pessoas que integram múltiplas aplicações estão cada vez mais fazen-

do uso de abordagens de comunicação baseada em mensagens assíncronas. Há mui-

toa ser dito a favor de seu uso dentro de uma aplicação também.

Este livro não pretende ser específico para nenhuma plataforma de software em
particular. Deparei-me pela primeira vez com estes padrões quando estava trabalhando com Smalltalk, C++ e CORBA no final da década de 80 e início da de 90. No

final da década de 90, comecei a realizar trabalhos em Java e descobri que estes padrões se aplicavam bem tanto nos sistemas antigos Java/CORBA quanto no trabalho
mais recente baseado em J2EE. Mais recentemente fiz um trabalho introdutório com

a plataforma .NET da Microsoft e descobri que novamente os padrões se aplicavam.
Meus colegas de ThoughtWorks também apresentaram suas experiências, especialmente com Forte. Não posso afirmar que os padrões são genéricos para todas as plataformas já usadas ou ainda em uso com aplicações corporativas, mas até agora estes
padrões têm mostrado recorrência suficiente para serem úteis.

Forneci exemplos de código para a maioria dos padrões. Minha escolha de lin-

guagem para eles foi baseada no que acho que a maioria dos leitores provavelmente

será capaz de ler e entender. Java é uma boa escolha aqui. Qualquer um que possa ler
Cou C++ pode ler Java e, além disso, Java é menos complexa que C++. Basicamente,

a maioria dos programadores C++ consegue ler programas escritos em Java, mas o
contrário não é verdadeiro. Sou um entusiasta de objetos, então inevitavelmente ten-

do para uma linguagem OO. Em conseqiiéncia, a maioria dos exemplos de código estão em Java. Enquanto eu trabalhava no livro, a Microsoft começou a firmar seu am-

biente .NET, e sua linguagem C# tem a maioria das propriedades de Java que a tornam interessante para um autor. Assim, escrevi alguns dos exemplos de código também em C#, embora isso introduzisse certo risco já que os desenvolvedores não têm
muita experiência com .NET, e as técnicas para usá-lo eficientemente são menos ma-

duras. Ambas são linguagens baseadas em C, de modo que se você conseguir ler

uma deve ser capaz de ler ambas, mesmo se não gostar profundamente dessa lingua-

gem ou plataforma. Meu objetivo era usar uma linguagem que a grande maioria dos
desenvolvedores de software pudesse ler, ainda que esta não fosse a sua linguagem
principal ou preferida. (Minhas desculpas àqueles que gostam de Smalltalk, Delphi,
Visual Basic, Perl, Python, Ruby, COBOL, ou qualquer outra linguagem. Eu sei que

vocês acham que conhecem uma linguagem melhor do que Java ou CH. Tudo o que

posso dizer é que eu também!)
Os exemplos estão lá para inspirar e explicar as idéias nos padrões. Eles não são
soluções enlatadas. Em todos os casos você precisará efetuar uma quantidade razoável de trabalho para ajustá-los à sua aplicação. Padrões são pontos de partida úteis,
e não destinos.

x

Prerácio

Para Quem é

Este Livro

Escrevi este livro para programadores, projetistas e arquitetos que estejam construin-

do aplicações corporativas e que queiram melhorar sua compreensão sobre questões
arquiteturais ou sua comunicação sobre elas.
Presumo que a maioria dos meus leitores se dividirá em dois grupos: aqueles

com necessidades moderadas que querem construir seu próprio software e leitores
com necessidades maiores que usarão uma ferramenta. Para aqueles com necessidades moderadas, meu objetivo é que estes padrões sirvam como uma iniciação. Em
muitas áreas, você precisará de mais do que padrões, mas lhe darei uma vantagem

inicial neste campo muito maior do que a que tive. Aos usuários de ferramentas es-

pero que este livro dê alguma idéia do que acontece por baixo dos panos e também

ajude-os a escolher quais padrões suportados por ferramentas usar. Por exemplo, se

você usa uma ferramenta para o mapeamento objeto-relacional, ainda assim tem que

tomar decisões sobre como mapear certas situações. Ler os padrões deve lhe dar al-

guma orientação nesse sentido.

Há uma terceira categoria, aqueles exigentes e que querem construir seu pró-

prio software. A primeira coisa que eu diria neste caso é para considerarem com atenção a opção de usar ferramentas. Já vi mais de um projeto virar um longo exercício

de construção de frameworks, o que não era o objetivo inicial do projeto. Se você ainda não estiver convencido, vá em frente. Lembre-se nesse caso de que muitos dos

exemplos de código neste livro são deliberadamente simples para ajudar a compreensão, e você descobrirá que precisará fazer muitos ajustes para lidar com as demandas maiores que enfrentar.

Já que padrões são soluções comuns para problemas recorrentes, há uma boa
chance de que você já tenha se deparado com alguns deles. Se você vem trabalhando
com aplicações corporativas há algum tempo, pode conhecer a maioria deles. Não
reivindico a apresentação de nada novo neste livro. Na verdade, afirmo o oposto —
este é um livro de (para nossa indústria) idéias antigas. Se você é novo neste campo,
espero que o livro o ajude a aprender sobre estas técnicas. Se você estiver familiariza-

do com as técnicas, espero que o livro o ajude a comunicar e ensiná-las a outros. Uma

parte importante dos padrões é tentar construir um vocabulário comum, de modo

que você possa dizer que essa classe é uma Fachada Remota (368) e outros projetistas
saibam o que você quer dizer.

Agradecimentos
O que está escrito aqui deve-se muito às pessoas que trabalharam comigo de diver-

sas formas no decorrer dos anos. Muitas pessoas ajudaram de várias maneiras diferentes. Muitas vezes não consigo lembrar coisas importantes que as pessoas disseram e que estão neste livro, mas posso reconhecer aquelas contribuições de que me
lembro.

Começarei

com

meus

colaboradores.

David

Rice, um

dos meus

colegas na

ThoughtWorks, deu uma grande contribuição - cerca de um décimo do livro. En-

quanto trabalhávamos duro para atender o prazo (ao mesmo tempo que ele dava suporte a um cliente), tivemos várias conversas tarde da noite, via troca de mensagens,

em que ele confessou finalmente entender por que escrever um livro era, ao mesmo

tempo, tão difícil e tão compulsivo.

PrerAcio

xi

Matt Foemmel é outro colega da ThoughtWorks, e embora o Ártico vá precisar

de ar condicionado antes que ele escreva prosa por diversão, foi um grande colabo-

rador de exemplos de código (assim como um crítico bastante sucinto do livro.) Fi-

quei satisfeito por Randy Stafford colaborar com Camada de Serviço (141), pois ele é
um firme defensor dela. Também gostaria de agradecer Edward Hieatt e Rob Mee

por suas colaborações, que se originaram da percepção de Rob de uma lacuna no tex-

to enquanto fazia revisão. Ele se tornou meu revisor favorito: ele não apenas percebe

que algo está faltando como também escreve uma seção para consertá-lo!
Como de costume, devo mais do que posso dizer à minha equipe de revisores

oficiais de primeira classe:
John Brewer

Rob Mee

Kyle Brown

Gerard Meszaros

John Crupi

Randy Stafford

Jens Coldewey

Leonard Fenster

Alan Knight

Dirk Riehle

David Siegel

Kai Yu

Eu poderia quase listar a lista telefônica da ThoughtWorks aqui, devido à quantidade de colegas que me ajudaram, conversando sobre seus próprios projetos e experiências. Muitos padrões se formaram na minha mente porque tive a oportunida-

de de conversar com projetistas talentosos, de modo que tenho de agradecer à com-

panhia inteira.

Kyle Brown, Raquel Reinitz e Bobby Woolf não mediram esforços para ter longas e detalhadas sessões de revisão comigo na Carolina do Norte. Seu pente fino introduziu todo o tipo de bom senso aqui. Apreciei em especial as diversas e longas
conversas telefônicas com Kyle que contribuiu mais do que posso registrar.

No início de 2000, preparei uma palestra para a Java One, com Alan Knight e

Kai Yu, que foi a gênese deste material. Além de agradecê-los, também devo agrade-

cer Josh Mackenzie, Rebecca Parsons e Dave Rice pela ajuda em refinar essas pales-

tras e pelas contribuições posteriores. Jim Newkirk foi de grande valia ao ajudar a me

familiarizar com o mundo .NET.
Aprendi muito com as pessoas que trabalham nesta área por meio de boas conversas e colaborações. Gostaria de agradecer em especial a Colleen Roe, David Muirhead e Randy Stafford por compartilharem seu trabalho no exemplo do Foodsmart

na Gemstone. Também tive ótimas conversas no workshop em Crested Butte que Bruce Eckel organizou e devo agradecer a todas as pessoas que compareceram aquele

evento nos últimos anos. Joshua Kerievsky não teve tempo de fazer uma revisão
completa, mas foi um excelente consultor de padrões.
Como sempre, tive a notável ajuda do grupo de leitura da UIUC. Meus agradecimentos: Ariel Gertzenstein, Bosko Zivaljevic, Brad Jones, Brian Foote, Brian Ma-

rick, Federico Balaguer, Joseph Yoder, John Brant, Mike Hewner, Ralph Johnson e
Weerasak Witthawaskul.

Dragos Manolescu, ex-hitman da UIUC, juntou seu próprio grupo para me dar

retorno. Meus agradecimentos a Muhammad

Glenn

Graessle,

Daniel

Hein, Prabhaharan

Anan, Brain Doyle, Emad Ghosheh,

Kumarakulasingam,

Reinke, Kevin Reynolds, Sripriya Srinivasan e Tirumala Vaddiraju.

Joe Quint, John

Kent Beck me deu muitas boas idéias. Lembro de que foi ele que deu o nome

ao padrão Caso Especial (462). Jim Odell foi o responsável por me introduzir no

xii

PREFÁCIO

mundo da consultoria, ensino e escrita — nenhum agradecimento jamais fará justi-

ça a essa ajuda.

Enquanto escrevia este livro, coloquei esboços na Web. Durante esse tempo,

muitas pessoas me enviaram e-mails apontando problemas, fazendo perguntas ou falando sobre alternativas. Essas pessoas incluem Michael Banks, Mark Bernstein, Graham Berrisford, Bjorn Beskow, Bryan Boreham, Sean Broadley, Peris Brodsky, Paul

Campbell, Chester Chen, John Coakley, Bob Corrick, Pascal Costanza, Andy Czer-

wonka, Martin Diehl, Daniel Drasin, Juan Gomez Duaso, Don Dwiggins, Peter Foreman, Russell Freeman, Peter Gassmann, Jason Gorman, Dan Green, Lars Gregori,

Rick Hansen, Tobin Harris, Russel Healey, Christian Heller, Richard Henderson,

Kyle Hermenean, Carsten Heyl, Akira Hirasawa, Eric Kaun, Kirk Knoernschild, Jes-

per Ladegaard, Chris Lopez, Paolo Marino, Jeremy Miller, Ivan Mitrovic, Thomas

Neumann, Judy Obee, Paolo Parovel, Trevor Pinkney, Tomas Restrepo, Joel Rieder,
Matthew Roberts, Stefan Roock, Ken Rosha, Andy Schneider, Alexandre Semenov,

Stan Silvert, Geoff Soutter, Volker Termath, Christopher Thames, Volker Turau, Knut

Wannheden, Marc Wallace, Stefan Wenig, Brad Wiemerslage, Mark Windholtz, Mi-

chael Yoon.
Há muitos outros colaboradores cujos nomes eu nunca soube ou não consigo

lembrar, mas meus agradecimentos não são menos sinceros.

Meu maior agradecimento é, como sempre, para minha esposa Cindy, cuja com-

panhia aprecio muito mais do que alguém poderá apreciar este livro.

Colofão
Este é o primeiro livro que escrevi usando XML e tecnologias relacionadas. O texto
principal foi escrito como uma série de documentos XML usando o

fiel TextPad.

Também usei um DTD feito em casa. Enquanto estava trabalhando, usei XSLT para
gerar as páginas Web para o site HTML. Para os diagramas, baseei-me no velho amigo Visio, usando os excelentes gabaritos UML de Pavel Hruby (muito melhores que

aqueles com a ferramenta. Tenho um link no meu Web site, se você os quiser.) Escre-

vi um pequeno programa que importava automaticamente os exemplos de código

para a saída, o que me poupou do pesadelo costumeiro de recortar e colar código. No

meu primeiro esboço, experimentei XSL-FO com Apache FOP. Na ocasião, ele não se

mostrou muito apropriado para a tarefa, então para trabalhos posteriores escrevi

scripts em XSLT e em Ruby para importar o texto para o FrameMaker.

Usei diversas ferramentas open source enquanto trabalhava neste livro - em es-

pecial, JUnit, NUnit, ant, Xerces, Xalan, Tomcat, Jboss, Ruby e Hsql. Meus agradeci-

mentos para os muitos desenvolvedores dessas ferramentas. Também houve uma

longa lista de ferramentas comerciais. Em especial, baseei-me no Visual Studio for
-NET e no excelente Idea da IntelliJ — a primeiro IDE que me entusiasmou desde
Smalltalk — para Java.

O livro foi adquirido para a Addison-Wesley por Mike Hendrickson que, assistido por Ross Venables, supervisionou sua publicação. Comecei o trabalho no ma-

nuscrito em novembro de 2000 e lancei o esboço final para produção em junho de
2002. Enquanto escrevo isto, o livro está pronto para o lançamento em novembro de

2002 na OOPSLA.
Sarah Weaver foi a editora de produção, coordenando a edição, composição, re-

visão, indexação e produção dos arquivos finais. Dianne Wood foi a editora de cópia,

Prerácio

xiii

executando o complicado trabalho de polir meu inglês sem introduzir nenhum refi-

namento desagradável. Kim Arney Mulcahy compôs o livro no formato que você vê
aqui, organizou os diagramas, configurou o texto no Sabon e preparou os arquivos

Framemaker finais para a impressão. O formato do texto é baseado no formato que

usamos para Refatoração*. Cheryl Ferguson fez a revisão das páginas e desentocou to-

dos os erros que tinham escorregado pelas fendas. Irv Hershman preparou o índice.

A foto da capa
Durante os dois anos em que escrevi este livro uma construção mais importante era
feita em Boston. A ponte Leonard P. Zakim Bunker Hill (tente colocar esse nome em

uma placa de sinalização à beira da estrada) vai substituir a feia ponte de dois an-

dares usada pela rodovia interestadual 93 para atravessar o rio Charles. A ponte Za-

kim é uma ponte estaiada, um estilo incomum nos EUA mas muito utilizado na Eu-

ropa. À ponte Zakim não é longa, mas é a mais longa do gênero no mundo e a

primeira dos EUA com um projeto assimétrico. É linda, o que não impede que eu
brinque com Cindy sobre a previsão de Henry Petroski de que vamos ter uma
grande falha, em breve, em uma ponte estaiada.

Martin Fowler, Melrose, Massachussets, agosto de 2002

HTTP: //martinfowler.
com

* N.deR.T.: Refatoração, Aperfeiçoando o Projeto de Código Existente, Bookman, 2004.

