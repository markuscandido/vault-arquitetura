# 10_Cap_07_Estrategias_de_Distribuicao

120

Parte Il e Os PaprOes

8.

Roteiro de Transação (Transaction Script)

2
2

Organiza a lógica de negócio em procedimentos onde cada procedimento lida
com uma única solicitação da apresenta:

Serviço de Lançamento
receitaLançada (númeroDoContrato: long, aPartirDe: Date) : Dinheiro

calcularLançamentoDeReceitas (númeroDoContrato: long) : void

A maioria das aplicações podem ser percebidas como uma série de transações. Uma

transação pode enxergar alguma informação organizada de alguma forma particu-

lar, outra transação pode fazer alterações nesta informação. Cada interação entre um

sistema cliente e um sistema servidor contém uma certa quantidade de lógica. Em alguns casos isso pode ser tão simples quanto mostrar informações armazenadas no
banco de dados. Em outros, pode envolver muitos passos de validações e cálculos.

Um Roteiro de Transação organiza toda esta lógica primariamente como um único

procedimento, fazendo chamadas diretas ao banco de dados ou usando um fino en-

voltório para acesso ao banco de dados. Cada transação terá seu próprio Roteiro de

Transação, embora subtarefas comuns possam ser separadas em procedimentos.

Como Funciona
Com o Roteiro de Transação, a lógica do domínio é organizada primariamente pelas

transações que você executa usando o sistema. Se a sua necessidade for reservar o
quarto de um hotel, a lógica para verificar a disponibilidade de quartos, calcular taxas e atualizar o banco de dados é encontrada dentro do procedimento Reservar
Quarto de Hotel.

Para casos simples não há muito a dizer sobre como organizar isso. É claro que,

assim como com qualquer outro programa, você deve estruturar o código em módu-

los de uma maneira que faça sentido. A menos que a transação seja especialmente

complicada, isso não será um grande desafio. Um dos benefícios desta abordagem é
que você não precisa se preocupar com o que outras transações estão fazendo. Sua tarefa é ler a entrada de dados, inquirir o banco de dados, trabalhar sobre os dados e

gravar seus resultados no banco de dados.

Onde você deve colocar o Roteiro de Transação dependerá de como você organiza

suas camadas. Pode ser em uma página servidora, um script CGI ou um objeto de sessão distribuído. Minha preferência é separar os Roteiros de Transação o máximo que você puder. Coloque-os no mínimo em sub-rotinas distintas; melhor ainda, coloque-os
em classes separadas daquelas que lidam com a apresentação e a fonte de dados. Além
disso, não permita chamadas dos Roteiros de Transação para qualquer lógica de apresentação. Isso facilitará a modificação do código e os testes dos Roteiros de Transação.

Você pode organizar seus Roteiros de Transação em classes de duas maneiras distintas. A mais comum é ter diversos Roteiros de Transação em uma única classe, on-

de cada classe define um assunto comum para Roteiros de Transação relacionados. Isso é simples e direto e é a melhor aposta para a maior parte dos casos. A outra maneira é ter cada Roteiro de Transação em sua própria classe (Figura 9.1), usando o padrão

Carítuto 9 e PADRÕES DE LÓGICA DE Domínio

121

Comando [Gang of Four]. Neste caso você define um supertipo para seus comandos

que define algum método para execução no qual a lógica dos Roteiros de Transação se
encaixa. A vantagem desta abordagem é que ela permite manipular instâncias de ro-

teiros como objetos em tempo de execução, embora eu raramente tenha visto neces-

sidade de fazer isso com os tipos de sistemas que usam Roteiros de Transação para organizar a lógica de domínio. É claro que, em muitas linguagens, você pode ignorar

as classes completamente e usar apenas funções globais. Contudo, você descobrirá

muitas vezes que instanciar um novo objeto ajuda em questões relacionadas a

threads, pois torna mais fácil isolar os dados.

Eu uso o termo Roteiro de Transação, porque na maior parte do tempo você terá
um Roteiro de Transação para cada transação no banco de dados. Isso não é uma regra
para 100% dos casos, mas é verdade em uma primeira aproximação.

Roteiro de Transação
run)

RT Receita Lançada
new (númeroDoContrato:long, aPartirDe: Date)|

run ()
Figura 9.1

Quando

A
RT Calcular Lançamento de Receitas
| | new (numeroDoContrato: long)

run ()

Usando comandos para Roteiro de Transação.

Usá-lo
A beleza do Roteiro de Transação é sua simplicidade. Organizar a lógica desta manei-

ra é natural para aplicações com apenas uma pequena quantidade de lógica, e envol-

ve muito pouco overhead, seja no desempenho, seja na compreensão.

No entanto, à medida que a lógica de negócio fica mais complicada, torna-se cada vez mais difícil mantê-la em um estado que possa ser caracterizado como um bom
projeto. Um problema específico a observar é a duplicação de código entre transações. Já que o ponto central é lidar com uma transação por roteiro, qualquer código

em comum tende a ser duplicado.

Uma fatoração cuidadosa pode minorar muitos destes problemas, no entanto
domínios de negócio mais complexos precisam construir um Modelo de Domínio

