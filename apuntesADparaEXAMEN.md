# 📚 Guía Completa MongoDB - Ejercicios de Repaso

> [!NOTE]
> 
> Esta guía está diseñada para ayudarte a comprender la estructura y resolución de ejercicios típicos con MongoDB Compass.

## 🌟 Índice

### 🎯 Estructura General de un Proyecto en MongoDB con Java

#### 1️⃣ 🔧 Preparación del Entorno

1. Instalar MongoDB y MongoDB Compass (Si no lo tienes ya instalado)
2. Crear un proyecto Maven en tu IDE.
3. Añadir dependencias necesarias en pom.xml:

```xml
<dependencies>
    <!-- Conector Java para MongoDB -->
    <dependency>
        <groupId>org.mongodb</groupId>
        <artifactId>mongodb-driver-sync</artifactId>
        <version>4.9.0</version>
    </dependency>

    <!-- Para manipulación de JSON y XML -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.13.3</version>
    </dependency>
</dependencies>
```

#### 2️⃣ 🔗 Conexión a MongoDB

> [!TIP] Si no puedes conectarte...
>
> Asegúrate de que MongoDB esté en ejecución.
> Verifica que la URL de conexión mongodb://localhost:27017 sea correcta.

```java
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoDatabase;

public class MongoConnection {
    public static void main(String[] args) {
        try (MongoClient client = MongoClients.create("mongodb://localhost:27017")) {
            MongoDatabase db = client.getDatabase("miBaseDatos");
            System.out.println("🔥 Conexión exitosa a la base de datos");
        }
    }
}
```

### 🏗️ CRUD en MongoDB con Java

#### 3️⃣ 🆕 Crear una colección e insertar datos manualmente

```java
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

public class InsertarDatos {
    public static void main(String[] args) {
        MongoDatabase db = MongoClients.create("mongodb://localhost:27017").getDatabase("miBaseDatos");
        MongoCollection<Document> coleccion = db.getCollection("pokemon");
        
        Document pokemon = new Document("nombre", "Pikachu")
                .append("tipo", "Eléctrico")
                .append("nivel", 10);
        
        coleccion.insertOne(pokemon);
        System.out.println("✅ Pokémon insertado correctamente");
    }
}
```

#### 4️⃣ 📂 Insertar datos usando JSON

```java
import java.nio.file.*;
import org.bson.Document;
import com.fasterxml.jackson.databind.ObjectMapper;

public class InsertarDesdeJSON {
    public static void main(String[] args) throws Exception {
        String json = Files.readString(Path.of("pokemon.json"));
        Document doc = Document.parse(json);
        MongoDatabase db = MongoClients.create("mongodb://localhost:27017").getDatabase("miBaseDatos");
        db.getCollection("pokemon").insertOne(doc);
        System.out.println("✅ Datos insertados desde JSON");
    }
}
```

#### 5️⃣ 📖 Leer datos de MongoDB

```java
import com.mongodb.client.FindIterable;

public class LeerDatos {
    public static void main(String[] args) {
        MongoDatabase db = MongoClients.create("mongodb://localhost:27017").getDatabase("miBaseDatos");
        MongoCollection<Document> coleccion = db.getCollection("pokemon");
        FindIterable<Document> documentos = coleccion.find();
        for (Document doc : documentos) {
            System.out.println(doc.toJson());
        }
    }
}
```

#### 6️⃣ 📤 Exportar a JSON/XML

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.dataformat.xml.XmlMapper;
import java.io.File;

public class ExportarDatos {
    public static void main(String[] args) throws Exception {
        MongoDatabase db = MongoClients.create("mongodb://localhost:27017").getDatabase("miBaseDatos");
        MongoCollection<Document> coleccion = db.getCollection("pokemon");
        Document doc = coleccion.find().first();
        
        ObjectMapper jsonMapper = new ObjectMapper();
        jsonMapper.writeValue(new File("pokemon.json"), doc);
        
        XmlMapper xmlMapper = new XmlMapper();
        xmlMapper.writeValue(new File("pokemon.xml"), doc);
        
        System.out.println("✅ Datos exportados a JSON y XML");
    }
}
```

### 🚀 Implementación de Swagger para Documentación de API

> [!NOTE] ¿Qué es Swagger?
>
> Swagger es una herramienta que permite documentar y probar APIs de manera sencilla. Genera una interfaz interactiva donde puedes ver y probar los endpoints de tu aplicación sin necesidad de usar Postman o herramientas similares.

#### 🛠️ Configurar Swagger en un Proyecto con Java

1. Añadir dependencias en pom.xml:

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

2. Crear la configuración de Swagger en Java:

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;

@Configuration
public class SwaggerConfig {
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
    }
}
```

3. Acceder a la documentación de la API:    

Levanta tu aplicación y accede a: http://localhost:8080/swagger-ui/. Aquí podrás ver los endpoints y probarlos de forma interactiva.

### 💡 ProTips 

> [!TIP] Cosas que debes recordar:
> 
> ✅ Usa MongoDB Compass para visualizar la base de datos y hacer consultas rápidas.
> ✅ No te olvides de cerrar la conexión a la base de datos para evitar bloqueos.
> ✅ Usa índices para mejorar el rendimiento en consultas grandes.
> ✅ Mantén el código modular y reutilizable, creando métodos separados para cada operación.
> ✅ Respeta las convenciones de nombres en las colecciones y documentos para mantener la coherencia.