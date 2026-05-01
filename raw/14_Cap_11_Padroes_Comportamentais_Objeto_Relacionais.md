# 14_Cap_11_Padroes_Comportamentais_Objeto_Relacionais

Parte Il e Os PaprOes

208

(o1ujwogogoyalqo) Je6ae>

Ro

[ee

‘oqujwog op ojafgo

se6aueoy

opebsidua

opefsiduz
ap sopeadew

eyunaaebsez1aze)a
(eneyp) Jeosng +

“PUISBJUPJ WN ap eed eu sepijonus sassej)

eyunaaebiepiazey¢

{o1ujwogogoralqo) 1ebsue) +
(eneyp)erensqyersng+

sopeq ap ajuos

(sopegagajuos1) jut
(olujwogogoyalqg) sebsue>

Lo
Jopeadew 0.351604
Jopeadey
T
'
)
sopep ap ajuoy
1
De
1
oluop
)
)
'
\
)
'
)
)

(olujwogogoyalqo) sebaue>

sopeq ap ajuos|
«se poqup

pie ebse)

PLL esnbiy

CapiTULO 11

if (resultado == null)

}

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

209

(

resultado = CriarFantasma (chave) ;
mapaCarregado.
adá (chave, resultado) ;

return resultado;

}
IDictionary mapaCarregado = new Hashtable );
public abstract ObjetoDoDomínio CriarFantasma (Long chave) ;
class MapeadorDeimpregado. ..
public override ObjetoDoDomínio CriarFantasma(long chave) {

}

return new Empregado (chave) ;

Como

você pode ver, o método

E)
de busca retorna um objeto no seu estado de

fantasma. Os dados reais não vêm do banco de dados até que a carga seja disparada

