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
*"     VALUE(I_CENTROCUST) TYPE  KOSTL
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


  DATA: w_ztbposn        TYPE ztbposn,
        header           LIKE bapimepoheader,
        headerx          LIKE bapimepoheaderx,
        account          LIKE bapimepoaccount OCCURS 0 WITH HEADER LINE,
        accountx         LIKE bapimepoaccountx OCCURS 0 WITH HEADER LINE,
        item             LIKE bapimepoitem OCCURS 0 WITH HEADER LINE,
        itemx            LIKE bapimepoitemx OCCURS 0 WITH HEADER LINE,
        return           LIKE bapiret2 OCCURS 0 WITH HEADER LINE,
        pocontractlimits LIKE bapiesucc OCCURS 0 WITH HEADER LINE,
        w_header(40)     VALUE 'PO Header',
        purchaseorder    LIKE bapimepoheader-po_number,
        delivery_date    LIKE bapimeposchedule-delivery_date,
        ws_langu         LIKE sy-langu.

  CONSTANTS : c_x VALUE 'X'.

  SELECT SINGLE bsart
    INTO @DATA(v_bsart)
    FROM ekko
    WHERE bsart = @i_tp_pedido.

  IF sy-subrc <> 0.

    w_ztbposn-bsart   = i_tp_pedido.
    w_ztbposn-lifnr   = ''.
    w_ztbposn-bukrs   = i_empresa.
    w_ztbposn-dzbdet  = i_pagamento.
    w_ztbposn-char255 = i_texto.
    w_ztbposn-matnr   = i_material.
    w_ztbposn-netpr   = i_preco.
    w_ztbposn-werks   = i_centro.
    w_ztbposn-kostl   = i_centrocust.
    w_ztbposn-bwert   = i_vlliquid.
    w_ztbposn-mwskz   = i_codimpost.


  ENDIF.

*&---------------------------------------------------------------------*
*POPULATE HEADER DATA FOR PO
*&---------------------------------------------------------------------*
*HEADER-COMP_CODE = sociedad .
  header-doc_type   = i_tp_pedido .
  header-vendor     =   i_cnpj .
  header-creat_date = sy-datum .
  header-created_by = sy-uname .
  header-purch_org  = 'ekorg' .
  header-pur_group  = 'EKGRP' .
  header-comp_code  = i_empresa .
  header-langu      = 'PT' .
  header-incoterms1 = ''.
  header-incoterms2 = ''.

*HEADER-SALES_PERS = vendedor .
*HEADER-CURRENCY = 'DOP' .
*HEADER-ITEM_INTVL = 10 .
*HEADER-PMNTTRMS = 'N30' .
*HEADER-EXCH_RATE = 1 .

*&---------------------------------------------------------------------*
*POPULATE HEADER FLAG.
*&---------------------------------------------------------------------*
  headerx-comp_code = c_x.
  headerx-doc_type = c_x.
  headerx-vendor = c_x.
  headerx-creat_date = c_x.
  headerx-created_by = c_x.
  headerx-purch_org = c_x.
  headerx-pur_group = c_x.
  headerx-langu = c_x.
*HEADERX-sales_pers = c_x.
*HEADERX-CURRENCY = c_x.
*HEADER-ITEM_INTVL = c_x.
*HEADER-PMNTTRMS = c_x.
*HEADER-EXCH_RATE = c_x.
*HEADER-EXCH_RATE = c_x.

*&---------------------------------------------------------------------*
*POPULATE ITEM DATA.
*&---------------------------------------------------------------------*
  item-po_item = '00010'.
  item-quantity = '1'.
*ITEM-MATERIAL = material .
  item-short_text = 'prueba bapi_po_create1'.
*ITEM-TAX_CODE = ''.
  item-acctasscat = 'K' .
*ITEM-ITEM_CAT = 'D' .
  item-matl_group = '817230000' .
  item-plant = '3001' .
  item-trackingno = '99999'.
  item-preq_name = 'test'.
