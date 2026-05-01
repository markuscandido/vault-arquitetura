# 11_Cap_08_Juntando_Tudo

126

85

Parte Il e Os PApROes

Modelo de Dominio (Domain

e
8

Model)

Um modelo de objetos do domínio que incorpora tanto o comportamento

quanto os dados.

Contrato

receitaLançada (data)
calcularLançamentos
*
q

Produto

7

calcularLangamentos (contrato)

Estratégégieia de
Lançamento

Estratégia
Completa de

Lançamento

No pior caso, a lógica de negócios pode ser muito complexa. As regras e a lógica de

negócio descrevem muitos casos diferentes e muitas variações de comportamento.

Os objetos foram projetados justamente para lidar com essa complexidade. Um Mo-

delo de Domínio cria uma rede de objetos interconectados em que cada um representa

algum conceito significativo, que pode ser tão grande quanto uma corporação, ou tão
pequeno quanto uma linha em um formulário de pedidos.

Como Funciona
Colocar um Modelo de Domínio em uma aplicação envolve a inserção de uma camada

inteira de objetos que modelam a área de negócios com a qual você está trabalhando.
Você encontrará objetos que representam os dados do negócio e objetos que captu-

ram as regras usadas pelo negócio. Em sua maior parte, dados e processos são com-

binados para juntar os processos aos dados com os quais eles trabalham.

Um modelo de domínio OO frequentemente se parece com um modelo de ban-

co de dados, embora muitas diferenças persistam. Um Modelo de Domínio mistura da-

dos e processos, tem atributos multivalorados, uma complexa rede de associações e
usa herança.

Em conseqiiéncia, consigo enxergar dois estilos de Modelo de Domínio. Um Modelo de Domínio simples se parece muito com o projeto de um banco de dados, tendo,

na maior parte das vezes, um objeto de domínio para cada tabela do banco de dados.
Um Modelo de Domínio rico pode parecer diferente do projeto de um banco de dados,

CapituLo 9 e PADRÕES DE LÓGICA DE Domínio

127

com herança, estratégias e outros padrões [Gang of Four], e complexas redes de pe-

5
s

mais complexa, mas é mais difícil de mapear para o banco de dados. Um Modelo de

8

quenos objetos interconectados. Um Modelo de Domínio rico é melhor para a lógica

Dominio simples pode usar o Registro Ativo (165), enquanto que um Modelo de Dominio rico requer o Mapeador de Dados (170).

Uma vez que o comportamento do negócio é sujeito a muitas alterações, é im-

portante poder modificar, criar e testar esta camada facilmente. Em conseqiiéncia,
quanto menor o acoplamento do Modelo de Domínio a outras camadas no sistema, melhor. Você perceberá que uma linha mestra de muitos padrões de camadas é manter
tão poucas dependências entre o modelo de domínio e outras partes do sistema

quanto for possível.

Existem vários escopos diferentes que você pode usar com um Modelo de Domi-

nio. O caso mais simples é uma aplicação monousuário na qual todo o conjunto de
objetos é lido de um arquivo e trazido para a memória. Uma aplicação desktop pode
funcionar desta maneira, porém isso é menos comum para um sistema de informa-

ções multicamadas, simplesmente porque há objetos demais. Trazer todos os objetos
para a memória demora muito e consome memória demais. A beleza dos bancos de
dados orientados a objetos é que eles dão a impressão de fazer isso enquanto movem
objetos entre a memória e o disco.
Sem um banco de dados OO, você mesmo tem que fazer isso. Normalmente,

uma sessão envolve trazer para a memória todos os objetos nela envolvidos. Isso certamente não significa trazer todos os objetos e, na maior parte das vezes, nem mesmo todas as classes. Assim, se você estiver olhando um grupo de contratos, poderia
trazer apenas os produtos referenciados pelos contratos dentro do seu conjunto de
trabalho. Se você estiver apenas efetuando cálculos nos contratos e em objetos de lan-

çamento de receitas, não precisa trazer para a memória nenhum objeto produto. O
que exatamente você trará para a memória é controlado pelos seus objetos de mapeamento de banco de dados.

Se você precisar do mesmo conjunto de objetos entre chamadas ao servidor, você tem de salvar o estado do servidor em algum lugar, o que é o assunto da seção sobre gravação do estado do servidor (página 93-94).
Uma preocupação comum com a lógica de domínio são os objetos de domínio

