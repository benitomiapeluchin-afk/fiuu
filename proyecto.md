# 🛠️ FIUU! PHP — DOCUMENTO MAESTRO DE IMPLEMENTACIÓN

## Parte 1/3 — Arquitectura, modelo de datos, seguridad y fundamentos
**Versión:** 1.0
**Fecha:** 30 de agosto de 2026
**Proyecto:** Fiuu! — Plataforma Universal de Gestión Operativa
**Arquitectura:** PHP 8.4 + MariaDB + PDO + HTML + CSS + JavaScript
**Framework:** Ninguno
**Objetivo:** Sistema web liviano, modular, seguro y fácil de mantener.

---

# 1. MISIÓN DEL PROYECTO
Fiuu! es una plataforma universal para transformar información operativa desordenada en trabajo organizado.

El sistema permite administrar:

- Personas.
- Clientes.
- Ubicaciones.
- Activos.
- Órdenes de trabajo.
- QR.
- Evidencias.
- Historial.
- Usuarios.
- Roles.
- Permisos.
- Configuración por rubro.
El sistema NO debe estar diseñado exclusivamente para mantenimiento.

Debe poder utilizarse para:

- Empresas de mantenimiento.
- Autoelevadores.
- Fotocopiadoras.
- Colegios.
- Consorcios.
- Servicios técnicos.
- Empresas de logística.
- Instaladores.
- Servicios generales.
- O cualquier organización que necesite organizar trabajo operativo.

---

# 2. DECISIÓN ARQUITECTÓNICA FUNDAMENTAL

## NO utilizar Laravel.
El proyecto debe implementarse con:

```
PHP 8.4
MariaDB
PDO
HTML5
CSS
JavaScript vanilla
JSON
PHP Sessions
```
Se podrán utilizar librerías externas exclusivamente cuando aporten una función concreta que sería innecesariamente compleja desarrollar desde cero.

Ejemplo:

- Librería para leer XLSX.
No se debe incorporar un framework completo únicamente por comodidad.

---

# 3. OBJETIVO DE LA ARQUITECTURA
La aplicación debe ser:

- Liviana.
- Comprensible.
- Modular.
- Segura.
- Fácil de instalar.
- Fácil de copiar.
- Fácil de actualizar.
- Compatible con cPanel.
- Compatible con hosting PHP convencional.
- Fácil de mantener por una IA.
- Fácil de auditar por un programador humano.
Debe evitarse cualquier abstracción innecesaria.

## Regla:

> No construir un "Laravel casero".
No crear capas abstractas solamente para imitar un framework.

---

# 4. MODELO DE INSTALACIÓN
Fiuu! NO será un SaaS multi-tenant centralizado.

Cada organización tendrá una instalación independiente.

Ejemplo:

```
Servidor
│
├── fiuu-cliente-a/
│   ├── aplicación
│   ├── configuración
│   ├── archivos
│   └── MariaDB propia
│
├── fiuu-cliente-b/
│   ├── aplicación
│   ├── configuración
│   ├── archivos
│   └── MariaDB propia
```
Esto simplifica:

- Seguridad.
- Backups.
- Migraciones.
- Aislamiento.
- Eliminación.
- Restauración.
- Soporte.
No es necesario construir un sistema multi-tenant complejo.

---

# 5. PRINCIPIOS DE DISEÑO

## 5.1 Simplicidad
Cada componente debe hacer una cosa.

## 5.2 Seguridad
Nunca confiar en datos enviados por el navegador.

## 5.3 Separación
La interfaz nunca accede directamente a MariaDB.

## 5.4 API
Toda operación de datos se realiza mediante PHP.

## 5.5 Configuración
El rubro se define mediante JSON cuando sea posible.

## 5.6 Modularidad
Los módulos opcionales no deben modificar innecesariamente el núcleo.

## 5.7 UX
La aplicación debe ser comprensible por una persona de aproximadamente 14 años.

---

# 6. ARQUITECTURA GENERAL

```
                  INTERNET
                     │
                    HTTPS
                     │
                     ▼
            ┌──────────────────┐
            │   INTERFAZ WEB   │
            │ HTML + CSS + JS  │
            └────────┬─────────┘
                     │
                  fetch()
                     │
                     ▼
            ┌──────────────────┐
            │      API PHP     │
            │                  │
            │ autenticación    │
            │ permisos         │
            │ validación       │
            │ lógica           │
            └────────┬─────────┘
                     │
                    PDO
                     │
                     ▼
            ┌──────────────────┐
            │     MariaDB      │
            └──────────────────┘
```
La base de datos NO debe ser accesible directamente desde Internet.

---

# 7. ESTRUCTURA DE CARPETAS
La estructura inicial recomendada es:

```
fiuu/
│
├── api/
│   ├── auth.php
│   ├── usuarios.php
│   ├── personas.php
│   ├── clientes.php
│   ├── ubicaciones.php
│   ├── activos.php
│   ├── ordenes.php
│   ├── qr.php
│   ├── archivos.php
│   ├── notificaciones.php
│   └── configuracion.php
│
├── core/
│   ├── bootstrap.php
│   ├── database.php
│   ├── auth.php
│   ├── permissions.php
│   ├── validation.php
│   ├── security.php
│   ├── response.php
│   ├── request.php
│   └── helpers.php
│
├── config/
│   ├── config.php
│   └── app.php
│
├── public/
│   ├── index.html
│   ├── login.html
│   ├── dashboard.html
│   ├── ordenes.html
│   ├── activos.html
│   ├── clientes.html
│   ├── personas.html
│   ├── ubicaciones.html
│   ├── usuarios.html
│   ├── configuracion.html
│   ├── qr.html
│   │
│   ├── css/
│   ├── js/
│   └── assets/
│
├── qr/
│   └── index.php
│
├── install/
│   └── instalar.php
│
├── imports/
│   ├── templates/
│   └── processors/
│
├── templates/
│   └── rubros/
│       ├── colegio.json
│       ├── consorcio.json
│       ├── autoelevadores.json
│       └── fotocopiadoras.json
│
├── storage/
│   ├── uploads/
│   ├── logs/
│   └── installed.lock
│
├── database/
│   ├── schema.sql
│   └── migrations/
│
└── README.md
```

