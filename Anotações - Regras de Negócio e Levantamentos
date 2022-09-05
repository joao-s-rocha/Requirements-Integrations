# Regras de Negócio - Regra Fiscal

- Se **Tipo de Regra** igual a **Saída**, permitir definir uma **Finalidade da Operação** cujo **Tipo de Movimentação** seja igual a **Saída**
- Se **Tipo de Regra** igual a **Entrada**, permitir definir uma **Finalidade da Operação** cujo **Tipo de Movimentação** seja igual a **Entrada**
- Se **Finalidade de Operação** for de uma **Operação** de **Venda a Consumidor Final**, não permitir incluir **UFs de Destino** diferentes das UFs das Filiais informadas no campo **Empresas**
- Se **Tipo de Regra** igual a **Saída** e **Finalidade de Operação** for diferente de uma **Operação** de **Venda a Consumidor Final** e **UF** de Origem/Destino diferente das UFs das Filiais informadas no campo **Empresas**, listar apenas **CFOP** iniciado em "6"
  - Se **Regra criada pelo Integrador** aplicar o **CFOP Padrão Interestadual**
- Se **Tipo de Regra** igual a **Entrada** e **Finalidade de Operação** e **UF** de Origem/Destino diferente das UFs das Filiais informadas no campo **Empresas**, listar apenas **CFOP** iniciado em "2"
  - Se **Regra criada pelo Integrador** aplicar o **CFOP Padrão Interestadual**
- Se **Tipo de Regra** igual a **Saída**, o campo "Destaque" do ICMS ST deverá ser preenchido conforme as regras:
  - Se **Finalidade da Operação** indicar que haverá o Destaque do ICMS ST, utilizar **Dentro da NF**
  - Se **Finalidade da Operação** indicar que não haverá o Destaque do ICMS ST, utilizar **Manter Original**
- Se **Tipo de Regra** igual a **Saída** e **Código do Tributo** indicar um Tributo cuja **Situação Tributária** estiver entre "F", "I" ou "N", **Zerar ICMS** deve ser marcado como "S".

## Integrado com Mix Fiscal

- Se **Tipo de Regra** igual a **Saída** e **CST do ICMS** igual a **"60"** e **Alíquota do ICMS ST** igual a **"0,00"**, **Zerar Base de Cálculo e Valores do ICMS ST**.

# Camada de Tratamento de Dados

- Capturar o Retorno e Relacionar os dados com a Biblioteca do Integrador
- Relacionar Tipo de Consulta (Operação, Perfil Fiscal) com o Tipo de Regra Fiscal a ser criada
  - Ex.: Se Tipo de Consulta definida é **Venda a Consumidor Final**, criar Regras Fiscais cuja Finalidade de Operação seja correspondente.

# Simulações

## Atualizando o Sistema sem Integração Fiscal

1. Separar todos os Produtos e suas informações Tributárias para criar uma Regra Padrão de "Venda a Consumidor Final" por "NCM" e Demais Dados do Produto.
2. Criar o Perfil Fiscal "Pessoa Física/Jurídica Não Contribuinte", definido a Característica Tributária como "Pessoa Física não Contribuinte do ICMS" e Regime Tributário como "Lucro Real".
3. Criar a Finalidade Operação "Venda a Consumidor Final Tributado Estadual", definindo a Operação como "Venda a Consumidor Final", Tipo de Movimentação como "Saída", CFOP Padrão como "5102".
   1. Quando o Produto possuir Alíquota de ICMS igual a "Subst. Trib." e CST Estadual entre "000,020,040,041", utilizar esta Regra.
4. Criar a Finalidade de Operação "Venda a Consumidor ST Estadual", definindo a Operação com "Venda a Consumidor Final", Tipo de Movimentação como "Saída", CFOP Padrão como "5405".
   1. Quando o Produto possuir Alíquota de ICMS igual a "Subst. Trib." e CST Estadual igual a "060", utilizar esta Regra
