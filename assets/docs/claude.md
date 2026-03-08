/**
 * ========================================
 * PROYECTO DE 20 TAREAS - ESPECIFICACIONES
 * ========================================
 */

/**
 * ESTRUCTURA DE TAREAS
 * ====================
 * 
 * Total: 20 tareas (A-T)
 * Duración: 1-14 días por tarea
 * Relaciones: FS (mayormente), FF (2), SS (2)
 * Lags: 4 relaciones con posposición
 */

/**
 * TABLA DE TAREAS
 * ================
 */

const tasks = [
    {
        code: 'A',
        name: 'Análisis de requisitos',
        optimistic: 3,
        mostLikely: 5,
        pessimistic: 9,
        predecessors: '',  // Sin predecesoras
        work: 40,
        cost: 4000
    },
    {
        code: 'B',
        name: 'Diseño de arquitectura',
        optimistic: 4,
        mostLikely: 6,
        pessimistic: 10,
        predecessors: 'A',  // FS (Finish-Start) - implícito
        work: 48,
        cost: 5500
    },
    {
        code: 'C',
        name: 'Configuración de infraestructura',
        optimistic: 2,
        mostLikely: 3,
        pessimistic: 6,
        predecessors: 'A',  // FS
        work: 24,
        cost: 3000
    },
    {
        code: 'D',
        name: 'Desarrollo módulo autenticación',
        optimistic: 5,
        mostLikely: 8,
        pessimistic: 13,
        predecessors: 'B',  // FS
        work: 64,
        cost: 7200
    },
    {
        code: 'E',
        name: 'Desarrollo módulo usuarios',
        optimistic: 4,
        mostLikely: 7,
        pessimistic: 11,
        predecessors: 'B',  // FS
        work: 56,
        cost: 6400
    },
    {
        code: 'F',
        name: 'Diseño de base de datos',
        optimistic: 3,
        mostLikely: 5,
        pessimistic: 8,
        predecessors: 'B',  // FS
        work: 40,
        cost: 4500
    },
    {
        code: 'G',
        name: 'Implementación base de datos',
        optimistic: 2,
        mostLikely: 4,
        pessimistic: 7,
        predecessors: 'F',  // FS
        work: 32,
        cost: 3600
    },
    {
        code: 'H',
        name: 'Desarrollo API REST',
        optimistic: 6,
        mostLikely: 9,
        pessimistic: 14,
        predecessors: 'D;E;G',  // Múltiples predecesoras FS
        work: 72,
        cost: 8000
    },
    {
        code: 'I',
        name: 'Desarrollo frontend inicial',
        optimistic: 5,
        mostLikely: 7,
        pessimistic: 11,
        predecessors: 'C+3d',  // FS con LAG de +3 días
        work: 56,
        cost: 6000
    },
    {
        code: 'J',
        name: 'Integración autenticación frontend',
        optimistic: 3,
        mostLikely: 5,
        pessimistic: 8,
        predecessors: 'D;I',  // Dos predecesoras FS
        work: 40,
        cost: 4800
    },
    {
        code: 'K',
        name: 'Desarrollo dashboard',
        optimistic: 4,
        mostLikely: 6,
        pessimistic: 10,
        predecessors: 'JSS',  // SS (Start-Start) - K puede comenzar cuando J comienza
        work: 48,
        cost: 5600
    },
    {
        code: 'L',
        name: 'Desarrollo reportes',
        optimistic: 5,
        mostLikely: 8,
        pessimistic: 12,
        predecessors: 'H',  // FS
        work: 64,
        cost: 7000
    },
    {
        code: 'M',
        name: 'Pruebas unitarias backend',
        optimistic: 3,
        mostLikely: 5,
        pessimistic: 9,
        predecessors: 'H',  // FS
        work: 40,
        cost: 4200
    },
    {
        code: 'N',
        name: 'Pruebas unitarias frontend',
        optimistic: 3,
        mostLikely: 4,
        pessimistic: 7,
        predecessors: 'K',  // FS
        work: 32,
        cost: 3800
    },
    {
        code: 'O',
        name: 'Pruebas de integración',
        optimistic: 4,
        mostLikely: 6,
        pessimistic: 10,
        predecessors: 'M;N',  // Dos predecesoras FS
        work: 48,
        cost: 5400
    },
    {
        code: 'P',
        name: 'Documentación técnica',
        optimistic: 2,
        mostLikely: 3,
        pessimistic: 5,
        predecessors: 'LFF',  // FF (Finish-Finish) - P debe terminar cuando L termine
        work: 24,
        cost: 2800
    },
    {
        code: 'Q',
        name: 'Documentación usuario',
        optimistic: 2,
        mostLikely: 4,
        pessimistic: 7,
        predecessors: 'KFF+2d',  // FF con LAG de +2 días
        work: 32,
        cost: 3200
    },
    {
        code: 'R',
        name: 'Configuración servidor producción',
        optimistic: 3,
        mostLikely: 5,
        pessimistic: 8,
        predecessors: 'C',  // FS
        work: 40,
        cost: 4500
    },
    {
        code: 'S',
        name: 'Despliegue a producción',
        optimistic: 2,
        mostLikely: 3,
        pessimistic: 5,
        predecessors: 'O;R-1d',  // FS con LAG de -1 día en R (puede comenzar 1 día antes que R termine)
        work: 24,
        cost: 2600
    },
    {
        code: 'T',
        name: 'Capacitación usuarios',
        optimistic: 3,
        mostLikely: 5,
        pessimistic: 9,
        predecessors: 'Q;S',  // Dos predecesoras FS
        work: 40,
        cost: 4000
    }
];