---

# 8. REGLA SOBRE ARCHIVOS PÚBLICOS
El navegador solamente debe tener acceso directo a:

```
/public/
```
Nunca deben quedar públicamente accesibles:

```
/config/
/core/
database/
storage/
```
especialmente:

```
config.php
database.php
schema.sql
logs/
```
Si el hosting utiliza `public_html`, la configuración deberá impedir el acceso directo a esos directorios.

---

# 9. LOS 6 CONCEPTOS UNIVERSALES
El núcleo conceptual de Fiuu! permanece igual.

## 1. ORGANIZACIÓN
Quién utiliza el sistema.

Tabla:

```
organizations
```

---

## 2. PERSONAS
Quién participa en la operación.

Ejemplos:

- Jefe.
- Técnico.
- Chofer.
- Administrativo.
- Operario.
Tabla:

```
people
```

---

## 3. CLIENTES
Para quién se trabaja.

Ejemplos:

- Colegio.
- Fábrica.
- Consorcio.
- Comercio.
Tabla:

```
clients
```

---

## 4. UBICACIONES
Dónde ocurre algo.

Ejemplos:

- Aula.
- Planta.
- Departamento.
- Taller.
- Oficina.
Tabla:

```
locations
```

---

## 5. ACTIVOS
Qué objeto se administra.

Ejemplos:

- Aire acondicionado.
- Ascensor.
- Autoelevador.
- Fotocopiadora.
- Proyector.
Tabla:

```
assets
```

---

## 6. ÓRDENES DE TRABAJO
Qué hay que hacer.

Tabla:

```
work_orders
```

---

# 10. TABLAS PRINCIPALES

## organizations

```
id
name
legal_name
logo
config
status
created_at
updated_at
```

---

## users
Representa las cuentas que pueden ingresar.

```
id
person_id
name
email
password_hash
role
active
last_login_at
created_at
updated_at
```

---

## people

```
id
name
last_name
email
phone
role
active
notes
created_at
updated_at
```

---

## clients

```
id
name
legal_name
code
contact_name
contact_email
contact_phone
address
status
config
created_at
updated_at
```

---

## locations

```
id
client_id
parent_id
name
type
description
address
qr_enabled
created_at
updated_at
```
`parent_id` permite estructuras jerárquicas.

Ejemplo:

```
Colegio
└── Pabellón Primaria
    └── Primer Piso
        └── Aula P1-03
```

---

## asset_types

```
id
name
slug
icon
custom_fields
active
created_at
updated_at
```
`custom_fields` será JSON.

---

## assets

```
id
client_id
location_id
asset_type_id
name
code
brand
model
serial_number
status
custom_data
notes
created_at
updated_at
```
`custom_data` será JSON.

---

# 11. work_orders

```
id
code
client_id
location_id
asset_id
created_by
assigned_to
category
title
description
priority
status
due_at
started_at
completed_at
cancelled_at
created_at
updated_at
```
Estados iniciales:

```
entrada
pendiente
asignada
en_proceso
finalizada
cancelada
```
Prioridades:

```
normal
media
alta
urgente
```

---

# 12. qr_codes

```
id
uuid
entity_type
entity_id
active
created_at
updated_at
```
El UUID será generado criptográficamente.

No utilizar IDs secuenciales visibles como identificador público.

---

# 13. attachments

```
id
work_order_id
uploaded_by
filename
stored_filename
mime_type
size
path
created_at
```
Las fotografías y documentos se almacenan en filesystem.

MariaDB almacena únicamente la referencia.

---

# 14. work_order_comments

```
id
work_order_id
user_id
comment
created_at
```

---

# 15. notifications

```
id
user_id
work_order_id
type
title
message
priority
read_at
created_at
```

---

# 16. AUDITORÍA
Debe existir una tabla:

```
audit_logs
```
Campos:

```
id
user_id
action
entity_type
entity_id
old_data
new_data
ip
user_agent
created_at
```
Debe registrar operaciones importantes:

- Crear.
- Modificar.
- Asignar.
- Reasignar.
- Cambiar estado.
- Cancelar.
- Eliminar.
- Cambiar permisos.

---

# 17. AUTENTICACIÓN
Utilizar exclusivamente funciones nativas seguras de PHP.

Creación:

```
password_hash($password, PASSWORD_DEFAULT)
```
Verificación:

```
password_verify($password, $hash)
```
Nunca almacenar:

```
contraseña
password
clave
```
en texto plano.

---

# 18. SESIONES
Utilizar:

```
session_start();
```
La sesión debe almacenar únicamente información mínima:

```
user_id
role
```
La organización se determina por la instalación.

Al iniciar sesión correctamente:

```
session_regenerate_id(true);
```
Configurar cookies:

```
HttpOnly
Secure
SameSite=Lax o Strict
```
cuando corresponda.

---

# 19. AUTORIZACIÓN
La autenticación responde:

> ¿Quién sos?
La autorización responde:

> ¿Qué podés hacer?
Crear:

```
core/permissions.php
```
Funciones conceptuales:

```
canViewOrders()
canCreateOrder()
canAssignOrder()
canCancelOrder()
canManageUsers()
canManageAssets()
canManageClients()
canManageConfiguration()
```

---

# 20. ROLES INICIALES

## jefe
Puede:

