# Índice da Wiki

Catálogo de todas as páginas de conhecimento.

## Fontes
- [[Livro_PEAA]]: Patterns of Enterprise Application Architecture (Martin Fowler).

## Entidades
*Nenhuma entidade definida ainda.*

## Conceitos
- [[Aplicacoes_Corporativas]]: Características e desafios de sistemas corporativos.
- [[Arquitetura]]: Definição subjetiva e foco no desenvolvimento de software.
- [[Camadas]]: Técnica fundamental de decomposição.
    - [[Camada_de_Apresentacao]]
    - [[Camada_de_Dominio]]
    - [[Camada_de_Fonte_de_Dados]]
- [[Apresentacao_Separada]]: Princípio de manter a UI independente da lógica.
- [[Concorrencia]]: Gerenciamento de acesso simultâneo a dados.

## Padrões
### Apresentação
- [[MVC]]: Separação fundamental de UI e Lógica.
- [[Page_Controller]]: Um objeto manipulando requisições para uma página específica.
- [[Template_View]]: Renderização de HTML com marcadores dinâmicos.

### Lógica de Domínio
- [[Transaction_Script]]: Organização procedural da lógica.
- [[Domain_Model]]: Organização da lógica orientada a objetos.
- [[Table_Module]]: Organização da lógica orientada a tabelas.
- [[Camada_de_Servico]]: Limite da aplicação e coordenação.

### Mapeamento de Fonte de Dados
- [[Table_Data_Gateway]]: Um objeto por tabela para todos os registros.
- [[Row_Data_Gateway]]: Um objeto por registro de banco de dados.
- [[Active_Record]]: Objeto de registro que inclui lógica de negócio.
- [[Data_Mapper]]: Camada independente para mapeamento objeto-relacional.

### Comportamento e Metadados Objeto-Relacional
- [[Unit_of_Work]]: Gerenciamento de transações de banco de dados.
- [[Identity_Map]]: Garantia de unicidade de objetos.
- [[Lazy_Load]]: Carregamento de dados sob demanda.
- [[Campo_de_Identidade]]: Mapeamento de chaves primárias para objetos.
- [[RecordSet]]: Estrutura de dados em memória representando tabelas.

### Concorrência e Infraestrutura
- [[Bloqueio_Offline_Otimista]]: Detecção de conflitos baseada em versão.
- [[Bloqueio_Offline_Pessimista]]: Prevenção de conflitos baseada em bloqueio.
- [[Estado_da_Sessao]]: Gerenciamento de estado de interação do usuário.
- [[Estrategias_de_Distribuicao]]: Por que (não) distribuir objetos.
- [[Fachada_Remota]]: Interface de granularidade grossa para comunicação remota.

### Padrões Básicos
- [[Registry]]: Objeto bem conhecido para encontrar serviços.
- [[Value_Object]]: Objeto cuja igualdade é baseada em valor, não identidade.
- [[Caso_Especial]]: Subclasse para lidar com comportamentos específicos (ex: Null Object).

## Estratégia e Síntese
- [[Sintese]]: Diretrizes para escolher os padrões adequados.
