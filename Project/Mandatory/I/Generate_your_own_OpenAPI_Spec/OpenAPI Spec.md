# Problems we ran into


We want to document that for our OpenAPI Spec we have encountered a slight deviation, that you can't seem to remove because of the base of swagger that swaggo utililizes.

We chose to use swaggo to generate our spec.

For our /api/weather we apply the following code:

```go
type WeatherResponse struct {  
       Data map[string]any `json:"data"`  
}
```

It as now seems that the default behavior when swaggo runs on map, is that it adds these 3 lines seen below.

![additionalProp.png](https://github.com/WeKnowXD/WeKnowXD_Doc/blob/main/Project/Mandatory/I/Generate_your_own_OpenAPI_Spec/additionalProp.png)

now it seems you should be able to remove these but either that implementation requires handling with swaggo that we are too unfamiliar and lacking too much time to potentially fix after having looked for possibilities for most of the day.

# Our solution

So as a group we decided to manually remove the belown seen addtionalProperoties both in the json and yaml on our generated files. With the note we are aware of this issue that we want to potentially fix in the future.

Besides that we have been succesfull with a 1:1 generation of the legacy servers spec

```json
"main.WeatherResponse": {

	"description": "WeatherData",

	"type": "object",

	"properties": {

		"data": {

			"type": "object",

			"additionalProperties": {} //<--- problematic line in .json we removed

		}

	}

}
``` 

```yaml
main.WeatherResponse:
	description: WeatherData
	properties:
		data:
			additionalProperties: {} #<-- problematic line in .yaml we removed
			type: object
	type: object
```

