# 12_Cap_09_Padroes_de_Logica_de_Dominio

CapituLo 9 e PADRÕES DE LÓGICA DE Domínio

139

LançamentoDeReceitas lr = new LançamentoDeReceitas (tabela. DataSet) ;
Produto produto = new Produto (tabela. DataSet) ;
long IDdoProduto = lerIDdoProduto (IDdoContrato) ;

if (produto.LerTipoDoProduto (IDdoProduto) == TipoDoProduto.PT)

(

lr.Inserir (IDdoContrato, quantia, (DateTime) LerDataAssinatura (IDdoContrato)) ;
) else if (produto. LerTipoDoProduto (IDdoProduto) == TipoDoProduto.P) (
Decimal [] alocação = alocar (quantia, 3);

lr. Inserir (IDdoContrato, alocação[0], (DateTime) LerDataAssinatura (IDdoContrato)) ;
lr. Inserir (IDdoContrato, alocação[1], (DateTime)
LerDataAssinatura (IDdoContrato) . adicionaDias (60)) ;
r. Inserir
(IDdoContrato, alocação(2], (DateTime)

LerDataAssinatura (IDdoContrato). adicionaDias (90));

) else if (produto. LerTipoDoProduto
| IDdoProduto)
Decimal [] alocação = alocar(quantia, 3);
lr.

Inserir

(IDdoContrato, alocação(0],

== TipoDoProduto.BD) (

(DateTime)

LerDataAssinatura (IDdoContrato) } ;

lr. Inserir (IDdoContrato, alocação(1], (DateTime)

LerDataAssinatura (IDdoContrato). adicionaDias (30));

lr. Inserir (IDdoContrato, alocação(2], (DateTime)
LerDataAssinatura (IDdoContrato). adicionaDias

}

} else throw new Exception

(60));

("identificação inválida de produto");

private Decimal[] alocar (Decimal quantia, int quantos)

Decimal resultadoInferior = quantia / quantos;

resultado inferior = Decimal.Round (resultadoInferior,

{

Decinal resultadoSuperior = resultadoInferior + 0.01m;
Decimal[] resultados = new Decimal

2);

[quantos] ;

int resto = (int) quantia $ quantos;

for (int i = 0; i < resto; i++) resultados[i] = resultadoSuperior;
for (int i = resto; i < quantos; i++) resultados[i] = resultadoInferior;

}

return resultados;

Normalmente eu usaria a classe Dinheiro (455) aqui, mas para variar um pouco
usarei dessa vez um decimal. Uso aqui um método de alocação similar ao que uso

em Dinheiro (455).

Para isso funcionar, precisamos de parte do comportamento definido em outras

classes. O produto precisa ser capaz de nos dizer de que tipo ele é. Podemos fazer isso com uma enumeração para o tipo do produto e um método de busca.
public enum TipoDoProduto (PT, P, BD};

class Produto...
public TipoDoProduto LerTipoDoProduto (Long id) (

}

String enumeração = (String) this [id] ["tipo"l;
return (TipoDoProduto) Enum.Parse (typeof (TipoDoProduto) , enumeração) ;

LerTipoDoProduto encapsula os dados no data table. Há um bom argumento para fa-

zer isso para todas as colunas de dados, em oposição a acessá-los diretamente como
fiz com a quantia no contrato. Embora o encapsulamento geralmente seja uma boa
coisa, não o uso aqui porque ele não combina com a suposição de um ambiente em

que diferentes partes do sistema acessem o conjunto de dados diretamente. Não há

encapsulamento quando o conjunto de dados se move para a interface com o usuário,

140

o
E

Parte Il e Os PaprOes

de modo que funções de acesso a colunas só fazem sentido quando há funcionalidade adicional a ser executada, tal como converter uma string em um tipo do produto.

Esta também é uma boa hora para mencionar que, embora eu esteja usando aqui

um data set não tipado, porque estes são mais comuns em diferentes plataformas, há
um argumento forte (página 473-474) para o uso de data set .NET fortemente tipados.
O outro comportamento adicional consiste em inserir um novo registro de lan-

camento de receitas.

class LancamentoDeReceitas...
public long Inserir (long IDdoContrato, Decimal quantia, DateTime data) (
DataRow

novaLinha

= tabela. NewRon

long id = GetNextID (1);
novaLinha ["ID']

= id;

novaLinha ["IDdoContrato"] = IDdoContrato;
novabinha["quantia"] = quantia;
novaLinha["data"]

= String.Format ("(0:s)", data);

tabela. Rows. Add (novaLinha) ;

}

return id;

Uma vez mais, o ponto central deste método é menos encapsular os datarows e

mais ter um método, em vez de diversas linhas de cédigo repetidas.

A segunda parte da funcionalidade é somar todas as receitas lançadas em um
contrato até uma determinada data. Uma vez que esta funcionalidade usa a tabela de
lançamento de receitas, faz sentido definir

o método lá.

class LançamentoDeReceitas...
public Decimal ReceitaLançada
aPartirDe) ;

DateTime aPartirDe)

{

String filtro = String.Format ("IDdoContrato = {0} AND data <= &(1:dJ4", IDdoContrato,

DataRow [] linhas = tabela.Select (filtro) ;
Decimal resultado = 0m;
foreach

}

}

