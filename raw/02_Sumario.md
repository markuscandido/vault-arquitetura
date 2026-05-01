# 02_Sumario

Sumário

Introdução:

oras

wesintioee

SR

URL cee nee

Hes

RO

ERR

RO

Arquitetura

Aplicações Corporativas .

Tipos de Aplicações Corporativas .
Pensando em Desempenho .
Padrões .
:
A Estrutura dos Padrós
Limitações Destes Padrões

PARTE

AS INARRATIVAS. +50 eco ato ão jiu

Carítuo 1

Criando Camadas........

o it) ng ots aes ato teta a
0.6.6. e cece

A Evolução das Camadas nas Aplicações Corporativas . .

cena

nsta a

cece cence eens

As Três Camadas Principais

Escolhendo Onde Rodar suas Camadas.

Caríruio 2

Organizando a Lógica do Domínio. .

Fazendo uma Escolha

E

8

E

Camada de Serviço ... 6.2.0. e cece cece cece eee ee eect eee e aeee
CapituLo 3
Mapeando para Bancos de Dados Relacionais
Padrões de Arquitetura ...

O Problema Comportamental
Lendo Dados

Padrões de Mapeamento Estrutural...
Mapeando Relacionamentos

S65

.

a

Herança

Construindo o Mapeamento .

Mapeamento Duplo.

Usando Metadados...
Conexões de Bancos de Dados

g
3
ê
E
BES
1.2.1... c eet e teeter eet teeters

16

Sumário

Questões Diversas .

Leitura Adicional . .

Carítuio 4
Apresentação Web..........0
6... cece
eee eee
Padrões de Vista
Padrões de Controlador de Entrada

Leitura Adicional
Capituo 5

Concorrência (por Martin Fowler e David Rice) ...........000 5 78

Problemas de Concorrência.
Contextos de Execução . .

Isolamento e Imutabilidade.
ê
Controles de Concorrência Otimista e Pessimista
Evitando Leituras Inconsistentes . . .

Deadlocks.

Transações .
ACID...

a

Recursos Transacionais.
Reduzindo o Isolamento Transacional para Aumentar a Vivacidade. .
Transações de Negócio e de Sistema
Padrões para o Controle de Concorrência Offline

Concorrência em Servidores de Aplicação.
Leitura Adicional

Capitulo 6

Estado da Sessão

O Valor de Não Possuir Estado
Estado da Sessão

.93
...

+93
295

Modos de Armazenar o Estado da Sessão.

Capitulo 7
O

Estratégias de Distribuição ..... 1... 6.

Fascínio dos Objetos Distribuídos

Interfaces Locais e Remotas.

eee eee

.. .

Onde Você tem que Distribuir
Trabalhando com as Fronteiras da Distribuição

Interfaces para Distribuição.

Carítuo 8

.95

.

Juntando Tudo .

Começando com a Camada de Domi
Descendo para a Camada de Dados

Camada de Dados para o Roteiro de Transação (120)
Camada de Dados para o Módulo Tabela (134)
Camada de Dados para o Modelo de Domínio (126)

A Camada de Apresentação

Alguns Conselhos Específicos para Determinadas Tecnologias .
Java e J2EE.
NET

cece

o

Procedimentos Armazenados .

Serviços Web ..........

Outros Esquemas de Camadas

PARTE I
Carítuio

.

(OS: PADROES vcccqenscsencerien sus) sie] dio ase cis cn the Hee its io ia a ao red 117
9
Padrões de Lógica de Dominio.............ese
eee e eens A19

Roteiro de Transação (Transaction Script) .
Como Funciona

.120

- 120

Sumário

17

Quando Usá-lo

“121

Exemplo: Lançamento de Receitas (Java) .

.122

O Problema do Lançamento de Receitas

122

Modelo de Domínio (Domain Model) . .

Como Funciona

Quando usá-lo

. 126

- 126

«129.

...

Leitura Adicional .

Exemplo: Lançamento de Receita (lava)

