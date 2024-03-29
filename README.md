
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

ret = objUninfe.carta_correcao(chaveNFe , cMotivo , nSequenciaCCe )
?ret.Mensagem
IF ret.Sucesso
   ?"Protocolo: " + ret.Protocolo
endif 

```

### Cancelamento
```Cobol
chaveNFe     = "00000000000000000000000000000000000000000000"
protocoloNFe = "000000000000000"
cMotivo      = "Nota Fiscal Incorreta"

ret = objUninfe.cancelar(chaveNFe, protocoloNFe, cMotivo)

IF ret.Sucesso
   ?"Cancelamento Aprovado: " + str(ret.Codigo)
   ?ret.Protocolo
else
   ?"Cancelamento Rejeitado"
   ?ret.Mensagem
endif 

```

### Manifestação do Destinatário
```Cobol
chaveNFe        = "00000000000000000000000000000000000000000000"
cEvento         = "Ciência da Emissão"
cJustificativa  = ""

ret = objUninfe.manifestar_nfe(chaveNFe, cEvento, cJustificativa)  

IF ret.Sucesso
   ?"Manifestação Aprovada: " + ret.Mensagem
   ?ret.Protocolo
else
   ?"Manifestação Rejeitada"
   ?ret.Mensagem
endif 

```

### Consultar NSU
Consulta as notas emitidas contra o CNPJ
Enviar a primeira consulta com o numero Zero, depois pegar o numero da ultima consulta (ult_nsu) e ir baixando as notas até chegar no NSU Máximo (max_nsu)

```Cobol
objUninfe.pasta_distribuicao = "C:\arquivos-xml\"

nsu = 0
ret = objUninfe.consultar_distribuicao(nsu)

IF ret.Sucesso
   ?"Numero maximo para consulta: "  + objUninfe.max_nsu)
   ?"Numero para proxima consulta: " + objUninfe.ult_nsu)
   nsu = INT(objUninfe.ult_nsu)
ELSE
  ?"Erro ao consultar NSU: " + ret.Mensagem
ENDIF  

```


