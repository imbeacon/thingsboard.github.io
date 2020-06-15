A custom converter is converter written for some device:



|**Parameter**|**Default value**|**Description**|
|:-|:-|-
| type                        | **custom**                      | Provides information to connector that custom converter will be uses for converting data from request.                                |
| extension                   | **CustomRESTUplinkConverter**   | Name of custom converter class.                                                                                                       |
| key                         | **Totaliser**                   | In this example value "Totaliser" will be interpreted as a telemetry key in ThingsBoard instance.                                     |
| datatype                    | **float**                       | In this example type of the data.                                                                                                     |
| fromByte                    | **0**                           | In this example start byte position in the response string.                                                                           |
| toByte                      | **4**                           | In this example start byte position in the response string.                                                                           |
| byteorder                   | **big**                         | In this example byteorder for response string.                                                                                        |
| signed                      | **true**                        | In this example using to indicate is the data in response signed.                                                                     |
| multiplier                  | **1**                           | In this example every data from in response should be multiplied to some coefficient.                                                 | 
|--- 

{% capture difference %}
<br>
  
**Custom converter usually needed if you want to collect data from some device with not regular structure in response or when the data needs some processing before sending it to the ThingsBoard.**  
{% endcapture %}
{% include templates/info-banner.md content=difference %}


Mapping subsection in the configuration will look like:
```json
    {
      "url": "/send_info",
      "httpMethod": "GET",
      "converter": {
        "type": "custom",
        "deviceNameJsonExpression": "Device ${name}",
        "deviceTypeJsonExpression": "custom",
        "extension": "CustomRequestUplinkConverter",
        "extension-config": [
          {
          "key": "Totaliser",
          "datatype": "float",
          "fromByte": 0,
          "toByte": 4,
          "byteorder": "big",
          "signed": true,
          "multiplier": 1
          }]
        }
      }
```
