# Introdução

- O presente documento objetiva descrever em detalhes os processos e meios para Integrações Fiscais de Consulta Tributária do Parceiro **iMendes** no Sistema Ganso.
- No Modelo de Integração definido, o Sistema Ganso comunica-se com o portal tributário do Parceiro Integrador através de uma **API**, e realiza a consulta da Tributação dos produtos obtendo os dados para os mesmos.
- A Consulta pode ocorrer durante a realização de um **novo cadastro**, consultando a tributação de um ou mais **produto(s) cadastrado(s)** ou **enviando todos os Produtos** para Revisão Geral, denominada como **Saneamento** pelo Parceiro.
- Este Parceiro possui Resposta Imediata para consultas simples ou lotes pequenos, desde que o Produto e Código EAN/GTIN tenha sido revisado por sua equipe. Contudo, o **Envio de Todos os Produtos** requer prazo de até 45 dias para devolução integral das informações.
- Este Parceiro recomenda que o Sistema disponibilize decisão invidual ao usuário, que pode ou não acatar as alterações tributárias para seus produtos através do próprio Sistema Ganso.
- Este Parceiro retorna dados suficientes para permitir a criação de Regras Fiscais de Entrada e Saída completas no Sistema Ganso, entretanto, deve ser utilizada a Camada de Tratamento de dados para garantir o direcionamento correto dos dados.

# Roadmap do Integrador

Conforme Análise e Testes da Plataforma do Integrador, foram levantados os passos a seguir para implementação completa da integração, dividida em Etapas:

