##AFIP - Web Services

<br>
*Los denominados “Web Services” (ws) o Servicios Web, posibilitan que en vez de efectuar consultas o solicitudes desde “navegadores web” y esperar visualizar la respuesta en una página web, se efectúe la comunicación directamente entre sistemas implementados por los contribuyentes o consultantes y los sistemas o bases de datos de la AFIP.*

<br>
### Pasos iniciales

<br>
Los programadores de aplicaciones pueden solicitar acceso a los diversos webservices (denominados "servicios") que están disponibles en el ambiente de homologación de la AFIP.

Los programadores pueden obtener y gestionar sus certificados usando la aplicación web WSASS (Autoservicio de Acceso a APIs de Homologación) que está disponible accediendo con clave fiscal de Nivel 2.

Para poder acceder a un servicio, la aplicación a programar debe utilizar un certificado de seguridad, que se obtiene en este sitio. Entre otras cosas, el certificado contiene un Distinguished Name (DN) que incluye un CUIT. Cada DN será identificado por un "alias" o "nombre simbólico", que actúa como una abreviación.

Para obtener el certificado, distinguimos dos casos según si el DN ya fué dado de alta (DN existente) o si aún no existe.

Una vez generado el DN y obtenido el certificado, se puede solicitar autorización de acceso a los servicios de AFIP.

<br>
### Como generar una solicitud de certificado

<br>
Para obtener el certificado por primera vez, hay que dar de alta al DN. Para esto hay que presentar una "solicitud de certificado" o "Certificate Signing Request" (CSR).

El CSR se genera en su computadora, usando la herramienta OpenSSL (disponible para Windows, UNIX/Linux y MacOSX). Se hace en dos pasos, primero hay que generar una clave privada en formato PKCS#10 y segundo, generar el CSR. Para ello, la forma de ejecutar 'openssl' en la línea de comandos es así:

    openssl genrsa -out MiClavePrivada 1024

<br>
donde MiClavePrivada es el nombre del archivo elegido.

    openssl req -new -key MiClavePrivada -subj "/C=AR/O=subj_o/CN=subj_cn/serialNumber=CUIT subj_cuit" -out MiPedidoCSR

donde hay que reemplazar:
 * subj_o por el nombre de su empresa.
 * subj_cn por el nombre de su sistema cliente.
 * subj_cuit por la CUIT (sólo los 11 dígitos, sin guiones) de la empresa o del programador (persona jurídica). Observar que en el serialNumber debe escribirse "CUIT" seguido de un espacio en blanco y luego los 11 dígitos de la CUIT.
 * MiClavePrivada por el nombre del archivo de la clave privada generado antes.
 * MiPedidoCSR por el nombre del archivo CSR que se va a crear.

<br>
### Ejemplo

<br>
El programador Juan Pérez de la empresa Supersistemas SRL, cuya CUIT es 20-12345678-9, va a solicitar un certificado para usar en una aplicación que está programando, denominada "Supersistema Negriazul". Para ello deberá ejecutar en la línea de comandos lo siguiente:

    openssl genrsa -out ClavePrivadaJuanP 1024

    openssl req -new -key ClavePrivadaJuanP -subj "/C=AR/O=Supersistemas SRL/CN=Supersistema Negriazul/serialNumber=CUIT 20123456789" -out CSRJuanP
    
La salida en pantalla será similar a lo siguiente:


```bash
D:\juanp>openssl genrsa -out ClavePrivadaJuanP 1024
Generating RSA private key, 1024 bit long modulus
.......................++++++
.....................................................++++++
unable to write 'random state'
e is 65537 (0x10001)

D:\juanp>openssl req -new -key ClavePrivadaJuanP -subj "/C=AR/O=Supersistemas SR
L/CN=Supersistema Negriazul/serialNumber=CUIT 20123456789" -out CSRJuanP

D:\juanp>dir
05/08/2015  02:11 PM               887 ClavePrivadaJuanP
05/08/2015  02:11 PM               647 CSRJuanP
               2 File(s)          1,534 bytes
D:\juanp>
```

Si no hay errores, el contenido del archivo 'CSRJuanP' será utilizado al momento de obtener el DN y el certificado.

<br>
### Normativa

