# Esquema de Room 

## Room
- Librería de persistencia para bases de datos SQLite en Android.
- Facilita el acceso a la base de datos sin escribir consultas SQL manualmente.
- Proporciona una capa de abstracción sobre SQLite.

## Dependencias
    implementation (libs.androidx.room.runtime)
    ksp (libs.androidx.room.compiler.v252)
    implementation (libs.androidx.room.ktx)

## Como usar Room
### Creación de la Entidad (Entity) 
- Definir la clase que representa una tabla en la base de datos.
- El siguiente código define una entidad de datos User. Cada instancia de User representa una fila en una tabla de user en la base de datos de la app.
  
      @Entity
      data class User(
        @PrimaryKey val uid: Int,
        @ColumnInfo(name = "first_name") val firstName: String?,
        @ColumnInfo(name = "last_name") val lastName: String?
      )
  
### Creación de la DAO (Objeto de acceso a datos) 
- El DAO define los métodos de acceso a la base de datos (consultas).
- El siguiente código define un DAO llamado UserDao. UserDao proporciona los métodos que el resto de la app usa para interactuar con los datos de la tabla user.

      @Dao
      interface UserDao {
          @Insert
          suspend fun insert(user: User)
      
          @Update
          suspend fun update(user: User)
      
          @Delete
          suspend fun delete(user: User)
      
          @Query("SELECT * FROM user_table")
          suspend fun getAllUsers(): List<User>
      }

### Creación de la Base de Datos (RoomDatabase)
- La clase debe tener una anotación @Database que incluya un array entities que enumere todas las entidades de datos asociados con la base de datos.
- Debe ser una clase abstracta que extienda RoomDatabase.
- Para cada clase DAO que se asoció con la base de datos, esta base de datos debe definir un método abstracto que tenga cero argumentos y muestre una instancia de la clase DAO.

      @Database(entities = [User::class], version = 1)
      abstract class AppDatabase : RoomDatabase() {
          abstract fun userDao(): UserDao
      }

### Inicialización de Room
- Crear una instancia de la base de datos en una actividad o clase.
- Usar Room.databaseBuilder para crear la base de datos.

      val db = Room.databaseBuilder(
                  applicationContext,
                  AppDatabase::class.java, "database-name"
              ).build()

### Uso de Room en el Proyecto
- Insertar, actualizar, eliminar y consultar datos usando el DAO.
- Ejemplo de inserción y consulta:

      val userDao = db.userDao()
      
      // Insertar un nuevo usuario
      val user = User(name = "John Doe", age = 30)
      db.userDao().insert(user)
      
      // Consultar todos los usuarios
      val users = db.userDao().getAllUsers()