Módulo Tabela (Table Module) .

- 129

.

129

- 134

Como Funciona

- 134

. 137

Quando Usa-lo

Exemplo: Lançamento de Recelias iacom um Módulo Tabela (A).
Camada de Servico (Service Layer) (por Randy Stafford)
Como

Funciona

Quando Usá-la

...

...

a

Leitura Adicional .

137
.141

A

a

14
5

144

++ 145

Exemplo: Lancamento de Receitas (ava) ....cc.ccacsessvecveciveessvuns 145
CapituLo

10

Padrões Arquiteturais de Fontes de Dados (Data Source)

Gateway de Tabela de Dados (Table Data Gateway) .
Como Funciona ...
:

Quando Usá-lo

151
151

.152

...

Leitura Adicional .

153

Exemplo: O Gateway Pessoa (C#) .
Exemplo: Usando Conjuntos de Dados ADO.NET (c% .
Gateway de Linha de Dados (Row Data Gateway)
à
Como Funciona

ASS
.155
.158
.158

Quando Usá-lo

Exemplo: Um Registro Pessoa (Java)
Exemplo: Um Armazenador de Dados para um Objeto de Dominio (Java)

Registro Ativo (Active Record) .
Como Funciona.

Caríruio

11

.

175

a ie
. 180

- 183

Padrões Comportamentais Objeto-Relacionais........... 186

Unidade de Trabalho (Unit of Work) . . .
Como

. 165

«170

Exemplo: Um Mapeador Simples de Banco de Dados (Java)
Exemplo: Separando os Métodos de Busca (Java) .

Exemplo: Criando um Objeto Vazio (Java)

.161
. 164

.166
.167
.170

Como Funciona .

...

159.

.165

Quando Usá-lo ...
Exemplo: Uma Pessoa Simples (Java) .
Mapeador de Dados (Data Mapper)
Quando Usá-lo

.. 150

- 187

Funciona

Quando Usá-la

Exemplo: Unidade de Trabalho com Registro de one
Mapa de Identidade fidenty Map)
Como Funciona .

Quando Usá-lo

...

Exemplo: Métodos para um Mapa de Identidade ava)

Carga Tardia (Lazy Load) .
Como Funciona
Quando Usá-la

Exemplo: Inicialização Tardia (Java)

(Java) (eer David Rice). .
A

18

Sumário

Exemplo: Proxy Virtual (Java)

Exemplo: Usando um Armazenador de Valor (Java) .

:

.203

. 205

