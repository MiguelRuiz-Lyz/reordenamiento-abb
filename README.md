# 🔄 Reordenamiento de un Árbol Binario de Búsqueda (ABB)

> **Materia:** Estructura de Datos II (INF-310)
> **Unidad 1:** Árboles Binarios de Búsqueda
> **Reto:** Reordenamiento (rebalanceo) de un ABB
> **Autor:** Miguel Ruiz Loayza

Implementación en **Python** de un ABB con **modelo estático** (vector de capacidad fija) que almacena **nombres** y que incorpora la operación **`reordenar()`**: reconstruir el árbol para que quede **balanceado**, sin perder ningún elemento.

Este proyecto continúa los anteriores: reutiliza el ATD del ABB estático con recorrido inorden y le agrega el reordenamiento.

---

## ❓ ¿Por qué reordenar?

La forma de un ABB depende del **orden en que se insertan** los datos. Si se insertan ya ordenados (por ejemplo `Ana, Carlos, Luis, ...`), cada nodo solo tiene hijo derecho y el árbol se convierte en una **lista enlazada** disfrazada:

```
Ana
  └─ Carlos
       └─ Luis
            └─ Miguel
                 └─ Pedro
                      └─ Rosa
                           └─ Zoe          altura = 7  →  buscar cuesta O(n)
```

El reordenamiento lo transforma en un árbol de **altura mínima**:

```
              Miguel
            /        \
       Carlos         Rosa
       /    \        /    \
     Ana    Luis  Pedro   Zoe              altura = 3  →  buscar cuesta O(log n)
```

## 🧠 Algoritmo de `reordenar()`

1. Obtener los elementos con el recorrido **inorden** → salen **ordenados**.
2. **Vaciar** el árbol (`crear()`).
3. Insertar de nuevo con esta regla recursiva sobre la lista ordenada:
   - insertar el elemento del **medio**,
   - repetir con la **mitad izquierda**,
   - repetir con la **mitad derecha**.

Como el elemento del medio entra primero, queda como raíz, y cada mitad se reparte equilibrada a ambos lados.

```python
def reordenar(self):
    elementos = self.inorden()
    self.crear()
    self._insertar_balanceado(elementos, 0, len(elementos) - 1)

def _insertar_balanceado(self, elementos, inicio, fin):
    if inicio > fin:
        return
    medio = (inicio + fin) // 2
    self.insertar(elementos[medio])
    self._insertar_balanceado(elementos, inicio, medio - 1)
    self._insertar_balanceado(elementos, medio + 1, fin)
```

## ⚙️ Operaciones del ATD

| Operación | Descripción |
|---|---|
| `crear()` | Inicializa el árbol vacío |
| `vacio()` | ¿El árbol está vacío? |
| `insertar(elemento)` | Inserta sin duplicados. `False` si hay duplicado o no hay espacio |
| `buscar(elemento)` | Retorna el índice en el vector, o `None` |
| `eliminar(elemento)` | Elimina (hoja, un hijo o dos hijos) |
| `inorden()` | Recorrido Izquierda → Raíz → Derecha |
| **`altura()`** | Cantidad de niveles (vacío = 0, un nodo = 1) |
| **`esta_balanceado()`** | `True` si en todo nodo las alturas de sus subárboles difieren como máximo en 1 |
| **`reordenar()`** | Reconstruye el árbol balanceado |
| `mostrar_vector()` | Imprime el contenido interno del vector |
| **`mostrar_arbol()`** | Dibuja el árbol girado 90° (raíz a la izquierda) |

*(En negrita, lo nuevo respecto al proyecto anterior.)*

## 🧱 Modelo estático

El árbol vive en un vector de tamaño fijo (`capacidad = 50` por defecto):

```python
vector = [None] * capacidad
```

Cada posición guarda un `Nodo` cuyos hijos son **índices del vector**. `None` es una posición libre. El reordenamiento **no necesita más espacio**: vacía el vector y vuelve a llenar los mismos elementos, así que nunca supera la capacidad.

