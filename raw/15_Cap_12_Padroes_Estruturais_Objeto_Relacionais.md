# 15_Cap_12_Padroes_Estruturais_Objeto_Relacionais

CapiTuLo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

231

private static long númeroDaSegúência (LinhaDeltem li) {

return númeroDaSegiência (1i.lerchave( |);

[fo comparador não funciona bem aqui devido a chaves nulas não gravadas

protected String linhaDaTabelaDeChaves ( ) (

}

throw new UnsupportedoperationBxception( };

Esse algoritmo seria muito melhor se eu usasse o método Collections.max, mas

uma vez que podemos (e de fato iremos) ter pelo menos uma chave nula, esse méto-

do falharia.

Atualizações e Exclusões Após tudo isso, atualizações e exclusões são praticamente inofensivas. Mais uma vez, temos métodos abstratos para o suposto caso costumeiro e métodos sobrescritos para os casos especiais.
As atualizações funcionam desta forma:

class Mapeadorabstrato ..
public void atualizar (ObjetoDoDonínioConchave sujeito) (
PreparedStatement dec = null;
try {
dec = DB.prepare (stringDoConandoDeatualização( )) ;
carregarComandoDeAtualização
(sujeito,

dec.executel );

dec);

} catch (SQLException e) {
throw new Applicat ionException(e) ;

) finally {

}

‘DB. cleanUp (dec) ;

}
abstract protected String stringDoConandoDentualização( };
abstract protected void carregarConandoDeatual
ização (ObjetoDoDonínioConChave sujeito,
PreparedStatement dec)

throws SQLException;
class MapeadorDePedido. ..
protected void carregarComandoDeRtual
ização (ObjetoDoDonínioConChave sujeito, PreparedStatement dec)
throws SQLException
{
Pedido pedido = (Pedido) sujeito;
dec. setString(1, pedido. JerCliente( ));
dec. setLong(2, pedido.lerChave( ) .longvalue( ));
}
protected String stringDoComandoDeAtualização

}

( ) {

return "UPDATE pedidos SET cliente = ? WHERE id = 2";

class MapeadorDeLinhaDeitem. ..
protected String stringDoComandoDeAtualizacao ( ) {
return
"UPDATE

linhas

itens

UEC)

* SET quantia = ?, produto = ? "+
"WHERE idDoPedido = ? AND seq = 2";

232

Parte ll e Os Parões

}
protected void carregarConandoDeRtualização (ObjetoDoDonínioConchave sujeito, PreparedStatement dec)
throws SQLException
{
dec.setLong(3, idDoPedido (sujeito. lerChave( ));
dec.
set Long (4, númeroDaSegiência
(sujeito. lerChave(
LinhaDeltem li = (LinhaDeitem) sujeito;

dec.setInt(1, li.lerQuantia( |);

dec.setstring(2,

li.lerProduto(

));

));

As exclusões funcionam assim:

class Mapeadorabstrato...
public void excluir (ObjetoDoDomínioComChave sujeito)

PreparedStatement dec = null;
try {

a
5

{

dec = DB.prepare (stringDoComandoDeExclusão( )) ;

'onandoDeExclusão (sujeito, dec) ;

dec.execute(

);

) catch (SQLException e) (

throw new ApplicationException
(e);

) finally (
DB.cleantp (dec) ;
}

}
abstract protected String stringDoConandoDeBxclusao( );
protected void carregarConandoDeExclusão (ObjetoDoDonínioConChave sujeito, PreparedStatement dec)
throws SQLException
{
dec.setLong(1,

sujeito.lerChave(

}
class MapeadorDePedidos. ..

).longValue(

|);

protected String stringDoComandoDeExclusão ( ) {
return

"DELETE

FROM pedidos

}
class HapeadorDeLinhaDeltem....

WHERE

id

= 2";

protected String stringDoComandoDeExclusão ( ) {

return "DELETE FROM linhas itens WHERE idDoPedido = ? AND seq =

}
protected void carregarComandoDeBxclusdo (ObjetoDoDonínioConchave sujeito, Preparedstatement dec)
throws SQLException
{
dec.setLong(1,

idDoPedido (sujeito. lerChave( )));

dec. setLong(2, númeroDaSegiência
(sujeito. lerChave ( )));

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

233

Mapeamento de Chave Estrangeira (Foreign Key Mapping)

Mapeia uma associação entre objetos para uma
referência de chave estrangeira entre tabelas.
Álbum

título: String

1

*

«tabela»
Álbuns

ID: int

título: varchar

idDoArtista: int

Artista

nome: String

«tabela»
Artistas

ID: int

‘6

nome: varchar

Os objetos podem se referir uns aos outros diretamente por meio de referências. Mes-

mo o sistema orientado a objetos mais simples conterá um pequeno grupo de objetos

conectados entre si por todos os tipos de meios interessantes. Para gravar esses obje-

tos em um banco de dados, é vital gravar essas referências. Contudo, já que os dados

das referências são específicos à instância particular do programa sendo executado,
você não pode simplesmente gravar os valores de dados em estado bruto. Outra

complicação é o fato de que os objetos podem facilmente possuir coleções de referên-

cias para outros objetos. Tal estrutura viola a primeira forma normal dos bancos de

dados relacionais.
Um Mapeamento de Chave Estrangeira mapeia uma referência a um objeto como
uma chave estrangeira no banco de dados.

Como Funciona
A chave óbvia para esse problema é o Campo Identidade (215). Cada objeto contém a
chave do banco de dados da tabela do banco de dados apropriada. Se dois objetos
são conectados com uma associação, esta associação pode ser substituída por uma
chave estrangeira no banco de dados. Colocado de forma simples, quando você sal-

var um álbum no banco de dados, você grava o ID do artista ao qual o álbum está as-

sociado no registro do álbum, como na Figura 12.1.

Esse é o caso simples. Um caso mais complicado surge quando você tem uma

coleção de objetos. Você não pode gravar uma coleção no banco de dados, então vo-

cê tem que inverter a direção da referência. Assim, se você tiver uma coleção de fai-

xas no álbum, você coloca a chave estrangeira do álbum no registro da faixa, como

nas Figuras 12.2 e 12.3. A complicação acontece quando você tem uma atualização.

Atualizações significam que faixas podem ser acrescentadas ou removidas da cole-

ção de um álbum. Como você pode saber quais alterações colocar no banco de da-

dos? Basicamente, você tem três opções: (1) excluir e inserir, (2) adicionar um ponteiro reverso e (3) diferenciar a coleção.

234

Parte ll e Os Parões

: Álbum

‘Artista

ID = 1234

ID = 5678

“Kind of Blue"

nome =

«tabela»

«tabela»

: linha de Álbuns

linha de Artistas

ID = 1234
idDoArtista
título
= "Kind= of5678Blue!

IDs 9678
' Davisa
nome
= "Miles

Figura 12.1

E

5

“Miles Davis"

Mapeando uma coleção para uma chave estrangeira.

: Álbum

:Fabxa

ID = 1234
titulo = "Kind of Blue”

ID = 3333
titulo = "So What"

«tabela»

«tabela»

: linha de Álbuns

linha de Artistas

ID‘tulo = 1234
titulo == "Kit"Kind of Blue’ 5

ID = 3333
D=:
1234
idDoAlbum
titulo
= "So =What"

Figura 12.2

Mapeando uma coleção para uma chave estrangeira.

Álbum

Faixa

título: String

* ~ | titulo: String

«tabela»
Álbuns

«tabela»
Faixas

ID: int
titulo: varchar

fale:

Figura 12.3

1

ID: int
A
titulo: varchar

idDoAlbum: int

Classes e tabelas para uma referência multivalorada.

Capíruto 12

e PADRÕES ESTRUTURAIS OBJETO-RELACIONAIS

235

Com a exclusão e a inserção, você exclui todas as faixas do banco de dados vin-

culadas ao álbum e, então, insere todas as correntemente no álbum. À primeira vista
isso parece um tanto espantoso, especialmente se você não tiver alterado nenhuma

faixa. Contudo, a lógica é fácil de implementar e, como tal, funciona muito bem com-

parada com as alternativas. A desvantagem é que você só pode fazer isso se as faixas

forem Mapeamentos Dependentes (256), o que significa que elas devem pertencer ao ál-

bum e não podem ser referenciadas de fora dele.

Adicionar um ponteiro reverso coloca um vínculo da faixa de volta para o ál-

bum, efetivamente tornando a associação bidirecional. Isso altera o modelo de obje-

tos, mas, por outro lado, você agora pode tratar a atualização usando a técnica sim-

ples para campos univalorados.

Se nenhuma dessas opções for atrativa, você pode fazer uma diferenciação.

Existem duas possibilidades aqui: diferenciar em relação ao estado corrente do ban-

no álbum, obviamente

foi removida. Qualquer coisa no álbum que não estiver no

disco é obviamente um novo item a ser acrescentado. Observe então a lógica da apli-

cação para decidir o que fazer com cada item.

Diferenciar em relação ao que você leu na primeira vez significa que você tem
que guardar o que você leu. Isso é melhor já que evita outra leitura do banco de dados. Você pode também ter de diferenciar em relação ao banco de dados se estiver

usando um Bloqueio Offline Otimista (392).

No caso geral, qualquer coisa que tenha sido adicionada à coleção precisa ser

primeiro verificada para ver se é um objeto novo. Você pode fazer isso vendo se ele
tem uma chave. Se não tiver, ele precisa ser adicionado ao banco de dados. Esse pas-

so é tornado muito mais simples com uma Unidade de Trabalho (187), porque, dessa

maneira, qualquer objeto novo será primeiro inserido automaticamente. Em qualquer caso, você então encontra a linha associada no banco de dados e atualiza sua

chave estrangeira para apontar para o álbum corrente.

Para a remoção, você tem que saber se a faixa foi movida para outro álbum, se
ela não tem um álbum ou se foi completamente excluída. Se ela tiver sido movida pa-

ra outro álbum, ela deve ser atualizada quando você atualizar esse outro álbum. Se

ela não tiver um álbum, você precisa colocar um null na chave estrangeira. Se a faixa

foi excluída, então ela deveria ser apagada. Tratar exclusões é muito mais fácil se o

vínculo reverso for obrigatório, como ele é aqui, onde toda faixa deve estar em um álbum. Dessa maneira, você não tem que se preocupar em detectar itens removidos da

coleção, já que eles serão atualizados quando você processar o álbum ao qual eles foram adicionados.

Se esse vínculo for imutável, significando que você não pode alterar o álbum de

uma faixa, então a adição sempre significa inserção, e a remoção sempre significa ex-

clusão. Isso torna as coisas ainda mais simples.

Uma das coisas com que deve-se tomar cuidado é a existência de ciclos nas suas

associações. Digamos que você precise carregar um pedido, que tem uma associação
com um cliente (0 qual você carrega). O cliente tem um conjunto de pagamentos (os
quais você carrega), e cada pagamento tem pedidos os quais ele está pagando, o que
poderia incluir o pedido original que você está tentando carregar. Por conseguinte,
você carrega o pedido (agora volte ao início deste parágrafo.)

Para evitar se perder em ciclos, você tem duas escolhas que, no final das contas,

resumem-se à forma como você cria seus objetos. Normalmente, é uma boa idéia pa-

2

ap ojuawead

co de dados ou diferenciar em relação ao que você leu na primeira vez. Diferenciar
em relação ao banco de dados envolve reler coleção do banco de dados e então compará-la com a coleção no álbum. Qualquer coisa no banco de dados que não estiver

236

Parte Il e Os PacrÕEs

ra um método de criação incluir dados que lhe darão um objeto completamente for-

mado. Se você fizer isso, precisará colocar a Carga Tardia (200) em pontos apropriados

para quebrar os ciclos. Se você perder algum, você terá um estouro de pilha, mas se

os seus testes forem bons o suficiente, você poderá gerenciar esse fardo.

A outra escolha é criar objetos vazios e imediatamente colocá-los em um Mapa

de Identidade (196). Dessa maneira, quando o seu ciclo voltar ao início, o objeto já es-

tará carregado, e o ciclo terminará. Os objetos que você cria não estão completamen-

te formados, mas deveriam estar ao final do procedimento de carga. Isso evita ter

que tomar decisões em casos especiais sobre o uso de Carga Tardia (200) apenas para

fazer uma carga correta.

Chave Estrangeira

Quando

Usá-lo
Um Mapeamento de Chave Estrangeira pode ser usado para quase todas as associações
entre classes. O caso mais comum em que não é possível usá-lo é com associação
muitos-para-muitos. As chaves estrangeiras são valores únicos, e a primeira forma

normal diz que você não pode armazenar diversas chaves estrangeiras em um único

campo. Em vez disso, você precisará usar um Mapeamento de Tabela Associativa (244).
Se você tem um campo do tipo coleção sem nenhum ponteiro reverso, você de-

verá considerar se o lado “muitos” da associação deve ser um Mapeamento Dependente (256). Se esse for o caso, isso pode simplificar o tratamento da coleção.

Se o objeto relacionado é um Objeto Valor (453), então você deveria usar um Va-

lor Embutido (261).
Exemplo:

Referência Univalorada (Java)

Este é o caso mais simples, em que um álbum tem uma única referência para um artista.
class Artista...

private String none;
public Artista

}

(Long ID, String nome)

(

super (ID) ;
this.none = none;

public String lerNome( ) {

}

return

nome;

public void gravarNome(String nome)

)

this.nowe = nome;

{

class Álbum...
private String título;
private Artista artista;
public Álbum (Long ID, String título, Artista artista)

super (ID) ;
this.titulo = titulo;
this.artista

= artista;

}
public String lerTítulo ( ) {

(

Capíruto 12

}

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

237

return título;

public void gravarTítulo (String titulo) {

}

this.titulo = titulo;

public Artista lerArtista () {
return

artista;

public void gravarArtista (Artista artista) {
this.artista

= artista;

A Figura 12.4 mostra como você pode carregar um álbum. Quando o mapeador

de um álbum é instruído a carregar um determinado álbum, ele consulta o banco de

dados e traz o conjunto resultante dessa pesquisa. Ele então procura no conjunto re-

sultante por campo chave estrangeira e descobre esse objeto. Ele agora pode criar o
álbum com os objetos apropriados encontrados. Se o objeto Artista já estava na memória, ele seria trazido do cache. Caso contrário, ele seria carregado do banco de dados da mesma maneira.
A operação de busca usa comportamento abstrato para manipular um Mapa de

Identidade (196).

class MapeadorDeÁlbum...

public Album buscar (Long id) {
return (Álbum) buscanbstrata(id) ;
}
protected String comandoDeBusca ( ) (

}

return "SELECT ID, titulo, idDoArtista FROM álbuns WHERE ID =

class MapeadorAbstrato....

abstract protected String comandoDeBusca ( ) ;

protected ObjetoDoDonínio buscaAbstrata (Long id) {

ObjetoDoDonínio resultado = (ObjetoDoDonínio) mapaCarregado.
get (id) ;
if (resultado != null) return resultado;
Preparedstatement dec = null;
Resultset rs = null;
try {
dec = DB.prepare (comandoDeBusca( ));

dec.setLong(1,

id.longValue( |);

rs = dec.executeguery( );
rs.next ( );
resultado = carregar (rs);
return resultado;
) catch (SQLException e) {
throw new ApplicationBxception (e);

}

) finally {DB.cleanUp(dec,

rs);}

private Map mapaCarregado = new HashMap(

|;

A operação de busca chama uma operação de carga para efetivamente carregar

os dados para o álbum.

238

Parte ll e Os Parões

class Mapeadorabstrato....
protected ObjetoDoDomínio carregar (ResultSet rs) throws SQLException {

Long id = new Long(rs.getLong(1)) ;
if (mapaCarregado. containsKey(id)) return (ObjetoDoDominio) mapaCarregado.get
(id) ;
ObjetoDoDominio resultado = fazerCarga

fazerRegistro (id, resultado);

return

(id, rs);

resultado;

protected void fazerRegistro (Long id, ObjetoDoDomínio resultado)

(

Assert isFalse (napaCarregado. containskey (id) );

}

mapaCarregado.put (id, resultado) ;

abstract protected ObjetoDoDominio fazerCarga (Long id, ResultSet rs) throws SQLException;
class MapeadorDeAlbum. ..
protected ObjetoDoDominio fazerCarga (Long id, ResultSet rs) throws SQLException {

String titulo = rs.getString(2) ;
long idDoartista = rs.getLong(3) ;

Artista artista = RegistroMapeador.artista(

).buscar(idDoArtista) ;

Álbum resultado = new Álbun(id, titulo, artista) ;
return resultado;

Para atualizar um álbum, o valor da chave estrangeira é extraído do objeto ar-

tista associado.

um Mapeador

de Album

buscar (1) |

Ses

um mapeador

de artista
|

select * from álbuns where ID = 1

a result set

>

conjunto resultante]
dos álbuns

ler id do artista

novo (um Artista)

Figura 12.4

Seqiiéncia para carregar um campo univalorado.

um Album

CapiTuLo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

239

class Mapeadorabstrato...

abstract public void atualizar (ObjetoDoDomínio arg) ;
class MapeadorDeÁlbum...

public void atualizar (ObjetoDoDominio arg) {
PreparedStatement dec = null;
try {
dec = DB. prepare |
"UPDATE álbuns SET titulo
idDoartista

? WHERE id = 2";

dec.setLong(3, arg.lerID( ).longValue( ));
Álbum álbum = (Álbum) arg;
dec.setString(1, álbum. lerTítulo( ));
dec.setLong(2, álbum.lerArtista( ).lerID( ).longvalue(

dec.execute( |;

|);

} catch (SQLException e) {

throw new ApplicationException(e) ;

} finally {

}
Exemplo:

cleantp (dec) ;

Busca Multitabelas (Java)

Embora seja conceitualmente claro executar uma pesquisa por tabela, muitas vezes
isso é ineficiente, já que SQL consiste de chamadas remotas, e estas chamadas são
lentas. Por conseguinte, frequentemente vale a pena encontrar maneiras de recupe-

rar informações de diversas tabelas em uma única pesquisa. Posso modificar o exem-

plo anterior para usar uma única consulta para obter as informações tanto do álbum
quanto do artista com uma única chamada SQL. A primeira alteração é a do SQL do
comando de busca.
class MapeadorDeAlbum

public Album buscar (Long id) {
return (Album) buscanbstrata(id) ;
}
protected String comandoDeBusca ( ) {

return "SELECT a.1D, a.titulo, a.idDoArtista, r.nome "+
* FROM álbuns a, artistas 1, " +
* WHERE ID = ? AND a.idDoArtista = r.1D";

A seguir uso um método de carga diferente que carrega tanto a informação do

álbum quanto a do artista.
class MapeadorDeÁlbum...

protected ObjetoDoDomínio fazerCarga

String título = rs.getString(2);
long idDoartista = rs.getLong
(3) ;

(Long id, ResultSet rs) throws SQLException (

MapeadorDeArtista mapeadorDeArtista = RegistroMapeador.artista(
Artista

artista;

if (mapeadorDeartista.est4Carregado(idDoartista) }

);

240

Parte ll e Os Parões

else

artista = mapeadorDeArtista.buscar (idDoArtista) ;
artista = carregarArtista(idDoartista,

rs);

Album resultado = new Album(id, título, artista);

}

return resultado;

private Artista carregarArtista (long id, ResultSet rs) throws SQLException {

String nome = rs.getString(4) ;
Artista resultado = new Artista (new Long(id), none) ;
RegistroMapeador.artista( ).registrar(resultado.lerID( ), resultado) ;
return resultado;

Há alguma animosidade envolvendo a decisão sobre onde colocar o método
que mapeia o resultado da consulta SQL para o objeto artista. Por um lado é melhor

colocá-lo no mapeador de artista, já que esta é a classe que normalmente carrega o artista. Por outro lado, o método de carga é intimamente associado ao SQL e, dessa ma-

neira, deveria ficar com a consulta SQL. Neste caso, votei na segunda opção.
Exemplo: Coleção de Referências (C#)

O caso da coleção de referências ocorre quando você tem um campo que constitui
uma coleção. Usarei aqui um exemplo de equipes e jogadores em que iremos supor

que não podemos tornar um jogador um Mapeamento Dependente (256) (Figura 12.5).
class Equipe...
public String None;

public IList Jogadores; (

get {return ArrayList .ReadOnly (dadosDosJogadores) ; }

}

set {dadosDosJogadores = new ArrayList (valor) ;}

public void AdicionarJogador (Jogador arg)

}

dadosDosJogadores Add (arg) ;

private

{

IList dadosDosJogadores = new ArrayList(

};

No banco de dados, isso será manipulado com o registro do jogador tendo uma

chave estrangeira para a equipe (Figura 12.6).
class MapeadorDeEquipe..
public Equipe Buscar (long ia) (

}

return (Equipe) BuscaAbstrata (id);

Equipe

Figura 12.5

Uma equipe com vários jogadores.

Jogador

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

«tabela»
Equipe
ID: long

Figura 12.6

241

«tabela»
Jogador
ID: long
idDaEquipe: long

Estrutura do banco de dados para uma equipe com vários jogadores.

class Mapeadorabstrato...
protected ObjetoDoDomínio BuscaAbstrata (long id) {

}

protected DataRow BuscarLinha (long id) {
String filtro = String.Format
("id = {0}", id);

DataRow [ ] resultados = tabela Select (filtro) ;
return (resultados. Length == 0) ? null: resultados
(0) ;

}

protected DataTable tabela {

get {return acd.Dados.Tables (NomeDaTabela] ; }

}
public ArmazenadorDoConjuntoDeDados acd;

abstract protected String NomeDaTabela (get;)

class MapeadorDeBquipe. ..
protected override String NomeDaTabela {
get {return "Equipes";}

}

Oarmazenador do conjunto de dados é uma classe que armazena 0 conjunto de

dados em uso, junto com os adaptadores necessários para atualizá-los no banco de
dados.

class ArmazenadorDoConjuntoDeDados. .
public DataSet Dados = new DataSet ( );
private Hashtable AdaptadoresDeDados = new Hashtable( );
Para esse exemplo, iremos supor que a classe já foi povoada por algumas con-

sultas apropriadas.

O método de busca chama um método de carga para efetivamente carregar os
dados no novo objeto.

class Hapeadorabstrato. .
protected ObjetoDoDonínio Carregar (DataRow linha) (
long id = (int) linha["id"] ;
if (mapaDeidentidade
[id] != null) return (ObjetoDoDoménio) mapaDeldentidade
[id] ;
else {

ObjetoDoDonánio resultado = CriarobjetoDoDonínio( );
resultado.1d = id;

ens]

Assert.True (id != ObjetoDoDomínio. PROCURADOR ID) ;
DataRow linha = BuscarLinha
(id) ;
return (linha == null) ? null: Carregar (Linha) ;

s

s

242

Parte ll e Os Parões
mapaDeIdentidade.Add

}

}

(resultado.
Id, resultado) ;

fazerCarga (resultado, linha);
return resultado;

abstract protected ObjetoDoDomínio CriarObjetoDoDominio ( );

private IDictionary mapaDeldentidade = new Hastable( );
abstract protected void fazerCarga (ObjetoDoDonínio obj, DataRow Linha) ;
class mapeadorDeBquipe...
protected override void fazerCarga (ObjetoDoDomínio obj, DataRow linha) {
Equipe equipe = (Equipe) obj;
equipe.Nome = (String) linha["nome"] ;

equipe. Jogadores = Registrolapeador.
Jogador. BuscarPorEquipe (equipe. Id) ;
Para trazer os jogadores, executo um método especializado de busca sobre o

mapeador de jogadores.

class MapeadorDedogador. .
public IList BuscarPorEquipe (long id) {
String filtro = String.Format ("idDaRquipe {0}", id);

DataRon[ | linhas = tabela.Select (filtro) ;
IList resultado = new ArrayList ( );

foreach (DataRow linha in linhas) (
}

resultado.
Add (Carregar (Linha));

return resultado;

Para a atualização, a equipe grava seus próprios dados e delega ao mapeador

de jogadores a gravação dos dados na tabela de jogadores.

class MapeadorAbstrato...
public virtual void Atualizar (ObjetoDoDonínio arg) (
Gravar (arg, BuscarLinha (arg. 1d));
}
abstract protected void Gravar

(ObjetoDoDomínio obj, DataRow linha);

class MapeadorDeEquipe...
protected override void Gravar (ObjetoDoDomínio obj, DataRow linha) (

Equipe equipe = (Equipe) obj;
linha['nome"] = equipe.Nome;
gravarJogadores (eguipe) ;

}
private void gravarJogadores (Equipe equipe) {
foreach (Jogador j in equipe.Jogadores) {
RegistroMapeador. Jogador AssociaçãoBquipe (j, equipe. Id);
}

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

243

class MapeadorDeJogador..

public void AssociaçãoEquipe (Jogador jogador, long idDarquipe) (
DataRow linha = BuscarLinha (jogador. 1d) ;
linha["idDaBquipe") = idDaEquipe;

O código de atualização torna-se muito mais simples pelo fato da associação do

jogador com a equipe ser obrigatória. Se movermos um jogador de uma equipe para
outra, desde que atualizemos ambas as equipes, não temos que fazer uma diferenciação complicada para lidar com os jogadores. Deixarei esse caso como exercício para

Er

o leitor.

8

s

244

Parte ll e Os Parões

Mapeamento de Tabela Associativa (Association Table Mapping)
Grava uma associação como uma tabela com chaves estrangeiras

para as tabelas que são vinculadas pela associação.

32

=Be

Empregado

-—————————
e 2]

«tabela»

«tabela»
po

Empregados

28

ID

E2

Habilidade

«tabela»
mi

habilidade-

cnpregades

IDdoEmpregado
IDdaHabilidade

Habilidades

ID

Os objetos podem manipular campos multivalorados com bastante facilidade usan-

do coleções como valores de campos. Os bancos de dados relacionais não têm essa

característica e são restritos apenas a campos univalorados. Quando você estiver ma-

peando uma associação um-para-muitos, pode manipulá-la usando um Mapeamento
de Chave Estrangeira (233), basicamente usando uma chave estrangeira para o lado
univalorado da associação. Contudo, uma associação muitos-para-muitos não pode
fazer isso porque não há lado univalorado para armazenar a chave estrangeira.

A resposta é a solução clássica que tem sido há décadas usada pela comunidade de dados relacionais: criar uma tabela extra para armazenar o relacionamento. De-

pois use um Mapeamento de Tabela Associativa para mapear o campo multivalorado

para essa tabela de vinculação.
Como Funciona

A idéia básica por trás do Mapeamento de Tabela Associativa é usar uma tabela de vi
culação para armazenar a associação. Essa tabela tem apenas os IDs das chaves es-

trangeiras para as duas tabelas que são vinculadas. Ela possui uma linha para cada

par de objetos associados.

A tabela de vinculação não tem um objeto correspondente em memória. A con-

sequência disso é que ela não tem ID. Sua chave primária é a composição das duas
chaves primárias das tabelas que são associadas.
Em termos simples, para carregar dados da tabela de vinculação, você executa

duas consultas. Considere carregar as habilidades de um empregado. Neste caso, pe-

lo menos conceitualmente, você executa consultas em duas etapas. A primeira etapa

consulta a tabela habi lidadesEmpregados para encontrar todas as linhas que referenciam

o empregado que você quer. A segunda etapa, para cada linha do conjunto resultante, encontra o objeto habilidade correspondente usando o ID relacionado.

Se toda a informaçãojá estiver em memória, este esquema funciona bem. Se não

estiver, ele pode ser terrivelmente custoso no que diz respeito às consultas, já que vo-

cê tem de executar uma consulta para cada habilidade na tabela associativa. Você po-

CapiTuLo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

245

de evitar este custo juntando a tabela de habilidades com a tabela associativa, o que

lhe permite obter todos os dados em uma única consulta, embora ao custo de tornar

o mapeamento um pouco mais complicado.

Atualizar os dados referentes à associação das tabelas envolve muitas das questões da atualização de campos multivalorados. Felizmente a questão é tornada muito mais simples já que você pode, de muitas maneiras, tratar a tabela associativa como um Mapeamento Dependente (256). Nenhuma outra tabela deve referenciar a tabela associativa, de modo que você pode livremente criar e destruir vínculos na medi-

da em que eles forem necessários.
Quando

Usá-lo
O caso canônico de Mapeamento de Tabela Associativa é uma associação muitos-paraO Mapeamento de Tabela Associativa também pode ser usada para qualquer outra forma de associação. Entretanto, devido ao fato de ele ser mais complexo que o

Mapeamento de Chave Estrangeira (233) e envolver uma junção (join) extra, ele, usual-

mente, não é a melhor escolha. Ainda assim, em dois casos, o Mapeamento de Tabela

Associativa é apropriado para uma associação mais simples. Ambos os casos envol-

vem bancos de dados em que você tem menos controle sobre o esquema. Às vezes,
você pode precisar associar duas tabelas existentes, mas não pode adicionar colunas a essas tabelas. Neste caso, você pode criar uma nova tabela e usar o Mapeamen-

to de Tabela Associativa. Outras vezes, um esquema existente usa uma tabela associativa, mesmo quando ela não é realmente necessária. Neste caso, freqiientemente é

mais fácil usar um Mapeamento de Tabela Associativa do que simplificar o esquema
do banco de dados.
Em um projeto de banco de dados relacional, você pode muitas vezes ter tabelas associativas que também carregam informação sobre o relacionamento. Um
exemplo é uma tabela associativa pessoa /companhia que também contenha informações sobre o emprego da pessoa na companhia. Neste caso, a tabela pessoa /companhia realmente corresponde a um verdadeiro objeto do domínio.

Exemplo: Empregados e Habilidades (C#)
Aqui está um exemplo simples usando o modelo do esboço. Temos uma classe Empregado com um conjunto de habilidades, cada uma das quais pode aparecer associada a mais de um empregado.

class Empregado...
public IList Habilidades (

get {return ArrayList .ReadOnly (dadosDasHabi
lidades) ;)

set {dadosDasHabilidades = new ArrayList (valor);}

}
dade arg) {
public void Adic: arHabilidade (Hal
dades .Add (arg) ;
dadosDastial
}
public void removerHabilidade (Habilidade arg) {
dadosDaskabilidades
Remove (arg) ;

}
private IList dadosDastabilidades = new ArrayList( );

ad

muitos, já que realmente não há alternativas para essa situação.

Ss

&

246

Parte ll e Os Parões

Para carregar um empregado do banco de dados, precisamos pegar as habilida-

des usando um mapeador de empregado. Cada classe mapeadora de empregados tem
um método de busca que cria um objeto empregado. Todos os mapeadores são subclasses da classe mapeadora abstrata que junta serviços comuns para os mapeadores.

class MapeadorDeEmpregado. ..
public Empregado Buscar (Long id) {

}

return

(Empregado)

BuscaAbstrata

(id);

class MapeadorAbstrato...
protected ObjetoDoDomínio Buscanbstrata

(long id) {

Assert isTrue (id != ObjetoDoDominio, PROCURADOR_ID) ;
DataRow linha = Buscarbinha (id);

}

return (linha == null) ? null: Carregar (inha) ;

protected DataRow BuscarLinha (long id) {

Associ

String filtro = String.Format
("id = {0}", id);

)

DataRow[ | resultados = tabela.Select (filtro) ;
return (resultados.Length == 0) ? null: resultados
[0] ;

protected DataTable tabela (

get {return acd.Dados
Tables [NomeDaTabela) ;}

}
public AmazenadorDoConjuntoDeDados acd;

abstract protected String NomeDaTabela {get;}

class MapeadorDeEmpregado. ..
protected override String NomeDaTabela

}

get {return "Empregados";}

{

Oarmazenador do conjunto de dados é um objeto simples que contém um con-

junto de dados ADO.NET e os adaptadores relevantes para gravar este conjunto no
banco de dados.

class ArmazenadorDoConjuntoDeDados....
public DataSet Dados = new DataSet ( );
private Hashtable AdaptadoresDeDados = new Hashtable( );

Para tornar este exemplo simples — na verdade, simplista — iremos supor que o

conjunto de dados já foi carregado com todos os dados de que precisamos.

O método de busca chama os métodos de carga para carregar os dados do empregado.

class Hapeadorabstrato...
protetced ObjetoDoDomínio Carregar

long id = (int) Linha["id"];
if

(mapaDeIdentidade
[id]

else {

(DataRow linha)

!= null return

(

(ObjetoDoDomínio)

ObjetoDoDonínio resultado = CriarobjetoDoDomínio( );

mapaDeIdentidade
[id] ;

Caíruto 12
resultado. Ic

}

}

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

247

id;

napaDeTdentidade Add (resultado.Tá, resultado) ;
fazerCarga (resultado, Linha);
return resultado;

abstract protected ObjetoDoDominio CriarobjetoDoDomínio(
private Dictionary mapaDeldentidade = new Hashtable(

abstract protected void fazerCarga

);

(ObjetoDoDomínio obj, DataRow linha) ;

class HapeadorDeBmpregado...
protected override void fazerCarga (ObjetoDoDonínio obj, DataRow Linha) (
Empregado emp = (Empregado) obj;
emp.Nome = (String) linha ["nome"] ;

carregarHabilidades (emp) ;

Carregar as habilidades é suficientemente complicado para justificar um método separado para realizar o trabalho.

class HapeadorDeimpregado...
private IList carregarHabilidades (Empregado emp) {

DataRow[ | linhas = linhasHabilidadesAssociadas (emp) ;
IList resultado = new ArrayList(

);

foreach (DataRow linha in linhas) {
long idDaHabilidade = (int) linha["idDaHabilidade"] ;
emp. AdicionarHlidade
abi (Regist roMapeador Habilidade. Buscar (idDatabilidade) | ;

}

return resultado;

}
private DataRow| ] linhasHabilidadesAssociadas (Empregado emp) (

String filtro = String.Format ("idDoBmpregado = {0}", emp. Id);

return tabelaHabilidadesassociadas.Select
(filtro) ;

}
private DataTable tabelaliabilidadesAssociadas (
get {return acd.Dados. Tables [*habi lidadessmpregados*] ;}
}
Para tratar as alterações nas informações sobre as habilidades, usamos um mé-

todo de atualização no mapeador abstrato.

class Hapeadorabstrato...
public virtual void Atualizar (ObjetoDoDomínio arg) (

}

1d)) ;
(arg.nha
Gravar (arg, Buscarki

abstract protected void Gravar

(ObjtetoDoDominio arg,

DataRow linha) ;

O método de atualização chama um método de gravação na subclasse.

class MapeadorDeBmpregado..
protected override void Gravar (ObjetoDoDominio obj, DataRow
Empregado emp = (Empregado) obj;

ha) (

&

248

Parte ll e Os Parões

linha["nome"] = emp.Nome;
gravarHabilidades (emp) ;

Mais uma vez, criei um método separado para gravar as habilidades.
class HapeadorDeEnpregado....
private void gravarHabilidades (Empregado emp) {
excluirHabilidades (emp) ;
foreach (Habilidade h in emp.Habilidades) {

DataRow linha = tabelaHabilidadesAssociadas.NewRow( } ;
linha ["idDoBmpregado"]

3
E
ss

28

Es
é

)

= emp.Id;

linha ["idDallabilidade"] = h.1d;
tabelatabi lidadesAssociadas. Rows. Add (Linha) ;

}
private void excluirtabilidades (Empregado emp) {

DataRow[ | linhasDasHabilidades = linhasHabilidadesAssociadas
(emp) ;

}

foreach (DataRow 1i in linhasDasHabilidades) 1i.Delete( );

A lógica aqui executa a ação simples de apagar todas as linhas existentes na ta-

bela associativa e criar novas linhas. Isso me poupa de ter que descobrir quais foram
adicionadas e quais foram excluídas.

Exemplo: Usando

SQL Direto (Java)

Uma das coisas boas de ADO.NET é que ele me permite discutir o básico de um ma-

peamento objeto-relacional sem me prender aos detalhes pegajosos relativos à minimização de consultas. Com outros esquemas de mapeamento relacional, você fica
mais próximo do SQLe tem que levar muito disso em conta.
Quando você está indo diretamente ao banco de dados, é importante minimizar

as consultas. Para a minha primeira versão, pegarei o empregado e todas as suas habilidades em duas consultas diferentes ao banco de dados. Isso é fácil de acompanhar, mas não é a melhor maneira de fazer, então, tenha paciência comigo.
Aqui está a DDL para as tabelas:
create table empregados

(ID int primary key,

prenome varchar,

sobrenome varchar)

create table habilidades (ID int primary key, none varchar)
create table empregadoabilidades (idDoBmpregado int, idDaHabilidade int, primary key
{idDoEmpregado,

idDaHabilidade))

Para carregar um único Empregado, seguirei uma abordagem semelhante à que

utilizei antes.

O mapeador de empregado define um envoltório simples para um mé-

todo abstrato de busca na Camada Supertipo (444).

class HapeadorDeEnpregado....
public Enpregado buscar (Long chave) (
return buscar (new Long(chave));

}
public Enpregado buscar (Long chave) {

Caíruto 12

}

return (Empregado) buscaAbstrata

protected String comandoDeBusca

}

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

249

(chave);

( | {

return
"SELECT " + LIST
DE COLUNAS
A +
” FROM empregados" +
" WHERE ID = 2";

public

static

final String

LISTA
DE COLUNAS

= " ID,

sobrenome,

prenome

";

class HapeadorAbstrato...
protected ObjetoDoDomínio buscaAbstrata

(Long id) (

ObjetoDoDominio resultado = (ObjetoDoDonínio) mapaCarregado.get
(id) ;
if (resultado != null) return resultado;
PreparedStatement dec = null;
ResultSet rs = null;
try {
dec = DB.prepare (comandoDeBusca( )) ;
dec. setLong(1, id.longvalue( }) ;
rs = dec.executeQuery( );
rs.next( );

resultado = carregar (rs);

retum resultado;
) catch (SQLException e) {
throw new ApplicationBrception(e) ;
) finally {DB.cleanUp(dec, rs) ;

)

)
abstract protected String conandoDeBusca ( ) ;
protected Map mapaCarregado = new Hashiap( );
Os métodos de busca chamam então os métodos de carga. Um método abstrato

de carga trata a carga do ID enquanto os dados reais do empregado são carregados
no mapeador de empregados.

class Hapeadorabstrato...
protected ObjetoDoDomínio carregar (ResultSet rs) throws SQLException {

}

Long id = new Long(rs.getLong(1)) ;
return carregar (id, rs);

public ObjetoDoDomínio carregar (Long id, ResultSet rs) throws SQLException {

if (foiCarregado(id)) return (ObjetoDoDonínio) mapaCarregado.get
(id) ;
ObjetoDoDominio resultado = fazerCarga(id, rs) ;
mapaCarregado.put (id, resultado);
return resultado;

}
abstract protected ObjetoDoDonínio fazerCarga (Long id, ResultSet rs) throws SQLException;
class MapeadorDeEmpregado. ..
protected ObjetoDoDominio fazerCarga

(Long id, ResultSet rs) throws SQLException {

Empregado resultado = new Bmpregado(id) ;

resultado.gravarPrenome (rs.getString ("prenome") ;

resultado. gravarSobrenome (rs.getString("sobrenone") ;

&

250

Parrell

e Os PADRÕES

resultado.gravar
lidades (carregarkabi
Habi idades (id)) ;
return resultado;

O empregado precisa executar outra consulta para carregar as habilidades, mas
ele pode facilmente carregar todas as habilidades em uma única consulta. Para fazer
isso, ele chama o mapeador de habilidades para carregar os dados de uma habilidade específica.

class MapeadorDeEmpregado...
protected List carregarHabilidades (Long idDoBmpregado)

PreparedStatement dec = null;

(

ResultSet rs = null;

try {

List resultado = new ArrayList ( |;
dec = DB.prepare (comandoDeBuscaDeHabi
lidades) ;

2

dec.setObject (1, idDoEnpregado) ;
rs =

dec.executeQuery(

);

while (rs.next()) {
Long idDaHabilidade = new Long(rs.getLong
(1)) ;
resultado.Add( (Habilidade) RegistroMapeador.habilidade| ) .carregarLinha

(idDaHabilidade, rs));

}
return resultado;

) catch (SQLException e) {

throw new ApplicationException(e) ;
} finally {DB.cleanUp(dec, rs);

)

y
private static final String comandoDeBuscaDeHabilidades =
"SELECT habilidade.ID, " + MapeadorDeHabilidade.LISTA_DE_COLINAS +
* FROM habilidades habilidade, empregadoHabilidades eh * +
"WHERE eh.idDoEnpregado = ? AND habilidade.ID = eh. idDaHabilidade”;

class MapeadorDeliabilidade....
public static final String LISTA
DE COLUNAS = " habilidade.none noneDaliabilidade *;
class Mapeadorabstrato. .
protected ObjetoDoDomínio carregarLinha (Long id, ResultSet rs) throws SQLException {
return carvegar (id, rs) ;
}
class MapeadorDeHabilidade...
protected ObjetoDoDominio fazerCarga

(Long id, ResultSet rs) throws SQLException (

Habilidade resultado = new Habilidade (id) ;
resultado. gravarNone (rs .getString ("nomeDalabilidade") );
return resultado;

O mapeador abstrato também pode ajudar a encontrar empregados.

class HapeadorDeEnpregado....
public List buscarTodos ( ) {

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

251

return buscarTodos (conandoBuscarTodos) ;

}
private static final String comandoBuscarTodos =
"SELECT * + LISTA
DE COLUNAS +
"

FROM empregados empregado" +

* ORDER BY empregado. sobrenome”;
class Mapeadorabstrato...
protected List buscarTodos

(String sql)

(

Preparedstatement dec = null;
Resultset rs = null;
try {
List resultado = new ArrayList ( );
dec = DB.prepare (sql) ;
1s
-executeQuery( );

while

(rs.next(

return

resultado;

))

resultado.
add (carregar (rs) );

) catch (SQLException e) {
throw new ApplicationException(e) ;
) finally (DB.cleantp(dec, rs);)

}

Tudo isso funciona muito bem e é muito simples de acompanhar. Ainda assim,
há um problema no número de pesquisas, e isso se deve ao fato de que cada empre-

gado gasta duas consultas SQL para ser carregado. Embora possamos carregar os da-

dos básicos de um empregado para muitos empregados em uma única consulta, ainda precisamos de uma consulta por empregado para carregar as habilidades. Assim,
carregar 100 empregados exige 101 consultas.

Exemplo: Usando uma Única Consulta para Vários Empregados (Java)

É possível trazer do banco vários empregados, juntamente com suas habilidades,

em uma única busca. Este é um bom exemplo de otimização de consultas em mais
de uma tabela, o que certamente é mais complicado. Por esse motivo, faça isso so-

mente quando precisar, em vez de em todas as vezes. É melhor colocar mais energia

em acelerar as suas buscas lentas do que em muitas consultas que sejam menos importantes.

O primeiro caso que investigaremos é um caso simples no qual trazemos do
banco todas as habilidades de um empregado na mesma consulta que traz os dados
básicos. Para fazer isso, usarei um comando SQL mais complexo que realiza a junção

das três tabelas.

class MapeadorDeBmpregado...
protected String comandoDeBusca ( ) (

return
"SELECT * + LISTA
DE COLUNAS +
* FROM empregados empregado, habilidades habilidade, empregadoliabilidades eh" +
* WHERE empregado.1D
idDoBmpregado AND habilidade.1D = eh.idDaHabilidade AND
enpregado.1D = 2";

}

Ss

&

252

Parte ll e Os Parões

public static final String LISTA
DE COLUNAS =

* empregado.ID, empregado. sobrenone, empregado. prenome, " +
* eh idDaHabilidade, eh.idDoBmpregado, * +
“habilidade.ID, habilidade none *;

Os métodos buscaAbstrata e carregar da superclasse são os mesmos do exemplo

anterior, de modo que não irei repeti-los aqui.

O mapeador de empregados carrega

seus dados de maneira diferente para tirar proveito das múltiplas linhas de dados.

Associ

eamento de Tabela

class HapeadorDeEnpregado....
protected ObjetoDoDominio fazerCarga (Long id, ResultSet rs) throws SQLException {
Empregado resultado = (Enpregado) carregarLinha (id, rs);
carregarDadosDasHabilidades (resultado, rs);
while (rs.next()) {
Assert . isTrue (aLinhafPara0MesmoEmpregado (id, rs)) ;

}

carregarDadosDasHabi
lidades (resultado, rs) ;

return resultado;

}
protected ObjetoDoDomínio carregarLinha (Long id, ResultSet rs) throws SQLException {
Enpregado resultado = new Enpregado(id) ;
resultado.gravarPrenone
(rs. getString ("prenome") ;
resultado. gravarSobrenone (rs.getString ("sobrenome") ;
return resultado;
}

private boolean alinhafParaOMesmoEmpregado (Long id, ResultSet rs) throws SQLException {
return id.equals(new Long(rs.getLong(1)));

}
private void carregarDadosDasHabilidades (Empregado pessoa, ResultSet rs) throws SQLException {
Long idDatiabilidade = new Long(rs.getLong("idDaHabilidade") ;
pessoa.adicionarHabilidade ((Habilidade) RegistroMapeador.habilidade( ) .carregarLinha
(idDaHabilidade, rs)) ;
}
Neste caso, o método de carga do mapeador de empregados, em verdade percorre o resto do conjunto resultante para carregar todos os dados.
Tudo é simples quando estamos carregando os dados de um único empregado.
Entretanto, o benefício real desta consulta em mais de uma tabela aparece quando
queremos carregar muitos empregados. Obter a leitura correta pode ser complicado,

especialmente quando não queremos forçar o agrupamento do conjunto resultante

por empregados. Neste ponto é útil introduzir uma classe auxiliar para percorrer o
conjunto resultante focando na própria tabela associativa, carregando os emprega-

dos e habilidades à medida que prossegue.

Começarei com o SQL e a chamada para a classe carregadora especial.

class MapeadorDeEmpregado..
public List buscarTodos ( ) (

return buscarTodos (conandoBuscarTodos) ;

}
private static final String conandoBuscarTodos =
"SELECT

" + LISTA
DE COLUNAS

+

* FROM empregados enpregado, habilidades habilidade, empregadoHabilidades eh" +

Caíruto 12

e PADRÕES ESTRUTURAIS OBJETO-RELACIONAIS

253

* WHERE empregado.ID = eh.idDoEnpregado AND habilidade.ID = eh.idDaHabilidade" +
* ORDER BY empregado. sobrenome";

protected List buscarTodos (String sql)

{

CarregadorDaTabelaAssociativa carregador = new CarregadorDaTabelaAssociativa
(this, new
AdicionadorDeHabilidade ( ));
return carregador. rodar (comandoBuscar Todos) ;
}
class CarregadorDaTabelanssociativa...
private MapeadorAbstrato mapeadorFonte;
private Adicionador adicionadorAlvo;
public CarregadorDaTabelaassociativa (NapeadorAbstrato mapeadorPrincipal, Adicionador
adicionadorAlvo) (
this.mapeadorFonte = mapeadorPrincipal;
this.adicionadorAlvo

= adicionadorAlvo;

Não se preocupe com o adicionadorDeHabi
lidade — mais tarde ele se tornará um

pouco mais claro. Por enquanto, perceba que construímos o carregador com uma referência ao mapeador e então o mandamos fazer uma carga com uma consulta apro-

priada. Essa é a estrutura típica de um objeto método. Um objeto método [Beck Pat-

terns] é uma maneira de transformar um método complicado em um objeto por si só.
A grande vantagem disso é que lhe permite colocar valores em campos em vez de
passá-los por parâmetros. A maneira comum de usar um objeto método é criá-lo, dispará-lo e então deixá-lo morrer assim que o seu trabalho estiver terminado.
O comportamento de carga se dá em três passos.

class CarregadorDaTabelanssociativa..
protected List rodar (String sql)

(

carregarDados (sql) ;
adicionarTodos0b}etosNovoshoapaDeldentidade( ) ;
return formarResultado;

O método carregarDados constrói a chamada SQL, executa-a e efetua um laço no

conjunto resultante. Já que este é um objeto método, coloquei o conjunto resultante
em um campo de modo que não o tenha de passar por parâmetro.

class CarregadorDaTabelanssociativa. .
private ResultSet rs = null;

private void carregarDados (String sql) (
PreparedStatement dec = null;
try {
dec = DB. prepare (sql);
rs =

dec.executeQuery( );

while (rs.next( })

carregarLinha( );

) catch (SQLException e) (

throw new ApplicationException(e);

} finally {DB.cleanUp(dec, rs);

}

254

Parte Il e Os PApROes

O método carregarLinha carrega os dados de uma única linha no conjunto resul-

tante. Ele é um pouco complicado.
class CarregadorDaTabelanssociativa.

private List idsResultantes = new ArrayList ( );

private Map emProgresso = new Hashllap( );
private void carregarLinha

| ) throws SQLException {

Long ID = new Long(rs.getLong(1)) ;
if (!idsResultantes.contains(ID)) idsResultantes.add(1D) ;
if (!mapeadorFonte.carregou(ID))

{

if (!emProgresso.keySet ( ) .contains (ID))

emBrogresso.
put (ID, mapeadorFonte.carregarLinha
(ID, 15));
adicionadorAlvo. adicionar ( (ObjetoDoDomínio) emprogresso.get
(ID), rs);

Associ

2

}
class MapeadorAbstrato....
boolean carregou(Long id) {
return mapaCarregado. containsKey (id) ;
}
O carregador preserva qualquer ordenação presente no conjunto resultante, de

modo que a lista de saída de empregados estará na mesma ordem inicial. Então,

mantenho uma lista de IDs na ordem em que os vejo. Assim que tiver o ID, vejo se ele
já está totalmente carregado no mapeador — normalmente por uma consulta anterior.
Caso não esteja, carrego os dados que tiver e guardo-os em uma lista “em progresso”. Preciso de tal lista já que diversas linhas serão combinadas para reunir todos os
dados do empregado e posso não chegar a essas linhas consecutivamente.

A parte mais delicada deste código é assegurar que posso adicionar a habilida-

de que estou carregando na lista de habilidades dos empregados e ainda assim man-

ter genérico o carregador de modo que ele não dependa de empregados e habilida-

des. Para alcançar isso, preciso procurar fundo no meu baú de truques para encontrar uma interface interna — o Adicionador.

class CarregadorDaTabelanssociativa...
public static interface Adicionador (

void adicionar (ObjetoDoDominio

hospedeiro, ResultSet rs) throws SQLException;

O solicitante original tem que fornecer uma implementação para a interface, pa-

ra especializá-la para as necessidades específicas do empregado e habilidade.

class HapeadorDeEnpregado....
private static class AdicionadorDeHiabilidade inplements CarregadorDatabelarssociativa Adicionador {
public void adicionar (ObjetoDoDonínio hospedeiro, ResultSet rs) throws SQLException {
Empregado emp = (Empregado) hospedeiro;

Long idDatiabilidade = new Long (rs.getLong ("idDaliabilidade"));
enp.adicionarHabilidade( (Habilidade) RegistroMapeador.habilidade( ) .carregarLinha

(idDaHabilidade,

}

}

rs));

Caíruto 12

e PADRÕES ESTRUTURAIS OBJETO-RELACIONAIS

255

Este é o tipo de coisa que fica mais natural em linguagens que possuem ponteiros para funções ou fechamentos (closures), mas pelo menos a classe e a interface con-

seguem executar o trabalho. (Elas não têm que ser internas neste caso, mas isso ajuda a enfatizar seu escopo estreito.)

Você pode ter percebido que tenho um método carregar e um método carregarLi-

nha definidos na superclasse e a implementação de carregarLinha é uma chamada ao

método carregar. Fiz isso porque existem ocasiões em que você quer ter certeza de que

uma ação de carga não avançará o conjunto resultante. A carga faz o que ela tiver de
fazer para carregar um objeto, mas carregarLinha garante a carga de dados de uma li-

nha sem alterar a posição do cursor. Na maior parte do tempo, esses dois métodos são
a mesma coisa, mas no caso deste mapeador de empregados, eles são diferentes.

Agora todos os dados vieram do conjunto resultante. Tenho duas coleções: uma

lista dos IDs de todos os empregados que estavam no conjunto resultante, na ordem
da sua aparição inicial e uma lista de novos objetos que ainda não haviam aparecido
no Mapa de Identidade (196) do mapeador de empregados.
O próximo passo é colocar todos os novos objetos no Mapa de Identidade (196).

class CarregadorDaTabelaAssociativa...

&

private void adicionar TodosObjetosNovoshoMapaDeIdentidade

() {

for (Iterator it = emProgresso.values( ) .iterator( ); it.hasNext( );)
mapeadorFonte, colocarComoCarregado( (ObjetoDoDomínio) it.next ( ));

}
class MapeadorAbstrato. .

void colocarComoCarregado

}

(ObjetoDoDominio obj)

mapaCarregado.put (obj.lerID( ), obj);

{

O passo final é montar a lista resultante procurando os IDs no mapeador.
class CarregadorDaTabelaAssociativa...
private List formarResultado( ) (
List resultado = new ArrayList ( );

for ( Iterator it = idsResultantes. iterator( ); it.hasNext( );) {
Long id = (Long) it.next( );
resultado.add (napeadorFonte. procurar
(id) ;

}

return resultado;

}
class Hapeadorabstrato...
protected ObjetoDoDomínio procurar (Long id) {

}

return (ObjetoDoDonínio) mapaCarregado.
get (id) ;

Tal código é mais complexo do que o código de carga usual, mas esse tipo de
coisa pode ajudar a diminuir o número de consultas. Já que é complicado, isso é algo
a ser usado com parcimônia, quando você tiver partes lentas de interação com o ban-

co de dados. Entretanto, é um exemplo de como o Mapeador de Dados (170) pode fornecer boas consultas sem que a camada do domínio tome conhecimento da complexidade envolvida.

256

Parte Il e Os PaprOes

Mapeamento

Dependente (Dependent Mapping)
Faz uma classe executar o mapeamento do banco de dados

para uma classe filha.

.
Album

{ordenado}

A1

/2

I1
1i
H
Mapeador
deAlbum

Faixa

* | titulo

ve

7

| is

Alguns objetos aparecem naturalmente no contexto de outros objetos. As faixas de
um álbum podem ser carregadas ou gravadas sempre que o álbum correspondente

for carregado ou gravado. Se elas não forem referenciadas por nenhuma outra tabe-

la no banco de dados, você pode simplificar o procedimento de mapeamento fazendo o mapeador do álbum executar também o mapeamento das faixas — ou seja, tratando este mapeamento como um mapeamento dependente.

Como Funciona
A idéia básica por trás do Mapeamento Dependente é que uma classe (a dependente)
depende de alguma outra (a proprietária) para a sua persistência no banco de dados.
Cada dependente deve ter uma e apenas uma proprietária.

Isso se manifesta em termos das classes que executam o mapeamento. Para o

Registro Ativo (165) e o Gateway de Linhas de Dados (158), a classe dependente não con-

terá qualquer código de mapeamento para o banco de dados. Seu código de mapea-

mento reside na proprietária. Com o Mapeador de Dados (170) não há um mapeador

para a dependente. O código de mapeamento reside no mapeador da proprietária.

Em um Gateway de Tabelas de Dados (151), tipicamente não haverá nenhuma classe dependente, todo tratamento da dependente é feito na proprietária.
Na maioria dos casos, cada vez que você carrega uma proprietária, você tam-

bém carrega as dependentes. Se for custoso carregar as dependentes e elas não são
usadas com freqiiéncia, você pode usar uma Carga Tardia (200) para evitar carregar as

dependentes até que você precise delas.

Uma propriedade importante de uma dependente é que ela não tem um Campo

Identidade (215) e, portanto, ela não é armazenada em um Mapa de Identidade (196).

Por conseguinte, ela não pode ser carregada por um método de busca que procure

um ID. De fato, não existe um método de busca para uma dependente, já que todas

as buscas são feitas com a proprietária.

A própria dependente pode ser a proprietária de outra dependente. Neste caso,

a proprietária da primeira dependente é também responsável pela persistência da se-

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

257

gunda dependente. Você pode ter toda uma hierarquia de dependentes controlada

por uma única classe proprietária.

Normalmente é mais fácil para a chave primária no banco de dados ser uma
chave composta que inclua a chave primária da proprietária. Nenhuma outra tabela
deveria ter uma chave estrangeira apontando para a tabela da dependente, a menos

que que esse objeto tenha a mesma classe proprietária. O resultado é que nenhum
objeto na memória além da proprietária ou suas dependentes deve ter uma referência para uma dependente. A rigor, você pode relaxar essa regra desde que a referência não seja persistida no banco de dados, no entanto, uma referência não persistente é por si mesma uma boa fonte de confusão.
Em um modelo UML, é apropriado usar composição para mostrar o relaciona-

mento entre uma proprietária e suas dependentes.

Já que a escrita e a gravação das dependentes são deixadas a cargo da proprie-

tária e não há referências externas, as atualizações nas dependentes podem ser tra-

a proprietária e então inserir novamente todas as dependentes. Isso evita que você

tenha de fazer uma análise dos objetos adicionados ou removidos da coleção da

proprietária.

As dependentes são, de muitas formas, parecidas com Objetos Valor (453), ainda

que, frequentemente, elas não precisem de todos os mecanismos que você usa para

tornar algo em um Objeto Valor (453) (tal como sobrescrever o método equals). A prin-

cipal diferença é que não há nelas, estritamente no que diz respeito à memória, nada

de especial. A natureza dependente dos objetos, em verdade, deve-se apenas ao comportamento de mapeamento para o banco de dados.

O uso do Mapeamento Dependente torna mais complicado descobrir se a proprie-

tária foi alterada. Qualquer alteração em uma dependente precisa marcar a proprie-

tária como alterada a fim de que a proprietária grave as alterações no banco de da-

dos. Você pode simplificar isso consideravelmente tornando a dependente imutável,
a fim de que qualquer alteração nela tenha de ser feita removendo-a e adicionando

uma nova. Isso pode tornar o modelo na memória mais difícil de trabalhar, mas sim-

plifica o mapeamento para o banco de dados. Embora, em teoria, o mapeamento em

memória e o mapeamento no banco de dados devessem ser independentes quando
você está usando um Mapeador de Dados (170), na prática você tem que, ocasionalmente, aderir a essa solução conciliatória.

Quando

Usa-lo
Vocé usa um Mapeamento Dependente quando vocé tem um objeto que é referenciado

apenas por um único objeto, o que normalmente ocorre quando um objeto tem uma

coleção de dependentes. O Mapeamento Dependente é uma boa forma de lidar com a
situação complicada em que a classe proprietária tem uma coleção de referências para as suas dependentes, mas não há um ponteiro reverso. Desde que os muitos objetos não precisem de suas próprias identidades, o uso do Mapeamento Dependente tor-

na mais fácil gerenciar sua persistência.
Para que o Mapeamento Dependente funcione, existem várias pré-condições que

devem ser satisfeitas.

* Uma classe dependente deve ter uma e apenas uma proprietária.

E

oquaweadeyy

tadas por meio de exclusões e inserções. Assim, se você quiser atualizar a coleção
de dependentes, você pode seguramente excluir todas as linhas que apontam para

258

Parte ll e Os PADRÕES

* Nenhum outro objeto, com exceção da classe proprietária, deve referenciar as

dependentes.

Há uma corrente de projeto OO que usa a noção de objetos entidades e objetos

dependentes ao projetar um Modelo de Domínio (126). Tendo a pensar no Mapeamento
Dependente como uma técnica para simplificar o mapeamento para o banco de dados
em vez de como uma parte essencial do projeto OO. Em particular, evito grandes
grafos de dependentes. O problema com eles é que é impossível referenciar uma de-

pendente de fora do grafo, o que muitas vezes leva a complexos esquemas de busca

baseados na proprietária raiz.

Não recomendo o Mapeamento Dependente se você estiver usando uma Unidade

de Trabalho (187). A estratégia de remoção e reinserção absolutamente não ajuda se

você tiver uma Unidade de Trabalho (187) cuidando das coisas. Também pode levar a

problemas, uma vez que a Unidade de Trabalho (187) não estará controlando as dependentes. Mike Rettig contou-me a respeito de uma aplicação em que uma Unidade de
Trabalho (187) cuidaria das linhas inseridas para teste e então as excluiria ao terminar.

Devido a ela não manter registro das dependentes, linhas órfãs apareceram e causa-

88

ram falhas nos testes.

Exemplo: Álbuns e Faixas (Java)
Neste modelo de domínio (Figura 12.7), um álbum mantém uma coleção de faixas.

Esta quase que inútil aplicação simples não precisa de mais nada para referenciar
uma faixa, de modo que é uma candidata óbvia para um Mapeamento Dependente.

(De fato, qualquer um pensaria que o exemplo foi deliberadamente criado para o

padrão.)

Esta faixa tem apenas o atributo título. Eu a defini como uma classe imutável.

class Faixa...

private final String título;
public Faixa (String título)

this.título = título;

(

}
public String lerTítulo () {
return titulo;

}

As faixas são armazenadas na classe álbum.

Álb um

fordenado)

Faixa

o—s>E

título

Figura 12.7

Dependente.

Um álbum com faixas que podem ser manipuladas usando um Mapeamento

Capitulo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

259

class Álbum...

private List faixas = new ArrayList( );
public void adicionarFaixa (Faixa arg) {
faixas.add (arg) ;
}
public void removerFaixa

Hi

(Faixa arg)

faixas.remove (arg);

{

public void renoverFaixa
(int i) (

}

faixas. remove (i) ;

public Faixa{ | lerFaixas ( ) {

}

return (Faixal ]) faixas.toArray
(new Faixa [faixas.size( )]);

A classe mapeadora de álbuns manipula todo o SQL para as faixas e, portanto,
define os comandos SQL que acessam a tabela de faixas.
class MapeadorDeÁlbum...
protected String comandoDeBusca

[ ) (

return

"SELECT ID, a.titulo, £.ti
as tituloDaFaixa"+
* FROM álbuns a, faixas £"%
” WHERE a.1D = ? AND £.idDoAlbum = a.1D" +
* ORDER BY É.seg";

As faixas são carregadas no álbum sempre que o álbum for carregado.
class MapeadorDeAlbum. . .

protected ObjetoDoDominio fazerCarga (Long id, ResultSet rs) throws SQLException {

}

String título = rs.getString(2) ;
Álbum resultado = new Álbun(id, título) ;
carregarFaixas (resultado, rs) ;
return resultado;

public void carregarFaixas

}

(Album arg, ResultSet rs) throws SQLException {

arg.adicionarFaixa (novaFaixa(rs))) ;
while (rs.next ( 1){
arg. adicionarPaixa (novaFaixa (rs) ) ;
}

private Faixa novaFaixa

(ResultSet rs) throws SQLException {

String titulo = rs.getString(3);

Faixa novaFaixa = new Faixa (título);
return

novaFaixa;

Para aumentar a clareza, executei a carga de faixas em uma consulta separada.

Por questões de desempenho, você poderia considerar carregá-las na mesma consul-

ta de acordo com a linha seguida no exemplo da página 239-240.

260

Parte Il e Os PaprOes

Quando o álbum é atualizado, todas as faixas são apagadas e reinseridas.
class MapeadorDeálbum...

public void atualizar (ObjetoDoDomínio arg) (
PreparedStatement comandoDeAtualização = null;

try {
comandoDeAtualização = DB.prepare ("UPDATE álbuns SET titulo
conandoDeAtualizagio.setLong(2, arg.lerID( } .longValue( ));
Álbum álbum = (Álbum) arg;
comandoDeAtualização. setString(1, álbum. lerTítulo( ));

? WHERE id = 2");

comandoDeAtualização .execute( );

atualizarPaixas (álbum) ;

) catch (SQLException e) (

throw new ApplicationException
(e);
} finally {DB.cleanUp(comandoDeAtualizacao) ;

}

)

public void atualizarFaixas

(Album arg) throws SQLException {

PreparedStatement comandoBxcluirFaixas = null;

8

try {

comandoBxcluirPaixas = DB.prepare ("DELETE FROM faixas WHERE idDoAlbum = 2") ;
comandoBxcluirFaixas.setLong(1, arg. lerID( }.longValue( ));

comandoExcluirFaixas.execute(

);

for (int i = 0; i < arg.lerFaixas( ) length; i++) {
Faixa faixa = arg. lerraixas( ) [i];

)

inserirFaixa(faixa, 141, arg);

) finally {DB.cleanUp(comandoBxcluirFaixas) ;

}

)

public void inserirFaixa (Paixa faixa, int seq, Álbum álbum) throws SQLException {

PreparedStatement conandoInserirFaixas = nul
try {
comandoInserirFaixas =

DB.prepare ("INSERT INTO faixas (seq, idDoAlbum, título) VALUES (?,

serirraixas.setInt(1, seg);
InserirFaixas.setLong(2, álbum.lerID( ) .longValue( });

comandoInserirFaixas.setString(3,

comandoInserirPaixas
execute ( );

faixa.lerTitulo( ));

) finally {DB.cleanUp (comandoInserirFaixas) ;

}

CapiTuLo 12

Valor Embutido

(Embedded

e PADRÕES ESTRUTURAIS OBJETO-RELACIONAIS

261

Value)

Mapeia um objeto em diversos campos da tabela de um outro objeto.
«tabela»

Emprego
ID

pessoa: pessoa
periodo: FaixaDeDatas
salério: Dinheiro

Empregos
1D: int

idDaPessoa: int
inicio: date
fim: date
ValorDoSalário:
moedaDoSalári

Muitos objetos pequenos que fazem sentido em um sistema OO não fazem sentido
como tabelas em um banco de dados. Exemplos incluem objetos dinheiro que conhecem sua moeda e faixas de datas. Embora o pensamento padrão seja gravar um objeto como uma tabela, nenhuma pessoa sensata iria querer uma tabela de valores mo-

netários.

Um Valor Embutido mapeia os valores de um objeto em campos do registro do

proprietário do objeto. No desenho temos um objeto emprego com referências para

um objeto faixa de datas e um objeto dinheiro. Na tabela resultante, os campos referentes àqueles objetos são mapeados para campos na tabela empregos em vez de eles
mesmos, criarem novos registros.

Como Funciona

Este exercício é, em verdade, muito simples. Quando o objeto proprietário (emprego)

é carregado ou gravado, os objetos dependentes (faixa de datas e dinheiro) são carregados e gravados ao mesmo tempo. As classes dependentes não terão seus próprios
métodos de persistência já que toda a persistência é feita pelo proprietário. Você po-

de pensar no Valor Embutido como um caso especial do Mapeamento Dependente (256),

em que o valor é um único objeto dependente.
Quando Usá-lo

Este é um dos padrões no qual a execução é bastante direta, mas saber quando usá-

lo é um pouco mais complicado.

Os casos mais simples de Valor Embutido são os Objetos Valor (453) claros e simples, como dinheiro e faixa de datas. Uma vez que os Objetos Valor (453) não têm
identidade, você pode criá-los e destruí-los facilmente sem se preocupar com coisas

como Mapas de Identidade (196) para mantê-los todos em sincronismo. De fato, todos

os Objetos Valor (453) deveriam ser persistidos como um Valor Embutido, já que você

nunca iria querer uma tabela para eles.

Uma questão nebulosa é se vale a pena armazenar objetos de referência, tal co-

mo um pedido e uma remessa, usando um Valor Embutido. A principal questão aqui

é se os dados da remessa têm alguma relevância fora do contexto do pedido. Uma

questão é a carga e a gravação. Se você só carregar os dados da remessa na memória

262

Parte Il e Os PaprOes

quando você carregar o pedido, este é um argumento para gravar ambos na mesma

tabela. Uma outra questão é se você vai querer acessar os dados da remessa separa-

damente por meio de SQL. Isso pode ser importante se estiver fazendo relatórios por

meio de SQL e não tiver um banco de dados separado para relatórios.

Se você estiver mapeando para um esquema existente, você pode usar um Va-

lor Embutido quando uma tabela contiver dados que você divide em mais de um ob-

jeto na memória. Isso pode ocorrer porque você quer um objeto separado para fatorar algum comportamento no modelo de objetos, mas, no banco de dados, ele ainda

é uma única entidade. Neste caso, você tem que tomar cuidado para que qualquer al-

teração nos dependentes marque o proprietário como sujo — o que não é um problema com Objetos Valor (453) que são substituídos no proprietário.

Na maioria dos casos, você somente usará um Valor Embutido em um objeto de

referência quando a associação entre eles tiver um único valor em ambas as extremidades (uma associação um-para-um). Ocasionalmente você pode usá-lo se houver

vários candidatos a dependentes e seu número for pequeno e fixo. Neste caso, você
terá campos numerados para cada valor. Isso é um projeto confuso de tabelas, e mui-

to ruim para consultar com SQL, mas pode trazer benefícios no que se refere ao de-

sempenho. Se, entretanto, este for o caso, o LOB Serializado (264) é normalmente a

melhor escolha.

Uma vez que muito da lógica para decidir quando usar um Valor Embutido é a
mesma para um LOB Serializado (264), existe a questão óbvia de escolher qual dos

dois usar. A grande vantagem do Valor Embutido é que ele permite que sejam feitas

consultas SQL nos valores do objeto dependente. Ainda que o uso de XML como técnica de serialização, junto com extensões do SQL que permitam consultas baseadas

em XML, possa alterar isso no futuro, no momento você realmente precisa de um Va-

lor Embutido se quiser usar valores dependentes em uma consulta. Isso pode ser im-

portante para mecanismos de relatórios separados no banco de dados.

Um Valor Embutido só pode ser usado para dependentes razoavelmente simples. Ele funciona bem para um único dependente solitário, ou para alguns poucos

dependentes separados. O LOB Serializado (264) funciona melhor com estruturas
mais complexas, incluindo subgrafos de objetos potencialmente grandes.

Leitura Adicional
O Valor Embutido, em sua história, tem sido chamado por vários nomes diferentes. O

TOPLink se refere a ele como mapeamento agregado. Visual Age se refere a ele como
compositor.

Exemplo: Objeto Valor Simples (Java)
Este é o exemplo clássico de um objeto valor mapeado com um Valor Embutido. Começaremos com uma classe simples de oferta de produto com os seguintes campos.
class OfertaDeProduto...

private Produto produto;
private Dinheiro custoBásico;
private Integer ID;

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

263

Nesse campos o ID é um Campo Identidade (215), e o produto é um mapeamento

de registros comum. Mapearemos o custo básico usando um Valor Embutido. Para
manter as coisas simples, faremos o mapeamento global com um Registro Ativo (165).

Uma vez que estamos usando um Registro Ativo (165), precisamos de procedi-

mentos de gravação e carga. Estes procedimentos simples estão na classe de oferta de
produtos porque ela é a proprietária. A classe dinheiro não tem nenhum comportamento de persistência. Aqui está o método de carga.
class OfertaDeProduto...
public static OfertaDeProduto carregar

(ResultSet rs) (

try {
Integer id = (Integer) rs.getObject ("1D") ;
BigDecimal valorCustoBásico

= rs.getBigDecimal ("valor
custo básico");

Currency moedaCustoBásico
Registro. lerMoeda (rs get string ("moeda
custo básico") |;

Dinheiro custoBásico = new Dinheiro (valorCustoBásico, moedaCustoBásico) ;
Integer idDoProduto = (Integer) rs.getObject ("produto") ;
Produto produto = Produto.buscar( (Integer) rs.getObject ("produto")) ;

return new OfertaDeProduto(id, produto, custoBásico) ;

} catch (SQLException e) {

}

throw new ApplicationException(e) ;

Aqui está o comportamento de atualização. Mais uma vez, é uma simples varia-

ção das atualizações.
class OfertaDeProduto...

public void atualizar ( ) (
PreparedStatement dec = null;
try {
dec = DB. prepare (stringDoComandoDeatual
izacao) ;
dec.setBigDecimal(1,

custoBásico.quantia( ));

dec. setString(2, custoBásico.moeda( | .código( ));
dec.setInt (3, ID.intValue( ));

dec.execute( );
) catch (Exception e) (

throw new ApplicationBxceptionte) ;

) finally (DB.cleantp
(dec) ;}

}
private String stringDoConandoDeatualização =
"UPDATE oferta produtos" +
* SET valor custo básico = , moeda custo básico = ? * +
"

WHERE

id = 2";

264

Parte Il e Os PaprOes

LOB Serializado (Serialized LOB)
Grava um grafo de objetos serializando-os em um tinico objeto grande
(LOB — Large OBject), o qual ele armazena em um campo do banco de dados.

hierarquia)
pai

Cliente

g

Departamento

filhos

LOB E

«tabela»
Clientes

departamentos:
BLOB

Os modelos de objetos muitas vezes contêm grafos complicados de pequenos objetos. A maior parte da informação nestas estruturas não está nos objetos, mas nas associações entre eles. Considere o armazenamento da hierarquia de organização de
todos os seus clientes. Um modelo de objetos muito naturalmente apresenta o pa-

drão composição para representar hierarquias organizacionais, e você pode facil-

mente adicionar métodos que lhe permitem obter ancestrais, irmãos, descendentes e

outros relacionamentos comuns.
Não é tão fácil colocar tudo isso em um esquema relacional. O esquema básico

é simples — uma tabela organização com uma chave estrangeira origem, entretanto,
sua manipulação do esquema requer muitas junções, o que é lento e complicado.

Os objetos não têm que ser persistidos como linhas de tabelas relacionadas

umas às outras. Outra forma de persistência é a serialização, onde todo um grafo de

objetos é gravado como um único objeto grande (LOB) em uma tabela. Este LOB Serializado então se torna uma forma de memento [Gang of Four].

Como Funciona
Há duas maneiras por meio das quais você pode executar a serialização: como um bi-

nario (BLOB) ou como caracteres texto (CLOB). O BLOB é, freqiientemente, o mais

simples de criar, uma vez que muitas plataformas incluem a habilidade de serializar
automaticamente um grafo de objetos. Gravar o grafo é uma simples questão de apli-

car a serialização em um buffer e gravar esse buffer no campo pertinente.

As vantagens do BLOB são que ele é simples de programar (se a sua plataforma
suportá-lo) e que ele usa o mínimo de espaço. As desvantagens são que o seu banco
de dados deve suportar um tipo binário de dados para ele e que você não pode reconstruir o gráfico sem o objeto, de modo que o campo é completamente impenetrá-

vel a uma passada de olhos. O problema mais sério, contudo, é com o controle de

versões. Se você alterar a classe departamento, você pode não conseguir ler todas as

Capitulo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

265

suas serializações anteriores. Uma vez que os dados podem residir no banco de da-

dos por um longo período, a quantidade de dados pode não ser pequena.

A alternativa é um CLOB. Neste caso, você serializa o grafo do departamento

em uma string de texto que carrega toda a informação de que você precisa. Esta string
pode ser lida facilmente por um humano vendo a linha, o que ajuda em uma passada de olhos pelo banco de dados. Todavia a abordagem do texto normalmente preci-

sará de mais espaço, e você pode precisar criar seu próprio analisador (parser) para o
formato textual que você estiver usando. Também é provável que ele seja mais lento
do que uma serialização binária.

Muitas das desvantagens dos CLOBs podem ser superadas com o uso de XML.

Vários analisadores XML estão disponíveis como software livre, de modo que você

não precisa escrever o seu próprio. Além disso, XML é um padrão amplamente suportado, de modo que, para fazer manipulações adicionais, você pode tirar proveito
das ferramentas à medida que elas se tornam disponíveis. A desvantagem que o
XML não resolve é a questão do espaço. De fato, ele torna a questão do espaço mui-

to pior porque ele é um formato bastante prolixo. Uma maneira de lidar com isso é
usar um XML zipado como o seu BLOB — você perde a legibilidade humana direta,

mas é uma opção, se o espaço for realmente um problema.

Quando você usa um LOB Serializado, cuidado com problemas de identidade.

Digamos que você queira usar um LOB Serializado para armazenar os detalhes do

cliente em um pedido. Para isso, não coloque o LOB do cliente na tabela de pedidos,
caso contrário os dados do cliente serão copiados a cada pedido, o que torna a atua-

lização um problema. (Isso pode ser uma coisa boa, entretanto, se você quiser arma-

zenar um instantâneo dos dados do cliente como eles eram no instante da colocação

do pedido — isso evita relacionamentos temporais.) Se você quiser que os dados do
seu cliente sejam atualizados em cada pedido no sentido relacional clássico, precisa

colocar o LOB em uma tabela de clientes de modo que muitos pedidos podem ser associados a ela. Não há nada de errado com uma tabela que tenha apenas um ID e um
único campo LOB como seus dados.
De um modo geral, tenha cuidado com a duplicação de dados ao usar este padrão. Frequentemente, não é todo um LOB Serializado que é duplicado, mas parte de

um que se sobrepõe com parte de outro. O que se tem a fazer é prestar muita atenção

aos dados que são armazenados no LOB Serializado e estar certo de que eles não pos-

sam ser alcançados de nenhum outro lugar, mas apenas de um único objeto que atua
como o proprietário do LOB Serializado.

Quando Usá-lo

O LOB Serializado não é considerado tão frequentemente quanto poderia. XML o torna muito mais atrativo, uma vez que ele fornece uma abordagem textual fácil de im-

plementar. Sua principal desvantagem é que você não pode consultar a estrutura

usando SQL. Algumas extensões SQL que têm surgido se propõem a alcançar os dados XML dentro de um campo, mas ainda não é a mesma coisa (ou portável).
Este padrão funciona melhor quando você pode extrair um pedaço do modelo

de objetos e usá-lo para representar o LOB. Pense em um LOB como uma maneira de
pegar um grupo de objetos que provavelmente não serão pesquisados de fora da

aplicação usando SQL. Este grafo pode então ser enganchado no esquema SQL.

Um LOB Serializado tem um desempenho sofrível quando objetos fora do LOB
referenciam objetos enterrados dentro dele. Para lidar com esse caso você tem que

descobrir algum tipo de esquema de referências que suporte referências a objetos

266

Parte Il e Os PacrÕEs

dentro de um LOB — não é de forma alguma impossível, mas é complicado o sufi-

ciente para geralmente não valer a pena. Mais uma vez XML, ou mais exatamente
XPath, reduz um pouco esta complicação.

Se você estiver usando um banco de dados separado para os relatórios, e todas

as outras consultas SQL forem executadas sobre esse banco de dados, você pode

transformar o LOB em uma estrutura de tabela apropriada. O fato de que um banco

de dados para relatórios é normalmente não-normalizado significa que as estruturas
apropriadas para um LOB Serializado são frequentemente apropriadas também para

um banco de dados separado para relatórios.

izado

Exemplo: Serializando uma Hierarquia de Departamentos em XML (Java)

8

Para este exemplo, pegaremos a noção de clientes e departamentos do desenho e
mostraremos como você poderia serializar todos os departamentos em um CLOB
XML. No momento que escrevo isto, a manipulação Java de XML é um tanto primitiva e volátil, de modo que, quando você estiver lendo este código, ele poderá pare-

cer um pouco diferente daquilo a que você está habituado (também estou usando
uma versão antiga de JDOM).

O modelo de objetos do esboço dá origem às seguintes estruturas de classe:

class Cliente...

private String nome;

private List departamentos = new ArrayList ( |;
class Departamento...

private String nome;

private List subsidiárias = new ArrayList ( );

O banco de dados para isso tem apenas uma tabela.
create table clientes (ID int primary key, none varchar, departamentos varchar)
Trataremos o cliente como um Registro Ativo (165) e ilustraremos a gravação dos
dados com o comportamento de inserção.
class Cliente...
public Long inserir () {

PreparedStatement comandoDeInserção = null;
try {
comandoDeInserção = DB. prepare (stringDoConandoDeInser¢ao) ;
gravarID (encontrar0PróximoIDdoBancoDeDados(
comandoDeInserção.setInt
(1, lerID(

).intValue(

}) ;

));

comandoDeInserção.setString(2, none) ;
comandoDeInserção.setString(3, ImlStringer write (departamentosParaBlementokml ( )));
comandoDe Inserção. execute ( );

Registro. adicionarCliente (ti

return lerID( );
) catch (SQLException e) {

);

throw new ApplicationException(e);
) finally {DB.cleanUp (comandoDeInser¢ao) ;

)

Capíruto 12

}

public Element departamentosParaElementokml

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

267

( ) {

Element raiz = new Element (*ListaDeDepartanentos") ;
Iterator i = departamentos. iterator( |;
while (i.hasNext()) (

Departamento dep = (Departamento)

i.next(

raiz addContent (dep, paraBlementokml ( )) ;

}

);

}
return raiz;

class Departamento...
Element paraBlementoxml

( ) {

Elemento raiz = new Element ("Departamento") ;

raiz.setattribute("nome", nome) ;
Iterator i = subsidiérias.iterator( );

while (i.hasNext( )) {

Departamento dep = (Departamento)

)

i.next(

vaiz.addContent (dep.paraBlementoXml ( }) ;

);

return raiz;
O cliente tem um método para serializar seu campo departamentos em um úni-

co DOM XML. Cada departamento tem um método para serializar a si próprio (e recursivamente às suas subsidiárias) também em um DOM. O método de inserção en-

tão pega o DOM dos departamentos, converte-o em uma string (por meio de uma

classe utilitária) e a coloca no banco de dados. Não estamos particularmente preocu-

pados com a estrutura da string. Ela é legível para humanos, mas não iremos olhar
para ela regularmente.

<?xml version="1.0" encoding: "UTF-8"?>
<listaDeDepartanentos>
<departamento nome="US">
<departamento nome="New England" >

<departamento nome="Boston /">
<departamento nome="Vermont /">

</departamento>
«departamento nome="Califomia /">
«departamento nome="Mid-West /">

</departamento>
<departamento none="Europe" />
</listaDeDepartamentos>

Ler os dados de volta é razoavelmente simples. Basicamente é o reverso desse

processo.

class Cliente...
public static Cliente carregar

(ResultSet rs) throws SQLException (

Long id = new Long(rs.getLong("id")) ;
Cliente resultado = (Cliente) Registro. lerCliente (id) ;
if (resultado != null) return resultado;
String none = rs.getString ("none") ;

E

268

Parte ll e Os PADRÕES

String lobDepartamento = rs.getString ("departamentos") ;

resultado = new Cliente (nome) ;
resultado. lerDepartamentos (ImlStringer . read (lobDepartamento) ) ;

}

return resultado;

void lerDepartamentos (Element fonte) {
List resultado = new ArrayList ( );

Iterator it = fonte.getChildren ("departamento") .iterator{
while (it.hasNext ( ))

}

adicionarDepartamento (Departamento. lerXml ( (Elemento)

);

it.next(

)));

class Departamento...
static Departamento lerXml

[Element fonte)

{

String nome = fonte.getAttributeValue ("nome") ;
Departamento resultado = new Departamento (nome) ;
Iterator it = fonte.getChildren

while (it.hasNext ( ))

("departamento") .iterator(

);

resultado .adicionarSubsidiária
(lerXml ( (Element) it.next(
return resultado;

)));

O código de carga é obviamente uma imagem espelhada do código de inserção.

O departamento sabe como criar a si próprio (e suas subsidiárias) a partir de um elemento XML, e o cliente sabe como pegar em elemento XML e criar a lista de departa-

mentos a partir dele. O método de carga usa uma classe utilitária para transformar a
string do banco de dados em um elemento utilitário.

Um perigo óbvio aqui é que alguém pode tentar editar à mão o XML no banco

de dados e estragar o XML, tornando-o ilegível para o procedimento de leitura. Ferramentas mais sofisticadas que suportassem a adição de um campo contendo um

DTD ou esquema XML com o propósito de validação obviamente ajudaria.

CapiTuLo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

269

Herança de Tabela Única (Single Table Inheritance)
Representa uma hierarquia de herança de classes como uma única tabela que

tem colunas para todos os campos das diversas classes da hierarquia.
Jogadores
nome

«tabela»

Jogadores

Jogador de Futebol

Jogador de Criquete|

clube

média de rebatidas

nome

clube
média de rebatidas|

média de pontos

tipo

Jogador de Boliche
média de pontos
Os bancos de dados relacionais não suportam herança, então ao mapear de objetos para os bancos de dados, temos que considerar como representar nossas belas estruturas
de herança como tabelas relacionais. Ao mapear para um banco de dados relacional,

tentamos minimizar as junções (joins) que podem crescer rapidamente ao processar

uma estrutura de herança em diversas tabelas. A Herança de Tabela Única mapeia to-

dos os campos de todas as classes de uma estrutura de herança em uma única tabela.

Como Funciona
Neste esquema de mapeamento de herança, temos uma única tabela que contém todos os dados de todas as classes da hierarquia de herança. Cada classe armazena os
dados que sejam relevantes para ela em uma única linha da tabela. Quaisquer colunas
no banco de dados que não sejam relevantes são deixadas em branco. O comportamento básico de mapeamento segue o esquema geral dos Mapeadores de Herança (291).
Ao carregar um objeto na memória, você precisa saber qual classe instanciar.

Para isso você tem um campo na tabela que indica qual classe deveria ser usada. Es-

te pode ser o nome da classe ou um campo com um código. Um campo codificado
precisa ser interpretado por algum código para mapeá-lo para a classe pertinente. Es-

te código precisa ser estendido quando uma nova classe é adicionada à hierarquia. Se
você embutir o nome da classe na tabela, você pode usá-lo diretamente para instanciar uma instância da classe. O nome da classe, entretanto, demandará mais espaço e

pode ser menos fácil de ser processado por aqueles usando diretamente a estrutura
da tabela do banco de dados. Além disso, o nome da classe pode acoplar mair fortemente a estrutura da classe ao esquema do banco de dados.
Ao carregar os dados, você primeiro lê o código para descobrir qual subclas-

se instanciar. Ao salvar os dados, o código precisa ser gravado pela superclasse na
hierarquia.

E

270

Parte ll e Os Parões

Quando

Usá-la

A Herança de Tabela Única é uma das opções para mapear os campos de uma hierarquia de herança para um banco de dados relacional. As alternativas são a Herança de
Tabela de Classes (276) e a Herança de Tabela Concreta (283).

Estes são os pontos fortes da Herança de Tabela Única:
* Só há uma única tabela com a qual se preocupar no banco de dados.
e Não há junções na recuperação dos dados.

© Qualquer refatoração que mova campos para cima ou para baixo na hierarquia não requer que você altere o banco de dados.

Os pontos fracos da Herança de Tabela Única são:
B
Ê

* Algumas vezes os campos são relevantes, algumas vezes não, o que pode ser
confuso para as pessoas usando diretamente as tabelas.
* As colunas usadas apenas por algumas subclasses levam a um desperdício

de espaço no banco de dados. O quanto isso é realmente um problema depende das características específicas dos dados e o quão bem o banco de dados comprime as colunas vazias. O Oracle, por exemplo, é muito eficiente em
eliminar espaço desperdiçado, especialmente se você mantiver suas colunas

opcionais no lado direito da tabela do banco de dados. Cada banco de dados
tem os seus próprios artifícios para fazer isso.

* Atabela única pode acabar ficando grande demais, com muitos índices e com
bloqueios freqiientes, o que pode prejudicar o desempenho. Você pode evitar
isso tendo tabelas de índices separadas que listem as chaves das linhas que

tenham uma certa propriedade ou que copiem um subconjunto de campos
relevantes para um índice.

* Você tem um único espaço de nomes (namespace) para os campos, de modo

que você tem de estar certo de que não usa o mesmo nome para diferentes

campos na hierarquia. Nomes compostos, com o nome da classe como prefixo ou sufixo, ajudam aqui.
Lembre-se de que você não precisa usar uma única forma de mapeamento de
herança para toda a sua hierarquia. É perfeitamente correto mapear meia dúzia de
classes similares em uma única tabela, desde que você use a Herança de Tabela Con-

creta (283) para quaisquer classes que tenham uma quantidade grande de dados es-

pecíficos.

Exemplo: Uma Tabela Única para Jogadores (C#)
Como nos outros exemplos de herança, baseei este aqui nos Mapeadores de Herança

(291), usando as classes da Figura 12.8. Cada mapeador precisa ser associado a uma
tabela de dados em um conjunto de dados ADO.NET. Esta associação pode ser feita

genericamente na superclasse mapeadora. A propriedade dos dados do gateway é um

conjunto de dados que pode ser carregado por uma consulta.

CapiTuLo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

271

class Mapeador...
protected DataTable tabela {

}

get {return Gateway.Data.Tables [NomeDaTabela] ; }

protected Gateway Gateway;

abstract protected String NomeDaTabela (get;)

Uma vez que só há uma única tabela, esta pode ser definida pelo mapeador de

jogador abstrato.

class HapadorDeJogadorAbstrato. ..
protected override String NomeDaTabela (

}

get {return "Jogadores" ;)

E

Mapeador

Mapeador Abstrato

+inserir(Objeto do Domínio)

de Jogador

+atualizar(Objeto do Domínio)
+excluir(Objeto do Domínio)

#gravar

--métodos gancho

tee

#gravar(Objeto do Domínio, Linha)

#carregar(Objeto do Dominio, Linha)
Mapeador de
Jogador de Futebol
1

Mapeador de Jogador

+ buscar (chave): Jogador
>| de Futebol
#gravar
#carregar

+ buscar (chave): Jogador je}
+ inserir
+ atualizar

1

[>>>]

Figura 12.8

Mapeador de
Jogador de Criquete

Jogador
de+ buscar
seat (chave):
2

| agravar

carregar

Mapeador de
Jogador de Boliche
1 | + buscar (chave): Jogador
de Boliche
#gravar
#carregar

O diagrama de classes genérico dos Mapeadores de Herança (291).

272

Partell

e Os Parões

Cada classe precisa de um código de tipo para auxiliar o código do mapeador a

descobrir com que tipo de jogador ele está lidando. O código do tipo é definido na
superclasse e implementado nas subclasses.
class MapeadorAbstrat oDeJogador..
abstract public String CédigoDoTipo {get;}

class HapeadorDeJogadorDeCríguete. .
public const String CÓDIGO
DO TIPO = "C";
public override String CédigoDoTipo (

}

get {return CÓDIGO
DO TIPO; }

O mapeador de jogador tem campos para cada uma das trés classes mapeadoras concretas.

B
Ê

class MapeadorDedogador. ..
private MapeadorDeJogadorDeBoliche mapeadorB;

private NapeadorDeJogadorDeCriquete mapeadorC;

private MapeadorDeJogadorDeFutebol mapeadorF;

public private MapeadorDeJogador (Gateway gateway): base (gateway) {

mapeadorB = new MapeadorDeJogadorDeBoliche (Gateway) ;
mapeadorC = new NapeadorDeJogadorDeCriquete (Gateway) ;
mapeadorF = new NapeadorDeJogadorDeFutebol (Gateway) ;

Carregando um Objeto do Banco de Dados
Cada classe mapeadora concreta tem um método de busca que traz um objeto dos
dados.

class MapeadorDeJogadorDeCriquete. ..
public JogadorDeCriquete Buscar (long id) {

}

return (JogadorDeCriquete) BuscaAbstrata (id);

Isso invoca um comportamento genérico para buscar um objeto.

class Mapeador...
protected ObjetoDoDomínio Buscabstrata

DataRow linha = Buscarbinha (id);

}

(long id) {

return (linha == null) ? null: Buscar (Linha) ;

protected DataRow BuscarLinha

(long id) {

String filtro = String.Format
("id = {0}", id);
DataRow[ | resultados = tabela.Select (filtro);

}

return (resultados.Length == 0) ? null: resultados[0] ;

public ObjetoDoDominio Buscar (DataRow linha) {

ObjetoDoDonínio resultado = CriarobjetoDoDomínio ( );
Carregar (resultado, linha) ;

CapiTuLo 12

}

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

273

return resultado;

abstract protected ObjetoDoDominio CriarobjetoDoDomínio( ) ;
class MapeadorDedogadorDeCriquete. ..
protected override ObjetoDoDominio CriarObjetoDoDominio

}

return new JogadorDeCriquete( };

{ ) {

Carrego os dados no novo objeto com uma série de métodos de carga, um em
cada classe na hierarquia.

class MapeadorDedogadorDeCriquete. ..
protected override void Carregar (ObjetoDoDonínio obj, DataRow linha) {
base.Carregar (obj, linha);
JogadorDeCriquete jogadorDeCriquete = (JogadorDeCriquete) obj;
jogadorDeCriquete.nédiaDeRebat
idas = (double) linha ['médiaDeRebatidas") ;

}
class MapeadorAbstratoDeJogador..
protected override void Carregar (ObjetoDoDonínio obj, DataRow linha) (
base.Carregar (obj, linha);

Jogador jogador = (Jogador) obj;
jogador.nome = (String) linha [*nome"] ;

}
class Hapeador...

protected virtual void Carregar (ObjetoDoDomínio obj, DataRow linha}

}

obj.Id = (int) Linha["id"];

{

Também posso carregar um jogador por meio do mapeador de jogador. Ele preci-

sa ler os dados e usar o código de tipo para determinar qual mapeador concreto usar.

class MapeadorDeJogador
public Jogador Buscar

(long chave)

(

DataRow linha = BuscarLinha (chave);

if (linha == null) return null;

else {

String cédigoDeTipo = (String) linha[*tipo"] ;
switch (códigoDeTipo)

(

case MapeadorDeJogadorDeBoliche.CÓDICO
DE TIPO:

return (Jogador) mapeadorB.
Buscar (Linha) ;

case MapeadorDeJogadorDeCríquete. CÓDIGO
DE TIPO:

return (Jogador) mapeadorC.
Buscar (Linha) ;
case NapeadorDeJogadorDeFutebol.CÓDICO
DE TIPO:
return (Jogador) mapeadorF.
Buscar (Linha) ;
default:
throw new Exception ("tipo desconhecido") ;

E

274

Parte ll e Os Panrões

Atualizando um Objeto A operação básica para a atualização é a mesma para todos

os objetos, de modo que posso defini-la na superclasse mapeadora.
class Hapeador...
public virtual void Atualizar

)

(ObjetoDoDomínio arg)

(

Gravar (arg, Buscarkinha (arg. id)) ;

O método de gravação é similar ao método de carga — cada classe o define para

gravar os dados que contém.

class HapeadorDeJogadorDeCríguete. ..
protected override void Gravar

B

}

(ObjetoDoDominio obj, DataRow linha)

(

(ObjetoDoDominio obj, DataRow linha)

{

base Gravar (obj, inha) ;
JogadorDeCriquete jogadorDeCriquete = (JogadorDeCriquete) obj;
linha ["médiaDeRebatidas"] = jogadorDeCriquete médiaDeRebatidas;

class MapeadorabstratoDeJogador...
Ed

protected override void Gravar

Jogador jogador = (Jogador) obj;
linha ["nome"] = jogador.none;
linha["tipo"] = CédigoDoTipo;

O mapeador de jogadores transfere para o mapeador concreto apropriado.
class MapeadorDedogador. .
public override void Atualizar (ObjetoDoDomínio obj) {
MapeadorPara
(obj) .Atualizar (obj) ;
}
private Mapeador MapeadorPara (ObjetoDoDomínio obj) {

if (obj is JogadorDeFutebol)
return mapeadorF;
if (obj is JogadorDeBoliche)
return mapeadorB;

if (obj is JogadorDeCríquete)
return mapeadorC;
throw new Exception ("Nenhum mapeador disponível");

Inserindo um Objeto As inserções são semelhantes às atualizações. A única diferença real é que uma nova linha precisa ser criada na tabela antes da gravação.

class Mapeador...
public virtual long Inserir (ObjetoDoDomínio arg)

DataRow linha = tabela.NewRow( );
arg.Id = LerPróximo)
Linha["id"] = arg. 1
Gravar (arg, linha);

(

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

275

tabela.Rows.Add (Linha) ;
return arg. Td;
class mapeadorDeJogador...
public override long Inserir (ObjetoDoDominio obj)

return MapeadorPara (obj) . Inserir(obj) ;

{

Excluindo um Objeto As exclusões são bastante simples. Elas são definidas no nível

do mapeador abstrato ou no envoltório do jogador.

class Hapeador...
public virtual void Excluir (ObjetoDoDoninio obj) {
DataRow

}

linha = BuscarLinha

linha.Delete( );

(obj.Id);

class MapeadorDedogador...
public override void Excluir (ObjetoDoDonínio obj) {

}

MapeadorPara (obj) .Excluir (obj) ;

5

276

Parte Il e Os PaprOes

Herança de Tabela de Classes (Class Table Inheritance)
Representa uma hierarquia de herança de classes

com uma tabela para cada classe.

Jogador

«tabela»
JogadoresDeFutebol
clube

nome

E

de Classes

Jogador de Futebol
clube

Jogador de Criquete
média de rebatidas

Jogador de Boliche

2

«tabela»
JogadoresDeCriquete|
média de rebatidas
«tabela»
JogadoresDeBoliche
média de pontos

média de pontos

«tabela»
Jogadores
nome

Um aspecto muito visível da incompatibilidade objeto-relacional é o fato de que os
bancos de dados relacionais não suportam herança. Você quer estruturas de bancos

de dados que mapeiem claramente para os objetos e que permitam associações em
qualquer lugar da estrutura de herança. A Herança de Tabela de Classes suporta isso,
usando uma tabela no banco de dados por classe da estrutura de herança.

Como Funciona
A característica evidente da Herança de Tabela de Classes é que ela possui uma tabela
por classe no modelo do domínio. Os campos nas classes do domínio são mapeados

diretamente para campos nas tabelas correspondentes. Assim como com os outros
mapeamentos de herança, aqui também se aplica a abordagem básica dos Mapeadores de Herança (291).

Uma questão é como associar as linhas correspondentes das tabelas do banco
de dados. Uma solução possível é usar um valor de chave primária comum, de modo que, digamos, a linha da chave 101 na tabela de jogadores de futebol e a linha da
chave 101 na tabela de jogadores correspondam ao mesmo objeto do domínio. Visto

que a tabela da superclasse tem uma linha para cada linha nas outras tabelas, se vo-

cê usar este esquema as chaves primárias serão únicas por todas as tabelas. Uma al-

ternativa é deixar cada tabela ter suas próprias chaves primárias e usar chaves estrangeiras apontando para a tabela da superclasse para conectar as linhas.
O maior problema na implementação da Herança de Tabela de Classes é como recuperar os dados a partir de diversas tabelas de uma maneira eficiente. Obviamente, fazer uma chamada para cada tabela não é bom, já que você teria diversas chamadas para o banco de dados. Você pode evitar isso fazendo uma junção (join) pelas diversas ta-

Capíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

277

belas componentes. Não obstante, junções para mais de três ou quatro tabelas tendem

a ser lentas devido ao modo pelo qual os bancos de dados executam suas otimizações.
Além disso, há o problema de que frequentemente você não sabe exatamente

quais tabelas juntar em uma consulta. Se você estiver procurando um jogador de futebol, você sabe usar a tabela de jogadores de futebol, mas se você estiver procurando por um grupo de jogadores, quais tabelas você usa? Para realizar junções de maneira eficaz quando algumas tabelas não têm dados, você precisará fazer uma junção

externa (outer join), a qual não é padrão e, frequentemente, é lenta. A alternativa é ler
primeiro a tabela raiz e então usar um código para descobrir quais tabelas ler a seguir, mas isso envolve múltiplas consultas.
Quando

Usá-la
Herança de Tabela de Classes, Herança de Tabela Única (269) e Herança de Tabela Concreta

(283) são as três alternativas a considerar para o mapeamento de herança.
Os pontos fortes da Herança de Tabela de Classes são:

e Todas as colunas são relevantes para todas as linhas, de modo que as tabelas

são mais fáceis de compreender e não desperdiçam espaço.

* Orelacionamento entre o modelo do domínio e o banco de dados é bastante

direto.

Os pontos fracos da Herança de Tabela de Classes são:

© Você precisa alcançar diversas tabelas para carregar um objeto, o que significa uma junção ou diversas consultas para depois costurar os pedaços em memória.

* Qualquer refatoração de campos para cima ou para baixo na hierarquia gera
alterações no banco de dados.
e As tabelas dos supertipos podem se tornar um gargalo porque elas têm de

ser acessadas freqiientemente.

* Aalta normalização pode torná-la difícil de entender em consultas ad hoc.
Você não tem que escolher apenas um padrão de mapeamento de herança para
uma hierarquia de classes. Você pode usar Herança de Tabela de Classes para as classes

no topo da hierarquia e uma coleção de Heranças de Tabela Concreta (283) para aquelas
mais abaixo.

Leitura Adicional
Vários textos da IBM referem-se a este padrão como Mapeamento Raiz-Folha (Root-

Leaf Mapping) [Brown et al.].

Exemplo: Jogadores e Assemelhados (C#)

Aqui está a implementação para a descrição. Novamente seguirei o tema familiar
(talvez um pouco tedioso) de jogadores e assemelhados, usando Mapeadores de Heran-

ça (291) (Figura 12.9).

278

Parte ll e Os Parões

Mapeador
+inserir (Objeto do Domínio)

Mapeador Abstrato
de Jogador

+excluir (Objeto do Domínio)
--métodos gancho

dravar
carregar

+atualizar (Objeto do Domínio)

gravar (Objeto do Domínio, Linha)
carregar (Objeto do Domínio, Linha)

1
3

Mapeador de
Jogador de Futebol
+ buscar (chave): Jogador
| de Futebol
#gravar
#carregar

23

Mapeador de Jogador

ey
Er

+ buscar (chave); Jogador >} @———

e

+ atualizar

o

+ inserir

Mapeador de

Jogador de Criquete
1

(chave): Jogador
+5 buscar
u
e
E Críquete

Ane

Mapeador de

Jogador de Boliche

(___s]
Figura 12.9

1 | + buscar (chave): Jogador

de Boliche
gravar
carregar

O diagrama de classes genérico dos Mapeadores de Herança (291).

Cada classe precisa definir a tabela que armazena os seus dados e um código de
tipo para ela.

class MapeadorAbstratoDeJogador.
abstract public String CédigoDoTipo (get;}

protected static String NOME
DA TABELA = "Jogadores" ;
class

MapeadorDeJogadorDeFutebol...

public override String CédigoDoTipo (

get {return "F";}

}
protected new static String NOME
DA TABELA = "JogadoresDefutebol";
Diferentemente dos outros exemplos de herança, este não tem um nome de ta-

bela sobrescrito porque temos de ter o nome da tabela para esta classe mesmo quan-

do a instância é uma instância da subclasse.

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

279

Carregando um Objeto Se você tiver lido os outros mapeamentos, você sabe que o

primeiro passo é o método de busca nos mapeadores concretos.

class MapeadorDeJogadorDeFutebol...
public JogadorDeFutebol Buscar

}

(long id)

(

return (JogadorDeFutebol) BuscaAbstrata (id, NOME
DA TABELA) ;

O método abstrato de busca procura uma linha que seja igual à chave e, se en-

contrar, cria um objeto do domínio e chama o seu método de carga.

class Hapeador...
public ObjetoDoDonínio Buscaabstrata (long id, String noneDaTabela) {
DataRow linha = BuscarLinha (id, tabelaPara (noneDaTabela));
if (Linha == null) return null;
else {
ObjetoDoDomínio resultado = CriarObjetoDoDominio(

}

}

resultado.1d = id;
Carregar (resultado) ;
return resultado;

protected DataTable tabelaPara (String nome)

}

return Gateway.Data.Tables [nome] ;

{

protected DataRow BuscarLinha (long id, DataTable tabela)
String filtro = String.Format
("id = {0}", id);

}

};

(

DataRow [ ] resultados = tabela.Select (filtro);
return (resultados.Length == 0) ? null: resultados
(0) ;

protected DataRow

BuscarLinha (long id, String nomeDaTabela)

return BuscarLinha (id, tabelaPara (nomeDaTabela)) ;

{

}
protected abstract ObjetoDoDonínio CriarobjetoDoDomínio ( );
class MapeadorDeJogadorDeFutebol...
protected override ObjetoDoDomínio CriarObjetoDoDominio
return new JogadorDeFutebol ( );

( ) {

}

Existe um método de carga para cada classe que carrega os dados definidos por
essa classe.

class MapeadorDeJogadorDeFutebol...
protected override void Carregar

base. Carregar (obj) ;
DataRow

linha

= BuscarLinha

(ObjetoDoDominio obj)
(obj.Id,

tabelaPara

{

(NOME
DA TABELA) ) ;

JogadorDeFutebol jogadorDeFutebol = (JogadorDeFutebol) ob
jogadorDeFutebol.clube = (String) linha["clube"] ;

280

Parte ll e Os PADROES

class MapeadorabstratoDeJogador...

protected override void Carregar (ObjetoDoDonínio obj) {
DataRow

linha = BuscarLinha

(obj.Id,

Jogador jogador = (Jogador) obj;
jogador.none = (String) linha ['none");

tabelaPara
(NOME DA TABELA) ) ;

Assim com o outro exemplo de código, porém de forma muito mais perceptível

aqui, estou contando com o fato de que o conjunto de dados ADO.NET trouxe os dados do banco de dados e os colocou em cache em memória. Isso me permite fazer diversos acessos à estrutura de dados baseada em tabelas sem um alto custo em de-

sempenho. Se você for diretamente ao banco de dados, precisará reduzir essa carga.
Neste exemplo, você poderia fazer isso criando uma junção por todas as tabelas e
manipulando o resultado.
de Classes

O mapeador de jogadores determina que tipo de jogador ele tem que buscar e
então delega ao mapeador concreto correto.

2

class MapeadorDedogador. ..

public Jogador Buscar (long chave) {

DataRow linha = BuscarLinha (chave,
if (linha
null) return null;

tabelaPara

(NOME
DA TABELA) );

else (
String códigoDoTipo = (String) linha["tipo");
if (códigoDoripo == mapeadorB
. C6digoDoTipo}
return mapeadorB Buscar (chave) ;

)

if (e6digoDoTipo == mapeadorC. CédigoDoTipo)
return mapeadorC.
Buscar (chave) ;
Af (códigoDoripo == mapeador?.CódigoDoripo)
return mapeadorP.
Buscar (chave) ;
throw new Exception "tipo desconhecido”) ;

)
protected static String NOME
DA TABELA = "Jogadores" ;
Atualizando um Objeto O método de atualização aparece na superclasse mapeadora.

class Mapeador...
public virtual void Atualizar

}

Gravar (arg) ;

(ObjetoDoDomínio arg)

(

Ele é implementado por meio de uma série de métodos de gravação, um para
cada classe na hierarquia.

class HapeadorDeJogadorDeFutebol. .
protected override void Gravar
base.Gravar (obj) ;

(ObjetoDoDominio obj)

{

DataRow linha = BuscarLinha (obj.1d, tabelaPara
(NOME DA TABELA) ) ;
JogadorDeFutebol jogadorDeFutebol = (JogadorDeFutebol) obj;
Linha["clube"] = jogadorDeFutebol .clube;

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

281

class MapeadorabstratoDeJogador....
protected override void Gravar (ObjetoDoDomínio obj) (
DataRow linha = BuscarLinha (obj.Id, tabelaPara (NOME
DA TABELA) ) ;
Jogador jogador = (Jogador) obj;

linha ("none") = jogador. none;
linha["tipo"]

= CódigoDoTipo;

O método de atualização do mapeador de jogadores redefine o método geral
para transferir para o mapeador concreto correto.
class MapeadorDedogador. ..
public override void Atualizar (ObjetoDoDomínio obj) {
MapeadorPara
(obj) .Atualizar (obj) ;
}
private Mapeador MapeadorPara (ObjetoDoDomínio obj) {
if (obj is JogadorDeFutebol)

return mapeador?;

if (obj is JogadorDeBoliche)
return mapeadorB;
if (obj is JogadorDeCriquete)
return mapeadorC;

throw new Exception ("Nenhum mapeador disponível") ;

Inserindo um Objeto O método para inserir um objeto é declarado na superclasse mapeadora. Ele tem dois estágios: criar novas linhas no banco de dados e, então,

usar os métodos de gravação para atualizar estas linhas em branco com os dados

necessários.

class lapeador...
public virtual long Inserir (ObjetoDoDomínio obj) {

obj.1d = LerPróximolD() ;
AdicionarLinha (obj) ;
Gravar (obj) ;

return obj. 1d;

Cada classe insere uma linha em sua tabela.

class MapeadorDeJogadorDeFutebol...
protected override void AdicionarLinha
base.AdicionarLinha (obj);

(ObjetoDoDomínio obj)

(

InserirLinha (obj, tabelaPara
(NOME DA TABELA));

}
class HapeadorAbstratoDeJogador...

protected override void AdicionarLinha

}

(ObjetoDoDomínio obj)

InserirLinha (obj, tabelaPara
(NOME DA TABELA));

{

282

Parte ll e Os Parões

class Mapeador...

abstract protected void AdicionarLinha (ObjetoDoDonínio obj) ;
protected virtual void InserirLinha

DataRow linha = tabela.NewRow( );
Linha["id"] = arg.1d;
tabela.Rows.Ada (Linha) ;

((ObjetoDoDominio arg, DataTable tabela)

(

O mapeador de jogadores delega ao mapeador concreto apropriado.

de Classes

class HapeadorDeJogador. .
public override long Inserir (ObjetoDoDonínio obj) {
return NapeadorPara (obj) . Inserir (obj) ;
}

2

Excluindo um Objeto

Para excluir um objeto, cada classe exclui uma linha da tabe-

la correspondente no banco de dados.

class HapeadorDeJogadorDeFutebol. .
public override void Excluir (ObjetoDoDominio obj) (

}

base.Excluir (obj) ;
DataRow linha = BuscarLinha (obj.1d, NOME
DA TABELA) ;
linha.Delete( ) ;

class MapeadorAbstratoDeJogador...
public override void Excluir (ObjetoDoDomínio obj) (

DataRow linha = BuscarLinha (obj.1d, tabelaPara
(NOME DA TABELA) );

}

linha.Delete( );

class Mapeador..
public abstract void Excluir

(ObjetoDoDomínio obj) ;

O mapeador de jogadores novamente foge do trabalho duro e apenas delega

para o mapeador concreto.
class MapeadorDedogador. ..

public override void Excluir (ObjetoDoDoni
HapeadorPara
(obj) .Excluir (obj) ;
}

Capitulo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

283

Herança de Tabela Concreta (Concrete Table Inheritance)
Representa um hierarquia de herança de classes
com uma tabela por classe concreta na hierarquia.

«tabela»
JogadoresDeFutebol
nome
clube

Jogador
nome
I

Jogador de Futebol
clube

Jogador de Criquete|
média de rebatidas

Jogador de Boliche
média de pontos

«tabela»
JogadoresDeCriquete
nome
média de rebatidas
«tabela»
JogadoresDeBoliche

nome
média de rebatidas
média de pontos

Como qualquer purista da orientação a objetos lhe dirá, os bancos de dados relacionais não suportam herança — um fato que complica o mapeamento objeto-relacional.
Do ponto de vista da instância de um objeto, um caminho sensato para pensar em ta-

belas é pegar cada objeto na memória e mapeá-lo para uma única linha no banco de
dados. Isso sugere a Herança de Tabela Concreta, na qual existe uma tabela para cada
classe concreta na hierarquia de herança.
Confesso que tive alguma dificuldade para dar um nome para este padrão. A
maioria das pessoas pensa nele como orientado a folhas, já que você normalmente

tem uma tabela por classe folha em uma hierarquia. Seguindo essa lógica, eu poderia chamar este padrão de herança de tabela folha, e o termo “folha” é freqiientemente usado para este padrão. A rigor, entretanto, uma classe concreta que não seja uma
folha normalmente também tem uma tabela, de modo que resolvi escolher o termo
mais correto, ainda que menos intuitivo.

Como Funciona
A Herança de Tabela Concreta usa uma tabela do banco de dados para cada classe concreta na hierarquia. Cada tabela contém colunas para a classe concreta e todos os
seus ancestrais, de modo que qualquer campo na superclasse é duplicado pelas tabelas das subclasses. Assim como com todos estes esquemas de herança, o comporta-

mento básico usa Mapeadores de Herança (291).

Com este padrão, você precisa prestar atenção às chaves. Fazendo um trocadilho, a chave é assegurar que as chaves sejam únicas não apenas para uma tabela, mas
para todas as tabelas de uma hierarquia. Um exemplo clássico de onde você precisa
disto é se você tiver uma coleção de jogadores e estiver usando um Campo Identidade

gFi
E

284

Parte Il e Os PaprOes

(215) com chaves com escopo de tabela. Se as chaves puderem ser duplicadas entre

as tabelas que mapeiam as classes concretas, você terá diversas linhas para um valor
particular da chave. Assim, você realmente precisa de um sistema de alocação de

chaves que fique de olho no uso das chaves pelas tabelas. Além disso, você não pode
depender do mecanismo de unicidade de chaves primárias do banco de dados.

Isso se torna especialmente complicado se você estiver preso a bancos de dados

usados por outros sistemas. Em muitos desses casos, você não pode garantir a unici-

dade das chaves pelas tabelas. Nesta situação, ou você evita usar os campos da superclasse ou usa uma chave composta que envolva um identificador de tabela.

Você pode evitar alguns destes problemas não tendo campos do tipo da super-

classe, mas isto obviamente compromete o modelo de objetos. Uma alternativa é ter
métodos de acesso para o supertipo na interface mas usar vários campos privados

Concreta

para cada tipo concreto na implementação. A interface então combina os valores dos

É
2

campos privados. Se a interface pública for um único valor, ela pega qualquer um
dos valores privados que não forem nulos. Se a interface pública for uma coleção, ela
responde com a união dos valores dos campos da implementação.
Para chaves compostas você pode usar um objeto chave especial como seu cam-

po ID para o Campo Identidade (215). Esta chave usa tanto a chave primária da tabela

como o nome da tabela para garantir unicidade

Relacionados a isso, estão os problemas com a integridade referencial no banco
de dados. Considere um modelo de objetos como o da Figura 12.10. Para implementar integridade referencial, você precisa de uma tabela associativa que contenha colunas de chaves estrangeiras para a função caridade e para o jogador. O problema é
que não existe uma tabela para o jogador, de modo que você não pode montar uma

restrição de integridade referencial para o campo da chave estrangeira que recebe

tanto jogadores de futebol quanto de críquete. A sua escolha é entre ignorar a integri-

dade referencial ou usar múltiplas tabelas associativas, uma para cada uma das tabe-

las concretas no banco de dados. Além disso, você terá problemas se não puder ga-

rantir a unicidade das chaves.
Se você estiver procurando por jogadores com um comando select, precisa
olhar todas as tabelas para ver quais contêm o valor apropriado. Isso significa usar
múltiplas consultas ou usar uma junção externa (outer join), ambas opções ruins no
que se refere ao desempenho. Você não sofre o problema do desempenho quando
sabe de qual classe precisa, mas você tem que usar a classe concreta para melhorar
o desempenho.

Função Caridade

Figura 12.10

+

x | nome

Jogador

Jogador de Futebol

Jogador de Criquete

clube

média de rebatidas

Um modelo que causa problemas de integridade referencial para a

Herança de Tabela Concreta.

Capitulo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

285

Muitas vezes, este padrão é mencionado como sendo alinhado com o padrão

herança de tabela de folhas. Algumas pessoas preferem uma variação na qual

você tem uma tabela por classe folha em vez de uma tabela por classe concreta. Se

você não tiver nenhuma superclasse concreta na hierarquia, isso acaba sendo a
mesma coisa. Mesmo se você tiver superclasses concretas, a diferença é bem pe-

quena.
Quando

Usá-la
Ao buscar a solução para mapear herança, as alternativas são: Herança de Tabela Con-

creta, Herança de Tabela de Classes (276) e Herança de Tabela Única (269) .

Os pontos fortes da Herança de Tabela Concreta são:

* Cada tabela é autocontida e não tem campos irrelevantes. A conseqiiéncia é

que ela faz bastante sentido quando usada por outras aplicações que não estejam usando os objetos.

* Nao há junções a realizar durante a leitura dos dados dos mapeadores con-

cretos.

* Cada tabela é acessada apenas quando a classe é acessada, o que pode des-

concentrar a carga de acesso.

Os pontos fracos da Herança de Tabela Concreta são:
* Pode ser difícil tratar as chaves primárias.
* Você não pode forçar relacionamentos no banco de dados para classes abs-

tratas.

* Se os campos das classes do domínio subirem ou descerem na hierarquia, vo-

cê tem que alterar as definições das tabelas. Você não tem que fazer tantas al-

terações como com a Herança de Tabela de Classes (276), mas você não pode ignorar esse fato, como fizemos na Herança de Tabela Única (269).

* Se um campo da superclasse for alterado, você precisa alterar cada tabela que

tenha este campo porque os campos da superclasse são duplicados pelas ta-

belas.

* Uma busca na superclasse obriga você a verificar todas as tabelas, o que leva

a múltiplos acessos ao banco de dados (ou a uma junção esquisita).

Lembre-se de que o trio de padrões de herança pode coexistir em uma única

hierarquia. Assim, você poderia usar a Herança de Tabela Concreta para uma ou duas
subclasses e Herança de Tabela Unica (269) para o resto.

Exemplo: Jogadores Concretos (C#)
Aqui mostrarei uma implementação para a descrição do padrão. Assim como em to-

dos os exemplos de herança neste capítulo, estou usando o projeto básico das classes
de Mapeadores de Herança (291), mostrado na Figura 12.11.

Cada mapeador é associado à tabela do banco de dados que é a fonte dos da-

dos. Em ADO.NET um conjunto de dados (data set) armazena os dados da tabela.

286

Parte ll e Os PADRÕES

Mapeador

Mapeador Abstrato
de Jogador

+inserir (Objeto do Domínio)

+atualizar (Objeto do Domínio)
+excluir (Objeto do Domínio)

--métodos gancho
gravar (Objeto do Domínio, Linha)
carregar (Objeto do Domínio, Linha),

ga E

[

Mapeador de
Jogador de Futebol
1

2
ma

se

BE
ss
85

E
=

Mapeador de Jogador

+ buscar (chave): Jogador
| de Futebol
#gravar
#carregar

+ buscar (chave): Jogador jg
+ inserir
+ atualizar

Mapeador de
Jogador de Criquete
A

bs (chave): Jogador
de++ buscar
criquete
eens
E)
carregar

Mapeador de
Jogador de Boliche
[>>>

1 | + buscar (chave): Jogador
de Boliche

#gravar

#carregar
Figura 12.11

O diagrama de classes genérico dos Mapeadores de Herança (291).

class Hapeador...
public Gateway Gateway;

private IDictionary mapaDeldentidade = new Hashtable( );
public Mapeador

}

(Gateway gateway)

this.Cateway = gateway;

{

private DataTable tabela {
get {return Gateway. Data.Tables [NomeDaTabela] ; }

}

abstract public String NomeDaTabela (get; }

A classe gateway armazena o conjunto de dados dentro de sua propriedade de

dados. Os dados podem ser carregados fornecendo-se consultas apropriadas.

class Gateway...
public DataSet Dados = new DataSet ( );

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

287

Cada mapeador concreto precisa definir o nome da tabela que armazena seus

dados.

class MapeadorDedogadorDeCriquete. ..
public override String NomeDaTabela (
get [return "JogadoresDeCríquete";)

O mapeador de jogadores tem campos para cada mapeador concreto.
class MapeadorDeJogador
private MapeadorDedogadorDeBoliche mapeadorB;
private MapeadorDeJogadorDeCriquete mapeadorC;
private MapeadorDeJogadorDeFutebol mapeadorF;

public MapeadorDeJogador (Gateway gateway): base (gateway) {

napeadorB = new MapeadorDeJogadorDeBoliche (Gateway) ;

93.

mapeadorF = new NapeadorDeJogadorDeFutebol (Gateway) ;

ge

mapeadorC = new MapeadorDeJogadorDeCríquete (Gateway) ;

Carregando um Objeto do Banco de Dados Cada classe mapeadora concreta tem
um método de busca que retorna um objeto dado um valor da chave.

class MapeadorDeJogadorDeCriquete...
public JogadorDeCriquete Buscar (Long id) {
return (JogadorDeCriquete) Buscanbstrata
(id) ;
}
O comportamento abstrato na superclasse busca a linha correta do banco de da-

dos para o ID, cria um novo objeto do domínio do tipo correto e usa o método de car-

ga para carregá-lo (descreverei a carga logo em seguida).

class lapeador.
public ObjetoDoDominio Buscanbstrata (long id) (
DataRow linha = Buscarbinha (id);

if (Linha == null) return null;

else{

}

ObjetoDoDominio resultado = CriarObjetoDoDomínio( );
Carregar (resultado, linha) ;
return resultado;

ivate DataRow BuscarLinha (long id) {
String filtro = String.Format
("id = {0}", id);

DataRow[ ] resultados = tabela.Select (filtro) ;
if (resultados. Length == 0) return null;

}

else return resultados
[0];

protected abstract ObjetoDoDominio CriarObjetoDoDominio(

};

28

288

Parte Ile

Os PADRÕES

class MapeadorDeJogadorDeCriquete...
protetected override ObjetoDoDomínio CriarObjetoDoDomínio ( ) {

}

return new JogadorDeCriquete ( };

Acarga real de dados do banco de dados é feita pelo método de carga, ou então

por diversos métodos de carga: um de cada para a classe mapeadora e para todas as

suas superclasses.

class MapeadorDeCriquete...
protected override void Carregar

base.Carregar
(obj, linha) ;

(ObjetoDeDominio obj, DataRow linha)

JogadorDeCríquete jogadorDeCriquete = (JogadorDeCriquete)

Concreta

}

2

{

obj;

jogadorDeCriquete.nédiaDeRebatidas = (double) Linha ['médiaDeRebatidas") ;

class MapeadorAbstratoDeJogador. .
protected override void Carregar (ObjetoDoDoní ão obj, DataRow linha) (
base.Carregar
(obj, linha) ;
Jogador jogador = (Jogador) obj;
jogador.none = (String) linha [*nome"] ;
}
class Mapeador...
protected virtual void Carregar (ObjetoDoDominio obj, DataRow linha)

)

obj.1d = (int) Linha[*id"] ;

{

Esta é a lógica para encontrar um objeto, usando um mapeador para uma clas-

se concreta. Você também pode usar um mapeador para a superclasse: o mapeador
de jogadores, o qual precisa buscar um objeto de qualquer tabela em que ele esteja residindo. Uma vez que todos os dados já estão no conjunto de dados em memória,

posso fazer isso como:

class MapeadorDeJogador
public Jogador Buscar (Long chave) {

Jogador resultado;
resultado = mapeadorF Buscar (chave) ;
if

(resultado

!= null)

return resultado;

resultado = mapeadorB.Buscar (chave) ;

if (resultado != null) return resultado;
resultado = mapeadorC.Buscar (chave) ;

if (resultado != null) return resultado;

return null;

Lembre-se de que isso é razoável apenas porque os dados já estão na memória.
Se você precisar ir ao banco de dados três vezes (ou mais para mais subclasses), isso
ficará lento. Pode ajudar a executar uma junção por todas as tabelas concretas, o que
lhe permitirá acessar os dados em uma única chamada ao banco de dados. Entretan-

to, junções grandes são, por direito, frequentemente lentas, de modo que você preci-

Capiruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

289

sará executar alguns testes com sua própria aplicação para descobrir o que funciona

e o que não funciona no seu caso. Além disso, esta será uma junção externa (outer

join), a qual, além de tornar lenta a consulta, usa uma sintaxe não-portável e freqiien-

temente obscura.

Atualizando um Objeto O método de atualização pode ser definido na superclasse
mapeadora.

class Mapeador...
public virtual void Atualizar

}

(ObjetoDoDomínio arg)

Gravar (arg, BuscarLinha (arg. 1));

(

De forma semelhante à carga, usamos uma sequência de métodos de gravação

para cada classe mapeadora.

class MapeadorDedogadorDeCriquete. .

protected override void Gravar (ObjetoDoDomínio obj, DataRow 1i na)
base.Gravar (obj, linha) ;

{

JogadorDeCriquete jogadorDeCriquete = (JogadorDeCriquete) obj;

linha ["nédiaDeRebatidas"] = jogadorDeCriquete.médiaDeRebatidas;

}
class MapeadorDeJogadorabstrato. .

protected override void Gravar (ObjetoDeDomínio obj, DataRow linha) {

Jogador jogador = (Jogador) obj;
linha ("nome") = jogador.none;

O mapeador de jogadores precisa encontrar o mapeador concreto correto a ser

usado e então delegar a chamada de atualização.

class HapeadorDeJogador. .
public override void Atualizar (ObjetoDoDomínio obj)
MapeadorPara (obj) Atualizar (obj) ;

}

private Mapeador MapeadorPara

(ObjetoDoDominio obj)

if (obj is JogadorDefutebol)

(

{

return mapeador?;

if (obj is JogadorDeBoliche)
return mapeadorB;
if (obj is JogadorDeCríguete)
return mapeadorC;

throw new Exception ("Nenhum mapeador disponível") ;

Inserindo um Objeto A inserção é uma variação da atualização. O comportamento
extra é a criação de uma nova linha, o que pode ser feito na superclasse.

290

Parrell

e Os PADROES

class Mapeador...
public virtual long Inserir (ObjetoDoDomínio arg) (

DataRow linha = tabela.NewRow( );
arg.Id = LerPréximoID( );
linha ["id"] = arg.
Gravar (arg, linha);
tabela.Rows.Add

return arg. Id;

(linha);

Concreta

Mais uma vez, a classe jogador delega para o mapeador apropriado.

&
2

class MapeadorDeJogador. ..
public override long Inserir (ObjetoDoDomínio obj) {
return MapeadorPara (obj) .Inserir (obj) ;
}
Excluindo um Objeto A exclusão é bastante direta. Como antes, temos um método
definido na superclasse.

class Mapeador..

public virtual void Excluir (ObjetoDoDominio obj) {
DataRow linha = BuscarLinha (obj. 1d) ;
linha.Delete( );
e um método no mapeador de jogadores que executa a delegação.

class HapeadorDeJogador. .
public override void Excluir (ObjetoDoDonínio obj) {
MapeadorPara
(obj) .Exeluir (obj) ;
}

Capíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

291

Mapeadores de Herança (Inheritance Mappers)

Uma estrutura para organizar os mapeadores de bancos de dados que lidam
com hierarquias de herança.

Mapeador

Mapeador Abstrato
de Jogador

+inserir (Objeto do Domínio)
+atualizar (Objeto do Dominio)

+excluir (Objeto do Domínio)
métodos gancho

E
eearegat

#gravar (Objeto do Dominio, Linha)

#carregar (Objeto do Dominio, Linha)
Mapeador de

1.)
Mapeador de Jogador

Jogador de Futebol

8

+ buscar (chave): Jogador
de Futebol
#gravar
#earregar

E
3
E

i buscar (chave): E: Jogador | $$$
+ atualizar

&

$$

Mapeador de
Jogador de Criquete

AS+ buscar
ciqiste
rs (chave): Jogador

1 | #gravar

carregar

Mapeador de

Jogador de Boliche
1 | + buscar (chave): Jogador

de Boliche
#gravar
#carregar

Quando vocé mapeia de uma hierarquia de heranga orientada a objetos em memoria

para um banco de dados relacional, tem que minimizar a quantidade de cédigo necessária para gravar e carregar os dados do banco de dados. Você também quer for-

necer tanto comportamento abstrato de mapeamento quanto comportamento con-

creto que lhe permitam gravar ou carregar uma superclasse ou uma subclasse.
Ainda que os detalhes deste comportamento variem com o esquema de mapea-

mento de herança utilizado (Herança de Tabela Única (269), Herança de Tabela de Classes
(276) e Herança de Tabela Concreta (283)), a estrutura geral funciona da mesma manei-

ra para todos eles.

292

Parte Il e Os PaprOes

Como Funciona
Vocé pode organizar os mapeadores com uma hierarquia a fim de que cada classe do
dominio tenha um mapeador que grave e carregue os dados para ela. Dessa forma,
vocé tem um ponto em que pode alterar o mapeamento. Esta abordagem funciona

bem com mapeadores concretos que sabem como mapear os objetos concretos na hie-

rarquia. Existem ocasiões, no entanto, em que você também precisa de mapeadores
para as classes abstratas. Estes podem ser implementados com mapeadores que, na
verdade, estão fora da hierarquia básica, mas delegam para os mapeadores concretos

apropriados.

Para explicar melhor como isso funciona, começarei com os mapeadores concretos. No exemplo, os mapeadores concretos são os mapeadores de jogadores de fu-

tebol, de críquete e de boliche. Seu comportamento básico inclui as operações de bus-

ca, inserção, atualização e exclusão.

2
8

Os métodos de busca são declarados nas subclasses concretas porque retorna-

rão uma classe concreta. Portanto, o método de busca no MapeadorDeJogadorDeBo-

liche deveria retornar um jogador de boliche, não uma classe abstrata. As linguagens

OO comuns não deixam você alterar o tipo de retorno declarado de um método, de

modo que não é possível herdar a operação de busca e ainda declarar um tipo específico de retorno. Você pode, é claro, retornar um

tipo abstrato, mas isso obriga o

usuário da classe a executar um downcast - o que é melhor evitar. (Uma linguagem

com atribuição dinâmica de tipos não tem este problema.)

O comportamento básico do método de busca é encontrar a linha apropriada
no banco de dados, instanciar um objeto do tipo correto (uma decisão que é tomada
pela subclasse) e então carregar o objeto com os dados do banco de dados. O méto-

do de carga é implementado por cada mapeador na hierarquia, o qual carrega o

comportamento para o seu objeto do domínio correspondente. Isso significa que o

método de carga do mapeador de jogador de boliche carrega os dados específicos

para a classe jogador de boliche e chama o método da superclasse para carregar os
dados específicos do jogador de críquete, o qual chama o método da sua superclasse e assim por diante.
Os métodos de inserção e atualização operam de forma semelhante, usando um

método de gravação. Aqui você pode definir a interface na superclasse — na verdade,

em uma Camada Supertipo (444). O método de inserção cria uma nova linha e então

grava os dados do objeto do domínio usando os ganchos para os métodos de grava-

ção. O método de atualização apenas grava os dados, também usando os ganchos

para os métodos de gravação. Estes métodos operam de forma similar aos ganchos

para os métodos de carga, com cada classe armazenando os seus dados específicos e
chamando o método de gravação da superclasse.

Este esquema torna fácil escrever os mapeadores apropriados para gravar as in-

formações necessárias para uma parte específica da hierarquia. O próximo passo é

suportar a carga e gravação de uma classe abstrata — neste exemplo, um jogador. Ain-

da que o primeiro pensamento seja colocar métodos apropriados no mapeador da

superclasse, isso, na verdade, se torna complicado. Embora as classes mapeadoras

concretas possam apenas usar os métodos de inserção e atualização do mapeador
abstrato, a inserção e atualização do mapeador de jogadores precisam sobrescrevê-

los para, em seu lugar, invocar um mapeador concreto. O resultado é uma dessas
combinações de generalização e composição que dão um nó na sua cabeça.

Capitulo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

293

Prefiro separar os mapeadores em duas classes. O mapeador abstrato de joga-

dores é responsável por carregar e gravar os dados do jogador específico no banco de

dados. Esta é uma classe abstrata cujo comportamento é usado apenas pelos objetos

mapeadores concretos. Uma classe mapeadora de jogadores distinta é usada como

interface para as operações no nível do jogador. O mapeador de jogadores fornece
um método de busca e sobrescreve os métodos de inserção e atualização. A respon-

sabilidade de todos eles é descobrir qual mapeador concreto deveria tratar a tarefa e
delegar para ele.

Ainda que um esquema genérico como este faça sentido para todos os tipos de
mapeamento de herança, os detalhes de fato variam. Portanto, não é possível mostrar um exemplo de código para este caso. Você pode encontrar bons exemplos em

cada uma das seções dos padrões de mapeamento de herança: Herança de Tabela Única (269), Herança de Tabela de Classes (276) e Herança de Tabela Concreta (283).

Quando Usá-los
Este esquema geral faz sentido para qualquer mapeamento de banco de dados basea-

do em herança. As alternativas envolvem coisas como duplicar o código de mapea-

mento da superclasse entre os mapeadores concretos e incluir a interface do jogador

na classe mapeadora abstrata de jogadores. O primeiro é um crime hediondo, e o úl-

timo é possível, mas leva a uma classe mapeadora de jogadores deselegante e confusa. Em geral, então, é difícil pensar em uma boa alternativa para este padrão.

&

F

CaríTULO

1

3

Padrões de Mapeamento em
Metadados Objeto-Relacionais

CapituLo 13

e PADRÕES DE MAPEAMENTO EM METADADOS OBiETO-RELACIONAIS

295

Mapeamento em Metadados (Metadata Mapping)
Armazena os detalhes do mapeamento objeto-relacional em metadados.

Mapa de Dados
classeDoDomínio

nomeDaTabela

Mapa de Colunas
>

nomeDaColuna

ye | momeDocampo

Muito do código que lida com mapeamento objeto-relacional descreve como os campos no banco de dados correspondem aos campos dos objetos na memória. O código
resultante tende a ser tedioso e repetitivo de escrever. Um Mapeamento em Metadados

permite aos desenvolvedores definir os mapeamentos em uma forma tabular simples, a qual então pode ser processada por código genérico para realizar os detalhes
de leitura, inserção e atualização dos dados.

Como Funciona
A maior decisão a ser tomada no uso do Mapeamento em Metadados é como as infor-

=

minhos principais a trilhar: geração de código e programação reflexiva.

3

mações nos metadados se expressam em termos de código em execução. Há dois caComa

geração de código você escreve um programa cuja entrada são os meta-

dados e cuja saída é o código fonte das classes que executam o mapeamento. Essas

classes parecem ter sido escritas à mão, mas são inteiramente geradas durante o processo de construção, normalmente, imediatamente antes da compilação. As classes

de mapeamento resultantes são distribuídas com o código servidor.

Se você usar a geração de código, você deve se assegurar de que ela é comple-

tamente integrada ao seu processo de construção (build) seja qual for o roteiro de

construção que você estiver usando. As classes geradas nunca devem ser editadas à
mão e, desta forma, não precisam ter o seu código fonte controlado.

Um programa reflexivo pode solicitar um método chamado gravarNome a um ob-

jeto e então executar uma chamada a esse método passando o parâmetro apropriado.
Tratando métodos (e campos) como dados, o programa reflexivo pode ler os nomes

de campos e métodos de um arquivo de metadados e usá-los para realizar o mapea-

mento. Normalmente, desaconselho a reflexão, em parte porque ela é lenta, mas

principalmente porque ela frequentemente produz código difícil de depurar. Ainda
assim, a reflexão é, na verdade, bastante apropriada para o mapeamento em banco
de dados. Uma vez que você está lendo os nomes dos campos e dos métodos de um
arquivo, você está tirando total proveito da flexibilidade da reflexão.
A geração de código é uma abordagem menos dinâmica, já que qualquer altera-

ção no mapeamento requer a recompilação e a redistribuição de pelo menos parte do

software. Com uma abordagem reflexiva, você pode apenas alterar o arquivo de da-

dos do mapeamento, e as classes existentes usarão os novos metadados. Você pode

fazer isso até mesmo em tempo de execução, relendo os metadados quando ocorrer

alguma interrupção específica. Alterações nos mapeamentos deveriam ser bastante
raras, já que implicam alterações no banco de dados ou no código. Os modernos am-

bientes de desenvolvimento facilitam ainda a tarefa de redistribuir parte de uma

aplicação.

296

Parte Il e Os PaprOes

A programação reflexiva muitas vezes padece com a velocidade, embora o pro-

blema aqui dependa muito do ambiente que você estiver usando — em alguns, uma

chamada reflexiva pode ser uma ordem de grandeza mais lenta. Lembre-se, contudo,

de que a reflexão está sendo executada no contexto de uma chamada SQL, então, sua

velocidade mais lenta pode não fazer tanta diferença assim, considerando a baixa ve-

locidade da chamada remota. Como com qualquer aspecto relacionado ao desempenho, você precisa efetuar medidas dentro do seu ambiente para descobrir o quanto

este é um fator importante para você.
Ambas as abordagens podem ser um pouco complicadas para depurar. A com-

paração entre elas depende muito do quão habituados estão os desenvolvedores com

código gerado e código reflexivo. O código gerado é mais explícito, de modo que vo-

cê pode ver o que está acontecendo no depurador. Como conseqiiéncia, normalmen-

te, prefiro a geração à reflexão e penso que ela é normalmente mais fácil para desen-

volvedores menos sofisticados (o que, suponho, faz de mim um desenvolvedor não-

sofisticado).

Na maioria das ocasiões, você mantém os metadados em um formato de arqui-

vo separado. XML é, atualmente, uma escolha popular uma vez que fornece uma es-

trutura hierárquica ao mesmo tempo em que o livra de escrever seus próprios analisadores (parsers) e outras ferramentas. Uma etapa de carga pega esses metadados e
e 8

os transforma em uma estrutura de linguagem de programação, a qual então
conduz
o processo de geração de código ou o mapeamento reflexivo.
Nos casos mais simples, você pode dispensar o arquivo externo e criar a representação dos metadados diretamente no código fonte. Isso lhe poupa de ter que
realizar a análise sintática (parse), mas torna a edição dos metadados um pouco
mais difícil.

Uma alternativa é manter as informações do mapeamento no próprio banco de
dados, que as mantém junto aos dados. Se o esquema do banco de dados for alterado, a informação do mapeamento já está lá.
Quando você estiver decidindo de que modo armazenar as informações em me-

tadados, você pode, quase que completamente, esquecer as questões relativas ao desempenho no acesso e na análise sintática (parsing). Se você usar a geração de código,
© acesso e a análise ocorrem apenas durante a construção e não durante a execução.

Se você estiver usando a programação reflexiva, você, tipicamente, efetuará o acesso

ea análise durante a execução, mas apenas uma única vez, durante a inicialização do

sistema; você pode então manter a representação em memória.

O quão complexo tornar seus metadados é uma das suas maiores decisões.

Quando você se depara com um problema genérico de mapeamento relacional, exis-

te uma série de fatores diferentes para manter nos metadados, mas muitos projetos

podem arranjar-se com muito menos do que um esquema geral completo, e, assim,
seus metadados podem ser muito mais simples. No geral, vale mais a pena desenvol-

ver seu projeto à medida que as suas necessidades aumentarem, já que não é difícil
adicionar novas capacidades a um software orientado a metadados.
Um dos desafios dos metadados é que, embora um esquema simples de metadados frequentemente funcione bem durante 90% do tempo, há casos especiais que

tornam a vida muito mais complicada. Para lidar com esta minoria de casos, você fre-

qiientemente tem que adicionar uma grande complexidade aos metadados. Uma alternativa útil é sobrescrever o código genérico com subclasses nas quais o código especial é escrito à mão. Essas subclasses de casos especiais são subclasses tanto do có-

digo gerado quanto das rotinas reflexivas. Uma vez que esses casos especiais são...

bem... especiais, não é fácil descrever em termos gerais como você deve organizar as

CarituLo 13

e PADRÕES DE MAPEAMENTO EM METADADOS OBiETO-RELACIONAIS

297

coisas para sustentar a sobrescrita. Meu conselho é tratá-las caso a caso. Quando vo-

cê precisar da sobrescrita, altere o código gerado
/ reflexivo para isolar o método que
deve ser sobrescrito e então sobrescreva-o no seu caso especial.

Quando

Usá-lo
O Mapeamento em Metadados pode reduzir muito a quantidade de trabalho necessária

para tratar o mapeamento em um banco de dados. Entretanto, algum trabalho de configuração é requerido para preparar o framework do Mapeamento em Metadados. Além
disso, ainda que frequentemente seja fácil tratar a maioria dos casos com Mapeamento
em Metadados, você pode encontrar exceções que realmente complicam os metadados.

Não é surpresa que as ferramentas comerciais para o mapeamento objeto-rela-

cional usem o Mapeamento em Metadados — ao vender um produto, produzir um Ma-

peamento em Metadados sofisticado sempre vale o esforço.
Se você estiver construindo seu próprio sistema, você mesmo deve avaliar os
compromissos. Compare a adição de novos mapeamentos usando código escrito à

mão com o uso do Mapeamento em Metadados. Se você usar reflexão, investigue seus

efeitos sobre o desempenho. Às vezes, ela causa uma diminuição na velocidade, mas,

às vezes, não. Sua própria medição revelará se este é um problema para você.

O trabalho adicional de codificar à mão pode ser grandemente reduzido pela

criação de uma boa Camada Supertipo (444) que trate todo o comportamento comum.
Desta maneira, você precisará apenas adicionar algumas poucas rotinas para cada

mapeamento. Normalmente, o Mapeamento em Metadados pode reduzir ainda mais

este número.

O Mapeamento em Metadados pode interferir com a refatoração, especialmente se

você estiver usando ferramentas automatizadas. Se você alterar o nome de um campo privado, pode fazer uma aplicação falhar inesperadamente. Mesmo as ferramentas automatizadas de refatoração não conseguirão descobrir o nome do campo escondido em um arquivo XML de dados de um mapeamento. Usar a geração de códi-

go é um pouco mais fácil, uma vez que os mecanismos de busca podem descobrir a

convenção corrente. Ainda assim, qualquer atualização automática será perdida
quando você regerar o código. Uma ferramenta pode lhe avisar sobre um problema,
mas é sua responsabilidade alterar os metadados. Se você usar reflexão, você nem

mesmo receberá o aviso.
Por outro lado, o Mapeamento em Metadados pode tornar a refatoração do banco

de dados mais fácil, já que os metadados representam uma declaração da interface
do esquema do seu banco de dados. Dessa forma, alterações no banco de dados po-

de ser contingenciadas por alterações no Mapeamento em Metadados.
Exemplo: Usando Metadados e

Reflexão (Java)

A maioria dos exemplos neste livro usa código explícito porque é o mais fácil de entender. Entretanto, isso leva a uma programação bastante tediosa, e programação te-

diosa é um sinal de que algo está errado. Você pode remover bastante da programação tediosa usando metadados.

Armazenando os metadados

A primeira questão a ser respondida sobre os metada-

dos é como eles serão mantidos. Aqui os estou mantendo em duas classes. O mapa

de dados corresponde ao mapeamento de uma classe para uma tabela. Este é um ma-

peamento simples, mas será suficiente para o exemplo.

=

298

Parrell

e Os PADROES

class MapaDeDados...

private Class classeDoDonínio;
private String nomeDaTabela;

private List mapasDeColunas = new ArrayList ( );

O mapa de dados contém uma coleção de mapas de colunas que mapeiam colunas na tabela para campos.

class MapaDeColunas....
private String nomeDaColuna;
private String nomeDocampo;
private Field campo;
private MapaDeDados mapaDeDados;

Este não é um mapeamento extremamente sofisticado. Estou apenas usando os

mapeamentos de tipos padrão de Java, o que significa que não há conversão de tipos
entre campos e colunas. Também estou forçando um relacionamento um-para-um
entre tabelas e classes.

Estas estruturas armazenam os mapeamentos. A próxima questão é como elas

28

são povoadas. Para este exemplo irei povoá-las com código Java em classes específicas de mapeamento. Isso pode parecer um pouco estranho, mas proporciona a
maioria dos benefícios dos metadados - evitando código repetitivo.

class MapeadorDePessoa..
protected void carregarMapaDeDados () {
mapaDeDados = new MapaDeDados(Pessoa.class,

"pessoas") ;

napaDeDados.adicionarColuna("sobrenone", "varchar", "sobreNome") ;
mapaDeDados.adicionarColuna ("prenone", "varchar", "preNone") ;
mapaDeDados..adicionarColuna ("número
de dependentes", "int", "númeroDeDependentes") ;

Durante a construção do mapeador de colunas, crio a conexão com o campo. A
rigor, isso é uma otimização, de modo que você pode não ter que calcular os campos.
Entretanto, fazer isso reduz os acessos subseqiientes em uma ordem de magnitude
no meu pequeno laptop.

class HapaDeColunas...
public MapaDeColunas (String noneDaColuna, String nomeDoCampo, MapaDeDados mapaDeDados) (
this.noneDaColuna = noneDaColuna;
this.nomeDoCanpo = nomeDoCampo;
this.mapaDeDados = mapaDeDados;
inicializarCampo( };

}
private void inicializarCampo () {
try {

camp

mapaDeDados. lerClasseDoDomínio(

campo. setAccessible
(true) ;

) catch (Exception e) (

)

).getDeclaredPield

(lerNomeDoCampo(

throw new ApplicationBxception ("Não foi possível configurar o camp.

));

" + nomeDoCampo, e);

Capituco 13

e PADRÕES DE MAPEAMENTO EM METADADOS OBJETO-RELACIONAIS

299

Não é um desafio muito grande escrever uma rotina para carregar o mapa de

um arquivo XML ou dos metadados de um banco de dados. Embora esse desafio não

seja grande, declinarei dele e o deixarei para você.
Agora que os mapeamentos estão definidos, posso fazer uso deles. O poder da
abordagem de metadados é que todo o código que em verdade manipula alguma
coisa está em uma superclasse, de modo que não tenho que escrever o código de mapeamento que tive de escrever nos casos explícitos.
Busca pelo ID Começarei com o método de busca pelo ID.

class Mapeador...

public Object buscarObjeto (Long chave) (
if (udt estáCarregado (chave) ) return udt. JerObjeto (chave) ;

String sql = "SELECT" + mapaDeDados.listaDeColunas{ ) + " FROM "+ mapaDeDados

lerNoneDaTabela( | + * WHERE ID = 2";
PreparedStatement dec = null;
ResultSet rs = null;

ObjetoDoDonínio resultado = null;
try {

dec = DB.prepare (sql) ;
dec.setLong(1, chave.longValue( ));

rs = dec.executeQuery( );
rs.next( );

resultado = carregar (rs) ;

} catch (Exception e) { throw new ApplicationException (e);
) finally (DB.cleanUp(dec, rs) ;

}
return resultado;

}
private UnidadeDeTrabalho udt;
protected MapaDeDados mapaDeDados;
class DataMap...
public String listaDeColunas ( ) {
StringBuffer resultado = new StringBuffer(* ID");

for (Iterator it = mapasDeColunas. iterator( ); it.hasNext( ); ) {

resultado. append(*, ");

MapaDeColunas mapaDeColunas = (MapaDeColunas)

)

}

it.next( );

resultado. append (mapaDeColunas. lerNoneDaColuna( });

return resultado. toString( );

public String lerNomeDaTabela ( ) {
return nomeDaTabela;

}

O comando SELECT é construído mais dinamicamente do que os outros exem-

plos, mas ainda assim vale a pena prepará-lo de modo que seja possível à sessão do

banco de dados colocá-la em cache. Se isso for um problema, a lista de colunas pode
ser calculada durante a construção e colocada em cache, uma vez que não há nenhu-

ma chamada para atualização de colunas durante a vida do mapa de dados. Para es-

te exemplo estou usando uma Unidade de Trabalho (187) para lidar com a sessão do

banco de dados.

300

Parrell

e Os PADROES

Como é comum nos exemplos neste livro, separei a carga da busca, de modo

que podemos usar o mesmo método de carga a partir de outros métodos de busca.
class Mapeador...
public ObjetoDoDonínio carregar (Resultset rs)
throws InstantiationException, IllegalAccessException, SQLException
Long chave = new Long (rs.getLong({"ID")) ;
if (udt estaCarregado(chave)) return udt .lerObjeto (chave) ;

ObjetoDoDonínio resultado =

(ObjetoDoDonínio) mapaDeDados.

resultado.gravarID
chave) ;

lerClasseDoDomínio( ) .newInstance( |;

udt .registrarLimpo (resultado) ;

carregarCanpos (rs, resultado) ;
return resultado;

}

private void carregarCampos (ResultSet rs, ObjetoDoDominio resultado) throws SQLException {
for (Iterator it = mapaDeDados. lerColunas( ); it.hasNext( ); ) {

MapaDeColunas mapaDeColunas = (MapaDeColunas) it.next( );

Object valorDaColuna = rs.getObject (mapaDeColunas. lerNomeDaColuna( )) ;

28

mapaDeColunas.gravarCampo (resultado, valorDaColuna) ;

}

class MapaDeColunas....
public void gravarCampo (Object resultado, Object valorDaColuna) {
try {

campo. set (resultado, valorDaColuna) ;

} catch (Exception e) { throw new ApplicationException ("Erro na gravação " + nomeDoCampo, e);

)

Este é um programa reflexivo clássico. Passamos por cada um dos mapas de colunas e os usamos para carregar o campo no objeto do dominio. Separei o método
carregarCampos para mostrar como podemos estendê-lo para casos mais complicados.
Se tivermos uma classe e uma tabela onde a hipótese simples dos metadados não vale, posso simplesmente sobrescrever o método carregarCampos em uma subclasse ma-

peadora e colocar aí o código arbitrariamente complexo. Esta é uma técnica comum

com metadados — fornecer um lugar em que sobrescrever em casos menos comuns.

Normalmente, é muito mais fácil sobrescrever os casos incomuns com subclasses do
que criar metadados sofisticados o suficiente para armazenar alguns casos especiais

eraros.

É claro que, se tivermos uma subclasse, poderíamos também usá-la para evitar

downcasting.

class MapeadorDePessoa...
public Pessoa buscar(Long chave) {
return (Pessoa) buscar0bjeto(chave) ;
}

Carítuio 13

e PADRÕES DE MAPEAMENTO EM METADADOS OBiETO-RELACIONAIS

Gravando no Banco de Dados

301

Para atualizações uso uma única rotina.

class apeador...
public void atualizar (ObjetoDoDominio obj) {

String sql = "UPDATE " + mapaDeDados.lerNoneDaTabela( | + mapaDeDados. listaDentualização( ) +
" WHERE ID = 2";

Preparedstatement dec = null;
try {

dec = DB.prepare (sal);

int contadorDeParâmetros = 1;

for (Iterator it = mapaDeDados.lerColunas( ); it.hasNext(); ) {

MapaDeColunas col = (MapaDeColunas) it.next ( |;

}

dec.setObject

(contadorDeParâmetros++,

col.lerValor(obj)) ;

dec. setLong(contadorDeParametros, obj.lerID( ).longValue( ));

dec.executelpdate( );

) catch (SQLException e) { throw new ApplicationException (e);
} finally {DB.cleanUp(dec) ;

}

}

class MapaDeDados. ..
public String listaDeAtualização { ) {

for (Iterator it = mapasDeColunas.iterator( ); it.hasNext(); } {
MapaDeColunas mapaDeColunas = (MapaDeColunas) it.next( );

}

}

resultado. append (napaDeColunas. lertiomeDaColuna( }) ;
resultado.append("=?,") ;

resultado.
setLength (resultado. length{ ) - 1);
return resultado.toString( );

public Iterator lerColunas( ) {

return Collections. unnodi fiableCollection mapasDeColunas) .iterator( |;

}
class MapaDeColunas..

public Object lerValor (Object sujeito) {

try {
return campo.
get (sujeito) ;
Jcatch

)

(Exception e) {

throw new ApplicationException

(e);

Inserções usam um esquema semelhante.
class Hapeador...
public Long inserir

(ObjetoDoDominio cbj)

(

String sql = "INSERT INTO " + mapaDeDados.lerNoneDaTabela( ) + " VALUES
(2! + mapaDeDados. listaDeInserção( ) + ")";

PreparedStatement dec = null;
try {

oi

StringBuffer resultado = new SttringBuffer(" SET ");

302

Parte ll e Os Parões

dec = DB.prepare (sql);

dec.setObject (1, obj.lerID( ));
int contadorDeParâmetros = 2;

for (Iterator it = mapaDeDados. lerColunas( ); it.hasNext();

}

MapaDeColunas col = (MapaDeColunas) it.next ( );
dec.setObject (contadorDeParânetros++, col. lerValor (obj);

) {

dec.executeUpdate | ) ;
} catch (SQLException e) { throw new ApplicationException (e);
} finally {DB.cleanUp (dec) ;

}

}

return obj.lerID(

);

class mapaDeDados...
public String listaDeInserção () {

StringBuffer resultado = new StringBuffer(

);

for (int i = 0; i < mapasDeColunas.size( ); itt) {

}

3

result append(",") ;
result append("?") ;

return resultado.toString(

);

Buscas Multiobjeto Há alguns caminhos que você pode seguir para obter diversos

objetos com uma única consulta. Se você quiser uma capacidade de consulta genéri-

ca no mapeador genérico, você pode ter uma consulta que receba uma cláusula SQL
where como parâmetro.
class Mapeador...
public Set encontrarObjetosOnde (String cláusulalhere) {

String sql = "SELECT" + mapaDeDados.listaDeColunas( ) + " FROM "+ mapaDeDados.
lerNomeDaTabela( ) + * WHERE " + cláusulaihere;
PreparedStatement dec = null;
ResultSet rs = null;

Set resultado = new HashSet ( ) ;
try {

dec = DB.prepare (sql) ;
rs = dec.executeQuery| |;

resultado = carregarTudo(rs) ;

} catch (Exception e) {

throw new ApplicationBxception (e);

} finally {DB.cleanUp(dec,

)

rs);

return resultado;

}

public Set carregarTudo (ResultSet rs) throws SQLException, InstantiationException, IL

legalAccessException {
Set resultado = new HashSet ( );

while (rs.next()) {
ObjetoDoDonánio novodbj = (ObjetoDoDoninio) mapaDeDados. lerClasseDoDoninio( ) .newInstance( ) ;
novoObj = carregar (xs) ;

CarítuLo 13

}

e PADRÕES DE MAPEAMENTO EM METADADOS OBiETO-RELACIONAIS

303

resultado.
add (novoobj) ;

return resultado;

Uma alternativa é fornecer métodos de busca para casos especiais nos subtipos

do mapeador.

class MapeadordePessoa...
public Set encontrarSobrenomesComo

(String padrão)

(

String sql =
"SELECT " + mapaDeDados.listaDeColunas( ) +
* FROM * + mapaDeDados.lerNomeDaTabela( ) +
" WHERE

UPPER (sobrenome)

PreparedStatement dec = null;

like

UPPER(?)";

ResultSet rs = null;

try {

dec = DB.prepare (sql)

dec.setString(1, padr: 1;
rs = ec. executequery( );
retum carregarTudo
(rs) ;

}

Uma alternativa adicional para seleções gerais é um Objeto de Pesquisa (304).
No geral, a grande vantagem da abordagem de metadados é que posso agora
adicionar novas tabelas e classes ao meu mapeamento de dados, e tudo o que tenho

que fazer é fornecer um método carregarMapaDeDados e quaisquer métodos de busca especializados que eu possa conceber.

epe)

} finally ( DB.cleantp(dec, rs);

ojuatueadi

) catch (Exception e) { throw new Applicationxception (e);

304

Parte ll e Os Panrões

Objeto de Pesquisa (Query Object)

Um objeto que representa uma consulta ao banco de dados.
:Consulta

:Critério
:Critério
operador = ">"
operador
campo = "númeroDeDependentes | | campo = "sobrenome"
valor = 0
valor = "fowler"

2

A SQL pode ser uma linguagem complicada, e muitos desenvolvedores não estão
particularmente familiarizados com ela. Além disso, você precisa conhecer o esquema do banco de dados para formular consultas. Você pode evitar isso criando métodos de pesquisa especializados que escondam a SQL dentro de métodos parametrizados, mas isso torna difícil formular consultas mais especializadas. Isso também leva a duplicação nas declarações SQLse o esquema do banco de dados mudar.
Um Objeto de Pesquisa é um interpretador [Gang of Four], ou seja, uma estrutura de objetos que pode transformar-se em uma consulta SQL. Você pode criar esta

consulta fazendo referência a classes e campos em vez de tabelas e colunas. Dessa
maneira, aqueles que escrevem as consultas podem fazê-lo independentemente do

esquema do banco de dados, e alterações no esquema podem ser localizadas em um
único lugar.

Como Funciona
Um Objeto de Pesquisa é uma aplicação do padrão Interpretador destinado a representar
uma consulta SQL. Suas principais tarefas são permitir a um cliente formular consultas
de vários tipos e transformar essas estruturas de objetos na string SQL apropriada.
Para representar qualquer consulta, você precisa de um Objeto de Pesquisa flexivel. Muitas vezes, no entanto, as aplicações podem se virar com muito menos do que

a capacidade total do SQL, caso em que seu Objeto de Pesquisa pode ser mais simples.
Ele não será capaz de representar todas as coisas, mas pode satisfazer as suas necessidades específicas. Além disso, normalmente, não é mais trabalhoso melhorá-lo

quando for preciso mais capacidade do que é criar um Objeto de Pesquisa inteiramente apto desde o princípio. Como consegiiência, você deve criar um Objeto de Pesquisa
minimamente funcional para suas necessidades correntes e aperfeiçoá-lo à medida
que essas necessidades aumentarem.

Uma característica comum do Objeto de Pesquisa é que ele pode representar consultas na linguagem dos objetos na memória em vez da linguagem do esquema do
banco de dados. Isso significa que, em vez de usar nomes de tabelas e colunas, você

pode usar nomes de objetos e campos. Embora isso não seja importante se os seus ob-

CapiTuLo 13

e PADRÕES DE MAPEAMENTO EM METADADOS OBiETO-RELACIONAIS

305

jetos e banco de dados tiverem a mesma estrutura, pode ser muito útil se você tiver

variações entre os dois. Para efetuar essa mudança de visão, o Objeto de Pesquisa pre-

cisa saber como a estrutura do banco de dados é mapeada para a estrutura dos objetos, uma habilidade que realmente precisa do Mapeamento em Metadados (295).
Para bancos de dados múltiplos, você pode projetar seu Objeto de Pesquisa de modo que ele produza strings SQLs diferentes dependendo do banco de dados sobre o

qual a pesquisa está sendo executada. No caso mais simples, isso pode dar conta das

aborrecidas diferenças na sintaxe SQL que continuam surgindo. Em um nível mais ambicioso, um Objeto de Pesquisa pode usar diferentes mapeamentos para lidar com as
mesmas classes sendo armazenadas em diferentes esquemas de banco de dados.

Um uso especialmente sofisticado do Objeto de Pesquisa é para eliminar pesqui-

sas redundantes sobre um banco de dados. Se você perceber que executou a mesma
consulta anteriormente em uma sessão, você pode usá-la para selecionar objetos do
Mapa de Identidade (196) evitando assim uma ida ao banco de dados. Uma abordagem

mais sofisticada pode detectar se uma pesquisa é um caso particular de uma pesquisa anterior, tal como uma consulta que é idêntica a uma consulta anterior a menos de
uma cláusula adicional acrescentada por um “AND”.

A maneira exata de obter essas características mais sofisticadas está além do es-

copo deste livro, mas elas são o tipo de característica que as ferramentas de mapea-

mento O/R podem fornecer.

Uma variação do Objeto de Pesquisa consiste em permitir que uma consulta seja
especificada por um exemplo de objeto de domínio. Assim, você poderia ter um objeto pessoa cujo sobrenome fosse gravado como Fowler, mas todos os outros atributos fossem gravados como nulos. Você poderia tratá-lo como um exemplo de consulta o qual é processado como o Objeto de Pesquisa do estilo Interpretador. Isso retorna

todas as pessoas no banco de dados cujo sobrenome seja Fowler, e é muito simples e
conveniente de usar. Entretanto, isso não funciona com pesquisas complexas.
Quando

Usá-lo
Os Objetos de Pesquisa consistem em um padrão bastante sofisticado para construir, então, a maioria dos projetos não os usa, se eles tiverem uma camada de fonte de dados

criada à mão. Você só precisa realmente deles quando você estiver usando um Mode-

lo de Domínio (126) e um Mapeador de Dados (170). Você realmente também precisa de

um Mapeamento em Metadados (295) para fazer um uso sério dos Objetos de Pesquisa.
Mesmo nesses casos os Objetos de Pesquisa não são sempre necessários, já que muitos desenvolvedores se sentem confortáveis com SQL. Você pode esconder muitos dos
detalhes do esquema do banco de dados atrás de métodos específicos de busca.
As vantagens do Objeto de Pesquisa vêm com necessidades mais sofisticadas:
manter esquemas de bancos de dados encapsulados, suportar múltiplos bancos de

dados, suportar múltiplos esquemas e otimizar para evitar múltiplas consultas. Alguns projetos com uma equipe de fonte de dados particularmente sofisticada poderia querer construir ela mesma estas capacidades, mas a maior parte das pessoas que
usam Objeto de Pesquisa o fazem com uma ferramenta comercial. Tendo a achar que

quase sempre você fica melhor comprando uma ferramenta.

Dito tudo isto, você pode descobrir que um Objeto de Pesquisa limitado satisfaz

suas necessidades, e não é difícil construir em um projeto que não justifique uma versão integral do mesmo. O artifício aqui é reduzir a funcionalidade a não mais do
aquilo que você realmente usa.

306

Parrell

e Os PADROES

Leitura Adicional
Você pode encontrar um exemplo de Objeto de Pesquisa em [Alpert et al.) na discussão
dos interpretadores. O Objeto de Pesquisa também está intimamente ligado ao padrão
Especificação em [Evans e Fowler] e [Evans].

Exemplo: Um Objeto de Pesquisa Simples (Java)
Este é um exemplo simples de um Objeto de Pesquisa — na verdade menos do que se-

ria útil na maior parte das situações, mas o suficiente para lhe dar uma idéia do que
é um Objeto de Pesquisa. Ele pode pesquisar uma única tabela baseado em um conjunto de critérios ligados por um conectivo “AND” (em uma linguagem ligeiramente
mais técnica, ele pode tratar uma conjunção de predicados elementares).
O Objeto de Pesquisa é configurado usando a linguagem dos objetos do domínio em
vez daquela da estrutura da tabela. Assim, uma pesquisa sabe a que classe ela pertence

e um conjunto de critérios que correspondem às cláusulas de uma cláusula where.

class ObjetoDePesquisa...
private Class classe;
private List critério = new ArrayList ( );
Um critério simples é o que pega um campo e um valor e um operador SQL pa-

2

ra compará-los.
class Critérios...

private String operadorSgl;
protected String campo;
protected Object valor;
Para tornar mais fácil criar os critérios corretos, posso fornecer um método de

criação adequado.
class Critérios...

public static Critérios maiorQue (String nomeDoCampo, int valor) {

return Critérios .maiorQue (noneDoCanpo, new Integer (valor));

public static Critérios maiorQue

}

(String nomeDoCampo,

return new Critérios(* >", nomeDoCanpo, valor) ;

Object valor)

{

private Critérios (String sql, String campo, Object valor) {

this.operadorSgl = sql;
this.campo = campo;
this.valor = valor;

Isso me permite encontrar todas as pessoas com dependentes formando uma
pesquisa tal como
class Critérios...

ObjetoDePesquisa pesquisa = new ObjetoDePesquisa (Pessoa. class) ;
pesquisa adicionarcritérios (Critérios.maiorQue(*nimeroDeDependentes", 0));

CapituLo 13

e PADRÕES DE MAPEAMENTO EM METADADOS OBiETO-RELACIONAIS

307

Assim, se eu tiver um objeto pessoa como este:
class Pessoa...

private String sobrenome;
private String prenome;
private int númeroDeDependentes;
Posso solicitar todas as pessoas com dependentes, criando uma pesquisa para
pessoas e adicionando um critério.

ObjetoDePesquisa pesquisa = new ObjetoDePesquisa (Pessoa.class) ;
pesquisa.adicionarCritérios (Critérios .maiorque ("núneroDeDependentes", 0));
Isso é suficiente para descrever a pesquisa. Agora essa pesquisa precisa ser executada transformando-se a si mesma em um select SQL. Neste caso, parto do princí-

pio de que minha classe mapeadora suporta um método que busque objetos basea-

do em uma string que corresponda a uma cláusula where.

class ObjetoDePesquisa...
public Set executar

(UnidadeDeTrabalho udt)

(

this.udt = udt;
return udt .lerMapeador (classe) .encontrarObjetosOnde (geraCláusulanhere( }) ;

}
class Mapeador....

public Set encontrarobjetosOnde (String cláusulanhere) (
String sql = "SELECT" + mapaDeDados.listaDeColunas( } + " FROM " + mapaDeDados.

lerNoneDaTabela( ) + * WHERE " + cláusulanhere;
Preparedstatement dec = null;
ResultSet rs = null;
Set resultado = new HashSet ( );

try {
dec = DB. prepare (sql);
rs = dec.executeQuery( );
resultado = carregarTudo(rs) ;
} catch (Exception e) {

throw new ApplicationBxception (e) ;

) finally {DB,cleanUp(dec,

}

rs) ;

return resultado;

}

Aqui estou usando uma Unidade de Trabalho (187) que armazena mapeadores indexados pela classe e um mapeador que usa Mapeamento em Metadados (295). O código é igual àquele do exemplo do Mapeamento em Metadados (295), para evitar a repetição de código nesta seção.

Para gerar a cláusula where, a consulta percorre cada um dos critérios e faz com
que cada um imprima a si próprio, juntando-os com um conectivo “AND”.

class ObjetoDePesquisa. ..
private String gerarCláusulanhere

() {

308

Parrell

e Os PADROES

StringBuffer resultado = new StringBuffer ( );

for (Iterator it = critérios.iterator( ); it.hasNext( ); ) {

Critérios c = (Critérios) it.next( );
if (resultado. length( ) !=0)

}

}

resultado. append(* AND ");
resul
append
(*c.gerarSgl (udt .lerMapeador (classe) . lerMapaDeDados( )));
tado.

return resultado. toString{ );

class Critérios...

public String gerarSql

(MapaDeDados mapaDeDados)

(

return mapaDeDados. lerColunaParaCampo (campo) + operadorSgl + valor;

}
class MapaDeDados...

public String lerColunaParaCampo (String nomeDoCampo)

{

for (Iterator it = lerColunas( ); it.hasNext( ); ) {

MapaDeColunas mapaDeColunas = (MapaDeColunas) it.next( );
if (mapaDeColunas.lerNomeDoCampo( } .equals (nomeDoCampo) )

}

return mapaDeColunas. lerlioneDaColuna( } ;

throw new ApplicationException ("Não foi encontrada a coluna para * + noweDoCampo) ;
e
3

Assim como critérios com operadores SQL simples, podemos criar classes de

critérios mais complexas que façam um pouco mais. Considere uma busca por um

padrão de letras que não diferencie letras maiúsculas de minúsculas, como a que
busca todas as pessoas cujos sobrenomes comecem com a letra F. Podemos formar

um objeto de pesquisa para encontrar todas as pessoas com tais dependentes.

ObjetoDePesquisa pesquisa = new ObjetoDePesquisa (Pessoa.class) ;
pesquisa adicionarcritérios
(Critérios naiorque ("númeroDeDependentes", 0)) ;
pesquisa adicionarCritérios (Critérios.éIgual ("sobrenone", "£%"));
O Objeto de Pesquisa usa agora uma classe de critérios diferente que forma uma
cláusula mais complexa na declaração where.
class Critérios...

public static Critérios éIgual (String nomeDoCampo, String padrão)
return new CritérioDeIgualdade (nomeDoCampo, padrão) ;

(

}

class CritérioDeIgualdade extends Critérios...

public String gerarSql (MapaDeDados mapaDeDados) (
return "UPPER(" + mapaDeDados. lerColunaParaCampo (campo)

}

+ ") LIKE UPPER(‘" + valor + "')";

CapituLo 13

e PADRÕES DE MAPEAMENTO EM METADADOS OBJETO-RELACIONAIS

309

Repositório (Repository)

por Edward Hieatt e Rob Mee

Faz a mediação entre as camadas de dominio e de mapeamento de dados
usando uma interface de tipo coleção para acessar objetos do domínio.
cliente

|

o repositório

novo

|

umCritério

As
;

igual (this, BENFEITOR)

igualando
(umCri

\
1
1
1
1
)
x'

|

|.

T
\
1
1
\
\

1

|

1(
1

uma Pessoa

T
1
1
1
1
1

T

i

i

1

1i
igualando |
(umCritério) |
T
pessoas que |
satisfizeram 1
ocritério |1
i

)
1
1
1
1

1

* satisfaz
(umCritério)

[o

1i
1
1
1

T
1
1
'1
1

Um sistema com um modelo de dominio complexo muitas vezes se beneficia de uma

camada, como a fornecida pelo Mapeador de Dados (170), que isola os objetos do do-

minio dos detalhes do código para acesso ao banco de dados. Em tais sistemas, pode
valer a pena construir uma camada adicional de abstração sobre a camada de mapea-

mento em que o código de criação de consultas fica concentrado. Isso se torna mais
importante quando há um grande número de classes do domínio ou consultas pesa-

das. Nestes casos, em especial, a adição desta camada ajuda a minimizar a lógica de

consulta duplicada.

Um Repositório realiza a mediação entre as camadas mapeadoras de dados e as
camadas de domínio, agindo como uma coleção de objetos de domínio em memória.
Os objetos clientes criam especificações de consultas declarativamente e as submetem ao Repositório para que sejam satisfeitas. Objetos podem ser adicionados e removidos do Repositório, assim como o podem ser de uma simples coleção de objetos, e o

código de mapeamento encapsulado pelo Repositório executará as operações apro-

priadas por trás dos panos. Conceitualmente, um Repositório encapsula o conjunto de
objetos persistidos e as operações executadas sobre eles, fornecendo uma visão mais

orientada a objetos da camada de persistência. O Repositório também dá suporte ao

objetivo de alcançar uma separação limpa e uma dependência unidirecional entre as
camadas de domínio e as camadas mapeadoras de dados.

310

Parte Il e Os PaprOes

Como Funciona

Repositório é um padrão sofisticado que faz uso de um número razoável de outros pa-

drões descritos neste livro. De fato, ele se parece com um pequeno banco de dados

orientado a objetos e, neste sentido, ele é semelhante ao Objeto de Pesquisa (304), o
qual é mais provável de ser encontrado pelas equipes de desenvolvimento em uma

ferramenta comercial para o mapeamento objeto-relacional do que construir um elas

mesmas. Entretanto, se uma equipe aceitou o desafio e construiu ela mesma um Objeto de Pesquisa (304), não é um passo muito grande adicionar a capacidade de um Repositório. Quando usado em conjunto com o Objeto de Pesquisa (304), o Repositório

acrescenta sem muito esforço, uma grande quantidade de aplicabilidade à camada
de mapeamento objeto-relacional.
Apesar de toda o mecanismo por trás dos panos, o Repositório apresenta uma interface simples. Os clientes criam um objeto critérios especificando as características
dos objetos que eles querem que sejam retornados por uma pesquisa. Por exemplo,
para encontrar objetos pessoa pelo nome, primeiro criamos um objeto critérios, con-

figurando cada critério individual apropriadamente: critérios .éIgual (Pessoa. SOBRENO-

ME,

"Fowler") e critérios como (Pessoa. PRENOME,

"M"). Então chamamos repositório.aten-

dendo (critérios) para retornar uma lista de objetos do domínio representando pessoas

com o sobrenome Fowler e um prenome começando com M. Diversos métodos convenientes semelhantes a atendendo (critérios) podem ser definidos em um repositó-

rio abstrato. Por exemplo, quando apenas uma combinação é esperada tinicoAtenden-

do(critérios) poderia retornar o objeto encontrado em vez de uma coleção. Outros

métodos comuns incluem peloIDdoObjeto
(id), o qual pode ser implementado trivial-

mente usando únicoAtendendo.

Para o código que usa um Repositório, ele se parece com uma coleção simples de

objetos do domínio em memória. O fato dos objetos do domínio geralmente não se-

rem armazenados diretamente no Repositório não é exposto ao código cliente. É claro

que o código que usa o Repositório deve estar ciente de que esta coleção aparente de
objetos pode muito bem mapear uma tabela produto com centenas de milhares de registros. Invocar o método todos( ) no RepositórioDeProdutos de um sistema de catálogo

pode não ser uma boa idéia.

O Repositório substitui métodos especializados de busca em classes do tipo Mapeador de Dados (170) por uma abordagem baseada em especificações para a seleção

de objetos [Evans e Fowler]. Compare isso com o uso direto do Objeto de Pesquisa
(304), no qual o código no cliente pode construir um objeto critérios (um exemplo
simples do padrão especificação), adicionar( | o mesmo diretamente ao Objeto de
Pesquisa (304) e executar a consulta. Com um Repositório, o código cliente constrói os

critérios e então os passa para o Repositório, pedindo a ele para selecionar dentre os

seus objetos, aqueles que satisfaçam aos critérios. Da perspectiva do código do

cliente, não existe a noção de “execução” de uma consulta. Em vez disso, ocorre a

seleção dos objetos apropriados por meio da “satisfação” da especificação da pesquisa. Isso pode parecer uma distinção acadêmica, mas ilustra o sabor declarativo

da interação dos objetos com o Repositório, o que é uma parte signgificativa do seu
poder conceitual.

Por trás dos panos, o Repositório combina o Mapeamento em Metadados (295) com

um Objeto de Pesquisa (304) para gerar automaticamente código SQL a partir dos cri-

térios. Se os critérios sabem como adicionar a si mesmos a uma consulta, se o Objeto
de Pesquisa (304) sabe como incorporar objetos critérios, ou se o próprio Mapeamento
em Metadados (295) controla a interação, é um detalhe de implementação.

CarituLo 13

e PADRÕES DE MAPEAMENTO EM METADADOS OBiETO-RELACIONAIS

311

A fonte de objetos para o Repositório não precisa necessariamente ser um banco

de dados relacional, o que é bom pois o Repositório é bastante apropriado para a substituição do componente de mapeamento de dados por objetos de estratégia especia-

lizados. Por esta razão, ele pode ser especialmente útil em sistemas com múltiplos es-

quemas de bancos de dados ou múltiplas fontes de objetos de domínio, assim como

durante os testes, quando o uso de objetos exclusivamente em memória é desejável

por motivos de velocidade.

O Repositório pode ser um bom mecanismo para melhorar a legibilidade e a clareza em um código que faz uso extensivo de consultas. Por exemplo, um sistema ba-

seado em um navegador, contendo muitas páginas de pesquisa, precisa de um meca-

nismo perfeito para transformar objetos HttpRequest em resultados de consultas. O có-

digo manipulador da solicitação pode, geralmente, sem muita complicação, conver-

ter o HttpRequest em um objeto critérios. Submeter os critérios ao Repositório apropria-

do deve requerer apenas uma linha ou duas de código adicional.
Quando

Usá-lo
Em um sistema grande, com muitos tipos de objetos de domínio e muitas pesquisas

possíveis, o Repositório reduz a quantidade de código necessário para lidar com to-

das as consultas que ocorrem. O Repositório promove o padrão Especificação (na forma do objeto critérios, nos exemplos mostrados aqui), o qual encapsula a consulta a
ser realizada de uma forma puramente orientada a objetos. Portanto, todo o código

para configurar um Objeto de Pesquisa em casos específicos pode ser removido. Os

clientes nunca precisam pensar em SQL e podem escrever código puramente em ter-

mos de objetos.

Entretanto, em situações com diversas fontes de dados, é onde realmente ve-

mos o Repositório ter sua oportunidade de mostrar o quanto é útil. Suponha, por
exemplo, que às vezes estejamos interessados em usar um armazenamento de dados simples em memória, comumente quando queremos executar um conjunto de

testes de unidade inteiramente em memória para obter melhor desempenho. Sem o

acesso a um banco de dados, muitos conjuntos de testes longos rodam significativa-

mente mais rápido. Criar um artefato para testes de unidade pode também ser mais
direto se tudo o que tivermos que fazer for criar alguns objetos do domínio e atirálos em uma coleção em vez de ter que gravá-los no banco de dados no início e excluí-los no final.

Também é possível, quando a aplicação estiver rodando normalmente, que cer-

tos tipos de objetos do domínio devam sempre ser armazenados em memória.

Um

exemplo de tal situação são os objetos de domínio imutáveis (aqueles que não podem ser alterados pelo usuário) os quais, uma vez em memória, devem permanecer
lá e nunca ser trazidos novamente. Como veremos mais adiante neste capítulo, uma

extensão simples do padrão Repositório permite que diferentes estratégias de consul-

ta sejam empregadas dependendo da situação.

Outro exemplo, no qual o Repositório poderia ser útil, é quando um alimentador

de dados é usado como fonte de objetos do domínio — digamos, um fluxo XML pela
Internet, talvez usando SOAP, poderia estar disponível como fonte. Uma EstratégiaDeRepositórioAlimentadaPorXML poderia ser implementada para ler dados do fluxo e
criar objetos do domínio a partir do XML.

312

Parte ll e Os Parões

Leitura Adicional
O padrão Especificação não tem ainda uma boa fonte de referência. A melhor descri-

ção publicada até o momento é [Evans e Fowler]. Uma descrição melhor está atualmente sendo preparada por [Evans].

Exemplo: Encontrando os Dependentes de uma Pessoa (Java)
Da perspectiva do objeto cliente, usar o Repositório é simples. Para recuperar seus dependentes do banco de dados, um objeto pessoa cria um objeto critérios representando os critérios de busca e os envia ao Repositório apropriado.

public class Pessoa {
public List dependentes ( ) (
Repositório repositório = Registro. repositórioPessoa( );
Critérios critérios = new Critérios( );
critérios. igual (Pessoa.BENFEITOR,

this);

return repositório. satisfazendo (critérios) ;
Pesquisas comuns podem ser supridas com subclasses especializadas do Repo-

jo. No exemplo anterior, poderíamos criar uma subclasse de Repositório, Reposi!

rioPessoa, e mover a criação do critério de pesquisa para o próprio Repositório.
public class RepositórioPessoa extends Repositório {
public List dependentesDe

(Pessoa umaPessoa)

Critérios critérios = new Critérios( );
critérios.
igual (Pessoa. BENFEITOR,

return satisfazendo (critérios);

{

umaPessoa) ;

O objeto pessoa então chama o método dependentes ( | diretamente no seu Repo-

sitório.

public class Pessoa {

public List dependentes( ) (
return Registro.repositérioPessoa| ) .dependentesDe (this) ;
}
Exemplo: Trocando Estratégias de Repositório (Java)
Uma vez que a interface do Repositório isola a camada do domínio da fonte de dados,

podemos refatorar a implementação do código da consulta dentro do Repositório sem

alterar quaisquer chamadas dos clientes. De fato, o código do domínio não precisa se
preocupar com a fonte ou com o destino dos objetos do domínio. No caso do armazenamento em memória, queremos alterar o método satisfazendo(

) para selecionar,

a partir de uma coleção, os objetos do domínio que satisfaçam os critérios. Entretan-

CapituLo 13

e PADRÕES DE MAPEAMENTO EM METADADOS OBiETO-RELACIONAIS

313

to, não estamos interessados em alterar permanentemente a fonte de dados usada,

mas, em vez disso, em poder escolher à vontade entre diversas fontes de dados. Vem
daí a necessidade de alterar a implementação do método satisfazendo ( ) para delegar
a um objeto estratégia o qual executa a pesquisa. O poder disso, é claro, é que pode-

mos ter diversas estratégias e podemos estabelecer a estratégia conforme desejado.
No nosso caso, é apropriado ter duas delas: uma EstratégiaRelacional, que busca no
banco de dados, e uma EstratégiaEmMemória, que busca na coleção de objetos do domi-

nio em memória. Cada estratégia implementa a interface EstratégiaDeRepositério, que

expõe o método satisfazendo( ), de modo que obtemos a seguinte implementação da

classe Repositório:

abstract class Repositório {

private EstratégiaDeRepositório estratégia;

protected List satisfazendo (Critérios algunsCritérios)

}

return estratégia. satisfazendo (algunsCritérios) ;

{

Uma EstratégiaRelacional implementa satisfazendo(

) criando um Objeto de Pes-

quisa a partir dos critérios e, então, pesquisando o banco de dados usando este Objeto de Pesquisa. Podemos configurá-lo com os campos e valores apropriados conforme
definidos pelos critérios, pressupondo aqui que o Objeto de Pesquisa sabe como povoar a si próprio a partir dos critérios.
public class EstratégiaRelacional implements EstratégiaDeRepositório {

protected List satisfazendo (Critérios critérios) (
Pesquisa pesquisa = new Pesquisa (classeDoMeuObjetoDoDoninio| )]
pesquisa.adicionarCritérios (critérios) ;
return pesquisa executar (unidadeDeTrabalho( |);

Uma EstratégiaBntenória implementa percorrendo a coleção de objetos do domínio e perguntando aos critérios para cada objeto do domínio se estes são satisfeitos
por este objeto. Os critérios podem implementar o código que valida os objetos usando reflexão para inquirir os objetos do domínio sobre os valores de campos especi
cos. O código para fazer a seleção se parece com este:
public class EstratégiaBmitemória implements EstratégiaDeRepositério

private Set objetosDoDomínio;

protected List satisfazendo (Critérios critérios)

List resultados = new ArrayList ( |;

Iterator it = objetosDoDomínio. iterator(

);

(

while (it.hasNext( )) {
ObjetoDoDominio cada = (ObjetoDoDomínio) it.next( );
if (critérios.séoSatisfeitosPor
(cada) )
resultados. add cada) ;

}

return resultados;

(

CAPITULO

1

4

Padrões de Apresentação Web

CapituLo 14 e PADRÕES DE APRESENTAÇÃO WEB

315

Modelo Vista Controlador (Model View Controller)
Divide a interação da interface com o usuário em três papéis distintos.

Vista

Controlador

O Modelo Vista Controlador (MVC) é um dos padrões mais citados (e mais citados in-

devidamente). Ele começou como um framework desenvolvido por Trygve Reens-

kaug para a plataforma Smalltalk no final dos anos 70. Desde então ele tem exercido

um papel de influência na maioria dos frameworks para a interface com o usuário e no

pensar sobre o projeto de interfaces com o usuário.
Como Funciona
O MVC considera três papéis.

O modelo é um objeto que representa alguma infor-

mação sobre o domínio. É um objeto não-visual contendo todos os dados e compor-

tamento que não os usados pela interface de usuário. Na sua forma OO mais pura, o

modelo é um objeto dentro de um Modelo de Domínio (126). Você também poderia
pensar em um Roteiro de Transação (120) como o modelo, desde que ele não contenha

nenhum mecanismo de interface com o usuário. Tal definição amplia a noção de mo-

delo, mas se adapta à divisão de papéis do MVC.
A vista representa a exibição do modelo na interface com o usuário. Assim, se

nosso modelo for um objeto cliente nossa vista poderia ser um frame cheio de contro-

les para a interface com o usuário ou uma página HTML com informações do modelo. A vista diz respeito apenas à apresentação de informações, quaisquer alterações
nessas informações são manipuladas pelo terceiro membro da tríade MVC: o controlador. O controlador recebe a entrada do usuário, manipula o modelo e faz com que

a vista seja atualizada apropriadamente. Dessa forma, a interface de usuário é uma

combinação da vista e do controlador.

Quando penso sobre o MVC, vejo duas separações principais: separar a apre-

sentação do modelo e separar o controle da vista.

Destas, separar a apresentação do modelo é uma das heurísticas mais funda-

mentais do bom projeto de software. Esta separação é importante por diversas razões.

* Fundamentalmente, a apresentação e o modelo referem-se a preocupações
diferentes. Quando você está desenvolvendo uma vista, você está pensando

nos mecanismos de interface com o usuário e em como construir uma boa in-

