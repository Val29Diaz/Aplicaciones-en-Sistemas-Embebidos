---

# Parcial – Aplicaciones en Sistemas Embebidos

Repositorio con el desarrollo del Parcial en base a las clases teorico-practicas donde se define microcontroladores vs microprocesadores; arquitecturas, tipos etc. `` 

---

## 1. Preguntas Teoricas

## Que son microcontroladores y los microprocesadores

Los **Microcontroladores**, son circuitos integrados (conjunto de componentes en un mismo chip) que permite el control, programacion y aplicacion en diferentes sistemas. se caracterizan por tener bajo consumo de energia y tamaño reducido.

Los **Microprocesadores**, Son  circuitos integrados que realizan funciones de unidad central de procesamiento (CPU) de un sistema. Ellos necesitan de una memoria y perifericos externos para funcionar a diferencia de los microcontroladores.

### Arquitectura Von Neuman
Es un modelo de organizacion de computadores en el cual los datos y las instrucciones del programa se almacenan en la misma memoria y se utilizan los mismo buses para acceder a ambos.

| Ventajas                           | Desventajas          | Características                                 |
| ---------                          | ---------------      | ----------------------------------------------- |
| Diseño mas simple                  | Cuello de botellas   | Memoria unica para datos y programas            |
| menor complejidad en hardware      | menos velocidad      | Un solo bus de comunicación                     |
| Mas flexible para programacion     |                      | Ejecucion secuencial de instrucciones           |

### Arquitectura Harvard
Es un modelo de organizacion que usa memorias separadas para las instrucciones de los programas y datos, permite leer instruciones y datos al mismo tiempo aumentando el rendimiento de el mismo.

| Ventajas                  | Desventajas               | Características                                 |
| ---------                 | ---------------           | ----------------------------------------------- |
| Rendimiento (velocidad)   |  Uso de varias memorias   | Memoria separada para instrucciones y datos     |
| acceso simultáneo         |  Costo                    | Usa varios buses de comunicación                |
| Eficiencia                |  Hardware mas complejo    | eficiencia en acceso a memoria                  |

### Diferencias entre una arquitecturas

| **Von neuman**     |**Harvard**      |                                
| -------------      | --------------- | 
| Una memoria        | Varias memorias | 
| Diseño sencillo    | Complejo        | 
| cuello botella     | Eficiencia      | 


* ## **Procesadores RISC** 

Se caracterisan por tener un conjunto amplio y complejo de instruciones, que permiten realizar varias operaciones en una sola instruccion. ejemplo **x86**

* ## **Procesadores CISC**

Se caracterisan por tener un conjunto reducido de instrucciones sencillas **ARM**  

### ARM


|**Ventajas**               | **Desventajas**           | **Características**                             | **Es muy usado en la actualidad** |
| ------------------------- | ---------------------     | ----------------------------------------------- |----------------------------       |
| Rendimiento (velocidad)   |  Uso de varias memorias   | Memoria separada para instrucciones y datos     |
| acceso simultáneo         |  Costo                    | Usa varios buses de comunicación                |
| Eficiencia                |  Hardware mas complejo    | eficiencia en acceso a memoria                  |


### Arduino
<img width="919" height="593" alt="image" src="https://github.com/user-attachments/assets/5ef1b6e9-f52d-4dd8-88cc-6fa69f6e374e" />

La arquitectura del arduino es RISC (AVR) sigue un modelo Harvard con memoria.
* Permite ejecutar instrucciones en un solo ciclo de reloj.
* Bajo consumo de energia


### PIC16F887
<img width="798" height="383" alt="image" src="https://github.com/user-attachments/assets/ec2ae7ce-5a8d-4fb1-9907-026a7110f3a3" />

Es un microcontrolador de la familia de PIC fabricado por Microchip y esta basado en arquitectura RISC.
* Memoria flash
* Perisfericos integrados
* Concversor ADC

---

## 2. Preguntas Practicas

