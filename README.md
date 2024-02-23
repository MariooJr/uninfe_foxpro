
# UniNFe FoxPro

Classe para realizar a integração com a DLL do UniNFe utilizando Visual FoxPro.

## Instalação

Baixar e Instalar a DLL do UNINFE

[DLL UniNFe - Integração para transmissão dos XML´s da NFe e NFCe / Integração ERP](https://unimake.com.br/downloads)
## Funcionalidades

Integração da Nota Fiscal Eletronica com o Sefaz nos ambientes de Homologação e Produção

- Carta de Correção
- Cancelamento
- Inutilização
- Autorização NF-e

Todas as funções retornam um objeto com as seguintes propriedades

| Propriedade   | Tipo       | Descrição                           |
| :---------- | :--------- | :---------------------------------- |
| Chave | string | Chave de Acesso da NF-e |
| Codigo | Integer | Código do Retorno do Sefaz |
| DataRecibo | string | Data e Hora do Evento |
| Mensagem | string | Descrição do Retorno do Sefaz |
| Protocolo | string | Protocolo de Autorização |
| Sucesso | Bool | Se a operação foi executada com sucesso ou não |
| Xml | string | Conteúdo do Arquivo XML retornado pelo Sefaz |

## Como utilizar
Instanciar a Classe ou adicionar ao form

```Cobol
  objUninfe = NEWOBJECT("nfe","c:\uninfe_foxpro\uninfe")
```

Configurações Iniciais

```Cobol
  objUninfe.AMBIENTE    = "HOMOLOGACAO/PRODUCAO"
  objUninfe.CERTIFICADO = "Passar o ID do Certificado ou deixar em branco"
  objUninfe.CNPJ        = "00000000000000"
  objUninfe.PASTA_NFE   = "C:\NFE\"
  objUninfe.UF          = "SP"
```
- Ambiente: HOMOLOGACAO ou PRODUCAO
- Certificado: Passar o ID do Certificado caso tenha
- CNPJ: CNPJ do Emitente
- PASTA_NFE: Pasta onde ficará salvo o xml de retorno do Sefaz
- UF: UF do Emitente

### Inutilizando Numerção
```Cobol
numeroInicio = 10
numeroFinal  = 10
serieNFe     = 1

retSefaz = objUninfe.inutilizar(numeroInicio, numeroFinal, serieNFe)
messagebox(retSefaz.Mensagem)

```

### Carta de Correção
```Cobol
chaveNFe = "00000000000000000000000000000000000000000000"
cMotivo  = "Nome da transportadora incorreto; Segue o nome correto: Joao do Frete"
nSequenciaCCe  = 3

ret = thisform.nfe1.carta_correcao(chaveNFe , cMotivo , nSeqCCe )
thisform.retorno(ret.Mensagem)

```

### Cancelamento
```Cobol
chaveNFe     = "00000000000000000000000000000000000000000000"
protocoloNFe = "000000000000000"
cMotivo      = "Nota Fiscal Incorreta"

ret = thisform.nfe1.cancelar(chaveNFe, protocoloNFe, cMotivo)

IF ret.Sucesso
   ?"Cancelamento Aprovado: " + str(ret.Codigo)
   ?ret.Protocolo
else
   ?"Cancelamento Rejeitado"
   ?ret.Mensagem
endif 

```
