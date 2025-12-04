# Documentación de la API Servienvios

## Descripción General

La API Servienvios es un sistema centralizado que permite la integración de sus clientes con los servicios de la empresa. El sistema proporciona endpoints unificados para la generación de guías, cotización de envíos, seguimiento de paquetes y generación de relaciones de despachos.

## Características Principales

-   **Acceso dual**: Aplicación Web y Endpoints Directos
-   **Generación de documentos**: PDFs y etiquetas de envío
-   **Seguimiento unificado**: Consulta de estado de envíos
-   **Relación de despachos**: Generación de reportes PDF con información consolidada de despachos por operador y rango de fechas

## Autenticación

### API Key (Método Principal)

```http
Authorization: <api_key>
```

**Usado en todos los endpoints:**

-   Generación de guías
-   Cotización de envíos
-   Seguimiento de envíos

### Generación de API Key

Para obtener una API Key, utilice la aplicación web de Servienvios ERP en la sección de Cuenta del usuario. La API Key se genera automáticamente y se puede regenerar cuando sea necesario.

## Endpoints Principales

### URLs de los Servicios

-   **Endpoint Principal**: `https://gjxa520zff.execute-api.us-east-1.amazonaws.com/{stage}/proxy`

    -   Desarrollo: `https://gjxa520zff.execute-api.us-east-1.amazonaws.com/dev/proxy`
    -   Producción: `https://gjxa520zff.execute-api.us-east-1.amazonaws.com/prod/proxy`

-   **Servicio de Cotización**: `https://cm1tv5pq4f.execute-api.us-east-1.amazonaws.com/getTarifa-dev`

-   **Servicio de Tracking**: `https://73jt637mn6.execute-api.us-east-1.amazonaws.com/tracking-guias`

-   **Servicio de Relación de Despachos**: `https://uvppnzex63azjb7pte6lndingq0rexbz.lambda-url.us-east-1.on.aws/`

---

## 1. Generación de Guías

### Descripción

Genera una guía de envío basada en las condiciones comerciales pactadas.

### Headers Requeridos

```http
Content-Type: application/json
Authorization: <api_key>
```

### Parámetros de Entrada

| Campo                           | Tipo   | Obligatorio | Descripción                             |
| ------------------------------- | ------ | ----------- | --------------------------------------- |
| `codigo_origen`                 | string | **SÍ**      | Código DANE de la ciudad de origen      |
| `codigo_destino`                | string | **SÍ**      | Código DANE de la ciudad de destino     |
| `nombre_remitente`              | string | **SÍ**      | Nombre del remitente                    |
| `nombre_destinatario`           | string | **SÍ**      | Nombre del destinatario                 |
| `direccion_remitente`           | string | **SÍ**      | Dirección del remitente                 |
| `direccion_destinatario`        | string | **SÍ**      | Dirección del destinatario              |
| `cajas`                         | string | **SÍ**      | Número de cajas                         |
| `kilos`                         | string | **SÍ**      | Peso en kilogramos                      |
| `ciudad_origen`                 | string | **SÍ**      | Nombre de la ciudad de origen           |
| `ciudad_destino`                | string | **SÍ**      | Nombre de la ciudad de destino          |
| `codigo_postal_origen`          | string | **SÍ**      | Código postal de origen                 |
| `codigo_postal_destino`         | string | **SÍ**      | Código postal de destino                |
| `tipo_documento_remitente`      | string | **SÍ**      | Tipo de documento (CC, NIT, PP, CE, TI) |
| `numero_documento_remitente`    | string | **SÍ**      | Número de documento del remitente       |
| `tipo_documento_destinatario`   | string | **SÍ**      | Tipo de documento del destinatario      |
| `numero_documento_destinatario` | string | **SÍ**      | Número de documento del destinatario    |
| `peso_volumen`                  | string | **SÍ**      | Peso volumen en kilogramos              |
| `telefono_remitente`            | string | **SÍ**      | Teléfono del remitente                  |
| `telefono_destinatario`         | string | **SÍ**      | Teléfono del destinatario               |
| `valor_declarado`               | string | **SÍ**      | Valor declarado del envío               |
| `observaciones`                 | string | **NO**      | Observaciones adicionales               |
| `referencia`                    | string | **NO**      | Referencia del envío                    |

