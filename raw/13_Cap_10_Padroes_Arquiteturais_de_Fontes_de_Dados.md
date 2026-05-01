# 13_Cap_10_Padroes_Arquiteturais_de_Fontes_de_Dados

Capiruto 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

um mapeador

martin: Pessoa

de pessoa

atualizar (martin)

id=1

T

|

-

ler muitos dados

DT

o banco de dados

T
1
1
1
|

T
update pessoa set... where id =

Figura 10.4

173

g

i

Atualizando dados.

aplicação grande, pode ser excessivo ter um único mapeador com muitos métodos
de busca, de modo que faz sentido dividir estes métodos por classe de domínio ou
raiz de hierarquia de domínio. Desse modo, você obtém um número grande de pequenas classes de busca, mas é mais fácil para um desenvolvedor localizar o método

de busca de que ele precisa.

Assim como com qualquer procedimento de busca em um banco de dados, os

métodos de busca precisam usar um Mapa de Identidade (196) a fim de persistir a

identidade dos objetos lidos do banco de dados. Você pode usar um Registro (448)

ou Mapas de Identidade (196), ou você pode fazer cada método de busca armazenar

um Mapa de Identidade (196) (supondo que exista apenas um método de busca por

classe por sessão).

Manipulando Métodos de Pesquisa Para trabalhar com um objeto, você tem que

carregá-lo do banco de dados. Normalmente a camada de apresentação dá início ao
processo carregando alguns objetos iniciais. O controle passa então para a camada de
domínio, momento em que o fluxo de controle do código passa a se mover de objeto
em objeto usando as associações entre eles. Este mecanismo funciona efetivamente

desde que a camada de domínio tenha todos os objetos de que precisa carregados na

memória, ou então que você use a Carga Tardia (200) para carregar objetos adicionais

quando necessário.

Ocasionalmente, você pode precisar que os objetos do domínio invoquem métodos de busca no Mapeador de Dados. No entanto, descobri que, com uma boa Carga
Tardia (200), você pode completamente evitar isso. Para aplicações mais simples, no
entanto, pode não valer a pena tentar gerenciar tudo com associações e Carga Tardia

(200). Ainda assim, não é desejável introduzir uma dependência dos seus objetos de

domínio com o seu Mapeador de Dados.

Você pode resolver esse dilema usando uma Interface Separada (445). Ponha to-

dos os métodos de busca necessários para o código do domínio em uma classe de interface que você pode colocar no pacote do domínio.

174

Parte Il e Os PaprOes

Mapeando Dados em Atributos do Dominio

Os mapeadores precisam ter acesso

aos atributos nos objetos do dominio. Isso, frequentemente pode ser um problema,

porque você precisa de métodos públicos para dar suporte aos mapeadores que não

são necessários para a lógica do domínio. (Estou pressupondo que você não irá co&
s

8

meter o pecado capital de tornar os atributos públicos.) Não existe uma resposta fá-

cil para este problema. Você poderia usar um nível mais baixo de visibilidade empa-

cotando os mapeadores próximo aos objetos do domínio (o que corresponderia em
Java a colocá-los no mesmo pacote), mas isso confunde o cenário dependências, porque você não quer que outras partes do sistema que conheçam os objetos do domí-

nio conheçam também os mapeadores. Você pode usar reflexão, que freqiientemen-

te pode contornar as regras de visibilidade da linguagem. Isso é mais lento, mas a

velocidade menor pode ser tão insignificante quanto um erro de arredondamento,

comparada ao tempo gasto pela chamada SQL. Você pode ainda usar métodos públicos, mas protegê-los com um campo de status de modo que eles gerem uma exce-

ção se forem usados fora do contexto de uma carga do banco de dados. Neste caso,
nomeie-os de modo que não sejam confundidos com métodos de acesso regulares
(getters e setters).
Associada a esta questão, está a da criação do objeto. Em essência, você tem

duas opções. Uma é criar o objeto com um construtor rico de modo que ele seja criado, ao menos, com todos os dados obrigatórios. A outra opção é criar um objeto vazio e então povoá-lo com os dados obrigatórios. Normalmente prefiro a primeira

opção, uma vez que é bom ter um objeto bem formado desde o início. Isso também

significa que, se você tiver um campo imutável, você pode garantir que ele não será

alterado, simplesmente não fornecendo nenhum método para alterar seu valor.

O problema com um construtor rico é que você tem de estar ciente das referên-

cias cíclicas. Se você tiver dois objetos que referenciam um ao outro, cada vez que vo-

cê tentar carregar um deles, ele tentará carregar o outro, que por sua vez tentará carregar o primeiro, e assim por diante, até que você fique sem espaço na pilha. Para
evitar esse problema, é necessário um código condicional especial, freqiientemente

usando a Carga Tardia (200). A codificação deste código condicional especial é confu-

sa, de modo que vale a pena tentar passar sem isso. Você pode fazê-lo criando um
objeto vazio. Use um construtor sem parâmetros para criar um objeto em branco e
insira esse objeto vazio imediatamente no Mapa de Identidade (196). Desta forma, se

você tiver um ciclo, o Mapa de Identidade (196) retornará um objeto de modo a inter-

romper a carga recursiva.
Usar um objeto vazio como esse significa que você pode precisar de alguns mé-

todos de escrita para os valores que forem verdadeiramente imutáveis quando o ob-

jeto for carregado. Uma combinação de uma convenção de nomes apropriada e, tal-

vez, algumas sentinelas para verificação de status pode resolver isso. Você também
pode usar reflexão para a carga de dados.
Mapeamentos Baseados em Metadados

Uma das decisões que você precisa tomar

com respeito ao armazenamento da informação é como os atributos nos objetos do

domínio são mapeados para colunas no banco de dados. A maneira mais simples, e
muitas vezes a melhor, de fazer isso é com código explícito, o que requer uma classe

de mapeamento para cada objeto do domínio. O mapeador realiza o mapeamento

por meio de atribuições e tem campos (usualmente strings constantes) para armazenar a consulta SQL para acesso ao banco de dados. Uma alternativa é usar o Mapeamento de Metadados (295), que armazena os metadados como dados, em uma classe

CapituLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

175

ou em um arquivo separado. A grande vantagem dos metadados é a de que todas as

variações nos mapeadores podem ser tratadas por meio de dados sem a necessidade

de código fonte adicional, seja pela geração de código ou por meio de programação

reflexiva.

Quando Usá-lo

A principal ocasião para usar o Mapeador de Dados é quando você quiser que o esque-

ma do banco de dados e o modelo de objetos evoluam independentemente. A situa-

ção mais comum para isso é com um Modelo de Domínio (126). O principal benefício
do Mapeador de Dados é que, quando você estiver trabalhando no modelo do domí-

nio, você pode ignorar o banco de dados, tanto na fase de projeto quanto na de construção e teste. Os objetos do domínio não têm qualquer conhecimento da estrutura

do banco de dados porque toda a correspondência é feita pelos mapeadores.

Isso lhe ajuda no código porque você pode entender e trabalhar com os objetos
do domínio sem ter que entender como eles estão armazenados no banco de dados.

Você pode modificar o Modelo do Domínio (126) ou o banco de dados sem ter que alterar o outro. Em mapeamentos complicados, especialmente aqueles envolvendo

bancos de dados já existentes, isso é muito valioso.

O preço, é claro, é a camada adicional não-existente se você usa o Registro Ativo
(165), de modo que a chave para o uso ou não deste padrão é a complexidade da ló-

gica de negócio. Se sua lógica de negócio for razoavelmente simples, você provavel-

mente não precisa de um Modelo de Domínio (126) ou de um Mapeador de Dados. Lógi-

ca mais complicada leva-o a um Modelo de Dominio (126) e, consequentemente, a um

Mapeador de Dados.

Eu não escolheria um Mapeador de Dados sem um Modelo de Domínio (126), mas

posso usar o Modelo de Domínio (126) sem um Mapeador de Dados? Se o modelo do do-

minio for bastante simples e o banco de dados estiver sob o controle do desenvolvedor do modelo do domínio, então é razoável que os objetos do domínio acessem di-

retamente o banco de dados com o Registro Ativo (165). Efetivamente, isso coloca a ló-

gica de mapeamento descrita aqui nos próprios objetos do domínio. À medida que

as coisas se tornam mais complicadas, é melhor refatorar o código de acesso ao banco de dados em uma camada separada.
Lembre-se de que você não tem que criar uma camada completa de mapeamen-

to de dados. Esta é uma tarefa bastante complicada, e existem produtos disponíveis

que fazem isso para você. Na maioria dos casos, recomendo comprar uma camada
para o mapeamento no banco de dados em vez de criar uma você mesmo.

Exemplo: Um Mapeador Simples de Banco de Dados (Java)
Este exemplo mostra um uso absurdamente simples de um Mapeador de Dados para
lhe dar uma idéia da sua estrutura básica. Nosso exemplo consiste em uma classe

pessoa e uma tabela isomórfica pessoa.
class Pessoa...

private String sobrenone;
private String prenome;

private int númeroDeDependentes;

g

176

Parte ll e Os Pacrões

O esquema do banco de dados se parece com:

create table pessoas (ID int primary key, sobrenome varchar,
prenone varchar, númeroDeDependentes int)

Usaremos o caso simples aqui onde a classe Mapeador de Pessoa também imple-

menta o método de busca e o Mapa de Identidade (196). Entretanto, acrescentei um mapeador abstrato Camada Supertipo (444) para indicar para onde posso extrair algum

comportamento comum. A carga envolve a verificação de que o objeto já não esteja

no Mapa de Identidade (196) e, então, a busca dos dados a partir do banco de dados.
O comportamento de busca começa no Mapeador de Pessoa, o qual encapsula as
chamadas para um método abstrato de busca pelo ID.

class MapeadorDePessoa...
protected String declaraçãoDeBusca

}

