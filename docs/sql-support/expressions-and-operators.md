# Expressions and operators

## Basic operators[ ](https://docs.yugabyte.com/preview/explore/ysql-language-features/expressions-operators/#basic-operators) <a href="#basic-operators" id="basic-operators"></a>

### Mathematical operators[ ](https://docs.yugabyte.com/preview/explore/ysql-language-features/expressions-operators/#mathematical-operators) <a href="#basic-operators" id="basic-operators"></a>

| OPERATOR | DESCRIPTION                             | EXAMPLE                |
| -------- | --------------------------------------- | ---------------------- |
| +        | Addition                                | 1 + 2 results in 3     |
| -        | Subtraction                             | 1 - 2 results in -1    |
| \*       | Multiplication                          | 2 \* 2 results in 4    |
| /        | Division                                | 6 / 2 results in 3     |
| %        | Remainder                               | 5 % 4 results in 1     |
| ^        | Exponent (association of left to right) | 2.0 ^ 3.0 results in 8 |

### Comparison operators[ ](https://docs.yugabyte.com/preview/explore/ysql-language-features/expressions-operators/#comparison-operators) <a href="#comparison-operators" id="comparison-operators"></a>

| OPERATOR | DESCRIPTION              | EXAMPLE |
| -------- | ------------------------ | ------- |
| <        | Less than                | a < 5   |
| >        | Greater than             | a > 5   |
| <=       | Less than or equal to    | a <= 5  |
| >=       | Greater than or equal to | a >= 5  |
| =        | Equal                    | a = 5   |
| <>       | Not equal                | a <> 5  |
| !=       | Not equal                | a != 5  |

### Logical operators[ ](https://docs.yugabyte.com/preview/explore/ysql-language-features/expressions-operators/#logical-operators)

| OPERATOR | DESCRIPTION                                                                    |
| -------- | ------------------------------------------------------------------------------ |
| AND      | Allows the existence of multiple conditions in a `WHERE` clause.               |
| NOT      | Negates the meaning of another operator. For example, `NOT IN`, `NOT BETWEEN`. |
| OR       | Combines multiple conditions in a `WHERE` clause.                              |

### Conditional expressions and operators

| Expression | Description                                                                                                                                         |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| CASE       | A conditional statement that allows you to perform conditional logic in queries, returning different values based on specific conditions being met. |
| CAST       | convert a value of one data type to another                                                                                                         |

