---
layout: post
title:  "The AWS DAX"
date:   2017-10-17 13:05:11 +0800
categories: aws development dax
---

AWS DAX (DynamoDB Accelerator)
=====================

[TOC]

When we using DynamoDB, we will meet some throughput exceeded or bad performance issue. In order to optimize these cases, we usually cache the data by ourself by using some in memory cache service or gate way to do the http cache. But that''s means we must care about the performance of these services and also make sure they are high available and scalable. 

In year 2017, DynamoDB release the DAX service to let user easy to manage the cache of DynamoDB.  

## What is DAX

Dax is a in memory cache service for AWS DynamoDB and could handle multiply operations of DynamoDB. It's high available and scalable and could measured in single-digit microseconds. 

To make it easy to modify the existing application to use dax, the DaxClient is very similar to DynamoDB lower-leve client in java. So you just need to modify a few code to use it.

## The usage scenario

Dax is idea for:  

* Read heavy or read intensive
* Bursty workload  
* Time cost intensive  

Dax is not idea for:

* Write intensive  
* Need strongly consistent read
* Time cost not intensive, do not required for microsecond response
* Already have other cache solution for the application

## The design

![Dax Design]({{ site.url }}/image/aws_dax/dax_design.png)

When application send request to Dax, Dax attempt to read the data from cache, if hit,  Dax return it immediately, otherwise will sent the request to DynamoDB and return it to the client side and store it in the cache.

## Java code

As we know, DAX support some read operations of DynamoDB, include:
* Query
* Scan
* Get Item
* Batch Get Item

When DAX storing the data in cache, it follow the rule as below:

***Item Cache***
DAX use item key value as the cache key and item as the cache value

***Query Cache***
DAX use query parameter as the cache key and item set as the cache value 

So let's do some test to check the algorithm

First let's prepare some data

* Create a table named `dax_test`
* Hash key is id, another two fields is name and age
* Create two index, named `index_user_name` & `index_user_age`
* 1000 objects with name field value of `ChrisYe`
* 500 objects with name field value of `ChrisYe` and age field of `28`

