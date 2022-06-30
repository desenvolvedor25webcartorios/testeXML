## Biblioteca de Integração com a API do Banco do Brasil
#### Serviços integrados
- [API Cobrança](#api-cobrança)
- [API PIX](#api-pix)

***

#### Informações de uso
É necessario criar uma aplicação no site de devs do Banco do brasil ([Portal Developers BB](https://app.developers.bb.com.br/)) e realizar todos os processos de configuração ([Documentação da configuração](https://apoio.developers.bb.com.br/referency/post/6050dda3737e1c0012e2d00e))

***
#### Variáveis sensíveis
- $sEndpointApi: Endpoint da API
- $sEndpointAuth: Endpoint da autenticação
- $sClientId: Identificador público da API
- $sClientSecret: Identificador secreto da API
- $sAppKey: Chave da aplicação

***

### API Cobrança
Informações e instruções sobre a integração da API de cobrança
- [Documentação Oficial BB](https://apoio.developers.bb.com.br/referency/post/5f9c2149f39b8500120ab13c)

#### Instruções
- **Valores monetários ou percentuais:** Os valores devem ser representados por decimal e separados por ponto. Ex: 123.45.
- **Valores numéricos:** Campos numéricos nunca devem iniciar com 0, pois inválida o Json. Ex: o CPF 075.113.750-22, deverá ser informado como 7511375022.
- **Datas:** Datas sempre devem estar no formato dd.mm.aaaa . Ex.: 05.12.2020 
- **Atenção ao preenchimento dos seguintes campos:**
    - **numeroTituloCliente:** Número de identificação do boleto (correspondente ao NOSSO NÚMERO), no formato STRING, com 20 dígitos, que deverá ser formatado da seguinte forma: “000” + (número do convênio com 7 dígitos) + (número de controle com 10 dígitos - se necessário, completar com zeros à esquerda).

    - **textoCampoUtilizacaoBeneficiario:** Deve ser informado uma String com 30 caracteres em maiúsculo.
    - **textoMensagemBloquetoOcorrencia:** Mensagem definida pelo beneficiário para ser impressa no boleto; pode ter no máximo 165 caracteres, sendo que cada 55 caracteres equivalem a uma linha de mensagem.

#### > Registrar boleto de cobrança: Cria um boleto bancário, com ou sem PIX
- Função: **registrar()**
- Recebe um array com as informações do boleto de cobrança
- Realiza a validação dos dados
- Realiza a formatação dos dados
- Retorna erro ou as informações necessárias para gerar um boleto de cobrança bancária

**Array de exemplo:**
```
 $aExemploArray = [
   "numeroConvenio" => 0, 
   "numeroCarteira" => 0, 
   "numeroVariacaoCarteira" => 0, 
   "codigoModalidade" => 0, 
   "dataEmissao" => "string", 
   "dataVencimento" => "string", 
   "valorOriginal" => 0, 
   "valorAbatimento" => 0, 
   "quantidadeDiasProtesto" => 0, 
   "quantidadeDiasNegativacao" => 0, 
   "orgaoNegativador" => 0, 
   "indicadorAceiteTituloVencido" => "string", 
   "numeroDiasLimiteRecebimento" => 0, 
   "codigoAceite" => "string", 
   "codigoTipoTitulo" => 0, 
   "descricaoTipoTitulo" => "string", 
   "indicadorPermissaoRecebimentoParcial" => "string", 
   "numeroTituloBeneficiario" => "string", 
   "campoUtilizacaoBeneficiario" => "string", 
   "numeroTituloCliente" => "string", 
   "mensagemBloquetoOcorrencia" => "string", 
   "desconto" => [
        "tipo" => 0, 
        "dataExpiracao" => "string", 
        "porcentagem" => 0, 
        "valor" => 0 
    ], 
   "segundoDesconto" => [
        "dataExpiracao" => "string", 
        "porcentagem" => 0, 
        "valor" => 0 
    ], 
   "terceiroDesconto" => [
        "dataExpiracao" => "string", 
        "porcentagem" => 0, 
        "valor" => 0 
    ], 
   "jurosMora" => [
        "tipo" => 0, 
        "porcentagem" => 0, 
        "valor" => 0 
    ], 
   "multa" => [
        "tipo" => 0, 
        "data" => "string", 
        "porcentagem" => 0, 
        "valor" => 0 
    ], 
   "pagador" => [
        "tipoInscricao" => 0, 
        "numeroInscricao" => 0, 
        "nome" => "string", 
        "endereco" => "string", 
        "cep" => 0, 
        "cidade" => "string", 
        "bairro" => "string", 
        "uf" => "string", 
        "telefone" => "string" 
    ], 
   "beneficiarioFinal" => [
        "tipoInscricao" => 0, 
        "numeroInscricao" => 0, 
        "nome" => "string" 
    ], 
   "indicadorPix" => "string" 
]; 
```
**Informações sobre os dados:**

|    Nº   |      Campo      |  Obrigatório |  Observações |
|:----------:|:-------------:|:------:|:------|
| 1 |  numeroConvenio | S | Informar o número do convênio de cobrança, com 7 dígitos. |
| 2 | numeroCarteira | S | Informar o número da carteira de cobrança. |
| 3 | numeroVariacaoCarteira | S | Informar o número da variação da carteira de cobrança. |
| 4 | codigoModalidade | S | Código que identifica a característica dos boletos dentro das modalidades de cobrança existentes no BB. Domínio: 1 - Simples; 4 - Vinculada. |
| 5 | dataEmissao | S | Qualquer data, a partir da data atual, no formato “dd.mm.aaaa”. |
| 6 | dataVencimento | S | Qualquer data maior ou igual que a data de emissão, no formato “dd.mm.aaaa”. |
| 7 | valorOriginal | S | Valor do boleto no registro, em reais. Deve ser maior que a soma dos campos “valorDesconto” e “valorAbatimento”, se informados. Deve ser maior do que zero. No caso de emissão com valor equivocado, sugerimos cancelar o boleto e emitir um novo. |
| 8 | valorAbatimento | N | Valor de dedução do boleto. Se informado, deve ser maior que zero. |
| 9 | quantidadeDiasProtesto | N | Quantidade de dias após a data de vencimento do boleto para iniciar o processo de cobrança através de protesto. (3 a 5 dias **úteis** ou 6 a 29, 35, 40 ou 45 dias **corridos**) |
| 10 | indicadorAceiteTituloVencido | N | Indicador de que o boleto pode ou não ser recebido após o vencimento. Se não informado, será assumida a informação de limite de recebimento parametrizada no convênio. Quando informado “S”, em conjunto com o campo “numeroDiasLimiteRecebimento”, será definida a quantidade de dias corridos após o vencimento que este boleto ficará disponível para pagamento. Atenção: neste caso, se o campo “numeroDiasLimiteRecebimento” ficar com valor zero, também será assumida a informação de limite de recebimento parametrizada no convênio. Quando informado “N”, está definindo que o boleto NÃO permite pagamento em atraso. |
| 11 | numeroDiasLimiteRecebimento | N | Quantidade de dias corridos para recebimento após o vencimento. |
| 12 | codigoAceite | N | Código para identificar se o boleto de cobrança foi aceito (reconhecimento da dívida pelo Pagador). Domínio: A - Aceito; N - Não aceito |
| 13 | codigoTipoTitulo | S | 	Código para identificar o tipo de boleto de cobrança. |
| 14 | descricaoTipoTitulo | N | Descrição do tipo de boleto. Se houver dúvidas com relação ao tipo, consultar as regras da Febraban. |
| 15 | indicadorPermissaoRecebimentoParcial | S | Código para identificação da autorização de pagamento parcial do boleto. |
| 16 | numeroTituloBeneficiario | N | 	Número de identificação do boleto (equivalente ao SEU NÚMERO), no formato String, limitado a 15 caracteres, podendo aceitar letras (maiúsculas). |
| 17 | campoUtilizacaoBeneficiario | N | Informações adicionais sobre o beneficiário. Pode ter até 30 caracteres, em maiúsculas. |
| 18 | numeroTituloCliente | S | Número de identificação do boleto para o BB (correspondente ao NOSSO NÚMERO), no formato String, com 20 dígitos. Deve ser montado da seguinte forma: 000 + número do convênio (7 dígitos) + número de controle (10 dígitos). |
| 19 | mensagemBloquetoOcorrencia | N | Mensagem definida pelo beneficiário para ser impressa no boleto. Pode ter até 165 caracteres, sendo que cada 55 caracteres equivalem a uma linha de mensagem. Atenção: não são permitidos caracteres de escape ( \r ou \n ). |
| 20 | desconto | N | 	Define a ausência ou a forma como será concedido o desconto para o boleto. |
| 20.1 | tipo | N | Define como o desconto será concedido. Domínio: 0 - Sem desconto; 1 - Valor fixo até a data informada; 2 - percentual até a data informada. |
| 20.2 | dataExpiracao | N | Define a data de expiração do desconto (somente se tipo > 0), no formato “dd.mm.aaaa”. |
| 20.3 | porcentagem | N | 	Define a porcentagem do desconto (somente se tipo = 2). |
| 20.4 | valor | N | 	Define o valor do desconto (somente se tipo = 1). |
| 21 | segundoDesconto | N | Define a forma como será concedido um segundo desconto para o boleto. Só pode ser usado se for definido um primeiro desconto. O tipo do segundo desconto será igual ao do primeiro desconto. |
| 21.1 | dataExpiracao | N | 	Define a data de expiração do desconto, no formato “dd.mm.aaaa”. Deve ser posterior a data de expiração do primeiro desconto. |
| 21.2 | porcentagem | N | 	Define a porcentagem do desconto (somente se o tipo = 2). |
| 21.3 | valor | N | Define o valor do desconto (somente se o tipo = 1). |
| 22 | terceiroDesconto | N | Define a forma como será concedido um terceiro desconto para o boleto. Só pode ser usado se for definido um segundo desconto. O tipo do terceiro desconto será igual ao do primeiro desconto. |
| 22.1 | dataExpiracao | N | 	Define a data de expiração do desconto, no formato “dd.mm.aaaa”. Deve ser posterior a data de expiração do segundo desconto. |
| 22.2 | porcentagem | N | 	Define a porcentagem do desconto (somente se o tipo = 2). |
| 22.3 | valor | N | 	Define o valor do desconto (somente se o tipo = 1). |
| 23 | jurosMora | N | 	Define a forma que serão cobrados (ou não) os juros por atraso no pagamento. |
| 23.1 | tipo | N | Código utilizado pela FEBRABAN para identificar o tipo da taxa de juros. Domínio: 0 - Dispensar; 1 - Valor fixo por dia de atraso; 2 - Taxa mensal; 3 - Isento. |
| 23.2 | porcentagem | N | Define a taxa mensal de juros (somente informar se tipo = 2). A taxa incide sobre o valor atual do boleto (valorOriginal - valorAbatimento). |
| 23.3 | valor | N | Define o valor fixo por dia de atraso (somente informar se tipo = 1). |
| 24 | multa | N | Define a forma que será cobrada (ou não) a multa por atraso no pagamento. |
| 24.1 | tipo | N | Código utilizado pela FEBRABAN para identificar o tipo da multa. Domínio: 0 - Dispensar; 1 - Valor fixo (a partir da data estipulada no registro); 2 - Percentual (a partir da data estipulada no registro). |
| 24.2 | data | N | Define a data a partir da qual será cobrada a multa (somente informar se tipo = 1 ou 2). Deve ser posterior a data de vencimento do boleto. |
| 24.3 | porcentagem | N | Define a porcentagem da multa (somente informar se tipo = 2). A porcentagem incide sobre o valor atual do boleto (valorOriginal - valorAbatimento). |
| 24.4 | valor | N | Define o valor da multa (somente informar se tipo = 1). |
| 25 | pagador | S | 	Identifica o pagador do boleto. |
| 25.1 | tipoInscricao | S | Define o tipo do inscrição do pagador. Domínio: 1 - Pessoa física; 2 - Pessoa Jurídica. |
| 25.2 | numeroInscricao | S | Define o número de inscrição do pagador; se pessoa física, CPF; se pessoa jurídica, CNPJ. Numérico, deve ser preenchido sem ponto, hífen, barra, e sem zeros à esquerda. |
| 25.3 | nome | S | Identifica o nome do pagador. Pode ter até 30 caracteres. |
| 25.4 | endereco | S | Identifica o endereço do pagador. Pode ter até 30 caracteres. |
| 25.5 | cep | S | 	Identifica o CEP do pagador. Numérico, deve ser preenchido sem ponto ou hífen, e sem zeros à esquerda. |
| 25.6 | cidade | S | Identifica a cidade do pagador. Pode ter até 30 caracteres. |
| 25.7 | bairro | S | 	Identifica o bairro do pagador. Pode ter até 30 caracteres. |
| 25.8 | uf | S | Identifica o estado (UF) do pagador. Deve ter 2 caracteres e ser um estado válido. |
| 25.9 | telefone | N | 	Define o número de telefone do pagador. Pode ter até 30 caracteres. |
| 26 | beneficiarioFinal | N | 	Identifica o beneficiário final (antigo avalista) do boleto, se houver. |
| 26.1 | tipoInscricao | N | Define o tipo do inscrição do beneficiário final (antigo avalista). Domínio: 1 - Pessoa física; 2 - Pessoa Jurídica. |
| 26.2 | numeroInscricao | N | Define o número de inscrição do beneficiário final (antigo avalista); se pessoa física, CPF; se pessoa jurídica, CNPJ. Numérico, deve ser preenchido sem ponto, hífen, barra, e sem zeros à esquerda. |
| 26.3 | nome | N | Identifica o nome do beneficiário final (antigo avalista). Pode ter até 30 caracteres. |
| 27 | quantidadeDiasNegativacao | N | Quantidade de dias corridos depois do vencimento do boleto para a negativação automática, através do órgão negativador selecionado. Não confundir com protesto. |
| 28 | orgaoNegativador | N | Código do órgão negativador selecionado. Domínio: 10 - SERASA; 11 - QUOD. |
| 29 | indicadorPix | N | Código para informar se o boleto terá um QRCode Pix atrelado. Se não informado, ou utilizado caractere inválido, o sistema assumirá ‘N’. Atenção: conforme regulamentação do Bacen, é permitido somente para modalidade de cobrança simples (ver codigoModalidade). Domínio: S - QRCode dinâmico; N - sem Pix. |



&nbsp;
#### > Alterar boleto de cobrança: Altera um dado de um boleto já registrado, em ser (não pode estar baixado, liquidado ou protestado).
- Função: **alterar()**
- **ATENÇÃO**
    - **Só pode ser enviada uma alteração por chamada**
    - **A alteração só será aceita se enviada a partir de 30 minutos após a geração do boleto; se enviada antes desse prazo, o sistema retorna erro**
- Recebe um array com as informações da alteração
- Realiza a validação dos dados
- Realiza a formatação dos dados
- Retorna erro ou as informações da alteração

**Array de exemplo:**
```
$aExemploArray = [
   "numeroBoleto" => 0, 
   "numeroConvenio" => 0, 
   "indicadorNovaDataVencimento" => "string", 
   "alteracaoData" => [
        "novaDataVencimento" => "string" 
    ], 
   "indicadorAtribuirDesconto" => "string", 
   "desconto" => [
        "tipoPrimeiroDesconto" => 0, 
        "valorPrimeiroDesconto" => 0, 
        "percentualPrimeiroDesconto" => 0, 
        "dataPrimeiroDesconto" => "string", 
        "tipoSegundoDesconto" => 0, 
        "valorSegundoDesconto" => 0, 
        "percentualSegundoDesconto" => 0, 
        "dataSegundoDesconto" => "string", 
        "tipoTerceiroDesconto" => 0, 
        "valorTerceiroDesconto" => 0, 
        "percentualTerceiroDesconto" => 0, 
        "dataTerceiroDesconto" => "string" 
    ], 
   "indicadorAlterarDesconto" => "string", 
   "alteracaoDesconto" => [
        "tipoPrimeiroDesconto" => 0, 
        "novoValorPrimeiroDesconto" => 0, 
        "novoPercentualPrimeiroDesconto" => 0, 
        "novaDataLimitePrimeiroDesconto" => "string", 
        "tipoSegundoDesconto" => 0, 
        "novoValorSegundoDesconto" => 0, 
        "novoPercentualSegundoDesconto" => 0, 
        "novaDataLimiteSegundoDesconto" => "string", 
        "tipoTerceiroDesconto" => 0, 
        "novoValorTerceiroDesconto" => 0, 
        "novoPercentualTerceiroDesconto" => 0, 
        "novaDataLimiteTerceiroDesconto" => "string" 
    ], 
   "indicadorAlterarDataDesconto" => "string", 
   "alteracaoDataDesconto" => [
        "novaDataLimitePrimeiroDesconto" => "string", 
        "novaDataLimiteSegundoDesconto" => "string", 
        "novaDataLimiteTerceiroDesconto" => "string" 
    ], 
   "indicadorProtestar" => "string", 
   "protesto" => [
        "quantidadeDiasProtesto" => 0 
    ], 
   "indicadorSustacaoProtesto" => "string", 
   "indicadorCancelarProtesto" => "string", 
   "indicadorIncluirAbatimento" => "string", 
   "abatimento" => [
        "valorAbatimento" => 0 
    ], 
   "indicadorAlterarAbatimento" => "string", 
   "alteracaoAbatimento" => [
        "novoValorAbatimento" => 0 
    ], 
   "indicadorCobrarJuros" => "string", 
   "juros" => [
        "tipoJuros" => 0, 
        "valorJuros" => 0, 
        "taxaJuros" => 0 
    ], 
   "indicadorDispensarJuros" => "string", 
   "indicadorCobrarMulta" => "string", 
   "multa" => [
        "tipoMulta" => 0, 
        "valorMulta" => 0, 
        "dataInicioMulta" => "string", 
        "taxaMulta" => 0 
    ], 
   "indicadorDispensarMulta" => "string", 
   "indicadorNegativar" => "string", 
   "negativacao" => [
        "quantidadeDiasNegativacao" => 0, 
        "tipoNegativacao" => 0 
    ], 
   "indicadorAlterarSeuNumero" => "string", 
   "alteracaoSeuNumero" => [
        "codigoSeuNumero" => "string" 
    ], 
   "indicadorAlterarEnderecoPagador" => "string", 
   "alteracaoEndereco" => [
        "enderecoPagador" => "string", 
        "bairroPagador" => "string", 
        "cidadePagador" => "string", 
        "UFPagador" => "string", 
        "CEPPagador" => 0 
    ], 
   "indicadorAlterarPrazoBoletoVencido" => "string", 
   "alteracaoPrazo" => [
        "quantidadeDiasAceite" => 0 
    ] 
];
```

**Informações sobre os dados:**

|    Nº   |      Campo      |  Obrigatório |  Observações |
|:----------:|:-------------:|:------:|:------|
| 1 |  numeroConvenio | S | Identifica o número do convênio de cobrança. |
| 2 | indicadorCancelarAbatimento | S | Indica a intenção (ou não) de cancelar o abatimento concedido a um boleto. |
| 3 | alteracaoAbatimento | N | Sem observação. |
| 3.1 | novoValorAbatimento | N | Valor de dedução do boleto. Se informado, deve ser maior que zero. |
| 4 | indicadorAlterarDataDesconto | S | Indica a intenção (ou não) de alterar a data de desconto(s) concedido(s) em um boleto. Se sim, deve ser usado em conjunto com o campo alteracaoDataDesconto. |
| 5 | alteracaoDataDesconto | N | Observar as regras para datas de desconto no campo desconto da Geração de Boletos. |
| 5.1 | novaDataLimitePrimeiroDesconto | N | Qualquer data maior ou igual que a data de emissão, no formato “dd.mm.aaaa”. |
| 5.2 | novaDataLimiteSegundoDesconto | N | Qualquer data maior ou igual que a data de emissão, no formato “dd.mm.aaaa”. |
| 5.3 | novaDataLimiteTerceiroDesconto | N | Qualquer data maior ou igual que a data de emissão, no formato “dd.mm.aaaa”. |
| 6 | indicadorAlterarDesconto | S | Indica a intenção (ou não) de alterar o desconto concedido em um boleto. Se sim, deve ser usado em conjunto com o campo alteracaoDesconto. |
| 7 | alteracaoDesconto | N | 	Observar as instruções sobre o campo desconto na Geração de Boletos. |
| 8 | indicadorAlterarEnderecoPagador | S | 	Indica a intenção (ou não) de alterar o endereço do pagador no boleto. Se sim, deve ser usado em conjunto com o campo alteracaoEndereco. Atenção: a alteração do endereço não implica em nova geração ou envio de boleto. Isso é responsabilidade do beneficiário. |
| 9 | alteracaoEndereco | N | 	Observar as instruções sobre o campo pagador na Geração de Boletos. |
| 10 | indicadorAlterarPrazoBoletoVencido | S | Indica a intenção (ou não) de alterar o prazo para recebimento de boleto vencido. Se sim, deve ser usado em conjunto com o campo alteracaoPrazo. |
| 11 | alteracaoPrazo | N | Sem observação. |
| 11.1 | quantidadeDiasAceite | N | Define a quantidade de dias corridos após o vencimento em que o boleto poderá ser recebido. Equivalente ao campo numeroDiasLimiteRecebimento na Geração de Boletos. |
| 12 | indicadorAlterarSeuNumero | S | Indica a intenção (ou não) de alterar o campo numeroTituloBeneficiario (equivalente ao SEU NÚMERO) no boleto. Se sim, deve ser usado em conjunto com o campo alteracaoSeuNumero. |
| 13 | alteracaoSeuNumero | N | Observar as instruções sobre o campo numeroTituloBeneficiario na Geração de Boletos. |
| 14 | indicadorAtribuirDesconto | S | Indica a intenção (ou não) de atribuir desconto ao boleto. Se sim, deve ser usado em conjunto com o campo desconto. |
| 15 | 	desconto | N | Observar as instruções sobre o campo desconto na Geração de Boletos. Lembrete: o tipo definido para o primeiro desconto será replicado no segundo e no terceiro desconto (se informados). |
| 16 | indicadorCancelarProtesto | S | Indica a intenção (ou não) de cancelar uma instrução de protesto enviada ao BB na mesma data ou que ainda não tenha sido processada pelo BB. |
| 17 | indicadorCobrarJuros | S | Indica a intenção (ou não) de cobrar juros no boleto. Se sim, deve ser usado em conjunto com o campo juros. |
| 18 | juros | N | Observar as instruções sobre o campo juros na Geração de Boletos. |
| 19 | indicadorCobrarMulta | S | 	Indica a intenção (ou não) de cobrar multa no boleto. Se sim, deve ser usado em conjunto com o campo multa. |
| 20 | multa | N | Observar as instruções sobre o campo multa na Geração de Boletos. |
| 21 | indicadorDispensarJuros | S | 	Indica a intenção (ou não) de dispensar os juros sobre o boleto. |
| 22 | indicadorDispensarMulta | S | 	Indica a intenção (ou não) de dispensar a multa sobre o boleto. |
| 23 | indicadorIncluirAbatimento | S | Indica a intenção (ou não) de incluir abatimento em um boleto. Se sim, deve ser usado em conjunto com o campo abatimento. |
| 24 | abatimento | N | Sem observação. |
| 24.1 | valorAbatimento | N | Define o valor a ser concedido como abatimento. |
| 25 | indicadorNegativar | S | Indica a intenção (ou não) de negativar ou cancelar a negativação do boleto. Se sim, deve ser usado em conjunto com o campo negativacao. Não confundir com protesto. |
| 26 | negativacao | N | Sem observação. |
| 26.1 | quantidadeDiasNegativacao | N | Quantidade de dias, após o vencimento do boleto, em que o boleto será negativado. |
| 26.2 | tipoNegativacao | N | Código para identificação do tipo de negativação que deverá ser aplicada ao boleto. Domínio: 1 - incluir; 2 - alterar prazo; 3 - cancelar; 4 - excluir. |
| 27 | indicadorNovaDataVencimento | S | Indica a intenção (ou não) de atribuir nova data de vencimento ao boleto. Se sim, deve ser usado em conjunto com o campo alteracaoData. |
| 28 | alteracaoData | N | Sem observação |
| 28.1 | novaDataVencimento | N | 	Define a nova data de vencimento, formato “dd.mm.aaaa”. |
| 29 | indicadorProtestar | S | 	Indica a intenção (ou não) de protestar o boleto. Não confundir com negativação. |
| 30 | protesto | N | Sem observação. |
| 30.1 | quantidadeDiasProtesto | N | Observe as instruções sobre o campo quantidadeDiasProtesto na Geração de Boletos. |
| 31 | indicadorSustacaoProtesto | S | 	Indica a intenção (ou não) de sustar/cancelar um comando de protesto que já tenha sido processado pelo BB. |



&nbsp;
#### > Buscar boleto de cobrança: Retorna um JSON contendo os dados de um boleto de cobrança específico.
- Função: **buscar()**
- Recebe um array com as informações para buscar o boleto de cobrança
- Realiza a validação dos dados
- Retorna erro ou os dados de um boleto de cobrança específico

**Array de exemplo:**
```
 $aExemploArray = [
   "numeroBoleto" => 0, 
   "numeroConvenio" => 0
]; 
```
**Informações sobre os dados:**
|    Nº   |      Campo      |  Obrigatório |  Observações |
|:----------:|:-------------:|:------:|:------|
| 1 |  numeroBoleto | S | Informar o número do boleto de cobrança. |
| 2 | numeroConvenio | S | Informar o número do convênio de cobrança, com 7 dígitos. |



&nbsp;
#### > Baixa/Cancelamento de um boleto de cobrança: Executa a baixa (cancelamento) de um boleto já registrado e ainda em ser (não pode estar liquidado, protestado ou já baixado).
- Função: **baixar()**
- **ATENÇÃO**
    - **Um pedido de baixa só será aceito a partir de 30 minutos da geração do mesmo boleto. Se solicitado antes desse prazo, o sistema retornará erro.**
- Recebe um array com as informações da baixa/cancelamento
- Realiza a validação dos dados
- Retorna erro ou as informações da baixa/cancelamento

**Array de exemplo:**
```
 $aExemploArray = [
   "numeroBoleto" => 0, 
   "numeroConvenio" => 0
]; 
```
**Informações sobre os dados:**
|    Nº   |      Campo      |  Obrigatório |  Observações |
|:----------:|:-------------:|:------:|:------|
| 1 |  numeroBoleto | S | Informar o número do boleto de cobrança. |
| 2 | numeroConvenio | S | Informar o número do convênio de cobrança, com 7 dígitos. |



&nbsp;
#### > Listar boletos de cobrança: Retorna um JSON contendo os boletos de cobrança.
- Função: **listar()**
- **ATENÇÃO**
    - **Os parâmetros montam a URL que fará a chamada via GET; se for fornecido algum parâmetro inexistente, ou a combinação dos parâmetros não encontra resultado (lista vazia), o retorno será 404 - not found**
    - **Os dados fornecidos pelos parâmetros serão pesquisados no formato “E” (parâmetro1 E parâmetro2 E…); assim, algumas combinações podem não retornar dados (lista vazia). Por exemplo: cpfPagador E cnpjPagador (ou o pagador é pessoa física ou é pessoa jurídica); indicadorSituacao = “A” (boletos em ser) E codigoEstadoTituloCobranca = 7 (baixado)**
- Recebe um array com as informações da listagem
- Realiza a validação dos dados
- Realiza a formatação dos dados
- Retorna erro ou um as informações dos boletos de cobrança

**Array de exemplo:**
```
 $aExemploArray = [
   "indicadorSituacao" => string, 
   "contaCaucao" => 0, 
   "agenciaBeneficiario" => 0, 
   "contaBeneficiario" => 0, 
   "carteiraConvenio" => 0, 
   "variacaoCarteiraConvenio" => 0, 
   "modalidadeCobranca" => 0, 
   "cnpjPagador" => 0, 
   "digitoCNPJPagador" => 0, 
   "cpfPagador" => 0, 
   "digitoCPFPagador" => 0, 
   "dataInicioVencimento" => string, 
   "dataFimVencimento" => string, 
   "dataInicioRegistro" => string, 
   "dataFimRegistro" => string, 
   "dataInicioMovimento" => string, 
   "dataFimMovimento" => string, 
   "codigoEstadoTituloCobranca" => 0, 
   "boletoVencido" => string, 
   "indice" => 0
]; 
```
**Informações sobre os dados:**
|    Nº   |      Campo      |  Obrigatório |  Observações |
|:----------:|:-------------:|:------:|:------|
| 1 | indicadorSituacao | S | Define a faixa de boletos a ser pesquisada. Sempre em MAIÚSCULA. Domínio: A - boletos em ser; B - boletos baixados, liquidados ou protestados |
| 2 | agenciaBeneficiario | S | Indica o número da agência cadastrada como beneficiária do convênio, sem o dígito e sem zeros a esquerda. |
| 3 | contaBeneficiario | S | Indica o número da conta cadastrada como beneficiária do convênio, sem o dígito e sem zeros a esquerda. |
| 4 | carteiraConvenio | N | Indica o número da carteira do convênio de cobrança. |
| 5 | variacaoCarteiraConvenio | N | 	Indica o número da variação do convênio de cobrança. |
| 6 | modalidadeCobranca | N | Indica a modalidade de cobrança na qual o boleto está cadastrado no BB. Domínio: 1 - Simples; 4 - Vinculada. |
| 7 | cnpjPagador | N | Indica o CNPJ do pagador a ser pesquisado, sem o dígito e sem zeros a esquerda. Informar em conjunto com digitoCnpjPagador. |
| 8 | digitoCNPJPagador | N | 	Indica o dígito do CNPJ do pagador a ser pesquisado, sem zeros a esquerda. Informar em conjunto com cnpjPagador. |
| 9 | cpfPagador | N | Indica o CPF do pagador a ser pesquisado, sem o dígito e sem zeros a esquerda. Informar em conjunto com digitoCpfPagador. |
| 10 | digitoCPFPagador | N | 	Indica o dígito do CPF do pagador a ser pesquisado, sem zeros a esquerda. Informar em conjunto com cpfPagador. |
| 11 | dataInicioVencimento | N | 	Data inicial de vencimento que delimita o período da consulta, formato dd.mm.aaaa. Se informada data posterior a atual, o campo dataFimVencimento deve ser preenchido com data posterior ou igual a dataInicioVencimento. Se informada data anterior ou igual a data atual e o campo dataFimVencimento não for informado, o sistema assume a data atual como dataFimVencimento. |
| 12 | dataFimVencimento | N | Data final de vencimento que delimita o período da consulta, formato dd.mm.aaaa. Se informada, deve ser uma data posterior ou igual a dataInicioVencimento. |
| 13 | dataInicioRegistro | N | 	Data inicial de registro que delimita o período da consulta, formato dd.mm.aaaa. Deve ser anterior ou igual a data atual. |
| 14 | dataFimRegistro | N | Data fim de registro que delimita o período da consulta, formato dd.mm.aaaa. Se informada, deve ser posterior ou igual a dataInicioRegistro, e igual ou anterior à data atual. Se não informada, o sistema assume a data atual. |
| 15 | dataInicioMovimento | N | 	Data início de movimento que delimita o período de consulta de boletos baixados, liquidados ou protestados, formato dd.mm.aaaa. Deve ser anterior ou igual à data atual. |
| 16 | dataFimMovimento | N | Data fim de movimento que delimita o período de consulta de boletos baixados, liquidados ou protestados, formato dd.mm.aaaa. Se informada, deve ser posterior ou igual a dataInicioMovimento, e igual ou anterior à data atual. Se não informada, o sistema assume a data atual. |
| 17 | codigoEstadoTituloCobranca | N | Código da situação atual do boleto. Domínios: 1 - NORMAL, 2 - MOVIMENTO CARTORIO, 3 - EM CARTORIO, 4 - TITULO COM OCORRENCIA DE CARTORIO, 5 - PROTESTADO ELETRONICO, 6 - LIQUIDADO, 7 - BAIXADO, 8 - TITULO COM PENDENCIA DE CARTORIO, 9 - TITULO PROTESTADO MANUAL, 10 - TITULO BAIXADO/PAGO EM CARTORIO, 11 - TITULO LIQUIDADO/PROTESTADO, 12 - TITULO LIQUID/PGCRTO, 13 - TITULO PROTESTADO AGUARDANDO BAIXA, 14 - TITULO EM LIQUIDACAO, 15 - TITULO AGENDADO BB, 16 - TITULO CREDITADO, 17 - PAGO EM CHEQUE - AGUARD.LIQUIDACAO, 18 - PAGO PARCIALMENTE, 19 - PAGO PARCIALMENTE CREDITADO, 21 - TITULO AGENDADO OUTROS BANCOS. |
| 18 | boletoVencido | N | 	Define se a pesquisa trará apenas boletos vencidos ou não. Sempre MAIÚSCULA. |
| 19 | indice | N | Representa o índice da listagem pelo qual sua pesquisa se iniciará, podendo retornar até 300 registros por chamada. O default é 0, o que trará os resultados de 0 a 299 (se houver). Quando o resultado da pesquisa tiver mais que 300 registros, na resposta, o campo indicadorContinuidade retornará com “S”. Recomendamos utilizar o valor do campo proximoIndice (informado na resposta), no campo indice da próxima chamada, para retornar com os próximos registros. |



&nbsp;
#### > Consultar Pix de um boleto de cobrança: Retorna um JSON contendo os dados de um Pix vinculado a um boleto de cobrança.
- Função: **consultarPixBoleto()**
- Recebe um array com as informações do boleto de cobrança
- Realiza a validação dos dados
- Retorna erro ou as informações do Pix vinculado

**Array de exemplo:**
```
 $aExemploArray = [
   "numeroBoleto" => 0, 
   "numeroConvenio" => 0
]; 
```
**Informações sobre os dados:**
|    Nº   |      Campo      |  Obrigatório |  Observações |
|:----------:|:-------------:|:------:|:------|
| 1 |  numeroBoleto | S | Informar o número do boleto de cobrança. |
| 2 | numeroConvenio | S | Informar o número do convênio de cobrança, com 7 dígitos. |



&nbsp;
#### > Gerar Pix para um boleto de cobrança: Retorna um JSON contendo os dados de do Pix gerado.
- Função: **gerarPixBoleto()**
- **ATENÇÃO**
    - **Só é possível gerar Pix para um boleto de cobrança para boleto que não possui Pix vinculado** 
- Recebe um array com as informações do boleto de cobrança
- Realiza a validação dos dados
- Retorna erro ou as informações do Pix gerado

**Array de exemplo:**
```
 $aExemploArray = [
   "numeroBoleto" => 0, 
   "numeroConvenio" => 0
]; 
```
**Informações sobre os dados:**
|    Nº   |      Campo      |  Obrigatório |  Observações |
|:----------:|:-------------:|:------:|:------|
| 1 |  numeroBoleto | S | Informar o número do boleto de cobrança. |
| 2 | numeroConvenio | S | Informar o número do convênio de cobrança, com 7 dígitos. |



&nbsp;
#### > Cancelar Pix de um boleto de cobrança: Retorna um JSON contendo os dados de do Pix cancelado.
- Função: **cancelarPixBoleto()**
- Recebe um array com as informações do boleto de cobrança
- Realiza a validação dos dados
- Retorna erro ou as informações do Pix cancelado

**Array de exemplo:**
```
 $aExemploArray = [
   "numeroBoleto" => 0, 
   "numeroConvenio" => 0
]; 
```
**Informações sobre os dados:**
|    Nº   |      Campo      |  Obrigatório |  Observações |
|:----------:|:-------------:|:------:|:------|
| 1 |  numeroBoleto | S | Informar o número do boleto de cobrança. |
| 2 | numeroConvenio | S | Informar o número do convênio de cobrança, com 7 dígitos. |

***

### API Pix
Informações e instruções sobre a integração da API do Pix
[Documentação Oficial BB](https://apoio.developers.bb.com.br/referency/post/5fe0853e156f4c0012e4e2a9)
#### Instruções
- **Valores monetários ou percentuais:** Os valores devem ser representados por decimal e separados por ponto. Ex: 123.45.



&nbsp;
#### > Criar cobrança Pix: Cria uma cobrança imediata via Pix
- Função: **criarCobrancaPix()**
- Recebe um array com as informações do Pix
- Realiza a validação dos dados
- Retorna erro ou as informações do Pix criado

**Array de exemplo:**
```
 $aExemploArray = [
   "calendario" => [
        "expiracao" => 0 
    ], 
   "devedor" => [
        "cnpj" => "string", 
        "nome" => "string" 
    ], 
   "valor" => [
        "original" => 0, 
        "modalidadeAlteracao" => 0
    ], 
   "chave" => "string", 
   "solicitacaoPagador" => "string", 
   "infoAdicionais" => [
        [
            "nome" => "string", 
            "valor" => "string" 
        ],
    ] 
]; 
```
**Informações sobre os dados:**
|    Nº   |    Campo   |    Obrigatório   |  Observações |
|:----------:|:-------------:|:-------------:|:------|
| 1 |  valor | S | Valores monetários referentes à cobrança. Todos os campos que indicam valores monetários obedecem ao formato do ID 54 da especificação EMV/BR Code para QR Codes. O separador decimal é o caractere ponto. Não é aplicável utilizar separador de milhar. Exemplos de valores aderentes ao padrão: “0.00”, “1.00”, “123.99”, “123456789.23”. |
| 1.1 |  original | S | Valor original da cobrança. |
| 2 | chave | S | Chave DICT do recebedor. |
| 3 | calendario | N | Os campos aninhados sob o identificador calendário organizam informações a respeito de controle de tempo da cobrança. |
| 3.1 | expiracao | N | Tempo de vida da cobrança, especificado em segundos a partir da data de criação, para que o pagamento da cobrança possa ser realizado (Calendario.criacao). Se não for informado, assume-se a duração de 86400 segundos, que corresponde a 24 horas.Exemplo: 3600 (indica validade de 1 hora). |
| 4 | devedor | N | Os campos aninhados sob o objeto devedor são opcionais e identificam o devedor, ou seja, a pessoa ou a instituição a quem a cobrança está endereçada. Não identifica, necessariamente, quem irá efetivamente realizar o pagamento. Um CPF pode ser o devedor de uma cobrança, mas pode acontecer de outro CPF realizar, efetivamente, o pagamento do documento. Não é permitido que o campo **devedor.cpf** e campo **devedor.cnpj** estejam preenchidos ao mesmo tempo. Se o campo **devedor.cnpj** está preenchido, então o campo **devedor.cpf** não pode estar preenchido, e vice-versa. Se o campo **devedor.nome** está preenchido, então deve existir ou um devedor.cpf ou um campo **devedor.cnpj** preenchido. |
| 4.1 | cpf ou cnpj | N | CPF ou CNPJ do usuário. |
| 4.2 | nome | N | Nome do usuário. |
| 5 | solicitacaoPagador | N | Solicitação ao pagador. |
| 6 | infoAdicionais | N | Cada respectiva informação adicional contida na lista (nome e valor) deve ser apresentada ao pagador. |
| 6.1 | nome | N | Nome do campo. |
| 6.2 | valor | N | Dados do campo. |



&nbsp;
#### > Revisar cobrança Pix: Revisa uma cobrança imediata via Pix
- Função: **revisarCobrancaPix()**
- Recebe um array com as informações do Pix
- Realiza a validação dos dados
- Retorna erro ou as informações do Pix revisado

**Array de exemplo:**
```
 $aExemploArray = [
    "txid" => "string",
    "status" => "string", 
    "devedor" => [
        "cpf" => "string", 
        "nome" => "string" 
    ], 
   "valor" => [
        "original" => 0
    ], 
   "solicitacaoPagador" => "string"
]; 
```
**Informações sobre os dados:**
|    Nº   |    Campo   |    Obrigatório   |  Observações |
|:----------:|:-------------:|:-------------:|:------|
| 1 |  valor | N | Valores monetários referentes à cobrança. Todos os campos que indicam valores monetários obedecem ao formato do ID 54 da especificação EMV/BR Code para QR Codes. O separador decimal é o caractere ponto. Não é aplicável utilizar separador de milhar. Exemplos de valores aderentes ao padrão: “0.00”, “1.00”, “123.99”, “123456789.23”. |
| 1.1 |  original | N | Valor original da cobrança. |
| 2 | chave | N | Chave DICT do recebedor. |
| 3 | calendario | N | Os campos aninhados sob o identificador calendário organizam informações a respeito de controle de tempo da cobrança. |
| 3.1 | expiracao | N | Tempo de vida da cobrança, especificado em segundos a partir da data de criação, para que o pagamento da cobrança possa ser realizado (Calendario.criacao). Se não for informado, assume-se a duração de 86400 segundos, que corresponde a 24 horas.Exemplo: 3600 (indica validade de 1 hora). |
| 4 | devedor | N | Os campos aninhados sob o objeto devedor são opcionais e identificam o devedor, ou seja, a pessoa ou a instituição a quem a cobrança está endereçada. Não identifica, necessariamente, quem irá efetivamente realizar o pagamento. Um CPF pode ser o devedor de uma cobrança, mas pode acontecer de outro CPF realizar, efetivamente, o pagamento do documento. Não é permitido que o campo **devedor.cpf** e campo **devedor.cnpj** estejam preenchidos ao mesmo tempo. Se o campo **devedor.cnpj** está preenchido, então o campo **devedor.cpf** não pode estar preenchido, e vice-versa. Se o campo **devedor.nome** está preenchido, então deve existir ou um devedor.cpf ou um campo **devedor.cnpj** preenchido. |
| 4.1 | cpf ou cnpj | N | CPF ou CNPJ do usuário. |
| 4.2 | nome | N | Nome do usuário. |
| 5 | solicitacaoPagador | N | Solicitação ao pagador. |
| 6 | infoAdicionais | N | Cada respectiva informação adicional contida na lista (nome e valor) deve ser apresentada ao pagador. |
| 6.1 | nome | N | Nome do campo. |
| 6.2 | valor | N | Dados do campo. |
| 7 | status | N | Novo status da cobrança. O único status que pode ser informado na revisão da Cobrança é o REMOVIDA_PELO_USUARIO_RECEBEDOR. |
| 8 | txid | S | O campo txid determina o identificador da transação. |



&nbsp;
#### > Consultar cobrança Pix: Consulta uma cobrança através de um determinado txid.
- Função: **consultarCobrancaPix()**
- Recebe um array com as informações do Pix
- Realiza a validação dos dados
- Retorna erro ou as informações do Pix consultado

**Array de exemplo:**
```
 $aExemploArray = [
   "txid" => "string",
   "revisao" => 0,
]; 
```
**Informações sobre os dados:**
|    Nº   |    Campo   |    Obrigatório   |  Observações |
|:----------:|:-------------:|:-------------:|:------|
| 1 | txid | S | O campo txid determina o identificador da transação. |
| 2 | revisao | N | Permite recuperar revisões anteriores de uma cobrança. Na ausência desse parâmetro, sempre será retornada a cobrança conforme consta em sua última revisão. |



&nbsp;
#### > Consultar Pix recebidos: Retorna um JSON contendo os Pix recebidos.
- Função: **consultarPixRecebidos()**
- Recebe um array com as informações de consulta
- Realiza a validação dos dados
- Realiza a formatação dos dados
- Retorna erro ou as informações dos Pix recebidos

**Array de exemplo:**
```
 $aExemploArray = [
   "inicio" => "string",
   "fim" => "string",
   "paginaAtual" => 0,
]; 
```
**Informações sobre os dados:**
|    Nº   |    Campo   |    Obrigatório   |  Observações |
|:----------:|:-------------:|:-------------:|:------|
| 1 | inicio | S | Data inicial da pesquisa. Intervalo máximo: 5 dias. |
| 2 | fim | S | Data final da pesquisa. Intervalo máximo: 5 dias. |
| 3 | paginaAtual | N | Definir página consultada. O padrão é 0. |



&nbsp;
#### > Consultar Pix: Retorna um JSON contendo o Pix consultado.
- Função: **consultarPix()**
- Recebe um array com as informações de consulta
- Realiza a validação dos dados
- Retorna erro ou as informações do Pix consultado

**Array de exemplo:**
```
 $aExemploArray = [
   "e2eid" => "string"
]; 
```
**Informações sobre os dados:**
|    Nº   |    Campo   |    Obrigatório   |  Observações |
|:----------:|:-------------:|:-------------:|:------|
| 1 | e2eid | S | 	Id fim a fim da transação que transita na PACS002, PACS004 e PACS008. |



&nbsp;
#### > Solicitar devolução do Pix: Retorna um JSON contendo os dados da solicitação de devolução.
- Função: **consultarPix()**
- Recebe um array com as informações do Pix
- Realiza a validação dos dados
- Retorna erro ou as informações da solicitação de devolução

**Array de exemplo:**
```
 $aExemploArray = [
   "e2eid" => "string",
   "valor" => 0.00
]; 
```
**Informações sobre os dados:**
|    Nº   |    Campo   |    Obrigatório   |  Observações |
|:----------:|:-------------:|:-------------:|:------|
| 1 | e2eid | S | Id fim a fim da transação que transita na PACS002, PACS004 e PACS008. |
| 2 | valor | S | Valor solicitado para devolução. A soma dos valores de todas as devolucões não podem ultrapassar o valor total do Pix. |



&nbsp;
#### > Consultar solicitação de devolução do Pix: Retorna um JSON contendo os dados da solicitação de devolução.
- Função: **consultarDevolucaoPix()**
- Recebe um array com as informações de consulta
- Realiza a validação dos dados
- Retorna erro ou as informações da solicitação de devolução

**Array de exemplo:**
```
 $aExemploArray = [
   "e2eid" => "string",
   "id" => "string"
]; 
```
**Informações sobre os dados:**
|    Nº   |    Campo   |    Obrigatório   |  Observações |
|:----------:|:-------------:|:-------------:|:------|
| 1 | e2eid | S | Id fim a fim da transação que transita na PACS002, PACS004 e PACS008. |
| 2 | id | S | Id da Devolução. |
***