- Ver todo.
- Crear órdenes.
- Asignar.
- Reasignar.
- Cambiar prioridades.
- Cancelar.
- Administrar usuarios.
- Administrar personas.
- Administrar clientes.
- Administrar activos.
- Configurar sistema.

## operario
Puede:

- Ver sus tareas.
- Aceptar.
- Iniciar.
- Comentar.
- Subir evidencia.
- Finalizar.
- Crear reportes rápidos.

## administrativo
Puede configurarse según necesidades.

---

# 21. API
La API utiliza JSON.

Ejemplo:

```
POST /api/auth.php
POST /api/ordenes.php
GET  /api/ordenes.php
POST /api/activos.php
GET  /api/activos.php
```
Las respuestas deben ser consistentes:

Éxito:

```
{
  "ok": true,
  "data": {}
}
```
Error:

```
{
  "ok": false,
  "error": "Descripción del error"
}
```

---

# 22. PDO
Toda comunicación con MariaDB utilizará PDO.

Nunca:

```
$sql = "SELECT * FROM users WHERE email = '$email'";
```
Siempre:

```
$stmt = $pdo->prepare(
    "SELECT * FROM users WHERE email = ?"
);

$stmt->execute([$email]);
```

---

# 23. CSRF
Todas las operaciones autenticadas que modifiquen información deben tener protección CSRF cuando corresponda.

Implementar:

```
core/security.php
```
con:

```
generateCsrfToken()
validateCsrfToken()
```

---

# 24. VALIDACIÓN
Nunca confiar en JavaScript para validar seguridad.

El navegador puede validar para mejorar UX.

PHP debe validar nuevamente.

Ejemplo:

```
HTML
 ↓
JS valida
 ↓
API PHP valida nuevamente
 ↓
MariaDB
```

---

# 25. UPLOADS
Las cargas de archivos deben validar:

- MIME.
- Extensión.
- Tamaño.
- Nombre.
- Usuario.
- Permiso.
- Entidad destino.
Nunca utilizar directamente el nombre enviado por el usuario.

Generar nombre interno:

```
UUID.ext
```

---

# 26. QR PÚBLICO
Un QR público puede abrir:

```
/qr/?id=UUID
```
El UUID no debe revelar:

- ID interno.
- Usuario.
- Base de datos.
- información sensible.
La pantalla pública solamente permite reportar.

Nunca debe otorgar acceso administrativo.

---

# 27. PLANTILLAS JSON
El sistema debe permitir personalización sin modificar PHP.

Ejemplo:

```
{
  "rubro": "autoelevadores",
  "nombre_visible": "Alquiler y Mantenimiento de Autoelevadores",
  "labels": {
    "assets": "Autoelevadores",
    "locations": "Plantas / Talleres",
    "work_orders": "Servicios"
  },
  "asset_types": [],
  "categories": [],
  "qr_menus": [],
  "modules": []
}
```
Las plantillas se almacenarán en:

```
templates/rubros/
```

---

# 28. CONFIGURACIÓN
La configuración de cada instalación podrá almacenarse como JSON.

Ejemplo:

```
organization.config
```
Esto permite modificar:

- Nombre.
- Logo.
- colores.
- etiquetas.
- preferencias.
- módulos.
sin modificar código.

---

# 29. REGLA DEL JEFE
El menú:

```
⋯
```
es exclusivo de supervisión.

Acciones:

```
Ver
Reasignar
Cambiar prioridad
Mover estado
Cancelar
```

---

# 30. BOTÓN +
El botón:

```
+
```
permite:

### Jefe
Crear una orden.

### Operario
Crear un reporte rápido.

La acción debe ser inmediata.

---

# 31. REGLAS UX
La aplicación debe seguir:

1. Máximo 3 pasos para una acción habitual.
2. Respuesta visual inmediata.
3. Interfaz limpia.
4. Pocas opciones visibles.
5. Revelación progresiva.
6. Confirmación para acciones destructivas.
7. Diseño responsive.
8. Prioridad absoluta al uso móvil.

---

# 32. PRINCIPIO FUNDAMENTAL PARA LA IA DESARROLLADORA
La IA debe trabajar por fases.

Nunca debe:

- Reescribir todo el proyecto.
- Crear múltiples sistemas paralelos.
- Introducir Laravel.
- Introducir otro framework.
- Cambiar el modelo de datos sin autorización.
- Crear abstracciones innecesarias.
Antes de modificar una pieza importante debe comprender la estructura existente.

---

# 33. ORDEN DE IMPLEMENTACIÓN
El desarrollo se realizará en este orden:

```
FASE 0
Preparación

FASE 1
Base de datos

FASE 2
Core PHP

FASE 3
Autenticación

FASE 4
API

FASE 5
Usuarios y personas

FASE 6
Clientes y ubicaciones

FASE 7
Activos

FASE 8
Órdenes

FASE 9
Dashboard

FASE 10
QR

FASE 11
Archivos y evidencias

FASE 12
Plantillas

FASE 13
Importación

FASE 14
Notificaciones

FASE 15
Instalador

FASE 16
Seguridad

FASE 17
Testing

FASE 18
Producción
```

---

# 34. REGLA DE FINALIZACIÓN DE CADA FASE
Una fase solamente puede declararse terminada cuando:

```
✓ código creado
✓ sintaxis PHP correcta
✓ estructura comprobada
✓ base de datos comprobada
✓ permisos comprobados
✓ seguridad comprobada
✓ pruebas realizadas
✓ errores corregidos
```
La IA debe informar:

```
FASE X — COMPLETADA

Archivos creados:
...

Archivos modificados:
...

Pruebas:
...

Resultado:
PASS
```
Nunca debe declarar una fase completa únicamente porque escribió el código.

---

# 35. OBJETIVO FINAL
El resultado debe ser una aplicación:

