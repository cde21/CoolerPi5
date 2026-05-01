# Habilita el ventilador para Raspberry Pi 5 del sistema operativo Home Assistant
### Home Assistant Add-on / App
Habilita el ventilador activo de la Raspberry Pi 5 al ejecutar Home Assistant OS.<br>
El conector del ventilador integrado de la Raspberry Pi 5 se controla mediante firmware y, por el momento, no puede controlarse con Home Assistant.<br>
Este complemento modifica la configuración de arranque de Home Assistant para forzar el funcionamiento del ventilador. Lea atentamente las instrucciones de instalación, ya que requiere permisos de administrador para su ejecución.<br>
La configuración establece cuatro umbrales de velocidad del ventilador:<br>
35 °C: 40 % de velocidad<br>
50 °C: 55 % de velocidad<br>
60 °C: 69 % de velocidad<br>
65 °C: 98 % de velocidad

# Instalación
Primero, activa el Modo Avanzado: haz clic en tu nombre de usuario en la esquina inferior izquierda y luego activa el Modo Avanzado en Preferencias de Usuario.<br>
En Home Assistant, haz clic en Configuración -> Aplicaciones -> Botón Instalar aplicación (abajo a la derecha) -> Haz clic en los tres puntos de la esquina superior derecha -> Selecciona «Repositorios».<br>
Pega la URL de este repositorio: https://github.com/cde21/CoolerPi5.git<br>
Instala la aplicación/complemento «Pi 5 Fan Enabler» y desactiva primero el Modo de Protección. Luego, pulsa el botón de inicio y observa los registros. Puedes desinstalar el complemento cuando termines.

# Operación

**Nota importante:** Cuando se le solicite reiniciar, desconecte el cable de alimentación de su equipo y reinícielo, o realice un reinicio completo del host si sabe cómo hacerlo. Asegúrese de hacerlo **dos veces**: ¡debe reiniciar el equipo dos veces!

Presione el botón de inicio y observe los registros. Puede desinstalar el complemento cuando termine.

# Sensor Home Assistant
Puedes crear sensores en Home Assistant para monitorizar la velocidad del ventilador, tanto en RPM como en porcentaje de la velocidad máxima.<br>
Primero, revisa los registros del complemento y asegúrate de que haya detectado tu ventilador. Debería mostrarte dos rutas, por ejemplo:<br>
/sys/devices/platform/cooling_fan/hwmon/hwmon1/fan1_input<br>
/sys/devices/platform/cooling_fan/hwmon/hwmon1/pwm1<br>
<br>
Anota estas rutas y luego crea dos nuevos sensores de línea de comandos como se muestra a continuación:
```
command_line:
  - sensor:
      name: "Velocidad del cooler Pi 5 (RPM)"
      icon: "mdi:fan"
      unique_id: "pi5fan_rpm"
      command: 'cat /sys/devices/platform/cooling_fan/hwmon/hwmon1/fan1_input'
      unit_of_measurement: "RPM"
      scan_interval: 15
      value_template: "{{ value | int }}"
      state_class: "measurement"
  - sensor:
      name: "Velocidad del cooler Pi 5 (%)"
      icon: "mdi:fan"
      unique_id: "pi5fan_percentage"
      command: 'cat /sys/devices/platform/cooling_fan/hwmon/hwmon1/pwm1'
      unit_of_measurement: "%"
      scan_interval: 15
      value_template: "{{((value | int) / 255 * 100) | round(0, 'common')}}"
      state_class: "measurement"
```
Asegúrese de reemplazar las rutas en ambos comandos para que coincidan con las que se muestran en ele add-on's logs.


# Créditos
Adaptada del proyecto de sOckhamSter de https://github.com/sOckhamSter/Pi5FanEnabler.git <br>
Adapted from the work by adamoutler:<br>
https://github.com/adamoutler/HassOSConfigurator
