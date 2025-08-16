# 🎮 Proyecto ETL de Videojuegos  

Este proyecto implementa un proceso **ETL (Extracción, Transformación y Carga)** que integra información de **múltiples APIs públicas del mundo del videojuego y del universo Pokémon** (RAWG, GiantBomb y Pokémon Showdown).  

Los datos extraídos se transforman, normalizan y almacenan en **formato Parquet** para optimizar el análisis. Finalmente, se cargan en una **base de datos relacional MySQL (AWS RDS)** junto con un sistema de logging que asegura trazabilidad y control de las operaciones.  

---

## 🚀 Despliegue  

### 1. Requisitos  

- **Python 3.12**  
- Dependencias:  
  ```bash
  pip install mysql-connector-python pymysql sqlalchemy pandas requests
  ```
- Acceso a **MySQL en AWS RDS**  
- API Keys:  
  - RAWG → [https://rawg.io/apidocs](https://rawg.io/apidocs)  
  - GiantBomb → [https://www.giantbomb.com/api/](https://www.giantbomb.com/api/)  
  - Pokémon Showdown (no requieren API Key):  
    - [pokedex.json](https://play.pokemonshowdown.com/data/pokedex.json)  
    - [moves.json](https://play.pokemonshowdown.com/data/moves.json)  

---

### 2. Estructura del proyecto  

```
proyecto_videojuegos/
│── Proyecto Gabriel Rodriguez.py
│── registro_etl.csv
│── carga_global.csv
│── *.parquet
│
├── logs/
│   ├── registro_etl.log
│   ├── carga_global.log
```

---

### 3. Ejecución  

#### Extracción y guardado local  
```bash
python Proyecto Gabriel Rodriguez.py
```

Archivos generados en formato **Parquet**:  
- `rawg_games.parquet`  
- `giantbomb_games.parquet`  
- `pokemon_pokedex.parquet`  
- `pokemon_moves.parquet`  

#### Carga en base de datos  
```bash
python Proyecto Gabriel Rodriguez.py
```

---

## ⚙️ ¿Cómo funciona?  

### 🔹 Extracción  
- **RAWG** → videojuegos y metadatos.  
- **GiantBomb** → juegos, plataformas y ratings.  
- **Pokémon Showdown**:  
  - `pokedex.json` → información de Pokémon.  
  - `moves.json` → información de ataques.  

### 🔹 Transformación  
- Conversión de fechas.  
- Desanidamiento de diccionarios (`baseStats`, `abilities`, `flags`).  
- Normalización de columnas a **snake_case** sin acentos ni símbolos.  

### 🔹 Carga  
La carga se realiza con **SQLAlchemy**, añadiendo columnas técnicas:  

| Campo             | Descripción                               |
|-------------------|-------------------------------------------|
| `dw_fecha_registro` | Fecha/hora de carga                     |
| `dw_id_carga`       | UUID único por carga                     |
| `dw_deleted`        | Marcado lógico (default: False)          |
| `dw_source`         | Fuente de origen                         |
| `dw_endpoint`       | URL consultada para la extracción        |

Además, se registran las operaciones en las tablas:  
- `log_operaciones`  
- `log_etl_fuentes`  

---

## 📊 Consultas de ejemplo  

```sql
-- Top 10 juegos con mayor rating en RAWG
SELECT name, rating, released
FROM rawg_games
ORDER BY rating DESC
LIMIT 10;

-- Top Pokémon por peso
SELECT name, weightkg
FROM core_pokemon_pokedex
ORDER BY weightkg DESC
LIMIT 5;
```

---

## 📝 Logs  

El sistema de logging asegura trazabilidad en tres niveles:  

- **Local (archivos):**  
  - `logs/registro_etl.log`  
  - `logs/carga_global.log`  

- **CSV:**  
  - `registro_etl.csv`  
  - `carga_global.csv`  

- **Base de datos:**  
  - `log_operaciones`  
  - `log_etl_fuentes`  

---

## 👨‍💻 Autor  

Proyecto desarrollado por **Gabriel Rodriguez**.  
