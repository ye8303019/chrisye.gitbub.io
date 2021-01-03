---
layout: post
title:  "Getting Start With GraphQL for Java"
date:   2017-12-22 11:42:11 +0800
categories: GraphQL java
---

# Preface
By the evolving of the API design, more and more developer are focus on the RESTFUL API style. But also more and more questions comes. How to do the versioning? How to design the response body to meet different requirement? especially when client wants different fields by one request. How to validating the parameter type and logic? How to fetching and caching the data?
So here comes the **GrapqhQL**, it's let us have another choice to design the API beside the RESTFUL.

# What is GraphQL?
![GraphQL]({{ site.url }}/images/graphql/logo.png) 

Here is the introduction from GraphQL official web site.

    GraphQL is a query language designed to build client applications by providing an intuitive and flexible syntax 
    and system for describing their data requirements and interactions.

    GraphQL is not a programming language capable of arbitrary computation, but is instead a language used to query 
    application servers that have capabilities defined in this specification. GraphQL does not mandate a particular 
    programming language or storage system for application servers that implement it. Instead, application servers take 
    their capabilities and map them to a uniform language, type system, and philosophy that GraphQL encodes. 
    This provides a unified interface friendly to product development and a powerful platform for tool‐building.

Compare to RESTFUL, GraphQL have 5 advantages 

* Easy to extend
* Query as documentation
* Fetch the data more centralized
* Strong type system 
* No more versioning

Let's see what Github says~

    You may be wondering why we chose to start supporting GraphQL. Our API was designed to be RESTful and 
    hypermedia-driven. We’re fortunate to have dozens of different open-source clients written in a plethora of 
    languages.  Businesses grew around these endpoints.

    Like most technology, REST is not perfect and has some drawbacks. Our ambition to change our API focused on solving 
    two problems.

    The first was scalability. The REST API is responsible for over 60% of the requests made to our database tier. This
    is partly because, by its nature, hypermedia navigation requires a client to repeatedly communicate with a server 
    so that it can get all the information it needs. Our responses were bloated and filled with all sorts of *_url hints 
    in the JSON responses to help people continue to navigate through the API to get what they needed. Despite all the 
    information we provided, we heard from integrators that our REST API also wasn’t very flexible. It sometimes required 
    two or three separate calls to assemble a complete view of a resource. It seemed like our responses simultaneously 
    sent too much data and didn’t include data that consumers needed.

    As we began to audit our endpoints in preparation for an APIv4, we encountered our second problem. We wanted to 
    collect some meta-information about our endpoints. For example, we wanted to identify the OAuth scopes required for 
    each endpoint. We wanted to be smarter about how our resources were paginated. We wanted assurances of type-safety 
    for user-supplied parameters. We wanted to generate documentation from our code. We wanted to generate clients 
    instead of manually supplying patches to our Octokit suite. We studied a variety of API specifications built to 
    make some of this easier, but we found that none of the standards totally matched our requirements.

    And then we learned about GraphQL.


# Getting start
So let's get familiar with it from a simple example

```js
type Query {
    hello: String
}
```
This is a `schema` definition for the query. We define a root type named `Query`, and from this type we can get `hello` field, which is in the type of `String`

Then assume that we build a GraphQL server and make it running already. Now we can send a query to retrieve the value for `hello`.
```js
query Query {
  hello
}
```
Let's see what we get.
```js
{hello=world}
```
So here we should know some main concepts of GraphQL

##### Operation
GraphQL have two operations, `query` & `mutation`. For example, the schema design could be:
```js
type Mutation {
  setMessage(message: String): String
}

type Query {
  getMessage: String
}
``` 

#### Schema
Schema is the query definitions of the GraphQL, it's contain the subtypes, fields, interfaces, input objects, enum defines... for example
```js
type Query {
    patent_biblio: PatentBiblio
    legal: Legal
    patent_biblio_legal: PatentBiblioAndLegal

}

interface Patent {
    id(patentId: String): String!
    pn: String!
}

type PatentBiblio implements Patent{
    id(patentId: String): String!
    pn: String!
    apno: String
    ans: Assignee
    familyType: FamilyType
}

type Assignee implements Person{
    name: String!
    lang: String!
}

interface Person {
    name: String!
}

type Legal {
    legalStatus:[String]
    eventStatus:[String]
    l001ep:String
}

union PatentBiblioAndLegal = PatentBiblio | Legal

enum FamilyType {
    ORIGINAL
    INPADOC
}
``` 
This schema define a `Query` type that could let client fetch `PatentBiblio` and `Legal`. The `PatentBiblio` implements the `Patent` interface, and also `PatentBiblio` contains `Assignee` type which implements the `Person` type. The `familyType` in PatentBiblio will return the enum type named `FamilyType`.

The structure is like below: 
![GraphQL Schema Demo]({{ site.url }}/images/graphql/GraphQL_Schema_Demo.png)
##### Field
In the example below, `pn` is a field of `Patent`, and it's type is `String`.
##### Argument
In the example below, `patentId` is a argument of `id` field in `PatentBiblio`, and it's type is `String`.
##### Main Types of the GraphQL
* Scalar
    - Int
    - Float
    - String
    - Boolean
* Object
* Interface
* Inpit Object
* Enum
* List

As we know, GraphQL is based on strong type validating, so we must be very clear about what kinds of type we want to define.
For example, in the example below, we can see that:  
`patent_biblio` is related to Object `PatentBiblio`  
`legalStatus` in `Legal` is the `List` type, the format could be `[List]`
`familyType` in `PatentBiblio` related to `Enum` type `FamilyType`

```js
schema {
    query: LitigationQuery
}

type LitigationQuery {
    litigation: Litigation
}

type Litigation {
    defendant(queryInput: LitigationQueryInput): String
    plaintiff: String
}

input LitigationQueryInput {
    patentId: String
}
```
In this example, we make the argument in field `defendant` to be the `Input Object` type named`LitigationQueryInput`. 
##### Fragment
In order to reduce the duplicate text in the query string, GraphQL allow for the reuse of the repeated selections of the fields, for example:
```js
query Query($patentId: String!, $offset: Int, $limit: Int) {
  patent(patentId: $patentId, offset: $offset, limit: $limit) {
    id
    pn
    my_name
    person {
      name
    }
    citations {
      ...citationPatent
      citations {
        ...citationPatent
        citations {
          ...citationPatent
        }
      }
    }
  }
  total(patentId: $patentId)
  offset(offset: $offset, limit: $limit)
}

fragment citationPatent on Patent {
  id
  pn
}
```

# GraphQL for Java
GraphQL library support many languages, such as

* C# / .NET
* Clojure
* Elixir
* Erlang
* Go
* Groovy
* Java
* JavaScript
* PHP
* Python
* Scala
* Ruby

Before we start to introduce the GraphQL for Java, let's think about some questions first.

* How we define the schema?  
* How we fetch the data?  
* How to send query?  
* How to validating the query?
* How to serialized the response?  
* How to catch the exception?  
* How to do the logging?  
* How to do the pagination?  
* How to serve vai HTTP/HTTPS?  

So let's begin from an Hello World again!
Create a maven architecture project first, add the dependency for GraphQL
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.chris.graphql</groupId>
    <artifactId>project-graphql</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>project-graphql Maven Webapp</name>
    <url>http://maven.apache.org</url>
    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.graphql-java</groupId>
            <artifactId>graphql-java</artifactId>
            <version>6.0</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.9.3</version>
        </dependency>
    </dependencies>
    <build>
        <finalName>project-graphql</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.5</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>${project.build.sourceEncoding}</encoding>
                    <optimize>true</optimize>
                    <showWarnings>true</showWarnings>
                </configuration>
            </plugin>
        </plugins>

        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>

    </build>
</project>
```
Because the GraphQL-Java based on Java 8, so we also make the project compile in JDK 1.8, then let's create a class named `HelloWorld`
```java
package com.chris.graphql;

import graphql.ExecutionResult;
import graphql.GraphQL;
import graphql.Scalars;
import graphql.schema.GraphQLFieldDefinition;
import graphql.schema.GraphQLObjectType;
import graphql.schema.GraphQLSchema;

/**
 * com.chris.graphql.HelloWorld
 * <p>
 * Author: ChrisYe
 * Date: 11/28/2017
 */

public class HelloWorld {

    public static void main(String... args) {
        //  Programmatically
        GraphQLObjectType queryType = GraphQLObjectType.newObject()
                .name("Query")
                .field(GraphQLFieldDefinition.newFieldDefinition()
                        .name("hello")
                        .type(Scalars.GraphQLString)
                        .dataFetcher(env -> "world"))
                .build();

        //Query by
        GraphQLSchema graphQLSchema = GraphQLSchema.newSchema()
                .query(queryType)
                .build();
        GraphQL build = GraphQL.newGraphQL(graphQLSchema).build();
        ExecutionResult executionResult = build.execute("query Query {hello}");

        System.out.println(executionResult.getData().toString());
    }

}
```
Run the main method, and get the result:
```js
{hello=world}
```
OK, that's it. In this example, we define the schema programmatically.  
First we define a `GraphQLObjectType`, which named `Query` and have field `hello`, the type of it is `String`, also we define a `static datfetcher` for this field, let it return the value of `world` for field `hello`.  
Second we generate the `schema` and build a executable `GraphQL` object vai this schema.  
Third we send a query string `query Query {hello}` to the `GraphQL` to get the result.
We should know that we could define the schema in 3 ways.

* By String
* By IDL file
* By Program  

By String
```java
 // IDL string schema
 String schema = "type Query{hello: String}";

