# **Elaboración de script de implementación de base de datos**
En esta parte vamos a describir paso a paso como hemos realizado los scripts SQL para la estructura y la inserción de datos en nuestra Base de datos: "programa_transitabilidad".

---
---
---

## _**Implementación de las sentencias para la base de datos**_
1. Creación de la BD: CREATE TABLE programa_transitabilidad;
2. Para poder seleccionar la BD y crear sentencias en ella: USE programa_transitabilidad;
3. Para insertar una tabla utilizamos la sentencia: CREATE TABLE nombre_tabla (datos a insertar);

En este proyecto seguimos el siguiente orden para la creación de tablas según el diseño físico de la base de datos:

**ubigeo → user → institution → representative → institution_representative → student → guardian → student_guardian**

Este orden garantiza que todas las **claves foráneas (FK)** puedan insertarse sin generar errores, ya que primero se crean las tablas base y luego las tablas que dependen de ellas.  
De esta manera evitamos conflictos al momento de registrar información en las tablas transaccionales.

---
---

## _Tablas Maestras_

---
---

### Tabla "ubigeo"
```sql
CREATE TABLE ubigeo (
id_ubigeo CHAR(6) PRIMARY KEY,
departme VARCHAR(80),
province VARCHAR(60),
district VARCHAR(80)
);
```

**Explicación:** Almacena códigos y nombres de departamento/provincia/distrito. id_ubigeo se usa como FK en otras tablas.

---

### Tabla "user"
```sql
CREATE TABLE user (
id_user INT PRIMARY KEY AUTO_INCREMENT,
email VARCHAR(150) NOT NULL,
password VARCHAR(20) NOT NULL,
registration_information BOOLEAN,
event_information BOOLEAN,
information_communication BOOLEAN,
content_information BOOLEAN
);
```

**Explicación:** Usuario del sistema (correo y permisos). Los campos booleanos sirven para permisos o preferencias.

---

### Tabla "institution"
```sql
CREATE TABLE institution (
id_institution INT PRIMARY KEY AUTO_INCREMENT,
name VARCHAR(100),
management VARCHAR(7),
specific_address VARCHAR(100),
ubigeo_id_ubigeo CHAR(6),
teacher_training BOOLEAN,
student_benefits BOOLEAN,
educational_innovation BOOLEAN,
extracurricular_activities BOOLEAN,
another_reason TEXT,
FOREIGN KEY (ubigeo_id_ubigeo) REFERENCES ubigeo(id_ubigeo)
);
```

**Explicación:** Almacena datos de colegios/instituciones; ubigeo_id_ubigeo referencia ubigeo.

---

### Tabla "representative"
```sql
CREATE TABLE representative (
id_representative INT PRIMARY KEY AUTO_INCREMENT,
role VARCHAR(20),
name VARCHAR(60),
last_name VARCHAR(90),
cellphone_contact CHAR(9),
email_contact VARCHAR(150),
user_id_user INT,
FOREIGN KEY (user_id_user) REFERENCES user(id_user)
);
```

**Explicación:** director/coordinador que podrá estar asociado a un user del sistema.

---

### Tabla "institution_representative"
```sql
CREATE TABLE institution_representative (
id_institution_representative INT PRIMARY KEY AUTO_INCREMENT,
institution_id_institution INT,
representative_id_representative INT,
FOREIGN KEY (institution_id_institution) REFERENCES institution(id_institution),
FOREIGN KEY (representative_id_representative) REFERENCES representative(id_representative)
);
```

**Explicación:** Relación N-N (si una institución tiene varios representantes y viceversa).

---

### Tabla "guardian"
```sql
CREATE TABLE guardian (
id_guardian INT PRIMARY KEY AUTO_INCREMENT,
role VARCHAR(20),
name VARCHAR(60),
last_name VARCHAR(90),
email VARCHAR(150),
cellphone_number CHAR(9),
user_id_user INT,
FOREIGN KEY (user_id_user) REFERENCES user(id_user)
);
```

**Explicación:** Datos del apoderado; puede estar asociado a un user.

---
---

## _Tablas Transaccionales_

---
---

### Tabla "student"
```sql
CREATE TABLE student (
id_student INT PRIMARY KEY AUTO_INCREMENT,
name VARCHAR(60),
last_name VARCHAR(90),
degree CHAR(1),
cellphone_number CHAR(9),
type_document CHAR(3),
number_document VARCHAR(15),
specific_address VARCHAR(100),
ubigeo_id_ubigeo CHAR(6),
institution_id_institution INT,
user_id_user INT,
FOREIGN KEY (ubigeo_id_ubigeo) REFERENCES ubigeo(id_ubigeo),
FOREIGN KEY (institution_id_institution) REFERENCES institution(id_institution),
FOREIGN KEY (user_id_user) REFERENCES user(id_user)
);
```

