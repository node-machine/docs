# Understanding Inputs

This is a quick reference sheet for seeing how validation and coercion work for inputs.

<!-- > These tables will eventually be automatically generated. -->

## Principles

Validation and coercion tend to be _**forgiving** and **protective** of the user_ but _harsh to the implementor_.

For instance, a user can pass in `"2"` (a string) to an input expecting a number example and the value will be cast to the number `2`.


#### Using `required`

| input required?        | actual value provided  | yields outcome...
| ---------------------- | ---------------------- | ----------------------
| `true`                 | `undefined`            | throws Error
| `false` or `undefined` | `undefined`            | `undefined`
| either way             | `null`                 | throws Error
| either way             | `Infinity`             | throws Error
| either way             | `-Infinity`            | throws Error
| either way             | `NaN`                  | throws Error



#### Specifying a type schema as an `example`

Whenever possible, use an `example` to define the type of your input.  This is the most specific and unambiguous way to ensure your machine will be used properly.


| input `example`          | actual value provided  | yields outcome...
| ------------------------ | ---------------------- | ----------------------
| `"hello!"`               | `"hi"`                 | `"hi"`
| `"hello!"`               | `""`                   | `""`
| `"hello!"`               | `7`                    | `"7"`
| `"hello!"`               | `true`                 | `"true"`
| `7` or `-7.7` etc.       | `3.5`                  | `3.5`
| `7` or `-7.7` etc.       | `-3`                   | `-3`
| `7` or `-7.7` etc.       | `"-3"`                 | `-3`
| `7` or `-7.7` etc.       | `"10.5"`               | `10.5`
| `true` or `false`        | `true`                 | `true`
| `true` or `false`        | `false`                | `false`
| `true` or `false`        | `"true"`               | `true`
| `true` or `false`        | `"false"`              | `false`




#### Let your hair down with some `typeclass`

Sometimes, an `example` is too narrow.  For instance, `parse-json` from [machinepack-util](http://node-machine.org/machinepack-util/parse-json) uses `typeclass: '*'` on its `schema` input to allow any value.

| input `typeclass`        | actual value provided  | yields outcome...
| ------------------------ | ---------------------- | ----------------------
| `"*"`               | `"hi"`                 | `"hi"`
| `"*"`               | `""`                   | `""`
| `"*"`               | `7`                    | `7`
| `"*"`               | `true`                 | `true`
| `"dictionary"`               | `"hi"`                 | throws Error
| `"dictionary"`               | `""`                   | throws Error
| `"dictionary"`               | `7`                    | throws Error
| `"dictionary"`               | `true`                 | throws Error
| `"dictionary"`               | `{foo:'bar',baz:13}`                 | `{foo: 'bar', baz: 13}`
| `"array"`               | `"hi"`                 | throws Error
| `"array"`               | `""`                   | throws Error
| `"array"`               | `7`                    | throws Error
| `"array"`               | `true`                 | throws Error
| `"array"`               | `{foo:'bar',baz:13}`                 | throws Error
| `"array"`               | `[{foo:'bar',baz:13}]`                 | `[{foo:'bar',baz:13}]`


> TODO: more docs on objects and arrays


#### Special cases
+ If a custom `validate()` function was provided for an input, all built-in validation and/or coercion is skipped.
+ The configured `inputs` object accessible to `getExample()` and `validate()` are not guaranteed to have been coerced.  You should make sure your custom `getExample()` and `validate()` functions take this into account (e.g. if a user passes the value `"3"` into some input (x) with a numeric example, the `validate` function for some other input (y) might receive `inputs.x` as either `"3"` or `3`)

