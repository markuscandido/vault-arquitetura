# 16_Cap_13_Padroes_de_Mapeamento_em_Metadados_Objeto_Relacionais

316

Parte Il e Os PaprOes

terface com o usuário. Quando você está trabalhando com um modelo, você

está pensando em políticas de negócio, talvez em interações com bancos de

dados. Certamente você usará bibliotecas diferentes, muito diferentes, ao tra-

balhar com a vista ou com o modelo. Muitas vezes, as pessoas preferem uma
área à outra e se especializam em um dos lados da linha.

* Dependendo do contexto, os usuários querem ver as mesmas informações

básicas do modelo de diferentes formas. Separar o modelo da vista permite
que você desenvolva diversas apresentações — de fato, interfaces completamente diferentes — e ainda use o mesmo código do modelo. O mais notável é
que isso poderia estar fornecendo o mesmo modelo com um cliente rico, um
navegador Web, uma API remota e uma interface em linha de comando.
Mesmo dentro de uma única interface Web você poderia ter diferentes páginas de clientes em diferentes pontos de uma aplicação.

* Osobjetos não-visuais são geralmente mais fáceis de testar do que os visuais.

Separar a apresentação do modelo permite que você teste facilmente toda a

lógica do domínio, sem recorrer a coisas tais como complicadas ferramentas

de roteiros (scripting) para a interface com o usuário.

Um ponto-chave nesta separação é a direção das dependências: a apresentação
depende do modelo, mas o modelo não depende da apresentação. As pessoas pro-

gramando o modelo não deveriam ter conhecimento algum de qual apresentação es-

tá sendo usada, o que ao mesmo tempo simplifica a sua tarefa e torna mais fácil a adi-

ção de novas apresentações mais tarde. Isso também significa que as alterações na

apresentação podem ser feitas livremente sem alterar o modelo.

Este princípio introduz uma questão comum. Com uma interface de cliente rico com diversas janelas, é provável que existam diversas apresentações de um modelo em uma tela de uma só vez. Se um usuário fizer uma alteração no modelo a partir

de uma apresentação, as outras precisam mudar também. Para fazer isso sem criar
uma dependência, você normalmente precisa de uma implementação do padrão Ob-

server [Gang of Four], tal como propagação de evento ou um listener. A apresentação
atua como o observador do modelo: toda vez que o modelo muda, ele dispara um
evento e as apresentações atualizam a informação.

A segunda divisão, a separação da vista e do controlador, é menos importante.

De fato, a ironia é que quase todas as versões de Smalltalk não fizeram realmente

uma separação vista /controlador. O exemplo clássico de por que você iria querer se-

pará-los é para suportar comportamento editável e não-editável, o que você pode fazer com uma vista e dois controladores para os dois casos, em que os controladores
são estratégias [Gang of Four] para a vista. Na prática, entretanto, a maioria dos sistemas tem apenas um controlador por vista, de modo que esta separação geralmente não é feita. Ela voltou a estar em voga com interfaces Web nas quais ela se torna

útil para novamente separar o controlador e a vista.
O fato da maioria dos frameworks para a interface com o usuário combinar vista

e controle levou a muitas citações errôneas do padrão MVC. O modelo e a vista são
óbvios, mas onde está o controle? A idéia comum é que ele se situa entre o modelo e
a vista, como no Controlador de Aplicação (360) — o fato da palavra “controlador” ser

usada em ambos os contextos não ajuda. Sejam quais forem os méritos de um Controlador de Aplicação (360), trata-se de algo muito diferente de um controle MVC.

CapiTuLo 14 e PADRÕES DE APRESENTAÇÃO WEB

317

Para os propósitos deste conjunto de padrões, estes princípios são realmente tu-

do o que você precisa saber. Se você quiser se aprofundar mais em MVC a melhor referência disponível é [POSA].

Quando

Usá-lo
Como eu disse, o valor do MVC

está nas suas duas separações. Destas, a separação

entre a apresentação e o modelo é um dos mais importantes princípios do projeto de

software, e a única vez em que você não deve segui-lo é em sistemas muito simples
em que o modelo não tem nele nenhum comportamento real. Assim que você tiver
alguma lógica não-visual, deve aplicar a separação. Infelizmente, muitos dos frame-

works para interface com o usuário tornam isso difícil, e aqueles que não o fazem,
muitas vezes, são ensinados sem enfatizar a separação.

A separação entre a vista e o controlador é menos importante, de modo que eu

só a recomendaria quando fosse realmente útil. Para sistemas com clientes ricos, isso

acaba sendo quase nunca, embora seja comum emfront ends Web no qual o controle

é separado. A maioria dos padrões sobre o projeto Web é baseada nesse princípio.

318

Parte Il e Os PaprOes

Controlador de Pagina (Page Controller)
Um objeto que trata uma solicitação para uma página
ou ação específica em um site Web.

Controlador de Pagina
-- tratar comandos get
post HTTP

-- decidir qual

modeloe

vista usar

po

Po
Ln

H

Modelo

=

-- lógica do dominio

Ai
|

i
4

Ea

!

a
Vista
-- mostrar HTML

A experiência Web básica da maioria das pessoas é com páginas HTML estáticas.

Quando você solicita um HTML estático, você passa para o servidor Web o nome e o

caminho de um documento HTML nele armazenado. A noção-chave é que cada pá-

gina em um site Web é um documento separado no servidor. Com páginas dinâmi-

cas, as coisas podem ficar muito mais interessantes já que há um relacionamento
muito mais complexo entre nomes de caminhos e o arquivo que atende à solicitação.

Entretanto, a abordagem de um caminho levando a um arquivo que trata a solicita-

ção é um modelo simples de entender.

2

S5

O resultado é que o Controlador de Página tem um controlador de entrada para
cada página lógica do site Web. Esse controlador pode ser a própria página, como freqiientemente ocorre em ambientes de páginas servidoras, ou pode ser um objeto separado que corresponda àquela página.

Como Funciona
A idéia básica por trás de um Controlador de Página é fazer com que um módulo no
servidor Web aja como um controlador para cada página no site Web. Na prática, ele

não funciona exatamente como um módulo por página, já que, às vezes, você pode

clicar em um link e obter páginas diferentes, dependendo da informação dinâmica.

Mais precisamente, os controladores se associam a cada ação, que pode ser clicar em

