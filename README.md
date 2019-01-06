# Golang dynamic struct

*Still in progress*

Package dynamic struct provides possibility to dynamically, in runtime,
extend or merge existing defined structs or to provide completely new struct.

Main features:
* Building completely new struct in runtime
* Extending existing struct in runtime
* Merging multiple structs in runtime
* Adding new fields into struct
* Removing existing fields from struct
* Modifying fields' types and tags

Works out-of-the-box with:
* https://github.com/go-playground/form
* https://github.com/go-playground/validator
* https://github.com/leebenson/conform
* https://golang.org/pkg/encoding/json/

## Add new struct
```go
package main

import (
	"encoding/json"
	"fmt"
	"log"

	"github.com/ompluscator/dynamic-struct"
)

func main() {
	instance := dynamicstruct.NewStruct().
		AddField("Integer", 0, `json:"int"`).
		AddField("Text", "", `json:"someText"`).
		AddField("Float", 0.0, `json:"double"`).
		AddField("Boolean", false, "").
		AddField("Slice", []int{}, "").
		AddField("Anonymous", "", `json:"-"`).
		Build()

	data := []byte(`
{
    "int": 123,
    "someText": "example",
    "double": 123.45,
    "Boolean": true,
    "Slice": [1, 2, 3],
    "Anonymous": "avoid to read",
    "NilFloat": 567
}
`)

	err := json.Unmarshal(data, &instance)
	if err != nil {
		log.Fatal(err)
	}

	data, err = json.Marshal(instance)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println(string(data))
	// Out:
	// {"int":123,"someText":"example","double":123.45,"Boolean":true,"Slice":[1,2,3]}
}
```

## Extend existing struct
```go
package main

import (
	"encoding/json"
	"fmt"
	"log"

	"github.com/ompluscator/dynamic-struct"
)

type Data struct {
	Integer int `json:"int"`
}

func main() {
	instance := dynamicstruct.ExtendStruct(Data{}).
		AddField("Text", "", `json:"someText"`).
		AddField("Float", 0.0, `json:"double"`).
		AddField("Boolean", false, "").
		AddField("Slice", []int{}, "").
		AddField("Anonymous", "", `json:"-"`).
		Build()

	data := []byte(`
{
    "int": 123,
    "someText": "example",
    "double": 123.45,
    "Boolean": true,
    "Slice": [1, 2, 3],
    "Anonymous": "avoid to read",
    "NilFloat": 567
}
`)

	err := json.Unmarshal(data, &instance)
	if err != nil {
		log.Fatal(err)
	}

	data, err = json.Marshal(instance)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println(string(data))
	// Out:
	// {"int":123,"someText":"example","double":123.45,"Boolean":true,"Slice":[1,2,3]}
}
```

## Merge existing structs
```go
package main

import (
	"encoding/json"
	"fmt"
	"log"

	"github.com/ompluscator/dynamic-struct"
)

type DataOne struct {
	Integer int     `json:"int"`
	Text    string  `json:"someText"`
	Float   float64 `json:"double"`
}

type DataTwo struct {
	Boolean bool
	Slice []int
	Anonymous string `json:"-"`
}

func main() {
	instance := dynamicstruct.MergeStructs(DataOne{}, DataTwo{}).
		Build()

	data := []byte(`
{
"int": 123,
"someText": "example",
"double": 123.45,
"Boolean": true,
"Slice": [1, 2, 3],
"Anonymous": "avoid to read",
"NilFloat": 567
}
`)

	err := json.Unmarshal(data, &instance)
	if err != nil {
		log.Fatal(err)
	}

	data, err = json.Marshal(instance)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println(string(data))
	// Out:
	// {"int":123,"someText":"example","double":123.45,"Boolean":true,"Slice":[1,2,3]}
}
```