<br/>
<table width="95%" align="center">
    <thead>
        <tr>
            <th>#</th>
            <th>Título</th>
            <th>Número</th>
            <th>Fecha</th>
            <th>Estado</th>
            <th>Ubicación</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>R.G. AFIP Nº 2969/2010</td>
            <td>2969</td>
            <td>10/11/2010</td>
            <td>Vigente</td>
            <td>Resoluciones Generales</td>
        </tr>
        <tr>
            <td colspan="6">
            PROCEDIMIENTO. Régimen especial de emisión y almacenamiento electrónico de comprobantes originales. Codificación de las operaciones efectuadas. Código de Autorización Electrónico Anticipado "C.A.E.A.". Resolución General N° 2904. Resolución General N° 2926. Norma complementaria.
            </td>
        </tr>
        <tr>
            <td>2</td>
            <td>R.G. AFIP Nº 2955/2010</td>
            <td>2955</td>
            <td>04/11/2010</td>
            <td>Vigente</td>
            <td>Resoluciones Generales</td>
        </tr>
        <tr>
            <td colspan="6">
            Impuesto al Valor Agregado. Operaciones de venta de cosas muebles nuevas, locaciones y prestaciones de obras y/o servicios concertadas electrónicamente a través de "portales virtuales". Régimen especial de ingreso. Requisitos, plazos y demás condiciones.
            </td>
        </tr>
        <tr>
            <td>3</td>
            <td>R.G. AFIP Nº 2904/2010</td>
            <td>2904</td>
            <td>06/09/2010</td>
            <td>Vigente</td>
            <td>Resoluciones Generales</td>
        </tr>
        <tr>
            <td colspan="6">
            ADMINISTRACION FEDERAL DE INGRESOS PUBLICOS - Procedimiento. Régimen especial de emisión y almacenamiento electrónico de comprobantes originales. Codificación de las operaciones efectuadas. Resolución General Nº 2485, sus modificatorias y complementarias. Norma complementaria. Resolución General Nº 2757, su modificatoria y sus complementarias. Su derogación.
            </td>
        </tr>
    </tbody>
</table>

<br>
### Arquitectura

<br>
El intercambio de información entre AFIP y los Entes Externos (EE) se implementa a través de web services SOAP sobre transporte HTTPS.

Todos estos web services de negocio (WSN) están directamente accesibles a través de Internet, no se requiere el establecimiento de canales especiales de comunicaciones ni VPNs. El acceso a los WSN está regulado por otro WS llamado Web Service de Autenticación y Autorización (WSAA), el cual autentica a los clientes y les concede permiso de acceso a cada uno de los WSN mediante el otorgamiento de un Ticket de Acceso (TA). Cada TA es válido para un WSN en particular y tiene una validez limitada en el tiempo (actualmente, doce horas). El cliente será responsable de presentar al WSN el TA otorgado por el WSAA, de lo contrario el WSN rechazará su solicitud de acceso.

<br>
##### Autenticación y Autorización

<br>
La autenticación del cliente se realiza utilizando criptografía de clave pública basada en certificados digitales X.509. A estos efectos, AFIP actúa como Autoridad Certificante y emite estos certificados sin aplicar ningún cargo a los EE.

Una vez obtenido el certificado digital el EE llevará a cabo los trámites requeridos en cada caso para obtener la autorización inicial para acceder a un WSN y lo relacionará con un certificado digital.

