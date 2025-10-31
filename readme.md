# Guía Completa para Construir el Sistema de Tutorías con PHP y MySQL

Esta guía proporciona un tutorial paso a paso para construir un sistema completo de tutorías utilizando PHP y MySQL. El sistema permite a los usuarios registrarse, iniciar sesión, registrarse como tutores, solicitar tutorías, y a los administradores gestionar usuarios, asignaturas y solicitudes. Se basa en el patrón MVC (Modelo-Vista-Controlador) para una estructura organizada y mantenible.

## ¿Qué es este Sistema?

Este es un sistema web de tutorías que incluye:

- **Autenticación de Usuarios**: Registro, login y logout con roles (usuario normal y administrador).
- **Gestión de Tutores**: Los usuarios pueden registrarse como tutores con asignaturas específicas.
- **Solicitudes de Tutoría**: Los usuarios pueden solicitar tutorías a tutores en asignaturas específicas.
- **Panel de Administración**: Los administradores pueden gestionar usuarios, asignaturas y solicitudes.
- **Gestión de Perfiles**: Edición de perfil con subida de fotos.
- **Seguridad**: Contraseñas hasheadas, validación de sesiones y permisos por roles.

## Estructura del Proyecto

- `index.php`: Punto de entrada principal, maneja el enrutamiento.
- `config.php`: Configuración de la base de datos.
- `models/`: Modelos para interactuar con la base de datos.
- `controllers/`: Controladores para manejar la lógica de negocio.
- `views/`: Vistas para la presentación de datos.
- `images/`: Carpeta para almacenar fotos de perfil de usuarios.
- `tutoriaApp.sql`: Script SQL para crear la base de datos.

## Tecnologías Utilizadas

- PHP 7+
- MySQL/MariaDB
- HTML/CSS (básico)
- PDO para conexiones a base de datos

## Instrucciones de Instalación

1. Importar el archivo `tutoriaApp.sql` en MySQL para crear la base de datos.
2. Configurar la conexión a la base de datos en `config.php`.
3. Crear la carpeta `images/` en la raíz del proyecto para almacenar fotos de perfil.
4. Ejecutar el servidor web (ej. Apache) y acceder a `index.php`.

## Próximas Mejoras

- Implementar un sistema de notificaciones.
- Agregar validación de formularios en el lado del cliente.
- Mejorar el diseño de la interfaz con CSS y JavaScript.
- Implementar paginación en listas largas.
- Agregar funcionalidad de búsqueda y filtros.
- Eliminar fotos antiguas al actualizar perfil para liberar espacio.

