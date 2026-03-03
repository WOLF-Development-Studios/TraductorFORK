# Modelo dominio

## Estado
Borrador

## Owner
T2 — Bruno (Tech Lead / Sistemas)

## Objetivo
Definir formalmente el modelo de dominio del sistema SaaS de traducción asistida por IA, especificando:

- Entidades del sistema
- Separación estricta entre capa Nube y capa Local
- Clasificación de retención de datos (P0, P1, P2)
- Relaciones entre entidades
- Restricciones arquitectónicas clave
- Supuestos técnicos relevantes para implementación

Este documento sirve como referencia para backend, cliente local y capa de seguridad.

---

# 1. Convenciones

## 1.1 Clasificación de almacenamiento

- `<<Nube>>` → Persistido en backend.
- `<<Local>>` → Persistido exclusivamente en cliente.
- `<<NubeYLocal>>` → Metadata en nube + datos sensibles en local.

## 1.2 Clasificación de retención

- `<<P0>>` → Metadato indispensable.
- `<<P1>>` → Sensible (debe cifrarse o mantenerse local).
- `<<P2>>` → Prohibido almacenar en servidor.

---

# 2. Entidades

---

# 2.1 Capa Nube (Orquestación y Control)

---

## Workspace `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| name | string | P0 |
| created_at | Date | P0 |
| tipo_de_workspace | enum | P0 |

Responsabilidad:  
Contenedor lógico multi-tenant. No almacena contenido de documentos.

---

## Usuario `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| workspace_id | uuid | P0 |
| creado_en | Date | P0 |
| ultimo_login | Date | P0 |
| password_hash | string | P1 |
| email | string | P1 |

Notas:
- `password_hash` debe usar Argon2id o bcrypt.
- Nunca almacenar contraseña en claro.

---

## Device `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| workspace_id | uuid | P0 |
| created_at | Date | P0 |
| llave_publica | string | P1 |
| etiqueta_dispositivo | string | P1 |

Responsabilidad:
- Asociación de dispositivo para control de acceso.
- Base para cifrado por dispositivo.
- Posible revocación de acceso futuro.

---

## Plan `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | char | P0 |
| description | char | P0 |
| costo | float | P0 |

---

## Suscripción `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | char | P0 |
| id_plan | char | P0 |
| fecha_de_compra | Date | P0 |
| fecha_limite | Date | P0 |
| estado | enum(active, inactive) | P0 |
| permiso_de_usar_servicios | bool | P0 |

---

## RegistrosDeUso `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| id_user | uuid | P0 |
| id_plan | char | P0 |
| uso_actual | float | P0 |

Responsabilidad:  
Registro agregado de consumo (tokens, llamadas, etc.).  
No almacena texto procesado.

---

## AuditEvento `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| workspace_id | uuid | P0 |
| object_type | string | P0 |
| object_id | uuid | P0 |
| event_type | enum(subir, exportar, eliminar, traducir, etc.) | P0 |
| modificado_en | Date | P0 |

Nota:  
Nunca almacenar contenido del documento en auditoría.

---

## Proyecto `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| workspace_id | uuid | P0 |
| name | string | P0 |
| etiqueta | string | P0 |
| creado_en | Date | P0 |
| configuracion_json | text | P0 |

---

## ModeloIA `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| proveedor | string | P0 |
| nombre | string | P0 |
| version | string | P0 |
| capacidades | enum | P0 |
| estado | enum(activo, desuso) | P0 |
| creado_en | Date | P0 |

Responsabilidad:  
Catálogo de modelos disponibles.

---

## ModelConfig `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| model_id | uuid | P0 |
| scope | enum(workspace, proyecto, usuario) | P0 |
| parametros_json | text | P1 |

Responsabilidad:  
Configura comportamiento del modelo según alcance.

---

## TrabajoDeTraducción `<<Nube>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| document_id | uuid | P0 |
| segment_id | uuid? | P0 |
| configuracion_id | uuid? | P0 |
| estado | enum(en cola, ejecutándose, terminado, fallido) | P0 |
| creado_en | Date | P0 |

