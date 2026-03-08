Plan Backend – “ProjectZero”  
(Metodología Agile, MVP incremental, IndexedDB nativo, sin frameworks, sin dependencias)

Objetivo general  
Entregar, en el menor tiempo posible, un núcleo backend (lógica + persistencia) que permita crear, editar y eliminar proyectos, tareas, hitos, dependencias y calendarios; calcular fechas y la ruta crítica; y, más adelante, renderizar el diagrama de Gantt.  
El backend vive íntegramente en el cliente (Service Worker + IndexedDB) y se comunica con la UI mediante un API CRUD REST-like ya existente.

---

1. Visión y épicas (Product Backlog inicial)
Epic 1 – Persistencia pura  
Epic 2 – Modelo de dominio inmutable  
Epic 3 – Motor de fechas/calendario  
Epic 4 – Cálculo de ruta crítica  
Epic 5 – Gantt (solo renderizado, última epic)

---

2. Squad y roles (una sola persona puede asumirlos todos)
- PO (tú)  
- Scrum Master (auto-organizado)  
- Backend dev (JS puro)  
- QA (manual, chrome DevTools)

---

3. Cadencia  
Sprints de 1 semana.  
Definición de Done: test unitario verde + código < 200 líneas + sin warnings eslint + PR a “main”.

---

4. Backlog detallado por sprint

Sprint 0 – Setup (día 0)
- Repo Git vacío, ramas main/dev.  
- eslint + prettier sin plugins.  
- estructura de carpetas:  
  /src  
    /core (entidades)  
    /infra (IDB)  
    /usecases  
    /tests  
- IndexedDB shim para tests (fake-indexeddb) → NO es dependencia de producción.

Sprint 1 – Conexión IDB y esqueleta CRUD
Historias:  
H1.1 “Como dev quiero abrir una BD ‘ProjectZero’ versión 1 con objectStore ‘projects’ para poder guardar datos.”  
H1.2 “Como dev quiero poder insertar un proyecto {id, name, createdAt} y leerlo por id.”  
Tareas técnicas:  
- Wrapper IDB minimal (promesas nativas, sin librerías).  
- Funciones: openDB(), addProject(), getProject().  
- Tests con shim.  
Entregable: core/infra/idb.js + tests verdes.

Sprint 2 – Entidad Tarea y relación Project 1-N
Historias:  
H2.1 “Como usuario puedo crear tareas dentro de un proyecto.”  
H2.2 “Como usuario puedo listar las tareas de un proyecto.”  
Modelo:  
  Task = { id, projectId, name, duration, start, end, predecessors:[], resource:null }  
IDB: nuevo objectStore ‘tasks’ con índice projectId.  
Entregable: addTask(), listTasksByProject().

Sprint 3 – Validaciones de dominio
Historias:  
H3.1 “Como sistema rechazo duraciones negativas.”  
H3.2 “Como sistema rechazo fechas fin < fecha ini.”  
Tareas:  
- Crear value-objects DateRange, Duration.  
- Lanzar errores de dominio (DomainError).  
Entregable: core/entities/dateRange.js + tests.

Sprint 4 – Dependencias (predecesores)
Historias:  
H4.1 “Como usuario puedo indicar que una tarea depende de otra.”  
H4.2 “Como sistema evito ciclos.”  
Algoritmo DFS sin frameworks.  
Entregable: validateNoCycle(tasks) + tests.

Sprint 5 – Calendario laborable
Historias:  
H5.1 “Como usuario puedo definir días laborables (L-V).”  
H5.2 “Como sistema ajusto fechas al siguiente día laborable.”  
Modelo: Calendar = { workingDays:[1,2,3,4,5], holidays:[] }  
Entregable: addWorkingDays(date, duration) + tests.

Sprint 6 – Cálculo de fechas forward
Historias:  
H6.1 “Como sistema calculo automáticamente fecha fin a partir de duración y calendario.”  
H6.2 “Como sistema respeto predecesores: la tarea no empieza hasta que termine el último predecesor.”  
Entregable: calculateStartEnd(tasks, calendar) + tests.

Sprint 7 – Cálculo backward y holgura
Historias:  
H7.1 “Como sistema calculo lateStart/lateFinish.”  
H7.2 “Como sistema calculo holgura (slack) de cada tarea.”  
Entregable: calculateSlack(tasks) + tests.

