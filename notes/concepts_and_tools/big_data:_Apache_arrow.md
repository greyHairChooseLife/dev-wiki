# How to convert csv to arrow table?

## From rows to columns

1. Need to make each column to be as a vector
2. Need to make array of columns (the Arrow might help us)

### Describe the data

- Arrow tables are strictly typed. We need to describe the type of columns.
- A lot of types are there.
  For the table data, `Struct` type is good choice. Like Object in JS, keys has its own type.
  - string
    `ex) nameField = new arrow.Field('name', new arrow.Utf8());`
  - integer, no larger than 2^32
    `ex) popEstField = new arrow.Field('pop_est', new arrow.Unit32());`
  - Dictionary
    This type is for a vlues that are limited like a few strings. So the Arrow doesn't need to store it everytime.
    `ex) continentField = new arrow.Field('continent', new arrow.Dictionary(new arrow.Utf8(), new arrow.Uint8()));`

  Finally, we combine these fields into a struct type.
  `ex) type = new arrow.Struct([nameField, popEstField, continentField])`

### From rows to vectors

Arrow provides a convenient function for **creating a vector** from a row-oriented data set: **vectorFromArray**.
We just need to give it our data, and a type that describes the data.

`ex) vector = arrow.vectorFromArray(data, type) // this type is what we made above`

> Too easy? We can touch a little about the way the vector is built.
>
> First, create a builder from some type.
> Then, loop over the data and feed each row to the builder. When loops' done, ask builder for a vector.
> ```
> ex)
> vector = arrow.makeBuilder({type})
>
> {
>   for (const row of data) {
>     builder.append(row)
>   }
>   builder.finish()
>   return builder.toVector()
> }
> ```