SchemaParser schemaParser = new SchemaParser();

TypeDefinitionRegistry typeDefinitionRegistry = schemaParser.parse(schema);

RuntimeWiring runtimeWiring = RuntimeWiring.newRuntimeWiring()
        .type("Query", builder -> builder.dataFetcher("hello", new StaticDataFetcher("world")))
        .build();

SchemaGenerator schemaGenerator = new SchemaGenerator();
GraphQLSchema graphQLSchema = schemaGenerator.makeExecutableSchema(typeDefinitionRegistry, runtimeWiring);
GraphQL build = GraphQL.newGraphQL(graphQLSchema).build();        
```

By IDL file
```java
//  IDL file shcema
File schema = new File(HelloWorld.class.getClassLoader().getResource("helloworld.graphqls").getFile());

SchemaParser schemaParser = new SchemaParser();

TypeDefinitionRegistry typeDefinitionRegistry = schemaParser.parse(schema);

RuntimeWiring runtimeWiring = RuntimeWiring.newRuntimeWiring()
        .type("Query", builder -> builder.dataFetcher("hello", new StaticDataFetcher("world")))
        .build();

SchemaGenerator schemaGenerator = new SchemaGenerator();
GraphQLSchema graphQLSchema = schemaGenerator.makeExecutableSchema(typeDefinitionRegistry, runtimeWiring);
GraphQL build = GraphQL.newGraphQL(graphQLSchema).build();
```

The file in resource folder named `helloworld.graphqls`
```js
type Query {
    hello: String
}
```
By program
```java
//  Programmatically
        GraphQLObjectType queryType = GraphQLObjectType.newObject()
                .name("Query")
                .field(GraphQLFieldDefinition.newFieldDefinition()
                        .name("hello")
                        .type(Scalars.GraphQLString)
                        .dataFetcher(env -> "world"))
                .build();

        //Query by
        GraphQLSchema graphQLSchema = GraphQLSchema.newSchema()
                .query(queryType)
                .build();
        GraphQL build = GraphQL.newGraphQL(graphQLSchema).build();
```
Let's get more deep on the GraphQL java, this time, we will make every thing together, and we try to fullfill more points & functions on GraphQL Java

* Schema - Object
* Schema - Interface
* Schema - Enumeration
* Schema - Variable & attrubute
* Schema parse cache
* Data fetcher
* Type resolver
* Execution input
* Transfer result to regular json

So let's start from another example
Step one, define the schema, put it in the resource folder
```js
type Query {
    patent_biblio: PatentBiblio
    legal: Legal
    patent_biblio_legal: PatentBiblioAndLegal

}

interface Patent {
    id(patentId: String): String!
    pn: String!
}

type PatentBiblio implements Patent{
    id(patentId: String): String!
    pn: String!
    apno: String
    ans: Assignee
    familyType: FamilyType
}

type Assignee implements Person{
    name: String!
    lang: String!
}

interface Person {
    name: String!
}

type Legal {
    legalStatus:[String]
    eventStatus:[String]
    l001ep:String
}

union PatentBiblioAndLegal = PatentBiblio | Legal

enum FamilyType {
    ORIGINAL
    INPADOC
}

type QueryLitigation {
    litigation: Litigation
}

type Litigation {
    defendant(queryInput: LitigationQueryInput): String
    plaintiff: String
}

input LitigationQueryInput {
    patentId: String
}
```
Step two, write test class, NestObjTest.class
```java
package com.chris.graphql;

import com.chris.graphql.entity.Assignee;
import com.chris.graphql.entity.FamilyType;
import com.chris.graphql.entity.Legal;
import com.chris.graphql.entity.PatentBiblio;
import com.chris.graphql.entity.PatentBiblioAndLegal;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.github.benmanes.caffeine.cache.Cache;
import com.github.benmanes.caffeine.cache.Caffeine;

import java.io.File;
import java.util.Arrays;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

import graphql.ExecutionInput;
import graphql.ExecutionResult;
import graphql.GraphQL;
import graphql.execution.preparsed.PreparsedDocumentEntry;
import graphql.schema.DataFetcher;
import graphql.schema.GraphQLObjectType;
import graphql.schema.GraphQLSchema;
import graphql.schema.idl.RuntimeWiring;
import graphql.schema.idl.SchemaGenerator;
import graphql.schema.idl.SchemaParser;
import graphql.schema.idl.TypeDefinitionRegistry;
import graphql.schema.idl.TypeRuntimeWiring;

/**
 * Created by ye830 on 12/2/2017.
 */
public class NestObjTest {
    public static void main(String... args) {

        // Define static value
        PatentBiblio patentBiblio = new PatentBiblio();
        patentBiblio.setId("88d378a5-3909-4e9c-82de-55dd70e4685c");
        patentBiblio.setPn("IL168994A");
        patentBiblio.setApno("IL168994");
        patentBiblio.setFamilyType(FamilyType.INPADOC);

        Assignee assignee = new Assignee();
        assignee.setName("ChrisYe");
        assignee.setLang("EN");

        patentBiblio.setAns(assignee);

        Legal legal = new Legal();
        legal.setLegalStatus(Arrays.asList("1", "2"));
        legal.setEventStatus(Arrays.asList("61", "62"));
        legal.setL001ep("EP");

        PatentBiblioAndLegal patentBiblioAndLegal = new PatentBiblioAndLegal();
        patentBiblioAndLegal.setPatentBiblio(patentBiblio);
        patentBiblioAndLegal.setLegal(legal);


        // Define the schema
        File schema = new File(NestObjTest.class.getClassLoader().getResource("nestobj.graphqls").getPath());

        // Define the TypeDefineRegistry
        TypeDefinitionRegistry typeDefinitionRegistry = new SchemaParser().parse(schema);

        // Define the DataFetcher
        DataFetcher patentIdDataFetcher = env -> {
            Object patentId = env.getArgument("patentId");
            if (patentId != null) {
                return patentId;
            }
            return patentBiblio.getId();
        };

        // Define the run time wiring, map the value bean and DataFetcher to the type or fields
        RuntimeWiring runtimeWiring = RuntimeWiring.newRuntimeWiring()
                .type("Query", builder -> builder
                        // patent_biblio -> value bean
                        // patent_biblio_legal -> value bean
                        .dataFetcher("patent_biblio", env -> patentBiblio)
                        .dataFetcher("patent_biblio_legal", env -> legal)
                )
                // PatentBiblio -> id -> patentIdDataFetcher
                .type("PatentBiblio", builder -> builder
                        .dataFetcher("id", patentIdDataFetcher))
                // Define the type resolver for the interface Patent
                .type(TypeRuntimeWiring.newTypeWiring("Patent").typeResolver(env -> {
                    Object javaObject = env.getObject();
                    if (javaObject instanceof PatentBiblio) {
                        return (GraphQLObjectType) env.getSchema().getType("PatentBiblio");
                    } else {
                        return (GraphQLObjectType) env.getSchema().getType("PatentBiblio");
                    }
                }))
                // Define the typeResolver for the interface Person
                .type(TypeRuntimeWiring.newTypeWiring("Person").typeResolver(env -> {
                    Object javaObject = env.getObject();
                    if (javaObject instanceof Assignee) {
                        return (GraphQLObjectType) env.getSchema().getType("Assignee");
                    } else {
                        return (GraphQLObjectType) env.getSchema().getType("Assignee");
                    }
                }))
                // Define the typeResolver for the union type PatentBiblioAndLegal
                .type(TypeRuntimeWiring.newTypeWiring("PatentBiblioAndLegal").typeResolver(env -> {
                    Object javaObject = env.getObject();
                    if (javaObject instanceof PatentBiblio) {
                        return (GraphQLObjectType) env.getSchema().getType("PatentBiblio");
                    }
                    if (javaObject instanceof Legal) {
                        return (GraphQLObjectType) env.getSchema().getType("Legal");
                    } else {
                        return (GraphQLObjectType) env.getSchema().getType("PatentBiblio");
                    }
                }))
                .build();

        // Define the GraphQLSchema
        GraphQLSchema graphQLSchema = new SchemaGenerator().makeExecutableSchema(typeDefinitionRegistry, runtimeWiring);

        // Define the cache, using for documentation parsing
        Cache<String, PreparsedDocumentEntry> cache = Caffeine.newBuilder().maximumSize(10_000).build();

        // Define the GraphQL
        GraphQL graphQL = GraphQL.newGraphQL(graphQLSchema)
                .preparsedDocumentProvider(cache::get)
                .build();

        // Define the query string
        String queryString = "query Query($patentId: String!){patent_biblio {id(patentId: $patentId) pn apno ans{name lang} familyType}}";
        
        // Define the variableMap
        Map<String, Object> variableMap = new HashMap<>();
        variableMap.put("patentId", "1111111");

        // Define the ExecutionInput
        ExecutionInput executionInput = ExecutionInput.newExecutionInput()
                .query(queryString)
                .operationName("Query")
                .variables(variableMap)
                .build();

        Long startTime = new Date().getTime();

        // Turn it into regular json
        ExecutionResult executionResult = graphQL.execute(executionInput);
        Map<String, Object> specificationResult = executionResult.toSpecification();

        ObjectMapper objectMapper = new ObjectMapper();
        try {
            System.out.println(objectMapper.writeValueAsString(specificationResult));
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }

        Long endTime = new Date().getTime();

        System.out.println("Cost:" + String.valueOf(endTime - startTime) + " ms");

        // Execute the query once more, compare the time cost to check if cache works
        startTime = new Date().getTime();
        queryString = "query Query($patentId: String!){patent_biblio {id(patentId: $patentId) pn apno ans{name lang} familyType}}";
        variableMap = new HashMap<>();
        variableMap.put("patentId", "1111111");
        executionInput = ExecutionInput.newExecutionInput()
                .query(queryString)
                .operationName("Query")
                .variables(variableMap)
                .build();

        executionResult = graphQL.execute(executionInput);
        specificationResult = executionResult.toSpecification();

        objectMapper = new ObjectMapper();
        try {
            System.out.println(objectMapper.writeValueAsString(specificationResult));
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }

        endTime = new Date().getTime();

        System.out.println("Cost:" + String.valueOf(endTime - startTime) + " ms");
    }
}
```
Step 3, define some beans, correspondence with the schema
```java
/**
 * Created by ye830 on 12/2/2017.
 */