El cliente solicitará al WSAA que le conceda un TA para un WSN en particular mediante el envío de una estructura CMS (ver PKCS#7, S/MIME) conteniendo la solicitud propiamente dicha (ver TRA en Manual para el desarrollador del WSAA) más su firma digital separada más su certificado X.509. Basado en el análisis de estos datos, el WSAA autenticará al cliente mediante la verificación de su firma digital y la comprobación en su base de datos de que el EE haya completado los trámites de autorización inicial para acceder al WSN para el cual solicita permiso en el TRA; si estos controles son superados, entonces el WSAA contestará devolviendo un TA, de lo contrario, devolverá un mensaje de error explicativo.

El cliente del WSN extraerá del TA dos componentes -Token y Sign- y los enviará junto con los datos de negocio en cada solicitud que le envíe al WSN.

Ambientes de ejecución Toda esta infraestructura está replicada en dos ambientes separados, el de Testing y el de Producción. El de Testing es provisto para que los EEs puedan probar sus desarrollos hasta que estén listos para pasar al ambiente de Producción. Como estos dos ambientes estan comletamente aislados uno del otro, los certificados digitales de uno no son válidos en el otro.

<br>
### Herramientas de análisis/depuración

<br>
En nuestra experiencia desarrollando y soportando aplicaciones relacionadas con web services y/o networking en general, hemos encontrado que es muy práctico (o casi necesario) disponer de algunas herramientas específicas para el manejo de certifica dos digitales y criptografía en general, para capturar y analizar el tráfico de red y para probar/depurar web services SOAP. En este sentido, podemos sugerir las siguientes herramientas, todas gratis, la mayoría disponibles para múltiples sistemas operativos:

Para el manejo de certificados digitales y criptografía de clave pública: OpenSSL. Esta herramienta forma parte standard de la mayoría del sistemas operativos Unix/GNU/Linux, en el caso de Windows, se puede obtener una versión binaria en: http://www.openssl.org/related/binaries.html
Para capturar/analizar tráfico de red en general, un popular “sniffer” es: WireShark. Esta herramienta suele formar parte de muchas distribuciones GNU/Linux, pero también puede obtenerse una versión binaria para Windows en: http://www.wireshark.org/
Para capturar/analizar tráfico HTTP en particular, una herramienta más limitada pero más simple de utilizar es: Fiddler. Esta herramienta sólo está disponible para entornos Windows y se puede descargar en: http://www.fiddler2.com/fiddler2/
Para probar/depurar web services SOAP, una herramienta que ha probado ser muy útil es soapUI. Esta herramienta puede obtenerse en: http://www.soapui.org/
Todas estas herramientas son de libre disponibilidad y en nuestra experiencia y la de otros usuarios, han probado ser muy útiles, pero no asumimos ninguna responsabilidad sobre su funcionalidad ni estamos en condiciones de dar soporte ni contestar consultas sobre su uso.

<br>
**Lista de autoridades certificantes reconocidas y utilizadas por AFIP para servicios SSL/TLS.**

<br>
Verifique que las mismas se encuentren incluidas en las aplicaciones informáticas que utilizan los Webservices del organismo.

<br>
<table width="95%" align="center">
    <thead>
        <tr>
            <th>Autoridad</th>
            <th>Certificado</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><a href="http://www.geotrust.com/">GeoTrust Global CA.</a></td>
            <td>
                <a href="http://www.afip.gob.ar/ws/WSAA/GeoTrust_Global_CA_DER.zip">Formato DER</a>
                <br>
                <a href="http://www.afip.gob.ar/ws/WSAA/GeoTrust_Global_CA.zip">Formato PEM</a>
            </td>
        </tr>
        <tr>
            <td><a href="https://www.comodo.com/">AddTrust External CA Root</a></td>
            <td>
                <a href="http://www.afip.gob.ar/ws/WSAA/ac_root_der.zip">Formato DER</a>
                <br>
                <a href="http://www.afip.gob.ar/ws/WSAA/ac_root_pem.zip">Formato PEM</a>
            </td>
        </tr>
    </tbody>
</table>


<br>
### WebService de Autenticación y Autorización

<br>
Para poder autenticarse ante el WSAA necesita obtener un certificado digital X.509 emitido por la CA (Autoridad Certificante) de AFIP, a tales efectos, deberá generar una clave privada y un CSR (Certificate Signing Request). Luego deberá enviar el CSR:En el entorno de Testing a través de la aplicación web WSASS (Autoservicio de Acceso a APIs de Homologación) que está disponible accediendo con clave fiscal de Nivel 2 como mínimo.En el entorno de Producción a través de nuestro portal [http://www.afip.gob.ar](http://www.afip.gob.ar ""), siguiendo los pasos indicados en la documentación:

<br>
 * Documentación
   * [Especificación Técnica del WebService de Autenticación y Autorización](http://www.afip.gob.ar/ws/WSAA/Especificacion_Tecnica_WSAA_1.2.2.pdf "")
   * [¿Cómo obtener el Certificado Digital para entorno de producción?](http://www.afip.gob.ar/ws/WSAA/wsaa_obtener_certificado_produccion.pdf "")
   * [¿Cómo asociar el Certificado Digital a un WSN (Web Service de Negocio)?](http://www.afip.gob.ar/ws/WSAA/wsaa_asociar_certificado_a_wsn_produccion.pdf "")

<br>
 * Ejemplos
   * [Java](http://www.afip.gob.ar/ws/WSAA/ejemplos/wsaa_client_java.tgz "")
   * [PHP](http://www.afip.gob.ar/ws/WSAA/ejemplos/wsaa-client-php.zip "")
   * [.NET](http://www.afip.gob.ar/ws/WSAA/ejemplos/wsaa_cliente_dotnet2-10.09.30.zip "")

<br>
 * URLs
   * Testing: [https://wsaahomo.afip.gov.ar/ws/services/LoginCms](https://wsaahomo.afip.gov.ar/ws/services/LoginCms "")
   * Producción: [https://wsaa.afip.gov.ar/ws/services/LoginCms](https://wsaa.afip.gov.ar/ws/services/LoginCms "")

<br>
### WebServices Disponibles

<br>
#####Actualización / Consulta PEMA

<br>
Permite a los prestadores PEMA actualizar los dispositivos DES para traslados, tránsito como asi también realizar la consulta de los mismos.

 * [Manual de desarrollador](http://www.afip.gob.ar/ws/WDIAUTIDES/ManualDesarrollador-WdiaUtiDEs.pdf "")
 * [README](http://www.afip.gob.ar/ws/WDEPMOVIMIENTOS/README.txt "")

<br>
#####Aprobar y Denegar Despachos INV (vitivinicultura)

<br>
Permite al Instituto Nacional de Vitivinicultura consultar, aprobar o denegar despachos que se encuentren bajo el régimen de vitivinicultura.

 * [Manual de desarrollador](http://www.afip.gob.ar/ws/WGESINV/wgesinv-ManualParaElDesarrollador.pdf "")
 * [README](http://www.afip.gob.ar/ws/WGESINV/README.txt "")
 * [Ejemplo (PHP)](http://www.afip.gob.ar/ws/WGESINV/ejemplos/wgesinv-client-php.zip "")

<br>
#####Bonos Fiscales Electrónicos (BFE)

<br>
Permite la autorización de comprobantes electrónicos, a fin de gestionar los Bonos en la Secretaría de Industria según RG 2557.

 * [Manual de desarrollador](http://www.afip.gob.ar/ws/WSBFE/WSBFE-ManualParaElDesarrollador.pdf "")
 * [Guía adicional](http://www.afip.gob.ar/ws/WSBFE/WSBFE-GuiaAdicionalParaElProgramador.pdf "")
 * [README](http://www.afip.gob.ar/ws/WSBFE/README.txt "")
 * [Ejemplo (.NET)](http://www.afip.gob.ar/ws/WSBFE/ejemplos/wsbfe_cliente_dotnet2-13.04.24.zip "")

<br>
#####Certificados (DNRPA)

<br>
Permite la consulta por parte de la Dirección Nacional de los Registros de la Propiedad Automotor de los certificados CETA (Certificado de Transferencia.

 * [Manual de desarrollador](http://www.afip.gob.ar/ws/WSCTA/WSCTA-ManualParaElDesarrollador.pdf "")
 * [README](http://www.afip.gob.ar/ws/WSCTA/README.txt "")
 * [Ejemplo (PHP)](http://www.afip.gob.ar/ws/WSCTA/ejemplos/wscta-client-php.zip "")

<br>
#####Código de Trazabilidad de Granos

<br>
Permite la obtención del Código de Trazabilidad de Granos para los contribuyentes del universo agropecuario.

 * [Manual de desarrollador (v1.1)](http://www.afip.gob.ar/cartaDePorte/documentos/Web%20Service%20CTGv1.1.pdf "")
 * [Manual de desarrollador (v2.0)](http://www.afip.gob.ar/cartaDePorte/documentos/WebServiceCTGv2.0.pdf "")
 * [Manual de desarrollador (v3.0)](http://www.afip.gob.ar/cartaDePorte/documentos/WebServiceCTGv3.0.pdf "")
 * [Diseño de registros XML (v1.1)](http://www.afip.gob.ar/cartaDePorte/documentos/CTGService_v1.1.wsdl "")
 * [README](http://www.afip.gob.ar/ws/WSCTG/README.txt "")
 * [AFIPRootDesaCA.zip](http://www.afip.gob.ar/ws/WSCTG/AFIPRootDesaCA.zip "")
 * [Ejemplo (PHP)](http://www.afip.gob.ar/ws/WSCTG/ejemplos/wsctg-client-php.zip "")

<br>
#####Constatación de Comprobantes (WSCDC)

<br>
Esta funcionalidad le permitirá verificar en forma dinámica si los comprobantes recibidos se encuentran autorizados por la AFIP.

 * [Manual de desarrollador (v0.1)](http://www.afip.gob.ar/ws/WSCDCV1/ManualDelDesarrolladorWSCDCV1.pdf "")

<br>
#####Consulta de Operaciones Cambiarias (C.O.C.)

 * [Manual de desarrollador (v2.1)](http://www.afip.gob.ar/ws/WSCOC/ManualDelDesarrollador_WSCOC_v2_1.pdf "")
 * [Manual de desarrollador (v2.2)](http://www.afip.gob.ar/ws/WSCOC/ManualDelDesarrolladorWSCOCv22.pdf "")
 * [Manual de desarrollador (v2.3)](http://www.afip.gob.ar/ws/WSCOC/ManualDelDesarrolladorWSCOCv23.pdf "")
 * [Manual de desarrollador (v2.4)](http://www.afip.gob.ar/ws/WSCOC/ManualDelDesarrolladorWSCOCv24.pdf "")
 * [Manual de desarrollador (v2.5)](http://www.afip.gob.ar/ws/WSCOC/ManualDelDesarrolladorWSCOCv25.pdf "")
 * [Manual de desarrollador (v2.6)](http://www.afip.gob.ar/ws/WSCOC/ManualDelDesarrolladorWSCOCv26.pdf "")
 * [Ejemplo (Java)](http://www.afip.gob.ar/ws/WSCOC/java/client_V1.zip "")
 * [Ejemplo (.NET)](http://www.afip.gob.ar/ws/WSCOC/net/wscoc_cliente-11.11.07_01/wscoc_cliente-11.11.07_01.zip "")

<br>
#####Consumir Comunicaciones de Ventanilla Electrónica

<br>
Permite consultar las comunicaciones que fueron publicadas en el sistema Ventanilla Electrónica.

 * [Manual de desarrollador](http://www.afip.gob.ar/ws/WSCComu/vecuwsconcomunicaciones.pdf "")

<br>
#####Coraza Electrónica de Seguridad (CES)

 * [Manual de desarrollador](http://www.afip.gob.ar/ws/WSCES/ManualDesa-wgesprecintosdepfis.pdf "")

<br>
#####Creación de VEPs para entidades externas

<br>
Permite crear un VEP y enviarlo a una entidad de pago. También permite consultar el estado (enviado, pagado, expirado) de un VEP creado mediante este servicio.

 * [Manual del desarrollador](http://www.afip.gob.ar/ws/WSCREATEVEP/Manual%20para%20el%20desarrollador%20del%20CreateVEP%20web%20service.pdf "")

<br>
#####Liquidación Primaria de Granos

 * [Manual del desarrollador (v1.0)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladorWSLPGV1.pdf "")
 * [Manual del desarrollador (v1.1)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladorWSLPGV11.pdf "")
 * [Manual del desarrollador (v1.2)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladoWSLPGV12.pdf "")
 * [Manual del desarrollador (v1.3)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladoWSLPGV13.pdf "")
 * [Manual del desarrollador (v1.4)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladorWSLPGV14.pdf "")
 * [Manual del desarrollador (v1.5)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladorWSLPGV15.pdf "")
 * [Manual del desarrollador (v1.6)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladorWSLPGV16.pdf "")
 * [Manual del desarrollador (v1.7)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladorWSLPGV17.pdf "")
 * [Manual del desarrollador (v1.8)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladorWSLPGV18.pdf "")
 * [Manual del desarrollador (v1.9)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladorWSLPGV19.pdf "")
 * [Manual de desarrollador (v1.10)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualDelDesarrolladorWSLPGV110.pdf "")
 * [Manual de desarrollador (v1.11)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualrWSLPGV111.pdf "")
 * [Manual de desarrollador (v1.12)](http://www.afip.gob.ar/ws/WSLiquiGranos/ManualrWSLPGV112.pdf "")
 * [Manual de desarrollador (v1.13)](http://www.afip.gob.ar/ws/WSLiquiGranos/manual_wslpg_1.13.pdf "")
 * [Manual de desarrollador (v1.14)](http://www.afip.gob.ar/ws/WSLiquiGranos/manual_wslpg_1.14.pdf "")
 * [Ejemplo (.NET)](http://www.afip.gob.ar/ws/WSLiquiGranos/wslpg%20_cliente_dotnet2-13.04.16.zip "")

<br>
#####Movimientos de Ingreso/Egreso para Terminales/Depositarios

<br>
Posibilita realizar salidas de zona primaria de importacion general, salida directo a plaza, salida zona primaria de exportacion, salida de contenedores.

 * [Manual del desarrollador](http://www.afip.gob.ar/ws/WDEPMOVIMIENTOS/wdepmovimientos-ManualParaElDesarrollador.pdf "")
 * [README](http://www.afip.gob.ar/ws/WDEPMOVIMIENTOS/README.txt "")

<br>
#####ONCCA Reporte Fiscal Compensaciones

<br>
WebService para envío automático por parte del organimso A.P.E. del Ministerio de Salud, de las Resoluciones de Revocación emitidas para el cobro de deudas que mantienen las Obras Sociales con dicho organismo. Estas RR son procesadas internamente por el sistema S.T.E. (Sistema de Transferencias Externas) que se encarga de retener de la distribución previsional de cada O.S. los fondos adeudados y transferirlos al A.P.E. a través de Notas de Transferencia enviadas al B.N.A. La funcionalidad descripta está reglamentada por el Decreto 213/04.

 * [README](http://www.afip.gob.ar/ws/WSONCCA/README.txt "")
 * [AFIPRootDesaCA.zip](http://www.afip.gob.ar/ws/WSONCCA/AFIPRootDesaCA.zip "")

<br>
#####Operación de Seguros de Caución (SEG)

<br>
Permite la autorización de comprobantes (cabecera y montos globales) para Facturación Electrónica para las empresas que emiten Seguros de Caución según RG 2668.

 * [Manual del desarrollador](http://www.afip.gob.ar/ws/WSSEG/WSSEG-ManualParaElDesarrollador.pdf "")
 * [Guía adicional](http://www.afip.gob.ar/ws/WSSEG/WSSEG-GuiaAdicionalParaElProgramador.pdf "")
 * [F 136 - Motivos](http://www.afip.gob.ar/ws/WSSEG/F136_MOTIVOS.txt "")
 * [README](http://www.afip.gob.ar/ws/WSSEG/README.txt "")
 * [Ejemplo (PHP)](http://www.afip.gob.ar/ws/WSSEG/ejemplos/wsseg-client-php.zip "")

<br>
#####Presentacion de DDJJ

<br>
Permite automatizar la presentación de DDJJ

 * [Manual del desarrollador](http://www.afip.gov.ar/genericos/ws/wsddjj/WSPresentaciondeDDJJManualparaelDesarrolladorv20140109.pdf "")

<br>
#####PUC Consulta Nivel 10

<br>
Permite consultar los siguientes datos de un contribuyente pasando como parámetro su cuit: datos de la persona y domicilios.

 * [Manual del desarrollador](http://www.afip.gob.ar/ws/WSPUC2/WSPN10-manual-desarrollador-v1.3.pdf "")

<br>
#####PUC Consulta Nivel 3

<br>
Permite consultar los siguientes datos de un contribuyente pasando como parámetro su cuit: datos de la persona, domicilios e impuestos inscriptos.

 * [Manual del desarrollador](http://www.afip.gob.ar/ws/WSPUC2/WSPN3-manual-desarrollador-v1.3.pdf "")

<br>
#####Recepción de Eventos de Entrada y Salida de Vehiculos

<br>
Posibilita la recepción de información de Chile relativa al Ingreso y egreso de vehículos (argentinos o correspondientes a dicho país) y consulta de los datos de egreso de un vehículo argentino.

 * [Manual del desarrollador](http://www.afip.gob.ar/ws/wEnysa/wEnysa-ManualDesarrollador.pdf "")

<br>
#####Régimen Percepción IVA

<br>
Permite la consulta por parte de los responsables obligados a actuar como agentes del Régimen especial de ingreso de IVA en el comercio por Internet a través de “portales virtuales” puedan consultar la situación de los sujetos pasibles, a fin de cumplir con lo establecido en la RG 2955/2010.

 * [Manual del desarrollador](http://www.afip.gob.ar/ws/WSRGIVA/WSRGIVA-ManualParaElDesarrollador.pdf "")
 * [README](http://www.afip.gob.ar/ws/WSRGIVA/README.txt "")
 * [Ejemplo (PHP)](http://www.afip.gob.ar/ws/WSRGIVA/ejemplos/cliente-php.zip "")

<br>
#####Seguimiento Vehicular

<br>
Usado por los prestadores OLS para informar las posiciones geográficas y estado de los Precintos Electrónicos de Monitoreo Aduanero instalados en los contenedores en tránsito.

 * [Manual del desarrollador](http://www.afip.gob.ar/ws/WSSV/WSSV-ManualParaElDesarrollador.pdf "")
 * [Manual de generación de rutas con GoogleEarth](http://www.afip.gob.ar/ws/WSSV/ManualDeGeneracionDeRutasConGoogleEarth.pdf "")
 * l[Ruta de calibración](http://www.afip.gob.ar/ws/WSSV/RutaDeCalibracion.pdf "")
 * [Agregar datos de rutas](http://www.afip.gob.ar/ws/WSSV/AgregarDatosDeRutas.pdf "")
 * [README](http://www.afip.gob.ar/ws/WSSV/README.txt "")
 * [Ejemplo (PHP)](http://www.afip.gob.ar/ws/WSSV/ejemplos/wssv-client-php.zip "")

<br>
#####TRABAJO_F931

<br>
Permite consultar información de las Declaraciones Juradas F931 registradas en la base de datos de Seguridad Social de AFIP. Actualmente es un servicio exclusivo habilitado para el Ministerio de Trabajo, Empleo y Seguridad Social Permite acceder a la siguiente información de las Declaraciones Juradas F931 registradas en la base de datos de Seguridad Social de AFIP:

 * Consultas de presentación para una relación Empleador / Empleado / Período Fiscal. El webservice busca la declaración jurada del período (de no encontrar presentación, se busca hasta 3 períodos anteriores) e informa:
   * PERIODO FISCAL de la DJ.
   * REMUNERACIÓN TOTAL declarada para el empleado en la DJ.
   * REMUNERACIÓN IMPONIBLE DE APORTES DE SEGURIDAD SOCIAL declarada para el empleado en la DJ.
   * REMUNERACIÓN IMPONIBLE DE CONTRIBUCIONES DE SEGURIDAD SOCIAL declarada para el empleado en la DJ. *Nota: el período fiscal solicitado debe estar dentro de los últimos 12 meses*.
 * Consultas de presentación para una relación Empleador / Período. El webservice busca la declaración jurada del período (de no encontrar presentación, se busca hasta 3 períodos anteriores) e informa:
   * REMUNERACIÓN TOTAL declarada en la DJ determinativa.
   * REMUNERACIÓN IMPONIBLE DE APORTES DE SEGURIDAD SOCIAL declarada en la DJ determinativa.
   * CANTIDAD DE EMPLEADOS declarados en la DJ. *Nota: el período fiscal solicitado debe estar dentro de los últimos 12 meses.*
   * [Manual del desarrollador](http://www.afip.gob.ar/ws/TRABAJO_F931/TrabajoF931-ManualParaElDesarrollador.pdf "")
   * [README](http://www.afip.gob.ar/ws/TRABAJO_F931/README.txt "")
   * [Casos de prueba](http://www.afip.gob.ar/ws/TRABAJO_F931/WS_SIPA-Casosdeprueba.xls "")
   * [Ejemplo (PHP)](http://www.afip.gob.ar/ws/TRABAJO_F931/ejemplos/TrabajoF931_Cliente-php.zip "")
   * [Ejemplo (.NET)](http://www.afip.gob.ar/ws/TRABAJO_F931/ejemplos/TrabajoF931_ClienteDemoDotNet_v1.0.0.0.zip "")


<br>
###Facturación electrónica

<br>
#####Introducción

<br>
Esta sección brinda asistencia inicial sobre aspectos técnicos de software, a los DESARROLLADORES EXTERNOS que necesitan consumir de nuestros webservices SOAP para Facturación Electrónica, en nuestro entorno de TESTING exclusivamente.

En caso de tener dudas acerca del webservice que necesita usar en su caso, sugerimos consulte ese tema con el área de Normativa. Para consultas sobre Normativa de Factura Electrónica: enviar su consulta a facturaelectronica@afip.gov.ar.

<br>
#####Webservices de factura electrónica disponibles

<br>
Actualmente están disponibles los siguientes webservices SOAP de Factura Electrónica:

 * Web Service de Factura Electrónica V0 ("wsfe"), para la mayoría de quienes emiten comprobantes electrónicos de tipos "A" y "B". Reemplazado por "wsfev1" desde el 1-jul-2011, ver e).
 * Web Service de Bonos Fiscales Electrónicos V0 ("wsbfe"), para quienes facturen bienes de capital y deseen acceder al beneficio de los Bonos Fiscales Electrónicos otorgados por el Ministerio de Economía.
 * Web Service de Seguros de Caución ("wsseg"), para las empresas de Seguros que necesitan emitir pólizas de Seguros de Caución.
 * Web Service de Factura Electronica de Exportacion V0 ("wsfex"), para empresas exportadoras que emiten comprobantes tipo "E".
 * Web Service de Factura Electrónica V1 ("wsfev1"), comprobantes "A", "B" y "C" sin detalle de ítem, CAE y CAEA (solo para "A" y "B"). Reemplaza al anterior WSFE.
 * Web Service de Factura Electrónica ("wsmtxca"), comprobantes "A" y "B" con detalle de items, CAE y CAEA.
 * Web Service de Bonos Fiscales Electrónicos V1 ("wsbfev1"). Reemplaza al anterior WSBFE.
 * Web Service de Factura Electrónica de Exportación V1 ("wsfexv1"). Reemplaza al anterior WSFEX.

<br>
En la lista siguiente se indica la documentación disponible, según el webservice específico:

 * Para "wsfev1", consultar: [Manual del desarrollador (v2.6)](http://www.afip.gob.ar/fe/documentos/manualdesarrolladorCOMPGv26.pdf "")
 * Para "wsbfev1", consultar: [Manual del desarrollador (v1.1)](http://www.afip.gob.ar/fe/documentos/WSBFEv1%20-%20Manual%20para%20el%20desarrollador.pdf "")
 * Para "wsseg", consultar: [Manual del desarrollador (v0.2)](http://www.afip.gob.ar/ws/WSSEG/WSSEG-ManualParaElDesarrollador.pdf "")
 * Para "wsfexv1", consultar: [Manual del desarrollador (v1.2)](http://www.afip.gob.ar/fe/documentos/WSFEX-Manualparaeldesarrollador_V1_2.pdf "")
 * Para "wsmtxca", consultar: [Manual del desarrollador (v0.2)](http://www.afip.gob.ar/fe/documentos/manualdesarrolladormtx_v0_2.pdf "")

<br>
###WSASS (Autogestión de certificados para Servicios Web en los ambientes de homologación)

<br>
Para los desarrolladores que quieran implementar aplicaciones que consuman de los servicios web de AFIP, se dispone del WSASS, es una aplicación web que permite al usuario gestionar sus certificados. Para solicitar el nuevo servicio, se debe pedir por medio de [www.afip.gob.ar](http://www.afip.gob.ar "") autenticándose con clave fiscal y un mínimo nivel de autorización 2:

En caso de tener dudas acerca de la informacion de estos pasos , comunicarse con mayuda@afip.gob.ar
Para consultas sobre el entorno de Homologacion o Producción: enviar su consulta por la seccion disponible en el servicio WSASS.

Una vez que posea el servicio de WSASS podrá auto-gestionarse el certificado digital en forma interactiva (Véase manual en el menú “Introducción” de WSASS).

<br>
###Consideraciones generales

 * [Generación de Certificados Digitales para Utilización con Webservices AFIP](http://www.afip.gob.ar/ws/WSAA/WSAA.ObtenerCertificado.pdf "")
 * [Delegación de Webservices AFIP con el Administrador de Relaciones](http://www.afip.gob.ar/ws/WSAA/ADMINREL.DelegarWS.pdf "")
 * [Cliente Login CMS](http://www.afip.gob.ar/ws/WSAA/ClienteLoginCms_VB.exe.zip "")
