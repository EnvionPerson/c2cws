# CEG to Core WebServices (C2CWS)
[apiary  Doc](http://docs.cegtocoreapi.apiary.io/#)
# Description of the Properties

## objectTargetAction
- **compare**: compare all fields and objectRules and fieldRules have top priority
- **live**: directly to Table (without placing in Comparison);
- **force**: places it in Comparison Directly (even without real comparison between DB's version)
- **retrospective**: takes value from Live (real ) Table value puts in Comparison
    when new value will be placed in Live Table. So at the result you will have;
    Live : value = new from Feed
    Comparison: value = old value from Live.
- **ignore**: does nothing. (applying link rules and multiSelect rules )

##examples:
```json
    "defaultRules":{
        "skuRules":{
            "objectTargetAction":"compare"
            }
        }
```

## anchorActionOnChange
- **flag**
- **no_flag**

# Apply default rules
## compare with object rules
```json
"defaultRules":{
    "skuRules":{
        "objectRules": [{
            "name": "AAXX",
            "objectTargetAction": "live"
        }],
        "objectTargetAction":"compare"
        }
}
```
in this example: all products will compare with live products, but
product with name "AAXX" directly to live table(SKUS)

use cases:
- if *skuRules* have *objectTargetAction* one of  (**live**, **force**, **retrospective**, **ignore**) then *objectRules* and *fieldRules* will *ignore*  
```javascript
"defaultRules":{  
    "skuRules":{
        <!-- IGNORE BLOCK-->
        "fieldRules": [{
            "coreFieldName": "test",
            "objectTargetAction": "compare"
        }],
        "objectRules": [{
            "name": "AAXX",
            "objectTargetAction": "compare"
        }],
        <!-- IGNORE -->
        "objectTargetAction":"live"
    }
}
```
- if *skuRules* have *objectTargetAction* **compare** then *fieldRules* have **top priority**, next priority *objectRules* and bottom *skuRules*  
```javascript
"defaultRules":{  
    "skuRules":{
        <!-- TOP PRIORITY BLOCK-->
        "fieldRules": [{
            "coreFieldName": "test",
            "objectTargetAction": "live"
        }],
        <!-- LOWER -->
        "objectRules": [{
            "name": "AAXX",
            "objectTargetAction": "force"
        }],
        <!-- LOWEST -->
        "objectTargetAction":"compare"
    }
}
```
## Multi-Select

```javascript
"defaultRules":{  
    "skuRules":{
        "multiSelect":{
            <!-- uses as default if column name not specify -->
            "default": {oneOf:["append", "remove", "replace"]},
            <!-- concrete column specification top priority on default-->
            "columnName": "remove"
        }
    }
}
```

### Actions
- **remove**: Remove set B from set A (database field values) i.e: remove setB{1,2} from setA{1,2,4,5} => setA{4,5}
- **append**: Append set B to set A (database field values) and ignore duplicates. i.e: append setB{1,2} from setA{2,3,4} => setA{1,2,3,4}
- **replace**: Replace set B on set A (database field values) with set i.e: replace setA{1,2} on setB{3,4}  => setA{3,4}

# Apply Product Link Rules (skuLinkRules)

```json
 "product": {
     "skuLinkRules": {
         "anchorLinkRules": [1,2,3],
         "parentChildLinkRules": [1,2,4]
     }
 }
```
## Definition
- **anchorLinkRules**: get rule from table 'anchor_rule' and apply to current product( or item)
- **parentChildLinkRules**: get rule from table 'sku_links_rule' and apply(*link*) to another product
