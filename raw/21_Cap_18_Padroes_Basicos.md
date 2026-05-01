# 21_Cap_18_Padroes_Basicos

456

Parte Il e Os PaprOes

pativeis como um erro. Em algumas situações mais sofisticadas, você pode usar a
idéia de Ward Cunningham de um saco de dinheiro. Esse é um objeto que contém

moedas de tipos diferentes juntas em um objeto. Este objeto pode então participar de

cálculos como qualquer objeto dinheiro. Ele também pode ser avaliado em uma moe-

da em particular.

A multiplicação e a divisão acabam sendo mais complicadas devido a proble-

mas de arredondamento. Quando você multiplica dinheiro, faça isso com uma grandeza escalar. Se você quiser adicionar uma taxa de 5% a uma conta, multiplique-a
por 0,05, de modo que você se preocupe apenas com a multiplicação por tipos numéricos normais.
A complicação vem com o arredondamento, especialmente ao alocar dinheiro

entre lugares diferentes. Aqui está o simples enigma de Matt Foemmel. Suponha que

eu tenha uma regra de negócio que diga que tenho de alocar a totalidade de uma im-

portância em dinheiro entre duas contas: 70% para uma e 30% para outra. Tenho 5
centavos para alocar. Se eu fizer o cálculo matemático, acabo com 3,5 centavos e 1,5

centavo. Não importa de que maneira eu arredonde essas quantias, arrumo um problema. Se eu fizer o arredondamento costumeiro para o mais próximo, então 1,5 vira

2e 3,5 vira 4. Assim eu acabo ganhando 1 centavo. Arredondar para baixo me dá 4
centavos e arredondar para cima me dá 6. Não há um esquema geral de arredonda-

mento que eu possa aplicar que tanto evite a perda quanto o ganho de 1 centavo.
Tenho visto diversas soluções para esse problema.

© Talvez a mais comum seja ignorá-lo - afinal, é apenas 1 centavo aqui e ali. Entretanto isso tende a deixar os contadores compreensivelmente nervosos.

* Durante a alocação você sempre executa a última alocação subtraindo do que
vocêjá alocou até o momento. Isso evita a perda de centavos, mas você pode

ter o acúmulo de uma quantia significativa de centavos na última alocação.

* Permita aos usuários de uma classe Dinheiro declarar o esquema de arredondamento quando eles chamam o método. Isso permite a um programador di-

zer que o caso dos 70% arredonda para cima e o dos 30% para baixo. As coi-

sas ficam mais complicadas quando você tem de fazer a alocação por dez

contas em vez de duas. Você também tem que se lembrar de arredondar. Pa-

ra encorajar as pessoas a lembrar, tenho visto algumas classes Dinheiro forcarem a existência de um parâmetro para arredondamento na operação de

multiplicação. Isso não apenas obriga o programador a pensar sobre qual arredondamento precisa, mas também poderia lembrá-lo dos testes a escrever.
Entretanto, isso se torna confuso se você tiver muitos cálculos de taxas que

sejam todos arredondados da mesma maneira.

* Minha solução favorita: tenha uma função de alocação na classe Dinheiro. O

Dinheiro.

parâmetro para o alocador é uma lista de números, representando a propor-

ção a ser alocada (ele se pareceria com algo como unDinheiro.alocar [7,3])). O
alocador retorna uma lista de objetos dinheiro, garantindo que nenhum cen-

tavo seja perdido espalhando-os pelos objetos dinheiro alocados de um mo-

do que, de fora, parece pseudo-randômico. O alocador tem suas falhas: você
tem que se lembrar de usá-lo e quaisquer regras precisas sobre para onde devem ir os centavos são difíceis de impingir.

Capíruto 18 e Paproes BAsicos

457

A questão fundamental aqui é entre usar multiplicação para determinar o valor

de uma cobrança proporcional (como um imposto) e usá-la para alocar uma soma de

dinheiro em diversos lugares. A multiplicação funciona bem no primeiro caso, mas

um alocador funciona melhor para este último. O importante é considerar sua intenção ao usar multiplicação ou divisão em um valor monetário.
Você pode querer converter de um tipo de moeda para outro com um método

como unDinheiro.converterPara (Moeda. DOLLARS) . O modo óbvio de fazer isso é procurar

uma taxa de câmbio e multiplicar por ela. Embora funcione em muitas situações, há

casos em que não funciona — novamente devido ao arredondamento. As regras de
conversão entre as moedas européias tinham arredondamentos específicos aplicados

que faziam uma simples multiplicação não funcionar. Assim, é sensato ter um objeto
conversor para encapsular o algoritmo.
As operações de comparação permitem-lhe ordenar objetos de dinheiro. Assim
como a operação de adição, as conversões precisam ter ciência da moeda. Você pode

escolher ou levantar uma exceção ao comparar moedas diferentes ou fazer uma con-

versão entre elas.
Uma classe Dinheiro pode encapsular o comportamento de impressão. Isso torna muito mais fácil fornecer uma boa apresentação em interfaces com o usuário e relatórios. Uma classe Dinheiro também pode analisar uma string para fornecer um

mecanismo de entrada que conheça moedas, o que, novamente, é muito útil para a

interface com o usuário. Aqui, as bibliotecas da sua plataforma podem ajudar. Cada

vez mais as plataformas fornecem suporte à globalização com formatadores de nú-

meros explícitos para países específicos.
Armazenar um objeto Dinheiro em um banco de dados sempre traz um proble-

ma, uma vez que os bancos de dados também não parecem entender que dinheiro é

importante (embora seus vendedores entendam.) O caminho óbvio a tomar é usar
um Valor Embutido (261), o que resulta em armazenar uma moeda para cada objeto

dinheiro. Isso pode ser exagerado quando, por exemplo, uma conta possa ter todos
os seus lançamentos em libras. Neste caso, você pode armazenar a moeda na conta e
alterar o mapeamento do banco de dados para trazer a moeda da conta sempre que
você carregar os lançamentos.
Quando

Usá-lo

Uso Dinheiro para quase todo cálculo numérico em ambientes orientados a objetos. A

razão principal é encapsular a manipulação do comportamento de arredondamento,

o que ajuda a reduzir os problemas de erros de arredondamento. Outra razão para

usar Dinheiro é tornar muito mais fácil o trabalho com diversas moedas. A objeção

mais comum a Dinheiro é o desempenho, embora raramente eu tenha ouvido falar de

casos em que ele fez qualquer diferença perceptível, e mesmo nesses casos o encapsulamento, muitas vezes, torna mais fácil a otimização.

Exemplo: Uma Classe Dinheiro (Java)
por Matt Foemmel e Martin Fowler

A primeira decisão é que tipo de dados usar para a quantia. Se alguém precisar ser
convencido de que um número de ponto flutuante é uma má idéia, peça a ele para

rodar este código.

g

458

Parte Il e Os PaprOes

double val = 0.00;
0; ie 10; i++) val += 0.10;

System.out .println(val == 1.00);

Com os números em ponto flutuante descartados, a escolha fica entre inteiros e

decimais de ponto fixo, o que em Java se resume a BigDecimal, BigInteger e long. Usar

um valor inteiro de fato torna a matemática interna mais fácil, e se usarmos long po-

demos usar primitivas e, assim, ter expressões matemáticas legíveis.

class Dinheiro. .
private long quantia;
private Currency moeda;
Estou usando uma quantia inteira, ou seja, a quantidade da menor unidade bá-

sica da moeda, a qual chamo de centavos no código porque este é um nome tão bom

quanto qualquer outro. Com um tipo long obtemos um erro de estouro (overflow) se o
número ficar grande demais. Se você nos der $92.233.720.368.547.758,09 escreveremos para você uma versão que use BigIntegers.

É útil fornecer construtores de diversos tipos numéricos.

public Dinheiro (double quantia, Currency nosda) (
this.moeda = moeda;

this.quantia = Math.round(quantia * fatorCentavos( ));

}
publ ic Dinheiro (long quantia, Currency moeda) {
this.moeda =

}

