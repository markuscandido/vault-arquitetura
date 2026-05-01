# 19_Cap_16_Padroes_de_Concorrencia_Offline

CapiTuLo 16

Cliente

ia

Versão

m

lerValor()
incrementar( )

(versão =
cliente.versão)

413

1

1

Endereço

Figura 16.2

e PADRÕES DE CONCORRENCIA OFFLINE

1
x

Compartilhando uma versão.

Um Bloqueio Offline Pessimista (401) compartilhado requer que cada membro do
grupo compartilhe algum tipo de token bloqueável, sobre o qual ele deve então ser
obtido. Como o Bloqueio Offline Pessimista (401) é muitas vezes usado como um com-

plemento do Bloqueio Offline Otimista (392), uma versão de objeto compartilhada é

uma candidata excelente para o papel de token bloqueável (Figura 16.3).

Eric Evans e David Siegel [Evans] definem um agregado como um cluster de ob-

jetos associados que tratamos como uma unidade na hora de alterar dados. Cada
agregado tem uma raiz que fornece o único ponto de acesso aos membros do conjun-

to e um limite que define o que está incluído no conjunto. As características do agregado pedem um Bloqueio de Granularidade Alta, já que trabalhar com qualquer um de
seus membros requer bloquear todos eles. Bloquear um agregado é uma alternativa
a um bloqueio compartilhado que eu chamo de bloqueio de raiz (veja a Figura 16.4).

Por definição bloquear a raiz bloqueia todos os membros do agregado. O bloqueio da

raiz nos dá um único ponto de disputa.

Usar um bloqueio de raiz como Bloqueio de Granularidade
Alta torna necessário

implementar navegação para a raiz no grafo de objetos. Isso permite a um mecanis-

mo de bloqueio, quando solicitado a bloquear qualquer objeto no agregado, navegar

Cliente

é

1

1
7

Versão
%*

1

Endereço
(versão =

e

cliente.versão)

Figura 16.3

Bloqueando uma versão compartilhada.

1
1
[E

Bloqueio

Pessimista

414

Parte Il e Os PaprOes

i

ee

ee

|
|

lá
|
|

Figura 16.4

1

Cliente

|
raiz

a

vw

1

Endereço

o

q

|
|

11

I
|
1
Pu
||

Bloqueio

fronteira

!

|
I

i]

Bloqueando a raiz.

até a raiz e bloqueá-la em vez do objeto. Esta navegação pode ser realizada de algu-

mas formas. Você pode manter uma navegação direta para a raiz em cada objeto no

agregado, ou pode usar uma seqiiéncia de relacionamentos intermediários. Por
exemplo, em uma hierarquia a raiz óbvia é o ancestral no topo, ao qual você pode conectar os descendentes diretamente. Alternativamente, você pode dar a cada nó uma

conexão ao seu ancestral imediato e navegar nessa estrutura para alcançar a raiz. Em

um grafo grande, esta última estratégia poderia causar problema de desempenho, já

que cada ancestral deve ser carregado para determinar se ele próprio também tem

um ancestral. Assegure-se de usar uma Carga Tardia (200) ao carregar os objetos que

constituem o caminho para a sua raiz. Isso não apenas evita que os objetos sejam carregados antes de serem necessários como evita um laço de mapeamento infinito

quando você mapeia um relacionamento bidirecional. Esteja alerta para o fato de que
Cargas Tardias (200) para um único agregado podem ocorrer por diversas transações

de sistema e, deste modo, você pode acabar com um agregado construído com par-

tes inconsistentes. É claro que isso não é bom.
Perceba que um bloqueio compartilhado também funciona para bloqueio de

agregados, já que bloquear qualquer objeto no agregado irá simultaneamente bloquear a raiz.

As implementações do bloqueio compartilhado e do bloqueio da raiz do Bloqueio de Granularidade Alta têm ambos seus compromissos. Ao usar um banco de dados relacional, o bloqueio compartilhado carrega o fardo de que quase todas as suas

seleções irão requerer um join com a tabela versão. Contudo, carregar objetos ao navegar para a raiz pode causar um problema de desempenho também. O bloqueio da
raiz e o Bloqueio Offline Pessimista (401) talvez sejam uma combinação estranha.
Quando você navega para a raiz e a bloqueia, pode precisar recarregar alguns obje-

tos para garantir que estejam atualizados e, como sempre, construir um sistema so-

bre dados legados colocará numerosas restrições sobre sua escolha de implementa-

ção. Implementações de bloqueios existem em abundância, e as sutilezas são até

mais numerosas. Assegure-se de chegar a uma implementação que satisfaça a suas

necessidades.

Capituto 16

Quando

e PADRÕES DE CONCORRENCIA OFFLINE

415

Usá-lo

A razão mais óbvia para usar um Bloqueio de Granularidade Alta é para satisfazer re-

quisitos de negócio. Este é o caso ao bloquear um agregado. Considere um objeto lea-

sing que possua uma coleção de bens. Provavelmente não faz sentido para o negócio
que um usuário edite o objeto de leasing e outro usuário edite simultaneamente um

bem. Bloquear um bem ou o leasing deveria resultar no bloqueio do leasing e de todos

os seus bens.

Uma conseqiiéncia muito positiva de usar o Bloqueio de Granularidade Alta é que

obter e liberar bloqueios é menos custoso. Esta é certamente uma motivação legítima

para usá-los. O bloqueio compartilhado pode ser usado além do conceito do agrega-

do [Evans], mas seja cauteloso ao trabalhar com requisitos não-funcionais como de-

sempenho. Cuidado com a criação de relacionamentos não-naturais entre objetos pa-

ra facilitar o Bloqueio de Granularidade Alta.

Exemplo: Bloqueio Offline Otimista (392) Compartilhado (Java)
Para este exemplo temos um modelo de domínio com uma Camada Supertipo (444),
um banco de dados relacional como nosso armazenamento persistente e Mapeadores
de Dados (170).

A primeira coisa a fazer é criar uma tabela e uma classe de versão. Para manter
as coisas simples, criaremos uma classe de versão bastante versátil que irá não apenas armazenar seu valor, mas também terá um método estático de busca. Perceba

que estamos usando um mapa de identidade para colocar em memória cache versões

para uma sessão. Se os objetos compartilharem uma versão, é crucial que todos
apontem para exatamente a mesma instância dela. Como a classe de versão é uma
parte do nosso modelo de domínio, é provavelmente uma forma pobre colocar códi-

go de banco de dados nele, de modo que deixarei a versão com separação de código
de banco de dados na camada de mapeamento como um exercício para você.
tabela versão...

create table versão (id bigint primary key, valor bigint,
nodificadoPor varchar, modificado datetine)
class Versão...

private Long id;

private long valor;
private String nodificadoPor;
private Timestamp modificado;
private boolean bloqueado;
private boolean étiova;
private static final String SQL ATUALIZAR =
"UPDATE versão SET valor = ?, modificadoPor = ?, modificado
“WHERE id = ? AND valor
private static final String SQL EXCLUIR =
"DELETE FROM versão WHERE id = ? AND valor =
private static final String SQL INSERIR =
"INSERT INTO versão VALUES (2, 2, 2, 2)";
private static final String SQL CARREGAR
"SELECT id, valor, modificadoPor, modificado FROM versão WHERE id = ? *;
ic static Versão buscar (Long id) {

416

Parte ll e Os Parões
Versão versão = GerenciadorDeSessãoDeAp]
icação. lerSessão ( ). lerMapaDeIdent
idade ( |. lerVersão (id);

if (versão == null) (
versão = carregar (id) ;
}

}

return versão;

