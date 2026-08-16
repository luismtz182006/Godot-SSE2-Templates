# godot-sse2-templates

![Build Status](https://github.com/USUARIO/godot-sse2-templates/actions/workflows/build-templates.yml/badge.svg)

Godot Windows (x86_64) export templates, recompiladas **sin** el
requisito de SSE4.2 que el motor exige desde la versión 4.5. Pensado
para quien tiene una CPU anterior a ~2011 y no puede jugar/exportar
proyectos hechos en Godot 4.5 o más nuevo.

## El problema

Desde **Godot 4.5.0**, los templates oficiales de Windows x86_64 se
compilan exigiendo soporte de **SSE4.2** en el CPU. Si tu procesador
no lo tiene, cualquier juego o proyecto exportado en una de estas
versiones simplemente no abre — muestra:

> A CPU with SSE4.2 instruction set support is required.

y se cierra. Esto afecta CPUs anteriores a Intel Nehalem / AMD
Bulldozer (~2008-2011) — por ejemplo toda la línea AMD Bobcat/Brazos
(E-350, E-240, C-50...) y equivalentes de Intel de esa época.

Es un requisito deliberado de los desarrolladores de Godot (agregado
por rendimiento), no un bug — hay una
[discusión abierta en su GitHub](https://github.com/godotengine/godot/discussions)
pidiendo una alternativa oficial para hardware viejo. Mientras eso no
exista, este repo compila esa alternativa por su cuenta.

## Versiones afectadas

Confirmado revisando directamente el código fuente de cada versión:

| Versión         | ¿Requiere SSE4.2? |
|-----------------|:------------------:|
| 4.4.1 y anteriores | No — corren normal en cualquier CPU |
| 4.5, 4.5.1, 4.5.2 | **Sí** |
| 4.6, 4.6.1, 4.6.2, 4.6.3 | **Sí** |
| 4.7, 4.7.1 | **Sí** |

Este repo compila una versión parcheada para cada una de las
versiones marcadas como "Sí" en la tabla.

> El requisito solo aplica a **Windows x86_64**. Los templates de
> 32 bits nunca lo tuvieron, y Linux/macOS no se ven afectados.

## Cómo conseguir los templates

1. Ve a la pestaña **[Releases](../../releases)** de este repo.
2. Descarga el `.exe` que corresponda a la versión de Godot con la
   que se exportó tu proyecto (el nombre del archivo incluye la
   versión, ej. `godot-4.6.2-stable-sse2-windows-x86_64.exe`).

Si necesitas una versión que todavía no está en Releases, corre tú
mismo el workflow (pestaña **Actions** → *Build SSE2-compatible
Windows templates* → **Run workflow**) después de agregar esa
versión a la lista en
`.github/workflows/build-templates.yml` (es una sola línea).

## Cómo usarlos

**Para un proyecto propio en Godot:** reemplaza el template oficial
correspondiente dentro de tu carpeta de export templates de Godot
(normalmente `%APPDATA%\Godot\export_templates\<versión>\`) por este
archivo, respetando el nombre que Godot espera para esa plataforma.

**Para correr un juego/proyecto ya exportado por alguien más:** el
`.exe` original suele traer separado un archivo `.pck` con los datos
del proyecto. Corre el template parcheado apuntando a ese `.pck`:

```
godot-4.6.2-stable-sse2-windows-x86_64.exe --main-pack "NombreDelProyecto.pck"
```

Si abre sin el error de SSE4.2, puedes renombrar el `.exe` original a
otra cosa (como respaldo) y renombrar este al mismo nombre que
Godot espera junto al `.pck`, para tener doble clic normal.

## Cómo funciona el parche

Dos cambios sobre el código fuente oficial de Godot, antes de
compilar:

1. **`SConstruct`** — se quita el flag de compilador `-msse4.2` que
   activa el uso de esas instrucciones en el motor.
2. **`platform/windows/cpu_feature_validation.c`** — Godot agrega un
   chequeo explícito de CPU al arrancar (independiente del punto 1)
   que muestra el mensaje de error y cierra el programa si no detecta
   SSE4.2. Se neutraliza para que siempre continúe.

Ambos cambios están confirmados contra el código fuente real de cada
versión listada arriba — el texto exacto que se busca/reemplaza es
idéntico en las 9.

## Licencia y disclaimer

Este repo solo contiene el workflow de compilación — Godot Engine en
sí es software libre bajo licencia MIT
([godotengine/godot](https://github.com/godotengine/godot)). Este
proyecto no está afiliado ni respaldado por Godot Engine ni la Godot
Foundation. Los binarios que genera son builds no oficiales; úsalos
bajo tu propio criterio.

El código de este repositorio se distribuye bajo licencia MIT — ver
[LICENSE](LICENSE).
