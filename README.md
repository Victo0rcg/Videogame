# 🎮 Piedra, Papel o Tijeras con Visión Artificial

Un juego interactivo de Piedra, Papel o Tijeras que utiliza visión artificial para detectar los gestos de la mano del jugador en tiempo real mediante la cámara web. Implementado con una arquitectura orientada a objetos modular y simplificada.

> **Nota**: Este proyecto utiliza una arquitectura orientada a objetos completa con patrón State Pattern para gestión de pantallas, Singleton para recursos compartidos, y SQLite para persistencia de datos.

## 📋 Tabla de Contenidos

- [Descripción](#descripción)
- [Características](#características)
- [Arquitectura del Proyecto](#arquitectura-del-proyecto)
- [Estructura de Directorios](#estructura-de-directorios)
- [Patrones de Diseño](#patrones-de-diseño)
- [Tecnologías Utilizadas](#tecnologías-utilizadas)
- [Dependencias](#dependencias)
- [Instalación](#instalación)
- [Uso](#uso)
- [Funcionamiento Técnico](#funcionamiento-técnico)
- [Componentes del Sistema](#componentes-del-sistema)
- [Sistema de Base de Datos](#sistema-de-base-de-datos)
- [Sistema de Autenticación](#sistema-de-autenticación)
- [Configuración](#configuración)
- [Requisitos del Sistema](#requisitos-del-sistema)

## 📝 Descripción

Este proyecto es un juego de Piedra, Papel o Tijeras desarrollado en Python que combina:
- **Visión Artificial**: Detección de gestos de mano en tiempo real usando MediaPipe
- **Interfaz Gráfica**: Interfaz moderna desarrollada con Pygame
- **Animaciones**: Efectos visuales dinámicos durante las rondas
- **Sistema de Sonido**: Efectos de sonido para mejorar la experiencia de juego
- **Persistencia de Datos**: Base de datos SQLite para usuarios y estadísticas
- **Autenticación**: Sistema de registro e inicio de sesión con hash de contraseñas

El juego permite competir contra una IA que realiza jugadas aleatorias, mientras el jugador utiliza gestos de mano detectados por la cámara web. Las estadísticas de partidas se guardan automáticamente para usuarios autenticados.

## ✨ Características

- 🎯 **Detección de gestos en tiempo real** mediante visión artificial (MediaPipe)
- 🎨 **Interfaz gráfica moderna** con múltiples pantallas (inicio, menú, instrucciones, juego, login, registro, historial)
- 🎬 **Animaciones fluidas** de colisión entre jugadas
- 🔊 **Sistema de sonido** con efectos para diferentes eventos
- 📊 **HUD informativo** con puntuaciones y estado de ronda
- ⏱️ **Cuenta regresiva** antes de cada ronda
- 🏆 **Sistema de puntuación** con victoria al alcanzar 3 puntos
- 🔄 **Retorno automático al menú** al finalizar una partida
- 👤 **Sistema de usuarios** con registro e inicio de sesión
- 💾 **Persistencia de datos** con SQLite
- 📈 **Historial de partidas** con estadísticas por usuario
- 🔐 **Autenticación segura** con hash de contraseñas (bcrypt)

## 🏗️ Arquitectura del Proyecto

El proyecto sigue una arquitectura modular simplificada basada en capas, con separación clara de responsabilidades:

```
┌─────────────────────────────────────────┐
│           main.py (Punto de entrada)    │
│  - Inicialización mínima                │
│  - Delegación a GameApp                 │
└─────────────────────────────────────────┘
                    │
        ┌───────────┴───────────┐
        │                       │
┌───────▼────────┐    ┌─────────▼─────────┐
│   screens/     │    │      app/         │
│  (Pantallas)   │    │  (Lógica/App)     │
│                │    │                   │
│ - screen_base  │    │ - game_app        │
│ - start        │    │ - screen_manager │
│ - menu         │    │ - game_logic     │
│ - game         │    │ - auth_manager   │
│ - login        │    │ - hand_detector   │
│ - register     │    │ - resource_mgr    │
│ - profile      │    │ - sound_manager   │
└────────────────┘    └─────────┬─────────┘
                                │
                    ┌───────────▼───────────┐
                    │    database/          │
                    │  (Persistencia)       │
                    │                       │
                    │ - database.py         │
                    │ - UsuarioRepository   │
                    └───────────────────────┘
```

### Flujo de Estados (State Pattern)

El juego utiliza el patrón State Pattern para gestionar las diferentes pantallas:

```
[INICIO] → [MENÚ] → [JUEGO] → [MENÚ]
    │         │         │
    │         ├─────────┘
    │         ├─[INSTRUCCIONES]
    │         ├─[LOGIN] ──→ [MENÚ] (con sesión activa)
    │         ├─[REGISTER] ─→ [MENÚ] (después de registro)
    │         └─[HISTORIAL] ──→ [MENÚ] (visualizador público)
    │
    └─────────[SALIR]
```

## 📁 Estructura de Directorios

```
Piedra, Papel o Tijeras/
│
├── main.py                 # Punto de entrada principal
├── config.py               # Configuración global del juego
├── requirements.txt         # Dependencias del proyecto
├── README.md               # Documentación del proyecto
│
├── app/                    # Módulo principal de la aplicación
│   ├── __init__.py         # Exportaciones del módulo
│   ├── game_app.py         # Aplicación principal (inicialización Pygame)
│   ├── screen_manager.py   # Gestor de pantallas (State Pattern)
│   ├── game_logic.py       # Lógica del juego, modelos y estrategias IA
│   │                        # - Score, RoundResult (dataclasses)
│   │                        # - AIStrategy, RandomStrategy
│   │                        # - GameLogic
│   ├── auth_manager.py     # Gestor de autenticación (Singleton)
│   ├── hand_detector.py    # Detección de gestos (MediaPipe)
│   ├── resource_manager.py # Gestor de recursos (Singleton)
│   │                        # - ResourceManager
│   │                        # - cargar_imagen() (utilidad)
│   │                        # - dibujar_texto() (utilidad)
│   └── sound_manager.py    # Gestor de efectos de sonido
│
├── screens/                # Pantallas del juego
│   ├── __init__.py         # Exportaciones del módulo
│   ├── screen_base.py      # Clase base abstracta + Boton
│   │                        # - Screen (ABC)
│   │                        # - Boton (componente UI)
│   ├── start_screen.py     # Pantalla de inicio
│   ├── menu_screen.py      # Menú principal
│   ├── instructions_screen.py # Pantalla de instrucciones
│   ├── game_screen.py      # Pantalla principal del juego
│   ├── login_screen.py     # Pantalla de inicio de sesión
│   ├── register_screen.py  # Pantalla de registro
│   └── profile_screen.py   # Pantalla de historial/estadísticas
│
├── database/               # Persistencia de datos
│   ├── __init__.py         # Exportaciones del módulo
│   └── database.py         # Conexión SQLite y repositorio
│                            # - Database (gestión de conexión)
│                            # - UsuarioRepository (CRUD usuarios)
│
└── Resources/              # Recursos del juego
    ├── UI/                  # Fondos de interfaz
    │   ├── fondo_inicio.png
    │   ├── fondo_menu_nuevo.png
    │   ├── fondo_instrucciones.png
    │   ├── fondo_juego.png
    │   ├── fondo_historial.png
    │   └── fondo_sesion.png
    ├── piedra.png          # Imágenes de jugadas
    ├── papel.png
    ├── tijera.png
    ├── click.wav           # Efectos de sonido
    ├── collision.wav
    ├── defeat.wav
    ├── round_start.wav
    └── victory.wav
```

## 🎯 Patrones de Diseño

El proyecto implementa varios patrones de diseño para mantener una arquitectura limpia y mantenible:

### 1. **State Pattern** (Patrón Estado)
- **Implementación**: `ScreenManager` y clases `Screen`
- **Propósito**: Gestionar transiciones entre pantallas del juego
- **Beneficios**: Facilita agregar nuevas pantallas, separa lógica de cada estado

### 2. **Singleton Pattern** (Patrón Singleton)
- **Implementaciones**: 
  - `ResourceManager`: Garantiza una única instancia para recursos compartidos
  - `AuthManager`: Mantiene una única sesión de usuario activa
- **Propósito**: Controlar acceso a recursos compartidos y estado global
- **Beneficios**: Evita duplicación de recursos, mantiene consistencia

### 3. **Template Method Pattern** (Patrón Método Plantilla)
- **Implementación**: Clase base `Screen` con método `run()`
- **Propósito**: Define el esqueleto del algoritmo de ejecución de pantallas
- **Beneficios**: Reutilización de código común, flexibilidad en implementaciones

### 4. **Strategy Pattern** (Patrón Estrategia)
- **Implementación**: `AIStrategy` y `RandomStrategy` en `game_logic.py`
- **Propósito**: Permite cambiar algoritmos de decisión de la IA
- **Beneficios**: Extensibilidad para nuevas estrategias de IA

### 5. **Repository Pattern** (Patrón Repositorio)
- **Implementación**: `UsuarioRepository` en `database/database.py`
- **Propósito**: Abstraer acceso a datos y operaciones de base de datos
- **Beneficios**: Separación de lógica de negocio y persistencia, facilita testing

## 🛠️ Tecnologías Utilizadas

- **Python 3.7+**: Lenguaje de programación principal
- **Pygame 2.6+**: Framework para desarrollo de videojuegos e interfaz gráfica
- **OpenCV (cv2) 4.5+**: Procesamiento de imágenes y captura de video
- **MediaPipe 0.8+**: Framework de Google para detección de gestos y poses
- **SQLite3**: Base de datos embebida (incluida en Python)
- **bcrypt**: Hash seguro de contraseñas

## 📦 Dependencias

El proyecto requiere las siguientes dependencias Python:

```python
pygame>=2.0.0          # Interfaz gráfica y gestión de eventos
opencv-python>=4.5.0  # Procesamiento de video y captura de cámara
mediapipe>=0.8.0       # Detección de gestos de mano
bcrypt>=4.0.0          # Hash seguro de contraseñas
pytest>=7.0.0          # Framework de testing (desarrollo)
pytest-cov>=3.0.0      # Cobertura de código (desarrollo)
```

**Nota**: `sqlite3` es parte de la biblioteca estándar de Python y no requiere instalación.

### Instalación de Dependencias

```bash
pip install -r requirements.txt
```

O usando un entorno virtual (recomendado):

```bash
python -m venv .venv
.venv\Scripts\activate  # Windows
# o
source .venv/bin/activate  # Linux/Mac

pip install -r requirements.txt
```

## 🚀 Instalación

1. **Clonar o descargar el proyecto**
   ```bash
   cd "Piedra, Papel o Tijeras"
   ```

2. **Crear y activar entorno virtual** (recomendado)
   ```bash
   python -m venv .venv
   .venv\Scripts\activate  # Windows
   # o
   source .venv/bin/activate  # Linux/Mac
   ```

3. **Instalar dependencias**
   ```bash
   pip install -r requirements.txt
   ```

4. **Verificar que la cámara web esté disponible**

5. **Ejecutar el juego**
   ```bash
   python main.py
   ```

## 🎮 Uso

### Controles del Juego

- **Mouse**: Navegación por menús y botones
- **ESPACIO**: Iniciar una nueva ronda (en pantalla de juego)
- **ESC**: Volver al menú principal (desde pantalla de juego o instrucciones)
- **TAB**: Cambiar entre campos de texto (en pantallas de login/registro)
- **ENTER**: Confirmar acción (en pantallas de login/registro)

### Gestos de Mano

El juego detecta tres gestos mediante análisis de landmarks de MediaPipe (usando solo los 4 dedos principales, excluyendo el pulgar):

- **👊 Piedra**: Puño cerrado (todos los 4 dedos principales bajados)
- **✋ Papel**: Mano abierta (todos los 4 dedos principales levantados)
- **✌️ Tijera**: Índice y medio levantados, anular y meñique bajados

### Flujo de Juego

1. **Pantalla de Inicio**: Click en "INICIAR" para comenzar
2. **Menú Principal**: 
   - Seleccionar "JUGAR" para comenzar una partida
   - Seleccionar "INSTRUCCIONES" para ver las reglas
   - Seleccionar "INICIAR SESIÓN" para acceder a la pantalla de login
   - Seleccionar "HISTORIAL" para ver estadísticas de usuarios
   - Seleccionar "SALIR" para cerrar el juego
3. **Pantalla de Juego**:
   - Presionar ESPACIO para iniciar una ronda
   - Realizar el gesto deseado frente a la cámara
   - Esperar la cuenta regresiva (3, 2, 1, ¡YA!)
   - Observar la animación de colisión
   - Ver los resultados y puntuación
   - Repetir hasta que alguien alcance 3 puntos
   - Al finalizar, el juego retorna automáticamente al menú principal
   - Si hay usuario autenticado, se guardan las estadísticas automáticamente

## ⚙️ Funcionamiento Técnico

### Ciclo de Vida de la Aplicación

1. **Inicialización** (`main.py` → `GameApp.__init__()`):
   - Inicializa Pygame
   - Crea ventana principal (1280x720)
   - Inicializa `ResourceManager` (Singleton) y carga recursos
   - Inicializa `SoundManager`
   - Crea `ScreenManager` con todas las pantallas pre-instanciadas

2. **Bucle Principal** (`GameApp.run()` → `ScreenManager.run()`):
   - `ScreenManager` gestiona el estado actual
   - Cada pantalla ejecuta su propio bucle (`Screen.run()`)
   - Las pantallas retornan acciones que `ScreenManager` procesa
   - Transiciones entre pantallas mediante `change_state()`

3. **Ciclo de una Pantalla** (`Screen.run()`):
   - `enter()`: Inicialización al entrar
   - Bucle principal:
     - Procesa eventos (`handle_event()`)
     - Actualiza estado (`update(dt)`)
     - Renderiza (`render()`)
   - `exit()`: Limpieza al salir

### Ciclo de una Ronda de Juego

1. **Estado Inicial**: El jugador presiona ESPACIO
2. **Cuenta Regresiva**: Se muestra "3", "2", "1", "¡YA!" durante 4 segundos
3. **Detección** (en "¡YA!"):
   - Se captura un frame de la cámara
   - `HandDetector` procesa el frame con MediaPipe
   - Se analizan landmarks de la mano
   - Se determina el gesto (rock/paper/scissors)
   - `GameLogic` elige jugada aleatoria para la IA
4. **Animación**: Las imágenes se mueven hacia el centro y colisionan
5. **Resultado**: `GameLogic.determine_round_winner()` determina ganador
6. **Actualización**: Se actualiza `Score` y se verifica fin de partida
7. **Efecto Visual**: Círculo de colisión con fade-out
8. **Estado Final**: Se muestran los resultados y se espera la siguiente ronda

### Lógica de Ganador

Las reglas del juego son implementadas en `GameLogic.determine_round_winner()`:

- **Piedra** gana a **Tijera**
- **Tijera** gana a **Papel**
- **Papel** gana a **Piedra**
- **Empate** si ambas jugadas son iguales

### Condición de Victoria

El juego termina cuando (`GameLogic.check_game_end()`):
- El **jugador** alcanza **3 puntos** → "¡GANASTE!"
- La **IA** alcanza **3 puntos** → "¡GANA LA IA!"

Después de 4 segundos, el juego retorna automáticamente al menú principal. Si hay usuario autenticado, se actualizan las estadísticas en la base de datos.

### Detección de Gestos

El proceso de detección utiliza MediaPipe Hands:

1. **Captura de Frame**: OpenCV captura frame BGR de la cámara
2. **Conversión**: Frame se convierte a RGB para MediaPipe
3. **Detección**: MediaPipe detecta landmarks de la mano (21 puntos)
4. **Análisis**: `HandDetector._estan_dedos_levantados()` compara coordenadas Y de:
   - Puntas de dedos principales (landmarks 8, 12, 16, 20)
   - Articulaciones medias (landmarks 6, 10, 14, 18)
   - **Nota**: El pulgar se excluye de la detección debido a su movimiento lateral
5. **Clasificación** (usando solo 4 dedos principales):
   - **Papel**: Todos los 4 dedos principales levantados (índice, medio, anular, meñique)
   - **Tijera**: Índice y medio levantados, anular y meñique bajados (2 dedos específicos)
   - **Piedra**: Todos los 4 dedos principales bajados (0 dedos)

## 🔧 Componentes del Sistema

### 1. `main.py` - Punto de Entrada

**Responsabilidades**:
- Punto de entrada mínimo y limpio
- Inicialización de `GameApp`
- Gestión del ciclo de vida del juego

**Características**:
- Código simplificado sin supresión de warnings
- Delegación completa a `GameApp` para arquitectura limpia

### 2. `config.py` - Configuración Global

**Contenido**:
- Dimensiones de pantalla (1280x720)
- FPS objetivo (60)
- Colores del juego (RGB)
- Fuentes de texto (pygame.font.Font)
- Tamaños y posiciones de elementos UI
- Configuración de animaciones
- Mapeo de landmarks de MediaPipe
- Puntos para ganar (3)

**Características**:
- Centraliza toda la configuración visual y de juego
- Facilita el ajuste de parámetros sin modificar código
- Inicializa fuentes de Pygame al importar

### 3. `app/game_app.py` - Aplicación Principal

**Clase**: `GameApp`

**Responsabilidades**:
- Inicialización de Pygame
- Carga de recursos globales mediante `ResourceManager`
- Gestión del ciclo principal del juego
- Delegación a `ScreenManager` para gestión de pantallas

**Métodos principales**:
- `__init__()`: Inicializa pygame, carga recursos, crea ScreenManager
- `run()`: Ejecuta el bucle principal del juego
- `cleanup()`: Limpia recursos al salir

**Flujo**:
```
GameApp.__init__() → ResourceManager.load_all() → ScreenManager.__init__()
GameApp.run() → ScreenManager.run() → [bucle de pantallas]
```

### 4. `app/screen_manager.py` - Gestor de Pantallas

**Clase**: `ScreenManager`

**Responsabilidades**:
- Gestión de transiciones entre pantallas usando State Pattern
- Mantenimiento del estado actual del juego
- Coordinación de eventos entre pantallas

**Pantallas gestionadas**:
- `inicio`: Pantalla de bienvenida (`StartScreen`)
- `menu`: Menú principal (`MenuScreen`)
- `instrucciones`: Instrucciones del juego (`InstructionsScreen`)
- `jugando`: Pantalla de juego (`GameScreen`)
- `login`: Pantalla de login (`LoginScreen`)
- `register`: Pantalla de registro (`RegisterScreen`)
- `profile`: Pantalla de historial/estadísticas (`ProfileScreen`)

**Métodos principales**:
- `__init__()`: Pre-instanciación de todas las pantallas
- `change_state(new_state)`: Cambia a una nueva pantalla (exit + enter)
- `run()`: Ejecuta el bucle principal del gestor

**Patrón**: State Pattern - cada pantalla es un estado del sistema

### 5. `app/game_logic.py` - Lógica del Juego

**Contenido consolidado**:
- **Modelos de datos**:
  - `Score`: Puntuación del juego (jugador, ia)
  - `RoundResult`: Resultado de una ronda (player_move, ai_move, winner)
- **Estrategias de IA**:
  - `AIStrategy`: Interfaz abstracta para estrategias
  - `RandomStrategy`: Estrategia aleatoria (implementación actual)
- **Lógica del juego**:
  - `GameLogic`: Reglas del juego y determinación de ganadores

**Clase**: `GameLogic`

**Responsabilidades**:
- Reglas del juego
- Determinación de ganadores
- Selección de jugadas de la IA
- Verificación de fin de partida

**Métodos principales**:
- `__init__(ai_strategy)`: Inicializa con estrategia de IA (por defecto RandomStrategy)
- `choose_ai_move()`: Selecciona jugada usando la estrategia configurada
- `determine_round_winner(player_move, ai_move)`: Compara jugadas y determina ganador
- `check_game_end(score)`: Verifica si el juego ha terminado (3 puntos)

**Características**:
- Lógica pura sin dependencias de UI
- Fácil de testear y mantener
- Extensible mediante Strategy Pattern para nuevas estrategias de IA

### 6. `app/auth_manager.py` - Gestor de Autenticación

**Clase**: `AuthManager` (Singleton)

**Responsabilidades**:
- Gestión de sesión de usuario
- Registro de nuevos usuarios
- Inicio de sesión
- Hash y verificación de contraseñas (bcrypt)
- Actualización de estadísticas de usuario

**Atributos**:
- `current_user`: Usuario autenticado actual (dict o None)
- `_instance`: Instancia única (Singleton)

**Métodos principales**:
- `register(username, password)`: Registra nuevo usuario con hash de contraseña
- `login(username, password)`: Autentica usuario y establece sesión
- `logout()`: Cierra sesión actual
- `update_stats(wins, losses)`: Actualiza estadísticas del usuario actual
- `hash_password(password)`: Genera hash bcrypt de contraseña
- `verify_password(password, password_hash)`: Verifica contraseña contra hash

**Características**:
- Singleton para mantener una única sesión global
- Integración con `UsuarioRepository` para persistencia
- Manejo robusto de errores

### 7. `screens/screen_base.py` - Clase Base de Pantallas

**Contenido consolidado**:
- **Clase base**: `Screen` (clase abstracta)
- **Componente UI**: `Boton` (componente reutilizable)

**Clase**: `Screen` (ABC)

**Responsabilidades**:
- Define la interfaz común para todas las pantallas
- Gestiona el ciclo de vida de las pantallas
- Proporciona métodos estándar para eventos, actualización y renderizado

**Métodos principales**:
- `__init__(screen, clock, resources)`: Inicializa pantalla base
- `enter()`: Llamado al entrar a la pantalla
- `exit()`: Llamado al salir de la pantalla
- `handle_event(event)`: Maneja eventos de pygame (abstracto)
- `update(dt)`: Actualiza el estado de la pantalla (abstracto)
- `render()`: Renderiza la pantalla (abstracto)
- `run()`: Ejecuta el bucle principal de la pantalla (Template Method)

**Clase**: `Boton`

**Características**:
- Diseño moderno con sombras y bordes redondeados
- Efecto hover (cambio de color)
- Integración con sistema de sonido
- Renderizado con pygame.draw.rect()

**Métodos**:
- `__init__(texto, posicion, fuente, ancho, alto, sound_manager)`: Inicializa botón
- `dibujar(superficie)`: Renderiza el botón con efectos visuales
- `verificar_clic(evento)`: Detecta clicks y reproduce sonido

### 8. `screens/game_screen.py` - Pantalla de Juego

**Clase**: `GameScreen`

**Responsabilidades**:
- Integración de cámara y detección de gestos
- Gestión del ciclo completo de rondas
- Animaciones de colisión
- HUD con puntuaciones
- Guardado de estadísticas al finalizar

**Características**:
- Detección de gestos en tiempo real mediante `HandDetector`
- Animaciones fluidas de colisión
- Cuenta regresiva antes de cada ronda
- Retorno automático al menú al finalizar
- Guardado automático de estadísticas si hay usuario autenticado

**Métodos principales**:
- `__init__()`: Inicializa cámara, detector, lógica del juego
- `enter()`: Reinicializa cámara al entrar
- `exit()`: Libera cámara al salir
- `_start_round()`: Inicia una nueva ronda
- `_capture_moves()`: Captura jugadas del jugador y la IA
- `_update_collision_animation()`: Actualiza animación de colisión
- `_draw_hud()`: Dibuja el HUD con puntuaciones
- `_save_game_stats()`: Guarda estadísticas en base de datos

**Flujo de una ronda**:
```
ESPACIO → _start_round() → cuenta regresiva → _capture_moves() → 
animación → resultado → actualización score → verificación fin → 
guardado stats (si autenticado) → retorno menú
```

### 9. `app/hand_detector.py` - Detector de Gestos

**Clase**: `HandDetector`

**Responsabilidades**:
- Detección de manos usando MediaPipe
- Análisis de gestos (piedra, papel, tijera)
- Procesamiento de frames de video

**Métodos principales**:
- `__init__(max_hands, detection_confidence)`: Inicializa detector de MediaPipe
- `start()`: Inicia el detector (lazy initialization)
- `procesar_frame(frame)`: Procesa frame y devuelve (gesto, frame_procesado)
- `_estan_dedos_levantados(puntos_mano, dedos_requeridos)`: Verifica si dedos están levantados (compara Y: punta < articulación)
- `_estan_dedos_bajados(puntos_mano, dedos_requeridos)`: Verifica si dedos están bajados (compara Y: punta >= articulación)
- `stop()`: Detiene y libera recursos del detector

**Lógica de Detección**:
- **Papel**: Todos los 4 dedos principales levantados (índice, medio, anular, meñique)
- **Tijera**: Índice y medio levantados, anular y meñique bajados (verificación explícita)
- **Piedra**: Todos los 4 dedos principales bajados

**Landmarks utilizados** (MediaPipe Hands - solo 4 dedos principales):
- Punto 8 (punta índice) vs 6 (articulación media índice)
- Punto 12 (punta medio) vs 10 (articulación media medio)
- Punto 16 (punta anular) vs 14 (articulación media anular)
- Punto 20 (punta meñique) vs 18 (articulación media meñique)

**Nota**: El pulgar (puntos 4 y 3) se excluye de la detección porque su movimiento es principalmente lateral, no vertical, lo que causa falsos positivos en la clasificación de gestos.

### 10. `app/resource_manager.py` - Gestor de Recursos

**Contenido consolidado**:
- **Funciones utilitarias**:
  - `cargar_imagen()`: Carga imágenes con manejo de errores
  - `dibujar_texto()`: Renderiza texto en pantalla
- **Clase**: `ResourceManager` (Singleton)

**Clase**: `ResourceManager`

**Responsabilidades**:
- Carga centralizada de recursos (imágenes)
- Gestión de recursos compartidos entre pantallas
- Manejo de errores si faltan recursos

**Métodos principales**:
- `__init__()`: Inicializa diccionario de imágenes
- `load_all()`: Carga todas las imágenes del juego
- `get_image(key)`: Obtiene imagen por clave
- `get_ai_images()`: Retorna diccionario de imágenes de IA
- `get_player_images()`: Retorna diccionario de imágenes de jugador
- `get_background(screen_name)`: Obtiene fondo con fallback
- `to_dict()`: Convierte recursos a diccionario para compatibilidad

**Características**:
- Singleton para evitar carga múltiple de recursos
- Manejo robusto de errores (superficies transparentes si falta imagen)
- Soporte para subcarpetas de recursos

### 11. `app/sound_manager.py` - Gestor de Sonido

**Clase**: `SoundManager`

**Responsabilidades**:
- Carga de archivos de sonido
- Reproducción de efectos de sonido
- Manejo de errores si faltan archivos

**Sonidos gestionados**:
- `clic_menu`: Click en botones
- `inicio_ronda`: Inicio de nueva ronda
- `fin_partida_ganar`: Victoria del jugador
- `fin_partida_perder`: Derrota del jugador
- `colision`: Colisión de jugadas

**Métodos principales**:
- `__init__()`: Inicializa mixer y carga sonidos
- `cargar_sonidos()`: Carga todos los archivos de sonido
- `reproducir(nombre_sonido, volumen)`: Reproduce un sonido específico

**Características**:
- Manejo silencioso de errores (el juego continúa sin sonidos si faltan archivos)
- Volumen configurable por sonido

### 12. `database/database.py` - Persistencia de Datos

**Clases**:
- `Database`: Gestión de conexión SQLite
- `UsuarioRepository`: Operaciones CRUD de usuarios

**Clase**: `Database`

**Responsabilidades**:
- Gestión de conexión a base de datos SQLite
- Creación de esquema de base de datos
- Cierre de conexión

**Métodos principales**:
- `__init__(db_path)`: Inicializa conexión a base de datos
- `crear_tablas()`: Crea esquema de tablas si no existen
- `cerrar()`: Cierra conexión a base de datos

**Clase**: `UsuarioRepository`

**Responsabilidades**:
- Operaciones CRUD de usuarios
- Gestión de estadísticas (wins, losses)
- Búsqueda de usuarios

**Métodos principales**:
- `crear_usuario(username, password_hash)`: Crea nuevo usuario
- `obtener_por_username(username)`: Obtiene usuario por nombre
- `autenticar(username, password_hash)`: Verifica credenciales
- `sumar_ganada(user_id)`: Incrementa victorias
- `sumar_perdida(user_id)`: Incrementa derrotas
- `obtener_estadisticas(username)`: Obtiene estadísticas de usuario

**Esquema de Base de Datos**:

```sql
CREATE TABLE usuarios (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT UNIQUE NOT NULL,
    password_hash TEXT NOT NULL,
    wins INTEGER DEFAULT 0,
    losses INTEGER DEFAULT 0,
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)
```

## 💾 Sistema de Base de Datos

### Arquitectura

El sistema utiliza SQLite como base de datos embebida, lo que significa:
- No requiere servidor de base de datos separado
- Archivo de base de datos local (`gestor_juego.db`)
- Transacciones ACID
- Fácil portabilidad

### Esquema

**Tabla `usuarios`**:
- `id`: INTEGER PRIMARY KEY AUTOINCREMENT
- `username`: TEXT UNIQUE NOT NULL
- `password_hash`: TEXT NOT NULL (hash bcrypt)
- `wins`: INTEGER DEFAULT 0
- `losses`: INTEGER DEFAULT 0
- `fecha_registro`: TIMESTAMP DEFAULT CURRENT_TIMESTAMP

### Operaciones

**Creación de usuario**:
- Valida unicidad de username
- Hash de contraseña con bcrypt
- Inicializa wins y losses en 0

**Autenticación**:
- Busca usuario por username
- Verifica hash de contraseña con bcrypt
- Retorna usuario si credenciales válidas

**Actualización de estadísticas**:
- `sumar_ganada(user_id)`: Incrementa wins
- `sumar_perdida(user_id)`: Incrementa losses
- Operaciones atómicas con manejo de errores

**Búsqueda**:
- `obtener_estadisticas(username)`: Retorna estadísticas públicas de cualquier usuario

## 🔐 Sistema de Autenticación

### Flujo de Registro

1. Usuario ingresa username y password en `RegisterScreen`
2. `AuthManager.register()` valida y crea usuario:
   - Verifica que username no exista
   - Genera hash bcrypt de contraseña
   - Crea usuario en base de datos mediante `UsuarioRepository`
3. Si exitoso, establece sesión automáticamente
4. Retorna a menú con sesión activa

### Flujo de Login

1. Usuario ingresa username y password en `LoginScreen`
2. `AuthManager.login()` autentica:
   - Busca usuario por username
   - Verifica hash de contraseña con bcrypt
   - Establece `current_user` si credenciales válidas
3. Si exitoso, retorna a menú con sesión activa

### Flujo de Guardado de Estadísticas

1. Al finalizar partida en `GameScreen`:
   - Se determina si jugador ganó o perdió
   - Si hay `AuthManager.current_user`:
     - Se llama `AuthManager.update_stats(wins, losses)`
     - Se actualiza base de datos mediante `UsuarioRepository`
2. Estadísticas se guardan automáticamente sin intervención del usuario

### Seguridad

- **Hash de contraseñas**: bcrypt con salt automático
- **No almacenamiento de contraseñas en texto plano**
- **Validación de unicidad de usuarios**
- **Manejo robusto de errores**

## ⚙️ Configuración

### Parámetros Principales en `config.py`

#### Pantalla
```python
ANCHO_PANTALLA = 1280
ALTO_PANTALLA = 720
FPS = 60
```

#### Juego
```python
PUNTOS_PARA_GANAR = 3  # Puntos necesarios para ganar la partida
OPCIONES_JUEGO = ["rock", "paper", "scissors"]
```

#### Animaciones
```python
VELOCIDAD_ANIMACION = 20  # Píxeles por frame
DURACION_EFECTO_COLISION = 500  # Milisegundos
```

#### Detección de Manos
```python
MAPA_PUNTAS_DEDOS = {
    "INDICE": (8, 6),
    "MEDIO": (12, 10),
    "ANULAR": (16, 14),
    "MENIQUE": (20, 18)
}
```

### Personalización

Para modificar el comportamiento del juego, edita `config.py`:

- **Dificultad**: Cambiar `PUNTOS_PARA_GANAR`
- **Velocidad**: Ajustar `VELOCIDAD_ANIMACION` y `DURACION_EFECTO_COLISION`
- **Colores**: Modificar constantes de color
- **Tamaños**: Ajustar dimensiones de elementos UI

## 💻 Requisitos del Sistema

### Hardware
- **Procesador**: Cualquier procesador moderno (recomendado: 2+ GHz)
- **RAM**: Mínimo 4 GB (recomendado: 8 GB)
- **Cámara Web**: Cámara USB o integrada funcional
- **Pantalla**: Resolución mínima 1280x720

### Software
- **Sistema Operativo**: Windows, Linux o macOS
- **Python**: Versión 3.7 o superior
- **Cámara**: Drivers de cámara instalados y funcionando

### Dependencias del Sistema
- **OpenCV**: Requiere codecs de video (generalmente incluidos)
- **MediaPipe**: Requiere bibliotecas de procesamiento de imágenes
- **Pygame**: Requiere bibliotecas de audio (generalmente incluidas)

## 🐛 Solución de Problemas

### La cámara no se detecta
- Verifica que la cámara esté conectada y funcionando
- Cierra otras aplicaciones que puedan estar usando la cámara
- En Linux, verifica permisos de acceso a `/dev/video0`

### Los gestos no se detectan correctamente
- Asegúrate de tener buena iluminación
- Mantén la mano frente a la cámara con suficiente espacio
- Evita fondos muy complejos o similares al color de la piel

### El juego va lento
- Reduce el tamaño de la ventana de cámara en `config.py`
- Cierra otras aplicaciones que consuman recursos
- Verifica que la cámara no esté capturando a muy alta resolución

### Los sonidos no se reproducen
- Verifica que los archivos `.wav` existan en `Resources/`
- Comprueba que el sistema de audio funcione correctamente
- El juego continuará funcionando sin sonidos si faltan archivos

### Error de base de datos
- Verifica permisos de escritura en el directorio del proyecto
- Asegúrate de que SQLite esté disponible (incluido en Python)
- Revisa que `gestor_juego.db` no esté bloqueado por otra aplicación

## 🔄 Estado del Proyecto

### Funcionalidades Implementadas ✅
- ✅ Detección de gestos en tiempo real
- ✅ Sistema de juego completo con rondas
- ✅ Animaciones de colisión
- ✅ Sistema de sonido
- ✅ Múltiples pantallas con navegación
- ✅ HUD informativo
- ✅ Retorno automático al menú
- ✅ Sistema de autenticación completo
- ✅ Sistema de registro de usuarios
- ✅ Persistencia de datos con SQLite
- ✅ Guardado automático de estadísticas
- ✅ Historial público de usuarios
- ✅ Hash seguro de contraseñas (bcrypt)

### Mejoras Futuras 🚧
- 🚧 Estrategias de IA más avanzadas (análisis de patrones)
- 🚧 Sistema de rankings/leaderboards
- 🚧 Modo multijugador local
- 🚧 Exportación de estadísticas
- 🚧 Personalización de avatares

## 📄 Licencia

Este proyecto es de código abierto y está disponible para uso educativo y personal.

## 👥 Contribuciones

Las contribuciones son bienvenidas. Por favor:
1. Fork el proyecto
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

## 📧 Contacto

Para preguntas o sugerencias sobre el proyecto, por favor abre un issue en el repositorio.

---

**¡Disfruta jugando Piedra, Papel o Tijeras con Visión Artificial!** 🎮✋👊✌️
