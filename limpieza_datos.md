Limpieza de datos
================

Vamos a procesar y limpiar los datos en cuatro archivos: 3 con información de contratos, y 1 sobre la reconstrucción de las escuelas.

Vamos a usar principalmente herramientas de *Tidyverse*

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.1.0       ✔ purrr   0.3.1  
    ## ✔ tibble  2.0.1       ✔ dplyr   0.8.0.1
    ## ✔ tidyr   0.8.3       ✔ stringr 1.4.0  
    ## ✔ readr   1.3.1       ✔ forcats 0.4.0

    ## ── Conflicts ────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

### Leer los datos

``` r
contratos_2017.original <- data.table::fread("data/Contratos2017.csv", encoding = "UTF-8")
contratos_2018.original <- data.table::fread("data/Contratos2018_190127084316.csv", encoding = "UTF-8")
contratos_2019.original <- data.table::fread("data/Contratos2019_190403084456.csv", encoding = "UTF-8")
sep_reconstruc.original <- data.table::fread("data/sep_reconstruccion al 29 Nov 2018.csv", encoding = "Latin-1")
```

Hagamos una copia para poder modificar

``` r
contratos_2017 <- contratos_2017.original
contratos_2018 <- contratos_2018.original
contratos_2019 <- contratos_2019.original
sep_reconstruc <- sep_reconstruc.original
```

Notas:

-   el parsing de algunos archivos con read\_csv fallaba...

-   optamos por data.table::fread que tiene heuristicos mas robustos

-   los encodings no los conocemos... revisamos los colnames y escogemos con cual no tenemos errores

``` r
contratos_2017 %>% colnames
```

    ##  [1] "GOBIERNO"                     "SIGLAS"                      
    ##  [3] "DEPENDENCIA"                  "CLAVEUC"                     
    ##  [5] "NOMBRE_DE_LA_UC"              "RESPONSABLE"                 
    ##  [7] "CODIGO_EXPEDIENTE"            "TITULO_EXPEDIENTE"           
    ##  [9] "PLANTILLA_EXPEDIENTE"         "NUMERO_PROCEDIMIENTO"        
    ## [11] "EXP_F_FALLO"                  "PROC_F_PUBLICACION"          
    ## [13] "FECHA_APERTURA_PROPOSICIONES" "CARACTER"                    
    ## [15] "TIPO_CONTRATACION"            "TIPO_PROCEDIMIENTO"          
    ## [17] "FORMA_PROCEDIMIENTO"          "CODIGO_CONTRATO"             
    ## [19] "TITULO_CONTRATO"              "FECHA_INICIO"                
    ## [21] "FECHA_FIN"                    "IMPORTE_CONTRATO"            
    ## [23] "MONEDA"                       "ESTATUS_CONTRATO"            
    ## [25] "ARCHIVADO"                    "CONVENIO_MODIFICATORIO"      
    ## [27] "RAMO"                         "CLAVE_PROGRAMA"              
    ## [29] "APORTACION_FEDERAL"           "FECHA_CELEBRACION"           
    ## [31] "CONTRATO_MARCO"               "IDENTIFICADOR_CM"            
    ## [33] "COMPRA_CONSOLIDADA"           "PLURIANUAL"                  
    ## [35] "CLAVE_CARTERA_SHCP"           "ESTRATIFICACION_MUC"         
    ## [37] "FOLIO_RUPC"                   "PROVEEDOR_CONTRATISTA"       
    ## [39] "ESTRATIFICACION_MPC"          "SIGLAS_PAIS"                 
    ## [41] "ESTATUS_EMPRESA"              "CUENTA_ADMINISTRADA_POR"     
    ## [43] "C_EXTERNO"                    "ORGANISMO"                   
    ## [45] "ANUNCIO"

``` r
contratos_2018 %>% colnames
```

    ##  [1] "GOBIERNO"                     "SIGLAS"                      
    ##  [3] "DEPENDENCIA"                  "CLAVEUC"                     
    ##  [5] "NOMBRE_DE_LA_UC"              "RESPONSABLE"                 
    ##  [7] "CODIGO_EXPEDIENTE"            "TITULO_EXPEDIENTE"           
    ##  [9] "PLANTILLA_EXPEDIENTE"         "NUMERO_PROCEDIMIENTO"        
    ## [11] "EXP_F_FALLO"                  "PROC_F_PUBLICACION"          
    ## [13] "FECHA_APERTURA_PROPOSICIONES" "CARACTER"                    
    ## [15] "TIPO_CONTRATACION"            "TIPO_PROCEDIMIENTO"          
    ## [17] "FORMA_PROCEDIMIENTO"          "CODIGO_CONTRATO"             
    ## [19] "TITULO_CONTRATO"              "FECHA_INICIO"                
    ## [21] "FECHA_FIN"                    "IMPORTE_CONTRATO"            
    ## [23] "MONEDA"                       "ESTATUS_CONTRATO"            
    ## [25] "ARCHIVADO"                    "CONVENIO_MODIFICATORIO"      
    ## [27] "RAMO"                         "CLAVE_PROGRAMA"              
    ## [29] "APORTACION_FEDERAL"           "FECHA_CELEBRACION"           
    ## [31] "CONTRATO_MARCO"               "IDENTIFICADOR_CM"            
    ## [33] "COMPRA_CONSOLIDADA"           "PLURIANUAL"                  
    ## [35] "CLAVE_CARTERA_SHCP"           "ESTRATIFICACION_MUC"         
    ## [37] "FOLIO_RUPC"                   "PROVEEDOR_CONTRATISTA"       
    ## [39] "ESTRATIFICACION_MPC"          "SIGLAS_PAIS"                 
    ## [41] "ESTATUS_EMPRESA"              "CUENTA_ADMINISTRADA_POR"     
    ## [43] "C_EXTERNO"                    "ORGANISMO"                   
    ## [45] "ANUNCIO"

``` r
contratos_2019 %>% colnames
```

    ##  [1] "Orden de gobierno"             "Siglas de la Institución"     
    ##  [3] "Institución"                   "Clave de la UC"               
    ##  [5] "Nombre de la UC"               "Responsable de la UC"         
    ##  [7] "Código del expediente"         "Referencia del expediente"    
    ##  [9] "Clave CUCOP"                   "Título del expediente"        
    ## [11] "Plantilla del expediente"      "Fundamento legal"             
    ## [13] "Número del procedimiento"      "Fecha de fallo"               
    ## [15] "Fecha de publicación"          "Fecha de apertura"            
    ## [17] "Carácter del procedimiento"    "Tipo de contratación"         
    ## [19] "Tipo de procedimiento"         "Forma de participación"       
    ## [21] "Código del contrato"           "Núm. de control del contrato" 
    ## [23] "Título del contrato"           "Fecha de inicio del contrato" 
    ## [25] "Fecha de fin del contrato"     "Importe del contrato"         
    ## [27] "Moneda del contrato"           "Estatus del contrato"         
    ## [29] "Convenio modificatorio"        "Clave del programa federal"   
    ## [31] "Fecha de firma del contrato"   "Contrato marco"               
    ## [33] "Compra consolidada"            "Contrato plurianual"          
    ## [35] "Clave de cartera SHCP"         "Folio en el RUPC"             
    ## [37] "RFC"                           "Proveedor o contratista"      
    ## [39] "Estratificación de la empresa" "Clave del país de la empresa" 
    ## [41] "RFC verificado en el SAT"      "Crédito externo"              
    ## [43] "Organismo financiero"          "Dirección del anuncio"

