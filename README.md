# Robótica Móvil: Talleres

Talleres de la materia Robótica Móvil. Un robot diferencial (Pioneer P3-DX) simulado en CoppeliaSim y programado con ROS 2 Humble, desde la intro a ROS hasta localización con EKF y planeamiento de caminos.

## Estructura

| Carpeta | Contenido |
|---|---|
| `talleres/` | Un taller por carpeta, numerado y con nombre del tema (ver abajo) |
| `docker/` | Imagen con ROS 2 Humble + CoppeliaSim y scripts para levantar el contenedor |
| `referencias/` | Guía de Docker de la materia |

Cada taller es un **workspace independiente** con la misma estructura:

| Dentro de un taller | Contenido |
|---|---|
| `consigna.pdf` | Enunciado del taller |
| `paquetes/` | Paquetes ROS 2. Las escenas de CoppeliaSim (`.ttt`) están en `paquetes/<paquete>/coppeliaSim/` |
| `urdf/` | Modelo del robot para RViz |

Los talleres son acumulativos: cada uno trae una copia de los paquetes de los anteriores, con los cambios que se le fueron haciendo. Por eso no se pueden compilar todos juntos (colcon rechaza paquetes con el mismo nombre) y se monta **un taller a la vez**.

## Talleres

| Taller | Paquete nuevo | Qué se implementa |
|---|---|---|
| `05-intro-ros2` | `ros_intro` (+ `ros2-keyboard`) | Primer nodo: teleoperación del robot con el teclado (`keys_to_twist`) |
| `06-modelo-diferencial` | `modelo_diferencial`, `robmovil_msgs` | Cinemática del robot diferencial y odometría a partir de los encoders (`pioneer_odometry_node`) |
| `07-lazo-abierto` | `lazo_abierto` | Generación de trayectorias y seguimiento a lazo abierto con control *feedforward* |
| `08-lazo-cerrado` | `lazo_cerrado` | Seguimiento de trayectorias a lazo cerrado con un controlador cinemático de posición, más un nodo logger |
| `09-lidar-imu` | `imu_laser` | Calibración del sesgo de la IMU y detección de postes (landmarks) con el LiDAR |
| `10-localizacion-ekf` | `robmovil_ekf`, `kfilter` | Localización con un EKF que fusiona odometría y landmarks (biblioteca `kfilter` de la cátedra) |
| `11-planeamiento-astar` | `astar_planning`, `robmovil_planning` | Planeamiento de caminos con A* sobre una grilla de ocupación |
| `12-planeamiento-rrt` | `rrt_planning`, `rrt_pioneer_planning` | Planeamiento con RRT respetando la cinemática del Pioneer |

## Uso

```bash
# Host: construir la imagen y levantar el contenedor con un taller montado en /root/ros2_ws/src/robotica
bash docker/start-docker.sh build
bash docker/start-docker.sh start talleres/10-localizacion-ekf   # sin argumento: el 12
bash docker/start-docker.sh open
```

El contenedor queda atado al taller con el que se creó: para cambiar de taller, `bash docker/start-docker.sh down` y de nuevo `start <taller>`. La primera vez hay que registrar los mensajes en el plugin de CoppeliaSim (ver `docker/pasos_docker.txt`).

Dentro del contenedor:

```bash
cd /root/ros2_ws && colcon build --symlink-install && source install/setup.bash
ros2 launch <paquete> <archivo>.launch.py
```

con la escena `.ttt` del taller abierta en CoppeliaSim.