Exemplo: Usando Fantasmas (C#) . 20... 0-c0eeeceeeeeeeeeeeeeeeeeees 206
Caríruio

12

Padrões Estruturais Objeto-Relacionais .

Campo Identidade (Identity Field)
Como

Funciona

Quando Usá-lo

...

....

...

Leitura Adicional .
Exemplo: Chave Integral (C#) .
Exemplo: Usando uma Tabela de Chaves (Java) .
Exemplo: Usando uma Chave Composta (Java).

Mapeamento de Chave seston
Como Funciona.

Quando Usá-lo

Goran Key Mapping)

.

a

..

Exemplo: Referência Univalorada (ava) .

Exemplo: Busca Multitabelas (Java).

Exemplo: Coleção de Referências (C#)

Mapeamento de Tabela Associativa (Association Table Mapping)
Como Funciona ...

Quando Usá-lo

...

Exemplo: Empregados e Habilidades (C#)
Exemplo: Usando SQL Direto (Java)

Exemplo: Usando uma Única Consulta para Vários Empregados (lava)

(por Matt Foemmel e Martin Fowler)
Mapeamento Dependente (Dependent Mapping)
Como Funciona ...
:

.

8
Quando Usá-lo ...
Exemplo: Álbuns e Faixas (Java) ...
Valor Embutido (Embedded Value) ...
Como Funciona ...
Quando Usá-lo

...

Leitura Adicional
Exemplo: Objeto Valor Simples (Java) .

LOB Serializado (Serialized LOB)
Como

Funciona.

Quando Usá-lo ...

Exemplo: Serializando uma Hierarquia de Departamentos em XML (Java).

Herança de Tabela Única (Single Table Inheritance) .

Como Funciona .

Quando Usá-la

...

Exemplo: Uma Tabela Única para Jogadores (C#)
Carregando um Objeto do Banco de Dados

Herança de Tabela de Classes (Class Table Inheritance)
Como

Funciona

Quando Usá-la

...

...

Leitura Adicional .
Exemplo: Jogadores e Assemelhados (ca)

Herança de Tabela Concreta (Concrete Table Inheritance)
Como

Funciona

Quando Usá-la

Exemplo: Jogadores Concretos (C#)

Mapeadores de Herança (Inheritance Mappers)
Como Funciona ...
E

Quando Usá-los

.

.

Sumário

Caríruio

13

Padrões de Mapeamento em Metadados

Objeto-RelacionaiS ya

«junio

sisters

eso

Mapeamento em Metadados (Metadata Mapping)
Como Funciona.
a
Quando Usá-lo ...

Exemplo: Usando Metadados e Reflexão (ava)
Objeto de Pesquisa (Query Object) .

e e ra

nina

.

Leitura Adicional

.

.297
.304

.304

.305

...

. 306

Exemplo: Um Objeto de Pesquisa Simples (Java) ...

Repositório (Repository) (por Edward Hieatt e Rob Mee)

Como Funciona ...
Quando Usá-lo ...
Leitura Adicional .

e

.306

E

Exemplo: Encontrando os Dependentes de uma Pessoa (Java)

Exemplo: Trocando Estratégias de Repositório (Java)

Capítulo

14

asters 294
.295
.295
«297

Como Funciona ...

Quando Usá-lo

19

.309

.310
31
312
«312

.312

Padrões de Apresentação Web

Modelo Vista Controlador (Model View Controller) .
Como

Funciona

Quando Usá-lo

Controlador de Página (Page Controller)
Como

Funciona

.

Quando Usá-lo

Exemplo: Apresentação Simples com um Controlador Servlet
e uma Vista JSP (Java) .

.320

Exemplo: Manipulador de Página com um Código Por Trás (c&) E

«325:

Exemplo: Usando uma JSP como Manipulador

(Java).

Controlador Frontal (Front Controller) .
Como Funciona ...
Quando Usá-lo ...
Leitura Adicional

.330

Vista Padrão (Template View)

.333

Como Funciona .
Quando Usá-la ...

Exemplo: Usando uma JSP como uma Vista com um | Controle

Exemplo: Página Servidora ASP.NET (C#)
Vista de Transformação (Transform View)

.

Como Funciona .
Quando Usá-la

...

Exemplo: Transformação Simples (ava) .

Vista em Duas Etapas (Two Step View) .
Como Funciona

Quando Usá-la
Exemplo: XSLT em Duas Etapas (XSLT)
Exemplo: JSP e Identificadores Customizados (ava) |

Controlador de Aplicação (Application Controller).
Como Funciona ...

Quando Usá-lo

Leitura Adicional

...

.328

.328
.330
.330

Exemplo: Exibição Simples (Java) . .

Separado (Java)

.322

E

Exemplo: Controlador de Aplicação Modelo de Estados (Java)

.333
.336
.337

.339
.343

.343

.344

.344

.347

.347
390
.353
3355

.360

. 360

.362
.362

.362

20

Sumário

Carítuo

15

Padrões de Distribuição ..........
6. eee eee eee eens 367

Fachada Remota (Remote Facade)

Como Funciona .
Quando Usá-la

.

. 368

.368

...

Exemplo: Usando um Session Bean Java como Fachada Remota (ava) .
Exemplo: Serviço Web (C#)

Objeto de Transferência de Dados (Data Transfer Object)
Como Funciona

Quando Usá-lo

.

...

...

Leitura Adicional .

. 380

.383

.

.385

.385

Exemplo: Serializando Usando XML (Java)

16

.372
325
.380

Exemplo: Transferindo Informações Sobre Albuns (Java) .
Capítulo

5871

.389

Padrões de Concorrência Offline ........
cece eee eee 391

Bloqueio Offline Otimista (Optimistic Offline Lock) (por David Rice) .
Como Funciona .

Quando Usá-lo ...

.

“

:

2392:
393

- 396

Exemplo: Camada de Domínio com Mapeadores de Dados (170) (ava)

- 396

Como Funciona

. 402

Bloqueio Offline Pessimista (Pessimistic Offline Lock) (por David Rice)

Quando Usá-lo

...

...

Exemplo: Gerenciador de Bloqueios. Simples (Java)
Bloqueio de Granularidade Alta (Coarse-Grained Lock)

. 401

. 405

É

. 406

(por David Rice e Matt: Foemme)) sev sisi ieee x saswesuga ra eterna
cr 412
Como Funciona .
.412
Ms
Quando Usá-lo ...

Exemplo: Bloqueio Offline Otimista (392) Compartilhado (Java) .
Exemplo: Bloqueio Offline Pessimista (401) Compartilhado (Java)
Exemplo: Bloqueio Offline Otimista (392) de Raiz (Java)

Bloqueio Implicito (Implicit Lock) (por David Rice)

.

Como Funciona .
Quando Usá-lo ...

Exemplo: Bloqueio Offline Pessimista (401) implícito (Java)

Capitulo

17

Padrões de Estado de Sessão

Estado da Sessão no Cliente (Client Session State)

Como Funciona .

Quando Usá-lo ...
Estado da Sessão no Servidor (Server Session State)
Como Funciona .

Quando Usá-lo ...

Estado da Sessão no Banco de Dados (Database session State) .

Como Funciona ...

Quando Usá-lo

CarituLo

18

Gateway

Como

...

Padrões Básicos
Funciona

...

Quando Usá-lo
Exemplo: Um Gateway para um Serviço de Mensagens Proprietário (Java) .

Mapeador (Mapper) . .
Como

Funciona

Quando Usá-lo

...

...

k

415
.420

- 420

.422

.422
. 424
. 424

Sumário

Camada Supertipo (Layer Supertype) . .
Como Funciona .

.

...

Quando Usá-la

Exemplo: Objeto do Domínio (ava)
Interface Separada (Separated Interface) .

Implementação da Unidade de Trabalho

Como

Funciona

...

Quando Usá-la

a

:

21

.. 444
+444

. 444

«444
..445

.

.445

...

Registro (Registry) ....
Como

Funciona .. .

Quando Usá-lo ...

Exemplo: Um Registro Singleton (ava)

Exemplo: Registro à Prova de Thread (Java) (por Matt Foemmel e

Martin Fowler) .

Objeto Valor (Value Object)
Como

Funciona

Quando Usá-lo

Dinheiro (Money)
Como

Funciona

...

. 452

.

.453

.453

.454

....

Quando Usá-lo

.455

...

.455

457

Exemplo: Uma Classe Dinheiro (Java) (por Matt Foemmel e Martin Fowler)...

.. 457

Como Funciona ...

. 462

Caso Especial (Special Case)
Quando Usa-lo

. 463

...

.463
.463

Leitura Adicional .
Exemplo: Um Objeto Nulo Simples (C8)

Plugin (por David Rice e Matt Foemmel)
Como Funciona

. 465
. 465

Quando Usá-lo

. 466

Exemplo: Um Gerador de Identidades (Java)

. 466

Stub de Serviço (Service Stub) (por David Rice).
Como Funciona.

Quando Usá-lo

=

...

. 469
. 469

470

a

Exemplo: Serviço tie Impostos Sobre Vendas (aval .

.470

Conjunto de Registros (Record Set) ...
Como Funciona
Quando Usá-lo

Referências ss essas ener

ie Aaa

.462

nie asrho ni

eta

o

Meare