``` r
sep_reconstruc %>% colnames
```

    ##  [1] "FECHA_EVENTO"                                     
    ##  [2] "TIPO_EVENTO"                                      
    ##  [3] "TIPO_INFRAESTRUCTURA"                             
    ##  [4] "CCT"                                              
    ##  [5] "DESC_CCT"                                         
    ##  [6] "DIRECCION_ESCUELA"                                
    ##  [7] "NIVEL"                                            
    ##  [8] "SOSTENIMIENTO"                                    
    ##  [9] "MATRICULA"                                        
    ## [10] "ID_ENTIDAD_FEDERATIVA"                            
    ## [11] "ENTIDAD_FEDERATIVA"                               
    ## [12] "ID_MUNICIPIO"                                     
    ## [13] "MUNICIPIO"                                        
    ## [14] "ID_LOCALIDAD"                                     
    ## [15] "LOCALIDAD"                                        
    ## [16] "LATITUD"                                          
    ## [17] "LONGITUD"                                         
    ## [18] "INFRAESTRUCTURA_VALORADA"                         
    ## [19] "NIVEL_GOBIERNO_VALORACION"                        
    ## [20] "INSTANCIA_GOBIERNO_VALORACION"                    
    ## [21] "TIPO_DAÑO"                                        
    ## [22] "DETALLE_DAÑO"                                     
    ## [23] "FOTOS_DAÑO"                                       
    ## [24] "ESTATUS_OPERACION"                                
    ## [25] "FECHA_VERIFICACION"                               
    ## [26] "URL_DICTAMEN"                                     
    ## [27] "COSTO_ORIGINAL_PRE"                               
    ## [28] "MONTO_TOTAL_DE_ATENCIÓN_PRE"                      
    ## [29] "MONTO_EJERCIDO_PRE"                               
    ## [30] "DESC_OBRAS_PRE"                                   
    ## [31] "FECHA_INICIO_PRE"                                 
    ## [32] "REPRESENTANTE_PADRES_PRE"                         
    ## [33] "FECHA_FIN_PRE"                                    
    ## [34] "INSTANCIA_RESPONSABLE_OBRAS_PRE"                  
    ## [35] "COSTO_ORIGINAL_CIEN"                              
    ## [36] "MONTO_TOTAL_DE_ATENCIÓN_CIEN"                     
    ## [37] "MONTO_EJERCIDO_CIEN"                              
    ## [38] "DESC_OBRAS_CIEN"                                  
    ## [39] "FECHA_INICIO_CIEN"                                
    ## [40] "FECHA_FIN_CIEN"                                   
    ## [41] "INSTANCIA_RESPONSABLE_OBRAS_CIEN"                 
    ## [42] "COSTO_ORIGINAL_FONDEN_API"                        
    ## [43] "MONTO_TOTAL_DE_ATENCIÓN_FONDEN_API"               
    ## [44] "MONTO_EJERCIDO_FONDEN_API"                        
    ## [45] "DESC_OBRAS_FONDEN_API"                            
    ## [46] "FECHA_INICIO_FONDEN_API"                          
    ## [47] "FECHA_FIN_FONDEN_API"                             
    ## [48] "INSTANCIA_RESPONSABL_OBRAS_FONDEN_API"            
    ## [49] "COSTO_ORIGINAL_FONDEN_RECONSTRUCCIÓN"             
    ## [50] "MONTO_TOTAL_DE_ATENCIÓN_FONDEN_RECONSTRUCCIÓN"    
    ## [51] "MONTO_EJERCIDO_FONDEN_RECONSTRUCCIÓN"             
    ## [52] "DESC_OBRAS_FONDEN_RECONSTRUCCIÓN"                 
    ## [53] "FECHA_INICIO_FONDEN_RECONSTRUCCIÓN"               
    ## [54] "FECHA_FIN_FONDEN_RECONSTRUCCIÓN"                  
    ## [55] "INSTANCIA_RESPONSABLE_OBRAS_FONDEN_RECONSTRUCCIÓN"
    ## [56] "COSTO_ORIGINAL_SEGURO"                            
    ## [57] "MONTO_TOTAL_DE_ATENCIÓN_SEGURO"                   
    ## [58] "MONTO_EJERCIDO_SEGURO"                            
    ## [59] "DESC_OBRAS_SEGURO"                                
    ## [60] "FECHA_INICIO_SEGURO"                              
    ## [61] "FECHA_FIN_SEGURO"                                 
    ## [62] "INSTANCIA_RESPONSABLE_OBRAS_SEGURO"               
    ## [63] "COSTO_ORIGINAL_OTRO_ESTATAL"                      
    ## [64] "MONTO_TOTAL_DE_ATENCIÓN_OTRO_ESTATAL"             
    ## [65] "MONTO_EJERCIDO_OTRO_ESTATAL"                      
    ## [66] "DESC_OBRAS_OTRO_ESTATAL"                          
    ## [67] "FECHA_INICIO_OTRO_ESTATAL"                        
    ## [68] "FECHA_FIN_OTRO_ESTATAL"                           
    ## [69] "INSTANCIA_RESPONSABLE_OBRAS_OTRO_ESTATAL"         
    ## [70] "URL_ACTA_ENTREGA"                                 
    ## [71] "PORCENTAJE_AVANCE_PRE"                            
    ## [72] "PORCENTAJE_AVANCE_CIEN"                           
    ## [73] "PORCENTAJE_AVANCE_FONDEN_APIN"                    
    ## [74] "PORCENTAJE_AVANCE_FONDEN_RECONSTRUCCIÓN"          
    ## [75] "PORCENTAJE_AVANCE_SEGURO"                         
    ## [76] "PORCENTAJE_AVANCE_OTRO_ESTATAL"

### Ejemplo: 2017

Usemos la db de 2017 para ejemplificar

-   Paso 1: quitar vars que no necesitamos

``` r
#vars de contratos

campos_contratos <- c("SIGLAS",
                      "DEPENDENCIA",
                      "TITULO_CONTRATO",
                      "PLANTILLA_EXPEDIENTE",
                      "TIPO_CONTRATACION",
                      "TIPO_PROCEDIMIENTO",
                      "TITULO_CONTRATO",
                      "IMPORTE_CONTRATO",
                      "PROVEEDOR_CONTRATISTA"
                      )
```

Filtramos con *select*

``` r
contratos_2017 <- contratos_2017 %>% 
  select(campos_contratos)
```

Notar que con \* %&gt;% \* podemos pasarle la db a una función