(long IDdoContrato,

(DataRow linha in linhas)

{

resultado += (Decimal) linha["quantia"] ;

return resultado;

Este fragmento tira proveito da ótima característica do ADO.NET que lhe permite definir uma cláusula where e então selecionar um subconjunto da tabela de dados para manipular. De fato, você pode ir além e usar uma função agregada.
class LançamentoDeReceitas

public Decimal ReceitaLançada? (long IDdoContrato, DateTime aPartirDe) (
aPartirDe) ;

String filtro = String.Format ("IDdoContrato = {0} AND data <= #{1:d}4", IDdoContrato,
String expressãoAComputar = "sum(quantia)";

Object sona = tabela.Conpute (expressãoRComputar, filtro) ;
return (soma is System.DBNull) ? 0: (Decimal) soma) ;

Carítuto 9 e PADRÕES DE LÓGICA DE Domínio

Camada

141

de Serviço (Service Layer)

por Randy Stafford
Define os limites de uma aplicação com uma camada de serviços que

estabelece um conjunto de operações disponíveis e coordena a
resposta da aplicação em cada operação.

Camada de Serviço
Modelo de Domínio

Camada de Dados

Serviço de Lançamento
As aplicações corporativas normalmente requerem diferentes tipos de interfaces pa-

ra os dados armazenados e para a lógica implementada: carregadores de dados, interfaces com usuários, gateways de integração e outros. Apesar de seus objetivos di-

ferentes, estas interfaces frequentemente requerem interações comuns com a aplica-

ção para acessar e manipular os dados e invocar a lógica de negócio. As interações

podem ser complexas, envolvendo transações por meio de múltiplos recursos e a

coordenação de diversas respostas a uma ação. Codificar a lógica das interações se-

paradamente em cada interface acarreta em muita duplicação de código.
Uma Camada de Serviço define a fronteira de uma aplicação [Cockburn PloP] e
seu conjunto de operações disponíveis, a partir da perspectiva das camadas de interface dos clientes. Ela encapsula a lógica de negócio da aplicação, controlando as transações e coordenando as respostas na implementação de suas operações.

Como Funciona

Uma Camada de Serviço pode ser implementada de algumas formas diferentes, sem

violar as características de definição expressas acima. As diferenças aparecem na alo-

cação de responsabilidades por trás da interface da Camada de Serviço. Antes que eu

me aprofunde nas diversas possibilidades de implementação, deixe-me apresentar
alguns princípios básicos.

e
5

142
e

Parte Il e Os PaprOes

Tipos de “Lógica de Negócio” Assim como no Roteiro de Transação (120) e no Modelo de Domínio (126), a Camada de Serviço é um padrão para organizar a lógica do negó-

cio. Muitos projetistas, incluindo a mim, gostam de dividir a “lógica de negócio” em

Ss

dois

tipos: “lógica do domínio”, relacionada estritamente ao domínio do problema

(tais como as estratégias para calcular o lançamento de receitas de um contrato) e “ló-

gica da aplicação”, relacionada às responsabilidades da aplicação [Cockburn UC]

(tais como notificar os administradores de contratos e as aplicações integradas dos

cálculos de lançamento de receitas). A lógica da aplicação é algumas vezes chamada
de “lógica de fluxo de trabalho” (workflow logic), embora pessoas diferentes tenham

interpretações diferentes para “fluxo de trabalho”.

Os Modelos de Domínio (126) são preferíveis aos Roteiros de Transação (120) para

evitar a duplicação da lógica de domínio e para gerenciar a complexidade usando

padrões clássicos de projeto. Contudo, colocar lógica da aplicação em objetos de domúnio puros traz algumas conseqiiéncias indesejáveis. Primeiro, os objetos de domí-

nio tornam-se menos reutilizáveis em outras aplicações se eles implementarem lógi-

ca específica da aplicação e dependerem de pacotes específicos da aplicação. Em segundo lugar, misturar ambos os tipos de lógica nas mesmas classes torna mais difícil
reimplementar a lógica da aplicação, digamos, em uma ferramenta de fluxo de trabaTho (workflow) se isso se tornar desejável. Por estas razões, a Camada de Serviço fatora
cada tipo de lógica de negócio em uma camada separada, produzindo os benefícios

costumeiros da disposição em camadas e tornando os objetos de domínio puros,
mais reutilizáveis de uma aplicação para outra.

Variações de Implementação As duas variantes básicas de implementação são as

abordagens da fachada de domínio e a do roteiro de operação. Na abordagem da fachada de domínio uma Camada de Serviço é implementada como um conjunto de fachadas finas sobre um Modelo de Domínio (126). As classes que implementam as fachadas não implementam nenhuma lógica de negócio. Em vez disso, o Modelo de Domínio

(126) implementa toda a lógica de negócio. As fachadas finas estabelecem uma frontei-

ra e um conjunto de operações por meio do qual as camadas clientes interagem com a

aplicação, exibindo as características que definem uma Camada de Serviço.

Na abordagem roteiro de operação, uma Camada de Serviço é implementada co-

mo um conjunto de classes maiores que implementam diretamente a lógica da aplicação, mas que delegam a lógica de domínio para as classes de objetos de domínio
encapsulados. As operações disponíveis para clientes de uma Camada de Serviço são
implementadas como roteiros, vários deles organizados em cada classe definindo
uma área relacionada de lógica. Cada uma dessas classes, constitui um “serviço” da

aplicação, e é comum haver nomes de tipos de serviços que começam com “Serviço”.

Uma Camada de Serviço é composta destas classes de serviços da aplicação, que de-

vem estender um Supertipo de Camada (444), abstraindo suas responsabilidades e
comportamentos comuns.
Ser ou Não Ser Remoto?A interface de uma classe Camada de Serviço tem granulari-

dade alta quase que por definição, uma vez que ela declara um conjunto de opera-

ções de aplicação destinado à interface com as camadas clientes. As classes Camada de

Serviço são, portanto, apropriadas para invocação remota, da perspectiva da granu-

laridade da interface.
No entanto, a invocação remota traz embutido o custo de lidar com a distribui-

ção de objetos. Isso provavelmente impõe muito trabalho adicional para fazer as as-

CapituLo 9 e PADRÕES DE LÓGICA DE Dominio

143

sinaturas dos métodos da sua Camada de Serviços lidar com Objetos de Transferência de
Dados (380). Não subestime o custo deste trabalho, especialmente se você tiver um

Modelo de Domínio (126) complexo e interfaces de usuário sofisticadas para comple-

xos casos de uso de atualização! É significativo e trabalhoso — talvez ele perca apenas

para o custo e trabalho necessários para o mapeamento objeto-relacional. Lembre-se
da Primeira Lei do Projeto de Objetos Distribuídos (página 100-101).

Meu conselho é começar com uma Camada de Serviço invocada localmente, cu-

jas assinaturas dos métodos lidem com objetos do domínio. Acrescente chamadas re-

motas quando precisar (se precisar) colocando Fachadas Remotas (368) na sua Camada
de Serviço ou fazendo os objetos da sua Camada de Serviço implementarem interfaces
remotas. Se a sua aplicação tiver uma interface com o usuário baseada na Web ou um

gateway de integração baseado em serviços Web, não há lei que diga que a sua lógica

de negócio tenha que rodar em um processo separado das páginas no servidor e serviços Web. De fato, você pode economizar algum esforço de desenvolvimento e tempo de resposta durante a execução, sem sacrificar escalabilidade, começando com
uma abordagem que as coloque lado a lado.

Identificando Serviços e Operações Identificar as operações necessárias na frontei

ra de uma Camada de Serviço é bastante claro. Elas são determinadas pelas necessidades dos clientes da Camada de Serviço, a mais significativa (e primeira) das quais é

geralmente uma interface com o usuário. Uma vez que uma interface com o usuário
é projetada para suportar os casos de uso que os atores querem realizar com uma

aplicação, o ponto de partida para identificar as operações da Camada de Serviço é o
modelo de caso de uso e o projeto da interface com o usuário da aplicação.

Por mais desapontador que seja, muitos dos casos de uso em uma aplicação corporativa são simples e maçantes casos de uso “CRUD” (iniciais em inglês para criar, ler,
atualizar e excluir) sobre objetos do domínio — criar um destes, ler um conjunto daqueles, atualizar este outro. Minha experiência é de que quase sempre existe uma corres-

pondência um-para-um entre casos de uso CRUD e operações da Camada de Serviço.

As responsabilidades da aplicação em levar a cabo esses casos de uso, todavia,

podem ser tudo, menos maçantes. Validações à parte, criação, atualização ou exclusão de um objeto do domínio em uma aplicação, cada vez mais requerem notifica-

ções a outras pessoas e aplicações integradas. Essas respostas devem ser coordenadas em transações atômicas por operações da Camada de Serviço.

Seria bom se fosse assim tão fácil identificar as abstrações da Camada de Serviço

para agrupar operações relacionadas. Não há uma lei obrigatória nesta área, apenas

heurísticas vagas. Para uma aplicação suficientemente pequena, pode ser o bastan-

te ter apenas uma abstração, com o mesmo nome da aplicação. Segundo minha experiência, aplicações maiores são particionadas em diversos “subsistemas”, cada

um dos quais inclui uma fatia vertical completa da pilha de camadas da arquitetu-

ra. Neste caso, prefiro uma abstração por subsistema, cujo nome provém desse subsistema. Outras possibilidades incluem abstrações refletindo partições importantes
em um modelo de domínio, caso estas sejam diferentes das partições do subsistema
(p. ex., ServiçoDeContratos,

ServiçoDeProdutos) e abstrações cujo nome deriva de com-

portamentos temáticos da aplicação (p. ex., ServiçoDeLançamento).
Implementação Java

Tanto na abordagem fachada de domínio quanto na abordagem roteiro de operação, uma classe da Camada de Serviço pode ser implementada como um POJO

144

Parte Il e Os PaprOes

ou um session bean sem estado. O compromisso é entre a facilidade de teste e a
facilidade de controle de transação. Os POJOs poderiam ser mais fáceis de tes-

tar, já que não têm que ser carregados em um container EJB para rodar, mas é
mais difícil para uma Camada de Serviço baseada em POJOs se adaptar a um am-

biente de serviços de transações distribuídas gerenciados por contâineres, espe-

cialmente em chamadas inter-serviços. Os EJBs, por outro lado, já vêm com o

potencial para transações distribuídas gerenciadas por contâineres, mas têm
que ser carregados em um container antes que possam ser executados e testados. Escolha seu veneno.

Minha maneira preferida de aplicar uma Camada de Serviço em J2EE é com
session beans EJB 2.0 sem estado, usando interfaces locais, e a abordagem do rotei-

ro de operação, delegando a objetos POJO de classes do domínio. É muito conve-

niente implementar uma Camada de Serviço usando session beans sem estado, por
causa das transações distribuídas gerenciadas pelo contáiner fornecidas pelo EJB.

Além disso, com as interfaces locais introduzidas no EJB 2.0, uma Camada de Servi-

ço pode explorar os valiosos serviços de transação ao mesmo tempo em que evita
as espinhosas questões envolvendo distribuição de objetos.

Em uma observação específica para Java, deixe-me diferenciar a Camada de

Serviço do padrão Fachada de Sessão (Session Facade) documentado na literatura de

padrões J2EE [Alur et al.] e [Marinescu]. A Fachada de Sessão foi motivada pelo desejo de evitar a perda de desempenho causado pelo excesso de chamadas remo-

tas em entity beans. Nesse sentido, ela recomenda o uso de session beans atuando
como fachada para entity beans. Em vez disso, a motivação da Camada de Serviço é

fatorar responsabilidade para evitar duplicação e promover a reutilização. É um

padrão arquitetural que transcende a tecnologia. De fato, o padrão fronteira da

aplicação [Cockburn PloP] que inspirou a Camada de Serviços precede o EJB em

três anos. A Fachada de Serviço pode estar no espírito da Camada de Serviço, mas,

como correntemente chamada, definida em seu escopo e apresentada, os padrões
não são os mesmos.
Quando

Usá-la

O benefício da Camada de Serviço é que ela define um conjunto comum de operações
da aplicação disponível para muitos tipos de clientes e coordena uma resposta da
aplicação em cada operação. A resposta pode envolver lógica de aplicação que preci-

se ser executada atomicamente por diversos recursos transacionais. Assim, em uma

aplicação com mais de um tipo de cliente da sua lógica de negócio e respostas com-

plexas aos casos de uso, envolvendo múltiplos recursos transacionais, faz bastante

sentido incluir uma Camada de Serviço com transações gerenciadas por containers,

mesmo em uma arquitetura não-distribuída.
A questão mais fácil de responder é provavelmente quando não usá-la. Você

provavelmente não precisa de uma Camada de Serviço se a lógica de negócio da sua

aplicação só tiver um tipo de cliente — digamos, uma interface com o usuário — e as

respostas aos seus casos de uso não envolverem múltiplos recursos transacionais.
Neste caso, seus Controladores de Página podem controlar manualmente as transações e coordenar qualquer resposta que seja requerida, talvez delegando diretamente à camada da Fonte de Dados (Data Source).

No entanto, se você prever um segundo tipo de cliente ou um segundo recurso

transacional em respostas de casos de usos, vale a pena projetar uma Camada de Ser-

viço desde o início.

Capitulo 9 e PADRÕES DE LÓGICA DE Dominio

145

Leitura Adicional
Não há muita coisa anterior sobre a Camada de Serviço, cuja inspiração é o padrão

fronteira da aplicação de Alistair Cockburn [Cockburn PloP]. Na área de serviços re-

motos [Alpert et al.) discutem o papel das fachadas em sistemas distribuídos. Com-

pare este trabalho com as várias apresentações de Fachadas de Seção [Alur et al.] e [Ma-

rinescu]. No tópico sobre responsabilidades da aplicação que devem ser coordena-

das dentro de operações da Camada de Serviço, é muito útil a descrição de Cockburn
de casos de uso como um contrato de comportamento [Cockburn UC]. Uma referên-

cia anterior sobre fundamentos é a metodologia Fusion para a identificação de “operações do sistema” [Coleman et al.].

Exemplo:

Lançamento

de Receitas (Java)

Este exemplo continua o exemplo de lançamento de receitas dos padrões Roteiro de

Transação (120) e Modelo de Domínio (126), demonstrando como a Camada de Serviços é

usada para fazer um roteiro da lógica de aplicação e delegar a lógica do domínio em

uma operação da Camada de Serviço. Ele usa a abordagem do roteiro da operação para implementar uma Camada de Serviço, primeiro com POJOs e depois com EJBs.
Para realizar a demonstração, expandimos o cenário para incluir um pouco de

lógica de aplicação. Suponha que os casos de uso da aplicação requeiram que, quan-

do o lançamento de receitas de um contrato for calculado, a aplicação deva respon-

der enviando um e-mail, notificando este evento a um administrador de contrato de-

signado e publicando uma mensagem, usando um middleware orientado a mensa-

gens, para notificar outras aplicações integradas.
Começamos

alterando a classe ServiçoDeLançamento do exemplo

do Roteiro de

Transação (120) para estender uma Camada Supertipo (444) e para usar alguns Gateways

(436) na execução da lógica de aplicação. Isso produz o diagrama de classes da Figu-

ra 9.7. ServiçoDeLançamento se torna uma implementação POJO de um serviço de

aplicação Camada de Serviço, e seus métodos representam duas das operações dispo-

níveis na fronteira da aplicação.

Os métodos da classe ServiçoDeLançamento constroem o roteiro da lógica de

aplicação das operações, delegando a lógica do domínio para os objetos pertencentes

às classes do domínio (do exemplo do Modelo de Domínio (126)).
public class ServicoDeAplicacao (

protected GatewayDeEmail obterGatewayDeBmail()

}

//retorna uma instancia de GatewayDeBnail

(

protected GatewayDeIntegracao obterGatewayDeIntegracao() {
//retorna uma instancia de GatewayDeIntegracao

}

}

public interface GatewayDeBmail {
void enviarEmail (String endereco, String assunto,

}

public interface GatewayDeIntegracao

{

String corpoDaMensagem) ;

void publicarCálculoDeLançanentoDeReceita [Contrato contrato) ;

}
public class ServiçoDeLançamento
extends ServiçoDeAplicação

(

g
5

Parte Il e Os PaprOes
146

w

«esmo

“ojuatuesueyaqgosinias OfOd SaSse|> ap ewesbel

£6 eanbiy

'Buo| :0e1;u0DOGosaWUNU Ul) sepesue7sejao)+

UI) seYardyagoyuaWESUeTEIND|e>+

oeSeayjdyeqosiaies

loeSesBarujeqhemayen :oBdesbajujagÃem
ss)oje
do +
Wa Áemajeo :()]leugagÁemo
eg) go+