```
PHP 8.4
+
MariaDB
+
PDO
+
HTML
+
CSS
+
JavaScript
+
JSON
```
sin Laravel.

Debe poder copiarse a un hosting compatible con PHP y configurarse mediante un instalador visual.

La aplicación debe funcionar sin depender de:

- Node.js en producción.
- Docker.
- Laravel.
- Redis.
- servicios externos obligatorios.
Los servicios externos podrán agregarse posteriormente como módulos opcionales.

---

# FIN DE PARTE 1/3

# 🛠️ FIUU! PHP — DOCUMENTO MAESTRO DE IMPLEMENTACIÓN

## Parte 2/3 — Implementación funcional, API, interfaz, QR e importación
**Versión:** 1.0
**Dependencias:** Parte 1/3
**Framework:** Ninguno

---

# 1. FASE 0 — PREPARACIÓN

## Objetivo
Crear una instalación limpia de Fiuu! PHP.

Eliminar toda dependencia de:

- Laravel.
- Blade.
- Artisan.
- Eloquent.
- Middleware Laravel.
- Migraciones Laravel.
- configuración Laravel.
El proyecto debe comenzar limpio.

---

# 2. FASE 1 — BASE DE DATOS
Crear:

```
database/schema.sql
```
Debe crear todas las tablas definidas en Parte 1.

Orden:

```
organizations
people
users
clients
locations
asset_types
assets
work_orders
work_order_comments
attachments
qr_codes
notifications
audit_logs
```
Agregar:

- Primary keys.
- Foreign keys.
- Índices.
- Unique constraints.
- timestamps.
- índices sobre campos consultados frecuentemente.

---

# 3. FASE 2 — CONEXIÓN PDO
Crear:

```
core/database.php
```
Responsabilidades:

- Conectar.
- Configurar PDO.
- Activar exceptions.
- Desactivar emulación de prepares.
- Devolver conexión.
Configuración:

```
config/config.php
```
Nunca almacenar credenciales directamente en archivos públicos.

---

# 4. FASE 3 — BOOTSTRAP
Crear:

```
core/bootstrap.php
```
Debe cargar:

```
config
database
security
auth
helpers
```
Las APIs utilizarán:

```
require_once '../core/bootstrap.php';
```

---

# 5. FASE 4 — AUTENTICACIÓN
Implementar:

```
POST /api/auth.php?action=login
POST /api/auth.php?action=logout
GET  /api/auth.php?action=me
```
Login:

```
email
password
```
Flujo:

```
usuario
 ↓
buscar cuenta
 ↓
password_verify()
 ↓
regenerar sesión
 ↓
guardar user_id
 ↓
respuesta JSON
```
Logout:

```
destruir sesión
```

---

# 6. FASE 5 — USUARIOS
Implementar:

```
GET
POST
PUT
DELETE/desactivar
```
El jefe podrá:

- Crear usuario.
- Activar.
- Desactivar.
- Cambiar rol.
- Vincular persona.
No permitir que un operario administre usuarios.

---

# 7. FASE 6 — PERSONAS
API:

```
/api/personas.php
```
Funciones:

```
listar
crear
editar
activar
desactivar
```
Una persona puede existir sin tener cuenta de acceso.

Ejemplo:

```
Carlos Ruiz
Técnico externo
sin login
```

---

# 8. FASE 7 — CLIENTES
API:

```
/api/clientes.php
```
Permitir:

```
listar
crear
editar
ver
activar
desactivar
```
Campos:

```
nombre
razón social
código
contacto
email
teléfono
dirección
estado
```

---

# 9. FASE 8 — UBICACIONES
API:

```
/api/ubicaciones.php
```
Debe soportar jerarquías.

Ejemplo:

```
Colegio
 └── Primaria
     └── Primer Piso
         └── Aula P1-03
```
Cada ubicación puede tener:

```
cliente
tipo
descripción
ubicación padre
QR
```

---

# 10. FASE 9 — ACTIVOS
API:

```
/api/activos.php
```
CRUD:

```
crear
listar
ver
editar
desactivar
```
Campos:

```
nombre
código
marca
modelo
serie
tipo
cliente
ubicación
estado
custom_data
```

---

# 11. CAMPOS DINÁMICOS
El tipo de activo determina campos adicionales.

Ejemplo:

```
{
  "custom_fields": [
    {
      "key": "horometro",
      "label": "Horómetro",
      "type": "number"
    }
  ]
}
```
El activo almacena:

```
{
  "horometro": 4520
}
```
No crear columnas SQL para cada característica de cada rubro.

---

# 12. FASE 10 — ÓRDENES DE TRABAJO
API:

```
/api/ordenes.php
```
Operaciones:

```
crear
listar
ver
editar
asignar
reasignar
cambiar prioridad
cambiar estado
cancelar
finalizar
```

---

# 13. CREACIÓN DE ORDEN
Ejemplo:

```
{
  "title": "Aire acondicionado no funciona",
  "description": "No enciende",
  "priority": "alta",
  "client_id": 2,
  "location_id": 10,
  "asset_id": 43
}
```
PHP debe:

1. Autenticar.
2. Autorizar.
3. Validar.
4. Crear.
5. Registrar auditoría.
6. Devolver JSON.

---

# 14. CÓDIGO DE ORDEN
Generar código legible:

```
OT-000001
OT-000002
OT-000003
```
El código visible no debe reemplazar al ID interno.

---

# 15. ESTADOS
Flujo inicial:

```
ENTRADA
   ↓
PENDIENTE
   ↓
ASIGNADA
   ↓
EN PROCESO
   ↓
FINALIZADA
```
También:

```
CANCELADA
```
Debe controlarse qué roles pueden realizar cada transición.

---

# 16. REASIGNACIÓN
El jefe puede:

```
Orden
 ↓
⋯
 ↓
Reasignar
 ↓
Seleccionar persona
 ↓
Confirmar
```
Registrar en:

```
audit_logs
```

---

# 17. EVIDENCIA FOTOGRÁFICA
El operario puede:

```
abrir orden
 ↓
agregar foto
 ↓
subir
 ↓
guardar referencia
```
La foto se almacena en:

```
storage/uploads/
```
La base de datos almacena:

```
work_order_id
filename
path
mime
size
uploaded_by
```

---

# 18. COMENTARIOS
Cada orden debe permitir comentarios.

Ejemplo:

```
Carlos:
"Se reemplazó capacitor."

Ariel:
"Perfecto."
```
Los comentarios quedan asociados a la orden.

---

# 19. HISTORIAL
La ficha de una orden debe mostrar:

```
Creada
 ↓
Asignada
 ↓
Aceptada
 ↓
En proceso
 ↓
Comentario
 ↓
Foto
 ↓
Finalizada
```
Debe utilizarse:

```
audit_logs
```
y comentarios/evidencias.

---

# 20. DASHBOARD DEL JEFE
Debe mostrar:

```
Urgentes
Pendientes
En proceso
Finalizadas
```
Además:

```
Bandeja de entrada
Equipo
Actividad reciente
```
No saturar la pantalla.

---

# 21. DASHBOARD DEL OPERARIO
Debe mostrar principalmente:

```
MIS TAREAS
```
Cada tarjeta:

```
OT-104
Aire acondicionado
Urgente
Aula 12

[Aceptar]
```
Después:

```
En proceso

[Ver]
```
Dentro:

```
Descripción
Ubicación
Activo
Fotos
Comentarios

[Agregar foto]
[Comentar]
[Finalizar]
```

---

# 22. BOTÓN +
Siempre disponible según rol.

## Jefe

```
+
 ↓
Nueva orden
```

## Operario

```
+
 ↓
Reportar incidente
```

---

# 23. MENÚ ⋯
Solamente supervisión.

Opciones:

```
Ver detalle
Reasignar
Cambiar prioridad
Mover estado
Cancelar
```
Las acciones destructivas requieren confirmación.

---

# 24. INTERFAZ HTML
No utilizar Blade.

La interfaz será:

```
HTML
CSS
JavaScript
```
El JavaScript consume la API:

```
fetch('/api/ordenes.php')
```
Procesa:

```
JSON
```
y actualiza el DOM.

---

# 25. ESTILO VISUAL
Mantener:

```
#F2F2F7
```
como fondo principal.

Tarjetas:

```
blancas
rounded-2xl
sombras sutiles
```
Tipografía:

```
Inter
```
Diseño:

```
limpio
espaciado
móvil primero
```

---

# 26. REGLA DE 3 TOQUES
Las operaciones habituales deben resolverse en aproximadamente tres acciones.

Ejemplo:

```
Abrir
→ aceptar
→ finalizar
```
No agregar formularios innecesarios.

---

# 27. QR
Crear:

```
qr/index.php
```
Ruta:

```
/qr/?id=UUID
```
El sistema:

```
recibe UUID
 ↓
busca QR
 ↓
identifica entidad
 ↓
carga configuración
 ↓
muestra pantalla
```

---

# 28. QR PARA ACTIVOS
Ejemplo:

```
Autoelevador AE-001
```
QR:

```
/qr/?id=UUID
```
Pantalla pública:

```
Autoelevador AE-001

¿Qué ocurre?

🚨 No arranca
💧 Fuga
🔧 Otro problema
```

---

# 29. QR PARA UBICACIONES
Ejemplo:

```
Aula P1-03
```
Pantalla:

```
Aula P1-03

🏫 Reportar problema
💡 Iluminación
❄️ Climatización
🖥️ Tecnología
```

---

# 30. QR PÚBLICO VS USUARIO LOGUEADO

### Usuario no autenticado
Solamente:

```
reportar
```

### Usuario autenticado
Puede mostrar:

```
historial
activo
órdenes
acciones permitidas
```
Nunca exponer información sensible a usuarios públicos.

---

# 31. LOCALSTORAGE
Para QR públicos se puede recordar:

```
nombre
teléfono
ubicación declarada
```
No almacenar:

- contraseñas.
- tokens de sesión.
- información sensible.

---

# 32. PLANTILLAS
Crear inicialmente:

```
colegio.json
consorcio.json
autoelevadores.json
fotocopiadoras.json
```
Cada plantilla define:

```
labels
asset_types
categories
qr_menus
modules
custom_fields
```

---

# 33. EJEMPLO COLEGIO

```
{
  "rubro": "colegio",
  "labels": {
    "assets": "Equipos",
    "locations": "Sectores",
    "work_orders": "Solicitudes"
  },
  "modules": [
    "people",
    "clients",
    "locations",
    "assets",
    "qr",
    "notifications"
  ]
}
```

---

# 34. IMPORTACIÓN CSV
Debe existir un importador sencillo.

Archivos:

```
clientes.csv
ubicaciones.csv
activos.csv
personal.csv
```
Proceso:

```
subir
 ↓
detectar columnas
 ↓
validar
 ↓
mostrar resumen
 ↓
confirmar
 ↓
importar
```
Nunca importar directamente sin mostrar errores.

---

# 35. IMPORTACIÓN EXCEL
Se podrá agregar una librería específica para XLSX.

La aplicación debe convertir conceptualmente:

```
XLSX
 ↓
filas
 ↓
validador
 ↓
importador
 ↓
MariaDB
```

---

# 36. ERRORES DE IMPORTACIÓN
Debe informar:

```
Fila 18
Cliente "Depósito Norte"
Ubicación inexistente
```
No debe abortar silenciosamente.

Idealmente:

```
47 registros correctos
3 registros con errores
```
y permitir corregir los errores.

---