um [ink ou em um botão.

O Controlador de Página pode ser estruturado tanto como um roteiro (script CGI,
servlet, etc), quanto como uma página servidora (ASP, PHP, JSP, etc). Usar uma pá-

gina servidora geralmente combina o Controlador de Página e uma Vista Padrão (333)

no mesmo arquivo. Isso funciona bem para a Vista Padrão (333), mas não tão bem pa-

ra o Controlador de Página, porque é mais complicado estruturar apropriadamente o

módulo. Se a página for uma apresentação simples, isso não é problema. Entretan-

to, se houver lógica envolvida para extrair dados da solicitação ou decidir qual vista mostrar, então você pode acabar com um código scriptlet complicado na página

servidora.
Um modo de lidar com o código scriptlet é usar um objeto auxiliar. Neste caso,

a primeira coisa que a página servidora faz é chamar o objeto auxiliar para tratar to-

CapituLo 14 e PADRÕES DE APRESENTAÇÃO Wes

319

da a lógica. O objeto auxiliar pode retornar o controle para a página servidora origi-

nal ou ele pode transferi-lo para uma outra página servidora diferente para que esta
atue como a vista. Neste caso, a página servidora trata a solicitação, mas a maior par-

te da lógica do controlador fica no objeto auxiliar.

Outra abordagem é tornar o controlador e o manipulador um script. O servidor

Web passa o controle para o script, este executa as responsabilidades do controlador
e finalmente transfere o controle para uma vista apropriada para que esta mostre os
resultados.
As responsabilidades básicas de um Controlador de Página são:

* Decodificar a URL e extrair quaisquer dados do formulário para ter disponiveis todos os dados para a ação.

* Criar e chamar quaisquer objetos do modelo para processar os dados. Todos

os dados relevantes da solicitação HTML devem ser passados para o mode-

lo, de modo que os objetos do modelo não precisem de nenhuma conexão
com a solicitação HTML.

* Determinar qual vista deve mostrar a página de resposta e transferir a informação do modelo para ela.
O Controlador de Página não precisa ser uma única classe, mas pode chamar
objetos auxiliares. Isso é particularmente útil se diversos manipuladores tiverem que
executar tarefas semelhantes. Uma classe auxiliar pode então ser um bom lugar para

colocar qualquer código que, de outra forma, seria duplicado.

Não há nenhum motivo para que você não possa ter algumas URLs tratadas

por páginas servidoras e algumas por scripts. Quaisquer URLs que tenham pouca

ou nenhuma lógica de controle são melhor manipuladas por uma página servido-

ra, uma vez que ela fornece um mecanismo simples que é fácil de entender e modi-

ficar. Quaisquer URLs com lógica mais complicada vão para um script. Já me depa-

rei com equipes que queriam tratar tudo da mesma forma: tudo são páginas servi-

doras ou tudo é um roteiro. Quaisquer vantagens de consistência em tal aplicação
são geralmente contrabalançadas pelos problemas de páginas servidoras carrega-

das de scriptlets ou um número grande de scripts que servem apenas como uma sim-

ples passagem.
Quando

Usá-lo
A principal decisão a tomar é quando usar um Controlador de Página ou um Controla-

dor Frontal (328). Dos dois, o Controlador de Página é o mais rotineiro para se trabalhar

e leva a um mecanismo natural de estruturação no qual ações particulares são tratadas por páginas servidoras particulares ou classes scripts. Seu compromisso é, portanto, a maior complexidade do Controlador Frontal (328) contra as várias vantagens

do Controlador Frontal, a maior parte das quais faz diferença em sites Web que tenham
maior complexidade de navegação.
O Controlador de Página funciona especialmente bem em um site onde a maior
parte da lógica de controle seja bastante simples. Neste caso, a maioria das URLs po-

de ser tratada com uma página servidora e os casos mais complicados com objetos

auxiliares. Quando a sua lógica de controle é simples, o Controlador Frontal (328) adi-

ciona um grande overhead.

E

320

Parte ll e Os Parões

Não é raro encontrar um site onde algumas solicitações são tratadas por Contro-

Iadores de Página e outras por Controladores Frontais (328), especialmente quando uma

equipe estiver refatorando de uma solução para a outra. Na realidade, os dois pa-

drões se misturam sem muito problema.

Exemplo: Apresentação Simples com um Controlador Servlet
e uma Vista JSP (Java)

Um exemplo simples de um Controlador de Página exibe algumas informações a res-

peito de alguma coisa. Aqui o mostraremos exibindo algumas informações sobre um
artista.

A URL é executada em http: //www. thingy. com/recordingApp/artista?name=daniela-

Mercury.

O servidor Web precisa ser configurado para reconhecer /artista como uma cha-

mada para o ControladorDeartista. Em Tomcat, você faz isso com o seguinte código no
arquivo web.xml:

<servlet>

«servlet -name>artista</servlet-name>

«<servlet -class>act ionController .ControladorDeArtista</servlet-class>

</servlet>

«servlet-mapping>

<servlet -name>artista</servlet
-name>
«<url-pattern>/artista</url-pattern>

<servlet-mapping>
ção.

O controlador de artistas precisa implementar um método para tratar a solicita-

e

&5

Action Servlet

Artista

Controlador

de Artista

[SSC

ti

ti

Bust
lerNome

>) Ausiliar de Artista -——————>

>)

Artistajsp

Figura 14.1
Classes envolvidas em uma exibição simples com um servlet Controlador de
Página e uma vista JSP.

CapiTuLo 14 e PADRÕES DE APRESENTAÇÃO Wes

321

class ControladorDeArtista...

