# yaql
Yaql's A Query Language

The goal of this project is to define a way to express SQL syntax using yaml. It may possibly one day include libraries to translate a yaql object into a SQL query, depending on how interested I become in this.

## Rules
The following rules describe valid yaql.

### Root
The root node must be one of the following, which defines the type of statement that will be executed.
* select
* update
* insert
* delete
* create
* alter
* drop

## Examples

### Simple
```yaml
select:
  columns:
    id
    name
    date
  on:
    foo
  where:
    status: active
```
This is equivalent to the following.
```yaml
--- 
select: 
  columns: "id name date"
  true: foo
  where: 
    status: 
      equals: active
      operator: and
```