inchados. Quando você criar uma tela para manipular pedidos perceberá que uma

parte do comportamento do pedido só é necessário para esta tela. Se você colocar es-

sas responsabilidades no pedido, o risco é que a classe Pedido se torne grande demais porque está cheia de responsabilidades que só são usadas em um único caso de
uso. Essa preocupação leva as pessoas a considerarem se uma parte da responsabil

dade é geral, em cujo caso ela deve ficar na classe Pedido ou específica, em cujo caso
ela deve ficar em alguma classe de uso específico, que poderia ser um Roteiro de Tran-

sação (120) ou talvez a própria apresentação.

O problema em separar comportamento específico de uma situação de uso é
que isso pode levar a duplicação. O comportamento separado do pedido é difícil de
achar, de modo que as pessoas tendem a não encontrá-lo e o duplicam. A duplicação

pode levar rapidamente a mais complexidade e inconsistência, mas descobri que inchaços ocorrem com muito menor freqiiéncia do que esperado. Se realmente eles
ocorrerem, são relativamente fáceis de ver e não são difíceis de consertar. Meu conselho é não separar comportamento específico de uma situação de uso. Coloque tu-

do no objeto que parece ser o mais adequado. Conserte o inchaço quando, e se, ele se

tornar um problema.

128

85
e
3

Parte ll e Os Pacrões

Implementação Java
As discussões sobre o desenvolvimento de um Modelo de Domínio em J2EE

são

sempre acaloradas. Muitos dos materiais de ensino e dos livros introdutórios de

JQEE sugerem que você use entity beans para desenvolver um modelo de domínio,
mas há sérios problemas com esta abordagem, pelo menos com a especificação
corrente (2.0).

Os entity beans são mais úteis quando você usa CMP (Container Managed
Persistence). Na verdade, eu diria que não há muito sentido em usar entity beans

sem CMP. Entretanto, CPM é uma forma limitada de mapeamento objeto-relacional, e não consegue suportar muitos dos padrões que você precisa em um Modelo

de Domínio rico.

Os entity beans não devem ser reentrantes. Isso quer dizer que, se você fizer

uma chamada de um entity bean para outro objeto, esse outro objeto (ou qualquer
objeto que ele chamar) não pode chamar o primeiro entity bean. Um Modelo de Do-

minio rico frequentemente usa reentrância, de modo que esta é uma limitação im-

portante. Ela fica pior pelo fato de que é difícil localizar comportamento reentrante. Em conseqiiéncia, algumas pessoas dizem que um entity bean nunca deve chamar outro. Embora isso evite reentrancia, diminui significativamente as vantagens

de usar um Modelo de Domínio.
Um Modelo de Domínio deve usar objetos de granularidade baixa, com inter-

faces de granularidade baixa. Os entity beans podem ser remotos (antes da versão
2.0 eles tinham que ser). Se você tiver objetos remotos com interfaces de granularidade baixa, obterá desempenhos péssimos. Você pode evitar facilmente esse
problema, usando apenas interfaces locais para seus entity beans em um Modelo

de Domínio.
Para rodar os entity beans você precisa de um container e um banco de dados

conectado. Isso irá aumentar o tempo de criação e também o de teste, já que estes

têm que ser executados sobre um banco de dados. Os entity beans são também complicados para depurar.

A alternativa é usar objetos Java normais, embora isso muitas vezes cause

uma reação de surpresa — é incrível como muitas pessoas pensam que você não pode rodar objetos Java normais em um container EJB. Cheguei à conclusão de que as
pessoas se esquecem dos objetos Java normais porque eles não têm um nome bonito. É por isso que, durante a preparação para uma palestra em 2000, Rebecca Parsons, John Mackenzie e eu lhes demos um: POJO (Plain Old Java Objects — velhos
e bons objetos Java). Um modelo de domínio POJO é fácil de ser reunido e cons-

truído, pode rodar e ser testado fora de um container EJB e é independente do EJB

(talvez seja por isso que os vendedores de EJB não lhe encorajem a usá-los).
Minha visão global é que usar entity beans como um Modelo de Domínio fun-

