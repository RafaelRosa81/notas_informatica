1️⃣ La idea clave (en una frase)
raster_compare NO es un paquete instalado en Python.

Es solo una carpeta con código dentro de tu proyecto.
Eso es normal, no es un error, y no tiene nada que ver con requirements.txt.

2️⃣ Qué significa “paquete instalado” vs “paquete local”
🔹 Paquete instalado (ej. numpy, rasterio)

Cuando hacés:
```bash
conda install numpy
```

Python guarda numpy en un lugar como:
```bash
.../envs/spatial_analysis/Lib/site-packages/numpy/
```

Ese lugar está siempre en el “mapa” que Python usa para buscar imports.
Por eso esto siempre funciona:
```bash
import numpy
```

¿Dónde entran los “paquetes instalados”?

Los paquetes de requirements.txt viven en otro lugar:
```bash
envs/spatial_analysis/
└─ Lib/site-packages/
   ├─ numpy/
   ├─ rasterio/
   ├─ pandas/
   └─ ...
``` 
sin importar desde dónde ejecutes el script.

🔹 Paquete local (tu caso: raster_compare)
Tu estructura es algo así:
```bash
spatial_analysis/        ← RAÍZ DEL PROYECTO
│
├─ raster_compare/       ← TU CÓDIGO (NO instalado)
│  ├─ __init__.py
│  ├─ core.py
│  └─ report.py
│
├─ scripts/
│  ├─ compare_rasters.py
│  └─ run_from_config.py
│
└─ config/
   └─ example_config.yml
```

👉 raster_compare no está instalado en el entorno
👉 Solo existe como carpeta dentro del proyecto

Python NO sabe automáticamente que esa carpeta existe.

3️⃣ Entonces… ¿cómo decide Python qué se puede importar?
Python usa una lista llamada sys.path.

Podés verla así:
```bash
import sys
print(sys.path)
```

Esa lista contiene:
el directorio desde donde ejecutaste Python
el directorio del script
site-packages (donde están los paquetes instalados)

4️⃣ El punto clave: desde dónde ejecutás el script
❌ Caso que falla (el que te pasó)
```bash
python scripts/run_from_config.py
```

En este caso, Python piensa:
“Mi mundo empieza en scripts/”

Entonces busca:
```bash
scripts/raster_compare/   ❌ (no existe)
```

Por eso falla:
```bash
from raster_compare.core import ...
```

👉 No es que el import esté mal
👉 Es que Python no ve el proyecto completo

✅ Caso que funciona (el que probaste)
```bash
python -m scripts.run_from_config
```

Acá le decís a Python:
“Este proyecto es un módulo, empezá a buscar desde la raíz”

Entonces Python ve:
```bash
spatial_analysis/
├─ raster_compare/   ✅
├─ scripts/
```

Y el import funciona.

5️⃣ Entonces… ¿qué es un “módulo” en Python?
En Python:

Archivo .py → módulo
Carpeta con __init__.py → paquete
Proyecto ejecutado con -m → módulo raíz

Cuando hacés:
```bash
python -m scripts.run_from_config
```

Le estás diciendo a Python:

“Tratame esta carpeta como un paquete completo”
Eso NO instala nada, solo cambia el punto de vista del intérprete.

6️⃣ ¿Por qué NO se soluciona solo “corrigiendo el import”?
Probemos tu idea:
```bash
from ..raster_compare.core import align_to_reference
```

❌ Esto no funciona en scripts ejecutados directamente
Porque:
imports relativos (..) solo funcionan dentro de paquetes
run_from_config.py no está siendo tratado como parte de un paquete si lo ejecutás directamente
Es una limitación del lenguaje, no una elección nuestra.

7️⃣ Entonces, ¿qué opciones reales existen?
Hay solo 3 soluciones correctas en Python.

✅ Opción 1 — Ejecutar como módulo (la más simple)
NO instala nada. NO toca imports.
```bash
python -m scripts.run_from_config --config config/example_config.yml
```

✔ estándar
✔ limpio
✔ recomendado para proyectos científicos

✅ Opción 2 — Instalar el proyecto como paquete (editable)
Esto sí “instala” raster_compare, pero solo una vez.
```bash
pip install -e .
```

Ahora Python sabe que:
raster_compare ∈ site-packages
Y entonces esto funciona desde cualquier lado:
```bash
python scripts/run_from_config.py
```

✔ bueno si querés comportamiento “tipo comando”
✔ común en proyectos grandes

❌ Opción 3 — Modificar sys.path
```bash
import sys
sys.path.append("..")
```

❌ funciona
❌ pero es frágil
❌ no profesional
❌ no reproducible

No la recomiendo.

8️⃣ Resumen conceptual (muy corto)
requirements.txt → dependencias externas
raster_compare → código del proyecto
Python no instala tu propio código automáticamente

Ejecutar con -m no instala, solo cambia el contexto

Nada está “mal” en tu proyecto

9️⃣ Recomendación honesta (sin imponer)
Para tu caso (análisis GIS, scripts, QGIS):

👉 Usá siempre:
```bash
python -m scripts.run_from_config --config config/example_config.yml
```

9️⃣ Conclusiones
Tres soluciones posibles (solo una necesitás)
SOLUCIÓN A  Ejecutar como módulo (-m)   ← la que ya usaste
SOLUCIÓN B  Instalar el proyecto (pip -e .)
SOLUCIÓN C  Hackear sys.path (NO recomendada)

✅ Regla mental simple (para siempre)

Si el código es tuyo y está dentro del proyecto → usar python -m
Si el código es de terceros → se instala (pip / conda)
