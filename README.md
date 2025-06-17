# Atividade - [LAB] AT-06-10: Implementar ORM de uma classe Modelo do Projeto

# Classes

## Database

```java
import java.sql.*;
import com.j256.ormlite.jdbc.JdbcConnectionSource;

public class Database
{
   private String databaseName = null;
   private JdbcConnectionSource connection = null;
   
   public Database(String databaseName) {
       this.databaseName = databaseName;
   }    
   
   public JdbcConnectionSource getConnection() throws SQLException {
      if ( databaseName == null ) {
          throw new SQLException("database name is null");
      }
      if ( connection == null ) {
          try {
              connection = new JdbcConnectionSource("jdbc:sqlite:"+databaseName);             
            } catch ( Exception e ) {
                System.err.println( e.getClass().getName() + ": " + e.getMessage() );
                System.exit(0);
            }
            System.out.println("Opened database successfully");
      }
      return connection;
   }
   
   public void close() {
       if ( connection != null ) {
           try {
               connection.close();
               this.connection = null;
           } catch (java.lang.Exception e) {
               System.err.println(e);
           }
       }
   }
}
```

## UserRepository

```java
import com.j256.ormlite.dao.DaoManager;
import com.j256.ormlite.dao.Dao;
import java.sql.SQLException;
import com.j256.ormlite.table.TableUtils;
import java.util.List;
import java.util.ArrayList;

public class UserRepository
{
    private static Database database;
    private static Dao<User, Integer> dao;
    private List<User> loadedUsers;
    private User loadedUser; 
    
    public UserRepository(Database database) {
        UserRepository.setDatabase(database);
        loadedUsers = new ArrayList<User>();
    }
    
    public static void setDatabase(Database database) {
        UserRepository.database = database;
        try {
            dao = DaoManager.createDao(database.getConnection(), User.class);
            TableUtils.createTableIfNotExists(database.getConnection(), User.class);
        }
        catch(SQLException e) {
            System.out.println(e);
        }            
    }
    
    public User create(User user) {
        int nrows = 0;
        try {
            nrows = dao.create(user);
            if ( nrows == 0 )
                throw new SQLException("Error: object not saved");
            this.loadedUser = user;
            loadedUsers.add(user);
        } catch (SQLException e) {
            System.out.println(e);
        }
        return user;
    }    

    public void update(User user) {
      // TODO
    }

    public void delete(User user) {
      // TODO
    }
    
    public User loadFromId(int id) {
        try {
            this.loadedUser = dao.queryForId(id);
            if (this.loadedUser != null)
                this.loadedUsers.add(this.loadedUser);
        } catch (SQLException e) {
            System.out.println(e);
        }
        return this.loadedUser;
    }    
    
    public List<User> loadAll() {
        try {
            this.loadedUsers =  dao.queryForAll();
            if (this.loadedUsers.size() != 0)
                this.loadedUser = this.loadedUsers.get(0);
        } catch (SQLException e) {
            System.out.println(e);
        }
        return this.loadedUsers;
    }
     
}
```

## User

```java
import java.util.Date;
import java.text.SimpleDateFormat;
import com.j256.ormlite.table.DatabaseTable;
import com.j256.ormlite.field.DatabaseField;
import com.j256.ormlite.field.DataType;

@DatabaseTable(tableName = "Usuario")
public class User
{   
    @DatabaseField(generatedId = true)
    private int id;
    
    @DatabaseField
    private String nome;
    
    @DatabaseField
    public String categoria;
    
    @DatabaseField
    public int senha;    
    
    public int getId(){
        return this.id;
    }
    
    public void setId(int id){
        this.id = id;
    }
    
    public String getnome(){
        return this.nome;
    }

    public void setnome(String nome){
        this.nome = nome;
    }

    public String getcategoria(){
        return this.categoria;
    }

    public void setcategoria(String categoria){
        this.categoria = categoria;
    }

    public int getsenha(){
        return this.senha;
    }

    public void setsenha(int senha){
        this.senha = senha;
    }
}
```

## Implementei o ORM para a classe Usuario do projeto.

### Instanciei dois usuários.

![image](https://github.com/user-attachments/assets/6f9ac84d-9bc5-45ea-9e74-bec79f7f4d68)

### Utilizei o "create()" e coloquei eles no banco de dados.

### Tabela no DB Browser.

![image](https://github.com/user-attachments/assets/5c888d7f-c989-4c69-9a6a-5b216333f259)