(126). Um Modelo de Domínio (126) lhe dará muito mais opções na estruturação do có-

digo, aumentando a legibilidade e diminuindo a duplicação.

É difícil quantificar o ponto de corte entre as duas estratégias, especialmente

quando você está mais familiarizado com um padrão do que com outro. Você pode

refatorar o projeto de um Roteiro de Transação para o de um Modelo de Domínio (126),

mas é uma alteração mais difícil do que de outra forma precisaria ser. Uma escolha
inicial acertada é, portanto, a melhor maneira de seguir adiante.

E)

Parte ll e Os PaprOes

ansação

122

8
ê

Entretanto, ainda que você se torne um fanático por objetos, não descarte os Roteiros de Transação. Há muitos problemas simples por aí, e uma solução simples lhe le-

vará adiante mais rápido.

O Problema do Lançamento de Receitas
Para este padrão, e outros que falam de lógica do domínio, usarei o mesmo proble-

ma como ilustração. Para não ter de descrever o problema diversas vezes, eu o farei
apenas uma vez aqui.
O lançamento de receitas é um problema comum em sistemas de negócios. O
problema central é quando você pode realmente contabilizar em seus livros o dinheiro que você recebe. Se eu lhe vender uma xícara de café, esta é uma questão

simples: eu lhe dou o café, pego seu dinheiro e contabilizo o dinheiro nos livros no

mesmo nanossegundo. Entretanto, para muitas coisas isso fica complicado. Diga-

mos que você me dê um adiantamento para que eu fique disponível durante esse
ano. Ainda que você me pague uma remuneração ridícula hoje, posso não poder

lançá-la imediatamente em meus livros, porque o serviço será executado no decor-

rer de um ano. Uma solução poderia ser contabilizar apenas uma duodécima par-

te dessa remuneração para cada mês do ano, desde que você pudesse rescindir o

contrato após um mês, quando percebesse que escrever atrofiou minhas habilidades de programação.

As regras para lançamento de receitas são muitas, variadas e voláteis. Algu-

mas são estabelecidas por regulamentos, algumas por padrões profissionais e ou-

tras por políticas da companhia. Rastrear receitas acaba sendo um problema bas-

tante complexo.

Não quero me aprofundar nesta complexidade neste momento, assim, em vez
disso, vamos imaginar uma companhia que venda três tipos de produtos: processa-

dores de texto, bancos de dados e planilhas. De acordo com as regras, quando você

assina um contrato de um processador de texto, pode lançar as receitas nos livros

imediatamente. Se for uma planilha, pode colocar um terço hoje, um terço em sessen-

ta dias e um terço em noventa dias. Se for um banco de dados, pode colocar um tergo hoje, um terço em trinta dias e um terço em sessenta dias. Não há base para estas

regras além da minha própria imaginação febril. Disseram-me que as regras reais são
tão racionais quanto essas.

Exemplo:

Lançamento

de Receitas (Java)

Este exemplo usa dois roteiros de transação: um para calcular o lançamento de receitas para um contrato e outro para dizer quanto da receita de um contrato foi lançado

até uma determinada data. A estrutura do banco de dados tem três tabelas: uma para os produtos, uma para os contratos e uma para os lançamentos de receitas.

CREATE TABLE produtos (ID int primary key, nome varchar, tipo varchar)
CREATE TABLE contratos (ID int primary key, produto int, receita decimal, dataAssinatura date)
CREATE TABLE lançamentosDeReceitas (contrato int, quantia decimal, lancadaim date,
PRIMARY KEY (contrato, langadaBm))
O primeiro roteiro calcula o total de lançamentos até uma determinada data.
Posso fazer isso em duas etapas: na primeira seleciono as linhas apropriadas na tabela de lançamentos de receitas; na segunda, somo as quantias.

CapituLo 9 e PADRÕES DE LÓGICA DE Domínio

Produto
a

tipo

Lançamento de
Receita

Contrato)
1

— data assinatura

* | receita

123

1

* | - quantia

= data

se um contrato tiver quaisquer

lançamentos de receitas, a receita do
contrato deve ser igual à soma dos
valores de seus lançamentos de receitas
Figura 9.2

Um modelo conceitual para o lançamento simplificado de receitas. Cada

contrato tem múltiplos lançamentos de receitas que indicam quando as várias parcelas da

receita devem ser lançadas.

Muitos projetos de Roteiros de Transação têm roteiros que operam diretamente no
banco de dados, inserindo código SQL no procedimento. Aqui estou usando um sim-

ples Gateway de Tabela de Dados (151) para encapsular as consultas SQL. Já que este
exemplo é tão simples, usarei um único gateway para todas as tabelas. Posso definir
um método de busca apropriado no gateway.

