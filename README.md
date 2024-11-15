# Cartucho para MSX compatible con KONAMI4
Este cartucho es compatible con el mapper KONAMI8K (KONAMI4).<BR>
Usa CPLD compatible con KONAMI VRC007431 y está disponible en dos versiones:<BR>
- Versión 1Mbit+FRAM (Misma configuración de bancos que GAME MASTER2<BR>
- Versión 4Mbit<BR>
** Ninguna versión incluye SCC<BR><BR>
Ambos cartuchos están disponibles para su compra aquí:<BR>
https://ifc.booth.pm/items/3092101<BR> 
https://ifc.booth.pm/items/3092120<BR>

## ■ Mapa de memoria

Especificaciones similares a las de KONAMI4. Tenga en cuenta que el espacio de memoria 0x4000-0x5FFF está fijo en BANK0<BR>
<BR>
| Page (8kB)                        | Switching address            | Initial segment | 
| --------------------------------- | ---------------------------- | --------------- | 
| 4000h ~ 5FFFh  | 5000h (mirrors: 5001h ~ 57FFh) | 0  (FIXED)      | 
| 6000h ~ 7FFFh  | 7000h (mirrors: 7001h ~ 77FFh) | 1               | 
| 8000h ~ 9FFFh  | 9000h (mirrors: 9001h ~ 97FFh) | 2               | 
| A000h ~ BFFFh  | B000h (mirrors: B001h ~ B7FFh) | 3               |

<BR>

| BIT7 | BIT6 | BIT5 | BIT4 | BIT3 | BIT2 | BIT1 | BIT0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| Unused | Unused | Segment[5] | Segment[4] | Segment[3] | Segment[2] | Segment[1] | Segment[0] |

Bit 0 ~ 5 = Segment number <BR>
Bit 6 ~ 7 = Unused <BR>
<BR>
En caso de que F-RAM esté habilitado, es como se indica a continuación:<BR>
| BIT7 | BIT6 | BIT5 | BIT4 | BIT3 | BIT2 | BIT1 | BIT0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| Unused | Unused | SRAM Segment | SRAM/ROM | Segment[3] | Segment[2] | Segment[1] | Segment[0] |

Bit 0 ~ 3 = Segment number <BR>
Bit 4 = 1 to select the SRAM (writable on page B000h ~ BFFFh only) <BR>
Bit 5 = SRAM segment select (two segments of 4kB available) <BR>
Bit 6 ~ 7 = Unused <BR>
<BR>
Referencia：
https://www.msx.org/wiki/MegaROM_Mappers#Game_Master_2_.28Konami.29


<BR>
Para las páginas mencionadas a continuación, el control Flash con el BIT7 ha sido ampliado para permitir la escritura en Flash.<BR>
  
| Page (8kB)                        | Switching address            | Initial segment | 
| --------------------------------- | ---------------------------- | --------------- | 
| A000h ~ BFFFh (mirror: 2000h ~ 3FFFh) | B000h (mirrors: B001h ~ B7FFh) | 3               |

<BR>
  
| BIT7 | BIT6 | BIT5 | BIT4 | BIT3 | BIT2 | BIT1 | BIT0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| Flash | Unused | Segment[5] | Segment[4] | Segment[3] | Segment[2] | Segment[1] | Segment[0] |

Bit 0 ~ 5 = Segment number <BR>
Bit 6 = Unused <BR>
Bit 7 = Flash control<BR>
<BR>
En caso de que F-RAM esté habilitado, es de la siguiente manera:<BR>
| BIT7 | BIT6 | BIT5 | BIT4 | BIT3 | BIT2 | BIT1 | BIT0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| Flash | Unused | SRAM Segment | SRAM/ROM | Segment[3] | Segment[2] | Segment[1] | Segment[0] |

Bit 0 ~ 3 = Segment number <BR>
Bit 4 = 1 to select the SRAM (writable on page B000h ~ BFFFh only) <BR>
Bit 5 = SRAM segment select (two segments of 4kB available) <BR>
Bit 6 = Unused <BR>
Bit 7 = Flash control<BR>
<BR>
## ■ Control de Flash
Para activar el modo de escritura, escriba 0x80 en la dirección 0xA000-0xBFFF.<BR>
La dirección de control de Flash 2AAAh está mapeada en 4AAAh, y la dirección de control 5555h está mapeada en 5555h.<BR>
Para realizar la escritura, utilice el rango de direcciones 6000h ~ 7FFFh.<BR>
Es posible que no se pueda escribir en otras áreas.<BR>
<BR>
Este producto utiliza Flash de la serie SST<BR>
El sistema CMD es prácticamente el mismo que el de los Flash AMD, pero las direcciones CMD son diferentes.<BR>
Además, tenga en cuenta que el comportamiento del BUSY también es diferente al de los Flash AMD, por lo que debe prestar atención a este detalle.<BR>
  
 | Page 4 | A000h - AFFFh (A000h used) set bit7 = 1'b1  |  Set Flash Write mode | 
 | ------ | ------------------------------------------- | ------------------ | 
 | Page 1 | 4000h - 4fffh = Flash Address 2000 - 2fffh (BA1) MSX:4AAAh  |  Flash:2AAAh |
 | Page 1 | 5000h - 5fffh = Flash Address 5000 - 5fffh (BA2) MSX:5555h  |  Flash:5555h |

CMD：
1. Set Flash mode         : 0xA000 = 0x80
1. Page Select            : 0x6000 = Segment
1. Flash Byte-Program 1st : 0x5555 = 0xAA
1. Flash Byte-Program 2nd : 0x4AAA = 0x55
1. Flash Byte-Program 3rd : 0x5555 = 0xA0
1. Write Data             : 0x6000～0x7fff = data
1. Page Reselect          : 0x6000 = Segment

Referencia:
https://www.microchip.com/wwwproducts/en/SST39SF010A
<BR>

## Programación de la memoria Flash
Como programa de escritura, se incluye el eseRC755. Inserte el cartucho correspondiente en uno de los slots y ejecute el siguiente comando。<BR>
`>eseRC755.com [Archivo ROM]`<BR>
<BR>
El código fuente se puede compilar con z88dk. Las opciones de compilación son las siguientes:<BR>
`zcc +msx -create-app -subtype=msxdos -lmsxbios  main.c -o eseRC755.com`<BR>

  
## ■ Versiones de PCB
Existen dos versiones:'Versión 1Mbit+FRAM / Versión 4Mbit<BR>
El que tiene un IC en IC1 es la 'Versión 1Mbit+FRAM', y el que no tiene IC es la 'Versión 4Mbit<BR>
<BR>
Al cortar J1 y añadir SW1, es posible desconectar el ROM. Agregue esto si le preocupa la conexión posterior.<BR>
Parte recomendada: SW1 IS-1245T-G [Switronic] (disponible en Akizuki Denshi).<BR>
<BR>
Si necesita el diagrama de circuitos y los archivos Gerber, consulte los archivos incluidos.<BR>
<BR>
## ■ Acerca del CPLD
Los datos grabados en el CPLD Xilinx XC9536XL son diferentes entre la Versión 1Mbit+FRAM y la Versión 4Mbit<BR>
Es posible crear cada versión modificando el USE_MRAM en el RTL.<BR>
<BR>