### Creación de la Base de Datos en MySQL (usando script SQL)
Estando en PHPMYADMIN, crea una nueva base de datos llamada tutoriaapp, una vez creada la base, haz clic en ella e ir a la solapa SQL, en la consola, pegar el siguiente código y hacer clic al botón Continuar:

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;
-- ----------------------------
-- Table structure for asignaturas
-- ----------------------------
DROP TABLE IF EXISTS `asignaturas`;
CREATE TABLE `asignaturas`  (
  `id` int(0) NOT NULL AUTO_INCREMENT,
  `nombre` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
  PRIMARY KEY (`id`) USING BTREE,
  UNIQUE INDEX `nombre`(`nombre`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 8 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of asignaturas
-- ----------------------------
INSERT INTO `asignaturas` VALUES (7, 'Ajedrez');
INSERT INTO `asignaturas` VALUES (4, 'Ciencias Naturales');
INSERT INTO `asignaturas` VALUES (6, 'Francés');
INSERT INTO `asignaturas` VALUES (5, 'Historia');
INSERT INTO `asignaturas` VALUES (2, 'Lengua y Literatura');
INSERT INTO `asignaturas` VALUES (1, 'Matemáticas');

-- ----------------------------
-- Table structure for solicitudes
-- ----------------------------
DROP TABLE IF EXISTS `solicitudes`;
CREATE TABLE `solicitudes`  (
  `id` int(0) NOT NULL AUTO_INCREMENT,
  `user_id` int(0) NOT NULL,
  `tutor_id` int(0) NOT NULL,
  `asignatura_id` int(0) NOT NULL,
  `estado` enum('pendiente','aceptada','rechazada') CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT 'pendiente',
  `fecha_solicitud` timestamp(0) NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `user_id`(`user_id`) USING BTREE,
  INDEX `tutor_id`(`tutor_id`) USING BTREE,
  INDEX `asignatura_id`(`asignatura_id`) USING BTREE,
  CONSTRAINT `solicitudes_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `usuarios` (`id`) ON DELETE CASCADE ON UPDATE RESTRICT,
  CONSTRAINT `solicitudes_ibfk_2` FOREIGN KEY (`tutor_id`) REFERENCES `tutores` (`id`) ON DELETE CASCADE ON UPDATE RESTRICT,
  CONSTRAINT `solicitudes_ibfk_3` FOREIGN KEY (`asignatura_id`) REFERENCES `asignaturas` (`id`) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE = InnoDB AUTO_INCREMENT = 8 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of solicitudes
-- ----------------------------
INSERT INTO `solicitudes` VALUES (3, 7, 5, 7, 'pendiente', '2025-10-22 13:44:07');
INSERT INTO `solicitudes` VALUES (4, 11, 5, 7, 'aceptada', '2025-10-22 17:32:44');
INSERT INTO `solicitudes` VALUES (6, 11, 4, 5, 'aceptada', '2025-10-22 21:20:46');
INSERT INTO `solicitudes` VALUES (7, 11, 4, 2, 'aceptada', '2025-10-22 21:21:20');

-- ----------------------------
-- Table structure for tutor_asignatura
-- ----------------------------
DROP TABLE IF EXISTS `tutor_asignatura`;
CREATE TABLE `tutor_asignatura`  (
  `tutor_id` int(0) NOT NULL,
  `asignatura_id` int(0) NOT NULL,
  PRIMARY KEY (`tutor_id`, `asignatura_id`) USING BTREE,
  INDEX `asignatura_id`(`asignatura_id`) USING BTREE,
  CONSTRAINT `tutor_asignatura_ibfk_1` FOREIGN KEY (`tutor_id`) REFERENCES `tutores` (`id`) ON DELETE CASCADE ON UPDATE RESTRICT,
  CONSTRAINT `tutor_asignatura_ibfk_2` FOREIGN KEY (`asignatura_id`) REFERENCES `asignaturas` (`id`) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of tutor_asignatura
-- ----------------------------
INSERT INTO `tutor_asignatura` VALUES (4, 1);
INSERT INTO `tutor_asignatura` VALUES (4, 4);
INSERT INTO `tutor_asignatura` VALUES (4, 5);
INSERT INTO `tutor_asignatura` VALUES (4, 6);
INSERT INTO `tutor_asignatura` VALUES (6, 6);
INSERT INTO `tutor_asignatura` VALUES (5, 7);

-- ----------------------------
-- Table structure for tutores
-- ----------------------------
DROP TABLE IF EXISTS `tutores`;
CREATE TABLE `tutores`  (
  `id` int(0) NOT NULL AUTO_INCREMENT,
  `user_id` int(0) NOT NULL,
  `descripcion` text CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL,
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `user_id`(`user_id`) USING BTREE,
  CONSTRAINT `tutores_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `usuarios` (`id`) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE = InnoDB AUTO_INCREMENT = 7 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of tutores
-- ----------------------------
INSERT INTO `tutores` VALUES (4, 2, 'Soy un libro abiertito');
INSERT INTO `tutores` VALUES (5, 7, 'aprender y enseñar');
INSERT INTO `tutores` VALUES (6, 11, 'francamente');

-- ----------------------------
-- Table structure for usuarios
-- ----------------------------
DROP TABLE IF EXISTS `usuarios`;
CREATE TABLE `usuarios`  (
  `id` int(0) NOT NULL AUTO_INCREMENT,
  `nombre` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
  `pass` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
  `rol` enum('admin','user') CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT 'user',
  `activo` tinyint(1) NULL DEFAULT 1,
  `foto` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT 'images/photo.png',
  PRIMARY KEY (`id`) USING BTREE,
  UNIQUE INDEX `nombre`(`nombre`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 13 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of usuarios
-- ----------------------------
INSERT INTO `usuarios` VALUES (2, 'mariela', '$2y$10$qnBqKVAW0ZQ7S59plYyc4.KgspV89egGS6V9gpIPhWjHUkWOEPeLO', 'user', 1, 'images/yo.jpg');
INSERT INTO `usuarios` VALUES (3, 'juan', '$2y$10$Ru.QuzXVZXXCgLowiodM9ehiI4FBEk6o2SSJAhe8TbeBnMC9rH2Uq', 'user', 1, 'images/photo.png');
INSERT INTO `usuarios` VALUES (5, 'admin', '$2y$10$poKJZaD/Q0Q0.UFdWbqbA.5JGyQjXSdpLYCg7LxrKrbzZaKD/T5A.', 'admin', 1, 'images/photo.png');
INSERT INTO `usuarios` VALUES (6, 'administrador', '$2y$10$ANHAOlNYxe.PDFBE0rgpaeahvVsqY9Xj2ijvC53RB4VDlPT2ctn6a', 'user', 1, 'images/photo.png');
INSERT INTO `usuarios` VALUES (7, 'Pedro', '$2y$10$hT0mZ4CUTpS6ugr16dFjt.aiuK8jjmjVl0GMHJaxpiWpbGgLe2kQ6', 'user', 1, 'images/photo.png');
INSERT INTO `usuarios` VALUES (9, 'pepe', '$2y$10$r39E668YrluhnMSmRpL55Ovvd/lVtddvi3CQtwkfOwvkxZZOY1y8O', 'admin', 1, 'images/photo.png');
INSERT INTO `usuarios` VALUES (10, 'luisa', '$2y$10$gSYJzL4eKtoxtWhk7KkdYOwOrySbo9L4mCVgOKTvc2HzxI8U1.ykK', 'user', 1, 'images/photo.png');
INSERT INTO `usuarios` VALUES (11, 'luli', '$2y$10$lBQWuvCYbQmVJ8hcwgSQKeNy0UrQQHcsjsdPdQ21SA8Qj3XUoEBQi', 'user', 1, 'images/mamiMari.jpg');
INSERT INTO `usuarios` VALUES (12, 'admin2', '$2y$10$zRfv8R7NGdkv5yZLpHdmbO/FFsVYqPEpbItSFoa9tnLZg7BP5JnCK', 'admin', 1, 'images/mamiMari.jpg');

SET FOREIGN_KEY_CHECKS = 1;

### Lo siguiente son las credenciales de algunos de los usuarios creados en la base de datos según su rol:
USERs
contraseña de los usuarios:
juan: 123
mariela: 123

ADMINs
admin: 123
pepe: 123

Admins y Users tienen acceso a funcionalidades diferentes según lo definido en la aplicación.

### Paso 1: Crear el archivo `config.php` en la raíz del proyecto, escribir el siguiente código dentro:
<?php
    $servername = "localhost"; //nombre servidor base de datos
    $username = "root"; //nombre de usuario para la conexion
    $password = ""; //contraseña para la coneccion (vacia por defecto en desarrollo)
    $database = "tutoriaApp"; //nombre de la base de datos

    try {
        //crear una nueva instancia de PDO para la conexion
        $con = new PDO ("mysql:host=$servername;dbname=$database",$username,$password);
        //configurar el PDO pra lanzar excepciones en caso de error
        $con->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        //Linea comentada para depuracion: mostrar msj de conexion exitosa
        // echo "conectado";
    }catch(PDOException $e){
        //en caso de error en la conexion, terminar el script y mostrar el mensaje de error
        die ("Error de conexión: " . $e->getMessage());
    }
?>

## PRUEBA Paso 1: 
2. Crea el archivo `config.php` en la raíz del proyecto con el contenido proporcionado.
3. Crea un archivo de prueba simple llamado `test_db.php` en la raíz con el siguiente contenido para verificar la conexión:

<?php
require_once('config.php');

try {
    $stmt = $con->query("SELECT COUNT(*) as total FROM usuarios");
    $result = $stmt->fetch(PDO::FETCH_ASSOC);
    echo "Conexión exitosa. Total de usuarios: " . $result['total'];
} catch (PDOException $e) {
    echo "Error en la conexión: " . $e->getMessage();
}
?>

4. Accede a test_db.php desde tu navegador (ej. http://localhost/tutoriaapp3/test_db.php). Deberías ver "Conexión exitosa. Total de usuarios: X" donde X es el número de usuarios en la BD.
5. Elimina test_db.php después de la verificación para seguridad.

## Paso 2: Crear los Modelos:
### models/UserModel.php: y escribir el siguiente código.

<?php
/**
 * Modelo de Usuario
 * Maneja las operaciones de base de datos relacionadas con usuarios.
 */

require_once('config.php');

class UserModel {
    private $conn;

    public function __construct() {
        // Obtener la conexión a la base de datos desde config.php
        global $con;
        $this->conn = $con;
    }

    /**
     * Autentica a un usuario con nombre y contraseña.
     * @param string $nombre Nombre de usuario
     * @param string $pass Contraseña
     * @return bool True si la autenticación es exitosa, false en caso contrario
     */
    public function authenticate($nombre, $pass) {
        // Preparar la consulta para obtener el usuario por nombre
        $stmt = $this->conn->prepare("SELECT nombre, pass, activo FROM usuarios WHERE nombre = :nombre");
        $stmt->bindParam(':nombre', $nombre);
        $stmt->execute();
        // Obtener el resultado como un array asociativo
        $user = $stmt->fetch(PDO::FETCH_ASSOC);

        // Verificar que el usuario exista, que la contraseña coincida y que esté activo
        if ($user && password_verify($pass, $user['pass']) && $user['activo'] == 1) {
            return true;
        }
        return false;
    }

    /**
     * Obtiene el rol de un usuario por ID.
     * @param int $userId ID del usuario
     * @return string|null Rol del usuario o null si no existe
     */
    public function getUserRole($userId) {
        $stmt = $this->conn->prepare("SELECT rol FROM usuarios WHERE id = :id");
        $stmt->bindParam(':id', $userId);
        $stmt->execute();
        $user = $stmt->fetch(PDO::FETCH_ASSOC);
        return $user ? $user['rol'] : null;
    }

    public function getUserId($nombre) {
        $stmt = $this->conn->prepare("SELECT id FROM usuarios WHERE nombre = :nombre");
        $stmt->bindParam(':nombre', $nombre);
        $stmt->execute();
        $user = $stmt->fetch(PDO::FETCH_ASSOC);
        return $user ? $user['id'] : null;
        }

    /**
     * Registra un nuevo usuario.
     * @param string $nombre Nombre de usuario
     * @param string $pass Contraseña
     * @param string $foto Ruta de la foto (opcional)
     * @return bool True si el registro es exitoso, false si el usuario ya existe
     */
    public function register($nombre, $pass, $foto = 'images/photo.png') {
        // Verificar si el usuario ya existe en la base de datos
        $stmt = $this->conn->prepare("SELECT id FROM usuarios WHERE nombre = :nombre");
        $stmt->bindParam(':nombre', $nombre);
        $stmt->execute();

        // Si ya existe, retornar false
        if ($stmt->rowCount() > 0) {
            return false; // Usuario ya existe
        }

        // Encriptar la contraseña antes de guardarla
        $hashedPass = password_hash($pass, PASSWORD_DEFAULT);
        // Preparar la consulta para insertar el nuevo usuario
        $stmt = $this->conn->prepare("INSERT INTO usuarios (nombre, pass, foto) VALUES (:nombre, :pass, :foto)");
        $stmt->bindParam(':nombre', $nombre);
        $stmt->bindParam(':pass', $hashedPass);
        $stmt->bindParam(':foto', $foto);
        $stmt->execute();

        return true;
    }

    /**
     * Registra un nuevo usuario con rol.
     * @param string $nombre Nombre del usuario
     * @param string $pass Contraseña (ya hasheada)
     * @param string $rol Rol del usuario ('admin' o 'user')
     * @param string $foto Ruta de la foto (opcional)
     * @return bool True si se registró correctamente
     */
    public function registerWithRole($nombre, $pass, $rol, $foto = 'images/photo.png') {
        // Verificar si ya existe
        $stmt = $this->conn->prepare("SELECT id FROM usuarios WHERE nombre = :nombre");
        $stmt->bindParam(':nombre', $nombre);
        $stmt->execute();
        if ($stmt->rowCount() > 0) {
            return false; // Ya existe
        }

        // Insertar nuevo usuario
        $stmt = $this->conn->prepare("INSERT INTO usuarios (nombre, pass, rol, foto) VALUES (:nombre, :pass, :rol, :foto)");
        $stmt->bindParam(':nombre', $nombre);
        $stmt->bindParam(':pass', $pass);
        $stmt->bindParam(':rol', $rol);
        $stmt->bindParam(':foto', $foto);
        $stmt->execute();
        return true;
    }

    /**
     * Obtiene todos los usuarios con su rol y si son tutores.
     * @return array Lista de usuarios
     */
    public function getAllUsers() {
        $stmt = $this->conn->prepare("
            SELECT u.id, u.nombre, u.rol, u.activo, u.foto, CASE WHEN t.id IS NOT NULL THEN 'Sí' ELSE 'No' END AS es_tutor
            FROM usuarios u
            LEFT JOIN tutores t ON u.id = t.user_id
            ORDER BY u.nombre
        ");
        $stmt->execute();
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
    }

    /**
     * Elimina un usuario por ID.
     * @param int $userId ID del usuario
     * @return bool True si se eliminó correctamente
     */
    public function deleteUser($userId) {
        // Primero eliminar registros relacionados en tutores y tutor_asignatura
        $stmt = $this->conn->prepare("DELETE FROM tutor_asignatura WHERE tutor_id IN (SELECT id FROM tutores WHERE user_id = :user_id)");
        $stmt->bindParam(':user_id', $userId);
        $stmt->execute();

        $stmt = $this->conn->prepare("DELETE FROM tutores WHERE user_id = :user_id");
        $stmt->bindParam(':user_id', $userId);
        $stmt->execute();

        // Eliminar solicitudes relacionadas
        $stmt = $this->conn->prepare("DELETE FROM solicitudes WHERE user_id = :user_id OR tutor_id IN (SELECT id FROM tutores WHERE user_id = :user_id)");
        $stmt->bindParam(':user_id', $userId);
        $stmt->execute();

        // Finalmente eliminar el usuario
        $stmt = $this->conn->prepare("DELETE FROM usuarios WHERE id = :id");
        $stmt->bindParam(':id', $userId);
        $stmt->execute();

        return $stmt->rowCount() > 0;
    }

    /**
     * Obtiene los datos de un usuario por ID.
     * @param int $userId ID del usuario
     * @return array|null Datos del usuario o null si no existe
     */
    public function getUserById($userId) {
        $stmt = $this->conn->prepare("SELECT id, nombre, rol, activo, foto FROM usuarios WHERE id = :id");
        $stmt->bindParam(':id', $userId);
        $stmt->execute();
        return $stmt->fetch(PDO::FETCH_ASSOC);
    }

    /**
     * Actualiza los datos de un usuario.
     * @param int $userId ID del usuario
     * @param string $nombre Nuevo nombre
     * @param string $pass Nueva contraseña (opcional)
     * @param string $foto Nueva ruta de foto (opcional)
     * @return bool True si se actualizó correctamente
     */
    public function updateUser($userId, $nombre, $pass = null, $foto = null) {
        // Verificar si el nombre ya existe en otro usuario
        $stmt = $this->conn->prepare("SELECT id FROM usuarios WHERE nombre = :nombre AND id != :id");
        $stmt->bindParam(':nombre', $nombre);
        $stmt->bindParam(':id', $userId);
        $stmt->execute();
        if ($stmt->rowCount() > 0) {
            return false; // Nombre ya existe
        }

        if ($pass && $foto) {
            $hashedPass = password_hash($pass, PASSWORD_DEFAULT);
            $stmt = $this->conn->prepare("UPDATE usuarios SET nombre = :nombre, pass = :pass, foto = :foto WHERE id = :id");
            $stmt->bindParam(':nombre', $nombre);
            $stmt->bindParam(':pass', $hashedPass);
            $stmt->bindParam(':foto', $foto);
            $stmt->bindParam(':id', $userId);
        } elseif ($pass) {
            $hashedPass = password_hash($pass, PASSWORD_DEFAULT);
            $stmt = $this->conn->prepare("UPDATE usuarios SET nombre = :nombre, pass = :pass WHERE id = :id");
            $stmt->bindParam(':nombre', $nombre);
            $stmt->bindParam(':pass', $hashedPass);
            $stmt->bindParam(':id', $userId);
        } elseif ($foto) {
            $stmt = $this->conn->prepare("UPDATE usuarios SET nombre = :nombre, foto = :foto WHERE id = :id");
            $stmt->bindParam(':nombre', $nombre);
            $stmt->bindParam(':foto', $foto);
            $stmt->bindParam(':id', $userId);
        } else {
            $stmt = $this->conn->prepare("UPDATE usuarios SET nombre = :nombre WHERE id = :id");
            $stmt->bindParam(':nombre', $nombre);
            $stmt->bindParam(':id', $userId);
        }
        $stmt->execute();
        return true;
    }

    /**
     * Desactiva o activa la cuenta de un usuario.
     * @param int $userId ID del usuario
     * @param bool $activo Estado de la cuenta
     * @return bool True si se actualizó correctamente
     */
    public function toggleUserStatus($userId, $activo) {
        $stmt = $this->conn->prepare("UPDATE usuarios SET activo = :activo WHERE id = :id");
        $stmt->bindParam(':activo', $activo, PDO::PARAM_BOOL);
        $stmt->bindParam(':id', $userId);
        $stmt->execute();
        return true;
    }
}
?>
```

### models/TutorModel.php: Luego de crear el archivo, escribir el siguiente código php en él:

<?php
    require_once ('config.php');

    class TutorModel {
        private $conn;

        public function __construct() {
            global $con;
            $this->conn = $con;
        }
        //obtener todos los tutores con sus asignaturas
        public function getAllTutors () {
            $stmt = $this->conn->prepare("
                SELECT t.id, t.user_id, u.nombre, t.descripcion, GROUP_CONCAT(a.nombre SEPARATOR ', ') AS asignaturas, u.foto
                FROM tutores t
                JOIN usuarios u ON t.user_id = u.id
                LEFT JOIN tutor_asignatura ta ON t.id = ta.tutor_id
                LEFT JOIN asignaturas a ON ta.asignatura_id = a.id
                WHERE u.activo = 1
                GROUP BY t.id
            ");
            $stmt->execute();
            return $stmt->fetchAll (PDO::FETCH_ASSOC);
        }

        //REGISTRAR UN TUTOR
        public function registerTutor($userId, $descripcion, $asignaturas) {
        // Verificar si el usuario ya es tutor
        $stmt = $this->conn->prepare("SELECT id, descripcion FROM tutores WHERE user_id = :user_id");
        $stmt->bindParam(':user_id', $userId);
        $stmt->execute();
        $existingTutor = $stmt->fetch(PDO::FETCH_ASSOC);

        if ($existingTutor) {
            // Si ya es tutor, actualizar descripción y asignaturas
            $tutorId = $existingTutor['id'];

            // Actualizar descripción si cambió
            if ($existingTutor['descripcion'] !== $descripcion) {
                $stmt = $this->conn->prepare("UPDATE tutores SET descripcion = :descripcion WHERE id = :id");
                $stmt->bindParam(':descripcion', $descripcion);
                $stmt->bindParam(':id', $tutorId);
                $stmt->execute();
            }

            // Obtener asignaturas actuales del tutor
            $currentAsignaturas = $this->getTutorAsignaturas($tutorId);
            $currentIds = array_column($currentAsignaturas, 'id');

            // Asignaturas a agregar (en $asignaturas pero no en $currentIds)
            $toAdd = array_diff($asignaturas, $currentIds);

            // Asignaturas a remover (en $currentIds pero no en $asignaturas)
            $toRemove = array_diff($currentIds, $asignaturas);

            // Agregar nuevas asignaturas
            foreach ($toAdd as $asignaturaId) {
                $stmt = $this->conn->prepare("INSERT INTO tutor_asignatura (tutor_id, asignatura_id) VALUES (:tutor_id, :asignatura_id)");
                $stmt->bindParam(':tutor_id', $tutorId);
                $stmt->bindParam(':asignatura_id', $asignaturaId);
                $stmt->execute();
            }

            // Remover asignaturas no seleccionadas
            foreach ($toRemove as $asignaturaId) {
                $stmt = $this->conn->prepare("DELETE FROM tutor_asignatura WHERE tutor_id = :tutor_id AND asignatura_id = :asignatura_id");
                $stmt->bindParam(':tutor_id', $tutorId);
                $stmt->bindParam(':asignatura_id', $asignaturaId);
                $stmt->execute();
            }

            return true;
        } else {
            // Si no es tutor, insertar nuevo tutor
            $stmt = $this->conn->prepare("INSERT INTO tutores (user_id, descripcion) VALUES (:user_id, :descripcion)");
            $stmt->bindParam(':user_id', $userId);
            $stmt->execute();
            $tutorId = $this->conn->lastInsertId();

            // Insertar asignaturas
            foreach ($asignaturas as $asignaturaId) {
                $stmt = $this->conn->prepare("INSERT INTO tutor_asignatura (tutor_id, asignatura_id) VALUES (:tutor_id, :asignatura_id)");
                $stmt->bindParam(':tutor_id', $tutorId);
                $stmt->bindParam(':asignatura_id', $asignaturaId);
                $stmt->execute();
            }
            return true;
        }
        }

    /**
     * Agrega una nueva asignatura a la base de datos.
     * @param string $nombre Nombre de la asignatura
     * @return bool True si se insertó correctamente
     */
    public function addAsignatura($nombre) {
        // Verificar si ya existe
        $stmt = $this->conn->prepare("SELECT id FROM asignaturas WHERE nombre = :nombre");
        $stmt->bindParam(':nombre', $nombre);
        $stmt->execute();
        if ($stmt->rowCount() > 0) {
            return false; // Ya existe
        }

        // Insertar nueva asignatura
        $stmt = $this->conn->prepare("INSERT INTO asignaturas (nombre) VALUES (:nombre)");
        $stmt->bindParam(':nombre', $nombre);
        $stmt->execute();
        return true;
    }


        //Obtener asignaturas disponibles
        public function getAsignaturas(){
            $stmt = $this->conn->prepare("SELECT id, nombre FROM asignaturas");
            $stmt->execute();
            return $stmt->fetchAll (PDO::FETCH_ASSOC);
        }

        //Obtener asignatura por ID
        public function getAsignaturaById($id) {
            $stmt = $this->conn->prepare("SELECT id, nombre FROM asignaturas WHERE id = :id");
            $stmt->bindParam(':id', $id);
            $stmt->execute();
            return $stmt->fetch(PDO::FETCH_ASSOC);
        }

        //Actualizar asignatura
        public function updateAsignatura($id, $nombre) {
            try {
                $stmt = $this->conn->prepare("UPDATE asignaturas SET nombre = :nombre WHERE id = :id");
                $stmt->bindParam(':id', $id);
                $stmt->bindParam(':nombre', $nombre);
                $stmt->execute();
                return $stmt->rowCount() > 0;
            } catch (PDOException $e) {
                return false;
            }
        }

        //Eliminar asignatura
        public function deleteAsignatura($id) {
            $stmt = $this->conn->prepare("DELETE FROM asignaturas WHERE id = :id");
            $stmt->bindParam(':id', $id);
            $stmt->execute();
            return $stmt->rowCount() > 0;
        }

        /**
         * Obtiene las asignaturas de un tutor específico.
         * @param int $tutorId ID del tutor
         * @return array Lista de asignaturas del tutor
         */
        public function getTutorAsignaturas($tutorId) {
            $stmt = $this->conn->prepare("
                SELECT a.id, a.nombre
                FROM asignaturas a
                JOIN tutor_asignatura ta ON a.id = ta.asignatura_id
                WHERE ta.tutor_id = :tutor_id
            ");
            $stmt->bindParam(':tutor_id', $tutorId);
            $stmt->execute();
            return $stmt->fetchAll(PDO::FETCH_ASSOC);
        }

        //Verificar si un usuario es tutor
        public function isTutor($userId) {
            $stmt = $this->conn->prepare("SELECT id FROM tutores WHERE user_id = :user_id");
            $stmt->bindParam(':user_id', $userId);
            $stmt->execute();
            return $stmt->fetch(PDO::FETCH_ASSOC) !== false;
        }

        //Obtener ID del tutor por user_id
        public function getTutorIdByUserId($userId) {
            $stmt = $this->conn->prepare("SELECT id FROM tutores WHERE user_id = :user_id");
            $stmt->bindParam(':user_id', $userId);
            $stmt->execute();
            $result = $stmt->fetch(PDO::FETCH_ASSOC);
            return $result ? $result['id'] : null;
        }

        //Obtener datos del tutor por user_id
        public function getTutorByUserId($userId) {
            $stmt = $this->conn->prepare("SELECT id, descripcion FROM tutores WHERE user_id = :user_id");
            $stmt->bindParam(':user_id', $userId);
            $stmt->execute();
            return $stmt->fetch(PDO::FETCH_ASSOC);
        }
    }
?>

### models/RequestModel.php: luego de crearlo, escribir el siguiente código php:

<?php
    require_once('config.php');

    class RequestModel {
        private $conn;

        public function __construct() {
            global $con;
            $this->conn = $con;
        }

        //crear una solicitud
        public function createRequest ($userId, $tutorId, $asignaturaId) {
            // Verificar si ya existe una solicitud pendiente o aceptada para el mismo usuario, tutor y asignatura
            $stmt = $this->conn->prepare("SELECT id FROM solicitudes WHERE user_id = :user_id AND tutor_id = :tutor_id AND asignatura_id = :asignatura_id AND estado IN ('pendiente', 'aceptada')");
            $stmt->bindParam(':user_id', $userId);
            $stmt->bindParam(':tutor_id', $tutorId);
            $stmt->bindParam(':asignatura_id', $asignaturaId);
            $stmt->execute();
            if ($stmt->rowCount() > 0) {
                return false; // Ya existe una solicitud activa
            }

            $stmt = $this->conn->prepare("INSERT INTO solicitudes (user_id, tutor_id, asignatura_id) VALUES (:user_id, :tutor_id, :asignatura_id)");
            $stmt->bindParam(':user_id', $userId);
            $stmt->bindParam(':tutor_id', $tutorId);
            $stmt->bindParam(':asignatura_id', $asignaturaId);
            $stmt->execute();
            return true;
        }

    /**
     * Acepta una solicitud cambiando su estado a 'aceptada'.
     * @param int $requestId ID de la solicitud
     * @return bool True si se actualizó correctamente
     */
    public function acceptRequest($requestId) {
        $stmt = $this->conn->prepare("UPDATE solicitudes SET estado = 'aceptada' WHERE id = :id");
        $stmt->bindParam(':id', $requestId);
        $stmt->execute();
        return $stmt->rowCount() > 0;
    }

    /**
     * Rechaza una solicitud cambiando su estado a 'rechazada'.
     * @param int $requestId ID de la solicitud
     * @return bool True si se actualizó correctamente
     */
    public function rejectRequest($requestId) {
        $stmt = $this->conn->prepare("UPDATE solicitudes SET estado = 'rechazada' WHERE id = :id");
        $stmt->bindParam(':id', $requestId);
        $stmt->execute();
        return $stmt->rowCount() > 0;
    }

    /**
     * Obtiene todas las solicitudes pendientes para que el admin las revise.
     * @return array Lista de solicitudes pendientes
     */
    public function getPendingRequests() {
        $stmt = $this->conn->prepare("
            SELECT s.id, u.nombre AS usuario, tu.nombre AS tutor, a.nombre AS asignatura, s.fecha_solicitud
            FROM solicitudes s
            JOIN usuarios u ON s.user_id = u.id
            JOIN tutores t ON s.tutor_id = t.id
            JOIN usuarios tu ON t.user_id = tu.id
            JOIN asignaturas a ON s.asignatura_id = a.id
            WHERE s.estado = 'pendiente'
        ");
        $stmt->execute();
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
    }

    /**
     * Obtiene todas las solicitudes aceptadas para que el admin las revise.
     * @return array Lista de solicitudes aceptadas
     */
    public function getAcceptedRequests() {
        $stmt = $this->conn->prepare("
            SELECT s.id, u.nombre AS usuario, tu.nombre AS tutor, a.nombre AS asignatura, s.fecha_solicitud
            FROM solicitudes s
            JOIN usuarios u ON s.user_id = u.id
            JOIN tutores t ON s.tutor_id = t.id
            JOIN usuarios tu ON t.user_id = tu.id
            JOIN asignaturas a ON s.asignatura_id = a.id
            WHERE s.estado = 'aceptada'
        ");
        $stmt->execute();
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
    }

    /**
     * Obtiene todas las solicitudes rechazadas para que el admin las revise.
     * @return array Lista de solicitudes rechazadas
     */
    public function getRejectedRequests() {
        $stmt = $this->conn->prepare("
            SELECT s.id, u.nombre AS usuario, tu.nombre AS tutor, a.nombre AS asignatura, s.fecha_solicitud
            FROM solicitudes s
            JOIN usuarios u ON s.user_id = u.id
            JOIN tutores t ON s.tutor_id = t.id
            JOIN usuarios tu ON t.user_id = tu.id
            JOIN asignaturas a ON s.asignatura_id = a.id
            WHERE s.estado = 'rechazada'
        ");
        $stmt->execute();
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
    }


        //obtener solicitudes de un usuario
        public function getUserRequests($userId){
            $stmt = $this->conn->prepare("SELECT s.id, u.nombre AS tutor, a.nombre AS asignatura, s.estado, s.fecha_solicitud, TIMESTAMPDIFF(HOUR, s.fecha_solicitud, NOW()) AS horas_transcurridas
                FROM solicitudes s
                JOIN tutores t ON s.tutor_id = t.id
                JOIN usuarios u ON t.user_id = u.id
                JOIN asignaturas a ON s.asignatura_id = a.id
                WHERE s.user_id = :user_id
                ");
            $stmt->bindParam(':user_id', $userId);
            $stmt->execute();
            return $stmt->fetchAll(PDO::FETCH_ASSOC);
        }

        //cancelar solicitud
        public function cancelRequest($requestId, $userId) {
            // Verificar que la solicitud pertenece al usuario y no ha sido aceptada
            $stmt = $this->conn->prepare("SELECT estado, TIMESTAMPDIFF(HOUR, fecha_solicitud, NOW()) AS horas_transcurridas FROM solicitudes WHERE id = :id AND user_id = :user_id");
            $stmt->bindParam(':id', $requestId);
            $stmt->bindParam(':user_id', $userId);
            $stmt->execute();
            $request = $stmt->fetch(PDO::FETCH_ASSOC);

            if (!$request) {
                return false; // Solicitud no encontrada o no pertenece al usuario
            }

            if ($request['estado'] === 'aceptada') {
                return false; // No se puede cancelar una solicitud aceptada
            }

            if ($request['horas_transcurridas'] >= 24) {
                return false; // No se puede cancelar después de 24 horas
            }

            // Cancelar la solicitud (eliminarla)
            $stmt = $this->conn->prepare("DELETE FROM solicitudes WHERE id = :id");
            $stmt->bindParam(':id', $requestId);
            $stmt->execute();
            return true;
        }
    }

## PRUEBA Paso 2: Verificación de Modelos:

1. Crear un archivo `test_models.php` en la raíz del proyecto con el siguiente contenido para probar los modelos:

<?php
require_once('config.php');
require_once('models/UserModel.php');
require_once('models/TutorModel.php');
require_once('models/RequestModel.php');

$userModel = new UserModel();
$tutorModel = new TutorModel();
$requestModel = new RequestModel();

echo "Probando UserModel...<br>";
$users = $userModel->getAllUsers();
echo "Total usuarios: " . count($users) . "<br>";

echo "Probando TutorModel...<br>";
$tutors = $tutorModel->getAllTutors();
echo "Total tutores: " . count($tutors) . "<br>";

echo "Probando RequestModel...<br>";
$pending = $requestModel->getPendingRequests();
echo "Solicitudes pendientes: " . count($pending) . "<br>";

echo "Modelos verificados exitosamente.";
?>

2. Accede a `test_models.php` desde tu navegador (ej. `http://localhost/tutoriaapp3/test_models.php`). Deberías ver los conteos de usuarios, tutores y solicitudes sin errores.
3. Elimina `test_models.php` después de la verificación.

## Paso 3: Crear los Controladores
### controllers/AuthController.php: una vez creado el archivo en la carpeta controllers, escribir el siguiente código php:

<?php
/**
 * Controlador de Autenticación
 * Maneja las acciones relacionadas con login, registro y logout.
 */

require_once('models/UserModel.php');

class AuthController {
    private $userModel;

    public function __construct() {
        // Crear instancia del modelo de usuario para acceder a la base de datos
        $this->userModel = new UserModel();
    }

    /**
     * Maneja el proceso de login.
     */
    public function login() {
        // Iniciar sesión para manejar estado de usuario
        session_start();

        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            // Obtener datos del formulario
            $nombre = $_POST['nombre'];
            $pass = $_POST['pass'];

            // Intentar autenticar usuario
            if ($this->userModel->authenticate($nombre, $pass)) {
                $userId = $this->userModel->getUserId($nombre);
                $user = $this->userModel->getUserById($userId);
                if (!$user['activo']) {
                    $_SESSION['login_error'] = "Tu cuenta está desactivada. Contacta al administrador para reactivarla.";
                    header("Location: index.php?action=home");
                    exit();
                }
                $_SESSION['nombre'] = $nombre;
                $_SESSION['user_id'] = $userId;
                /*****************AGREGADO**************************/
                $_SESSION['rol'] = $this->userModel->getUserRole($_SESSION['user_id']);
                /***************************************************/
                header("Location: index.php?action=home");
                exit();
            } else {
                // Si falla, mostrar mensaje de error y recargar vista home
                $_SESSION['login_error'] = "Usuario o contraseña incorrectos.";
                header("Location: index.php?action=home");
                exit();
            }
        } else {
            // Si no es POST, mostrar formulario login
            include('views/login.php');
        }
    }

    /**
     * Maneja el proceso de registro.
     */
    public function register() {
        // Iniciar sesión para manejar estado de usuario
        session_start();

        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            // Obtener datos del formulario
            $nombre = $_POST['nombre'];
            $pass = $_POST['pass'];

            // Manejar subida de foto
            $foto = 'images/photo.png'; // Imagen por defecto
            if (isset($_FILES['foto']) && $_FILES['foto']['error'] == 0) {
                $targetDir = "images/";
                $fileName = basename($_FILES["foto"]["name"]);
                $targetFile = $targetDir . $fileName;
                $imageFileType = strtolower(pathinfo($targetFile, PATHINFO_EXTENSION));

                // Verificar si es una imagen real
                $check = getimagesize($_FILES["foto"]["tmp_name"]);
                if ($check !== false) {
                    // Verificar tamaño (máximo 5MB)
                    if ($_FILES["foto"]["size"] <= 5000000) {
                        // Permitir ciertos formatos
                        if (in_array($imageFileType, ['jpg', 'png', 'jpeg', 'gif'])) {
                            if (move_uploaded_file($_FILES["foto"]["tmp_name"], $targetFile)) {
                                $foto = $targetFile;
                            }
                        }
                    }
                }
            }

            // Intentar registrar usuario
            if ($this->userModel->register($nombre, $pass, $foto)) {
                // Si es exitoso, mostrar mensaje de éxito y redirigir a home
                $_SESSION['register_success'] = "Registro exitoso. Puede iniciar sesión.";
                header("Location: index.php?action=home");
                exit();
            } else {
                // Si falla, mostrar mensaje de error y redirigir a register
                $_SESSION['register_error'] = "El usuario ya existe. Intente con otro nombre de usuario.";
                header("Location: index.php?action=register");
                exit();
            }
        } else {
            // Si no es POST, mostrar formulario registro con lista de tutores
            require_once('models/TutorModel.php');
            $tutorModel = new TutorModel();
            $tutors = $tutorModel->getAllTutors();
            include('views/register.php');
        }
    }

    /**
     * Maneja el logout.
     */
    public function logout() {
        // Iniciar sesión para destruirla
        session_start();
        // Destruir sesión para cerrar sesión del usuario
        session_destroy();
        // Redirigir a home
        header("Location: index.php?action=home");
        exit();
    }

    /**
     * Muestra el formulario para editar perfil.
     */
    public function editProfileForm() {
        session_start();
        if (!isset($_SESSION['user_id'])) {
            header("Location: index.php?action=login");
            exit();
        }

        $user = $this->userModel->getUserById($_SESSION['user_id']);
        include('views/edit_profile.php');
    }

    /**
     * Procesa la actualización del perfil.
     */
    public function editProfile() {
        session_start();
        if (!isset($_SESSION['user_id'])) {
            header("Location: index.php?action=login");
            exit();
        }

        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            $nombre = $_POST['nombre'];
            $pass = !empty($_POST['pass']) ? $_POST['pass'] : null;

            // Manejar subida de foto
            $foto = null;
            if (isset($_FILES['foto']) && $_FILES['foto']['error'] == 0) {
                $targetDir = "images/";
                $fileName = basename($_FILES["foto"]["name"]);
                $targetFile = $targetDir . $fileName;
                $imageFileType = strtolower(pathinfo($targetFile, PATHINFO_EXTENSION));

                // Verificar si es una imagen real
                $check = getimagesize($_FILES["foto"]["tmp_name"]);
                if ($check !== false) {
                    // Verificar tamaño (máximo 5MB)
                    if ($_FILES["foto"]["size"] <= 5000000) {
                        // Permitir ciertos formatos
                        if (in_array($imageFileType, ['jpg', 'png', 'jpeg', 'gif'])) {
                            if (move_uploaded_file($_FILES["foto"]["tmp_name"], $targetFile)) {
                                $foto = $targetFile;
                            }
                        }
                    }
                }
            }

            if ($this->userModel->updateUser($_SESSION['user_id'], $nombre, $pass, $foto)) {
                $_SESSION['nombre'] = $nombre;
                $_SESSION['profile_success'] = "Perfil actualizado exitosamente.";
            } else {
                $_SESSION['profile_error'] = "Error al actualizar el perfil. El nombre de usuario ya existe.";
            }
            header("Location: index.php?action=edit_profile");
            exit();
        }
    }

    /**
     * Desactiva o activa la cuenta del usuario.
     */
    public function toggleAccountStatus() {
        session_start();
        if (!isset($_SESSION['user_id'])) {
            header("Location: index.php?action=login");
            exit();
        }

        $user = $this->userModel->getUserById($_SESSION['user_id']);
        $newStatus = $user['activo'] ? 0 : 1;
        $this->userModel->toggleUserStatus($_SESSION['user_id'], $newStatus);

        if ($newStatus) {
            $_SESSION['profile_success'] = "Cuenta reactivada exitosamente.";
        } else {
            $_SESSION['profile_success'] = "Cuenta desactivada exitosamente.";
            session_destroy();
            header("Location: index.php?action=home");
            exit();
        }

        header("Location: index.php?action=edit_profile");
        exit();
    }
}
?>

### controllers/HomeController.php: creado el archivo, escribir el siguiente código php:

<?php
require_once('models/TutorModel.php');

class HomeController {
    public function index() {
        /**AGREGADO**************** */
        session_start();
        if (isset($_SESSION['user_id']) && $_SESSION['rol'] === 'admin') {
            // Redirigir a dashboard de admin
            header("Location: index.php?action=admin_dashboard");
            exit();
        }
        $tutorModel = new TutorModel();
        $tutors = $tutorModel->getAllTutors();
        include('views/home.php');
    }
}
?>


### controllers/TutorController.php: creado el archivo, escribir el siguiente código:

<?php
require_once('models/TutorModel.php');
require_once('models/RequestModel.php');
class TutorController {
    private $tutorModel;
    private $requestModel;
    public function __construct() {
        $this->tutorModel = new TutorModel();
        $this->requestModel = new RequestModel();
    }

    // Mostrar formulario para registrarse como tutor
    public function registerForm() {
        session_start();
        if (!isset($_SESSION['user_id'])) {
            header("Location: index.php?action=login");
            exit();
        }
        // Verificar que no sea admin
        if ($_SESSION['rol'] === 'admin') {
            header("Location: index.php?action=home");
            exit();
        }
        // Verificar que la cuenta esté activa
        require_once('models/UserModel.php');
        $userModel = new UserModel();
        $user = $userModel->getUserById($_SESSION['user_id']);
        if (!$user['activo']) {
            $_SESSION['error'] = "Tu cuenta está desactivada. No puedes registrarte como tutor.";
            header("Location: index.php?action=home");
            exit();
        }
        $asignaturas = $this->tutorModel->getAsignaturas();

        // Si ya es tutor, obtener asignaturas actuales para marcar checkboxes
        $isTutor = $this->tutorModel->isTutor($_SESSION['user_id']);
        $currentAsignaturas = [];
        $tutor = [];
        if ($isTutor) {
            $tutorId = $this->tutorModel->getTutorIdByUserId($_SESSION['user_id']);
            $currentAsignaturas = $this->tutorModel->getTutorAsignaturas($tutorId);
            $currentIds = array_column($currentAsignaturas, 'id');
            // Obtener datos del tutor para pre-llenar descripción
            $tutor = $this->tutorModel->getTutorByUserId($_SESSION['user_id']);
        }

        include('views/register_tutor.php');
    }

    // Procesar registro de tutor
    public function register() {
        session_start();
        if (!isset($_SESSION['user_id'])) {
            header("Location: index.php?action=login");
            exit();
        }
        // Verificar que la cuenta esté activa
        require_once('models/UserModel.php');
        $userModel = new UserModel();
        $user = $userModel->getUserById($_SESSION['user_id']);
        if (!$user['activo']) {
            $_SESSION['error'] = "Tu cuenta está desactivada. No puedes registrarte como tutor.";
            header("Location: index.php?action=home");
            exit();
        }
        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            $descripcion = $_POST['descripcion'];
            $asignaturas = $_POST['asignaturas']; // Array de IDs

            $this->tutorModel->registerTutor($_SESSION['user_id'], $descripcion, $asignaturas);
            header("Location: index.php?action=home");
            exit();
        }
    }
    
    // Mostrar formulario para solicitar tutoría
    public function requestForm($tutorId) {
        session_start();
        if (!isset($_SESSION['user_id'])) {
            header("Location: index.php?action=login");
            exit();
        }
        // Verificar que no sea admin
        if ($_SESSION['rol'] === 'admin') {
            header("Location: index.php?action=home");
            exit();
        }
        // Verificar que la cuenta esté activa
        require_once('models/UserModel.php');
        $userModel = new UserModel();
        $user = $userModel->getUserById($_SESSION['user_id']);
        if (!$user['activo']) {
            $_SESSION['error'] = "Tu cuenta está desactivada. No puedes solicitar tutorías.";
            header("Location: index.php?action=home");
            exit();
        }
        $asignaturas = $this->tutorModel->getTutorAsignaturas($tutorId);
        include('views/request_tutoria.php');
    }
    
    // Procesar solicitud
    public function request() {
        session_start();
        if (!isset($_SESSION['user_id'])) {
            header("Location: index.php?action=login");
            exit();
        }
        // Verificar que no sea admin
        if ($_SESSION['rol'] === 'admin') {
            header("Location: index.php?action=home");
            exit();
        }
        // Verificar que la cuenta esté activa
        require_once('models/UserModel.php');
        $userModel = new UserModel();
        $user = $userModel->getUserById($_SESSION['user_id']);
        if (!$user['activo']) {
            $_SESSION['error'] = "Tu cuenta está desactivada. No puedes solicitar tutorías.";
            header("Location: index.php?action=home");
            exit();
        }
        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            $tutorId = $_POST['tutor_id'];
            $asignaturaId = $_POST['asignatura_id'];
            if ($this->requestModel->createRequest($_SESSION['user_id'], $tutorId, $asignaturaId)) {
                header("Location: index.php?action=my_requests");
                exit();
            } else {
                $_SESSION['request_error'] = "Ya tienes una solicitud activa para este tutor y asignatura.";
                header("Location: index.php?action=home");
                exit();
            }
        }
    }
    
    // Mostrar solicitudes del usuario
    public function myRequests() {
        session_start();
        if (!isset($_SESSION['user_id'])) {
            header("Location: index.php?action=login");
            exit();
        }
        // Verificar que no sea admin
        if ($_SESSION['rol'] === 'admin') {
            header("Location: index.php?action=home");
            exit();
        }
        $requests = $this->requestModel->getUserRequests($_SESSION['user_id']);
        include('views/my_requests.php');
    }

    // Cancelar solicitud
    public function cancelRequest() {
        session_start();
        if (!isset($_SESSION['user_id'])) {
            header("Location: index.php?action=login");
            exit();
        }
        // Verificar que no sea admin
        if ($_SESSION['rol'] === 'admin') {
            header("Location: index.php?action=home");
            exit();
        }
        if ($_SERVER["REQUEST_METHOD"] == "POST" && isset($_POST['request_id'])) {
            $requestId = $_POST['request_id'];
            if ($this->requestModel->cancelRequest($requestId, $_SESSION['user_id'])) {
                $_SESSION['cancel_success'] = "Solicitud cancelada exitosamente.";
            } else {
                $_SESSION['cancel_error'] = "No se pudo cancelar la solicitud. Puede que ya haya sido aceptada o haya pasado más de 24 horas.";
            }
        }
        header("Location: index.php?action=my_requests");
        exit();
    }
}
?>

### controllers/AdminController.php: una vez creado el archivo, escribir el siguiente código php:

<?php
/**
 * Controlador de Administrador
 * Maneja acciones exclusivas para administradores.
 */

require_once('models/TutorModel.php');
require_once('models/RequestModel.php');
require_once('models/UserModel.php'); // Agregado

class AdminController {
    private $tutorModel;
    private $requestModel;
    private $userModel; // Agregado

    public function __construct() {
        $this->tutorModel = new TutorModel();
        $this->requestModel = new RequestModel();
        $this->userModel = new UserModel(); // Agregado
    }

    /**
     * Muestra el dashboard del administrador.
     */
    public function dashboard() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        $asignaturas = $this->tutorModel->getAsignaturas();
        $pendingRequests = $this->requestModel->getPendingRequests();
        $acceptedRequests = $this->requestModel->getAcceptedRequests();
        $tutores = $this->tutorModel->getAllTutors();
        $usuarios = $this->userModel->getAllUsers();
        include('views/admin_dashboard.php');
    }

    /**
     * Muestra el formulario para agregar asignatura.
     */
    public function addAsignaturaForm() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        include('views/add_asignatura.php');
    }

    /**
     * Procesa la adición de una nueva asignatura.
     */
    public function addAsignatura() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            $nombre = $_POST['nombre'];
            if ($this->tutorModel->addAsignatura($nombre)) {
                $success = "Asignatura añadida exitosamente.";
            } else {
                $error = "La asignatura ya existe.";
            }
            include('views/add_asignatura.php');
        }
    }

    /**
     * Acepta una solicitud de tutoría.
     */
    public function acceptRequest() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        if (isset($_GET['request_id'])) {
            $this->requestModel->acceptRequest($_GET['request_id']);
        }
        header("Location: index.php?action=admin_dashboard");
        exit();
    }

    /**
     * Rechaza una solicitud de tutoría.
     */
    public function rejectRequest() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        if (isset($_GET['request_id'])) {
            $this->requestModel->rejectRequest($_GET['request_id']);
        }
        header("Location: index.php?action=admin_dashboard");
        exit();
    }

    /**
     * Muestra el formulario para editar asignatura.
     */
    public function editAsignaturaForm() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        if (isset($_GET['asignatura_id'])) {
            $asignatura = $this->tutorModel->getAsignaturaById($_GET['asignatura_id']);
            include('views/edit_asignatura.php');
        } else {
            header("Location: index.php?action=admin_dashboard");
            exit();
        }
    }

    /**
     * Procesa la edición de una asignatura.
     */
    public function editAsignatura() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        if ($_SERVER["REQUEST_METHOD"] == "POST" && isset($_POST['asignatura_id'])) {
            $id = $_POST['asignatura_id'];
            $nombre = $_POST['nombre'];
            if ($this->tutorModel->updateAsignatura($id, $nombre)) {
                $success = "Asignatura actualizada exitosamente.";
            } else {
                $error = "Error al actualizar la asignatura.";
            }
            $asignatura = $this->tutorModel->getAsignaturaById($id);
            include('views/edit_asignatura.php');
        } else {
            header("Location: index.php?action=admin_dashboard");
            exit();
        }
    }

    /**
     * Elimina una asignatura.
     */
    public function deleteAsignatura() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=admin_dashboard");
            exit();
        }

        if (isset($_GET['asignatura_id'])) {
            $this->tutorModel->deleteAsignatura($_GET['asignatura_id']);
        }
        header("Location: index.php?action=admin_dashboard");
        exit();
    }

    /**
     * Muestra el formulario para registrar un nuevo usuario.
     */
    public function registerUserForm() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        include('views/register_user.php');
    }

    /**
     * Procesa el registro de un nuevo usuario.
     */
    public function registerUser() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            $nombre = $_POST['nombre'];
            $pass = password_hash($_POST['pass'], PASSWORD_DEFAULT);
            $rol = $_POST['rol'];

            // Manejar subida de foto
            $foto = 'images/photo.png'; // Imagen por defecto
            if (isset($_FILES['foto']) && $_FILES['foto']['error'] == 0) {
                $targetDir = "images/";
                $fileName = basename($_FILES["foto"]["name"]);
                $targetFile = $targetDir . $fileName;
                $imageFileType = strtolower(pathinfo($targetFile, PATHINFO_EXTENSION));

                // Verificar si es una imagen real
                $check = getimagesize($_FILES["foto"]["tmp_name"]);
                if ($check !== false) {
                    // Verificar tamaño (máximo 5MB)
                    if ($_FILES["foto"]["size"] <= 5000000) {
                        // Permitir ciertos formatos
                        if (in_array($imageFileType, ['jpg', 'png', 'jpeg', 'gif'])) {
                            if (move_uploaded_file($_FILES["foto"]["tmp_name"], $targetFile)) {
                                $foto = $targetFile;
                            }
                        }
                    }
                }
            }

            if ($this->userModel->registerWithRole($nombre, $pass, $rol, $foto)) {
                $success = "Usuario registrado exitosamente.";
            } else {
                $error = "El usuario ya existe.";
            }
            include('views/register_user.php');
        }
    }

    /**
     * Elimina un usuario.
     */
    public function deleteUser() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        if (isset($_GET['user_id'])) {
            // Prevenir que el admin se elimine a sí mismo
            if ($_GET['user_id'] == $_SESSION['user_id']) {
                $error = "No puedes eliminar tu propia cuenta.";
            } else {
                if ($this->userModel->deleteUser($_GET['user_id'])) {
                    $success = "Usuario eliminado exitosamente.";
                } else {
                    $error = "Error al eliminar el usuario.";
                }
            }
        }
        header("Location: index.php?action=admin_dashboard");
        exit();
    }

    /**
     * Activa o desactiva la cuenta de un usuario.
     */
    public function toggleUserStatus() {
        session_start();
        if (!isset($_SESSION['user_id']) || $_SESSION['rol'] !== 'admin') {
            header("Location: index.php?action=login");
            exit();
        }

        if (isset($_GET['user_id'])) {
            $user = $this->userModel->getUserById($_GET['user_id']);
            $newStatus = $user['activo'] ? 0 : 1;
            $this->userModel->toggleUserStatus($_GET['user_id'], $newStatus);
        }
        header("Location: index.php?action=admin_dashboard");
        exit();
    }
}
?>