ciona se você tiver uma lógica de domínio bastante simples. Neste caso, você pode
construir um Modelo de Domínio que tenha um relacionamento simples com o banco de dados: na sua maior parte apenas um entity bean por tabela do banco de dados. Se você tiver uma lógica de domínio mais rica, com herança, estratégias e ou-

tros padrões mais sofisticados, você estará melhor servido com um modelo de do-

minio POJO e um Mapeador de Dados (170), usando uma ferramenta comercial ou

uma camada desenvolvida em casa.
A maior frustração para mim no uso de EJB é que considero um Modelo de

Domínio complicado o suficiente para lidar, e quero ficar tão independente quanto

possível dos detalhes do ambiente de implementação. O EJB faz com que você tenha de pensar nele quando pensa no Modelo de Dominio, o que significa que tenho
que me preocupar tanto com o domínio quanto com o ambiente EJB.

CapituLo 9 e PADRÕES DE LÓGICA DE Dominio

Quando

129

usá-lo

Se o como para um Modelo de Domínio é difícil porque é um assunto extenso, o quando é difícil devido tanto à falta de clareza quanto à simplicidade do conselho. Tudo
se resume à complexidade do comportamento do seu sistema. Se você tiver regras
de negócio complicadas e em constante mudança, envolvendo validação, cálculos
e derivações, é bastante possível que você queira um modelo de objetos que lide

com elas. Por outro lado, se você tiver verificações simples para fazer (not null) e

apenas algumas somas para calcular, um Roteiro de Transação (120) é uma aposta

melhor.

Um fator importante aqui é o quão confortável a equipe de desenvolvimento se

sente com objetos de domínio. Aprender como projetar e usar um Modelo de Domínio

é um exercício significativo — que já proporcionou vários artigos sobre a “mudança de
paradigma”

representado pelo uso de objetos. Certamente

é necessário prática e

orientação para que alguém se acostume a um Modelo de Domínio, mas descobri que,

uma vez acostumadas, muito poucas pessoas querem voltar a um Roteiro de Transação

(120), a não ser para problemas muito simples.
Se você estiver usando um Modelo de Domínio, minha primeira escolha para a interação com o banco de dados é o Mapeador de Dados (170). Isso manterá seu Modelo

de Domínio independente e é a melhor abordagem para lidar com casos em que o Modelo de Domínio e o esquema do banco de dados divergem.

Quando você usa o Modelo de Domínio, talvez possa considerar a Camada de Ser-

viço (141) para dar ao seu Modelo de Domínio uma API mais separada.
Leitura Adicional

Qualquer livro sobre projeto OO falará sobre Modelos de Domínio,já que a maior par-

te do que as pessoas chamam de desenvolvimento OO é centrado sobre seu uso.

Se você estiver procurando por um livro introdutório sobre projeto OO, atual-

mente meu favorito é [Larman]. Para exemplos de Modelo de Domínio, veja [Fowler
AP]. [Hay] também dá bons exemplos em um contexto relacional. Para construir um

bom Modelo de Domínio, você deve ter uma boa compreensão sobre os conceitos rela-

cionados ao uso de objetos. Para isso sempre gostei de [Martin e Odell]. Para com-

preender os padrões que você verá em um Modelo de Domínio rico, ou em qualquer

outro sistema OO, você deve ler [Gang of Four].
Eric Evans está escrevendo atualmente um livro [Evans] sobre a criação de Mo-

delos de Domínio. Enquanto escrevo isto, vi apenas um manuscrito inicial, mas ele pa-

rece bastante promissor.

Exemplo: Lançamento de Receita (Java)
Uma das maiores frustrações em descrever um Modelo de Domínio é o fato de que qual-

quer exemplo que eu mostre tem de ser necessariamente simples para que você possa

entendê-lo. Contudo, esta simplicidade esconde a força do Modelo de Domínio. Você só

aprecia essa força quando tem um domínio realmente complicado.

Mas ainda que o exemplo possa não fazer justiça ao porquê de você querer um

Modelo de Domínio, pelo menos lhe dará uma boa idéia de como ele se parece. Por es-

ta razão, estou usando o mesmo exemplo que usei para o Roteiro de Transação (120),
uma pequena questão de lançamento de receitas.

8
>
>
E

130

85
e

Parte ll e Os Pacrões

Uma coisa a ser percebida imediatamente é que cada classe, neste pequeno

