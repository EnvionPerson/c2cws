# c2cw
# Description of the Properties

# objectTargetAction
- **compare**: compare all fields and objectRules and fieldRules have top priority
- **live**: directly to Table (without placing in Comparison);
- **force**: places it in Comparison Directly (even without real comparison between DB's version)
- **retrospective**: takes value from Live (real ) Table value puts in Comparison
    when new value will be placed in Live Table. So at the result you will have;
    Live : value = new from Feed
    Comparison: value = old value from Live.
- **ignore**: does nothing.

##examples:
```json
    "defaultRules":{
        "skuRules":{
            "objectTargetAction":"compare"
            }
        }
```

# anchorActionOnChange
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
