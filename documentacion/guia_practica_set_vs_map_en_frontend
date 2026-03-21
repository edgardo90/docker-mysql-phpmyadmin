# 🧠 Guía práctica: Set vs Map (Frontend / React / JS)

---

## 📌 Idea base

- **Set** → sirve para validar existencia (¿está o no?)
- **Map** → sirve para obtener datos por clave (id → objeto)

---

## 🔹 Set

### Qué es

Estructura que guarda valores únicos y permite búsquedas rápidas.

```ts
const set = new Set([1, 2, 3]);

set.has(2); // true
```

### Cuándo usarlo

✔️ Validar si un valor existe  
✔️ Comparar listas  
✔️ Reemplazar `.includes()`

---

## 🔹 Map

### Qué es

Estructura clave → valor

```ts
const map = new Map([
  [1, "Juan"],
  [2, "Ana"]
]);

map.get(1); // "Juan"
```

---

## 🔍 Cómo crear un Map desde una lista

```ts
const lista = [
  { codigo: 10, nombre: "Ford" },
  { codigo: 20, nombre: "Toyota" }
];

const map = new Map(lista.map(c => [c.codigo, c]));
```

### Resultado

```
10 → { codigo: 10, nombre: "Ford" }
20 → { codigo: 20, nombre: "Toyota" }
```

---

## ⚡ Diferencia con find

### ❌ find

```ts
lista.find(c => c.codigo === 20);
```

- recorre todo el array
- O(n)

---

### ✅ Map

```ts
map.get(20);
```

- acceso directo
- O(1)

---

## 📊 Cuándo usar cada uno

| Caso | Usar |
|------|------|
| 1 búsqueda | find |
| pocas búsquedas | find |
| muchas búsquedas | Map |
| dentro de map() | Map |
| validación de existencia | Set |

---

## 🚨 Regla mental clave

Si ves esto:

```ts
array.map(...).find(...)
```

👉 probablemente deberías usar **Map**

---

## 🧠 Ejemplo real (multi select)

### ❌ Mala práctica

```ts
const selected = ids.map(id =>
  lista.find(item => item.id === id)
);
```

---

### ✅ Buena práctica

```ts
const map = new Map(lista.map(item => [item.id, item]));

const selected = ids.map(id => map.get(id));
```

---

## ⚡ Set vs includes

### ❌ includes

```ts
ids.includes(10);
```

O(n)

---

### ✅ Set

```ts
const set = new Set(ids);
set.has(10);
```

O(1)

---

## 🧠 Regla final (nivel senior)

- **find** → simple y una sola búsqueda
- **Set** → validar existencia
- **Map** → muchas búsquedas o lookup por ID

---

## 🚀 Insight clave

> La IA escribe código que funciona.  
> El desarrollador decide si escala.

---

## 🧩 Tip extra (React)

Usar `useMemo` para no recrear el Map en cada render:

```ts
const map = useMemo(() => {
  return new Map(lista.map(item => [item.id, item]));
}, [lista]);
```

---

## 🧾 Resumen final

- `Set` → rápido para existencia
- `Map` → rápido para lookup
- `find` → simple pero no escala

---

💡 Si tu código empieza a crecer o repetís búsquedas → pensá en Map o Set.