``` r
contratos_2017 %>% head
```

    ##     SIGLAS                              DEPENDENCIA
    ## 1:     CFE         Comisión Federal de Electricidad
    ## 2:    IMSS     Instituto Mexicano del Seguro Social
    ## 3:   INAES Instituto Nacional de la Economía Social
    ## 4: DICONSA                    Diconsa, S.A. de C.V.
    ## 5:     SON          SON-Secretaría de Salud Pública
    ## 6: CONAGUA               Comisión Nacional del Agua
    ##                                                                                                                             TITULO_CONTRATO
    ## 1:                                                                                                                12-1-8579-DAB APARTARRAYO
    ## 2:                                                                                                         DC17APP0054 - Tapachula, Chiapas
    ## 3: ARRENDAMIENTO DE VEHÍCULOS TERRESTRES PARA EL INSTITUTO NACIONAL DE LA ECONOMÍA SOCIAL (INAES), PARA LOS EJERCICIOS FISCALES 2017 Y 2018
    ## 4:             ARRENDAMIENTO DE VEHICULOS TERRESTRES PARA LOS PROGRAMAS SOCIALES DE COMEDORES COMUNITARIOS Y FOMENTO A LA ECONOMÍA SOCIAL, 
    ## 5:                                                                           CLAVES DESIERTAS DE EQUIPO E INSTRUMENTAL MEDICO\t  Adjudicada
    ## 6:                                                         Consultoría para Diseño, Estructuración y Ejecución de Modelo de Energia Limpia 
    ##                                                        PLANTILLA_EXPEDIENTE
    ## 1:  Z15122015 08. Adjudicación Directa Internacional Abierta Art. 42 LAASSP
    ## 2:                          09. Proyecto de Asociación Público Privada LAPP
    ## 3: 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 4: 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 5: 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 6:                          09. Proyecto de Asociación Público Privada LAPP
    ##                   TIPO_CONTRATACION           TIPO_PROCEDIMIENTO
    ## 1:                    Adquisiciones Adjudicación Directa Federal
    ## 2: Servicios Relacionados con la OP           Licitación Pública
    ## 3:                   Arrendamientos           Licitación Pública
    ## 4:                   Arrendamientos           Licitación Pública
    ## 5:                    Adquisiciones           Licitación Pública
    ## 6:                        Servicios           Licitación Pública
    ##    IMPORTE_CONTRATO
    ## 1:             1815
    ## 2:      11143423653
    ## 3:         56335715
    ## 4:        233132945
    ## 5:         11202833
    ## 6:         17200000
    ##                                           PROVEEDOR_CONTRATISTA
    ## 1:                                       RTE DE MEXICO SA DE CV
    ## 2:       PROMOTORA Y DESARROLLADORA MEXICANA DE INFRAESTRUCTURA
    ## 3:                                    CASANOVA VALLEJO SA DE CV
    ## 4:                                    CASANOVA VALLEJO SA DE CV
    ## 5: DISTRIBUIDORA DE DISPOSITIVOS MEDICOS DE MEXICO S.A. DE C.V.
    ## 6:                           RICO, ROBLES, LIBENSON Y BERNAL SC

``` r
nrow(contratos_2017)
```

    ## [1] 234221

-   Paso 2: Filtramos por dependencias

Nos interesan 4: INIFED y los Gobiernos de CDMX, Morelos, Puebla y Estado de México.

Veamos cuales son los identificadores de las dependencias en la columna de CIFRAS

``` r
unique(contratos_2017$SIGLAS) %>% sort
```

    ##   [1] "AEFCM"                 "AEM"                  
    ##   [3] "AGN"                   "AGROASEMEX"           
    ##   [5] "AGS"                   "AICM"                 
    ##   [7] "APBP"                  "API-Altamira"         
    ##   [9] "API-Coatzacoalcos"     "API-Dos Bocas"        
    ##  [11] "API-Ensenada"          "API-Guaymas"          
    ##  [13] "API-Lázaro Cárdenas"   "API-Manzanillo"       
    ##  [15] "API-Mazatlán"          "API-Progreso"         
    ##  [17] "API-Puerto Madero"     "API-Puerto Vallarta"  
    ##  [19] "API-Salina Cruz"       "API-Tampico"          
    ##  [21] "API-Topolobampo"       "API-Tuxpan"           
    ##  [23] "API-Veracruz"          "APRENDEMX"            
    ##  [25] "ARTF"                  "ASA"                  
    ##  [27] "ASEA"                  "ASERCA"               
    ##  [29] "BANCOMEXT"             "BANJERCITO"           
    ##  [31] "BANOBRAS"              "BANSEFI"              
    ##  [33] "BC"                    "BCS"                  
    ##  [35] "BIENESTAR"             "BIRMEX"               
    ##  [37] "CAMP"                  "CAPUFE"               
    ##  [39] "CCC"                   "CDMX"                 
    ##  [41] "CEAV"                  "CECUTT"               
    ##  [43] "CENACE"                "CENAGAS"              
    ##  [45] "CENAM"                 "CENAPRECE"            
    ##  [47] "CENETEC"               "CENSIDA"              
    ##  [49] "CENTROGEO"             "CETI"                 
    ##  [51] "CFE"                   "CHIH"                 
    ##  [53] "CHIS"                  "CIAD"                 
    ##  [55] "CIATEC"                "CIATEJ"               
    ##  [57] "CIATEQ"                "CIBNOR"               
    ##  [59] "CICESE"                "CICY"                 
    ##  [61] "CIDE"                  "CIDESI"               
    ##  [63] "CIDETEQ"               "CIESAS"               
    ##  [65] "CIJ"                   "CIMAT"                
    ##  [67] "CIMAV"                 "CINVESTAV"            
    ##  [69] "CIO"                   "CIQA"                 
    ##  [71] "CISEN"                 "CJEF"                 
    ##  [73] "CMM"                   "CNB"                  
    ##  [75] "CNBV"                  "CNEGSR"               
    ##  [77] "CNH"                   "CNPSS"                
    ##  [79] "CNSF"                  "CNSNS"                
    ##  [81] "CNTS"                  "COAH"                 
    ##  [83] "COFAA"                 "COFECE"               
    ##  [85] "COFEPRIS"              "COL"                  
    ##  [87] "COLBACH"               "COLEF"                
    ##  [89] "COLMICH"               "COLPOS"               
    ##  [91] "COLSAN"                "COMESA"               
    ##  [93] "COMIMSA"               "CONACYT"              
    ##  [95] "CONADE"                "CONADESUCA"           
    ##  [97] "CONADIS"               "CONAFE"               
    ##  [99] "CONAFOR"               "CONAGUA"              
    ## [101] "CONALEP"               "CONALITEG"            
    ## [103] "CONAMED"               "CONAMER"              
    ## [105] "CONAMPROS"             "CONANP"               
    ## [107] "CONAPESCA"             "CONAPRED"             
    ## [109] "CONASAMI"              "CONAVI"               
    ## [111] "CONAZA"                "CONDUSEF"             
    ## [113] "CONEVAL"               "CONOCER"              
    ## [115] "CONSAR"                "CONUEE"               
    ## [117] "CPTM"                  "CRAE"                 
    ## [119] "CRE"                   "CSAEGRO"              
    ## [121] "CULTURA"               "DGO"                  
    ## [123] "DICONSA"               "DIF"                  
    ## [125] "ECHASA"                "ECOSUR"               
    ## [127] "EDUCAL"                "ESSA"                 
    ## [129] "FCE"                   "FEESA"                
    ## [131] "FICINE"                "FIDENA"               
    ## [133] "FIFOMI"                "FIFONAFE"             
    ## [135] "FIRA"                  "FIRCO"                
    ## [137] "FIT"                   "FND"                  
    ## [139] "FOCIR"                 "FONART"               
    ## [141] "FONATUR"               "FONATUR Constructora" 
    ## [143] "FONATUR Mantenimiento" "FONATUR TREN MAYA"    
    ## [145] "FONHAPO"               "GACM"                 
    ## [147] "GRO"                   "GTO"                  
    ## [149] "HG"                    "HGM"                  
    ## [151] "HGO"                   "HIM"                  
    ## [153] "HJM"                   "HRAEB"                
    ## [155] "HRAEI"                 "HRAEO"                
    ## [157] "HRAEPY"                "HRAEV"                
    ## [159] "IEPSA"                 "IFAI"                 
    ## [161] "IFT"                   "IMCINE"               
    ## [163] "IMER"                  "IMJ"                  
    ## [165] "IMP"                   "IMPI"                 
    ## [167] "IMSS"                  "IMT"                  
    ## [169] "IMTA"                  "INACIPE"              
    ## [171] "INADEM"                "INAES"                
    ## [173] "INAH"                  "INALI"                
    ## [175] "INAOE"                 "INAPAM"               
    ## [177] "INAPESCA"              "INBAL"                
    ## [179] "INCAN"                 "INCARD"               
    ## [181] "INCARURAL"             "INCMNSZ"              
    ## [183] "INDAABIN"              "INDESOL"              
    ## [185] "INEA"                  "INECC"                
    ## [187] "INECOL"                "INEE"                 
    ## [189] "INEEL"                 "INEGI"                
    ## [191] "INER"                  "INFONACOT"            
    ## [193] "INFOTEC"               "INGER"                
    ## [195] "INIFAP"                "INIFED"               
    ## [197] "ININ"                  "INM"                  
    ## [199] "INMEGEN"               "INMUJERES"            
    ## [201] "INNN"                  "INP"                  
    ## [203] "INPER"                 "INPI"                 
    ## [205] "INPSIQ"                "INR"                  
    ## [207] "INSP"                  "INSUS"                
    ## [209] "IPAB"                  "IPICYT"               
    ## [211] "IPN"                   "ISSFAM"               
    ## [213] "ISSSTE"                "JAL"                  
    ## [215] "LICONSA"               "LOTENAL"              
    ## [217] "MEX"                   "MICH"                 
    ## [219] "MOR"                   "MORA"                 
    ## [221] "NAFIN"                 "NAY"                  
    ## [223] "NL"                    "NOTIMEX"              
    ## [225] "OAX"                   "OnceTV"               
    ## [227] "PA"                    "PF"                   
    ## [229] "PGR"                   "POI"                  
    ## [231] "PR"                    "PRODECON"             
    ## [233] "PROFECO"               "PROFEDET"             
    ## [235] "PROFEPA"               "PROMEXICO"            
    ## [237] "PROMTEL"               "PRONABIVE"            
    ## [239] "PRONOSTICOS"           "PROSPERA"             
    ## [241] "PRS"                   "PUE"                  
    ## [243] "Q ROO"                 "QRO"                  
    ## [245] "RAN"                   "SACM"                 
    ## [247] "SADER"                 "SAE"                  
    ## [249] "SAP"                   "SAT"                  
    ## [251] "SCT"                   "SCVSHF"               
    ## [253] "SE"                    "SECTUR"               
    ## [255] "SEDATU"                "SEDENA"               
    ## [257] "SEGOB"                 "SEMAR"                
    ## [259] "SEMARNAT"              "SENASICA"             
    ## [261] "SENEAM"                "SENER"                
    ## [263] "SEP"                   "SEPOMEX"              
    ## [265] "SESNA"                 "SESNSP"               
    ## [267] "SFP"                   "SGM"                  
    ## [269] "SHCP"                  "SHF"                  
    ## [271] "SIAP"                  "SIN"                  
    ## [273] "SLP"                   "SNICS"                
    ## [275] "SON"                   "SPF"                  
    ## [277] "SPR"                   "SRE"                  
    ## [279] "SSA"                   "STPS"                 
    ## [281] "TAB"                   "TAMPS"                
    ## [283] "TECNM"                 "TELECOMM"             
    ## [285] "TFJA"                  "TGM"                  
    ## [287] "TLAX"                  "Tribunales Agrarios"  
    ## [289] "TVMETRO"               "UPN"                  
    ## [291] "VER"                   "YUC"                  
    ## [293] "ZAC"                   "ZEE"