```java
package dynamodb;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClient;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBHashKey;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBIndexHashKey;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapper;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapperFieldModel;
import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBTable;
import com.amazonaws.services.dynamodbv2.document.DynamoDB;
import com.amazonaws.services.dynamodbv2.model.AttributeDefinition;
import com.amazonaws.services.dynamodbv2.model.CreateTableRequest;
import com.amazonaws.services.dynamodbv2.model.GlobalSecondaryIndex;
import com.amazonaws.services.dynamodbv2.model.KeySchemaElement;
import com.amazonaws.services.dynamodbv2.model.Projection;
import com.amazonaws.services.dynamodbv2.model.ProjectionType;
import com.amazonaws.services.dynamodbv2.model.ProvisionedThroughput;

import java.util.ArrayList;
import java.util.List;

import credential.AwsCredential;

/**
 * dynamodb.DaxDataPrepare
 * <p>
 * Author: ChrisYe
 * Date: 10/17/2017
 */

public class DaxDataPrepare {

    private static final String DAX_TEST_TABLE = "dax_test";

    private static final String DAX_TEST_TABLE_HASH_KEY = "id";

    private static final String DAX_TEST_USER_NAME = "user_name";

    private static final String DAX_TEST_USER_AGE = "user_age";

    private static final String INDEX_USER_NAME = "index_user_name";

    private static final String INDEX_USER_AGE = "index_user_age";


    public void createTable() {
        AmazonDynamoDBClient amazonDynamoDBClient = AwsCredential.getDynamoDBClient();

        DynamoDB dynamoDB = AwsCredential.getDynamoDB();
        if(dynamoDB.getTable(DAX_TEST_TABLE) == null){
            // Attributes
            AttributeDefinition hashKey = new AttributeDefinition(DAX_TEST_TABLE_HASH_KEY, DynamoDBMapperFieldModel.DynamoDBAttributeType.S.name());
            AttributeDefinition userNameIndex = new AttributeDefinition(DAX_TEST_USER_NAME, DynamoDBMapperFieldModel.DynamoDBAttributeType.S.name());
            AttributeDefinition userAgeIndex = new AttributeDefinition(DAX_TEST_USER_AGE, DynamoDBMapperFieldModel.DynamoDBAttributeType.N.name());
            List<AttributeDefinition> attributeDefinitionList = new ArrayList<>();
            attributeDefinitionList.add(hashKey);
            attributeDefinitionList.add(userNameIndex);
            attributeDefinitionList.add(userAgeIndex);

            // Indexes
            GlobalSecondaryIndex userNameGSI = new GlobalSecondaryIndex();
            userNameGSI
                    .withIndexName(INDEX_USER_NAME)
                    .withKeySchema(new KeySchemaElement(DAX_TEST_USER_NAME, "HASH"))
                    .withProjection(new Projection().withProjectionType(ProjectionType.ALL))
                    .withProvisionedThroughput(new ProvisionedThroughput(20L, 10L));
            GlobalSecondaryIndex userAgeGSI = new GlobalSecondaryIndex();
            userAgeGSI
                    .withIndexName(INDEX_USER_AGE)
                    .withKeySchema(new KeySchemaElement(DAX_TEST_USER_AGE, "HASH"))
                    .withProjection(new Projection().withProjectionType(ProjectionType.ALL))
                    .withProvisionedThroughput(new ProvisionedThroughput(20L, 10L));

            //KeySchema
            KeySchemaElement hashKeySchemaElement = new KeySchemaElement(DAX_TEST_TABLE_HASH_KEY, "HASH");

            // Table
            CreateTableRequest createTableRequest = new CreateTableRequest();
            createTableRequest
                    .withTableName(DAX_TEST_TABLE)
                    .withAttributeDefinitions(attributeDefinitionList)
                    .withGlobalSecondaryIndexes()
                    .withProvisionedThroughput(new ProvisionedThroughput(20L, 10L))
                    .withGlobalSecondaryIndexes(userNameGSI, userAgeGSI)
                    .withKeySchema(hashKeySchemaElement);

            amazonDynamoDBClient.createTable(createTableRequest);
        }
    }

    public void insertData() {
        //  Data prepare
        //* 1000 objs with same name
        //* 500  objs with same name & same age
        DynamoDBMapper dynamoDBMapper = AwsCredential.getDynamoDBMapper();
        for (int i = 0; i < 1000; i++) {
            if(i >= 500){
                dynamoDBMapper.save(new DaxDataPrepare.DaxTest(String.valueOf(i), "ChrisYe", 28));
            }else{
                dynamoDBMapper.save(new DaxDataPrepare.DaxTest(String.valueOf(i), "ChrisYe"));
            }
        }
    }

    public static void main(String[] args) {
        new DaxDataPrepare().createTable();
        new DaxDataPrepare().insertData();
    }

    @DynamoDBTable(tableName = DAX_TEST_TABLE)
    public class DaxTest {

        public DaxTest(String id, String userName) {
            this.id = id;
            this.userName = userName;
        }

        public DaxTest(String id, String userName, Integer userAge) {
            this.id = id;
            this.userName = userName;
            this.userAge = userAge;
        }

        @DynamoDBHashKey(attributeName = DAX_TEST_TABLE_HASH_KEY)
        private String id;

        @DynamoDBIndexHashKey(attributeName = DAX_TEST_USER_NAME, globalSecondaryIndexName = INDEX_USER_NAME)
        private String userName;

        @DynamoDBIndexHashKey(attributeName = DAX_TEST_USER_AGE, globalSecondaryIndexName = INDEX_USER_AGE)
        private Integer userAge;

        public String getId() {
            return id;
        }

        public void setId(String id) {
            this.id = id;
        }

        public String getUserName() {
            return userName;
        }

        public void setUserName(String userName) {
            this.userName = userName;
        }

        public Integer getUserAge() {
            return userAge;
        }

        public void setUserAge(Integer userAge) {
            this.userAge = userAge;
        }
    }
}
```
## Test

In order to check the item cache and query cache business logic, i prepare the test case below:

* Hash Key Query
    * Query for a hash key `id`, check the hits
    * Query for a hash key `id` again, check the hits
* Index Query
    * Query for `index_user_name`, check the hits
    * Query for `index_user_name` again, check the hits
    * Query for `index_user_age`, check the hits
    * Query for `index_user_age` again, check the hits
* Scan
    * Scan for `100` per time, repeat for `10` times, check for hits
    * Scan for `100` per time, repeat for `10` times again, check for hits
    * Scan for `25` per time, repeat for 4 times
* Get Item
    * Get `100` items, check the hits
    * Get `10` items, check the hits
