# CONVERT D7

Generador de archivos BIN para displays de 7 segmentos.

Este repositorio convierte texto a patrones de segmentos y crea un archivo binario de 32768 bytes (32 KB), útil para EEPROM o pruebas de circuito.

## Que hace este proyecto

- Lee una tabla de caracteres en [tabla7seg.json](tabla7seg.json).
- Convierte cada caracter a su byte de 7 segmentos.
- Soporta modo:
  - CC (Common Cathode)
  - CA (Common Anode, invierte bits)
- Aplica espejo de bits (bus invertido) antes de guardar.
- Genera un archivo .bin.
- Si no escribes nombre de salida, usa el texto como nombre del archivo.
- Rellena con ceros hasta 32768 bytes.

## Estructura del repositorio

- [main,py](main,py): Script principal de conversion.
- [tabla7seg.json](tabla7seg.json): Mapa de caracteres a binario de 8 bits.
- [README.md](README.md): Esta documentacion.

## Requisitos

- Python 3.8 o superior (recomendado: 3.10+).
- No requiere librerias externas.

## Como ejecutar

### Opcion 1: Interactivo

En la carpeta del proyecto:

```powershell
python "main,py"
```

El script te pedira:

1. Modo: CC o CA
2. Texto
3. Nombre del archivo .bin (opcional)

Si dejas vacio el nombre, usa el texto como nombre del archivo.

### Opcion 2: Texto por argumento

```powershell
python "main,py" "HOLA 123"
```

Luego te pedira:

1. Modo: CC o CA
2. Nombre del archivo .bin (opcional)

## Ejemplo rapido

Entrada:

- Modo: CC
- Texto: HOLA 123
- Nombre de salida: (vacio)

Salida esperada:

- Archivo: HOLA_123.bin
- Tamano: 32768 bytes
- Consola: preview de los primeros bytes

## Tabla de segmentos

El archivo [tabla7seg.json](tabla7seg.json) define los patrones por caracter.

Metadatos actuales:

- bit_order: dp g f e d c b a
- Los valores del mapa estan en binario de 8 bits (MSB -> LSB)

Ejemplo del mapa:

- "0": "00111111"
- "A": "01110111"
- "-": "01000000"

## Logica aplicada por el script

Por cada caracter del texto:

1. Busca el caracter exacto en la tabla.
2. Si no existe, intenta con mayuscula.
3. Si sigue sin existir, usa 0 y muestra aviso.
4. Convierte string binario a entero.
5. Si modo = CA, invierte bits.
6. Aplica espejo de bits (reverse de los 8 bits).
7. Agrega el byte al buffer.

Al final:

- Completa con 0x00 hasta 32768 bytes.
- Escribe el archivo BIN.

## Personalizacion

### 1) Agregar nuevos caracteres

Edita [tabla7seg.json](tabla7seg.json), seccion map:

```json
"@": "01111110"
```

Usa siempre 8 bits en texto binario.

### 2) Cambiar texto por defecto

En [main,py](main,py), variable:

- TEXTO_PRUEBA

### 3) Cambiar nombre de salida por defecto

Si quieres otro comportamiento, edita la funcion nombre_archivo_valido en [main,py](main,py).

## Notas de hardware

- El script ya aplica inversion para CA y espejo de bits por bus invertido.
- Si tu cableado no necesita espejo, comenta o elimina esta linea en [main,py](main,py):

```python
val = int('{:08b}'.format(val)[::-1], 2)
```

- Si ves caracteres "raros" en el display, primero revisa:
  - tipo de display (CC/CA)
  - orden fisico de pines
  - necesidad real de espejo

## Problemas comunes

1. El script no encuentra caracteres:
- Verifica que esten en [tabla7seg.json](tabla7seg.json).

2. El nombre de archivo falla en Windows:
- El script limpia caracteres invalidos automaticamente.

3. El output no coincide con lo esperado:
- Revisa modo CC/CA y la inversion de bits (espejo).

## Licencia

Actualmente este repositorio no incluye archivo LICENSE.

Si quieres publicarlo como open source, agrega una licencia (por ejemplo MIT).