**⚠️ IMPORTANTE**: Los valores posibles para los campos `codigo_origen`, `codigo_destino`, `ciudad_origen`, `ciudad_destino`, `codigo_postal_origen`, `codigo_postal_destino` se deben consultar en los archivos anexos a esta documentación.

### Ejemplo de Solicitud

```json
{
    "codigo_origen": "11001000",
    "codigo_destino": "05001000",
    "codigo_postal_origen": "110111",
    "codigo_postal_destino": "050001",
    "ciudad_origen": "BOGOTA",
    "ciudad_destino": "MEDELLIN",
    "nombre_remitente": "Empresa ABC",
    "direccion_remitente": "Calle 123 #45-67",
    "telefono_remitente": "3001234567",
    "tipo_documento_remitente": "NIT",
    "numero_documento_remitente": "900123456",
    "nombre_destinatario": "Juan Pérez",
    "direccion_destinatario": "Carrera 78 #90-12",
    "poblacion_destinatario": "MEDELLIN",
    "telefono_destinatario": "3009876543",
    "tipo_documento_destinatario": "CC",
    "numero_documento_destinatario": "12345678",
    "cajas": "2",
    "kilos": "5.5",
    "peso_volumen": "5.0",
    "valor_declarado": "150000",
    "observaciones": "Manejar con cuidado",
    "referencia": "REF-2025-001"
}
```

### Respuesta Exitosa (Síncrona)

```json
{
    "success": true,
    "message": "Guía generada exitosamente con [Transportista]",
    "remesa": "123456789",
    "pdf": {
        "success": true,
        "url": "https://s3.amazonaws.com/bucket/guia-123456.pdf",
        "s3Key": "solistica/123456789.pdf",
        "size": 245760,
        "error": null
    },
    "sticker": {
        "success": true,
        "url": "https://s3.amazonaws.com/bucket/sticker-123456.pdf",
        "s3Key": "solistica/stickers/123456789_sticker.pdf",
        "size": 128000,
        "error": null
    },
    "guia": {
        "numero_guia": "[CARRIER]-123456789",
        "estado": "Procesado",
        "fecha_generacion": "2024-01-15T10:30:00.000Z",
        "datos_envio": {
            /* datos originales del envío */
        }
    }
}
```

**Nota**: Los campos `pdf` y `sticker` pueden variar según el transportista seleccionado. No todos los transportistas generan ambos documentos.

### Respuesta Asíncrona (Procesamiento en Segundo Plano)

Cuando el procesamiento toma más tiempo del esperado, el sistema retorna un código `202` con un ID de seguimiento:

```json
{
    "success": true,
    "message": "Guía en procesamiento. Use el ID de seguimiento para consultar el estado.",
    "trackingId": "TRK-1705312345-abc123xyz",
    "status": "PROCESSING",
    "estimatedTime": "2-5 minutos"
}
```

Para consultar el estado de una guía en procesamiento, utilice el endpoint de seguimiento con el `trackingId` proporcionado.

### Códigos de Respuesta

-   `200`: Guía generada exitosamente (síncrono)
-   `202`: Guía en procesamiento (asíncrono) - Se proporciona `trackingId` para consultar el estado
-   `400`: Datos de entrada inválidos o Client ID no encontrado
-   `401`: No autorizado (API Key o JWT inválido)
-   `500`: Error interno del servidor o error en el procesamiento del transportista
-   `503`: Timeout del procesamiento

---

## 2. Cotización de Envíos

### Endpoint

```
POST https://cm1tv5pq4f.execute-api.us-east-1.amazonaws.com/getTarifa-dev
```

### Descripción

Obtiene una cotización estimada para un envío sin generar la guía.

### Headers Requeridos

```http
Content-Type: application/json
Authorization: <api_key>
```

### Parámetros de Entrada

| Campo             | Tipo   | Requerido | Descripción                              |
| ----------------- | ------ | --------- | ---------------------------------------- |
| `origen`          | string | Sí        | Código de la ciudad de origen            |
| `destino`         | string | Sí        | Código de la ciudad de destino           |
| `tipo_envio`      | string | No        | Tipo de envío ("paqueteria" por defecto) |
| `alto`            | number | No        | Altura en centímetros                    |
| `ancho`           | number | No        | Ancho en centímetros                     |
| `largo`           | number | No        | Largo en centímetros                     |
| `peso`            | number | No        | Peso en kilogramos                       |
| `unidades`        | number | No        | Número de unidades                       |
| `valor_declarado` | number | No        | Valor declarado del envío                |

