# SQL Data definition

## Basic types

* **char(n)**: A fixed-length character string with user-specified length n. Space characters will be appended to the string if the length of the string is smaller than the fixed-length.
* **varchar(n)**:A variable-length character string with user-specified maximum length
n. The full form, character varying, is equivalent. Prefer `varchar` to `char`.
* **int**: An integer (a finite subset of the integers that is machine dependent). The full
form, integer, is equivalent.
* **smallint**: A small integer (a machine-dependent subset of the integer type).
* **numeric(p, d)**: A fixed-point number with user-specified precision. The number
consists of p digits (plus a sign), and d of the p digits are to the right of the decimal
point. Thus, numeric(3,1) allows 44.5 to be stored exactly, but neither 444.5 nor
0.32 can be stored exactly in a field of this type.
* **real, double precision**: Floating-point and double-precision floating-point numbers
with machine-dependent precision.
* **float(n)**: A floating-point number with precision of at least n digits.

## Basic Schema Definition

```sql
create table department
    (dept_name varchar (20),
    building varchar (15),
    budget numeric (12,2),
    primary key (dept_name));
```

SQL supports a number of different integrity constraints. In this section, we discuss
only a few of them:

* **primary key (Aj1, Aj2, … , Ajm)**: The primary-key specification says that attributes
Aj1, Aj2, … , Ajm form the primary key for the relation.

    The primary-key attributes are required to be nonnull and unique. 

    Although the primary-key specification is optional, it is generally a good idea to specify a primary key for each relation.

* **foreign key (Ak1, Ak2, … , Akn) references s**: The foreign key specification says that the values of attributes (Ak1, Ak2, … , Akn) for any tuple in the relation must correspond to values of the `primary key` attributes of some tuple in relation s.

* **not null**: The not null constraint on an attribute specifies that the null value is not
allowed for that attribute

### drop table

The drop table command deletes all information about the dropped relation from the database. 

The command `drop table r;` is a more drastic action than `delete from r;`.

The latter retains relation r, but deletes all tuples in r. The former deletes not only all
tuples of r, but also the schema for r. After r is dropped, no tuples can be inserted into
r unless it is re-created with the create table command.

We use the `alter table` command to add attributes to an existing relation. 

The form of the alter table command is

`alter table r add A D;`

where r is the name of an existing relation, A is the name of the attribute to be added, and D is the type of the added attribute.

We can drop attributes from a relation by the command

`alter table r drop A;`

where r is the name of an existing relation, and A is the name of an attribute of the
relation. Many database systems do not support dropping of attributes, although they
will allow an entire table to be dropped.

# Basic Structure of SQL Queries

```sql
select A1, A2, … , An
from r1, r2, … , rm
where P;
```
Each Ai represents an attribute, and each ri a relation. P is a predicate. If the where
clause is omitted, the predicate P is true.


### keywords for select:

* select distinct

    In those cases where we want to force the elimination of duplicates, we insert thekeyword `distinct` after select. 
    ```sql
    select distinct dept_name
    from instructor;
    ```

* select all
    SQL allows us to use the keyword `all` to specify explicitly that duplicates are not removed.
    ```sql
    select all dept_name
    from instructor;
    ```

### Queries on Multiple Relations

```sql
select name, instructor.dept_name, building
from instructor, department
where instructor.dept_name= department.dept_name;
```
The from clause by itself defines a Cartesian product of the relations listed in the clause. 

# Additional Basic Operations

The `as` clause is particularly useful in renaming relations. One reason to rename a relation is to replace a long relation name with a shortened version that is more convenient to use elsewhere in the query.

To illustrate, we rewrite the query "For all instructors in the university who have taught some course, find their names and the course ID of all courses they taught."

```sql
select T.name, S.course_id
from instructor as T, teaches as S
where T.ID= S.ID;
```

Suppose that we want to write the query “Find the names of all instructors whose
salary is greater than at least one instructor in the Biology department.” We can write
the SQL expression:
```sql
select distinct T.name
from instructor as T, instructor as S
where T.salary > S.salary and S.dept
name = 'Biology';
```

### String Operations

Percent (%): The `%` character matches any substring.
Underscore (_): The `_` character matches any character.

# Set Operations

* The Union Operation:
```sql
(select course
id
from section
where semester = 'Fall' and year= 2017)
union
(select course
id
from section
where semester = 'Spring' and year= 2018);
```

* The Intersect Operation:
    `intersect`
    The intersect operation automatically eliminates duplicates.
* The Except Operation:
    `except`
    The except operation outputs all tuples from its first input that do not occur in the second input.

# Null Values

`Null values` present special problems in relational operations, including arithmetic operations, comparison operations, and set operations.

The result of an arithmetic expression (involving, for example, +, −, ∗, or ∕) is null if any of the input values is null.

Comparisons involving nulls are more of a problem. 

SQL therefore treats as unknown the result of any comparison involving a null value (other than predicates `is null` and `is not null`, which are described later in this section).

Boolean operations:
* and: The result of true and unknown is unknown, false and unknown is false, while unknown and unknown is unknown.
* or: The result of true or unknown is true, false or unknown is unknown, while un-
known or unknown is unknown.
* not: The result of not unknown is unknown.

