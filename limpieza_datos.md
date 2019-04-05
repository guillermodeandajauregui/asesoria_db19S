Limpieza de datos
================

Vamos a procesar y limpiar los datos en cuatro archivos: 3 con información de contratos, y 1 sobre la reconstrucción de las escuelas

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

contratos_2017 %>% head
```

    ##    GOBIERNO  SIGLAS                              DEPENDENCIA   CLAVEUC
    ## 1:      APF     CFE         Comisión Federal de Electricidad 018TOQ018
    ## 2:      APF    IMSS     Instituto Mexicano del Seguro Social 050GYR040
    ## 3:      APF   INAES Instituto Nacional de la Economía Social 020L00001
    ## 4:      APF DICONSA                    Diconsa, S.A. de C.V. 020VSS005
    ## 5:       GE     SON          SON-Secretaría de Salud Pública 926005961
    ## 6:      APF CONAGUA               Comisión Nacional del Agua 016B00015
    ##                                                                                          NOMBRE_DE_LA_UC
    ## 1:                                    CFE-Gerencia Divisional de Distribución Baja California #018TOQ018
    ## 2:                  IMSS-Coordinación Técnica de Adquisición de Bienes de Inversión y Activos #050GYR040
    ## 3: INAES-Coordinación General de Administración y Finanzas - Dirección de Recursos Materiales #020L00001
    ## 4:                                     DICONSA-Dirección de Administración y Recursos Humanos #020VSS005
    ## 5:                           SON-Secretaría de Salud Pública-Direccion de Recursos Materiales #926005961
    ## 6:                                                    CONAGUA-Gerencia de Recursos Materiales #016B00015
    ##                        RESPONSABLE CODIGO_EXPEDIENTE
    ## 1:          Seferino Trigo Saucedo            118746
    ## 2:            Magali Olivares Cruz           1150039
    ## 3:   Jesús Martín Arciniega Peláez           1156985
    ## 4:     Benjamín Cervantes Albarrán           1167858
    ## 5: Jorge Alberto Terrazas Valencia           1193443
    ## 6:  Francisco Javier Izaza Arteaga           1197283
    ##                                                                  TITULO_EXPEDIENTE
    ## 1:                                                       ADIB 8579/2011 2DO.EVENTO
    ## 2:                      APP-019GYR040-E24-2016 CONCURSO PÚBLICO TAPACHULA, CHIAPAS
    ## 3:                 ARRENDAMIENTO DE VEHÍCULOS TERRESTRES PARA EL INAES (2017-2018)
    ## 4:  ARRENDAMIENTO DE VEHICULOS TERRESTRES PARA LOS PROGRAMAS SOCIALES DE COMEDORES
    ## 5:                                CLAVES DESIERTAS DE EQUIPO E INSTRUMENTAL MEDICO
    ## 6: Consultoría para Diseño, Estructuración y Ejecución de Modelo de Energia Limpia
    ##                                                        PLANTILLA_EXPEDIENTE
    ## 1:  Z15122015 08. Adjudicación Directa Internacional Abierta Art. 42 LAASSP
    ## 2:                          09. Proyecto de Asociación Público Privada LAPP
    ## 3: 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 4: 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 5: 08. Reporte de otras contrataciones y contrataciones con crédito externo
    ## 6:                          09. Proyecto de Asociación Público Privada LAPP
    ##      NUMERO_PROCEDIMIENTO EXP_F_FALLO PROC_F_PUBLICACION
    ## 1: AA-018TOQ018-I235-2011                 14/12/11 19:59
    ## 2: APP-019GYR040-E24-2016                 13/09/16 20:03
    ## 3:  LA-020L00001-E49-2016    09/09/16                   
    ## 4:  CE-020VSS005-E83-2016                               
    ## 5:  CE-926005961-E56-2016    13/01/17     22/12/16 10:06
    ## 6: APP-016B00015-E48-2016                 10/11/16 14:35
    ##    FECHA_APERTURA_PROPOSICIONES      CARACTER
    ## 1:               04/01/12 14:00 Internacional
    ## 2:               25/04/17 10:00              
    ## 3:                                   Nacional
    ## 4:                                   Nacional
    ## 5:               06/01/17 11:00      Nacional
    ## 6:               24/11/16 11:00              
    ##                   TIPO_CONTRATACION           TIPO_PROCEDIMIENTO
    ## 1:                    Adquisiciones Adjudicación Directa Federal
    ## 2: Servicios Relacionados con la OP           Licitación Pública
    ## 3:                   Arrendamientos           Licitación Pública
    ## 4:                   Arrendamientos           Licitación Pública
    ## 5:                    Adquisiciones           Licitación Pública
    ## 6:                        Servicios           Licitación Pública
    ##    FORMA_PROCEDIMIENTO CODIGO_CONTRATO
    ## 1:               Mixta         1383070
    ## 2:               Mixta         1601048
    ## 3:         Electrónica         1190766
    ## 4:         Electrónica         1203088
    ## 5:          Presencial         1886821
    ## 6:         Electrónica         1333719
    ##                                                                                                                             TITULO_CONTRATO
    ## 1:                                                                                                                12-1-8579-DAB APARTARRAYO
    ## 2:                                                                                                         DC17APP0054 - Tapachula, Chiapas
    ## 3: ARRENDAMIENTO DE VEHÍCULOS TERRESTRES PARA EL INSTITUTO NACIONAL DE LA ECONOMÍA SOCIAL (INAES), PARA LOS EJERCICIOS FISCALES 2017 Y 2018
    ## 4:             ARRENDAMIENTO DE VEHICULOS TERRESTRES PARA LOS PROGRAMAS SOCIALES DE COMEDORES COMUNITARIOS Y FOMENTO A LA ECONOMÍA SOCIAL, 
    ## 5:                                                                           CLAVES DESIERTAS DE EQUIPO E INSTRUMENTAL MEDICO\t  Adjudicada
    ## 6:                                                         Consultoría para Diseño, Estructuración y Ejecución de Modelo de Energia Limpia 
    ##    FECHA_INICIO FECHA_FIN IMPORTE_CONTRATO MONEDA ESTATUS_CONTRATO
    ## 1:     05/04/17  25/04/18             1815    USD         Expirado
    ## 2:     28/07/17  28/07/42      11143423653    MXN           Activo
    ## 3:     01/01/17  31/10/18         56335715    MXN         Expirado
    ## 4:     01/01/17  31/12/18        233132945    MXN         Expirado
    ## 5:     10/01/17  01/01/18         11202833    MXN         Expirado
    ## 6:     16/01/17  16/05/17         17200000    MXN         Expirado
    ##    ARCHIVADO CONVENIO_MODIFICATORIO RAMO CLAVE_PROGRAMA APORTACION_FEDERAL
    ## 1:        No                      0   NA                                NA
    ## 2:        No                      0   NA                                NA
    ## 3:        No                      0   NA                                NA
    ## 4:        No                      1   NA                                NA
    ## 5:        No                      0   NA                                NA
    ## 6:        No                      0   NA                                NA
    ##    FECHA_CELEBRACION CONTRATO_MARCO IDENTIFICADOR_CM COMPRA_CONSOLIDADA
    ## 1:          31/01/12             NA                                   0
    ## 2:          28/07/17             NA                                   0
    ## 3:          23/09/16             NA                                   1
    ## 4:                               NA                                   1
    ## 5:                               NA                                   0
    ## 6:                               NA                                   0
    ##    PLURIANUAL CLAVE_CARTERA_SHCP ESTRATIFICACION_MUC FOLIO_RUPC
    ## 1:          0                                  Micro       8016
    ## 2:          1                              No MIPYME         NA
    ## 3:          1                                Pequeña      10861
    ## 4:          1                                Mediana      10861
    ## 5:          0                              No MIPYME      11521
    ## 6:          0                                Pequeña         NA
    ##                                           PROVEEDOR_CONTRATISTA
    ## 1:                                       RTE DE MEXICO SA DE CV
    ## 2:       PROMOTORA Y DESARROLLADORA MEXICANA DE INFRAESTRUCTURA
    ## 3:                                    CASANOVA VALLEJO SA DE CV
    ## 4:                                    CASANOVA VALLEJO SA DE CV
    ## 5: DISTRIBUIDORA DE DISPOSITIVOS MEDICOS DE MEXICO S.A. DE C.V.
    ## 6:                           RICO, ROBLES, LIBENSON Y BERNAL SC
    ##    ESTRATIFICACION_MPC SIGLAS_PAIS ESTATUS_EMPRESA CUENTA_ADMINISTRADA_POR
    ## 1:             Pequeña          MX      HABILITADO                     PoC
    ## 2:           No MIPYME          MX      HABILITADO                     PoC
    ## 3:             Pequeña          MX      HABILITADO                     PoC
    ## 4:             Pequeña          MX      HABILITADO                     PoC
    ## 5:             Mediana          MX      HABILITADO                     PoC
    ## 6:             Pequeña          MX      HABILITADO                     PoC
    ##    C_EXTERNO ORGANISMO
    ## 1:         0          
    ## 2:         0          
    ## 3:         0          
    ## 4:         0          
    ## 5:         0          
    ## 6:         0          
    ##                                                                                           ANUNCIO
    ## 1:   https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=75743
    ## 2:  https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=951032
    ## 3:  https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=958781
    ## 4:  https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=969194
    ## 5:  https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=994725
    ## 6: https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=1004357

``` r
contratos_2018 %>% head
```

    ##    GOBIERNO  SIGLAS                                 DEPENDENCIA   CLAVEUC
    ## 1:      APF LOTENAL Lotería Nacional para la Asistencia Pública 006HHQ001
    ## 2:      APF LOTENAL Lotería Nacional para la Asistencia Pública 006HHQ001
    ## 3:      APF LOTENAL Lotería Nacional para la Asistencia Pública 006HHQ001
    ## 4:      APF LOTENAL Lotería Nacional para la Asistencia Pública 006HHQ001
    ## 5:      APF LOTENAL Lotería Nacional para la Asistencia Pública 006HHQ001
    ## 6:      APF LOTENAL Lotería Nacional para la Asistencia Pública 006HHQ001
    ##                                       NOMBRE_DE_LA_UC
    ## 1: LOTENAL-Gerencia de Recursos Materiales #006HHQ001
    ## 2: LOTENAL-Gerencia de Recursos Materiales #006HHQ001
    ## 3: LOTENAL-Gerencia de Recursos Materiales #006HHQ001
    ## 4: LOTENAL-Gerencia de Recursos Materiales #006HHQ001
    ## 5: LOTENAL-Gerencia de Recursos Materiales #006HHQ001
    ## 6: LOTENAL-Gerencia de Recursos Materiales #006HHQ001
    ##                   RESPONSABLE CODIGO_EXPEDIENTE
    ## 1: Irizabeth Elizarraras Soto           1810636
    ## 2: Irizabeth Elizarraras Soto           1828702
    ## 3: Irizabeth Elizarraras Soto           1793727
    ## 4: Irizabeth Elizarraras Soto           1795028
    ## 5: Irizabeth Elizarraras Soto           1799214
    ## 6: Irizabeth Elizarraras Soto           1799277
    ##                                                                   TITULO_EXPEDIENTE
    ## 1:                                                   MATERIALES Y ÚTILES DE OFICINA
    ## 2:    SERVICIO DE MANTENIMIENTO CORRECTIVO A COMPONENTES DEL CIRCUITO CERRADO DE TV
    ## 3:                     ADQUISICIÓN DE HERRAMIENTAS MENORES PARA LA LOTERÍA NACIONAL
    ## 4:                                                           SERVICIOS DE TELEFONIA
    ## 5:       SERVICIO DE MAQUILA PARA EL TAPADITO, DE LOS SORTEOS MAYOR 3683, MAGNO 369
    ## 6: SERVICIO DE MANTENIMIENTO EN INTERIORES Y EXTERIORES DE LOS INMUEBLES DE LOTENAL
    ##               PLANTILLA_EXPEDIENTE   NUMERO_PROCEDIMIENTO EXP_F_FALLO
    ## 1: 05. Adjudicación Directa LAASSP AA-006HHQ001-E305-2018            
    ## 2: 05. Adjudicación Directa LAASSP AA-006HHQ001-E306-2018            
    ## 3: 05. Adjudicación Directa LAASSP AA-006HHQ001-E300-2018            
    ## 4: 05. Adjudicación Directa LAASSP AA-006HHQ001-E301-2018            
    ## 5: 05. Adjudicación Directa LAASSP AA-006HHQ001-E302-2018            
    ## 6: 05. Adjudicación Directa LAASSP AA-006HHQ001-E303-2018            
    ##    PROC_F_PUBLICACION FECHA_APERTURA_PROPOSICIONES CARACTER
    ## 1:                                                 Nacional
    ## 2:                                                 Nacional
    ## 3:                                                 Nacional
    ## 4:                                                 Nacional
    ## 5:                                                 Nacional
    ## 6:                                                 Nacional
    ##    TIPO_CONTRATACION           TIPO_PROCEDIMIENTO FORMA_PROCEDIMIENTO
    ## 1:     Adquisiciones Adjudicación Directa Federal               Mixta
    ## 2:         Servicios Adjudicación Directa Federal               Mixta
    ## 3:     Adquisiciones Adjudicación Directa Federal               Mixta
    ## 4:         Servicios Adjudicación Directa Federal               Mixta
    ## 5:         Servicios Adjudicación Directa Federal               Mixta
    ## 6:         Servicios Adjudicación Directa Federal               Mixta
    ##    CODIGO_CONTRATO
    ## 1:         1946820
    ## 2:         1966610
    ## 3:         1925438
    ## 4:         1927312
    ## 5:         1932296
    ## 6:         1932360
    ##                                                                     TITULO_CONTRATO
    ## 1:                                                   MATERIALES Y ÚTILES DE OFICINA
    ## 2:    SERVICIO DE MANTENIMIENTO CORRECTIVO A COMPONENTES DEL CIRCUITO CERRADO DE TV
    ## 3:                     ADQUISICIÓN DE HERRAMIENTAS MENORES PARA LA LOTERÍA NACIONAL
    ## 4:                                             SERVICIOS DE TELEFONÍA INSTITUCIONAL
    ## 5:      SERVICIO DE MAQUILA PARA EL TAPADITO, DE LOS SORTEOS MAYOR 3683, MAGNO 369 
    ## 6: SERVICIO DE MANTENIMIENTO EN INTERIORES Y EXTERIORES DE LOS INMUEBLES DE LOTENAL
    ##    FECHA_INICIO  FECHA_FIN IMPORTE_CONTRATO MONEDA ESTATUS_CONTRATO
    ## 1:   2018-07-27 2018-08-06         110000.0    MXN         Expirado
    ## 2:   2018-07-27 2018-08-05         183200.0    MXN         Expirado
    ## 3:   2018-07-31 2018-08-30         189658.5    MXN         Expirado
    ## 4:   2018-03-01 2019-02-28         217241.4    MXN           Activo
    ## 5:   2018-07-31 2018-10-31         115900.0    MXN         Expirado
    ## 6:   2018-07-26 2018-08-10         323162.4    MXN         Expirado
    ##    ARCHIVADO CONVENIO_MODIFICATORIO RAMO CLAVE_PROGRAMA APORTACION_FEDERAL
    ## 1:        No                      0   NA                                NA
    ## 2:        No                      0   NA                                NA
    ## 3:        No                      0   NA                                NA
    ## 4:        No                      0   NA                                NA
    ## 5:        No                      0   NA                                NA
    ## 6:        No                      0   NA                                NA
    ##    FECHA_CELEBRACION CONTRATO_MARCO IDENTIFICADOR_CM COMPRA_CONSOLIDADA
    ## 1:                               NA                                   0
    ## 2:                               NA                                   0
    ## 3:                               NA                                   0
    ## 4:                               NA                                   0
    ## 5:                               NA                                   0
    ## 6:                               NA                                   0
    ##    PLURIANUAL CLAVE_CARTERA_SHCP ESTRATIFICACION_MUC FOLIO_RUPC
    ## 1:          0                                Pequeña      36116
    ## 2:          0                                  Micro         NA
    ## 3:          0                                  Micro         NA
    ## 4:          0                              No MIPYME       6404
    ## 5:          0                                Pequeña         NA
    ## 6:          0                                Pequeña         NA
    ##                  PROVEEDOR_CONTRATISTA ESTRATIFICACION_MPC SIGLAS_PAIS
    ## 1:           SUMINISTROS LARY SA DE CV             Mediana          MX
    ## 2:           BRANCO AARON BARRERA ROMO               Micro          MX
    ## 3:               DANIEL HERNANDEZ VEGA               Micro          MX
    ## 4:                  AXTEL S A B DE C V           No MIPYME          MX
    ## 5:                ADEM STUDIO SA DE CV             Mediana          MX
    ## 6: CONSTRUCTORA CONARDIS, S.A. DE C.V.                              MX
    ##    ESTATUS_EMPRESA CUENTA_ADMINISTRADA_POR C_EXTERNO ORGANISMO
    ## 1:      HABILITADO                     PoC        NA          
    ## 2:      HABILITADO                     PoC        NA          
    ## 3:      HABILITADO                     PoC        NA          
    ## 4:      HABILITADO                     PoC        NA          
    ## 5:      HABILITADO                     PoC        NA          
    ## 6:      HABILITADO                      UC        NA          
    ##                                                                                           ANUNCIO
    ## 1: https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=1573880
    ## 2: https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=1590647
    ## 3: https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=1557815
    ## 4: https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=1559133
    ## 5: https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=1563068
    ## 6: https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=1564193

``` r
contratos_2019 %>% head
```

    ##    Orden de gobierno Siglas de la Institución
    ## 1:                GE                     COAH
    ## 2:                GE                     COAH
    ## 3:               APF                      SAT
    ## 4:               APF                CINVESTAV
    ## 5:               APF                      HIM
    ## 6:               APF                  LICONSA
    ##                                                                           Institución
    ## 1:                                       _Gobierno del Estado de Coahuila de Zaragoza
    ## 2:                                       _Gobierno del Estado de Coahuila de Zaragoza
    ## 3:                                              Servicio de Administración Tributaria
    ## 4: Centro de Investigación y de Estudios Avanzados del Instituto Politécnico Nacional
    ## 5:                                         Hospital Infantil de México Federico Gómez
    ## 6:                                                              Liconsa, S.A. de C.V.
    ##    Clave de la UC
    ## 1:      905011981
    ## 2:      905011981
    ## 3:      006E00001
    ## 4:      011L4J995
    ## 5:      012NBG001
    ## 6:      020VST001
    ##                                                                                           Nombre de la UC
    ## 1:         COAH-Secretaría de Fiscalización y Rendición de Cuentas-Coordinación Administrativa #905011981
    ## 2:         COAH-Secretaría de Fiscalización y Rendición de Cuentas-Coordinación Administrativa #905011981
    ## 3: SAT-Administración General de Recursos y Servicios, Administración de Recursos Materiales 1 #006E00001
    ## 4:                                               CINVESTAV-Subdirección de Recursos Materiales #011L4J995
    ## 5:                                                 HIM-Departamento de Adquisiciones Generales #012NBG001
    ## 6:                                    LICONSA-Subdirección de Adquisiciones de Consumo Interno #020VST001
    ##           Responsable de la UC Código del expediente
    ## 1: Jesús Fernando Ramos Flores                109177
    ## 2: Jesús Fernando Ramos Flores                109177
    ## 3:   José Luis González García                879057
    ## 4: Bruno René Palafox Carvallo                861624
    ## 5:     Nicolas Gonzalez Bustos               1047703
    ## 6:  Juan Alberto Lugo Olivares               1200493
    ##                                                          Referencia del expediente
    ## 1:                                                                                
    ## 2:                                                                                
    ## 3:                                                           LA-006E00001-N71-2015
    ## 4:                                                                                
    ## 5: SERVICIO DE SEGURIDAD Y VIGILANCIA DEL 1 DE JUNIO DE 2016 AL 31 DE MAYO DE 2019
    ## 6:                                                  LICITACION PUBLICA ELECTRÓNICA
    ##    Clave CUCOP
    ## 1:        5410
    ## 2:        5410
    ## 3:        3990
    ## 4:        2510
    ## 5:        3380
    ## 6:        3450
    ##                                                              Título del expediente
    ## 1:                  CAMIONETA TIPO PICK UP y AUTOMOVIL SEDAN  LA-905011989-I1-2011
    ## 2:                  CAMIONETA TIPO PICK UP y AUTOMOVIL SEDAN  LA-905011989-I1-2011
    ## 3:                       Servicios Administrados de Monitoreo y Gestión 2 (SAMyG2)
    ## 4:                                           MATERIAL DE LABORATORIO REQ.3447-2015
    ## 5: SERVICIO DE SEGURIDAD Y VIGILANCIA DEL 1 DE JUNIO DE 2016 AL 31 DE MAYO DE 2019
    ## 6:   CONTRATACIÓN PLURIANUAL DEL SEGURO DE GASTOS MEDICOS MAYORES Y SEGURO DE VIDA
    ##                                           Plantilla del expediente
    ## 1:                V20151215 01. Licitación Pública Nacional LAASSP
    ## 2:                V20151215 01. Licitación Pública Nacional LAASSP
    ## 3:                V20151215 01. Licitación Pública Nacional LAASSP
    ## 4: V20151220 12. Adjudicación Directa Nacional Simplificada LAASSP
    ## 5:                                   01. Licitación Pública LAASSP
    ## 6:                                   01. Licitación Pública LAASSP
    ##    Fundamento legal Número del procedimiento Fecha de fallo
    ## 1:                      LA-905011989-I1-2011     2011-12-30
    ## 2:                      LA-905011989-I1-2011     2011-12-30
    ## 3:                     LA-006E00001-N71-2015     2015-09-28
    ## 4: Art. 41 fr. XVII   SA-011L4J995-N211-2015               
    ## 5:                    LA-012NBG001-E102-2016     2016-05-27
    ## 6:                    LA-020VST001-E107-2016     2016-12-16
    ##    Fecha de publicación Fecha de apertura Carácter del procedimiento
    ## 1:     2011-12-12 13:56  2011-12-29 00:00      Internacional Abierto
    ## 2:     2011-12-12 13:56  2011-12-29 00:00      Internacional Abierto
    ## 3:     2015-08-24 18:25  2015-10-22 09:00                   Nacional
    ## 4:     2015-07-23 09:03  2015-07-24 09:00                   Nacional
    ## 5:     2016-04-26 10:46  2016-05-16 08:30                   Nacional
    ## 6:     2016-11-17 09:05  2016-12-06 11:00                   Nacional
    ##    Tipo de contratación        Tipo de procedimiento
    ## 1:        Adquisiciones           Licitación Pública
    ## 2:        Adquisiciones           Licitación Pública
    ## 3:            Servicios           Licitación Pública
    ## 4:        Adquisiciones Adjudicación Directa Federal
    ## 5:            Servicios           Licitación Pública
    ## 6:            Servicios           Licitación Pública
    ##    Forma de participación Código del contrato Núm. de control del contrato
    ## 1:                  Mixta             2032577                           NA
    ## 2:                  Mixta             2032662                           NA
    ## 3:            Electrónica             1060250                           NA
    ## 4:            Electrónica             2024274                           NA
    ## 5:            Electrónica             1989889                           NA
    ## 6:            Electrónica             2021500                           NA
    ##                                                                                              Título del contrato
    ## 1:                                                                                   COMPRA DE AUTOMOVILES SEDAN
    ## 2:                                                                                     COMPRA DE VEHICULOS SEDAN
    ## 3:                                                     Servicios Administrados de Monitoreo y Gestión 2 (SAMyG2)
    ## 4:                                                                         MATERIAL DE LABORATORIO REQ.3447-2015
    ## 5: DATOS RELEVANTES DEL CONTRATO SERVICIO DE SEGURIDAD Y VIGILANCIA DEL 1 DE JUNIO DE 2016 AL 31 DE MAYO DE 2019
    ## 6:                                                                                  SEGURO DE VIDA INSTITUCIONAL
    ##    Fecha de inicio del contrato Fecha de fin del contrato
    ## 1:                   2019-03-29                2019-03-29
    ## 2:                   2019-03-29                2019-03-29
    ## 3:                   2019-03-01                2019-11-18
    ## 4:                   2019-01-25                2019-04-25
    ## 5:                   2019-01-01                2019-05-31
    ## 6:                   2019-01-01                2019-03-31
    ##    Importe del contrato Moneda del contrato Estatus del contrato
    ## 1:           1707172.00                 MXN             Expirado
    ## 2:           1723448.00                 MXN             Expirado
    ## 3:         210993802.32                 MXN               Activo
    ## 4:             21375.74                 MXN               Activo
    ## 5:           4035859.20                 MXN               Activo
    ## 6:           1377501.50                 MXN             Expirado
    ##    Convenio modificatorio Clave del programa federal
    ## 1:                      0                           
    ## 2:                      0                           
    ## 3:                      1                           
    ## 4:                      0                           
    ## 5:                      0                           
    ## 6:                      1                           
    ##    Fecha de firma del contrato                  Contrato marco
    ## 1:     2011-12-13 00:00:00 GMT No se utilizó el Contrato Marco
    ## 2:     2011-12-13 00:00:00 GMT No se utilizó el Contrato Marco
    ## 3:     2019-03-01 00:00:00 GMT No se utilizó el Contrato Marco
    ## 4:                             No se utilizó el Contrato Marco
    ## 5:     2019-01-02 00:00:00 GMT No se utilizó el Contrato Marco
    ## 6:     2018-12-14 00:00:00 GMT No se utilizó el Contrato Marco
    ##    Compra consolidada Contrato plurianual Clave de cartera SHCP
    ## 1:                  0                   0                      
    ## 2:                  0                   0                      
    ## 3:                  0                   0                      
    ## 4:                  0                   0                      
    ## 5:                  0                   1                      
    ## 6:                  0                   0                      
    ##    Folio en el RUPC          RFC
    ## 1:            51528 ACO740701LH0
    ## 2:               NA SMA0301178T2
    ## 3:             3851 MME000601MI7
    ## 4:            13598 ABM9905262R0
    ## 5:             4261 MSP020926VB5
    ## 6:             5118 R&S811221KR6
    ##                              Proveedor o contratista
    ## 1:                 AUTOMOTORES COAHUILENSES SA DE CV
    ## 2:                  SIERRA MADRE AUTOMOTRIZ SA DE CV
    ## 3:                       MICRONET DE MEXICO SA DE CV
    ## 4:        APPLIED BIOSYSTEMS DE MEXICO S DE RL DE CV
    ## 5: Multiproductos de Seguridad Privada, S.A. De C.V.
    ## 6:                             SEGUROS SURA SA DE CV
    ##    Estratificación de la empresa Clave del país de la empresa
    ## 1:                       Mediana                           MX
    ## 2:                       Pequeña                           MX
    ## 3:                     No MIPYME                           MX
    ## 4:                       Mediana                           MX
    ## 5:                       Mediana                           MX
    ## 6:                     No MIPYME                           MX
    ##    RFC verificado en el SAT Crédito externo Organismo financiero
    ## 1:                        1              NA                     
    ## 2:                        1              NA                     
    ## 3:                        1              NA                     
    ## 4:                        1              NA                     
    ## 5:                        1              NA                     
    ## 6:                        1              NA                     
    ##                                                                             Dirección del anuncio
    ## 1:   https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=70439
    ## 2:   https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=70439
    ## 3:  https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=644869
    ## 4:  https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=619393
    ## 5:  https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=850338
    ## 6: https://compranet.funcionpublica.gob.mx/esop/guest/go/opportunity/detail?opportunityId=1002282

``` r
sep_reconstruc %>% head
```

    ##    FECHA_EVENTO TIPO_EVENTO TIPO_INFRAESTRUCTURA        CCT
    ## 1:     07/09/17       Sismo              Escuela 07DBP0002N
    ## 2:     07/09/17       Sismo              Escuela 07DBT0009T
    ## 3:     07/09/17       Sismo              Escuela 07DBT0010I
    ## 4:     07/09/17       Sismo              Escuela 07DCC0037Y
    ## 5:     07/09/17       Sismo              Escuela 07DCC0049C
    ## 6:     07/09/17       Sismo              Escuela 07DCC0075A
    ##                                                                     DESC_CCT
    ## 1: CENTRO DE ESTUDIOS DE BACHILLERATO 6/3 PROFR. ELISEO MELLANES CASTELLANOS
    ## 2:                             CENTRO DE CAPACITACION TECNICA INDUSTRIAL 133
    ## 3:                             CENTRO DE CAPACITACION TECNICA INDUSTRIAL 112
    ## 4:                                                              JUAN ESCUTIA
    ## 5:                                                           JUSTICIA SOCIAL
    ## 6:                                                     JOSE SANTOS DEGOLLADO
    ##                                   DIRECCION_ESCUELA        NIVEL
    ## 1:                     CALLE TERCERA SUR ORIENTE 15 BACHILLERATO
    ## 2: PROLONGACIÓN INSURGENTES UNIDAD ADMINISTRATIVA 0 CAPACITACION
    ## 3:                 PROLONGACIÓN CALLE CENTRAL SUR 0 CAPACITACION
    ## 4:                                CALLE PRINCIPAL 0   PREESCOLAR
    ## 5:            CALLE ABSALON CASTELLANOS DOMINGUEZ 0   PREESCOLAR
    ## 6:                               CALLE CHIXTONTIC 0   PREESCOLAR
    ##    SOSTENIMIENTO MATRICULA ID_ENTIDAD_FEDERATIVA ENTIDAD_FEDERATIVA
    ## 1:       PÚBLICO       197                     7            CHIAPAS
    ## 2:       PÚBLICO       215                     7            CHIAPAS
    ## 3:       PÚBLICO      3163                     7            CHIAPAS
    ## 4:       PÚBLICO       118                     7            CHIAPAS
    ## 5:       PÚBLICO        35                     7            CHIAPAS
    ## 6:       PÚBLICO        48                     7            CHIAPAS
    ##    ID_MUNICIPIO                  MUNICIPIO ID_LOCALIDAD
    ## 1:           92                   TECPATÁN            1
    ## 2:           78 SAN CRISTÓBAL DE LAS CASAS            1
    ## 3:          101           TUXTLA GUTIÉRREZ            1
    ## 4:           93                   TENEJAPA           15
    ## 5:           61   OCOZOCOAUTLA DE ESPINOSA          526
    ## 6:           93                   TENEJAPA            9
    ##                        LOCALIDAD  LATITUD  LONGITUD
    ## 1:                      TECPATÁN 17.13427 -93.30786
    ## 2:    SAN CRISTÓBAL DE LAS CASAS 16.71291 -92.63568
    ## 3:              TUXTLA GUTIÉRREZ 16.73312 -93.12050
    ## 4:                        MATZAM 16.79000 -92.47786
    ## 5: ABSALÓN CASTELLANOS DOMÍNGUEZ 16.97833 -93.41861
    ## 6:                    CHIXTONTIC 16.89151 -92.45799
    ##       INFRAESTRUCTURA_VALORADA   NIVEL_GOBIERNO_VALORACION
    ## 1: Información en construcción Información en construcción
    ## 2:                          Sí                     Estatal
    ## 3:                          Sí                     Estatal
    ## 4:                          Sí                     Estatal
    ## 5:                          Sí                     Estatal
    ## 6:                          Sí                     Estatal
    ##    INSTANCIA_GOBIERNO_VALORACION  TIPO_DAÑO
    ## 1:   Información en construcción 2 Moderado
    ## 2:              PROTECCION CIVIL 2 Moderado
    ## 3:                       INIFECH    3 Menor
    ## 4:              Gobierno Estatal    3 Menor
    ## 5:              PROTECCI¿N CIVIL 2 Moderado
    ## 6:              PROTECCION CIVIL 2 Moderado
    ##                                                                                          DETALLE_DAÑO
    ## 1:                                         EDIF. ""M"": DAÑO SEVERO ESTRUCTURAL EN RIESGO DE COLAPSO.
    ## 2:   Muros divisorios de tabique, block o tablaroca colapsados o con grietas;Reconstrucción Parcial  
    ## 3:                                                                        Información en construcción
    ## 4:                                     1.-REPARACIÓN DE MUROS; 2.-REPARACIÓN DE CUARTEADURAS EN FIRME
    ## 5:   Muros divisorios de tabique, block o tablaroca colapsados o con grietas;Reconstrucción Parcial  
    ## 6:                                                                        Información en construcción
    ##                                                                                                                                                                                                                                         FOTOS_DAÑO
    ## 1:                                                                                                                                                                              http://143.137.111.153:8080/FMexico/foto/07DBP0002N/07DBP0002N.jpg
    ## 2: http://143.137.111.153:8080/FMexico/foto/07DBT0009T/ev1.JPG;http://143.137.111.153:8080/FMexico/foto/07DBT0009T/ev2.JPG;http://143.137.111.153:8080/FMexico/foto/07DBT0009T/ev3.JPG;http://143.137.111.153:8080/FMexico/foto/07DBT0009T/ev4.JPG
    ## 3:                                                                                                                                                                                     http://143.137.111.153:8080/FMexico/foto/07DBT0010I/ev1.jpg
    ## 4:                                                                                                                           http://dgdge.sep.gob.mx/fotosant/07DCC0037Y1_rgdRQ_ANT.jpg;http://dgdge.sep.gob.mx/fotosant/07DCC0037Y1_H9hEq_ANT.jpg
    ## 5: http://143.137.111.153:8080/FMexico/foto/07DCC0049C/ev1.PNG;http://143.137.111.153:8080/FMexico/foto/07DCC0049C/ev2.PNG;http://143.137.111.153:8080/FMexico/foto/07DCC0049C/ev3.PNG;http://143.137.111.153:8080/FMexico/foto/07DCC0049C/ev4.PNG
    ## 6:                                                                                                                                                                                                                                   No disponible
    ##    ESTATUS_OPERACION          FECHA_VERIFICACION
    ## 1:    En su inmueble Información en construcción
    ## 2:    En su inmueble Información en construcción
    ## 3:    En su inmueble Información en construcción
    ## 4:    En su inmueble                    14/10/17
    ## 5:    En su inmueble Información en construcción
    ## 6:    En su inmueble Información en construcción
    ##                                                                               URL_DICTAMEN
    ## 1:                    http://143.137.111.153:8080/FMexico/dictamen/07DBP0002N/Dictamen.pdf
    ## 2:                    http://143.137.111.153:8080/FMexico/dictamen/07DBT0009T/Dictamen.pdf
    ## 3:                    http://143.137.111.153:8080/FMexico/dictamen/07DBT0010I/Dictamen.png
    ## 4: http://dgdge.sep.gob.mx/Acciones_Sismos_SEPT2017/updFile/upPDF/07DCC0037Y1_CertAfec.pdf
    ## 5:                    http://143.137.111.153:8080/FMexico/dictamen/07DCC0049C/Dictamen.pdf
    ## 6:                    http://143.137.111.153:8080/FMexico/dictamen/07DCC0075A/Dictamen.pdf
    ##    COSTO_ORIGINAL_PRE MONTO_TOTAL_DE_ATENCIÓN_PRE MONTO_EJERCIDO_PRE
    ## 1:          No aplica                   No aplica          No aplica
    ## 2:          No aplica                   No aplica          No aplica
    ## 3:          No aplica                   No aplica          No aplica
    ## 4:              50000                       50000              50000
    ## 5:          No aplica                   No aplica          No aplica
    ## 6:          No aplica                   No aplica          No aplica
    ##                                                    DESC_OBRAS_PRE
    ## 1:                                                      No aplica
    ## 2:                                                      No aplica
    ## 3:                                                      No aplica
    ## 4: 1.-REPARACIÓN DE MUROS; 2.-REPARACIÓN DE CUARTEADURAS EN FIRME
    ## 5:                                                      No aplica
    ## 6:                                                      No aplica
    ##    FECHA_INICIO_PRE REPRESENTANTE_PADRES_PRE FECHA_FIN_PRE
    ## 1:        No aplica                No aplica     No aplica
    ## 2:        No aplica                No aplica     No aplica
    ## 3:        No aplica                No aplica     No aplica
    ## 4:         14/11/17    RANULFO GOMEZ RAMIREZ      13/12/17
    ## 5:        No aplica                No aplica     No aplica
    ## 6:        No aplica                No aplica     No aplica
    ##    INSTANCIA_RESPONSABLE_OBRAS_PRE COSTO_ORIGINAL_CIEN
    ## 1:                       No aplica           No aplica
    ## 2:                       No aplica             1500000
    ## 3:                       No aplica           No aplica
    ## 4:     Información en construcción           No aplica
    ## 5:                       No aplica           No aplica
    ## 6:                       No aplica           683391.37
    ##    MONTO_TOTAL_DE_ATENCIÓN_CIEN         MONTO_EJERCIDO_CIEN
    ## 1:                    No aplica                            
    ## 2:                   1337697.26 Información en construcción
    ## 3:                    No aplica                            
    ## 4:                    No aplica                            
    ## 5:                    No aplica                            
    ## 6:                    657107.09 Información en construcción
    ##                                                                  DESC_OBRAS_CIEN
    ## 1:                                                                     No aplica
    ## 2:                  CONSTRUCCION DE 02 AULAS DIDACTICAS ESTR RC Y OBRA EXTERIOR.
    ## 3:                                                                     No aplica
    ## 4:                                                                     No aplica
    ## 5:                                                                     No aplica
    ## 6: REHABILITACION DE EDIFICIOS, CONSTRUCCION DE SANITARIO RURAL Y OBRA EXTERIOR.
    ##    FECHA_INICIO_CIEN              FECHA_FIN_CIEN
    ## 1:         No aplica                   No aplica
    ## 2:          11/12/17 Información en construcción
    ## 3:         No aplica                   No aplica
    ## 4:         No aplica                   No aplica
    ## 5:         No aplica                   No aplica
    ## 6:          23/02/18 Información en construcción
    ##    INSTANCIA_RESPONSABLE_OBRAS_CIEN COSTO_ORIGINAL_FONDEN_API
    ## 1:                        No aplica                 No aplica
    ## 2:      Información en construcción                 No aplica
    ## 3:                        No aplica                 No aplica
    ## 4:                        No aplica                 No aplica
    ## 5:                        No aplica                    337392
    ## 6:      Información en construcción                 No aplica
    ##    MONTO_TOTAL_DE_ATENCIÓN_FONDEN_API   MONTO_EJERCIDO_FONDEN_API
    ## 1:                          No aplica                   No aplica
    ## 2:                          No aplica                   No aplica
    ## 3:                          No aplica                   No aplica
    ## 4:                          No aplica                   No aplica
    ## 5:        Información en construcción Información en construcción
    ## 6:                          No aplica                   No aplica
    ##                                                                    DESC_OBRAS_FONDEN_API
    ## 1:                                                                             No aplica
    ## 2:                                                                             No aplica
    ## 3:                                                                             No aplica
    ## 4:                                                                             No aplica
    ## 5: DEMOLICIÓN Y REMOCIÓN DE ESCOMBRO POR COLAPSO DE EDIFICIO Y RENTA DE 2 AULAS MÓVILES.
    ## 6:                                                                             No aplica
    ##    FECHA_INICIO_FONDEN_API        FECHA_FIN_FONDEN_API
    ## 1:               No aplica                   No aplica
    ## 2:               No aplica                   No aplica
    ## 3:               No aplica                   No aplica
    ## 4:               No aplica                   No aplica
    ## 5:                25/09/17 Información en construcción
    ## 6:               No aplica                   No aplica
    ##    INSTANCIA_RESPONSABL_OBRAS_FONDEN_API
    ## 1:                             No aplica
    ## 2:                             No aplica
    ## 3:                             No aplica
    ## 4:                             No aplica
    ## 5:                               INIFECH
    ## 6:                             No aplica
    ##    COSTO_ORIGINAL_FONDEN_RECONSTRUCCIÓN
    ## 1:                              2038357
    ## 2:                            No aplica
    ## 3:                            No aplica
    ## 4:                            No aplica
    ## 5:                              1035282
    ## 6:                            No aplica
    ##    MONTO_TOTAL_DE_ATENCIÓN_FONDEN_RECONSTRUCCIÓN
    ## 1:                   Información en construcción
    ## 2:                                     No aplica
    ## 3:                                     No aplica
    ## 4:                                     No aplica
    ## 5:                   Información en construcción
    ## 6:                                     No aplica
    ##    MONTO_EJERCIDO_FONDEN_RECONSTRUCCIÓN
    ## 1:          Información en construcción
    ## 2:                            No aplica
    ## 3:                            No aplica
    ## 4:                            No aplica
    ## 5:          Información en construcción
    ## 6:                            No aplica
    ##                                                                                                                   DESC_OBRAS_FONDEN_RECONSTRUCCIÓN
    ## 1: EDIF. ""B"": DEMOLICIÓN DE EDIFICIO EN RIESGO DE COLAPSO Y CONSTRUCCIÓN DE 3 AULAS DIDÁCTICAS EN ESTRUCTURA REGIONAL, RENTA DE 3 AULAS MÓVILES.
    ## 2:                                                                                                                                       No aplica
    ## 3:                                                                                                                                       No aplica
    ## 4:                                                                                                                                       No aplica
    ## 5:                                                    EDIF. ""A"": CONSTRUCCIÓN DE 2 AULA DIDÁCTICA ESTRUCTURA REGIONAL. RENTA DE 2 AULAS MÓVILES.
    ## 6:                                                                                                                                       No aplica
    ##    FECHA_INICIO_FONDEN_RECONSTRUCCIÓN FECHA_FIN_FONDEN_RECONSTRUCCIÓN
    ## 1:                           02/02/18     Información en construcción
    ## 2:                          No aplica                       No aplica
    ## 3:                          No aplica                       No aplica
    ## 4:                          No aplica                       No aplica
    ## 5:                           29/10/17     Información en construcción
    ## 6:                          No aplica                       No aplica
    ##    INSTANCIA_RESPONSABLE_OBRAS_FONDEN_RECONSTRUCCIÓN COSTO_ORIGINAL_SEGURO
    ## 1:                                           INIFECH             No aplica
    ## 2:                                         No aplica             No aplica
    ## 3:                                         No aplica             No aplica
    ## 4:                                         No aplica             No aplica
    ## 5:                                           INIFECH             No aplica
    ## 6:                                         No aplica             No aplica
    ##    MONTO_TOTAL_DE_ATENCIÓN_SEGURO MONTO_EJERCIDO_SEGURO DESC_OBRAS_SEGURO
    ## 1:                      No aplica             No aplica         No aplica
    ## 2:                      No aplica             No aplica         No aplica
    ## 3:                      No aplica             No aplica         No aplica
    ## 4:                      No aplica             No aplica         No aplica
    ## 5:                      No aplica             No aplica         No aplica
    ## 6:                      No aplica             No aplica         No aplica
    ##    FECHA_INICIO_SEGURO FECHA_FIN_SEGURO INSTANCIA_RESPONSABLE_OBRAS_SEGURO
    ## 1:           No aplica        No aplica                          No aplica
    ## 2:           No aplica        No aplica                          No aplica
    ## 3:           No aplica        No aplica                          No aplica
    ## 4:           No aplica        No aplica                          No aplica
    ## 5:           No aplica        No aplica                          No aplica
    ## 6:           No aplica        No aplica                          No aplica
    ##    COSTO_ORIGINAL_OTRO_ESTATAL MONTO_TOTAL_DE_ATENCIÓN_OTRO_ESTATAL
    ## 1:                   No aplica                            No aplica
    ## 2:                   No aplica                            No aplica
    ## 3: Información en construcción          Información en construcción
    ## 4:                   No aplica                            No aplica
    ## 5:                   No aplica                            No aplica
    ## 6:                   No aplica                            No aplica
    ##    MONTO_EJERCIDO_OTRO_ESTATAL     DESC_OBRAS_OTRO_ESTATAL
    ## 1:                   No aplica                   No aplica
    ## 2:                   No aplica                   No aplica
    ## 3: Información en construcción Información en construcción
    ## 4:                   No aplica                   No aplica
    ## 5:                   No aplica                   No aplica
    ## 6:                   No aplica                   No aplica
    ##      FECHA_INICIO_OTRO_ESTATAL      FECHA_FIN_OTRO_ESTATAL
    ## 1:                   No aplica                   No aplica
    ## 2:                   No aplica                   No aplica
    ## 3: Información en construcción Información en construcción
    ## 4:                   No aplica                   No aplica
    ## 5:                   No aplica                   No aplica
    ## 6:                   No aplica                   No aplica
    ##    INSTANCIA_RESPONSABLE_OBRAS_OTRO_ESTATAL
    ## 1:                                No aplica
    ## 2:                                No aplica
    ## 3:              Información en construcción
    ## 4:                                No aplica
    ## 5:                                No aplica
    ## 6:                                No aplica
    ##                                                                          URL_ACTA_ENTREGA
    ## 1:                                                            Información en Construcción
    ## 2:                                                            Información en Construcción
    ## 3:                                                            Información en Construcción
    ## 4: http://dgdge.sep.gob.mx/Acciones_Sismos_SEPT2017/updFile/upPDF/07DCC0037Y1_ActaCon.pdf
    ## 5:                                                            Información en Construcción
    ## 6:                                                            Información en Construcción
    ##    PORCENTAJE_AVANCE_PRE PORCENTAJE_AVANCE_CIEN
    ## 1:             No aplica              No aplica
    ## 2:             No aplica                     37
    ## 3:             No aplica              No aplica
    ## 4:                   100              No aplica
    ## 5:             No aplica              No aplica
    ## 6:             No aplica                     36
    ##    PORCENTAJE_AVANCE_FONDEN_APIN PORCENTAJE_AVANCE_FONDEN_RECONSTRUCCIÓN
    ## 1:                     No aplica                                      32
    ## 2:                                                                      
    ## 3:                                                                      
    ## 4:                                                                      
    ## 5:                           100                                     100
    ## 6:                                                                      
    ##    PORCENTAJE_AVANCE_SEGURO PORCENTAJE_AVANCE_OTRO_ESTATAL
    ## 1:                No aplica                      No aplica
    ## 2:                No aplica                      No aplica
    ## 3:                No aplica    Información en construcción
    ## 4:                No aplica                      No aplica
    ## 5:                No aplica                      No aplica
    ## 6:                No aplica                      No aplica

Notas:

-   el parsing de algunos archivos con read\_csv fallaba...

-   optamos por data.table::fread que tiene heuristicos mas robustos

-   los encodings no los conocemos... revisamos los colnames y escogemos con cual no tenemos errores