### Ejemplo de Solicitud

```json
{
    "origen": "11001000",
    "destino": "05001000",
    "tipo_envio": "paqueteria",
    "alto": 30,
    "ancho": 40,
    "largo": 50,
    "peso": 5.5,
    "unidades": 2,
    "valor_declarado": 150000
}
```

### Respuesta Exitosa

```json
{
    "costo_total": 25000
}
```

### Códigos de Respuesta

-   `200`: Cotización calculada exitosamente
-   `400`: Datos de entrada inválidos o tipo de envío no válido
-   `401`: No autorizado (API Key o JWT inválido)
-   `404`: Ruta no encontrada
-   `500`: Error interno del servidor

---

## 3. Seguimiento de Envíos

### Endpoint

```
GET /api/tracking-guias?tracking_number=<numero_seguimiento>
POST /api/tracking-guias
```

### Descripción

Consulta el estado actual de un envío utilizando el número de seguimiento.

### Headers Requeridos

```http
Content-Type: application/json
Authorization: <api_key>
```

### Parámetros

#### GET

-   `tracking_number` (query parameter): Número de seguimiento

#### POST

```json
{
    "tracking_number": "123456789"
}
```

### Respuesta Exitosa

```json
{
    "carrier": "deprisa",
    "tracking": {
        "numero_guia": "123456789",
        "estado": "En tránsito",
        "ubicacion_actual": "Centro de distribución Bogotá",
        "fecha_ultima_actualizacion": "2024-01-15T14:30:00.000Z",
        "historial": [
            {
                "fecha": "2024-01-15T10:00:00.000Z",
                "estado": "Recibido",
                "ubicacion": "Oficina de origen"
            },
            {
                "fecha": "2024-01-15T14:30:00.000Z",
                "estado": "En tránsito",
                "ubicacion": "Centro de distribución Bogotá"
            }
        ]
    }
}
```

---

## 4. Generación de Relación de Despachos

### Endpoint

```
POST https://uvppnzex63azjb7pte6lndingq0rexbz.lambda-url.us-east-1.on.aws/
```

### Descripción

Genera un documento PDF con la relación de despachos (guías de envío) para un operador específico o todos los operadores, filtrado por rango de fechas. El documento incluye información detallada de cada despacho y se genera en formato PDF con orientación horizontal.

### Headers Requeridos

```http
Content-Type: application/json
Authorization: <api_key>
```

O alternativamente:

```http
Content-Type: application/json
Authorization: Bearer <jwt_token>
```

### Parámetros de Entrada

| Campo          | Tipo   | Requerido | Descripción                                                                                                |
| -------------- | ------ | --------- | ---------------------------------------------------------------------------------------------------------- |
| `operador`     | string | **SÍ**    | Operador de transporte. Valores posibles: `"todos"`, `"deprisa"`, `"solistica"`, `"tcc"`, `"coordinadora"` |
| `fechaInicial` | string | **SÍ**    | Fecha inicial del rango (formato: `YYYY-MM-DD`)                                                            |
| `fechaFinal`   | string | **SÍ**    | Fecha final del rango (formato: `YYYY-MM-DD`)                                                              |
| `environment`  | string | No        | Ambiente del sistema. Valores: `"development"` (por defecto) o `"production"`                              |

### Ejemplo de Solicitud

```json
{
    "operador": "todos",
    "fechaInicial": "2024-01-01",
    "fechaFinal": "2024-01-31",
    "environment": "development"
}
```

### Respuesta Exitosa

```json
{
    "success": true,
    "operador": "todos",
    "tablesQueried": [
        "GuiasCoordinadora",
        "GuiasDeprisa",
        "GuiasSolistica",
        "GuiasTcc"
    ],
    "tableResults": {
        "GuiasCoordinadora": 15,
        "GuiasDeprisa": 23,
        "GuiasSolistica": 8,
        "GuiasTcc": 12
    },
    "count": 58,
    "items": [
        {
            "id": "123456789",
            "nombre_remitente": "Empresa ABC",
            "nombre_destinatario": "Juan Pérez",
            "direccion_destinatario": "Calle 123 #45-67",
            "cajas": "2",
            "kilos": 5.5,
            "referencia": "REF-2024-001",
            "observaciones": "Manejar con cuidado",
            "valor_declarado": 150000,
            "fecha_creacion": "2024-01-15T10:30:00.000Z"
        }
    ],
    "base64": "JVBERi0xLjQKJeLjz9MKMy..."
}
```

