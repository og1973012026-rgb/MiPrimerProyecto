# Sistema de Punto de Venta (POS)

```
Interfaz (PySide6)  →  Servicios  →  Repositorios  →  SQL Server
```

**Tecnologías:** Python · PySide6 · Qt Designer · SQL Server · pyodbc · bcrypt · python-dotenv

---

## Requisitos

- Python 3.10 o superior
- SQL Server (o SQL Server Express) y SQL Server Management Studio (SSMS)
- ODBC Driver 18 for SQL Server

> **Nota (Windows):** si el comando `python` no funciona, usa `py` (por ejemplo `py -m venv .venv`).
> Dentro del entorno virtual activado, `python` funciona normalmente.

---

## Pasos para dejar el sistema funcionando

Sigue las secciones en este orden:

1. Instalación
2. Base de datos
3. Configuración (`.env`)
4. Iniciar el programa

---

## 1. Instalación

Abre PowerShell **dentro de la carpeta del proyecto** (donde está `main.py`).

**1.1 Crear el entorno virtual**

```powershell
python -m venv .venv
```

**1.2 Activarlo**

```powershell
`py -m venv .venv
```

Si aparece un error de "ejecución de scripts deshabilitada", ejecuta esto una vez en la misma
terminal y vuelve a activar:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

Está activo cuando la terminal muestra `(.venv)` al inicio de la línea.

**1.3 Instalar las librerías**

```powershell
python -m pip install -r requirements.txt
```

---

## 2. Base de datos

Todo este paso se hace en **SQL Server Management Studio (SSMS)**.

**2.1 Conectarte al servidor**

Abre SSMS. En la ventana *Connect to Server* verás el campo **Server name**
(por ejemplo `localhost\SQLEXPRESS`). **Anota ese nombre**: lo necesitarás en el paso 3.

**2.2 Ejecutar los scripts**

Ejecuta los archivos de la carpeta `database/` **uno por uno y en este orden**.
Para cada uno: menú **archivo →abri → archivo…**, elige el archivo y presiona **Ejecutar** (o `F5`).

| Orden | Archivo | Qué hace |
|-------|---------|----------|
| 1 | `01_create_database.sql` | Crea la base de datos `POSDB` |
| 2 | `02_tables.sql` | Crea las tablas e índices |
| 3 | `03_seed.sql` | Inserta los roles y las categorías iniciales |

Cada script debe terminar con el mensaje *Commands completed successfully*.
`04_reports.sql` solo contiene las consultas de los reportes y no se ejecuta.


**2.4 Crear el usuario administrador**

En la terminal, con el entorno virtual activado:

```powershell
python database/create_admin_hash.py
```

El script pide una contraseña e imprime un `INSERT`. Copia ese `INSERT`, pégalo en una
ventana nueva de SSMS (Nueva consulta) con la base `POSDB` seleccionada y ejecútalo.
Recuerda esa contraseña: es la del usuario `admin`.

---

## 3. Configuración (`.env`)

Crea un archivo llamado `.env` en la raíz del proyecto, junto a `main.py`, con este contenido:

```
DB_SERVER=
DB_NAME=POSDB
DB_USER=
DB_PASSWORD=
TAX_RATE=0.13
```

| Variable | Qué poner |
|----------|-----------|
| `DB_SERVER` | El nombre de servidor que anotaste en el paso 2.1 |
| `DB_NAME` | El nombre exacto de la base de datos (`POSDB` si usaste los scripts) |
| `DB_USER` | Usuario de SQL Server; déjalo vacío para usar tu usuario de Windows |
| `DB_PASSWORD` | Contraseña de `DB_USER`; vacía si `DB_USER` está vacío |
| `TAX_RATE` | Tasa de impuesto de las ventas (`0.13` = 13 %) |

Escribe los valores sin comillas. El archivo `.env` no se sube a Git.

---

## 4. Iniciar el programa

Con la terminal en la carpeta del proyecto y el entorno virtual activado:

```powershell
python main.py
```

Aparece la ventana de inicio de sesión. Ingresa el usuario `admin` y la contraseña que usaste
en el paso 2.4.

---

## Solución de problemas

| Problema | Solución |
|----------|----------|
| `python` no se reconoce | Usa `py`, o activa primero el entorno virtual |
| `No module named ...` | Activa el entorno e instala `requirements.txt` (paso 1.3) |
| No conecta con la base de datos | Revisa `DB_SERVER` y `DB_NAME` en `.env`, que SQL Server esté encendido y el ODBC Driver 18 |
| `Invalid object name '...'` | Falta crear esa tabla: ejecuta `02_tables.sql` |
| Usuario o contraseña incorrectos | Falta ejecutar el `INSERT` del admin (paso 2.4), o la contraseña no es la que usaste |

---

## Interfaces (Qt Designer)

Los archivos `.ui` están en `app/ui/`. Si modificas uno, regenera su archivo Python
(los `*_ui.py` no se editan a mano):

```powershell
cd app\ui
pyside6-uic login.ui -o login_ui.py
```

---

## Estructura

```
POS/
├── app/
│   ├── config.py · db.py · security.py · session.py
│   ├── models/
│   ├── repositories/    consultas a SQL Server
│   ├── services/        reglas de negocio
│   └── ui/              pantallas, archivos .ui y *_ui.py
├── database/            scripts SQL y create_admin_hash.py
├── docs/
├── main.py
├── requirements.txt
└── .gitignore
```

---

## Autores

Enrique David Gil Canales,
Krissia Alejandra Ardón Cuéllar
Carlos Fernando Alvarado Baires,
Edwin David Ortiz Gómez 
Francisco Javier López Raymundo 
Gabriel Antonio Cruz Beltrán
