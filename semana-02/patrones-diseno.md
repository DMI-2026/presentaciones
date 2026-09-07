---
marp: true
theme: default
paginate: true
size: 16:9
transition: fade
footer: 'DMI · Unidad I · Patrones de diseño'
style: |
  section {
    font-family: -apple-system, "Segoe UI", Helvetica, Arial, sans-serif;
    background: #ffffff;
    color: #1a1a1a;
  }
  h1 { color: #0b3d91; }
  h2 { color: #0b3d91; }
  table { font-size: 0.85em; }
  footer { color: #6b7280; font-size: 0.6em; }
  .box {
    border: 1px solid #0b3d91;
    border-radius: 6px;
    padding: 0.5em 0.8em;
    display: inline-block;
    margin: 0.3em;
  }
  .flow { font-size: 1.1em; text-align: center; }
  .columns { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 1.2rem; }
  .no { border-left: 4px solid #b91c1c; padding-left: 0.8em; }
  .si { border-left: 4px solid #0b3d91; padding-left: 0.8em; }
---

<!-- _paginate: skip -->
<!-- _footer: '' -->

# Desarrollo Móvil Integral
## Patrones de diseño en el desarrollo móvil con Flutter
Unidad I — Definición del proceso de desarrollo móvil

<!--
Actividad 6 de la secuencia. Abrir preguntando quién ya usó un patrón sin
saber su nombre. Objetivo de salida: que cada equipo pueda decir qué patrón
va en qué componente de su proyecto y por qué.
-->

---

## Qué es un patrón de diseño

<div class="columns">
<div class="no">

**No es**
* Código para copiar y pegar
* Una librería que se instala

</div>
<div class="si">

**Sí es**
* Solución **nombrada** a un problema recurrente de estructura
* **Vocabulario compartido** del equipo

</div>
</div>

<div class="flow">
<span class="box">Problema recurrente</span> → <span class="box">Nombre + estructura conocida</span> → <span class="box">Vocabulario del equipo</span>
</div>

<!--
Insistir en la columna izquierda: el error típico es buscar "el código del
patrón". Ejemplo verbal: decir "esto es un Repository" ahorra diez minutos
de explicación en una revisión de código.
-->

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

<!--
Esta es la diapositiva ancla de la sesión. Pedir que identifiquen en voz
alta cuál señal ya vieron en su propio proyecto. Volver a esta tabla en la
Actividad 7 cuando propongan sus patrones.
-->

---

## Las tres familias del GoF

| Familia | Patrones del curso |
| --- | --- |
| Creacional | Factory |
| Estructural | Adapter, Repository* |
| Comportamiento | Strategy, Observer |

\* Repository no es uno de los 23 patrones originales del GoF: es un patrón de arquitectura de aplicaciones empresariales (Fowler). Se agrupa aquí por su rol estructural: aísla una capa de otra.

<!--
Aclarar el asterisco explícitamente: si alguien busca Repository en el libro
del GoF no lo va a encontrar. Es de Fowler, PoEAA.
-->

---

## Repository: aísla el dominio del origen de los datos

La pantalla pide información sin saber si viene de la red, de caché o de memoria.

<div class="flow">
<span class="box">UI</span> → <span class="box">Repository (interfaz)</span> → <span class="box">Red</span> / <span class="box">Caché</span> / <span class="box">Memoria</span>
</div>

<!--
Preguntar: ¿qué pasa si mañana el backend cambia de REST a GraphQL? Con
Repository se toca una implementación; sin él, toda la app.
-->

---

## Repository en la práctica

Una misma interfaz sirve un repositorio falso en pruebas y uno real contra la API.

| Entorno | Implementación |
| --- | --- |
| Pruebas | `FakeRepository` (memoria) |
| Producción | `ApiRepository` (red) |

Eso permite probar la aplicación antes de tener servidor.

<!--
Conectar con la práctica de la semana: pueden avanzar la UI aunque el
servidor no exista todavía. Es el argumento más convincente del patrón.
-->

---

## Adapter: traduce en el borde

Si cambia el contrato de la API se toca una clase, no toda la aplicación.

<div class="flow">
<span class="box">API del proveedor (JSON)</span> → <span class="box">Adapter</span> → <span class="box">Modelo de dominio (Dart)</span>
</div>

<!--
Ejemplo concreto: el proveedor renombra un campo de "user_name" a
"userName". Con Adapter se corrige en un solo lugar.
-->

---

## Factory: centraliza qué objeto construir

| Mecanismo | Qué resuelve | Nivel |
| --- | --- | --- |
| Constructor factory (Dart) | Devuelve instancia ya creada o de caché | Lenguaje |
| Factory Method (GoF) | Una subclase decide qué clase concreta crear | Patrón de diseño |
| Abstract Factory (GoF) | Crea familias completas de objetos relacionados | Patrón de diseño |

<!--
Ojo con la confusión frecuente: el "factory constructor" de Dart es una
característica del lenguaje, no el patrón Factory Method del GoF.
-->

---

## Strategy: intercambia el algoritmo en ejecución

Elimina condicionales anidados.

| Enfoque | Cuándo conviene |
| --- | --- |
| Clase Strategy | Varias variantes, cada una con estado propio |
| Función como parámetro | Lógica simple, sin estado |
| Clases selladas + `switch` exhaustivo | Conjunto cerrado y conocido de variantes (Dart 3) |

<!--
En Dart la clase Strategy compite con dos alternativas más simples. Que
elijan la más ligera que resuelva su caso, no la más "patrón".
-->

---

## Observer: un cambio de estado notifica a quien escucha

En Flutter: `Listenable`, `ChangeNotifier`, `ValueNotifier`, `Stream`.

<div class="flow">
<span class="box">Estado (ChangeNotifier)</span> → notifyListeners() → <span class="box">Widget 1</span> <span class="box">Widget 2</span> <span class="box">Widget 3</span>
</div>

<!--
Este es el patrón que ya usan sin saberlo: setState y los widgets con
estado son Observer. Anclar ahí antes de nombrar las clases.
-->

---

## Fugas por suscripción

El error más frecuente del patrón — el compilador no lo señala.

* `addListener` en `initState`, `removeListener` en `dispose`
* `StreamSubscription.cancel()` es obligatorio
* Síntoma: memoria creciente, callbacks sobre widgets ya destruidos
* El compilador no marca error si se omite

<!--
Revelar una por una. Este punto vale una pregunta de examen: pedir que
digan dónde va el cancel() antes de mostrarlo.
-->

---

## Inyección de dependencias

El objeto recibe lo que necesita en vez de construirlo.

<div class="flow">
<span class="box">Clase</span> recibe por constructor → <span class="box">Repository real</span> (producción) o <span class="box">Repository falso</span> (pruebas)
</div>

Eso es lo que permite sustituir la implementación real por una falsa en las pruebas.

<!--
Cerrar el círculo con la diapositiva de Repository en la práctica: sin
inyección, el repositorio falso no puede entrar.
-->

---

## Inyección frente a localizador de servicios

| Criterio | Inyección de dependencias | Localizador de servicios |
| --- | --- | --- |
| Visibilidad de la dependencia | Explícita en el constructor | Oculta dentro del método |
| Facilidad de prueba | Alta — se sustituye en el constructor | Baja — requiere configurar el localizador global |
| Momento del error si falta el registro | Compilación | Ejecución |

<!--
La última fila es el criterio decisivo: un error en compilación cuesta
minutos, uno en ejecución cuesta una demo fallida.
-->

---

## Dirección de las dependencias

La capa de datos nunca conoce la de presentación.

<div class="flow">
<span class="box">Presentación</span> → <span class="box">Dominio</span> → <span class="box">Datos</span>
</div>

Flecha permitida: hacia abajo. Flecha rota: Datos → Presentación.

<!--
Enlaza con la Actividad 5 (revisión del diagrama de otro equipo): esta es
la regla que van a usar para detectar dependencias que rompen las capas.
-->

---

## Cuánto cuesta cada capa

| | Vida corta | Vida larga |
| --- | --- | --- |
| Equipo pequeño | Arquitectura ligera, capas fusionadas | Repository + Adapter mínimo, sin sobre-diseño |
| Equipo grande | Capas separadas para paralelizar trabajo | Arquitectura completa por capas + inyección de dependencias |

<!--
Ubicar su propio proyecto en la matriz en voz alta: equipo de 6, vida de un
cuatrimestre. Casi todos caen en la celda de arriba a la izquierda.
-->

---

## Conclusiones

* Un patrón se aplica ante una señal observable, no por costumbre
* Repository y Adapter aíslan el dominio de lo externo
* Factory y Strategy centralizan decisiones que cambian con el tiempo
* Observer y la inyección de dependencias sostienen el estado y las pruebas

<!--
Revelar una por una y cerrar con el entregable: la Actividad 7 pide el
patrón por componente con su justificación.
-->

---

## Referencias

- Gamma, Helm, Johnson, Vlissides. *Design Patterns: Elements of Reusable Object-Oriented Software*. 1994.
- Fowler, M. *Patterns of Enterprise Application Architecture*. 2002 — patrón Repository.
- Documentación oficial de Flutter — `Listenable`, `ChangeNotifier`, `Stream`.
- Documentación oficial de Dart — Effective Dart, null safety, clases selladas.
