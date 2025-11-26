#**Elaboración de script de implementación de base de datos**
En esta parte vamos a describir paso a paso como hemos realizado los scripts SQL para la estructura y la inserción de datos en nuestra Base de datos: "programa_transitabilidad".

##_**Implementación de las sentencias para la base de datos**_
1. Creación de la BD: CREATE TABLE programa_transitabilidad;
2. Para poder seleccionar la BD y crear sentencias en ella: USE programa_transitabilidad;
3. Para insertar una tabla utilizamos la sentencia: CREATE TABLE nombre_tabla (datos a insertar);

En este caso utilizamos el siguiente orden para crear las tablas según nuestro diseño físico es el siguiente:

ubigeo → user → institution → representative → institution_representative → student → guardian → student_guardian.

Estamos utilizando este orden para evitar conflictos al insertar las FK en las tablas transaccionales.

##_Tablas Maestras_

###Tabla "ubigeo"
CREATE TABLE ubigeo (
id_ubigeo CHAR(6) PRIMARY KEY,
departme VARCHAR(80),
province VARCHAR(60),
district VARCHAR(80)
);

**Explicación:** Almacena códigos y nombres de departamento/provincia/distrito. id_ubigeo se usa como FK en otras tablas.

###Tabla "user"
CREATE TABLE user (
id_user INT PRIMARY KEY AUTO_INCREMENT,
email VARCHAR(150) NOT NULL,
password VARCHAR(20) NOT NULL,
registration_information BOOLEAN,
event_information BOOLEAN,
information_communication BOOLEAN,
content_information BOOLEAN
);

**Explicación:** Usuario del sistema (correo y permisos). Los campos booleanos sirven para permisos o preferencias.

###Tabla "institution"
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

**Explicación:** Almacena datos de colegios/instituciones; ubigeo_id_ubigeo referencia ubigeo.

###Tabla "representative"
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

**Explicación:** director/coordinador que podrá estar asociado a un user del sistema.

###Tabla "institution_representative"
CREATE TABLE institution_representative (
id_institution_representative INT PRIMARY KEY AUTO_INCREMENT,
institution_id_institution INT,
representative_id_representative INT,
FOREIGN KEY (institution_id_institution) REFERENCES institution(id_institution),
FOREIGN KEY (representative_id_representative) REFERENCES representative(id_representative)
);

**Explicación:** Relación N-N (si una institución tiene varios representantes y viceversa).

###Tabla "guardian"
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

**Explicación:** Datos del apoderado; puede estar asociado a un user.

##_Tablas Transaccionales_

###Tabla "student"
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

**Explicación:** Datos personales del estudiante. institution_id_institution indica de qué colegio procede.

###Tabla "student_guardian"
CREATE TABLE student_guardian (
id_student_guardian INT PRIMARY KEY AUTO_INCREMENT,
student_id_student INT,
guardian_id_guardian INT,
FOREIGN KEY (student_id_student) REFERENCES student(id_student),
FOREIGN KEY (guardian_id_guardian) REFERENCES guardian(id_guardian)
);

**Explicación:** Relación N-N entre estudiantes y apoderados (un apoderado puede tener varios estudiantes y viceversa).

##_**Implementación de sentencias para inserción de registros**_

##_Tablas MAESTRAS_

###Tabla "ubigeo"

###Tabla "user"

###Tabla "institution"

###Tabla "representative"

###Tabla "student"

###Tabla "guardian"


##_Tablas TRANSACCIONALES_

###Tabla "institution_representative"

###Tabla "student_guardian"