## 📈 Complejidad

| Operación | Tiempo |
|---|---|
| `inorden`, `altura`, `esta_balanceado` | O(n) |
| `buscar` | O(h) |
| `reordenar` | O(n) por el inorden + n inserciones; cada inserción cuesta O(capacidad) por la búsqueda de espacio libre + O(h) del descenso |

*(h = altura, n = cantidad de nodos.)*

## 🗂️ Estructura del proyecto

```
reordenamiento-abb/
├── .vscode/
│   ├── launch.json          # Ejecutar/depurar con F5
│   └── settings.json        # Configuración de tests
├── src/
│   ├── __init__.py
│   ├── nodo.py              # Clase Nodo
│   └── arbol_binario_busqueda.py   # ABB estático + reordenamiento
├── tests/
│   ├── __init__.py
│   └── test_reordenamiento.py      # 17 pruebas unitarias
├── .gitignore
├── main.py                  # Demostración antes/después
└── README.md
```

## ✅ Requisitos

- [Python 3.8 o superior](https://www.python.org/downloads/)
- [Visual Studio Code](https://code.visualstudio.com/) con la extensión **Python** (recomendado)
- [Git](https://git-scm.com/)

No se necesitan librerías externas.

## ▶️ Cómo ejecutarlo

Desde la carpeta raíz del proyecto:

```bash
python main.py
```

En Windows también puede ser `py main.py`; en Linux/macOS, `python3 main.py`.

## 🧪 Cómo ejecutar las pruebas

```bash
python -m unittest -v
```

Deberías ver `Ran 17 tests ... OK`.

## 🖥️ Salida de `main.py`

```
=== ANTES de reordenar ===
Inorden        : ['Ana', 'Carlos', 'Luis', 'Miguel', 'Pedro', 'Rosa', 'Zoe']
Altura         : 7
¿Balanceado?   : False

Árbol (girado 90°, raíz a la izquierda):
                                                Zoe
                                        Rosa
                                Pedro
                        Miguel
                Luis
        Carlos
Ana

Vector:
[0] Elemento: Ana, Izq: None, Der: 1
[1] Elemento: Carlos, Izq: None, Der: 2
[2] Elemento: Luis, Izq: None, Der: 3
[3] Elemento: Miguel, Izq: None, Der: 4
[4] Elemento: Pedro, Izq: None, Der: 5
[5] Elemento: Rosa, Izq: None, Der: 6
[6] Elemento: Zoe, Izq: None, Der: None

=== DESPUÉS de reordenar ===
Inorden        : ['Ana', 'Carlos', 'Luis', 'Miguel', 'Pedro', 'Rosa', 'Zoe']
Altura         : 3
¿Balanceado?   : True

Árbol (girado 90°, raíz a la izquierda):
                Zoe
        Rosa
                Pedro
Miguel
                Luis
        Carlos
                Ana

Vector:
[0] Elemento: Miguel, Izq: 1, Der: 4
[1] Elemento: Carlos, Izq: 2, Der: 3
[2] Elemento: Ana, Izq: None, Der: None
[3] Elemento: Luis, Izq: None, Der: None
[4] Elemento: Rosa, Izq: 5, Der: 6
[5] Elemento: Pedro, Izq: None, Der: None
[6] Elemento: Zoe, Izq: None, Der: None

¿Existe Luis? (índice en el vector): 3
¿Existe Juan? : None
```

## 🚀 Posibles mejoras

- [ ] Rebalanceo **automático** al insertar/eliminar (árbol AVL con rotaciones).
- [ ] Mejorar `insertar` con una lista de posiciones libres para evitar recorrer el vector.
- [ ] Agregar recorridos **preorden** y **postorden**.
- [ ] Versión **dinámica** (nodos enlazados).

## 👤 Autor

**Miguel Ruiz Loayza** — Estructura de Datos II (INF-310)