/**
 * RESUMEN DE CARACTERÍSTICAS
 * ===========================
 */

const projectSummary = {
    totalTasks: 20,
    
    durationRange: {
        min: 1,
        max: 14,
        note: 'Cada tarea tiene rango suficiente para cambiar ruta crítica'
    },
    
    relationships: {
        FS: 16,  // Finish-Start (mayoría)
        FF: 2,   // Finish-Finish (P y Q)
        SS: 1,   // Start-Start (K)
        total: 19
    },
    
    lags: {
        positive: [
            { task: 'I', predecessor: 'C', lag: '+3d' },
            { task: 'Q', predecessor: 'K', lag: '+2d' }
        ],
        negative: [
            { task: 'S', predecessor: 'R', lag: '-1d' }
        ],
        total: 3  // Solo 3 de las 4 permitidas (puedes agregar una más si lo deseas)
    },
    
    totals: {
        work: 920,    // horas
        cost: 101900  // USD
    }
};

/**
 * RELACIONES ESPECIALES DETALLADAS
 * =================================
 */

/**
 * 1. START-START (SS)
 * -------------------
 * K (Desarrollo dashboard) SS con J (Integración autenticación frontend)
 * - K puede comenzar cuando J comienza
 * - Permite trabajo paralelo
 */

/**
 * 2. FINISH-FINISH (FF)
 * ---------------------
 * P (Documentación técnica) FF con L (Desarrollo reportes)
 * - P debe terminar cuando L termine
 * - Permite que P comience antes pero sincroniza el fin
 * 
 * Q (Documentación usuario) FF+2d con K (Desarrollo dashboard)
 * - Q debe terminar 2 días después que K termine
 * - Incluye LAG positivo
 */

/**
 * 3. LAGS (POSPOSICIÓN/ADELANTO)
 * -------------------------------
 * I depende de C+3d (FS con +3 días)
 * - I debe comenzar 3 días DESPUÉS que C termine
 * - Tiempo de espera/preparación
 * 
 * Q depende de KFF+2d (FF con +2 días)
 * - Q debe terminar 2 días DESPUÉS que K termine
 * - Tiempo adicional para revisión
 * 
 * S depende de R-1d (FS con -1 día)
 * - S puede comenzar 1 día ANTES que R termine
 * - Permite superposición/inicio anticipado
 */

/**
 * RUTAS CRÍTICAS POTENCIALES
 * ===========================
 * 
 * Dependiendo de las duraciones aleatorias, las rutas críticas pueden ser:
 * 
 * Ruta 1: A → B → D → H → L → P
 * Ruta 2: A → B → D → H → M → O → S → T
 * Ruta 3: A → B → E → H → L → P
 * Ruta 4: A → C → I → J → K → N → O → S → T
 * 
 * Los rangos amplios de duración (3-9, 4-10, etc.) aseguran que
 * diferentes simulaciones cambien la ruta crítica.
 */

/**
 * DISTRIBUCIONES PARA MONTE CARLO
 * ================================
 */

const distributions = {
    uniform: 'Distribución uniforme entre optimistic y pessimistic',
    triangular: 'Distribución triangular con mostLikely como moda',
    normal: 'Distribución normal con media en mostLikely',
    pert: 'Distribución PERT (Beta) con parámetro gamma'
};

/**
 * FORMATO PARA IMPORTACIÓN
 * =========================
 * 
 * El archivo CSV generado tiene este formato:
 * code,name,optimistic,mostLikely,pessimistic,predecessors,work,cost
 * 
 * Predecessors notation:
 * - "A" → A con relación FS (implícito)
 * - "AFF" → A con relación FF
 * - "ASS" → A con relación SS
 * - "A+3d" → A con relación FS y lag +3 días
 * - "A;B;C" → Múltiples predecesoras separadas por ;
 */

/**
 * ESTADÍSTICAS ESPERADAS
 * =======================
 */

const expectedStats = {
    projectDuration: {
        optimistic: '~45 días',
        mostLikely: '~65 días',
        pessimistic: '~95 días',
        note: 'Variabilidad alta para análisis de sensibilidad'
    },
    
    criticalTasks: {
        likely: ['A', 'B', 'D', 'H', 'M', 'O', 'S', 'T'],
        note: 'Puede variar significativamente según duraciones aleatorias'
    },
    
    parallelWork: {
        maxConcurrent: 5,
        phases: [
            'Análisis inicial (A)',
            'Desarrollo paralelo (D, E, F, G)',
            'Backend y Frontend paralelo (H, I, J, K)',
            'Pruebas y Documentación paralelo (M, N, P, Q)',
            'Despliegue final (R, S, T)'
        ]
    }
};

console.log('Proyecto generado exitosamente');
console.log('Total tareas:', tasks.length);
console.log('Total trabajo:', projectSummary.totals.work, 'horas');
console.log('Total costo:', projectSummary.totals.cost, 'USD');