# Árbol Binario de Búsqueda (ABB)

## 1. Árbol inicial

Inserción en orden: 50, 30, 20, 40, 70, 60, 80

```
            50   <- RAÍZ
          /    \
        30      70
       /  \    /  \
     20    40 60    80
    (hoja)(hoja)(hoja)(hoja)
```

| Nodo | Hijo izquierdo | Hijo derecho | Tipo |
|------|---------------|--------------|------|
| 50 | 30 | 70 | raíz |
| 30 | 20 | 40 | interno |
| 70 | 60 | 80 | interno |
| 20, 40, 60, 80 | null | null | hoja |

**¿Qué propiedad debe cumplir todo ABB?**
Para cada nodo, todas las claves de su subárbol izquierdo son menores y todas las del subárbol derecho son mayores que la clave del nodo (y esto se cumple en todos los nodos, no solo en la raíz).

**¿Cuál es la raíz?** 50 (el primer valor insertado).

**¿Qué nodos son hojas?** 20, 40, 60 y 80.

**Subárbol izquierdo y derecho de 50:** izquierdo = {30, 20, 40}; derecho = {70, 60, 80}.

**Recorrido inorden esperado:** 20 30 40 50 60 70 80 (ascendente).
Preorden: 50 30 20 40 70 60 80. Postorden: 20 40 30 60 80 70 50.

## 2. Búsqueda

En cada llamada de `buscarRec` se revisa: (1) si el nodo es `null`, devuelve `false`; (2) si la clave coincide, devuelve `true`; (3) si no, busca solo en el subárbol izquierdo (clave menor) o derecho (clave mayor).

**¿Por qué no hay que recorrer todos los nodos?**
Porque en cada comparación se descarta un subárbol completo. La propiedad del ABB garantiza que la clave no puede estar en el lado que no corresponde.

**Buscar 40:** se visitan 50, luego 30 y luego 40 (encontrado).

**Buscar 90:** 50 → 70 → 80 → derecho de 80 es `null`. Esa condición (`raiz == null`) permite concluir que no existe.

**Valor del caso base con `null`:** `false`.

**¿Y si el árbol no respetara la regla menor-izquierda / mayor-derecha?**
La búsqueda podría descartar el subárbol donde sí está la clave y devolver `false` aunque exista. Habría que recorrer todo el árbol para estar seguros.

## 3. Eliminación

`eliminarRec` localiza el nodo y, al encontrarlo, revisa tres casos: hoja, un hijo o dos hijos. Devuelve la referencia que debe quedar en esa posición, y el padre la asigna (`raiz.izquierdo = eliminarRec(...)`).

**¿Por qué requiere más casos que la búsqueda?**
La búsqueda solo lee. La eliminación modifica la estructura y debe dejar el árbol conectado y ordenado según el número de hijos del nodo.

**¿Qué pasa si la clave no existe?**
Se llega a `null`, se devuelve `null` y no se cambia nada en el árbol.

**¿Por qué eliminar una hoja es lo más sencillo?**
No tiene subárboles que reubicar, así que basta devolver `null` para que el padre ya no la apunte.

**Un solo hijo, ¿por qué se devuelve ese hijo?**
Todo el subárbol del hijo ya cumple el orden respecto al abuelo (estaba del mismo lado del padre). Entonces el hijo puede ocupar el lugar del nodo eliminado sin romper nada.

**¿Por qué el menor del subárbol derecho sirve de sustituto?**
Es mayor que todo el subárbol izquierdo y menor o igual que el resto del derecho, por lo que al ponerlo en el nodo se mantiene la regla del ABB. Además, por ser el más a la izquierda, tiene a lo más un hijo y es fácil de quitar.

**¿Por qué hay que eliminar el valor sustituto de su lugar original?**
Porque copiar el valor lo deja duplicado. El ABB no admite claves repetidas y el nodo original seguiría ahí.

**¿Qué riesgo hay si no se reconectan bien los subárboles?**
Se perderían nodos (quedarían desconectados y sus datos desaparecerían) o se violaría el orden del ABB.

**¿Por qué eliminar la raíz puede cambiar la variable `raiz`?**
Porque si la raíz es hoja o tiene un solo hijo, el método devuelve otra referencia y `eliminar()` la asigna con `raiz = eliminarRec(raiz, clave)`.

**¿Qué propiedad debe seguir cumpliendo el árbol?**
La propiedad del ABB: menores a la izquierda, mayores a la derecha. Por eso el inorden sigue ascendente.

## 4. Método auxiliar: valor mínimo

```java
private int valorMinimo(Nodo nodo) {
    while (nodo.izquierdo != null) nodo = nodo.izquierdo;
    return nodo.clave;
}
```

**¿Hacia dónde me desplazo?** Siempre hacia el hijo izquierdo.

**¿Qué condición indica que encontré el mínimo?** Que el nodo actual no tenga hijo izquierdo (`nodo.izquierdo == null`).

**Mínimo del subárbol con raíz 70:** 60.

## 5. Pruebas (salida del programa)

```
Inorden:
20 30 40 50 60 70 80
Preorden:
50 30 20 40 70 60 80
Postorden:
20 40 30 60 80 70 50

BÚSQUEDA
La clave 40 se encontró.

ELIMINACIÓN
Después de eliminar 20
30 40 50 60 70 80
Después de eliminar 70
30 40 50 60 80
Después de eliminar 50
30 40 60 80
```

- Eliminar 20: hoja.
- Eliminar 70: dos hijos; lo reemplaza 80 (menor del subárbol derecho) y 60 queda como hijo izquierdo de 80.
- Eliminar 50: raíz con dos hijos; la reemplaza 60 y la nueva raíz es 60.

Árbol final:

```
        60
       /  \
     30    80
       \
        40
```

## 6. Reflexiones finales

**¿Cómo ayuda el inorden a comprobar que el ABB conserva su estructura?**
En un ABB válido, el inorden siempre sale en orden ascendente. Si después de insertar o eliminar sale desordenado, algo se rompió.

**Caso de eliminación más difícil:**
El de dos hijos, sobre todo cuando es la raíz. Hay que encontrar el sucesor, copiar su valor y luego eliminar ese valor del subárbol derecho, que puede tener a su vez un hijo que reconectar (como el 60 al eliminar el 70 y el 50).

**Papel de la recursividad:**
Cada llamada trabaja con un subárbol más pequeño, y el caso base es `null`. Evita escribir ciclos con pilas para bajar y subir por el árbol. En la eliminación, además, el retorno de cada llamada es lo que reconecta los nodos al regresar.

**Qué aprendí sobre el cambio de referencias:**
Eliminar un nodo en realidad es cambiar quién lo apunta. Por eso el método devuelve una referencia y el padre la guarda; si olvidas asignarla, el árbol no cambia.

**Diferencia entre buscar y eliminar, para un compañero:**
Buscar solo baja por una rama comparando y dice si la clave existe, sin cambiar nada. Eliminar primero hace esa misma búsqueda, pero al encontrar el nodo tiene que decidir qué referencia ocupará su lugar según tenga 0, 1 o 2 hijos, cuidando que el árbol siga ordenado.
