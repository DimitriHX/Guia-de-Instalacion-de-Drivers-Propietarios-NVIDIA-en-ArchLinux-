Guía Completa: Instalación de Drivers NVIDIA Proprietarios en Arch Linux

Optimizada para Laptops con GPU Híbrida (Intel + NVIDIA) y Series RTX 40

Este repositorio sirve como una guía exhaustiva y probada para la instalación correcta de los drivers propietarios de NVIDIA en Arch Linux. Está diseñada específicamente para laptops con tecnología Optimus (gráficos híbridos Intel/NVIDIA) y hardware moderno, como la serie RTX 40.

Contenido

1. Prerrequisitos y Actualización

2. Instalación de Drivers y Utilidades

3. Configuración del Kernel y DRM

4. Bloqueo de Drivers Libres (Nouveau)

5. Verificación de la Instalación

6. Uso y Gaming (PRIME Offloading)

7. Solución de Problemas Comunes

8. Notas sobre Wayland vs X11

9. Especificaciones de Hardware Probado
    
    
1. Prerrequisitos y Actualización

Antes de comenzar, es crítico asegurarse de que el sistema esté completamente actualizado para evitar conflictos entre versiones del kernel y los módulos de NVIDIA.
Bash

sudo pacman -Syu

    Nota: Si se actualizó el kernel (linux), se recomienda reiniciar el sistema antes de continuar.

    
2. Instalación de Drivers y Utilidades

Instalaremos el metapaquete nvidia, las utilidades de configuración y nvidia-prime para gestionar el cambio entre la GPU integrada y la dedicada.
Bash

sudo pacman -S nvidia nvidia-utils nvidia-settings nvidia-prime

Una vez instalados, verifica que los módulos se hayan cargado correctamente (es posible que debas reiniciar primero):

lsmod | grep nvidia


Deberías ver una salida que incluya: nvidia_drm, nvidia_modeset, nvidia_uvm, y nvidia.


3. Configuración del Kernel y DRM

Para garantizar un rendimiento óptimo, especialmente en juegos y entornos de escritorio modernos, es necesario habilitar el DRM (Direct Rendering Manager) Kernel Mode Setting.

Crea o edita el archivo de configuración:
Bash

sudo nano /etc/modprobe.d/nvidia.conf

Añade la siguiente línea:
Fragmento de código

options nvidia_drm modeset=1

Finalmente, regenera la imagen initramfs para aplicar los cambios al arranque:
Bash

sudo mkinitcpio -P



4. Bloqueo de Drivers Libres (Nouveau)

Aunque el driver propietario suele hacerlo automáticamente, es una buena práctica bloquear explícitamente el driver de código abierto nouveau para evitar conflictos al cargar el sistema.

Crea el archivo de blacklist:
Bash

sudo nano /etc/modprobe.d/blacklist-nouveau.conf

Añade el siguiente contenido:
Fragmento de código

blacklist nouveau
options nouveau modeset=0



5. Verificación de la Instalación

Reinicia tu computadora. Al volver, verificaremos qué tarjeta gráfica está renderizando por defecto (debería ser la Intel para ahorrar energía) y si la NVIDIA está disponible bajo demanda.

1. Verificar GPU Integrada (Uso normal):
Bash

glxinfo | grep "OpenGL renderer"
# Salida esperada ejemplo: Mesa Intel(R) Graphics (RPL-P)

2. Verificar GPU Dedicada (NVIDIA):
Bash

prime-run glxinfo | grep "OpenGL renderer"
# Salida esperada ejemplo: NVIDIA GeForce RTX 4050 Laptop GPU/PCIe/SSE2

3. Estado general de NVIDIA:
Bash

nvidia-smi

Este comando mostrará la temperatura, versión del driver y procesos activos en la GPU.



6. Uso y Gaming (PRIME Offloading)

Para ejecutar juegos o aplicaciones pesadas utilizando la tarjeta NVIDIA, debes anteponer el comando prime-run.
Ejemplos de uso en terminal:
Bash

# Ejecutar Steam
prime-run steam

# Ejecutar Lutris
prime-run lutris

# Ejecutar un binario/script específico
prime-run ./Juego.bin

Configuración en Launchers de Juegos:

    Steam: En las "Propiedades" del juego > "Opciones de lanzamiento", añade: prime-run %command%

    Lutris / Heroic Games Launcher: Busca en las opciones del "Runner" la casilla: Enable Prime Render Offload (o similar) y actívala.
    
    
    
7. Solución de Problemas Comunes
Error: "prime-run: command not found"

Falta instalar el paquete de gestión híbrida. Solución:
Bash

sudo pacman -S nvidia-prime

Steam no detecta la GPU NVIDIA

Asegúrate de iniciar Steam usando prime-run steam desde la terminal para probar, o configura las opciones de lanzamiento de cada juego individualmente como se indicó en la sección 6.
Verificación de Logs

Si tienes problemas gráficos o pantallazos negros, revisa los logs de Xorg:
Bash

cat /var/log/Xorg.0.log | grep NVIDIA

8. Notas sobre Wayland vs X11

Aunque el soporte de NVIDIA para Wayland ha mejorado drásticamente, X11 sigue siendo más estable para gaming en algunas configuraciones híbridas.

Si experimentas parpadeos o bajo rendimiento en Wayland, se recomienda usar X11.

Para forzar X11 en GDM (GNOME):

    Editar configuración: sudo nano /etc/gdm/custom.conf

    Descomentar: WaylandEnable=false

Para SDDM (KDE Plasma): Simplemente selecciona "Plasma (X11)" en la esquina inferior de la pantalla de inicio de sesión.


9. Especificaciones de Hardware Probado

Esta guía fue creada y validada utilizando el siguiente hardware:
Componente	Especificación
Modelo	HP Victus 15
CPU	Intel Core i5 13ª Gen
GPU	NVIDIA GeForce RTX 4050 Laptop GPU
OS	Arch Linux
Kernel	6.x