Sprint 8 – Ruta crítica
Historias:  
H8.1 “Como usuario quiero ver la lista de tareas críticas (slack=0).”  
Entregable: getCriticalPath(tasks) + tests.

Sprint 9 – Hitos (milestones)
Historias:  
H9.1 “Como usuario puedo convertir una tarea en hito (duration=0).”  
H9.2 “Como sistema recalculo fechas cuando muevo un hito.”  
Entregable: flagMilestone() + tests.

Sprint 10 – Export/import JSON
Historias:  
H10.1 “Como usuario puedo descargar todo el proyecto en JSON.”  
H10.2 “Como usuario puedo importar ese JSON en otra ventana.”  
Entregable: exportProject(), importProject() + tests.

Sprint 11 – Concurrencia offline (opcional nice-to-have)
Historias:  
H11.1 “Como usuario puedo editar en dos pestañas y el último en guardar gana (last-write-wins).”  
Entregable: timestamp de versión en Project + merge simple.

Sprint 12 – Gantt (renderizado mínimo)
Historias:  
H12.1 “Como usuario puedo ver un Gantt básico (divs+CSS) con barras proporcionales a duración.”  
H12.2 “Como usuario puedo ver la ruta crítica en rojo.”  
Backend solo entrega datos: getGanttDataset() → {tasks:[{name,start,end,slack,isCritical}]}  
La UI (ya existente) pinta con HTML+CSS puro.  
Entregable: getGanttDataset() + tests.

---

5. Convenciones de código
- ESModules (<script type="module">).  
- Nada de npm en producción; solo devDependencies para tests.  
- Nombres en español o inglés, pero consistentes (elegir uno).  
- Comentarios JSDoc mínimos.

---

6. Testing
- Framework de test: ni uno. Script node que ejecuta cada función y compara outputs.  
- Cobertura objetivo: 80 % de usecases.

---

7. CI/CD (light)
- GitHub Actions solo para correr tests en push.  
- Build = copiar /src a /dist sin minificar.

---

8. Definición de MVP
El MVP está listo al final del Sprint 8: puedo crear proyecto, tareas, dependencias, calendario, y obtener la ruta crítica sin errores. Los sprints 9-12 son mejoras graduales.

---

9. Riesgos y mitigaciones
- Riesgo: IndexedDB bloqueado en modo privado → mitigación: detectar y avisar al usuario.  
- Riesgo: cálculo de ciclos lento con >10 k tareas → mitigación: límite soft 1 k tareas por proyecto (documentado).

---

10. Glosario rápido
- Tarea = trabajo con duración.  
- Hitos = tareas con duración 0.  
- Ruta crítica = cadena más larga de tareas sin holgura.  
- Calendario = conjunto de días laborables.

---
Estructura de carpetas y archivos – “ProjectZero”  
(raíz del proyecto, sin frameworks, sin build, solo carpetas lógicas)

```
projectzero/
│
├── index.html              ← punto de entrada de la UI (ya existe)
├── css/
│   └── styles.css          ← estilos generales (puedes tocarlo, pero no es backend)
│
└── src/
    ├── infra/              ← todo lo que habla con IndexedDB
    │   ├── idb.js          ← wrapper genérico: openDB, upgrade, tx helpers
    │   ├── projectRepo.js  ← CRUD proyectos
    │   ├── taskRepo.js     ← CRUD tareas
    │   └── calendarRepo.js ← CRUD calendarios (días laborables, festivos)
    │
    ├── core/               ← entidades y value-objects (sin dependencias)
    │   ├── Project.js      ← clase Project + validaciones
    │   ├── Task.js         ← clase Task + validaciones
    │   ├── Milestone.js    ← extiende Task con duration = 0
    │   ├── DateRange.js    ← value-object (start, end)
    │   ├── Duration.js     ← value-object (días enteros)
    │   ├── Calendar.js     ← value-object (workingDays[], holidays[])
    │   └── errors.js       ← DomainError, CycleError, etc.
    │
    ├── usecases/           ← casos de uso orquestados (entrada/salida plana)
    │   ├── project/
    │   │   ├── createProject.js
    │   │   ├── listProjects.js
    │   │   ├── deleteProject.js
    │   │   └── exportProject.js
    │   ├── task/
    │   │   ├── createTask.js
    │   │   ├── updateTask.js
    │   │   ├── deleteTask.js
    │   │   ├── setPredecessors.js
    │   │   └── toggleMilestone.js
    │   ├── calendar/
    │   │   ├── setWorkingDays.js
    │   │   └── addHoliday.js
    │   └── scheduler/
    │       ├── calculateDates.js      ← forward + backward
    │       ├── getCriticalPath.js
    │       └── getGanttDataset.js     ← última capa antes de la UI
    │
    ├── api/                  ← adaptador entre UI existente y usecases
    │   └── crudAdapter.js    ← expone funciones que ya invoca tu API CRUD
    │
    └── service-worker.js     ← opcional: cache y sync offline
```

