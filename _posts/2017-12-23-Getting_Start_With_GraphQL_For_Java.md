---
layout: post
title:  "Getting Start With GraphQL for Java"
date:   2017-12-22 11:42:11 +0800
categories: GraphQL java
---
[TOC]

# Preface
By the evolving of the API design, more and more developer are focus on the RESTFUL API style. But also more and more questions comes. How to do the versioning? How to design the response body to meet different requirement? especially when client wants different fields by one request. How to validating the parameter type and logic? How to fetching and caching the data?
So here comes the **GrapqhQL**, it's let us have another choice to design the API beside the RESTFUL.

# What is GraphQL?
![GraphQL]({{ site.url }}/images/graphql/logo.png) 

Here is the introduction from GraphQL official web site.

    GraphQL is a query language designed to build client applications by providing an intuitive 
    and flexible syntax and system for describing their data requirements and interactions.

    GraphQL is not a programming language capable of arbitrary computation, but is instead a 
    language used to query application servers that have capabilities defined in this 
    specification. 
    
    GraphQL does not mandate a particular programming language or storage system for application 
    servers that implement it. Instead, application servers take their capabilities and map them 
    to a uniform language, type system, and philosophy that GraphQL encodes. This provides a 
    unified interface friendly to product development and a powerful platform for tool‐building.

Compare to RESTFUL, GraphQL have 5 advantages 

* Easy to extend
* Query as documentation
* Fetch the data more centralized
* Strong type system 
* No more versioning

Let's see what Github says~

    You may be wondering why we chose to start supporting GraphQL. Our API was designed to be 
    RESTful and hypermedia-driven. We’re fortunate to have dozens of different open-source 
    clients written in a plethora of languages.  Businesses grew around these endpoints.

    Like most technology, REST is not perfect and has some drawbacks. Our ambition to change 
    our API focused on solving two problems.

    The first was scalability. The REST API is responsible for over 60% of the requests made 
    to our database tier. This is partly because, by its nature, hypermedia navigation requires 
    a client to repeatedly communicate with a server so that it can get all the information it 
    needs. Our responses were bloated and filled with all sorts of *_url hints in the JSON 
    responses to help people continue to navigate through the API to get what they needed. 
    Despite all the information we provided, we heard from integrators that our REST API also 
    wasn’t very flexible. It sometimes required two or three separate calls to assemble a 
    complete view of a resource. It seemed like our responses simultaneously sent too much 
    data and didn’t include data that consumers needed.

    As we began to audit our endpoints in preparation for an APIv4, we encountered our second 
    problem. We wanted to collect some meta-information about our endpoints. For example, we 
    wanted to identify the OAuth scopes required for each endpoint. We wanted to be smarter 
    about how our resources were paginated. We wanted assurances of type-safety for 
    user-supplied parameters. We wanted to generate documentation from our code. We wanted 
    to generate clients instead of manually supplying patches to our Octokit suite. We 
    studied a variety of API specifications built to make some of this easier, but we 
    found that none of the standards totally matched our requirements.

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
Let's get more deep on the GraphQL java, this time, we will make every thing together. For example:
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
        File schema = new File(NestObjTest.class.getClassLoader().getResource("nestobj.graphqls").getPath());

        // Define the TypeDefineRegistry
        TypeDefinitionRegistry typeDefinitionRegistry = new SchemaParser().parse(schema);


        DataFetcher patentIdDataFetcher = env -> {
            Object patentId = env.getArgument("patentId");
            if (patentId != null) {
                return patentId;
            }
            return patentBiblio.getId();
        };

        // Define the run time wiring
        RuntimeWiring runtimeWiring = RuntimeWiring.newRuntimeWiring()
                .type("Query", builder -> builder
                        .dataFetcher("patent_biblio", env -> patentBiblio)
                        .dataFetcher("patent_biblio_legal", env -> legal)
                )
                .type("PatentBiblio", builder -> builder
                        .dataFetcher("id", patentIdDataFetcher))
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

        Cache<String, PreparsedDocumentEntry> cache = Caffeine.newBuilder().maximumSize(10_000).build();

        // Define the GraphQL
        GraphQL graphQL = GraphQL.newGraphQL(graphQLSchema)
                .preparsedDocumentProvider(cache::get)
                .build();

        // Execute the query
        // String queryString = "{patent_biblio {id pn apno ans{name lang} familyType}}";

        // Execute the union query
//        String queryString = "{patent_biblio_legal {... on PatentBiblio {id} ... on Legal {legalStatus}}}";
//        ExecutionResult executionResult = graphQL.execute(queryString);
//        System.out.println(executionResult.getData().toString());

        // Query by ExecutionInput
        String queryString = "query Query($patentId: String!){patent_biblio {id(patentId: $patentId) pn apno ans{name lang} familyType}}";
        Map<String, Object> variableMap = new HashMap<>();
        variableMap.put("patentId", "1111111");
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

        System.out.println(endTime - startTime);
    }
}
```
and also define some beans to support this example, they are:
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
Then let's run the code, then we will get
```js
{"data":{"patent_biblio":{"id":"1111111","pn":"IL168994A","apno":"IL168994","ans":{"name":"ChrisYe","lang":"EN"},"familyType":"INPADOC"}}}
```

TO be continued~

_Reference:_  
_[1]:http://facebook.github.io/graphql/October2016/_  
_[2]:http://graphql.org/_    
_[3]:https://developer.github.com/v4/_    
_[4]:https://githubengineering.com/the-github-graphql-api/_
_[5]:http://graphql.org/graphql-js/mutations-and-input-types/_  
_[6]:http://www.baeldung.com/graphql_