moeda;

this.quantia = quantia * fatorCentavos(

);

private static final int[ | centavos = new int[]

private int fatorCentavos ( ) {

return centavos (moeda.getDefaultFractionDigits(

(1, 10, 100, 1000};
)];

Diferentes moedas têm diferentes quantias fracionárias. A classe Currency de

Java 1.4 lhe informará o número de dígitos fracionários em uma classe. Podemos de-

terminar quantas unidades menores há em uma unidade maior elevando dez à po-

tência, mas isso é tão trabalhoso em Java que o array é mais fácil (e provavelmente
mais rápido). Estamos preparados para aceitar o fato de que este código falha se alguém usar quatro dígitos fracionários.
Embora, na maior parte do tempo, você vá querer usar diretamente as operações na classe dinheiro, há ocasiões em que você precisará acessar os dados encapsulados.

class Dinheiro...
public BigDecimal quantia ( ) {

Dinheiro.

return BigDecimal .valueOf (quantia, moeda.getDefaultPractionDigits( ));

public Currency moeda ( ) {

}

return

moeda;

CapituLo 18 e Paproes BAsicos

459

Você deve sempre questionar seu uso de métodos de acesso. Quase sempre há

uma maneira melhor que não quebrará o encapsulamento. Um exemplo que não po-

deríamos evitar é o mapeamento de banco de dados, como em Valor Embutido (261).
Se você usar muito frequentemente uma moeda para quantias literais, um cons-

trutor auxiliar pode ser útil.
class Dinheiro...

public static Dinheiro dólares (double quantia)

}

return new Dinheiro (quantia, Currency.
USD) ;

(

Como Dinheiro é um Objeto Valor (453), você precisará definir equals.
class Dinheiro...

public boolean equals (Object outro) {

}

return

(outro instanceof Dinheiro)

&& equals(

(Dinheiro) outro) ;

public boolean equals (Dinheiro outro) (

}

return moeda.equals(outro.moeda)

&& (quantia == outro.quantia) ;

E sempre que houver um método equals deveria haver um hash.
class Dinheiro...

public int hashcode( ) {

}

return

(int)

(quantia * (quantia >>> 32));

Começaremos pela aritmética com a adição e a subtração.
class Dinheiro...

public Dinheiro adicionar (Dinheiro outro) (
asserçãoMesnaloedaque (outro) ;
return novoDinheiro

(quantia + outro. quantia) ;

private void asserçãolesmaMoedaQue

(Dinheiro parâmetro)

(

Assert equals ("erro de comparação de moedas", moeda, parâmetro.moeda) ;

private Dinheiro novoDinheiro (Long quantia) (
Dinheiro dinheiro = new Dinheiro(
dinheiro.moeda = this.moeda,
dinheiro.quantia = quantia,
return dinheiro;

);

Perceba aqui o uso de um método fábrica privado que não faz a conversão

usual para a quantia baseada em centavos. Usaremos isso algumas vezes dentro do
próprio código da classe Dinheiro.
Com a adição definida, a subtração é fácil.

g

460

Parte ll e Os PADROES
class Dinheiro

public Dinheiro subtrair (Dinheiro outro) (
asserçãoMesmalioedaque (outro) ;
return novoDinheiro (quantia - outro.quantia) ;
O método base para a comparação é compararCom.
class Dinheiro..

public int compararCom(Objeto outro) {

}

return compararCon( (Dinheiro) outro) ;

public int compararCom(Dinheiro outro) {
asserçãoMesmalíoedaQue (outro) ;
if (quantia < outro.quantia)

return -1;

else if (quantia == outro.quantia) return 0;
else return 1;

Embora, atualmente, isso seja tudo o que você consegue na maioria das classes

Java, achamos que o código fica mais legível com os outros métodos de comparação
tais como estes.
class Dinheiro...

public boolean maiorDoQue (Dinheiro outro) (
return

(compararCom(outro)

> 0);

Agora estamos prontos para olhar a multiplicação. Estamos fornecendo um

modo de arredondamento padrão, mas você pode também configurar o seu próprio.
class Dinheiro. .

public Dinheiro multiplicar (double quantia) {
return multiplicar
(new BigDecimal (quantia)) ;
}

public Dinheiro multiplicar (BigDecimal quantia) (
return multiplicar (quantia, BigDecimal.ROUND
HALF EVEN) ;

}
public Dinheiro multiplicar (BigDecimal quantia, int modoDearredondamento) (
return new Dinheiro(quantia( ) multiplicar (quantia), moeda, modoDearredondamento) ;
)
Se você quiser alocar uma importância em dinheiro entre muitos destinatários

Dinheiro

e não quiser perder centavos, irá precisar de um método de alocação. O mais simples
deles aloca a mesma quantia (quase) entre vários destinatários.
class Dinheiro..

public Dinheiro( ] alocar(int n) (

Dinheiro resultadoBaixo = novoDinheiro(quantia / n);
Dinheiro resultadoAlto = novoDinheiro
(resultadoBaixo. quantia +1);

Carítuto 18 e PaprõEs BAsicos

Dinheiro[ ]

461

resultados = new Dinheiro [n] ;

int resto = (int) quantia % n;

for (int i = 0; i < resto; i++)
for (int i = resto; i < n; i++)
return resultados;

resultados[i]
resultados[i]

= resultadoAlto;
= resultadoBaixo;

Um algoritmo de alocação mais sofisticado pode lidar com qualquer razão.
class Dinheiro...

public Dinheiro[ ] alocar (longl ] razões) (
long total = 0;
for

(int i = 0; i < razões.length;

long resto = quantia;

i++)

total

razões[i];

Dinheiro [ ] resultados = new Dinheiro[razões.length) ;
for (int i = 0; ic resultados. length; i++) {
resultados
[il = novoDinheiro (quantia * razões[il / total);
resto -= resultados
[i] quantia;

for (int i = 0; i < resto; im) (

resultados[i] .quantias+;

return resultados;

Você pode usar isso para resolver o Enigma de Foemmel.
class Dinheiro.

public void testeAlocar2(

long[ ]

) {

alocação = (3,7);

Dinheiro[ ] resultado = Dinheiro. dólares (0.05) .alocar (alocação) ;
assertEquals (Dinheiro. dólares (0.02), resultado[0]);

assertEquals [Dinheiro.dólares (0.03), resultado[1]);

g

462

Parte ll e Os PADRÕES

Caso Especial (Special Case)
Uma subclasse que fornece comportamento especial
para casos particulares.

Cliente

Cliente
Não Encontrado

Cliente
Desconhecido

Os nulos são coisas complicadas em programas orientados a objetos porque eles
frustram o polimorfismo. Normalmente, você pode invocar o método bobo livremen-

te em uma variável que seja uma referência para um determinado tipo sem se preo-

cupar se o item pertence a este tipo ou uma subclasse. Com uma linguagem forte-

mente tipada, você pode até mesmo fazer o compilador verificar se a chamada é correta. Entretanto, já que uma variável pode conter um nulo, você pode se deparar com
um erro em tempo de execução ao invocar uma mensagem sobre um nulo, o que lhe
dará um belo e amigável rastreamento de pilha.
Se for possível que uma variável seja nula, você tem que lembrar de cercá-la

com código para testar a condição nula, de modo que você possa fazer a coisa certa

se um nulo estiver presente. Muitas vezes, a coisa certa é a mesma em muitos contex-

tos, então você acaba escrevendo código similar em muitos lugares - cometendo o
pecado da duplicação de código.
Os nulos são um exemplo comum de tais problemas, e outros aparecem regularmente. Em sistemas numéricos você precisa lidar com o infinito, que tem regras
especiais para coisas, como a adição, que quebram as invariantes costumeiras dos

números reais. Uma das minhas primeiras experiências em software de negócio foi

com um cliente de um serviço que não era completamente conhecido, referido como

“ocupante.” Todos estes sugerem a alteração do comportamento usual do tipo.

Em vez de retornar nulo, ou algum valor estranho, retorne um Caso Especial que
tenha a mesma interface da que o solicitante espera.

Como Funciona
A idéia básica é criar uma subclasse para tratar o Caso Especial. Assim, se você tiver

um objeto Cliente e quiser evitar verificações de nulos, cria um objeto Cliente nulo.
Pegue todos os métodos do Cliente e sobrescreva-os no Caso Especial para fornecer al-

gum comportamento inofensivo. Então, sempre que você tiver um nulo, coloque
2
6

uma instância do cliente nulo no seu lugar.

Geralmente, não existe nenhuma razão para distinguir entre instâncias diferen-

tes do cliente nulo, de modo que, frequentemente, você pode implementar um Caso
Especial com um peso-mosca [Gang of Four]. Você não pode fazer isso todo o tempo.

Capituto 18 e Paproes BÁsicos

463

Para um serviço, você pode acumular débitos de um ocupante, mesmo que você não

possa sempre entregar a conta, de modo que é importante manter os ocupantes separados.

Um nulo pode significar coisas diferentes. Um cliente nulo pode significar a nãoexistência de um cliente ou pode significar que existe um cliente mas não sabemos

quem ele é. Em vez de usar somente um cliente nulo, considere ter Casos Especiais se-

parados para clientes ausentes ou desconhecidos.
Um modo comum de um Caso Especial sobrescrever métodos é retornar outro

Caso Especial, então, se você pedir a um cliente desconhecido a sua última conta, po-

de bem obter uma conta desconhecida.
A aritmética de ponto flutuante IEEE 754 oferece bons exemplos de Casos Espe-

ciais com infinito negativo, infinito positivo e não-é-um-número (NaN). Se você di
dir por zero, em vez de obter uma exceção a qual terá de tratar, o sistema simples-

mente retorna NaN, e NaN participa de expressões aritméticas como qualquer outro

número de ponto flutuante.

Quando Usá-lo
Use o Caso Especial sempre que, em diversos lugares do sistema, você tiver o mesmo

comportamento após uma verificação condicional da ocorrência de uma instância

particular de uma classe, ou o mesmo comportamento após uma verificação da ocor-

rência de nulos.

Leitura Adicional
Ainda não vi o Caso Especial escrito como um padrão, mas o Objeto Nulo foi descrito em [Woolf]. Se você perdoar o trocadilho irresistível, vejo o Objeto Nulo como um

caso especial do Caso Especial.

Exemplo: Um Objeto Nulo Simples (C#)
Aqui está um exemplo simples de um Caso Especial usado como um objeto nulo.

Temos um empregado regular.

class Enpregado...
public virtual String Nome (

get{return nome;)
set
( nome = valor);

}
private String
publ:

nome;

‘tual Decimal TotalAtéoMomento {
{return calcularTotalDoPeriodo
(0) ;}

public virtual Contrato Contrato {

}

get {return _contrato;}

private Contrato _contrato;
As caracteristicas da classe poderiam ser sobrescritas por um empregado nulo.

464

Parte ll e Os Panrões

class EmpregadoNulo: Empregado, INull...

public override String None {

get {return "Empregado Nulo";}

set { }

)
public override Deci
}

get{return 0m;)

ial TotalAtéOMomento {

public override Contrato Contrato {

}

get {return Contrato. NULL; }

Perceba que quando vocé pede a um empregado nulo seu contrato, obtém de

volta um contrato nulo.

Os valores padrão aqui evitam muitos testes para a verificação de nulos se eles

acabam com os mesmos valores nulos. Os valores nulos repetidos são manipulados

pelo objeto nulo por default. Você também pode testar a ocorrência de nulos explicitamente dando ao cliente um método éxulo ou usando uma verificação de tipo para
uma interface de sinalização.

CapituLo 18 e Paproes BAsicos

465

Plugin
por David Rice e Matt Foemmel
Conecta classes durante a configuração em vez de na compilação.

Objeto do
Domínio

«interface»

Gerador de ID

2
4

A Interface Separada (445) é frequentemente usada quando o código da aplicação roda

em múltiplos ambientes de execução, cada um deles requerendo implementações di-

ferentes de um comportamento específico. A maioria dos desenvolvedores fornece a

implementação correta escrevendo um método fábrica. Suponha que você defina seu

gerador de chave primária com uma Interface Separada (445), de modo que você possa usar um contador simples em memória para testes de unidade, mas uma seqiién-

cia gerenciada pelo banco de dados para código de produção. Seu método fábrica

conterá provavelmente uma declaração condicional que olha uma variável de ambiente local, determina se o sistema está em modo de teste e retorna o gerador de

chaves correto. Uma vez que você tenha algumas fábricas, tem uma bagunça nas

mãos. Estabelecer uma nova configuração de distribuição — digamos “execute testes
de unidade contra a base de dados na memória sem controle de transações” ou “exe-

cute no modo de produção em um banco de dados DB2 com controle de transações

integral” — requer editar declarações condicionais em várias fábricas, reconstruir e re-

distribuir. A configuração não deveria ser dispersa por toda a sua aplicação, nem de-

veria requerer uma reconstrução ou redistribuição. Um Plugin resolve ambos os problemas fornecendo a configuração em tempo de execução e centralizada.

Como Funciona
A primeira coisa a fazer é definir com uma Interface Separada (445) quaisquer compor-

tamentos que terão implementações diferentes baseadas no ambiente de execução.

Além disso, usamos o padrão fábrica básico, apenas com alguns requisitos especiais.

A fábrica Plugin requer que suas instruções de conexão sejam declaradas em um tini-

co ponto externo para que a configuração possa ser gerenciada com facilidade. Adicionalmente, a conexão com as implementações devem ocorrer dinamicamente em

tempo de execução, em vez de durante a compilação, de modo que a reconfiguração

não irá requerer uma reconstrução.

Um arquivo texto funciona bastante bem como meio de declaração de regras de co-

nexão. A fábrica Plugin irá simplesmente ler o arquivo texto, buscar uma entrada especi-

ficando a implementação de uma interface requerida e retornar essa implementação.
O Plugin funciona melhor em uma linguagem que suporte reflexão porque a fá-

brica pode construir implementações sem dependências de tempo de compilação.

Ao usar reflexão, o arquivo de configuração deve conter os mapeamentos dos nomes

na interface para os nomes na classe de implementação. A fábrica pode permanecer

independentemente em um pacote framework e não precisa ser alterada quando você

adiciona novas implementações às suas opções de configuração.

Mesmo quando você não estiver usando uma linguagem que suporte reflexão,
ainda vale a pena estabelecer um ponto central de configuração. Você pode até mes-

466

Parte Il e Os PaprOes

uma

configuração
de plugin

solicitante
lerPlugin

T

I

T

|
|
procurarPluginPeloTipo |

=
[>

Figura 18.2

+)

umplugin

Um solicitante obtém uma implementação Plugin de uma interface separada.

mo usar um arquivo texto para estabelecer as regras de conexão, com a única diferença de que sua fábrica usará lógica condicional para mapear uma interface para a im-

plementação desejada. Cada tipo de implementação deve ser contemplada na fábrica —na prática, isso não é nada demais. Apenas acrescente outra opção dentro do mé-

todo fábrica sempre que você adicionar uma nova implementação ao código base.
Para forçar as dependências de camada e de pacote com uma verificação em tempo

de construção, coloque esta fábrica no seu próprio pacote para evitar interromper

seu processo de construção.
Quando Usá-lo

Use Plugin toda vez que tiver comportamentos que requeiram implementações dife-

rentes baseadas no ambiente de execução.

Exemplo: Um Gerador de Identidades (Java)

Como discutido acima, a geração de chave ou IDs é uma tarefa cuja implementação

poderia variar entre ambientes de distribuição (Figura 18.3).
Primeiro, escreveremos a Interface Separada (445) GeradorDeld assim como
quaisquer implementações necessárias.

interface GeradorDeld...
public Long préximald( );
class GeradorDeId0racle implements GeradorDeld...
public GeradorDeldoracle ( ) {

Plugin

this.segúência-

Environment .getProperty ("id. sequence") ;

this. fontededados = Environment .getProperty ("id. source") ;
No GeradorDeldOracle, próximoId( ) seleciona o próximo número disponível de

uma sequência definida de uma fonte de dados definida.

Capíruto 18 e PaprÕEs BAsicos

Objetodo
Dominio
T

cinterfacen
Gerador de ID

Ls

préximald

|
|
|
I
|
|

Oracle

Corsa

próximald

proximal
óximald

Polito

Gerador de ID
|

|

467

f|

é

Plugins

|

carregarinstância q

if (em Teste) return new Contador

if (em Produção) return new GeradorDeldOracle
Figura 18.3

Múltiplos geradores de identidade.

class Contador implenents GeradorDeld...
private long contador =
public synchronized Long préximold(
return new Long(contador+#) ;
}

Agora que temos algo para construir, vamos escrever a fábrica plugin que reali-

zará os mapeamentos correntes da interface para a implementação.
class FábricaPlugin
i

static Properties props = new Properties(

);

static {

try {
String arquivoDePropriedades = System.getProperty ("plugins");
propriedades.
load (new FileInputStream(ar
} catch (Exception ex) {

}

throw new ExceptionInInitializerError
(ex) ;

}
public static Object lerPlugin (Class iface) {

String nomeDaImplementação = propriedades.getProperty (iface.getName( ));

if (nomeDalmplementacdo == mull) {
throw new RuntimeException ("inplementação não especificada para " +

468

Parte Il e Os PaprOes

iface.getName( ) + " nas propriedades da FébricaPlugin.") ;

}

try {
return Class. forName (noneDaInplementação) .newInstance( };
) catch (Exception ex) {

throw new RuntimeException ("fábrica incapaz de criar instância de " +
iface.getName( |);

Perceba que estamos carregando a configuração procurando por uma proprie-

dade de sistema chamada plugins que localizará o arquivo contendo nossas instru-

ções de conexão. Existem muitas opções para definir e armazenar instruções de co-

nexão, mas achamos que um simples arquivo de propriedades é a mais fácil. Usar a
propriedade de sistema para encontrar o arquivo, em vez de olhar no classpath, torna
simples especificar uma nova configuração em qualquer lugar da sua máquina. Isso
pode ser muito conveniente ao mover construções (builds) entre ambientes de desen-

volvimento, teste e produção. Aqui está como dois arquivos diferentes de configuração, um para teste e um para produção, poderiam se parecer:
arquivo de configuração teste. propriedades...
fconfiguração para teste

GeradorDerd = GeradorDeTdTeste
arquivo de configuração produção propriedades. .
iconfiguração para produção
GeradorDeId

= GeradorDeIdOracle

Vamos voltar à interface GeradorDeld e adicionar

um membro estático INSTANCE

que é configurado por uma chamada à fábrica Plugin. Ela combina o Plugin com o pa-

drão singleton para fornecer uma chamada extremamente simples e legível para obter um ID.

interface GeradorDeld...
public static final GeradorDeId INSTANCIA =
(GeradorDeld)

FábricaPlugin.
lerPlugin (GeradorDeld.class) ;

Podemos agora fazer essa chamada sabendo que obteremos o ID correto para o

ambiente correto.

class Cliente extends ObjetoDoDonínio...
private Cliente

super (id) ;

}

(String nome, Long id) (

this.nome = nome;

public Cliente criar (String nome) {
Long novaldDoObjeto = GeradorDeld. INSTANCIA.préximald(

Cliente obj = new Cliente(nome, novardDoObjeto) ;
obj marcarilovo( |;
return obj;

|;

CapituLo 18 e PaprÕEs BAsicos

469

Stub de Serviço (Service Stub)

por David Rice
Remove dependência de serviços problemáticos durante os testes.

WSDL
Serviço de Impostos ACME

obterinformaçãoSobrelmposto
Interface

Serviço de Impostos

Gerador
de Contas

t4

obterinformacaoSobrelmposto

Classe
Stub de Servico de Imposto
obterinformaçãoSobrelmposto

Sistemas corporativos, muitas vezes, dependem do acesso a serviços de terceiros, tais

como pontuação de crédito, busca por valores de taxas e mecanismos de determina-

ção de preços. Qualquer desenvolvedor que já tenha criado tais sistemas pode falar

da frustração de depender de recursos completamente fora de seu controle. A entre-

ga dessas características é imprevisível, e como esses serviços são muitas vezes remotos, a confiabilidade e o desempenho podem sofrer também.

Esses problemas, no mínimo, diminuem a velocidade do processo de desenvol-

vimento. Os desenvolvedores ficam esperando pelo serviço voltar a operar ou talvez

coloquem algo no código para compensar por características ainda por serem entre-

gues. Muito pior, e bastante provável, tais dependências levarão a vezes em que os

testes não poderão ser executados. Quando os testes não podem ser executados, o

processo de desenvolvimento é interrompido.
Substituir durante os testes o serviço por um Stub de Serviço que rode em memória, rápida e localmente melhora a sua experiência de desenvolvimento.

Como Funciona
O primeiro passo é definir o acesso ao serviço com um Gateway (436). O Gateway (436)

não deve ser uma classe, mas sim uma Interface Separada (445) de modo que você possa ter uma implementação que chame o serviço real e, pelo menos, uma que seja apenas um Stub de Serviço. A implementação desejada do Gateway (436) deve ser carrega-

da usando um Plugin (465). A chave para escrever um Stub de Serviço é que você o

mantenha tão simples quanto possível - a complexidade frustrará o seu intento.

Vamos investigar o processo de criar um stub para um serviço de impostos so-

bre vendas que, dado um endereço, tipo do produto e quantia vendida, fornece os
valores e faixas dos impostos estaduais sobre vendas. A maneira mais simples de fornecer um Stub de Serviço é escrever duas ou três linhas de código que usem um valor

de tributação simples que satisfaça todas as solicitações.

As leis sobre impostos não são tão simples, é claro. Determinados produtos são

isentos de impostos em certos estados, então confiaremos que o nosso serviço real de

impostos saiba quais combinações de estado e produto são isentas. Entretanto, mui-

s

470

Parte Il e Os PaprOes

to da funcionalidade da nossa aplicação depende de se os impostos são cobrados, então precisamos acomodar a isenção de impostos no nosso Stub de Serviço. O meio

mais simples de adicionar este comportamento ao stub é por meio de uma declaração

condicional que isente uma combinação específica de endereço e produto e, a seguir,

use esses mesmos dados em quaisquer casos de teste relevantes. O número de linhas
de código no nosso stub pode ainda ser contado com uma única mão.

Um Stub de Serviço mais dinâmico mantém uma lista de combinações isentas de

produtos e estados, permitindo aos casos de teste acréscimo a essa lista. Mesmo aqui
temos em torno de 10 linhas de código. Estamos mantendo as coisas simples dado
nosso objetivo de aumentar a velocidade do processo de desenvolvimento.
O Stub de Serviço dinâmico traz uma questão interessante relacionada à depen-

dência entre ele e os casos de teste. O Stub de Serviço depende de um método de con-

figuração para adicionar isenções que não estejam na interface Gateway (436) original

de serviço de impostos. Para tirar proveito de um Plugin (465) para carregar o Stub de
Serviço, este método deve ser adicionado ao Gateway (436), o que é bom, pois ele não
acrescenta muito ruído ao seu código e é feito em nome dos testes. Assegure-se de
que a implementação do Gateway (436) que chama o serviço real levante falhas de asserção dentro dos métodos de teste.

Quando

Usá-lo

Use o Stub de Serviço sempre que descobrir que a dependência de um determinado
serviço esteja retardando seu desenvolvimento e testes.

Muitos adeptos da Programação Extrema usam o termo Objeto Falso para um

Stub de Serviço. Permanecemos com Stub de Serviço porque ele tem estado em uso há
mais tempo.

Exemplo: Serviço de Impostos Sobre Vendas (Java)
Nossa aplicação usa um serviço de impostos que é distribuído como um serviço Web.
O primeiro item do qual cuidaremos é definir um Gateway (436), de modo que nosso
código do domínio não seja forçado a lidar com os mistérios dos serviços Web. O Ga-

teway (436) é definido como uma interface para facilitar a carga de quaisquer Stubs de
Serviço que viermos a escrever. Usaremos um Plugin (465) para carregar a implementação correta do serviço de impostos.

interface ServigoDelmpostos. ..
public static final ServiçoDeImpostos INSTANCIA =

(ServigoDelnpostos) PábricaPlugin.
lerPlugin (ServigoDelnpostos. class) ;
public InformagaoSobrelmposto obter InformaçãoSobre InpostoSobreVendas
(String códigoDoProduto, Endereço endereço, Dinheiro quantiavendida) ;
O Stub de Serviço para um cálculo simples de tributação se pareceria com:

£
5
33
2

class ServiçoDeImpostosTaxaPlana implements ServiçoDeImpostos....
private static
final BigDecimal TAXA PLANA = new BigDecimal ("0.0500") ;
public InformaçãoSobreInposto obter InformaçãoSobre ImpostoSobreVendas
(String códigoDoProduto,

Endereço endereço,

Dinheiro quantiaVendida)

(

return new InformaçãoSobreImposto (TAXA PLANA, quant iavendida miltiplica (TAXA PLANA)) ;

CapituLo 18 e Paproes Básicos

471

Aqui está um Stub de Serviço que fornece isenções de impostos para uma com-

binação específica de endereço e produto:

class ServiçoDeInpostosProdutoIsento implements ServiçoDeImpostos. ..
private static final BigDecimal TAXA ISENTO = new BigDecimal ("0.0000") ;
private static final BigDecimal TAXA PLANA = new BigDecimal ("0.0500") ;
private static final String ESTADO ISENTO = "IL";
private static final String PRODUTO ISENTO= "12300";
public InformaçãoSobreImposto obter InformaçãoSobre InpostoSobrevendas
(String códigoDoProduto,

Endereço endereço,

if [códigoDoproduto.
equals (PRODUTO ISENTO) &&
endereço. lerCódigoDoEstado(

Dinheiro quantiaVendida)

(

) .equals (ESTADO ISENTO) ) {

return new InformaçãoSobreImposto
(TAXA ISENTO, quantiaVendida.multiplica
(TAXA ISENTO)) ;

) else {

return new InformaçãoSobreImposto
(TAXA PLANA, quantiaVendida.multiply
(TAXA PLANA)) ;

Agora aqui está um Stub de Serviço mais dinâmico, cujos métodos permitem que

um caso de teste adicione e remova combinações com isenção. Uma vez que achemos

necessário adicionar estes métodos de teste, precisamos voltar e adicioná-los aos nos-

sos Stubs de Serviço mais simples assim como à implementação que chama o serviço

Web real para o cálculo de impostos. Os métodos de teste não usados deveriam todos causar falhas de asserção.

class TesteDeServiçoDeImpostos implements ServicoDeImpostos...
private static Set isenções

= new HashSet ( );

public InformaçãoSobreImposto obterInformaçãoSobre Impostosobrevendas
(String códigoDoProduto, Endereço endereço, Dinheiro quantiaVendida) {
BigDecimal taxa = lerTaxa (códigoDoProduto, endereço) ;

}

return new InformaçãoSobreImposto (taxa, quantiavendida. multiplica (taxa) );

public static void adicionarisenção (String cédigoDoProduto, String códigoDoEstado)

}

isenções.
add (lerChaveDalsenc&o (códigoDoProduto, códigoDoEstado)) ;

{

public static void zerar( ) {

}

isenções.clear ( );

private static BigDecimal lerTaxa (String códigoDoProduto, Endereço endereço) {
if (isenções. contains (lerChaveDaIsenção (códigoDoproduto, endereço. LerCódigoDoEstado( )))) {
return TAXA ISENTO;

} else {

return TAKA_PLANA;

Não é mostrada a implementação que chama o serviço Web que fornece os da-

dos reais sobre os impostos, a qual a configuração do Plugin (465) de produção ligaria a interface do serviço de impostos. As configurações do nosso Plugin (465) de tes-

te apontariam para o Stub de Serviço apropriado acima.

472

Parte ll e Os Parões

Finalmente, qualquer solicitante do serviço de impostos deve acessar esse servi-

ço através do Gateway (436). Temos aqui um gerador de contas que cria contas padrão

e então chama o serviço de impostos para criar quaisquer impostos correspondentes.

class GeradorDeConta...
public Conta[ | calcularContas

(ProgramaçãoDeFaturamento programação)

List contas= new Arraybist ( ) ;

{

Conta contaBásica = new Conta (programação. lerQuantiaFaturada( ), false);
contas. adá (contaBásica) ;

InformaçãoSobreImposto informação =
ServiçoDeImpostos. INSTANCIA. obter InformaçãoSobre ImpostoSobrevendas (
programação. lerProduto( ), programação. lerEndereço( |,

programação. lerQuantiaFaturada( ));
if (informação. lerTaxaEstadual ( ) .compareTo
(new BigDecimal (0)) > 0) (
Conta impostoDaConta = new Conta (informação.

}

contas. add (impostoDaConta] ;

lerQuantiaDoEstado( ), true);

return (Contal |) contas.toArray
(new Conta [contas.size( }]);

S

8
e
&=

CapituLo 18 e Paproes Básicos

473

Conjunto de Registros (Record Set)
Uma representação em memória de dados tabulares.

*

Conjunto

de Registros

lg

Linha

Tabela

|

*

Coluna

Nos últimos vinte anos, o modo dominante de representar dados em um banco de

dados tem sido a forma relacional tabular. Apoiado pelas grandes e pequenas com-

panhias de banco de dados e uma linguagem de consultas razoavelmente padrão,

quase todo novo desenvolvimento que vejo usa dados relacionais.
Além disso, tem surgido uma profusão de ferramentas para a construção rápi-

da de interfaces com o usuário. Esses frameworks baseiam-se no fato de que os dados

correspondentes são relacionais, e eles fornecem controles conectados aos dados (da-

ta aware) de vários tipos para a interface com o usuário. Tais controles permitem a vi-

sualização e manipulação desses dados quase que sem programação.

O lado ruim destes ambientes é que, embora tornem a exibição e as atualizações
simples de dados ridiculamente fáceis, elas não oferecem um bom lugar onde colo-

car a lógica de negócio. Quaisquer validações além de “esta é uma data válida?”, e

quaisquer cálculos ou regras de negócio não têm um bom local para ir. Elas ficam ou
jogadas no banco de dados como procedimentos armazenados ou misturadas com o
código da interface com o usuário.
A idéia do Conjunto de Registros é dar a você o bolo e deixá-lo comer, fornecen-

do uma estrutura em memória que se pareça exatamente com o resultado de uma
pesquisa SQL, mas que possa ser gerada e manipulada por outras partes do sistema.
Como Funciona
Um Conjunto de Registros é geralmente algo que você não irá construir sozinho, sendo fornecido pelo vendedor da plataforma de software com a qual você está trabaIhando. Exemplos incluem o conjunto de dados do ADO.NET e o conjunto de linhas

do JDBC 2.0.

O primeiro elemento essencial de um Conjunto de Registros é que ele se pare-

ce exatamente com o resultado de uma consulta a um banco de dados. Isso signi-

fica que você pode usar a abordagem clássica em duas camadas de emitir uma

consulta e jogar os dados diretamente em uma interface com o usuário direta-

mente conectada aos dados (data aware) com toda a comodidade que essas ferra-

mentas de duas camadas lhe dão. O segundo elemento essencial é que você mes-

mo pode criar facilmente um Conjunto de Registros ou pegar um que tenha resul-

tado de uma consulta ao banco de dados e manipulá-lo facilmente com código de

lógica de domínio.

474

Parte Il e Os PaprOes

Embora as plataformas, muitas vezes, lhe déem um Conjunto de Registros, vocé

mesmo pode criar um. O problema é que não há muito sentido nisso sem as ferramentas para as interfaces com o usuário conectadas aos dados (data aware) que, nes-

te caso, você mesmo precisaria criar. De qualquer forma é razoável dizer que criar
uma estrutura de Conjunto de Registros como uma lista de mapas, o que é comum em
linguagens de scripts dinamicamente tipadas, é um bom exemplo deste padrão.

A habilidade de desconectar um Conjunto de Registros de sua conexão à fonte de

dados é bastante valiosa. Isso lhe permite passar o Conjunto de Registros adiante em
uma rede sem ter que se preocupar com as conexões ao banco de dados. Além disso,
se você puder serializar facilmente o Conjunto de Registros, ele pode também atuar como um Objeto de Transferência de Dados (380) para uma aplicação.
A desconexão traz a questão do que acontece quando você atualiza o Conjunto
de Registros. Cada vez mais, as plataformas estão permitindo que o Conjunto de Registros seja uma forma de Unidade de Trabalho (187), de modo que você pode modificá-lo
e então retorná-lo à fonte de dados para que seja gravado. Uma fonte de dados pode

tipicamente usar um Bloqueio Offline Otimista (392) para verificar se existe algum con-

flito e, em caso contrário, gravar as alterações no banco de dados.
Interface Explícita

A maioria das implementações de Conjuntos de Registros usam

uma interface implícita. Isso significa que, para extrair informações do Conjunto de
Registros, você invoca um método genérico com um argumento para indicar qual
campo você deseja. Por exemplo, para obter o passageiro de uma reserva de passagem aérea, você pode usar uma expressão como unaReserva ["passageiro"]. Uma inter-

face explícita requer uma classe Reserva real com métodos e propriedades defini-

dos. Com uma reserva explícita, a expressão para o passageiro poderia ser unsReserva.passageiro.

As interfaces implícitas são flexíveis, visto que você pode usar um Conjunto de

Registros genérico para qualquer tipo de dados. Isso lhe poupa de ter que escrever

uma nova classe cada vez que definir um novo tipo de Conjunto de Registros. Em geral, no entanto, considero interfaces implícitas uma Coisa Ruim. Se estou programan-

do uma reserva, como sei como obter o passageiro? A string apropriada é “passagei10” ou “convidado” ou “viajante”? O único modo pelo qual posso descobrir é andar

pelo código base tentando encontrar onde as reservas são criadas e usadas. Se eu ti-

ver uma interface explícita, posso olhar a definição da reserva para ver a proprieda-

de de que preciso.

Este problema é exacerbado em linguagens tipadas estaticamente. Se eu quiser
o sobrenome do passageiro, tenho que recorrer a alguma expressão horrível tal como

( (Pessoa) umaReserva ["passageiro"]) .sobrenome, mas então o compilador perde toda a in-

formação sobre os tipos e tenho que entrar manualmente com ela para obter a infor-

mação que quero. Uma interface explícita pode manter a informação sobre os tipos,

então posso usar umaReserva. passageiro. sobrenome.

Por esses motivos, geralmente franzo as sobrancelhas para interfaces implícitas
(e também para seu primo malvado, passar dados em dicionários). Também não sou
muito fã deles com Conjuntos de Registros, mas o que se salva aqui é que o Conjunto de

3
2
&5|

Registros geralmente carrega nele informação sobre as colunas válidas. Além disso, os
nomes de colunas são definidos pelo SQL que cria o Conjunto de Registros, então não
é muito difícil encontrar as propriedades quando você precisa delas.

Contudo, é bom dar um passo adiante e ter uma interface explícita. ADO.NET

fornece isso com seus conjuntos de dados fortemente tipados, classes geradas que

CapituLo 18 e Paproes Básicos

475

fornecem uma interface explícita e completamente tipada para um Conjunto de Regis-

tros. Já que um conjunto de dados ADO.NET pode conter muitas tabelas e os relacio-

namentos entre elas, conjuntos de dados fortemente tipados também fornecem pro-

priedades que podem usar essas informações sobre relacionamentos. As classes são

geradas a partir do XSD da definição do conjunto de dados.

As interfaces implícitas são mais comuns, então usei conjuntos de dados não-ti-

pados nos exemplos deste livro. Para código de produção em ADO.NET, todavia, sugiro o uso de conjuntos de dados que sejam tipados. Em um ambiente diferente do
ADO.NET, sugiro o uso de geração de código para seus próprios Conjuntos de Regis-

tros explícitos.
Quando

Usá-lo

Na minha opinião, o valor de um Conjunto de Registros vem de se ter um ambiente
que se baseia nele como uma forma comum de manipular dados. Várias ferramentas
de interface com o usuário usam Conjuntos de Registros, e esta é uma razão convincen-

te para usá-los você também. Se você tiver um desses ambientes, deve usar um Mó-

dulo Tabela (134) para organizar a sua lógica de domínio: Obtenha um Conjunto de Re-

gistros do banco de dados, passe-o para um Módulo Tabela (134) para calcular infor-

mações derivadas, passe-o para uma interface com o usuário para exibi-lo e editar
seus dados e passe-o de volta para um Módulo Tabela (134) para validação. A seguir
grave as atualizações no banco de dados.

De diversas maneiras, as ferramentas que tornam o Conjunto de Registros tão

valioso apareceram devido à onipresença dos bancos de dados relacionais e SQL e à

ausência de quaisquer alternativas reais de estrutura e linguagem de pesquisa. Ago-

ra, é claro, existe o XML, que tem uma estrutura amplamente padronizada e uma

linguagem de pesquisa em XPath, e acredito ser provável que venhamos a ver ferramentas que usem uma estrutura hierárquica da mesma forma que as ferramentas
atuais usam Conjuntos de Registros. Talvez isso seja na verdade um caso particular de

um padrão mais genérico: algo como Estrutura de Dados Genérica. Mas deixarei de
pensar nesse padrão até lá.

Referências

[Alexander et al.]
Alexander, et al. A Pattern Language. Oxford, 1997.
Uma inspiração para muitas pessoas no movimento de padrões. Sou menos fascinado por ele do
que a maioria, mas penso que vale a pena dar uma olhada para entender uma abordagem de onde
tantos extraem tanto.

[Alpert etal.]

Alpert, Brown and Woolf. Design Patterns Smalltalk Companion. Addison-Wesley, 1998
Pouco conhecido fora da comunidade Smalltalk, este livro espande e explica muitos dos padrões
clássicos.

[Alur et al.)

Alur, Culpi and Malks. Core J2EE Patterns: Best Practices and Design Strategies. Prentice Hall, 2001.
Pertence à nova onda de livros sobre padrões que dão nova vida à área. Embora os padrões sejam
expressos especificamente para a plataforma J2EE, a maioria deles também faz sentido em outros
lugares.
[Ambler]

http: / /www.ambysoft.com/mappingObjetct-html

Uma fonte de idéias titeis a respeito do mapeamento objeto-relacional.

[Beck XP 2000]
Beck, Extreme Programming Explained*, Addison-Wesley, 2000.
O manifesto da Programação Extrema. Deve ser lido por qualquer pessoa interessada em processos de software.

[Beck Patterns]
Beck. Smalltalk Best Practice Patterns. Prentice Hall, 1997.
De forma injusta, é pouco lido devido à sua base Smalltalk. Tem mais bons conselhos para outras
linguagens OO do que a maioria dos livros que são escritos especificamente para elas. O único aspecto negativo é que você vai perceber o quanto todos nós perdemos por não programar em
Smalltalk.
* N. de R. As obras assinaladas nesta lista foram publicadas no Brasil pela Bookman Editora.

478

REFERÊNCIAS

[Beck TDD]

Beck. Test-Driven Development: By Example. Addison-Wesley, 2003.
Deve ser lançado no mesmo dia deste livro. TDD é o guia do Kent para o apertado ciclo de teste e
refatoração que pode envolver um projeto.

[Bernstein e Newcomer]
Bernstein and Newcomer. Principles of Transaction Processing. Morgan Kaufmann, 1997.
Uma excelente introdução ao complicado mundo das transações.
[Brown etal.]

Brown et al. Enterprise Java Programming with IBM Websphere. Addison-Wesley, 2001.
Embora dois terços deste livro sejam um manual de software, o outro terço contém uma quantidade maior de bons conselhos sobre projeto do que a maioria dos livros inteiros dedicados ao assunto.

[Brown and Whitenack]
ttp://memibers.aol.com/kgb1001001/Chasmis.kttm!
Um dos primeiros e melhores artigos sobre mapeamento objeto-relacional.
[Cockburn UC]
Cockburn. Writing Effective Use Cases*. Addison-Wesley, 2001.
De longe a melhor referéncia sobre casos de uso.
[Cockburn PloP]
Cockburn, “Prioritizing Forces in Software Design,” in [PLoPD 2].

Uma discussão sobre as fronteiras de uma aplicação.

[Coleman et al.]
Coleman, Arnold and Bodoff. Object-Oriented Development: The Fusion Method, Second Edition. Prentice Hall, 2001.
Embora muito deste livro pré-UML seja principalmente de interesse histórico, sua discussão do
modelo de interface é muito útil para quem esteja projetando uma camada de serviço.
[Evans and Fowler]
http:/fmartinfowlercom/apsupp/spec.pdf
Uma discussão sobre o padrão Especificação.
[Evans]
Evans. Domain Driven. Addison-Wesley, em preparação.
Um livro sobre o desenvolvimento de modelos de domínio. Embora eu normalmente não goste
de referenciar livros ainda não publicados, o manuscrito promete uma discussão fascinante sobre
um aspecto importante e difícil do desenvolvimento de aplicações corporativas.
[Fowler Temporal Patterns]

http:/martinfowler.com/ap2/timeNarrative.html

Padrões que lidam com histórias de objetos que mudam com o tempo.

[Fowler AP]
Fowler. Padrões de Análise, Addison-Wesley, 1997.
Padrões para o modelo de domi
[Fowler Refactoring]
Fowler, Refactoring’. Addison-Wesley, 1999.
Uma técnica para aperfeiçoar o projeto de uma base de código existente.
[Fowler CI)
http://martinfowoler.com/articles/continuoustntegration
html
Um ensaio que explica como criar software, automaticamente, diversas vezes por dia.

Rererências

479

[Gang of Four]

Gamma, Helm, Johnson e Vlissides. Design Patterns.* Addison-Wesley, 1995.
O livro seminal sobre padrões.
[Hay]

Hay. Data Model Patterns. Dorset House, 1995.

Padrões de modelos conceituais a partir de uma perspectiva relacional.

[Jacobson et al.]

Jacobson et al. Object-Relational Software Engeneering. Addison-Wesley, 1992.
Um dos primeiros livros sobre o projeto OO. Introduz casos de uso e a abordagem de projeto interface-controlador-entidade.
[Keller and Coldeway]

http://www.objectarchitects.de/ObjectArchitects/orpatterns/index.htm
Uma referéncia excelente sobre mapeamento objeto-relacional

(Kirtland]

Kirtland. Designing Component-Based Applications. Microsoft Press, 1998.
Descrição da arquitetura DNA.

[Knight and Dail
Knight and Dai. “Objects and the Web.” IEEE Software, March/April 2002.
Um excelente artigo sobre o padrão modelo-vista-controle, sua evolução e uso em aplicações Web.
[Larman]

Larman. Applying UML and Patterns, Second Edition. Prentice Hall, 2001.
Atualmente meu favorito para uma introdução ao projeto OO.
[Lea]
Lea. Concurrent Programming in Java, Second Edition. Addison-Wesley, 2000.
Se você quiser programar com múltiplas threads, precisa entender este livro primeiro.
[Marinescu]

Marinescu. EJB Design Patterns.* New York: John Wiley, 2002.
Um livro recente sobre padrões para Java EJBs.
[Martin and Odell]
Martin and Odell. Object Oriented Methods: A Foundation (UML Edition). Prentice Hall, 1998.
Modelagem de objetos de uma perspectiva conceitual, assim como uma investigação sobre os fundamentos de que trata a modelagem.
[Nilsson]
Nilsson. .NET Enterprise Design with Visual Basic .NET and SQL Server 2000. Sams, 2002.

Um livro sólido sobre arquitetura para a plataforma da Microsoft.

[PLoPD 2}
Vlissides, Coplien and Kerth (eds.). Pattern Languages of Program Design 2. Addison-Wesley, 1996.
Compêndio de artigos sobre padrões.
[PLoPD 3}
Martin, Buschmann and Rielhe (eds). Pattern Languages of Program Design 3. Addison-Wesley, 1998.
Compêndio de artigos sobre padrões.
IPOSA]
Buschmann et al. Pattern-Oriented Software Architecture. Wiley, 2000.
O melhor livro sobre padrões de arquitetura mais amplos.

480

REFERÊNCIAS

[Rielhe atal.]
Rielhe, Siberski, Baumer, Megert and Zullighoven. “Serializer,” in [PLOPD 3].
Descrição detalhada das estruturas de serialização de objetos, especialmente quando você precisa
serializá-los em diferentes formas.

[Schmidt]
Schmidt, Stal, Rohnert and Buschmann. Pattern-Oriented Softiware Architecture, Volume 2. New York: John
Wiley, 2000.
Padrões para sistemas concorrentes e distribuídos. Mais para pessoas que projetam servidores de
aplicação do que para aquelas que os usam, mas é bom ter algum conhecimento destas idéias
quando você usa os resultados.
[Snodgrass]
Snodgrass. Developing Time-Oriented Database Applications in SQL. Morgan-Kaufmann, 1999.
Como lidar com o rastreamento de informações históricas em bancos de dados relacionais.
[Struts]

http://jakarta.apache.org/struts/

Um framework para a construção de aplicações Web em Java que vem crescendo em popularidade.

[Waldo et al.]
Waldo, Wyant, Wollrath and Kendall. A Note on Distributed Computing. SMLI
search.sun.com/technical-reports/1994/smli_tr-94-29,
pdf, Sun Microsystems, 1994.

TR-94-29, http:/fre-

Um artigo clássico sobre por que “objetos distribuídos transparentes” são um paradoxo perigoso.

[wiki]
hittp://e2.com/egifwiki
O wiki web original, desenvolvido por Ward Cunningham. Um site Web sinuoso, porém fascinante, onde todos os tipos de pessoas compartilham todos os tipos de idéias.
[Woolf]

Woolf. “Null Object,” in [PLoPD 3].

Uma descrição do padrão Objeto Nulo.

[Yoder]

hitp:/foww joeyoder.com/Research/objectmappings

Uma boa fonte de padrões objeto-relacionais.

Índice

A

ACID (atomicidade, consistência, isolamento e durabilidade), 85-90
recursos transacionais, 86-87
reduzindo o isolamento da transação para melhorar a capacidade de resposta, 86-88
transações de sistema e de negócio, 87-90
Afinidade, servidor, 96-97
Álbuns, transferindo informações sobre, 385-389
Álbuns e faixas (Java), 257-260

Aplicações corporativas, evolução das camadas, 3840
Apresentação

camadas, 108-110
lógica, 39-40
Web, 71-77
Apresentação Web, Padrões de, 314-366
Controlador de Aplicação, 360-366
Controlador de Página, 318-327
Controlador Frontal, 328-332
MVC (Modelo Vista Controlador), 315-317
Vista de Transformação, 343-346
Vista em Duas Etapas, 347-359
Vista Padrão, 333-342
Armazenador de dados para objeto de dominio (Java), 163-164
Armazenador de valor, usando, 204-206
Armazenados, Procedimentos, 111-113
ASPNET, pagina servidora (C#), 339-342
Assemelhados, jogadores e, 277-282
Atualizações perdidas, 79

Banco de Dados, estado da sessão no, 432-434
como funciona, 432-433
quando usá-lo, 433-434
Bancos de dados
carregando objetos do, 271-275
mapeando para bancos de dados relacionais, 52-70
conexões a bancos de dados, 67-69
criando mapeamento, 64-67
lendo dados, 58-60
padrões arquiteturais, 52-57
padrões para mapeamento estrutural, 59-65
problema comportamental, 56-58
questões diversas, 68-70
usando metadados, 66-68
Bloqueando
otimista, 81-82
pessimista, 81-82
Bloqueio, gerenciador simples, 405-411
Bloqueio Implícito, 422-425
como funciona, 422-424
exemplo
Bloqueio Offline Pessimista implícito Java), 423425

quando usá-los, 423-424
Bloqueio Offline Otimista, 392-400
como funciona, 392-396
exemplo
camada de domínio com Mapeadores de Dados
(ava), 396-400
quando usá-lo, 395-397

482

Ínvice

Bloqueio Offline Pessimista implícito, 423-425
Bloqueio Otimista, 81-82
Bloqueios
Offline Otimista compartilhado, 414-420
Offline Otimista de raiz, 420-421
Offline Pessimista compartilhado, 419-421
Bloqueios de Granularidade Alta, 412-421
como funciona, 412-415
exemplos
Offline Otimista compartilhado, 414-420

Offline Otimista de raiz (Java), 420-421
Offline Pessimista compartilhado (Java), 419421

quando usá-los, 414-415

Bloqueio Offline Pessimista, 401-411
como funciona, 401-406
exemplo
gerenciador simples de bloqueio (Java), 405411
quando usá-lo, 405-406
Brown, camadas de, 113-114
Busca em diversas tabelas, 239-240

c

cr

chave integral, 219-221
coleção de referências, 240-243
empregados e habilidades, 245-249
Gateway Pessoa, 152-155
jogadores concretos, 285-290
jogadores e assemelhados, 277-282
lançamento de receita com Módulo Tabela, 137140
manipulador de página com código por trás, 324327
objetos nulos simples, 463-464
página servidora ASP.NET, 339-342
serviço Web, 374-379
tabela única para jogadores, 270-272
usando conjuntos de dados ADO.NET, 154-157
usando fantasmas, 205-213
Camada de dominio com Mapeadores de Dados (Java), 396-400
Camada Supertipo, 444
como funciona, 444
exemplo
objetos de domínio (Java), 444
quando usá-la, 444
Camadas
apresentação, 108-110
básicas J2EE, 113-114
Brown, 113-114
começando com a camada de dominio, 106-107
decidindo onde rodar suas, 41-44
descendo para a fonte de dados, 106-110

camadas de apresentação, 108-110

fonte de dados Módulos Tabela, 107-108
fonte de dados para Modelos de Domínio, 107109
fonte de dados para Roteiros de Transação, 106108

Marinescu, 113-115

Microsoft DNA, 113-115
Nilsson, 114-115
Serviço, 49-51
três principais, 39-42
Camadas, evolução das, 38-40
Camadas, Serviço, 141-148
como funciona, 141-145
exemplo
lançamento de receitas, 145, 147-148
leitura adicional, 144-145
quando usá-la, 144-145,
Camadas de Dados, descendo para as, 106-110
camadas de apresentação, 108-110
fonte de dados Módulos Tabela, 107-108
fonte de dados para Modelos de Domínio, 107109
fonte de dados para Roteiros de Transação, 106108
Camadas de domínio, começando com, 106-107
Camadas DNA, Microsoft, 113-115
Campos, Identidade, 215-232
como funciona, 215-219
exemplos
chave integral (C#), 219-221
usando chave composta (Java), 222-232
usando tabela de chaves (Java), 220-223
leitura adicional, 219-220
quando usá-los, 218-220
Capacidade de resposta, 79-80
reduzindo isolamento para aumentar a, 86-88
Carga, flutuação de, 201-202
Carga Tardia, 200-213
como funciona, 200-203
exemplos
inicialização tardia (Java), 202-203
proxy virtual Java), 202-205
usando armazenador de valor (Java), 204-206
usando fantasmas (C#), 205-213
quando usá-la, 202-203

Caso Especial, 462-464
como funciona, 462-463
exemplo
objetos nulos simples (C#), 463-464
leitura adicional, 462-463
quando usá-lo, 462-463
Chave composta (Java), 222-232
Chave estrangeira, mapeamento de, 233-243
como funciona, 233-236
exemplos
busca em diversas tabelas (Java), 239-240
coleção de referências (C#), 240-243

inoice

referência de valor único (Java), 236-240
quando usá-lo, 235-237
Chaves
compostas, 222-232
integrais, 219-221
Chaves, tabela de, 220-223
Classe Dinheiro, 457-461
Classe Tabela, Herança de, 276-282
como funciona, 276-277
exemplo
jogadores e assemelhados (C#), 277-282
leitura adicional, 277-279
quando usá-la, 276-279
Cliente, Estado da Sessão no, 427-428
como funciona, 427-428
quando usá-lo, 427-428
Compartilhado, Bloqueio Offline Otimista (Java),
414-420
Compartilhado, Bloqueio Offline Pessimista (Java),
419-421

Concorréncia, 78-92
concorrência em servidor de aplicação, 91-92
contextos de execução, 79-81
controles de concorrência otimista e pessimista,
81-85
isolamento e imutabilidade, 80-82
offline, 78-79, 88-89
padrões de controle de concorrência offline, 89-91
problemas de concorréncia, 79-80

servidor de aplicação, 91-92
Concorrência, controles de
Offline, padrões para, 89-91
otimista e pessimista, 81-85
ACID (atomicidade, consistência, isolamento e
durabilidade), 85-90
deadlocks, 84-85
evitando leituras inconsistentes, 82-84
transações, 85
Concorrência offline, padrões de, 391-425
Conexões de bancos de dados, 67-69
Confirmada (commited), leitura, 86-87
Conjuntos de dados ADO.NET, 154-157
Conselhos específicos para determinadas tecnologias, alguns, 109-113
Java e J2EE, 109-111
“NET, 110-112
procedimentos armazenados, 111-113
serviços Web, 112-113
Contextos de execução, 79-81
Controlador de Aplicação, 360-366
como funciona, 360-362
exemplo
Controlador de Aplicação modelo de estados
(Java), 362-366
leitura adicional, 362-363
quando usá-lo, 361-363

483

Controlador de página, 318-327
como funciona, 318-319
exemplos
exibição simples com controle servlet (Java), 319322
exibição simples com vista JSP (Java), 319-322
manipulador de pagina com código por trás
(C#), 324-327
usando JSP como manipulador (Java), 321-325
quando usá-lo, 318-320
Controlador frontal, 328-332
como funciona, 328-330
exemplo
exibição simples (Java), 330-332
leitura adicional, 330-331
quando usá-lo, 329-330
Controladores
Aplicação de modelo de estados, 362-366
exibição simples com, 319-322
usando JSP como vista com controladores separados, 337-340
Controles
de concorrência otimista e pessimista, 81-85
ACID (atomicidade, consistência, isolamento e
durabilidade), 85-90

deadlocks, 84-85
evitando leituras inconsistentes, 82-84
transações, 85
padrões para concorrência offline, 89-91
Correção, 79-80
Customizadas, JSP e igs, 355-359

D

Dados
imutáveis, 81-82
lendo, 58-60
registro de, 95
Dados, Mapeador de, 170-185

camada de domínio com, 396-400

como funciona, 170-175
exemplos
criando objetos vazios (Java), 183-185
mapeador simples de banco de dados (Java),
175-181
separando métodos de busca (Java), 180-184
quando usá-lo, 174-176
Deadlocks, 84-85
Dependentes de uma pessoa, encontrando os, 311313
Dinheiro, 455-461
como funciona, 455-457
exemplo
classe dinheiro (Java), 457-461
quando usá-lo, 456-458
Dinheiro, classe (Java), 457-461
Distribuição, estratégias de, 99-104