public class Assignee extends Person {
    private String lang;

    public String getLang() {
        return lang;
    }

    public void setLang(String lang) {
        this.lang = lang;
    }
}
```
```java
package com.chris.graphql.entity;

/**
 * Created by ye830 on 12/8/2017.
 */
public enum FamilyType {
    ORIGINAL("ORIGINAL"),
    INPADOC("INPADOC");

    private String familyType;

    private FamilyType(String familyType){
        this.familyType = familyType;

    }

    public String getFamilyType() {
        return familyType;
    }

    public void setFamilyType(String familyType) {
        this.familyType = familyType;
    }
}
```
```java
package com.chris.graphql.entity;

import java.util.List;

/**
 * Created by ye830 on 12/8/2017.
 */
public class Legal {
    private String l001ep;
    private List<String> legalStatus;
    private List<String> eventStatus;

    public String getL001ep() {
        return l001ep;
    }

    public void setL001ep(String l001ep) {
        this.l001ep = l001ep;
    }

    public List<String> getLegalStatus() {
        return legalStatus;
    }

    public void setLegalStatus(List<String> legalStatus) {
        this.legalStatus = legalStatus;
    }

    public List<String> getEventStatus() {
        return eventStatus;
    }

    public void setEventStatus(List<String> eventStatus) {
        this.eventStatus = eventStatus;
    }
}
```
```java
package com.chris.graphql.entity;

/**
 * Created by ye830 on 12/2/2017.
 */
public class PatentBiblio extends Patent {
    private String apno;
    private Person ans;
    private FamilyType familyType;

    public String getApno() {
        return apno;
    }

    public void setApno(String apno) {
        this.apno = apno;
    }

    public Person getAns() {
        return ans;
    }

    public void setAns(Person ans) {
        this.ans = ans;
    }

    public FamilyType getFamilyType() {
        return familyType;
    }

    public void setFamilyType(FamilyType familyType) {
        this.familyType = familyType;
    }
}
```
```java
package com.chris.graphql.entity;

/**
 * Created by ye830 on 12/2/2017.
 */
public class Person {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```
```java
package com.chris.graphql.entity;

/**
 * Created by ye830 on 12/2/2017.
 */
public class PatentBiblioAndLegal {
    private PatentBiblio patentBiblio;
    private Legal legal;

    public PatentBiblio getPatentBiblio() {
        return patentBiblio;
    }

    public void setPatentBiblio(PatentBiblio patentBiblio) {
        this.patentBiblio = patentBiblio;
    }

    public Legal getLegal() {
        return legal;
    }

    public void setLegal(Legal legal) {
        this.legal = legal;
    }
}
```
Then let's run the code
```js
{"data":{"patent_biblio":{"id":"1111111","pn":"IL168994A","apno":"IL168994","ans":{"name":"ChrisYe","lang":"EN"},"familyType":"INPADOC"}}}
Cost:1699 ms
{"data":{"patent_biblio":{"id":"1111111","pn":"IL168994A","apno":"IL168994","ans":{"name":"ChrisYe","lang":"EN"},"familyType":"INPADOC"}}}
Cost:3 ms
```

The whole pictures of this example is like below:
![GraphQL_NestObject.png]({{ site.url }}/images/graphql/GraphQL_NestObject.png)

From this example, we could see that:

* DataFetcher is a function interface, we could use the environment to get the variables
* GraphQL Java will automatically map the bean fields with the data result 
* TypeResolver is needed when some type is implement the interface
* The cache works fine for the documentation parsing, suggest to use Caffeine Cache
* Use `toSpecification` to turn the result into Map object, so we can turn it into regular json object by using serialize tools

So let's write more examples for `Exception`, `Validation`, `Batch Get`, `Asynchronization fetch`, 

## Exception
```java
package com.chris.graphql;

import com.chris.graphql.entity.Litigation;

import java.io.File;
import java.util.HashMap;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;
import java.util.Optional;

import graphql.ErrorType;
import graphql.ExceptionWhileDataFetching;
import graphql.ExecutionInput;
import graphql.ExecutionResult;
import graphql.GraphQL;
import graphql.GraphQLError;
import graphql.execution.AsyncExecutionStrategy;
import graphql.language.SourceLocation;
import graphql.schema.GraphQLObjectType;
import graphql.schema.GraphQLSchema;
import graphql.schema.idl.RuntimeWiring;
import graphql.schema.idl.SchemaGenerator;
import graphql.schema.idl.SchemaParser;
import graphql.schema.idl.TypeDefinitionRegistry;
import graphql.schema.idl.TypeRuntimeWiring;

/**
 * Created by ye830 on 12/2/2017.
 */
public class ExceptionTest {
    public static void main(String... args) {
        Litigation litigation  = new Litigation();
        litigation.setDefendant("Zhang San");
        litigation.setPlaintiff("Li Si");

        // Define the schema
        File schema = new File(ExceptionTest.class.getClassLoader().getResource("inputobj.graphqls").getPath());

        // Define the TypeDefineRegistry
        TypeDefinitionRegistry typeDefinitionRegistry = new SchemaParser().parse(schema);

        // Define the run time wiring
        RuntimeWiring runtimeWiring = RuntimeWiring.newRuntimeWiring()
                .type("LitigationQuery", builder -> builder
                        .dataFetcher("litigation", env -> {
                            throw new CustomRuntimeException();
                        })
                )
                .type("Litigation", builder -> builder
                        .dataFetcher("defendant", env -> {
                            Object queryInput = env.getArgument("queryInput");
                            if(queryInput != null){
                                Object patentId = Optional.ofNullable(((HashMap)queryInput).get("patentId")).orElse("6666666666666");
                                return patentId;
                            }
                            return "6666666666666";
                        }))
                .type(TypeRuntimeWiring.newTypeWiring("Litigation").typeResolver(env -> {
                    Object javaObject = env.getObject();
                    if (javaObject instanceof Litigation) {
                        return (GraphQLObjectType) env.getSchema().getType("Litigation");
                    } else {
                        return (GraphQLObjectType) env.getSchema().getType("Litigation");
                    }
                }))
                .build();

        // Define the GraphQLSchema
        GraphQLSchema graphQLSchema = new SchemaGenerator().makeExecutableSchema(typeDefinitionRegistry, runtimeWiring);

        // Define the GraphQL
        GraphQL graphQL = GraphQL.newGraphQL(graphQLSchema)
//                .queryExecutionStrategy(new AsyncExecutionStrategy(handlerParameters -> {
//                    ExceptionWhileDataFetching error = new ExceptionWhileDataFetching(handlerParameters.getPath(), handlerParameters.getException(), handlerParameters.getField().getSourceLocation());
//                    handlerParameters.getExecutionContext().addError(error, handlerParameters.getPath());
//                }))
                .build();

        String queryString = "query LitigationQuery($input: LitigationQueryInput){litigation{defendant(queryInput: $input)}}";

        Map<String,Object> variableMap = new HashMap<>();
        Map<String,Object> litigationQueryInput = new HashMap<>();
        litigationQueryInput.put("patentId", "2222222222222");
        variableMap.put("input", litigationQueryInput);
        ExecutionInput executionInput = ExecutionInput.newExecutionInput()
                .query(queryString)
                .operationName("LitigationQuery")
                .variables(variableMap)
                .build();
        ExecutionResult executionResult = graphQL.execute(executionInput);
        executionResult.getErrors().stream().forEach(error -> {
            System.out.println("my customized exception:");
            System.out.println(error.getExtensions().get("foo"));
            System.out.println(error.getExtensions().get("fizz"));
            System.out.println(error.getExtensions().get("message"));
        });


    }

    static class CustomRuntimeException extends RuntimeException implements GraphQLError {