Responsabilidad:  
Orquestación de ejecución del modelo.  
No almacena texto ni prompts.

---

# 2.2 Capa Híbrida (Metadata en nube + contenido local)

---

## Documento `<<NubeYLocal>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| proyecto_id | uuid | P0 |
| status | enum(boceto, en progreso, completado) | P0 |
| creado_en | Date | P0 |
| idioma_original | string | P0 |
| idioma_final | string | P0 |
| titulo | string | P1 |

---

## DocumentVersion `<<NubeYLocal>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| document_id | uuid | P0 |
| kind | enum(original, traducido) | P0 |
| version_no | int | P0 |
| archivo_local_id | uuid | P0 |
| creado_en | Date | P0 |
| version_hash | string | P1 |

Notas:
- `version_hash` es hash lógico de la versión.
- Puede diferir del hash físico del archivo.

---

## Segmento `<<NubeYLocal>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| documento_id | uuid | P0 |
| idx | int | P0 |
| status | enum(pendiente, traducido) | P0 |
| hash_fuente | string | P1 |
| hash_salida | string | P1 |

Nunca se almacena texto del segmento en nube.

---

## Glosario `<<NubeYLocal>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| workspace_id | uuid | P0 |
| proyecto_id | uuid | P0 |
| creado_en | Date | P0 |
| etiqueta | string | P0 |
| ultimo_cambio | Date | P0 |
| nombre | string | P1 |

---

# 2.3 Capa Local (Contenido sensible)

---

## ArchivoLocal `<<Local>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P0 |
| documento_id | uuid | P0 |
| dispositivo_id | uuid | P0 |
| size_bytes | long | P0 |
| formato_original | string | P0 |
| creado_en | Date | P0 |
| identificador_hash | string | P1 |
| ruta | string | P2 |

---

## IndiceDeContextoDelDocumento `<<Local>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| document_id | uuid | P0 |
| chunking_config_hash | string | P0 |
| creado_en | Date | P0 |
| modificado_en | Date | P0 |
| encriptado | bool | P0 |
| index_version | int | P2 |
| embedding_model | string | P2 |
| storage_ref | string | P2 |

Embeddings nunca salen del entorno local.

---

## Termino `<<Local>>`

| Campo | Tipo | Retención |
|--------|------|------------|
| id | uuid | P2 |
| termino_fuente | string | P2 |
| termino_final | string | P2 |
| notas | string | P2 |

---

# 3. Relaciones Principales

- Workspace 1 — N Usuario  
- Workspace 1 — N Proyecto  
- Workspace 1 — N Device  
- Workspace 1 — N AuditEvento  

- Proyecto 1 — N Documento  

- Documento 1 — N DocumentVersion  
- Documento 1 — N Segmento  
- Documento 1 — 0..1 IndiceDeContextoDelDocumento  
- Documento 1 — N TrabajoDeTraducción  

- DocumentVersion 1 — 1 ArchivoLocal (opcional si materializado)  

- Glosario 1 — N Termino  

- TrabajoDeTraducción N — 1 ModelConfig  
- ModelConfig N — 1 ModeloIA  

---

# 4. Restricciones Arquitectónicas Obligatorias

1. Ningún texto fuente o traducido se almacena en servidor.
2. Ningún embedding o índice vectorial se almacena en servidor.
3. No se registran prompts completos en logs.
4. AuditEvento solo guarda metadatos.
5. version_hash debe calcularse sobre contenido normalizado.
6. password_hash debe usar algoritmo resistente a GPU (Argon2id recomendado).

---

# 5. Decisiones Clave

- Separación estricta entre control (nube) y contenido (local).
- ModeloIA desacoplado de configuración.
- Versionado lógico independiente de materialización física.
- RAG completamente local.
- Segmentación persistente sin almacenar texto.
## Referencias
-