**Explicación:** Datos personales del estudiante. institution_id_institution indica de qué colegio procede.

---

### Tabla "student_guardian"
```sql
CREATE TABLE student_guardian (
id_student_guardian INT PRIMARY KEY AUTO_INCREMENT,
student_id_student INT,
guardian_id_guardian INT,
FOREIGN KEY (student_id_student) REFERENCES student(id_student),
FOREIGN KEY (guardian_id_guardian) REFERENCES guardian(id_guardian)
);
```

**Explicación:** Relación N-N entre estudiantes y apoderados (un apoderado puede tener varios estudiantes y viceversa).

---
---
---

## _**Implementación de sentencias para inserción de registros**_

---
---

## _Tablas MAESTRAS_

---
---

### Tabla "ubigeo"
```sql
INSERT INTO ubigeo (id_ubigeo, departme, province, district) VALUES
('150501','Lima','Cañete','San Vicente'),
('150502','Lima','Cañete','Asia'),
('150503','Lima','Cañete','Calango'),
('150504','Lima','Cañete','Cerro Azul'),
('150505','Lima','Cañete','Coayllo'),
('150506','Lima','Cañete','Chilca'),
('150507','Lima','Cañete','Imperial'),
('150508','Lima','Cañete','Lunahuaná'),
('150509','Lima','Cañete','Mala'),
('150510','Lima','Cañete','Nuevo Imperial'),
('150511','Lima','Cañete','Pacarán'),
('150512','Lima','Cañete','Quilmaná'),
('150513','Lima','Cañete','San Antonio'),
('150514','Lima','Cañete','San Luis'),
('150515','Lima','Cañete','Santa Cruz de Flores'),
('150516','Lima','Cañete','Zúñiga');
```

**Insertamos los 16 distritos de Cañete**

---

### Tabla "user"
```sql
INSERT INTO user (email, password, registration_information, event_information, information_communication, content_information) VALUES
('admin1@vg.edu', '123456', TRUE, TRUE, TRUE, TRUE),
('admin2@vg.edu', '123456', TRUE, TRUE, TRUE, TRUE),
('colegio1@ie.edu', '123456', TRUE, TRUE, TRUE, FALSE),
('colegio2@ie.edu', '123456', TRUE, FALSE, TRUE, FALSE),
('colegio3@ie.edu', '123456', TRUE, TRUE, FALSE, FALSE),
('colegio4@ie.edu', '123456', TRUE, TRUE, TRUE, TRUE),
('colegio5@ie.edu', '123456', FALSE, TRUE, TRUE, TRUE),
('rep1@ie.edu', '123456', TRUE, TRUE, TRUE, FALSE),
('rep2@ie.edu', '123456', TRUE, TRUE, TRUE, FALSE),
('guardian1@familia.com', '123456', TRUE, TRUE, FALSE, FALSE);
```

**Insertamos datos correos únicos, contraseñas sin restricciones extra**

---

### Tabla "institution"
```sql
INSERT INTO institution (name, management, specific_address, ubigeo_id_ubigeo,
teacher_training, student_benefits, educational_innovation, extracurricular_activities, another_reason)
VALUES
('I.E. José Buenaventura Sepúlveda', 'Publica', 'San Vicente de Cañete', '150501', TRUE, TRUE, TRUE, FALSE, 'Desarrollar competencias digitales y creatividad'),
('I.E. Gerardo Salomón Mejía Saco', 'Publica', 'Imperial', '150507', TRUE, TRUE, TRUE, TRUE, 'Fortalecer capacidades tecnológicas y lógicas'),
('I.E. Santa Rita de Casia', 'Publica', 'Nuevo Imperial', '150510', TRUE, TRUE, TRUE, TRUE, 'Preparar a los alumnos para retos tecnológicos'),
('Centro de Mujeres', 'Publica', 'Imperial', '150507', TRUE, TRUE, TRUE, TRUE, 'Promover autonomía tecnológica'),
('I.E. 2243 José Carlos Mariátegui La Chira', 'Publica', 'Quilmaná', '150512', TRUE, TRUE, TRUE, TRUE, 'Desarrollar competencias digitales y vocacionales'),
('I.E. San Luis Gonzaga', 'Publica', 'San Luis', '150514', TRUE, TRUE, FALSE, TRUE, 'Ampliar oportunidades digitales'),
('I.E. Nuestra Señora del Carmen', 'Privada', 'Asia', '150502', TRUE, FALSE, TRUE, FALSE, 'Potenciar el aprendizaje virtual'),
('I.E. San Martín de Porres', 'Publica', 'Mala', '150509', TRUE, TRUE, TRUE, FALSE, 'Motivar a los jóvenes hacia carreras técnicas'),
('I.E. José Olaya', 'Privada', 'Cerro Azul', '150504', TRUE, TRUE, TRUE, TRUE, 'Innovación educativa permanente'),
('I.E. San Pedro Apóstol', 'Publica', 'San Antonio', '150513', FALSE, TRUE, TRUE, FALSE, 'Promover habilidades tecnológicas básicas');
```

