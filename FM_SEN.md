```
ABAP
FUNCTION zfmm_teste_pocreate.
*"----------------------------------------------------------------------
*"*"Interface local:
*"  IMPORTING
*"     VALUE(I_TP_PEDIDO) TYPE  BSART
*"     VALUE(I_CNPJ) TYPE  CHAR14
*"     VALUE(I_EMPRESA) TYPE  BUKRS
*"     VALUE(I_PAGAMENTO) TYPE  DZBDET
*"     VALUE(I_TEXTO) TYPE  CHAR255
*"     VALUE(I_MATERIAL) TYPE  MATNR
*"     VALUE(I_PRECO) TYPE  BPREI
*"     VALUE(I_CENTRO) TYPE  EWERK
*"     VALUE(I_CENTROCUST) TYPE  KOSTL OPTIONAL
*"     VALUE(I_VLLIQUID) TYPE  BWERT
*"     VALUE(I_CODIMPOST) TYPE  MWSKZ
*"  EXPORTING
*"     VALUE(E_RETURN) TYPE  CHAR255
*"----------------------------------------------------------------------


*➔ Tipo de pedido(EKKO – BSART)
*➔ Fornecedor(EKKO – LIFNR), aqui vamos precisar realizar uma busca pois o Sênior vai encaminhar o
*CPNJ
*➔ Empresa(EKKO – BUKRS)
*➔ Pagamento EM(EKKO - ZBD1T)
*➔ Textos
*➔ Material(EKPO – MATNR)
*➔ Preço líquido(EKPO – NETPR)
*➔ Centro(EKPO – WERKS)
*➔ Centros de custos(EKKN – KOSTL)
*➔ Valor líquido do pedido em moeda de pedido (EKKN – NETWR)
*➔ Cód.imposto(EKPO – MWSKZ)
*➔ Organização de compras(EKKO – EKORG) - criar uma tabela no sap – ddd
*➔ Grupo de compradores(EKKO – EKGRP) - criar uma tabela no sap - C11
*➔ Código de aprovação(EKKO – ZZCODAPR) - criar uma tabela no sap - para manutenção
*➔ Incoterms 1(EKKO - INCO1) = SEM (deixar fixo)
*➔ Incoterms 2(EKKO – INCO2) = SEM FRETE (deixar fixo)
*➔ Classificação contábil(EKKN – ZEKKN) = K(deixar fixo)
*➔ Quantidade(EKPO – MENGE) = 1 (deixar fixo)
*➔ Código de distribuição para ClsContáb múltipla(EKPO – VRTKZ) = 3 (deixar fixo)
*➔ EM(EKPO – WEPOS) = VAZIO (deixar fixo)
*➔ Nº de endereço de entrega(EKPO - ADRN2) = 28164 (deixar fixo)


  DATA: lt_return      TYPE TABLE OF bapiret2,
        ls_return      TYPE bapiret2,
        ls_po_header   TYPE bapimepoheader,
        ls_po_headerx  TYPE bapimepoheaderx,
        lt_po_items    TYPE TABLE OF bapimepoitem,
        ls_po_item     TYPE bapimepoitem,
        lt_po_itemx    TYPE TABLE OF bapimepoitemx,
        ls_po_itemx    TYPE bapimepoitemx,
        lt_po_account  TYPE TABLE OF bapimepoaccount,
        ls_po_account  TYPE bapimepoaccount,
        lt_po_accountx TYPE TABLE OF bapimepoaccountx,
        ls_po_accountx TYPE bapimepoaccountx,
        lv_po_number   TYPE bapimepoheader-po_number,
        lt_po_text     TYPE TABLE OF bapimepotextheader,
        ls_po_text     TYPE bapimepotextheader,
        l_error        TYPE abap_bool.

* Simulação de recebimento dos dados do Sênior
  DATA: lv_cnpj TYPE string VALUE '77644102000174'.


* Buscar fornecedor pelo CNPJ
  SELECT SINGLE lifnr INTO ls_po_header-vendor
    FROM lfa1 WHERE stcd1 = lv_cnpj.
  IF sy-subrc <> 0.
    WRITE: 'Erro: Fornecedor não encontrado para CNPJ', lv_cnpj.
    EXIT.
  ENDIF.

* Preencher cabeçalho do pedido
  ls_po_header-doc_type   = i_tp_pedido.   " Tipo de Pedido
  ls_po_header-comp_code  = i_empresa. " Empresa
  ls_po_header-pmnttrms   = 'M305'. " Pagamento
  ls_po_header-purch_org  = 'VOVC'. " Organização de Compras
  ls_po_header-pur_group  = 'DAO'. " Grupo de Compradores
  ls_po_header-incoterms1 = 'CIF'. " Incoterms 1
  ls_po_header-incoterms2 = 'CIF'. " Incoterms 2
  ls_po_header-vendor     = '0001129876'. "Fornecedor

* Informar quais campos foram alterados no cabeçalho
  ls_po_headerx-doc_type   = 'X'.
  ls_po_headerx-comp_code  = 'X'.
  ls_po_headerx-pmnttrms   = 'X'.
  ls_po_headerx-purch_org  = 'X'.
  ls_po_headerx-pur_group  = 'X'.
  ls_po_headerx-incoterms1 = 'X'.
  ls_po_headerx-incoterms2 = 'X'.
  ls_po_headerx-vendor     = 'X'.

* Preencher itens do pedido
  ls_po_item-po_item    = '0010'.
  ls_po_item-material   = '000000000003014708'.
  ls_po_item-plant      = '4128'. " Centro
  ls_po_item-quantity   = 1. " Quantidade fixa
  ls_po_item-tax_code   = 'I0'. " Código de imposto
  ls_po_item-costcenter = ''. "Centro de custo
  APPEND ls_po_item TO lt_po_items.

* Informar quais campos foram alterados nos itens
  ls_po_itemx-po_item     = '00010'.
  ls_po_itemx-material    = 'X'.
  ls_po_itemx-plant       = 'X'.
  ls_po_itemx-quantity    = 'X'.
  ls_po_itemx-net_price   = 'X'.
  ls_po_itemx-tax_code    = 'X'.
  ls_po_itemx-costcenter  = 'X'.
  APPEND ls_po_itemx TO lt_po_itemx.

* Adicionar texto ao cabeçalho
  ls_po_text-po_number = lv_po_number.
  ls_po_text-text_id = 'F01'. " Identificador do texto
  ls_po_text-text_form = '*'.
  ls_po_text-text_line = 'Pedido gerado automaticamente'.
  APPEND ls_po_text TO lt_po_text.


* Chamar a BAPI para criar o pedido
  CALL FUNCTION 'BAPI_PO_CREATE1'
    EXPORTING
      poheader  = ls_po_header
      poheaderx = ls_po_headerx
    TABLES
      return        = lt_return
      poitem        = lt_po_items
      poitemx       = lt_po_itemx
      potextheader  = lt_po_text.

* Verificar retorno
  LOOP AT lt_return INTO ls_return WHERE type = 'E'.
    WRITE: 'Erro:', ls_return-message.
    e_return = 'Erro:'.
    CONCATENATE e_return ls_return-message INTO e_return.
    l_error = 'X'.
    EXIT.
  ENDLOOP.

IF l_error IS INITIAL.
* Confirmar pedido
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
    EXPORTING
      wait = 'X'.

   LOOP AT lt_return INTO ls_return WHERE type = 'S'.
     e_return = ls_return-message.
  ENDLOOP.

ENDIF.

ENDFUNCTION.
```