        @Override
        public Map<String, Object> getExtensions() {
            Map<String, Object> customAttributes = new LinkedHashMap<>();
            customAttributes.put("foo", "bar");
            customAttributes.put("fizz", "whizz");
            return customAttributes;
        }

        @Override
        public List<SourceLocation> getLocations (){
            return null;
        }

        @Override
        public ErrorType getErrorType() {
            return ErrorType.DataFetchingException;
        }
    }
}
```
If an exception happens during the data fetcher call, GraphQL will throws the `graphql.ExceptionWhileDataFetching` error, and if the exception you throw is the `GraphQLError`, then GraphQL will transfer it into the `graphql.ExceptionWhileDataFetching`
So this means you can customize your own exception.
In the example above, we define a customized exception named `CustomRuntimeException`, implements the GraphQLError, and add two extention attributes, then run the code, the result will be
```java
19:47:07.933 [main] WARN  g.e.SimpleDataFetcherExceptionHandler - Exception while fetching data (/litigation) : null
com.chris.graphql.ExceptionTest$CustomRuntimeException: null
	at com.chris.graphql.ExceptionTest.lambda$null$0(ExceptionTest.java:47)
	at graphql.execution.ExecutionStrategy.fetchField(ExecutionStrategy.java:219)
	at graphql.execution.ExecutionStrategy.resolveField(ExecutionStrategy.java:165)
	at graphql.execution.AsyncExecutionStrategy.execute(AsyncExecutionStrategy.java:55)
	at graphql.execution.Execution.executeOperation(Execution.java:154)
	at graphql.execution.Execution.execute(Execution.java:98)
	at graphql.GraphQL.execute(GraphQL.java:546)
	at graphql.GraphQL.parseValidateAndExecute(GraphQL.java:488)
	at graphql.GraphQL.executeAsync(GraphQL.java:463)
	at graphql.GraphQL.execute(GraphQL.java:394)
	at com.chris.graphql.ExceptionTest.main(ExceptionTest.java:91)
my customized exception:
bar
whizz
null
```
Imagine that you don't like the exception message and want to define it by your self, so you define the  `AsyncExecutionStrategy` to change the exception behavior
```java
GraphQL graphQL = GraphQL.newGraphQL(graphQLSchema)
                .queryExecutionStrategy(new AsyncExecutionStrategy(handlerParameters -> {
                    ExceptionWhileDataFetching error = new ExceptionWhileDataFetching(handlerParameters.getPath(), handlerParameters.getException(), handlerParameters.getField().getSourceLocation());
                    handlerParameters.getExecutionContext().addError(error, handlerParameters.getPath());
                }))
                .build();
```


## Asynchronized data fetching
```java
package com.chris.graphql;

import com.chris.graphql.entity.Assignee;
import com.chris.graphql.entity.FamilyType;
import com.chris.graphql.entity.Legal;
import com.chris.graphql.entity.PatentBiblio;
import com.chris.graphql.entity.PatentBiblioAndLegal;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;

import java.io.File;
import java.util.Arrays;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

import graphql.ExecutionInput;
import graphql.ExecutionResult;
import graphql.GraphQL;
import graphql.schema.GraphQLObjectType;
import graphql.schema.GraphQLSchema;
import graphql.schema.idl.RuntimeWiring;
import graphql.schema.idl.SchemaGenerator;
import graphql.schema.idl.SchemaParser;
import graphql.schema.idl.TypeDefinitionRegistry;
import graphql.schema.idl.TypeRuntimeWiring;

/**
 * Created by ye830 on 12/2/2017.
 */
public class AsynTest {
    public static void main(String... args) {

        Long startTime = new Date().getTime();

        // Define static value
        PatentBiblio patentBiblio = new PatentBiblio();
        patentBiblio.setId("88d378a5-3909-4e9c-82de-55dd70e4685c");
        patentBiblio.setPn("IL168994A");
        patentBiblio.setApno("IL168994");
        patentBiblio.setFamilyType(FamilyType.INPADOC);

        Assignee assignee = new Assignee();
        assignee.setName("ChrisYe");
        assignee.setLang("EN");

        patentBiblio.setAns(assignee);


        Legal legal = new Legal();
        legal.setLegalStatus(Arrays.asList("1", "2"));
        legal.setEventStatus(Arrays.asList("61", "62"));
        legal.setL001ep("EP");

        PatentBiblioAndLegal patentBiblioAndLegal = new PatentBiblioAndLegal();
        patentBiblioAndLegal.setPatentBiblio(patentBiblio);
        patentBiblioAndLegal.setLegal(legal);


        // Define the schema
        File schema = new File(AsynTest.class.getClassLoader().getResource("nestobj.graphqls").getPath());

        // Define the TypeDefineRegistry
        TypeDefinitionRegistry typeDefinitionRegistry = new SchemaParser().parse(schema);

        // Define the run time wiring
        RuntimeWiring runtimeWiring = RuntimeWiring.newRuntimeWiring()
                .type("Query", builder -> builder
                        .dataFetcher("patent_biblio", env -> {
                            CompletableFuture future = CompletableFuture.supplyAsync(() -> {
                                try {
                                    System.out.println("i am in patent_biblio");
                                    Thread.sleep(5000);
                                } catch (InterruptedException e) {
                                    e.printStackTrace();
                                }
                                return patentBiblio;
                            });
                            return future;
                        })
                        .dataFetcher("legal", env -> {

                            CompletableFuture future = CompletableFuture.supplyAsync(() -> {
                                try {
                                    System.out.println("i am in patent_biblio_legal");
                                    Thread.sleep(5000);
                                } catch (InterruptedException e) {
                                    e.printStackTrace();
                                }
                                return legal;
                            });
                            return future;
                        })
                )
//                .type("Query", builder -> builder
//                        .dataFetcher("patent_biblio", env -> {
//                            try {
//                                    System.out.println("i am in patent_biblio");
//                                    Thread.sleep(5000);
//                                } catch (InterruptedException e) {
//                                    e.printStackTrace();
//                                }
//                                return patentBiblio;
//                        })
//                        .dataFetcher("legal", env -> {
//                            try {
//                                System.out.println("i am in legal");
//                                Thread.sleep(5000);
//                            } catch (InterruptedException e) {
//                                e.printStackTrace();
//                            }
//                            return legal;
//                        })
//                )
                .type("PatentBiblio", builder -> builder
                        .dataFetcher("id", env -> {
                            Object patentId = env.getArgument("patentId");
                            if (patentId != null) {
                                return patentId;
                            }
                            return patentBiblio.getId();
                        }))
                .type(TypeRuntimeWiring.newTypeWiring("Patent").typeResolver(env -> {
                    Object javaObject = env.getObject();
                    if (javaObject instanceof PatentBiblio) {
                        return (GraphQLObjectType) env.getSchema().getType("PatentBiblio");
                    } else {
                        return (GraphQLObjectType) env.getSchema().getType("PatentBiblio");
                    }
                }))
                .type(TypeRuntimeWiring.newTypeWiring("Person").typeResolver(env -> {
                    Object javaObject = env.getObject();
                    if (javaObject instanceof Assignee) {
                        return (GraphQLObjectType) env.getSchema().getType("Assignee");
                    } else {
                        return (GraphQLObjectType) env.getSchema().getType("Assignee");
                    }
                }))
                .type(TypeRuntimeWiring.newTypeWiring("PatentBiblioAndLegal").typeResolver(env -> {
                    Object javaObject = env.getObject();
                    if (javaObject instanceof PatentBiblio) {
                        return (GraphQLObjectType) env.getSchema().getType("PatentBiblio");
                    }
                    if (javaObject instanceof Legal) {
                        return (GraphQLObjectType) env.getSchema().getType("Legal");
                    } else {
                        return (GraphQLObjectType) env.getSchema().getType("PatentBiblio");
                    }
                }))
                .build();

        // Define the GraphQLSchema
        GraphQLSchema graphQLSchema = new SchemaGenerator().makeExecutableSchema(typeDefinitionRegistry, runtimeWiring);

        // Define the GraphQL
        GraphQL graphQL = GraphQL.newGraphQL(graphQLSchema).build();

        // Execute the query
        // String queryString = "{patent_biblio {id pn apno ans{name lang} familyType}}";

        // Execute the union query
        // String queryString = "{patent_biblio_legal {... on PatentBiblio {id} ... on Legal {legalStatus}}}";
        //ExecutionResult executionResult = graphQL.execute(queryString);

        // Query by ExecutionInput
        String queryString = "query Query($patentId: String!){patent_biblio {id(patentId: $patentId) pn apno ans{name lang} familyType} legal {legalStatus}}";

        Map<String, Object> variableMap = new HashMap<>();
        variableMap.put("patentId", "1111111");
        ExecutionInput executionInput = ExecutionInput.newExecutionInput()
                .query(queryString)
                .operationName("Query")
                .variables(variableMap)
                .build();
        CompletableFuture<ExecutionResult> completableFuture = graphQL.executeAsync(executionInput);

        Map<String, Object> specificationResult;
        try {
            specificationResult = completableFuture.get(10, TimeUnit.SECONDS).toSpecification();
            ObjectMapper objectMapper = new ObjectMapper();
            try {
                System.out.println(objectMapper.writeValueAsString(specificationResult));
            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }

        Long endTime = new Date().getTime();

        System.out.println(endTime - startTime);
    }
}
```

This example is quite similar with the `NestObject.class`, the most different place is we change the datafetcher behavior from return the data result directly into return the CompletableFuture
GraphQL support get the value asynchronized by returning the CompletableFuture in each Datafetcher. So think about that we have several fields and want to get the value concurrently, then we can use 
Completable Future to make this happen. So the result of the example above will be:
```java
i am in patent_biblio
i am in patent_biblio_legal
{"data":{"patent_biblio":{"id":"1111111","pn":"IL168994A","apno":"IL168994","ans":{"name":"ChrisYe","lang":"EN"},"familyType":"INPADOC"},"legal":{"legalStatus":["1","2"]}}}
7736
```

Please notice that, the sentence `i am in patent_biblio` & `i am in patent_biblio_legal` will display at the same time which means GraphQL get the result concurrently.

## Batch data loader & Validation
```java
package com.chris.graphql;

import com.chris.graphql.entity.AWSCredential;
import com.chris.graphql.entity.Patent;
import com.chris.graphql.entity.Person;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;

import org.dataloader.BatchLoader;
import org.dataloader.DataLoader;
import org.dataloader.DataLoaderRegistry;

import java.io.File;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Optional;
import java.util.concurrent.CompletableFuture;
import java.util.function.BiFunction;
import java.util.stream.Collectors;

import graphql.ExecutionInput;
import graphql.ExecutionResult;
import graphql.GraphQL;
import graphql.GraphQLError;
import graphql.annotations.GraphQLTypeResolver;
import graphql.execution.ExecutionPath;
import graphql.execution.instrumentation.ChainedInstrumentation;
import graphql.execution.instrumentation.Instrumentation;
import graphql.execution.instrumentation.dataloader.DataLoaderDispatcherInstrumentation;
import graphql.execution.instrumentation.fieldvalidation.FieldAndArguments;
import graphql.execution.instrumentation.fieldvalidation.FieldValidation;
import graphql.execution.instrumentation.fieldvalidation.FieldValidationEnvironment;
import graphql.execution.instrumentation.fieldvalidation.FieldValidationInstrumentation;
import graphql.execution.instrumentation.fieldvalidation.SimpleFieldValidation;
import graphql.execution.instrumentation.tracing.TracingInstrumentation;
import graphql.schema.DataFetcher;
import graphql.schema.GraphQLSchema;
import graphql.schema.idl.RuntimeWiring;
import graphql.schema.idl.SchemaGenerator;
import graphql.schema.idl.SchemaParser;
import graphql.schema.idl.TypeDefinitionRegistry;

/**
 * Created by ye830 on 12/21/2017.
 */
public class BatchTest {
    public static void main(String... args) {

        BatchLoader<String, Object> patentBatchLoader = keys -> CompletableFuture.supplyAsync(() -> getPatentByPatentIds(keys));

        DataLoader<String, Object> patentDataLoader = new DataLoader(patentBatchLoader);

        DataFetcher patentDataFetcher = env -> patentDataLoader.load(env.getArgument("patentId"));

        DataFetcher offsetDataFetcher = env -> {
            AWSCredential awsCredential = env.getContext();
            System.out.println(awsCredential.getFoo());
            Integer offset = env.getArgument("offset");
            Integer limit = env.getArgument("limit");
            return offset + limit;
        };


        DataFetcher totalDataFetcher = env -> {
            String patentId = env.getArgument("patentId");
            return getTotalCountByPatentId(patentId);
        };

        DataFetcher patentCitationDataFetcher = env -> {
            if (env.getSource() != null) {
                Patent patent = env.getSource();
                return patentDataLoader.loadMany(getPatentCitationIds(patent.getId()));
            }
            return Collections.EMPTY_LIST;
        };

        Long startTime = new Date().getTime();

        // Define the schema
        File schemaFile = new File(DataLoader.class.getClassLoader().getResource("dataloader.graphqls").getPath());

        // Define the type definition registry
        TypeDefinitionRegistry typeDefinitionRegistry = new SchemaParser().parse(schemaFile);


        // Define the runtime wiring
        RuntimeWiring runtimeWiring = RuntimeWiring.newRuntimeWiring()
                .type("Query", builder -> builder
                        .dataFetcher("patent", patentDataFetcher)
                        .dataFetcher("offset", offsetDataFetcher)
                        .dataFetcher("total", totalDataFetcher)
                )
                .type("Patent", builder -> builder
                        .dataFetcher("citations", patentCitationDataFetcher))
                .build();

        // Define the graphQL schema
        GraphQLSchema graphQLSchema = new SchemaGenerator().makeExecutableSchema(typeDefinitionRegistry, runtimeWiring);

        // Define data loader instrumentation
        DataLoaderRegistry registry = new DataLoaderRegistry();
        registry.register("patent", patentDataLoader);
        DataLoaderDispatcherInstrumentation dataLoaderDispatcherInstrumentation = new DataLoaderDispatcherInstrumentation(registry);

        // Define field validation instrumentation
        ExecutionPath fieldPath = ExecutionPath.parse("/patent");
        FieldValidation fieldValidation = new SimpleFieldValidation()
                .addRule(fieldPath, (fieldAndArguments, fieldValidationEnvironment) -> {
                    Integer offset = fieldAndArguments.getArgumentValue("offset");
                    if(offset > 1000){
                        return Optional.of(fieldValidationEnvironment.mkError("offset should less equal 1000", fieldAndArguments));
                    }
                    return Optional.empty();
                });
        FieldValidationInstrumentation fieldValidationInstrumentation = new FieldValidationInstrumentation(fieldValidation);

        TracingInstrumentation tracingInstrumentation  = new TracingInstrumentation();

        // Define the instrumentation chain
        List<Instrumentation> chainedList = new ArrayList<>();
        chainedList.add(dataLoaderDispatcherInstrumentation);
        chainedList.add(fieldValidationInstrumentation);
        chainedList.add(tracingInstrumentation);
        ChainedInstrumentation chainedInstrumentation = new ChainedInstrumentation(chainedList);

        // Define the graphQL
        GraphQL graphQL = GraphQL.newGraphQL(graphQLSchema)
                .instrumentation(chainedInstrumentation)
                .build();

        // Define the execution
        //String queryString = "query Query($patentId: String!,$offset: Int,$limit: Int){patent(patentId: $patentId, offset: $offset, limit: $limit) {id pn my_name person {name} citations{id pn citations{id pn citations{id pn}}}} total(patentId: $patentId) offset(offset: $offset, limit: $limit)}";
        String queryString = "query Query($patentId: String!, $offset: Int, $limit: Int) {\n" +
                "  patent(patentId: $patentId, offset: $offset, limit: $limit) {\n" +
                "    id\n" +
                "    pn\n" +
                "    my_name\n" +
                "    person {\n" +
                "      name\n" +
                "    }\n" +
                "    citations {\n" +
                "      ...citationPatent\n" +
                "      citations {\n" +
                "        ...citationPatent\n" +
                "        citations {\n" +
                "          ...citationPatent\n" +
                "        }\n" +
                "      }\n" +
                "    }\n" +
                "  }\n" +
                "  total(patentId: $patentId)\n" +
                "  offset(offset: $offset, limit: $limit)\n" +
                "}\n" +
                "\n" +
                "fragment citationPatent on Patent {\n" +
                "  id\n" +
                "  pn\n" +
                "}";
        Map<String, Object> variableMap = new HashMap<>();
        variableMap.put("patentId", "1");
        variableMap.put("offset", "1000");
        variableMap.put("limit", "10");

        // Define the context, for example: AWS Credential
        AWSCredential awsCredential = new AWSCredential("foo", "bar");

        ExecutionInput executionInput = ExecutionInput.newExecutionInput()
                .query(queryString)
                .context(awsCredential)
                .operationName("Query")
                .variables(variableMap)
                .build();

        ExecutionResult executionResult = graphQL.execute(executionInput);
        Map<String, Object> specificationResult = executionResult.toSpecification();

        ObjectMapper objectMapper = new ObjectMapper();
        try {
            System.out.println(objectMapper.writeValueAsString(specificationResult));
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }

        Long endTime = new Date().getTime();

        System.out.println("Total Cost: " + (endTime - startTime) + " ms");
    }