**Insertamos las 5 I.E. que tienen convenio con Valle Grande más 5 instituciones educativas**

---

### Tabla "representative"
```sql
INSERT INTO representative (role, name, last_name, cellphone_contact, email_contact, user_id_user) VALUES
('Director','Carlos','Ramírez Díaz','987654321','rep1@ie.edu',8),
('Subdirector','María','Gómez Torres','987654322','rep2@ie.edu',9),
('Coordinador','Luis','Torres Rivas','987654323','rep3@ie.edu',3),
('Director','Ana','Morales Pineda','987654324','rep4@ie.edu',4),
('Coordinador','Pedro','Vargas Soto','987654325','rep5@ie.edu',5),
('Director','Lucía','Huamán Torres','987654326','rep6@ie.edu',6),
('Coordinador','Miguel','Barrios León','987654327','rep7@ie.edu',7),
('Director','Sandra','López Díaz','987654328','rep8@ie.edu',2),
('Subdirector','Pablo','Mendoza Vega','987654329','rep9@ie.edu',1),
('Director','Rosa','Quispe Llona','987654330','rep10@ie.edu',10);
```

**Insertamos representantes para cada institución educativa**

---

### Tabla "institution_representative"
```sql
INSERT INTO institution_representative (institution_id_institution, representative_id_representative) VALUES
(1,1),
(2,2),
(3,3),
(4,4),
(5,5),
(6,6),
(7,7),
(8,8),
(9,9),
(10,10);
```

**Insertamos la relación entre cada I.E. con su respectivo representante**

---

### Tabla "guardian"
```sql
INSERT INTO guardian (role, name, last_name, email, cellphone_number, user_id_user) VALUES
('Madre','Luisa','Gonzales Pérez','madre1@familia.com','921111111',10),
('Padre','Juan','Ramírez Vela','padre1@familia.com','922222222',1),
('Madre','Carmen','Torres Gómez','madre2@familia.com','923333333',2),
('Padre','Miguel','López Ruiz','padre2@familia.com','924444444',3),
('Madre','Julia','Huamán Díaz','madre3@familia.com','925555555',4),
('Padre','Ricardo','Vargas León','padre3@familia.com','926666666',5),
('Madre','Diana','Pineda Torres','madre4@familia.com','927777777',6),
('Padre','Oscar','Mendoza Soto','padre4@familia.com','928888888',7),
('Madre','Maritza','Quispe Llona','madre5@familia.com','929999999',8),
('Padre','Henry','Salazar Huerta','padre5@familia.com','930000000',9);
```

**Datos de apoderados que pueden repetirse con algún estudiante**

---
---

## _Tablas TRANSACCIONALES_

---
---

### Tabla "student"
```sql
INSERT INTO student (name, last_name, degree, cellphone_number, type_document, number_document,
specific_address, ubigeo_id_ubigeo, institution_id_institution, user_id_user)
VALUES
('Ana','Pérez Huamán','5','911111111','DNI','88888801','Av. Lima 123','150501',1,1),
('Luis','Ramírez Torres','4','922222222','DNI','88888802','Jr. Libertad','150507',2,2),
('María','Gómez Pineda','3','933333333','DNI','88888803','Av. Unión','150510',3,3),
('Pedro','Torres Díaz','6','944444444','DNI','88888804','Calle Real','150507',4,4),
('Lucía','Vargas León','1','955555555','CE','CE00001','Av. Grau','150512',5,5),
('Carlos','Soto Huamán','2','966666666','DNI','88888806','Av. Sol','150514',6,6),
('Rosa','Morales Soto','3','977777777','DNI','88888807','Jr. Puno','150502',7,7),
('Jorge','Barrios Vega','4','988888888','CE','CE00002','Los Álamos','150509',8,8),
('Daniela','Quispe Flores','5','999999999','DNI','88888809','Los Cedros','150504',9,9),
('Mateo','Salazar Ruiz','6','900000000','DNI','88888810','Av. Ferrocarril','150513',10,10);
```

**Insertamos estudiantes según las claves foráneas de las tablas maestras**

---

### Tabla "student_guardian"
```sql
INSERT INTO student_guardian (student_id_student, guardian_id_guardian) VALUES
(1,1),
(2,2),
(3,3),
(4,4),
(5,5),
(6,6),
(7,7),
(8,8),
(9,9),
(10,10);
```
**Insertamos la relación entre los estudiantes y sus respectivos tutores**