# 37. GENERACIÓN DE QR MASIVA
Después de importar:

```
activo
 ↓
generar UUID
 ↓
crear QR
 ↓
asociar
```
Debe existir posteriormente una utilidad para imprimir:

```
Código
Nombre
QR
```

---

# 38. NOTIFICACIONES
Primera versión:

```
notificaciones internas
```
Ejemplo:

```
🔴 Nueva orden urgente
OT-104
Aire acondicionado
```
El sistema no debe depender inicialmente de WhatsApp.

---

# 39. NOTIFICACIONES DEL NAVEGADOR
Podrán incorporarse posteriormente mediante:

```
Web Push
```
como módulo opcional.

No hacer que el núcleo dependa de esto.

---

# 40. MÓDULOS FUTUROS
La arquitectura debe permitir agregar:

```
Preventivo
Stock
Alquileres
Facturación
Proveedores
```
sin modificar el núcleo innecesariamente.

---

# 41. PREVENTIVO
Futuro:

```
Activo
 ↓
frecuencia
 ↓
fecha próxima
 ↓
generar orden automáticamente
```

---

# 42. STOCK
Futuro:

```
producto
stock
movimiento
orden
consumo
```
Al finalizar una orden:

```
consumo → descuento
```

---

# 43. PROVEEDORES
Futuro:

```
proveedor
contacto
especialidad
órdenes
documentación
```

---

# 44. REGLA DE EXTENSIBILIDAD
Antes de agregar una nueva función, la IA debe responder:

1. ¿Pertenece al núcleo?
2. ¿Es un módulo?
3. ¿Necesita una tabla nueva?
4. ¿Puede resolverse con configuración JSON?
5. ¿Afecta permisos?
6. ¿Afecta API?
7. ¿Afecta interfaz?
8. ¿Necesita auditoría?

---

# 45. FASES FUNCIONALES

## FASE 5
Usuarios y personas.

## FASE 6
Clientes.

## FASE 7
Ubicaciones.

## FASE 8
Activos.

## FASE 9
Órdenes.

## FASE 10
Dashboard.

## FASE 11
QR.

## FASE 12
Archivos.

## FASE 13
Plantillas.

## FASE 14
Importación.

## FASE 15
Notificaciones.

Cada fase debe probarse antes de comenzar la siguiente.

---

# 46. RESULTADO FUNCIONAL MÍNIMO
Al terminar el núcleo debe ser posible:

```
Instalar
 ↓
crear jefe
 ↓
crear personas
 ↓
crear usuarios
 ↓
crear clientes
 ↓
crear ubicaciones
 ↓
crear activos
 ↓
generar QR
 ↓
crear orden
 ↓
asignar
 ↓
trabajar
 ↓
subir foto
 ↓
comentar
 ↓
finalizar
 ↓
ver historial
```

---

# 47. CRITERIO DE ÉXITO
Una persona sin conocimientos técnicos debe poder utilizar el sistema diariamente sin comprender:

- PHP.
- MariaDB.
- API.
- JSON.
- servidores.
Todo eso debe quedar oculto detrás de una interfaz sencilla.

---

# FIN DE PARTE 2/3

# 🛠️ FIUU! PHP — DOCUMENTO MAESTRO DE IMPLEMENTACIÓN

## Parte 3/3 — Testing, instalador, cPanel, producción y protocolo de desarrollo
**Versión:** 1.0
**Dependencias:** Parte 1/3 + Parte 2/3
**Framework:** Ninguno

---

# 1. OBJETIVO
Esta parte define cómo convertir el sistema desarrollado en una aplicación lista para entregar e instalar en un hosting real.

---

# 2. FASE 16 — INSTALADOR WEB
Crear:

```
/install/instalar.php
```
El instalador debe ser visual.

No mostrar:

- stack traces.
- errores PDO.
- rutas internas.
- contraseñas.
- información técnica innecesaria.

---

# 3. PASO 1 — REQUISITOS
Mostrar:

```
PHP 8.4       ✓
PDO           ✓
PDO MySQL     ✓
JSON          ✓
Sessions      ✓
File uploads  ✓
Writable storage ✓
```
Cada requisito debe mostrar:

```
✓ Correcto
✕ Falta
```

---

# 4. PASO 2 — BASE DE DATOS
Solicitar:

```
Servidor
Base de datos
Usuario
Contraseña
Puerto
```
Ejemplo cPanel:

```
localhost
fiuu_empresa
fiuu_usuario
********
3306
```
Probar conexión antes de continuar.

---

# 5. PASO 3 — ORGANIZACIÓN
Solicitar:

```
Nombre de organización
Razón social
Logo
Rubro inicial
```
Rubros:

```
Colegio
Consorcio
Autoelevadores
Fotocopiadoras
Otro
```

---

# 6. PASO 4 — JEFE
Solicitar:

```
Nombre
Apellido
Email
Contraseña
Confirmar contraseña
```
Crear:

```
people
users
```
Rol:

```
jefe
```

---

# 7. INSTALACIÓN
Al finalizar:

```
crear tablas
 ↓
crear organización
 ↓
aplicar plantilla
 ↓
crear persona
 ↓
crear usuario
 ↓
crear configuración
 ↓
crear directorios
 ↓
crear installed.lock
```

---

# 8. INSTALLED.LOCK
Crear:

```
storage/installed.lock
```
Si existe:

```
installer bloqueado
```
Nunca permitir reinstalación accidental.

Para reinstalar:

```
eliminar instalación manualmente
```
o mediante procedimiento administrativo explícito.

---

# 9. PROTECCIÓN DEL INSTALADOR
El instalador debe comprobar:

```
installed.lock
```
antes de ejecutar cualquier operación.

---

# 10. FASE 17 — SEGURIDAD FINAL
Revisar:

## SQL Injection
Todo SQL debe utilizar:

```
PDO prepared statements
```

---

# 11. XSS
Todo contenido generado por usuarios debe escaparse antes de renderizarse como HTML.

JavaScript debe utilizar:

```
textContent
```
cuando corresponda.

---

# 12. CSRF
Toda modificación autenticada debe estar protegida.

---

# 13. SESIONES
Revisar:

```
Secure
HttpOnly
SameSite
session_regenerate_id()
```

---

# 14. AUTORIZACIÓN
Probar directamente las APIs.

No confiar en que un botón esté oculto.

Ejemplo:

Aunque el operario no vea:

```
Cancelar
```
si intenta:

```
POST /api/ordenes.php?action=cancel
```
el servidor debe responder:

```
403
```

---

# 15. UPLOADS
Probar:

```
archivo válido
archivo demasiado grande
extensión falsa
MIME incorrecto
archivo ejecutable
nombre malicioso
```
Nunca permitir ejecución de PHP dentro de:

```
storage/uploads/
```

---

# 16. QR
Probar:

```
UUID válido
UUID inexistente
UUID desactivado
UUID manipulado
```
Nunca mostrar información interna.

---

# 17. FASE 18 — TESTING
Crear una suite simple de pruebas.

No es obligatorio utilizar PHPUnit si la dependencia resulta innecesaria para el tamaño del sistema, aunque puede incorporarse como herramienta de desarrollo.

Debe existir como mínimo:

```
tests/
├── auth/
├── api/
├── database/
├── qr/
├── imports/
└── security/
```

---

# 18. TESTS DE AUTENTICACIÓN
Probar:

```
login correcto
login incorrecto
usuario inexistente
usuario desactivado
logout
sesión expirada
```

---

# 19. TESTS DE PERMISOS

### Jefe
Debe poder:

```
crear
editar
asignar
cancelar
```

### Operario
Debe:

```
ver propias
aceptar propias
finalizar propias
```
Debe fallar al intentar:

```
administrar usuarios
cancelar órdenes ajenas
cambiar permisos
```

---

# 20. TESTS DE API
Probar:

```
GET
POST
PUT
DELETE
```
con:

```
datos correctos
datos incompletos
datos inválidos
sin sesión
sesión incorrecta
rol incorrecto
```

---

# 21. TESTS DE BASE DE DATOS
Verificar:

```
foreign keys
unique
nullable
índices
transacciones
integridad
```

---

# 22. TRANSACCIONES
Operaciones que modifiquen varias tablas deben utilizar transacciones.

Ejemplo:

Crear orden + auditoría:

```
BEGIN
 ↓
INSERT orden
 ↓
INSERT auditoría
 ↓
COMMIT
```
Si algo falla:

```
ROLLBACK
```

---

# 23. AUDITORÍA
Probar que queden registrados:

```
crear orden
asignar
reasignar
cambiar prioridad
cancelar
finalizar
crear usuario
modificar permisos
```

---

# 24. TESTS DE QR
Probar:

```
crear QR
resolver QR
desactivar QR
reportar desde QR
```

---

# 25. TESTS DE IMPORTACIÓN
Probar:

```
archivo correcto
columnas faltantes
duplicados
cliente inexistente
ubicación inexistente
campos inválidos
archivo vacío
```
El importador nunca debe corromper datos existentes.

---

# 26. BACKUP
La producción debe permitir:

```
backup MariaDB
backup storage/uploads
backup configuración
```
No depender únicamente del backup del hosting.

---

# 27. CPANEL
La instalación estándar debe ser compatible con cPanel.

Configuración:

```
PHP 8.4
MariaDB
SSL
```

---

# 28. ESTRUCTURA EN CPANEL
Idealmente:

```
/home/usuario/
│
├── fiuu/
│   ├── api/
│   ├── core/
│   ├── config/
│   ├── database/
│   ├── storage/
│   └── templates/
│
└── public_html/
    └── fiuu/
        └── archivos públicos
```
El objetivo es que los archivos sensibles estén fuera de `public_html`.

Si el hosting obliga a colocar todo dentro de `public_html`, proteger mediante:

```
.htaccess
```
o mecanismos equivalentes.

---

# 29. HTTPS
Fiuu! debe ejecutarse exclusivamente bajo:

```
HTTPS
```
No permitir login mediante HTTP sin cifrado.

---

# 30. CONFIGURACIÓN DE PRODUCCIÓN
No almacenar:

```
credenciales
contraseñas
secretos
```
en archivos públicos.

La configuración de producción debe estar fuera del directorio público siempre que el hosting lo permita.

---

# 31. LOGS
Guardar errores técnicos en:

```
storage/logs/
```
Nunca mostrar al usuario:

```
PDOException
SQL
ruta del servidor
credenciales
stack trace
```

---

# 32. ERRORES DE PRODUCCIÓN
El usuario debe recibir:

```
Ocurrió un problema.
Intentá nuevamente.
```
La información técnica debe quedar únicamente en logs.

---

# 33. FASE 19 — OPTIMIZACIÓN
No optimizar prematuramente.

Primero:

```
correctitud
 ↓
seguridad
 ↓
claridad
 ↓
rendimiento
```

---

# 34. CACHÉ
No agregar Redis inicialmente.

Si posteriormente se necesita:

```
cache
colas
procesamiento
```
se evaluará según necesidad real.

---

# 35. CRON
Si posteriormente se implementa mantenimiento preventivo:

```
cron.php
```
podrá ejecutar:

```
órdenes programadas
notificaciones
recordatorios
```
El sistema no debe requerir un servidor de colas para funcionar inicialmente.

---

# 36. FASE 20 — DOCUMENTACIÓN
Crear:

```
README.md
```
Debe explicar:

```
requisitos
instalación
estructura
configuración
base de datos
API
usuarios
roles
plantillas
backup
actualización
```