**Nota**: El campo `base64` contiene el PDF generado codificado en Base64. Para decodificarlo y guardarlo como archivo PDF, decodifique el string Base64 y guarde el resultado binario con extensión `.pdf`.

### Estructura del PDF Generado

El PDF generado incluye:

-   **Encabezado**: Logo de la empresa, título "RELACIÓN DE DESPACHOS", información del operador, rango de fechas, total de despachos y número de página
-   **Tabla de Despachos**: Columnas con la siguiente información:
    -   ID/Guía
    -   Remitente
    -   Destinatario
    -   Dirección Destinatario
    -   Cajas
    -   Kilos
    -   Referencia
    -   Observaciones
    -   Valor Declarado
-   **Sección de Firmas**: Al final de cada página, se incluyen secciones para "ENTREGADO POR" y "RECIBIDO POR" con espacios para firma, nombre, número de ID y fecha

**Características del PDF**:

-   Orientación horizontal (landscape)
-   Tamaño de página: Letter
-   Márgenes: 1cm
-   Los despachos se agrupan por operador, con cada operador en páginas separadas
-   Paginación automática cuando hay muchos despachos

### Códigos de Respuesta

-   `200`: Relación de despachos generada exitosamente
-   `400`: Parámetros faltantes o inválidos (operador, fechaInicial, fechaFinal)
-   `401`: No autorizado (API Key o JWT inválido, o client_id no encontrado)
-   `500`: Error interno del servidor

### Notas Importantes

1. **Filtrado por Fechas**: El sistema busca despachos cuya `fecha_creacion` esté dentro del rango especificado (incluyendo las horas completas del día inicial y final).

2. **Operador "todos"**: Cuando se especifica `operador: "todos"`, el sistema consulta todas las tablas de guías (Coordinadora, Deprisa, Solistica, TCC) y agrupa los resultados por operador en el PDF.

3. **Estructura de Datos por Operador**: Cada operador almacena los datos de manera diferente:

    - **Coordinadora**: Usa `codigo_remision` como ID, `detalle` array para cajas y kilos
    - **Deprisa**: Usa `numero_envio` como ID
    - **Solistica**: Usa `remesa` como ID, `division` como remitente
    - **TCC**: Usa `remesa` como ID, datos en `datos_originales.despacho`

4. **Ambiente**: El parámetro `environment` determina qué tablas de DynamoDB se consultan. Asegúrese de usar el ambiente correcto según su entorno de trabajo.

---

## Mejores Prácticas

### 1. **CRÍTICO: Envío de Campos Completos**

-   **SIEMPRE** envíe todos los campos listados en la tabla de parámetros
-   **NUNCA** envíe solicitudes con campos faltantes
-   El proxy no puede determinar qué operador será seleccionado sin procesar la solicitud completa
-   Los campos faltantes causarán errores cuando se seleccione un operador que los requiera

### 2. Manejo de Errores

-   Siempre verificar el campo `success` en las respuestas
-   Para respuestas con código `202`, guardar el `trackingId` y consultar el estado periódicamente
-   Implementar reintentos para errores temporales
-   Verificar los campos `pdf.success` y `sticker.success` para confirmar la generación de documentos

### 4. Validación de Datos

-   Validar códigos DANE antes de enviar
-   Verificar formatos de teléfono y documentos
-   Sanitizar datos de entrada (el sistema remueve caracteres HTML potenciales)
-   Validar que todos los campos requeridos estén presentes

### 5. Seguridad

-   Nunca exponer API keys en el frontend
-   Usar HTTPS en todas las comunicaciones
-   Implementar rate limiting
-   Rotar API keys regularmente
-   El sistema soporta autenticación mediante API Key o JWT Bearer token

### 6. Monitoreo y Logging

-   Implementar logging estructurado en el cliente
-   Monitorear métricas de rendimiento
-   Configurar alertas para errores frecuentes
-   Revisar logs regularmente para optimizaciones
-   Para guías asíncronas, implementar polling del estado usando el `trackingId`