* Batch Get Item
    * Batch get `100` same items as Get item, check the hits
    * Batch get `100` same items as Get item again, check the hits
    * Batch get `25` item which included by the result above, check the hits
    * Batch get `10000`, check the time
    * Batch get `10000` again, check the time

So i prepare a small application

```java
    package com.patsnap.chris;

    import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapper;
    import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapperConfig;
    import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBQueryExpression;
    import com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBScanExpression;
    import com.amazonaws.services.dynamodbv2.datamodeling.PaginatedQueryList;
    import com.amazonaws.services.dynamodbv2.datamodeling.ScanResultPage;
    import com.amazonaws.services.dynamodbv2.model.AttributeValue;
    import com.amazonaws.services.dynamodbv2.model.Select;
    import com.amazonaws.services.lambda.runtime.Context;
    import com.amazonaws.services.lambda.runtime.LambdaLogger;
    import com.fasterxml.jackson.databind.ObjectMapper;

    import org.apache.log4j.Logger;

    import java.io.IOException;
    import java.io.InputStream;
    import java.io.InputStreamReader;
    import java.io.OutputStream;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;
    import java.util.Map;
    import java.util.Optional;
    import java.util.UUID;

    public static void main(String[] args) throws Exception {

        Long start = new Date().getTime();

        String debug = Optional.ofNullable(args[0]).orElse("true");
        String opType = Optional.ofNullable(args[1]).orElse("hashKeyQuery");

        DynamoDBMapper mapper;
        if (Boolean.valueOf(debug)) {
            mapper = DynamoDBClient.getDynamoDBMapper();
        } else {
            mapper = DynamoDBDaxClient.getDynamoDBMapper();
        }

        // Hash Key Query
        if ("hashKeyQuery".equalsIgnoreCase(opType)) {
            String hashKeyValue = Optional.ofNullable(args[2]).orElse("");

            DaxTestTableObj daxTestTableObj = new DaxTestTableObj();
            daxTestTableObj.setId(hashKeyValue);
            DynamoDBQueryExpression<DaxTestTableObj> queryExpression = new DynamoDBQueryExpression<DaxTestTableObj>()
                    .withHashKeyValues(daxTestTableObj);
            PaginatedQueryList<DaxTestTableObj> paginationList = mapper.query(DaxTestTableObj.class, queryExpression, DynamoDBMapperConfig.DEFAULT);
            System.out.println("hashKeyQuery: " + paginationList.size());
        }

        // Index Query
        if ("indexQuery".equalsIgnoreCase(opType)) {
            String hashKeyValue = Optional.ofNullable(args[2]).orElse("");
            String indexName = Optional.ofNullable(args[3]).orElse("");

            DaxTestTableObj daxTestTableObj = new DaxTestTableObj(hashKeyValue);
            if ("index_user_age".equalsIgnoreCase(indexName)) {
                daxTestTableObj = new DaxTestTableObj(Integer.valueOf(hashKeyValue));
            }
            DynamoDBQueryExpression<DaxTestTableObj> queryExpression = new DynamoDBQueryExpression<DaxTestTableObj>()
                    .withIndexName(indexName)
                    .withHashKeyValues(daxTestTableObj)
                    .withConsistentRead(false);
            PaginatedQueryList<DaxTestTableObj> paginationList = mapper.query(DaxTestTableObj.class, queryExpression, DynamoDBMapperConfig.DEFAULT);
            System.out.println("indexQuery: " + paginationList.size());
        }

        // Scan
        if ("scan".equalsIgnoreCase(opType)) {
            int total = 10000;
            int limit = 100;
            if (args != null && args.length > 0) {
                total = Integer.valueOf(Optional.ofNullable(args[2]).orElse("10000"));
                limit = Integer.valueOf(Optional.ofNullable(args[3]).orElse("100"));
            }

            Map<String, AttributeValue> exclusiveStartKey = null;
            DynamoDBScanExpression dynamoDBScanExpression = new DynamoDBScanExpression();
            dynamoDBScanExpression.setLimit(limit);
            dynamoDBScanExpression.setSelect(Select.ALL_ATTRIBUTES);
            dynamoDBScanExpression.withExclusiveStartKey(exclusiveStartKey);
            int count = 0;
            do {
                ScanResultPage<DaxTestTableObj> result = mapper.scanPage(DaxTestTableObj.class, dynamoDBScanExpression, DynamoDBMapperConfig.DEFAULT);
                exclusiveStartKey = result.getLastEvaluatedKey();
                dynamoDBScanExpression.withExclusiveStartKey(exclusiveStartKey);
                count += limit;
            } while (exclusiveStartKey != null && count < total);

            System.out.println("Scan count: " + count);
        }

        // Get Item
        if ("getItem".equalsIgnoreCase(opType)) {

            int limit = Integer.valueOf(Optional.ofNullable(args[2]).orElse("100"));

            for (int i = 0; i < limit; i++) {
                mapper.load(DaxTestTableObj.class, String.valueOf(i));
            }
            System.out.println("GetItem count: " + limit);
        }


        // Batch get item
        if ("batchGetItem".equalsIgnoreCase(opType)) {

            int limit = Integer.valueOf(Optional.ofNullable(args[2]).orElse("100"));

            List<DaxTestTableObj> itemsToGet = new ArrayList<>();
            for (int i = 0; i < limit; i++) {
                DaxTestTableObj obj = new DaxTestTableObj();
                obj.setId(String.valueOf(i));
                itemsToGet.add(obj);
            }
            Map<String, List<Object>> result = mapper.batchLoad(itemsToGet);
            System.out.println("BatchGetItem count: " + result.get("dax_test").size());
        }

        Long end = new Date().getTime();

        System.out.println("Cost: " + (end - start) + " ms");

    }
```