## Paso 4: Crear las Vistas:

### views/home.php: creado el archivo, escribir el siguiente código:

<!DOCTYPE html>
<html>
<head>
    <title>Plataforma de Tutorías</title>
</head>
<body>
    <h1>Plataforma de Tutorías</h1>

    <?php if (isset($_SESSION['user_id'])): ?>
        <p>Bienvenido, <?php echo $_SESSION['nombre']; ?>!
        <?php if ($_SESSION['rol'] === 'admin'): ?>
            <a href="index.php?action=admin_dashboard">Panel de Admin</a> |
        <?php else: ?>
            <a href="index.php?action=register_tutor">Registrarse como Tutor</a> |
            <a href="index.php?action=my_requests">Mis Solicitudes</a> |
        <?php endif; ?>
        <a href="index.php?action=edit_profile">Editar Perfil</a> | <a href="index.php?action=logout">Cerrar Sesión</a></p>
        <?php if (isset($_SESSION['request_error'])) { echo "<p style='color:red;'>".$_SESSION['request_error']."</p>"; unset($_SESSION['request_error']); } ?>
    <?php else: ?>
        <div style="float: right; width: 300px; border: 1px solid #ccc; padding: 10px; margin: 10px;">
            <h3>Iniciar Sesión</h3>
            <?php if (isset($_SESSION['login_error'])) { echo "<p style='color:red;'>".$_SESSION['login_error']."</p>"; unset($_SESSION['login_error']); } ?>
            <?php if (isset($_SESSION['register_success'])) { echo "<p style='color:green;'>".$_SESSION['register_success']."</p>"; unset($_SESSION['register_success']); } ?>
            <?php if (isset($_SESSION['register_error'])) { echo "<p style='color:red;'>".$_SESSION['register_error']."</p>"; unset($_SESSION['register_error']); } ?>
            <form method="POST" action="index.php?action=login">
                <label for="nombre">Nombre:</label>
                <input type="text" id="nombre" name="nombre" required><br>
                <label for="pass">Contraseña:</label>
                <input type="password" id="pass" name="pass" required><br>
                <button type="submit">Iniciar Sesión</button>
            </form>
            <p><a href="index.php?action=register">Registrarse</a></p>
        </div>
    <?php endif; ?>

    <h2>Lista de Tutores Disponibles</h2>

    <ul>
        <?php foreach ($tutors as $tutor): ?>
            <li>
                <img src="<?php echo $tutor['foto']; ?>" alt="Foto de <?php echo $tutor['nombre']; ?>" style="width: 100px; height: 100px; border-radius: 50%;"><br>
                <strong><?php echo $tutor['nombre']; ?></strong><br>
                Descripción: <?php echo $tutor['descripcion']; ?><br>
                Asignaturas: <?php echo $tutor['asignaturas']; ?><br>
                <?php if (isset($_SESSION['user_id'])): ?>
                    <?php if ($tutor['user_id'] == $_SESSION['user_id']): ?>
                        <em>No puedes solicitarte tutoría a ti mismo</em>
                    <?php else: ?>
                        <a href="index.php?action=request_tutoria&tutor_id=<?php echo $tutor['id']; ?>">Solicitar Tutor</a>
                    <?php endif; ?>
                <?php else: ?>
                    <em>Inicia sesión para solicitar tutoría</em>
                <?php endif; ?>
            </li>
        <?php endforeach; ?>
    </ul>
