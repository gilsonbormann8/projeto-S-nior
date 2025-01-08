```ABAP

*&---------------------------------------------------------------------*
*& Report Z_TESTE_PO_CREATE_GBA
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
REPORT Z_TESTE_PO_CREATE_GBA.
*DATA DECLARATION
constants : c_x value 'X'.

*Structures to hold PO header data
data : header like bapimepoheader ,
headerx like bapimepoheaderx .

*Structures to hold PO account data
data : account like bapimepoaccount occurs 0 with header line ,
accountx like bapimepoaccountx occurs 0 with header line .

*Internal Tables to hold PO ITEM DATA
data : item like bapimepoitem occurs 0 with header line,
itemx like bapimepoitemx occurs 0 with header line,

*Internal table to hold messages from BAPI call
return like bapiret2 occurs 0 with header line,

*Internal table to hold messages from BAPI call
pocontractlimits like bapiesucc occurs 0 with header line.

data : w_header(40) value 'PO Header',
purchaseorder like bapimepoheader-po_number,
delivery_date like bapimeposchedule-delivery_date.

data : ws_langu like sy-langu.

*text-001 = 'PO Header' - define as text element
selection-screen begin of block b1 with frame title text-001.
parameters : company like header-comp_code default '122' ,
doctyp like header-doc_type default 'NB' ,
cdate like header-creat_date default sy-datum ,
vendor like header-vendor default '2000000012' ,
pur_org like header-purch_org default 'PU01' ,
pur_grp like header-pur_group default '005' .
*sociedad like HEADER-COMP_CODE default '122' ,
*vendedor like HEADER-SALES_PERS default 'sale person'.


selection-screen end of block b1.

selection-screen begin of block b2 with frame title text-002.
parameters : item_num like item-po_item default '00010',
material like item-material default '12000000' ,
tipo_imp like item-acctasscat default 'K' ,
*pos_doc like ITEM-ITEM_CAT default 'F' ,
shorttxt like item-short_text default 'PRUEBA BAPI' ,
grup_art like item-matl_group default '817230000' ,
plant like item-plant default '3001' ,
mpe like item-trackingno default '9999' ,
*contrato like ITEM-AGREEMENT default '4904000003' ,
*quantity like ITEM-QUANTITY default 1 .
po_unit like item-po_unit default 'EA'.

selection-screen end of block b2.

* Par?mnetros de imputaci?n
selection-screen begin of block b3 with frame title text-004.
parameters : centro like account-costcenter default '1220813150',
cuenta like account-gl_account default '6631400' ,
num_pos like account-po_item default '10' ,
serial like account-serial_no default '01' ,
ind_imp like account-tax_code default 'I2' .

selection-screen end of block b3.


*&---------------------------------------------------------------------*
start-of-selection.
*&---------------------------------------------------------------------*
*DATA POPULATION
*&---------------------------------------------------------------------*
  ws_langu = sy-langu. "Language variable

*POPULATE HEADER DATA FOR PO
*HEADER-COMP_CODE = sociedad .
  header-doc_type = doctyp .
  header-vendor = vendor .
  header-creat_date = cdate .
  header-created_by = 'TD17191' .
  header-purch_org = pur_org .
  header-pur_group = pur_grp .
  header-comp_code = company .
  header-langu = ws_langu .
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
  item-po_item = item_num.
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


  append item. clear item.

*&---------------------------------------------------------------------*
*POPULATE ITEM FLAG TABLE
*&---------------------------------------------------------------------*
  itemx-po_item = item_num.
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
  append itemx. clear itemx.

*&---------------------------------------------------------------------*
*POPULATE ACCOUNT DATA.
*&---------------------------------------------------------------------*
  account-po_item = item_num.
  account-serial_no = serial .
  account-creat_date = sy-datum .
  account-costcenter = centro .
  account-gl_account = cuenta .
  account-gr_rcpt = 'tester'.
  append account. clear account.

*&---------------------------------------------------------------------*
*POPULATE ACCOUNT FLAG TABLE.
*&---------------------------------------------------------------------*
  accountx-po_item = item_num .
  accountx-po_itemx = c_x .
  accountx-serial_no = serial .
  accountx-serial_nox = c_x .
  accountx-creat_date = c_x .
  accountx-costcenter = c_x .
  accountx-gl_account = c_x .
  account-gr_rcpt = c_x.
  append accountx. clear accountx.


*&---------------------------------------------------------------------*
*BAPI CALL
*&---------------------------------------------------------------------*
  call function 'DIALOG_SET_NO_DIALOG'.

  call function 'BAPI_PO_CREATE1'
    exporting
      poheader         = header
      poheaderx        = headerx
    importing
      exppurchaseorder = purchaseorder
    tables
      return           = return
      poitem           = item
      poitemx          = itemx
      poaccount        = account
      poaccountx       = accountx.


*&---------------------------------------------------------------------*
*Confirm the document creation by calling database COMMIT
*&---------------------------------------------------------------------*
  call function 'BAPI_TRANSACTION_COMMIT'
  exporting
  wait = 'X'
* IMPORTING
* RETURN =
  .

end-of-selection.
*&---------------------------------------------------------------------*
*Output the messages returned from BAPI call
*&---------------------------------------------------------------------*
  loop at return.
    write / return-message.
  endloop.


```
