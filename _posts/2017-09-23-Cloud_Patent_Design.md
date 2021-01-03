---
layout: post
title:  "The Cloud Patent Design"
date:   2017-09-23 13:05:11 +0800
categories: cloud cloud_patent
---

Cloud Patent Design
===================

* [Versioning](#versioning)
* [Patent](#patent)
    * [GET /cloud_patent/patent_id](#get-cloud_patentcompany_id)
    * [GET /cloud_patent/patent/{patent_id}](#get-cloud_patentpatentpatent_id)
    * [GET /cloud_patent/patent_valuation/{patent_id}](#get-cloud_patentpatent_valuationpatent_id)
    * [GET /cloud_patent/classification/{classification_type}/{classification_code}](#get-cloud_patentclassificationclassification_typeclassification_code)
* [Trademark](#trademark)
    * [GET /cloud_patent/trademark_id](#get-cloud_patenttrademark_id)
    * [GET /cloud_patent/trademark/{trademark_id}](#get-cloud_patenttrademarktrademark_id)
* [Company](#company)
    * [GET /cloud_patent/company_id](#get-cloud_patentcompany_id)
    * [GET /cloud_patent/company/{company_id}](#get-cloud_patentcompanycompany_id) 
    * [GET /cloud_patent/company_staff/{company_id}](#get-cloud_patentcompany_staffcompany_id)
    * [GET /cloud_patent/company_change/{company_id}](#get-cloud_patentcompany_changecompany_id) 
    * [GET /cloud_patent/company_tree/{company_id}](#get-cloud_patentcompany_treecompany_id) 
    * [GET /cloud_patent/company_shareholder/{company_id}](#get-cloud_patentcompany_shareholdercompany_id) 
    * [GET /cloud_patent/company_investment/{company_id}](#get-cloud_patentcompany_investmentcompany_id) 
* [Agency](#agency)
    * [GET /cloud_patent/agency_id](#get-cloud_patentagency_id)
    * [GET /cloud_patent/agency/{agency_id}](#get-cloud_patentagencyagency_id)
    * [GET /cloud_patent/agency/agent/{agent_id}](#get-cloud_patentagencyagentagent_id)
    * [GET /cloud_patent/agency/{agency_id}/patent](#get-cloud_patentagencyagency_idpatent)
* [Error Codes](#error-codes)

Versioning
----------

The service use [Json Schema](http://json-schema.org/documentation.html) to versioning, the client could add the `Accept` header to request for specified version of response.

The `Accept` header like the format below:
```js
    Accept: application/json; profile="http://mydomain.com/myservice/myresource-schema.json"; version=1.0.0
```

The `profile` means the [Json Schema](http://json-schema.org/documentation.html) id.

The `version` means the version of the resource. If no version on the accept, it's means the latest version.

The `Accept` header Example:
```js
    Accept: application/json; profile="http://cloud-soi.patsnap.com/cloud-patent/patent_id-schema.json"; version=1.0.0
```

The response could use the [Json Schema](http://json-schema.org/documentation.html) for validating.

Patent
----------

### GET /cloud_patent/patent_id

_**Get related patent id by a comapny name or a organization number or a registration number**_

#### Request Parameters

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**q**|String|true||The query string  </br>Support fields: </br>&nbsp;&nbsp;&nbsp;&nbsp;**ANS**</br>&nbsp;&nbsp;&nbsp;&nbsp;**ORG_NUM**</br>&nbsp;&nbsp;&nbsp;&nbsp;**REG_NUM**|String length less equal 150|q=ANS:CompanyA</br>q=ORG_NUM:000000000</br>q=REG_NUM:000000000000000|
|**offset**|Integer|false|0|The offset of pagination|Number great equal 0|offset=1|
|**limit**|Integer|false|10|The limit of pagination|Number great equal 1 and less equal 100|limit=20|

#### Version

1.0.0

#### Response body

```json
{
  "patent_id":[
    "00000000-0000-0000-0000-000000000000",
    "11111111-1111-1111-1111-111111111111"
  ],
  "offset":0,
  "limit":10,
  "total":100
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/patent_id-schema.json", 
  "properties": {
    "limit": {
      "default": 10, 
      "description": "The limit of pagination.", 
      "title": "The limit schema", 
      "type": "integer",
      "minimum": 0,
      "maximum": 100
    }, 
    "offset": {
      "default": 0, 
      "description": "The offset of pagination.", 
      "title": "The offset schema", 
      "type": "integer",
      "minimum": 0
    }, 
    "patent_id": {
      "items": {
        "description": "A patent unique id.", 
        "title": "The patent id schema", 
        "type": "string"
      }, 
      "type": "array"
    }, 
    "total": {
      "description": "The total count of current requst.", 
      "title": "The total schema", 
      "type": "integer"
    }
  }, 
  "type": "object"
}
```

### GET /cloud_patent/patent/{patent_id}

_**Get patent basic information by a batch of patent id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**patent_id**|String|true||A batch of **`comma separated`** patent id|Patent id should be legal</br></br> The number of patent id should less equal 10|patent_id=00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111|

#### Version

1.0.0

#### Response body

```json
[
    {
        "patent_id": "00000000-0000-0000-0000-000000000000",
        "pn": "CN102699XXXX",
        "patent_type": "Applications",
        "ipc": "A01,A21",
        "ans": [
            {
                "lang": "CN",
                "text": "张三"
            }
        ],
        "an": [
            {
                "lang": "CN",
                "text": "张三"
            }
        ],
        "in": [
            {
                "lang": "CN",
                "text": "李四"
            }
        ],
        "pbdt": 20000101,
        "apdt": 20000101,
        "isdt": 20000101,
        "apno": "CN201210101000.0",
        "thumbnail": "http://thumbnail.domain/example.png",
        "abstract": "某种类型的专利摘要",
        "extenal_link": "PatSnap",
        "legal": [
            {
                "l007ep": 20000101,
                "legal_desc": [
                    {
                        "lang": "CN",
                        "text": "授权"
                    }
                ]
            }
        ]
    }
]
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {
    "lang_array":{
      "items": {
        "properties": {
          "lang": {
            "description": "The language of the text.", 
            "title": "The lang schema", 
            "type": "string"
          }, 
          "text": {
            "description": "The text of current language.", 
            "title": "The text schema", 
            "type": "string"
          }
        }, 
        "type": "object"
      }, 
      "type": "array"
    },
  }, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/patent-schema.json", 
  "items":{
    "properties": {
      "abstract": {
        "description": "The patent abstract.", 
        "title": "The abstract schema", 
        "type": "string"
      },
      "an": {
        "description": "The assignee name.", 
        "$ref":"#/definitions/lang_array",
      }, 
      "ans": {
        "description":"The standard assignee name",
        "$ref":"#/definitions/lang_array",
      }, 
      "apdt": { 
        "description": "An apply date of the patent.", 
        "title": "The apdt schema", 
        "type": "integer"
      }, 
      "apno": {
        "description": "An apply number of the patent.", 
        "title": "The apno schema", 
        "type": "string"
      }, 
      "extenal_link": { 
        "description": "The source of current patent.", 
        "title": "The extenal_link schema", 
        "type": "string"
      }, 
      "in": {
        "description": "The inventor of the patent",
        "$ref":"#/definitions/lang_array"
      }, 
      "ipc": {
        "description": "The ipc code of the patent.", 
        "title": "The ipc schema", 
        "type": "string"
      }, 
      "isdt": {
        "description": "The issue date of the patent.", 
        "title": "The isdt schema", 
        "type": "integer"
      }, 
      "legal": {
        "description": "The description of the legal status.",
        "items": {
          "properties": {
            "l007ep": {
              "description": "The legal status date.", 
              "id": "/properties/legal/items/properties/l007ep", 
              "title": "The l007ep schema", 
              "type": "integer"
            }, 
            "legal_desc": { 
              "description": "The legal status description.",
              "$ref":"#/definitions/lang_array"
            }
          }, 
          "type": "object"
        }, 
        "type": "array"
      }, 
      "patent_id": {
        "description": "The unique id of the patent.", 
        "title": "The patent_id schema", 
        "type": "string"
      }, 
      "patent_type": {
        "description": "The type of the patent, enum: Applications,Patents,Design, Utilites.", 
        "title": "The patent_type schema", 
        "type": "string"
      }, 
      "pbdt": {
        "description": "The public date of the patent.", 
        "title": "The pbdt schema", 
        "type": "integer"
      }, 
      "pn": {
        "description": "The public number of the patent.", 
        "title": "The pn schema", 
        "type": "string"
      }, 
      "thumbnail": {
        "description": "The thumbnail download address.", 
        "title": "The thumbnail schema", 
        "type": "string"
      }
    }, 
    "type": "object"
  }
}
```

### GET /cloud_patent/patent_valuation/{patent_id}
_**Get patent basic information by a batch of patent id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**patent_id**|String|true||A batch of **`comma separated`** patent id|Patent id should be legal</br></br> The number of patent id should less equal 10|patent_id=00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111|

#### Version

1.0.0

#### Response body

```json
[
  {
    "patent_id": "00000000-0000-0000-0000-000000000000",
    "legal": 0,
    "assignee": 0,
    "market_attractiveness": 0,
    "market_coverage": 0,
    "technology": 0
  }
]
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/patent_valuation-schema.json", 
  "items": {
    "properties": {
      "assignee": {
        "default": 0.0, 
        "description": "The .", 
        "title": "The assignee schema", 
        "type": "number"
      }, 
      "legal": {
        "default": 0.0, 
        "description": "The valuation of legal.", 
        "title": "The legal schema", 
        "type": "number"
      }, 
      "market_attractiveness": {
        "default": 0.0, 
        "description": "The valuation of market attractiveness.", 
        "title": "The market_attractiveness schema", 
        "type": "number"
      }, 
      "market_coverage": {
        "default": 0.0, 
        "description": "The valuation of market coverage.", 
        "title": "The market_coverage schema", 
        "type": "number"
      }, 
      "patent_id": {
        "default": "00000000-0000-0000-0000-000000000000", 
        "description": "An explanation about the purpose of this instance.", 
        "title": "The patent_id schema", 
        "type": "string"
      }, 
      "technology": {
        "default": 0.0, 
        "description": "The valuation of technology.", 
        "title": "The technology schema", 
        "type": "number"
      }
    }, 
    "type": "object"
  }, 
  "type": "array"
}
```


### GET /cloud_patent/classification/{classification_type}/{classification_code}
|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**classification_type**|String|true||Classification type, enum:</br> &nbsp;&nbsp;&nbsp;&nbsp;**ipc**|Classification type should be legal</br>|classification_type=ipc|
|**classification_code**|String|true||A batch of **`comma separated`** IPC code|IPC code should be legal</br></br> The number of IPC code should less equal 10|ipc_code=A01,A02|

#### Version

1.0.0

#### Response body

```json
[
    {
        "ipc": "A01",
        "version": "2016.01",
        "desc": [
            {
                "text": "AGRICULTURE; FORESTRY; ANIMAL HUSBANDRY; HUNTING; TRAPPING; FISHING",
                "lang": "EN"
            }
        ],
        "parent": [
            {
                "ipc": "A",
                "version": "2016.01",
                "desc": [
                    {
                        "text": "HUMAN NECESSITIES",
                        "lang": "EN"
                    }
                ]
            }
        ]
    }
]
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/classification-schema.json",
    "definitions": {
        "lang_array": {
            "items": {
                "properties": {
                    "lang": {
                        "description": "The language of the text.",
                        "title": "The lang schema",
                        "type": "string"
                    },
                    "text": {
                        "description": "The text of current language.",
                        "title": "The text schema",
                        "type": "string"
                    }
                },
                "type": "object"
            },
            "type": "array"
        }
  },
  "id": "http://cloud-soi.patsnap.com/cloud_patent/classification.json", 
  "items": {
    "properties": {
      "desc": {
        "$ref":"#/definitions/lang_array"
      }, 
      "ipc": {
        "description": "The ipc code.", 
        "title": "The ipc schema", 
        "type": "string"
      }, 
      "parent": {
        "items": {
          "properties": {
            "desc": {
              "$ref":"#/definitions/lang_array"
            }, 
            "ipc": {
              "description": "The ipc code.", 
              "title": "The ipc schema", 
              "type": "string"
            }, 
            "version": {
              "description": "The version of the ipc.", 
              "title": "The version schema", 
              "type": "string"
            }
          }, 
          "type": "object"
        }, 
        "type": "array"
      }, 
      "version": {
        "description": "The version of the ipc.", 
        "title": "The version schema", 
        "type": "string"
      }
    }, 
    "type": "object"
  }, 
  "type": "array"
}
```

Trademark
----------

### GET /cloud_patent/trademark_id

_**Get related trademark id by a company name or a organization number or a registration number**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**q**|String|true||The query string  </br>Support fields: </br>&nbsp;&nbsp;&nbsp;&nbsp;**ANS**</br>&nbsp;&nbsp;&nbsp;&nbsp;**ORG_NUM**</br>&nbsp;&nbsp;&nbsp;&nbsp;**REG_NUM**|String length less then 150|q=ANS:CompanyA</br>q=ORG_NUM:000000000</br>q=REG_NUM:000000000000000|
|**offset**|Integer|false|0|The offset of pagination|Number great equal 0|offset=1|
|**limit**|Integer|false|10|The limit of pagination|Number great equal 1 and less equal 100|limit=20|

#### Version

1.0.0

#### Response body

```json
{
  "trademark_id":[
    "00000000-0000-0000-0000-000000000000",
    "11111111-1111-1111-1111-111111111111"
  ],
  "offset":0,
  "limit":10,
  "total":100
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/trademark_id-schema.json", 
  "properties": {
    "limit": {
      "default": 10, 
      "description": "The limit of pagination.", 
      "title": "The limit schema", 
      "type": "integer",
      "minimum": 0,
      "maximum": 100
    }, 
    "offset": {
      "default": 0, 
      "description": "The offset of pagination.", 
      "title": "The offset schema", 
      "type": "integer",
      "minimum": 0
    }, 
    "trademark_id": {
      "items": {
        "description": "A trademark unique id.", 
        "title": "The trademark id schema", 
        "type": "string"
      }, 
      "type": "array"
    }, 
    "total": {
      "description": "The total count of current requst.", 
      "title": "The total schema", 
      "type": "integer"
    }
  }, 
  "type": "object"
}
```

### GET /cloud_patent/trademark/{trademark_id}
_**Get trademark basic information by a batch of trademark id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**trademark_id**|String|true||A batch of **`comma separated`** trademark id|Trademark id should be legal</br></br> The number of trademark id should less equal 10|trademark_id=00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111|

#### Version

1.0.0

#### Response body

```json
[
    {
        "trademark_id":"00000000-0000-0000-0000-000000000000",
        "org_number":"000000000",
        "credit_code":"000000000000000000",
        "company_name":"CompanyA",
        "apno":"0000000",
        "apdt":"00000000",
        "rgno":"00000000",
        "ncl":[
            {
                "code":"0403",
                "title":[
                    {
                        "lang":"EN",
                        "text":"solid fuel"
                    }
                ],
                "note":"XXXX"
            }
        ],
        "title":[
            {
                "lang":"EN",
                "text":"FRANTIC JEANS"
            }
        ],
        "representive":[
            {
                "lang":"EN",
                "text":"CompanyA"
            }
        ],
        "current_status":"Removed - Not Renewed",
        "legal_status":[
            {
                "category_code":"690",
                "category_content":"NOTICE OF ALLOWANCE - CANCELLED"
            }
        ],
        "thumbnail":"http://thumbnail.domain/example.png",
        "goods_service":[
            {
                "content":"皮衣",
                "lang":"CN",
                "ncl":"2501"
            }
        ]
    }
]
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {
    "lang_array": {
        "items": {
            "properties": {
                "lang": {
                    "description": "The language of the text.",
                    "title": "The lang schema",
                    "type": "string"
                },
                "text": {
                    "description": "The text of current language.",
                    "title": "The text schema",
                    "type": "string"
                }
            },
            "type": "object"
        },
        "type": "array"
    }
  }, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/trademark-schema.json", 
  "items": {
    "properties": {
      "apdt": {
        "description": "The apply date of the trademark.", 
        "title": "The apdt schema", 
        "type": "string"
      }, 
      "apno": {
        "description": "The apply number of the trademark.", 
        "title": "The apno schema", 
        "type": "string"
      }, 
      "company_name": {
        "description": "The company name.", 
        "title": "The company_name schema", 
        "type": "string"
      }, 
      "credit_code": {
        "description": "The credit code of the company.", 
        "title": "The credit_code schema", 
        "type": "string"
      }, 
      "current_status": {
        "description": "Current status of the trademark.", 
        "title": "The current_status schema", 
        "type": "string"
      }, 
      "goods_service": {
        "items": {
          "properties": {
            "content": {
              "description": "The content of the goods.", 
              "title": "The content schema", 
              "type": "string"
            }, 
            "lang": {
              "description": "The language of the content.", 
              "title": "The lang schema", 
              "type": "string"
            }, 
            "ncl": {
              "description": "The nice code.", 
              "title": "The ncl schema", 
              "type": "string"
            }
          }, 
          "type": "object"
        }, 
        "type": "array"
      }, 
      "legal_status": {
        "items": {
          "properties": {
            "category_code": {
              "description": "The legal status category code.", 
              "title": "The category_code schema", 
              "type": "string"
            }, 
            "category_content": {
              "description": "The legal status category content.", 
              "title": "The category_content schema", 
              "type": "string"
            }
          }, 
          "type": "object"
        }, 
        "type": "array"
      }, 
      "ncl": {
        "items": {
          "properties": {
            "code": {
              "description": "The nice code of the trademark.", 
              "title": "The code schema", 
              "type": "string"
            }, 
            "note": {
              "description": "The note of the trademark.", 
              "title": "The note schema", 
              "type": "string"
            }, 
            "title": {
              "$ref":"#/definitions/lang_array"
            }
          }, 
          "type": "object"
        }, 
        "type": "array"
      }, 
      "org_number": {
        "description": "The organization number of the company.", 
        "title": "The org_number schema", 
        "type": "string"
      }, 
      "representive": {
        "$ref":"#/definitions/lang_array"
      }, 
      "rgno": {
        "description": "The registration number of the trademark.", 
        "title": "The rgno schema", 
        "type": "string"
      }, 
      "thumbnail": {
        "description": "The thumbnail download url of the trademark.", 
        "title": "The thumbnail schema", 
        "type": "string"
      }, 
      "title": {
        "$ref":"#/definitions/lang_array"
      }, 
      "trademark_id": {
        "description": "The unique id of the trademark.", 
        "title": "The trademark_id schema", 
        "type": "string"
      }
    }, 
    "type": "object"
  }, 
  "type": "array"
}
```

Company
--------

### GET /cloud_patent/company_id

_**Get related company id by a company name or organization number**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**company_name**|String|true||The company name  </br>|String length less equal 100|company_name=CompanyA|
|**org_number**|String|true||The company name  </br>|String length less equal 20|org_number=000000000|
|**reg_number**|String|true||The company name  </br>|String length less equal 20|reg_number=000000000000000|

#### Version

1.0.0

#### Response body

```json
{
  "company_id":"000000000"
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/company_id-schema.json", 
  "properties": {
    "company_id": {
      "default": "000000000", 
      "description": "The id of the company.", 
      "title": "The company_id schema", 
      "type": "string"
    }
  }, 
  "type": "object"
}
```

### GET /cloud_patent/company/{company_id}

_**Get company basic information by a batch of company id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**company_id**|String|true||A batch of **`comma separated`** company id|Company id should be legal</br></br> The number of company id should less equal 10|company_id=000000000,111111111|

#### Version

1.0.0

#### Response body

```json
{
    "company_id": "000000000",
    "company_name": "CompanyA",
    "org_number": "000000000",
    "credit_code": "000000000000000000Y",
    "company_type": "个体工商户",
    "industry": {
        "code": "703",
        "desc": "房地产业-房地产业-房地产中介服务"
    },
    "reg_capital": 0.00,
    "reg_capital_type": "万元 人民币",
    "legal_person": "张三",
    "date_establish": 20000101,
    "reg_status": "在业",
    "reg_address": [
        {
            "lang": "CN",
            "text": "XX路"
        }
    ],
    "business_scope": "钢管租赁",
    "reg_institute": "XX局",
    "date_approved": 20000101,
    "date_from": 20000101,
    "date_to": 20000101
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {
    "lang_array": {
        "items": {
            "properties": {
                "lang": {
                    "description": "The language of the text.",
                    "title": "The lang schema",
                    "type": "string"
                },
                "text": {
                    "description": "The text of current language.",
                    "title": "The text schema",
                    "type": "string"
                }
            },
            "type": "object"
        },
        "type": "array"
    }
  }, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/company-schema.json", 
  "properties": {
    "business_scope": {
      "description": "The business scope of the company.", 
      "title": "The business_scope schema", 
      "type": "string"
    }, 
    "company_id": {
      "description": "The unique id of the company.", 
      "title": "The company_id schema", 
      "type": "string"
    }, 
    "company_name": {
      "description": "The name of the company.", 
      "title": "The company_name schema", 
      "type": "string"
    }, 
    "company_type": {
      "description": "The type of the company.", 
      "title": "The company_type schema", 
      "type": "string"
    }, 
    "credit_code": {
      "description": "The credit code of the company.", 
      "title": "The credit_code schema", 
      "type": "string"
    }, 
    "date_approved": {
      "description": "The license approved date.", 
      "title": "The date_approved schema", 
      "type": "integer"
    }, 
    "date_establish": {
      "description": "The establish date pf the company.", 
      "title": "The date_establish schema", 
      "type": "integer"
    }, 
    "date_from": {
      "description": "The business start date.", 
      "title": "The date_from schema", 
      "type": "integer"
    }, 
    "date_to": {
      "description": "The business end date.", 
      "title": "The date_to schema", 
      "type": "integer"
    }, 
    "industry": {
      "properties": {
        "code": {
          "description": "The company industry code.", 
          "title": "The code schema", 
          "type": "string"
        }, 
        "desc": {
          "description": "The company description.", 
          "title": "The desc schema", 
          "type": "string"
        }
      }, 
      "type": "object"
    }, 
    "legal_person": {
      "description": "The legal person of the company.", 
      "title": "The legal_person schema", 
      "type": "string"
    }, 
    "org_number": {
      "description": "The organization of the company.", 
      "title": "The org_number schema", 
      "type": "string"
    }, 
    "reg_address": {
      "$ref":"#/definitions/lang_array",
      "description": "The registered address of the company.", 
    }, 
    "reg_capital": {
      "default": 0.00, 
      "description": "The registered captial of the company.", 
      "title": "The reg_capital schema", 
      "type": "number"
    }, 
    "reg_capital_type": {
      "description": "The registered captial type.", 
      "title": "The reg_capital_type schema", 
      "type": "string"
    }, 
    "reg_institute": {
      "description": "The registered institute of the company.", 
      "title": "The reg_institute schema", 
      "type": "string"
    }, 
    "reg_status": {
      "description": "The registration status of the company.", 
      "title": "The reg_status schema", 
      "type": "string"
    }
  }, 
  "type": "object"
}
```


### GET /cloud_patent/company_staff/{company_id}

_**Get company staff information by a batch of company id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**company_id**|String|true||A batch of **`comma separated`** company id|Company id should be legal</br></br> The number of company id should less equal 10|company_id=000000000,111111111|

#### Version

1.0.0

#### Response body

```json
{
    "company_id": "000000000",
    "staff": [
        {
            "name":"张三",
            "type":"经理"
        }
    ]
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/company_staff-schema.json", 
  "properties": {
    "company_id": {
      "default": "000000000", 
      "description": "The unique id of the company.", 
      "title": "The company_id schema", 
      "type": "string"
    }, 
    "staff": {
      "items": {
        "properties": {
          "name": {
            "description": "The staff name of the company.", 
            "title": "The name schema", 
            "type": "string"
          }, 
          "type": {
            "description": "The staff type of the company.", 
            "title": "The type schema", 
            "type": "string"
          }
        }, 
        "type": "object"
      }, 
      "type": "array"
    }
  }, 
  "type": "object"
}
```

### GET /cloud_patent/company_change/{company_id}

_**Get company change information by a batch of company id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**company_id**|String|true||A batch of **`comma separated`** company id|Company id should be legal</br></br> The number of company id should less equal 10|company_id=000000000,111111111|

#### Version

1.0.0

#### Response body

```json
{
    "company_id": "000000000",
    "change": [
        {
            "change_itme": "地址变更",
            "change_before":"地址 A",
            "change_after":"地址 B",
            "change_time": 20000101
        }
    ]
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/company_change-schema.json", 
  "properties": {
    "change": {
      "items": {
        "properties": {
          "change_after": {
            "description": "The situation after change.", 
            "title": "The change_after schema", 
            "type": "string"
          }, 
          "change_before": {
            "description": "The situation before change.", 
            "title": "The change_before schema", 
            "type": "string"
          }, 
          "change_itme": {
            "description": "The change item.", 
            "title": "The change_itme schema", 
            "type": "string"
          }, 
          "change_time": {
            "description": "The change time.", 
            "title": "The change_time schema", 
            "type": "integer"
          }
        }, 
        "type": "object"
      }, 
      "type": "array"
    }, 
    "company_id": {
      "description": "The unique id of the company.", 
      "title": "The company_id schema", 
      "type": "string"
    }
  }, 
  "type": "object"
}
```

### GET /cloud_patent/company_tree/{company_id}

_**Get company branch information by a batch of company id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**company_id**|String|true||A batch of **`comma separated`** company id|Company id should be legal</br></br> The number of company id should less equal 10|company_id=000000000,111111111|

#### Version

1.0.0

#### Response body

```json
{
    "company_id": "000000000",
    "branch": [
        {
            "change_itme": "地址变更",
            "change_before":"地址 A",
            "change_after":"地址 B",
            "change_time": 20000101
        }
    ]
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/company_tree-schema.json", 
  "properties": {
    "change": {
      "items": {
        "properties": {
          "change_after": {
            "description": "The situation after change.", 
            "title": "The change_after schema", 
            "type": "string"
          }, 
          "change_before": {
            "description": "The situation before change.", 
            "title": "The change_before schema", 
            "type": "string"
          }, 
          "change_itme": {
            "description": "The change item.", 
            "title": "The change_itme schema", 
            "type": "string"
          }, 
          "change_time": {
            "description": "The change time.", 
            "title": "The change_time schema", 
            "type": "integer"
          }
        }, 
        "type": "object"
      }, 
      "type": "array"
    }, 
    "company_id": {
      "description": "The unique id of the company.", 
      "title": "The company_id schema", 
      "type": "string"
    }
  }, 
  "type": "object"
}
```

### GET /cloud_patent/company_shareholder/{company_id}

_**Get company shareholder information by a batch of company id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**company_id**|String|true||A batch of **`comma separated`** company id|Company id should be legal</br></br> The number of company id should less equal 10|company_id=000000000,111111111|

#### Version

1.0.0

#### Response body

```json
{
    "company_id": "000000000",
    "shareholder": [
        {
            "investor_name": "张三"
        }
    ]
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/company_shareholder-schema.json", 
  "properties": {
    "company_id": {
      "description": "The unique id of the company.", 
      "title": "The company_id schema", 
      "type": "string"
    }, 
    "shareholder": {
      "items": {
        "properties": {
          "investor_name": {
            "description": "The investor name of the company.", 
            "title": "The investor_name schema", 
            "type": "string"
          }
        }, 
        "type": "object"
      }, 
      "type": "array"
    }
  }, 
  "type": "object"
}
```

### GET /cloud_patent/company_investment/{company_id}

_**Get company investment information by a batch of company id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**company_id**|String|true||A batch of **`comma separated`** company id|Company id should be legal</br></br> The number of company id should less equal 10|company_id=000000000,111111111|

#### Version

1.0.0

#### Response body

```json
{
    "company_id": "000000000",
    "investment": [
        {
            "outcompany_name": "Company A"
        }
    ]
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/company_id-schema.json", 
  "properties": {
    "company_id": {
      "description": "The unique id of the company.", 
      "title": "The company_id schema", 
      "type": "string"
    }, 
    "investment": {
      "items": {
        "properties": {
          "outcompany_name": {
            "description": "The outcompany name.", 
            "title": "The outcompany_name schema", 
            "type": "string"
          }
        }, 
        "type": "object"
      }, 
      "type": "array"
    }
  }, 
  "type": "object"
}
```

Agency  
-------------

### GET /cloud_patent/agency_id

_**Get agency id by a agency number**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**agency_number**|String|true||The agency number  </br>|String length less equal 10|agency_number=00000|

#### Version

1.0.0

#### Response body

```json
{
  "agency_id":"00000000-0000-0000-0000-000000000000"
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/angency_id-schema.json", 
  "properties": {
    "agency_id": {
      "description": "The unique id of the agency.", 
      "title": "The agency_id schema", 
      "type": "string"
    }
  }, 
  "type": "object"
}
```

### GET /cloud_patent/agency/{agency_id}

_**Get agency information by a batch of agency number**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**agency_id**|String|true||A batch of **`comma separated`** agency id|Agency id should be legal</br></br> The number of agency id should less equal 10|agency_id=00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111|

#### Version

1.0.0

#### Response body

```json
{
  "agency_id":"00000000-0000-0000-0000-000000000000",
  "agency_number":"00000",
  "agency_name":"Agency A",
  "agency_status":"正常",
  "agency_director":[
    "张三","李四"
  ],
  "angency_telephone":"13900000000",
  "agency_address":"地址A"
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/angency-schema.json", 
  "properties": {
    "agency_address": {
      "description": "An explanation about the purpose of this instance.", 
      "title": "The agency_address schema", 
      "type": "string"
    }, 
    "agency_director": {
      "items": {
        "description": "The directors of the agency.", 
        "title": "The 0 schema", 
        "type": "string"
      }, 
      "type": "array"
    }, 
    "agency_id": {
      "description": "The unique id of the agency.", 
      "title": "The agency_id schema", 
      "type": "string"
    }, 
    "agency_name": {
      "description": "The name of the agency.", 
      "title": "The agency_name schema", 
      "type": "string"
    }, 
    "agency_number": {
      "description": "The number of the agency.", 
      "title": "The agency_number schema", 
      "type": "string"
    }, 
    "agency_status": {
      "description": "The current status of the agency.", 
      "title": "The agency_status schema", 
      "type": "string"
    }, 
    "angency_telephone": {
      "default": "", 
      "description": "The telephone number of the agency.", 
      "title": "The angency_telephone schema", 
      "type": "string"
    }
  }, 
  "type": "object"
}

```

### GET /cloud_patent/agency/agent/{agent_id}

_**Get agent information by a batch of agent id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**agent_id**|String|true||A batch of **`comma separated`** agent id|Agency id should be legal</br></br> The number of agent id should less equal 10|agent_id=00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111|

#### Version

1.0.0

#### Response body

```json
[
    {
      "agent_name":"张三",
      "qualification_no":"1115212",
      "license_no":"1139115212.7",
      "major":[
        "电力","电子"
      ]
    }
]
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/agent-schema.json", 
  "items": {
    "properties": {
      "agent_name": {
        "description": "The agent name.", 
        "title": "The agent_name schema", 
        "type": "string"
      }, 
      "license_no": {
        "description": "The agent license number.", 
        "title": "The license_no schema", 
        "type": "string"
      }, 
      "major": {
        "items": {
          "description": "The major of the agent.", 
          "title": "The 0 schema", 
          "type": "string"
        }, 
        "type": "array"
      }, 
      "qualification_no": {
        "description": "The qualification number of the agent.", 
        "title": "The qualification_no schema", 
        "type": "string"
      }
    }, 
    "type": "object"
  }, 
  "type": "array"
}
```

### GET /cloud_patent/agency/{agency_id}/patent

_**Get agent related patent number and patent id by a agency id**_

|Query Param|Type|Required|Default|Description|Restriction|Example|
|-----------|----|--------|-------|-----------|-----------|-------|
|**agency_id**|String|true||Agency id|Agency id should be legal</br>|agency_id=00000000-0000-0000-0000-000000000000|
|**offset**|Integer|false|0|The offset of pagination|Number great equal 0|offset=1|
|**limit**|Integer|false|10|The limit of pagination|Number great equal 1 and less equal 100|limit=20|

#### Version

1.0.0

#### Response body

```json
{
  "patent_id":[
    "00000000-0000-0000-0000-000000000000",
    "11111111-1111-1111-1111-111111111111"
  ],
  "offset":0,
  "limit":10,
  "total":100
}
```

#### Response json schema

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#", 
  "definitions": {}, 
  "id": "http://cloud-soi.patsnap.com/cloud_patent/agency/patent-schema.json", 
  "properties": {
    "limit": {
      "default": 10, 
      "description": "The limit of pagination.", 
      "title": "The limit schema", 
      "type": "integer",
      "minimum": 0,
      "maximum": 100
    }, 
    "offset": {
      "default": 0, 
      "description": "The offset of pagination.", 
      "title": "The offset schema", 
      "type": "integer",
      "minimum": 0
    }, 
    "patent_id": {
      "items": {
        "description": "A patent unique id.", 
        "title": "The patent id schema", 
        "type": "string"
      }, 
      "type": "array"
    }, 
    "total": {
      "description": "The total count of current requst.", 
      "title": "The total schema", 
      "type": "integer"
    }
  }, 
  "type": "object"
}
```


Error Codes
----------

|code number|HTTP status|decription|
|-----------|-----------|----------|
|100010|400|Bad request|
|100011|404|Empty result, data not found|
|100012|415|The json schema is unsupported|


