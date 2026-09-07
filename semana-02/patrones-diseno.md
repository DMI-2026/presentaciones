---
marp: true
theme: default
paginate: true
size: 16:9
style: |
  section {
    font-family: -apple-system, "Segoe UI", Helvetica, Arial, sans-serif;
    background: #ffffff;
    color: #1a1a1a;
  }
  h1 { color: #0b3d91; }
  h2 { color: #0b3d91; }
  table { font-size: 0.85em; }
  .box {
    border: 1px solid #0b3d91;
    border-radius: 6px;
    padding: 0.5em 0.8em;
    display: inline-block;
    margin: 0.3em;
  }
  .flow { font-size: 1.1em; text-align: center; }
---

# Desarrollo Móvil Integral
## Patrones de diseño en el desarrollo móvil con Flutter
Unidad I — Definición del proceso de desarrollo móvil

---

## Qué es un patrón de diseño

- Solución **nombrada** a un problema recurrente de estructura
- No es código para copiar y pegar
- Su valor real: **vocabulario compartido** del equipo
- Documenta una relación entre clases, no una implementación fija

<div class="flow">
<span class="box">Problema recurrente</span> → <span class="box">Nombre + estructura conocida</span> → <span class="box">Vocabulario del equipo</span>
</div>

---

## Cuándo aplicar uno

Sin señal no hay patrón: hay adorno.

| Señal observable | Síntoma en el código | Patrón candidato |
| --- | --- | --- |
| La pantalla no sabe si los datos vienen de red o caché | Llamadas HTTP mezcladas con lógica de UI | Repository |
| Cambia el formato de un proveedor externo | If/else por proveedor esparcidos en el código | Adapter |
| Se decide en tiempo de ejecución qué clase construir | Constructores condicionales repetidos | Factory |
| El algoritmo cambia según el contexto | Condicionales anidados por caso | Strategy |
| Varias partes deben reaccionar a un cambio de estado | Llamadas manuales a funciones de refresco | Observer |

---

## Las tres familias del GoF

| Familia | Patrones del curso |
| --- | --- |
| Creacional | Factory |
| Estructural | Adapter, Repository* |
| Comportamiento | Strategy, Observer |

\* Repository no es uno de los 23 patrones originales del GoF: es un patrón de arquitectura de aplicaciones empresariales (Fowler). Se agrupa aquí por su rol estructural: aísla una capa de otra.

---

## Repository: aísla el dominio del origen de los datos

La pantalla pide información sin saber si viene de la red, de caché o de memoria.

<div class="flow">
<span class="box">UI</span> → <span class="box">Repository (interfaz)</span> → <span class="box">Red</span> / <span class="box">Caché</span> / <span class="box">Memoria</span>
</div>

---

## Repository en la práctica

Una misma interfaz sirve un repositorio falso en pruebas y uno real contra la API.

| Entorno | Implementación |
| --- | --- |
| Pruebas | `FakeRepository` (memoria) |
| Producción | `ApiRepository` (red) |

Eso permite probar la aplicación antes de tener servidor.

---

## Adapter: traduce en el borde

Si cambia el contrato de la API se toca una clase, no toda la aplicación.

<div class="flow">
<span class="box">API del proveedor (JSON)</span> → <span class="box">Adapter</span> → <span class="box">Modelo de dominio (Dart)</span>
</div>

---

## Factory: centraliza qué objeto construir

| Mecanismo | Qué resuelve | Nivel |
| --- | --- | --- |
| Constructor factory (Dart) | Devuelve instancia ya creada o de caché | Lenguaje |
| Factory Method (GoF) | Una subclase decide qué clase concreta crear | Patrón de diseño |
| Abstract Factory (GoF) | Crea familias completas de objetos relacionados | Patrón de diseño |

---

## Strategy: intercambia el algoritmo en ejecución

Elimina condicionales anidados.

| Enfoque | Cuándo conviene |
| --- | --- |
| Clase Strategy | Varias variantes, cada una con estado propio |
| Función como parámetro | Lógica simple, sin estado |
| Clases selladas + `switch` exhaustivo | Conjunto cerrado y conocido de variantes (Dart 3) |

---

## Observer: un cambio de estado notifica a quien escucha

En Flutter: `Listenable`, `ChangeNotifier`, `ValueNotifier`, `Stream`.

<div class="flow">
<span class="box">Estado (ChangeNotifier)</span> → notifyListeners() → <span class="box">Widget 1</span> <span class="box">Widget 2</span> <span class="box">Widget 3</span>
</div>

---

## Fugas por suscripción

El error más frecuente del patrón — el compilador no lo señala.

- `addListener` en `initState`, `removeListener` en `dispose`
- `StreamSubscription.cancel()` es obligatorio
- Síntoma: memoria creciente, callbacks sobre widgets ya destruidos
- El compilador no marca error si se omite

---

## Inyección de dependencias

El objeto recibe lo que necesita en vez de construirlo.

<div class="flow">
<span class="box">Clase</span> recibe por constructor → <span class="box">Repository real</span> (producción) o <span class="box">Repository falso</span> (pruebas)
</div>

Eso es lo que permite sustituir la implementación real por una falsa en las pruebas.

---

## Inyección frente a localizador de servicios

| Criterio | Inyección de dependencias | Localizador de servicios |
| --- | --- | --- |
| Visibilidad de la dependencia | Explícita en el constructor | Oculta dentro del método |
| Facilidad de prueba | Alta — se sustituye en el constructor | Baja — requiere configurar el localizador global |
| Momento del error si falta el registro | Compilación | Ejecución |

---

## Dirección de las dependencias

La capa de datos nunca conoce la de presentación.

<div class="flow">
<span class="box">Presentación</span> → <span class="box">Dominio</span> → <span class="box">Datos</span>
</div>

Flecha permitida: hacia abajo. Flecha rota: Datos → Presentación.

---

## Cuánto cuesta cada capa

| | Vida corta | Vida larga |
| --- | --- | --- |
| Equipo pequeño | Arquitectura ligera, capas fusionadas | Repository + Adapter mínimo, sin sobre-diseño |
| Equipo grande | Capas separadas para paralelizar trabajo | Arquitectura completa por capas + inyección de dependencias |

---

## Conclusiones

- Un patrón se aplica ante una señal observable, no por costumbre
- Repository y Adapter aíslan el dominio de lo externo
- Factory y Strategy centralizan decisiones que cambian con el tiempo
- Observer y la inyección de dependencias sostienen el estado y las pruebas

---

## Prueba de asset local

![w:120](./img/prueba.png)

---

## Referencias

- Gamma, Helm, Johnson, Vlissides. *Design Patterns: Elements of Reusable Object-Oriented Software*. 1994.
- Fowler, M. *Patterns of Enterprise Application Architecture*. 2002 — patrón Repository.
- Documentación oficial de Flutter — `Listenable`, `ChangeNotifier`, `Stream`.
- Documentación oficial de Dart — Effective Dart, null safety, clases selladas.
