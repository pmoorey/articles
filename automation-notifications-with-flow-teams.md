# Automation notifications with Microsoft Flow and Teams

This article is a quick explaination of how you can add easily feedback to your automation processes.  This is extremely simple and effectively, and can be modified to suit whatever your requirements might be.

## Step 1 - Create a Microsoft Flow

Microsoft Flow is an excellent tool for creating simple integrations between different systems.  In this case we'll use it to communicate the outcome of an automation process to Microsoft Teams.  Start by go to https://flow.microsoft.com/ and create a new flow. Choose 'When a HTTP request is received' as your input, this will create a webhook (unique URL) which you can post data to.

### Define your data schema
The webhook expects to recieve a JSON document, you need to define the schema of the document in order for Flow to interpet the data and expose it as variables later in the process.  If you don't have a schema available, or don't want to write one, you can simply paste in a example JSON document and it will create the schema for you.  This works well for simple JSON documents.

At this point you need to think about the data you wish to communicate to the end user, here's an example:

<table style="width:100%;">
<thead>
  <tr>
    <th>Example JSON Document</th>
    <th>Example JSON Schema</th>
  </tr>
</thead>
  <tr>
    <td valign="top">
    <pre>

{
    "id": "2425",
    "requestor": "user@company.com",
    "result": "Succeeded",
    "url": "https://automation.mycompany.com/job/2425",
    "data": {
        "device_name": "router-xyz",
        "interface": "GigabitEthernet1/0/33",
        "vlan_name": "printers",
        "vlan_number": 36
    }
}
</pre>
</td>
    <td valign="top"><pre>
{
    "type": "object",
    "properties": {
        "id": {
            "type": "string"
        },
        "requestor": {
            "type": "string"
        },
        "result": {
            "type": "string"
        },
        "url": {
            "type": "string"
        },
        "data": {
            "type": "object",
            "properties": {
                "device_name": {
                    "type": "string"
                },
                "interface": {
                    "type": "string"
                },
                "vlan_name": {
                    "type": "string"
                },
                "vlan_number": {
                    "type": "integer"
                }
            }
        }
    }
}
</pre>
    </td>
  </tr>
</table>

At this point you should have the first step in your process complete:
!(Step 1)[https://github.com/pmoorey/articles/blob/master/img/flow-step1.jpg]




