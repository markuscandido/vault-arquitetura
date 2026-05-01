# 18_Cap_15_Padroes_de_Distribuicao

Capítuto 15

e PADRÕES DE DistrisuicAo

389

Tal rotina irá lidar muito bem com a maioria dos casos (embora você vá ter que

adicionar código extra para lidar com primitivas).
Exemplo: Serializando Usando XML (Java)

Enquanto escrevo isto, a manipulação de XML por Java está em contínua mudança e

APIs, ainda voláteis, estão de modo geral melhorando. Quando você ler, esta seção

pode estar desatualizada ou completamente irrelevante, mas o conceito básico da
conversão para XML é quase que o mesmo.
Primeiro, leio a estrutura de dados do Objeto de Transferência de Dados, então pre-

ciso decidir como serializá-la. Em Java, você obtém serialização binária simplesmen-

te usando uma interface de marcador. Isso funciona de maneira completamente au-

tomática com um Objeto de Transferência de Dados, então, é minha primeira escolha.

Entretanto, muitas vezes, é necessário serialização baseada em texto. Para este exemplo, então, usarei XML.

Neste exemplo, estou usando JDOM, já que isso torna o trabalho com XML

muito mais fácil do que usar as interfaces padrão W3C. Escrevo métodos para ler e
gravar um elemento XML para representar essa classe em cada classe Objeto de Trans-

ferência de Dados.
class ÁlbunorD...

Element paraBlementoXML

()

{

Element raiz = new Element ("álbum") ;
raiz.setAttribute

("título",

título);

raiz.setAttribute("artista", artista);

for (int i = 0; i < faixas length; i++)

vaiz.addContent (faixas [i] .paraBlementoxMb( ));

}

return

raiz;