    static Map<String, List<String>> patentCitationIdMap = new HashMap();

    static {
        patentCitationIdMap.put("1", Arrays.asList(new String[]{"2", "3", "4"}));
        patentCitationIdMap.put("2", Arrays.asList(new String[]{"1", "3", "4"}));
        patentCitationIdMap.put("3", Arrays.asList(new String[]{"2", "3", "4"}));
        patentCitationIdMap.put("4", Arrays.asList(new String[]{"1", "2", "3"}));
    }

    static List<String> getPatentCitationIds(String patentId) {
        return patentCitationIdMap.get(patentId);
    }

    static List<Object> getPatentByPatentIds(List<String> patentIds) {
        List<Object> patentCitations = patentIds.stream().map(id -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            Patent patent = new Patent();
            patent.setId(id);
            patent.setPn("AAA");
            patent.setMyName("Patent Demo");
            Person person = new Person();
            person.setName("Chris");
            patent.setPerson(person);
            return patent;
        }).collect(Collectors.toList());
        return patentCitations;
    }

    static Integer getTotalCountByPatentId(String patentId){
        return 100;
    }
}
```
Sometimes, we need to get the data in the batch model, for example, we need to get one patent, and this patent have many leaves patents, so in this situation,
we could use the `Dataloader` on the DataFetcher. The result of the example above could be:
```java
foo
{"data":{"patent":{"id":"1","pn":"AAA","my_name":null,"person":{"name":"Chris"},"citations":[{"id":"2","pn":"AAA","citations":[{"id":"1","pn":"AAA","citations":[{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"3","pn":"AAA","citations":[{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"4","pn":"AAA","citations":[{"id":"1","pn":"AAA"},{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"}]}]},{"id":"3","pn":"AAA","citations":[{"id":"2","pn":"AAA","citations":[{"id":"1","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"3","pn":"AAA","citations":[{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"4","pn":"AAA","citations":[{"id":"1","pn":"AAA"},{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"}]}]},{"id":"4","pn":"AAA","citations":[{"id":"1","pn":"AAA","citations":[{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"2","pn":"AAA","citations":[{"id":"1","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"3","pn":"AAA","citations":[{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]}]}]},"total":100,"offset":1010},"extensions":{"tracing":{"version":1,"startTime":"2018-01-06T12:14:39.764Z","endTime":"2018-01-06T12:14:49.731Z","duration":9978962056,"parsing":{"startOffset":27126149,"duration":10353775},"validation":{"startOffset":56870281,"duration":28084143},"execution":{"resolvers":[{"path":["total"],"parentType":"Query","returnType":"Int","fieldName":"total","startOffset":5651396258,"duration":80592},{"path":["offset"],"parentType":"Query","returnType":"Int","fieldName":"offset","startOffset":5654907323,"duration":290279},{"path":["patent"],"parentType":"Query","returnType":"Patent","fieldName":"patent","startOffset":5640733240,"duration":1099040620},{"path":["patent","id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":6756011623,"duration":1500992},{"path":["patent","pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":6757782837,"duration":76216},{"path":["patent","my_name"],"parentType":"Patent","returnType":"String","fieldName":"my_name","startOffset":6758077127,"duration":1296410},{"path":["patent","person"],"parentType":"Patent","returnType":"Person","fieldName":"person","startOffset":6759695543,"duration":75122},{"path":["patent","person","name"],"parentType":"Person","returnType":"String","fieldName":"name","startOffset":6759985093,"duration":68923},{"path":["patent","citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":6760451873,"duration":3042131320},{"path":["patent","citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9808188936,"duration":130552},{"path":["patent","citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9808540115,"duration":76581},{"path":["patent","citations",0,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9808802315,"duration":188535},{"path":["patent","citations",0,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9809279671,"duration":56524},{"path":["patent","citations",0,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9809511967,"duration":47043},{"path":["patent","citations",0,"citations",0,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9810061893,"duration":92991},{"path":["patent","citations",0,"citations",0,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9814951784,"duration":156809},{"path":["patent","citations",0,"citations",0,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9815470348,"duration":72934},{"path":["patent","citations",0,"citations",0,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9816725185,"duration":627966},{"path":["patent","citations",0,"citations",0,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9819106860,"duration":222814},{"path":["patent","citations",0,"citations",0,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9819789162,"duration":71475},{"path":["patent","citations",0,"citations",0,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9820038597,"duration":109402},{"path":["patent","citations",0,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9821130062,"duration":101743},{"path":["patent","citations",0,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9821394084,"duration":47043},{"path":["patent","citations",0,"citations",1,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9821611794,"duration":77675},{"path":["patent","citations",0,"citations",1,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9823231668,"duration":357744},{"path":["patent","citations",0,"citations",1,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9824039053,"duration":116330},{"path":["patent","citations",0,"citations",1,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9824609400,"duration":84604},{"path":["patent","citations",0,"citations",1,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9824909525,"duration":98462},{"path":["patent","citations",0,"citations",1,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9825267269,"duration":67829},{"path":["patent","citations",0,"citations",1,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9826787952,"duration":91168},{"path":["patent","citations",0,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9831843040,"duration":143316},{"path":["patent","citations",0,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9832200419,"duration":66735},{"path":["patent","citations",0,"citations",2,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9832735028,"duration":630154},{"path":["patent","citations",0,"citations",2,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9833677706,"duration":63453},{"path":["patent","citations",0,"citations",2,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9833886663,"duration":264388},{"path":["patent","citations",0,"citations",2,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9834603974,"duration":54701},{"path":["patent","citations",0,"citations",2,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9834968646,"duration":54336},{"path":["patent","citations",0,"citations",2,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9835346082,"duration":47043},{"path":["patent","citations",0,"citations",2,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9835549934,"duration":47407},{"path":["patent","citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9835967119,"duration":816866},{"path":["patent","citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9837155950,"duration":66735},{"path":["patent","citations",1,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9837411586,"duration":78040},{"path":["patent","citations",1,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9837758389,"duration":3663498},{"path":["patent","citations",1,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9841666947,"duration":476626},{"path":["patent","citations",1,"citations",0,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9842329556,"duration":75487},{"path":["patent","citations",1,"citations",0,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9842690217,"duration":62724},{"path":["patent","citations",1,"citations",0,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9843400234,"duration":170302},{"path":["patent","citations",1,"citations",0,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9844302798,"duration":67464},{"path":["patent","citations",1,"citations",0,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9844866217,"duration":150245},{"path":["patent","citations",1,"citations",0,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9846153875,"duration":117789},{"path":["patent","citations",1,"citations",0,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9846488644,"duration":42667},{"path":["patent","citations",1,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9846870456,"duration":39384},{"path":["patent","citations",1,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9847053886,"duration":35738},{"path":["patent","citations",1,"citations",1,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9847243516,"duration":68558},{"path":["patent","citations",1,"citations",1,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9847528689,"duration":38656},{"path":["patent","citations",1,"citations",1,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9847685498,"duration":30633},{"path":["patent","citations",1,"citations",1,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9847936393,"duration":106484},{"path":["patent","citations",1,"citations",1,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9848816712,"duration":53607},{"path":["patent","citations",1,"citations",1,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9849164245,"duration":49960},{"path":["patent","citations",1,"citations",1,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9849335276,"duration":34279},{"path":["patent","citations",1,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9850513167,"duration":49231},{"path":["patent","citations",1,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9850688210,"duration":48501},{"path":["patent","citations",1,"citations",2,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9850880392,"duration":81687},{"path":["patent","citations",1,"citations",2,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9851231207,"duration":136388},{"path":["patent","citations",1,"citations",2,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9852709224,"duration":66370},{"path":["patent","citations",1,"citations",2,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9853526455,"duration":75851},{"path":["patent","citations",1,"citations",2,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9853759115,"duration":44855},{"path":["patent","citations",1,"citations",2,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9854280232,"duration":65641},{"path":["patent","citations",1,"citations",2,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9865397267,"duration":80228},{"path":["patent","citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9866131353,"duration":53971},{"path":["patent","citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9866304937,"duration":37196},{"path":["patent","citations",2,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9866667421,"duration":727886},{"path":["patent","citations",2,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9869150110,"duration":66735},{"path":["patent","citations",2,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9869392617,"duration":39385},{"path":["patent","citations",2,"citations",0,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9869612879,"duration":91533},{"path":["patent","citations",2,"citations",0,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9870719659,"duration":63818},{"path":["patent","citations",2,"citations",0,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9870908195,"duration":33185},{"path":["patent","citations",2,"citations",0,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9878985322,"duration":114871},{"path":["patent","citations",2,"citations",0,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9897527814,"duration":103932},{"path":["patent","citations",2,"citations",0,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9897913637,"duration":41573},{"path":["patent","citations",2,"citations",0,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9898777181,"duration":55431},{"path":["patent","citations",2,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9899199837,"duration":55794},{"path":["patent","citations",2,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9899377797,"duration":34643},{"path":["patent","citations",2,"citations",1,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9899598059,"duration":88980},{"path":["patent","citations",2,"citations",1,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9899884326,"duration":38291},{"path":["patent","citations",2,"citations",1,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9900031654,"duration":32821},{"path":["patent","citations",2,"citations",1,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9900275984,"duration":39385},{"path":["patent","citations",2,"citations",1,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9900421124,"duration":30633},{"path":["patent","citations",2,"citations",1,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9900621694,"duration":40114},{"path":["patent","citations",2,"citations",1,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9900751882,"duration":29538},{"path":["patent","citations",2,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9900993660,"duration":31361},{"path":["patent","citations",2,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9901143540,"duration":35373},{"path":["patent","citations",2,"citations",2,"citations"],"parentType":"Patent","returnType":"[Patent]","fieldName":"citations","startOffset":9901285762,"duration":55795},{"path":["patent","citations",2,"citations",2,"citations",0,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9901484144,"duration":31726},{"path":["patent","citations",2,"citations",2,"citations",0,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9901599745,"duration":28809},{"path":["patent","citations",2,"citations",2,"citations",1,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9901780622,"duration":31727},{"path":["patent","citations",2,"citations",2,"citations",1,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9901928315,"duration":32091},{"path":["patent","citations",2,"citations",2,"citations",2,"id"],"parentType":"Patent","returnType":"String!","fieldName":"id","startOffset":9902142013,"duration":32820},{"path":["patent","citations",2,"citations",2,"citations",2,"pn"],"parentType":"Patent","returnType":"String!","fieldName":"pn","startOffset":9902261625,"duration":31727}]}}}}
Total Cost: 11627 ms
```
May be you can't catch the point of why we need to do this, but once if we compare the time cost between this example result and the normal way to get the result, then we could find a huge different
So let's change the datafetcher into the normal way
```java
package com.chris.graphql;

import com.chris.graphql.entity.Patent;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;

import org.dataloader.BatchLoader;
import org.dataloader.DataLoader;
import org.dataloader.DataLoaderRegistry;

import java.io.File;
import java.util.Arrays;
import java.util.Collections;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.stream.Collectors;

import graphql.ExecutionInput;
import graphql.ExecutionResult;
import graphql.GraphQL;
import graphql.execution.instrumentation.dataloader.DataLoaderDispatcherInstrumentation;
import graphql.schema.DataFetcher;
import graphql.schema.GraphQLSchema;
import graphql.schema.idl.RuntimeWiring;
import graphql.schema.idl.SchemaGenerator;
import graphql.schema.idl.SchemaParser;
import graphql.schema.idl.TypeDefinitionRegistry;

/**
 * Created by ye830 on 12/21/2017.
 */
public class SingleTest {
    public static void main(String... args) {

        DataFetcher patentDataFetcher = env -> getPatentByPatentIds(Arrays.asList(((String)env.getArgument("patentId")))).get(0);

        DataFetcher patentCitationDataFetcher = env -> {
            if(env.getSource()!=null){
                Patent patent = env.getSource();
                List<String> patentIds = getPatentCitationIds(patent.getId());
                return getPatentByPatentIds(patentIds);
            }
            return Collections.EMPTY_LIST;
        };

        Long startTime = new Date().getTime();

        // Define the schema
        File schemaFile = new File(DataLoader.class.getClassLoader().getResource("dataloader.graphqls").getPath());

        // Define the type definition registry
        TypeDefinitionRegistry typeDefinitionRegistry = new SchemaParser().parse(schemaFile);



        // Define the runtime wiring
        RuntimeWiring runtimeWiring = RuntimeWiring.newRuntimeWiring()
                .type("Query", builder -> builder
                        .dataFetcher("patent", patentDataFetcher)
                )
                .type("Patent", builder -> builder
                        .dataFetcher("citations", patentCitationDataFetcher))
                .build();

        // Define the graphQL schema
        GraphQLSchema graphQLSchema = new SchemaGenerator().makeExecutableSchema(typeDefinitionRegistry, runtimeWiring);

        // Define the graphQL
        GraphQL graphQL = GraphQL.newGraphQL(graphQLSchema)
                .build();

        // Define the execution
        String queryString = "query Query($patentId: String!){patent(patentId: $patentId) {id pn citations{id pn citations{id pn citations{id pn}}}}}";
        Map<String, Object> variableMap = new HashMap<>();
        variableMap.put("patentId", "1");
        ExecutionInput executionInput = ExecutionInput.newExecutionInput()
                .query(queryString)
                .operationName("Query")
                .variables(variableMap)
                .build();

        ExecutionResult executionResult = graphQL.execute(executionInput);
        Map<String, Object> specificationResult = executionResult.toSpecification();

        ObjectMapper objectMapper = new ObjectMapper();
        try {
            System.out.println(objectMapper.writeValueAsString(specificationResult));
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }

        Long endTime = new Date().getTime();

        System.out.println("Total Cost: "+(endTime - startTime)+" ms");
    }



    static Map<String, List<String>> patentCitationIdMap = new HashMap();
    static{
        patentCitationIdMap.put("1", Arrays.asList(new String[]{"2","3","4"}));
        patentCitationIdMap.put("2", Arrays.asList(new String[]{"1","3","4"}));
        patentCitationIdMap.put("3", Arrays.asList(new String[]{"2","3","4"}));
        patentCitationIdMap.put("4", Arrays.asList(new String[]{"1","2","3"}));
    }

    static List<String> getPatentCitationIds(String patentId){
        return patentCitationIdMap.get(patentId);
    }

    static List<Object> getPatentByPatentIds(List<String> patentIds){
        List<Object> patentCitations  = patentIds.stream().map(id -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            Patent patent = new Patent();
            patent.setId(id);
            patent.setPn("AAA");
            return patent;
        }).collect(Collectors.toList());
        return patentCitations;
    }
}
```
then the total time cost could increase to nearly 5 seconds.
```java
{"data":{"patent":{"id":"1","pn":"AAA","citations":[{"id":"2","pn":"AAA","citations":[{"id":"1","pn":"AAA","citations":[{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"3","pn":"AAA","citations":[{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"4","pn":"AAA","citations":[{"id":"1","pn":"AAA"},{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"}]}]},{"id":"3","pn":"AAA","citations":[{"id":"2","pn":"AAA","citations":[{"id":"1","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"3","pn":"AAA","citations":[{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"4","pn":"AAA","citations":[{"id":"1","pn":"AAA"},{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"}]}]},{"id":"4","pn":"AAA","citations":[{"id":"1","pn":"AAA","citations":[{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"2","pn":"AAA","citations":[{"id":"1","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]},{"id":"3","pn":"AAA","citations":[{"id":"2","pn":"AAA"},{"id":"3","pn":"AAA"},{"id":"4","pn":"AAA"}]}]}]}}}
Total Cost: 49478 ms
```

## Using annotation to define the schema
Imagine that we need to define a large graphql schema which so many types need to be included. So we also need to add a lot of beans map to the schema, so may be we can put these two together. Let's use `graphql-java-annotation` to make this happen.

Maven dependency
```xml
<dependency>
    <groupId>com.graphql-java</groupId>
    <artifactId>graphql-java-annotations</artifactId>
    <version>3.0.3</version>
</dependency>
```
Value Object
```java
package com.chris.graphql.entity;

import com.chris.graphql.datafetcher.TeamNameDataFetcher;

import graphql.annotations.GraphQLDataFetcher;
import graphql.annotations.GraphQLField;
import graphql.annotations.GraphQLName;

/**
 * Created by ye830 on 12/22/2017.
 */
@GraphQLName("team")
public class Team {
    @GraphQLName("team_name")
    @GraphQLField
    @GraphQLDataFetcher(TeamNameDataFetcher.class)
    public
    String name;

    @GraphQLName("team_total_members")
    @GraphQLField
    public String total;
}

```

Test class
```java
package com.chris.graphql;

import com.chris.graphql.entity.Team;

import graphql.ExecutionInput;
import graphql.ExecutionResult;
import graphql.GraphQL;
import graphql.annotations.GraphQLAnnotations;
import graphql.schema.GraphQLObjectType;
import graphql.schema.GraphQLSchema;

/**
 * Created by ye830 on 12/22/2017.
 */
public class AnnotationTest {
    public static void main(String... args) {
        //  Programmatically
        GraphQLObjectType queryType = GraphQLAnnotations.object(Team.class);

        //Query by
        GraphQLSchema graphQLSchema = GraphQLSchema.newSchema()
                .query(queryType)
                .build();
        GraphQL build = GraphQL.newGraphQL(graphQLSchema).build();
        ExecutionInput executionInput = ExecutionInput.newExecutionInput()
                .operationName("team")
                .query("query team{team_name}")
                .context(new Team())
                .root(new Team())
                .build();
        ExecutionResult executionResult = build.execute(executionInput);

        //ExecutionResult executionResult = build.execute("query team{team_name}", new Team());

        System.out.println(executionResult.getData().toString());
    }
}

```
All these example are on Github, please check [https://github.com/ye8303019/project-graphql-java-demo/tree/master](https://github.com/ye8303019/project-graphql-java-demo/tree/master) to get the complete source code.

OK, that's all the regular test for GraphQL, next step, let's integrate GraphQL with Spring.
Imagine that we want to provide the GraphQL service vai HTTP/HTTPS. and also we may think if we could find some useful starter to help us make the code more elegant.
### Integration with spring boot
In this example, let's using the `spring-boot-starter` & `spring-boot-starter-web` to build a microservice.

pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.chris.graphql</groupId>
    <artifactId>project-graphql</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>project-graphql Maven Webapp</name>
    <url>http://maven.apache.org</url>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.4.4.RELEASE</version>
    </parent>
    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <spring.boot.version>1.4.4.RELEASE</spring.boot.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.graphql-java</groupId>
            <artifactId>graphql-java</artifactId>
            <version>6.0</version>
        </dependency>
        <dependency>
            <groupId>com.github.ben-manes.caffeine</groupId>
            <artifactId>caffeine</artifactId>
            <version>2.6.0</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.3</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.9.3</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.9.3</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <version>1.4.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>1.4.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>compile</scope>
        </dependency>
    </dependencies>
    <build>
        <finalName>project-graphql</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.5</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>${project.build.sourceEncoding}</encoding>
                    <optimize>true</optimize>
                    <showWarnings>true</showWarnings>
                </configuration>
            </plugin>
        </plugins>

        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>

    </build>
</project>

```
graphql file
```js
type Query {
    patent(patentId: String, offset: Int = 0, limit: Int = 10): Patent
    total(patentId: String): Int
    offset(offset: Int = 0, limit: Int = 10): Int


}

type Patent {
    id: String!
    pn: String!
    my_name: String
    apno: String
    citations: [Patent]
    person: Person
}

type Person {
    name: String
}
```
GraphQL Bean
```java
package com.chris.graphql.dataloader;

import com.chris.graphql.entity.Patent;

import org.dataloader.BatchLoader;
import org.dataloader.DataLoader;
import org.dataloader.DataLoaderRegistry;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.io.File;
import java.util.Arrays;
import java.util.Collections;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.stream.Collectors;

import graphql.GraphQL;
import graphql.execution.instrumentation.dataloader.DataLoaderDispatcherInstrumentation;
import graphql.schema.DataFetcher;
import graphql.schema.GraphQLSchema;
import graphql.schema.idl.RuntimeWiring;
import graphql.schema.idl.SchemaGenerator;
import graphql.schema.idl.SchemaParser;
import graphql.schema.idl.TypeDefinitionRegistry;

/**
 * Created by ye830 on 12/21/2017.
 */
@Configuration
public class CitationDataLoader {

    @Bean("CitationGraphQL")
    protected GraphQL getGraphQL() {
        BatchLoader<String, Object> patentBatchLoader = keys -> CompletableFuture.supplyAsync(() -> getPatentByPatentIds(keys));

        DataLoader<String, Object> patentDataLoader = new DataLoader(patentBatchLoader);

        DataFetcher patentDataFetcher = env -> patentDataLoader.load(env.getArgument("patentId"));

        DataFetcher patentCitationDataFetcher = env -> {
            if (env.getSource() != null) {
                Patent patent = env.getSource();
                return patentDataLoader.loadMany(getPatentCitationIds(patent.getId()));
            }
            return Collections.EMPTY_LIST;
        };

        Long startTime = new Date().getTime();

        // Define the schema
        File schemaFile = new File(DataLoader.class.getClassLoader().getResource("dataloader.graphqls").getPath());

        // Define the type definition registry
        TypeDefinitionRegistry typeDefinitionRegistry = new SchemaParser().parse(schemaFile);

        // Define the runtime wiring
        RuntimeWiring runtimeWiring = RuntimeWiring.newRuntimeWiring()
                .type("Query", builder -> builder
                        .dataFetcher("patent", patentDataFetcher)
                )
                .type("Patent", builder -> builder
                        .dataFetcher("citations", patentCitationDataFetcher))
                .build();

        // Define the graphQL schema
        GraphQLSchema graphQLSchema = new SchemaGenerator().makeExecutableSchema(typeDefinitionRegistry, runtimeWiring);

        // Define data loader registry
        DataLoaderRegistry registry = new DataLoaderRegistry();
        registry.register("patent", patentDataLoader);

        // Define the graphQL
        return GraphQL.newGraphQL(graphQLSchema)
                .instrumentation(new DataLoaderDispatcherInstrumentation(registry))
                .build();
    }

    static Map<String, List<String>> patentCitationIdMap = new HashMap();

    static {
        patentCitationIdMap.put("1", Arrays.asList(new String[]{"2", "3", "4"}));
        patentCitationIdMap.put("2", Arrays.asList(new String[]{"1", "3", "4"}));
        patentCitationIdMap.put("3", Arrays.asList(new String[]{"2", "3", "4"}));
        patentCitationIdMap.put("4", Arrays.asList(new String[]{"1", "2", "3"}));
    }

    private List<String> getPatentCitationIds(String patentId) {
        return patentCitationIdMap.get(patentId);
    }

    private List<Object> getPatentByPatentIds(List<String> patentIds) {
        List<Object> patentCitations = patentIds.stream().map(id -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            Patent patent = new Patent();
            patent.setId(id);
            patent.setPn("AAA");
            return patent;
        }).collect(Collectors.toList());
        return patentCitations;
    }

}
```
Controller
```java
package com.chris.graphql.controller;

import com.chris.graphql.entity.QueryBean;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.util.Date;
import java.util.HashMap;
import java.util.Map;

import graphql.ExecutionInput;
import graphql.ExecutionResult;
import graphql.GraphQL;

/**
 * Created by ye830 on 9/11/2017.
 */

@RestController
@RequestMapping(value = "/patent")
public class PatentController {

    @Autowired
    GraphQL citationGraphQL;

    @RequestMapping(
            value = "/citation",
            method = RequestMethod.POST,
            produces = "application/json"
    )
    public Map<String, Object> citation(@RequestBody QueryBean queryBean){
        Long startTime = new Date().getTime();
        // Define the execution
        String queryString = queryBean.getQuery();
        Map<String, Object> variableMap = new HashMap<>();
        for(Map.Entry<String, Object> entry : queryBean.getVariable().entrySet()){
            variableMap.put(entry.getKey(), entry.getValue());
        }
        ExecutionInput executionInput = ExecutionInput.newExecutionInput()
                .query(queryString)
                .operationName("Query")
                .variables(variableMap)
                .build();

        ExecutionResult executionResult = citationGraphQL.execute(executionInput);
        Map<String, Object> specificationResult = executionResult.toSpecification();

        Long endTime = new Date().getTime();

        System.out.println("Total Cost: "+(endTime - startTime)+" ms");
        return specificationResult;
    }
}

```
So we could get the result by send post request to the server

![GraphQL_RESTFUL]({{ site.url}}/images/graphql/GraphQL_RESTFUL.png)

### Integration with GraphQL spring boot starter & GraphiQL Spring starter
Spring hava provide us a very useful starter for GraphQL, we just need to add the `graphql-spring-boot-starter`, `graphiql-spring-boot-starter`, `graphql-java-tools` dependency and write the bean and add some configurations then every thing will set up by spring

pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.chris.graphql</groupId>
    <artifactId>project-graphql-spring</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.4.4.RELEASE</version>
    </parent>

    <properties>
        <java.version>1.8</java.version>
        <spring.boot.version>1.4.4.RELEASE</spring.boot.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.graphql-java</groupId>
            <artifactId>graphql-java</artifactId>
            <version>6.0</version>
        </dependency>
        <dependency>
            <groupId>com.graphql-java</groupId>
            <artifactId>graphql-java-annotations</artifactId>
            <version>3.0.3</version>
        </dependency>
        <dependency>
            <groupId>com.github.ben-manes.caffeine</groupId>
            <artifactId>caffeine</artifactId>
            <version>2.6.0</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.3</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.9.3</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.9.3</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <version>1.4.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>1.4.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>com.graphql-java</groupId>
            <artifactId>graphql-spring-boot-starter</artifactId>
            <version>3.9.2</version>
        </dependency>
        <dependency>
            <groupId>com.graphql-java</groupId>
            <artifactId>graphiql-spring-boot-starter</artifactId>
            <version>3.9.2</version>
        </dependency>
        <dependency>
            <groupId>com.graphql-java</groupId>
            <artifactId>graphql-java-tools</artifactId>
            <version>4.3.0</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.5</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>${project.build.sourceEncoding}</encoding>
                    <optimize>true</optimize>
                    <showWarnings>true</showWarnings>
                </configuration>
            </plugin>
        </plugins>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>
</project>
```
application.properties
```properties
graphql.servlet.corsEnabled=true
graphql.servlet.mapping=/graphql
graphql.servlet.enabled=true

graphiql.enabled=true
graphiql.endpoint=/graphql
graphiql.mapping=/graphiql
```

graphql files
```js
type Query {
    # Get patent information by a patent id
    patent(patent_id: String):Patent
}

type Patent {
    # Patent unique id
    id: String
    # Patent number
    pn: String
    # A list of patent IPC code
    ipc: [String]
    # The Assignee of the patent
    assignee: Assignee
    # A list of inventors of the patent
    inventor: [Inventor]
}

interface Person {
    # Person name
    name: String
    # Person age
    age: Int
    # Person address
    address: String
}

# The assignee of the patent
type Assignee implements Person {
    # The name of the assignee
    name: String
    # The age of the assignee
    age: Int
    # The address of the assignee
    address: String
    # The normalized name of the assignee
    nname: String
}

# The inventor of the patent
type Inventor implements Person {
    # The name of the inventor
    name: String
    # The age of the inventor
    age: Int
    # The address of the inventor
    address: String
    # The country of the inventor
    country: String
}


```

For the java codes please check my github repository
[project-graphql-spring-demo](https://github.com/ye8303019/project-graphql-spring-demo/tree/master)


_Reference:_  
_[1]:http://facebook.github.io/graphql/October2016/_  
_[2]:http://graphql.org/_    
_[3]:https://developer.github.com/v4/_    
_[4]:https://githubengineering.com/the-github-graphql-api/_
_[5]:http://graphql.org/graphql-js/mutations-and-input-types/_  
_[6]:http://www.baeldung.com/graphql_
_[7]:https://github.com/graphql-java/graphql-spring-boot
_[8]:https://github.com/graphql-java/graphql-java-tools
_[9]:https://github.com/graphql-java/graphql-java-servlet
_[10]:https://github.com/apottere/awesome-graphql-java
_[11]:https://github.com/apottere/awesome-graphql

