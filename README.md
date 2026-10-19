# BatGest

**Gestión inteligente de la batería de ThinkPad desde los ajustes rápidos de GNOME.**  
**Smart ThinkPad battery management from GNOME Quick Settings.**

> **Estado / Status:** prototipo funcional en pruebas (*testing preview*). La integración gráfica se ha validado en un ThinkPad X13 Gen 1 AMD; todavía quedan pruebas de extremo a extremo y no se ha publicado una versión instalable en este repositorio.

## Español

### ¿Qué es BatGest?

**BatGest** es una herramienta para gestionar los **umbrales de carga de la batería** de portátiles Lenovo ThinkPad compatibles con [TLP](https://linrunner.de/tlp/), desde el menú de **ajustes rápidos de GNOME Shell**.

El proyecto comenzó como un script Bash para un ThinkPad X280 y está evolucionando hacia una extensión con un submenú similar al de Wi-Fi. Su objetivo es equilibrar **autonomía, comodidad y longevidad de la batería**, evitando recargas innecesarias sin renunciar a la carga máxima cuando sea necesaria.

### Entorno y compatibilidad

| Componente | Entorno de desarrollo |
| --- | --- |
| Portátil | Lenovo ThinkPad X13 Gen 1 AMD |
| Sistema operativo | EndeavourOS (Arch Linux) |
| Escritorio | GNOME Shell 51.1 |
| Sesión gráfica | Wayland |
| Gestión energética | TLP 1.10.2 |
| Interfaz | Extensión de GNOME / Quick Settings |
| Backend | Bash, Python y Polkit |

**Advertencia:** la compatibilidad con otros modelos de ThinkPad, distribuciones, versiones de GNOME o disposiciones de batería no está comprobada. La implementación actual utiliza `BAT0` y rutas adaptadas al entorno de desarrollo.

### Funcionalidades

**Comprobadas en el X13:**
- Integración en los ajustes rápidos de GNOME, sin abrir una terminal.
- Submenú desplegable con información de batería.
- Consulta de nivel, estado de carga, salud aproximada, ciclos y umbrales efectivos.
- Indicador **naranja mientras está cargando** y gris cuando no carga, según el tema de GNOME.
- Visualización de los perfiles de batería y de la carga puntual.

**Implementadas, pendientes de validación completa:**
- Cambio de perfil y aplicación de umbrales mediante TLP.
- Persistencia de los límites elegidos entre reinicios.
- Carga puntual mediante `tlp chargeonce`.
- Autorización específica con Polkit sin guardar la contraseña.
- Restauración de umbrales y comprobaciones de seguridad.

**Limitación conocida:** la interfaz consulta el estado periódicamente, de modo que el color y la información mostrada pueden tardar en actualizarse tras seleccionar un perfil.

### Perfiles

| Perfil | Inicio de carga | Fin de carga | Uso previsto |
| --- | ---: | ---: | --- |
| **Estándar** | 30 % | 90 % | Uso diario y días de clase |
| **Ahorro** | 20 % | 80 % | Conservación de la batería durante largas sesiones conectado a la red |
| **Viaje** | 99 % | 100 % | Máxima autonomía en circunstancias excepcionales |

**Importante:** los porcentajes son **umbrales de carga**, no límites de descarga. Por ejemplo, el 30 % en Estándar significa que el controlador puede comenzar a cargar cuando la batería cae por debajo de ese valor **si está conectada a la corriente**; no impedirá que el portátil siga descargándose cuando esté desenchufado.

Seleccionar un perfil tampoco obliga siempre a cargar inmediatamente: el controlador puede esperar al umbral inferior. Si ya está por debajo de él y hay alimentación externa, la carga puede comenzar automáticamente.

### Carga puntual y margen de protección

BatGest contempla una carga puntual hasta el máximo del perfil elegido, incluso si todavía no se ha alcanzado su umbral inferior.

Para evitar recargas pequeñas innecesarias, propone la operación solo cuando faltan **al menos 10 puntos porcentuales** para el límite superior. **Viaje** está exento de esa regla para permitir alcanzar la carga máxima bajo demanda.

Ejemplo: estando al 75 % en **Ahorro** (máximo 80 %), no ofrecería una recarga de solo cinco puntos; al 70 %, sí podría ofrecerla.

Este margen es una decisión de uso conservadora, **no una condición de seguridad electroquímica**. Cargar al 100 % puntualmente es normal; lo que favorece el envejecimiento es permanecer mucho tiempo a tensiones altas, especialmente con calor. El controlador detiene la carga al alcanzar el umbral superior, pero eso no elimina el envejecimiento químico normal.

### Arquitectura y seguridad

```text
Ajustes rápidos de GNOME
          │
          ▼
Extensión BatGest (sin privilegios)
          │
          ▼
Polkit + helper privilegiado restringido
          │
          ▼
TLP + controlador de carga ThinkPad
```

BatGest no necesita almacenar una contraseña de administrador ni utilizar claves SSH. Las acciones privilegiadas se encaminan a un componente propiedad de `root`, limitado a operaciones y perfiles previstos.

**Consideración de seguridad:** si una regla Polkit permite esas operaciones sin contraseña al usuario local activo, otros procesos que actúen con su identidad pueden solicitar las mismas acciones autorizadas. El helper y la política deben someterse a revisión antes de recomendar la instalación fuera del entorno de desarrollo.

### Estado del proyecto

- [x] Prototipo integrado en GNOME Shell 51.
- [x] Visualización de batería y cambios de color durante la carga.
- [x] Submenú con tres perfiles y opción de carga puntual.
- [ ] Completar las pruebas de carga, límites y margen de 10 puntos.
- [ ] Verificar persistencia entre reinicios y restauración al desconectar.
- [ ] Mejorar la actualización inmediata de la interfaz tras una acción.
- [ ] Publicar aquí el código fuente, las pruebas y un instalador revisado.
- [ ] Implementar actualizaciones desde GitHub, con comprobación de integridad, copia de seguridad y reversión.
- [ ] Validar otras versiones de GNOME y otros modelos de ThinkPad.

### Instalación y licencia

**Todavía no hay una versión publicada lista para instalar desde este repositorio.** Las instrucciones reproducibles se incorporarán cuando se haya subido y revisado el código y el instalador.

**Licencia:** pendiente de decidir. Que el repositorio sea público no implica, por sí mismo, permiso para redistribuir o modificar su contenido.

---

## English

### What is BatGest?

**BatGest** manages **battery charge thresholds** on Lenovo ThinkPad laptops supported by [TLP](https://linrunner.de/tlp/), directly from **GNOME Shell Quick Settings**.

The project started as a Bash script for a ThinkPad X280 and is evolving into a GNOME extension with a Wi-Fi-like submenu. Its purpose is to balance **battery life, usability, and longevity**, avoiding unnecessary top-ups while keeping maximum charge available when needed.

### Development environment and compatibility

| Component | Development environment |
| --- | --- |
| Laptop | Lenovo ThinkPad X13 Gen 1 AMD |
| Operating system | EndeavourOS (Arch Linux) |
| Desktop | GNOME Shell 51.1 |
| Display session | Wayland |
| Power management | TLP 1.10.2 |
| Interface | GNOME Quick Settings extension |
| Backend | Bash, Python and Polkit |

**Warning:** compatibility with other ThinkPads, distributions, GNOME versions, or battery layouts has not been verified. The current implementation assumes `BAT0` and development-machine-specific paths.

### Features

**Verified on the X13:**
- GNOME Quick Settings integration without opening a terminal.
- Expandable submenu with battery information.
- Battery level, charging state, estimated health, cycle count, and effective thresholds.
- **Orange indicator while charging**, grey otherwise, following the GNOME theme.
- Display of charging profiles and one-time-charge controls.

**Implemented, not yet fully validated end to end:**
- Changing profiles and applying thresholds through TLP.
- Persisting the selected profile across restarts.
- Requesting a one-time charge with `tlp chargeonce`.
- Scoped Polkit authorization without storing passwords.
- Threshold restoration and safety checks.

**Known limitation:** battery state is polled periodically, so the indicator and displayed information may take some time to update after selecting a profile.

### Battery profiles

| Profile | Start charging | Stop charging | Intended use |
| --- | ---: | ---: | --- |
| **Standard** | 30% | 90% | Everyday use and class days |
| **Savings** | 20% | 80% | Battery preservation during long periods on external power |
| **Travel** | 99% | 100% | Maximum battery life for exceptional circumstances |

**Important:** these values are **charge thresholds**, not discharge limits. For example, the 30% Standard threshold allows charging to begin below that level **when connected to power**; it does not stop the battery from discharging when unplugged.

Selecting a profile does not necessarily initiate charging immediately: the controller may wait for the lower threshold. Charging can start automatically when the battery is below that threshold and external power is connected.

### One-time charging and protection margin

BatGest provides for one-time charging up to the selected profile's maximum even when the battery has not dropped below its regular start threshold.

To avoid unnecessary short top-ups, it offers this action only if the battery is **at least 10 percentage points below** the upper limit. **Travel mode** is exempt so maximum charge remains available on demand.

For example, at 75% with **Savings** selected (80% maximum), no five-point top-up would be offered; at 70%, it could be.

This is a conservative usability choice, **not an electrochemical safety requirement**. Occasional charging to 100% is normal; spending long periods at high cell voltage, especially when hot, contributes more to aging. The controller stops charging at the configured upper threshold, but normal chemical aging continues.

### Architecture and security

```text
GNOME Quick Settings
          │
          ▼
BatGest extension (unprivileged)
          │
          ▼
Polkit + restricted privileged helper
          │
          ▼
TLP + ThinkPad charge controller
```

BatGest does not need to store an administrator password or SSH keys. Privileged actions go through a `root`-owned helper that accepts only predefined operations and profiles.

**Security consideration:** when Polkit allows these operations without a password for an active local user, other processes running with that user's identity may request the same authorized actions. The helper and policy require review before broader deployment.

### Project status

- [x] GNOME Shell 51 prototype.
- [x] Battery information and charging-color indicator.
- [x] Three-profile submenu and one-time-charge option.
- [ ] Complete charging, threshold, and 10-point margin tests.
- [ ] Verify persistence across restarts and restoration on unplugging.
- [ ] Improve immediate interface refresh after an action.
- [ ] Publish reviewed source code, tests, and installer here.
- [ ] Add GitHub-based updates with integrity checks, backup, and rollback.
- [ ] Test additional GNOME versions and ThinkPad models.

### Installation and license

**No installable release has been published in this repository yet.** Reproducible installation instructions will be added once the code and installer have been uploaded and reviewed.

**License:** to be decided. Public repository visibility alone does not grant permission to reuse or redistribute the code.