``` r
dependencias   <- c("INIFED", "CDMX", "MOR", "PUE", "MEX")

contratos_2017 <- contratos_2017 %>% 
  filter(SIGLAS%in%dependencias)

head(contratos_2017)
```

    ##   SIGLAS                   DEPENDENCIA
    ## 1    MOR MOR-Comisión Estatal del Agua
    ## 2    MOR MOR-Comisión Estatal del Agua
    ## 3    MOR MOR-Comisión Estatal del Agua
    ## 4    MOR MOR-Comisión Estatal del Agua
    ## 5    MOR MOR-Comisión Estatal del Agua
    ## 6    MOR MOR-Comisión Estatal del Agua
    ##                                                                    TITULO_CONTRATO
    ## 1                  CONSTRUCCIÓN DE ALCANTARILLADO HUITCHILA, PRIMERA ETAPA DE DOS.
    ## 2 COORDINACIÓN E IMPLEMENTACIÓN DEL COMPONENTE DE PROGRAMACIÓN DE ACCIONES DE ATEN
    ## 3 ESTUDIOS DE FACTIBILIDAD TÉCNICA Y ECONÓMICA PARA LA CONSTRUCCIÓN DE SISTEMAS DE
    ## 4 AMPLIACIÓN DE ALCANTARILLADO SANITARIO EN LA LOCALIDAD RURAL DE HUITCHILA, ZONA 
    ## 5                AMPLIACIÓN DEL SISTEMA DE AGUA POTABLE EN LA LOCALIDAD CHAVARRIA.
    ## 6 CONSTRUCCIÓN DEL SISTEMA DE AGUA POTABLE EN LA LOCALIDAD RURAL DE LOS AMATES. IN
    ##                                                       PLANTILLA_EXPEDIENTE
    ## 1 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 2 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 3 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 4 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 5 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 6 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ##                  TIPO_CONTRATACION                   TIPO_PROCEDIMIENTO
    ## 1                     Obra Pública Invitación a cuando menos 3 personas
    ## 2 Servicios Relacionados con la OP                                 Otro
    ## 3 Servicios Relacionados con la OP                                 Otro
    ## 4                     Obra Pública                   Licitación Pública
    ## 5                     Obra Pública                   Licitación Pública
    ## 6                     Obra Pública                   Licitación Pública
    ##   IMPORTE_CONTRATO
    ## 1        1288387.4
    ## 2         594999.4
    ## 3        1809295.1
    ## 4        5772983.5
    ## 5         504344.4
    ## 6        2700989.5
    ##                                          PROVEEDOR_CONTRATISTA
    ## 1                    PROYECTO Y CONSTRUCCIONES MARSAL SA DE CV
    ## 2                                     MIRANDA ARANA VELASCO SC
    ## 3 CONSULTORIA Y DESARROLLO DE INFRAESTRUCTURA AQUATOR SA DE CV
    ## 4              BCG BUILDING AND CONSULTING GROUP, S.A. DE C.V.
    ## 5                               RAJIVA CONSTRUCCIONES SA DE CV
    ## 6                            IECO CONSTRUCCIONES, S.A. DE C.V.

