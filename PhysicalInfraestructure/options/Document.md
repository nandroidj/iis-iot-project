# Infrastructura física

### Seleccion de la tecnología a emplear:

Se estudiaron 3 opciones : GSM/LTE, SigFox y Lora. 

Se observó que a nivel general la zona no posee una buena cobertura GSM/LTE(se adjuntan los documentos correspondientes obtenidos del link https://www.nperf.com/es/map/AR/-/152383.Claro-Mvil/signal/?ll=-40.42813457627493&lg=-63.584999999999994&zoom=4). 

Por lo cual se descarta utilizar GSM/LTE.

Para seleccionar entre Lora y SigFox se presenta la siguiente tabla:

|                         | SigFox                | Lora             |    
|:-----------------------:|:---------------------:|:----------------:|
| Ancho de banda          | 100 Hz                | 250 Khz          |
| Bidireccional?          | Limitado/half duplex  | Si/half duplex   |  
| Maximos mensajes (p/dia)|  140                  |Ilimitado        |    
| Longitud carga útil     | Max 12 bytes          | Max 243 bytes    |
| Alcance                 | 10km Urbano 40km rural| 5K Urbano 20Km rural |  
| Autenticación/cifrado   | NO   | SI
| Consumo bateria al Tx   |~230mA,3.3V=>0.759W(**) | 0.633W(***)
| precio chip/nodo        | 6.94 (****)AX-SFE-1-01-TX30 | 13.91 (*****) LR1120IMLTRT

(** )https://development.libelium.com/sigfox_networking_guide/hardware

(*** )http://www.multitech.net/developer/products/multiconnect-conduit-platform/accessory-cards/mtac-lora/mtac-lora-power-draw/

(**** )https://ar.mouser.com/ProductDetail/onsemi/AX-SFEU-1-01-TX30?qs=tCMd4XlZ%2FiDiekwIKrlBBw%3D%3D

(***** )https://ar.mouser.com/ProductDetail/Semtech/LR1120IMLTRT?qs=MyNHzdoqoQKzng%252BALV%2FZpQ%3D%3D

Aún cuando ambas tecnologías son buenas candidatas, el hecho de que sigfox tenga una carga útil de 12bytes inclina la balanza a utilizar Lora.

Dentro de las bandas en que puede ser utilizo Lora, se selecciona la correspondiente a 915Mhz ya que se encuentra dentro de las bandas no "Licenciadas" según la RESOL-2019-4653-APN-ENACOM#JGM

## Capa de percepción:
Para seleccionar los nodos sensores se parte de la premisa que deben poder utilizarse en el territorio nacional y que sean de bajo costo.
Para utilizarce los mismos deben estar inscriptos en el RAMATEL, o en su defecto poderse inscribir(se deberá realizar los ensayos correspondientes en un laboratorio y luego hacer la presentación en Enacom). Generalmente, si un dispositivo cumple con normas FCC el ensayo de laboratorio es un mero trámite ya que se corrobora su funcionamiento. Para la importación/comercialización del mismo en el territorio nacional, la firma importadora debe estar inscripta también en el organismo con un representante Técnico y un representante legal. El representante técnico es quien gestiona en Enacom el proceso.
Una vez finalizado, se entrega un nro de aprobación que debe estar impreso en el dispositivo y sea legible. La validez del certificado de aprobación es de 3 años.

La lista de laboratorios acreditados para realizar los ensayos se puede obtener de : https://www.enacom.gob.ar/laboratorios-acreditados_p349

Mientras que el representante técnico se puede generar desde el sistema Hertz. 

* Opción 1: 

<img src="./nodes/sensor1.webp" width="20%">

Marca: TekTelic (Canada)

Modelo: T0005982

Precio: 136.4 USD FOB (4/8/2022)

Distribuidor(link): https://www.digikey.com

Bateria: 10 años

Proteccion: ip67

Temperatura de operacion: -40ºC a 65ºC

Parámetros Lora: 

                 RF Power: 23 dBm       
                 RF Sensitivity: -137 dBm
                 Bandas : todas las bandas globales
                 Lora device Class: Class (A)
                 Normativas: FCC 15.247 RSS-247 y FCC 15.209 RSS-Gen


* Opción 2:

<img src="./nodes/sensor2.jpg" width="20%">

Marca: Milesight

Modelo: EM500-SMTC

Precio: 630 eur

Distribuidor(link): https://www.reichelt.com/

Bateria: 4-10 años

Protección: ip66

Temperatura de operacion: -30ºC + 70ºC

Parámetros Lora:
                
                RF Power: 20dBm
                RF Sensitivity: -147 dBm
                Frecuency: US915
                Lora device Class: Class (A)
                FCC: aprobado(documentación adjunta)



