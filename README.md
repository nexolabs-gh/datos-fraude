# Datos del curso «Detección de Fraude y Anomalías con Python»

Datos sintéticos de **Yaguar**, una billetera digital ficticia que opera en México, Colombia, Perú, Chile y Ecuador. Son del curso de Academia Bayes y los notebooks los leen directo desde aquí: no hace falta descargarlos. Los datos son ficticios y no describen a ninguna persona ni empresa real.

## Cómo leerlos

```python
import pandas as pd

BASE = "https://raw.githubusercontent.com/nexolabs-gh/datos-fraude/main/v1"
operaciones = pd.read_parquet(f"{BASE}/a_operaciones.parquet")
```

Si GitHub no responde, el espejo es `https://cdn.jsdelivr.net/gh/nexolabs-gh/datos-fraude@main/v1`.

## Versiones

- **`v1/` es inmutable:** ningún archivo cambia; solo se agregan archivos nuevos, y cada agregado se avisa.
- **`v1/manifiesto.json`** trae el sha256 y el tamaño de cada archivo, para comprobar que lees los mismos bytes.

## Dos carteras

- **A** (prefijo `a_`): la de las clases.
- **B** (prefijo `b_`): la de las entregas evaluadas. Cada alumno trabaja una muestra personal de B que sale de su correo.

Las dos tienen las mismas tablas y el mismo diccionario. B trae dos columnas más en `senales`.

## El caso

Cada operación pasa por el sistema vigente, que la aprueba, la retiene para revisión o la declina. Un caso es un cliente en un día local, y los analistas revisan K casos por día. Los reclamos de los clientes llegan después. La capacidad, los costos y las reglas del vigente están en `parametros_caso.json`.

## Tiempo

- **Operaciones:** del 2025-01-01 al 2025-12-31, según `ts_utc` (en UTC y sin huso).
- **`fecha_local` y `hora_local`:** en el huso del país del cliente. El día de un caso es su `fecha_local`.
- **Corte 1, 2026-01-31:** es el «hoy» del curso. Los reclamos y las columnas que dependen de ellos están como se conocían ese día.
- **Partición** (por el mes de `ts_utc`): M01 rodaje · M02–M06 entrenamiento · M07–M08 validación · M09–M10 OOT · M11–M12 monitoreo.

## Tablas

| Archivo | Una fila por | Llave |
|---|---|---|
| `operaciones` | operación intentada | `id_operacion` |
| `clientes` | cliente | `id_cliente` |
| `eventos_cuenta` | evento de cuenta: cambio de celular, cambio de clave o dispositivo nuevo | — |
| `comercios` | comercio | `id_comercio` |
| `reclamos` | reclamo, al corte 1 | `id_reclamo` |
| `auditoria` | operación aprobada que se revisó a posteriori: una muestra al azar | `id_operacion` |
| `parametros_caso.json` | — | K, capacidad, costos, fracciones, reglas del vigente y escenarios |

## Intermedios

Vienen precalculados para que ninguna celda del curso espere. `particion`, `senales`, `score_sup` y `score_anom` están en el mismo orden que `operaciones`.

| Archivo | Una fila por | Qué trae |
|---|---|---|
| `particion` | operación | Partición, madurez y etiquetas al corte 1 |
| `senales` | operación | Señales de comportamiento del cliente, la tarjeta, el dispositivo y el comercio |
| `score_sup` | operación | Scores de los modelos supervisados |
| `score_anom` | operación | Score de anomalía |
| `red` | cliente | Grafo cliente–dispositivo y paso de fondos |
| `cola` | operación retenida por la política del curso | Caso, franja, prioridad y revisión |
| `razones` | caso revisado de M07–M12 | Reglas del curso y las tres razones del score |
| `importancias` | señal | Importancia por permutación |
| `transversales.json`, `testigos.json` | — | Las cifras y los casos de la cartera A que citan las clases |

## Diccionario

`v1/diccionario.csv` describe cada columna: `tabla`, `columna`, `tipo`, `disponible_en` y `descripcion`. `disponible_en` dice cuándo se conoce el valor:

- `decision`: al momento de decidir la operación;
- `posterior`: después de decidirla;
- `etiqueta`: es la etiqueta o sale de ella.

## Formato

Parquet. Los ids son `int64`, las categóricas `category` y `ts_utc` es `datetime64[ns]` sin huso. Los montos están en USD con 2 decimales.

## Uso

Material docente de Academia Bayes.