public void executarLeitura (HttpServletRequest solicitação, HrtpServletResponse resposta)
throws IOException, ServletException {

Artista artista = Artista.buscarChamado(solicitacdo.getParameter
("nome") ) ;
if

{artista ==

null)

forward("/NomeDoArtistaFaltando.jsp",

else {

solicitação,

resposta);

solicitação. setattribute ("auxiliar", new AuxiliarDeArtista (artista));
forward

("/artista.jsp",

solicitação,

resposta);

Embora este seja um caso muito simples, ele cobre os pontos mais importantes.

Primeiro, o controlador precisa criar os objetos do modelo necessários para executar

suas tarefas; no caso do exemplo, apenas encontrar o objeto do modelo correto para

a apresentação. Em segundo lugar, ele coloca a informação correta na solicitação

HTTP a fim de que a JSP possa exibi-la apropriadamente. Neste caso, ele cria um objeto auxiliar e coloca-o na solicitação. Finalmente, ele transfere o controle para a Vista Padrão (333) para que esta trate a apresentação. A transferência de controle é um
comportamento comum, logo, ele naturalmente se situa em uma superclasse para todos os Controladores de Página.
class ActionServlet...

protected void forward (String alvo,

HttpServletRequest solicitação,

HttpServletResponse resposta)
throws IOException, ServletException

RequestDispatcher despachante = getServletContext ( ) .getRequestDispatch
(alvo)
er ;
forward (solicitação,
despachant
e.

resposta);

O ponto principal de acoplamento entre a Vista Padrão (333) e o Controlador de
Página são os nomes dos parâmetros usados na solicitação (request) para passar
quaisquer objetos de que a JSP precise.
A lógica do controlador aqui é realmente muito simples, mas podemos continuar a usar o servlet como um controlador mesmo quando ela fica mais complexa.

Podemos ter um comportamento semelhante para álbuns, com a ressalva de que os

álbuns clássicos têm um objeto do modelo diferente e são apresentados com uma JSP
diferente. Para obter este comportamento, podemos novamente usar uma classe con-

troladora.

class ControladorDeálbum. ..
public void executarLeitura

(HttpServletRequest solicitação, HttpServletResponse resposta)

throws IOException, ServletBxception

Album álbum = Album.buscar (solicitação.getParameter
("id") ;
if (Album == null) {

forward(*/erroflbunfaltando.
jsp", solicitação, resposta) ;
retum

E

322

Parte Il e Os PaprOes

solicitacdo.setAttribute("auxiliar", álbum) ;

if (álbum instanceof ÁlbunClássico)
forward ("/albumClassico.jsp", solicitação,

else

forward

("/álbum.jsp",

solicitação,

resposta);

resposta) ;

Perceba que neste caso estou usando os objetos do modelo como auxiliares em
vez de criar uma classe auxiliar separada. Vale a pena fazer isso se uma classe auxi-

liar for apenas um despachante burro para a classe do modelo. Porém, se você fizer

isso, assegure-se de que a classe do modelo não contém qualquer código dependente do servlet. Qualquer código que seja dependente do servlet deve estar em uma clas-

se auxiliar separada.

Exemplo: Usando uma JSP como Manipulador (Java)
Usar um servlet como controlador é um caminho possível, mas o caminho mais co-

mum é fazer da própria página servidora o controlador. O problema com esta abor-

dagem é que ela resulta em código scriptlet no início da página servidora, e, como você deve ter percebido, penso que o código scriplet tem a mesma relação com um soft-

ware bem-projetado que o wrestling profissional tem com o esporte.

Apesar disso, você pode tratar solicitações com uma página servidora como um

manipulador, enquanto delega o controle para o objeto auxiliar para que este efetivamente execute a função de controlador. Isso preserva a propriedade simples de ter
sua URL disponibilizada por páginas servidoras. Farei isso para a exibição do álbum,

usando a URL na forma http: //localhost :8080/isa/album.

jsp?id=zero. A maioria dos ál-

buns é apresentada diretamente com a JSP de álbum, mas gravações de clássicos reE

S5

querem uma apresentação diferente, uma JSP de álbum clássico.
O comportamento deste controlador aparece em uma classe auxiliar para o JSP.
A classe auxiliar é configurada na própria JSP do álbum.

álbun.jsp...
<jsp:useBean id="auxiliar" class="actioncontroller.AuxiliarCtr1Álbun*/>

<sauxiliar.
init (solicitação, resposta); $>

A chamada ao método init configura o auxiliar para executar o comportamento
do controlador.
class AuxiliarCtrlAlbum extends ControladorAuxiliar...

public void init (HttpServletRequest solicitação, HttpServletResponse resposta) {
init (solicitação, resposta) ;
super.

js", solicitação, resposta) ;
if (Lerálbum( ) == null) forvard ("erroPaltandoálbum.
if (lerálbum( ) instanceof Álbunclássico) {

solicitação. setattribute ( "auxiliar", lerálbum( ))
jsp", solicitação, resposta) ;
forward ("/albunClassico.

O comportamento comum do auxiliar fica, naturalmente, em uma superclasse

auxiliar.

CapiTuLo 14 e PADRÕES DE APRESENTAÇÃO WEB

323

class ControladorAuxiliar...
public void init

(HttpServletRequest solicitação, HttpServletResponse resposta)

this.solicitação = solicitação;

}

(

this.resposta = resposta;

protected vois forward

(String alvo,

HrtpServletRequest solicitação,
HttpServletResponse resposta)

try {
RequestDispatcher despachante = solicitação.getReguestDispatcher
(alvo) ;
if (despachante == null) resposta. senderror (resposta.SC
NO CONTENT) ;
else despachante. forward (solicitação, resposta) ;
) catch (IOException e) {

throw new ApplicationException
) catch (ServletException e) (

)

(e);

throw new ApplicationException (e) ;

A principal diferença entre o comportamento do controlador aqui e aquele
quando do uso de um servlet é que a JSP manipuladora também é a vista default e, a
menos que o controlador transfira o controle para uma outra JSP, este retorna para o
manipulador original. Isso é uma vantagem quando você tem páginas na qual, na

maior parte do tempo, a JSP atua diretamente como a vista e, portanto, não há nenhuma transferência a ser feita. A inicialização do objeto auxiliar atua para dar a par-

tida em qualquer comportamento do modelo e configurar as coisas para a vista mais
tarde. É um modelo simples de seguir, já que as pessoas geralmente associam uma
página Web com a página servidora que atua como sua vista. Frequentemente isso =
E
também se adapta naturalmente à configuração do servidor Web.
A chamada para inicializar o manipulador é um pouco deselegante. Em um am-

biente JSP essa complicação pode ser muito melhor tratada com uma etiqueta personalizada (custom tag). Tal etiqueta pode criar automaticamente um objeto apropriado, colocá-lo na solicitação e inicializá-lo. Com isso, tudo o que você precisa é de uma

etiqueta simples na página JSP.

<auxiliar:init name: "actioncontroller.AuxiliarCtrlÁlbum"/>

A implementação da etiqueta personalizada executa então o trabalho.
class EtiquetaDeInicializaçãoDoAuxiliar extends EtiquetaDoAuxiliar...

private String nomeDaClasseAuxiliar;

public void gravarNome (String nomeDaClasseAuxiliar)

this noneDaClassehuxiliar

= noneDaClasseAuxiliar;

public int iniciarBtiqueta ( ) throws dspException {

{

ControladorAuxiliar auxiliar = null;
try {
auxiliar = (Controladorauxiliar)
Class. forName (noneDaClasseAuxiliar) .newInstance( );
} catch (Exception e) {

324

Parte ll e Os Parões

throw new ApplicationException ("Não foi possível instanciar" + nomeDaClasseAuxiliar, e);

iniciarAuxiliar (auxiliar) ;

pageContext .setAttribute (AUXILIAR,

}

return SKIP BODY;

auxiliar) ;

private void iniciarAuxiliar (ControladorAuxiliar auxiliar)

{

HttpServletRequest solicitação = (HttpServletRequest) pageContext .getRequest ( | ;

}

HttpServletResponse resposta = (HttpServletResponse) pageContext .getResponse( |;
auxiliar.
init (solicitação, resposta) ;

class Etiquetaduxiliar...
public static final String AUXILIAR = "auxiliar";

Se eu for usar etiquetas personalizadas dessa forma, eu poderia ainda criá-las

para acessar também as propriedades.

class EtiquetauxiliarDeLeitura extends Etiquetanuxiliar...
private String nomeDaPropriedade;
public void gravarPropriedade (String noneDaPropriedade) {
this.noneDaPropriedade = noneDaPropriedade;
)
public int iniciarBtiqueta

| ) throws JspException {

try {

E

pageContext .getOut ( ) .print (lerPropriedade (nomeDaPropriedade) ) ;

2

) catch (IOException e) {

3

)

3

£

&

}

throw new JspException("Ndo

foi possível imprimir em um writer");

return SKIP BODY;

class Etiquetanuxiliar...
protected Object lerPropriedade (String propriedade) throws Jspexception {
Object auxiliar = lerduxiliar( );
try {
final Method leitor = auxiliar.getClass(
return leitor.
invoke (auxiliar, null) ;

).getMethod (LendoMétodo (propriedade),

null);

) catch (Exception e) (

throw new JspException

}

}

("Não foi possível chamar "+ lendoMétodo{propriedade)

+ " - " + e.getMessage( |);

private Object lerAuxiliar ( ) throws JspException {

Object auxiliar = pageContext .getAttribute (AUXILIAR) ;
if

(auxiliar == null)

return auxiliar;

throw new JspException ("Auxiliar não encontrado") ;

private String lendoMétodo (String propriedade) (
String noneDoMétodo = "ler" + propriedade.
substring (0,1) .toUpperCase( ) +
propriedade. substring (1);
return nomeDonétodo;

CapituLo 14 e PADRÕES DE APRESENTAÇÃO WEB

325

(Você pode achar que é melhor usar o mecanismo de Java Beans do que apenas

chamar um método de leitura usando reflexão. Se este for o caso, provavelmente você está certo... e, provavelmente, você também é inteligente o bastante para descobrir

como alterar o método para fazer isso.)

Com a etiqueta de leitura definida, posso usá-la para extrair informações do ob-

jeto auxiliar. A etiqueta é mais curta e elimina qualquer chance de eu digitar “auxiliar” errado.

<B><auxiliar:get property = "título"/></B>

Exemplo: Manipulador de Página com um Código Por Trás (C#)
O sistema Web em .NET é projetado para trabalhar com os padrões Controlador de Página e Vista Padrão (333), ainda que você certamente possa decidir tratar eventos Web
com uma abordagem diferente. Neste próximo exemplo, usarei o estilo preferido de

«NET, construindo a camada de apresentação sobre um domínio usando um Módulo

Tabela (134) e usando conjuntos de dados (data sets) como os principais transportadores de informação entre as camadas.

Desta vez, teremos uma página que mostra pontos marcados e a média de pontos para um turno de uma partida de críquete. Como sei que terei muitos leitores aflitos com a falta de experiência nesta forma de arte, deixe-me resumir dizendo que os
pontos marcados são os pontos do rebatedor, e a média de pontos é o número de

pontos que ele marca dividido pelo número de bolas arremessadas em sua direção.

Os pontos marcados e as bolas arremessadas estão no banco de dados. A média de

pontos precisa ser calculada pela aplicação — um pedaço de lógica de dominio pequeno, mas pedagogicamente útil.
O manipulador neste projeto é uma página Web ASP.NET, capturada em um ar-

quivo .aspx. Assim como com outras construções de páginas servidoras, este arquivo permite que você insira lógica de programação diretamente nas páginas como

scriptlets. Já que você sabe que eu preferiria beber cerveja ruim a escrever scriptlets,

você sabe que há pouca chance de eu fazer isso. Meu salvador neste caso é o meca-

nismo de código por trás da ASP.NET que permite que você associe um arquivo e
classe comuns à página aspx, sinalizado no cabeçalho da página aspx.

<%e Page language = "C#" Codebehind="bat .aspx.cs" AutoBventiireup="false! trace: "False"
Inherits="rebatedores. PáginaDeRebatidas"

%>

A página é configurada como uma subclasse da classe código “por trás”, e des-

ta forma pode usar todas as suas propriedades e métodos do tipo protected. O objeto
página é o manipulador da solicitação, e o código por trás pode definir o tratamento

definindo um método Carregar Página. Se a maioria das páginas seguir um fluxo comum, posso definir uma Camada Supertipo (444) que tenha um método padrão [Gang
of Four] para isso.

class PáginaDeCríguete...
protected void Carregar Página

(object remetente,

db = new OleDbConnection
(DB. ConnectionString) ;
if (háParâmetrosFaltando( ))

System.EventArgs e) {

transferegrro (mensagemeFaltaDeParânetros) ;

DataSet ds = lerDados( );

E

326

Parte Il e Os PacrÕEs

if (nãoHáDados (ds) )

transfereBrro ("Nenhum dado corresponde à sua solicitação”) ;
aplicarLógicaDoDonínio (ds) ;

DataBind ( );

prepararInterfaceConsuário (ds) ;

O método padrão divide o tratamento da solicitação em vários passos comuns.

Desta maneira, podemos definir um fluxo único comum para tratar solicitações Web,

ao mesmo tempo em que permitimos que cada Controlador de Página forneça imple-

mentações para os passos específicos. Se você fizer isso, depois que tiver escrito uns
poucos Controladores de Páginas, saberá que fluxo comum usar no método padrão. Se

alguma página precisar fazer algo completamente diferente, ela sempre poderá so-

brescrever o método de carga da página.

A primeira tarefa é fazer a validação dos parâmetros chegando à página. Em
um exemplo mais realista, isso poderia requerer a verificação de valores de várias

formas,

mas

neste

caso

estamos

apenas

decodificando

uma

URL

na

forma

http: //localhost /rebatedores /bat .aspx?equipe=Inglaterra&per{odo=2&jogo=905. A única va-

lidação neste exemplo é que os vários parâmetros requeridos para a consulta ao
banco de dados estejam presentes. Como

de costume, tenho sido extremamente

simplista na manipulação de erros até que alguém escreva um bom conjunto de padrões sobre validação — então, aqui, a página especificada define um conjunto de
parâmetros obrigatórios, e a Camada Supertipo (444) tem a lógica para verificá-los.

class PáginaDecríquete...
abstract protected String( | parametrosObrigatérios(
private Boolean háParâmetrosFaltando () {

foreach (String parâmetro in parametrosObrigatérios| ))
if (Request .Params [parâmetro] == null)

o:

S5

);

}

return true;

return false;

private String mensagemDeFaltaDeParametros ( ) {

get {
String resultado = "<p>Estéo faltando parânetros obrigatórios nesta
página: <p>";
resultado += "<UL>";
foreach (String parâmetro in parâmetrosObrigatórios! |)
if (Request
.Params (parânetro] == null)
resultado

+= String. Format ("<LI>{0}</LI>", parâmetro) ;

resultado += "</UL>"
return resultado;

protected void transfereErro (String mensagem) (
Context . Items. Add ("mensagemDeErro", mensagem) ;

Context .Server.Transfer("Brro.aspx") ;

}
class PaginaDeRebatidas. ..

override protected String[ | parémetrosObrigatérios ( ) {
String( ] resultado = ("equipe",

return resultado;

"período",

"jogo"};

CapiTuLo 14 e PADRÕES DE APRESENTAÇÃO Wes

327

O próximo estágio é extrair os dados do banco de dados e colocá-los em um ob-

jeto do tipo conjunto de dados (data set) ADO.NET desconectado. Aqui está uma única consulta para a tabela de rebatidas.

class PáginaDeCríguete...
abstract protected DataSet lerDados( |;
protected Boolean nãoHáDados

(DataSet ds)

(

foreach (DataTable tabela in ds.Tables)
if (tabela.Rows.Count != 0) return false;
return

true;

}
class PáginaDeRebatidas..
override protected DataSet lerDados

( ) (

OleDbCommand comando = new OleDbCommand (SQL, db) ;
comando. Parameters.Add

(new OleDbParameter("equipe",

equipe));

comando. Paraneters Add (new OleDbParaneter("perfodo", período));
comando. Parameters.Add (new OleDbParameter("jogo", jogo));
OleDbadapter da = new OleDbAdapter (comando) ;
DataSet resultado = new DataSet ( );

da.Fill (resultado, Rebatidas.TABLE NAME);
return resultado;

}
private const String SQL =
@"SELECT

+ from

rebatidas

WHERE equipe = ? AND período = ? AND idDoJogo = ?
ORDER

BY ordemDeRebatidas";

Agora é a hora da lógica do domínio, organizada como um Módulo Tabela (134).

O controlador passa o conjunto de dados encontrado para o Módulo Tabela (134) para
processamento.

class PáginaDecríguete...
protected virtual void aplicarLógicaDoDomínio

(DataSet ds)

{ )

class PAginaDeRebatidas. ..
override protected void aplicarLógicaDoDomínio

rebatidas = new Rebatidas(conjuntoDeDados) ;
rebatidas.Calculartédias( );

(DataSet conjuntoDeDados)

{

Neste ponto, a parte controladora do manipulador da página está terminada.

Quero dizer com isso, em termos clássicos do Modelo Vista Controle (315), que o con-

trolador deve agora transferir a apresentação para a vista. Neste projeto, a PáginaDerebatidas atua tanto como controle quanto como vista, e a última chamada a prepararIn-

terfaceConlsuário, é parte do comportamento da vista. Posso agora dizer adeus a este

exemplo deste padrão. Entretanto, suspeito que você pensará que falta um certo fechamento dramático, então você poderá encontrar a continuação do exemplo mais
adiante (página 333).

E

328

Parte ll e Os Panrões

Controlador Frontal (Front Controller)
Um controlador que trata todas as solicitações para um site Web.

A
Manipulador

Comando
RS

executarGet
executarPost

processar

Comando
Concreto 1
processar

Comando
Concreto 2
processar

Em um site Web complexo, há muitas coisas semelhantes que você precisa fazer ao

tratar uma solicitação. Estas coisas incluem segurança, internacionalização e forneci-

mento de apresentações particulares para determinados usuários. Se o comporta-

mento do controlador de entrada for espalhado por vários objetos, muito deste com-

Cont

3

portamento pode acabar duplicado. Além disso, é difícil alterar comportamento em

tempo de execução.

O Controlador Frontal consolida todo o tratamento de solicitações canalizando-

as através de um único objeto manipulador. Este objeto pode executar o comporta-

mento comum, o qual pode ser modificado em tempo de execução com decoradores.
O manipulador então despacha para objetos do tipo comando que possuem compor-

tamento específico relacionado a uma solicitação.

Como Funciona
Um Controlador Frontal trata todas as chamadas a um site Web, e é normalmente es-

truturado em duas partes: um manipulador Web e uma hierarquia de comandos. O

manipulador Web é o objeto que efetivamente recebe as solicitações post ou get do

servidor Web. Ele extrai apenas as informações necessárias da URL e da solicitação
para decidir que tipo de ação iniciar e então delega a um objeto comando para exe-

cutar a ação (veja a Figura 14.2).
O manipulador Web é quase sempre implementado como uma classe em vez

de como uma página servidora, já que ele não produz nenhuma resposta. Os co-

mandos também são classes em vez de páginas servidoras e, na verdade, não preci-

sam de nenhum conhecimento sobre o ambiente Web, embora freqiientemente a in-

formação HTTP seja passada a eles. O manipulador Web é, ele próprio, normalmente um programa razoavelmente simples que não faz nada além de decidir qual comando executar.

Capitulo 14 e PADRÕES DE APRESENTAÇÃO Wes

329

um manipulador
tratar get HTTP

| [—
examinar URL
new (com dados)

processar

Figura 14.2

comando
concreto
r

Como o Controlador Frontal funciona.

O manipulador Web pode decidir qual comando executar tanto estática quanto
dinamicamente. A versão estática envolve analisar a URL e usar lógica condicional.
A versão dinâmica envolve pegar um pedaço padrão da URL e usar instanciação dinâmica para criar uma classe comando.

O caso estático tem a vantagem da lógica explícita, verificação de erros no despacho em tempo de compilação e muita flexibilidade na aparência das suas URLs. O caso
dinâmico permite a você adicionar novos comandos sem alterar o manipulador Web.
Com a invocação dinâmica você pode colocar o nome da classe comando na
URL ou você pode usar um arquivo de propriedades que liga as URLs aos nomes das
classes comando. O arquivo de propriedades é outro arquivo para editar, mas torna

mais fácil alterar os nomes de suas classes sem ser necessária uma grande pesquisa

pelas suas páginas Web.

Um padrão particularmente útil a ser usado com o Controlador Frontal é o Filtro

Interceptador, descrito em [Alur et al.]. Este é basicamente um decorador que encapsula o manipulador do controlador frontal permitindo a você criar uma cadeia de fil-

tros (ou pipeline de filtros) para lidar com questões tais como autenticação, logging e

identificação de local. O uso de filtros permite que você, dinamicamente, prepare os
filtros a usar em tempo de configuração.

Rob Mee mostrou-me uma variação interessante do Controlador Frontal usando

um manipulador Web de dois estágios, dividido em um manipulador Web degene-

rado e em um despachante. O manipulador Web degenerado extrai os dados básicos
dos parâmetros http e os passa para o despachante de tal modo que o despachante é

completamente independente do framework do servidor Web. Isso torna os testes
mais fáceis, porque o código de teste pode alimentar diretamente o despachante sem

ter que rodar em um servidor Web.

Lembre-se de que tanto o manipulador quanto os comandos são parte do con-

trolador. Em função disso, os comandos podem (e devem) escolher que vista usar para a resposta. A única responsabilidade do manipulador é escolher qual comando
executar. Uma vez que isso tiver sido feito, ele participa mais dessa solicitação.

s

330

Parte ll e Os Parões

Quando

Usá-lo
O Controlador Frontal é um projeto mais complicado do que sua contraparte óbvia, o
Controlador de Página (318). Ele precisa portanto apresentar algumas vantagens para
justificar o esforço.

Apenas um único Controlador Frontal tem que ser configurado no servidor Web;
o manipulador Web faz o resto do trabalho de despacho (dispatching). Isso simplifica

a configuração do servidor Web, o que é uma vantagem se ele for complicado de configurar. Com objetos comando dinâmicos, você pode adicionar novos comandos sem

alterar nada. Eles também tornam mais fácil portar a aplicação, já que você tem ape-

nas que registrar o manipulador em um modo específico do servidor Web utilizado.

Devido ao fato de você criar novos objetos comando em cada solicitação, você

não tem que se preocupar em tornar as classes dos comandos seguras a threads. Dessa maneira, você evita as dores de cabeça da programação multithread, entretanto vo-

cê tem de se assegurar de que não compartilha quaisquer outros objetos, tais como os

objetos do modelo.

Uma vantagem comumente mencionada de um Controlador Frontal é que ele
permite que você fatore código que, de outra forma, seria duplicado no Controlador

de Página (318). Contudo, para ser justo, você também pode fazer muito disso com
uma superclasse Controlador de Página (318).

Existe apenas um único controlador, então você, usando decoradores [Gang of

Four], pode facilmente melhorar o seu comportamento em tempo de execução. Você

pode ter decoradores para autenticação, codificação de caracteres, internacionaliza-

ção, e assim por diante, e adicioná-los usando um arquivo de configuração ou, até
mesmo, enquanto o servidor estiver rodando. ([Alur et al.) descrevem esta aborda-

Cont

E

gem em detalhe sob o nome Filtro Interceptador.)
Leitura Adicional

[Alur et al.] fornecem uma descrição detalhada de como implementar o Controlador
Frontal em Java. Eles também descrevem o Filtro Interceptador, que combina muito

bem com o Controlador Frontal.

Varios frameworks Web Java usam este padrão. Um exemplo excelente aparece

em [Struts].

Exemplo: Exibição Simples (Java)
Aqui está um caso simples de uso de Controlador Frontal para a tarefa original e inovadora de exibição de informações sobre um artista. Usaremos comandos dinâmicos com

uma URL da forma http: //localhost :8080/isa/musica?nome=malFuncionaécomando=Artista. O

parâmetro comando diz ao manipulador Web qual comando usar.

Começaremos com o manipulador, que implementei como um servlet.

class ServletProntal...

public void executarGet

(lttpServletRequest solicitação, HttpServletResponse resposta)

throws IOException,

ServletException {

ConandoFrontal comando = lerComando (solicitação) ;
conando.init (getServletContext ( ), solicitação, resposta);
comando. processar ( |;

CapiTuLo 14 e PADRÕES DE APRESENTAÇÃO Wes

331

HttpServiet

Front Serviet

Front Command

executarGet
executarPost

processar

Controlador

Controlador

processar

processar

de Artista

Figura 14.3.

de Álbum

As classes que implementam o Controlador Frontal.

private ComandoFrontal lerComando

(HttpServletRequest solicitação)

(

try {
return (ComandoProntal) lerClasseDoConando (solicitação) .newInstance( |;
) catch (Exception e) (

throw new ApplicationBxception (e);

}

private Class lerClasseDoComando (HttpServletRequest solicitação)

{

Class resultado;
final String nomeDaClasseDoComando =
"controladorFrontal." + "Comando" + (String) solicitação .getParameter ("comando") ;
try {

resultado = Class. forllame (noneDaClasseDoComando) ;

} catch (ClassNotFoundexception e) (

}

resultado = UnknowCommand. class;

return resultado;

A lógica é direta. O manipulador tenta instanciar uma classe cujo nome é forma-

do pela concatenação do nome do comando e a palavra “Comando”. Assim que ele
obtém o novo comando, ele o inicializa com as informações necessárias provenientes

do servidor HTTP. Passei o que precisava para este exemplo simples. Você pode preci-

sar de mais, como a sessão HTTP. Se você não conseguir encontrar um comando, usei

o padrão Caso Especial (462) e retornei um comando desconhecido. Como é muitas ve-

332

Parte ll e Os Paproes

zes o caso, o Caso Especial (462) permite que você evite muita verificação adicional de

erros.

Os comandos compartilham uma quantidade razoável de dados e comportamento. Todos eles precisam ser inicializados com informações do servidor Web.
class ComandoFrontal...

protected ServletContext contexto;

protected HttpServletRequest solicitação;

protected HttpServletResponse resposta;
public void init (ServletContext contexto,
HttpServletReguest solicitação,
HttpServletResponse resposta )

this.contexto = contexto;
this.solicitação = solicitação;
this.resposta = resposta;

Eles também podem fornecer comportamento comum, como um método para

passar adiante, e definir um método abstrato de processamento para os comandos

reais sobrescreverem.
class FrontConmand...

abstract public void process( ) throws ServletException, IOException;
protected forward (String alvo) throws ServletException, 10Bxception
{

RequestDispatcher despachante = contexto.getRequestDispatcher
(alvo) ;
despachante . forward (solicitação,

&

resposta);

O objeto comando é muito simples, pelo menos neste caso. Ele apenas implementa o método de processamento, o que envolve chamar o comportamento apro-

priado nos objetos do modelo, colocando a informação necessária para a vista na solicitação e passando adiante para uma Vista Padrão (333).

class Comandohrtista...
public void process( | throws ServletException, 10Exception {

Artista artista = Artista.buscarPeloNone
(sol icitacio.getParameter ("none") |;
solicitação. setAttribute ("auxiliar",

forward ("/artista.jsp") ;

new AuxiliarDeArtista(artista)) ;

O comando desconhecido apenas traz uma entediante página de erro.
class UnknowCommand...
public void process(

}

| throws ServletException,

forward!" /desconhecido. jsp") ;

I0Exception {

CapiTuLo 14 e PADRÕES DE APRESENTAÇÃO WEB

333

Vista Padrão (Template View)

Representa informações em HTML

inserindo marcadores em uma página HTML.

<HTML>

<P><B>

<ispilerPropriedade

Modelo

Livro

Autor

Auxilar do Livre

lesTítulo
lerAutor

name="AuxiliarDeLivro"

property="titulo'/> </B>
<BR/>

:
<jsp:lerPropriedade

Autor:

name="AuxiliarDoLivro"
property="autor/>
</P>
</HTML>

Escrever um programa que produza HTML é muitas vezes mais difícil do que você

poderia imaginar. Embora linguagens de programação estejam melhores na criação
de texto do que costumavam ser (alguns de nós lembram a manipulação de caracteres em Fortran e Pascal padrão), criar e concatenar strings ainda é trabalhoso. Se não
houver muito a ser feito, isso não é tão ruim, mas uma página HTML inteira é muita

manipulação de texto.

Com páginas HTML estáticas — aquelas que não se alteram de solicitação para

solicitação — você pode usar bons editores WYSIWYG. Mesmo aqueles que gostam
de editores de texto primitivos acham mais fácil simplesmente digitar o texto e iden-

tificadores em vez de mexer com concatenação de strings em uma linguagem de pro-

gramação.

É claro que o problema é com páginas Web dinâmicas — aquelas que pegam o

resultado de algo como pesquisas em bancos de dados e as inserem em HTML. A pá-

gina parece diferente em cada resultado, por isso editores HTML comuns não são

apropriados para o trabalho.

A melhor maneira de trabalhar é organizar a página Web dinâmica como você

faz com uma página estática, mas colocar marcadores que podem ser transformados

em chamadas para juntar informações dinâmicas. Já que a parte estática da página
atua como um padrão para a resposta específica, chamo isso de Vista Padrão.

Como Funciona
A idéia básica da Vista Padrão é inserir marcadores em uma página HTML estática

quando ela é escrita. Quando a página é usada para atender uma solicitação, os marcadores são substituídos pelo resultado de alguma computação, como uma pesquisa
em um banco de dados. Dessa forma a página pode ser mostrada do modo costumei-

ro, muitas vezes com editores WYSIWYG, muitas vezes por pessoas que não são pro-

gramadoras. Os marcadores então se comunicam com programas reais para enviar
os resultados.

334

Parte Il e Os PaprOes

Muitas ferramentas usam a Vista Padrão. O resultado é que este padrão não diz

respeito a como construir você mesmo uma Vista Padrão, mas sim como usá-la eficaz-

mente e qual é a alternativa.

Inserindo os Marcadores

Há um número de maneiras pelas quais os marcadores

podem ser colocados no HTML. Uma é usar etiquetas HTML-like. Isto funciona bem

com editores WYSIWYG porque eles percebem que qualquer coisa entre os colchetes

em ângulo (<>) é especial e a ignora ou a trata de maneira diferente. Se os identifica-

dores seguirem as regras de XML bem-formada, você também pode usar ferramentas XML no documento resultante (desde que seu HTML seja XHTML, é claro).
Outra maneira de fazer isso é usando marcadores de texto especiais no corpo do
texto. Editores WYSIWYG tratam isso como texto comum, ainda ignorando-o mas
provavelmente fazendo coisas aborrecidas tais como verificação ortográfica. A van-

tagem é que a sintaxe pode ser mais fácil do que a sintaxe pesada de HTML/XML.
Muitos ambientes fornecem o conjunto de etiquetas que você usa, mas mais e
mais plataformas agora lhe dão a habilidade de definir suas próprias etiquetas e

marcadores, de modo que você pode projetá-los para se ajustarem às suas necessidades específicas.

Uma das formas mais populares de Vista Padrão é uma página servidora como

ASP, JSP ou PHP. Estas realmente vão um passo à frente em relação à forma básica de

uma Vista Padrão, visto que permitem que você insira na página lógica de programa-

ção arbitrária, referida como scriptlet. Entretanto, no meu modo de ver, esta caracte-

rística é em verdade um grande problema e, ao usar tecnologia de páginas servidoras, você estaria melhor limitando-se ao comportamento básico da Vista Padrão.
8

A desvantagem mais óbvia de colocar muitos scriptlets em uma página é que isso elimina a possibilidade de não-programadores editarem a página. Isso é especialmente importante quando você estiver usando projetistas gráficos para o projeto da
página. Entretanto, os maiores problemas de inserir scriptlets na página decorrem do

fato de que uma página é um módulo pobre para um programa. Mesmo com uma

linguagem orientada a objetos a construção na página faz com que você perca a

maior parte das características estruturais que tornam possível fazer um projeto mo-

dular quer seja em OO quer em um estilo procedural.

Pior ainda, colocar muitos scriptlets na página torna fácil demais misturar as di-

ferentes camadas de uma aplicação corporativa. Quando a lógica do domínio começa a aparecer em páginas servidoras, torna-se muito difícil estruturá-la bem e muito
fácil duplicá-la em diferentes páginas servidoras. Tudo considerado, o pior código
que vi nos últimos anos foi o código de páginas servidoras.

Objeto Auxiliar A chave para evitar scriptlets é fornecer um objeto comum como um
auxiliar para cada página. Este auxiliar tem toda a lógica real de programação. A página só tem chamadas para ele, o que a simplifica e a torna uma Vista Padrão mais pu-

ra. A simplicidade resultante permite a não-programadores editarem a página e aos

programadores concentrarem-se no objeto auxiliar. Dependendo da ferramenta que

você estiver usando, você muitas vezes pode reduzir todos os padrões em uma pági-

na para etiquetas HTML/XML, o que mantém a página mais consistente e mais aces-

sível a suporte com ferramentas.

Isso soa como um princípio simples e recomendável, mas, como sempre, algu-

mas questões tornam as coisas mais complicadas. Os marcadores mais simples são

aqueles que obtêm informações do resto do sistema e as colocam no lugar correto na

CapituLo 14 e PADRÕES DE APRESENTAÇÃO WEB

335

página. Eles são facilmente traduzidos em chamadas para o objeto auxiliar que resul-

tam em texto (ou algo trivialmente transformado em texto), e o mecanismo coloca o
texto na página.

Exibição Condicional Uma questão mais complicada é o comportamento condicional da página. O caso mais simples é a situação em que algo é mostrado apenas se
uma condição for verdadeira. Poderia ser algum tipo de etiqueta condicional do tipo

<IF condição = "$quedaDePreço > 0.1">... mostrar alguma coisa </IF>. O problema é que,
quando você começa a ter etiquetas condicionais como essa, você começa a ir pelo caminho de transformar os padrões em uma linguagem de programação . Isso o leva

aos mesmos problemas com os quais se depara quando insere scriptleis na página. Se

você precisar mesmo de uma linguagem de programação, até poderia usar scriptlets,
mas você sabe o que eu penso dessa idéia!

Como consegiiência disso, vejo etiquetas puramente condicionais como indí-

cio de problemas, algo que você deveria tentar evitar. Você não pode evitá-las sempre, mas deve tentar alguma coisa mais focado do que uma etiqueta <IF> de propó-

sito geral.

Se você estiver mostrando algum texto condicionalmente, uma opção é mover

a condição para o objeto auxiliar. A página então irá sempre inserir o resultado da

chamada no objeto auxiliar. Se a condição não for verdadeira o objeto auxiliar irá enviar de volta uma string vazia, mas, desta forma, ele armazena toda a lógica. A abor-

dagem funciona melhor se não houver markups para o texto retornado ou se for suficiente retornar um markup vazio que é ignorado pelo navegador.

Isto não funciona se, digamos, você quiser realçar em uma lista itens que apre-

sentam boas vendagens colocando seus nomes em negrito. Em tal situação, você

neira de obter isso é fazer o objeto auxiliar gerar o markup. Isso mantém toda a lógica
fora da página, ao custo de retirar a escolha do mecanismo de destaque do projetista
da página e de dá-la ao código de programação.

Para manter a escolha do HTML nas mãos do projeto da página, você precisa de
algum tipo de etiqueta condicional. Entretanto, é importante olhar para além de um
simples <IF>. Um bom caminho a escolher é uma etiqueta focada, de modo que, em

vez de uma etiqueta que se parece com
<IF expressão = "estáVendendoBastante(

| "><B></IF>

<property name = "preço"/>
<IP expressão = "estáVendendoBastante( )"></B></IF>
você tem uma como

<destacar se condição = "estávendendoluito” style = "bold">
<property name = "preço"/>
</destacar>
Em ambos os casos é importante que a condição seja feita baseada em uma úni-

ca propriedade Boleana do objeto auxiliar. Colocar alguma expressão mais complexa
na página é na verdade colocar a lógica na própria página.

Outro exemplo seria colocar informações em uma página que dependa do local

no qual o sistema esteja sendo executado. Considere algum texto que só deva ser
mostrado nos Estados Unidos ou no Canadá, o qual, em vez de

or

sempre precisa dos nomes exibidos, mas, às vezes, quer 0 markup especial. Uma ma-

336

Parte Il e Os PaprOes

<IP expressão = "local = 'US'

|| 'CA' ">

. texto especial </IF>

Seria algo como
<local includes = "US, CA">...

texto especial

</local>

Iteração Iterar sobre uma coleção apresenta problemas semelhantes. Se você quiser

uma tabela em que cada linha corresponda a uma linha de item em um pedido, você

precisa de uma construção que permita a exibição fácil de informação para cada li-

nha. Aqui é difícil evitar uma iteração geral sobre uma etiqueta de coleção, mas isso
geralmente funciona com simplicidade suficiente para se ajustar bem.
É claro que os tipos de etiquetas com as quais você tem que trabalhar muitas vezes são limitados pelo ambiente no qual você está. Alguns ambientes lhe dão um
conjunto fixo de padrões, caso em que você pode estar mais limitado do que gostaria, seguindo estes tipos de diretrizes. Em outros ambientes, entretanto, você pode
ter mais escolha nas etiquetas a usar. Muitos deles permitem até que você defina suas

próprias bibliotecas de etiquetas.

Quando Processar O nome Vista Padrão salienta o fato de que a função primária deste
padrão é executar o papel de vista no padrão Modelo Vista Controlador (315). Para muitos sistemas a Vista Padrão deveria apenas executar o papel de vista. Em sistemas mais

simples, pode ser razoável que ela execute o papel do controle, e possivelmente até o

de modelo, embora eu fosse lutar para separar o processamento do modelo tanto
quanto possível. Onde a Vista Padrão tem outras responsabilidades além da vista, é ime
s

portante assegurar que essas responsabilidades são manipuladas pelo objeto auxiliar,
não pela página. Responsabilidades de modelo e de controle envolvem lógica de programação que, como qualquer lógica de programação, deveria ficar no objeto auxiliar.
Qualquer sistema padrão demanda um processamento extra pelo servidor Web.
Isso pode ser feito compilando-se a página após ela ser criada, compilando-a na sua

primeira solicitação ou interpretando-a a cada solicitação. Obviamente esta última
opção não é uma boa idéia, se a interpretação tomar algum tempo.

Uma coisa a observar com a Vista Padrão são as exceções. Se uma exceção che-

gar ao container Web, você pode se descobrir com uma página tratada pela metade,

que, em vez de um redirecionamento, fornece alguma saída estranha para o navega-

dor solicitante. Você precisa investigar como o seu servidor Web manipula exceções.

Se ele fizer algo estranho, capture você mesmo todas as exceções na classe auxiliar
(outra razão para desdenhar os scriptlets.)
Usando Scripts Embora páginas servidoras sejam uma das formas mais comuns de
Vista Padrão atualmente, você pode escrever scripts no estilo Vista Padrão. Tenho visto

uma quantidade razoável de Perl feita desta maneira. Mais notadamente demonstrada pelo CGL.
pm de Perl, o truque é evitar concatenar strings tendo chamadas de fun-

ções que tenham como saída os identificadores apropriados à resposta. Dessa forma,

você pode escrever o script na sua linguagem de programação e evitar a confusão de
misturar a impressão de strings com lógica de programação.

Quando Usá-la

Para implementar a vista no Modelo Vista Controlador (315), a escolha principal é en-

tre Vista Padrão e Vista de Transformação (343). A força da Vista Padrão é que ela permi-

CapiTuLo 14 e PADRÕES DE APRESENTAÇÃO Wes

337

te que você forme o conteúdo da página olhando a estrutura da mesma. Isso parece

ser mais fácil de fazer e aprender para a maioria das pessoas. Em especial, ela supor-

ta muito bem a idéia de um projetista gráfico compondo uma página com um pro-

gramador trabalhando no auxiliar.

A Vista Padrão tem duas fraquezas significativas. Primeiro, as implementações

comuns tornam fácil demais colocar lógica complicada na página, tornando-a assim
difícil de sofrer manutenção, especialmente por não-programadores. Você precisa
de muita disciplina para manter a página simples e orientada à exibição, colocando

a lógica no auxiliar. A segunda fraqueza é que a Vista Padrão é mais difícil de testar
do que a Vista de Transformação (343). A maioria das implementações de Vista Padrão
são projetadas para trabalhar dentro de um servidor Web e são muito difíceis ou impossíveis de testar de outra forma. Implementações de Vista de Transformação (343)
são muito mais fáceis de inserir em uma rotina de testes e testar sem rodar um servidor Web.

Ao pensar sobre uma vista, você também precisa considerar Vista em Duas Eta-

pas (347). Dependendo de seu esquema do padrão, você pode conseguir implemen-

tar este padrão usando identificadores especializados. Entretanto, você pode achar
mais fácil implementá-lo baseado em uma

Vista de Transformação (343). Se você vai

precisar de Vista em Duas Etapas (347), você precisará levar isso em consideração na

sua escolha.

Exemplo: Usando uma JSP como uma Vista
com um Controle Separado (Java)
Quando se usa uma JSP como uma vista apenas, ela é sempre chamada a partir de

um controle em vez de diretamente do container de servlet. Assim, é importante pas-

sar para a JSP qualquer informação de que ela vá precisar para descobrir o que exi-

bir. Uma boa maneira de fazer isso é fazer o controlador criar um objeto auxiliar e

passá-lo para a JSP usando a solicitação HTTP. Mostraremos isso com o exemplo de
exibição simples do Controlador de Página (318). O método de manipulação Web para

o servlet parece com o seguinte:
class ControladorDeArtista...

public void executarGet (HttpServletReguest solicitação, HttpServletResponse resposta)
throws IOException, ServletException {

Artista artista = Artista.buscarPeloNone (solicitagao.getParameter ("nome") ) ;
if

(artista == null)
forward("/ErroArtistaNãoEncontrado.jsp",

else {

solicitação,

resposta);

solicitação. setAttribute("auxiliar", new AuxiliarDeArtista
forward ("/artista.jsp", solicitação, resposta);

(artista));

Quando se considera a Vista Padrão, o comportamento importante é criar o au-

xiliar e colocá-lo na solicitação. A página servidora pode agora alcançar o auxiliar

com o identificador useBean.

<jsp:useBean id="auxiliar” type="actionController.auxiliarDekrtista" scope="solicitação?/>

338

Parte ll e Os ParõEs

Com o auxiliar no lugar, podemos usá-lo para acessar as informações que pre-

cisamos exibir. As informações do modelo de que o auxiliar precisa foram passadas
para ele quando foi criado.
class AuxiliarDeArtista...

private Artista artista;

private AuxiliarDeartista
this.artista

(Artista artista)

(

= artista;

Podemos usar o auxiliar para obter informações apropriadas sobre o modelo.
No caso mais simples, fornecemos um método para obter dados simples, como o no-

me do artista.

class AuxiliarDeartista...
public String lerNome( ) {

)

return artista. lerNome( );

Então acessamos esta informação por meio de uma expressão Java.
<B> <t=auxiliar.lerNome( )%></B>

ou uma propriedade

<B><jsp: lerPropriedade name="auxiliar" property="none" .></B>

8

A escolha entre propriedades ou expressões depende de quem está editando a

JSP. Os programadores acham as expressões fáceis de ler e mais compactas, mas edi-

tores HTML podem não ser capazes de manipulá-las. Não-programadores irão pro-

vavelmente preferir identificadores, já que eles se adaptam à forma geral de HTML e
deixam menos espaço para erros que confundem.
Usar um auxiliar é um modo de remover código scriptlet complicado. Se você

quiser mostrar uma lista de álbuns de um artista, você precisa executar um laço, o

que você pode fazer com um scriptlet na página servidora.

Ub
&

for (Iterator it = auxiliar. lerÁlbuns( ) iterator( ); it.hasNext( );) {
Album álbum = (Álbum) it.next( ) ;%>

<LI><$=4lbun. lerTitulo( }$></LI>

a)»
</UL>

Francamente, esta mistura de Java e HTML é realmente horrível de ler. Uma al-

ternativa é mover o laço do for para o auxiliar.
class AuxiliarDeArtista...

public String lerListaDeAlbuns( ) (
StringBuffer resultado = new StringBuffer( );
resultado,
append ("<UL>") ;

CapiTuLo 14 e PADRÕES DE APRESENTAÇÃO Wee

339

for (Iterator it = lerAlbuns( ) iterator( |; it.hasiext( );) {
Álbum álbum = (Álbum) it.next( );
append ("<LI>");
resultad
o.

result
ado.
append (álbum.
lerTitulo( ));
resultado.append ("</LI>") ;

resultado. append("</UL>") ;
return resultado. tostring( };

}
public List lerAlbuns( ) {

return artista. lerAlbuns( |;

)

Considero isso mais fácil de acompanhar porque a quantidade de HTML é bem

pequena. Isso também permite que você use uma propriedade para ler a lista. Mui-

tas pessoas não gostam de colocar código HTML em auxiliares. Embora eu prefira

não fazê-lo, havendo a escolha entre HTML e scriptlets, eu escolho o primeiro em au-

xiliares em qualquer momento.
O melhor caminho a seguir é um identificador especializado para iteração.

<Ul>etag: forgach host = "auxiliar" collection = "Albuns" id = "cada">
<LI><jsp:lerPropriedade name="cada" property = "título"/></LI>
</tag: forBach></UL>

Esta é uma alternativa muito melhor, pois mantém os scriptlets fora da JSP e

HTML fora do auxiliar.

Exemplo: Página Servidora ASP.NET (C#)
Este exemplo continua o que comecei no Controlador de Página (318) (página 325). Re-

cordando para você, ele mostra os pontos feitos por rebatedores em um único perío-

do de uma partida de críquete. Para aqueles que pensam que críquete (cricket, grilo)
é um inseto pequeno e barulhento, omitirei as longas rapsódias sobre o esporte mais

imortal do mundo e resumirei tudo no fato de que a página exibe três informações
essenciais:

* Um número de ID para referenciar a partida.
* Os pontos de quais equipes estão mostrados e de quais períodos eles são.
*

Uma

tabela mostrando o nome de cada rebatedor e a média de pontos (o

número de bolas que ele recebeu dividido pelo número de pontos que ele
marcou).

Se você não entende o que estas estatísticas significam, não se preocupe. O críquete é cheio de estatísticas — talvez sua maior contribuição à humanidade seja for-

necer estatísticas estranhas para artigos excêntricos.

A discussão sobre o Controlador de Página (318) abordou como uma solicitação
Web é manipulada. Para resumir, o objeto que atua como o controlador e como a vis-

ta é a página ASP.NET aspx. Para manter o código do controle fora de um scriptlet,

você define um código separado atrás da classe.

340

Parte ll e Os Parões

<%e Page language = "CH" Codebehind="bat .aspx.cs" AutoBventHireup="false! trace="False"
inherits="rebatedores. PáginaDeRebatidas" $>
A página pode acessar os métodos e propriedades do código atrás da classe diretamente. Além disso, esse código pode definir um método Carregar Página para manipular a solicitação. Neste caso, defini Carregar Página como um método padrão
[Gang of Four] em uma Camada Supertipo (444).

class PéginaDeCriquete...
protected void Carregar Página (Object sender, System.EventArgs e) (

db = new OleDbConnection
(DB. ConnectionString) ;
if (háParâmetrosFaltando( ))
erroDeTransferência (mensagenDePaltaDeParâmetros) ;
DataSet ds = lerDados( );

if (nãoHáDados (ds) )
erroDeTransferência ("Nenhum dado corresponde à sua solicitação") ;
aplicarLógicaDoDonínio (ds) ;
LigarDados( );
PrepararInterfaceDeUsuário
(ds) ;

Para os propósitos da Vista Padrão, eu posso ignorar tudo menos as duas últi-

mas linhas da carga da página. A chamada a LigarDados permite que diversas variá-

veis de página sejam ligadas apropriadamente a suas fontes de dados corresponden-

tes. Isso será suficiente nos casos mais simples, mas para os mais complicados a últi8

ma linha chama um método no código separado da página específica para preparar

quaisquer objetos para seu uso.

O número de ID, equipe e período do jogo são valores únicos para a página, to-

dos eles vindos para a página como parâmetros na solicitação HTTP. Posso fornecer

esses valores usando propriedades no código atrás da classe
class PáginaDeRebatidas
protected String equipe (
get {return Request.Params ["equipe"];)

}

protected String jogo {

}

get {return Request .Parans ["jogo"l ;}

protected String período (

}

get {return Request. Params ["periodo"] ;}

protected String perfdodrdinal (

}

get {return (período == "1") 2 "Le": "2°";}

Com as propriedades definidas, posso usá-las no texto da página.

<P>

Id do Jogo:

<asp:label id = "rótuloJogo" Text="<t# jogo $>" runat="server" font-bold="Truet>
</asp: label >inbsp;