---

# 37. DOCUMENTACIÓN PARA USUARIO FINAL
No explicar PHP.

Debe explicar:

```
Cómo ingresar
Cómo crear una orden
Cómo asignar
Cómo finalizar
Cómo usar QR
Cómo importar
Cómo administrar personas
```

---

# 38. PROTOCOLO PARA LA IA DESARROLLADORA
La IA que implemente Fiuu! debe seguir estrictamente:

```
1. Leer la fase.
2. Revisar estructura existente.
3. Identificar dependencias.
4. Implementar solamente esa fase.
5. Ejecutar pruebas.
6. Corregir errores.
7. Revisar seguridad.
8. Reportar resultado.
9. Esperar autorización para avanzar.
```
No avanzar automáticamente sobre fases posteriores si la instrucción de trabajo exige ejecución por fases.

---

# 39. PROHIBICIONES PARA LA IA
La IA NO debe:

- Instalar Laravel.
- Introducir React.
- Introducir Vue.
- Introducir Node.js obligatorio.
- Cambiar MariaDB por otra base.
- Exponer MariaDB.
- Crear un framework propio.
- Crear una arquitectura innecesariamente compleja.
- Eliminar funcionalidades existentes sin autorización.
- Modificar reglas de negocio sin autorización.
- Declarar PASS sin haber ejecutado pruebas.

---

# 40. PROTOCOLO DE AUTOVERIFICACIÓN
Después de cada modificación:

```
PHP syntax
 ↓
SQL integrity
 ↓
API test
 ↓
permission test
 ↓
security check
 ↓
UI check
```

---

# 41. CHECKLIST FINAL

## Arquitectura

```
☐ Sin Laravel
☐ PHP 8.4
☐ MariaDB
☐ PDO
☐ HTML
☐ CSS
☐ JavaScript
```

## Seguridad

```
☐ HTTPS
☐ password_hash
☐ password_verify
☐ sesiones seguras
☐ CSRF
☐ SQL preparado
☐ autorización servidor
☐ uploads protegidos
☐ errores ocultos
☐ logs
```

## Funcionalidad

```
☐ usuarios
☐ personas
☐ clientes
☐ ubicaciones
☐ activos
☐ órdenes
☐ estados
☐ prioridades
☐ asignaciones
☐ comentarios
☐ fotos
☐ historial
☐ QR
☐ plantillas
☐ importación
```

## Instalación

```
☐ instalador visual
☐ conexión MariaDB
☐ creación de tablas
☐ organización
☐ jefe
☐ plantilla
☐ installed.lock
```

---

# 42. PRUEBA FINAL DE USO
Una instalación limpia debe permitir realizar:

```
1. Abrir instalador
2. Configurar MariaDB
3. Crear organización
4. Crear jefe
5. Iniciar sesión
6. Crear persona
7. Crear usuario
8. Crear cliente
9. Crear ubicación
10. Crear activo
11. Generar QR
12. Escanear QR
13. Crear reporte
14. Ver orden
15. Asignar técnico
16. Técnico acepta
17. Técnico inicia
18. Técnico agrega foto
19. Técnico comenta
20. Técnico finaliza
21. Jefe ve historial
```
Todo debe funcionar sin acceder directamente a MariaDB desde el navegador.

---

# 43. CRITERIO DE PRODUCCIÓN
Fiuu! puede considerarse listo para producción cuando:

```
✓ instalación limpia funciona
✓ login funciona
✓ roles funcionan
✓ API funciona
✓ CRUD funciona
✓ QR funciona
✓ uploads funcionan
✓ importación funciona
✓ auditoría funciona
✓ seguridad comprobada
✓ backups comprobados
✓ errores controlados
✓ HTTPS activo
✓ pruebas PASS
```

---

# 44. FILOSOFÍA FINAL DEL PROYECTO
Fiuu! debe permanecer pequeño por dentro y poderoso por fuera.

La complejidad debe estar en:

```
la experiencia
```
y no en:

```
la infraestructura.
```
La persona que utiliza Fiuu! no debe saber que existe:

```
PHP
PDO
MariaDB
API
JSON
```
Debe simplemente sentir:

> "Sé exactamente qué tengo que hacer."

---

# 45. ARQUITECTURA FINAL

```
                    FIUU!
                       │
             ┌─────────┴─────────┐
             │                   │
          USUARIO             QR PÚBLICO
             │                   │
             ▼                   ▼
       HTML + JS             HTML + JS
             │                   │
             └─────────┬─────────┘
                       │
                     HTTPS
                       │
                       ▼
                  API PHP
                       │
        ┌──────────────┼──────────────┐
        │              │              │
     Auth          Permisos        Validación
        │              │              │
        └──────────────┼──────────────┘
                       │
                      PDO
                       │
                       ▼
                    MariaDB
                       │
              ┌────────┴────────┐
              │                 │
          Datos             Auditoría
              │
              ▼
          Archivos
```

---

# 46. DEFINICIÓN FINAL DEL PRODUCTO
Fiuu! PHP debe ser:

> **Una aplicación web universal, liviana y segura para organizar operaciones, personas, clientes, ubicaciones, activos y órdenes de trabajo, instalada independientemente para cada organización y configurable mediante plantillas, sin depender de un framework pesado.**
Tecnologías oficiales:

```
PHP 8.4
MariaDB
PDO
HTML5
CSS
JavaScript
JSON
HTTPS
```
Framework:

```
NINGUNO
```

---

# FIN DEL DOCUMENTO MAESTRO FIUU! PHP 1.0

## Estado inicial recomendado

```
FASE 0 — LISTA PARA COMENZAR
```
La IA desarrolladora deberá comenzar exclusivamente por la FASE 0 y avanzar secuencialmente, verificando cada fase antes de continuar.