---

## Ejemplos de Integración

### JavaScript/Node.js

```javascript
// Generar guía
const response = await fetch(
    'https://gjxa520zff.execute-api.us-east-1.amazonaws.com/dev/proxy',
    {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            Authorization: apiKey,
        },
        body: JSON.stringify(guiaData),
    }
);

const result = await response.json();

if (response.status === 202) {
    // Procesamiento asíncrono
    console.log('Guía en procesamiento. Tracking ID:', result.trackingId);
    // Implementar polling para consultar el estado
} else if (result.success) {
    console.log('Guía generada:', result.remesa);
    if (result.pdf?.success) {
        console.log('PDF disponible:', result.pdf.url);
    }
    if (result.sticker?.success) {
        console.log('Sticker disponible:', result.sticker.url);
    }
} else {
    console.error('Error:', result.error);
}
```

### Python

```python
import requests

# Generar guía
headers = {
    'Content-Type': 'application/json',
    'Authorization': api_key
}

response = requests.post(
    'https://gjxa520zff.execute-api.us-east-1.amazonaws.com/dev/proxy',
    headers=headers,
    json=guia_data
)

result = response.json()

if response.status_code == 202:
    # Procesamiento asíncrono
    print(f"Guía en procesamiento. Tracking ID: {result['trackingId']}")
    # Implementar polling para consultar el estado
elif result['success']:
    print(f"Guía generada: {result['remesa']}")
    if result.get('pdf', {}).get('success'):
        print(f"PDF disponible: {result['pdf']['url']}")
    if result.get('sticker', {}).get('success'):
        print(f"Sticker disponible: {result['sticker']['url']}")
else:
    print(f"Error: {result['error']}")
```

### Generar Relación de Despachos

#### JavaScript/Node.js

```javascript
// Generar relación de despachos
const response = await fetch(
    'https://uvppnzex63azjb7pte6lndingq0rexbz.lambda-url.us-east-1.on.aws/',
    {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            Authorization: apiKey, // o 'Bearer ' + jwtToken
        },
        body: JSON.stringify({
            operador: 'todos',
            fechaInicial: '2024-01-01',
            fechaFinal: '2024-01-31',
            environment: 'development',
        }),
    }
);

const result = await response.json();

if (result.success) {
    console.log(`Total de despachos: ${result.count}`);
    console.log(`Operador: ${result.operador}`);

    // Decodificar y guardar PDF
    if (result.base64) {
        const pdfBuffer = Buffer.from(result.base64, 'base64');
        const fs = require('fs');
        fs.writeFileSync(
            `relacion-despachos-${result.operador}-${
                new Date().toISOString().split('T')[0]
            }.pdf`,
            pdfBuffer
        );
        console.log('PDF guardado exitosamente');
    }
} else {
    console.error('Error:', result.error);
}
```

#### Python

```python
import requests
import base64

# Generar relación de despachos
headers = {
    'Content-Type': 'application/json',
    'Authorization': api_key  # o f'Bearer {jwt_token}'
}

payload = {
    'operador': 'todos',
    'fechaInicial': '2024-01-01',
    'fechaFinal': '2024-01-31',
    'environment': 'development'
}

response = requests.post(
    'https://uvppnzex63azjb7pte6lndingq0rexbz.lambda-url.us-east-1.on.aws/',
    headers=headers,
    json=payload
)

result = response.json()

if result.get('success'):
    print(f"Total de despachos: {result['count']}")
    print(f"Operador: {result['operador']}")

    # Decodificar y guardar PDF
    if result.get('base64'):
        pdf_data = base64.b64decode(result['base64'])
        filename = f"relacion-despachos-{result['operador']}-{result['fechaInicial']}.pdf"
        with open(filename, 'wb') as f:
            f.write(pdf_data)
        print(f'PDF guardado como: {filename}')
else:
    print(f"Error: {result.get('error', 'Error desconocido')}")
```

En los documentos adjuntos se incluye una colección de Postman que permite realizar pruebas de los endpoints expuestos por la aplicación.

## Soporte y Contacto

Para soporte técnico o consultas sobre la API, contactar al equipo de desarrollo de Servienvios desarrollo@servienvios.co.

**Versión de la API**: 1.0  
**Última actualización**: Septiembre 2025