Como identificar los que son de escuelas?

``` r
contratos_2017 %>% filter(SIGLAS == "MOR", TIPO_CONTRATACION == "Obra Pública") %>% select(DEPENDENCIA) %>% table
```

    ## .
    ##                                 MOR-Comisión Estatal del Agua 
    ##                                                            55 
    ## MOR-Instituto Estatal de Infraestructura Educativa de Morelos 
    ##                                                           415 
    ##                       MOR-Presidencia Municipal de Cuernavaca 
    ##                                                            22 
    ##                  MOR-Presidencia Municipal de Emiliano Zapata 
    ##                                                            12 
    ##                       MOR-Presidencia Municipal de Jantetelco 
    ##                                                            28 
    ##                        MOR-Presidencia Municipal de Miacatlán 
    ##                                                             1 
    ##                         MOR-Presidencia Municipal de Ocuituco 
    ##                                                             7 
    ##                  MOR-Presidencia Municipal de Puente de Ixtla 
    ##                                                             2 
    ##                          MOR-Presidencia Municipal de Temixco 
    ##                                                            11 
    ##                   MOR-Presidencia Municipal de Tlaquiltenango 
    ##                                                             1 
    ##                       MOR-Presidencia Municipal de Tlayacapan 
    ##                                                             3 
    ##                        MOR-Presidencia Municipal de Totolapan 
    ##                                                             1 
    ##                         MOR-Presidencia Municipal de Yautepec 
    ##                                                             3 
    ##                        MOR-Presidencia Municipal de Zacatepec 
    ##                                                             2 
    ##                              MOR-Secretaría de Obras Públicas 
    ##                                                            96

``` r
contratos_2017 %>% filter(SIGLAS == "MEX", TIPO_CONTRATACION == "Obra Pública") %>% select(DEPENDENCIA) %>% table
```

    ## .
    ##                                                                                                         MEX-Junta de Caminos del Estado de México 
    ##                                                                                                                                                53 
    ##                                                                MEX-Organismo de Agua Potable, Alcantarillado y Saneamiento de Naucalpan de Juárez 
    ##                                                                                                                                                 5 
    ##                                                                                         MEX-ORGANISMO DESCENTRALIZADO DE AGUA POTABLE DE ECATEPEC 
    ##                                                                                                                                                12 
    ## MEX-Organismo Público descentralizado, para la Prestacion de los Servicios de Agua Potable, Alcantarillado y Saneamiento del municipio de Metepec 
    ##                                                                                                                                                 7 
    ##                                                                                                   MEX-Presidencia Municipal de Almoloya de Juárez 
    ##                                                                                                                                                 1 
    ##                                                                                                          MEX-Presidencia Municipal de Atlacomulco 
    ##                                                                                                                                                 8 
    ##                                                                                                             MEX-Presidencia Municipal de Calimaya 
    ##                                                                                                                                                 5 
    ##                                                                                                               MEX-Presidencia Municipal de Chalco 
    ##                                                                                                                                                 5 
    ##                                                                                                        MEX-Presidencia Municipal de Chapa de Mota 
    ##                                                                                                                                                15 
    ##                                                                                              MEX-Presidencia Municipal de Coacalco de Berriozábal 
    ##                                                                                                                                                 8 
    ##                                                                                                   MEX-Presidencia Municipal de Cuautitlán Izcalli 
    ##                                                                                                                                                 3 
    ##                                                                                                        MEX-Presidencia Municipal de Donato Guerra 
    ##                                                                                                                                                26 
    ##                                                                                                  MEX-Presidencia Municipal de Ecatepec de Morelos 
    ##                                                                                                                                                25 
    ##                                                                                                               MEX-Presidencia Municipal de El Oro 
    ##                                                                                                                                                 3 
    ##                                                                                                           MEX-Presidencia Municipal de Ixtapaluca 
    ##                                                                                                                                                58 
    ##                                                                                                    MEX-Presidencia Municipal de Ixtapan de la Sal 
    ##                                                                                                                                                 1 
    ##                                                                                                           MEX-Presidencia Municipal de Ixtlahuaca 
    ##                                                                                                                                                 5 
    ##                                                                                                            MEX-Presidencia Municipal de Jilotepec 
    ##                                                                                                                                                21 
    ##                                                                                                               MEX-Presidencia Municipal de La Paz 
    ##                                                                                                                                                57 
    ##                                                                                                              MEX-Presidencia Municipal de Metepec 
    ##                                                                                                                                                37 
    ##                                                                                                        MEX-Presidencia Municipal de Mexicaltzingo 
    ##                                                                                                                                                 2 
    ##                                                                                                  MEX-Presidencia Municipal de Naucalpan de Juárez 
    ##                                                                                                                                                21 
    ##                                                                                                           MEX-Presidencia Municipal de Nextlalpan 
    ##                                                                                                                                                15 
    ##                                                                                                       MEX-Presidencia Municipal de Nezahualcóyotl 
    ##                                                                                                                                                58 
    ##                                                                                                            MEX-Presidencia Municipal de Ocoyoacac 
    ##                                                                                                                                                10 
    ##                                                                                                          MEX-Presidencia Municipal de Otzolotepec 
    ##                                                                                                                                                 3 
    ##                                                                                                            MEX-Presidencia Municipal de Papalotla 
    ##                                                                                                                                                 9 
    ##                                                                                                          MEX-Presidencia Municipal de Santo Tomás 
    ##                                                                                                                                                 1 
    ##                                                                                                              MEX-Presidencia Municipal de Tecámac 
    ##                                                                                                                                                 5 
    ##                                                                                                        MEX-Presidencia Municipal de Temascaltepec 
    ##                                                                                                                                                 1 
    ##                                                                                                           MEX-Presidencia Municipal de Tepetlixpa 
    ##                                                                                                                                                11 
    ##                                                                                                        MEX-Presidencia Municipal de Tianguistenco 
    ##                                                                                                                                                 1 
    ##                                                                                                          MEX-Presidencia Municipal de Tlalmanalco 
    ##                                                                                                                                                 2 
    ##                                                                                                  MEX-Presidencia Municipal de Tlalnepantla de Baz 
    ##                                                                                                                                                 3 
    ##                                                                                                               MEX-Presidencia Municipal de Toluca 
    ##                                                                                                                                                 9 
    ##                                                                                                            MEX-Presidencia Municipal de Tonanitla 
    ##                                                                                                                                                 1 
    ##                                                                                                             MEX-Presidencia Municipal de Tonatico 
    ##                                                                                                                                                 1 
    ##                                                                                                       MEX-Presidencia Municipal de Valle de Bravo 
    ##                                                                                                                                                 1 
    ##                                                                                          MEX-Presidencia Municipal de Valle de Chalco Solidaridad 
    ##                                                                                                                                                16 
    ##                                                                                                     MEX-Presidencia Municipal de Villa de Allende 
    ##                                                                                                                                                22 
    ##                                                                                                          MEX-Presidencia Municipal de Zacazonapan 
    ##                                                                                                                                                 4 
    ##                                                                                                                    MEX-Secretaría de Obra Pública 
    ##                                                                                                                                                 4 
    ##                                                                                                            MEX-Secretaría del Agua y Obra Pública 
    ##                                                                                                                                                 1 
    ##                                                                                                     MEX-Universidad Autónoma del Estado de México 
    ##                                                                                                                                                 9

