<div align="center">

# Guía Completa: Drivers NVIDIA en Arch Linux
### Optimizada para Laptops Híbridas (Intel + NVIDIA) y Series RTX 40

[![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?logo=arch-linux&logoColor=white)](https://archlinux.org/)
[![NVIDIA](https://img.shields.io/badge/NVIDIA-76B900?logo=nvidia&logoColor=white)](https://www.nvidia.com/)
[![Driver Status](https://img.shields.io/badge/Drivers-Proprietary-red)]()

</div>

Este repositorio sirve como una guía exhaustiva y probada para la instalación correcta de los drivers propietarios de NVIDIA en Arch Linux. Está diseñada específicamente para laptops con tecnología **Optimus** (gráficos híbridos Intel/NVIDIA) y hardware moderno, como la serie **RTX 40**.

---

## 📑 Contenido

1. [Prerrequisitos y Actualización](#1-prerrequisitos-y-actualización)
2. [Instalación de Drivers y Utilidades](#2-instalación-de-drivers-y-utilidades)
3. [Configuración del Kernel y DRM](#3-configuración-del-kernel-y-drm)
4. [Bloqueo de Drivers Libres (Nouveau)](#4-bloqueo-de-drivers-libres-nouveau)
5. [Verificación de la Instalación](#5-verificación-de-la-instalación)
6. [Uso y Gaming (PRIME Offloading)](#6-uso-y-gaming-prime-offloading)
7. [Solución de Problemas Comunes](#7-solución-de-problemas-comunes)
8. [Notas sobre Wayland vs X11](#8-notas-sobre-wayland-vs-x11)
9. [Especificaciones de Hardware Probado](#9-especificaciones-de-hardware-probado)

---

### 1. Prerrequisitos y Actualización

Antes de comenzar, es crítico asegurarse de que el sistema esté completamente actualizado para evitar conflictos entre versiones del kernel y los módulos de NVIDIA.

```bash
sudo pacman -Syu
```

    
### 2. Instalación de Drivers y Utilidades

Instalaremos el metapaquete nvidia, las utilidades de configuración y nvidia-prime para gestionar el cambio entre la GPU integrada y la dedicada.
Bash

```bash 
sudo pacman -S nvidia nvidia-utils nvidia-settings nvidia-prime
```

Una vez instalados, verifica que los módulos se hayan cargado correctamente (es posible que debas reiniciar primero):

``` bash
lsmod | grep nvidia
```


Deberías ver una salida que incluya: nvidia_drm, nvidia_modeset, nvidia_uvm, y nvidia.


### 3. Configuración del Kernel y DRM

Para garantizar un rendimiento óptimo, especialmente en juegos y entornos de escritorio modernos, es necesario habilitar el DRM (Direct Rendering Manager) Kernel Mode Setting.

Crea o edita el archivo de configuración:
Bash

```bash
sudo nano /etc/modprobe.d/nvidia.conf
```

Añade la siguiente línea:
Fragmento de código

```bash
options nvidia_drm modeset=1
```

Finalmente, regenera la imagen initramfs para aplicar los cambios al arranque:

```bash
sudo mkinitcpio -P
```

### 4. Bloqueo de Drivers Libres (Nouveau)

Aunque el driver propietario suele hacerlo automáticamente, es una buena práctica bloquear explícitamente el driver de código abierto nouveau para evitar conflictos al cargar el sistema.

Crea el archivo de blacklist:
```bash
sudo nano /etc/modprobe.d/blacklist-nouveau.conf
```
Añade el siguiente contenido:
Fragmento de código

```bash
blacklist nouveau
options nouveau modeset=0
```

### 5. Verificación de la Instalación

Reinicia tu computadora. Al volver, verificaremos qué tarjeta gráfica está renderizando por defecto (debería ser la Intel para ahorrar energía) y si la NVIDIA está disponible bajo demanda.

1. Verificar GPU Integrada (Uso normal):

```bash
glxinfo | grep "OpenGL renderer"
```
# Salida esperada ejemplo: Mesa Intel(R) Graphics (RPL-P)

2. Verificar GPU Dedicada (NVIDIA):

```bash
prime-run glxinfo | grep "OpenGL renderer"
```
# Salida esperada ejemplo: NVIDIA GeForce RTX 4050 Laptop GPU/PCIe/SSE2

3. Estado general de NVIDIA:

```bash
nvidia-smi
```
Este comando mostrará la temperatura, versión del driver y procesos activos en la GPU.

### 6. Uso y Gaming (PRIME Offloading)

Para ejecutar juegos o aplicaciones pesadas utilizando la tarjeta NVIDIA, debes anteponer el comando prime-run.
Ejemplos de uso en terminal:
Bash

# Ejecutar Steam
```bash
prime-run steam
```

# Ejecutar Lutris
```bash
prime-run lutris
```

# Ejecutar un binario/script específico
```bash
prime-run ./Juego.bin
```

Configuración en Launchers de Juegos:

    Steam: En las "Propiedades" del juego > "Opciones de lanzamiento", añade: prime-run %command%

    Lutris / Heroic Games Launcher: Busca en las opciones del "Runner" la casilla: Enable Prime Render Offload (o similar) y actívala.
    
    
### 7. Solución de Problemas Comunes
Error: "prime-run: command not found"

Falta instalar el paquete de gestión híbrida. Solución:

```bash
sudo pacman -S nvidia-prime
```
Steam no detecta la GPU NVIDIA

Asegúrate de iniciar Steam usando prime-run steam desde la terminal para probar, o configura las opciones de lanzamiento de cada juego individualmente como se indicó en la sección 6.
Verificación de Logs

Si tienes problemas gráficos o pantallazos negros, revisa los logs de Xorg:
```bash
cat /var/log/Xorg.0.log | grep NVIDIA
```

### 8. Notas sobre Wayland vs X11

Aunque el soporte de NVIDIA para Wayland ha mejorado drásticamente, X11 sigue siendo más estable para gaming en algunas configuraciones híbridas.

Si experimentas parpadeos o bajo rendimiento en Wayland, se recomienda usar X11.

Para forzar X11 en GDM (GNOME):

    Editar configuración: sudo nano /etc/gdm/custom.conf

    Descomentar: WaylandEnable=false

Para SDDM (KDE Plasma): Simplemente selecciona "Plasma (X11)" en la esquina inferior de la pantalla de inicio de sesión.


### 9. Especificaciones de Hardware Probado

Esta guía fue creada y validada utilizando el siguiente hardware:
| Componente | Especificación |
| :--- | :--- |
| `Modelo` | HP Victus 15 |
| `CPU` | Intel Core i5 13ª Gen|
| `GPU` | NVIDIA GeForce RTX 4050 Laptop GPU |
| `OS` | Arch Linux |
| `Kernel` | 6.x |
		
	
	
	

