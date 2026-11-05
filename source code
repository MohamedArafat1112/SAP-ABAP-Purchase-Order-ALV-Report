
*& Report ZMA_ABAP_REVISION

REPORT zma_abap_revision.



TYPES: BEGIN OF str_data,

         ebeln TYPE ekko-ebeln, "Purchasing Document Number
         bukrs TYPE ekko-bukrs, "Company Code
         aedat TYPE ekko-aedat, "Creation Date of Purchasing Document
         ernam TYPE ekko-ernam, "User who created the purchasing document

         matnr TYPE ekpo-matnr, "Material Number
         matkl TYPE ekpo-matkl, "Material Group
         menge TYPE ekpo-menge, "Purchase Order Quantity
         netpr TYPE ekpo-netpr, "Net Price in Purchasing Document
         netwr TYPE ekpo-netwr, "Net Order Value in PO Currency
         ntgew TYPE ekpo-ntgew, "Net Weight

         name1 TYPE lfa1-name1, "Vendor Name 1
         name2 TYPE lfa1-name2, "Vendor Name 2
         ort01 TYPE lfa1-ort01, "Vendor City
         ort02 TYPE lfa1-ort02, "Vendor District

         sn    TYPE n LENGTH 4,  "Sequential Number
         LINE_COLOR TYPE CHAR4,  "ALV Row Color Information

       END OF str_data.

*---------------------------------------------------------------------*
* 2. Declare Internal Tables, Work Areas and ALV Variables
*---------------------------------------------------------------------*

DATA : it_data       TYPE TABLE OF str_data,
       wa_data       TYPE str_data,

       fieldcat      TYPE TABLE OF slis_fieldcat_alv WITH HEADER LINE,

       it_listheader TYPE slis_t_listheader,
       lw_listheader TYPE slis_listheader,

       g_repid       TYPE sy-repid,

       GS_LAYOUT     TYPE slis_layout_alv.

*---------------------------------------------------------------------*
* 3. Selection Screen
* Allow the User to Filter Purchase Orders by Document Number
*---------------------------------------------------------------------*

SELECT-OPTIONS : s_ebeln FOR wa_data-ebeln.

*---------------------------------------------------------------------*
* 4. Retrieve Purchase Order Header, Item and Vendor Data
* EKKO : Purchase Order Header
* EKPO : Purchase Order Items
* LFA1 : Vendor Master Data
*---------------------------------------------------------------------*

START-OF-SELECTION.

  SELECT * FROM ekko INNER JOIN ekpo ON ekko~ebeln = ekpo~ebeln
                     INNER JOIN lfa1 ON lfa1~lifnr = lfa1~lifnr
  INTO CORRESPONDING FIELDS OF TABLE it_data
  WHERE ekko~ebeln IN s_ebeln.

*---------------------------------------------------------------------*
* 5. Prepare ALV Output
* Sort Purchase Orders and Generate Sequential Numbers
*---------------------------------------------------------------------*

END-OF-SELECTION.

  SORT it_data BY ebeln ASCENDING.

  LOOP AT it_data INTO wa_data.

    "Generate Sequential Number for Each Record
    wa_data-sn = sy-tabix.

    "Update Internal Table with Sequential Number
    MODIFY it_data FROM WA_data.

  ENDLOOP.

*---------------------------------------------------------------------*
* 6. Configure ALV Field Catalog
* Define Column Names, Width, Alignment and Highlighting
*---------------------------------------------------------------------*

  PERFORM fieldcat USING 'IT_DATA' 'SN' 'INDEX' '4' 'C' 'X' 'C500'.

  PERFORM fieldcat USING 'it_data' 'EBELN' 'Purchase Document Number' '15' 'C' 'X' 'C700'.

  PERFORM fieldcat USING 'it_data' 'BUKRS' 'Company Code' '15' 'C ' '' ''.

  PERFORM fieldcat USING 'it_data' 'AEDAT' 'Creation date' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'ERNAM' 'Creation user' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'MATNR' 'material number' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'MATKL' 'material group' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'MENGE' 'quantity' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'NETPR' 'net price' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'NETWR' 'net order currency' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'NTGEW' 'net weight' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'NAME1' 'vendor name 1' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'NAME2' 'vendor name 2' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'ORT01' 'vendor City' '15' 'C' '' ''.

  PERFORM fieldcat USING 'it_data' 'ORT02' 'vendor district' '15' 'C' '' ''.