(| (

return "SELECT " + COLUNAS +
* FROM pessoas" +
" WHERE id = 2";

public

static

final

String

COLUNAS

public Pessoa buscar (Long id)

=

" id,

sobrenome,

prenome,

númeroDeDependentes

";

return (Pessoa) buscanbstata (id);

)
public Pessoa buscar (long id) {
return buscar (new Long (id) );
}
class MapeadorAbstrato...
protected Map mapaCarregado = new Hashlap( );
abstract protected String declaraçãoDeBusca(

);

protected ObjetoDoDominio buscaRbstrata (Long id) (

ObjetoDoDonínio resultado = (ObjetoDoDomínio) mapaCarregado.ler
(id) ;
if (resultado != null) return resultado;
PreparedStatement declaraçãoDeBusca = null;
try{
declaraçãoDeBusca = DB.prepare (declaraçãoDeBusca( ));
declaraçãoDeBusca.setLong(1, id.longValue( ));
ResultSet rs = declaraçãoDeBusca.executeQuery( |;
rs.next( );

resultado = carregar (rs);
return resultado;

} catch (SQLException e) {

throw new ApplicationException (e);

} finally {

}

DB. cleanlp (declaraçãoDeBusca) ;

O método de busca chama o método de carga, o qual é dividido entre o mapea-

dor abstrato e o mapeador de pessoa. O mapeador abstrato verifica o ID, extraindo-o
dos dados e registrando o novo objeto no Mapa de Identidade (196).

Capiruto 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

177

class Mapeadorabstrato...
protected ObjetoDoDomínio carregar (ResultSet rs) throws SQLException {

Long id = new Long(rs.getLong(1)) ;
if [mapaCarregado. containskey(id)) return (ObjetoDoDonínio) mapaCarregado.
ler (id) ;
ObjetoDoDonínio resultado = fazerCarga(id, rs) ;
mapaCarregado.put (id, resultado);
return

resultado;

}
abstract protected ObjetoDoDonínio fazerCarga (Long id, ResultSet rs)
throws SQLException;

class MapeadorDePessoa....
protected ObjetoDoDomínio fazerCarga

(Long id, ResultSet rs) throws SQLException (

String parametroSobrenone = rs.getString(2] ;
String parâmetroPrenone = rs.getString(3) ;

int parâmetroNúmeroDeDependentes = rs.getInt
(4) ;

return new Pessoa (id, parâmetroSobrenone, parâmetroPrenone,
parânetroNúneroDeDependentes) ;
Perceba que o Mapa de Identidade (196) é verificado duas vezes, uma pelo méto-

do buscaAbstrata e outra método carregar. Há uma razão para esta loucura.

Preciso verificar o mapa no método de busca porque, se o objeto já estiver lá,

posso evitar uma ida ao banco de dados — sempre quero evitar essa ida ao banco
de dados, se puder. Contudo, também preciso verificar na carga porque é possível

que eu tenha consultas que não posso ter certeza de resolver no Mapa de Identidade (196). Suponha que eu queira encontrar todas as pessoas cujo sobrenome satis-

faça algum padrão de busca. Não posso ter certeza de que eu já tenha todas essas
pessoas carregadas, de modo que tenho que ir ao banco de dados e executar uma
consulta.

class MapeadorDePessoa...
private static String declaraçãoEncontrarSobrenome =
"SELECT " + COLUNAS +
* FROM pessoas " +
"

WHERE UPPER (sobrenome) like UPPER(?)" +

"ORDER BY sobrenone";

public List encontrarPeloSobrenome (String nome) {

PreparedStatement dec = null;
ResultSet rs = null;

try {

dec = DB.prepare (declaraçãoEncontrarSobrenone) ;
dec.setString (1, nome) ;
rs = dec.executeQuery( );

return carregarTodos (rs) ;
) catch (SQLException e) {

throw new ApplicationException

} finally {
DB.cleantp (dec, rs);
}

(e);

178

Parte ll e Os PaprOes

class MapeadorAbstato.
protected List carregarTodos (ResultSet rs) throws SQLException {
List resultado = new ArrayList ( );

while (rs.next( })
resultado. add (carregar
(rs) ) ;
return resultado;

3
s

8

Quando faço isso posso extrair algumas linhas do conjunto resultante que cor-

respondam a pessoas que eu já tenha carregado. Tenho que me assegurar de que eu
não faça uma duplicata, então tenho que verificar o Mapa de Identidade (196) novamente.

Escrever um método de busca dessa forma em cada subclasse que precise dele

implica em uma codificação básica, porém repetitiva, o que posso eliminar fornecen-

do um método genérico.

class Hapeadorabstrato...
public List encontrarMuitos (OrigemDaDeclaração origem)
PreparedStatement dec = null;

ResultSet rs = null;
try {

dec = DB.prepare(origem.sql(

(

|);

for(int i =0; ic origem.parânetros( ).length; i++)
rs =

dec.setObject
(i+1, origem.parametros( ) [il];

dec.executeQuery(

|;

return carregarTodos
(rs) ;

} catch (SQLException e) (

throw new ApplicationBxception (e);
) finally {
DB.cleanUp (dec, rs);
)
Para isto funcionar, preciso de uma interface que encapsule tanto a string SQL

quanto a carga dos parâmetros na declaração preparada.
interface OrigenDaDeclaração....
String sal;
Object [ ] parâmetros ();

Posso, então, usar esse recurso fornecendo uma implementação apropriada co-

mo uma classe interna.

class HapeadorDePessoa...
public List encontrarPeloSobrenome2
return encontrarMuitos

(String padrão)

(

(new EncontrarPeloSobrenome

(padrão) ) ;

static class EncontrarPeloSobrenome implements OrigemDaDeclaração(

private String sobrenone;

public EncontrarPeloSobrenome

(String sobrenome)

this.sobrenome = sobrenome;

{

CapiruLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

179

}

public String sql () {

return

"SELECT

" + COLUNAS

+

"

BY sobrenome";

* FROM pessoas " +
" WHERE UPPER (sobrenome) like UPPER(?)" +
)

ORDER

public Object [ | parâmetros

( ) (

Object [ ] resultado = {sobrenone};
retum resultado;

Esse tipo de trabalho pode ser executado em outros lugares onde houver códi-

go repetitivo de chamada de declarações. De modo geral, fiz os exemplos aqui mais
diretos para torná-los mais fáceis de seguir. Se você perceber que está escrevendo

uma grande quantidade de código direto repetitivo, deve considerar fazer algo semelhante.
Com a atualização, o código JDBC é específico para o subtipo.

class MapeadorDePessoa...
private static final String stringDaDeclaraçãoDeAtualização =

"UPDATE pessoas "+
"SET sobrenone 2 prenome
númeroDeDependentes = ? " +
"WHERE id = 2";
public void atualizar (Pessoa sujeito) {
PreparedStatement declaraçãoDentualização = null;
try{
declaraçãoDeatualização = DB.prepare (stringDaDeclaraçãoDeAtualização) ;
declaraçãoDeatual
ização. setString(1, sujeito.lerSobrenome()) ;
declaraçãoDeRtualização.setString(2, sujeito. lerPrenome()) ;
declaraçãoDeAtualização. set Int (3, sujeito. lerNúmeroDeDependentes ());

declaraçãoDentualização .setInt (4, sujeito. lerID( ) intvalue( ));
declaraçãoDeatualização .execute( );

) catch (Exception e) (

throw new ApplicationException

(e);

) finally {
DB. cleantlp (declaraçãoDeAtualização) ;

}

Para a inserção, algum código pode ser fatorado para a Camada Supertipo (444).

class Hapeadorabstrato...
public Long inserir

(ObjetoDoDomínio sujeito)

(

PreparedStatement declaraçãoDeInserção = null;
try {
declaraçãoDeInserção = DB.prepare (declaraçãoDeInserção( |);
sujeito. gravarID (encontrarPróximoIdNoBancoDeDados( }) ;
declaraçãoDeInserção.setInt

(1, sujeito.lerID(

fazerInserção (sujeito, declaraçãoDeInserção) ;

).intValue{

));

180

Parte Il e Os PaprOes

declaraçãoDeInserção.execute( ) ;
napaCarregado.put (sujeito.leriD( ), sujeito);
return sujeito.lerID( );

} catch (SQLException e) {

throw new ApplicationException

}

(e);

) finally {
DB, cleanllp (declaraçãoDeInserção) ;
}

abstract protected String declaraçãoDeInserção(

);

abstract protected void fazerInserção (ObjetoDoDomínio sujeito, PreparedStatement declaraçãoDeInserção)
throws SQLException;
class MapeadorDePessoa...
protected String declaraçãoDeInserção ( ) {

return "INSERT INTO pessoas VALUES (?, 2, ?, 2)";

}
protected void fazerInserção (
ObjetoDoDonínio sujeitoabstrato,
PreparedStatement dec)

{

throws SQLException

Pessoa sujeito = (Pessoa) sujeitonbstrato;

dec.setString (2, sujeito.lerSobrenome( ));

}

dec.setString (3, sujeito.lerPrenome( ));
dec.setInt (4, sujeito.lerNimeroDeDependentes(

));

Exemplo: Separando os Métodos de Busca (Java)

Para permitir que os objetos do domínio invoquem o comportamento de busca, posso usar uma Interface Separada (445) para separar as interfaces de busca dos mapeado-

res (Figura 10.5). Posso colocar estas interfaces de busca em um pacote separado que

seja visível à camada de domínio ou, como neste caso, posso colocá-las na própria camada do domínio.
Uma das pesquisas mais comuns é aquela que busca um objeto pelo ID que o

identifica. A maior parte deste processamento é bastante genérico, de modo que ele

pode ser tratado por uma Camada Supertipo (444) apropriada. Tudo que é preciso é

uma Camada Supertipo (444) para objetos do domínio que conheça os IDs.

A interface para pesquisas reside na interface para buscas. Normalmente, é melhor não torná-la genérica porque você precisa conhecer o tipo de retorno.

interface buscadorDeArtista...
Artista buscar (Long id);
Artista buscar (long id);

É melhor declarar a interface para buscas no pacote do domínio com os métodos de busca armazenados em um Registro (448). Neste caso, fiz com que a classe ma-

peadora implementasse a interface para buscas.

CapituLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

181

mapeador

domínio
Objeto do Domínio
id: Long

Mapeador Abstrato
Artista

+ inserir

+ atualizar
# buscaAbstrata
# carregar
# fazerCarga
Album
/

v
«interface»
Buscador de Artista

E eee

buscar (id)
Figura 10.5

Mapeador
de Artista
buscar (id)
* fazerCarga

Definindo uma interface de busca no pacote do domínio.

class MapeadorDeartista implements buscadorDeArtista..
public Artista buscar (Long ia) (

return (Artist) buscanbstrata (id);

}
public Artista buscar (long id) {
return buscar (new Long(id}) ;
}

A parte principal do método de busca é executada pela Camada Supertipo (444)

do mapeador, que verifica o Mapa de Identidade (196) para ver se o objeto já está na
memória. Se não estiver, ele completa um prepared statement (carregado pelo mapeador de artista) e o executa.

class Mapeadorabstrato...
abstract protected String declaraçãoDeBusca (|;
protected Map mapaCarregado = new HashMap( );
protected ObjetoDoDonínio buscanbstrata (Lon ia) {
o) mapaCarregado.get (id);
ObjetoDoDoníno resultado = (ObjetoDoDor
if (resultado != null) return resultado;
Preparedstatement dec = null;
ResultSet rs = null;

182

Parte ll e Os PaprOes

try {
dec = DB. prepare (declaraçãoDeBusca( }) ;
dec.setLong(1, id.longValue( |);
rs = dec.executeQuery| |;

re.next( );

resultado = carregar (rs);
return resultado;

} catch (SQLException e) (

throw new ApplicationException

}

} finally {cleanUp(dec,
)

rs);

class MapeadorDeartista...
protected String declaraçãoDeBusca ( ) {
return

"select

" + LIST
DE COLUNAS
A

+

" from artistas

art

where

ID = ?";

public static String LISTA
DE COLUNAS = “art.ID, art.nome";

A parte do comportamento referente à busca diz respeito à obtenção de um ob-

jeto existente ou de um novo. A parte referente à carga diz respeito a colocar os dados do banco de dados em um novo objeto.

class MapeadorAbstrato...
protected ObjetoDoDomínio carregar (ResultSet rs) throws SQLException {
Long id = new Long (rs.getLong (id"));

}

if (mapaCarregado.containskey (id)) return [ObjetoDoDonínio) mapaCarregado.get
(id) ;
ObjetoDoDonínio resultado = fazerCarga (id, rs);
mapaCarregado.put (id, resultado) ;
retum resultado;

abstract protected ObjetoDoDominio fazerCarga (Long id, ResultSet rs)
throws SQLException;

class MapeadorDeartista. ..
protected ObjetoDoDomínio fazerCarga

(Long id, ResultSet rs) throws SQLException {

String none = rs.getString ("nome") ;

Artista resultado = new Artista (id, nome);

return resultado;

Observe que o método de carga também verifica o Mapa de Identidade (196). Em-

bora, neste caso, isso seja redundante, a carga pode ser chamada por outros métodos

de busca que ainda não fizeram esta verificação. Neste esquema, tudo que uma sub-

classe tem que fazer é desenvolver um método fazerCarga para carregar os dados

reais necessários, além de retornar uma declaração apropriada a partir do método declaraçãoDeBusca.

Você também pode fazer uma busca baseada em uma consulta. Suponha que te-

nhamos um banco de dados de faixas e álbuns e queiramos um método de busca que
encontre todas as faixas em um álbum específico. Novamente, a interface declara os

métodos de busca.

CapituLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

183

interface buscadorDeFaixas...

Faixa buscar (Long id);
Faixa buscar (long id);

List buscarDoAlbum (Long idDoálbum) ;

Uma vez que este é um método de busca específico para esta classe, ele é imple-

mentado em uma classe específica, tal como a classe mapeadora de faixas, em vez de

em uma Camada Supertipo (444). Assim como com qualquer método de busca, exis-

tem dois métodos para a implementação. Um deles estabelece a expressão prepara-

da (prepared statement) e o outro encapsula a chamada para a expressão preparada e

interpreta os resultados.

class MapeadorDeFaixa...
public static final String declaraçãoBuscaDoálbum =
ECT ID, seq, idDoAlbum, titulo "+

"FROM faixas "+
"WHERE IDdoálbum = ? ORDER BY seq";

public List buscarPorAlbum (Long idDoálbum)

PreparedStatement dec = null;
ResultSet rs = null;
try {

{

dec = DB.prepare (declaraçãoBuscaDoálbum ) ;
dec.setLong (1, idDoAlbum.longValue( });

rs = dec.executeQuery( );

List resultado = new ArrayList ( );

while

(rs.next( |)

ado.(rs) );
result
add (carregar
return resultado;

} catch (SQLException e) {

throw new ApplicationException

) finally {cleanUp(dec, rs);

(e);

}

O método de busca chama um método de carga para cada linha no conjunto re-

sultante. Este método tem a responsabilidade de criar o objeto na memória e carregá-

lo com os dados. Como no exemplo anterior, parte disso pode ser tratada em uma Ca-

mada Supertipo (444), incluindo a verificação do Mapa de Identidade (196) para ver se

algo já está carregado.

Exemplo: Criando um Objeto Vazio (Java)
Há duas abordagens básicas para carregar um objeto. Uma é criar um objeto comple-

tamente válido com um construtor, que é o que fiz nos exemplos acima. Isso resulta

no seguinte código construtor:
class Mapeadorabstrato...

protected ObjetoDoDonínio carregar

(ResultSet rs) throws SQLException {

Long id = new Long(rs.getLong(1));
if (mapaCarregado.containskey
(id)} return (ObjetoDoDoninio) mapaCarregado.get
(id) ;
ObjetoDoDonínio resultado = fazerCarga(id, rs);

g

184

Parte ll e Os Pacrões

mapaCarregado.put (id, resultado) ;
return resultado;

}
abstract protected ObjetoDoDonínio fazerCarga (Long id, ResultSet rs) throws SQLException;
class MapeadorDePessoa...
protected ObjetoDoDomínio fazerCarga

(Long id, ResultSet rs) throws SQLException {

String parâmetrosobrenone = rs.getString(2) ;
String parânetroPrenome = rs.getString(3);

int parâmetroNúmeroDeDependentes = rs.get
Int (4) ;

return new Pessoa (id, pardmetroSobrenone, parânetroPrenone, parânet roNúmeroDeDependentes) ;

A alternativa é criar um objeto vazio e, mais tarde, carregá-lo com os métodos

de gravação.

class MapeadorAbstrato...
protected ObjetoDoDomínioBL carregar (ResultSet rs) throws SQLException {
Long id = new Long(rs.getLong(1)) ;

}

if (mapaCarregado.containsKey(id)) return (ObjetoDoDomínioEL) mapaCarregado.get
(id) ;
ObjetoDoDoníniogL resultado = criardbjetoDoDoninio( ) ;
resultado.gravarID
id) ;
mapaCarregado. put (id, resultado) ;
fazerCarga (resultado, rs) ;
return resultado;

abstract protected ObjetoDoDomínioEL criarObjetoDoDominio(

);

abstract protected void fazerCarga (ObjetoDoDomínioBL obj, ResultSet rs) throws SQLException;

class HapeadorDePessoa...
protected ObjetoDoDominioBL criarobjetoDoDonínio( ) {
return new Pessoa( );

}

protected void fazerCarga (ObjetoDoDomínioBL obj, ResultSet rs) throws SQLException {

Pessoa pessoa = (Pessoa) obj;
pessoa. carregarSobrenoneBD (rs.getString(2));
pessoa. gravarPrenone (rs.getString (3) ;
pessoa. gravarllineroDeDependentes (rs.getInt (4)) ;

Perceba que estou usando um tipo diferente de objeto de dominio, Camada Su-

pertipo (444) aqui, porque quero controlar o uso de métodos de gravação. Digamos

que eu queira que o sobrenome de uma pessoa seja um campo imutável. Neste caso,

não quero alterar o valor do campo, uma vez que este tenha sido gravado, então
acrescento um campo status no objeto do domínio.

class ObjetoDoDoniniokb. ..
private int estado = CARREGANDO;
private static final int CARREGANDO = 0;

private static final int ATIVO = 1;

public void fiqueativo ( ) {
estado = ATIVO;

}

CapiruLo 10

e PADRÕES ARQUITETURAIS DE FONTES DE DADOS (DATA Source)

185

Posso, então, verificar o valor deste durante uma carga.
class Pessoa...

public void carregarSobrenomeBD

assertEstadofcarregando ( );

}

(String sobrenome)

(

this.sobrenone = sobrenone;

&
g

class ObjetoDoDominioBL...
void assertEstadofCarregando ( ) {
Assert .isTrue

(estad

CARREGANDO) ;

O que me desagrada nisso é termos agora um método na interface que a
maioria dos clientes da classe Pessoa não pode usar. Este é um argumento para o ma-

peador usando reflexão gravar o campo, ignorando assim completamente os mecanismos de proteção Java.

A condição guarda baseada no status compensa o problema? Não estou inteiramente certo. Por um lado isso irá pegar falhas causadas por pessoas chamando mé-

todos de atualização na hora errada. Por outro lado, estas falhas são tão severas que

compensam o custo do mecanismo? No momento não tenho uma opinião sólida a favor de qualquer das opções.

CaríTULO

1

1

Padrões Comportamentais
Objeto-Relacionais

CapiTuLo 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

187

Unidade de Trabalho (Unit of Work)

Mantém uma lista de objetos afetados por uma

transação de negócio e coordena a gravação das alterações

ea resolução de problemas de concorrência.
Unidade de Trabalho

registrarNovo(objeto)
registrarSujo(objeto)
registrarLimpo(objeto)
registrarExcluído(objeto)

s

confirmar( )

3
Quando você está enviando dados de e para um banco de dados, é importante man-

ter registro do que você alterou, senão esses dados não serão gravados de volta no
banco de dados. De maneira similar, você tem que inserir novos objetos que criou e
remover quaisquer objetos que apagou.
Você pode alterar o banco de dados a cada mudança no seu modelo de objetos,

mas isso pode levar a muitas chamadas pequenas ao banco de dados, o que acaba

sendo muito lento. Além disso, requer que você tenha uma transação aberta para a
interação inteira, o que não é prático se você tiver uma transação que envia diversas

solicitações. A situação é até pior se você precisar manter registro dos objetos que leu
para que possa evitar leituras inconsistentes.
Uma Unidade de Trabalho mantém registro de tudo o que você faz durante uma

transação de negócio que possa afetar seu banco de dados. Quando você tiver termi-

nado, ela descobre tudo o que precisa ser feito para alterar o banco de dados como re-

sultado do seu trabalho.

Como Funciona
As coisas óbvias que fazem você lidar com o banco de dados são alterações: novos

objetos criados e objetos já existentes atualizados ou excluídos. A Unidade de Trabalho

é um objeto que mantém registro dessas coisas. Assim que você começa a fazer algo
que possa afetar um banco de dados, você cria uma Unidade de Trabalho para manter

registro das alterações. Cada vez que você cria, altera ou exclui um objeto, informa a
Unidade de Trabalho. Você também pode informá-la sobre objetos que leu para que ela

possa verificar leituras inconsistentes checando se nenhum dos objetos foi alterado

no banco de dados durante a transação de negócio.
Achave da Unidade de Trabalho é que, na hora de confirmar o trabalho, ela decide o que fazer. Abre uma transação, executa qualquer verificação de concorrência

(usando Bloqueio Offline Pessimista (401) ou Bloqueio Offline Otimista (392)) e grava as

alterações no banco de dados. Os programadores de aplicações nunca chamam explicitamente métodos para atualização do banco de dados. Dessa forma, eles não têm
que manter registro do que foi alterado ou se preocupar a respeito de como a integridade referencial afeta a ordem na qual precisam fazer as coisas.

É claro que para isso funcionar a Unidade de Trabalho precisa saber de quais ob-

jetos deve manter registro. Você pode obter isso com o solicitante do objeto realizan-

do esse trabalho ou fazendo o objeto informar à Unidade de Trabalho.

188

Parte Il e Os PaprOes

Com o registro do solicitante (Figura 11.1), o usuário de um objeto tem que se

lembrar de registrar esse objeto na Unidade de Trabalho para atualizações. Quaisquer
objetos que não estiverem registrados não serão gravados na hora da confirmação.

Embora isso permita que o esquecimento cause problemas, dá flexibilidade permi-

tindo que as pessoas façam alterações na memória que elas não queiram que sejam

gravadas. Não obstante, eu argumentaria que causará muito mais confusão do que

valeria a pena. É melhor fazer uma cópia explícita para esse propósito.

Com o registro do objeto (Figura 11.2), o ônus é removido do solicitante. O tru-

2

que comum aqui é colocar métodos de registro em métodos de objetos. Carregar um
objeto do banco de dados registra o objeto como limpo; os métodos de gravação registram o objeto como sujo. Para este esquema funcionar, a Unidade de Trabalho precisa ser passada para o objeto ou estar em um lugar bem conhecido. Passar a Unidade

de Trabalho é tedioso, mas normalmente não há problemas em tê-la presente em al-

5

gum tipo de objeto de sessão.
Mesmo o registro do objeto deixa algo a ser lembrado, ou seja, o desenvolvedor
do objeto tem que se lembrar de acrescentar uma chamada de registro no lugar certo.
A consistência se torna habitual, mas ainda é uma falha complicada quando perdida.
Este é um lugar natural para a geração de código gerar chamadas apropriadas,

mas só funciona quando você puder separar claramente código gerado de código

não-gerado. Este problema é especialmente apropriado para a programação orienta-

cliente
novo(ID)

banco de dados

uma
ounidade

T
i
)
!
1

T
1
1
1
1

um usuário
E

select |L

a

.
gravar limite |
decrédito 1

1
1
1
registrar sujo (um usuário)

:
1
1
1

'
'

'
'

1
1

gravar

update

Figura 11.1

'à

‘
7

confirmar

i

1

Fazendo o solicitante registrar um objeto alterado.

1
1
1
1
1
1
!

LJv
1

|

CapiTULO 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

cliente

banco de dados

rh

'
'

novo

um usuário

189

uma unidade

“de trabalho

'

select

'
'
'j

| registrar limpo

gravar limite
decrédito

i}

if
1

1
1
1
1
|
1
registrar sujo
[Eg
1
'
1
T
¥
'

1
i

o
confirmar

i)
1
1
1

|
!
1

gravar

s

o
1
1

1

u

7

1
i
i)
1

i
a

update

UW
Figura 11.2

UI7
i

i

7
1
1
11
1

Fazer com que o objeto receptor registre a si próprio.

da a aspectos. Também me deparei com pós-processamento dos arquivos objeto pa-

ra resolver isso. Neste exemplo, um pós-processador examinou todos os arquivos Ja-

va .class, procurou pelos métodos apropriados e inseriu chamadas de registro no byte

code. Todo esse procedimento parece deselegante, mas ele separa o código do banco

de dados do código comum. A programação orientada a aspectos fará isso de modo

mais limpo com o código fonte e, quando suas ferramentas se tornarem mais co-

muns, espero ver esta estratégia sendo usada.

Outra técnica que vi é o controlador de unidade de trabalho (Figura 11.3), que o

produto TOPLink usa. Aqui a Unidade de Trabalho lida com todas as leituras do banco

de dados e registra objetos limpos toda vez que eles são lidos. Em vez de marcar ob-

jetos como sujos, a Unidade de Trabalho faz uma cópia na hora da leitura e então compara o objeto na hora da confirmação do trabalho. Embora isso acrescente overhead ao

processo de confirmação, permite uma atualização seletiva de apenas aqueles cam-

pos que foram realmente modificados. Também evita chamadas de registro nos objetos do domínio. Uma abordagem híbrida é fazer cópias apenas dos objetos modifica-

dos. Isso requer registro, mas suporta atualização seletiva e reduz grandemente o
overhead da cópia se houver muito mais leituras do que atualizações.

3

190

Parte ll e Os Paorões

um cliente
m

banco de dados

novo

E
=

gravarlimite

1
|
1

confirmar

q
'
|
!
!

decrédito

carregar (um usuário)!

T
1
'
1
1

>

carregar
select
nino
!
4

novo

clone do usuário

|
ae
1
1
1
0
1
\
1
1
1
1
1
1
diferenças do (clone do usuário)
1
| {usuario alterado)
| update

Vo
Figura 11.3

de trabalho

um usuário

1
1

E
3

uma unidade

o

a

Usando a Unidade de Trabalho como controlador do acesso ao banco de dados.

A criação de objetos é freqiientemente um momento especial para considerar o

registro do solicitante. Não é incomum as pessoas criarem objetos que apenas supos-

tamente são transitórios. Um bom exemplo disso ocorre nos testes de objetos de do-

minio, que rodam muito mais rapidamente sem as gravações no banco de dados. O

registro do solicitante pode tornar isso perceptível. Entretanto, há outras soluções,
como fornecer um construtor transitório que não registre na Unidade de Trabalho ou,

melhor ainda, fornecer um Caso Especial (462) de Unidade de Trabalho que não faça nada na hora da confirmação. Outra área onde uma Unidade de Trabalho pode ser útil é

na atualização quando um banco de dados usa integridade referencial. Na maior
parte do tempo, você pode evitar essa questão assegurando que o banco de dados

apenas verifique a integridade referencial quando a transação é confirmada, em vez

de em cada chamada SQL. A maioria dos bancos de dados permite isso, e se estiver

disponível não há um bom motivo para não fazê-lo. Se não estiver, a Unidade de Trabalho é o lugar natural para lidar com a atualização. Em sistemas menores isso pode
ser feito com código explícito que contenha detalhes sobre quais tabelas gravar pri-

meiro baseado nas dependências das chaves estrangeiras. Em uma aplicação maior é

Capíruto 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

191

melhor usar metadados para descobrir em qual ordem gravar no banco de dados.

Como você faz isso está além do escopo deste livro e é uma razão comum para usar

uma ferramenta comercial. Se você mesmo tiver que fazê-lo, fui informado de que a

chave do enigma é um arranjo topológico.

Você pode usar uma técnica semelhante para minimizar os deadlocks. Se cada
transação usar a mesma seqiiéncia de tabelas para editar, você reduz grandemente o
risco de deadlocks. A Unidade de Trabalho é um lugar ideal para armazenar uma se-

quiência fixa de gravações em tabelas para que você sempre acesse as tabelas na mesma ordem.

Os objetos precisam ser capazes de encontrar sua Unidade de Trabalho corrente.

Uma boa maneira de fazer isso é com um Registro com o escopo de uma thread. Ou-

s

tra maneira é passar a Unidade de Trabalho para os objetos que necessitem dela, seja
em uma chamada de método ou quando você cria um objeto. Em ambos os casos, as-

segure-se de que apenas uma thread possa acessar uma Unidade de Trabalho — aí reside

o caminho para a loucura.
A Unidade de Trabalho torna óbvia a questão de lidar com atualizações em lotes.
A idéia por trás de uma atualização em lote é enviar diversos comandos SQL como

uma única unidade de modo que eles possam ser processados em uma única chama-

da remota. Isso é especialmente importante quando muitas atualizações, inserções e
exclusões são enviadas em uma rápida sucessão. Diferentes ambientes fornecem di-

ferentes graus de suporte a atualizações em lote. JDBC tem um recurso que permite
a você colocar em lote declarações individuais. Se você não tiver este recurso, pode

simulá-lo criando uma string que tenha diversas declarações SQL e, então, submeten-

do-as como uma única declaração. [Nilsson] descreve um exemplo disso para plataformas Microsoft. Entretanto, se você fizer isso verifique se interfere com a pré-com-

pilação das declarações.

A Unidade de Trabalho funciona com qualquer recurso transacional, não apenas
bancos de dados, de modo que você também pode usá-la para coordenar filas de

mensagens e monitores transacionais.
Implementação .NET

Em .NET a Unidade de Trabalho é feita pelo conjunto de dados desconectado. Isso a
torna um padrão levemente diferente da variedade clássica. A maioria das Unidades de Trabalho com as quais me deparei registram e rastreiam as alterações nos objetos. NET lê dados do banco de dados para um conjunto de dados, o qual é uma
série de objetos dispostos como tabelas de um banco de dados, linhas e colunas. O
conjunto de dados é basicamente uma imagem na memória do resultado de uma
ou mais consultas SQL. Cada linha de dados tem o conceito de uma versão (corrente, original, proposta) e um estado (inalterado, acrescentado, excluído, modi
cado), o que, junto com o fato de que o conjunto de dados simula a estrutura do
banco de dados, torna direta a gravação de alterações no banco de dados.
Quando Usá-la
O problema fundamental com o qual a Unidade de Trabalho lida é manter registro dos
vários objetos que você manipulou, de modo que você saiba quais precisa considerar
para sincronizar seus dados na memória com o banco de dados. Se você for capaz de

fazer todo seu trabalho dentro de uma transação de sistema, os únicos objetos com os

quais precisa se preocupar são aqueles que você altera. Embora a Unidade de Trabalho
seja geralmente a melhor maneira de fazer isso, há alternativas.

3

192

Parte Il e Os PaprOes

Talvez a alternativa mais simples seja gravar explicitamente qualquer objeto to-

da vez que o alterar. O problema aqui é que você pode ter muito mais chamadas ao

banco de dados do que quer já que, se você alterar um objeto em três momentos di-

ferentes no seu trabalho, tem três chamadas em vez de uma no seu estado final.

Para evitar diversas chamadas ao banco de dados, você pode deixar todas as

suas atualizações para o final. Para tanto, você precisa manter registro de todos os
objetos que alterou. Você pode usar variáveis, no seu código, para isso, mas logo elas
se tornam não-gerenciáveis, já que você tem mais do que apenas algumas. As variá2

veis muitas vezes trabalham bem com um Roteiro de Transação (120), mas elas podem

ficar muito difíceis com um Modelo de Domínio (126).
Em vez de guardar objetos em variáveis, você pode dar a cada objeto uma flag,

indicando-o como sujo, que é configurada quando o objeto é alterado. Então, você

precisa encontrar todos os objetos sujos no final da sua transação e gravá-los. O va5

lor desta técnica depende de quão fácil é encontrar os objetos sujos. Se todos eles es-

tiverem em uma única hierarquia, então você pode percorrer a hierarquia e gravar

qualquer um que tenha sido alterado. Entretanto, uma rede de objetos mais geral, tal
como um Modelo de Domínio (126), é mais difícil de percorrer.

A grande força da Unidade de Trabalho é que ela mantém toda esta informação

em um único lugar. Assim que você a tiver trabalhando para si, não terá mais que se
lembrar de fazer muita coisa para manter o registro das suas alterações. Além disso,
a Unidade de Trabalho é uma plataforma sólida para situações mais complicadas, como lidar com transações de negócio que se estendem por diversas transações de sis-

tema usando Bloqueio Offline Otimista (392) e Bloqueio Offline Pessimista (401).

Exemplo: Unidade de Trabalho com Registro de Objeto (Java)
por David Rice

Aqui está uma Unidade de Trabalho que pode manter registro de todas as alterações

em uma dada transação de negócio e então perpetrá-las (commit) no banco de da-

dos quando instruída a fazê-lo. Nossa camada de domínio tem uma Camada Supertipo (444), ObjetoDoDomínio, com a qual a Unidade de Trabalho irá interagir. Para arma-

zenar o conjunto de alterações usamos três listas: objetos do domínio novos, sujos
e excluídos.

class UnidadeDeTrabalho...

private List objetosNovos
private List objetosSujos

= new ArrayList ( };
= new ArrayList ( };

private List objetosExcluidos = new ArrayList ( );

Os métodos de registro mantém o estado destas listas. Eles devem executar as-

serções básicas tais como assegurar que um ID não seja nulo ou que um objeto sujo

não esteja sendo registrado como um novo.
class UnidadeDeTrabalho. ..

public void registrarNovo (ObjetoDoDominio obj) {

Assert .notNull ("id não nulo", obj.lerld( ));
Assert. isTrue ("objeto não sujo", !objetossujos. contains (obj}) ;
Assert .isTrue ("objeto não excluído",

!objetosExcluídos.contains
(obj)) ;

Assert isTrue("objeto ainda não registrado cono novo", tobjetosilovos. contains (obj)) ;

Capíruto 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

193

objetosNadd
ovos
{obj} ;

}
public void registrarSujo (ObjetoDoDomínio obj) {
("id não nulo”, obj.lerId( });
Assert.notiiull

Assert .isTrue ("objeto não excluído", !objetosExcluídos.contain
(obj))s ;
(obj))
if (!objetossujos. contains (obj) && !objetosNovos.contai
ns {

}

}

ada (obj). ;
objetosSujos

public void registrarRemovido (ObjetoDoDomínio obj)

Assert notiull ("id não nulo", obj.lerId( ));
if [objetosiovos. remove (obj)) return;
remove (obj)
objetossujos
. ;

{

s

if (lobjetosRemovidos.contains (obj) {

}

}

objetosRemovidos .add (obj) ;

3

public void registrarLimpo (ObjetoDoDomínio obj) {

}

Assert notNull ("id não nulo", obj. lerld( ));

Perceba que registrarLimpo( ) não faz nada aqui. Uma prática comum é colocar
um Mapa de Identidade (196) dentro de uma Unidade de Trabalho. Um Mapa de Identidade (196) é necessário quase sempre que você armazenar o estado de objetos do domínio na memória porque múltiplas cópias do mesmo objeto resultariam em comportamento indefinido. Estivesse um Mapa de Identidade (196) no lugar, registrarLimpo(

)

colocaria o objeto registrado nele. Da mesma forma, registrarNovo( | colocaria o novo
objeto no mapa e registrarExcluido( ) removeria um objeto excluído do mapa. Sem o
Mapa de Identidade (196), você tem a opção de não incluir registrarLimpo( ) na sua Uni-

dade de Trabalho. Já vi implementações deste método que removem objetos alterados

da lista de sujos, mas desfazer parcialmente alterações é sempre complicado. Tenha
cuidado ao reverter qualquer estado no conjunto de alterações.

commit ( ) localizará o Mapeador de Dados (170) para cada objeto e chamará o mé-

todo de mapeamento apropriado. Os métodos atualizarSujos(

) e apagarRemovidos(

não são mostrados, mas eles se comportariam como inserirNovos( ), o que é esperado.
class UnidadeDeTrabalho...

public void commit ( ) (
inserirNovo( );

atualizarSujos( );

apagarRenovidos( |;

}
private void inserirNovos( ) {

for (Iterator objetos = objetosNovos.iterator|

}; objetos.hasNext ( );)

ObjetoDoDominio obj = (ObjetoDoDominio) objetos.next ( );
RegistroDoMapeador. lerMapeador (obj getClass | |) .insert (obj) ;

(

Não está incluído nesta Unidade de Trabalho o rastreamento de quaisquer objetos

que tenhamos lido e queiramos verificar erros de leitura inconsistente na hora da
confirmação. Isso é abordado pelo Bloqueio Offline Otimista (392).

)

194

Parte ll e Os PaprOes

A seguir, precisamos facilitar o registro de objetos. Primeiro cada objeto do domúnio precisa encontrar a Unidade de Trabalho servindo a transação de negócio corrente. Já que essa Unidade de Trabalho será necessária para todo o modelo de domínio passá-la como um parâmetro, é provavelmente pouco razoável. Já que cada transação de
negócio é executada dentro de uma única thread, podemos associar a Unidade de Traba-

lho com a thread correntemente em execução usando a classe java. lang. ThreadLocal.

Mantendo as coisas simples, adicionaremos esta funcionalidade usando métodos estáticos na nossa classe Unidade de Trabalho. Se já tivermos algum tipo de objeto de ses2

5

são associado à thread da execução da transação de negócio, devemos colocar a Unida-

de de Trabalho corrente nesse objeto de sessão, em vez de adicionar o overhead de gerenciamento do mapeamento de outra thread. Além disso, a Unidade de Trabalho pertence
logicamente à sessão.
class UnidadeDeTrabalho.
private static ThreadLocal corrente = new ThreadLocal(

public static void novoCorrente () {

}

);

gravarCorrente (new UnidadeDeTrabalho( ));

public static void gravarCorrente (UnidadeDeTrabalho udt) {

}

corrente.set (udt) ;

public static UnidadeDeTrabalho lerCorrente () {

}

return (UnidadeDeTrabalho) corrente.get ( };

Agora podemos dar ao nosso objeto de dominio abstrato os métodos de marca-

ção para registrar a si próprio na UnidadeDeTrabalho corrente.
class ObjetoDoDonínio. ..
protected void marcarNovo( ) (

UnidadeDeTrabalho. lerCorrente( ) .registrarNovo (this) ;

}
protected void marcarLimpo( ) {
UnidadeDeTrabalho.lerCorrente( } .registrarLinpo(this) ;
}
protected void marcarsujo( ) {

)

UnidadeDeTrabalho. lerCorrente( ) .registrarSujo(this) ;

protected void marcarExcluido () {

)

UnidadeDeTrabalho. lerCorrente( ) .xegistrarExcluído
(this) ;

Os objetos de domínio concretos precisam se lembrar de marcar a si mesmos co-

mo novos e sujos quando apropriado.
class Álbum...

public static Álbum criar (String none) {

Album obj = new Album(geradorID.novoID{

obj marcarmovo( ) ;
return obj;

}, nome) ;

CapiTULO 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

195

public void gravarTítulo (String título) {

this.titulo = titulo;
marcarSujo( );

Não está mostrado que o registro de objetos removidos pode ser feito por um

método remover | | no objeto de domínio abstrato. Além disso, se você tiver implementado registrarLimpo(

|, seus Mapeadores de Dados (170) precisarão registrar qual-

quer objeto recém-carregado como limpo.
A parte final é registrar e perpetrar (commit) a Unidade de Trabalho onde for apro-

priado. Isso pode ser feito explícita ou implicitamente. Aqui está como o gerencia- s
mento explícito da Unidade de Trabalho se parece:
class RoteiroDeEdicaoDeAlbun. .

public static void atualizarTítulo (Long IdDoálbum, String título) (
UnidadeDeTrabalho.novoCorrente( ) ;

Mapeador mapeador = RegistroDoMapeador.lerMapeador
(Álbum. class) ;

Album álbum = (Álbum) mapeador.
buscar (IdDoAlbum) ;
álbum.gravarTítulo (título);
UnidadeDeTrabalho. lerCorrente( ) commit ( );

Excetuando-se as aplicações muito simples, o gerenciamento implícito da Unidade de Trabalho é mais apropriado, pois evita a codificação repetitiva e tediosa. Aqui

está um servlet Camada Supertipo (444) que registra e grava (commit) a Unidade de Trabalho para os seus subtipos concretos. Os subtipos implementarão tratarLeitura( ) em
vez de sobrescreverem executarLeitura( ). Qualquer código em execução dentro de
tratarLeitura( | terá uma Unidade de Trabalho com a qual trabalhar.
class ServletUnidadeDeTrabalho...

final protected void executarleitura (ittpServletRequest solicitação, HttpServletResponse resposta)
try{

}

throws ServletException,

I0Exception {

UnidadeDeTrabalho.novoCorrente( );
tratarLeitura (solicitação, resposta) ;
UnidadeDeTrabalho. Lercorrente ( ) .conmit ( ) ;
} finally (
UnidadeDeTrabalho. gravarCorrente (null) ;
)

abstract void tratarLeitura (HttpServletRequest solicitação, HttpServletResponse resposta)
throws ServletException, IOException;
O exemplo de servlet acima é obviamente um pouco simplista, pois omite o con-

trole de transação de sistema. Se você estivesse usando um Controlador Frontal (328),

você mais provavelmente encapsularia a gerência da Unidade de Trabalho nos seus co-

mandos, em vez de executarLeitura(

|. Um encapsulamento similar pode ser feito

com quase qualquer contexto de execução.

3

196

Parte Il e Os PaprOes

Mapa de Identidade (Identity Map)
Assegura que cada objeto seja carregado apenas uma vez, mantendo cada
objeto carregado em um mapa. Procura objetos usando o mapa quando se
referindo a eles.

procurador
procurar (1)

ummapa
mapa ded

banco de dados

encontrado = ler (1) |

E
[encontrado não-nulo]|
encontrado

|[encontrado é nulo] encontrado = select where id = 1

[|

T
1
1

Um provérbio antigo diz que um homem com dois relógios nunca sabe que horas
são. Se dois relógios são confusos, você pode entrar em uma confusão ainda maior ao
carregar objetos de um banco de dados. Se você não for cuidadoso poderá carregar
os dados do mesmo registro do banco de dados em dois objetos diferentes. Então,
quando você atualizar ambos os objetos, passará por momentos interessantes gravando as alterações no banco de dados corretamente.
Relacionado a isso está um problema óbvio de desempenho. Se você carregar os
mesmos dados mais de uma vez, estará incorrendo em um custo alto em chamadas

remotas. Assim, não carregar os mesmos dados duas vezes não apenas ajuda na con-

sistência, mas pode também aumentar a velocidade da sua aplicação.
Um Mapa de Identidade mantém um registro de todos os objetos que foram lidos
do banco de dados em uma única transação de negócio. Sempre que quiser um obje-

to, você verifica o Mapa de Identidade primeiro para ver se já o tem.

Como Funciona
A idéia básica por trás do Mapa de Identidade é ter uma série de mapas contendo ob-

jetos que foram trazidos do banco de dados. Em um caso simples, com um esquema

isomórfico, você terá um mapa por tabela do banco de dados. Quando você carre-

gar um objeto do banco de dados, primeiro verifica o mapa. Se houver um objeto
nele que corresponda ao que você estiver carregando, retorne-o. Caso contrário, você vai ao banco de dados, colocando os objetos no mapa para referência futura
quando carregá-los.

Há um número de escolhas na implementação com as quais se preocupar. Além
disso, já que Mapas de Identidade interagem com gerenciamento de concorrência, você deveria considerar também o Bloqueio Offline Otimista (392).

Escolha das Chaves

A primeira coisa a considerar é a chave do mapa. A escolha ób-

via é a chave primária da tabela correspondente do banco de dados. Isso funciona

Capíruto 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

197

bem se a chave for uma coluna única e imutável. Uma chave primária substituta se

adapta bem nesta abordagem porque você pode usá-la como a chave no mapa. A

chave será normalmente um tipo de dados simples de modo que o comportamento

de comparação funcionará bem.

Explícito ou Genérico Você tem que escolher entre tornar um Mapa de Identidade explícito ou genérico. Um Mapa de Identidade explícito é acessado com métodos distintos para cada tipo de objeto que você precisar: tal como encontrarPessoa
(1). Um mapa

genérico usa um único método para todos os tipos de objetos, com talvez um pará-

metro para indicar qual o tipo do objeto que você precisa, tal como em encontrar (*Pes-

soa", 1). A vantagem óbvia é que você pode suportar um mapa genérico com um ob-

jeto genérico e reutilizável. É fácil criar um Registro (448) reutilizável que funcione
para todos os tipos de objetos e não precise de atualização quando você adicionar um

novo mapa.

Contudo, prefiro um Mapa de Identidade explícito. Para começar, isso lhe dá ve-

rificação em tempo de compilação em uma linguagem fortemente tipada, mas, mais

do que isso, tem todas as outras vantagens de uma interface explícita: é mais fácil ver

quais mapas estão disponíveis e como eles se chamam. Isso significa acrescentar um

método cada vez que você acrescentar um novo mapa, mas é um overhead pequeno

em razão da clareza.

O tipo da sua chave afeta a escolha. Você só pode usar um mapa genérico se to-

dos os seus objetos tiverem o mesmo tipo de chave. Este é um bom argumento para

encapsular diferentes tipos de chaves de bancos de dados por trás de um único obje-

to chave. (Veja mais detalhes em Campo Identidade (215).)

Quantos Aqui a decisão varia entre um mapa por classe e um mapa para a sessão inteira. Um único mapa para a sessão funciona apenas se você tiver chaves únicas de

banco de dados (veja a discussão em Campo Identidade (215) sobre os compromissos

envolvidos.) Uma vez que você tenha um Mapa de Identidade, o benefício é que você
tem apenas um lugar para ir e nenhuma decisão complicada sobre herança.
Se você tiver diversos mapas, o caminho óbvio é um mapa por classe ou por tabela, o que funciona bem se o esquema do seu banco de dados e modelos de objetos
forem o mesmo. Se eles forem diferentes, é geralmente mais fácil basear os mapas

nos seus objetos em vez de nas suas tabelas, já que os objetos não devem conhecer as
complexidades do mapeamento.

A herança aparece aqui como um sério complicador. Se você tiver carros como

subtipo de veículos, você tem um mapa ou mapas separados? Mantê-los separados
pode tornar referências polimórficas muito mais complicadas, já que qualquer busca

precisa saber procurar em todos os mapas. Como consegiiência, prefiro usar um úni-

co mapa para cada árvore de herança, mas isso significa que você também deve tornar suas chaves únicas pelas árvores de herança, o que pode ser difícil se você usar

Herança de Tabela Concreta (283).

Uma vantagem de um único mapa é que você não tem que acrescentar novos

quando adiciona tabelas de banco de dados. Todavia, vincular seus mapas aos seus
Mapeadores de Dados (170) (veja a seguir) não será nenhum fardo extra.
Onde Colocá-los Os Mapas de Identidade precisam estar em algum lugar onde sejam
fáceis de encontrar. Eles também estão vinculados ao contexto do processo no qual
você está trabalhando. Você precisa assegurar que cada sessão tenha sua própria instância que seja isolada de qualquer outra instância de sessão. Assim, você precisa co-

198

Parte Il e Os PaprOes

locar o Mapa de Identidade em um objeto específico de sessão. Se você estiver usando

Unidade de Trabalho (187), este é de longe o melhor local para os Mapas de Identidade já
que a Unidade de Trabalho (187) é o principal lugar para manter registro dos dados vindos do banco de dados ou indo para o banco de dados. Se você não tiver uma Unidade de Trabalho (187), a melhor aposta é um Registro (448) que seja vinculado à sessão.

Como sugeri aqui, você normalmente vê um único Mapa de Identidade para uma

sessão, caso contrário precisará fornecer proteção transacional para o seu mapa, o

que dá mais trabalho do que qualquer desenvolvedor sensato quer executar. Todavia, há algumas exceções. A maior de todas é usar um banco de dados orientado a

objetos como um cache transacional, mesmo se você usar um banco de dados relacional para armazenar os dados. Embora eu não tenha visto qualquer estudo indepen-

dente sobre performance, as chances são de que vale a pena dar uma olhada. Muitas

pessoas que respeito são grandes fas de cache transacional como uma forma de me-

E

lhorar o desempenho.

A outra exceção são objetos que sejam apenas de leitura em todas as situações.

Se um objeto não puder nunca ser modificado, não há necessidade de se preocupar

com ele sendo compartilhado pelas sessões. Em sistemas de desempenho intensivo,

pode ser muito benéfico carregar todos os dados apenas de leitura uma única vez e

mantê-los disponíveis para o processo inteiro. Neste caso, você tem seus Mapas de
Identidade apenas de leitura em um contexto de processo e seus Mapas de Identidade
atualizáveis em um contexto de sessão. Isso também se aplica a objetos que não sejam completamente apenas de leitura, mas que sejam atualizados tão raramente que
você não se importe de limpar o Mapa de Identidade do processo e potencialmente voltar ao servidor quando isso acontecer.
Mesmo se você estiver inclinado a ter apenas um Mapa de Identidade, você pode
dividi-lo em dois: linhas apenas para leitura e linhas atualizáveis. Você pode evitar
que os clientes saibam qual é qual fornecendo uma interface que verifique ambos os
mapas.
Quando

Usá-lo

Geralmente você usa um Mapa de Identidade para gerenciar qualquer objeto trazido
de um banco de dados e modificado. A razão principal é que você não deseja ter uma
situação na qual dois objetos na memória correspondam a um único registro no ban-

co de dados — você poderia modificar os dois registros inconsistentemente e assim

confundir o mapeamento do banco de dados.

Outra importância do Mapa de Identidade é que ele atua como um cache para as

leituras do banco de dados, o que significa que você pode evitar ir ao banco de dados
cada vez que precisar de algum dado.
Você pode não precisar de um Mapa de Identidade para objetos imutáveis. Se você não pode alterar um objeto, então não tem que se preocupar com anomalias causadas por modificações. Já que Objetos Valor (453) são imutáveis, resulta que você não
precisa de Mapas de Identidade para eles. Ainda assim, Mapas de Identidade apresentam
vantagens aqui, a mais importante das quais é a de desempenho do cache. Outra é
que eles ajudam a prevenir o uso das formas errôneas de testes de igualdade, um
problema importante em Java, onde você não pode sobrescrever o operador
Você não precisa de um Mapa de Identidade para um Mapeamento Dependente

(256). Já que objetos dependentes têm sua persistência controlada pelos pais, não há

necessidade de um mapa para armazenar identidade. Contudo, embora

você não

CapiTULO 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

199

precise de um mapa, talvez queira fornecer um se precisar acessar 0 objeto por meio
de uma chave de banco de dados. Neste caso, o mapa é meramente um índice, de

modo que é discutível se ele realmente conta como um mapa.
O Mapa de Identidade ajuda a evitar conflitos de atualização dentro de uma mes-

ma sessão, mas não faz nada para lidar com conflitos que atravessem sessões. Este é
um problema complexo que discutimos mais no Bloqueio Offline Otimista (392) e no

Bloqueio Offline Pessimista (401).

Exemplo: Métodos para um Mapa de Identidade (Java)
Para cada Mapa de Identidade, temos um campo mapa e métodos de acesso.
private Map pessoas = new HashMap( );
public static void
adicionarPessoa (Pessoa parâmetro)

(

instânciaúnica. pessoas. put (parânetro. lerID( ), parânetro) ;

public static Pessoa lerPessoa

(Long chave)

(

public static Pessoa lerPessoa (long chave)

(

}
}

return (Pessoa) instanciatinica
pessoas. get (chave) ;
return lerPessoa (new Long (chave)) ;

Um dos aborrecimentos de Java é o fato de que long não é um objeto, de modo

que você não pode usá-lo como indice para um mapa. Isso não é tão aborrecedor

quanto poderia ter sido, já que não fazemos nenhuma operação aritmética no índice.
A única situação em que isso realmente atrapalha é quando você quer resgatar um
objeto com um literal. Você quase nunca precisa fazer isso em código de produção,
mas muitas vezes o faz no código de teste, de forma que incluí um método de leitu-

ra que recebe um long para tornar o teste mais fácil.

200

Parte Il e Os PaprOes

Carga Tardia (Lazy Load)
Um objeto que não contém todos os dados de que você precisa,

mas sabe como obtê-los.

um usuário

o banco de dados

|

Hl

obter pedidos

Carga Tardia

EE

1
E,
1
[pedidos nao-carregados] !1
1
1
carregar pedidos
1

aeee
=
retornar pedidos

Para carregar dados de um banco de dados para a memória, é conveniente projetar
as coisas de modo que quando você carregar um objeto de interesse também carre-

gue os objetos que sejam relacionados a ele. Isso torna a carga mais fácil para o desenvolvedor que estiver usando o objeto, que de outra forma tem que carregar explicitamente todos os objetos de que precisa.

Todavia, se você levar isso ao seu final, chegará ao ponto em que carregar um

objeto pode ter o mesmo efeito de carregar um grande número de objetos relacionados — algo que prejudica o desempenho quando apenas alguns dos objetos são realmente necessários.

Uma Carga Tardia interrompe este processo de carga por um tempo, deixando

um marcador na estrutura do objeto de modo que se os dados forem necessários po-

dem ser carregados apenas quando forem usados. Como muitas pessoas sabem, se
você deixar as coisas para mais tarde vai se dar bem quando descobrir que não precisava realmente fazê-las.

Como Funciona
Há quatro maneiras principais pelas quais você pode implementar a Carga Tardia: ini-

cialização tardia, proxy virtual, armazenador de valor e fantasma.
Inicialização tardia [Padrões Beck] é a abordagem mais simples. A idéia básica

é que cada acesso ao campo verifique primeiro para ver se ele é nulo. Se for, ele cal-

cula o valor do campo antes de retornar esse mesmo campo. Para fazer isso funcionar, você tem que assegurar que o campo seja auto-encapsulado, o que quer dizer
que todo acesso ao campo, mesmo de dentro da classe, é feito por meio de um método de leitura.
Usar um nulo para sinalizar um campo que não foi carregado ainda funciona

bem, a menos que nulo seja um valor legal de campo. Neste caso, você precisa de al-

CapiTuLo 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

201

go a mais para sinalizar que esse campo não foi carregado ou usar um Caso Especial

(462) para o valor nulo.

Usar inicialização tardia é simples, mas tende a forçar uma dependência entre o

objeto e o banco de dados. Por esta razão, ela funciona melhor com Registro Ativo

(165), Gateway de Tabelas de Dados (151) e Gateway de Linhas de Dados (158). Se você es-

tiver usando um Mapeador de Dados (170), precisará de uma camada adicional de in-

direção, o que você pode obter usando um proxy virtual [Gang of Four]. Um proxy

virtual é um objeto que parece com o objeto que deveria estar no campo, mas, na ver-

dade não contém nada. Apenas quando um dos seus métodos é chamado, ele carre-

ga 0 objeto correto a partir do banco de dados.

O bom do proxy virtual é que ele parece exatamente com o objeto que deve es-

tar lá. O ruim é que ele não é esse objeto, então você pode facilmente se deparar com
um desagradável problema de identidade. Além disso, você pode ter mais de um

proxy virtual para o mesmo objeto real. Todos esses proxies terão diferentes identidades de objeto, mas ainda assim representam o mesmo objeto conceitual. Você tem

que, no mínimo, sobrescrever o método de igualdade e lembrar-se de usá-lo em vez

de um método identidade. Sem isso, e disciplina, você irá se deparar com algumas

falhas difíceis de rastrear.

Em alguns ambientes, outro problema é que você acaba tendo que criar muitos

proxies virtuais, um para cada classe na qual estiver usando proxy. Você pode normal-

mente evitar isso em linguagens tipadas dinamicamente, mas em linguagens tipadas

estaticamente, as coisas muitas vezes ficam confusas. Mesmo quando a plataforma
fornece recursos convenientes, como

aparecer.

os proxies de Java, outros obstáculos podem

Esses problemas não lhe atingem se você apenas usar proxies virtuais para clas-

ses coleções, tais como listas. Já que coleções são Objetos Valor (453), suas identidades
não importam. Adicionalmente, você tem apenas umas poucas classes coleção para
escrever coleções virtuais.

Com classes do domínio, você pode contornar esses problemas usando um ar-

mazenador de valor. Este conceito, com o qual me deparei pela primeira vez em

Smalltalk, é um objeto que encapsula algum outro objeto. Para obter o objeto subjacente, você solicita seu valor ao armazenador de valor, mas apenas no primeiro

acesso ele pega os dados do banco de dados. As desvantagens do armazenador de
valor são que a classe precisa saber que ele existe e que você perde a característica

explícita da forte verificação de tipos. Você pode evitar problemas de identidade as-

segurando que o armazenador de valor nunca seja passado para além da classe a
qual ele pertence.

Um fantasma é o objeto real em um estado parcial. Quando você carrega o ob-

jeto do banco de dados, ele contém apenas seu ID. Sempre que você tentar acessar

um campo, ele carrega seu estado completo. Pense em um fantasma como um objeto, onde cada campo é inicializado tardiamente de uma só vez, ou como um proxy
virtual, onde o objeto é seu próprio proxy virtual. É claro que não há necessidade de
carregar todos os dados de uma vez só. Você pode reuni-los em grupos que sejam comumente usados juntos. Se você usar um fantasma, pode colocá-lo imediatamente
no seu Mapa de Identidade (196). Dessa forma, você mantém a identidade e evita todos

os problemas causados por referências cíclicas durante a leitura de dados.

Um proxy virtual /fantasma não precisa ser completamente destituído de dados.

Se você tiver alguns dados que sejam rápidos de pegar e comumente usados, pode

fazer sentido carregá-los quando você carregar o proxy ou o fantasma. (Isso é às ve-

zes referido como um “objeto leve.”)

202

Parte Il e Os PaprOes

A herança muitas vezes coloca um problema com a Carga Tardia. Se você for

usar fantasmas, precisará saber que tipos de fantasmas criar, o que você muitas ve-

zes não sabe dizer sem carregar a coisa apropriadamente. Os proxies virtuais podem

sofrer do mesmo problema em linguagens tipadas estaticamente.

Outro perigo com a Carga Tardia é que ela pode facilmente causar mais acessos

ao banco de dados do que você precisa. Um bom exemplo dessa onda de cargas é se
você preencher um conjunto com Cargas Tardias e então olhar um de cada vez. Isso
fará com que você vá ao banco de dados uma vez para cada objeto em vez de lê-los
todos de uma só vez. Já vi ondas de cargas prejudicar o desempenho de uma aplica-

Carga Tardia

ção. Uma maneira de evitar isso é nunca ter uma coleção de Cargas Tardias, mas, em

vez disso, tornar a própria coleção uma Carga Tardia e, quando você carregá-la, car-

regar todos os conteúdos. A limitação desta tática é quando a coleção é muito gran-

de, como todos os endereços IP do mundo. Estes normalmente não são conectados

através de associações no modelo de objetos, de modo que não ocorrem com muita

frequência, mas, quando ocorrem, você precisará de um Manipulador de Listas de Va-

lores [Alur et al.].

A Carga Tardia é uma boa candidata à programação orientada a aspectos. Você

pode colocar o comportamento da Carga Tardia em um aspecto separado, o que lhe

permite alterar a estratégia de carga tardia separadamente assim como liberar os de-

senvolvedores do domínio de ter que lidar com questões de carga tardia. Já vi também um projeto de pós-processador de byte codes Java para implementar a Carga Tardia de forma transparente.
Muitas vezes, você irá se deparar com situações nas quais diferentes casos de
uso funcionam melhor com diferentes variedades de carga tardia. Alguns precisam

de um subconjunto do grafo de objetos, outros precisam de outro subconjunto. Para uma eficiência máxima, você quer carregar o subgrafo correto para o caso de uso
correto.

Amaneira de lidar com isso é ter objetos de interação com o banco de dados se-

parados para os diferentes casos de uso. Assim, se você usar Mapeador de Dados (170)

pode ter dois objetos mapeadores de pedidos: um que carrega as linhas dos itens

imediatamente e outro que os carrega tardiamente. O código da aplicação escolhe o

mapeador apropriado dependendo do caso de uso. Uma variação disso é ter o mesmo objeto carregador básico, mas delegar para um objeto estratégia a decisão sobre
o padrão de carga. Isto é um pouco mais sofisticado, mas pode ser uma maneira me-

lhor de fatorar comportamento.

Teoricamente você poderia querer uma faixa de diferentes graus de carga tar-

dia, mas na prática você precisa realmente de apenas dois:

uma carga completa e

uma suficiente para o propósito de identificação em uma lista. Acrescentar mais, nor-

malmente, adiciona mais complexidade do que vale a pena.
Quando Usá-la

Quando usar a Carga Tardia é uma decisão relacionada a quanto você quer trazer do
banco de dados quando carrega um objeto e quantas chamadas ao banco de dados is-

so irá requerer. Normalmente, não tem sentido usar a Carga Tardia em um campo que
é armazenado na mesma linha do resto do objeto, porque na maioria das vezes não
custa mais trazer dados adicionais em uma chamada, mesmo se o campo for muito

grande - como um LOB Serializado (264). Isso quer dizer que normalmente só vale a
pena considerar Carga Tardia se o campo requerer uma chamada de banco de dados

extra para ser acessado.

Capíruto 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

203

Em termos de desempenho, trata-se de decidir quando você quer pagar o preço

de trazer de volta os dados. Muitas vezes, é uma boa idéia trazer tudo de que você
irá precisar em uma única chamada de forma que você tenha logo tudo no lugar, especialmente se isso corresponder a uma única interação com uma interface gráfica
com o usuário. O melhor momento para usar a Carga Tardia é quando ela envolver

uma chamada extra, e os dados que você estiver chamando não forem usados quan-

do o objeto principal for usado.

Acrescentar a Carga Tardia aumenta um pouco a complexidade do programa,

por isso minha preferência é não usá-la a menos que efetivamente ache que precisarei fazê-lo.

Exemplo: Inicialização Tardia (Java)
A essência da inicialização tardia é um código como este:
class Fornecedor...

public List lerProdutos( ) (
if (produtos == null) produtos = Produto.buscarPorFornecedor (lerID( ));
return produtos;

Desta forma, o primeiro acesso ao campo produtos faz com que os dados sejam
carregados do banco de dados.

Exemplo: Proxy Virtual (Java)
Achave para o proxy virtual é fornecer uma classe que se pareça com a classe real que

você usa normalmente mas que na verdade mantenha um simples envoltório em tor-

no dessa classe real. Assim, a lista de produtos de um fornecedor seria armazenada

em um campo de lista comum.
class LyFornecedor...

private Lista produtos;

O mais complicado na criação de um proxy de lista como este é configurá-lo de

modo que você possa fornecer uma lista subjacente que seja criada apenas quando
for acessada. Para fazer isso, precisamos passar o código necessário para criar a lista

na lista virtual quando esta for instanciada. A melhor maneira de fazer isso em Java

é definir uma interface para o comportamento de carga.
public interface CarregadorDeListaVirtual

}

List carregar( );

{

Podemos então instanciar a lista virtual com um carregador que chama o método de mapeamento apropriado.

class MapeadorDeFornecedor...
public static class CarregadorDeProduto implements CarregadorDeListaVirtual

private Long id;

(

204

Parte Il e Os PaprOes

public CarregadorDeProduto (Long id) {

}

this.id = id;

public List carregar ( ) {

)

return MapeadorDeProduto.criar( ) .buscarPorFornecedor
(id) ;

Durante o método de carga, atribuímos o carregador de produto ao campo lista.
Carga Tardia

class MapeadorDeFornecedor
protected ObjetoDoDomínio fazerCarga (Long id, ResultSet rs) throws SQLException {
String parâmetroNome = rs.getString(2);

LyFornecedor resultado = new LWPornecedor (id, parémetroNome) ;
resultado.gravarProdutos

return resultado;

(new ListaVirtual (new CarregadorDeProduto(id))) ;

Alista fonte da lista virtual é auto-encapsulada e avalia o carregador na primei-

ra referência:

class ListaVirtual...
private List fonte;

private CarregadorDeListaVirtual carregador;

public ListaVirtual (CarregadorDeListaVirtual carregador)

}

this.carregador = carregador;

{

private List lerFonte () {

if (fonte == null) fonte = carregador.carregar( );

return

fonte.

fonte;

Os métodos comuns da lista para os quais delegar são implementados na lista

class ListaVirtual...
public int size () {

}

return lerFonte( ).size( );

public boolean isEmpty ( } {

return lerPonte( ).isEnpty( );

)
// ... e assim por diante para o resto dos métodos de lista
Dessa maneira, a classe do domínio não sabe nada a respeito de como a classe

mapeadora executa a Carga Tardia. Na verdade, a classe do domínio não sabe nem

que há uma Carga Tardia.

Capíruto 11

Exemplo:

Usando

um Armazenador

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

205

de Valor (Java)

Um armazenador de valor pode ser usado como uma Carga Tardia genérica. Neste caso o tipo do domínio está ciente de que algo está acontecendo, já que o campo produto é tipado como um armazenador de valor. Esse fato pode ser escondido dos clientes do fornecedor pelo método de leitura.
class AVFornecedor...

private ArmazenadorDeValor produtos;
public List lerProdutos

return (List) produtos. lerValor( );

ebJeo

}

() {

O próprio armazenador de valor executa o comportamento da Carga Tardia. Ele

precisa receber o código necessário para carregar seu valor quando acessado. Podemos fazer isso definindo uma interface carregadora.
class ArmazenadorDeValor...

private Object valor;
private CarregadorDeValor carregador;
public ArmazenadorDeValor

}

(CarregadorDeValor carregador)

this.carregador = carregador;

public Object lerValor ( ) {

if (valor

}

return

null)

valor = carregador.carregar(

{

);

valor;

public interface CarregadorDeValor {

}

Object carregar(

);

Um mapeador pode configurar o armazenador de valor criando uma imple-

mentação do carregador e colocando-a no objeto fornecedor.

class MapeadorDeFornecedor. ..
protected ObjetoDoDominio fazerCarga

}

(Long id, ResultSet rs) throws SQLException {

String parâmetroNone = rs.getString(2) ;
AvFornecedor resultado = new AVFornecedor (id, parâmetroNone) ;
resultado.gravarProdutos (new ArmazenadorDeValor (new CarregadorDeProduto
(id) )) ;
return resultado;

public static class CarregadorDeProduto implements CarregadorDeValor {

private Long id;
public CarregadorDeProduto (Long id) ;
}

this.id = id;

public Object carregar ( ) {
return MapeadorDeProduto.criar( | .buscarPorFornecedor
(id) ;
)

206

Parrell

e Os PADROES

Exemplo: Usando Fantasmas (C#)

Muito da lógica para tornar objetos fantasmas pode ser criada em Camadas Supertipo

(444). Como conseqiiéncia, se você usar fantasmas, tende a vê-los usados em todo lugar. Começarei nossa exploração de fantasmas olhando alguns objetos do domínio
Camada Supertipo (444). Cada objeto do domínio sabe se é um fantasma ou não.

class ObjetoDoDoninio...

Carga Tardia

CarregarEstado Estado;

public ObjetoDoDomínio (long chave) (
this.Chave = chave;
}

public Boolean Érantasma {
get {return Estado == CarregarEstado. FANTASMA) ; }

}

public Boolean EstáCarregado {
get {return Estado == CarregarStatus. CARREGADO) ; }

)

public void MarcarCarregando ( ) (
Debug.Assert (ÉFantasma) ;

}

Estado = CarregarEstado. CARREGANDO;

public void MarcarCarregado (| {

)

Debug.Assert (Estado = CarregarEstado.CARREGANDO) ;
Estado = CarregarEstado. CARREGADO;

enum CarregarEstado (FANTASMA, CARREGANDO, CARREGADO);
Os objetos do domínio podem estar em três estados: fantasma, carregando e
carregados. Gosto de encapsular a informação do estado com propriedades apenas

de leitura e métodos explícitos de alteração de estado.
O elemento mais intrusivo dos fantasmas é que cada método de acesso precisa ser
modificado de modo que provoque uma carga se o objeto realmente for um fantasma.

class Empregado...
public String Nome (

get {

Carregar( );
return nome;

set {

Carregar(

}

)

);

none = valor;

String none;
class ObjetoDoDonínio. ..
protected void Carregar ( | (
if (Fantasma)

FonteDeDados.Carregar (this);

Capíruto 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

207

Essa necessidade é um alvo ideal para programação orientada a aspectos para

pós-processamento de byte code.

Para que a carga funcione, o objeto do domínio precisa chamar o mapeador cor-

reto. Entretanto, minhas regras de visibilidade dizem que o código do domínio pode

não ver o código do mapeador. Para evitar a dependência, preciso usar uma combi-

nação interessante de Registro (448) e Interface Separada (445) (Figura 11.4). Defino um

Registro (448) para o domínio para operações na fonte de dados.
class FonteDeDados...
public static void Carregar (ObjetoDoDoninio obj) (

}

instance. Carregar (obj) ;

E)

A instância da fonte de dados é definida usando uma interface.
class FonteDeDados...
public interface IFonteDeDados (

}

void Carregar (ObjetoDoDomínio obj);

O registro de mapeadores, definido na camada de fonte de dados, implementa

a interface da fonte de dados. Neste caso, coloquei os mapeadores em um dicionário

indexado pelo tipo de domínio. O método de carga encontra o mapeador correto e
lhe manda carregar o objeto do domínio apropriado.

class RegistroMapeador: IFonteDeDados...
public void Carregar (ObjetoDoDominio obj} {

}

Mapeador (obj .GetType( |) .Carregar (obj) ;

public static Napeador Mapeador (Type tipo)

{

return (Napeador) instance .mapeadores [tipo] ;

}
IDictionary mapeadores = new Hashtable( |;

O código anterior mostra como os objetos do domínio interagem com a fonte de

dados. A lógica da fonte de dados usa Mapeadores de Dados (170). A lógica de atualização nos mapeadores é a mesma do caso sem fantasmas — o comportamento interes-

sante para este exemplo reside no comportamento de busca e de carga.
Classes mapeadoras concretas têm seus próprios métodos de busca que usam
um método abstrato e realizam downcast no resultado.

class HapeadorDeBmpregado...
public Enpregado Buscar (Long chave) (
return (Enpregado) Buscanbstrata (chavel ;
}
class Hapeador...
public ObjetoDoDoninio Buscanbstrata (long chave) {
ObjetoDoDonínio resultado;
resultado = (ObjetoDoDonínio) mapaCarregado [chave] ;