exemplo (Figura 9.3) contém tanto comportamento quanto dados. Mesmo a modes-

ta classe Lançamento de Receitas contém um método simples para descobrir se o va-

lor do objeto pode ser lançado em determinada data.

public class LançanentoDeReceitas...
private Dinheiro quantia;
private MéDate data;

public LangamentoDeReceitas (Dinheiro quantia, MfDate data)

}

this.quantia = quantia;
this.data = data;

{

public Dinheiro lerQuantia() {
return quantia;

boolean éLançávelEm(MDate aPartirDe)

}

(

return aPartirDe.after(data) || aPartirDe.equals
(data) ;

Calcular o quanto de uma receita já foi lançada em determinada data, envolve
tanto a classe Contrato quanto o LançamentoDeReceitas.

Contrato

ERES
= dataAssinatura

+ receitaLancada(data): Dinheiro

*

1

Produto

+ calcularLançamentos(Contrato)

+ calcularLangamentos()

*

1

vi

Estratégia de Lancamento

x

+ calcularLançamentoDeReceitas(Contrato)

Lançamento de Receitas

A

=quantia: Dinheiro
— data:

+ éLançavelEm(data)

Estratégia Completa de Lançamento
+ calcularLançamentoDeReceitas(Contrato)

Estratégia de Lançamento em Três Etapas

~ primeiraEtapadeLançamento
- segundaEtapadeLançamento
+ calcularLançamentosDeReceitas(Contrato)

Figura 9.3

Diagrama de classes para as classes do exemplo de um Modelo de Dominio.

Carítuto 9 e PADRÕES DE LÓGICA DE Domínio

131

5
s

class Contrato ..
private List lançamentoDeReceitas = new ArrayList
();
public Dinheiro receitaLançada (MEDate aPartirDe) (

8

Dinheiro resultado = Dinheiro.dollars
(0) ;

Iterator it = lançamentoDeReceitas.iterator();
while (it.hasNext
()) {

LançamentoDeReceitas r = (LangamentoDeReceitas) it next();
if (r.éLançávelBn(adartirDe))

}

resultado = resultado.add(r.lerQuantia()) ;

return resultado;

Uma coisa comum que você descobre em modelos de dominio é como múlti-

plas classes têm de interagir para executar até as tarefas mais simples. Isso é o que le-

va à reclamação frequente de que em programas OO perde-se muito tempo passean-

do pelas classes, tentando encontrá-las. Esta reclamação tem bastante fundamento. O

benefício surge quando a decisão sobre se algo é lançável ou não em uma certa data

fica mais complexa, e outros objetos precisam saber. Manter o comportamento no ob-

jeto que precisa saber evita a duplicação de código e reduz o acoplamento entre os di-

ferentes objetos.

Olhar para o cálculo e a

criação destes objetos de lançamento de receitas refor-

ça o conceito de sistemas constituídos de muitos objetos pequenos. Neste caso, o cálculo e a criação começam com o cliente e são encaminhados através do produto para
uma hierarquia de estratégias. O padrão hierarquia [Gang of Four] é um padrão OO

conhecido que permite combinar um grupo de operações em uma pequena hierar-

quia de classes. Cada instância de um produto é conectada a uma única instância de

estratégia que determina o algoritmo usado para calcular o lançamento de receitas.
Neste caso, temos duas subclasses de estratégia de lançamento para os dois diferentes casos. A estrutura do código tem esta aparência:
class Contrato...
private Produto produto;

private Dinheiro receita;
private MéDate dataAssinatura;
private Long id;

public Contrato (Produto produto,

this. produto = produto;

Dinheiro receita,

MfDate dataAssinatura)

receita;

}

this.datadssinatura = dataAssinatura;

class Produto...
private String nome;

private EstratégiaLançamento estratégiaLançamento;
public Produto (String nome, EstratégiaLançamento estratégiaLancamento)

}

this.none = nome;
this.estratégiaLançamento = estratégiaLançamento;

public static Produto novoProcessadorDeTexto
(String nome) {
return new Produto(nome,

new EstratégiaLançamentoTotal());

{

{

132

85
E

Parte Il e Os PaprOes

}

public static Produto novaPlanilha
(String nome) {
return new Produto (nome, new EstratégiaLançamentoEnTrêsEtapas
(60, 90)) ;
}
public static Produto novoBancoDeDados (String nome)

}

{

return new Produto(nome, new EstratégiaLançamentoEnTrêsEtapas
(30, 60));

abstract class EstratégiaLancamento (
abstract void calcularLançamentoDeReceitas
(Contrato contrato);

}

class EstratégialançamentoTotal extends EstratégiaLançamento {

void calcularLançamentoDeReceitas
(Contrato contrato) (

)

contrato.adicionarLançamentoDeReceitas
(new LançamentoDeReceitas
(contrato. lerReceita (),
contrato. lerDataAssinatura()));

class EstratégiabancamentoBnTrésEtapas extends Estratégiabancamento {

private int primeiraBtapaDeLancamento;
private int segundaBtapaDeLançamento;
public EstratégiaLançamentoEnTrêsEtapas
(int primeiraBtapaDeLancanento,

{
}

int segundaRtapaDeLançamento)

this. primeiraBtapaDeLancamento = prineiraBtapaDeLançamento;
this. segundaBtapaDeLancamento

= segundaBtapaDeLancamento;

void calcularLançamentoDeReceitas
(Contrato contrato)

(

Dinheiro[] alocação = contrato. lerReceita() .alocar
(3) ;

contrato. adicionarLançamentoDeReceitas
(new LançamentoDeReceitas
(alocação [0], contrato. LerDataassinatura())) ;
contrato.adicionarLançamentoDeReceitas (new LançamentoDeReceitas

(alocação (1], contrato. lerDatanssinatura () .adicionaDias(pr neiraBtapaDeLançamento) )) ;
contrato. adicionarLançamentoDeReceitas
(new LançamentoDeReceitas
(alocação[2], contrato. lerDataAssinatura () .adicionaDias (segundaBtapaDeLancamento) )) ;

O grande valor das estratégias é que elas fornecem pontos de conexão bem limitados para estender a aplicação. Adicionar um novo algoritmo de lançamento de
receita exige apenas a criação de uma nova subclasse que sobrescreva o método cal-

cularLançamentoDeReceitas. Isso torna fácil estender o comportamento algorítmico da
aplicação.

Quando você cria produtos, os conecta com os objetos de estratégia pertinentes.

Farei isto em meu código de teste.

class Teste...
private Produto processador = Produto.novoProcessadorDeTexto
("Thinking Word") ;
private Produto planilha = Produto.novaPlanilha ("Thinking Calc");

private Produto db = Produto.novoBancoDeDados ("Thinking DB") ;

Quando tudo estiver pronto, não é necessário conhecer as subclasses de estraté-

gias para calcular os lançamentos.

CapituLo 9 e PADRÕES DE LÓGICA DE Dominio

133
5

class Contrato

public void calcularLanganentos () {
produto. calcularLanganentoDeReceitas (this);
}

E

class Produto...
void calcularLançamentoDeReceitas

(Contrato contrato)

{

estratégiaLançamento. calcularLançamentoDeReceitas (contrato) ;

A prática OO de mandar mensagens de um objeto para outro não só move o
comportamento para o objeto mais qualificado a lidar com ele, mas também resolve

muito do comportamento condicional. Você perceberá que não há instruções condi-

cionais neste cálculo. Você estabelece o caminho da decisão quando cria os produtos

com a estratégia apropriada. Uma vez que tudo esteja no lugar, os algoritmos sim-

plesmente seguem o trilho. Modelos de domínio funcionam muito bem quando vo-

cê tem expressões condicionais parecidas, porque estas expressões podem ser fatoradas para a própria estrutura do objeto. Isso tira a complexidade dos algoritmos e a
coloca nos relacionamentos entre objetos. Quanto mais parecida for a lógica, mais vezes você encontrará a mesma rede de relacionamentos usada em diferentes partes do
sistema. Qualquer algoritmo que seja dependente do tipo de cálculo de lançamento

pode seguir esta rede específica de objetos.

Perceba neste exemplo que não mostrei nada sobre como os objetos são trazidos

do banco de dados ou nele armazenados. Há algumas razões para isso. Em primeiro
lugar, mapear um Modelo de Domínio em um banco de dados é sempre um pouco difícil, de modo que me acovardei e não forneci um exemplo. Em segundo lugar, sobre

muitos aspectos, a questão central a respeito de um Modelo de Domínio é esconder o

banco de dados, tanto das camadas superiores quanto das pessoas trabalhando no
próprio Modelo de Domínio. Assim, escondê-lo aqui reflete a realidade do que é pro-

gramar neste ambiente.

134

e

Parte Il e Os PaprOes

Módulo Tabela (Table Module)

Uma única instância que trata a lógica de negócio para todas as linhas de uma
tabela ou visão de um banco de dados.

Contrato

CalcularLançamentos (ID)

Produto
LerTipoDoProduto (ID)

Lançamento de Receitas

/

Inserir (ID, quantia, data)

ReceitaLancada (IDdoContrato, data)

Um dos conceitos-chave da orientação a objetos é empacotar os dados com o comportamento que os usam. A abordagem orientada a objetos tradicional é baseada em
objetos identificados, junto com as diretrizes de Modelo de Domínio (126). Assim, se tivermos uma classe Empregado, qualquer instância dela corresponde a um empregado em particular. Este esquema funciona bem porque, uma vez que tenhamos uma

referência para um empregado, podemos executar operações neste empregado, seguir relacionamentos e obter informações sobre ele.
Um dos problemas com o Modelo de Domínio (126) é a interface com bancos de

dados relacionais. Sob vários aspectos, esta abordagem trata o banco de dados relacional como uma tia louca trancada no sótão sobre a qual ninguém quer falar. Em
conseqiiéncia, muitas vezes você precisa de uma considerável ginástica de progra-

mação para extrair e gravar dados no banco de dados, realizando a transformação

entre as duas diferentes representações dos dados.
Um Módulo Tabela organiza a lógica do domínio com uma classe por tabela do

banco de dados, e uma única instância de uma classe contém os vários procedimentos
que atuarão sobre os dados. A distinção primária do Modelo de Domínio (126) é que, se
você tiver muitos pedidos, um Modelo de Domínio (126) terá um objeto Pedido por pedido, enquanto que um Módulo Tabela terá um objeto para tratar todos os pedidos.

Como Funciona

A força do Módulo Tabela é que ele permite empacotar juntos os dados e o comporta-

mento e, ao mesmo tempo, tira proveito do poder de um banco de dados relacional.

Na superfície, o Módulo Tabela se parece muito com um objeto regular. A principal di-

CapituLo 9 e PADRÕES DE LÓGICA DE Dominio

135

ferença é que ele não tem o conceito de identidade dos objetos com os quais está tra-

balhando. Assim, se você quiser obter o endereço de um empregado, deve usar um
método como umMóduloEmpregado. lerEndereço (long

IDdoEmpregado). Cada vez que você

quiser fazer algo com um empregado específico, tem que passar algum tipo de iden-

tificação deste empregado. Frequentemente, esta será a chave primária usada no
banco de dados.
Normalmente, você usa o Módulo Tabela com uma estrutura de suporte orienta-

da a tabelas. Os dados tabulares são geralmente o resultado de uma chamada SQL e

são armazenados em um Conjunto de Registros (473) que imita uma tabela SQL. O Módulo Tabela dá a você uma interface explicitamente baseada em métodos que age sobre esses dados. Juntar o comportamento à tabela dá a você muitos dos benefícios do

encapsulamento, na medida em que o comportamento está próximo aos dados sobre

os quais ele atua.
Com freqiiéncia, você precisará do comportamento de múltiplos Módulos Tabela de modo a executar algum trabalho útil. Muitas vezes você encontrará múltiplos
Módulos Tabela operando sobre o mesmo Conjunto de Registros (473) (Figura 9.4).

O caso mais simples é a utilização de um Módulo Tabela para cada tabela no ban-

co de dados. No entanto, se você tiver consultas e visões interessantes no banco de
dados, pode ter também Módulos Tabelas para elas.
O Módulo Tabela pode ser uma instância ou uma coleção de métodos estáticos. A

vantagem de uma instância é que ela permite a você inicializar o Módulo Tabela com

um conjunto de dados existente, talvez o resultado de uma consulta ao banco. Você

pode então usar a instância para manipular as linhas no conjunto de registros. As instâncias também tornam possível a utilização de herança, de modo que podemos es-

crever um módulo de um contrato urgente que contenha comportamento adicional
ao do contrato normal.

O Módulo Tabela pode incluir consultas ao banco como métodos fábrica. A alter-

nativa é um Gateway para Tabelas de Dados (151), mas uma desvantagem disso é ter

new (oConjuntoDeDados)
A

umContrato

T
calcularLançamentos (IDdoContrato) |

new

(oConjuntoDeDados)

>>
| | tmProduto
new
(oConjuntoDeDados)
T
lerTipoDoProduto (IDdoProduto)!
* inserir

Figura 9.4

umLançamento
DeReceita

U

Diversos Módulos Tabela podem colaborar com um único Conjunto de

Registros (473).

136

Parte Il e Os PaprOes

uma classe e mecanismo Gateway para Tabelas de Dados (151) extra no projeto. A van-

2
2

tagem é que você pode usar um único Módulo Tabela nos dados de diferentes fontes
de dados, uma vez que você estará usando Gateways para Tabelas de Dados (151) diferentes para cada fonte de dados.

Quando você usa um Gateway para Tabelas de Dados (151), a aplicação primeiro

usa o Gateway para Tabelas de Dados (151) para inserir dados em um Conjunto de Re-

gistros (473). Você então cria um Módulo Tabela com o Conjunto de Registros (473) co-

mo um argumento. Se você precisar do comportamento de múltiplos Módulos Tabela, pode criá-los com o mesmo Conjunto de Registros (473). O Módulo Tabela pode en-

tão executar a lógica do negócio no Conjunto de Registros (473) e passar esse Conjunto de Registros (473) modificado para a apresentação para que seja exibido e editado

usando componentes que trabalham com tabelas. Esses componentes não sabem di-

zer se os conjuntos de registros vieram diretamente do banco de dados relacional ou
se um Módulo Tabela manipulou os dados no caminho. Após as modificações na interface com o usuário, o conjunto de dados volta para o Módulo Tabela para validação antes de ser gravado no banco de dados. Um dos benefícios deste estilo é que

você pode testar o Módulo Tabela criando um Conjunto de Registros (473) em memória, sem ir ao banco de dados.

A palavra “tabela” no nome do padrão sugere que você tenha um Módulo Tabe-

la por tabela no banco de dados. Embora isso seja verdadeiro em uma primeira aproximação, não é completamente verdadeiro. Também é útil ter um Módulo Tabela para
visões ou outras pesquisas comumente usadas. Na verdade, a estrutura do Módulo
Tabela não depende da estrutura real das tabelas no banco de dados, e sim das tabelas virtuais percebidas pela aplicação, incluindo visões e pesquisas.

UM gateway
ant
Barico
de Dados
De Dadosde
apresentas? | | Tabela
amo ima apresentação
7
7
7
solicitar dados
encontrar
i
'
selecionar
1
um conjunto

de registros

-

aplicar lógica do domínio (um conjunto de registros)

um conjunto

7

de registros

atualizar (conjunto de
registros modificado)

ui Tabela
ai im Módulo
T
1
))

1
|

7

1

'

1

'

validar (conjunto de registros modificado)
atualizar
1
(conjunto de registros modificado)

atualizar!

TOO
Figura 9.5

Interações típicas das camadas em torno de um Módulo Tabela.

)

'
'1

[|

CapituLo 9 e PADRÕES DE LÓGICA DE Domínio

Quando

137

Usá-lo
O Módulo Tabela é fortemente baseado em dados orientados a tabelas, de modo que,

obviamente, o seu uso faz sentido quando você está acessando dados tabulares usando um Conjunto de Registros (473). Além disso, o Módulo Tabela coloca essa estrutura
de dados bem no centro do código, então é desejável ainda que o modo de acesso à

estrutura de dados seja razoavelmente direta.

No entanto, o Módulo Tabela não dá a você todo o poder dos objetos na organi-

zação de lógica complexa. Você não pode ter relacionamentos diretos de instância
com instância, e o polimorfismo não funciona muito bem. Assim, para lidar com ló-

gica de domínio complexa, um Modelo de Domínio (126) é uma escolha melhor. Essencialmente, a escolha entre um e outro padrão é um compromisso entre a habilidade

do Modelo de Domínio (126) em lidar com lógica complexa e a facilidade de integração

do Módulo Tabela com suas estruturas de suporte orientadas a tabelas.

Se os objetos em um Modelo de Domínio (126) e as tabelas no banco de dados fo-

rem relativamente semelhantes, pode ser melhor usar um Modelo de Domínio (126)

que use Registros Ativos (165). O Módulo Tabela funciona melhor do que a combinação
Modelo de Domínio (126) e Registros Ativos (165) quando outras partes da aplicação fo-

rem baseadas em uma estrutura comum, baseada em tabelas. É por esse motivo que
você não vê muito o Módulo Tabela em um ambiente Java, embora isso possa mudar

na medida em que conjuntos de linhas se tornem mais largamente usados.
A situação mais conhecida em que me deparei com este padrão foi nos projetos

utilizando o Microsoft COM. Em COM (e .NET) o Conjunto de Registros (473) é o re-

positório primário de dados em uma aplicação. Conjuntos de registros podem ser
passados para a interface com o usuário, onde componentes data aware mostram as
informações. As bibliotecas ADO da Microsoft fornecem um bom mecanismo para

acessar os dados relacionais como conjuntos de registros. Nesta situação, o Módulo

Tabela permite a você introduzir a lógica do negócio na aplicação de uma maneira

bastante organizada, sem perder o modo como os diversos elementos atuam sobre os
dados tabulares.

Exemplo: Lançamento de Receitas com um Módulo Tabela (C#)

É hora de revisitar o exemplo do lançamento de receitas que usei nos outros padrões

para modelagem de domínio, desta vez com um Módulo Tabela. Apenas para recapi-

tular, nossa missão é lançar receitas referentes a pedidos em que as regras de lança-

mento variam de acordo com o tipo do produto. Neste exemplo, temos regras diferentes para processadores de texto, planilhas e banco de dados.
O Módulo Tabela é baseado em um esquema de dados de algum tipo, normal-

mente um modelo de dados relacional (embora no futuro possamos ver um modelo XML usado de forma semelhante). Neste caso, usarei o esquema relacional da Figura 9.6.

As classes que manipulam estes dados têm quase que a mesma forma; há uma

classe Módulo Tabela para cada tabela. Na arquitetura .NET, um objeto conjunto de
dados (data set) fornece uma representação em memória de uma estrutura do banco de dados. Assim, faz sentido criar classes que operem sobre estes data sets. Cada
classe Módulo Tabela tem um atributo do tipo tabela de dados (data table), que é a
classe .NET correspondente a uma tabela dentro do data set. A capacidade de ler
uma tabela é comum a todos os Módulos Tabela e por isso pode aparecer em um Supertipo de Camada (444).

138

e
E

Parte Il e Os PaprOes

«tabela»

Produtos

=
ID: Number

«tabela»

Contratos

1

Nome:
String
5
ga

E

* | Quantia: Number

Tipo: String

Figura 9.6

1

ID: Number
dataAssinatura: Date

<

«tabela»
Lançamento
de Receitas
a | quantia:
Dee Number
data: Date

Esquema do banco de dados para o lançamento de receitas.

class MóduloTabela...

protected DataTable tabela;

protected MéduloTabela

}

(DataSet ds, String nomeDaTabela)

tabela = ds.Tables(nomeDaTabelal ;

{

O construtor da subclasse chama o construtor da superclasse com o nome cor-

reto da tabela.

class Contrato. .
public Contrato (DataSet ds): base (ds, "Contratos")

()

Isso permite a você criar um novo Módulo Tabela simplesmente passando o conjunto de dados (data set) para o construtor do Módulo Tabela
contrato = new Contrato (dataset);

o qual mantém o código que cria o data set separado dos Módulos Tabela, seguindo as
diretrizes do ADO.NET.

Uma característica útil é o indexador C#, que localiza uma linha específica no

data table, dada a chave primária.
class Contrato...

public DataRow this [Long chave] (

get {

String filtro = String. format (

return tabela. Select (filtro) [0] ;

}

)

A primeira parte da função calcula o lançamento da receita de um contrato,

atualizando a tabela de lançamento de receitas de acordo. A quantia lançada depen-

de do tipo de produto que temos. Já que este comportamento usa principalmente da-

dos da tabela Contrato, decidi adicionar o método na classe Contrato.
class Contrato...
public void CalcularLançamentos (long IDdoContrato)
DataRow linhaDoContrato = this [IDdoContrato] ;

{

Decimal quantia = (Decimal) LinhaDoContrato ["quantia"] ;

