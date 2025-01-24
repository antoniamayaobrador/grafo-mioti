# Proyecto de Grafo en Neo4j para MIOTI

Este proyecto consiste en el diseño y desarrollo de un grafo en **Neo4j**, mapeando las relaciones principales dentro de la escuela MIOTI. El objetivo principal es estructurar y analizar las conexiones entre las distintas entidades (alumnos, profesores, asignaturas, aulas, etc.) para ofrecer una visión completa del ecosistema académico.

---

## Diseño del Grafo

### Nodos:
Los principales nodos diseñados para este grafo incluyen:
- **Alumnos**: Representan a los estudiantes de los distintos programas.
- **Profesores**: Relacionados con las asignaturas que imparten.
- **Asignaturas**: Materias que se cursan en los diferentes programas.
- **Aulas**: Espacios físicos donde se imparten las asignaturas.
- **Programas (Másters)**: Programas de estudio disponibles en la escuela.
- **Empleados No Docentes**: Personal administrativo y operativo.
- **Instalaciones**: Recursos adicionales como biblioteca y parking.
- **Escuela MIOTI**: Nodo central que conecta las entidades principales.

### Relaciones:
Las relaciones definidas en el grafo incluyen:
- **COMPAÑERO_DE**: Relación entre alumnos del mismo programa.
- **CURSA**: Conexión entre alumnos y las asignaturas que cursan.
- **DIRIGE**: Relación de la directora con la escuela.
- **ENSEÑA**: Vincula profesores con las asignaturas que imparten.
- **EN_EL_PROGRAMA_DE**: Relación entre asignaturas y programas.
- **ESTUDIA**: Indica el programa al que pertenece cada alumno.
- **USA**: Relación entre usuarios (alumnos y profesores) e instalaciones como biblioteca o parking.
- **SE_IMPARTE_EN**: Relación entre asignaturas y las aulas asignadas.
- **TRABAJA_PARA**: Relación del personal no docente con la escuela.

---

# MIOTI - Análisis de Relaciones con Neo4j

## Creación de Datos

### Alumnos
```cypher
CREATE (alumno1:Alumno {nombre: 'Ana López'}), 
       (alumno2:Alumno {nombre: 'Carlos García'}); 
MATCH (m:Master {nombre: 'Master en Deep Learning'}) 
MERGE (alumno1)-[:ESTUDIA]->(m), (alumno2)-[:ESTUDIA]->(m);
```

### Relaciones entre Alumnos
```cypher
MATCH (a1:Alumno)-[:ESTUDIA]->(m:Master {nombre: 'Master en Deep Learning'}),
      (a2:Alumno)-[:ESTUDIA]->(m) 
WHERE a1 <> a2 
MERGE (a1)-[:COMPAÑERO_DE]->(a2);
```

### Profesores
```cypher
CREATE (prof1:Profesor {nombre: 'Adrián De La Iglesia'}); 
MATCH (p:Profesor {nombre: 'Adrián De La Iglesia'}),
      (a:Asignatura {nombre: 'Deep Learning'}) 
MERGE (p)-[:ENSEÑA]->(a);
```

### Instalaciones
```cypher
CREATE (instalacion:Instalacion {nombre: 'Biblioteca'}); 
MATCH (alumno:Alumno {nombre: 'Ana López'}) 
MERGE (alumno)-[:USA]->(instalacion);
```

## Consultas

Aquí se incluyen algunos ejemplos de consultas que sirven para obtener insights del grafo realizado. 

### Usuarios de Biblioteca
```cypher
MATCH (n)-[:USA]->(i:Instalacion {nombre: 'Biblioteca'}) 
RETURN n.nombre AS Usuario;
```

### Relaciones de Alumno
```cypher
MATCH (a:Alumno {nombre: 'Laura Sánchez'})-[r]-(n) 
RETURN TYPE(r) AS Relacion, n.nombre AS Nodo;
```

### Profesores y Asignaturas
```cypher
MATCH (m:Master)-[:EN_EL_PROGRAMA_DE]->(a:Asignatura)<-[:ENSEÑA]-(p:Profesor) 
RETURN m, a, p;
```

### Relaciones con Instalaciones
```cypher
MATCH (i:Instalacion)<-[:USA]-(u) 
RETURN i, u;
```

### Grafo Completo
```cypher
MATCH (n)-[r]->(m) 
RETURN n, r, m;
```

## Observaciones
- Análisis de interacciones entre actores y entidades de MIOTI
- Monitoreo del uso de instalaciones y aulas
- Base para análisis avanzados del contexto académico

## Requisitos
- Neo4j
- Cypher

## Desarrollo Futuro
1. Expandir dataset con más datos de alumnos, profesores y asignaturas
2. Implementar análisis de centralidad y comunidades
3. Integrar con interfaces gráficas para consultas en tiempo real
