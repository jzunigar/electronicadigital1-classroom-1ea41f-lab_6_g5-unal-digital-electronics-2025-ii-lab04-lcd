# Lab04 - Visualización usando pantalla LCD 16x2

# Integrantes

- Sebastián Zuñiga
- ___________________
- ___________________

# Informe

## Índice

1. [Diseño implementado](#diseño-implementado)
2. [Simulaciones](#simulaciones)
3. [Implementación](#implementación)
4. [Conclusiones](#conclusiones)
5. [Referencias](#referencias)

---

# Diseño implementado

## Descripción

El objetivo de este laboratorio fue comprender el funcionamiento de una pantalla LCD 16x2 y su control mediante una Máquina de Estados Finitos (FSM) implementada en Verilog sobre una FPGA Altera Cyclone IV.

Las pantallas LCD basadas en el controlador HD44780 requieren una secuencia específica de inicialización antes de poder mostrar información. Para realizar este proceso se empleó una FSM encargada de enviar los comandos de configuración necesarios y posteriormente los datos correspondientes a los caracteres que deben visualizarse.

La comunicación con la pantalla se realizó en modo paralelo de 4 bits utilizando las señales de control RS y E, junto con las líneas de datos D4-D7.

Durante la primera parte del laboratorio se implementó un mensaje estático compuesto por dos líneas de texto. Posteriormente, en la segunda parte, se modificó el diseño para visualizar información dinámica proveniente de entradas externas representadas mediante switches de la FPGA.

## Diagramas

### Arquitectura general

```text
                 +----------------------+
                 |   FPGA Cyclone IV    |
                 |                      |
Switches ------->| Lógica de Conversión |
                 |                      |
                 |      FSM LCD         |
                 |                      |
                 +----------+-----------+
                            |
                            |
                            v
                   +----------------+
                   |    LCD 16x2    |
                   |                |
                   | RS             |
                   | E              |
                   | D4-D7          |
                   +----------------+
```

### Máquina de estados simplificada

```text
             +---------+
             | RESET   |
             +----+----+
                  |
                  v
        +------------------+
        | Inicialización   |
        +--------+---------+
                 |
                 v
        +------------------+
        | Configuración    |
        +--------+---------+
                 |
                 v
        +------------------+
        | Borrar Pantalla  |
        +--------+---------+
                 |
                 v
        +------------------+
        | Escribir Línea 1 |
        +--------+---------+
                 |
                 v
        +------------------+
        | Escribir Línea 2 |
        +--------+---------+
                 |
                 v
        +------------------+
        | Espera/Refresh   |
        +------------------+
```

La FSM controla la secuencia de comandos y datos necesarios para que la pantalla funcione correctamente y muestre la información requerida.

---

# Simulaciones

Las simulaciones se realizaron utilizando el testbench suministrado en el repositorio del laboratorio.

Durante las pruebas se verificó:

- Correcta inicialización de la pantalla LCD.
- Funcionamiento de la FSM.
- Generación adecuada de la señal Enable.
- Escritura correcta de caracteres.
- Posicionamiento adecuado del cursor.
- Actualización de datos dinámicos.

## Parte 1

Se comprobó la visualización del mensaje estático:

```text
BATERIA 1
BATERIA 2
```

La simulación mostró que cada carácter era enviado correctamente mediante los códigos ASCII correspondientes.

## Parte 2

Se modificó el diseño para incluir información dinámica utilizando tres switches como entrada.

Los switches representan un número binario de 3 bits con rango de 0 a 7. Dicho valor fue convertido a su representación ASCII para ser mostrado en la pantalla LCD.

Durante la simulación se verificó que los cambios en los switches producían una actualización correcta del número visualizado en cada línea.

### Evidencias

Agregar capturas de:

- GTKWave
- ModelSim
- Digital

Mostrando las señales:

- Clock
- Estado de la FSM
- RS
- E
- Bus de datos

---

# Implementación

La implementación se realizó sobre una FPGA Altera Cyclone IV utilizando el conector destinado para pantallas LCD presente en la tarjeta de desarrollo.

## Configuración física

La pantalla LCD fue conectada al header correspondiente verificando que el pin 1 de la pantalla coincidiera con el pin 1 indicado en la PCB de la tarjeta.

Las conexiones utilizadas fueron:

| LCD | Función |
|------|---------|
| VSS | GND |
| VDD | +5V |
| RW | GND |
| RS | Control |
| E | Enable |
| D4 | Datos |
| D5 | Datos |
| D6 | Datos |
| D7 | Datos |

## Configuración en Quartus

Para habilitar el funcionamiento correcto del módulo LCD fue necesario configurar el pin nCEO como entrada/salida regular:

1. Assignments → Device.
2. Device and Pin Options.
3. Dual-Purpose Pins.
4. Seleccionar la opción:

```text
Use as regular I/O
```

Posteriormente se compiló el proyecto y se programó la FPGA mediante Quartus Programmer.

## Parte 1: Visualización estática

Se implementó el diseño suministrado en el laboratorio para mostrar el siguiente mensaje:

```text
BATERIA 1
BATERIA 2
```

La pantalla mostró correctamente ambos renglones, verificando el correcto funcionamiento de la FSM y de la comunicación con el módulo LCD.

## Parte 2: Visualización dinámica

Posteriormente se modificó el diseño para permitir la visualización de información dinámica.

Se utilizaron tres switches de la FPGA para generar un número binario de 3 bits entre 0 y 7.

El valor obtenido fue convertido a código ASCII y enviado a la pantalla LCD, reemplazando el dígito mostrado junto al texto "BATERIA".

Por ejemplo:

```text
BATERIA 0
BATERIA 0
```

```text
BATERIA 3
BATERIA 3
```

```text
BATERIA 7
BATERIA 7
```

Dependiendo de la posición de los switches, el número mostrado se actualizaba automáticamente.

La implementación permitió comprobar el correcto funcionamiento de la lógica de conversión y visualización de datos en tiempo real.

---

# Conclusiones

1. Se comprendió el funcionamiento de una pantalla LCD 16x2 y los requerimientos necesarios para su inicialización y control.

2. Se analizó una Máquina de Estados Finitos implementada en Verilog para gestionar la comunicación entre la FPGA y la pantalla LCD.

3. Se verificó mediante simulación el correcto envío de comandos y caracteres hacia el módulo LCD.

4. Se implementó exitosamente el diseño sobre una FPGA Altera Cyclone IV obteniendo los resultados esperados.

5. Se modificó el sistema para visualizar información dinámica utilizando switches como fuente de datos.

6. Se reforzaron conceptos relacionados con FSM, representación ASCII, diseño digital y control de periféricos mediante hardware programable.

7. El laboratorio permitió evidenciar la utilidad de las pantallas LCD como interfaz de usuario en sistemas digitales embebidos.

---

# Referencias

1. Datasheet del controlador HD44780.
2. Datasheet LCD 16x2 Alfanumérica.
3. Guía de laboratorio 04 - Visualización usando pantalla LCD 16x2.
4. Manual de usuario de Quartus Prime.
5. Documentación oficial de Verilog HDL.
6. Altera Cyclone IV Device Handbook.

