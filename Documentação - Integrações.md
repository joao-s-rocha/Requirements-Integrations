# Documentação de Requisitos - Integrações Fiscais

---

- [Documentação de Requisitos - Integrações Fiscais](#documentação-de-requisitos---integrações-fiscais)
- [Introdução](#introdução)
- [Roadmap](#roadmap)
  - [Recursos Globais](#recursos-globais)
  - [Integrador Fiscal iMendes](#integrador-fiscal-imendes)
  - [Integrador Fiscal FGF](#integrador-fiscal-fgf)
  - [Integrador Fiscal Mix Fiscal](#integrador-fiscal-mix-fiscal)
- [Requisitos Globais](#requisitos-globais)
  - [Cadastro de Empresas](#cadastro-de-empresas)
  - [Parâmetros do Sistema - Autenticação e Regras de Negócio](#parâmetros-do-sistema---autenticação-e-regras-de-negócio)
    - [Parâmetros iMendes](#parâmetros-imendes)
    - [Parâmetros FGF](#parâmetros-fgf)
    - [Parâmetros Mix Fiscal](#parâmetros-mix-fiscal)
  - [Cadastro de Finalidade de Operações](#cadastro-de-finalidade-de-operações)
  - [Cadastro de Perfil Fiscal](#cadastro-de-perfil-fiscal)
  - [Cadastro de Produtos](#cadastro-de-produtos)
    - [Funções](#funções)
    - [Novos Campos](#novos-campos)
  - [Nova Tela - Gerenciador Tributário](#nova-tela---gerenciador-tributário)
- [Requisitos da Integração iMendes](#requisitos-da-integração-imendes)
  - [Método de Consulta Básico](#método-de-consulta-básico)
  - [Método de Consulta Avançado](#método-de-consulta-avançado)
  - [Captura e Armazenamento dos Dados](#captura-e-armazenamento-dos-dados)
  - [Recursos Adicionais](#recursos-adicionais)
  - [Regra Fiscal de Entrada e Saída x iMendes](#regra-fiscal-de-entrada-e-saída-x-imendes)
- [Requisitos da Integração FGF](#requisitos-da-integração-fgf)
- [Requisitos da Integração Mix Fiscal](#requisitos-da-integração-mix-fiscal)
- [Requisitos da Regra Fiscal de Saída Ganso](#requisitos-da-regra-fiscal-de-saída-ganso)
- [Requisitos de Segurança](#requisitos-de-segurança)
  - [Acessos Restritos](#acessos-restritos)
  - [Logs](#logs)
- [Requisitos de Homologação](#requisitos-de-homologação)
  - [Checklist do MVP iMendes](#checklist-do-mvp-imendes)
  - [Checklist do MVP FGF](#checklist-do-mvp-fgf)
  - [Checklist do MVP Mix Fiscal](#checklist-do-mvp-mix-fiscal)
- [Simulações](#simulações)

---

# Introdução

- O presente documento objetiva descrever em detalhes os processos e meios para Integrações Fiscais de Consulta Tributária dos parceiros **iMendes, FGF e Mix Fiscal** no Sistema Ganso.
- No Modelo de Integração escolhido, o Sistema Ganso comunica-se com o portal tributário do Parceiro Integrador através de uma **API**, e realiza a consulta da Tributação dos produtos obtendo os dados para os mesmos.
- A Consulta ocorre de modos distintos conforme o Parceiro Integrador:
  - **Parceiro iMendes**: Permite realizar consulta durante a realização de um **novo cadastro**, consultar a tributação de um ou mais **produto(s) cadastrado(s)** ou **enviar todos os Produtos** para revisão geral. Durante a consulta a resposta da API é imediata, contudo, ao enviar o cadastro completo para revisão, produtos que não possuírem classificação na base iMendes serão classificados e devolvidos em até 45 dias. O usuário pode acatar ou não as alterações através do próprio Sistema Ganso.
  - **Parceiro FGF**: Permite realizar a consulta de um produto cadastrado ou enviar todos os produtos para revisão geral. A resposta da API não é imediata, e o usuário precisa realizar o aceite no Portal Virtual do Integrador para que o Sistema possa consultar as correções tributárias.
  - **Parceiro Mix Fiscal**: Realiza a revisão geral do cadastros através de Cenários Fiscais. Inicialmente, a resposta da API é imediata, e o usuário pode acatar ou não as alterações através do próprio Sistema Ganso.
- O Integrador **FGF** disponibiliza apenas Informações para a Saída a Consumidor Final, contendo dados para complementação do cadastro com informações de Entrada.
- O Integrador **iMendes** retorna dados suficientes para permitir a criação de Regras Fiscais de Entrada e Saída completas no Sistema Ganso.
- Para a concretização da Integração, todos os parceiros dispoem de Métodos de Homologação obrigatórios, cujas regras são descritas nesta documentação na seção [Requisitos de Homologação](#requisitos-de-homologação)

# Roadmap

O **Roadmap** é um guia que indicará o caminho em uma ordem lógica para a implementação dos Recursos Necessários descritos neste documento. Cada Integrador Fiscal possui uma _rota_ específica que deve ser obedecida para que os objetivos sejam atingidos, contudo, os **Recursos Globais** devem ser implementados antes de cada Integrador.

## Recursos Globais

1. Etapa 1 - Preparação do Sistema Ganso. Implementar os Requisitos descritos em [Requisitos Globais](#recursos-globais)

## Integrador Fiscal iMendes

1.  Etapa 1 - Implementação do Método de Consulta Básico descrito em [Método de Consulta Básico iMendes](#método-de-consulta-básico)
2.  Etapa 2 - Implementação do Método de Consulta Avançado descrito em [Método de Consulta Avançado iMendes](#método-de-consulta-avançado)
3.  Etapa 3 - Validação das Implementações de acordo com o Checklist descrito em [Checklist MVP iMendes](#checklist-do-mvp-imendes)
4.  Etapa 4 - Implementações de Recursos Adicionais agregados à Regra Fiscal de Entrada e Saída do Sistema Ganso descritas em [Regra Fiscal de Entrada e Saída](#regra-fiscal-de-entrada-e-saída-x-imendes)

- **Roadmap ilustrado**

  ![Roadmap Integração iMendes](./roadmap-imendes.png)

## Integrador Fiscal FGF

## Integrador Fiscal Mix Fiscal

[Voltar ao Sumário](#documentação-de-requisitos---integrações-fiscais) | [Voltar ao Roadmap](#roadmap)

# Requisitos Globais

Nesta Seção, são descritos os **Requisitos Globais** obrigatórios e comuns a todas as Integrações desta documentação. Conforme definido no [Roadmap](#recursos-globais) é necessário adequar determinadas rotinas do Sistema Ganso para permitir integração fiscal, que requer modificações em Cadastros e Inclusão de Recursos específicos.

## Cadastro de Empresas

Os Integradores **iMendes** e **Mix Fiscal** requerem que o cadastro de Empresas contenha novos campos para agregar informações que são utilizadas durante uma requisição à API, que são:

| Nome              | Descritivo                                                                                                                                                                                                              | Validações                                                                                                                                                                                          | Obrigatório |
| :---------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------: |
| Regime Tributário | Campo para informar a Subclassificação do CRT "Regime Normal", cuja informação pode ser definida entre "**Lucro Real - LR**" ou "**Lucro Presumido - LP**". Este dado é obrigatório para a obtenção de Regras corretas. | Deve ser permitido informá-lo apenas se o CRT selecionado for igual a "3 - Regime Normal".                                                                                                          |   **Sim**   |
| Área da Loja      | Campo para informar o tamanho da área fisica ocupada pelo estabelecimento do cliente em Metros Quadrados                                                                                                                | Informação importante para o Integrador Mix Fiscal, que oferece para o Cliente Integrado insights de organização mercadológica de espaços, proximidade de produtos e composição de mix de produtos. |   **Não**   |

[Voltar ao Sumário](#documentação-de-requisitos---integrações-fiscais) | [Voltar ao Roadmap](#roadmap)

## Parâmetros do Sistema - Autenticação e Regras de Negócio

- Para organizar os Integradores Fiscais de modo claro e objetivo, em **Parâmetros Gerais do Sistema** criar uma Aba "**Integrações Fiscais**" que deve conter uma "**Sub-aba**" para cada Integrador contendo as respectivas configurações individuais.

### Parâmetros iMendes

| Tipo de Elemento          | Pai                 | Nome/Texto                                                                                                         | Descritivo                                                                                                                                                                                                                                                                                                 | Validações                                                                                                                                                                                                                                                                                                                                  | Obrigatório |
| :------------------------ | :------------------ | :----------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :---------: |
| **Caixa de Seleção**      | Sub-aba iMendes     | Ativar Integração de Consulta Tributária iMendes                                                                   | Parâmetro para Ativação das Configurações de Integração e funcionalidade no Sistema Ganso                                                                                                                                                                                                                  | Não permitir ativar se houver outra Integração Fiscal Ativada no Sistema Ganso                                                                                                                                                                                                                                                              |   **Sim**   |
| **Grupo**                 | Sub-aba iMendes     | **Autenticação**                                                                                                   | Organiza os campos de Configurações para conectividade com os Servidores iMendes                                                                                                                                                                                                                           | Ativar apenas se o Parâmetro Ativar Integração estiver selecionado                                                                                                                                                                                                                                                                          |   **Sim**   |
| **Campo Texto**           | Grupo Autenticação  | URL de Saneamento (Consulta Tributação)                                                                            | Campo para informar a URL da API que retorna Dados da Tributação do Produto consultado.                                                                                                                                                                                                                    | Permitir até 255 caracteres.                                                                                                                                                                                                                                                                                                                |   **Sim**   |
| **Campo Texto**           | Grupo Autenticação  | URL Envia e Recebe Dados (Outros Métodos)                                                                          | Campo para informar a URL da API utilizada para os Recursos Adicionais do iMendes descritos em [Recursos Adicionais iMendes](#recursos-adicionais)                                                                                                                                                         | Permitir até 255 caracteres.                                                                                                                                                                                                                                                                                                                |   **Sim**   |
| **Campo Texto Mascarado** | Grupo Autenticação  | Senha                                                                                                              | Campo para informar a Senha do Cliente Integrado.                                                                                                                                                                                                                                                          | Permitir até 30 caracteres alfanuméricos e símbolos                                                                                                                                                                                                                                                                                         |   **Sim**   |
| **Caixa de Combinação**   | Grupo Autenticação  | Versão da API                                                                                                      | Seleção da Versão da API contratada. Disponibilizar as versões 2.0 e 3.0 por padrão                                                                                                                                                                                                                        | Permitir selecionar apenas uma das versões                                                                                                                                                                                                                                                                                                  |   **Sim**   |
| **Campo de Texto**        | Grupo Autenticação  | Tempo de Resposta                                                                                                  | Timeout ou Tempo de Resposta máximo da API. Tempo máximo que o Sistema deve aguardar para obter a resposta antes de efetuar uma nova requisição.                                                                                                                                                           | Campo Numérico, interpretado em segundos, com valor padrão definido em 15 segundos.                                                                                                                                                                                                                                                         |   **Sim**   |
| **Botão de Ação**         | Grupo Autenticação  | Verificar Status                                                                                                   | Botão para acionar um comando de teste de conectividade com as APIs utilizando os dados de Autenticação e URLs informadas.                                                                                                                                                                                 | Deve retornar uma Mensagem amigável de Sucesso ou Falha, informando quais APIs foram testadas.                                                                                                                                                                                                                                              |   **Não**   |
| **Caixa de Combinação**   | Grupo Autenticação  | Ambiente Ativo                                                                                                     | Campo para selecionar o Ambiente de Comunicação Ativado, que pode ser definido entre "**1 - Homologação ou 2 - Produção**". Este código é enviado na Requisição durante uma consulta.                                                                                                                      | Permitir selecionar apenas um dos Ambientes.                                                                                                                                                                                                                                                                                                |   **Sim**   |
| **Grupo**                 | Sub-aba iMendes     | **Comportamento**                                                                                                  | Organiza os Parâmetros de Regra de Negócio e automatismos da Integração.                                                                                                                                                                                                                                   | Ativar apenas se o Parâmetro Ativar Integração estiver selecionado                                                                                                                                                                                                                                                                          |   **Sim**   |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir Consultar Tributação de Produtos através do Cadastro de Produtos                                          | Permite ao Usuário efetuar a Consulta Tributária durante o Cadastramento de um Novo Produto ou de um Produto Cadastrado através de uma ação no próprio Cadastro de Produtos.                                                                                                                               | Observar os Métodos de Consulta ([Ver Seção Métodos de Consulta](#método-de-consulta-básico)) e Acesso Restrito ([Ver Seção Acessos Restritos](#acessos-restritos)) da Operação.                                                                                                                                                            |   **Sim**   |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir Atualização Parcial das Informações Tributárias do Produto                                                | Permite ao Usuário decidir quais impostos serão atualizados no Produto através de uma Tela de Aceite                                                                                                                                                                                                       | Se este parâmetro for selecionado, gravar **Log** ([Ver Seção Logs](#logs)) dos campos que foram ignorados pelo Usuário e solicitar **Acesso Restrito** ([Ver Seção Acessos Restritos](#acessos-restritos)). O parâmetro "Permitir Atualização Automática de Tributos" deve ser desativado e não aplicado se este parâmetro for selecionado |   **Sim**   |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir Atualização Automática de Tributos dos Produtos                                                           | Permite atualização automatizada dos tributos de produtos ao consultar um Produto ou Receber atualizações em lote. Quando habilitado, não será exibida a tela comparativa de tributos "De > Para" e o Usuário não poderá decidir quais impostos serão atualizados.                                         | Exibir uma mensagem informando o usuário que quando selecionada esta opção o Sistema acata toda e qualquer alteração tributária do Integrador.                                                                                                                                                                                              |   **Não**   |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir Vincular um código iMendes a um Produto Cadastrado                                                        | Permite ao Usuário vincular um Produto Cadastrado que não possua Código de Barras Padronizado a um produto semelhante da Base de Dados iMendes, quando a consulta ocorrer por Descrição.                                                                                                                   | Solicitar Acesso Restrito durante o procedimento no Cadastro de Produtos                                                                                                                                                                                                                                                                    |   **Sim**   |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir atualização/criação de Regras Fiscais Ganso através das informações das Consultas Tributárias realizadas. | Permite que Regras Fiscais completas sejam criadas com base nos dados que as Consultas às APIs iMendes retornaram. Regras Fiscais com estas características não podem ser alteradas.                                                                                                                       | Exibir uma mensagem informando o usuário que as Regras Fiscais criadas não poderão ser alteradas manualmente, e são atualizadas automaticamente de acordo com as consultas realizadas pelo Usuário.                                                                                                                                         |   **Não**   |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir consultar mais de uma UF durante uma Consulta em Lotes                                                    | Permite informar mais de uma UF em uma única requisição para consulta em Lotes através de um _Gerenciador Tributário_. Necessário para atender à recomendação da iMendes, quanto ao tempo de resposta que pode ser exponencial ao número de UFs informadas.                                                | Se permitido, limitar a 5 UFs. Havendo a UF da(s) Empresa(s) Filial(is) a Requisição deverá ser gerada uma requisição especifica para a UF da Filial e uma para as demais. [Ver Método de Consulta Avançado](#método-de-consulta-avançado).                                                                                                 |   **Sim**   |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir consultar mais de uma Característica Tributária durante uma Consulta em Lotes                             | Permite informar mais de uma Característica Tributária em uma única requisição para consulta em Lotes através de um _Gerenciador Tributário_. Necessário para atender à recomendação da iMendes, quanto ao tempo de resposta que pode ser exponencial ao número de Características Tributárias informadas. | Se permitido, limitar a 3. Requer tratamento específico para o Retorno. [Ver Método de Consulta Avançado](#método-de-consulta-avançado).                                                                                                                                                                                                    |   **Sim**   |
| **Campo de Texto**        | Grupo Comportamento | Limite de Produtos por Consulta em Lotes                                                                           | Permite definir o Limite Máximo de Produtos a enviar em um Lote por Requisição. Recomendado para atender clientes com limitações de conexão de internet                                                                                                                                                    | Limitar ao valor máximo de 1000 e mínimo de 100. Valor Padrão: 100                                                                                                                                                                                                                                                                          |   **Sim**   |
| **Caixa de Seleção**      | Grupo Comportamento | Verificar Alterações Tributárias de Produtos Automaticamente a cada n dias                                         | Permite ao Sistema Ganso executar uma varredura automática na Base iMendes a fim de localizar atualizações de Tributações de Produtos, utilizando método específico da API Envia/Recebe dados.                                                                                                             | Informar um número inteiro de dias. Valor padrão: 15 dias                                                                                                                                                                                                                                                                                   |   **Não**   |

### Parâmetros FGF

### Parâmetros Mix Fiscal

[Voltar ao Sumário](#documentação-de-requisitos---integrações-fiscais) | [Voltar ao Roadmap](#roadmap)

## Cadastro de Finalidade de Operações

Os Integradores **iMendes** e **Mix Fiscal** requerem que uma **Operação** seja definida durante a Composição de uma Consulta Tributária, e estas possuem dados específicos de envio para que o Retorno de dados tributários sejam coerentes com o **Cenário** informado.

- A **iMendes** requer que um Código **CFOP** seja enviado (correto ou não, mas válido e coerente com a operação de Entrada ou Saída desejada) para consultar Regras para a operação e demais características informadas. Mesmo que o CFOP não seja o correto, é necessário haver um Código padrão de envio para obter o correto da operação.
- A **Mix Fiscal** requer que um **Cenário** Tributário pré-definido seja informado para consultar Regras e retornar os respectivos dados.

Deste modo, os novos campos necessários neste Cadastro são:
| Tipo de Elemento | Nome/Texto | Descritivo | Validações | Integrador |
|:---|:---|:---|:---|:---:|
| **Botão de Seleção** | Tipo de Movimentação | Seleção para indicar se a Finalidade de Operação cadastrada é uma Movimentação do Tipo **Entrada** ou **Saída**. Utilizado em conjunto com os demais campos para complementar dados da Requisição. Esta informação também será utilizada para composição de Regras Fiscais de Entrada ou Saída | Preenchimento Obrigatório. | **Todos** |
| **Campo** | CFOP Padrão Estadual | Campo para informar o Código do CFOP padrão para a Operação Estadual Cadastrada. Utilizado em conjunto com os demais campos para complementar dados da requisição à API **iMendes**. | Preenchimento Obrigatório. Validar se o CFOP existe na Tabela de CFOPs do Sistema Ganso conforme a definição do Tipo de Movimentação de **Entrada** ou **Saída**, e se o primeiro dígito é compatível com o Tipo de Movimentação: 1 para **Entrada** e 5 para **Saída**. | **iMendes** |
| **Campo** | CFOP Padrão Interestadual | Campo para informar o Código do CFOP padrão para a Operação Interestadual Cadastrada. Utilizado em conjunto com os demais campos para complementar dados da requisição à API **iMendes**. | Preenchimento Obrigatório. Validar se o CFOP existe na Tabela de CFOPs do Sistema Ganso conforme a definição do Tipo de Movimentação de **Entrada** ou **Saída**, e se o primeiro dígito é compatível com o Tipo de Movimentação: 2 para **Entrada** e 6 para **Saída**. | **iMendes** |
| **Caixa de Combinação** | Finalidade do Produto | Campo para definir a Finalidade do Produto. A API **iMendes** requer que seja informado o Código referente à finalidade do produto para retornar a regra correta. Deve existir por padrão as seguintes opções:<br> 0 = Revenda <br> 1 = Insumo <br> 2 = Uso e Consumo ou Ativo Imobilizado | Preenchimento obrigatório. Deve retornar o Código relacionado à opção definida. | **iMendes**
| **Caixa de Combinação** | Cenário Fiscal | Campo para definir o Cenário Fiscal vinculado à finalidade da operação para aprimorar o envio de requisição à API **Mix Fiscal** | Preenchimento Obrigatório quando o Integrador ativado é igual a **Mix Fiscal** e o Tipo de Movimentação for igual a **Saída**. A sigla referente à opção definida é utilizada na Requisição à API. Deve existir por padrão as seguintes opções: <br> SAC - Saída Atacado Contribuinte <br> SAS - Saída Atacado Simples Nacional <br> SVC - Saída Varejo Contribuinte <br> SNC - Saída Não Contribuinte <br> IND - Industrialização <br> TRA - Transferência | **Mix Fiscal** |

## Cadastro de Perfil Fiscal

Além de uma Operação bem definida, o Integrador **iMendes** requer que seja enviada na requisição uma **Característica Tributária** (Código específico) que no Sistema Ganso é definido como **Perfil Fiscal**. Atualmente, este cadastro é aberto e definido pelo usuário, e não garante que o um Código de Característica coerente seja enviado. Desde modo, é necessário criar um campo que contenha esta informação pré-definida que também poderá ser utilizada por outros Integradores Fiscais.

| Tipo de Elemento        | Nome/Texto                | Descritivo                                                                                                                                                                                               | Validações                                                             | Integrador  |
| :---------------------- | :------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------- | :---------: |
| **Caixa de Combinação** | Característica Tributária | Campo para informar a característica tributária vinculada ao Perfil Fiscal criado. O Perfil Fiscal poderá ter uma descrição livre. Apenas o Código vinculado à característica é utilizado na Requisição. | Preenchimento obrigatório se o Integrador **iMendes** estiver ativado. | **iMendes** |

Abaixo a tabela de Códigos e Descrição padrão que o Sistema deverá oferecer:

| Código | Descritivo                               |
| :----- | :--------------------------------------- |
| 0      | Industrial                               |
| 1      | Distribuidor                             |
| 2      | Atacadista                               |
| 3      | Varejista                                |
| 4      | Produtor Rural Pessoa Jurídica           |
| 6      | Produtor Rural Pessoa Física             |
| 7      | Pessoa Jurídica não Contribuinte do ICMS |
| 8      | Pessoa Física não Contribuinte do ICMS   |

**Observação:** _O Código 5 não existe na Tabela de Referência do Integrador iMendes._

Esta informação também será necessária para a Atualização/Criação de Regras Fiscais de Entrada e Saída automáticas conforme consultas.

[Voltar ao Sumário](#documentação-de-requisitos---integrações-fiscais) | [Voltar ao Roadmap](#roadmap)

## Cadastro de Produtos

Para disponibilizar ao Usuário um Método de Consulta de Tributos através do Cadastro de um Produto, é necessário incluir **Funções Específicas** e **Campos Específicos**, descritos a seguir:

### Funções

| Nome                                          | Descritivo                                                                                                                                                                                     | Validações                                                                                  | Integrador  |
| :-------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------ | :---------: |
| Consultar Tributação                          | Aciona a Consulta Tributária conforme Integrador ativado                                                                                                                                       | Solicitar Acesso Restrito para a Operação [Ver Seção Acessos Restritos](#acessos-restritos) |  **Todos**  |
| Consultar Histórico de Alterações Tributárias | Aciona a visualização do Histórico de Alterações do Produto                                                                                                                                    | Solicitar Acesso Restrito para a Operação [Ver Seção Acessos Restritos](#acessos-restritos) | **iMendes** |
| Desfazer Alterações Tributárias               | Aciona a Função contida no Histórico de Alterações para que o Usuário possa reverter dados Tributários atualizados pelo Integrador com possibilidade de escolha do ponto de reversão desejado. | Solicitar Acesso Restrito para a Operação [Ver Seção Acessos Restritos](#acessos-restritos) | **iMendes** |

### Novos Campos

| Tipo                 | Posicionamento                | Nome/Texto                             | Descritivo                                                                                                                                                                                                                                                                                                                                                       | Validações                                                                                                                                                                                                              |   Integrador    |
| :------------------- | :---------------------------- | :------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------: |
| **Campo**            | **Grupo de Dados do Produto** | Código iMendes                         | Campo para armazenar e exibir o Código iMendes, quando ocorrer o vínculo efetuado pelo Usuário durante a Consulta por Descrição                                                                                                                                                                                                                                  | Tipo Numérico Inteiro e Somente Leitura                                                                                                                                                                                 |   **iMendes**   |
| **Campo**            | A definir                     | Auditado por (Integrador Fiscal)       | Campo para armazenar e exibir a informação de que o Produto teve a tributação auditada/atualizada pelo Integrador Fiscal. Complementar esta informação com a Data/Hora da última atualização tributária.                                                                                                                                                         | Somente leitura e visualmente destacado.                                                                                                                                                                                |   **iMendes**   |
| **Campo**            | A definir                     | Enviado para Integrador Fiscal         | Campo para armazenar e exibir a informação de que o Produto foi enviado para Revisão Tributária para o Integrador Fiscal. Será utilizado para identificar quais Produtos estão pendentes de recepção da Tributação do Integrador **iMendes**, e como _flag_ para indicar que precisa receber atualização. Complementar esta informação com a Data/Hora do envio. | Somente leitura e visualmente destacado.                                                                                                                                                                                |    **Todos**    |
| **Caixa de Seleção** | A definir                     | **Não Tributar por Integrador Fiscal** | Parâmetro para restringir a atualização de Tributos do Produto pelo Integrador Fiscal (_exceto Mix Fiscal_) ativo. Por decisão do Usuário, alguns produtos podem ser tributados seguindo a sua própria interpretação ou por orientação de sua contabilidade, e não irão receber atualizações do Integrador.                                                      | Solicitar Acesso Restrito [Ver Seção Acessos Restritos](#acessos-restritos). O Integrador Mix Fiscal requer formalização via e-mail esta decisão, portanto, esta opção deve ser desativado para o Integrador Mix Fiscal | **iMendes/FGF** |

## Nova Tela - Gerenciador Tributário

# Requisitos da Integração iMendes

Nesta Seção são descritos os Requisitos da Integração iMendes, que atende à Homologação e abrange os principais recursos do Integrador. O método de Consulta Básico abrange o Cadastro de Produtos e o Método de Consulta Avançado abrange uma Nova Tela denominada **Gerenciador Tributário**

## Método de Consulta Básico

## Método de Consulta Avançado

## Captura e Armazenamento dos Dados

## Recursos Adicionais

## Regra Fiscal de Entrada e Saída x iMendes

---

# Requisitos da Integração FGF

---

# Requisitos da Integração Mix Fiscal

---

# Requisitos da Regra Fiscal de Saída Ganso

# Requisitos de Segurança

## Acessos Restritos

## Logs

# Requisitos de Homologação

## Checklist do MVP iMendes

## Checklist do MVP FGF

## Checklist do MVP Mix Fiscal

# Simulações
