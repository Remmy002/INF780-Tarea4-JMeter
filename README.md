# INF780 — Tarea 4: Pruebas de Rendimiento con Apache JMeter

**Asignatura:** INF780 – Verificación y Validación de Software  
**Docente:** M. Sc. Huáscar Fedor Gonzales Guzmán  
**Estudiante:** Clyder Remmy Moreira Quispe  

---

## Descripción

Pruebas de rendimiento (carga, estrés y picos) sobre la API REST `movies-api`
desarrollada en NestJS + TypeORM + PostgreSQL, utilizando Apache JMeter 5.6.3.

---

## Requisitos previos

- Java 17+ instalado
- Node.js 18+
- PostgreSQL corriendo
- Apache JMeter 5.6.3 descomprimido en `C:\jmeter\apache-jmeter-5.6.3`

---

## Levantar la API

```bash
cd INF780-MoviesApi
npm install
npm run start:dev
```

La API queda disponible en `http://localhost:3000`

---

## Estructura del repositorio

```
tarea4/
├── jmeter/
│   ├── smoke.jmx       # Smoke test (1 usuario, 5 loops)
│   ├── carga.jmx       # Prueba de carga (50 usuarios, 10 loops)
│   ├── estres.jmx      # Prueba de estrés (100/200/400 usuarios)
│   ├── picos.jmx       # Prueba de picos (200 usuarios, ramp-up 5s)
│   └── ids.csv         # IDs de películas para GET /movies/{id}
├── resultados/
│   ├── smoke.jtl
│   ├── carga.jtl
│   ├── estres-100.jtl
│   ├── estres-200.jtl
│   ├── estres-400.jtl
│   └── picos.jtl
└── informe/
    └── informe-tarea4.pdf
```

---

## Ejecutar los planes de prueba

### Smoke Test
```bash
jmeter -n -t jmeter/smoke.jmx -l resultados/smoke.jtl -e -o resultados/smoke-report
```

### Prueba de Carga (50 usuarios)
```bash
jmeter -n -t jmeter/carga.jmx -l resultados/carga.jtl -e -o resultados/carga-report
```

### Prueba de Estrés — 100 usuarios
```bash
jmeter -n -t jmeter/estres.jmx -l resultados/estres-100.jtl -e -o resultados/estres-report-100
```

### Prueba de Estrés — 200 usuarios
```bash
jmeter -n -t jmeter/estres.jmx -l resultados/estres-200.jtl -e -o resultados/estres-report-200
```

### Prueba de Estrés — 400 usuarios
```bash
jmeter -n -t jmeter/estres.jmx -l resultados/estres-400.jtl -e -o resultados/estres-report-400
```

### Prueba de Picos (200 usuarios, ramp-up 5s)
```bash
jmeter -n -t jmeter/picos.jmx -l resultados/picos.jtl -e -o resultados/picos-report
```

---

## Resultados resumen

| Escenario  | Usuarios | Throughput | Promedio (ms) | Error %  |
|------------|----------|------------|----------------|----------|
| Smoke      | 1        | 4.9/s      | 67             | 0.00%    |
| Carga      | 50       | 31.2/s     | 330            | 0.00%    |
| Estrés 100 | 100      | 24.7/s     | 2568           | 0.00%    |
| Estrés 200 | 200      | 12.2/s     | 13102          | 0.78%    |
| Estrés 400 | 400      | 10.1/s     | 38076          | 2.62%    |
| Picos      | 200      | 6.6/s      | 28594          | 99.33%*  |

*El alto error% en picos es causado por la Duration Assertion de 800ms.

---

## Punto de saturación

Identificado entre **100 y 200 usuarios concurrentes**. A partir de 200 usuarios
los tiempos superan los 13 segundos y comienzan a aparecer errores HTTP.