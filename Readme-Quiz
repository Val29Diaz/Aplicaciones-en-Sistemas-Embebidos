Desarrollodel quiz numero 1 de aplicaciones en sustemas embebidos 

Donde se nos pide realizar lo siguiente: 

Utilizar el código de ejemplo que expone mediaPipe denominado "Pose Landmark Detection" y desarrollar lo siguiente:
Analizar y explicar el funcionamiento del código y las funciones más relevantes.
Desarrollar una etiqueta sencilla dentro de la imagen que indique si la persona está parada o sentada.
Conectar el código con arduino, para que la persona cuando se pare prenda un led rojo y cuando se siente prenda un led verde.
Desarrollar la explicación en un readme en github, crear un repositorio y compartir por este medio la información.
# Pose Landmark Detection con MediaPipe y Control de LEDs vía Arduino

Este proyecto utiliza MediaPipe Pose para detectar la postura de una persona (de pie/sentada) y controla LEDs a través de Arduino basado en la detección.

## 📋 Tabla de Contenidos
- [Descripción](#descripción)
- [Requisitos](#requisitos)
- [Instalación](#instalación)
- [Funcionamiento del Código](#funcionamiento-del-código)
- [Conexión con Arduino](#conexión-con-arduino)
- [Uso](#uso)
- [Imagenes Demostrativas](#imagenes-demostrativas)

## 📝 Descripción

El sistema utiliza la cámara web para detectar landmarks corporales mediante MediaPipe Pose, analiza la posición de las caderas y rodillas para determinar si la persona está de pie o sentada, y envía señales a Arduino para controlar LEDs:

- **LED Rojo**: Persona de pie
- **LED Verde**: Persona sentada

## 🔧 Requisitos

### Software
- Python 3.7+
- OpenCV
- MediaPipe
- PySerial
- NumPy
- Arduino IDE

### Hardware
- Arduino (Uno, Nano, Mega, etc.)
- 2 LEDs (rojo y verde)
- 2 Resistencias de 220Ω
- Protoboard y cables jumper
- Cámara web

## 📦 Instalación

1. Clonar el repositorio:
```bash
git clone https://github.com/tu-usuario/pose-detection-arduino.git
cd pose-detection-arduino
## Pose Landmark Detection: Control de Postura con MediaPipe y Arduino

Este proyecto utiliza Visión Artificial para detectar en tiempo real si una persona está sentada o de pie, enviando señales de control a un Arduino para activar indicadores visuales (LEDs).

1. Análisis del Funcionamiento
El sistema se basa en la librería MediaPipe Pose, que utiliza una arquitectura de aprendizaje profundo para localizar 33 puntos clave (landmarks) del cuerpo humano en 3D.

2. Requisitos e Instalación
Para ejecutar el código de Python, instala las dependencias mediante PyPI:
bash
pip install mediapipe opencv-python pyserial
Usa el código con precaución.

3. Conexión con Arduino
El script de Python se comunica vía Protocolo Serial.
LED Rojo: Conectado al Pin 8 (Indica estado "Parado").
LED Verde: Conectado al Pin 9 (Indica estado "Sentado").

Código del Arduino (.py)
El código espera recibir el caracter 'R' para encender el rojo o 'G' para el verde a través del puerto Serial.read().

Codigo base:


## 2. pose_detection.py (Punto 1, 2 y 3)

```python
"""
Pose Landmark Detection con MediaPipe y Control de LEDs vía Arduino
Este script detecta si una persona está de pie o sentada y envía comandos a Arduino
"""

import cv2
import mediapipe as mp
import numpy as np
import serial
import time
import sys

# Configuración del puerto serie para Arduino
# Ajustar según el puerto de su sistema
# Windows: 'COM3', 'COM4', etc.
# Linux/Mac: '/dev/ttyUSB0', '/dev/ttyACM0', etc.
ARDUINO_PORT = 'COM3'  # Cambiar según corresponda
BAUD_RATE = 9600

class PoseDetector:
    """
    Clase principal para la detección de poses usando MediaPipe
    """
    
    def __init__(self):
        """Inicializa el detector de poses y la conexión con Arduino"""
        
        # Inicializar MediaPipe Pose
        self.mp_pose = mp.solutions.pose
        self.pose = self.mp_pose.Pose(
            static_image_mode=False,      # Modo video para mejor seguimiento
            model_complexity=1,            # Balance entre precisión y velocidad
            smooth_landmarks=True,         # Suavizar landmarks entre frames
            min_detection_confidence=0.5,  # Confianza mínima para detección inicial
            min_tracking_confidence=0.5    # Confianza mínima para seguimiento
        )
        
        # Para dibujar los landmarks
        self.mp_draw = mp.solutions.drawing_utils
        self.drawing_spec = self.mp_draw.DrawingSpec(
            thickness=2, 
            circle_radius=2, 
            color=(0, 255, 0)
        )
        
        # Inicializar conexión con Arduino
        self.arduino = None
        self.setup_arduino()
        
        # Variables para el estado de la postura
        self.current_posture = "DESCONOCIDO"
        self.posture_history = []
        self.history_length = 5  # Para suavizar las detecciones
        
    def setup_arduino(self):
        """Configura la conexión serie con Arduino"""
        try:
            self.arduino = serial.Serial(
                port=ARDUINO_PORT,
                baudrate=BAUD_RATE,
                timeout=1
            )
            time.sleep(2)  # Esperar a que se establezca la conexión
            print(f"✅ Conectado a Arduino en {ARDUINO_PORT}")
        except Exception as e:
            print(f"⚠️  No se pudo conectar a Arduino: {e}")
            print("Continuando sin control de LEDs...")
            self.arduino = None
    
    def determine_posture(self, landmarks, image_height, image_width):
        """
        Determina si la persona está de pie o sentada basado en los landmarks
        
        Args:
            landmarks: Landmarks detectados por MediaPipe
            image_height: Alto de la imagen
            image_width: Ancho de la imagen
            
        Returns:
            str: "DE PIE", "SENTADO" o "DESCONOCIDO"
        """
        
        # Obtener landmarks específicos
        # Índices según MediaPipe Pose
        left_hip = landmarks[self.mp_pose.PoseLandmark.LEFT_HIP.value]
        right_hip = landmarks[self.mp_pose.PoseLandmark.RIGHT_HIP.value]
        left_knee = landmarks[self.mp_pose.PoseLandmark.LEFT_KNEE.value]
        right_knee = landmarks[self.mp_pose.PoseLandmark.RIGHT_KNEE.value]
        left_shoulder = landmarks[self.mp_pose.PoseLandmark.LEFT_SHOULDER.value]
        right_shoulder = landmarks[self.mp_pose.PoseLandmark.RIGHT_SHOULDER.value]
        left_ankle = landmarks[self.mp_pose.PoseLandmark.LEFT_ANKLE.value]
        right_ankle = landmarks[self.mp_pose.PoseLandmark.RIGHT_ANKLE.value]
        
        # Verificar que los landmarks tengan suficiente confianza
        min_confidence = 0.5
        if (left_hip.visibility < min_confidence or 
            right_hip.visibility < min_confidence or
            left_knee.visibility < min_confidence or 
            right_knee.visibility < min_confidence):
            return "DESCONOCIDO"
        
        # Calcular posiciones promedio
        avg_hip_y = (left_hip.y + right_hip.y) / 2
        avg_knee_y = (left_knee.y + right_knee.y) / 2
        avg_shoulder_y = (left_shoulder.y + right_shoulder.y) / 2
        avg_ankle_y = (left_ankle.y + right_ankle.y) / 2
        
        # MÉTODO 1: Basado en proporciones corporales
        # Calcular alturas relativas
        torso_height = abs(avg_shoulder_y - avg_hip_y)
        leg_height = abs(avg_hip_y - avg_ankle_y)
        
        # MÉTODO 2: Basado en posición de caderas respecto a rodillas
        hip_knee_ratio = avg_hip_y / avg_knee_y if avg_knee_y != 0 else 1
        
        # Lógica de decisión mejorada
        if leg_height > torso_height * 1.2:  # Piernas más largas que torso (de pie)
            if avg_hip_y < avg_knee_y - 0.1:  # Caderas arriba de rodillas
                posture = "DE PIE"
            else:
                posture = "SENTADO"
        elif torso_height > leg_height * 0.8:  # Torso relativamente largo (sentado)
            if avg_hip_y > avg_knee_y + 0.1:  # Caderas abajo o al nivel de rodillas
                posture = "SENTADO"
            else:
                posture = "DE PIE"
        else:
            # Decisión basada en la relación cadera-rodilla
            posture = "DE PIE" if avg_hip_y < avg_knee_y - 0.15 else "SENTADO"
        
        return posture
    
    def smooth_posture(self, new_posture):
        """Suaviza las detecciones usando un historial"""
        if new_posture != "DESCONOCIDO":
            self.posture_history.append(new_posture)
            if len(self.posture_history) > self.history_length:
                self.posture_history.pop(0)
        
        if len(self.posture_history) > 0:
            # Devolver la postura más frecuente en el historial
            from collections import Counter
            posture_counts = Counter(self.posture_history)
            most_common = posture_counts.most_common(1)[0][0]
            return most_common
        else:
            return "DESCONOCIDO"
    
    def send_to_arduino(self, posture):
        """Envía comando a Arduino según la postura detectada"""
        if self.arduino and self.arduino.is_open:
            try:
                if posture == "DE PIE":
                    self.arduino.write(b'R')  # Rojo
                    print("🔴 Enviando comando ROJO a Arduino")
                elif posture == "SENTADO":
                    self.arduino.write(b'G')  # Verde
                    print("🟢 Enviando comando VERDE a Arduino")
            except Exception as e:
                print(f"Error enviando a Arduino: {e}")
    
    def draw_posture_label(self, image, posture):
        """
        Dibuja una etiqueta en la imagen indicando la postura detectada
        
        Args:
            image: Frame de video
            posture: Postura detectada
        """
        height, width, _ = image.shape
        
        # Configurar colores según postura
        if posture == "DE PIE":
            bg_color = (0, 0, 255)  # Rojo en BGR
            text_color = (255, 255, 255)
        elif posture == "SENTADO":
            bg_color = (0, 255, 0)  # Verde en BGR
            text_color = (255, 255, 255)
        else:
            bg_color = (128, 128, 128)  # Gris
            text_color = (255, 255, 255)
        
        # Texto a mostrar
        text = f"Postura: {posture}"
        
        # Calcular dimensiones del texto
        font = cv2.FONT_HERSHEY_SIMPLEX
        font_scale = 1
        thickness = 2
        (text_width, text_height), baseline = cv2.getTextSize(
            text, font, font_scale, thickness
        )
        
        # Posición de la etiqueta (esquina superior izquierda)
        margin = 20
        x, y = margin, margin + text_height + 10
        
        # Dibujar rectángulo de fondo
        cv2.rectangle(
            image,
            (x - 10, y - text_height - 10),
            (x + text_width + 10, y + 10),
            bg_color,
            -1  # Relleno
        )
        
        # Dibujar texto
        cv2.putText(
            image,
            text,
            (x, y - 5),
            font,
            font_scale,
            text_color,
            thickness
        )
        
        # Añadir indicador LED
        led_text = "LED: " + ("🔴" if posture == "DE PIE" else "🟢" if posture == "SENTADO" else "⚪")
        cv2.putText(
            image,
            led_text,
            (x, y + 40),
            font,
            0.8,
            text_color,
            2
        )
    
    def run(self):
        """Ejecuta el detector en tiempo real"""
        
        # Inicializar cámara
        cap = cv2.VideoCapture(0)
        cap.set(cv2.CAP_PROP_FRAME_WIDTH, 640)
        cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)
        
        if not cap.isOpened():
            print("❌ Error: No se pudo abrir la cámara")
            return
        
        print("✅ Cámara inicializada correctamente")
        print("📹 Presiona 'q' para salir")
        print("-" * 50)
        
        # Variables para FPS
        prev_time = 0
        curr_time = 0
        
        try:
            while True:
                # Leer frame
                ret, frame = cap.read()
                if not ret:
                    print("❌ Error: No se pudo leer el frame")
                    break
                
                # Voltear horizontalmente para efecto espejo
                frame = cv2.flip(frame, 1)
                
                # Convertir BGR a RGB
                image_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
                
                # Procesar con MediaPipe
                results = self.pose.process(image_rgb)
                
                # Calcular FPS
                curr_time = time.time()
                fps = 1 / (curr_time - prev_time) if prev_time > 0 else 0
                prev_time = curr_time
                
                if results.pose_landmarks:
                    # Dibujar landmarks en el frame
                    self.mp_draw.draw_landmarks(
                        frame,
                        results.pose_landmarks,
                        self.mp_pose.POSE_CONNECTIONS,
                        self.drawing_spec,
                        self.drawing_spec
                    )
                    
                    # Determinar postura
                    posture = self.determine_posture(
                        results.pose_landmarks.landmark,
                        frame.shape[0],
                        frame.shape[1]
                    )
                    
                    # Suavizar detección
                    smoothed_posture = self.smooth_posture(posture)
                    
                    # Enviar comando a Arduino si cambió la postura
                    if smoothed_posture != self.current_posture:
                        self.current_posture = smoothed_posture
                        self.send_to_arduino(smoothed_posture)
                    
                    # Dibujar etiqueta de postura
                    self.draw_posture_label(frame, smoothed_posture)
                    
                else:
                    # No se detectó persona
                    cv2.putText(
                        frame,
                        "No se detecta persona",
                        (20, 50),
                        cv2.FONT_HERSHEY_SIMPLEX,
                        1,
                        (0, 0, 255),
                        2
                    )
                
                # Mostrar FPS
                cv2.putText(
                    frame,
                    f"FPS: {int(fps)}",
                    (frame.shape[1] - 120, 30),
                    cv2.FONT_HERSHEY_SIMPLEX,
                    0.7,
                    (255, 255, 255),
                    2
                )
                
                # Mostrar frame
                cv2.imshow('Pose Detection + Arduino LED Control', frame)
                
                # Salir con 'q'
                if cv2.waitKey(1) & 0xFF == ord('q'):
                    break
                    
        except KeyboardInterrupt:
            print("\n👋 Deteniendo por interrupción del usuario")
            
        finally:
            # Limpiar recursos
            cap.release()
            cv2.destroyAllWindows()
            if self.arduino:
                self.arduino.close()
                print("🔌 Conexión con Arduino cerrada")
            print("✅ Programa finalizado")


def main():
    """Función principal"""
    print("=" * 50)
    print("🔍 POSE DETECTION WITH ARDUINO LED CONTROL")
    print("=" * 50)
    print("\n📋 Instrucciones:")
    print("   - Párese frente a la cámara para LED ROJO")
    print("   - Siéntese frente a la cámara para LED VERDE")
    print("   - Presione 'q' para salir\n")
    
    # Verificar puerto Arduino
    if len(sys.argv) > 1:
        global ARDUINO_PORT
        ARDUINO_PORT = sys.argv[1]
        print(f"📡 Usando puerto Arduino: {ARDUINO_PORT}")
    
    # Crear y ejecutar detector
    detector = PoseDetector()
    detector.run()


if __name__ == "__main__":
    main()