``` r
contratos_2017 %>% filter(SIGLAS == "PUE", TIPO_CONTRATACION == "Obra Pública") %>% select(DEPENDENCIA) %>% table
```

    ## .
    ##                                                                   PUE-Benemérita Universidad Autónoma de Puebla 
    ##                                                                                                              20 
    ##                                                            PUE-Comisión Estatal de Agua y Saneamiento de Puebla 
    ##                                                                                                              34 
    ##                                    PUE-Comité Administrador Poblano para la Construcción de Espacios Educativos 
    ##                                                                                                             198 
    ##                                                                            PUE-Presidencia Municipal de Acajete 
    ##                                                                                                               9 
    ##                                                                            PUE-Presidencia Municipal de Acateno 
    ##                                                                                                               2 
    ##                                                                            PUE-Presidencia Municipal de Acatlán 
    ##                                                                                                              14 
    ##                                                                          PUE-Presidencia Municipal de Acatzingo 
    ##                                                                                                              25 
    ##                                                                           PUE-Presidencia Municipal de Acteopan 
    ##                                                                                                               4 
    ##                                                                         PUE-Presidencia Municipal de Ahuacatlán 
    ##                                                                                                               4 
    ##                                                                           PUE-Presidencia Municipal de Ahuatlán 
    ##                                                                                                               2 
    ##                                                                        PUE-Presidencia Municipal de Ahuazotepec 
    ##                                                                                                               3 
    ##                                                                       PUE-Presidencia Municipal de Ahuehuetitla 
    ##                                                                                                               1 
    ##                                                                            PUE-Presidencia Municipal de Ajalpan 
    ##                                                                                                              16 
    ##                                                                    PUE-Presidencia Municipal de Albino Zertuche 
    ##                                                                                                               3 
    ##                                                                           PUE-Presidencia Municipal de Altepexi 
    ##                                                                                                               1 
    ##                                                                           PUE-Presidencia Municipal de Amixtlán 
    ##                                                                                                               1 
    ##                                                                             PUE-Presidencia Municipal de Amozoc 
    ##                                                                                                              10 
    ##                                                                           PUE-Presidencia Municipal de Aquixtla 
    ##                                                                                                               2 
    ##                                                                            PUE-Presidencia Municipal de Atempan 
    ##                                                                                                              33 
    ##                                                                            PUE-Presidencia Municipal de Atexcal 
    ##                                                                                                               2 
    ##                                                                       PUE-Presidencia Municipal de Atlequizayan 
    ##                                                                                                               5 
    ##                                                                            PUE-Presidencia Municipal de Atlixco 
    ##                                                                                                              30 
    ##                                                                        PUE-Presidencia Municipal de Atoyatempan 
    ##                                                                                                               3 
    ##                                                                             PUE-Presidencia Municipal de Atzala 
    ##                                                                                                               2 
    ##                                                                      PUE-Presidencia Municipal de Atzitzihuacán 
    ##                                                                                                               5 
    ##                                                                             PUE-Presidencia Municipal de Axutla 
    ##                                                                                                               1 
    ##                                                               PUE-Presidencia Municipal de Ayotoxco de Guerrero 
    ##                                                                                                               2 
    ##                                                                             PUE-Presidencia Municipal de Calpan 
    ##                                                                                                               1 
    ##                                                                           PUE-Presidencia Municipal de Caltepec 
    ##                                                                                                               1 
    ##                                                                        PUE-Presidencia Municipal de Camocuautla 
    ##                                                                                                               3 
    ##                                                                     PUE-Presidencia Municipal de Cañada Morelos 
    ##                                                                                                               4 
    ##                                                                          PUE-Presidencia Municipal de Caxhuacan 
    ##                                                                                                               1 
    ##                                                             PUE-Presidencia Municipal de Chalchicomula de Sesma 
    ##                                                                                                               6 
    ##                                                                           PUE-Presidencia Municipal de Chiautla 
    ##                                                                                                              11 
    ##                                                                        PUE-Presidencia Municipal de Chiautzingo 
    ##                                                                                                               1 
    ##                                                                            PUE-Presidencia Municipal de Chietla 
    ##                                                                                                              20 
    ##                                                                       PUE-Presidencia Municipal de Chignahuapan 
    ##                                                                                                               7 
    ##                                                                         PUE-Presidencia Municipal de Chignautla 
    ##                                                                                                               1 
    ##                                                                              PUE-Presidencia Municipal de Chila 
    ##                                                                                                               1 
    ##                                                                    PUE-Presidencia Municipal de Chila de la Sal 
    ##                                                                                                               6 
    ##                                                                          PUE-Presidencia Municipal de Chinantla 
    ##                                                                                                               5 
    ##                                                                          PUE-Presidencia Municipal de Coatzingo 
    ##                                                                                                               3 
    ##                                                                          PUE-Presidencia Municipal de Cohetzala 
    ##                                                                                                               2 
    ##                                                                          PUE-Presidencia Municipal de Coronango 
    ##                                                                                                               5 
    ##                                                                          PUE-Presidencia Municipal de Coxcatlán 
    ##                                                                                                               1 
    ##                                                                         PUE-Presidencia Municipal de Coyomeapan 
    ##                                                                                                               1 
    ##                                                                          PUE-Presidencia Municipal de Coyotepec 
    ##                                                                                                               2 
    ##                                                               PUE-Presidencia Municipal de Cuapiaxtla de Madero 
    ##                                                                                                              20 
    ##                                                                         PUE-Presidencia Municipal de Cuautempan 
    ##                                                                                                               4 
    ##                                                                        PUE-Presidencia Municipal de Cuautinchán 
    ##                                                                                                               2 
    ##                                                                 PUE-Presidencia Municipal de Cuayuca de Andrade 
    ##                                                                                                               5 
    ##                                                             PUE-Presidencia Municipal de Cuetzalan del Progreso 
    ##                                                                                                               2 
    ##                                                                     PUE-Presidencia Municipal de Domingo Arenas 
    ##                                                                                                               2 
    ##                                                                       PUE-Presidencia Municipal de Eloxochitlán 
    ##                                                                                                               2 
    ##                                                                          PUE-Presidencia Municipal de Esperanza 
    ##                                                                                                               2 
    ##                                                                  PUE-Presidencia Municipal de Francisco Z. Mena 
    ##                                                                                                              12 
    ##                                                             PUE-Presidencia Municipal de General Felipe Ángeles 
    ##                                                                                                               2 
    ##                                                                          PUE-Presidencia Municipal de Guadalupe 
    ##                                                                                                               3 
    ##                                                               PUE-Presidencia Municipal de Hermenegildo Galeana 
    ##                                                                                                               1 
    ##                                                                              PUE-Presidencia Municipal de Honey 
    ##                                                                                                               7 
    ##                                                                       PUE-Presidencia Municipal de Huatlatlauca 
    ##                                                                                                               6 
    ##                                                                       PUE-Presidencia Municipal de Huauchinango 
    ##                                                                                                               1 
    ##                                                                          PUE-Presidencia Municipal de Huehuetla 
    ##                                                                                                               2 
    ##                                                                PUE-Presidencia Municipal de Huehuetlán el Chico 
    ##                                                                                                               7 
    ##                                                               PUE-Presidencia Municipal de Huehuetlán el Grande 
    ##                                                                                                               1 
    ##                                                                        PUE-Presidencia Municipal de Huejotzingo 
    ##                                                                                                              10 
    ##                                                                           PUE-Presidencia Municipal de Hueyapan 
    ##                                                                                                               1 
    ##                                                                       PUE-Presidencia Municipal de Huitziltepec 
    ##                                                                                                               4 
    ##                                                                        PUE-Presidencia Municipal de Ixcaquixtla 
    ##                                                                                                               3 
    ##                                                                    PUE-Presidencia Municipal de Ixtacamaxtitlán 
    ##                                                                                                               9 
    ##                                                                PUE-Presidencia Municipal de Izúcar de Matamoros 
    ##                                                                                                              26 
    ##                                                                             PUE-Presidencia Municipal de Jalpan 
    ##                                                                                                               1 
    ##                                                                           PUE-Presidencia Municipal de Jolalpan 
    ##                                                                                                               5 
    ##                                                                            PUE-Presidencia Municipal de Jonotla 
    ##                                                                                                               1 
    ##                                                                             PUE-Presidencia Municipal de Jopala 
    ##                                                                                                               5 
    ##                                                                       PUE-Presidencia Municipal de Juan Galindo 
    ##                                                                                                               1 
    ##                                                                     PUE-Presidencia Municipal de Juan N. Méndez 
    ##                                                                                                               1 
    ##                                                       PUE-Presidencia Municipal de La Magdalena Tlatlauquitepec 
    ##                                                                                                               1 
    ##                                                                             PUE-Presidencia Municipal de Libres 
    ##                                                                                                               4 
    ##                                                                PUE-Presidencia Municipal de Los Reyes de Juárez 
    ##                                                                                                               9 
    ##                                                             PUE-Presidencia Municipal de Mazapiltepec de Juárez 
    ##                                                                                                               3 
    ##                                                                             PUE-Presidencia Municipal de Mixtla 
    ##                                                                                                               3 
    ##                                                                          PUE-Presidencia Municipal de Nauzontla 
    ##                                                                                                               1 
    ##                                                                      PUE-Presidencia Municipal de Nicolás Bravo 
    ##                                                                                                               9 
    ##                                                                          PUE-Presidencia Municipal de Nopalucan 
    ##                                                                                                               6 
    ##                                                                           PUE-Presidencia Municipal de Ocotepec 
    ##                                                                                                               4 
    ##                                                                            PUE-Presidencia Municipal de Olintla 
    ##                                                                                                               3 
    ##                                                                           PUE-Presidencia Municipal de Oriental 
    ##                                                                                                               4 
    ##                                                                          PUE-Presidencia Municipal de Pahuatlán 
    ##                                                                                                               3 
    ##                                                                    PUE-Presidencia Municipal de Palmar de Bravo 
    ##                                                                                                               1 
    ##                                                                           PUE-Presidencia Municipal de Pantepec 
    ##                                                                                                               2 
    ##                                                                        PUE-Presidencia Municipal de Petlalcingo 
    ##                                                                                                               3 
    ##                                                                            PUE-Presidencia Municipal de Piaxtla 
    ##                                                                                                              11 
    ##                                                                             PUE-Presidencia Municipal de Puebla 
    ##                                                                                                              32 
    ##                                                                          PUE-Presidencia Municipal de Quecholac 
    ##                                                                                                               3 
    ##                                                               PUE-Presidencia Municipal de Rafael Lara Grajales 
    ##                                                                                                               2 
    ##                                                                 PUE-Presidencia Municipal de San Andrés Cholula 
    ##                                                                                                               1 
    ##                                                   PUE-Presidencia Municipal de San Diego la Mesa Tochimiltzingo 
    ##                                                                                                               1 
    ##                                                            PUE-Presidencia Municipal de San Felipe Teotlalcingo 
    ##                                                                                                               1 
    ##                                                                PUE-Presidencia Municipal de San Felipe Tepatlán 
    ##                                                                                                               4 
    ##                                                                 PUE-Presidencia Municipal de San Gabriel Chilac 
    ##                                                                                                               2 
    ##                                                            PUE-Presidencia Municipal de San Jerónimo Xayacatlán 
    ##                                                                                                               2 
    ##                                                                    PUE-Presidencia Municipal de San José Chiapa 
    ##                                                                                                               2 
    ##                                                                PUE-Presidencia Municipal de San José Miahuatlán 
    ##                                                                                                               4 
    ##                                                                   PUE-Presidencia Municipal de San Juan Atzompa 
    ##                                                                                                               1 
    ##                                                              PUE-Presidencia Municipal de San Martín Texmelucan 
    ##                                                                                                               4 
    ##                                                              PUE-Presidencia Municipal de San Martín Totoltepec 
    ##                                                                                                               2 
    ##                                                            PUE-Presidencia Municipal de San Matías Tlalancaleca 
    ##                                                                                                               1 
    ##                                                                 PUE-Presidencia Municipal de San Miguel Ixitlán 
    ##                                                                                                               2 
    ##                                                         PUE-Presidencia Municipal de San Nicolás de los Ranchos 
    ##                                                                                                               3 
    ##                                                                  PUE-Presidencia Municipal de San Pablo Anicano 
    ##                                                                                                               5 
    ##                                                                  PUE-Presidencia Municipal de San Pedro Cholula 
    ##                                                                                                               6 
    ##                                                           PUE-Presidencia Municipal de San Pedro Yeloixtlahuaca 
    ##                                                                                                               4 
    ##                                                               PUE-Presidencia Municipal de San Salvador el Seco 
    ##                                                                                                              52 
    ##                                                           PUE-Presidencia Municipal de San Sebastián Tlacotepec 
    ##                                                                                                               1 
    ##                                                              PUE-Presidencia Municipal de Santa Inés Ahuatempan 
    ##                                                                                                               7 
    ##                                                               PUE-Presidencia Municipal de Santa Isabel Cholula 
    ##                                                                                                               1 
    ##                                                                PUE-Presidencia Municipal de Santiago Miahuatlán 
    ##                                                                                                               1 
    ##                                                            PUE-Presidencia Municipal de Santo Tomás Hueyotlipan 
    ##                                                                                                               3 
    ##                                                                           PUE-Presidencia Municipal de Soltepec 
    ##                                                                                                               2 
    ##                                                                  PUE-Presidencia Municipal de Tecali de Herrera 
    ##                                                                                                               4 
    ##                                                                       PUE-Presidencia Municipal de Tecamachalco 
    ##                                                                                                               1 
    ##                                                                         PUE-Presidencia Municipal de Tecomatlán 
    ##                                                                                                              14 
    ##                                                                           PUE-Presidencia Municipal de Tehuacán 
    ##                                                                                                              29 
    ##                                                                        PUE-Presidencia Municipal de Tehuitzingo 
    ##                                                                                                               2 
    ##                                                                         PUE-Presidencia Municipal de Teopantlán 
    ##                                                                                                               1 
    ##                                                                          PUE-Presidencia Municipal de Teotlalco 
    ##                                                                                                               3 
    ##                                                                   PUE-Presidencia Municipal de Tepanco de López 
    ##                                                                                                               1 
    ##                                                               PUE-Presidencia Municipal de Tepango de Rodríguez 
    ##                                                                                                               1 
    ##                                                                            PUE-Presidencia Municipal de Tepeaca 
    ##                                                                                                              38 
    ##                                                                          PUE-Presidencia Municipal de Tepeojuma 
    ##                                                                                                               6 
    ##                                                                        PUE-Presidencia Municipal de Tepetzintla 
    ##                                                                                                               2 
    ##                                                                            PUE-Presidencia Municipal de Tepexco 
    ##                                                                                                               2 
    ##                                                                PUE-Presidencia Municipal de Tepexi de Rodríguez 
    ##                                                                                                               7 
    ##                                                                       PUE-Presidencia Municipal de Tepeyahualco 
    ##                                                                                                               3 
    ##                                                         PUE-Presidencia Municipal de Tepeyahualco de Cuauhtémoc 
    ##                                                                                                               5 
    ##                                                                   PUE-Presidencia Municipal de Tetela de Ocampo 
    ##                                                                                                               9 
    ##                                                          PUE-Presidencia Municipal de Teteles de Avila Castillo 
    ##                                                                                                               2 
    ##                                                                          PUE-Presidencia Municipal de Teziutlán 
    ##                                                                                                               3 
    ##                                                                    PUE-Presidencia Municipal de Tianguismanalco 
    ##                                                                                                               1 
    ##                                                                             PUE-Presidencia Municipal de Tilapa 
    ##                                                                                                              12 
    ##                                                        PUE-Presidencia Municipal de Tlacotepec de Benito Juárez 
    ##                                                                                                               2 
    ##                                                                      PUE-Presidencia Municipal de Tlacuilotepec 
    ##                                                                                                              11 
    ##                                                                          PUE-Presidencia Municipal de Tlahuapan 
    ##                                                                                                               2 
    ##                                                                          PUE-Presidencia Municipal de Tlapacoya 
    ##                                                                                                               1 
    ##                                                                          PUE-Presidencia Municipal de Tlapanalá 
    ##                                                                                                               4 
    ##                                                                    PUE-Presidencia Municipal de Tlatlauquitepec 
    ##                                                                                                               3 
    ##                                                                             PUE-Presidencia Municipal de Tlaxco 
    ##                                                                                                               7 
    ##                                                                          PUE-Presidencia Municipal de Tochtepec 
    ##                                                                                                               7 
    ##                                                             PUE-Presidencia Municipal de Totoltepec de Guerrero 
    ##                                                                                                               3 
    ##                                                                           PUE-Presidencia Municipal de Tulcingo 
    ##                                                                                                               3 
    ##                                                               PUE-Presidencia Municipal de Tuzamapan de Galeana 
    ##                                                                                                               1 
    ##                                                                      PUE-Presidencia Municipal de Tzicatlacoyan 
    ##                                                                                                               1 
    ##                                                                PUE-Presidencia Municipal de Venustiano Carranza 
    ##                                                                                                               5 
    ##                                                                   PUE-Presidencia Municipal de Vicente Guerrero 
    ##                                                                                                               3 
    ##                                                                PUE-Presidencia Municipal de Xayacatlán de Bravo 
    ##                                                                                                               4 
    ##                                                                          PUE-Presidencia Municipal de Xicotepec 
    ##                                                                                                              11 
    ##                                                                           PUE-Presidencia Municipal de Xicotlán 
    ##                                                                                                               3 
    ##                                                             PUE-Presidencia Municipal de Xochitlán Todos Santos 
    ##                                                                                                               2 
    ##                                                                          PUE-Presidencia Municipal de Yaonáhuac 
    ##                                                                                                               1 
    ##                                                                           PUE-Presidencia Municipal de Zacapala 
    ##                                                                                                               2 
    ##                                                                        PUE-Presidencia Municipal de Zacapoaxtla 
    ##                                                                                                               1 
    ##                                                                           PUE-Presidencia Municipal de Zacatlán 
    ##                                                                                                               7 
    ##                                                                         PUE-Presidencia Municipal de Zapotitlán 
    ##                                                                                                               2 
    ##                                                                           PUE-Presidencia Municipal de Zaragoza 
    ##                                                                                                               6 
    ##                                                                             PUE-Presidencia Municipal de Zautla 
    ##                                                                                                               4 
    ##                                                                        PUE-Presidencia Municipal de Zinacatepec 
    ##                                                                                                               1 
    ##                                                                          PUE-Presidencia Municipal de Zoquiapan 
    ##                                                                                                               2 
    ##                                                                          PUE-Presidencia Municipal de Zoquitlán 
    ##                                                                                                               8 
    ##                                                                     PUE-Secretaría De Finanzas y Administración 
    ##                                                                                                             104 
    ##                  PUE-Sistema Operador de los Servicios de Agua Potable y Alcantarillado del Municipio de Puebla 
    ##                                                                                                               1 
    ## PUE-Sistema Operador de los Servicios de Agua Potable y Alcantarillado del municipio de San Martín Texmelucan T 
    ##                                                                                                               2