ouaweSuejadodinas

Pion :(BuO] :0)2)UO0 ONU

osayuig :(a3eq :aquiyege ur

PIOA :(oJenUOS :ojenuoS U) eadayagojusweduejado;najessenand +|
ovSsesbaqujaqhemazen
canepajup

«esn»

PIOA : (urns “odio ui ‘Buys :ojunsse Ui Bums :oSaJapua u)jfewasemnua+
eugagÃemojeo
«@2epa}upy

CapituLo 9 e PADRÕES DE LÓGICA DE Domínio

147

public void calcularLançamentoDeReceitas
(Long númeroDoContrato) (

Contrato contrato = Contrato. lerParaRtualização (númeroDoContrato) ;
contrato.calcularLançamentos () ;

obterGatewayDeBmail () .enviarEmail (

contrato. lerBrailDoAdministrador (),
"RE: Contrato #" + númeroDoContrato,
contrato + " teve o lançamento de receitas calculado.
") ;
obterGatewayDeIntegracao(] .publicarCálculoDebançamentoDeReceita (contrato) ;
public Dinheiro receitasLançadas
(long númeroDoContrato, Date aPartirDe)

return Contrato. ler (númeroDoContrato) .receitaLançada (aPartirDe) ;

(

}
Os detalhes de persistência são novamente deixados fora do exemplo. É sufi-

ciente dizer que a classe Contrato implementa métodos estáticos para ler contratos

da camada de dados, identificados pelos seus números. Um desses métodos tem um
nome que revela uma intenção de atualizar o contrato lido, o que permite a um Ma-

peador de Dados (170) relacionado registrar o(s) objeto(s) lido(s) com uma Unidade de

Trabalho (187), por exemplo.
Detalhes de controle de transação são também deixados de fora do exemplo. O
método calcularLangamentoDeReceitas() é inerentemente transacional porque, durante

sua execução, objetos persistentes referentes a contratos são modificados por meio da
adição de lançamentos de receitas, mensagens são enfileiradas em um middleware

orientado a mensagens e e-mails são enviados. Todas essas respostas devem ser exe-

cutadas atomicamente, no contexto de uma transação, porque não queremos enviar

e-mails e publicar mensagens para outras aplicações se as alterações do contrato não
puderem ser gravadas.

Na plataforma J2EE podemos deixar o contéiner EJB gerenciar as transações

distribuídas implementando serviços de aplicação (e Gateways (436)) como session
beans sem estado que usam contextos de transação. A Figura 9.8 mostra o diagrama

de classes de uma implementação do ServiçoDeLançamento que usa interfaces locais EJB

2.0 e o idioma “interface de negócio”. Nesta implementação uma Camada Supertipo

(444) é ainda usada, fornecendo implementações padrão dos métodos de beans re-

queridos pelo EJB, além dos métodos específicos da aplicação. Se partirmos do prin-

cípio de que as interfaces GatewayDeEmail e GatewayDeIntegraco também são “interfaces

de negócio” para seus respectivos session beans sem estado, então o controle da tran-

sação distribuída é obtido declarando-se os métodos calcularLançamentoDeReceitas, enviarEmail

e publicarCálculoDeLançamentoDeReceita como sendo transacionais. Os méto-

dos do ServiçoDeLançamento do exemplo POJO são movidos sem alterações para a
classe ImplementaçãoDoBeanServiçoDeLançamento.

O ponto importante sobre o exemplo é que a Camada de Serviço usa conjunta-

mente um roteiro de operação e objetos de classes do domínio na coordenação da res-

posta transacional da operação. O método calcularLançamentosDeReceitas contrói o
roteiro da lógica de aplicação da resposta requerida pelos casos de uso da aplicação,

mas delega a lógica do domínio para os objetos das classes do domínio. Ele também

apresenta algumas técnicas para combater a lógica duplicada dentro dos roteiros de

operação de uma Camada de Serviço. As responsabilidades são fatoradas em diferentes objetos, (p. ex., Gateways (436)) que podem ser reutilizados por meio de delegação. Uma Camada Supertipo (444) fornece acesso adequado a esses outros objetos.

Parte Il e Os PaprOes
148

ovdeaBaqujaqodinias :ossesbajujadoSiasisigo+

as :( )IeuzagoSn
as Iago +

polao|ezo7a(a"qla-xenef

«areyaqup

ogdeaijdyaqosiniasueagoqoesequawiajduy,

wv

ueaguolssas
ala xenef
«oe niajup»

“ojuawesuejaqosimias o esed gf3 assep ap eweibeiq

g'6 eanBiy

gago ustesueue|no|e>+

ouayuig :(a}eq :aquiyege ut 'Buo| :0je)uo0oJtunu Ul) sepeSuejse}a201+

pion :(6uo] :0Je]UO0
GOI UNU Ul) Seade

oquaweSuejadoSvasueagogossejuaajduu

ojuaweSuejadoSinasuesg
«eDeyiaqub»

Ta

DIBUUIG “(1g ‘equiuede UI Bud] 702 AUOJOGOISUU UI) SepeSUCTSCVEI—/+|
Pion :(BUO| :OJeRUO)Ogoranu Ul) Seja jagojuaedueuejnojes+

ojuauieSuejeqosinias
«arepaqup

CapituLo 9 e PADRÕES DE LÓGICA DE Domínio

149

g
Alguns podem argumentar que uma implementação mais elegante do roteiro
de operação usaria o padrão Observer [Gang of Four], mas o Observer é difícil de im- 5
plementar em uma Camada de Serviço sem estado e com múltiplas threads. Na minha

opinião, o código aberto do roteiro de operação é mais claro e mais simples.

Alguns poderiam ainda argumentar que as responsabilidades da lógica de apli-

cação poderiam ser implementadas em métodos de objetos do domínio, tais como

Contrato. calcularLançamentoDeReceitas (), ou até mesmo na camada de dados, eliminan-

do com isso a necessidade de uma Camada de Serviço separada. Acredito no entanto, essas atribuições de responsabilidades indesejáveis por uma série de motivos. Pri-

meiro, as classes de dominio são menos reutilizáveis em outras aplicações se imple-

mentarem lógica específica da aplicação (e dependerem de Gateways (436) específicos

da aplicação, e assim por diante). Elas deveriam modelar as partes do domínio do

problema que são de interesse da aplicação, o que não significa todas as responsabi-

lidades dos casos de uso da aplicação. Segundo, encapsular lógica de aplicação em
uma camada “mais alta” dedicada a esse propósito (o que a camada de dados não é)
facilita a alteração da implementação dessa camada — para usar talvez um mecanismo de fluxo de trabalho (workflow).

Como um padrão para a organização da camada de lógica de uma aplicação
corporativa, a Camada de Serviço combina roteiros e classes de objetos do domínio,

alavancando os melhores aspectos de ambos. Diversas variações são possíveis na implementação de uma Camada de Serviço — por exemplo, fachadas de domínio ou rotei-

ros de operação, POJOs ou session beans, ou uma combinação de ambos. A Camada de

Serviço pode ser projetada para chamadas locais, chamadas remotas ou ambas. Mais
importante de tudo, independentemente dessas variações, este padrão estabelece a

fundação para a implementação encapsulada da lógica de negócio de uma aplicação

e para a chamada consistente dessa lógica por seus diversos clientes.

CaríTULO

1

0

EE Padrões Arquiteturais de Fontes
de Dados (Data Source)

CapiruLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (Dara Source)

151

Gateway de Tabela de Dados (Table Data Gateway)

