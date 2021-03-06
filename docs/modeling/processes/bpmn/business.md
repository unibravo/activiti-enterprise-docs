---
Title: Business rule tasks
---

# Business rule tasks 
Business rule tasks are used to implement [decision tables](../../decisions.md) into a process definition. 

Business rule tasks are handled as [service tasks](../bpmn/service.md) by Activiti Enterprise and will always have the `implementation` value of `dmn-connector.EXECTUTE_TABLE`. The `name` of the decision table that is associated to the business rule task is stored in the [`<process-definition-name>-extensions.json` file](../../projects.md#files) as the `value` under `_activiti_dmn_table_`.

Business rule tasks are graphically represented by a single, thin rounded rectangle with a table icon inside.

The XML representation of a business rule task is: 

```xml
<bpmn2:serviceTask id="ServiceTask_4" implementation="dmn-connector.EXECUTE_TABLE" />
```

The extensions JSON of a process containing a business rule task is:

```json
"constants": {
	"ServiceTask_2f85xew": {
		"_activiti_dmn_table_": {
			"value": "my-decision-table-1"
            }
        }
    },
```