*ITEM-AGREEMENT = '' .
*ITEM-AGMT_ITEM = ''.
  item-quantity = '1' .
  item-po_unit = 'EA'.
*ITEM-ORDERPR_UN = 'EA'.
  item-conv_num1 = '1'.
  item-conv_den1 = '1'.
  item-net_price = '1000000' .
  item-price_unit = '1'.
  item-gr_pr_time = '0'.
  item-prnt_price = 'X'.
  item-unlimited_dlv = 'X'.
  item-gr_ind = 'X' .
  item-ir_ind = 'X' .
  item-gr_basediv = 'X'.
*ITEM-PCKG_NO = '' .


  APPEND item. CLEAR item.

*&---------------------------------------------------------------------*
*POPULATE ITEM FLAG TABLE
*&---------------------------------------------------------------------*
  itemx-po_item = '00010'.
  itemx-po_itemx = c_x.
*ITEMX-MATERIAL = C_X.
  itemx-short_text = c_x.
  itemx-quantity = c_x.
*ITEMX-TAX_CODE = C_X.
  itemx-acctasscat = c_x.
*ITEMX-ITEM_CAT = c_x.
  itemx-matl_group = c_x.
  itemx-plant = c_x.
  itemx-trackingno = c_x.
  itemx-preq_name = c_x.
*ITEMX-AGREEMENT = C_X.
*ITEMX-AGMT_ITEM = c_x.
  itemx-stge_loc = c_x.
  itemx-quantity = c_x.
  itemx-po_unit = c_x.
*ITEMX-ORDERPR_UN = C_X.
  itemx-conv_num1 = c_x.
  itemx-conv_den1 = c_x.
  itemx-net_price = c_x.
  itemx-price_unit = c_x.
  itemx-gr_pr_time = c_x.
  itemx-prnt_price = c_x.
  itemx-unlimited_dlv = c_x.
  itemx-gr_ind = c_x .
  itemx-ir_ind = c_x .
  itemx-gr_basediv = c_x .
  APPEND itemx. CLEAR itemx.

*&---------------------------------------------------------------------*
*POPULATE ACCOUNT DATA.
*&---------------------------------------------------------------------*
  account-po_item = '00010'.
  account-serial_no = '01' .
  account-creat_date = sy-datum .
  account-costcenter = i_centrocust .
  account-gl_account = '6631400' .
  account-gr_rcpt = 'tester'.
  APPEND account. CLEAR account.

*&---------------------------------------------------------------------*
*POPULATE ACCOUNT FLAG TABLE.
*&---------------------------------------------------------------------*
  accountx-po_item = '00010'.
  accountx-po_itemx = c_x .
  accountx-serial_no = '01' .
  accountx-serial_nox = c_x .
  accountx-creat_date = c_x .
  accountx-costcenter = c_x .
  accountx-gl_account = c_x .
  account-gr_rcpt = c_x.
  APPEND accountx. CLEAR accountx.

*&---------------------------------------------------------------------*
*BAPI CALL
*&---------------------------------------------------------------------*
  CALL FUNCTION 'DIALOG_SET_NO_DIALOG'.

  CALL FUNCTION 'BAPI_PO_CREATE1'
    EXPORTING
      poheader         = header
      poheaderx        = headerx
    IMPORTING
      exppurchaseorder = purchaseorder
    TABLES
      return           = return
      poitem           = item
      poitemx          = itemx
      poaccount        = account
      poaccountx       = accountx.

*&---------------------------------------------------------------------*
*Confirm the document creation by calling database COMMIT
*&---------------------------------------------------------------------*
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
    EXPORTING
      wait = 'X'
* IMPORTING
*     RETURN =
    .

  MODIFY ztbposn FROM w_ztbposn.
  COMMIT WORK AND WAIT.

  LOOP AT return.
    CONCATENATE e_return return-message INTO e_return SEPARATED BY space.
  ENDLOOP.

ENDFUNCTION.
```