Carpetas de desarrollo (no se despliegan)
```
tests/
│  ├── infra/
│  ├── core/
│  └── usecases/
└── eslint.config.js
```

Notas:
1. Cada archivo es un módulo ES (`export`/`import`).  
2. Nada de bundlers; el navegador carga los módulos directamente.  
3. La UI existente solo importa `src/api/crudAdapter.js` y éste, a su vez, los usecases necesarios.

---

Aplico el principio “clase solo cuando el estado se beneficié de encapsulación o polimorfismo; lo demás funciones puras”.  
A continuación indico, archivo por archivo, qué elementos son **funciones puras** (sin efectos laterales, mismo input → mismo output) y qué elementos forman **clases** (encapsulan estado y comportamiento).

---

### INFRA (efectos laterales → nunca puro)
- `idb.js`  
  – `openDB()` → promesa con side-effect (abre BD).  
  – `txRead()`, `txWrite()` → side-effects.  
  – **Ninguna función pura**; son “gateways”.

- `projectRepo.js`, `taskRepo.js`, `calendarRepo.js`  
  – Todas las exportaciones son `async` y tocan IndexedDB → **no puras**.

---

### CORE (modelo de dominio)

| Archivo | ¿Clase? | ¿Función pura? | Razón |
|---|---|---|---|
| `Project.js` | `class Project` | – | encapsula `id, name, createdAt` y valida invariantes internas. |
| `Task.js` | `class Task` | – | encapsula lógica de duración, fechas, predecesores. |
| `Milestone.js` | `class Milestone extends Task` | – | especializa con `duration = 0`. |
| `DateRange.js` | `class DateRange` | – | mantiene invariante `end ≥ start`. |
| `Duration.js` | `class Duration` | – | envuelve `value` y valida positivo. |
| `Calendar.js` | `class Calendar` | – | encapsula arreglos `workingDays` y `holidays` y ofrece `isWorkingDay(date)`. |
| `errors.js` | – | `DomainError(msg)`, `CycleError(path[])` | simples fábricas de errores; no mantienen estado propio. |

---

### USECASES (lógica de aplicación)

| Archivo | ¿Clase? | ¿Función pura? | Observación |
|---|---|---|---|
| `createProject.js` | – | `createProject(data) → Project` | **pura**: solo instancia y valida; **no** toca repo. |
| `listProjects.js` | – | `async listProjects() → Project[]` | **impura**: lee IndexedDB. |
| `deleteProject.js` | – | `async deleteProject(id)` | **impura**: borra. |
| `exportProject.js` | – | `exportProject(project, tasks[]) → JSON` | **pura**: serializa. |
| `createTask.js` | – | `createTask(dto) → Task` | **pura**: solo crea objeto. |
| `updateTask.js` | – | `async updateTask(id, patch)` | **impura**: repo. |
| `deleteTask.js` | – | `async deleteTask(id)` | **impura**: repo. |
| `setPredecessors.js` | – | `setPredecessors(taskId, predIds[], allTasks[]) → Task[]` | **pura**: valida ciclos y devuelve array nuevo; repo se invoca después. |
| `toggleMilestone.js` | – | `toggleMilestone(task) → Task` | **pura**: devuelve nueva instancia Milestone o Task. |
| `setWorkingDays.js` | – | `setWorkingDays(calendar, days[]) → Calendar` | **pura**: devuelve nuevo Calendar. |
| `addHoliday.js` | – | `addHoliday(calendar, date) → Calendar` | **pura**: mismo patrón inmutable. |
| `calculateDates.js` | – | `calculateDates(tasks[], calendar) → Task[]` | **pura**: algoritmo F–B completo; devuelve array nuevo. |
| `getCriticalPath.js` | – | `getCriticalPath(tasks[]) → Task[]` | **pura**: filtra slack=0. |
| `getGanttDataset.js` | – | `getGanttDataset(tasks[]) → plainObjects[]` | **pura**: mapea a DTO plano para la UI. |

