---
title: Adding Examples
sidebar:
  order: 10
---

:::note
OAS **2** This page applies to OpenAPI Specification ver. 2 (fka Swagger).  
To learn about the latest version, visit [OpenAPI 3 pages](/docs/specification/adding-examples).
:::
An API specification can include examples for:

- response MIME types,
- schemas (data models),
- individual properties in schemas.

Examples can be used by tools and libraries, for instance, Swagger UI auto-populates request bodies based on input schema examples, and some API mocking tools use examples to generate mock responses.

**Note:** Do not confuse example values with the `default` values. An example is used to illustrate what the value is supposed to be like. A default value is something that the server uses if the value is not provided in the request.

### Schema Examples

The `example` key is used to provide a schema example. Examples can be given for individual properties, objects and the whole schema.

#### Property Examples

Property examples can be specified inline. The example value must conform to the property type.

```yml
definitions:
  CatalogItem:
    type: object
    properties:
      id:
        type: integer
        example: 38
      title:
        type: string
        example: T-shirt
    required:
      - id
      - title
```

Note that multiple example values per property or schema are not supported, that is, you cannot have:

```yml
title:
  type: string
  example: T-shirt
  example: Phone
```

#### Object Examples

Properties of a type object can have complex inline examples that include that object’s properties. The example should comply with the object schema.

```yml
definitions:
  CatalogItem:
    type: object
    properties:
      id:
        type: integer
        example: 38
      title:
        type: string
        example: T-shirt
      image:
        type: object
        properties:
          url:
            type: string
          width:
            type: integer
          height:
            type: integer
        required:
          - url
        example:   # <-----
          url: images/38.png
          width: 100
          height: 100
    required:
      - id
      - title
```

#### Array Examples

An example for an array of primitives:

```yml
definitions:
  ArrayOfStrings:
    type: array
    items:
      type: string
    example:
      - foo
      - bar
      - baz
```

Similarly, an array of objects would be specified as:

```yml
definitions:
  ArrayOfCatalogItems:
    type: array
    items:
      $ref: '#/definitions/CatalogItem'
    example:
      - id: 38
        title: T-shirt
      - id: 114
        title: Phone
```

#### Whole Schema Examples

An `example` can be specified for the entire schema (including all nested schema), provided that the example conforms to the schema.

```yml
definition:
  CatalogItem:
    type: object
    properties:
      id:
        type: integer
      name:
        type: string
      image:
        type: object
        properties:
          url:
            type: string
          width:
            type: integer
          height:
            type: integer
    required:
      - id
      - name
    example:   # <----------
      id: 38
      name: T-shirt
      image:
        url: images/38.png
        width: 100
        height: 100
```

### Response Examples

Swagger allows examples on the response level, each example corresponding to a specific MIME type returned by the operation. Such as one example for `application/json`, another one for `text/csv` and so on. Each MIME type must be one of the operation’s `produces` values -- either explicit or inherited from the global scope.

```yml
produces:
  - application/json
  - text/csv
responses:
  200:
    description: OK
    examples:
      application/json: { "id": 38, "title": "T-shirt" }
      text/csv: >
        id,title
        38,T-shirt
```

All examples are **free-form**, meaning their interpretation is up to tools and libraries.

#### JSON and YAML Examples

Since JSON and YAML are interchangeable (YAML is a superset of JSON), both can be specified either using the JSON syntax:

```json
examples:
  application/json:
    {
      "id": 38,
      "title": "T-shirt"
    }
```

or the YAML syntax:

```yml
examples:
  application/json:
    id: 38
    title: T-shirt
    image:
      url: images/38.png
```

#### XML Examples

There is no specific syntax for XML response examples. But, since the response examples are free-form, you can use any format that you wish or that is supported by your tool.

```yml
examples:
  application/xml: '<users><user>Alice</user><user>Bob</user></users>'

examples:
  application/xml:
    users:
      user:
        - Alice
        - Bob

examples:
  application/xml:
    url: http://myapi.com/examples/users.xml
```

Alternatively, you can specify the example values in the response schema, as [explained above](#schema-examples).

#### Text Examples

Since all response examples are free-form, you can use any format supported by your tool or library. For instance, something like:

```yml
examples:
  text/html: '<html><body><p>Hello, world!</p></body></html>'
  text/plain: Hello, world!
```

See also [this post on Stack Overflow](https://stackoverflow.com/questions/3790454/in-yaml-how-do-i-break-a-string-over-multiple-lines) for tips on how to write multi-line strings in YAML.

#### Example Precedence

If there are multiple examples on different levels (property, schema, response), the higher-level example is used by the tool that is processing the spec. That is, the order of precedence is:

1.  Response example
2.  Schema example
3.  Object and array property examples
4.  Atomic property examples and array item examples

#### Examples and $ref

OpenAPI 2.0 `example` and `examples` keywords require inline examples and **do not support `$ref`**. The example values are displayed as is, so `$ref` would be displayed as an object property named `$ref`.

Referencing examples is [supported in OpenAPI 3.0](/docs/specification/adding-examples/).

_Did not find what you were looking for? [Ask the community](https://community.smartbear.com/t5/Swagger-Open-Source-Tools/bd-p/SwaggerOSTools)  
Found a mistake? [Let us know](https://github.com/swagger-api/swagger.io/issues)_