static AlbumOTD lerXML (Element fonte) {
AlbumOTD resultado = new AlbumOTD( );

resultado. gravarTitulo(fonte.getattributeValue ("titulo") ;
resultado.gravarArtista (fonte .getattributeValue ("artista") ;
List listaDeFaixas = new ArrayList ( |;
Iterator it = fonte.getChildren("faixa")

while (it hasNext ( ))

.iterator( );

listaDePaixa.add(PaixaOTD.lerXML((Blement) it.next( }));

}

resultado. gravarFaixas((Faixa0TD[ | | listaDeFaixas.toarray
(new FaixaoTD[0]));
return resultado;

class Faixa0TD...
Element paraBlementoxML ( ) {

Element resultado = new Element ("faixa");
resultado. setattribute ("titulo", titulo);
for (int i = 0; i < intérpretes.length; i++)

(

Element elenentoIntérprete = new Element ("intérprete");

}

elementoIntérprete.setAttribute("nome", intérpretes[i]);
resultado.addContent (elementoIntérprete) ;

return resultado;

390

Parte ll e Os PADROES

static PaixaOTD lerXml

(Element arg)

{

Faixa0TD resultado = new FaixaOTD( );
resultado.gravarTítulo (arg.getattributevalue ("título") ;
Iterator it = arg.getChildren("intérprete")

.iterator( );

List buffer = new ArrayList ( );
while (it.hasNext( )) (
Element cadaElemento = (Element) it.next ( );
buffer. add (cadaBlenento.getAttributeValue ("nome") ;
)
resultado .gravarIntérpretes(

return resultado;

(String[ ]) buffer.tohrray
(new String[0]));

É claro que estes métodos apenas criam os elementos no DOM XML. Para executar a serialização, preciso ler e escrever texto. Já que a faixa é transferida apenas no
contexto do álbum, preciso apenas gravar este código do álbum.
class Album OTD...
public void paraStringXML (Writer saída)

Element raiz = paraElementoxml ( );

{

Document doc = new Document (raiz);

XuLOutputter gravador = new ¥MLOutputter( ) ;
try {
gravador. output (doc, saída) ;
) catch (IOException e) {

)

}

e.printStackTrace( );

public static ÁlbumOTD lerStringkml

(Reader entrada)

try {
SaxBuilder construtor = new SAXBuilder( };
Document doc = construtorbuild (entrada) ;

E
(e)

Element raiz

{

= doc.getRootElement ( );

AlbumOTD resultado = lerXML (raiz) ;

return resultado;

) catch (Exception e) {

e.printStackTrace( );

thow new RuntimeException ( );

Embora este não seja um conhecimento sofisticado, ficarei feliz quando JAXB
tornar este tipo de coisa desnecessário.

CaríTULO

1

6

Padrões de Concorrência Offline

392

Parte ll e Os Parões

Bloqueio Offline Otimista (Optimistic Offline Lock)
por David Rice
Previne conflitos entre transações de negócio concorrentes
detectando um conflito e desfazendo a transação.

Sessão do Martin

Banco de Dados

Sessão do David

|

l

|

I
|
DEC
=

|

|

!
1
Tn

lerCliente 129

>>

|

|

I.

:
|

a

Transação

de Sistema

|

retornar cliente 129

|

Limite da

|

lerCliente 129

ee

|
|
a

=

=|

|
|
|

-—1
editar cliente > O

1

g
5

falha: versão

errada do cliente

(ae!

|
|

I

ite da

Transação
de Negócio

>,

Muitas vezes, uma transação de negócio é executada por meio de uma série de transações de sistema. Uma

vez fora de uma transação de sistema, não podemos contar

apenas com nosso gerenciador de banco de dados para assegurar que a transação de
negócio deixará os dados em um estado consistente. A integridade dos dados é um
risco, assim que duas sessões começam a trabalhar nos mesmos registros, e atualiza-

ções perdidas são bastante possíveis. Além disso, com uma sessão editando dados

que outra esteja lendo, uma leitura inconsistente torna-se provável.
O Bloqueio Offline Otimista resolve esse problema ratificando que as alterações

a serem gravadas por uma sessão não estejam em conflito com as alterações de ou-

Capituto 16

e PADRÕES DE CONCORRÊNCIA OFFLINE

393

tra sessão. Uma validação com sucesso antes da confirmação (commit) significa, de

certo modo, obter um bloqueio indicando que não há problema em ir em frente com

as alterações nos dados dos registros. Enquanto a validação e as atualizações ocor-

rerem dentro de uma única transação de sistema, a transação de negócio exibirá
consistência.

Enquanto que o Bloqueio Offline Pessimista (401) pressupõe que a chance de con-

flito na sessão é alta e, portanto, limita a concorrência do sistema, o Bloqueio Offline

Otimista supõe que a chance de conflito é baixa. A expectativa de que um conflito na

sessão não seja provável permite que diversos usuários trabalhem com os mesmos
dados ao mesmo tempo.

Como Funciona
Um Bloqueio Offline Otimista é obtido garantindo que, no tempo decorrido desde,
que uma sessão carregou um registro, outra sessão não o alterou. Ele pode ser obtido a qualquer momento, mas é válido apenas durante a transação de sistema na
qual foi obtido. Assim, para que uma transação de negócio não corrompa dados gravados ela deve obter um Bloqueio Offline Otimista para cada membro de seu conjun-

to de alterações, durante a transação de sistema na qual ela aplica as alterações no

banco de dados.
A implementação mais comum é associar um número de versão com cada registro no seu sistema. Quando um registro é carregado, esse número é mantido pela sessão junto com todo o estado dela. Obter o Bloqueio Offline Otimista é uma questão de
comparar a versão armazenada nos dados da sua sessão com a versão atual nos dados gravados. Assim que a verificação tenha sucesso, todas as alterações, incluindo

o incremento da versão, podem ser gravadas. O incremento da versão é o que evita

dados gravados inconsistentemente, já que a sessão com uma versão antiga não pode obter o bloqueio.
Com dados em um SGBDR a verificação é uma questão de adicionar o número

da versão ao critério de qualquer declaração SQL usada para atualizar ou apagar um

registro. Uma única declaração SQL pode tanto obter o bloqueio quanto atualizar os

dados gravados. O passo final é a transação de negócio inspecionar o contador de linha retornado pela execução do SQL. Um contador de linha igual a um indica suces-

so, zero indica que o registro foi alterado ou excluído. Com um contador de linha
que alterações sejam feitas nos dados gravados. Neste ponto, a transação de negócio

deve abortar ou tentar resolver o conflito e tentar de novo.
Além de um número de versão para cada registro, armazenar informações sobre quem modificou esse registro da última vez e quando isso foi feito pode ser muito útil durante o gerenciamento de conflitos de concorrência. Ao informar um usuário a respeito de uma falha de atualização devido a violação de concorrência, uma

aplicação correta dirá quando o registro foi alterado e por quem. É uma idéia ruim

usar o timestamp da modificação em vez de um contador de versão para suas verifi-

cações otimistas, porque os clocks do sistema são simplesmente não-confiáveis, espe-

cialmente se você estiver coordenando por meio de diversos servidores.
Em uma implementação alternativa, a cláusula WHERE

na atualização inclui

cada campo da linha. A vantagem aqui é que você pode usar essa cláusula sem usar
algum tipo de campo versão, o que pode ser útil se você não puder adicionar um

campo versão alterando as tabelas do banco de dados. O problema é que isso com-

2
S
2

nO

iguala zero, a transação de negócio deve desfazer a transação de sistema para evitar

394

Parte Il e Os PaprOes

Sessão

Banco de Dados

=a
| editar
| Cliente

<—

|
|
|
|
|
|

AE

L

Cliente

ep
=o

1
1
1
1
i
>

I
retorna
\
contador
ke delinha —___4

| verificar
Se : o contador
f

1 de linha
é igual a 1
|

SS
1
1

Figura 16.1

ma

Limite da
Transação
de Sistema

UPDATE Cliente

5

| | |ser..., versão = (cópia que a sessão possui da versão +1)
|" | [WHERE id = ? AND versão = cópia que a sessão possui da versão
I |
1

a
1
|

Verificação otimista de UPDATE.

plica a declaração UPDATE com uma cláusula WHERE potencialmente grande, o
que pode também ter um impacto no desempenho dependendo do quão hábil o banco de dados é em relação ao uso do índice da chave primária.
Muitas vezes, a implementação do Bloqueio Offline Otimista é feita por meio da

inclusão da versão nas declarações UPDATE e DELETE, porém isso não resolve o

problema de uma leitura inconsistente. Pense em um sistema de faturas que crie uma

cobrança e calcule a taxa de venda apropriada. Uma sessão cria a cobrança e então

procura pelo endereço do cliente para calcular a taxa, mas durante a sessão de gera-

ção da cobrança uma sessão separada de manutenção de clientes edita o endereço do
cliente. Como a taxa depende da localização, o valor calculado pela sessão de geração da cobrança poderia ser inválido, masjá que a sessão de geração da cobrança não
fez nenhuma alteração no endereço, o conflito não será detectado.

Não há motivo para que o Bloqueio Offline Otimista não possa ser usado para
detectar uma leitura inconsistente. No exemplo acima, a sessão de geração da cobrança precisa reconhecer que sua correção depende do valor do endereço do clien-

te. Ela deve, portanto, executar também uma verificação de versão no endereço, tal-

vez adicionando o endereço ao conjunto de alterações ou mantendo uma lista sepa-

rada de itens a terem sua versão checada. Esta última opção requer um pouco mais

de trabalho para ser configurada, mas resulta em código que declara suas intenções

mais claramente. Se você estiver verificando uma consistência de leitura simples-

mente relendo a versão em vez de uma alteração artificial, esteja ciente do nível de

isolamento da sua transação de sistema. A releitura da versão só funcionará com lei-

turas repetíveis ou isolamento maior. Qualquer coisa mais fraca do que isso requer
um incremento da versão.

Uma verificação de versão poderia ser demais para determinados problemas de
leitura inconsistente. Muitas vezes, uma transação depende apenas da presença de

um registro ou talvez do valor de apenas um de seus campos. Neste caso, você pode-

Capituto 16

e PADRÕES DE CONCORRÊNCIA OFFLINE

395

ria melhorar a esperteza de seu sistema verificando condições em vez de versão, já

que menos atualizações concorrentes resultarão na falha das transações de negócio
que estão competindo. Quanto mais você entender os problemas de concorrência,

melhor poderá gerenciá-los no seu código.
O Bloqueio de Granularidade Alta (412) pode ajudar com algumas leituras incon-

sistentes tratando um grupo de objetos como um único item bloqueável. Outra opção é simplesmente executar todos os passos das transações de negócio problemáti-

cas dentro de uma única transação longa. A facilidade de implementação poderia
provar que o benefício de usar algumas transações longas em algumas ocasiões com-

pensa o custo.

A detecção de uma leitura inconsistente fica um pouco difícil quando sua tran-

sação é dependente dos resultados de uma pesquisa dinâmica em vez da leitura de

registros específicos. É possível que você tenha que gravar os resultados iniciais e
compará-los aos resultados da mesma pesquisa na hora da confirmação como um

meio de obter um Bloqueio Offline Otimista.
Assim como com todos os esquemas de bloqueio, o Bloqueio Offline Otimista por
si só não fornece soluções adequadas para alguns dos problemas mais complicados
de concorrência e alguns problemas temporais em uma aplicação de negócio.
Nunca
é demais enfatizar que em uma aplicação de negócio o gerenciamento de concorrên-

cia é uma questão tanto de domínio quanto técnica. O cenário do endereço de clien-

te acima é realmente um conflito? Poderia ser admissível que eu tivesse calculado a

taxa com uma versão mais antiga do cliente, mas qual versão eu deveria realmente

estar usando? Esta é uma questão de negócio. Ou então considere uma coleção. Se

duas sessões adicionassem itens simultaneamente em uma coleção? O esquema de
Bloqueio Offline Otimista típico não evitaria isso embora pudesse muito bem ser uma

violação de regras de negócio.

Há um sistema usando Bloqueio Offline Otimista com o qual todos nós deveria-

mos estar familiarizados: gerenciamento de código fonte (GCF). Quando um sistema

GCF detecta um conflito entre programadores, normalmente pode descobrir a con-

solidação correta e tentar gravar novamente. Uma boa estratégia de consolidação tornao Bloqueio Offline Otimista bastante poderoso não apenas porque a concorrência do
sistema é bastante alta, mas também porque os usuários raramente têm que refazer

algum trabalho. É claro que a grande diferença entre um sistema GCF e uma aplica-

poderiam ser de tal complexidade que não valeria o custo da codificação. Outros poderiam ser de tal valor para o negócio que a consolidação deveria ser codificada de
qualquer jeito. Embora sendo feita com pouca freqiiéncia, a consolidação de objetos
de negócio é possível. Na verdade, consolidar dados de negócio é em si um padrão.

Pararei por aqui em vez de esgotar o assunto, mas compreenda o poder que a conso-

lidação acrescenta ao Bloqueio Offline Otimista.
O Bloqueio Offline Otimista só nos informa se uma transação de negócio será real-

mente gravada durante a última transação de sistema. Contudo, ocasionalmente é

útil conhecer com maior antecedência se ocorreu um conflito. Para isso, você pode

fornecer um método verificarAtual que verifica se mais alguém atualizou os dados.

Isso não garante que você não vá ter um conflito, mas pode valer a pena parar um

processo complicado se você puder perceber de antemão que ele não será gravado.

Use este verificarAtual sempre que descobrir cedo que uma falha possa ser útil, mas

lembre-se de que isso nunca garante que não haverá falha na hora da gravação.

2
S
2

E UNO

ção corporativa de negócio é que o GCF deve implementar apenas um tipo de consolidação, enquanto que o sistema de negócio poderia implementar centenas. Alguns

396

Parrell

Quando

e Os PADRÕES

Usá-lo

O gerenciamento otimista de concorrência é apropriado quando a chance de conflito
entre duas transações de negócio quaisquer for baixa. Quando os conflitos forem
prováveis, não é razoável anunciá-los apenas quando o usuário tiver concluído seu
trabalho e estiver pronto para a confirmação . No final, ele irá pressupor a falha das

transações de negócio e parará de usar o sistema. O Bloqueio Offline Pessimista (401) é

mais apropriado quando a chance de conflito for alta ou o custo de um conflito for

inaceitável.
Como um bloqueio otimista é muito mais fácil de implementar e não está propenso aos mesmos defeitos e erros em tempo de execução do Bloqueio Offline Pessimista (401), considere usá-lo como abordagem padrão de gerenciamento de conflito

de transações de negócio em qualquer sistema que você construir. A versão pessimis-

ta funciona bem como um complemento ao seu correlato otimista, então em vez de

perguntar quando usar uma abordagem otimista para evitar conflito, pergunte quan-

do a abordagem otimista sozinha não é boa o suficiente. A abordagem correta para a
gerência de concorrência maximizará o acesso concorrente aos dados ao mesmo tempo em que minimiza os conflitos.

Exemplo: Camada de Domínio com Mapeadores de Dados (170) (Java)

O exemplo mais curto de Bloqueio Offline Otimista envolveria apenas uma tabela de
banco de dados com uma coluna para a versão e declarações UPDATE e DELETE

que usam essa versão como parte de seu critério de atualização. É claro que você cria-

rá aplicações mais sofisticadas, então apresento a implementação usando um Mode-

lo de Domínio (126) e Mapeadores de Dados (170). Isso revelará mais questões que sur-

gem comumente durante a implementação de Bloqueio Offline Otimista.
Uma das primeiras coisas a fazer é assegurar-se de que sua Camada Supertipo

(444) do domínio é capaz de armazenar qualquer informação necessária para imple-

mentar o Bloqueio Offline Otimista — a saber, dados da modificação e da versão.

class ObjetoDoDonínio...
private Timestamp modificado;
private String modificadoPor;

private int versão;

Nossos dados são armazenados em um banco de dados relacional, então, cada

tabela deve também armazenar dados sobre versão e modificação. Aqui está o esque-

ma para uma tabela cliente assim como o SQL CRUD (criação, leitura, atualização e

exclusão) padrão necessário para suportar o Bloqueio Offline Otimista.
tabela cliente..

create table cliente (id bigint primary key, none varchar, criadoPor varchar,
criado datetime, modificadoPor varchar, modificado datetime, versão int)
CRUD SQL cliente...
INSERT INTO cliente VALUES (?, ?, ?,

SELECT + FROM cliente WHERE id
UPDATE cliente SBT none = ?, modificadoPor
+ modificado = ?, versão
WHERE id = ? AND versio
DELETE FROM cliente WHERE id = ? AND versão = ?

Capítuto 16

e PADRÕES DE CONCORRENCIA OFFLINE

397

Assim que você tiver mais do que algumas tabelas e objetos do domínio, irá
querer introduzir uma Camada Supertipo (444) para seus Mapeadores de Dados (170)
que lide com os segmentos tediosos e repetitivos de mapeamento O/R. Isso não apenas economiza muito trabalho durante a escrita de Mapeadores de Dados (170) como

também permite o uso de um Bloqueio Implícito (422) para evitar que um desenvolvedor estrague uma estratégia de bloqueio esquecendo de codificar uma parte dos me-

canismos de bloqueio.

A primeira parte a ser movida para seu mapeador abstrato é a construção SQL.

Isso requer que você forneça mapeadores com um pouco de metadados sobre suas

tabelas. Uma alternativa ao mapeador para construir SQL em tempo de execução é

gerá-lo com código. Entretanto, deixarei a construção de declarações SQL como um

exercício para o leitor. No mapeador abstrato abaixo, você verá que fiz algumas suposições sobre os nomes das colunas e posições de nossos dados para modificação.
Isso se torna menos factível com dados legados. O mapeador abstrato irá provavelmente requerer que um pouco de metadados de colunas seja fornecido por cada mapeador concreto.

Uma vez que o mapeador abstrato tenha declarações SQL, pode gerenciar as

operações CRUD. Aqui está como um método de busca é executado:

class HapeadorAbstrato...
public MapeadorAbstrato (String tabela, String [ ] colunas)

this.tabela = tabela;
this.colunas = colunas;
criarDeclarações( );

(

}
public ObjetoDoDominio buscar (Long id) {
ObjetoDoDonínio obj = GerenciadorDeSessãoDeaplicação.
lerSessão( ) .lexMapaDeTdentidade| ) . ler (id) ;
if (obj == null) {
Connection con = null;
Preparedstatement dec = null;
ResultSet rs = null;

try {
con = Connect iontanager. INSTANCE.getConnection( ) ;
dec = con.prepareStatement (SQLCarregar) ;

dec.setong(1, id.longValue( });
rs = dec.executeQuery( };
if (rs.next()) {
obj = carregar(id, rs);
String modificadoPor = rs.getString(colunas.length + 2);
‘Timestamp modificado = rs.getTimestamp (colunas. length + 3);
int versão = rs.get
Int (colunas. length + 4);
obj .gravaCanposSistema (modificado, modificadoPor, versão) ;
GerenciadorDeSessãoDeAplicação.
lerSessão( ) .lerMapaDeldentidade( ) grava (obj) ;
) else {

}

throw new SystemException

} catch (SQLException sqlEx)

(tabela + " "+ id + " não existe");

{

throw new SystenBxception ("erro não esperado buscando " + tabela +" "+ id);

} finally {

}

liberaRecursosaD (rs, con, dec);

398

Parte Il e Os PacrÕEs

}

return obj;

protected abstract ObjetoDoDominio carregar

(Long id, ResultSet rs) throws SQLException;

Há alguns itens a perceber aqui. Primeiro, o mapeador verifica um Mapa de

Identidade (196) para se assegurar de que o objeto já não está carregado. Não usar um

Mapa de Identidade (196), poderia resultar em versões diferentes de um objeto sendo
carregadas em momentos diferentes em uma transação de negócio, levando a comportamento indefinido na sua aplicação, assim como criando uma confusão em qual-

quer verificação de versão. Assim que o conjunto resultante seja obtido, o mapeador

transfere para um método abstrato de carga que cada mapeador concreto deve implementar para extrair seus campos e retornar um objeto ativado. O mapeador cha-

ma gravarCamposDeSiystema( ) para gravar a versão e os dados de modificação do obje-

to do domínio abstrato. Embora um construtor pudesse parecer o meio mais apro-

priado de passar estes dados, fazer assim empurraria parte da responsabilidade pelo armazenamento da versão para cada mapeador concreto e objeto do domínio e

dessa forma enfraqueceria o Bloqueio Implícito (422).

Aqui está como um método carregar ( | concreto se parece:

class MapeadorDeCliente extends MapeadorAbstrato...
protected ObjetoDoDominio carregar (Long id, ResultSet rs) throws SQLException {

String none = rs.getString(2) ;
return Cliente.ativar
(id, nome, endereços) ;

O mapeador abstrato irá gerenciar de maneira semelhante a execução das ope-

rações de atualização e exclusão. O trabalho aqui é verificar que a operação do ban-

co de dados retorne um contador de linha igual a um. Se nenhuma linha tiver sido
atualizada, o bloqueio otimista não pode ser obtido, e o mapeador precisa então le-

vantar uma exceção de concorrência. Aqui está a operação de exclusão:

class Hapeadorabstrato...
public void apagar (ObjetoDoDonínio objeto) {
GerenciadorDesessãoDexplicação.
lerSessão( | .lerMapaDeTdentidade( ) .remove (objeto. lerd( ));
Connection con = null
PreparedStatement dec = null
try {
con = Connect ionManager . INSTANCE. getConnection( ) ;
dec = con.prepareStatement (SQLExcluir) ;
dec.setLong(1,

objeto. lerId( ).longvalue(

int contiinha = dec.executeUpdate( ) ;

));

if (contbinha == 0) {

}

dispararExceçãoDeConcorrência (objeto) ;

) catch (SQLException e) {

throw new Systenxception ("erro inesperado na exclusão”) ;
) finally {
liberaRecursosBD (con, dec) ;

}

Capituto 16

e PADRÕES DE CONCORRÊNCIA OFFLINE

399

protected void dispararExceçãoDeConcorrência (ObjetoDoDomínio objeto) throws SQLException {
Connection con = null;

PreparedStatement dec = null;
ResultSet rs = null;

try {
con = Connect ionManager. INSTANCE.getConnection| ) ;
dec = con.preparestatenent (SQLVerificaVersão) ;
dec.setint(1, (int) objeto.lerid( ) .longValue( ));
rs = dec. executeQuery( );
if (rs.next())

{

int versão = rs.getInt (1);
modificadoPor = rs.getString(2);
imestamp modificado = rs.getTimestamp
(3) ;
if (versão > objeto. lerVersão( )) {

String quando = DateFormat .getDateTimeInstance( ) format (modificado) ;
throw new ConcurrencyException (tabela +! * + objeto.lerid( ) +
" modificada por " + modificadoPor + * em * + quando) ;

) else {
throw new SystemException("erro inesperado ao checar o timestamp"
}

} else {

}

throw new ConcurrencyBxcception(tabela +" " + objeto.lerld( } +
* foi apagado") ;

} finally {

)

liberaRecursosBD (rs, com, dec};

A declaração SQL usada para verificar a versão em uma exceção de concorrên-

cia também precisa ser conhecida pelo mapeador abstrato. Seu mapeador deveria
construí-la quando constrói o SQL CRUD. Ela se parecerá com algo assim:

SQLdeverificaçãoDeversão. ..
SELECT versão, modificadoPor, modificado FROM cliente WHERE id = ?

Este código não dá uma idéia perfeita das diversas partes executando por meio
de diversas transações de sistema dentro de uma única transação de negócio. O mais
importante a lembrar é que a aquisição de Bloqueios Offline Otimistas deve ocorrer

dentro da mesma transação de sistema em que ocorre a confirmação das suas altera-

ções para manter a consistência dos dados gravados. Com a verificação embutida
nas declarações UPDATE e DELETE, isso não será um problema.

Dê uma olhada no uso de um objeto versão no código exemplo de Bloqueio de

Granularidade Alta (412). Embora Bloqueio de Granularidade Alta (412) possa resolver

alguns problemas de leitura inconsistente, um simples objeto versão não compartilhado pode ajudar a detectar leituras inconsistentes porque é um lugar conveniente
para acrescentar comportamento de verificação otimista como incrementar( } ou verificarSeAVersdofaMaisRecente( ). Aqui está uma Unidade de Trabalho (187) na qual adicio-

namos verificações de leitura consistente no nosso processo de confirmação de gravação, por meio da medida mais drástica de incrementar a versão, porque não sabe-

mos qual o nível de isolamento:

400

Parte ll e Os PADROES

class UnidadeDeTrabalho.
private List leituras = new ArrayList ( );

public void registrarLeitura (ObjetoDoDomínio objeto) (
leituras.
add objeto) ;

}
public void confirmar ( ) {
try {
verificarLeiturasConsistentes ( } ;
inserirNovo( |;
excluirRemovido(

atualizarSujo( );

) catch

);

(ConcurrencyException e) (

desfazTransaçãoDeSistema ( );

)

}

throw

e;

public void verificarLeiturasConsistentes(

) {

for (Iterator iterator = leituras.iterator( ); iterator.hasNext ( ))
ObjetoDoDomínio dependente = (ObjetoDoDominio)

dependente. lerversão( ) incrementar ( );

iterator.next( );

Perceba que a Unidade de Trabalho (184) desfaz a transação de sistema quando

detecta uma violação de concorrência. Você provavelmente decidiria desfazer a transação se houvesse qualquer exceção durante a confirmação. Não esqueça este passo!
Como alternativa a objetos versão, você pode adicionar verificações de versão à sua

interface mapeadora.

g
E

Capituto 16

e PADRÕES DE CONCORRENCIA OrFLine

401

Bloqueio Offline Pessimista (Pessimistic Offline Lock)
por David Rice

Previne conflitos entre transações de negócio concorrentes permitindo
que apenas uma transação de negócio acesse os dados de cada vez.

Sessão do Martin

Banco de Dados

Sessão do David

Limite da
Transação
e Sistema

do ú

Timite da

t+
aiken

|
|

|

|

|

|
TH

Terre: cliente bloqueado | | |
===

=

|

diversas transações de sistema, em que você fica por conta própria para gerenciar o
acesso concorrente aos seus dados.

A primeira abordagem a tentar é o Bloqueio Offline Otimista (392). Entretanto, esse padrão tem seus problemas. Se diversas pessoas acessarem os mesmos dados dentro de uma transação de negócio, uma delas confirmará a gravação com facilidade,

mas as outras entrarão em conflito e falharão. Como o conflito só é detectado no final da transação de negócio, as vítimas farão todo o trabalho da transação apenas para descobrir no último minuto que tudo falhará e seu tempo terá sido desperdiçado.
Se isso acontecer, com muita freqiiéncia em transações de negócio longas, o sistema

logo se tornará bastante impopular.

o

felizmente, isso não funciona sempre bem porque sistemas de transações não são
equipados para trabalhar com transações longas. Por essa razão, você tem que usar

E

Já que a concorrência offline envolve manipulação de dados para uma transação de
negócio que perdura por diversas solicitações, a abordagem mais simples pareceria
ser a de ter uma transação de sistema aberta durante toda a transação de negócio. In-

nbojg

ee

Transação
SeiNegócio

|
|

ab

PS
/

402

Parte Il e Os PaprOes

O Bloqueio Offline Pessimista previne conflitos evitando-os completamente. Ele

força uma transação de negócio a obter um bloqueio sobre um fragmento de dados

antes de começar a usá-lo, de modo que, na maior parte do tempo, assim que você

começa uma transação de negócio, pode ter bastante certeza de que irá completá-la

sem ser interrompido pelo controle de concorrência.
Como Funciona

Você implementa o Bloqueio Offline Pessimista em três fases: determinando de

que tipo de bloqueios precisa, criando um gerenciador de bloqueios e definindo pro-

cedimentos para uma transação de negócios para usar os bloqueios. Além disso, se

você estiver usando Bloqueio Offline Pessimista como complemento para o Bloqueio Of-

fline Otimista (392), precisa determinar quais tipos de registros bloquear.

Em relação aos tipos de bloqueios, a primeira opção é um bloqueio exclusivo

de gravação, que requer apenas que uma transação de negócios obtenha um blo-

queio para editar dados da sessão. Isso evita conflito por não permitir que duas

transações de negócio façam alterações no mesmo registro, ao mesmo tempo. O que

este esquema de bloqueio ignora é a leitura dos dados, de modo que se não for cru-

cial que uma sessão de visualização tenha os dados mais recentes, esta estratégia será suficiente.

Se for muito importante que uma transação de negócio tenha sempre os dados
mais recentes, independentemente de suas intenções de editá-los, use o bloqueio exclusivo de leitura. Este requer que uma transação de negócio obtenha um bloqueio

simplesmente para carregar o registro. Tal estratégia tem claramente o potencial de
restringir severamente a concorrência de um sistema. Na maioria das aplicações corporativas, o bloqueio exclusivo de gravação vai permitir muito mais acesso concorrente aos registros do que este tipo de bloqueio.

Uma terceira estratégia combina os dois tipos para fornecer o bloqueio restriti-

vo do bloqueio exclusivo de leitura, assim como o aumento de concorrência do blo-

queio exclusivo de gravação. Chamado de bloqueio de leitura/gravação, é um pou-

co mais complexo do que os dois primeiros. O relacionamento dos bloqueios de leitura e de gravação é a chave para obter o melhor dos dois mundos:

* Bloqueios de leitura e de gravação são mutuamente exclusivos. Um registro
não pode ser bloqueado para gravação se alguma outra transação de negócio

possuir um bloqueio de leitura nele. Ele não pode ser bloqueado para grava-

E

ção se alguma outra transação de negócio possuir um bloqueio de gravação

nele.

*

Bloqueios de leitura concorrentes são aceitáveis. A existência de um único

bloqueio de leitura evita que alguma transação de negócio edite o registro,

então não há mal em permitir qualquer número de sessões como leitores,

uma vez que uma delas tenha recebido permissão de leitura.

Permitir diversos bloqueios de leitura é o que aumenta a concorrência do siste-

ma. O aspecto negativo deste esquema é que é um pouco desagradável de implementar e apresenta mais desafios para os especialistas no domínio se preocuparem

quando estiverem modelando o sistema.

Durante a escolha do tipo correto de bloqueio, pense em maximizar a concor-

rência do sistema, satisfazer às necessidades do sistema e minimizar a complexidade

Capituto 16

e PADRÕES DE CONCORRENCIA OFFLINE

403

do código. Tenha em mente também que a estratégia de bloqueio deve ser compreen-

dida pelos analistas e modeladores do domínio. O bloqueio não é só um problema

técnico. O tipo errado de bloqueio, simplesmente bloqueando todos os registros, ou

bloquear os tipos errados de registros pode resultar em uma estratégia de Bloqueio
Offline Pessimista ineficaz. Uma estratégia de Bloqueio Offline Pessimista ineficaz não

evita conflitos no começo da transação de negócio ou degrada a concorrência de seu

sistema multiusuário de tal maneira que ele pareça mais como um sistema monousuário. A estratégia de bloqueio errada não pode ser salva por uma implementação
técnica apropriada. Na verdade, não é uma má idéia incluir o Bloqueio Offline Pessimista no seu modelo de domínio.

Assim que você tiver decidido seu tipo de bloqueio, defina seu gerenciador de

bloqueios. O trabalho do gerenciador de bloqueio é dar ou negar qualquer solicitação de uma transação de negócio para obter ou liberar um bloqueio. Para executar
seu trabalho, ele precisa saber o que está sendo bloqueado, assim como o pretenden-

te a dono do bloqueio — a transação de negócio. É bastante possível que o seu concei-

to de uma transação de negócio não seja alguma coisa que possa ser identificada de

forma única, o que torna um pouco difícil passar uma transação de negócio para o

gerenciador de bloqueios. Neste caso, considere seu conceito de uma sessão, já que é
mais provável que você tenha um objeto de sessão à sua disposição. Os termos “sessão” e “transação de negócio” são razoavelmente intercambiáveis. Desde que as
transações de negócio sejam executadas serialmente dentro de uma sessão, esta ses-

são ficará bem como dona de um Bloqueio Offline Pessimista. O código exemplo deve

lançar alguma luz sobre essa idéia.

O gerenciador de bloqueios não deve consistir de muito mais do que uma tabe-

la que mapeia os bloqueios a seus donos. Um gerenciador de bloqueios simples po-

deria envolver uma tabela hash na memória ou poderia ser uma tabela de banco de

dados. Não importa qual, mas você deve ter uma e apenas uma tabela de bloqueios,

então, se ela estiver na memória, assegure-se de usar um singleton [Gang of Four]. Se

o seu servidor de aplicação for distribuído em clusters, uma tabela de bloqueios na

memória não funcionará, a menos que seja restrita a uma única instância no servidor.
O gerenciador de bloqueios baseado em banco de dados é provavelmente mais apro-

priado uma vez que você esteja em um ambiente que use um servidor de aplicação

distribuído em clusters.

O bloqueio, quer seja implementado como um objeto ou como um SQL sobre
uma tabela de banco de dados, deve permanecer privado para o gerenciador de blo-

queios. Transações de negócio devem interagir apenas com o gerenciador de bloqueios, nunca com um objeto de bloqueio.

Agora é hora de definir o protocolo de acordo com o qual uma transação de ne-

gócio deve usar o gerenciador de bloqueios. Este protocolo deve incluir o que blo-

quear e quando, quando liberar um bloqueio e como agir quando um bloqueio não

puder ser obtido.
O que bloquear depende de quando bloquear, então vejamos o quando primeiro. Geralmente, a transação de negócio deve obter um bloqueio antes de carregar os

dados, pois não há por que obter um bloqueio sem uma garantia de que você terá a
versão mais recente do item bloqueado. Já que estamos obtendo bloqueios dentro de
uma transação de sistema, há circunstâncias em que a ordem do bloqueio e da carga

não importará. Dependendo do tipo do seu bloqueio, se você estiver usando transa-

ções de leitura repetíveis ou serializáveis, a ordem na qual você carrega objetos e ob-

tém bloqueios pode não importar. Uma opção é executar uma verificação otimista so-

a8
zg

404

Parte Il e Os PaprOes

bre um item após obter o Bloqueio Offline Pessimista. Você deve estar bastante seguro

de que tem a versão mais recente de um objeto após tê-lo bloqueado, o que geralmente se traduz na obtenção do bloqueio antes de carregar os dados.

Agora, o que estamos bloqueando? Parece que estamos bloqueando objetos ou

registros ou apenas quase tudo, mas o que geralmente bloqueamos é realmente o ID,

ou a chave primária, que usamos para buscar esses objetos. Isso nos permite obter o

bloqueio antes de carregá-los. Bloquear o objeto funciona bem desde que não o obri-

gue a quebrar a regra de que um objeto seja a versão mais atual após você obter seu
bloqueio.
A regra mais simples para liberar bloqueios é fazê-lo quando a transação de negócio for completada. Liberar um bloqueio antes disso poderia ser admissível, de-

pendendo do seu tipo de bloqueio e da sua intenção de usar esse objeto novamente

dentro da transação. Ainda assim, a menos que você tenha uma razão muito especí-

fica para liberar prematuramente o bloqueio, tal como algum problema especialmente desagradável de capacidade de resposta do sistema, continue executando a liberação no momento da conclusão da transação de negócio.

A ação mais fácil para uma transação de negócio que não consiga obter um bloqueio é abortá-la. O usuário deveria achar isso aceitável, já que o Bloqueio Offline Pessimista deveria resultar em falha bem antes na transação. O desenvolvedor e o projetista podem certamente ajudar nessa situação, não esperando até tarde na transação

para obter um bloqueio especialmente disputado. Se for possível, obtenha todos os
seus bloqueios antes de o usuário começar a trabalhar.

Para qualquer determinado item que você pretenda bloquear, o acesso à tabe-

la de bloqueios deve ser serializado. Com uma tabela de bloqueios na memória é
mais fácil serializar o acesso ao gerenciador de bloqueios inteiro com qualquer

construção que sua linguagem de programação fornecer. Se você precisar de maior

concorrência do que isso permite, esteja ciente de que está entrando em um territó-

rio complexo.

Se a tabela de bloqueios for armazenada em um banco de dados, a primeira re-

gra, é claro, é interagir com ela dentro de uma transação de sistema. Tire todo o pro-

veito das capacidades de serialização que um banco de dados fornece. Com os bloqueios exclusivo de leitura e exclusivo de gravação, a serialização é uma simples
questão de fazer o banco de dados reforçar uma restrição de unicidade na coluna que

Pessimista

ES
5

armazena o ID do item bloqueável. Armazenar bloqueios de leitura /gravação em

um banco de dados torna as coisas um pouco mais difíceis, já que a lógica requer leituras da tabela de bloqueios além de inserções e desta maneira se torna imperativo

evitar leituras inconsistentes. Uma transação de sistema com um nível de isolamen-

to de serialização fornece a maior segurança, já que garante que não há leituras in-

consistentes. Usar transações serializáveis por todo o nosso sistema poderia nos tra-

zer problemas de desempenho, mas uma transação de sistema serializável separada

para obtenção de bloqueios e um nível de isolamento menos rígido para outro uso
poderia aliviar este problema. Outra opção é investigar se um procedimento armaze-

nado poderia ajudar no gerenciamento de bloqueios. O gerenciamento de concorrên-

cia pode ser difícil, então não tenha receio de transferi-lo para seu banco de dados em
momentos críticos.

A natureza serial do gerenciamento de bloqueios anuncia um gargalo de de-

sempenho. Uma consideração importante aqui é a granularidade do bloqueio, já que

quanto menos bloqueios requeridos, menor o gargalo que você terá. Um Bloqueio de

Granularidade Alta (412) pode cuidar da disputa pela tabela de bloqueios.

Capituto 16

e PADRÕES DE CONCORRENCIA OFrLinE

405

Com um esquema de bloqueio pessimista de transações de sistema, como “SE-

LECT FOR UPDATE...” ou EJBs de entidade, deadlocks são uma possibilidade que
não pode ser ignorada, pois estes mecanismos de bloqueio irão esperar até que um

bloqueio se torne disponível. Pense em um deadlock dessa forma. Dois usuários pre-

cisam dos recursos A e B. Se um obtém o bloqueio de A e o outro o de B, ambas as

transações poderiam esperar para sempre pelo outro bloqueio. Considerando que es-

tamos perdurando por diversas transações de sistema, esperar por um bloqueio não
faz muito sentido, especialmente porque uma transação de negócio poderia durar 20

minutos. Ninguém quer esperar por esses bloqueios. E isto é bom, porque codificar

uma espera envolve timeouts e logo se torna complicado. Simplesmente faça com que
seu gerenciador de bloqueios levante uma exceção assim que um bloqueio esteja in-

disponível. Isto elimina o fardo de lidar com deadlocks.

Um requisito final é gerenciar timeouts dos bloqueios de sessões perdidas. Se

uma máquina cliente pára de funcionar no meio de uma transação, essa transação

perdida não consegue ser concluída e nem liberar seus bloqueios obtidos. Isso é al-

go sério em uma aplicação Web na qual sessões são abandonadas regularmente pe-

los usuários. O ideal é você ter um mecanismo de timeout gerenciado pelo seu ser-

vidor de aplicação, em vez de fazer com que sua aplicação trate timeouts. Servidores de aplicações Web fornecem uma sessão HTTP para isso. Os timeouts podem ser

implementados registrando um objeto utilitário que libera todos os bloqueios

quando a sessão HTTP se torna inválida. Outra opção é associar um timestamp a cada bloqueio e considerar inválido qualquer bloqueio mais velho que uma determinada idade.

Quando Usá-lo

O Bloqueio Offline Pessimista é apropriado quando a chance de conflitos entre sessões
concorrentes for alta. Um usuário nunca deveria ter que jogar trabalho fora. Bloquear

também é apropriado quando o custo de um conflito for alto demais independentemente de sua probabilidade. Bloquear cada entidade de um sistema quase que certa-

mente criará grandes problemas de disputa de dados, então lembre-se de que o Blo-

queio Offline Pessimista é bastante complementar ao Bloqueio Offline Otimista (392) e só
use o Bloqueio Offline Pessimista quando for realmente necessário.

nbojg

uma transação de sistema. Muitas técnicas de bloqueio pessimista de transações de

elas podem não ser mais prejudiciais do que o Bloqueio Offline Pessimista e muito mais
fáceis de programar. Faça algum teste de carga antes de decidir.
Não use estas técnicas se as suas transações de negócio couberem dentro de
sistema já estão nos servidores de aplicações e de banco de dados que você

já

usando, entre eles a declaração SQL “SELECT FOR UPDATE” para bloqueio de ban-

co de dados e o EJB de entidade para bloqueio de servidor de aplicações. Por que se
preocupar com timeouts, visibilidade de bloqueio e coisas desse tipo, quando não há

necessidade? Compreender esses tipos de bloqueios pode com certeza agregar muito valor à sua implementação de Bloqueio Offline Pessimista. Compreenda, entretanto,
que o inverso não é verdadeiro! O que você leu aqui não irá lhe preparar para escre-

ver um monitor de transações ou gerenciador de banco de dados. Todas as técnicas
de bloqueio offline apresentadas neste livro dependem do seu sistema ter um moni-

tor de transações real próprio.

19)

uma transação longa. Transações longas nunca são boas, mas em algumas situações

au

Se você tiver que usar o Bloqueio Offline Pessimista, deveria também considerar

406

Parte Il e Os PaprOes

Exemplo: Gerenciador de Bloqueios Simples (Java)
Neste exemplo, iremos primeiro construir um gerenciador de bloqueios para blo-

queios exclusivos de leitura — lembre-se de que você precisa destes bloqueios para

ler ou editar um objeto. Demonstraremos então como o gerenciador de bloqueios
poderia ser usado em uma transação de negócios que perdura diversas transações
de sistema.

O primeiro passo é definir a interface do nosso gerenciador de bloqueios.

interface GerenciadorDeBloqueiosExclusivosDeLeitura. ..
public static final CerenciadorDeBloqueiosExclusivosDeLeitura INSTANCE =
(GerenciadorDeBlogueiosExclusivosDeLeitura)

Plugins .getPlugin (GerenciadorDeBloqueiosExclusivosDeLeitura.class) ;

public void obterBloqueio (Long bloqueável, String dono) throws ConcurrencyException;
public void liberarBloqueio (Long bloquedvel, String dono) ;
public void liberarTodosBloqueios (String dono) ;
Perceba que estamos identificando blogueável com o tipo long e o dono como
string. Bloqueável é do tipo long porque cada tabela no nosso banco de dados usa uma
chave primária desse tipo que é única em todo o sistema e, desta maneira, serve como um bom ID bloqueável (que deve ser único por todos os tipos manipulados pela
tabela de bloqueios). O ID do dono é uma string porque o exemplo será uma aplicação
Web, e o ID da sessão HTTP é um bom dono de bloqueio dentro dela.

Escreveremos um gerenciador de bloqueios que interage diretamente com uma

tabela de bloqueios no nosso banco de dados, em vez de com um objeto de bloqueio.
Perceba que esta é a nossa própria tabela chamada bloqueio, como qualquer tabela de
aplicação, e não parte do mecanismo interno de bloqueio do banco de dados. Obter
um bloqueio é uma questão de inserir com sucesso uma linha na tabela de bloqueios.
Liberá-lo é uma questão de apagar essa linha. Aqui está o esquema para a tabela de

bloqueios e parte da implementação do gerenciador de bloqueios:
tabela bloqueio...
create table bloqueio (idBlogueável bigint primary key, idDono bigint)
class InplenentaçãoBDGerenciadorDeBloqueiosExclusivosDeLeitura
implements GerenciadorDeBloqueiosExclusivos. .
private static final String SQL_INSERIR =
“insert into bloqueio values(?,

?)";

private static final String SQL EXCLUIR UNICO =

"delete from bloqueio where idBloqueável = ? and idDono = 2";

private

static

"delete
private

final

String

SQL

from bloqueio where

static

final

String

EXCLUIR

TODOS

=

idDono = 2";

SQL VERIFICAR

=

"select idBloquedvel from bloqueio where idBloqueável = ? and idDono = 2";

public void obterBloqueio (Long bloqueável, String dono) throws ConcurrencyException {
if (! temBloqueio(bloqueavel, dono)) {

Connection con = null
PreparedStatement dec = null;
try {

con = Connect ionNanager INSTANCE. getConnection( ) ;
dec = con.prepareStatenent (SQL INSERIR) ;

Capítuto 16

e PADRÕES DE CONCORRENCIA OFrLinE

407

dec.setLong(1, bloqueável. longvalue ( ));

dec.setString(2, dono) ;
dec.executeUpdate ( |;

) catch (SQLEXception sqlEx) {

throw new ConcurrencyBxception('não foi possível bloquear "+ bloqueével) ;

} finally {

}

}

}

liberaRecursosBD (con, dec);

public void liberarBloqueio (Long bloqueável, String dono) {

Connection con = null

PreparedStatement dec =

try {

wll;

con = Connect ionManager .INSTANCE.getConnection( ) ;
dec = con.prepareStatement
(SOL EXCLUIR UNICO) ;

dec.setLong(1, bloqueavel.longValue( ));
dec.setstring(2, dono) ;
dec.executeUpdate( );

} catch (SQLBXception sglgx) {

throw new SystemException ("erro inesperado ao liberar bloqueio sobre "+ bloqueável) ;
) finally (
liberaRecursosBD (con, dec) ;

}

O método público liberarTodosBlogueios( ) e o método privado tenBloqueio( )

não são mostrados no gerenciador de bloqueios. O método liberar TodosBlogueios ( )
faz exatamente o que seu nome dá a entender e libera todos os bloqueios de um do-

no. O método tenBlogueio( ) consulta o banco de dados para verificar se um dono já

possui um bloqueio. Não é incomum que um código de sessão tente obter um bloqueio que ele já possui. Isso significa que obterBlogueio( ) deve primeiro verificar se o
dono já não tem o bloqueio antes de tentar inserir a linha de bloqueio. Como a tabela de bloqueios é geralmente um ponto de disputa de recursos, estas leituras repeti-

tivas podem degradar o desempenho da aplicação. Pode ser necessário que você coloque em memória cache bloqueios que já foram obtidos em nível de sessão para verificação de propriedade. Tenha cuidado ao fazer isso.

Agora iremos montar uma aplicação Web simples para realizar a manutenção

de registros dos clientes. Primeiro, estabeleceremos um pouco de infra-estrutura para facilitar o processamento da transação de negócio. Alguns conceitos de uma sessão de usuário serão necessários para as camadas abaixo da camada Web, de modo

que não poderemos depender apenas da sessão HTTP. Vamos nos referir a essa nova

sessão como a sessão de aplicação para distingui-la da sessão HTTP. Sessões de aplicação armazenarão seus IDs, um nome de usuário e um Mapa de Identidade (196) pa-

ra colocar em memória cache os objetos carregados ou criados durante a transação de
negócio. Eles serão associados à thread correntemente em execução para que sejam
encontrados.

class SessãoDenplicação...
private String usuário;
private String id;

408

Parte ll e Os PADRÕES

private MapaDeIdent idade mpIdent idade ;

public SessãoDexplicação (String usuário, String id, MapaDeldentidade mprdentidade) {
this usuário = usuário;
this.mpldentidade = mpldentidade;
this.id = id;

}
class GerenciadorDeSessdoDeAplicagio. ..
private

static

ThreadLocal

atual

= new ThreadLocal(

);

public static SessãoDeAplicação lerSessão( ) {

return (SessdoDeAplicacéo) atual.get ( |;

}
public static void gravarSessio(SessdoDeAplicagio sessão) (
atual,
set (sessão) ;
}
Iremos usar um Controlador Frontal (328) para lidar com as solicitações, de mo-

do que precisaremos definir um comando. A primeira coisa que cada comando deve

fazer é indicar a sua intenção de iniciar uma nova transação de negócio ou continuar

uma que já exista. Isso é uma questão de estabelecer uma nova sessão de aplicação

ou encontrar a atual. Aqui temos um comando abstrato que fornece métodos convenientes para estabelecer o contexto da transação de negócio.
interface Comando...

public void init (HttpServletRequest so)
ação, HttpServletResponse resposta) ;
public void processar( ) throws Exception;
abstract class ComandoDeTransaçãoDeNegócio implements Comando.
public void init (HttpServletRequest solicitação, HttpServletResponse resposta) {
this. solicitação = solicitação;
}

this.resposta

= resposta;

protected void iniciarNovaTransaçãoDeNegócio ( ) {
HttpSession sessãoHttp = lerSolicitação(

) .getSession(true) ;

SessãoDexplicação sessdoDeAplicacdo = (SessãoDeaplicação) sessãoHttp.getattribute (APP_SESSION} ;
if (sessdoDeAplicacdo != null)

{

GerenciadorDeBloqueiosixclusivosDeLeitura. INSTANCE. liberarTodosBloqueios
(sessãoDenplicação.getId( });

}
sessdoDeAplicagdo = new SessdoDeAplicagio(lerSolicitacao( | getRemoteUser ( ),
sessiottp.getId( ), new MapaDeldentidade( }) ;
GerenciadorDeSessãoDeRplicação.gravarsessão (sessãoDenplicação) ;
sessãoHttp. setattribute (APP SESSION, sessãoDeRplicação) ;
sessãoHttp. setAttribute (LOCK REMOVER,

}

new RemovedorDeBloqueio (sessãoDeAplicação.get ID( )));

protected void continuarTransaçãoDeNegócio(

}{

protected HttpServletRequest lerSolicitação

() {

HttpSession sessdolittp = lerSolicitação( ) .getSession( );
SessãoDexplicação sessdoDeAplicacdo = (SessãoDeaplicação) sessãoHttp.getattribute
(APP SESSTON) ;
GerenciadorDeSessãoDeRplicação.
gravarSessão (sessãoDeApLicação) ;
return solicitação;

Capituto 16

}

protected HttpServletResponse lerResposta

return resposta;

e PADRÕES DE CONCORRENCIA OFFLINE

409

| } {

Perceba que, quando estabelecemos uma nova sessão de aplicação, removemos
os bloqueios de qualquer uma existente. Também adicionamos um listener para os
eventos associados à sessão HTTP que removerão quaisquer bloqueios possuídos

por uma sessão de aplicação quando a sessão HTTP correspondente expirar.
class RenovedorDeBloqueios implenents HttpSessionBindingListener...
private String idsessão;

public RemovedorDeBlogueios

}

this. idsessão = idsessão;

(String idsessão)

(

public void valorSolto (HttpSessionBindingEvent evento) {

try {
iniciarTransacdoDeSistema( ) ;
CerenciadorDeBloqueiosExclusivosDeLeitura. INSTANCE. 1iberarTodosBloqueios (this. idsessão) ;
confirmarTransaçãoDeSistema( ) ;
} catch (Exception e) {
tratarErroSério(e) ;

Nossos comandos contêm tanto lógica de negócio padrão quanto gerenciamen-

to de bloqueio, e cada comando deve ser executado dentro dos limites de uma única

transação de sistema. Para garantirmos isso, podemos decorá-lo [Gang of Four] com

um objeto comando transacional. Assegure-se de que todo o bloqueio e negócio de
domínio padrão ocorram dentro de uma única transação de sistema. Os métodos que
definem os limites da transação de sistema dependem do seu contexto de distribuição. É obrigatório desfazer a transação do sistema quando uma exceção de concor-

rência, ou qualquer outra exceção for detectada, pois isso irá evitar que quaisquer al-

terações sejam inseridas nos dados permanentes quando um conflito ocorre.
public ComandoTransacional (Comando impl) (

}

this.impl = impl;

public void processar( ) throws Exception {
iniciarTransaçãoDeSistema( ) ;
try {
imp] .processar( );

confimarTransaçãoDeSistema( ) ;

) catch (Exception e) {

desfazerTransaçãoDesistema( ) ;
throw

e;

e
a)

ejstuuissag

class ComandoTransacional implements Comando...

o

=

410

Parte ll e Os Parões

Agora é uma questão de escrever o servlet controlador e os comandos concretos.

O servlet controlador tem a responsabilidade de encapsular cada comando com um

controle de transação. Os comandos concretos devem estabelecer o contexto da tran-

sação de negócio, executar lógica de domínio e obter e liberar bloqueios onde for
apropriado.

class ServletControlador extends HttpServlet...

protected void doGet (HttpServletRequest solicitação, HttpservletResponse resposta)
throws ServletException,

IOException {

try {
String noneDoComando solicitação. getParameter ("comando") ;
Comando com = lerComando (nomeDoComando) ;
comando.
init (solicitação, resposta) ;
comando. processar );
} catch (Exception e) {

)

}

gravarExceção(e,

resposta.getWriter(

private Comando lerComandos (String nome)

try {

));

(

String nomeDaClasse = (String) comandos.get (nome) ;

Comando com = (Conando) Class. forName (noneDaClasse) .newInstance( ) ;
return new ComandoTransacional (com) ;

) catch (Exception e) {

e.printStackTrace( |;

}

}

throw new SystemException ("não foi possível criar objeto comando para " + nome);

class ConandoRditarCliente extends Comando TransaçãoDeNegócio
public void processar ( ) throws Exception {
iniciarNovaTransaçãoDeNegócio( );

Long idCliente = new Long(lerSolicitação( ) .getParameter
("id cliente");

}

GerenciadorDeBloqueiosExclusivosDeLeitura.
INSTANCE. obterBloqueio(
idCliente, GerenciadorDeSessãoDetpl
icação. lerSessão( ) .getId( ));
Mapeador mapeadorDeCliente = RegistroMapeador.
INSTANCE. lerMapeador (Cliente.class) ;
Cliente cliente = (Cliente) mapeadorDeCliente.
buscar (idCliente) ;
lerSolicitação( ) .lerSessio( }.setAttribute("cliente", cliente) ;
forward (*/editarCliente, jsp") ;

class ComandoGravarCliente extends Comando TransaçãoDeNegóc:
public void processar ( ) throws Exception {
continuarTransaçãoDeNegócio(

);

Cliente cliente = (Cliente) lerSolicitação( ) lerSessão( ) .getAttribute ("cliente") ;
String none = lerSolicitação( ) .getParameter ("noneDoCliente") ;
cliente. gravarNome (nome) ;

Mapeador mapeadorDeCliente = RegistroNapeador.
INSTANCE. lerMapeador (Cliente.class) ;

mapeadorDoCliente.atualizar (cliente) ;

GerenciadorDeBloqueiosixclusivosDeLeitura. INSTANCE. liberarBloqueio(cliente.lerID( ),
GerenciadorDeSessaoDeRplicacdo.lerSessao( | .getID( )];
forward ("/clienteGravado.jsp") ;

Capituto 16

e PADRÕES DE CONCORRENCIA OFFLINE

411

Os comandos recém-mostrados evitarão que duas sessões quaisquer traba-

lhem com o mesmo cliente ao mesmo tempo. Qualquer outro comando na aplicação

que trabalhar com um objeto cliente deve se assegurar de obter o bloqueio ou de tra-

balhar apenas com um cliente bloqueado por um comando anterior na mesma tran-

sação de negócio. Visto que temos uma verificação tenBloqueio( ) no gerenciador de
bloqueios, poderíamos simplesmente obter o bloqueio em cada comando. Isso po-

deria ser ruim para o desempenho, mas certamente garantiria que temos um bloqueio. O Bloqueio Implícito (422) discute outras abordagens seguras de mecanismos

de bloqueio.

A quantidade de código no framework poderia parecer um pouco fora de proporção em relação à quantidade de código de domínio. De fato, o Bloqueio Offline Pessimista requer, no mínimo, sincronizar a atuação de uma sessão de aplicação, uma

transação de negócio, um gerenciador de bloqueio e uma transação de sistema, o que

é claramente um desafio. Esse exemplo serve mais como inspiração do que como modelo de arquitetura, pois ele sofre de falta de robustez em muitas áreas.

ge
zg

E

412

Parte Il e Os PaprOes

Bloqueio de Granularidade Alta (Coarse-Grained

Lock)
por David Rice e Matt Foemmel

Bloqueia um conjunto de objetos relacionados utilizando para isso um único bloqueio.

Cliente

EE

1

ES

1

<— |

pioqueio

Endereço

Os objetos muitas vezes podem ser editados em grupos. Talvez você tenha um clien-

te e seu conjunto de endereços. Se este for o caso, ao usar a aplicação, faz sentido blo-

quear todos esses itens se você quiser bloquear qualquer um deles. Ter um bloqueio

separado para objetos individuais apresenta um número de desafios. Primeiro, qualquer um que for manipulá-los tem que escrever código que busque a todos para po-

der bloqueá-los. Isso é suficientemente fácil com um cliente e seus endereços, mas fi-

ca bastante complicado uma vez que se tem mais grupos para bloquear. E se os gru-

pos ficarem complicados? Onde fica este comportamento quando seu framework está

gerenciando a obtenção de bloqueios? Se a sua estratégia de bloqueios requerer que
um objeto seja carregado para que seja bloqueado, como com o Bloqueio Offline Oti-

mista (392), bloquear um grupo grande afetará o desempenho. Além disso, com o Bloqueio Offline Pessimista (401), um conjunto de bloqueios grande é uma dor de cabeça
para gerenciar e aumenta a disputa pela tabela de bloqueios.

Um Bloqueio de Granularidade Alta é um único bloqueio que cobre muitos obje-

tos. Ele não apenas simplifica o próprio ato de bloquear como também o libera de ter

que carregar todos os membros de um grupo para poder bloqueá-los.

Como Funciona
O primeiro passo na implementação do Bloqueio de Granularidade Alta é criar um único ponto de disputa para bloquear um grupo de objetos. Isso faz com que apenas um

bloqueio seja necessário para bloquear todo o conjunto. Você fornece então o cami-

nho mais curto possível para encontrar esse ponto único de bloqueio de modo a minimizar os membros do grupo que devem ser identificados e possivelmente carregados na memória no processo de obtenção desse bloqueio.

Com o Bloqueio Offline Otimista (392), fazer com que cada item do grupo com-

partilhe uma versão (veja a Figura 16.2) cria o ponto único de disputa, o que significa compartilhar a mesma versão, não uma versão igual. Incrementar essa versão blo-

queará todo o grupo com um bloqueio compartilhado. Configure seu modelo para

indicar cada membro do grupo na versão compartilhada, e você terá certamente mi-

nimizado o caminho para o ponto de disputa.