Make it into a executive jar named  `executable_jar-jar-with-dependencies.jar` Let's begin!

#### Hash Key Query

* Query for a hash key `id`, check the hits

![hash_key_query1]({{ site.url }}/aws_dax/hash_key_query1.png)
![hash_key_result1]({{ site.url }}/aws_dax/hash_key_result1.png)

* Query for a hash key `id` again, check the hits

![hash_key_result2]({{ site.url }}/aws_dax/hash_key_result2.png)

#### Index Query

* Query for `index_user_name`, check the hits

![index_query1]({{ site.url }}/aws_dax/index_query_result1.png)
![index_query_result1]({{ site.url }}/aws_dax/hash_key_result1.png)

* Query for `index_user_name` again, check the hits

![index_query_result2]({{ site.url }}/aws_dax/hash_key_result2.png)

* Query for `index_user_age`, check the hits

![index_query2]({{ site.url }}/aws_dax/index_query_result2.png)
![index_query_result2]({{ site.url }}/aws_dax/hash_key_result2.png)

* Query for `index_user_age` again, check the hits

![index_query_result3]({{ site.url }}/aws_dax/hash_key_result3.png)

#### Scan

* Scan for `100` per time, repeat for `10` times, check for hits

![scan1]({{ site.url }}/aws_dax/scant1.png)
![scan_result1]({{ site.url }}/aws_dax/scan_result1.png)

* Scan for `100` per time, repeat for `10` times again, check for hits

![scan_result2]({{ site.url }}/aws_dax/scan_result2.png)

* Scan for `25` per time, repeat for `4` times

![scan2]({{ site.url }}/aws_dax/scant2.png)
![scan_result3]({{ site.url }}/aws_dax/scan_result3.png)

#### Get Item

* Get `100` items, check the hits

![get_item1]({{ site.url }}/aws_dax/get_item1.png)
![get_item_result1]({{ site.url }}/aws_dax/get_item_result1.png)

* Get `10` items, check the hits

![get_item_result2]({{ site.url }}/aws_dax/get_item_result2.png)

#### Batch Get Item

* Batch get `100` same items as Get item, check the hits

![batch_get_item1]({{ site.url }}/aws_dax/batch_get_item1.png)
![batch_get_item_result1]({{ site.url }}/aws_dax/batch_get_item_result1.png)

* Batch get `100` same items as Get item again, check the hits

![batch_get_item2]({{ site.url }}/aws_dax/batch_get_item2.png)
![batch_get_item_result2]({{ site.url }}/aws_dax/batch_get_item_result2.png)

* Batch get `25` item which included by the result above, check the hits

![batch_get_item_result3]({{ site.url }}/aws_dax/batch_get_item_result3.png)

* Batch get `10000`, check the time

![batch_get_item3]({{ site.url }}/aws_dax/batch_get_item3.png)

* Batch get `10000` again, check the time

![batch_get_item4]({{ site.url }}/aws_dax/batch_get_item4.png)


***From the test result, we can get:***

* ***Hash key query do not cache***
* ***Different query parameter do not share cache***
* ***Get and batch get use item key as cache key***
* ***4 times time cost saving***