``` r
contratos_2017 %>% filter(SIGLAS == "CDMX", TIPO_CONTRATACION == "Obra Pública") %>% select(DEPENDENCIA) %>% table
```

    ## .
    ##                                                  CDMX-Alcaldía de Álvaro Obregón 
    ##                                                                                2 
    ##                                                   CDMX-Alcaldía de Benito Juárez 
    ##                                                                                5 
    ##                                                        CDMX-Alcaldía de Coyoacán 
    ##                                                                                3 
    ##                                           CDMX-Alcaldía de Cuajimalpa de Morelos 
    ##                                                                                9 
    ##                                                      CDMX-Alcaldía de Cuauhtémoc 
    ##                                                                               19 
    ##                                                      CDMX-Alcaldía de Iztapalapa 
    ##                                                                               13 
    ##                                          CDMX-Alcaldía de La Magdalena Contreras 
    ##                                                                                8 
    ##                                                  CDMX-Alcaldía de Miguel Hidalgo 
    ##                                                                                2 
    ##                                                      CDMX-Alcaldía de Milpa Alta 
    ##                                                                               10 
    ##                                                         CDMX-Alcaldía de Tlalpan 
    ##                                                                               12 
    ##                                             CDMX-Alcaldía de Venustiano Carranza 
    ##                                                                               20 
    ## CDMX-Instituto Local de la Infraestructura Física Educativa del Distrito Federal 
    ##                                                                                1 
    ##                       CDMX-Procuraduría General de Justicia del Distrito Federal 
    ##                                                                                1 
    ##                                  CDMX-Secretaría de Desarrollo Urbano y Vivienda 
    ##                                                                                3 
    ##                                             CDMX-Secretaría de Obras y Servicios 
    ##                                                                               75 
    ##                                             CDMX-Secretaría de Seguridad Pública 
    ##                                                                                1 
    ##                                               CDMX-Secretaría del Medio Ambiente 
    ##                                                                                1

``` r
contratos_2017 %>% filter(SIGLAS == "INIFED", TIPO_CONTRATACION == "Obra Pública") %>% select(DEPENDENCIA) %>% table
```

    ## .
    ## Instituto Nacional de la Infraestructura Física Educativa 
    ##                                                       337

-   Paso 3: Busquemos los CCTs de los planteles dentro de los títulos de los contratos