---

### API / ADAPTADOR
- `crudAdapter.js`  
  – Exporta funciones `async` que orquestan usecases + repos → **impuras**.  
  – Ejemplo: `handleCreateTask(req) → Response`.

---

### SERVICE-WORKER
- Event listeners → **impuros por definición**.

---

### Regla rápida de oro
1. ¿Toca IndexedDB, localStorage, fetch, DOM, Date.now()? → **Impura** (clase o función async).  
2. ¿Solo transforma datos de entrada y devuelve nuevos objetos? → **Función pura** (fuera de cualquier clase).  
3. ¿Encapsula invariantes o polimorfismo? → **Clase** (Task, Calendar, …).

---

Tabla de 20 tareas (A-T) con duraciones PERT, predecesoras, tipos de relación, lag, horas de trabajo y costo estimado.  
(Duraciones en días; O=optimista, M=más probable, P=pesimista → Esperada = (O+4M+P)/6)

| ID | Nombre | O | M | P | Esperada (d) | Predecesoras | Tipo | Lag (d) | Horas trabajo | Costo USD |
|----|--------|---|---|---|--------------|--------------|------|---------|---------------|-----------|
| A  | Inicio | 1 | 2 | 3 | 2            | —            | —    | —       | 16            | 800       |
| B  | Análisis | 2 | 3 | 6 | 3.33         | A            | FS   | 0       | 32            | 1 600     |
| C  | Diseño BD | 3 | 5 | 9 | 5.33         | B            | FS   | 0       | 48            | 2 400     |
| D  | Mock-ups | 2 | 4 | 7 | 4.17         | B            | SS   | 1       | 40            | 2 000     |
| E  | Servicios | 4 | 6 | 10 | 6.33        | C            | FS   | 0       | 56            | 2 800     |
| F  | Front core | 3 | 5 | 8 | 5.17        | D            | FS   | 0       | 48            | 2 400     |
| G  | Infra cloud | 2 | 3 | 5 | 3.17        | C            | FS   | 2       | 24            | 1 200     |
| H  | Seguridad | 1 | 2 | 4 | 2.17         | E            | FS   | 0       | 20            | 1 000     |
| I  | Testing unit | 2 | 3 | 5 | 3.17       | F            | FS   | 0       | 32            | 1 600     |
| J  | Integración | 3 | 4 | 6 | 4.17        | E, G         | FS   | 0       | 40            | 2 000     |
| K  | Ajustes UI | 1 | 2 | 3 | 2            | I            | FF   | 0       | 16            | 800       |
| L  | Performance | 2 | 4 | 7 | 4.17       | J            | FS   | 0       | 40            | 2 000     |
| M  | Doc técnica | 1 | 3 | 5 | 3            | H            | FS   | 1       | 24            | 1 200     |
| N  | Capacitación | 2 | 3 | 6 | 3.33       | K, M         | FS   | 0       | 32            | 1 600     |
| O  | Data migra | 3 | 5 | 9 | 5.33         | L            | FS   | 0       | 48            | 2 400     |
| P  | Pilotaje | 2 | 4 | 6 | 4              | O            | SS   | 0       | 40            | 2 000     |
| Q  | Ajustes pilot | 1 | 2 | 4 | 2.17       | P            | FS   | 0       | 20            | 1 000     |
| R  | Marketing | 2 | 3 | 5 | 3.17          | N            | FS   | 0       | 32            | 1 600     |
| S  | Go-live | 1 | 1 | 2 | 1.17            | Q, R         | FS   | 0       | 12            | 600       |
| T  | Soporte inicial | 3 | 5 | 8 | 5.17     | S            | FF   | 0       | 48            | 2 400     |

Relaciones especiales  
- SS: tareas D (SS+1d) y P (SS+0d)  
- FF: tareas K (FF+0d) y T (FF+0d)  

Lags totales usados: 4  
(B→D lag 1 d; C→G lag 2 d; H→M lag 1 d; D→F lag implícito 0, solo tipo SS)