5. Criar Regras Fiscais e Relacionar o Código da Regra Criada como Regra Fiscal de Saída Padrão no Produto.
6. O Sistema deve relacionar uma Regra a um Produto sempre que a Operação corresponder, NCM e CEST.

## Processo de Envio dos Produtos ao Integrador após Atualização

### FGF

1. Seleciona todos os Produtos e a Tributação existente na Regra Fiscal de Saída vinculada ao Produto
2. Preparar a Estrutura e enviar à API.
3. Capturar o retorno, e enviar à Camada de Tratamento.
4. **Camada de Tratamento** efetua um Comparativo de Dados entre a Regra Fiscal vinculada aos Produtos e os dados retornados pelo Integrador.
   1. Se pelo menos 1 dos Parâmetros retornados pelo Integrador for diferente para um mesmo Código de Regra criado anteriormente, é necessário criar uma Nova Regra Fiscal contendo os dados iguais e os dados diferentes, relacionando este novo código ao Produto de Tributação diferente.
   2. Se não houver diferenças nos Parâmetros, atualizar a Regra Fiscal existente com os dados retornados pelo Integrador.

### iMendes

1. O Usuário deverá definir as suas Respectivas Operações e cadastrá-las em Finalidade de Operação.
2. O Usuário deverá definir os Perfis Fiscais e relacioná-los com os Fornecedores e Clientes
3. O Primeiro Lote de Consulta deverá, obrigatoriamente, ser para Regras de "Venda a Consumidor Final".
4. Seleciona os Produtos por Código de Barras, Descrição, Operação e Perfil definidos na Consulta para "Venda a Consumidor Final"
5. Prepara a Estrutura e envia à API.
6. Captura o Retorno, e envia à **Camada de Tratamento**
7. A **Camada de Tratamento** interpreta os dados retornados e efetua um Comparativo de Dados entre a Regra Fiscal vinculada aos Produtos e os dados retornados pelo Integrador.
   1. Se pelo menos 1 dos Parâmetros retornados pelo Integrador for diferente para um mesmo Código de Regra criado anteriormente, é necessário criar uma Nova Regra Fiscal contendo os dados iguais e os dados diferentes, relacionando este novo código ao Produto de Tributação diferente.
   2. Se não houver diferenças nos Parâmetros, atualizar a Regra Fiscal existente com os dados retornados pelo Integrador.
8. Grava os devidos Logs e informações.
9. As demais Operações devem ser Consultadas pelo Usuário, conforme definir

### Mix Fiscal

1. O Usuário deverá definir as suas Respectivas Operações e cadastrá-las em Finalidade de Operação, e Criar os Respectivos Cenários para Entrada e Saída.
2. O Usuário deverá definir os Perfis Fiscais e relacioná-los com os Fornecedores e Clientes.
3. Coleta todos os Produtos e Gera o Envio do Registro 0200, com os dados tributários da Regra Fiscal Padrão de "Venda a Consumidor Final" com todos os Cenários específicos criados no Passo 1.
4. Prepara a Estrutura e envia à API.
5. Captura o Retorno, e envia à **Camada de Tratamento**
6. A **Camada de Tratamento** interpreta os dados retornados e efetua um Comparativo de Dados entre a Regra Fiscal vinculada aos Produtos e os dados retornados pelo Integrador, observando cada **Cenário** criado anteriormente.
   1. Compara os dados do **Cenário de Retorno** e Dados da Regra Fiscal que possua o mesmo ID de Cenário vinculado, e define se há necessidade em criar Novas Regras Fiscais ou simplesmente atualizar as existentes com base nos Critérios:
      1. Se pelo menos 1 dos Parâmetros retornados pelo Integrador for diferente para um mesmo Código de Regra criado anteriormente, é necessário criar uma Nova Regra Fiscal contendo os dados iguais e os dados diferentes, relacionando este novo código ao Produto de Tributação diferente.
      2. Se não houver diferenças nos Parâmetros, atualizar a Regra Fiscal existente com os dados retornados pelo Integrador.
7. Grava os devidos Logs e informações.