private static Versão carregar (Long id) {
ResultSet rs = null;
Connection con = null,

PreparedStatement dec = null;
Versio versio = null;
try {

con = ConnectionManager. INSTANCE.getConnection( } ;

dec = con.prepareStatement (SOL CARREGAR) ;
dec.setLong(1,

id.longvalue(

rs = dec.executeQuery( |;

|);

if (rs.next()) {
long valor = rs.getLong(2) ;
String modificadoPor = rs.getString(3) ;

Timestamp modificado = rs.getTimestamp
(4) ;
versão = new Versão(id, valor, modificadoPor, modificado);
GerenciadorDeSessaoDeAplicagao.lerSessao( ) .lerMapaDeIdentidade( ) .gravarVersão (versão) ;

} else {
}

throw new ConcurrencyException ("versão " + id + " não encontrada.");

} catch (SQLException sqlEx)

{

throw new SystemException ("erro de sql inesperado ao carregar a versão", sqlix) ;
) finally {
liberarRecursosBD(rs, con, dec);
}
return versão;

A versão também sabe como criar a si mesma. A inserção no banco de dados é
separada da criação para permitir o adiamento da inserção até que pelo menos um
dono seja inserido no banco de dados. Cada um dos nossos Mapeadores de Dados (170)

do domínio pode chamar a inserção na versão com segurança durante a inserção do

objeto do domínio correspondente. A versão executa um rastreamento verificando se

ele é novo para se assegurar de que será inserido apenas uma vez.
class Versão...

public static Versão criar () (
Versão versão = new Versão (GeradorDerd. INSTANCE.préximald( ), 0,
GerenciadorDeSessãoDeAplicação.
lerSessão( ) .lerUsudrio(

}

versio. éNova = true;
return versio;

public void inserir ( ) {
i£( €tloval )) {

Connection con = null;

PreparedStatement dec = null;

try {
con = Connect ionManager . INSTANCE. getConnection{ );

), now ( });

CapiTuLo 16

dec = con.prepareSta
(SQL
tement
INSERIR) ;

dec.setLong(1,
dec.setLong(2,

dec.setString(3,

this.lerId( ).longValue(
this.lerValor( ));

e PADRÕES DE CONCORRENCIA OFFLINE

417

));

this. lerModificadoPor( |);

dec.setTimestamp(4,

this. lerModificado(

dec. executeUpdate( |;

));

GerenciadorDeSessãoDe
icação. lerSessão(
Apl ) .lerMapaDeIdent idade
éNova = false;

) .gravarversão (this) ;

) catch (SQLException sqlBx) {

throw new SystemBxception ("erro de sgl inesperado ao inserir versio", sqlEx);

} finally {

)

cleanupDBResources (con, dec) ;

A seguir, temos um método incrementar ( ) que incrementa o valor da versão na

linha correspondente no banco de dados. É provável que diversos objetos em um

conjunto de alterações compartilhem a mesma versão, de modo que a versão primei-

ro se assegura de que já não esteja bloqueada antes de incrementar a si mesma. Após
chamar o banco de dados, o método incrementar( ) deve verificar se a linha da versão

foi realmente atualizada. Se este método retornar um contador de linha igual a zero,

ele detectou uma violação de concorrência e levanta uma exceção.
class Versão...

public void incrementar

( ) throws ConcurrencyException {

if (! estáBloqueado( )) (
Connection con = null;
PreparedStatement dec = null;
try {

con = ConnectionManager. INSTANCE.getConnection( } ;
dec = con.prepareStatenent
(SQL ATUALIZAR] ;
dec.setLong(1, valor + 1);

dec.setString(2,

this. lerNodificadoPor ( Di
dec.setTimestanp(3, this. lerModificado( Di

dec.setLong(4, id.longvalue( |);
dec. setLong(5, valor) ;
lec,executeUpdate(
int contadorDebinha
if (contadorDeLinha == 0) (

|;

levantarBxceçãoDeConcorrência( ) ;

}
valor ++;
bloqueado = true;

} catch (SQLException sqlBx) (

throw new SystenException ("erro de sql inesperado ao incrementar versão", sqlBx) ;
) finally {
liberarRecursosBD(con, dec) ;

}

}

private void levantarExceçãoDeConcorrência(

) (

Versão versãoAtual = carregar (this.lerid( ));

418

Parte Il e Os PaprOes

throw new ConcurrencyException (

"versão modificada por " + versãoAtual.modificadoPor + " em" +
DateFormat .getDateTimeInstance( } . format (versdoAtual.lerModificado(

) ) );

Com este código assegure-se de chamar incrementar apenas na transação de sis-

tema na qual você confirmar sua transação de negócio. O flag estáBloqueado faz isso de

modo que incrementar em transações anteriores resultará em obtenção falsa de blo-

queio durante a transação de confirmação. Isso não é problema, porque toda a questão de um bloqueio otimista é que você apenas obtém o bloqueio quando confirma a
gravação (commit).

Quando você usa este padrão, pode querer ver se os seus dados ainda estão
atuais com o banco de dados em uma transação de sistema anterior. Você pode fazer isso acrescentando um método verificarAtual na classe versão que simplesmente verifique se um Bloqueio Offline Otimista (392) está disponível sem realizar nenhuma atualização.

O método de exclusão que executa o SQL para remover a versão do banco de

dados não é mostrado. Se o contador de linha retornado for zero, uma exceção de

concorrência é levantada. Acontece assim, porque o Bloqueio Offline Otimista (392)

provavelmente não foi obtido durante a exclusão do último dos objetos usando esta
versão. Isso nunca deveria acontecer. O verdadeiro truque é saber quando não há

problema em excluir uma versão compartilhada. Se você estiver compartilhando

uma versão por um agregado, simplesmente apague-a após você apagar a raiz do

agregado. Outros cenários tornam as coisas muito mais problemáticas. Uma possibi-

lidade é que o objeto versão mantenha um contador de referência de seus donos e
apague a si próprio quando o contador chegar a zero. Esteja avisado de que isso po-

deria contribuir para um objeto versão bastante sofisticado. Assim que sua versão ficar complicada, você poderia considerar torná-la um objeto de domínio pleno. Isso
faz sentido, mas, é claro, seria um objeto de domínio especial sem uma versão.

Agora vamos ver como usamos a versão compartilhada. A Camada Supertipo

(444) do domínio contém um objeto versão, em vez de um contador simples. Cada

Mapeador de Dados (170) pode gravar a versão ao carregar o objeto do domínio.

class ObjetoDoDonínio....
ot
&

private Long id;
private Timestamp modificado;
private String modificadoPor;

private Versão versão;
public void gravarCamposDeSistena (Versio versão, Timestamp modificado, String modificadoPor) {
this.versão = versão;
this.modificado

= modificado;

this.modificadoPor = modificadoPor;

Para a criação, vamos olhar um agregado que consiste em uma raiz cliente e

seus endereços. O método de criação do cliente criará a versão compartilhada. Clientes terão um método acrescentarEndereço( | que cria um endereço passando a versão

do cliente.

Nosso mapeador abstrato de banco de dados inserirá a versão antes de in-

serir os objetos de domínio correspondentes. Lembre-se de que a versão garantirá

que será inserida apenas uma vez.

Capituto 16

e PADRÕES DE CONCORRENCIA OFFLINE

419

class Cliente extends ObjetoDoDoninio...
public static Cliente criar (String nome)

}

{

return new Cliente (GeradorDeId. INSTANCE.próximard( ), Versão.criar( ), none, new Arrayhist( });

class Cliente extends ObjetoDoDoninio...
public Endereço acrescentarEndereço(String linhal, String cidade, String estado)

{

Endereço endereço = Endereço.criar(this, lerversão( ), linhal, cidade, estado) ;
endereços.
ada (endereço) ;
return endereço;

}
class Endereço extends ObjetoDoDoninio. ..
public static Endereço criar (Cliente cliente, Versão versão,
String linhal, String cidade, String estado)

}

(

return new Endereço (GeradorDeld. INSTANCE.préximald( ), versão, cliente,
linhal, cidade, estado) ;

class Mapeadorabstrato...

public void inserir (ObjetoDoDominio objeto) {
objeto. lerversão( ) .inserir( };
Oincremento deveria ser chamado em uma versão pelo Mapeador de Dados (165)

antes que ele atualize ou exclua um objeto.
class Mapeadorabstrato...

public void atualizar (ObjetoDoDonínio objeto) {
objeto. lerversão( ). incrementar ( |;
class Mapeadorabstrato. ..
public void excluir (ObjetoDoDomínio objeto) {
objeto. lerVersão( ).incrementar(

);

Como este é um agregado, excluímos os endereços quando excluímos o cliente.

Isso nos permite excluir a versão imediatamente após.

class MapeadorDeCliente extends Mapeadorabstrato. ..
public void excluir (ObjetoDoDonínio objeto) (
Cliente cli = (Cliente) objeto;

for (Iterator iterator = cli.lerEnderecos( ) .iterator( );
Endereço endereço = (Endereço)

iterator.next(

};

terator.hasNext (};) {

RegistroMapeador. lerMapeador ( Endereço.class) .excluir (endereço) ;

}
super.excluir (objeto) ;
cli.lerversão( ) .excluir( };

420

Parte Il e Os PaprOes

Exemplo: Bloqueio Offline Pessimista (401) Compartilhado (Java)
Precisamos de algum tipo de token bloqueável que possamos associar a todos os ob-

jetos no conjunto relacionado. Como discutido anteriormente, usaremos Bloqueio
Offline Pessimista (401) como um complemento do Bloqueio Offline Otimista (392), a
fim de que possamos usar como token bloqueável a versão compartilhada. Usare-

mos todo o mesmo código para chegar a uma versão compartilhada.

A única questão é que alguns dos nossos dados devem ser carregados a fim de
obter a versão. Se obtivermos o Bloqueio Offline Pessimista (401) após carregarmos

seus dados, como saberemos que os dados são os atuais? Algo que podemos fazer fa-

cilmente é incrementar a versão dentro da transação de sistema na qual obtivemos o
Bloqueio Offline Pessimista (401). Assim que essa transação de sistema for confirmada,

nosso bloqueio pessimista é válido, e sabemos que temos a cópia mais recente de
qualquer dado compartilhando essa versão, independentemente de onde carrega-

mos dentro da transação de sistema.

class ComandoCarregarCliente...
try {
Cliente cliente = (Cliente) RegistroMapeador.
lerMapeador (Cliente.class) .buscar (id) ;
GerenciadorDeBloqueiosExclusivosDeLeitura.
INSTANCE. cbterBloqueio
(cliente. lerrd( |, GerenciadorDesessãoDeRplicação.
Lersessão( ) .lerid( ));
cliente. lerVersão( ) .incrementar( );
GerenciadorDeTransações. INSTANCE. confirmar };
) catch (Exception e) (

GerenciadorDeTransações. INSTANCE.desfazer( );
throw

e;

Você pode ver que o incremento da versão poderia ser algo que você iria querer
construir no seu gerenciador de bloqueios. Pelo menos você quer decorar [Gang of
Four] seu gerenciador de bloqueios com código que incremente a versão. Seu código
de produção irá, é claro, requerer a manipulação de exceções e o controle de transa-

ções mais robustos do que o exemplo mostra.
Exemplo:

Bloqueio Offline Otimista (392) de Raiz (Java)

Este exemplo faz a maioria das mesmas suposições dos exemplos anteriores, incluindo uma Camada Supertipo (444) e Mapeadores de Dados (170) de domínio. Há um obje-

to versão, mas neste caso ele não será compartilhado. Ele simplesmente fornece um

método incrementar() conveniente para permitir mais facilmente a obtenção do Blo-

queio Offline Otimista (392) fora do Mapeador de Dados (170). Também estamos usando uma Unidade de Trabalho (187) para rastrear nosso conjunto de alterações.

Nosso agregado contém relacionamentos pai-filho, de modo que usaremos na-

vegação filho-para-pai para encontrar a raiz. Precisaremos acomodar isso nos nossos
modelos de domínio e de dados.

class ObjetoDoDonínio...
private Long id;

private ObjetoDoDomínio pai;

public ObjetoDoDomínio (Long id, ObjetoDoDonínio pai) (

Capituto 16

e PADRÕES DE CONCORRENCIA OFFLINE

421

this.id = id;
this.pai = pai;
Assim que tivermos nossos donos, podemos obter nossos bloqueios de raiz antes de confirmarmos a gravação da Unidade de Trabalho.
class UnidadeDeTrabalho. .

public void confirmar ( ) throws SQLException {

for (Iterator iterator = _objetosModificados.iterator( ); iterator.hasNext( };) {

ObjetoDoDominio objeto = (ObjetoDoDomínio) iterator.next ( };
for (ObjetoDoDonínio dono = objeto; dono != null; dono = dono. lerPais( }) {

}

for

}

dono. lerversão(

) .incrementar(

);

(Iterator iterator = objetosModificados.iterator(

); iterator.hasNext(

ObjetoDoDominio objeto = (ObjetoDoDomínio) iterator.next ( };
Mapeador mapeador = RegistroMapeador. lerMapeador (objeto.getClass( |);
napeador.atualizar (objeto) ;

); ) {

Ê
B

422

Parte Il e Os PaprOes

Bloqueio Implicito (Implicit Lock)
por David Rice

Permite ao código de uma camada supertipo
ou de um framework obter bloqueios offline.

Tansação
de Negócio

|TETO
|

carregar cliente

Framework
||

!PO

! i

||.

|

retornarciente

<j

|

i

||
|
|

deGerenciador
Bloqueios

E:

;
bloquearcliente
sucesso

eee

||

|

|
|

A chave de qualquer esquema de bloqueio é que não haja lacunas no seu uso. Es-

quecer de escrever uma única linha de código que requeira um bloqueio pode produzir todo um esquema de bloqueio offline inútil. Falhar na recuperação de um

bloqueio de leitura em que outras transações usam bloqueios de gravação signifi-

ca que você poderia obter dados de sessão não-atualizados. Falhar no uso apropriado de um contador de versão pode resultar na gravação não-intencional por
cima das alterações de alguém. Geralmente, se um item pode ser bloqueado em
qualquer lugar, deve ser bloqueado em todo lugar. Ignorar a estratégia de bloqueio

da sua aplicação permite a uma transação de negócio criar dados inconsistentes.
E
E
e
2

Não liberar bloqueios não irá corromper seus dados, mas acabará trazendo sérios
prejuízos à produtividade. Devido ao fato de o gerenciamento de concorrência of-

fline ser difícil de testar, tais erros poderiam não ser detectados por todos os seus
conjuntos de testes.

Uma solução é não permitir que os desenvolvedores cometam esse erro. As ta-

refas de bloqueio que não podem ser negligenciadas não deveriam ser manipuladas

explicitamente pelos desenvolvedores, mas implicitamente pela aplicação. O fato de

que a maioria das aplicações corporativas faz uso de alguma combinação de framework, Camada Supertipo (444) e geração de código nos fornece uma vasta oportunidade para facilitar o Bloqueio Implícito.

Como Funciona
Implementar Bloqueio Implícito é uma questão de fatorar seu código de modo que

qualquer mecanismo de bloqueio que realmente não possa ser pulado possa ser execu-

tado pelo seu framework da aplicação. Pela falta de uma palavra melhor usaremos

Capituto 16

e PADRÕES DE CONCORRENCIA OFFLINE

423

“framework” significando uma combinação de Camadas Supertipo (444), classes de fra-

mework e qualquer outro código auxiliar. Ferramentas de geração de código são uma

outra via para forçar o bloqueio apropriado. Percebo que isso de forma alguma sig-

nifica uma idéia inovadora. É muito provável que você siga esse caminho, assim que
tiver codificado o mesmo mecanismo de bloqueio algumas vezes pela sua aplicação.

Ainda assim, tenho visto isso não muito bem feito com freqiiéncia suficiente para

que ele mereça uma breve olhada.
O primeiro passo é montar uma lista de quais tarefas são obrigatórias para que
uma transação de negócio trabalhe dentro da sua estratégia de bloqueio. Para o Bloqueio Offline Otimista (392), essa lista conterá itens como armazenar um contador de
versão para cada registro, incluindo a versão no critério de atualização SQL, e arma-

zenar uma versão incrementada ao alterar o registro. A lista do Bloqueio Offline Pessimista (401) incluirá itens junto com as linhas para obter qualquer bloqueio necessário

para carregar uma certa quantidade de dados — normalmente o bloqueio exclusivo
de leitura ou a parte de leitura do bloqueio de leitura /gravação — e liberar todos os

bloqueios quando a transação de negócio ou sessão for completada.

Perceba que a lista do Bloqueio Offline Pessimista (401) não inclui a obtenção de

qualquer bloqueio necessário apenas para editar uma determinada quantidade de

dados - ou seja, bloqueio exclusivo de gravação e a parte de gravação do bloqueio de
leitura/gravação. Sim, esses são obrigatórios se sua transação de negócio quiser edi-

tar os dados, mas obtê-los implicitamente apresenta, se os bloqueios estiverem indis-

poníveis, algumas dificuldades. Primeiro, os únicos pontos nos quais poderíamos

obter implicitamente um bloqueio de gravação, como no registro de um objeto sujo
dentro de uma Unidade de Trabalho (187), não nos oferecem nenhuma garantia de que,

se os bloqueios estiverem disponíveis, a transação irá abortar assim que o usuário comece a trabalhar. A aplicação não consegue descobrir por si mesma quando é um
bom momento para obter esses bloqueios. Uma transação que não falhe rapidamente, conflita com um dos objetivos de um Bloqueio Offline Pessimista (401) — que um

usuário não tenha que executar o mesmo trabalho duas vezes.

Segundo, e também importante, é que esses tipos de bloqueio limitam muito a

concorrência do sistema. Evitar um Bloqueio Implícito aqui nos ajuda a pensar sobre

como afetamos a concorrência tirando esta questão da área técnica e colocando-a no

domínio do negócio. Ainda assim, temos que nos assegurar de que os bloqueios ne-

cessários para a gravação sejam obtidos antes que as alterações sejam confirmadas.
O que o seu framework pode fazer é assegurar que um bloqueio de gravação já tenha
sido obtido antes de confirmar qualquer alteração. Não ter obtido o bloqueio até o
momento da confirmação é um erro de programação, e o código deve pelo menos levantar uma falha de asserção. Aconselho pular a declaração e levantar uma exceção

de concorrência aqui, já que você não quer tais erros no seu sistema de produção

quando as asserções são desativadas.

Uma palavra de precaução sobre o uso de Bloqueio Implícito. Embora ele permita aos desenvolvedores ignorar muito dos mecanismos de bloqueio, não lhes permi-

te ignorar as consegiiências. Por exemplo, se os desenvolvedores estiverem usando

Bloqueio Implícito com um esquema de bloqueio pessimista que espere pelos bloqueios, eles ainda precisam pensar sobre as possibilidades de deadlock. O perigo do

Bloqueio Implícito é que as transações de negócio podem falhar de maneiras inespera-

das assim que os desenvolvedores pararem de pensar sobre bloqueio.

Fazer o bloqueio funcionar é uma questão de determinar o melhor modo de fa-

zer com que seu framework execute implicitamente os mecanismos de bloqueio. Veja

424

Parte ll e Os Panrões

em Bloqueio Offline Otimista (392) exemplos de manipulação implícita desse tipo de

bloqueio. As possibilidades de uma implementação de Bloqueio Implícito de qualidade são numerosas demais para demonstrá-las todas aqui.

Quando

Usá-lo

O Bloqueio Implícito deve ser usado em todas as aplicações não-triviais que não tenham o conceito de framework. O risco de um único bloqueio esquecido é grande
demais.
Exemplo:

Bloqueio Offline Pessimista (401) Implícito (Java)

Vamos considerar um sistema que usa um bloqueio exclusivo de leitura. Nossa arquitetura contém um Modelo de Dominio (126) e estamos usando Mapeadores de Dados

(170) para realizar a mediação entre nossos objetos de domínio e nosso banco de dados relacional. Com o bloqueio exclusivo de leitura, o framework deve obter um blo-

queio sobre um objeto de domínio antes de permitir a uma transação de negócio fazer qualquer coisa com ele.
Qualquer objeto de domínio usado em uma transação de negócio é localizado

pelo método buscar ( ) em um mapeador. Isso é verdadeiro se a transação de negócio
usar o mapeador diretamente chamando buscar ( ) ou indiretamente navegando pelo
grafo de objetos. Agora é possível para nós decorar [Gang of Four] nossos mapeado-

res coma

funcionalidade de bloqueio requerida. Escreveremos um mapeador de blo-

queio que obtém um bloqueio antes de tentar encontrar um objeto.
interface Mapeador...
public ObjetoDoDominio buscar (Long id) ;
public void inserir [ObjetoDoDonínio obj) ;
public void atualizar (ObjetoDoDonínio obj) ;
public void excluir (ObjetoDoDonínio obj) ;
class MapeadorDeBloqueio inplements Mapeador...
private Mapeador imp];
public MapeadorDeBloqueio (Mapeador impl) (

E
E
=
2

}

this.impl

1;

public ObjetoDoDominio buscar (Long id) {
GerenciadorDeBloqueioExclusivosDeLeitura.

INSTANCE .obterBloqueio(

GerenciadorDesessãoDenpLicação.
Lersessão( ) .lerid( |);
return inpl.buscar
(id) ;

}
public void inserir (ObjetoDoDonínio obj) {
inp] inserir (obj) ;
}
public void atualizar (ObjetoDoDominio obj} {
imp] atualizar
(obj) ;

}
public void excluir (objetoDop
ipl excluir
(obj) ;
}

obj) {

Capituto 16

e PADRÕES DE CONCORRENCIA OFFLINE

425

Por ser muito comum procurar por um objeto mais de uma vez em uma sessão,

para que o código acima funcione, o gerenciador de bloqueio deve verificar primei-

ro se a sessão já não tem o bloqueio antes que ele obtenha um. Se estivéssemos usan-

do um bloqueio exclusivo de gravação em vez de um bloqueio exclusivo de leitura,

escreveríamos um decorador para mapeador que verificasse a obtenção prévia de
bloqueios na atualização e exclusão, em vez de realmente obter um bloqueio.
Uma das coisas boas relacionadas a decoradores é que o objeto sendo envolvido nem sabe que sua funcionalidade está sendo aumentada. Aqui podemos envolver
os mapeadores no nosso registro:

RegistroDeMapeadorDeBloqueios implements Registroapeador...
private Nap napeadores = new Hashlap( );
public void napeadorDeRegistro
(Class classe, Mapeador mapeador) {
mapeadores.put (classe, new MapeadorDeBloqueio (mapeador) ) ;
}
public Mapeador lerMapeador (Class classe) {

}

return (Hapeador) mapeadores.get (classe) ;

Quando a transação de negócio põe suas mãos em um mapeador, pensa que está para chamar um método de atualização padrão, mas o que realmente acontece é
mostrado na Figura 16.5.

carregar cliente

[>>
|

&

1
1

Figura 16.5

is

retornar cliente

à

i

|
|
|

Mapeador de bloqueio.

Mapeador
de Cliente

O Gerenciador
de Bloqueios

|

|
|

Nese

|

um Mapeador
de Bloqueio

|

Coe

Transação
de Edição

obter bloqueio
t|
carregarclie7 nte

|

CAPITULO

1

7

Padrões de Estado de Sessão

CapiTULO 17

e PADRÕES DE ESTADO DE SESSÃO

427

Estado da Sessão no Cliente (Client Session State)
Armazena o estado da sessão no cliente.

Como Funciona

Até mesmo os projetos mais orientados a servidor precisam de pelo menos um pe-

queno Estado da Sessão no Cliente, no mínimo para armazenar um identificador da
sessão. Em algumas aplicações você pode considerar a colocação de todos os dados
da sessão no cliente. Neste caso, o cliente envia todo o conjunto de dados de sessão

em cada solicitação, e o servidor os envia de volta a cada resposta. Isso permite um

servidor completamente sem estado.
Na maior parte do tempo você irá querer usar um Objeto de Transferência de Da-

dos (380) para tratar a transferência de dados. O Objeto de Transferência de Dados (380)
pode serializar a si próprio através da conexão permitindo assim que até mesmo da-

dos complexos sejam transmitidos.
O cliente também precisa armazenar os dados. Se for uma aplicação com cliente rico, ele pode fazer isso dentro de suas próprias estruturas, como os campos na sua
interface - embora eu preferisse beber Budweiser a ter de fazer isso. Um conjunto de

objetos não-visuais são, muitas vezes, uma escolha melhor, tais como o próprio Obje-

to de Transferência de Dados (380) ou um modelo de domínio. De uma maneira ou de

outra, isso normalmente não é um grande problema.

Com uma interface HTML, as coisas ficam um pouco mais complicadas. Exis-

tem três maneiras usuais de implementar o Estado da Sessão no Cliente: parâmetros
URL, campos ocultos e cookies.

Os parâmetros URL são os mais fáceis de trabalhar, para uma quantidade pe-

quena de dados. Essencialmente, todas as URLs, em qualquer página de resposta,

trazem o estado da sessão como um parâmetro. O limite claro desta técnica é que o
tamanho máximo de uma URL é limitado, mas se você tiver apenas alguns poucos

zer um campo oculto funcionar, você, quando constrói uma resposta, serializa nele

estado da sessão e o lê de volta em cada solicitação. Você precisará de um formato

para colocar os dados no campo oculto. A XML é uma escolha óbvia, mas é claro que

ela é bastante prolixa. Você pode também codificar os dados com algum esquema de
codificação baseado em texto. Lembre-se de que um campo oculto é oculto apenas
para a página exibida. Qualquer um pode ver os dados olhando o código fonte da

página.

Tome cuidado com um site confuso que tenha páginas velhas ou que tenham
sido consertadas. Você pode perder todos os dados da sessão se navegar até essas
páginas.

A ultima, e algumas vezes controversa, escolha são os cookies, os quais são enviados de um lado para o outro automaticamente. Assim como com um campo ocul-

to, você pode usar um cookie serializando nele o estado da sessão. Você fica limitado

o

oessas ep opejsa

itens de dados ela funciona bem, sendo por isso uma escolha popular para algo como uma identidade de sessão. Algumas plataformas fazem automaticamente a reescrita da URL para adicionar uma identidade de sessão. A alteração na URL pode ser
um problema com bookmarks, de modo que este é um argumento contra o uso de parâmetros URL para sites de consumidores.
Um campo oculto é um campo enviado para o navegador que não é mostrado
na página Web. Você o obtém com uma tag na forma <INPUT type = "hidden">. Para fa-

fa)

428

Parte Il e Os PaprOes

ao tamanho maximo que o cookie pode ter. Além disso, muitas pessoas não gostam de
cookies e os desativam. Se eles fizerem isso, seu site irá parar de funcionar. No entan-

to, atualmente, cada vez mais sites são dependentes de cookies, de modo que esse pro-

blema deverá ocorrer cada vez com menos freqiiéncia. Além disso, isso certamente
não é um problema para um sistema puramente interno à empresa.
Perceba que os cookies não são mais seguros que quaisquer das alternativas, en-

tão tenha em mente que podem ocorrer bisbilhotices de todo tipo. Além disso, os

cookies só funcionam dentro de um único nome de domínio, de modo

que se o seu

site estiver disperso em diferentes nomes de domínio, os cookies não serão movidos
entre eles.

Algumas plataformas conseguem detectar se os cookies estão habilitados e, se

não estiverem, podem usar a reescrita de URL. Isso pode tornar o Estado da Sessão no

Cliente bastante fácil para pequenas quantidades de dados.

Quando Usá-lo

O Estado da Sessão no Cliente tem uma série de vantagens. Em especial, ele suporta

bem objetos servidores sem estado com máxima clusterização e resiliência a falhas. É

claro que, se o cliente falhar, tudo está perdido, mas isso, muitas vezes, já é esperado

pelo usuário.

Os argumentos contrários ao Estado da Sessão no Cliente crescem muito com a

quantidade de dados envolvida. Com apenas alguns poucos campos, tudo funciona

muito bem. Com grandes quantidades de dados, as questões sobre onde armazenar

os dados e o custo do tempo de transferência em cada solicitação tornam-se proibiti-

vos. Isso é especialmente verdadeiro se sua constelação incluir um cliente http.

Existe também a questão da segurança. Qualquer dado enviado para o cliente é
vulnerável a ser visto e alterado. A criptografia é a única maneira de evitar isso, mas

criptografar e decriptografar a cada solicitação é uma sobrecarga para o desempe-

nho. Sem criptografia, você tem que estar seguro de que não está enviando algo que

preferiria esconder de olhos intrometidos. Os dedos podem também bisbilhotar, en-

tão, não pressuponha que o que foi enviado é o mesmo que foi recebido. Qualquer
dado voltando precisará ser completamente revalidado.

Quase sempre, você tem que usar o Estado da Sessão no Cliente para identificação
de sessão. Felizmente, isso deve ser apenas um número, o qual não será um fardo pa-

ra nenhum dos esquemas acima. Você deve ainda se preocupar com o roubo da ses-

são, que é o que acontece quando um usuário malicioso altera a sua identificação de

sessão para tentar entrar na sessão de outra pessoa. A maioria das plataformas, para

reduzir esse risco, incorpora uma identificação de sessão randômica. Se este não for
o caso, gere uma identificação simples de sessão por meio de um hash.

Capiruto 17

e PADRÕES DE ESTADO DE SESSÃO

429

Estado da Sessão no Servidor (Server Session State)
Mantém o estado da sessão serializado no servidor.

Como Funciona
Na forma mais simples deste padrão, um objeto de sessão é armazenado na memó-

ria em um servidor de aplicação. Você pode ter algum tipo de mapa na memória que
armazene estes objetos de sessão usando como chave uma identificação de sessão.
Tudo o que o cliente precisa fazer é fornecer essa identificação e, então, o objeto de

sessão pode ser trazido do mapa para processar a solicitação.

Este cenário básico pressupõe, é claro, que o servidor de aplicação tenha memó-

ria suficiente para executar esta tarefa. Ele pressupõe também que haja apenas um

servidor de aplicação — isto é, que não se estejam utilizando clusters — e que, se o servidor de aplicação falhar, seja apropriado que a sessão seja abandonada e todo o trabalho feito até o momento seja perdido.
Para muitas aplicações este conjunto de suposições não é de fato um problema.
Entretanto, para outras, pode ser problemático. Existem formas de lidar com os casos

em que as suposições não sejam válidas, e estes introduzem variações comuns que
acrescentam complexidade a um padrão essencialmente simples.
A primeira questão é a manipulação dos recursos de memória mantidos pelos

objetos de sessão. De fato, esta é a objeção mais comum ao Estado da Sessão no Servidor. A resposta, é claro, é não manter recursos na memória, mas, em vez disso, seria-

lizar todo o estado da sessão em um memento

mento persistente. Isso apresenta duas questões:

tado da Sessão no Servidor, e onde você o persiste?

[Gang of Four] para um armazena-

em que formato você persiste o Es-

O formato a ser usado é normalmente o formato mais simples possível, já que a

marca do Estado da Sessão no Servidor é sua simplicidade de programação. Diversas

plataformas fornecem um mecanismo de serialização binária simples que permitem
facilmente serializar um grafo de objetos. Outro caminho é serializar em algum outro formato, tal como texto — tão moderno quanto um arquivo XML.
O formato binário é normalmente mais fácil, uma vez que requer pouca programação, enquanto que a forma textual requer, geralmente, ao menos um pouco de có-

digo. Serializações binárias também requerem menos espaço em disco. Ainda que o

soas quiserem lê-lo. Segundo, pode haver problemas com o controle de versões. Se você

modificar uma classe, digamos, acrescentando um campo após tê-la serializado, poderá
não conseguir lê-la novamente. É claro que não é provável que muitas sessões convivam

com uma atualização do software do servidor, a menos que este seja um servidor 24/7 no

qual você pode ter um cluster de máquinas rodando, algumas atualizadas e outras não.

Isso nos remete à questão de onde armazenar o Estado da Sessão no Servidor.
Uma possibilidade óbvia é armazená-lo no próprio servidor de aplicação, no sistema

de arquivos ou em um banco de dados local. Este é um caminho simples, mas pode

não suportar a utilização eficiente de clusters ou a recuperação automática de falhas.

Para suportar estas características, o Estado da Sessão no Servidor precisa estar em al-

gum lugar acessível de modo geral, tal como um servidor compartilhado. Isso irá su-

ovssas ep opejsa

espaço total em disco raramente seja um problema, grandes grafos de objetos serializados demorarão mais tempo para serem ativados na memória.
Existem dois problemas recorrentes com a serialização binária. Em primeiro lugar,
a forma serializada não é legível por um ser humano — o que é um problema se as pes-

g

430

Parte Il e Os Parões

portar a utilização de clusters e a recuperação automática de falhas ao custo de um

tempo maior necessário para ativar o servidor - ainda que a utilização de uma me-

mória cache possa diminuir bastante esse custo.
Esta linha de raciocínio pode levar, ironicamente, ao armazenamento do Esta-

do da Sessão no Servidor serializado no banco de dados, usando uma tabela de ses-

sões indexada pelo ID da sessão. Essa tabela iria requerer um LOB Serializado (264)
para armazenar o Estado da Sessão no Servidor serializado. Quando se trata de manipular objetos grandes, o desempenho do banco de dados pode variar muito, então,
os aspectos de desempenho desta alternativa são bastante dependentes do banco
de dados utilizado.

Neste ponto estamos exatamente na fronteira entre o Estado da Sessão no Servidor

eo Estado da Sessão no Banco de Dados (432). Esta fronteira é completamente arbitrária,

mas tracei a linha no ponto onde você converte os dados no Estado da Sessão no Servi-

dor para um formato tabular.
Se você estiver armazenando o Estado da Sessão no Servidor em um banco de dados, você terá de se preocupar com o tratamento de sessões que desaparecem, espe-

cialmente em uma aplicação voltada aos consumidores. Um caminho é ter um dae-

mon que procure sessões antigas e as exclua, mas isso pode levar a uma grande con-

tenção na tabela de sessões. Kai Yu me falou sobre uma abordagem que ele usava

com sucesso: dividir a tabela de sessões em doze segmentos no banco de dados e, a

cada duas horas, rodar os segmentos, apagando tudo no segmento mais antigo e, então, direcionando todas as inserções para ele. Embora signifique que qualquer sessão

que permanecer ativa por vinte e quatro horas será sumariamente jogada fora, isto é

suficientemente raro para não se constituir em um problema.

Todas essas variações custam mais e mais trabalho para serem implementadas,

mas a boa notícia é que os servidores de aplicação vêm, cada vez mais, suportando

estas capacidades automaticamente. Assim, pode bem ser que estas passem a ser
preocupações exclusivamente dos vendedores de servidores de aplicação.
Implementação Java

As duas técnicas mais comuns para o Estado da Sessão no Servidor são usar a sessão
http e usar um session bean com estado. A sessão http é o caminho mais simples e
faz com que os dados da sessão sejam armazenados pelo servidor Web. Na maioria
dos casos, isto leva à afinidade com o servidor, e não é possível lidar com a recuperação automática de falhas. Alguns vendedores vêm implementando uma

sessão

http compartilhada que permite armazenar os dados da sessão http em um banco
de dados disponível para todos os servidores de aplicação. (Você também pode fazer isso manualmente, é claro.)
O outro caminho usual é através de um session bean com estado, o que requer

um servidor EJB. O contêiner EJB trata a persistência e a passivação*, de modo que
isso o torna muito fácil de programar. A principal desvantagem é que a especificação não exige que o servidor de aplicação evite a afinidade com um servidor. Al
guns servidores de aplicação, no entanto, fornecem esta capacidade. Um deles, o
WebSphere da IBM, pode serializar um session bean com estado em um BLOB no

8
5
Es

DB2, o que permite que múltiplos servidores de aplicação acessem este estado.

Várias pessoas dizem que, uma vez que os session beans sem estado têm um
desempenho melhor, você deveria sempre usá-los em vez de usar beans com estado.
* N.deR.

A passivação é o ato de separar a instância de um bean com estado de seu objeto EJB e salvar o seu estado.

Capitulo 17

e PADRÕES DE ESTADO DE SESSÃO

431

Francamente, isso é conversa fiada. Primeiro, faça um teste de carga com o seu ambiente para ver se a diferença de velocidade entre beans com estado e beans sem estado faz alguma diferença para a sua aplicação. A ThoughtWorks tem feito testes
de carga em aplicações com algumas centenas de usuários concorrentes e não encontrou nenhum problema de desempenho devido à utilização de beans com estado para tal nivel de carga de usuário. Se o ganho de desempenho não é significativo para a sua aplicação, e beans com estado são mais fáceis, então você deveria usálos. Há outras razões para ser cauteloso com beams com estado — a recuperação automática de falhas pode ser mais problemática dependendo do seu vendedor, mas
a diferença de desempenho só aparece sob carga pesada.
Uma alternativa é usar um entity bean. Em geral, não tenho dado muita atenção a entity beans, mas você pode usar um para armazenar um LOB Serializado (264)
de dados de sessão. Isso é bastante simples e é pouco provável que dê margem à
ocorrência de alguns dos muitos problemas que geralmente envolvem a utilização
de entity beans.
Implementação .NET

O Estado da Sessão no Servidor é facil de implementar com a capacidade embutida
do estado de sessão. Por meio de default. NET, armazena os dados da sessão no próprio processo servidor. Você pode ainda acomodar o armazenamento usando um
serviço de estado, o qual pode residir na máquina local ou em qualquer outra máquina na rede. Com um serviço de estado separado, você pode reinicializar o servidor Web e ainda assim reter o estado da sessão. Você faz a escolha entre o estado
de sessão embutido no processo e o serviço de estado em um arquivo de configuração, de modo que você não tem que alterar a aplicação.
Quando Usá-lo
A grande atração do Estado da Sessão no Servidor é a sua simplicidade. Em vários ca-

sos você não tem que executar nenhuma programação para fazê-lo funcionar. Se ele

vai ou não resolver seu problema, depende de você conseguir resolver a implemen-

tação em memória ou, em caso contrário, do nível de suporte que sua plataforma servidora de aplicação lhe dá.

Mesmo sem isso você bem pode descobrir que o esforço de que precisa é peque-

no. Serializar um BLOB para uma tabela de banco de dados pode acabar exigindo

muito menos esforço do que converter os objetos servidores para o formato tabular.

Onde o esforço de programação entra em cena é na manutenção da sessão, es-

pecialmente se você tiver que prover seu próprio suporte para permitir a utilização

de clusters e a recuperação automática de falhas. Pode ocorrer de isso ser mais pro-

blemático do que as outras opções, especialmente se você não tiver muitos dados de
sessão para tratar, ou se seus dados de sessão podem ser facilmente convertidos pa-

ra o formato tabular.

g

432

Parte ll e Os Panrões

Estado da Sessão no Banco de Dados (Database Session State)
Armazena dados de sessão como dados gravados no banco de dados.

Como Funciona
Quando uma chamada sai do cliente para o servidor, o objeto servidor primeiro traz

do banco de dados os dados solicitados pela requisição. Em seguida, executa o trabalho necessário e salva novamente todos os dados solicitados no banco de dados.
Para trazer as informações do banco de dados, o objeto servidor precisará de algumas informações sobre a sessão, o que requer que, ao menos, um número identi:
cador da sessão seja armazenado no cliente. Normalmente, contudo, esta informação

não é nada mais do que o conjunto apropriado de chaves necessárias para encontrar
a quantidade apropriada de dados no banco de dados.

Os dados envolvidos são geralmente uma mistura de dados de sessão, que são
locais apenas para a interação corrente, e dados gravados, que são relevantes para todas as interações.

Um dos aspectos-chave a considerar aqui é o fato de que os dados de sessão são
geralmente considerados locais à sessão e não deveriam afetar outras partes do sistema até que a sessão como um todo seja gravada no banco de dados. Assim, se você

estiver trabalhando em um pedido em uma sessão e quiser gravar seu estado intermediário no banco de dados, normalmente precisa tratá-lo de forma diferente de um

pedido que é confirmado ao final da sessão. Isso se deve ao fato de que você não quer
que pedidos pendentes apareçam em consultas ao banco de dados, buscando coisas
como disponibilidade de livros e receita diária.
Então, como você separa os dados de sessão? Adicionar um campo a cada linha do banco de dados que possa ter dados de sessão é um caminho. A maneira

mais simples disso requer apenas um campo booleano estáPendente. No entanto,
uma maneira melhor é armazenar uma identificação de sessão como um campo
pendente, o que torna muito mais fácil encontrar todos os dados de uma determinada sessão. Todas as consultas que querem apenas dados permanentes precisam
ser modificadas com uma cláusula IDdasessão is not

que filtre esses dados.

NULL, ou precisam de uma visão

O uso de um campo contendo a identificação da sessão é uma solução muito in-

vasiva, porque todas as aplicações que acessam o banco de dados precisam conhecer
o significado do campo para evitar obter dados de sessão. As Visões, algumas vezes

Estado da Sé essão no

resolvem esse problema, mas elas frequentemente impõem seus próprios custos.
Uma segunda alternativa é separar conjuntos de tabelas pendentes. Assim, se
você já tiver tabelas para pedidos e para itens de pedidos no seu banco de dados, vo-

8
e

cê simplesmente adicionaria tabelas para pedidos pendentes e itens de pedidos pendentes. Dados pendentes de sessão seriam armazenados nas tabelas pendentes.
Quando eles se tornarem registros permanentes, você os grava nas tabelas reais. Isso

remove muita da invasividade. No entanto, você precisará acrescentar ao seu código

de mapeamento de banco de dados a lógica apropriada para a seleção de tabelas, o
que certamente adicionará algumas complicações.
Frequentemente, os dados permanentes terão regras de integridade que não se
aplicam a dados pendentes. Neste caso, as tabelas pendentes lhe permitem abrir mão

das regras, quando não as quiser, mas forçar o seu uso, quando isso for conveniente.
Regras de validação geralmente também não são aplicadas ao salvar dados penden-

Capíruto 17

e PADRÕES DE ESTADO DE SESSÃO

433

tes. Você pode se deparar com diferentes regras de validação, dependendo de onde

você estiver na sessão, mas isso geralmente aparece na lógica do objeto servidor.

Se você usar tabelas pendentes, elas devem ser clones exatos das tabelas reai

Dessa forma, você pode manter sua lógica de mapeamento tão parecida quanto pos-

sível. Use o mesmo nome de campo nas duas tabelas, mas adicione um campo de
identificação da sessão nas tabelas pendentes de modo que você possa encontrar facilmente todos os dados de uma sessão.
Você precisará de um mecanismo para limpar os dados de sessão se uma sessão
for cancelada ou abandonada. Usando um identificador de sessão, você pode encontrar todos os dados relacionados à sessão e apagá-los. Se os usuários abandonarem a
sessão sem lhe informar, você irá precisar de algum tipo de mecanismo de timeout pa-

ra lidar com essa situação. Um daemon que rode a cada poucos minutos pode procurar por dados de sessões antigos. Isso requer uma tabela no banco de dados que re-

gistre a hora da última interação do usuário com a sessão.

As atualizações tornam o processo de desfazer uma transação (rollback) muito

mais complicado. Se você atualiza um pedido existente em uma sessão que permite o
roliback de toda a sessão, como você executa esse rollback? Uma opção é não permitir o

cancelamento de uma sessão assim. Quaisquer atualizações em registros permanentes existentes tornam-se parte dos dados permanentes ao final da solicitação. Isto é
simples e freqiientemente se encaixa na visão de mundo dos usuários. A alternativa é

complicada, quer você use campos pendentes, quer use tabelas pendentes. É fácil copiar todos os dados que podem ser modificados para tabelas pendentes, lá modificá-

los e gravá-los de volta nas tabelas permanentes ao final da sessão. Você pode fazer isso com um campo pendente, mas somente se o identificador da sessão for parte da
chave. Dessa forma, você pode manter juntos, na mesma tabela e ao mesmo

tempo,

identificadores de sessão novos e antigos, o que pode ser bastante confuso.
Se você for usar tabelas pendentes separadas que são lidas apenas por objetos
que tratam a sessão, então pode haver uma motivação pequena para colocar os da-

dos em uma forma tabular. É melhor usar um LOB Serializado (264). Neste ponto, cruzamos a fronteira para o Estado da Sessão no Servidor (429).

Você pode evitar toda essa confusão de dados pendentes simplesmente não tendo nenhum dado pendente. Isto é, você projeta o seu sistema de modo que todos os

dados sejam considerados dados permanentes. Isso, é claro, nem sempre é possível,

e, ainda que seja, pode ser tão complicado que os projetistas fariam melhor se pen-

sassem sobre dados pendentes explícitos. Ainda assim, se você tiver esta opção, isso
torna muito mais fácil trabalhar com o Estado da Sessão no Banco de Dados.

O Estado da Sessão no Banco de Dados é uma das alternativas para tratar o estado da
sessão. Ele deve ser comparado ao Estado da Sessão no Servidor (429) e ao Estado da Sessão no Cliente (427).
O primeiro aspecto a considerar neste padrão é o desempenho. Você ganhará
usando objetos sem estado no servidor, possibilitando assim a utilização da técnica
de pooling e a fácil utilização de clusters. No entanto, você pagará um preço pelo tempo necessário para trazer os dados do banco de dados e para o banco de dados a ca-

da solicitação. Você pode reduzir este custo colocando o objeto servidor em memória
cache, de modo que você não tenha que ler os dados do banco de dados quando es-

tes estiverem no cache. No entanto, você ainda terá de pagar pelos custos de escrita.

BP Opelsa

Usá-lo

g
2

ouo

Quando

434

Parte Il e Os PaprOes

A segunda questão importante diz respeito ao esforço de programação, a maior

parte do qual está relacionada ao tratamento do estado da sessão. Se você não tiver
estado de sessão algum e puder gravar todos os seus dados como registros perma-

nentes a cada solicitação, este padrão é uma escolha óbvia porque você não perde na-

da, quer seja em esforço, quer seja em desempenho (se você colocar em cache seus
objetos servidores).

Em uma escolha entre o Estado da Sessão no Banco de Dados e o Estado da Sessão no

Servidor (429), a questão mais importante pode ser o quão fácil é suportar a adoção
de clusters e a recuperação automática de falhas com o Estado da Sessão no Servidor

(429) no seu servidor de aplicação. A utilização de clusters e a recuperação automáti-

essão no

ca de falhas com o Estado da Sessão no Banco de Dados, são geralmente mais claras, pelo menos com as soluções normais.

8
8
8

e

CaríTULO

1

8

Padrões Básicos

436

Parte Il e Os PaprOes

Gateway
Um objeto que encapsula o acesso a um sistema ou recurso externo.

Cliente

lease

Bem

Lo
p=
2

=>

1
|
y
Gateway de
Determinação
de Preços

AI

}-----—

Pacote de
Determinação
de Preços

N

O software interessante raramente vive isolado. Até mesmo o sistema orientado a

objetos mais puro muitas vezes tem que lidar com coisas que não são objetos, como
tabelas de bancos de dados relacionais, transações CICS e estruturas de dados
XML.

Ao acessar recursos externos como esses, você geralmente obterá APIs para
eles. Entretanto, estas APIs naturalmente serão um pouco complicadas porque elas

levam em consideração a natureza do recurso. Qualquer pessoa que precise entender

um recurso precisa entender sua API — sejam JDBC e SQL para bancos de dados rela-

cionais ou W3C ou JDOM para XML. Isso não apenas torna o software mais difícil de
entender, como também muito mais difícil de alterar se você precisar trocar alguns
dados de um banco de dados relacional para uma mensagem XML em algum ponto,
no futuro.

Aresposta é tão comum que mal vale a pena dizer. Encapsule todo o código es-

pecial de API em uma classe cuja interface se pareça com um objeto normal. Os outros objetos acessam o recurso através deste Gateway, que traduz as chamadas sim-

ples de métodos na apropriada API especializada.

Como Funciona
Na verdade, este é um padrão muito simples de encapsulamento. Pegue o recurso

externo. O que a aplicação precisa fazer com ele? Crie uma API simples para seu uso
e use o Gateway para traduzir para a fonte externa.

Um dos usos-chave de uma Gateway é como um bom ponto no qual aplicar um
Stub de Serviço (473). Você pode muitas vezes alterar o projeto do Gateway para tornar

mais fácil a aplicação de um Stub de Serviço (473). Não tenha medo de fazer isso —
Stubs de Serviço (473) bem-colocados podem tornar um sistema muito mais fácil de

testar e, assim, muito mais fácil de escrever.
&

Mantenha um Gateway tão simples quanto possível. Enfoque os papéis básicos

da adaptação do serviço externo e do fornecimento de um bom ponto para stub. O
Gateway deve ser tão mínimo quanto possível e ainda assim capaz de lidar com essas

tarefas. Qualquer lógica mais complexa deve ficar nos clientes do Gateway.

CapituLo 18 e PaprÕEs Básicos

437

Muitas vezes, é uma boa idéia usar geração de código para criar Gateways. De-

finindo a estrutura do recurso externo, você pode gerar uma classe Gateway para en-

capsulá-lo. Você poderia usar metadados relacionais para criar uma classe que en-

capsule uma tabela relacional, ou um esquema XML ou DTD para gerar código para
um Gateway para XML. Os Gateways resultantes são burros, mas resolvem o proble-

ma. Outros objetos podem executar manipulações mais complicadas.
Às vezes, uma boa estratégia é criar um Gateway em termos de mais de um ob-

jeto. O formato óbvio é usar dois objetos: um frontal e um na retarguarda (front end,

back end). O da retaguarda age como uma camada mínima para o recurso externo e
absolutamente não simplifica a API do recurso. O frontal então transforma essa

API complicada em uma mais conveniente para sua aplicação usar. Esta aborda-

gem é boa se o encapsulamento do serviço externo e a adaptação às suas necessida-

des forem razoavelmente complicados, porque cada responsabilidade é manipula-

da por uma única classe. De modo inverso, se o encapsulamento do serviço externo for simples, uma classe pode lidar com ele e com qualquer adaptação que for
necessária.

Quando

Usá-lo

Você deve considerar um Gateway toda vez que tiver uma interface complicada para

algo que parece externo. Em vez de deixar a complicação se espalhar pelo sistema in-

teiro, use um Gateway para contê-la. Quase nunca há aspectos negativos na criação do

Gateway, e o código, em algum outro lugar do sistema, torna-se muito mais fácil de ler.
Os Gateways geralmente tornam um sistema mais fácil de testar, dando a você
um ponto claro no qual distribuir Stubs de Serviço (473). Mesmo se a interface do sis-

tema externo for boa, um Gateway é útil como um primeiro movimento na aplicação

de um Stub de Serviço (473).

Um benefício claro do Gateway é que ele também torna mais fácil para você

trocar algum tipo de recurso por outro. Qualquer alteração nos recursos significa

que você só tem que alterar a classe Gateway — a alteração não se propaga pelo resto do sistema. O Gateway é uma forma simples e poderosa de variação protegida.
Em muitos casos argumentar sobre esta flexibilidade é o foco do debate sobre o uso

do Gateway. Todavia, não se esqueça de que, mesmo que você não pense que o re-

curso vai ser alterado, pode se beneficiar da simplicidade e da testabilidade que o

Gateway lhe dá.
Quando você tem alguns subsistemas como este, outra escolha para desacoplá-

los é um Mapeador (442). Contudo, o Mapeador (442) é mais complicado do que o Gateway. Como conseqiiéncia, uso Gateway para a maioria dos meus acessos a recursos

externos.
Devo admitir que relutei um pouco entre fazer deste um novo padrão, em vez

de referenciar padrões existentes como Fachada e Adaptador [Gang of Four]. Decidi

separá-lo desses outros padrões porque acho que há uma distinção útil a ser feita.

* Embora a Fachada simplifique uma API mais complexa, isso geralmente é feito por quem escreve o serviço de uso geral. Um Gateway é escrito pelo cliente para seu uso particular. Além disso, uma Fachada sempre significa uma in-

terface diferente para o que ela está cobrindo, enquanto que um Gateway pode copiar inteiramente a fachada envolvida, sendo usada para substituição
ou testes.

2

438

Parte Il e Os PaprOes

© Os Adaptadores alteram a interface de uma implementação para que se ajuste
a outra interface com a qual você precisa trabalhar. Com o Gateway, geral-

mente não há uma interface existente, embora você possa usar um adaptador

para mapear uma implementação para a interface de um Gateway. Neste caso, o adaptador é parte da implementação do Gateway.

© O Mediador geralmente separa diversos objetos de modo que eles não conhegam uns aos outros, mas conheçam o mediador. Com um Gateway, geralmente, há apenas dois objetos envolvidos, e o recurso que está sendo envolvido
não conhece o Gateway.

Exemplo: Um Gateway para um Serviço de Mensagens Proprietário (Java)
Estava falando sobre este padrão com meu colega, Mike Retting, e ele me descreveu

a forma como costuma usá-lo em interfaces com software do tipo Enterprise Application Integration (EAI). Decidimos que isso seria uma boa inspiração para um exem-

plo de Gateway.

Para manter as coisas no nível costumeiro de enorme simplicidade, criaremos
um gateway para uma interface que apenas envia uma mensagem usando o serviço

de mensagens. A interface é apenas um único método.
int enviar

(String tipoDaMensagem,

Object [ ] parâmetros) ;

O primeiro parâmetro é uma string indicando o tipo da mensagem. O segundo

são os parâmetros da mensagem. O sistema de mensagens lhe permite enviar qual-

quer tipo de mensagem, então ele precisa de uma interface genérica com esta. Quando você configura o sistema de mensagens, especifica os tipos de mensagens que o

sistema enviará e o número e tipo de parâmetros para eles. Assim, poderíamos configurar a mensagem de confirmação com a string "CNRM" e ter parâmetros para um
número de identificação como uma string, uma quantidade inteira e uma string para

o código do registrador. O sistema de mensagens verifica os tipos dos parâmetros para nós e gera um erro se enviarmos uma mensagem errada ou a mensagem certa com
os parâmetros errados.

Isso é louvável, e provê a necessária flexibilidade, mas a interface genérica é

complicada de usar porque não é explícita. Você não pode perceber olhando a interface quais são os tipos legais de mensagens ou quais parâmetros são necessários pa-

ra um determinado tipo de mensagem. O que precisamos em vez disso é de uma interface com métodos como este:

public void enviarConfirmação

(String IDdoPedido,

int quantia,

String símbolo) ;

Desta maneira, se quisermos que um objeto do domínio envie uma mensagem,

ele pode fazer isso desta forma:

Gateway

class Pedido...

public void confirmar () (
if (éValido( ))ambiente. lerGatewayDeMensagens! ) .enviarConfimação
(id, quantia, símbolo) ;
Aqui o nome do método nos diz qual mensagem ele está enviando e os parâme-

tros são tipados e recebem nomes. Este é um método muito mais fácil de chamar do

CapituLo 18 e PaprÕEs Básicos

439

que o método genérico. É papel do gateway criar uma interface mais conveniente. Is-

so significa, porém, que cada vez que adicionarmos ou alterarmos um tipo de men-

sagem no sistema de mensagens, precisamos alterar a classe gateway, mas teríamos

que alterar o código da chamada de qualquer maneira. Pelo menos, dessa forma, o
compilador pode nos ajudar a encontrar os clientes e a verificar erros.

Há outro problema. Quando temos um erro com esta interface, ela nos informa

retornando um código de erro. Um zero indica sucesso, qualquer outra coisa indica

uma falha, e números diferentes indicam diferentes erros. Esta é uma maneira natu-

ral de um programador C trabalhar, mas não é a maneira pela qual Java faz as coisas.
Em Java, você levanta uma exceção para indicar um erro, então, os métodos do Gate-

way devem levantar exceções em vez de retornar códigos de erros.
A faixa completa de erros possíveis é algo que iremos naturalmente ignorar. Focarei apenas dois: enviar uma mensagem com um tipo desconhecido de mensagem
e enviar uma mensagem em que um dos parâmetros é nulo. Os códigos de retorno
são definidos na interface do sistema de mensagens.
public static final int PARÂMETRO NULO = -1;

DE MENSAGEM =
public static final int TIPO, DESCONHECIDO,
public static final int SUCESSO = 0;
Os dois erros têm uma diferença significativa. O erro referente ao tipo desco-

nhecido de mensagem indica um erro na classe gateway. Já que qualquer cliente está
apenas chamando um método completamente explícito, este erro nunca deveria ser
gerado. Entretanto, eles poderiam passar um nulo e assim ver o erro do parâmetro

nulo. Este erro não é uma exceção verificada já que indica um erro do programador

— não é algo para o qual você escreveria um manipulador específico. O gateway, em

verdade, poderia ele mesmo verificar os nulos, mas se o sistema de mensagens for le-

vantar o mesmo erro, isso provavelmente não valerá a pena.
Por essas razões, o gateway tem que traduzir da interface explícita para a interface genérica e traduzir os códigos de retorno em exceções.

class GatewayDeMensagens...

protected static final String CONFIRMBR = "CHERN";
private RenetenteDeMensagens remetente;
public void enviarConfirmação (String IDdoPedido, int quantia, String símbolo) (

Object | ] parâmetros = new Object [ ] { IDdoPedido, new Integer (quantia), simbolc

}

enviar

(CONFIRMAR,

parâmetros);

private void enviar (String mensagem,

Object

[ ] parâmetros)

{

int códigoDeRetorno = executarEnvio (mensagem, parâmetros) ;
if (códigoDeRetorno == RemetenteDeltensagens . PARÂMETRO NULO)

throw new NullPointerException ("Parâmetro nulo passado para tipo de mensagem: " + mensagem) ;

if

(cédigoDeRetorno

!= RemetenteDeMensagens.

throw new IllegalStateException

}

SUCESSO)

(

"Erro inesperado do sistema de mensagens #: " + códigoDeRetorno)

protected int executarEnvio (String mensagem,

Assert .notNull (remetente) ;

Object

return remetente
enviar (mensagem, parâmetros);

[ ] parâmetros)

(

2

440

Parte ll e Os Parões

Até aqui é difícil ver o propósito do método executarEnvio, mas ele está lá por ou-

tro papel-chave de um gateway — o teste. Podemos testar objetos que usam o gateway

sem o serviço de envio de mensagens estar presente. Para fazer isso, precisamos criar

um Stub de Serviço (473). Neste caso, o stub do gateway é uma subclasse do gateway re-

ale sobrescreve o método executarEnvio.

class StubDoGatewayDeMensagens....
protected int executarEnvio

(String tipoDaMensagem, Object

[ ] parâmetros)

(

private int éMensagemválida (String tipoDaMensagem, Object

[ ] parâmetros)

(

int códigoDeRetorno = éMensagemiálida (tipoDalensagem, parânetros) ;
if (c6digoDeRetorno == RenetenteDeMensagens. SUCESSO) (
mensagensEnviadas++;
}

)

return códigoDeRetorno;

if (todasAsNensagensDevenPalhar) return -999;
if [1 tiposválidosDeMensagens ( ) .contains (tipoDaMensagem) )
return RenetenteDeltensagens.TIPO
DESCONHECIDO DE MENSAGEM;
for (int i = 0; i < parâmetros. lengti
Object parâmetro = parâmetros [i] ;
if (parâmetro = null) {
}

}

return RenetenteDeMensagens.
PARÂMETRO NULO;

}
return RenetenteDeMensagens. SUCESSO;

public static List tiposValidosDeMensagens
List resultado = new ArrayList ( );

( ) (

resultado
add (CONFIRMAR) ;

return resultado;

}
private boolean todasAsMensagensDevenFalhar = false;
public void falharTodasAsMensagens( ) {

}

todasAsMensagensDevenPalhar = true;

public int lerNémeroDeMensagensEnviadas
return mensagensEnviadas;

( ) {

}

Capturar o número de mensagens enviadas é uma maneira simples de nos ajudar a testar se a porta funciona corretamente com testes como estes.

class TestadorDoGateway. ..
public void testarEnvioDeParâmetronulo

( ) (

try {
porta( ) .enviarConfirmação
(null, 5, "US");
falhar ("Não detectou parâmetro nulo") ;
} catch

}

(NullPointerException esperada)

(

}
assertBquals(0, porta( ) .lerlidmeroDemensagensEnviadas ( });

private StubDoGatewayDeMensagens porta

[ } {

CapituLo 18 e Paproes Básicos

}

441

return (StubDoGatewayDeMensagens) Ambiente. lerGatewayDeMensagens( |;

protected void configurar( ) throws Exception {
Ambiente.testarInicialização( );

Você geralmente configura o Gateway de modo que as classes possam encontrá-

lo a partir de um lugar bem conhecido. Aqui usei uma interface de ambiente estática. Você pode trocar entre o serviço real e o stub em tempo de configuração usando
um Plugin (465), ou pode fazer as rotinas de configuração de teste inicializarem o ambiente para usar o Stub de Serviço (473).

Neste caso, usei uma subclasse do gateway para servir de stub para o serviço de
mensagens. Outro caminho é criar uma subclasse do próprio serviço ou reimplemen-

tá-lo. Para testar, você conecta o gateway no Stub de Serviço (473) remetente. Isso fun-

ciona se reimplementar o serviço não for muito difícil. Você sempre tem a escolha de

fazer um stub para o serviço ou para o gateway. Em alguns casos, é até útil fazer para

os dois, usando o do gateway para testar clientes do gateway e o do serviço para testar
o próprio gateway.

442

Parte ll e Os Panrões

Mapeador (Mapper)

Um objeto que estabelece uma comunicação entre dois objetos independentes.
Cliente

E

Lease

Gateway de
Determinação --—————>
de Preços
i

Pacote de
Determinação
de Precos

1
1

Bem

1
Ji

As vezes, você precisa estabelecer comunicações entre dois subsistemas que ainda

assim precisam ignorar um do outro. Isso pode acontecer porque você não pode mo-

dificá-los, ou pode, mas não quer criar dependências entre os dois ou até mesmo entre eles e o elemento isolante.

Como Funciona
Um mapeador é uma camada isolante entre subsistemas. Ele controla os detalhes da

comunicação entre eles sem que nenhum desses subsistemas tenha ciência disso.

Um mapeador, muitas vezes, leva dados de uma camada para outra. Uma vez
ativado para esse transporte, é bastante fácil ver como ele funciona. A parte complicada de usar um mapeador é decidir como invocá-lo, já que ele não pode ser invocado diretamente por nenhum dos subsistemas entre os quais ele está mapeando. Às
vezes, um terceiro subsistema conduz o mapeamento e também invoca o mapeador.
Uma alternativa é fazer do mapeador um observador [Gang of Four] de um ou outro

subsistema. Dessa maneira, ele pode ser ativado escutando os eventos em um deles.

O funcionamento de um mapeador depende do tipo de camadas que ele estiver

mapeando. O caso mais comum de um mapeamento de camadas com que nos deparamos é um Mapeador de Dados (170), então, veja lá mais detalhes sobre como um Ma-

peador é usado.
Quando

Usá-lo
Basicamente, um Mapeador desacopla diferentes partes de um sistema. Quando vo-

cê quiser fazer isso, tem que escolher entre um Mapeador e um Gateway (436). O Gateway (436) é de longe a escolha mais comum, porque ele é muito mais simples de

usar um Gateway (436) do que um Mapeador, tanto na escrita do código quanto no

seu uso posterior.

A consegiiência disso é que você deveria usar um Mapeador somente quando

precisar se assegurar de que nenhum subsistema tem uma dependência em relação à

esta interação. A única ocasião em que isso é realmente importante é quando a inte-

CapituLo 18 e Paproes Básicos

443

ração entre os subsistemas é especialmente complicada e, de alguma forma, indepen-

dente do propósito principal de ambos os sistemas. Assim, em aplicações corporativas, na maior parte das vezes, encontramos um Mapeador usado para interações com

um banco de dados, como num Mapeador de Dados (170).

O Mapeador é semelhante ao Mediador [Gang of Four] visto que ele é usado pa-

ra separar elementos diferentes. Entretanto, os objetos que usam um mediador estão
cientes disso, mesmo se não estiverem cientes uns dos outros. Os objetos que um Mapeador separa não estão nem mesmo cientes do mapeador.

a

444

Parte ll e Os Paproes

Camada Supertipo (Layer Supertype)

Um tipo que atua como o supertipo para todos os tipos em sua camada.
Não é incomum que todos os objetos em uma camada tenham métodos que você não
quer ter duplicados por todo o sistema. Você pode mover todo este comportamento

para uma Camada Supertipo compartilhada.
Como Funciona

A Camada Supertipo é uma idéia simples que leva a um padrão bastante curto. Tudo

o que você precisa é de uma superclasse para todos os objetos em uma camada — por

exemplo, uma superclasse Objeto do Domínio para todos os objetos do domínio em

um Modelo de Domínio (126). Características comuns, como o armazenamento e a
manipulação de Campos Identidade (215), podem estar lá. De modo similar, todos os

Mapeadores de Dados (170) na camada de mapeamento podem ter uma superclasse
que conte com o fato de que todos os objetos do domínio têm uma superclasse em

comum.

Se você tiver mais de um tipo de objeto em uma camada, é útil ter mais do que

uma Camada Supertipo.

Quando

Usá-la
Use uma Camada Supertipo quando você tiver características comuns a todos os obje-

tos em uma camada. Eu freqiientemente faço isso automaticamente, porque uso muito características comuns.

Exemplo: Objeto do Domínio

(Java)

Os objetos do domínio podem ter uma superclasse comum para manipulação da

identidade.

class ObjetoDoDonínio..
private Long ID;

public Long leriD( ) {

}

return ID;

public void gravarID(Long ID) {

}

Assert .notNull ("Não posso configurar um 1D nulo”, 1D) ;
this.ID = ID;

public ObjetoDoDomínio

Cama

8
5

}

(Long ID)

this.ID = ID;

public ObjetoDoDomínio( ) {

}

{

