# yaql
Yaql's A Query Language

The goal of this project is to define a way to express SQL syntax using yaml. It may possibly one day include libraries to translate a yaql object into a SQL query, depending on how interested I become in this.

## Rules
The following rules describe valid yaql.

### Root
The root node must be one of the following, which defines the type of statement that will be executed.
* `select`
* `update`
* `insert`
* `delete`
* `create`
* `alter`
* `drop`

All root nodes require a child `on` that describes the table the action is acting upon.

### Select
The `select` action has the following available children.
* `columns`
* *`on`
* `where`
* `group`
* `order`

#### columns
`columns` is a collection of column names to select from. The values of the collection must either be a valid column in the table(s) being selected from, or a string literal.

#### on
`on` defines the table that data will be retrieve from. This is equivalent to the `from` keyword usually used in ANSI SQL.

#### where
`where` allows us to filter out rows of data that we do not want returned as part of our result set.

`where`'s children are a list of key: value pairs. If the value of the key is a simple value then there is an implied boolean operator of `and` and an implied relational operator of equals or `=`.

The default boolean and relational operators can be overridden. To override the relational operator provide a child key of one of the following.
* equals
* not-equals
* greater-than
* great-than-equals
* less-than
* less-than-equals
* in
* not-in

`in` and `not-in` take a collection as their values. All of the other relational operators take a value for which the key will be evaluated against.

The boolean operator is always ignored on the first child.

#### group
`group` is a collection of columns for which to group the results by.

#### order
`order` is a collection of columns for which to order the data results by. Each entry of the collection can either be the simple name of a column, which has a default sorting of `ascending`, or the column can be key with a value of either `asc` or `desc`.

#### Examples

##### Simple
```yaml
select:
  columns:
    - id
    - name
    - date
  on:
    foo
  where:
    -
      status: active
    -
      bar: baz
```
This is equivalent to the following.
```yaml
--- 
select: 
  columns:
    - id
    - name
    - date
  true: foo
  where:
    -
      status: 
        equals: active
    -
      bar:
        equals: baz
        operator: and
```
If you want to select all columns, e.g. `select *` just don't include `columns`.
```yaml
select:
  on: foo
```

For selecting from `dual` as in MySQL or Oracle SQL, or in MySQL or MS SQL where the `from` statement is ommited, just leave the value of `on` blank.

```yaml
select: 
  columns: 
    - "We don't need no stinkin' tables"
  on:
```

##### Complex
```yaml
select: 
  columns: 
    - "I'm just here."
    - required
    - name
  order: 
    - 
      status: desc
    - name
  on: foo
  where: 
    - 
      status: 
        in: 
          - active
          - pending
    - 
      requried: true
    - 
      viewCount: 
        greater-than-equals: 10
        operator: or

```