*---------------------------------------------------------------------*
* 7. Initialize ALV Program Information and Header
*---------------------------------------------------------------------*

  g_repid = sy-repid.

  "Build Custom ALV Header
  PERFORM build_alv_header.

  "Configure ALV Layout
  PERFORM MA_LAYOUT.

*---------------------------------------------------------------------*
* 8. Display ALV Grid
* REUSE_ALV_GRID_DISPLAY is Used to Display the Final Output
*---------------------------------------------------------------------*

  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
    EXPORTING
      i_callback_program     = g_repid
      i_callback_top_of_page = 'TOP_OF_PAGE'
      i_grid_title           = 'Purchasing Document ALV REPORT'
      IS_LAYOUT              = GS_LAYOUT
      it_fieldcat            = fieldcat[]
    TABLES
      t_outtab               = it_data.

  IF sy-subrc <> 0.
    "Implement suitable error handling here
  ENDIF.

*---------------------------------------------------------------------*
* 9. FIELD CATALOG FORM
* Dynamically Configure ALV Column Properties
*---------------------------------------------------------------------*

FORM fieldcat USING p1 p2 p3 p4 p5 p6 p7.

  "Set Internal Table Name
  fieldcat-tabname = p1.

  "Set Field Name
  fieldcat-fieldname = p2.

  "Set Column Heading
  fieldcat-seltext_m = p3.

  "Set Column Output Length
  fieldcat-outputlen = p4.

  "Set Column Alignment
  fieldcat-just = p5.

  "Fix Column Position
  fieldcat-fix_column = p6.

  "Set Column Emphasis Color
  fieldcat-emphasize = p7.

  "Append Field Catalog Entry
  APPEND fieldcat.

  CLEAR fieldcat.

ENDFORM.

*---------------------------------------------------------------------*
* 10. BUILD ALV HEADER FORM
* Prepare Report Title and Current Date
*---------------------------------------------------------------------*

FORM build_alv_header.

  "Main Report Heading
  lw_listheader-typ = 'H'.

  lw_listheader-info = 'Purchasing Document ALV REPORT'.

  APPEND lw_listheader TO it_listheader.

  CLEAR lw_listheader.

  "Display Current System Date
  lw_listheader-typ = 'S'.

  lw_listheader-key = 'DATA'.

  CONCATENATE sy-datum+6(2)
              sy-datum+4(2)
              sy-datum(4)
         INTO lw_listheader-info SEPARATED BY '/'.

  APPEND lw_listheader TO it_listheader.

  CLEAR lw_listheader.

  "Additional Report Information
  lw_listheader-typ = 'A'.

  lw_listheader-info = 'Purchasing Document REPORT'.

  APPEND lw_listheader TO it_listheader.

  CLEAR lw_listheader.

ENDFORM.

*---------------------------------------------------------------------*
* 11. TOP OF PAGE FORM
* Display Custom Header Information Above ALV Grid
*---------------------------------------------------------------------*

FORM top_of_page.

  CALL FUNCTION 'REUSE_ALV_COMMENTARY_WRITE'
    EXPORTING
      it_list_commentary = it_listheader.

ENDFORM.

*---------------------------------------------------------------------*
* 12. ALV LAYOUT FORM
* Optimize Column Width and Highlight Rows Based on Net Price
*---------------------------------------------------------------------*

FORM MA_LAYOUT.

  "Specify Field Containing Row Color Information
  gs_layout-info_fieldname = 'LINE_COLOR'.

  "Automatically Optimize Column Width
  gs_layout-colwidth_optimize = 'X'.

  "Loop Through ALV Data
  LOOP AT IT_data INTO wa_data.

    "Highlight Records Where Net Price is 500 or More
    IF wa_data-netpr >= 500.

      wa_data-line_color = 'C700'.

      "Update Row Color Information
      MODIFY it_data FROM wa_data.

    ENDIF.

  ENDLOOP.

ENDFORM.
