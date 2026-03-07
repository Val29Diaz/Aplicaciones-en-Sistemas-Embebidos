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











Te voy a ayudar a escribir **la Parte Empírica completa como si fuera para tu README de GitHub o informe**, manteniendo coherencia con el trabajo anterior que hiciste con **MediaPipe + Arduino + visión artificial**.
Además incluiré:

* Explicación conceptual
* Paso a paso del desarrollo
* Código
* Explicación línea por línea
* Mejoramiento del código

---

# Parte Empírica

## Detección de puntos del pulgar utilizando MediaPipe

## 1. Descripción del problema

En esta sección del laboratorio se plantea el desarrollo de un **algoritmo sencillo de visión artificial** que permita detectar únicamente los **puntos (landmarks) del pulgar de la mano** utilizando la librería **MediaPipe Hands**.

El objetivo es demostrar cómo un sistema embebido con visión artificial puede identificar partes específicas del cuerpo humano, en este caso **el pulgar**, a partir del reconocimiento de puntos clave de la mano.

Este tipo de tecnologías se utilizan en aplicaciones como:

* Interfaces gestuales
* Control de dispositivos sin contacto
* Sistemas de realidad aumentada
* Sistemas de asistencia para personas con discapacidad

El sistema utiliza la cámara web para capturar imágenes en tiempo real y MediaPipe para detectar **21 puntos clave de la mano**, de los cuales se seleccionan únicamente los correspondientes al pulgar.

---

# 2. Funcionamiento de MediaPipe Hands

La librería **MediaPipe Hands** es un modelo de aprendizaje automático desarrollado por Google que permite detectar y rastrear **21 landmarks de la mano en tiempo real**.

Los puntos están numerados de la siguiente manera:

| Landmark | Parte de la mano |
| -------- | ---------------- |
| 0        | muñeca           |
| 1–4      | pulgar           |
| 5–8      | índice           |
| 9–12     | medio            |
| 13–16    | anular           |
| 17–20    | meñique          |

Por lo tanto, para detectar **solo el pulgar**, se utilizan los puntos:

```
1
2
3
4
```

---

# 3. Instalación de dependencias

Primero se deben instalar las librerías necesarias en Python.

```bash
pip install mediapipe opencv-python
```

Estas librerías permiten:

* **OpenCV** → manejo de la cámara
* **MediaPipe** → detección de la mano y landmarks

---

# 4. Desarrollo del algoritmo

A continuación se presenta el código base que permite detectar únicamente los puntos del pulgar.