</body>
</html>

### views/admin_dashboard.php: dentro de este archivo, colocar el siguiente código php:

<!DOCTYPE html>
<html>
<head>
    <title>Panel de Administrador</title>
</head>
<body>
    <h2>Panel de Administrador</h2>
    <p>Bienvenido, <?php echo $_SESSION['nombre']; ?> (Admin) | <a href="index.php?action=logout">Cerrar Sesión</a></p>

    <h3>Gestión de Usuarios</h3>
    <a href="index.php?action=register_user">Registrar Nuevo Usuario</a>

    <h3>Gestión de Asignaturas</h3>
    <a href="index.php?action=add_asignatura">Agregar Nueva Asignatura</a>
    <ul>
        <?php foreach ($asignaturas as $asignatura): ?>
            <li>
                <?php echo $asignatura['nombre']; ?>
                <a href="index.php?action=edit_asignatura&asignatura_id=<?php echo $asignatura['id']; ?>">Editar</a>
                <a href="index.php?action=delete_asignatura&asignatura_id=<?php echo $asignatura['id']; ?>" onclick="return confirm('¿Estás seguro de eliminar esta asignatura?')">Eliminar</a>
            </li>
        <?php endforeach; ?>
    </ul>

    <h3>Lista de Usuarios</h3>
    <ul>
        <?php foreach ($usuarios as $usuario): ?>
            <li>
                <img src="<?php echo $usuario['foto']; ?>" alt="Foto de <?php echo $usuario['nombre']; ?>" style="width: 50px; height: 50px; border-radius: 50%; margin-right: 10px;">
                <?php echo $usuario['nombre']; ?> - Rol: <?php echo $usuario['rol']; ?> - Es Tutor: <?php echo $usuario['es_tutor']; ?>
                <?php if ($usuario['activo'] == 0): ?>
                    <span style="color:red; font-weight:bold;">(DESACTIVADO)</span>
                <?php endif; ?>
                <?php if ($usuario['id'] != $_SESSION['user_id']): ?>
                    <a href="index.php?action=toggle_user_status&user_id=<?php echo $usuario['id']; ?>"><?php echo $usuario['activo'] ? 'Desactivar' : 'Activar'; ?></a> |
                    <a href="index.php?action=delete_user&user_id=<?php echo $usuario['id']; ?>" onclick="return confirm('¿Estás seguro de eliminar este usuario?')">Eliminar</a>
                <?php endif; ?>
            </li>
        <?php endforeach; ?>
    </ul>

    <h3>Lista de Tutores</h3>
    <ul>
        <?php foreach ($tutores as $tutor): ?>
            <li>
                <?php echo $tutor['nombre']; ?> - Descripción: <?php echo $tutor['descripcion']; ?> - Asignaturas: <?php echo $tutor['asignaturas']; ?>
            </li>
        <?php endforeach; ?>
    </ul>

    <h3>Solicitudes Pendientes</h3>
    <ul>
        <?php foreach ($pendingRequests as $request): ?>
            <li>
                Usuario: <?php echo $request['usuario']; ?> - Tutor: <?php echo $request['tutor']; ?> - Asignatura: <?php echo $request['asignatura']; ?> - Fecha: <?php echo $request['fecha_solicitud']; ?>
                <a href="index.php?action=accept_request&request_id=<?php echo $request['id']; ?>">Aceptar</a>
                <a href="index.php?action=reject_request&request_id=<?php echo $request['id']; ?>" onclick="return confirm('¿Estás seguro de rechazar esta solicitud?')">Rechazar</a>
            </li>
        <?php endforeach; ?>
    </ul>

    <h3>Solicitudes Aceptadas</h3>
    <ul>
        <?php foreach ($acceptedRequests as $request): ?>
            <li>
                Usuario: <?php echo $request['usuario']; ?> - Tutor: <?php echo $request['tutor']; ?> - Asignatura: <?php echo $request['asignatura']; ?> - Fecha: <?php echo $request['fecha_solicitud']; ?>
            </li>
        <?php endforeach; ?>
    </ul>