1.  Etapa 1 - Implementação do Método de Consulta Básico descrito em [Método de Consulta Básico](#métodos-de-consulta)
2.  Etapa 2 - Implementação do Método de Consulta Avançado descrito em [Método de Consulta Avançado](#consulta-avançada-imendes---gerenciador-tributário)
3.  Etapa 3 - Validação das Implementações de acordo com o Checklist do Parceiro descrito em [Checklist MVP](#checklist-do-mvp-imendes)
4.  Etapa 4 - Implementações de Recursos Adicionais do Integrador para atender a Versão Final desejável em [Recursos Adicionais]()

- **Roadmap ilustrado**

  ![Roadmap Integração iMendes](./Roadmap-iMendes.png)

## Parâmetros do Sistema

Nesta seção, são descritos os Parâmetros necessários para o funcionamento desta Integração. Deve ser utilizada a nova aba de **Integrações Fiscais** dos Parâmetros do Sistema, e conter os elementos descritos abaixo:

| Tipo de Elemento          | Posicionamento      | Nome/Texto                                                                 | Descritivo                                                                                                                                                                                                                                                                | Validações                                                                                                                                                                                                                                  |
| :------------------------ | :------------------ | :------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Caixa de Seleção**      | Aba iMendes         | Ativar Integração de Consulta Tributária iMendes                           | Parâmetro para Ativação das Configurações de Integração e funcionalidade no Sistema Ganso                                                                                                                                                                                 | Não permitir ativar se houver outra Integração Fiscal Ativada no Sistema Ganso                                                                                                                                                              |
| **Grupo**                 | Aba iMendes         | **Autenticação**                                                           | Organiza os campos de Configurações para conectividade com os Servidores iMendes                                                                                                                                                                                          | Ativar apenas se o Parâmetro Ativar Integração estiver selecionado                                                                                                                                                                          |
| **Campo Texto**           | Grupo Autenticação  | URL da API Saneamento (Consulta Tributação)                                | Campo para informar a URL da API que retorna Dados da Tributação do Produto consultado.                                                                                                                                                                                   | Permitir até 255 caracteres.                                                                                                                                                                                                                |
| **Campo Texto**           | Grupo Autenticação  | URL da API Envia/Recebe Dados (Outros Métodos)                             | Campo para informar a URL da API utilizada para os Recursos Adicionais do iMendes descritos em [Recursos Adicionais iMendes](#recursos-adicionais)                                                                                                                        | Permitir até 255 caracteres.                                                                                                                                                                                                                |
| **Campo Texto Mascarado** | Grupo Autenticação  | Senha                                                                      | Campo para informar a Senha do Cliente Integrado.                                                                                                                                                                                                                         | Permitir até 30 caracteres alfanuméricos e símbolos                                                                                                                                                                                         |
| **Caixa de Combinação**   | Grupo Autenticação  | Versão da API                                                              | Seleção da Versão da API contratada. Disponibilizar as versões 2.0 e 3.0 por padrão                                                                                                                                                                                       | Permitir selecionar apenas uma das versões                                                                                                                                                                                                  |
| **Campo de Texto**        | Grupo Autenticação  | Tempo de Resposta                                                          | Timeout ou Tempo de Resposta máximo da API. Tempo máximo que o Sistema deve aguardar para obter a resposta antes de efetuar uma nova requisição.                                                                                                                          | Campo Numérico, interpretado em segundos, com valor padrão definido em 15 segundos.                                                                                                                                                         |
| **Botão de Ação**         | Grupo Autenticação  | Verificar Status                                                           | Botão para acionar um comando de teste de conectividade com as APIs utilizando os dados de Autenticação e URLs informadas.                                                                                                                                                | Deve retornar uma Mensagem amigável de Sucesso ou Falha, informando quais APIs foram testadas.                                                                                                                                              |
| **Caixa de Combinação**   | Grupo Autenticação  | Ambiente Ativo                                                             | Campo para selecionar o Ambiente de Comunicação Ativado, que pode ser definido entre "**1 - Homologação ou 2 - Produção**". Este código é enviado na Requisição durante uma consulta.                                                                                     | Permitir selecionar apenas um dos Ambientes.                                                                                                                                                                                                |
| **Grupo**                 | Sub-aba iMendes     | **Comportamento**                                                          | Organiza os Parâmetros de Regra de Negócio e automatismos da Integração.                                                                                                                                                                                                  | Ativar apenas se o Parâmetro Ativar Integração estiver selecionado                                                                                                                                                                          |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir Consultar Tributação de Produtos através do Cadastro de Produtos  | Permite ao Usuário efetuar a Consulta Tributária durante o Cadastramento de um Novo Produto ou de um Produto Cadastrado através de uma ação no próprio Cadastro de Produtos.                                                                                              | Observar os Métodos de Consulta ([Ver Seção Métodos de Consulta](#método-de-consulta-básico)) e Acesso Restrito ([Ver Seção Acessos Restritos](#acessos-restritos)) da Operação.                                                            |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir Atualização Automática de Tributos dos Produtos                   | Permite atualização automatizada dos tributos de produtos ao consultar um Produto ou Receber atualizações em lote. Quando habilitado, não será exibida a tela comparativa de tributos "(Antes x Depois)" e o Usuário não poderá decidir quais impostos serão atualizados. | Exibir uma mensagem informando o usuário que quando selecionada esta opção o Sistema acata toda e qualquer alteração tributária do Integrador.                                                                                              |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir Vincular Consultar Tributação por Descrição do Produto            | Permite ao Usuário consultar Tributação por Descrição e vincular um Produto Cadastrado que não possua Código de Barras Padronizado a um produto semelhante da Base de Dados iMendes, quando a consulta ocorrer por Descrição.                                             | Solicitar Acesso Restrito durante o procedimento no Cadastro de Produtos [Ver Seção Acessos Restritos](#acessos-restritos)                                                                                                                  |
| **Caixa de Seleção**      | Grupo Comportamento | Permitir consultar mais de uma UF durante uma Consulta em Lotes            | Permite informar mais de uma UF em uma única requisição para consulta em Lotes através do _Gerenciador Tributário_. Necessário para atender a recomendação da iMendes quanto ao tempo de resposta que pode ser exponencial ao número de UFs informadas.                   | Se permitido, limitar a 5 UFs. Havendo a UF da(s) Empresa(s) Filial(is) a Requisição deverá ser gerada uma requisição especifica para a UF da Filial e uma para as demais. [Ver Método de Consulta Avançado](#método-de-consulta-avançado). |
| **Campo de Texto**        | Grupo Comportamento | Limite de Produtos por Consulta em Lotes                                   | Permite definir o Limite Máximo de Produtos a enviar em um Lote por Requisição. Recomendado para atender clientes com limitações de conexão de internet                                                                                                                   | Limitar ao valor máximo de 1000 e mínimo de 100. Valor Padrão: 100                                                                                                                                                                          |
| **Caixa de Seleção**      | Grupo Comportamento | Verificar Alterações Tributárias de Produtos Automaticamente a cada n dias | Permite ao Sistema Ganso executar uma varredura automática na Base iMendes com finalidade de localizar atualizações de Tributações de Produtos, utilizando método específico da API Envia/Recebe dados.                                                                   | Informar um número inteiro de dias. Valor padrão: 15 dias                                                                                                                                                                                   |

# Requisitos da Integração iMendes

Nesta Seção são descritos os Requisitos a Implementar da Integração iMendes, que atende à Homologação e abrange os principais recursos do Integrador. Inicialmete, devem ser considerados 4 (quatro) Métodos de Consulta e cada um utiliza informações específicos para gerar a Requisição e obter os dados tributários. A seguir serão descritos os Métodos e suas definições.

## Métodos de Consulta

Os Métodos de Consulta são necessários para a tomada de decisão durante a consulta tributária de um Produto, pois, _depende de quais informações serão fornecidas pelo Usuário ao Sistema_. Cada Método trata um _input_ e possui uma API de consulta específica, que requer dados específicos. A seguir a definição de cada Método:

|    Método    |                   Tipo de Consulta                   | Descritivo                                                                                                                                                                                                        | Regras de Negócio                                                                                                                                                                                                                                                                                                                                                                                                                                                |     API a Consumir     | Tags de Envio Principais                                                     |
| :----------: | :--------------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------: | :--------------------------------------------------------------------------- |
| **Método 1** | **Código de Barras EAN/GTIN e Descrição do Produto** | Consultar a Tributação do Produto utilizando o Código de Barras EAN/GTIN e a Descrição do Produto.                                                                                                                | Identificar se o Código é um EAN válido, considerando tamanho de 8, 12, 13 ou 14 dígitos. Durante envio da Consulta, considerar sempre 14 dígitos para envio, preenchendo com Zeros à esquerda caso EAN diferente de tamanho 14. Se EAN válido, identificar se o Prefixo do Código de Barras inicia em "789" ou "790", ou "1789" ou "1790" e enviar origem igual a 0, caso contrário enviar origem igual a 8. Capturar o retorno dos dados pela Tag `"prodEAN"`. |     **Saneamento**     | `"codigo":"EAN", "codInterno":"N", "codIMendes":"", "descricao":"DESCRICAO"` |
| **Método 2** |                 **Apenas Descrição**                 | Consultar a Tributação de um Produto utilizando a Descrição. Método disponível em outro Endpoint, que retorna uma lista de Produtos semelhantes, e permite vincular um Produto da Lista com o Produto Cadastrado. | Permitir vincular apenas **um** Produto iMendes com **um** Produto Cadastrado. Solicitar Acesso Restrito para esta operação. Permitir vincular o Produto com/sem Código de Barras Padronizado que não está vinculado a um Código iMendes. [Ver Seção Acessos Restritos](#acessos-restritos)                                                                                                                                                                      | **Envia/Recebe Dados** | `"nomeservico":"DESCPRODUTOS", "dados":"CNPJ\|DESCRICAO" `                   |
| **Método 3** |            **Código iMendes e Descrição**            | Consultar a Tributação do Produto utilizando o Código iMendes previamente vinculado ao Produto Cadastrado.                                                                                                        | Verificar se o Produto possui um Código iMendes, se sim, utilizar esta informação para localizar a tributação.                                                                                                                                                                                                                                                                                                                                                   |     **Saneamento**     | `"codIMendes":"CODIGOVINCULADO", "descricao":"DESCRICAO"`                    |
| **Método 4** |                  **Código Interno**                  | Consultar a Tributação do Produto utilizando o **Código Interno** do Produto Ganso previamente classificado pela iMendes. Este Método requer que a Base de Produtos tenha sido enviada anteriormente à iMendes.   | Verificar se o Produto possui o campo **Enviado para Integrador Fiscal** preenchido para definir utilização deste método. [Ver definição do Campo no Cadastro de Produto](#cadastro-de-produtos)                                                                                                                                                                                                                                                                 |     **Saneamento**     | `"codInterno":"CODIGOINTERNO"`                                               |

Para ilustrar a tomada de decisão que o Sistema Ganso deverá realizar conforme o _Input de dados_ do Usuário ou Dados coletados do Produto, o **Fluxo de Decisão** abaixo define a direção a ser tomada.

![Fluxo de Decisão do Método de Consulta](./Flow-Decision-Method-02.png)

[Voltar ao Sumário](#introdução) | [Voltar ao Roadmap](#roadmap)

## Composição da Requisição

Conforme **Manual de Integração iMendes**, uma Consulta a **API de Saneamento** requer um padrão `JSON`. Uma Consulta Básica através do **Cadastro de Produtos** é compreendida nos seguintes passos:

1. Usuário aciona a **Função de Consulta**.
2. O Sistema identifica **quais dados foram inseridos pelo Usuário** através do **Fluxo de Decisão de Método de Consulta** para definir o [**Método de Consulta**](#métodos-de-consulta) ideal, coleta as Tags principais e efetua consulta utilizando a API correta.
3. Se o **Método de Consulta** definido for igual a **"Método 2 - Apenas Descrição"**, o **Passo 8** deve ser executado, senão, continuar a partir do **Passo 4**.
4. O Sistema Coleta dados do Emitente e gera a Tag `"emit"` do `JSON`.
5. O Sistema Coleta dados do Perfil do Destinatário da Operação e gera a Tag `"perfil"` do `JSON`.
6. O Sistema Coleta dados do Produto e gera a Tag `"produtos"`
7. O Sistema Constrói a estrutura `JSON` obedecendo a hierarquia estabelecida: `emit` > `perfil` > `produtos` e executa o **Passo 9**
8. Utilizar a **API Envia/Recebe Dados** e exibir a **Nova Tela - Consulta por Descrição** para Usuário decidir qual produto do iMendes vincular ao Produto Cadastrado. A partir deste ponto, o Produto possuirá um Código iMendes, e utilizará o Método 3. Continuar do **Passo 2**.
9. O Sistema envia requisição a API indicada no Método. [Ver Seção Parâmetros iMendes](#parâmetros-imendes)
10. O Sistema obtém retorno de dados tributários e os envia à Camada de Tratamento.

O Fluxo abaixo, demonstra os passos que compoem a Consulta a **API Saneamento**.

![Ilustração do Fluxo de Consulta API Saneamento](./iMendes-Workflow-01.png)

O Fluxo abaixo, demonstra os passos que compoem a Consulta a **API Envia/Recebe Dados**

![Ilustração do Fluxo de Consulta API Envia/Recebe](./iMendes-Workflow-02.png)

### Detalhamento da Estrutura JSON

A Tag `emit` que deve conter os dados da **Empresa**, possui a seguinte relação de dados abaixo:

| Dado                | Tag                 | Tipo      | Descritivo                                                                                                                                                                                                                   | Origem dos Dados                                                               | Preenchimento Obrigatório |
| :------------------ | :------------------ | :-------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------- | :-----------------------: |
| Ambiente            | `"amb"`             | Código    | Tipo de Ambiente de Envio da Requisição, sendo 1 = Homologação e 2 = Produção.                                                                                                                                               | Consultar o Parâmetro "Ambiente Ativo" em [Parâmetros](#parâmetros-do-sistema) |          **Sim**          |
| CNPJ                | `"cnpj"`            | Caractere | CNPJ do Emitente da Consulta (Cliente)                                                                                                                                                                                       | **Cadastro de Empresas** campo "CNPJ"                                          |          **Sim**          |
| CRT                 | `"crt"`             | Código    | Código do CRT da Empresa. 1 = Simples Nacional, 2 = Simples Nacional com excesso de sublimite ou 3 = Regime Normal                                                                                                           | **Cadastro de Empresas** campo "CRT"                                           |          **Sim**          |
| Regime Tributário   | `"regimeTrib"`      | Caractere | Regime Tributário da Empresa. Novo Campo Regime Tributário do Cadastro de Empresas. Se "Lucro Real" definir 'LR', se "Lucro Presumido" definir 'LP'                                                                          | **Cadastro de Empresas** campo novo "Regime Tributário".                       |          **Sim**          |
| UF                  | `"uf"`              | Caractere | UF do Emitente                                                                                                                                                                                                               | **Cadastro de Empresas** campo "UF" do Endereço                                |          **Sim**          |
| CNAE                | `"cnae"`            | Caractere | CNAE do Emitente                                                                                                                                                                                                             | **Cadastro de Empresas** campo "CNAE"                                          |          **Não**          |
| Substuto Tributário | `"substICMS"`       | Caractere | Indicativo de Emitente Substituto Tributário. Se houver uma Inscrição Estadual de Substituto Tributário informada no Cadastro de Empresas, deve ser enviada esta informação tanto na tag `"emit"` quando na Tag `"produtos"` | **Cadastro de Empresas** grid de dados "Inscrição Substituto Tributário"       |          **Não**          |
| Dia                 | `"Dia"`             | Número    | Dia da Vigência combinada com Mês e Ano para consultas específicas por Data                                                                                                                                                  | Não necessário                                                                 |          **Não**          |
| Mês                 | `"Mês"`             | Número    | Mês da Vigência combinada com Dia e Ano para consultas específicas por Data                                                                                                                                                  | Não necessário                                                                 |          **Não**          |
| Ano                 | `"Ano"`             | Número    | Ano da Vigência combinada com Dia e Mês para consultas específicas por Data                                                                                                                                                  | Não necessário                                                                 |          **Não**          |
| Interdependente     | `"interdependente"` | Caractere | Informação específica. Enviar sempre como "N"                                                                                                                                                                                | Não necessário                                                                 |          **Sim**          |

A Tag `perfil` compõe o **Perfil do Destinatário da Operação** em que se deseja obter a Tributação. Deste modo, através de **Consulta Básica** via **Cadastro de Produtos**, a operação específica de **Saída na Operação de Venda ao Consumidor Final (NFC-e)** deve ser fixada. Para as demais operações estes dados também serão necessários, contudo serão tratados de maneira distinta de acordo com as Operações Desejadas pelo Usuário, e devem ser realizadas Consultas através da **Mova Tela - Gerenciador Tributário**.

| Dado                      | Tag            | Tipo                          | Descritivo                                                                                                                                                                                                                                                  |                                                                                                                                                           Regra de Negócio                                                                                                                                                           | Preenchimento Obrigatório |
| :------------------------ | :------------- | :---------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :-----------------------: |
| UF                        | `"uf"`         | **Lista de Dados**            | Lista de UFs para Consulta de Regras.                                                                                                                                                                                                                       |                                                                                 Se a consulta ocorrer através do Cadastro de Produtos, utilizar a UF da Empresa Filial Logada, senão, utilizar as UFs informadas no campo do Gerenciador Tributário.                                                                                 |          **Sim**          |
| CFOP                      | `"cfop"`       | **Código da Operação**        | Código da Operação a ser Realizada. Deve ser enviada uma operação coerente com os dados desejados, por exemplo, uma Operação de Venda deve conter um CFOP que indique operação de Venda, mesmo que este não seja o correto (a iMendes retornará o correto). |                                                                     Se a consulta ocorrer através do Cadastro de Produtos, preencher com o Código **"5102"**, senão, preencher com o CFOP Padrão da Finalidade de Operação informada no Gerenciador Tributário.                                                                      |          **Sim**          |
| Característica Tributária | `"caracTrib"`  | **Lista de Códigos Inteiros** | Indica o **Tipo de Destinatário** da Operação. Esta informação deve ser obtida do Cadastro de Perfil Fiscal, do campo "Característica Tributária".                                                                                                          | Deve retornar um Código para cada Característica definida no Perfil Fiscal: <br>0 - Industrial <br>1 - Distribuidor <br>2 - Atacadista <br>3 - Varejista <br>4 - Produtor Rural Pessoa Jurídica <br>6 - Produtor Rural Pessoa Física <br>7 - Pessoa Jurídica não Contribuinte do ICMS <br>8 - Pessoa Física não Contribuinte do ICMS |          **Sim**          |
| Finalidade                | `"finalidade"` | Código                        | Indica a Destinação do Produto para a Operação informada. É importante para especificar a operação, e deve ser obtida do campo **"Finalidade do Produto"** do Cadastro da **Finalidade de Operação** informada para Consulta.                               |                                                                                                Valores possíveis: 0 - Mercadoria para Revenda <br>1 - Insumos de Produção <br>2 - Materiais para Uso e Consumo ou Ativo Imobilizado.                                                                                                 |          **Sim**          |
| Simples Nacional          | `"simplesN"`   | Caractere                     | Indica se o Destinatário da Operação é Simples Nacional ou Não. Preencher com "S" ou "N", conforme Regra de Negócio.                                                                                                                                        |                                                                                                                           Se CRT da Empresa Filial Logada é igual a 1 ou 2, enviar "S", senão, enviar "N".                                                                                                                           |          **Sim**          |
| Origem                    | `"origem"`     | Código                        | Indica a Origem da Mercadoria.                                                                                                                                                                                                                              |                                                                                                             Se Tipo de Consulta igual a **Método 1**, e **Código de Barras** não iniciar em 789 ou 790, enviar Código 8.                                                                                                             |          **Sim**          |
| Substituição Tributária   | `"substICMS"`  | Caractere                     | Indica se o destinatário é Substituto Tributário.                                                                                                                                                                                                           |                                                                                             Se houver uma Inscrição Estadual de Substituto Tributário informada no Cadastro de Empresas, deve ser enviado "S", caso contrário enviar "N"                                                                                             |          **Sim**          |

Obtidos os dados do Perfil, a Tag de `"produtos"` é um _array_ de Produtos e deve ser composta conforme dados e Regras de Negócio abaixo:

| Dado           | Tag            | Tipo      | Descritivo                                                                                                                               | Origem dos Dados                                                                                            | Regras de Negócio                                                                                                                                                                                                                            | Preenchimento Obrigatório |
| :------------- | :------------- | :-------- | :--------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-----------------------: |
| Código         | `"codigo"`     | Código    | Código de Barras EAN/GTIN ou Código Interno do Produto quando o mesmo foi enviado previamente para Saneamento pela iMendes.              | **Cadastro de Produtos** campo Código de Barras Padrão ou Código Interno                                    | Verificar o Método de Consulta apropriado para preenchimento deste campo, observando a Regra de Negócio. [Ver Seção Métodos de Consulta](#métodos-de-consulta)                                                                               |          **Sim**          |
| Código Interno | `"codInterno"` | Caractere | Indicativo de "Sim" ou "Não" para consulta via Código Interno, quando o Produto foi enviado previamente para Saneamento pela iMendes     | Preencher com "S" ou "N" de acordo com a Regra de Negócio                                                   | Se o Produto está sinalizado como **"Enviado para Integrador Fiscal"**, enviar "S" e utilizar o **Método de Consulta 4** informando Código Interno do Produto na Tag `codigo` do JSON. [Ver Seção Métodos de Consulta](#métodos-de-consulta) |          **Sim**          |
| Descrição      | `"descricao"`  | Caractere | Descrição Completa do Produto                                                                                                            | **Cadastro de Produtos** campo "Descrição"                                                                  | Sempre enviar a Descrição Completa do Produto.                                                                                                                                                                                               |          **Sim**          |
| Código iMendes | `"codImendes"` | Código    | Código _Único_ fornecido pela iMendes. Quando um produto é vinculado ao código iMendes esta informação deve ser utilizada para consulta. | **Cadastro de Produtos** campo **"Codigo iMendes"** [Ver Seção Cadastro de Produtos](#cadastro-de-produtos) | Verificar se o Código iMendes está preenchido, se sim, enviar este Código, senão enviar em branco. Utilizar o **Método de Consulta 3** [Ver Seção Métodos de Consulta](#métodos-de-consulta)                                                 |          **Não**          |
| NCM            | `"ncm"`        | Caractere | Nomenclatura Comum do Mercosul                                                                                                           | **Cadastro de Produtos** campo "NCM"                                                                        | Verificar se NCM está preenchido no Cadastro do Produto. Esta informação é importante para a correta classificação do produto.                                                                                                               |          **Não**          |

### Exemplo de Requisição API - JSON completo

A Estrutura abaixo exemplifica uma Consulta do Produto **Água Mineral** através do **Cadastro de Produtos** para **Operação de Saída por Venda a Consumidor Final** para o Estado de **MS** (Operação Interna). Na Tag `perfil/uf` é enviada apenas a UF correspondente à UF da Empresa Filial, ou seja, da Tag `emit/uf`.

```JSON
{
  "emit": {
    "amb": 1,
    "cnpj": "04391715000173",
    "crt": 3,
    "regimeTrib": "LR",
    "uf": "MS",
    "cnae": "",
    "substICMS": "N",
    "interdependente": "N"
  },
  "perfil": {
    "uf": ["MS"],
    "cfop": "5102",
    "caracTrib": [8],
    "finalidade": 0,
    "simplesN": "N",
    "origem": "0",
    "substICMS": "N"
  },
  "produtos": [
    {
      "codigo": "7894900531008",
      "codInterno": "N",
      "codIMendes": "",
      "descricao": "AGUA MINERAL CRYSTAL C/GAS 500ML",
      "ncm": "22011000"
    }
  ]
}
```

A requisição acima, retornará a seguinte Estrutura de Dados, também em `JSON`:

```JSON
 { "Cabecalho": {
        "sugestao": "Se a comunicação estiver lenta, reduza o número de UF's, Caract. Tributárias e produtos. Nessa ordem.",
        "amb": 1,
        "cnpj": "04391715000173",
        "dthr": "2022-08-18T11:37:34.7495236-03:00",
        "transacao": "48773646",
        "mensagem": "OK",
        "prodEnv": 1,
        "prodRet": 1,
        "prodNaoRet": 0,
        "comportamentosParceiro": "104;106;108",
        "comportamentosCliente": "",
        "versao": "2.3.5.0"
    },
    "Grupos": [
        {
            "codigo": "7408",
            "nCM": "22011000",
            "cEST": "03.005.04",
            "lista": "",
            "tipo": "",
            "codAnp": "",
            "passivelPMC": "S",
            "impostoImportacao": 20.00,
            "pisCofins": {
                "cstEnt": "73",
                "cstSai": "06",
                "aliqPis": 0.00,
                "aliqCofins": 0.00,
                "nri": "918",
                "ampLegal": "'Lei n 13.097/2015, Art. 28'",
                "redPis": 0,
                "redCofins": 0
            },
            "iPI": {
                "cstEnt": "03",
                "cstSai": "53",
                "aliqipi": 0.00,
                "codenq": "999",
                "ex": "00"
            },
            "Regras": [
                {
                    "uFs": [
                        {
                            "uF": "MS",
                            "CFOP": {
                                "cFOP": "5102",
                                "CaracTrib": [
                                    {
                                        "codigo": "8",
                                        "finalidade": "0",
                                        "codRegra": "1473",
                                        "codExcecao": 0,
                                        "cFOP": "5405",
                                        "cST": "60",
                                        "cSOSN": "",
                                        "aliqIcmsInterna": 17.00,
                                        "aliqIcmsInterestadual": 0.00,
                                        "reducaoBcIcms": 0.00,
                                        "reducaoBcIcmsSt": 0,
                                        "redBcICMsInterestadual": 0,
                                        "aliqIcmsSt": 0,
                                        "iVA": 0,
                                        "iVAAjust": 0,
                                        "fCP": 0.00,
                                        "codBenef": "",
                                        "pDifer": 0,
                                        "pIsencao": 0.00,
                                        "antecipado": "N",
                                        "desonerado": "N",
                                        "isento": "N",
                                        "tpCalcDifal": 0,
                                        "ampLegal": "'BASE LEGAL DA SUBSTITUICAO TRIBUTARIA - RICMS/MS, ANEXO III, SUBANEXO I, TABELAS IV-A E IV-B, ITEM 5.4'",
                                        "InfPDV": {
                                            "pICMSPDV": 0,
                                            "simbPDV": "F",
                                            "cstICMS": "60",
                                            "csosn": "",
                                            "cstSai": "06",
                                            "aliqPis": 0.00,
                                            "aliqCofins": 0.00
                                        },
                                        "Protocolo": {},
                                        "Convenio": {}
                                    }
                                ]
                            },
                            "mensagem": "OK"
                        }
                    ]
                }
            ],
            "prodEan": [
                "07894900531008"
            ],
            "Mensagem": "OK"
        }
    ],
    "SemRetorno": []
}
```

Após Processo de Envio e Captura de Retorno, os seguintes passos devem ocorrer:

1. A Camada de Tratamento deve interpretar e relacionar os dados com respectivos destinos, conforme descrito nas tabelas de [**Relação de Campos Ganso x Integrador Fiscal / Coluna "Ganso" e "Retorno iMendes"**](#relação-de-campos-ganso-x-integrador-fiscal).
2. É desejável que o Usuário visualize os dados antes da atualização e após atualização.
3. Efetivar a gravação dos dados na Regra Fiscal correspondente, e efetivar a gravação de [**Logs**](#logs).

[Voltar ao Sumário](#introdução) | [Voltar ao Roadmap](#roadmap)

## Consulta Avançada iMendes - Gerenciador Tributário

A Consulta Avançada do Integrador iMendes utilizará a [**Nova Tela do Gerenciador Tributário**](#nova-tela---gerenciador-tributário), e inicialmente, **Regras de Negócio** devem ser observadas para tomada de decisão de acordo com a ação tomada pelo Usuário:

### Regras de Negócio

| Ação do Usuário                                                                                                                                 | Validação                                                                                                                                               | Resposta ao Usuário                                                                                                                                                                                                                                                                                              | Dados da Composição                                                                                                                                                                                                 | Tags de Envio Principais                                        |
| :---------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :-------------------------------------------------------------- |
| Informar uma **Finalidade de Operação** do tipo **Entrada** ou **Saída** e no campo UFs informar a UF da Empresa Filial e Outras UFs            | Gerar uma **Requisição** específica para a UF da Empresa Filial e uma **Requisição** específica para as demais UFs.                                     | Informar que serão geradas duas Requisições distintas para API, e que os dados Recebidos serão agrupados por UF.                                                                                                                                                                                                 | Utilizar o **Código CFOP Padrão** da **Finalidade de Operação** informada para gerar a **Requisição**. Utilizar a **Finalidade do Produto** contida na **Finalidade da Operação** informada para compor a Consulta. | `perfil/uf`, `perfil/cfop`, `perfil/finalidade`                 |
| Filtrar e Selecionar Produtos que requerem **Métodos de Consulta** distintos. [**Ver Seção Métodos de Consulta iMendes**](#métodos-de-consulta) | Tratar cada situação de modo distinto (conforme o Método de Consulta) para assegurar que a consulta seja realizada utilizando a API correta da iMendes. | Informar ao Usuário que determinados Produtos poderão ser consultados por meios distintos e que alguns deverão ser processados individualmente, como por exemplo, Produtos que precisam ser **Consultados na Base iMendes por Descrição**. [Ver Seção Métodos de Consulta - Métodos 2 e 3](#métodos-de-consulta) | Código iMendes, campo "Enviado para Integrador Fiscal" do Cadastro de Produtos                                                                                                                                      | `produtos/codigo`, `produtos/codInterno`, `produtos/codImendes` |

[Voltar ao Sumário](#introdução) | [Voltar ao Roadmap](#roadmap)

### Simulações de Consulta

# Requisitos de Homologação

Nesta seção está contida a relação de recursos que o Sistema Ganso deve oferecer para que a Homologação seja concluída com sucesso.

## Checklist do MVP iMendes

| Item  | Requisito iMendes                                                                                                                                                                                                          | Requisito Ganso                                                                                                 | Grau de Importância | Implementado |
| :---: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------- | :-----------------: | :----------: |
| MVP 1 | Opção para Usuário definir que o Produto Cadastrado não deve ser enviado à iMendes                                                                                                                                         | Documentação de Requisitos de Integrações / Cadastro de Produtos                                                |      **Alto**       |      -       |
| MVP 2 | Opção para Usuário enviar informações manualmente                                                                                                                                                                          | [Ver Seção Consulta Avançada - iMendes](#consulta-avançada-imendes---gerenciador-tributário) (Consulta em Lote) |      **Alto**       |      -       |
| MVP 3 | Captura de Retorno controlado por Log e **Reversão** de tributação atualizada de qualquer Produto em qualquer ponto do histórico                                                                                           | Documentação de Requisitos de Integrações / Requisitos de Segurança                                             |      **Alto**       |      -       |
| MVP 4 | Consultar a Tributação de um único produto através do Cadastro do Produto e receber as atualizações após consulta                                                                                                          | Documentação de Requisitos de Integrações / Cadastro de Produtos                                                |      **Alto**       |      -       |
| VF 1  | Opção para Usuário vincular um Produto próprio com o Código iMendes                                                                                                                                                        | [Ver Seção Métodos de Consulta - Método 2](#métodos-de-consulta)                                                |      **Alto**       |      -       |
| VF 2  | Gravar e exibir no Cadastro do Produto um selo indicativo de auditoria da tributação pela IMendes                                                                                                                          | Documentação de Requisitos de Integrações / Cadastro de Produtos                                                |      **Alto**       |      -       |
| VF 3  | Gravar e exibir o Retorno da Consulta por Produto contendo todos os campos retornados pela API e permitir a seleção de campos individuais para atualização. Apresentar a relação **Antes e Depois** e um Aceite do Usuário | -                                                                                                               |      **Alto**       |      -       |
| VF 4  | Efetuar verificação períodica de Atualizações Tributárias dos Produtos auditados por iMendes através do Método **Alterados**                                                                                               | [Ver Seção Parâmetros iMendes / Verificação Periódica](#parâmetros-do-sistema)                                  |      **Alto**       |      -       |
| VF 5  | Criar um Relatório Gerencial para acompanhamento do Histórico de Mudanças Tributárias ocorridas em determinado período                                                                                                     | [Documentação de Requisitos de Integrações / Requisitos de Segurança                                            |      **Alto**       |      -       |
| VF 6  | Implementar o **Simulador Tributário** que efetua uma verificação no Cadastro de Produtos e aponta os problemas tributários em Clientes ainda não integrados                                                               | -                                                                                                               |      **Médio**      |      -       |
| VF 7  | Implementar mensagem de **Sugestão de Contratação da iMendes** para captura de novos clientes                                                                                                                              | -                                                                                                               |      **Baixo**      |      -       |

**Legenda**: MVP - Implementação Mínima | VF - Versão Final (Recursos Adicionais)