Gateway Pessoa
encontrar (id): ConjuntoDeDados
encontrarPeloSobrenome (String): ConjuntoDeDados
atualizar (id, sobrenome, prenome, númeroDeDependentes)

inserir (sobrenome, prenome, númeroDeDependentes)

apagar (id)

Misturar SQL com a lógica da aplicação pode causar vários problemas. Muitos desenvolvedores não se sentem confortáveis com SQL, e muitos dos que se sentem, podem não escrevê-lo bem. Os administradores de bancos de dados precisam poder en-

contrar facilmente as instruções SQL de modo que eles possam descobrir como ajustar e aprimorar o banco de dados.
Um Gateway de Tabela de Dados armazena todo o SQL utilizado para acessar uma

única tabela ou visão: seleções, inserções, atualizações e exclusões. Os outros códigos

chamam os métodos do Gateway para toda a interação necessária com o banco de dados.

Como Funciona
Um Gateway de Tabela de Dados tem uma interface simples, consistindo normalmente
de diversos métodos de busca para obter dados do banco de dados e métodos para

atualizar, inserir e excluir dados. Cada método mapeia os parâmetros de entrada em

uma chamada SQL e executa o SQL sobre a conexão com o banco de dados. O Gate-

way de Tabela de Dados normalmente não retém estado, já que seu papel é simplesmente enviar dados do banco e trazer dados para o banco.

O mais complicado em um Gateway de Tabela de Dados é como ele deve retornar
informações de uma consulta. Mesmo uma consulta simples, do tipo recuperar um
registro dada a chave primária, retornará diversos itens de dados. Em ambientes on-

de você pode retornar múltiplos itens, você pode usar esta característica para retornar uma única linha da tabela, mas muitas linguagens permitem apenas um único

valor de retorno, e muitas buscas retornam múltiplas linhas.
Uma alternativa é retornar alguma estrutura de dados simples, tal como um

mapa. Um mapa funciona, mas força a cópia dos dados do conjunto de registros que
retorna da consulta ao banco de dados para o mapa. Penso que o uso de mapas para
passar dados é uma prática ruim, porque ele frustra a verificação em tempo de com-

pilação e não é uma interface muito explícita, levando a falhas quando as pessoas

não entendem o que está no mapa. Uma alternativa melhor é usar um Objeto de Transferência de Dados (380). É mais um objeto que tem de ser criado, mas é um objeto que
pode muito bem ser usado em todo lugar.
Para evitar tudo isso, você pode retornar o Conjunto de Registros (473) resultante da consulta SQL. Conceitualmente, isso é confuso, uma vez que, idealmente, o objeto na memória não tem que saber nada a respeito da interface SQL. Isso também

pode tornar difícil substituir o banco de dados por um arquivo, se você não puder

sopeq ap

Um objeto que atua como um Gateway (436) para uma tabela do banco de dados. Uma instância lida com todas as linhas na tabela.

9
s
5

152

Parte Il e Os PaprOes

criar conjuntos de registros com facilidade no seu próprio código. Apesar disso, em
muitos ambientes que fazem uso extensivo de Conjuntos de Registros (473), tal como o
-NET, esta é uma abordagem muito efetiva. Desta forma, um Gateway de Tabela de Dados, combina muito bem com o Módulo Tabela (134). Se todas as suas atualizações fo-

rem feitas por meio do Gateway de Tabela de Dados, os dados retornados podem ser baseados em visões, em vez de nas tabelas reais, o que reduz o acoplamento entre o seu

código e o banco de dados.
Se você estiver usando um Modelo de Dominio (126), pode fazer o Gateway de Tabela de Dados retornar o objeto de domínio apropriado. O problema disso é que você

passa a ter dependências bidirecionais entre os objetos do domínio e o gateway. Os

dois são intimamente conectados, de modo que isso não é necessariamente algo ter-

rível, mas é algo que sempre reluto em fazer.

Na maioria das vezes em que você usar um Gateway de Tabela de Dados, terá um

para cada tabela no banco de dados. Para casos muito simples, entretanto, você pode ter um único Gateway de Tabela de Dados que lide com todos os métodos para todas
as tabelas. Você também pode ter um gateway para visões ou até mesmo para pesquisas interessantes que não são mantidas no banco de dados como visões. Obviamen-

te, muitas vezes, os Gateway de Tabela de Dados baseados em visões não são atualizá-

veis e, assim, não terão o comportamento de atualização. Contudo, se você puder fazer atualizações nas tabelas associadas, então encapsular essas atualizações por trás

de operações de atualização no Gateway de Tabela de Dados é uma excelente técnica.
Quando Usá-lo

Assim como com o Gateway de Linha de Dados (158), a decisão relativa ao Gateway de Tabela de Dados é, em primeiro lugar, usar ou não um Gateway (436) e, depois, qual deles.
Acho que o Gateway de Tabela de Dados é provavelmente o padrão de interface de
banco de dados mais simples de usar, na medida em que ele mapeia tão bem tanto para uma tabela de em banco de dados quanto para um tipo registro. Ele também consis-

te em um lugar natural para encapsular a exata lógica de acesso da fonte de dados. Eu

o uso menos com o Modelo de Domínio (126) porque acredito que o Mapeador de Dados
(170) provê um isolamento melhor entre o Modelo de Domínio (126) e o banco de dados.

O Gateway de Tabela de Dados funciona especialmente bem com o Módulo Tabela
(134), no qual ele gera um conjunto de registros para ser usado pelo Módulo Tabela
(134). Em verdade, não consigo imaginar qualquer outra abordagem de mapeamen-

to de banco de dados para o Módulo Tabela (134).

Assim como o Gateway de Linha de Dados, o Gateway de Tabela de Dados é muito

apropriado para Roteiros de Transação (120). A escolha entre os dois se reduz a como

eles lidam com múltiplas linhas de dados. Muitas pessoas gostam de usar um Objeto
de Transferência de Dados (380), mas isso me parece não compensar o trabalho, a me-

nos que o mesmo Objeto de Transferência de Dados (380) seja usado em algum outro lugar. Prefiro o Gateway de Tabela de Dados quando a representação do conjunto resul-

tante for conveniente para ser manipulado pelo Roteiro de Transação (120).

É interessante observar como muitas vezes faz sentido fazer os Mapeadores de

Dados (170) se comunicarem com o banco de dados por meio de Gateways de Tabela de
Dados. Embora isso não seja útil quando tudo é codificado à mão, pode ser muito efe-

tivo se você quiser usar metadados para os Gateways de Tabela de Dados, mas preferir

codificar à mão o mapeamento real para os objetos do domínio.

Um dos benefícios na utilização de um Gateway de Tabela de Dados é que a

mesma interface pode funcionar tanto na utilização de SQL para manipular o ban-

CapiruLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

153

co de dados quanto na utilização de procedimentos armazenados. De fato, os próprios procedimentos armazenados são muitas vezes organizados como Gateways
de Tabela de Dados. Dessa forma, os procedimentos armazenados para inserção e

atualização encapsulam a estrutura real da tabela. Neste caso, os procedimentos

de pesquisa podem retornar visões, o que ajuda a esconder a estrutura das tabelas
correspondentes.

Leitura Adicional
[Alur et al.] discutem o padrão Objeto de Acesso a Dados, que é um Gateway de Tabela de
Dados. Eles mostram como retornar uma coleção de Objetos de Transferência de Dados
(401) nos métodos de consulta. Não fica claro se eles vêem este padrão como sendo

sempre baseado em tabelas. A intenção e a discussão parecem sugerir ou um Gateway
de Tabela de Dados ou um Gateway de Linha de Dados (158).

Usei um nome diferente, em parte porque vejo este padrão como um uso particular do conceito mais genérico de Gateways (436) e quero que o nome do padrão re-

flita isso. Além do mais, o termo Objeto de Acesso a Dados (Data Access Object) e sua
abreviação DAO tem seu próprio significado específico dentro do mundo Microsoft.