# Aggregate Functions

## Basic aggregation
avg, min, max, sum, count

## Aggregation with Grouping

```sql
select dept_name, avg (salary) as avg_salary
from instructor
group by dept_name;
```

## The Having Clause

SQL applies predicates in the `having` clause after groups have been formed, so aggregate functions may be used in the having clause.

```sql
select dept_name, avg (salary) as avg_salary
from instructor
group by dept_name
having avg (salary) > 42000;
```
As was the case for the `select` clause, any attribute that is present in the `having` clause without being aggregated must appear in the group by clause, otherwise the query is erroneous.

## Aggregation with Null and Boolean Values

All aggregate functions except count (*) ignore null values in their input collection. 

As a result of null values being ignored, the collection of values may be empty. The count of an
empty collection is defined to be 0, and all other aggregate operations return a value of null when applied on an empty collection. The effect of null values on some of the more complicated SQL constructs can be subtle.

# Nested Subqueries

## Set Membership

The `in` connective tests for set membership, where the set is a collection of values produced by a select clause. The `not in` connective tests for the absence of set membership.

## Set Comparison

```sql
select distinct T.name
from instructor as T, instructor as S
where T.salary > S.salary and S.dept
name = 'Biology';
```
SQL offers an alternative style for writing the preceding query. The phrase “greater than at least one” is represented in SQL by > `some`. 
```sql
select name
from instructor
where salary > some (select salary
                    from instructor
                    where dept
                    name = 'Biology');
```

SQL also allows < some, <= some, >= some, = some, and <> some comparisons. As an exercise, verify that = some is identical to `in`, whereas <> some is not the same as `not in`.

The construct > `all` corresponds to the phrase "greater than all."

## Test for Empty Relations

The `exists` construct returns the value true if the argument subquery is nonempty. 

We can test for the nonexistence of tuples in a subquery by using the `not exists` construct. 

## Test for the Absence of Duplicate Tuples

The `unique` construct returns the value true if the argument subquerycontains no duplicate tuples. 

## Subqueries in the From Clause

```sql
select dept_name, avg_salary
from (select dept_name, avg (salary)
    from instructor
    group by dept_name)
    as dept_avg (dept_name, avg_salary)
where avg_salary > 42000
```

`lateral` keyword:
The SQL standard allows a subquery in the `from` clause that is prefixed by the `lateral` keyword
to access attributes of preceding tables or subqueries in the same from clause.

```sql
select name, salary, avg_salary
from instructor I1, lateral (select avg(salary) as avg_salary
                            from instructor I2
                            where I2.dept_name= I1.dept_name);
```
Without the `lateral` clause, the subquery cannot access the correlation variable I1 from
the outer query. Only the more recent implementations of SQL support the lateral
clause.

## The With Clause

The `with` clause provides a way of defining a temporary relation whose definition is available only to the query in which the with clause occurs. 

```sql
with max_budget (value) as
    (select max(budget)
    from department)
select budget
from department, max_budget
where department.budget = max_budget.value;
```

## Scalar Subqueries

SQL allows subqueries to occur wherever an expression returning a value is permitted, provided the subquery returns only one tuple containing a single attribute; such subqueries are called `scalar subqueries`.

```sql
select dept_name,
        (select count(*)
        from instructor
        where department.dept_name = instructor.dept_name)
as num_instructors
from department;
```
Scalar subqueries can occur in `select`, `where`, and `having` clauses.

## Scalar Without a From Clause

```sql
(select count (*) from teaches) / (select count (*) from instructor);
```

# Modification of the Database

## Deletion

A `delete` request is expressed in much the same way as a query. We can delete only whole tuples; we cannot delete values on only particular attributes. 

```sql
delete from r
where P;
```

## Insertion

The simplest `insert` statement is a request to insert one tuple.
Example 1:
```sql
insert into course
    values ('CS-437', 'Database Systems', 'Comp. Sci.', 4);
```

Exapmle 2:
```sql
insert into instructor
    select ID, name, dept_name, 18000
    from student
    where dept_name = 'Music' and tot_cred > 144;
```

## Updates

In certain situations, we may wish to change a value in a tuple without changing all values in the tuple. For this purpose, the update statement can be used. As we could for `insert` and `delete`, we can choose the tuples to be updated by using a query.

```sql
update instructor
set salary= salary * 1.05;
where salary < 70000;
```

# Summary

1. SQL is the most influential commercially marketed relational query language. The
SQL language has several parts:

    `Data-definition language (DDL)`, which provides commands for defining rela-
tion schemas, deleting relations, and modifying relation schemas.

    `Data-manipulation language (DML)`, which includes a query language and com-
mands to insert tuples into, delete tuples from, and modify tuples in the
database.
2. SQL includes a variety of language constructs for queries on the database. These include the select, from, and where clauses.
3. SQL supports basic set operations on relations, including `union`, `intersect`, and `except` which correspond to the mathematical set operations ∪, ∩, and −.
4. SQL handles queries on relations containing null values by adding the truth value "unknown" to the usual truth values of true and false.
5. SQL provides constructs for updating, inserting, and deleting information.