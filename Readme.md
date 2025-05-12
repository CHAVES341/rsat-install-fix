# 🛠️ Solución al error al instalar RSAT en Windows

Tuve problemas al instalar RSAT (Remote Server Administration Tools) en Windows. Desde Configuración no se instalaba correctamente y PowerShell mostraba errores sin muchos detalles.

🔗 Fuente del problema detectado en Reddit:  
[Is installing RSAT still broken? (r/SCCM)](https://www.reddit.com/r/SCCM/comments/19ffhej/is_installing_rsat_still_broken/)

---

## 🚧 Problema
- RSAT no se instalaba desde Configuración ni PowerShell.
- Mostraba mensajes como “no se pudo realizar la operación”.
- Causado por políticas de red, bloqueo de Windows Update o WSUS.


## ❓ ¿Por qué ocurre este error

Muchos equipos, especialmente si formaban parte de un dominio o entorno empresarial, están configurados para usar políticas estrictas de Windows Update y WSUS, lo que **bloquea la instalación de características opcionales como RSAT**.

---

## ✅ ¿Qué hace el script?

### 1. **Fuerza al equipo a comportarse como uno “normal”**
- Desactiva restricciones del registro aplicadas por políticas de grupo (como WSUS).
- Permite que Windows Update funcione como en un PC personal.

### 2. **Borra configuraciones de caché antiguas**
- Limpia restos de políticas antiguas para evitar errores o conflictos.

### 3. **Crea carpetas vacías en el Registro**
- Asegura que no haya errores de carga en componentes relacionados con Windows Update.

### 4. **Desactiva WSUS temporalmente**
- Cambia el origen de actualizaciones a los servidores públicos de Microsoft.
- Reinicia el servicio de actualización (`wuauserv`).

### 5. **Instala RSAT desde Windows Update**
- Llama directamente a la función:  
  ```powershell
  Add-WindowsCapability -Online -Name Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0
  ```

### 6. **Restaura la configuración original de WSUS**
- Deja el sistema tal como estaba antes, si usaba WSUS.

---

## 🖥️ ¿Cómo ejecutar el script?

1. Abre PowerShell como administrador.
2. Habilita la ejecución temporal de scripts (si es necesario): Sirve para permitir que se puedan ejecutar scripts .ps1 temporalmente y de forma segura. 
   Evita errores, No cambia nada permanente en el sistema. No requiere reinicio.
   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
   ```
3. Ejecuta el script:
   ```powershell
   .\fix-rsat.ps1
   ```
---

## ✅ Verificar instalación

Abre `Win + R`, escribe `dsa.msc` y presiona Enter. Si se abre la consola de Active Directory, ¡RSAT esta instalado correctamente!