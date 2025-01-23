# B2---Trabajo-Consulta-2-Conexi-n-base-de-datos-relacional

* ¿Qué es JDBC y cuáles son sus componentes?

JDBC (Java Database Connectivity) es una API de Java que permite a las aplicaciones Java interactuar con bases de datos relacionales. Proporciona un conjunto estándar de interfaces y clases que los desarrolladores pueden usar para conectar aplicaciones Java con una base de datos, enviar consultas SQL y procesar los resultados.

Componentes principales de JDBC

Driver Manager (Gestor de Controladores):

Es responsable de gestionar los controladores JDBC. Actúa como un puente entre la aplicación Java y el controlador específico de la base de datos. Se asegura de cargar y registrar el controlador adecuado para establecer la conexión con la base de datos.

Driver (Controlador):

Un controlador JDBC es una implementación de la API JDBC que traduce las llamadas de la aplicación Java en comandos específicos que la base de datos entiende. Hay cuatro tipos principales de controladores JDBC:

Tipo 1: Puente JDBC-ODBC (deprecated).

Tipo 2: Controladores nativos-parciales.

Tipo 3: Controladores independientes del protocolo de red.

Tipo 4: Controladores completamente Java que se conectan directamente a la base de datos.

Connection (Conexión):

Representa la conexión entre la aplicación Java y la base de datos. Una vez establecida, permite enviar instrucciones SQL.

Statement (Declaración):

Se utiliza para enviar consultas SQL a la base de datos. Existen tres tipos de objetos relacionados con declaraciones:

Statement: Para consultas SQL estáticas.

PreparedStatement: Para consultas SQL precompiladas con parámetros.

CallableStatement: Para invocar procedimientos almacenados en la base de datos.

ResultSet (Conjunto de Resultados):

Contiene los resultados de una consulta SQL, generalmente devueltos como filas de una tabla. Proporciona métodos para navegar por los resultados y recuperar datos.

SQLException:

Es una clase que maneja errores o excepciones que ocurren durante las operaciones con JDBC. Proporciona detalles como el código de error y el estado SQL.

Flujo típico de uso de JDBC
Cargar el controlador JDBC usando Class.forName("driver_class_name").
Establecer una conexión con la base de datos mediante DriverManager.getConnection().
Crear un objeto Statement, PreparedStatement o CallableStatement.
Ejecutar consultas SQL utilizando los métodos executeQuery() o executeUpdate().
Procesar los resultados usando ResultSet.
Cerrar el ResultSet, Statement y la conexión para liberar recursos.
  
* Documente 2 librerías de Scala que permitan conectarse a una base de datos relacional. En una tabla resuma sus diferencias.

1. Slick (Scala Language-Integrated Connection Kit)

Descripción: Es una librería oficial de Scala que combina el acceso a bases de datos relacionales con características funcionales y tipadas. Proporciona una abstracción similar a un ORM (pero no es uno completo), lo que permite construir consultas SQL tipadas y expresivas en Scala.

Características clave:

Soporte para consultas SQL tipadas.

Abstracción funcional para trabajar con tablas y consultas.

Integración con Futures para operaciones asíncronas.

Proporciona un DSL (Domain-Specific Language) para escribir consultas.

Uso típico: Ideal para desarrolladores que quieren trabajar en un estilo funcional y con consultas SQL tipadas.

2. Doobie
   
Descripción: Es una librería puramente funcional que permite realizar consultas SQL directamente en Scala. Es parte del ecosistema Typelevel y se integra bien con cats y otras herramientas funcionales.

Características clave:

Enfoque funcional puro (FP).

Uso de constructores y combinadores para construir y ejecutar consultas SQL.

Integración con cats-effect para el manejo de efectos.

Verificación estática de consultas SQL en tiempo de compilación (opcional).

Uso típico: Ideal para desarrolladores que trabajan en un entorno funcional puro o que necesitan un control más explícito sobre las consultas SQL.

| **Aspecto**              | **Slick**                                            | **Doobie**                                         |
|---------------------------|-----------------------------------------------------|---------------------------------------------------|
| **Paradigma**             | Híbrido: funcional e imperativo                     | Funcional puro                                    |
| **Nivel de abstracción**  | Abstracción similar a un ORM (consultas tipadas)    | SQL sin abstracciones adicionales                |
| **Manejo de consultas**   | DSL tipado para construir consultas                 | Consultas SQL escritas directamente              |
| **Verificación de SQL**   | Tipado basado en Scala (pero sin verificar SQL en tiempo de compilación) | Opcional: verificación de SQL en tiempo de compilación |
| **Ecosistema**            | Independiente, pero compatible con Future          | Parte del ecosistema Typelevel (cats-effect, fs2) |
| **Curva de aprendizaje**  | Moderada: requiere aprender el DSL                 | Más pronunciada si no se domina programación funcional |
| **Concurrencia**          | Usa `Future` para operaciones asíncronas           | Usa `IO` o `cats-effect`                         |
| **Popularidad**           | Muy popular en la comunidad Scala                  | Creciendo en popularidad, especialmente en FP    |
| **Compatibilidad**        | JDBC y múltiples bases de datos                    | JDBC y múltiples bases de datos                  |

  
* Documentar cómo establecer una conexión a una base de datos relacional (mysql). Siga los siguientes pasos:
  
Genere una base de datos en mysql
``` mysqul
CREATE DATABASE scala_demo;
USE scala_demo;

CREATE TABLE empleados (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100),
    puesto VARCHAR(100),
    salario DECIMAL(10, 2)
);

```
Genere una tabla con datos de prueba
``` mysql
INSERT INTO empleados (nombre, puesto, salario)
VALUES 
    ('Juan Pérez', 'Desarrollador', 50000.00),
    ('María López', 'Analista', 60000.00),
    ('Carlos Sánchez', 'Gerente', 80000.00);

```
Desde Scala establezca la conexión a la base datos

(opcional) Desde Scala realice la consulta de todos los datos de la tabla de prueba. 

``` Scala
package Bimestre_2.Semana_6
import java.sql.{Connection, DriverManager, ResultSet}

  object coneccion_sql  extends App {
    // Parámetros de conexión
    val url = "jdbc:mysql://localhost:3306/scala_demo" //
    val username = "root" //
    val password = "deathghost" //

    // Conexión a la base de datos
    var connection: Connection = null

    try {
      // Cargar el controlador JDBC
      Class.forName("com.mysql.cj.jdbc.Driver")

      // Establecer conexión
      connection = DriverManager.getConnection(url, username, password)
      println("Conexión establecida con éxito.")

      // Crear y ejecutar consulta
      val statement = connection.createStatement()
      val resultSet = statement.executeQuery("SELECT * FROM empleados") // Reemplaza con tu tabla

      // Procesar resultados
      println("ID | Nombre        | Puesto          | Salario")
      println("-----------------------------------------------")
      while (resultSet.next()) {
        val id = resultSet.getInt("id")
        val nombre = resultSet.getString("nombre")
        val puesto = resultSet.getString("puesto")
        val salario = resultSet.getBigDecimal("salario")
        println(f"$id%-3d| $nombre%-13s| $puesto%-15s| $$salario%-10.2f")
      }
    } catch {
      case e: Exception =>
        e.printStackTrace()
    } finally {
      // Cerrar conexión
      if (connection != null) {
        connection.close()
        println("Conexión cerrada.")
      }
    }
  }


```