class Gateway...
public ResultSet buscabançamentoPor (long IDdoContrato, MfDate aPartirDe) throws SQLException {
PreparedStatement dec = db.prepareStatement (declaraçãoBuscaLançamentos) ;
dec. setLong(1, IDdoContrato) ;
dec.setDate (2, aPartirDe.toSqlDate( |);
ResultSet resultado = dec.executeQuery(

}

return resultado;

|);

private static final String declaraçãoBuscaLançamentos =
"SELECT quantia * +
* FROM lancamentosDeReceitas " +
* NHERE contrato = ? AND lançadoBm <= 2";
private Connection db;

Eu então uso o roteiro para somar baseado no conjunto de resultados devolvi-

do pelo gateway.

class ServiçoDeLançamento...
public Dinheiro receitaLançada
(long númeroDoContrato, MéDate aPartirDe) {

Dinheiro resultado = Dinheiro.dollars
(0) ;
try {
ResultSet rs = db.buscaLançanentoPor (númeroDoContrato, aPartirDe) ;
while (rs.next()) (
}

resultado = resultado.add (Dinheiro. dollars (rs.getBigDecinal
("quantia"))) ;

return resultado;

} catch (SQLException e) {throw new AplicationException
|e) ;

}

124

Parte Il e Os PaprOes

8

2
2

Quando o cálculo for simples como este, você pode substituir o roteiro em me-

mória por uma chamada a uma declaração SQL que use uma função de agregação
para somar as quantias.

Para calcular as lançamentos de receitas em um contrato existente, uso uma se-

paração similar. O roteiro no serviço se encarrega da lógica de negócio.

class ServiçoDeLançamento...
public void calcularLançamentoDeReceitas
(long númeroDoContrato)

try {

{

ResultSet contratos = db.encontrarContrato (númeroDoContrato) ;

contratos. next() ;

Dinheiro receitaTotal =

Dinheiro.dollars (contratos.getBigDecimal ("receita")) ;

MéDate dataLançanento = new MfDate (contratos.getDate ("dataAssinatura")) ;
String tipo = contratos.getString("tipo") ;

if(tipo.equals("P")) // planilha {

Dinheiro alocação[] = receitaTotal.alocar
(3);

db. inserirLançamento
(númeroDoContrato, alocação [0] dataLançamento) ;

db.inserirLançamento
(númeroDoContrato, alocação [1] , dataLançamento .adicionaDias
(60) ) ;

db. inserirLançamento

InúmeroDoCont
rato, alocação [2] , dataLançamento .adicionaDias
(90) ) ;

} else if (tipo.equals("T"))

// processador de textos {

db. inserirLançamento (númeroDoContrato,

receitaTotal, dataLançamento) ;

) else if (tipo.equals("B")) // banco de dados {

Dinheiro alocaçao[] = receitaTotal.alocar
(3) ;
db. inserirLançamento

(númeroDoContrato, alocaçao [0] dataLançamento) ;

db. inserirLançamento

(númeroDoContrato, alocação [1] dataLançamento.adicionaDias (30) ) ;
db. inserirLançamento

)

InúmeroDoCont
rato, alocação [2] , dataLançamento .adicionaDias (60) ) ;

} catch (SQLException e) { throw new AplicationException
(e) ;

}

Perceba que estou usando Dinheiro (455) para executar a alocação. Ao dividir
uma quantia em três é fácil perder alguns centavos.
O Gateway de Tabela de Dados (151) fornece suporte ao SQL. Primeiro há um método de busca para um contrato.

class Gateway...
public ResultSet encontrarContrato
(long IDdoContrato)

throws SQLException {

PreparedStatement dec = db.prepareStatement (declaraçãoBuscaContrato) ;
dec.setLong(1, IDdoContrato) ;
ResultSet resultado = dec.executeQuery

}

return resultado;

|);

private static final String declaraçãoBuscaContrato =
"SELECT + 4
* FROM contratos c, produtos p " +
"WHERE c.ID = ? AND c.produto = p.ID";

CapituLo 9 e PADRÕES DE LÓGICA DE Domínio

125

Em segundo lugar, há um encapsulamento para a inserção.

E)

public void inserirLançamento
(Long IDdoContrato, Dinheiro quantia, MéDate aPartirDe)
throws SQLException {

PreparedStatement dec = db.prepareStatement (declaraçãoInserirbançamento) ;
dec.setLong(1,

IDdoContrato) ;

dec. setBigDecimal(2, quantia.quantia()) ;
dec.setDate(3, aPartirDe.toSqlDate()) ;

dec. executeUpdate () ;

}
private static final String declaraçãoInserirLançamento =
"INSERT

INTO

lançamentosDeReceitas

VALUES

(?,

?,

?)";

Em um sistema Java, o serviço de lançamento poderia ser uma classe regular ou

um session bean.

Quando você comparar este padrão ao exemplo no Modelo do Domínio (126), a

menos que sua mente seja tão torcida quanto a minha, provavelmente pensará que

este é muito mais simples. O pior cenário a se considerar é quando as regras ficam

mais complicadas. As regras típicas de lançamento de receitas são muito complexas,

variando não somente com o produto, mas também com a data (se o contrato foi as-

sinado antes de 15 de abril, esta regra se aplica...). É difícil manter um projeto coeren-

te com o Roteiro de Transação, quando as coisas ficam tão complicadas, e este é o mo-

tivo pelo qual fanáticos por objetos, como eu, preferem usar o Modelo de Domínio
(126) nestas circunstâncias.

oE

class Gateway...