</body>
</html>
```

## Paso 5: Crear el Router (index.php): creado el archivo, colocar el siguiente código:

<?php
require_once('controllers/AuthController.php');
require_once('controllers/HomeController.php');
require_once('controllers/TutorController.php');
require_once('controllers/AdminController.php');

$action = isset($_GET['action']) ? $_GET['action'] : 'home';

if ($action === 'home' && !isset($_SESSION['user_id'])) {
    // Mostrar lista de tutores sin sesión
    $controller = new HomeController();
    $controller->index();
    exit();
}

switch ($action) {
    case 'login':
        $controller = new AuthController();
        $controller->login();
        break;
    case 'register_user':
      $controller = new AdminController();
      $controller->registerUserForm();
      break;
    
    case 'register_user_process':
        $controller = new AdminController();
        $controller->registerUser();
        break;
    case 'delete_user':
        $controller = new AdminController();
        $controller->deleteUser();
        break;
    case 'toggle_user_status':
        $controller = new AdminController();
        $controller->toggleUserStatus();
        break;
    case 'admin_dashboard':
      $controller = new AdminController();
      $controller->dashboard();
      break;
    case 'add_asignatura_process':
        $controller = new AdminController();
        $controller->addAsignatura();
        break;
    case 'accept_request':
        $controller = new AdminController();
        $controller->acceptRequest();
        break;
    case 'reject_request':
        $controller = new AdminController();
        $controller->rejectRequest();
        break;
    case 'edit_asignatura':
        $controller = new AdminController();
        $controller->editAsignaturaForm();
        break;
    case 'edit_asignatura_process':
        $controller = new AdminController();
        $controller->editAsignatura();
        break;
    case 'delete_asignatura':
        $controller = new AdminController();
        $controller->deleteAsignatura();
        break;
    case 'add_asignatura':
    $controller = new AdminController();
    $controller->addAsignaturaForm();
    break;
    case 'register':
        $controller = new AuthController();
        $controller->register();
        break;
    case 'logout':
        $controller = new AuthController();
        $controller->logout();
        break;
    case 'register_tutor':
        $controller = new TutorController();
        $controller->registerForm();
        break;
    case 'register_tutor_process':
        $controller = new TutorController();
        $controller->register();
        break;
    case 'request_tutoria':
        $controller = new TutorController();
        $controller->requestForm($_GET['tutor_id']);
        break;
    case 'request_tutoria_process':
        $controller = new TutorController();
        $controller->request();
        break;
    case 'my_requests':
        $controller = new TutorController();
        $controller->myRequests();
        break;
    case 'cancel_request':
        $controller = new TutorController();
        $controller->cancelRequest();
        break;
    case 'edit_profile':
        $controller = new AuthController();
        $controller->editProfileForm();
        break;
    case 'edit_profile_process':
        $controller = new AuthController();
        $controller->editProfile();
        break;
    case 'toggle_account_status':
        $controller = new AuthController();
        $controller->toggleAccountStatus();
        break;
    case 'home':
    default:
        $controller = new HomeController();
        $controller->index();
        break;
}
?>

## Funcionalidades Implementadas

### 1. Gestión de Usuarios
- Registro de usuarios con nombre, contraseña y foto de perfil opcional.
- Inicio de sesión y logout.
- Edición de perfil (nombre, contraseña y foto de perfil).
- Desactivación/reactivación de cuenta para usuarios normales.
- Foto de perfil por defecto (photo.png) si no se asigna al registrarse.
- Edición de foto de perfil por usuarios logueados.

### 2. Gestión de Tutores
- Registro como tutor con descripción y asignaturas.
- Lista de tutores disponibles en la página de registro.

### 3. Gestión de Solicitudes de Tutoría
- Solicitar tutoría a un tutor específico.
- Ver mis solicitudes pendientes y aceptadas.
- Aceptar/rechazar solicitudes (solo administradores).

### 4. Gestión de Asignaturas
- Agregar, editar y eliminar asignaturas (solo administradores).

### 5. Panel de Administración
- Dashboard con estadísticas y gestión de usuarios, tutores, solicitudes y asignaturas.
- Registro de nuevos usuarios por administradores (con foto opcional).
- Eliminación de usuarios (excepto el propio administrador).

### 6. Roles y Permisos
- Usuario normal: puede registrarse, iniciar sesión, editar perfil, solicitar tutoría, ver solicitudes.
- Administrador: acceso completo a todas las funcionalidades, incluyendo gestión de usuarios, tutores, solicitudes y asignaturas.

### 7. Seguridad
- Contraseñas hasheadas con password_hash().
- Validación de sesiones para acceso a páginas restringidas.
- Prevención de eliminación de cuenta propia por administradores.
- Validación de imágenes subidas (tamaño máximo 5MB, formatos permitidos: JPG, PNG, JPEG, GIF).

### 8. Interfaz de Usuario
- Navegación intuitiva con enlaces en todas las páginas.
- Mensajes de éxito y error para feedback al usuario.
- Diseño simple y funcional con HTML básico.
- Formularios con enctype="multipart/form-data" para subida de archivos.

## Paso 6: Probar la Aplicación

1. Accede a index.php y registra un nuevo usuario.
2. Inicia sesión con el usuario registrado.
3. Regístrate como tutor seleccionando asignaturas.
4. Solicita tutoría a otro tutor.
5. Verifica tus solicitudes en "Mis Solicitudes".
6. Inicia sesión como admin (usuario: admin, contraseña: 123).
7. Ve al panel de admin y registra un nuevo usuario.
8. Acepta o rechaza solicitudes pendientes.
9. Agrega, edita o elimina asignaturas.
10. Desactiva o elimina usuarios desde la lista.

¡Felicidades! Has construido un sistema completo de tutorías con PHP y MySQL.


