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
> Asegúrate de que MongoDB esté en ejecución, si no lo tienes, ejecuta el comando:
> 
> ```bash
> sudo systemctl start mongod
> ```
> 
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
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

public class InsertarDatos {
    public static void main(String[] args) {
        try {
            MongoDatabase db = MongoClients.create("mongodb://localhost:27017").getDatabase("miBaseDatos");
            MongoCollection<Document> coleccion = db.getCollection("pokemon");

            Document pokemon = new Document("nombre", "Pikachu")
                    .append("tipo", "Eléctrico")
                    .append("nivel", 10);

            coleccion.insertOne(pokemon);
            System.out.println("✅ Pokémon insertado correctamente");
        } catch (Exception e) {
            System.err.println("❌ Error al insertar el Pokémon: " + e.getMessage());
        }
    }
}
```

#### 4️⃣ 📂 Insertar datos usando JSON

```java
import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;

public class InsertarDesdeJSON {
    public static void main(String[] args) throws Exception {
        try {
            String json = Files.readString(Path.of("pokemon.json"));
            ObjectMapper mapper = new ObjectMapper();
            List<Document> pokemones = mapper.readValue(json, new TypeReference<List<Document>>() {
            });

            MongoDatabase db = MongoClients.create("mongodb://localhost:27017").getDatabase("miBaseDatos");
            MongoCollection<Document> coleccion = db.getCollection("pokemon");
            coleccion.insertMany(pokemones);

            System.out.println("✅ Datos insertados desde JSON");
        } catch (Exception e) {
            System.err.println("❌ Error al insertar los datos: " + e.getMessage());
        }
    }
}
```

#### 5️⃣ 📖 Leer datos de MongoDB

```java
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

public class LeerDatos {
    public static void main(String[] args) {
        try {
            MongoDatabase db = MongoClients.create("mongodb://localhost:27017").getDatabase("miBaseDatos");
            MongoCollection<org.bson.Document> coleccion = db.getCollection("pokemon");
            FindIterable<org.bson.Document> documentos = coleccion.find();
            System.out.println("📋 Listado de Pokémon:");
            for (Document doc : documentos) {
                System.out.println("🔹 " + doc.toJson());
            }
            System.out.println("✅ Fin del listado");
        } catch (Exception e) {
            System.err.println("❌ Error al leer los datos: " + e.getMessage());
        }
    }
}
```

#### 6️⃣ 📤 Exportar a JSON/XML

Implementamos la siguiente dependencia en el pom.xml:

```xml
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.15.0</version>
</dependency>
```

##### Primer elemento

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.dataformat.xml.XmlMapper;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

import java.io.File;

public class ExportarDatos {
    public static void main(String[] args) {
        try {
            MongoDatabase db = MongoClients.create("mongodb://localhost:27017").getDatabase("miBaseDatos");
            MongoCollection<Document> coleccion = db.getCollection("pokemon");
            Document doc = coleccion.find().first(); // Exportaremos solo el primer elemento

            ObjectMapper jsonMapper = new ObjectMapper();
            jsonMapper.writeValue(new File("pokemon.json"), doc);

            XmlMapper xmlMapper = new XmlMapper();
            xmlMapper.writeValue(new File("pokemon.xml"), doc);

            System.out.println("✅ Datos exportados a JSON y XML");
        } catch (Exception e) {
            System.err.println("❌ Error al exportar los datos: " + e.getMessage());
        }
    }
}
```

##### Todos los elementos

```java
package org.example;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.dataformat.xml.XmlMapper;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import org.bson.Document;

import java.io.File;
import java.util.ArrayList;
import java.util.List;

public class ExportarDatos {
    public static void main(String[] args) {
        try {
            MongoDatabase db = MongoClients.create("mongodb://localhost:27017").getDatabase("miBaseDatos");
            MongoCollection<Document> coleccion = db.getCollection("pokemon");
            List<Document> documentos = new ArrayList<>();
            coleccion.find().into(documentos);

            ObjectMapper jsonMapper = new ObjectMapper();
            jsonMapper.writeValue(new File("pokemon.json"), documentos);

            XmlMapper xmlMapper = new XmlMapper();
            xmlMapper.writeValue(new File("pokemon.xml"), documentos);

            System.out.println("✅ Datos exportados a JSON y XML");
        } catch (Exception e) {
            System.err.println("❌ Error al exportar los datos: " + e.getMessage());
        }
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
> 
> ✅ No te olvides de cerrar la conexión a la base de datos para evitar bloqueos.
> 
> ✅ Usa índices para mejorar el rendimiento en consultas grandes.  
> 
> ✅ Mantén el código modular y reutilizable, creando métodos separados para cada operación.
> 
> ✅ Respeta las convenciones de nombres en las colecciones y documentos para mantener la coherencia.