```python
import cv2
import mediapipe as mp

# Inicializar MediaPipe Hands
mp_hands = mp.solutions.hands
hands = mp_hands.Hands(
    static_image_mode=False,
    max_num_hands=2,
    min_detection_confidence=0.5
)

mp_draw = mp.solutions.drawing_utils

# Abrir cámara
cap = cv2.VideoCapture(0)

while True:

    ret, frame = cap.read()

    if not ret:
        break

    frame = cv2.flip(frame, 1)

    rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

    results = hands.process(rgb)

    if results.multi_hand_landmarks:

        for hand_landmarks in results.multi_hand_landmarks:

            thumb_points = [1,2,3,4]

            for point in thumb_points:

                landmark = hand_landmarks.landmark[point]

                h, w, c = frame.shape

                cx = int(landmark.x * w)
                cy = int(landmark.y * h)

                cv2.circle(frame,(cx,cy),8,(0,255,0),-1)

    cv2.imshow("Deteccion Pulgar",frame)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

---

# 5. Explicación del código línea por línea

### Importación de librerías

```python
import cv2
import mediapipe as mp
```

Se importan las librerías necesarias:

* **cv2 (OpenCV)** para manejo de la cámara y visualización de imágenes.
* **mediapipe** para la detección de las manos.

---

### Inicialización del modelo de manos

```python
mp_hands = mp.solutions.hands
```

Se carga el módulo de detección de manos de MediaPipe.

---

```python
hands = mp_hands.Hands(
    static_image_mode=False,
    max_num_hands=2,
    min_detection_confidence=0.5
)
```

Aquí se configura el detector:

* `static_image_mode=False`
  Indica que se procesará video en tiempo real.

* `max_num_hands=2`
  Permite detectar máximo dos manos.

* `min_detection_confidence=0.5`
  Nivel mínimo de confianza para aceptar la detección.

---

### Utilidades de dibujo

```python
mp_draw = mp.solutions.drawing_utils
```

Permite dibujar los puntos detectados en la imagen.

---

### Inicialización de la cámara

```python
cap = cv2.VideoCapture(0)
```

Se inicia la cámara web del computador.

---

### Lectura de frames

```python
ret, frame = cap.read()
```

Se captura cada frame del video.

---

### Efecto espejo

```python
frame = cv2.flip(frame,1)
```

Se voltea la imagen horizontalmente para que el usuario se vea como en un espejo.

---

### Conversión de color

```python
rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
```

MediaPipe trabaja con imágenes en formato **RGB**, mientras que OpenCV usa **BGR**, por lo que se debe convertir.

---

### Procesamiento de la imagen

```python
results = hands.process(rgb)
```

Aquí se ejecuta el modelo de detección de manos.

---

### Verificación de manos detectadas

```python
if results.multi_hand_landmarks:
```

Se verifica si el modelo detectó alguna mano.

---

### Selección de puntos del pulgar

```python
thumb_points = [1,2,3,4]
```

Estos son los índices de los landmarks que pertenecen al pulgar.

---

### Obtención de coordenadas

```python
landmark = hand_landmarks.landmark[point]
```

Obtiene el landmark específico.

---

### Conversión a coordenadas de la imagen

```python
cx = int(landmark.x * w)
cy = int(landmark.y * h)
```

Convierte coordenadas normalizadas a coordenadas reales de la imagen.

---

### Dibujar punto

```python
cv2.circle(frame,(cx,cy),8,(0,255,0),-1)
```

Dibuja un círculo verde en el punto detectado.

---

### Mostrar imagen

```python
cv2.imshow("Deteccion Pulgar",frame)
```

Muestra la ventana con la detección.

---

### Salir del programa

```python
if cv2.waitKey(1) & 0xFF == ord('q'):
```

Permite salir presionando la tecla **q**.

---

# 6. Mejoramiento del algoritmo

El código puede mejorarse agregando:

### 1️⃣ Conexión entre puntos del pulgar

```python
thumb_connections = [(1,2),(2,3),(3,4)]

for connection in thumb_connections:

    start = hand_landmarks.landmark[connection[0]]
    end = hand_landmarks.landmark[connection[1]]

    x1,y1 = int(start.x*w), int(start.y*h)
    x2,y2 = int(end.x*w), int(end.y*h)

    cv2.line(frame,(x1,y1),(x2,y2),(255,0,0),2)
```

Esto dibuja la estructura del pulgar.

---

### 2️⃣ Mostrar etiqueta en pantalla

```python
cv2.putText(frame,"Pulgar Detectado",(20,50),
cv2.FONT_HERSHEY_SIMPLEX,1,(0,255,0),2)
```

Esto indica visualmente que el pulgar fue detectado.

---

### 3️⃣ Integración con Arduino

Se puede enviar un comando serial cuando el pulgar esté levantado:

```python
arduino.write(b'T')
```

Esto permitiría controlar dispositivos físicos con gestos.

---

# 7. Resultado esperado

El sistema mostrará en tiempo real:

* La imagen de la cámara
* Cuatro puntos verdes sobre el pulgar
* Conexión entre los puntos del pulgar
* Identificación del pulgar detectado

---

# 8. Conclusión

El desarrollo de este algoritmo demuestra cómo utilizar **MediaPipe y OpenCV para detectar partes específicas del cuerpo humano en tiempo real**. La detección del pulgar es un primer paso para implementar sistemas más avanzados de interacción gestual, los cuales pueden integrarse con **microcontroladores como Arduino o PIC** para controlar dispositivos físicos dentro de sistemas embebidos.

---

✅ Si quieres, también puedo darte **una versión mucho más avanzada del código (tipo proyecto universitario nivel alto)** que:

* detecta si el pulgar está **arriba o abajo 👍👎**
* controla **Arduino con gestos**
* y se ve **mucho más impresionante para el profesor**.
