```ABAP

&---------------------------------------------------------------------*
*& Report Z_TESTE_PO_CREATE_GBA
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT z_teste_po_create_gba.

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
      lv_po_number   TYPE bapimepoheader-po_number.

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
ls_po_header-doc_type   = 'Z4CP'.   " Tipo de Pedido
ls_po_header-comp_code  = '4014'. " Empresa
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
APPEND ls_po_item TO lt_po_items.

* Informar quais campos foram alterados nos itens
ls_po_itemx-po_item   = '00010'.
ls_po_itemx-material  = 'X'.
ls_po_itemx-plant     = 'X'.
ls_po_itemx-quantity  = 'X'.
ls_po_itemx-net_price = 'X'.
ls_po_itemx-tax_code  = 'X'.
APPEND ls_po_itemx TO lt_po_itemx.


* Chamar a BAPI para criar o pedido
CALL FUNCTION 'BAPI_PO_CREATE1'
  EXPORTING
    poheader  = ls_po_header
    poheaderx = ls_po_headerx
  TABLES
    return    = lt_return
    poitem    = lt_po_items
    poitemx   = lt_po_itemx.

* Verificar retorno
LOOP AT lt_return INTO ls_return WHERE type = 'E'.
  WRITE: 'Erro:', ls_return-message.
  EXIT.
ENDLOOP.

* Confirmar pedido
CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
  EXPORTING
    wait = 'X'.

* Obter número do pedido gerado
SELECT  ebeln INTO lv_po_number
  FROM ekko
  WHERE lifnr = ls_po_header-vendor
  ORDER BY ebeln DESCENDING.
ENDSELECT.

IF lv_po_number IS NOT INITIAL.

  DATA: lt_po_text TYPE TABLE OF bapimepotext,
        ls_po_text TYPE bapimepotext.

* Adicionar texto ao cabeçalho
  ls_po_text-po_number = lv_po_number.
  ls_po_text-text_id = 'F01'. " Identificador do texto
  ls_po_text-text_form = '*'.
  ls_po_text-text_line = 'Pedido gerado automaticamente'.
  APPEND ls_po_text TO lt_po_text.

* Chamar a BAPI para modificar o pedido e adicionar o texto
  CALL FUNCTION 'BAPI_PO_CHANGE'
    EXPORTING
      purchaseorder = lv_po_number
    TABLES
      return        = lt_return
      potextheader  = lt_po_text.

* Confirmar alteração
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
    EXPORTING
      wait = 'X'.

  WRITE: 'Pedido criado com sucesso:', lv_po_number.

ELSE.
  WRITE: 'Erro ao criar pedido'.
ENDIF.

```