Exemplo: O Gateway Pessoa (C#)
Um Gateway de Tabela de Dados é a forma usual de acesso a banco de dados no mun-

do Windows, então faz sentido ilustrar seu uso com C#. Tenho que enfatizar, no entanto, que esta forma clássica de Gateway de Tabela de Dados não se ajusta perfeitamen-

te ao ambiente .NET, já que ela não tira proveito do conjunto de dados (data set)
ADO.NET. Em vez disso, ela usa o leitor de dados (data reader), que é uma interface

do tipo cursor para registros de bancos de dados. O leitor de dados é a escolha certa
para manipular quantidades maiores de informação quando você não quiser trazer

tudo para a memória de uma só vez.

Para o exemplo, estou usando uma classe Gateway Pessoa que conecta a uma

tabela pessoa em um banco de dados. O Gateway Pessoa contém o código de busca,
retornando um leitor de dados ADO.NET para acessar os dados retornados.

class GatFewayPessoa...
public IDataReader EncontrarTodos( ) {

}

String sql = "SELECT * FROM pessoa";
return new OleDbConnand (sql, DB.Connection) . ExecuteReader ( );

public IDataReader EncontrarPeloSobrenome

(String sobrenome)

String sql = "SELECT * FROM pessoa WHERE sobrenome = 2";

(

IDbConmand conm = new OleDbCommand (sql, DB. Connection) ;
com. Parameters.Add (new OleDbParameter ("sobrenone", sobrenome) ;

}

return com. ExecuteReader ( );

public IDataReader EncontrarConcondição (String cléusulaCondicional) {

String sql = String.Format ("SELECT * FROM pessoa WHERE {0}", cláusulaCondicional) ;
return new OleDbCommand (sql, DB.Connection) .ExecuteReader( ) ;

Na maioria das vezes, você desejará enviar de volta um conjunto de linhas com

um leitor. Em raras ocasiões, você pode desejar pegar uma linha de dados individual:

154

Parte ll e Os PaprOes

class GatewayPessoa...

public Object [ ] Encontrarlinha (Long chave) (
String sql = "SELECT * FROM pessoa WHERE id = 2";
IDbCommand comm = new OleDbCommand(sql,

DB.Connection) ;

comm.Parameters.Add (new OleDbParameter("chave", chave)) ;
IDataReader leitor = comm.ExecuteReader ( );

leitor .Read( |;
Object [ ] resultado = new Object [leitor.PieldCount] ;
leitor .GetValues (resultado) ;

leitor.Close( );
return resultado;

Os métodos de atualização e inserção recebem os dados necessários em para-

metros e chamam as rotinas SQL apropriadas.

class GatewayPessoa...
public void atualizar (long chave, String sobrenome, String prenome, long númeroDeDependentes) (
String sql = e"
UPDATE pessoa
SET sobrenome = ?, prenome = , númeroDeDependentes = ?
WHERE id = 2";
TDbConmand conm = new OleDbConmand (sql, DB.Connect
ion) ;
conm.Parameters.Add (new OleDbParameter ("sobrenome", sobrenome));
comm. Parameters.Add

(new OleDbParameter

("prenome",

prenome)} ;

conm,Paraneters.Add (new OleDbParaneter ("nunDep", númeroDeDependentes)) ;
comm.Parameters.Add (new OleDbParameter ("chave", chave));

}

conm,BxecuteNonQuery( ) ;

class Gatenaypessoa...
public long inserir (String sobrenome, String prenome, long númeroDeDependentes)

String sql = "INSERT INTO pessoa VALUES (2, 2, 2, 2);
long chave = GetNextID(

);

TDbConmand comm = new OleDbConmand (sql, DB.Connection) ;
com. Parameters.Add
(new OleDbParameter("chave", chave) ;
conm,Parameters.Add (new OleDbParameter (*sobrenone", sobrenome));
comm. Parameters.Add (new OleDbParameter ("prenone", prenome)) ;
comm. Parameters.Add

(new OleDbParameter

conm,BxecuteNonQuery( ) ;
return key;

("numDep",

númeroDeDependentes)) ;

O método de exclusão precisa apenas de uma chave.
class GatewayPessoa...

public void excluir (Long chave) (

String sql = "DELETE FROM pessoa WHERE id = 2";
IDbCommand comm = new OleDbCommand (sql, DB.Connection) ;

conn, Parameters.
Add (new OleDbParaneter ("chave", chave) ;
com. ExecuteNonquery( );

{

CapiruLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

155

Exemplo: Usando Conjuntos de Dados ADO.NET (C#)
O Gateway de Tabela de Dados genérico trabalha com quase todo tipo de plataforma, já
que ele não é nada além de um envoltório para declarações SQL. Com o .NET você
usa mais frequentemente conjuntos de dados (data sets), mas o Gateway de Tabela de

Dados ainda é útil, embora ele assuma uma forma diferente.

2

Um conjunto de dados precisa de adaptadores de dados para nele carregar

[RS

o conjunto de dados e os adaptadores. Um gateway usa então o repositório para
armazená-los. Muito deste comportamento é genérico e pode ser feito em uma
superclasse.
O repositório indexa os conjuntos de dados e os adaptadores pelo nome da

há E
MM

os dados e para atualizar esses dados. Considero útil definir um repositório para

tabela.

class RepositórioDeConjuntosDeDados...
public DataSet dados = new DataSet ( );

private Hashtable adaptadoresDeDados = new Hashtable(

};

O gateway armazena o repositório e expõe o conjunto de dados para os seus

clientes.

class GatewayDeDados. ..
public RepositóricDeConjuntosDeDados repositório;
public DataSet dados (

}

get { return repositório.dados;)

O gateway pode atuar sobre um repositório existente ou pode criar um novo re-

positório.

Gateway
jeDados

| |)

1)

Armazenador

decomuntos
>
E Dados
nome da
tabela

1

E

— Conjunto
e Dados

1

Gateway
Pessoa

Adaptador
de Dados

Figura 10.1
Diagrama de classes de um gateway orientado a conjunto de dados e o
repositório de dados de apoio.

Fag

156

Parte Ile

Os PaorõEs

class GatewayDeDados.
protected GatewayDeDados ( ) {

}

repositório = new RepositérioDeConjuntosDeDados

protected GatewayDeDados

| );

(RepositórioDeConjuntosDeDados repositório)

this.repositório = repositório;

(

O código de busca pode ter um comportamento um pouco diferente aqui. Um

conjunto de dados é um container de dados orientados a tabelas e pode armazenar

dados provenientes de diversas tabelas. Por esse motivo, é melhor carregar os dados
em um conjunto de dados.

class GatewayDeDados....
public void CarregarTudo( ) {

String comando = String. Format ( "select * from {0}", NomeDaTabela) ;

}

repositório. PreencherDados (comando, NoneDaTabela) ;

public void CarregarCondicional (String cláusulaCondicional) {
String comando =
String.

)

repositório.

Format ("select * from {0} where {1}", NomeDaTabela,

cláusulaCondicional) ;

PreencherDados (comando, NoneDaTabela) ;

abstract public String NomeDaTabela (get; }

class GatewayPessoa...
public override String NomeDaTabela {

}

get {return "Pessoa"; }

class RepositórioDeConjuntosDeDados...
public void PreencherDados (String consulta, String nomeDaTabela)

{

if (adaptadoresDeDados.
Contains (noneDaTabela)) throw new MultipleLoadexception( ) ;
OleDhDatandapter ad = new OleDbDataAdapter (consulta, DB.connection) ;
OleDbConmandBuilder construtor= new OleDbCommandBuilder
(ad) ;
ad.Fill (dados, noneDaTabela) ;
adaptadoresDeDados.
Add (nomeDaTabela, ad) ;

Para atualizar os dados, você manipula o conjunto de dados diretamente no có-

digo cliente.

pessoa.CarregarTudo( );
pessoa [chave] ["sobrenone"] = "Odell";
pessoa. repositório.Atualizar(

);

O gateway pode ter um indexador para tornar mais fácil obter linhas específicas.
class GatewayDeDados...
public DataRow this [long chave)

get{

(

String filtro = String.Format
("id = {0}", chave);

CapiruLo 10

}

}

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

157

return Table.Select (filtro) [0];

public override DataTable Table {

}

get{ return Dados. Tables [NomeDaTabela) ; }

9
5
E

A atualização dispara o código de atualização no repositório.

class RepositérioDeConjuntosDeDados. .
public void Atualizar( ) (
foreach (String tabela in adaptadoresDeDados.Keys)
((OlenbDataRdapter) adaptadoresDeDados [tabela] ) Atualizar (Dados, tabela);
}
public DataTable this [String nomeDaTabela] {
get {return Dados. Tables [nomeDaTabelal ; }

}

A inserção pode ser feita essencialmente da mesma forma: obtenha um conjun-

to de dados, insira uma nova linha na tabela de dados e preencha cada coluna. No
entanto, um método de atualização pode fazer a inserção em uma única chamada.

class GatewayDeDados. .
public long Inserir

(String sobrenome,

String prenome,

long chave = new PersonGatewayDS() GetNextID( ) ;
DataRow novabinha = Table.NewRow| |;
novaLinha["id"]

= chave;

int númeroDeDependentes)

novaLinha [*sobrenone"] = sobrenone;
novaLinha["prenome"] = prenome;
novabinha[ 'númeroDeDependentes"] = númeroDeDependentes;
Table.Rows.Add (novaLinha) ;
return chave;

{

158

Parte Il e Os PaprOes

Gateway de Linha de Dados (Row Data Gateway)

Um objeto que age como um Gateway (436) para um único registro em uma
Busca Pessoa

encontrar (id)

encontrarPeloSobrenome
(String)

jee}

8

de Dados

fonte de dados. Haverá uma instância por linha.

Gateway Pessoa
sobrenome
prenome
númeroDeDependentes
inserir

atualizar
excluir

Embutir código de acesso ao banco de dados em objetos na memória pode lhe trazer

algumas desvantagens. Para começar, se os seus objetos na memória tiverem sua pró-

pria lógica de negócio, acrescentar o código de manipulação do banco de dados au-

menta a complexidade. Os testes também ficam complicados uma vez que, se os seus
objetos na memória estiverem ligados a um banco de dados, a execução dos testes fica
mais lenta devido à sobrecarga do acesso ao banco de dados. Você pode ter de acessar
múltiplos bancos de dados com todas aquelas pequenas e irritantes variações de SQL.

Um Gateway de Linha de Dados fornece objetos que parecem exatamente com o
registro na sua estrutura de registros, mas que podem ser acessados com os mecanismos normais da sua linguagem de programação. Todos os detalhes do acesso à fonte de dados ficam escondidos atrás desta interface.
Como Funciona
Um Gateway de Linha de Dados age como um objeto que encapsula exatamente um

único registro, tal como uma linha de um banco de dados. Nesta estrutura, cada co-

luna no banco de dados torna-se um campo. Normalmente, o Gateway de Linha de Dados faz qualquer conversão de tipo necessária dos tipos da fonte de dados para os tipos na memória, porém esta conversão é muito simples. Este padrão armazena os
dados de uma linha de modo que um cliente possa então acessar diretamente o Ga-

teway de Linha de Dados. O gateway age como uma boa interface para cada linha de da-

dos. Essa abordagem funciona especialmente bem em Roteiros de Transações (120).

Com o Gateway de Linha de Dados, você se depara com as questões relativas a

onde colocar as operações de busca que geram este padrão. Você pode usar métodos
estáticos de busca, mas eles impedem o polimorfismo, se você quiser substituir diferentes métodos de busca para diferentes fontes de dados. Neste caso, muitas vezes, faz sentido ter objetos de busca separados de modo que cada tabela em um ban-

co de dados relacional tenha uma classe de busca e uma classe gateway para os resultados (Figura 10.2).

CapiruLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

159

Muitas vezes é difícil perceber a diferença entre um Gateway de Linha de Dados

e um Registro Ativo (165). O ponto crucial da questão é se há alguma lógica de dominio presente. Se houver, você tem um Registro Ativo (165). Um Gateway de Linha

de Dados deve conter apenas a lógica de acesso ao banco de dados e nenhuma lógi-

ca de domínio.
Do mesmo modo que com qualquer outra forma de encapsulamento tabular,
você pode usar um Gateway de Linha de Dados com uma visão ou pesquisa assim como com uma tabela. Neste padrão, muitas vezes, as atualizações acabam por ser
mais complicadas, uma vez que você tem que atualizar as tabelas correspondentes.
Além disso, se você tiver dois Gateways de Linha de Dados que operam sobre as mesmas tabelas, você pode descobrir que o segunda Gateway de Linha de Dados que vo-

cê atualiza desfaz as alterações do primeiro. Não há um modo genérico de evitar isso. Simplesmente, os desenvolvedores precisam estar conscientes de como os Gateways de Linha de Dados virtuais são formados. Afinal, a mesma coisa pode acontecer

com visões atualizáveis. É claro que você pode decidir não fornecer operações de
atualização.

Os Gateways de Linha de Dados tendem a ser um pouco tediosos para codifi-

car, mas são bons candidatos para a geração de código baseada em um Mapeamento em Metadados (295). Desta forma, todo o código de acesso ao banco de dados

pode ser automaticamente criado para você durante o seu processo automatiza-

do de construção.
Quando Usá-lo

A escolha do Gateway de Linha de Dados muitas vezes exige dois passos: primeiro, usar
ou não um gateway e, segundo, usar um Gateway de Linha de Dados ou um Gateway de Tabela de Dados (151).

Uso o Gateway de Linha de Dados com maior freqiiéncia quando estou usando

um Roteiro de Transação (120). Neste caso, ela decompõe eficientemente o código de
acesso ao banco de dados e permite que este seja reutilizado facilmente em diferen-

tes Roteiros de Transação (120).

Não uso um Gateway de Linha de Dados quando estou usando um Modelo de Do-

minio (126). Se o mapeamento for simples, o Registro Ativo (165) faz o mesmo trabalho sem uma camada adicional de código. Se o mapeamento for complexo, o Mapea-

dor de Dados (170) funciona melhor, na medida em que ele é melhor para desacoplar

a estrutura de dados dos objetos do domínio uma vez que estes objetos não precisam
conhecer o layout do banco de dados. É claro que você pode usar o Gateway de Linha
de Dados para isolar os objetos do domínio da estrutura do banco de dados. Isso é
uma boa coisa se, quando você estiver usando o Gateway de Linha de Dados, estiver al-

terando a estrutura do banco de dados e não quiser alterar a lógica do domínio. To-

davia, fazer isso em larga escala lhe leva a três representações de dados: uma na ló-

gica do domínio, uma no Gateway de Linha de Dados e uma no banco de dados - e is-

so é demais. Por essa razão, normalmente, faço o Gateway de Linha de Dados espelhar
a estrutura do banco de dados.
Tenho visto com interesse Gateways de Linha de Dados usados muito eficiente-

mente com Mapeadores de Dados (170). Embora isso pareça demandar um trabalho

adicional, pode ser eficaz se os Gateway de Linha de Dados forem gerados automati-

camente a partir de metadados enquanto que os Mapeadores de Dados (170) são fei-

tos à mão.

Parte Il e Os PaprOes

160

Fossageum

sopeq ap
euur ap Aemayen

onou [onsiõos epe> esed],
sopep ap ojunfuo> wn

L = plazaym seossad wows 3>ajas

seossagagiopesimbsagum

onou

el

(Lequosua

‘seyul| wa epeaseq sopeg ap seyu!] ap Áemejes ewn wo? eosnq eun esed saosesayu|

1
1
1

sopeg ap osueg

aquaypun

TOL

eanbiy

Capiruto 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

161

Se você usar o Roteiro de Transação (120) com o Gateway de Linha de Dados, você

pode perceber que existe lógica de negócio repetida em multiplos roteiros, lógica es-

ta que faria sentido no Gateway de Linha de Dados. Mover esta lógica irá gradualmente transformar seu Gateway de Linha de Dados em um Registro Ativo (165), o que fre-

qiientemente é bom, uma vez que reduz a duplicação na lógica de negócio.
Exemplo: Um Registro Pessoa (Java)

Aqui está um exemplo de um Gateway de Linha de Dados. É uma tabela simples, Pessoa.
create table pessoas

(ID int primary key, sobrenome varchar,

prenome varchar, númeroDeDependentes int)

GatenayPessoa é um gateway para a tabela. Ele começa com atributos e métodos de

acesso.

class Catenaypessoa...
private String sobrenone;
private String prenome;

private int númeroDeDependentes;
public String lerSobrenone( ) {
}

return

sobrenome;

public void gravarSobrenome (String sobrenome)

}

this.sobrenone = sobrenone;

{

public String lerPrenone ( ) {
return prenome;

)

public void gravarPrenome (String prenome)
this.prenome = prenome;

(

}

public int lerNúmeroDeDependentes ( ) {

return númeroDeDependentes;

public void gravarNúmeroDeDependentes

(int númeroDeDependentes)

this númeroDeDependentes = númeroDeDependentes;

{

A classe gateway pode, ela própria, lidar com atualizações e inserções.

class GatewayPessoa...
private static final String stringDaDeclaraçãoDeatualização =
"UPDATE pessoas "+
"set sobrenome
" where id=?";

?, prenone = ?, númeroDeDependentes = 2" +

public void atualizar () {

PreparedStatement declaraçãoDeAtualização = null;
try {
declaracéoDeAtualizagao

= DB.prepare (stringDaDeclaraçãoDeAtualização) ;

declaraçãoDentualização .setstring(1, sobrenome) ;

162

Parte ll e Os PaprOes

declaraçãoDeatualização.setString(2, prenome) ;
declaraçãoDentualização..set Int (3, númeroDeDependentes) ;
declaraçãoDeRtualização.setInt(4, lerID( ) .intValue( ));
declaraçãoDeatualização
execute ( ) ;

) catch (Exception e) {

throw new ApplicationException (e);
} finally (DB.cleanUp(declaraçãoDeatualização) ;
}

}
private static final String stringDaDeclaraçãoDeInserção =
"INSERT INTO pessoas VALUES(?, ?, 2, 2)";
public Long inserir () {
PreparedStatement declaraçãoDeInserção = null;
try {
declaraçãoDeInserção = DB. prepare (stringDaDeclaracdoDeInser¢ao) ;
set ID(descobreProximoIDBancoDeDados | }) ;
declaraçãoDeInserção.setInt

(1,

lerID.intValue(

));

declaraçãoDeInserção.setString(2, sobrenone) ;
declaraçãoDeInserção.setString(3, prenome) ;
declaraçãoDeInserção.setTnt(4, ntimeroDeDependentes) ;
declaraçãoDeInserção.execute( ) ;
Registro.adicionarPessoa
(this) ;

return lerID( );

) catch

(SQLException e) (

throw new ApplicationException (e) ;

) finally (DB.cleanUp (declaraçãoDeInserção) ;

}

Para buscar pessoas no banco de dados, temos uma classe separada PesquisadorDePessoas. Esta trabalha junto com portão gateway para criar novos objetos gateway.

class PesquisadorDePessoas...

private static final String stringDaDeclaraçãoDeBusca =
"SELECT id, sobrenone, prenome, númeroDeDependentes " +
”

FROM pessoas "+

* WHERE id = 2";

public GatewayPessoa procurar (Long id ) {

GatewayPessoa resultado = (GatewayPessoa) Registro. lerPessoa (id) ;
if (resultado != null) return resultado;
PreparedStatement declaraçãoDeBusca = null;
ResultSet rs = null;
try {
declaraçãoDeBusca = DB. prepare (stringDaDeclaraçãoDeBusca) ;
declaraçãoDeBusca.setLong/1,

id.longValue{

rs = declaraçãoDeBusca.executequery( );
rs.next( );

resultado = GatevayPessoa. carregar (rs) ;
return

} catch

resultado;

(SQLException e) {

throw new ApplicationException (e);

} finally (DB.cleantp(declaraçãoDeBusca, rs);

)

));

CapituLo 10

}

public GatewayPessoa procurar

}

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

163

(long id) {

return procurar (new Long(id)) ;

class GatewayPessoa...
public static GatewayPessoa carregar

(ResultSet rs) throws SQLException {

Long id = new Long(rs.getLong(1)) ;
GatewayPessoa resultado = (GatewayPessoa) Registro. lerPessoa(id) ;
if (resultado != null) return resultado;
String parametroSobrenone = rs.getString(2) ;
String parâmetroPrenone = rs.getString(3) ;
int parâmetroNúmeroDeDependentes = rs.getInt (4);

resultado = new GatewayPessoa(id, parânetroSobrenone, parâmetroPrenone, parânetroNá

meroDeDependentes) ;
Registro.adicionarPessoa (resultado) ;

return resultado;

Para pesquisar mais de uma pessoa de acordo com algum critério, podemos

fornecer um método de busca apropriado.

class PesquisadorDePessoas. ..
private static final String declaraçãoDeBuscaDeResponsáveis =
"SELECT id, sobrenone, prenone, númeroDeDependentes "+
"

FROM pessoas "+

* WHERE númeroDeDependentes > 0";

public List encontrarResponsáveis

() {

List resultado = new ArrayList ( );

Preparedstatement dec = null;
ResultSet rs = null;

try {

dec = DB.prepare (declaraçãoDeBuscaDeResponsáveis) ;

rs = dec. executeQuery( );

while (rs.next(}} {

resultado. add (CatewayPessoa.carregar (rs) );

}
return resultado;

} catch (SQLException e) {

throw new ApplicationBxception (e);

} finally {DB.cleanUp(dec,

}

rs) ;

O pesquisador de pessoas usa um Registro (448) para armazenar Mapas de Iden-

tidade (196).

Podemos agora usar os gateways a partir de um Roteiro de Transação (120).
PesquisadorDePessoas pesquisador = new PesquisadorDePessoas
Iterator pessoas = pesquisador.encontrarResponsaveis(

StringBuffer resultado = new StringBuffer( );
while (pessoas.hasNext( )) (

( }

}.iterator(

GatewayPessoa cada = (GatewayPessoa) pessoas.next ( |;

);

164

Parte Il e Os PaprOes

resultado.append
resultado. append
resultado. append
resultado. append
resultado. append
resultado. append

(cada. lerSobrenone ( ));
(* ");
(cada.lerPrenone| ));
(* ");
(String. value0f (cada. LerNúmeroDeDependentes( }) ;
(*\n*) ;

}

8

return resultado. toString( );

Exemplo: Um Armazenador de Dados
para um Objeto de Dominio (Java)
Na maior parte das vezes, uso o Gateway de Linha de Dados com Roteiros de Transação
(120). Se quisermos usar o Gateway de Linha de Dados a partir de um Modelo de Dominio (126), os objetos do domínio precisam chegar aos dados a partir do gateway. Em

vez de copiarmos os dados para o objeto do domínio, podemos usar o Gateway de Linha de Dados como um armazenador de dados para o objeto do domínio.

class Pessoa...

private GatewayPessoa dados;
public Pessoa

}

(GatewayPessoa dados) {

this.dados = dados;

Métodos de acesso na lógica de dominio podem então delegar os dados para o

gateway.

class Pessoa...

public int lerNómeroDeDependentes

( ) {

return dados. lertlómeroDeDependentes ( ) ;

A lógica de domínio usa os métodos de leitura (getters) para trazer os dados do

gateway.

class Pessoa...

public Dinheiro lerisenção(

) {

Dinheiro baseDeIsenção = Dinheiro. dollars (1500) ;
Dinheiro isençãoPorDependente = Dinheiro. dollars (750) ;
retum baseDeIsenção. adicionar (isençãoPorDependente
multiplicar (this. lerliimeroDeDependentes| )));

Capiruto 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (Dara Source)

165

Registro Ativo (Active Record)

Um objeto que encapsula uma linha de uma tabela ou visão de um banco de

dados, o acesso ao banco de dados e adiciona lógica de domínio a esses dados.

Pessoa

sobrenome
prenome
númeroDeDependentes
inserir

atualizar
excluir

lerisenção
estáMarcadoParaAuditoria
lerRendimentosTaxáveis

Um objeto carrega tanto dados quanto comportamento. Muitos desses dados são

persistentes e precisam ser armazenados em um banco de dados. O Registro Ativo usa
a abordagem mais óbvia, colocando a lógica de acesso aos dados no objeto do domínio. Dessa maneira todos os objetos pessoa sabem como ler e gravar seus dados do
banco de dados e no banco de dados.

Como Funciona

A essência de um Registro Ativo é um Modelo de Domínio (126) no qual as classes são

muito semelhantes à estrutura das tabelas correspondentes. Cada Registro Ativo é

responsável por salvar e buscar seus dados no banco de dados e também por qual-

quer lógica de domínio que atue sobre os dados. Esta pode ser toda a lógica de do-

minio na aplicação, ou você pode descobrir que alguma lógica de domínio é manti-

da em Roteiros de Transação (120) com o código compartilhado e orientado a dados

no Registro Ativo.

A estrutura de dados do Registro Ativo deve corresponder exatamente aquela do

banco de dados: um atributo na classe para cada coluna na tabela. O tipo dos atributos deve ser o mesmo dos dados entregues a você pela interface SQL — não faça nenhuma conversão neste estágio. Você pode considerar o uso do Mapeamento de Chave
Estrangeira (233), mas também pode deixar as chaves estrangeiras como estiverem.
Você pode usar o Registro Ativo com visões ou tabelas, ainda que as atualizações por

meio de visões sejam obviamente mais difíceis. Visões são particularmente úteis pa-

ra a geração de relatórios.
A classe Registro Ativo tem, tipicamente, métodos que realizam as seguintes
funções:

* Constroem uma instância do Registro Ativo a partir de uma linha de um conjunto de dados resultantes de uma consulta SQL.

* Constroem uma nova instância para posterior inserção na tabela.

166

Parte ll e Os Paorões

* Encapsulam consultas SQL, comumente usadas, e retornam objetos do tipo

Registro Ativo, pelos métodos de busca estáticos.

* Atualizam o banco de dados e nele inserem os dados do Registro Ativo.

* Léeme
E

gravam os atributos.

* Implementam alguns fragmentos de lógica de negó
Os métodos de leitura e gravação podem fazer algumas outras coisas inteli-

gentes, tais como converter os tipos orientados a SQL para tipos mais apropriados

para armazenamento em memória. Além disso, se você solicitar uma tabela relacio-

nada, o método de leitura pode retornar o Registro Ativo apropriado, mesmo se vo-

cê não estiver usando o Campo Identidade (215) na estrutura de dados (executando
um lookup).

Nesse padrão as classes são convenientes, mas não escondem o fato de que um

banco de dados relacional está presente. Como conseqiiéncia, você normalmente encontrará uma quantidade menor dos outros padrões de mapeamento objeto-relacional presentes quando estiver usando o Registro Ativo.
O Registro Ativo é muito semelhante ao Gateway de Linha de Dados (158). A principal diferença é que o Gateway de Linha de Dados (158) contém apenas métodos de
acesso ao banco de dados, enquanto que um Registro Ativo contém tanto lógica de dominio quanto acesso aos dados. Como a maioria das fronteiras em software, a linha
divisória entre os dois padrões não é muito precisa, mas é útil.

Devido ao grande acoplamento entre o Registro Ativo e o banco de dados, en-

contro com maior frequência métodos de busca estáticos neste padrão. Entretanto,
nada existe que o impeça de separar os métodos de busca em uma classe separada,
como discuti em Gateway de Linha de Dados (158), e isso facilita os testes.

Assim como com os outros padrões tabulares, você pode usar o Reg istro Ativo
com uma visão ou pesquisa, bem como com uma tabela.

Quando Usá-lo

O Registro Ativo é uma boa escolha para a lógica de domínio que não seja muito com-

plexa, como criações, leituras, atualizações e exclusões. Derivações e validações baseadas em um único registro funcionam bem nesta estrutura.

Em um projeto inicial para um Modelo de Domínio (126), a escolha principal recai entre o Registro Ativo e o Mapeador de Dados (170). O Registro Ativo tem a vantagem
primária da simplicidade. É fácil construir Registros Ativos, e eles são fáceis de enten-

der. O principal problema relacionado ao seu uso é que eles trabalham bem somente
se os objetos do tipo Registro Ativo corresponderem diretamente às tabelas no banco

de dados em um esquema isomórfico. Se a sua lógica de negócio for complexa, você
logo irá desejar usar relacionamentos diretos entre seus objetos, coleções, herança e
assim por diante. Estas técnicas não são facilmente mapeadas em Registros Ativos, e
adicioná-las uma a uma torna-se muito confuso. Isso é o que lhe levará ao uso alternativo dos Mapeadores de Dados (170).

Outro argumento contra o Registro Ativo é o fato de que ele acopla o projeto dos

objetos ao projeto do banco de dados. Isso torna mais difícil refatorar esses projetos

à medida que o projeto prossegue.

O Registro Ativo é um bom padrão a considerar se você estiver usando o Roteiro
de Transação (120) e estiver começando a sentir os problemas da duplicação de códi-

CapiruLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

167

go e a dificuldade, muitas vezes, presente nos Roteiros de Transação (120) para atuali-

zar roteiros e tabelas. Neste caso, você pode começar gradualmente a criar Registros
Ativos e então lentamente neles refatorar o comportamento. Muitas vezes, é útil primeiro encapsular as tabelas como um Gateway (436) e só depois começar a mover o
comportamento de modo que as tabelas evoluam para um Registro Ativo.

Exemplo: Uma Pessoa Simples (Java)
Este é um exemplo simples, até mesmo simplista, para mostrar como o âmago do Registro Ativo funciona. Começamos com uma classe Pessoa básica.
class Pessoa...

private String sobrenone;
private String prenome;

private int númeroDeDependentes;

Também há um campo ID na superclasse.

O banco de dados tem exatamente a mesma estrutura.

create table pessoas (ID int primary key, sobrenome varchar,

prenome varchar, númeroDeDependentes int)

Para carregar um objeto, a classe Pessoa localiza o objeto e efetua a carga. Para

isso são usados métodos estáticos na classe Pessoa.
class Pessoa...

private static final String stringDaDeclaraçãoDeBusca =
"SELECT id, sobrenone, prenome, númeroDeDependentes " +
* FROM pessoas "+
"WHERE id = 2";
public static Pessoa procurar (Long id ) (

Pessoa resultado = (Pessoa) Registro. lerPessoa (id) ;
if (resultado != null) return resultado;

PreparedStatement declaraçãoDeBusca = null;
ResultSet rs = null;
try {
declaraçãoDeBusca = DB. prepare (stringDaDeclaraçãoDeBusca) ;
declaragdoDeBusca.setLong(1, id.longValue( |);
rs = declaraçãoDeBusca.executequery( );
rs.next( );

resultado = carregar (rs) ;
retum

resultado;

) catch (SQLException e) {
throw new ApplicationException

(e);

) finally (
DB.cleantp (declaraçãoDeBusca, rs) ;
}

}
public static Pessoa procurar (long id) {
}

return procurar (new Long(id)) ;

168

Parte Il»

Os Paorões

public static Pessoa carregar (ResultSet rs) throws SQLException {

Long id = new Long(rs.getLong(1));
Pessoa resultado = (Pessoa) Registro. lerPessoa (id) ;
if (resultado != null) return resultado;
String parametroSobrenone = rs.getString(2) ;
String parâmetroPrenome = rs.getString(3) ;
int parâmetroNúmeroDeDependentes = rs.getInt
(4) ;
resultado = new Pessoa (id, parâmetroSobrenone, parânetroPrenone, parametrolineroDeDependentes) ;
Registro.adicionarPessoa (resultado) ;
return resultado;

Para atualizar um objeto basta um simples método de instancia.
class Pessoa...

private static final String stringDaDeclaraçãoDeAtualização =
"UPDATE pessoas "+
” SET sobrenome ? prenome
núneroDeDependentes = 2" +
"WHERE id =?
public void atualizar () {
PreparedStatement declaracdoDeAtualizagio = null;
try{
declaraçãoDeAtualização

= DB. prepare (stringDaDeclaraçãoDeAtualização) ;

declaraçãoDentualização. setstring(1, sobrenone) ;
declaraçãoDeAtualização.setString(2,
declaraçãoDeAtualização .setString(3,
declaraçãoDeRtualização.setString(4,
declaraçãoDeAtualização .execute( ) ;

) catch

prenome) ;
númeroDeDependentes) ;
lerID( ).intValuel ));

(Exception e) (

throw new ApplicationException (e);

) finally (

}

DB.cleanUp (declaraçãoDeAtualização) ;

Inserções são, na sua maioria, também muito simples.
class Pessoa...

private static final String stringDaDeclaraçãoDeInserção =
"INSERT INTO pessoas VALUES (2, ?, 2, 2)";

public Long inserir () {

PreparedStatement declaraçãoDeInserção = null;
try {

declaraçãoDeInserção = DB.prepare (stringDaDeclaraçãoDeInserção) ;

set ID(encontrarProximolDdaBaseDeDados ( ));
declaraçãoDeInserção.setint (1, lerID.intvalue( });
declaraçãoDeInserção. setString(2, sobrenome) ;

declaraçãoDeInserção.setString(3, prenome) ;
declaraçãoDeInserção.set
Int (4, númeroDeDependentes) ;
declaraçãoDeInserção
execute ( );

Registro.adicionarPessoa( this) ;
return lerID( );

CapiruLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (Dara Source)

169

) catch (SQLException e) {

throw new ApplicationException (e);
} finally {

}

DB.cleanUp (declaraçãoDeInserção) ;

Qualquer lógica de negócio, tal como o cálculo de uma isenção, vai diretamen-

te para a classe Pessoa.
class Pessoa...

public Dinheiro lerisenção( ) (

Dinheiro baseDeisenção = Dinheiro .dollars(1500) ;

Dinheiro isençãoPorDependente = Dinheiro. dollars (750) ;

return baseDeIsenção. adicionar (isençãoPorDependente.multiplicar (this. leriiúmeroDeDependentes ( )));

170

Parte Il e Os PaprOes

Mapeador de Dados (Data Mapper)

Uma camada de Mapeadores (442) que move dados entre os objetos e um banco
de dados mantendo-os independentes um do outro e do próprio mapeador.
8
Pessoa

s

8

sobrenome
prenome
|<
númeroDeDependentes
lerisenção
estáMarcadoParaAuditoria

Mapeador Pessoa
—
ase
inserir
atualizar
excluir

lerRendimentosTaxáveis

Objetos e bancos de dados relacionais usam mecanismos diferentes para estruturar

os dados. Muitas partes integrantes de um objeto, tais como coleções e herança, não

estão presentes em bancos de dados relacionais. Quando você cria um modelo de ob-

jetos com muita lógica de negócio, o uso destes mecanismos é valioso para melhor
organizar os dados e o comportamento associado. Isso leva a esquemas diversos, isto é, o esquema dos objetos e o esquema relacional não combinam.

Ainda assim, você precisa transferir dados entre os dois esquemas, e esta

transferência se torna, por si só, complexa. Se os objetos na memória conhecem a
estrutura do banco de dados relacional, alterações em um tendem a se propagar
para o outro.

O Mapeador de Dados é uma camada de software que separa os objetos na memó-

ria do banco de dados. Sua responsabilidade é transferir dados entre os dois e tam-

bém isolá-los um do outro. Com o Mapcador de Dados, os objetos na memória não precisam nem mesmo saber que há um banco de dados presente. Eles não precisam conter comandos SQL e certamente não precisam ter nenhum conhecimento do esquema do banco de dados. (O esquema do banco de dados sempre ignora os objetos que

o usam.) Uma vez que o Mapeador de Dados é uma forma de Mapeador (442), ele próprio não é conhecido pela camada de domínio.

Como Funciona
A separação entre o domínio e a fonte de dados é a principal função de um Mapeador

de Dados, mas existe uma grande quantidade de detalhes que têm que ser tratados

para que isso aconteça. Existe ainda uma diversidade grande de formas pelas quais

camadas de mapeamento são criadas. Muitas das observações aqui são bastante ge-

rais, porque estou tentando lhe dar uma visão geral do que você precisa para separar
o joio do trigo.

Começaremos com um exemplo bastante básico de um Mapeador de Dados. Este

é o estilo mais simples que você pode ter para esta camada e pode parecer não valer
a pena usá-lo. Quando usamos exemplos simples de mapeamento de banco de dados, outros padrões são normalmente mais simples e, portanto, melhores. Você normalmente usará o Mapeador de Dados em casos mais complicados. No entanto, é mais

fácil explicar as idéias se começamos de modo simples em um nível bastante básico.

Um caso simples envolveria, por exemplo, uma classe Pessoa e uma classe Ma-

peador de Pessoa. Para carregar uma pessoa do banco de dados, um cliente chama-

Capiruto 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

171

ria um método de busca no mapeador (Figura 10.3). O mapeador usa um Mapa de

Identidade (196) para verificar se a pessoa já está carregada; em caso contrário, ele a

carrega.

As atualizações são mostradas na Figura 10.4. Um cliente solicita que o mapea-

dor grave um objeto do domínio. O mapeador extrai os dados do objeto de domínio
€ os envia para o banco de dados.

Toda a camada do Mapeador de Dados pode ser substituída, para efetuar testes,

ou para permitir que uma única camada de domínio trabalhe com diferentes bancos

de dados.

Um Mapeador de Dados simples apenas mapearia, campo a campo, uma tabela

do banco de dados em uma classe equivalente em memória. É claro que as coisas

normalmente não são simples. Os Mapeadores precisam de uma série de estratégias
para lidar com classes que se transformam em campos múltiplos, classes que têm

múltiplas tabelas, classes com herança, além de ter de conectar os objetos uns aos ou-

tros assim que eles tiverem sido extraídos do banco. Os vários padrões para o mapeamento objeto-relacional apresentados neste livro lidam todos com essas estratégias.

É normalmente mais fácil desenvolver esses padrões com um Mapeador de Dados do

que com as outras alternativas de organização.

Quando se trata de inserções e atualizações, a camada de mapeamento do ban-

co de dados precisa saber que objetos mudaram, quais novos objetos foram criados e
quais foram destruídos. Ela também tem que encaixar toda essa carga de trabalho em

um contexto transacional. O padrão Unidade de Trabalho (184) é uma boa maneira de

organizar isso.
A Figura 10.3 sugere que uma única requisição a um método de busca resulta

em uma única consulta SQL. Isso não é sempre verdade. Carregar um pedido típico,

com múltiplas linhas de pedido pode envolver a carga das linhas também. A requisição do cliente normalmente leva a um conjunto de objetos sendo carregados: o projetista do mapeador decide exatamente quanto pegar de cada vez. A essência aqui é
minimizar as consultas ao banco de dados, assim, tipicamente, os métodos de pes-

quisa precisam conhecer um pouco sobre a forma como os clientes usam os objetos
de modo a poder tomar as melhores decisões na carga dos dados.
O exemplo apresentado leva a casos em que é necessário carregar diversas clas-

ses de objetos de domínio a partir de uma única consulta. Se você quiser carregar pedidos e linhas de pedidos, normalmente será mais rápido executar uma única con-

sulta que junte as tabelas dos pedidos e das linhas dos pedidos. Você então usa o con-

junto resultante para carregar as instâncias do pedido e das linhas (página 239-240).
Uma vez que os objetos são bastante interconectados, você, em algum ponto,

tem de parar de trazer os dados. De outra forma, você possivelmente irá trazer todo

o banco de dados como resultado de uma única consulta. Mais uma vez, as camadas
de mapeamento têm uma série de técnicas para lidar com este problema ao mesmo

tempo em que, usando a Carga Tardia (200), minimizam o impacto sobre os objetos na

memória. Por esse motivo, os objetos na memória não podem desconhecer completamente a camada de mapeamento. Eles podem precisar conhecer os métodos de

pesquisa e alguns outros mecanismos.

Uma aplicação pode ter um ou vários Mapeadores de Dados. Se você estiver codificando explicitamente seus mapeadores, é melhor usar um para cada classe do do-

minio ou raiz de uma hierarquia de domínio. Se você estiver usando um Mapeamen-

to de Metadados (295), você pode ter êxito com apenas uma única classe de mapea-

mento. Neste último caso, o problema limitante são os métodos de pesquisa. Em uma

g

Parte Il e Os PaprOes
172

eossad : uquews

sope
ap sopeadeyy
q

ajueyjnsas
ojunfuo>
um

sopep soyinui 19}

‘nou

-———5 >

tusenuosua

eossad ap
Jopesdeu um

TS

“sopep ap o>ueq wn ap sopep opuazes,

Eloa!

sopeynsas ap ojuníuos win
ac

edew un

| = plasaym seossad wo , pajs

sopep ap osueg o

¢"01 en