pelo acesso a uma propriedade no objeto de domínio.
class Mapeador...
public void Carregar (ObjetoDoDominio obj) (

if (! obj. Fantasma) return;
1DBCommand conn = new OleDbCommand (declaraçãoDeBusca( ), DB.comection) ;
com. Parameters.Add (new OleDbParameter ("chave", obj.Chave)) ;
IDataReader leitor = comm.ExecuteReader ( |;

leitor.Read( |;

CarregarLinha (leitor, obj);

}

leitor.Close( };

protected abstract String declaraçãoDeBusca( );

public void CarregarLinha (IDataReader leitor, ObjetoDoDoninio obj) {
if lobj.ÉFantasma) (
obj .MarcarCarregando( );

fazerCargaDaLinha (leitor, obj);
}

obj .MarcarCarregado( );

}
protected abstract void fazerCargaDabinha (IDataReader leitor, ObjetoDoDominio obj) ;
Como é comum nestes exemplos, a Camada Supertipo (444) lida com todo o com-

portamento abstrato e então chama um método abstrato para uma subclasse especifica executar seu papel. Para este exemplo, usei um leitor de dados, uma abordagem
baseada em cursor que é a mais comum para as diversas plataformas no momento.
Deixarei para você estender isso para um conjunto de dados, o que seria realmente
mais apropriado para a maior parte dos casos em .NET.

Para este objeto Empregado, mostrarei três tipos de propriedades: um nome
que é um valor simples, um departamento que é uma referência a um outro objeto e
uma lista de registros de horários que mostra o caso de uma coleção. Todos são car-

regados juntos na implementação da subclasse do método associado.

210

Parte ll e Os Parões

class MapeadorDeBmpregado. ..
protected override void fazerCargaDaLinha (IDataReader leitor, ObjetoDoDomínio obj) {

Empregado empregado = (Empregado) obj;
empregado.None = (String) leitor ['nome") ;
MapeadorDeDepartamento mapDep =
(MapeadorDeDepartamento)

RegistroMapeador .Mapeador

(typeof

(Departamento));

empregado. Departamento = mapDep.Buscar( (int) leitor ["IdDoDepartamento"]) ;
carregarRegistrosDeTempo

BE
Ss

(empregado) ;

O valor do nome é carregado simplesmente lendo a coluna apropriada do cur-

sor corrente do leitor de dados. O departamento é lido usando o método de busca no

objeto mapeador de departamento. Isso acabará configurando a propriedade para

um fantasma do departamento. Os dados do departamento serão lidos apenas quando o próprio objeto departamento for acessado.
A coleção é o caso mais complicado. Para evitar carregamento em ondas, é im-

portante carregar todos os registros de tempo em uma única busca. Para isso, preci-

samos de uma implementação de lista especial que atue como uma lista fantasma.

Esta lista é apenas um envoltório fino em torno de um objeto lista real, para o qual todo o comportamento real é apenas delegado. A única coisa que o fantasma faz é assegurar que qualquer acesso à lista real provoque uma carga.
class ListaDoDomínio...

IList dados (

get (

)

}

Carregar ( );
return dados;

set { dados = valor;)

IList _dados = new ArrayList ( };

public int Count {

}

get { return dados.Count;}

A classe da lista do dominio é usada pelos objetos do dominio e é parte da camada do domínio. A carga real precisa acessar comandos SQL, então uso delegação

para definir uma função de carga que possa ser fornecida pela camada mapeadora.
class ListaDoDomínio.
public void Carregar ( ) (

if (BFantasma)

}

(

MarcarCarregando( ) ;
ExecutarCarregador (this) ;
MarcarCarregado( |;

}
public delegate void Carregador (ListaDoDomínio lista) ;
public Carregador ExecutarCarregador;

[

1
aneuyprdua = | IWaHM
1
= Das
1

1
1
1
1
1
1
1
1
1
1
1
1

1

sopep ap couea,

JopeadeiN
SERS

seBaue>

1
h

auoN

“elusejue} un ap ebie> ap eluanbas y

1
1
1
fe

i

|

1
i
i
|

sopegagajuos

=
opebardug
ida

(duajse
} Baue> [ewseques3]3

(dwa)sebaues

sapepaudosd sesianip JeBaueo -T
(dwa)sebaues [
1
\
r
1
dus) sopeade
(odisaigo
aunosejeg|

ande

—
L+

211
e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS
Capíruto 11

sp

eanbis

212

Parte ll e Os Parões

IList

Carga Tardia

Lista do Domínio
oo
+

1
>

«delegate»

ExecutarCarregador| Carregador(ListaDoDomínio)

FAS «delegate»

Carregar

domínio
fonte de dados

Carregador de Lista
Mapeador

|<—
1

Sal: String
ParametrosSql: IList
+Carregar (ListaDoDomínio)
+Anexar (ListaDoDominio)

Figura 11.6 Classes para uma lista fantasma. Como ainda não há um padrão aceito para
mostrar delegação em modelos UML, esta é minha abordagem corrente.
Pense em delegação como uma variedade especial de Interface Separada (445) pa-

ra uma única função. De fato, declarar uma interface com uma única função é uma

alternativa razoável de fazer isso.

O próprio carregador tem propriedades para especificar o SQL para a carga e 0

mapeador para usar para mapear os registros de tempo. O mapeador de empregados
configura o carregador quando carrega o objeto empregado.

class MapeadorDeEmpregado. ..
void carregarRegistrosDeTempo

(Empregado empregado)

(

CarregadorDeLista carregador = new CarregadorDeLista( Ne
carregador.Sgl = MapeadorDeRegistroDeTenpo.SQL BUSCAR | PELO EMPREGADO;
carregador. Parânetrossgl .Add (empregado. Chave) ;
carregador Mapeador = RegistroMapeador .Mapeador (typeof (RegistroDeTempo) ) ;
carregador. Anexar | (ListaDeDominio) empregado. RegistrosDeTenpo) ;

}
class CarregadorDeLista...
public String Sal;
public IList ParâmetrosSgl = new ArrayList ( |;
public Mapeador Mapeador;

Capíruto 11

e PADRÕES COMPORTAMENTAIS OBJETO-RELACIONAIS

213

Já que a sintaxe para atribuição de delegação é um pouco complicada, dei ao

carregador um método anexar.
class CarregadorDeLista...

public void Anexar (ListaDoDomínio lista) (
lista.ExecutarCarregador = new istaDoDomínio. Carregador (Carregar) ;

Quando o empregado é carregado, a coleção de registros de tempo permanece

em um estado de fantasma até que um dos métodos de acesso dispare o carregador.

Nesse momento, o carregador executa a consulta para preencher a lista.

class CarregadorDeLista
public void Carregar

(ListaDoDomínio lista)

lista.EstáCarregada = true;

{

1DbConmand conn = new OleDbCommand (Sql, DB.connection) ;
foreach (Object param in ParâmetrosSql)
com. Parameters.
Add (new OleDbParameter (param.Tostring( |, param)) ;
IDataReader leitor = conm. ExecuteReader( );
while (Leitor.Read( }) (
ObjetoDoDominio obj = FantasnaParaLinha (leitor) ;
Mapeador.CarregarLinha
(leitor, obj) ;
lista.Adicionar
(obj) ;

}

}

leitor.Close(

);

private ObjetoDoDominio FantasmaParaLinha (IDataReader leitor) {

return Mapeador .BuscaAbstrata ( (System. Int32) leitor [Mapeador .NomeDaColunaChave] ) ;

Usar listas de fantasmas como essa é importante para reduzir a onda de cargas.

Ela não a elimina completamente, já que há outros casos em que ela aparece. Nesse
exemplo, um mapeamento mais sofisticado poderia carregar os dados do departa-

mento em uma única consulta com os empregados. Entretanto, carregar sempre to-

dos os elementos juntos em uma coleção ajuda a eliminar os piores casos.

CAPITULO

1

2

Padroes Estruturais

Objeto-Relacionais

Capitulo 12

Campo

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

215

Identidade (Identity Field)
Guarda o campo ID de um banco de dados em um objeto para manter a

identidade entre um objeto na memória e uma linha do banco de dados.
Pessoa

id: long
Os bancos de dados relacionais diferenciam uma linha de outra usando uma chave —

em particular, a chave primária. Entretanto, objetos na memória não precisam de tal
chave, já que o sistema de objetos, por trás dos panos, assegura a identidade correta

(ou, no caso de C++, com posições de memória absolutas). Não há problemas para

ler dados de um banco de dados, mas, para gravá-los de volta, você precisa vincular

o banco de dados ao sistema de objetos em memória.
Em essência, o Campo Identidade é muito simples. Tudo o que você faz é armaze-

nar a chave primária da tabela do banco de dados relacional nos campos dos objetos.

Como Funciona
Embora a noção básica do Campo Identidade seja muito simples, há muitas questões
complicadas que vêm à tona.
Escolhendo a chave Aprimeira questão é que tipo de chave escolher no seu banco

de dados. É claro que nem sempre esta escolha se apresenta, visto que, muitas vezes,
você está lidando com um banco de dados já existente que já tem suas estruturas de
chaves estabelecidas. Há muita discussão e material sobre isso na comunidade de
banco de dados. Ainda assim, o mapeamento para objetos adiciona algumas preocupações à sua decisão.
A primeira preocupação é se devem ser usadas chaves com ou sem significado.

Uma chave com significado é como o número do seguro social nos Estados Unidos
para identificar uma pessoa. Uma chave sem significado é basicamente um número

randômico que o banco de dados inventa e que não se destina ao uso de seres humanos. O perigo de uma chave com significado é que, embora em teoria elas sejam boas

chaves, na prática não o são. Para simplesmente funcionar, as chaves precisam ser

únicas. Para funcionar bem, elas precisam ser imutáveis. Embora os números atribuídos sejam supostamente únicos e imutáveis, erros humanos muitas vezes fazem com

que eles não sejam nem uma coisa nem outra. Se você digitar errado trocando meu

número do Seguro Social pelo da minha esposa, o registro resultante não é nem único e nem imutável (presumindo que você quisesse consertar o erro.) O banco de dados deve detectar o problema da unicidade, mas ele só pode fazer isso após meu registro ir para o sistema e é claro que isso só poderia acontecer após o erro. Como con-

seqiiéncia, deve-se desconfiar de chaves com significado. Para sistemas pequenos

e/ou casos muito estáveis, você pode se sair bem com o seu uso, mas, em geral, você

deve tomar uma firme posição em favor da falta de significado da chave.

A próxima preocupação são as chaves simples versus as chaves compostas. Uma

chave simples usa apenas um campo do banco de dados. Uma chave composta usa
mais de um. A vantagem de uma chave composta é que ela, frequentemente, é mais

2

216

Parte Il e Os PaprOes

facil de usar quando uma tabela faz sentido no contexto de outra. Um bom exemplo

são os pedidos e as linhas de itens, em que uma boa chave para a linha do item é uma

chave composta pelo número do pedido e um número seqiiencial que identifica a li-

nha do pedido. Embora freqiientemente as chaves compostas façam sentido, há mui-

to a ser dito sobre a maior uniformidade das chaves simples. Se você usar as chaves

simples em todo lugar, você pode usar o mesmo código para toda manipulação de
chaves. As chaves compostas requerem tratamento especial em classes concretas.
(Com geração de código, isso não é problema). As chaves compostas também carre-

gam um pouco de significado, então, ao usá-las, seja cuidadoso com a regra da uni-

cidade e, especialmente, com a regra da imutabilidade.

Você tem que escolher o tipo da chave. A operação mais comum que você fará

com uma chave é o teste de igualdade, de modo que você quer um tipo com uma ope-

ração rápida de igualdade. A outra operação importante é a obtenção da próxima cha-

ve. Assim, um tipo inteiro longo é freqiientemente a melhor aposta. As strings também
podem funcionar, mas a verificação de igualdade pode ser mais lenta, e incrementar

strings é um pouco mais difícil. As preferências do DBA podem decidir a questão.

a
5

(Cuidado com o uso de datas ou horas em chave. Elas não apenas têm significado como também levam a problemas de portabilidade e consistência. As datas são

particularmente vulneráveis a isso, porque elas frequentemente são armazenadas

para uma precisão de frações de segundos, que podem facilmente sair de sincronismo e levar a problemas de identidade.)

Você pode ter chaves que são únicas na tabela ou em todo o banco de dados.

Uma chave única na tabela é única nessa tabela, o que, afinal, é o mínimo que você
precisa de uma chave. Uma chave única no banco de dados é única para todas as li-

nhas de todas as tabelas do banco de dados. Uma chave única na tabela geralmente

é boa, mas uma chave única no banco de dados é frequentemente mais fácil de gerar
e lhe permite usar um único Mapa de Identidade (196). Com os valores modernos sen-

do o que são, é bastante improvável que você fique sem números para novas chaves.

Se você realmente insistir, pode recuperar as chaves dos objetos excluídos com um
roteiro simples no banco de dados que compacte o espaço das chaves — ainda que rodar este roteiro vá requerer que você tire a aplicação do ar. Todavia, se você usar cha-

ves de 64 bits (o que você deveria fazer), é improvável que você precise disso.

Seja cauteloso com herança quando você usar chaves únicas por tabela. Se você

estiver usando Herança de Tabela Concreta (283) ou Herança de Tabela de Classe (276), a

vida fica muito mais fácil com chaves que sejam únicas na hierarquia em vez de úni-

cas em cada tabela. Ainda uso o termo “única na tabela”, ainda que, a rigor, isso de-

veria ser como “única no grafo da herança.”

O tamanho da sua chave pode afetar o desempenho, especialmente com índi-

ces. Isso depende do seu sistema de banco de dados e/ou de quantas linhas você
tem, mas vale a pena fazer uma verificação por alto antes de tomar uma decisão.

Representando o Campo Identidade em um Objeto A forma mais simples de um

Campo Identidade é um campo que corresponda ao tipo da chave no banco de dados.

Assim, se você usar uma chave inteira simples, um campo inteiro funcionará mui-

to bem.
As chaves compostas são mais problemáticas. A melhor aposta com elas é criar
uma classe chave. Uma classe chave genérica pode armazenar uma seqiiéncia de objetos que atuam como os elementos da chave. O comportamento chave para o objeto

chave (tenho uma cota de trocadilhos por livro a preencher) é a igualdade. Também

é útil pegar partes da chave quando você estiver mapeando para o banco de dados.

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

217

Se você usar a mesma estrutura básica para todas as chaves, pode executar toda a manipulação de chaves em uma Camada Supertipo (444). Você pode colocar comportamento padrão, que funcionará para a maioria dos casos na Camada Supertipo

(444) e estendê-la para os casos excepcionais nos subtipos particulares.

Você pode ter uma única classe chave, que compreende uma lista genérica de

objetos chave, ou uma classe chave para cada classe do domínio com campos explicitos para cada parte da chave. Normalmente prefiro ser explícito, porém, neste caso,
não estou certo de que compense tanto assim. Você acaba com muitas classes pequenas que não fazem nada interessante. O maior benefício é que você pode evitar os er-

ros causados por usuários, colocando os elementos da chave na ordem errada, mas

isso não parece ser um grande problema na prática.

Se é provável que você vá importar dados de diferentes instâncias de bancos de
dados, você precisa se lembrar que terá colisões de chaves a menos que tenha algum
esquema para separar as chaves entre os diferentes bancos de dados. Você pode resolver este problema com algum tipo de migração de chaves nas importações, mas

isso pode facilmente se tornar confuso.
Obtendo Uma Nova Chave

Para criar um objeto, você precisará de uma chave. Isso

parece ser uma questão simples, mas, muitas vezes, pode ser um problema conside-

rável. Você tem três escolhas básicas: deixar o banco de dados gerá-la automaticamente, usar uma GUID ou gerar a sua própria.
Deixar o banco de dados gerar a chave deveria ser o caminho mais fácil. Cada
vez que você insere dados no banco de dados, este gera uma chave primária única
sem que você tenha de fazer nada. Parece bom demais para ser verdade, e, infelizmente, muitas vezes é. Nem todos os bancos de dados fazem isso da mesma manei-

ra. Muitos dos que fazem lidam com isso de um modo que causa problemas para o
mapeamento objeto-relacional.

O método de geração automática mais comum é declarar um campo auto-gera-

do, o qual, toda vez que você inserir uma linha, é incrementado para um novo valor.

O problema com este esquema é que você não consegue determinar facilmente qual

valor foi criado para a chave. Se você quiser inserir um pedido e diversas linhas de

itens, você precisa da chave do novo pedido de modo que você possa colocar o valor

na chave estrangeira da linha do item. Além disso, você precisa desta chave antes
que a transação seja confirmada, de modo que possa gravar tudo dentro da transa-

ção. Infelizmente, os bancos de dados normalmente não lhe dão esta informação, de

modo que você geralmente não pode usar este tipo de geração automática em qual-

quer tabela na qual precise inserir objetos associados.

Uma abordagem alternativa à geração automática é um contador do banco de
dados, o qual o Oracle usa com a sua seqiiéncia. Uma seqiiéncia Oracle funciona enviando um comando select que referencia uma seqiiéncia. O banco de dados então retorna um conjunto de registros SQL consistindo no próximo valor na seqiiéncia. Vo-

cê pode configurar o incremento de uma seqiiéncia para qualquer valor inteiro, o que

lhe permite obter diversas chaves de uma só vez. A pesquisa da seqiiéncia é automa-

ticamente executada em uma transação separada, de forma que acessar a seqiiéncia
não bloqueará outras transações inserindo registros no banco ao mesmo tempo. Um

contador do banco de dados como este é perfeito para nossas necessidades, mas não
é padrão e não está disponível em todos os bancos de dados.

Um GUID (Identificador Globalmente Único) é um número gerado em uma

máquina que tem a garantia de ser único em todas as máquinas no espaço e no tem-

po. Muitas vezes plataformas lhe dão a API para gerar um GUID. O algoritmo é in-

2

218

Parte Il e Os PaprOes

teressante e envolve endereços de placas ethernet, hora do dia em nanossegundos,

números de identificação dos chips e provavelmente o número de fios de cabelo no
seu pulso esquerdo. Tudo que importa é que o número resultante é completamente

único e desse modo uma chave segura. A única desvantagem do GUID é que a cha-

ve resultante é grande e isso pode ser igualmente um grande problema. Sempre há

ocasiões em que alguém tem que digitar uma chave em uma janela ou expressão
SQL, e chaves longas são difíceis tanto de digitar quanto de ler. Elas também podem

levar a problemas de desempenho, especialmente com índices.

A tiltima opção é gerar a sua própria chave. Um mecanismo simples para siste-

mas pequenos é fazer uma varredura de tabela usando a função SQL max para en-

contrar a maior chave na tabela e então incrementá-la de uma unidade para usá-la.
Infelizmente, essa leitura bloqueia a tabela inteira enquanto estiver sendo executada,
o que significa que funciona bem se as inserções forem raras, mas seu desempenho

será muito diminuído se você tiver inserções rodando concorrentemente com atuali-

zações na mesma tabela. Você também tem que assegurar-se de que possui um com-

pleto isolamento entre as transações, caso contrário pode acabar com diversas tran-

a
5

sações obtendo o mesmo valor de ID.

Uma abordagem melhor é usar uma tabela de chaves separada. Esta tabela tipi-

camente tem duas colunas: nome e próximo valor disponível. Se você usar chaves

únicas no banco de dados, terá apenas uma linha nesta tabela. Se usar chaves únicas

por tabela, terá uma linha para cada tabela no banco de dados. Para usar esta tabela

de chaves, tudo o que você precisa fazer é ler essa linha e anotar o número, incremen-

tar o número e gravá-lo de volta na linha. Você pode pegar muitas chaves de uma só
vez adicionando um número apropriado quando atualizar a tabela de chaves. Isso di-

minui as custosas chamadas ao banco de dados e reduz a disputa na tabela de chaves.

Se você usar uma tabela de chaves, é uma boa idéia projetá-la de modo que o

acesso a ela esteja em uma transação separada daquela que atualiza a tabela na qual
você esteja inserindo. Digamos que eu esteja inserindo um pedido na tabela de pedi-

dos. Para fazer isso, precisarei bloquear a linha da tabela de pedidos na tabela de chaves com um bloqueio de gravação (já que estou atualizando). Esse bloqueio irá durar
enquanto durar a transação na qual estou, bloqueando com isso qualquer outra pes-

soa que queira uma chave. Para chaves únicas por tabela, isso significa qualquer pes-

soa inserindo na tabela de pedidos. Para chaves únicas no banco de dados, isso sig-

nifica qualquer pessoa inserindo em qualquer lugar.
Colocando o acesso à tabela de chaves em uma transação separada, você só bloqueia a linha para essa transação, que é muito mais curta. O aspecto negativo é que,
se você desfizer sua inserção na tabela de pedidos, a chave que você obteve da tabe-

la de chaves fica perdida para todos. Felizmente números são baratos, então este não

é um grande problema. Usar uma transação separada também lhe permite obter o ID

assim que você cria o objeto na memória, o que muitas vezes ocorre um pouco antes
de você abrir a transação para confirmar a transação de negócio.
Usar uma tabela de chaves afeta a escolha entre chave única por tabela ou por

banco de dados. Se você usar uma chave única por tabela, tem que adicionar uma linha na tabela de chaves toda vez que acrescentar uma tabela no banco de dados. Is-

to é mais trabalhoso, porém reduz a disputa pela linha. Se você mantiver seus aces-

sos à tabela de chaves em uma transação separada, a disputa não é um problema tão
grande, especialmente se você obtém várias chaves em uma única chamada. Todavia se você não conseguir fazer com que a atualização da tabela de chaves fique em

uma transação separada, você terá um forte argumento contra chaves únicas por

banco de dados.

CapiTULO 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

219

É bom separar o código para a obtenção de uma nova chave na sua própria clas-

se, pois isso facilita a criação de um Stub de Serviço (469) para propósitos de testes.
Quando

Usá-lo

Use um Campo Identidade quando houver um mapeamento entre os objetos na memória e as linhas em um banco de dados. Isso ocorre normalmente quando você usa um
Modelo de Domínio (126) ou um Gateway de Linhas de Dados (158). Você não precisa deste mapeamento se estiver usando um Roteiro de Transação (120), um Módulo Tabela
(134) ou um Gateway de Tabela de Dados (151).

Para um objeto pequeno com semântica de valor, como um objeto do tipo dinheiro ou uma faixa de datas que não terão sua própria tabela, é melhor usar um Va-

lor Embutido (261). Para um grafo complexo de objetos que não precise ser pesquisa-

do dentro do banco de dados relacional, o LOB Serializado (264) é normalmente mais

fácil de escrever e apresenta um desempenho melhor.
Uma alternativa ao Campo Identidade é estender o Mapa de Identidade (196) para
manter a correspondência. Isso pode ser usado para sistemas no qual você não quei-

ra armazenar um Campo Identidade no objeto em memória. O Mapa de Identidade (196)

precisa procurar em ambas as direções: dé-me a chave de um objeto ou o objeto cor-

respondente a uma chave. Não costumo ver isso com muita freqiiéncias porque geralmente é mais fácil armazenar a chave no objeto.

Leitura Adicional

[Marinescu] discute várias técnicas para a geração de chaves.
Exemplo: Chave Integral (C#)
A forma mais simples de um Campo Identidade é um campo inteiro no banco de dados
que mapeia para um campo inteiro em um objeto na memória.

class ObjetoDoDomínio...

public const long PROCURADOR.
public long Id = PROCURADOR1D;

public Boolean éNovo( ) {return Id == PROCURADOR ID;)

Um objeto que tenha sido criado na memória mas que não tenha sido salvo no

banco de dados não terá um valor para a sua chave. Para um objeto .NET este valor

é um problema, uma vez que valores .NET não podem ser nulos. Eis por que o valor
do procurador.

A chave se torna importante em dois locais: na busca e na inserção. Para uma

busca você precisa formar uma consulta usando uma chave na cláusula WHERE. Em
-NET você pode carregar muitas linhas em um conjunto de dados e então selecionar
uma delas em particular com uma operação de busca.
class MapeadorDeJogadorDeCriquete...
public JogadorDeCriquete Buscar

}

(long id)

{

return (JogadorDeCriquete) Buscadbstrata
(id) ;

2

220

Parte Il e Os PaprOes

class Mapeador...
protected ObjetoDoDomínio BuscaAbstrata (long id) (

)

DataRow linha = BuscarLinha (id);
return (linha == null) ? null: Buscar (Linha) ;

protected DataRow BuscarLinha

(long id) (

String filtro = String.Format
("id = {0}", id);

DataRow[ | resultados = tabela.Select (filtro);

}

return (resultados.Length == 0) 2 null: resultados
[0] ;

public ObjetoDoDomínio Buscar (DataRow linha) {

}

ObjetoDoDonínio resultado = CriarobjetoDoDomínio( ) ;
Carregar (resultado, linha) ;
return resultado;

abstract protected ObjetoDoDominio CriardbjetoDoDominio( );

2
5

A maior parte desse comportamento pode ficar na Camada Supertipo (444), mas

frequentemente você terá de definir o método de busca na classe concreta apenas para encapsular o downcast. É claro que você pode evitar isso em uma linguagem que
não use a tipagem em tempo de compilação.
Com um simples Campo Identidade inteiro, o comportamento de inserção também pode ser armazenado na Camada Supertipo (444).

class Mapeador...
public virtual long Inserir (ObjetoDoDomínio arg) (
DataRow linha = tabela.NewRow( );
arg.Id = lerPréximoID( );

linha["id!] = arg.1d;
Gravar (arg, linha) ;
tabela. Rows.Ada (Linha) ;
return arg. Id;

Essencialmente, a inserção envolve a criação de uma nova linha e o uso da pró-

xima chave nela. Assim que você a tiver, você pode gravar os dados do objeto locali-

zado na memória nessa nova linha.

Exemplo: Usando uma Tabela de Chaves (Java)
por Matt Foemmel e Martin Fowler
Se o seu banco de dados suportar um contador e você não estiver preocupado com o
fato de ficar dependente de um SQL específico de um determinado banco de dados,

deve usar o contador. Mesmo se você estiver preocupado com o fato de ficar dependente de um banco de dados, ainda assim você deve considerá-lo — desde que o seu
código de geração de chaves esteja bem encapsulado, você sempre poderá alterá-lo
para um algoritmo portável mais tarde. Você pode até mesmo ter uma estratégia

[Gang of Four] para usar contadores quando você os tiver e fazer os seus próprios em

caso contrário.

Cairuto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

221

Por enquanto, vamos supor que temos que fazer isso do modo difícil. A primei-

ra coisa de que precisamos é uma tabela de chaves no banco de dados.
CREATE TABLE chaves (nome varchar primary key, próximoID int)
INSERT INTO chaves ('pedidos", 1)

Esta tabela contém uma linha para cada contador no banco de dados. Neste ca-

so inicializamos a chave com 1. Se você estiver pré-carregando dados no banco de

dados, precisará configurar o contador para um número apropriado. Se você quiser

chaves únicas para todo o banco de dados, precisará de apenas uma linha. Se quiser
chaves únicas por tabela, precisará de uma linha por tabela.
Você pode encapsular todo o seu código de geração de chaves na sua própria
classe. Dessa maneira é mais fácil usá-lo mais extensamente em uma ou mais aplica-

ções, e é mais fácil colocar a reserva de chave na sua própria transação.

Construímos um gerador de chaves com sua própria conexão de banco de da-

dos, juntamente com a informação sobre quantas chaves pegar do banco de dados de
cada vez.
class GeradorDeChaves...

private Comection con;
private String nomeDaChave;
private long próximora;
private long máximord;

private int incrementarPor;
public GeradorDeChaves (Connection con, String nomeDaChave, int incrementarPor)
this.con

{

= con;

this.noneDaChave = noneDaChave;
this. incrementarPor = incrementarPor;
proximold = méximold = 0;
try {

conn.setAutoConmit (false) ;

) catch (SQLException exc)

(

throw new ApplicationException("Incapaz de desligar a confirmação

automática,

exc);

Precisamos assegurar que nenhuma confirmação automática está acontecendo, já que é fundamental ter a seleção e a atualização operando em uma mesma

transação.

Quando pedimos uma nova chave, o gerador verifica se existe uma em cache

uma vez de ir ao banco de dados.
class GeradorDeChaves...

public synchronized Long próximaChave ( ) {
if (préximold == méximold) {
reservartds( );
return new Long (próximord++) ;

2

222

Parte ll e Os Parões

Se o gerador não tiver uma chave em cache, ele precisa ir ao banco de dados.
class GeradorDeChaves...

private void reservarlds(

) {

PreparedStatement dec = null;
ResultSet rs = null;
long préximoldNovo;

try {
dec = con.prepareStatement ("SELECT préximold FROM chaves WHERE nome = ?
FOR UPDATE");
dec.setString(1, nomeDaChave) ;
re = dec.executeQuery( );
re.next( );

}

próximoIdNovos rs.gettong(1) ;

catch

(SQLException exc)

{

throw new ApplicationException("Incapaz

de gerar ids",

)
finally {
DB.cleanUp (dec, rs);
)
long máximoIdNovo = próximoIdNovo + incrementarPor;

2
5

exc) ;

dec = null;

try {
dec = con.prepareStatement ("UPDATE chaves SET préximold = ? WHERE nome = ? ");
ec.setLong(1, máximoIdNovo) ;
dec.setString(2,

nomeDaChave) ;

dec.executeUpdate| |;
conn.commit ( );

próxime

)

máximold = máximoIdNovo;

catch

}

próximordNovo;

(SQLException exc)

{

throw new ApplicationException("Incapaz de gerar ids", exc);

finally {
DB.cleantp (dec) ;

}

}

Nesse caso, usamos SELECT

... FOR UPDATE para dizer ao banco de dados para

manter um bloqueio de gravação sobre a tabela de chaves. Este é um comando espe-

cífico do Oracle, de modo que sua milhagem irá variar se você estiver usando algo

diferente. Se você não puder efetuar um bloqueio de gravação no SELECT, corre o risco da transação falhar se outra pessoa chegar lá antes de você. Nesse caso, contudo,

você pode executar novamente reservarlds com segurança, até que você obtenha um
conjunto imaculado de chaves.

Exemplo: Usando uma Chave Composta (Java)

Usar uma chave inteira simples é uma solução boa e simples, porém muitas vezes
você precisa de chaves de outros tipos ou de chaves compostas.

Capíruto 12

Uma Classe Chave

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

223

Assim que você precisar de algo a mais, vale a pena criar uma

classe chave. Uma classe chave precisa ser capaz de armazenar múltiplos elementos
da chave e ser capaz de dizer se duas chaves são iguais.
class Chave..

private Object [ ] campos;
public boolean éIgual (Object obj) {
if (1 (obj instanceof Chave)) return false;
Chave outraChave = (Chave) obj;
if (this.campos. length != outraChave.campos.length) return false;
for (int

0; ic campos. length; i++)

i£(! this. campos [i] .equals (outraChave.campos
[1])] return false;
return true;

O modo mais elementar de criar uma chave é com um array de parâmetros.
class Chave...

public Chave [Object [ ] campos) (

verificarChavenâolula (campos) ;
this.campos = campos;

private void verificarChaveNoNula (Object [ ] campos) (

if (campos == null) throw new IllegalArgument Exception ("Não pode haver uma chave nula");

for (int i= 0; ic campos. length; i++)
if (campos[i] == null)
throw new IllegalargumentBxception
("Não pode haver um elemento nulo na chave”);

Se você descobrir que frequentemente cria chaves com certos elementos, você

pode acrescentar construtores convenientes. Exatamente quais construtores devem
ser criados, vai depender dos tipos de chaves que sua aplicação possui.
class Chave...

public Chave [long arg) (

this.campos = new Object [1];

this.campos[0] = new Longlarg) ;

}
public Chave (Object campo) {
if (campo == null) throw new IllegalargumentException
("Não pode haver uma chave nula”) ;
this.campos = new Object [1];

}

this.campos[0] = campo;

public Chave (Object argl, Object arg2) {

this.campos = new Object [2] ;
this.campos[0] = argl;
this.campos[1]

= arg2;

verificarChaveNãoNula (campos) ;
Não tenha receio de adicionar esses métodos por conveniência. Afinal de contas, conveniência é importante para todos que usam as chaves.

224

Parte ll e Os Parões

De modo similar, você pode adicionar métodos de acesso para ler partes das

chaves. A aplicação precisará disso para os mapeamentos.
class Chave
public Object valor

return campos [i] ;

(int i) {

}
public Object valor () {
verificarsefchavesimples( |;
return campos
[0] ;
}
private void verificarSefChaveSimples (} {
if (campos. length > 1)
throw new IllegalStateBxception("Néo posso pegar o valor de uma chave composta") ;
}
public long valorLongo( ) {
verificarSefChavesimples( );

a
5

return valorLongo
(0) ;

}
public long valorLongo(int i){
i€ (1 (campos [i] instanceof Long) )
throw new IllegalState8xception
("No posso usar valorLongo sobre uma chave que não seja do

tipo long");

}

return ((Long) campos(i]} .longvalue( );

Neste exemplo mapearemos para uma tabela de pedidos e uma de linhas de

itens. A tabela de pedidos tem uma chave primária inteira simples, a chave primária

da tabela de linhas de itens é uma chave composta pela chave primária da tabela de

pedidos e um número seqiiencial.

CREATE TABLE pedidos (ID int primary key, cliente varchar)
CREATE TABLE linhas itens (idDoPedido int, seq int, quantia int, produto varchar,
primary key (idDoPedido, seq))
A Camada Supertipo (475) para objetos do dominio precisa ter um campo chave.

class ObjetoDoDonínioConchave...
private Chave chave;

protected ObjetoDoDominioComChave (Chave 1D) {

}

this.chave = ID;

protected ObjetoDoDominioComChave(

}
public Chave lerChave( )(
return

chave;

) {

}
public void gravarChave (Chave chave) {
this.chave = chave;
}

Capitulo 12

Lendo

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

225

Assim como em outros exemplos neste livro, dividi o comportamento em

busca (que chega à linha correta no banco de dados) e carregar (que carrega dados

dessa linha para o objeto do domínio). Ambas as responsabilidades são afetadas pe-

lo uso de um objeto chave.
A diferença principal entre estes e os outros exemplos neste livro (que usam cha-

ves inteiras simples) é que temos que fatorar certas partes do comportamento que são
sobrescritas por classes que têm chaves mais complexas. Neste exemplo, pressupo-

nho que a maioria das tabelas usa chaves inteiras simples. Entretanto, algumas usam
algo mais, de modo que tornei padrão o caso do inteiro simples e embuti o comportamento para isso no mapeador Camada Supertipo (444). A classe dos pedidos é um

desses casos simples. Aqui está o código para o comportamento de busca:
class HapeadorDePedido....
public Pedido buscar (Chave chave) {
return (Pedido) buscanbstrata (chave) ;
}
public Pedido buscar (Long id) {
return buscar (new Chave (id) );

}

protected String stringDoComandoDeBusca

( ) {

return "SELECT id, cliente FROM pedidos WHERE id = 2";

}
class Mapeadorabstrato. ..
abstract protected String stringDoConandoDeBusca ( );
protected Map mapaCarregado = new Hashlap( ) ;
public ObjetoDoDominioComChave buscabstrata

(Chave chave)

{

ObjetoDoDonínioConChave resultado = (ObjetoDoDominioConChave) mapaCarregado.get (chave);
if (resultado != null) return resultado;
ResultSet rs = null;
PreparedStatement conandoDeBusca = null;
try {
comandoDeBusca = DB.prepare(stringDoComandoDeBusca

carregarConandoDeBusca
(chave, comandoDeBusca) ;
rs = comandoDeBusca.executequery ) ;

( ));

rs.next ( );
if (rs.isAfterLast( ) ) return null;

resultado = carregar (rs) ;
return resultado;

) catch (SQLException e) {

throw new ApplicationException (e);

} finally {

}

}

DB.cleantp (comandoDeBusca, rs) ;

// método gancho para chaves que não sejam inteiros simples
protected void carregarComandoDeBusca (Chave chave, PreparedStatement buscador)
throws SQLException {

}

buscador.setLong(1, chave.valorLongo( }) ;

226

Parte ll e Os Parões

Extraí a construção do comando de busca, já que isso requer que parâmetros di-

ferentes sejam passados para o comando preparado. A linha de item é uma chave
composta, então ela precisa sobrescrever esse método.

class MapeadorDeLinhaDeitem. ..
public LinhaDeItem buscar (long idDoPedido, long seq) (
Chave chave = new Chave (new Long (idDoPedido), new Long (seg) ) ;

}

return (LinhaDeltem) buscaAbstrata (chave);

public LinhaDeltem buscar (Chave chave) (
return (LinhaDeltem) buscaAbstrata (chave);

}

public String stringDoComandoDeBusca( ) {
return

"SELECT idDoPedido, seq, quantia, produto "+
* FROM linhas itens *+
" WHERE (idDoPedido = 2) AND (seg = 2)";

2
5

}
| métodos gancho sobrescritos para a chave composta

protected void carregarConandoDeBusca (Chave chave, PreparedStatement buscador) throws SQLException {

}

buscador. setLong (1, idDoPedido (chave) ) ;
buscador. setLong (2, númeroDasegiência (chave) ) ;

|| métodos auxiliares para extrair valores apropriados da chave da linha de item

private static long idDoPedido (Chave chave) (
return chave.longValue(

private static long núneroDaSegiência (Chave chave) {
return chave. Jongvalue (1);
}
Além de definir a interface para os métodos de busca e fornecer uma string SQL
para o comando de busca, a subclasse precisa sobrescrever o método gancho para

permitir a inclusão de dois parâmetros no comando SQL. Também escrevi dois métodos auxiliares para extrair os pedaços da informação da chave. Isso cria código

mais claro do que aquele que eu obteria apenas colocando métodos explícitos de
acesso com índices numéricos da chave. Esses índices literais cheiram a problemas.

O comportamento de carga mostra uma estrutura semelhante — o comportamento padrão na Camada Supertipo (444) para chaves inteiras simples, sobrescrito para os casos mais complexos. Neste caso, o comportamento da carga do pedido se parece com:

class MapeadorAbstrato...
protected ObjetoDoDominioComChave carregar (ResultSet rs) throws SQLException (

Chave chave = criarChave
(rs) ;
if [mapaCarregado. containskey (chave) ) return (ObjetoDoDominioComChave)
mapaCarregado. get (chave) ;
ObjetoDoDonínioConChave resultado = fazerCarga (chave, rs);
mapaCarregado.put (chave, resultado) ;
return resultado;

}
abstract protected ObjetoDoDominioConChave fazerCarga (Chave id, ResultSet rs) throws SQLException;

CapiTuLo 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

227

// método gancho para chaves que não sejam inteiros simples

protected Chave criarChave

(ResultSet rs) throws SQLException (

return new Chave (rs.getLong(1)) ;

}
class MapeadorDePedido. .
protected ObjetoDoDonínioConChave fazerCarga (Chave chave, ResultSet rs)
throws SQLException {

String usuário = rs.getString(*usuério") ;
Pedido resultado = new Pedido(chave, usuário) ;
RegistoMapeador. LinhaDeltem( ) .carregarTodasAsLinhasDeltenDo (resultado) ;
return resultado;

Alinha de item precisa sobrescrever o método gancho para criar uma chave baseada em dois campos.
class MapeadorDeLinhaDelten. . .

protected ObjetoDoDominioComChave fazerCarga (Chave chave, ResultSet rs) throws SQLException {
Pedido oPedido = RegistroMapeador pedido( ) .busca(idDoPedido(chave)} ;

}

return fazerCarga

(chave,

rs, oPedido) ;

protected ObjetoDoDonínioConChave fazerCarga (Chave chave, ResultSet rs, Pedido pedido)
throws SQLException
{
LinhaDeltem resultado;
int quantia = rs.getInt ("quantia") ;
String produto = rs.getString ("produto") ;
resultado = new LinhaDeltem(chave, quantia, produto) ;
pedido.adicionarLinhaDeItem (resultado) ; //conecta ao pedido
return resultado;
}
//sobrescreve 0 caso padrão
protected Chave criarChave

(ResultSet rs) throws SQLException {

Chave chave = new Chave (new Long(rs.getLong("idDoPedido")),
return

chave;

new Long(rs.getLong("seq"))) ;

A linha de item também tem um método de carga separado para usar quando
estiver carregando todas as linhas do pedido.
class MapeadorDeLinhaDeltem. . .
public void carregarTodasAsLinhasDeltemDo

PreparedStatement dec = null;

(Pedido arg)

ResultSet rs = null;

try {
dec = DB. prepare (stringDaBuscaDoPedido) ;
dec.setLong(1,

arg.lerChave(

while

))

rs = dec.executeQuery( );
(rs.next{

}.longValue(

carregar (rs, arg);

) catch (SQLException e) {

throw new ApplicationException

(e);

});

{

228

Parte Il e Os PaprOes

} finally {DB.cleanUp(dec, rs);

}

}

private final static String stringDaBuscaDoPedido =

"SELECT idDoPedido, seq, quantia, produto * +
"PROM Linhas itens * +
"WHERE idDoPedido = 2";

protected ObjetoDoDomínioComChave carregar (ResultSet rs, Pedido pedido) throws SQLException {

Chave chave = criarChave (rs) ;
if (mapacarregado. containskey (chave)) return (ObjetoDoDonínioConChave) mapacarreg
get ado.
(chave) ;
ObjetoDoDonínioConChave resultado = fazerCarga (chave, rs, pedido) ;
mapaCarregado. put (chave, resultado) ;
return resultado;

Você precisa do tratamento especial porque o objeto Pedido só é colocado no

2
5

Mapa de Identidade (196) do pedido depois de criado. Criar um objeto vazio e inserilo diretamente no Campo Identidade evitaria a necessidade disso (página 173-174).
Inserção

Assim como a leitura, a inserção tem uma ação padrão para uma chave in-

teira simples e os ganchos para serem sobrescritos para chaves mais

interessantes.

No supertipo do mapeador forneci uma operação para atuar como a interface, junto

com um método modelo para executar o trabalho da inserção.

class Hapeadorabstrato...
public Chave inserir (ObjetoDoDominioComChave sujeito) {

try {

return executarInserção (sujeito,

} catch (SQLException e) {

)

}

objetoBuscarPréximaChaveNoBancoDeDados | )) ;

throw new ApplicationException
(e);

protected Chave executarInserção (ObjetoDoDominioComChave sujeito, Chave chave) throws SQLException {

sujeito.gravarChave
(chave) ;
PreparedStatement dec = DB.prepare [stringDoConandoDeInserção( |);
inserirChave (sujeito, dec) ;
inserirDados (sujeito, dec) ;
dec.execute( |;

mapaCarregado.put (sujeito. lerChave( ), sujeito);

}

return sujeito. lerChave( ));

abstract protected String stringDoComandoDeInserção( );
class MapeadorDePedido...
protected String stringDoComandoDeInserção ( ) {

}

return "INSERT INTO pedidos VALUES (?,2)";

Os dados do objeto vão para o comando insert por meio de dois métodos que

separam os dados da chave dos dados básicos do objeto. Faço isso porque posso fornecer uma implementação padrão para a chave que funcionará para qualquer classe,
tal como Pedido, que usa a chave inteira simples padrão.

Caíruto 12

e PADRÕES EsTRUTURAIS OBJETO-RELACIONAIS

229

class Mapeadorabstrato...

protected void inserirChave (ObjetoDoDoninioConChave sujeito, PreparedStatenent dec)
throws SQLException
{
dec. setLong(1, sujeito.lerChave( ).longvalue( |);

}

O resto dos dados para o comando insert é dependente da subclasse em particu-

lar, então este comportamento é abstrato na superclasse.

class Mapeadorabstrato. .
abstract protected vos inserirDados (ObjetoDoDonínioConchave sujeito, PreparedStatenent dec)
throws SQLException;
class MapeadorDePedido. ..
protected void inserirDados (ObjetoDoDonínioConchave sujeitoabstrato, Preparedstatement dec)
throws SQLException {

try {

Pedido sujeito = (Pedido) sujeitoAbstrato;

dec. setString(2, sujeito. lerCliente( )) ;

} catch (SQLException e) {

}

throw new Applicat ionBxception(e} ;

A linha de item sobrescreve esses dois métodos. Ela extrai dois valores para a
chave.

class MapeadorDeLinhaDeTtem...
protected String stringDoComandoDeInserção

( ) {

return "INSERT INTO linhas itens VALUES (?,

an

}
protected void inserirChave (ObjetoDoDonínioConchave sujeito, PreparedStatement dec)
throws SQLException
{
dec. setLong(1, idDoPedido(sujeito.lerChave( }));
dec. setLong(2, nimeroDaSeqiéncia(sujeito.lerChave( )));

Ela também fornece sua própria implementação do comando insert para o res-

to dos dados.

class MapeadorDeLinhaDeltem...

protected void inserirDados (ObjetoDoDonínioConChave sujeito, PreparedStatenent dec)
throws SQLException
{
LinhaDeltem item = (LinhaDeItem) sujeito;
dec.setInt(3, item.lerQuantia( ));
dec.setInt (4, item. lerProduto( ));

230

Parte Il e Os PaprOes

Colocar a carga dos dados no comando insert só vale a pena se a maior parte

das classes usarem o mesmo campo simples como chave. Se houver mais variação na

manipulação de chaves, então ter apenas um comando para inserir a informação é

provavelmente mais fácil.

Gerar a próxima chave do banco de dados também é algo que eu posso separar

em um caso padrão e um sobrescrito. Para o caso padrão posso usar o esquema da ta-

bela de chaves do qual falei anteriormente. Todavia, para a linha de item nos depa-

ramos com um problema. A chave da linha de item usa a chave do pedido como parte de sua chave composta. Entretanto, não existe uma referência da classe linha de

item para a classe pedido, de modo que é impossível mandar uma linha de item in-

serir a si mesma no banco de dados sem fornecer também o pedido correto. Isso leva

à sempre confusa abordagem de implementar o método na superclasse com uma ex-

ceção de operação não suportada.
class MapeadorDeLinhaDeltem...

public Chave inserir (ObjetoDoDonínioConChave sujeito) {

2
5

throw new UnsupportedOperat ionException

(FDeve fornecer um pedido ao inserir uma linha de item”);

}

public Chave inserir

(LinhaDeltem item, Pedido pedido)

{

try {
Chave chave = new Chave(pedido. lerChave( }.value( ), lerPróximonimeroDasegiência
(pedido) ) ;
return executarInserção (item, chave);
) catch (SQLException e) (

}

throw new ApplicationException(e);

É claro que podemos evitar isso tendo uma conexão de retorno da linha de item

para o pedido, tornando assim, efetivamente, a associação entre as duas classes bidi-

recional. Decidi não fazer isso aqui para ilustrar o que fazer quando você não tem essa conexão.

Fornecendo o pedido, é fácil obter da chave a parte referente ao pedido. O pró-

ximo problema é obter um número seqiiencial para a linha do pedido. Para encontrar
esse número, precisamos descobrir qual é o próximo número sequencial disponível
para um pedido, o que podemos fazer tanto com uma consulta em SQL usando max
ou olhando as linhas de pedido dos pedidos em memória. Para este exemplo, usarei
esta segunda opção.
class MapeadorDeLinhaDeltem...
private Long lerPróximoNúmeroDaSegúência

(Pedido pedido)

carregarTodasAsLinhasDeltemDo (pedido) ;
Iterator it = pedido.lerltens( } iterator( |;
LinhaDeItem candidata = (LinhaDelten) it.next( );

{

while (it.hasNext( )) (

LinhaDeltem esteltem = (LinhaDeltem) it.next( );
if (esteItem.lerChave( ) = null) continue;

if (númeroDaSegiência (esteltem) > númeroDas

)
return new Long (númeroDaSegiência (candidata) + 1);

ncia (candidata) ) candidata = esteltem;

