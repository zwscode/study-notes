# 1. Join Expressions

## The Natural Join

The `natural join` operation operates on two relations and produces a relation as the
result. Unlike the Cartesian product of two relations, which concatenates each tuple of
the first relation with every tuple of the second, natural join considers only those pairs
of tuples with the same value on those attributes that appear in the schemas of both
relations. 

`natural join` equals `natural inner join`.

```sql
select name, course_id
from student, takes
where student.ID = takes.ID;
```

```sql
select name, course_id
from student natural join takes;
```
the two equries above generate the same result.

## Join Conditions

`join` equals `inner join`

```sql
select *
from student join takes on student.ID = takes.ID;
```

## Outer Joins

The `outer-join` operation works in a manner similar to the join operations we have already studied, but it preserves those tuples that would be lost in a join by creating tuples in the result containing null values.

* The left outer join preserves tuples only in the relation named before (to the left of) the left outer join operation.
* The right outer join preserves tuples only in the relation named after (to the right of) the right outer join operation.
* The full outer join preserves tuples in both relations.

# 2. Views

The form of the create view command is:
```sql
create view v as <query expression>;
```

Example:
```sql
create view faculty as
select ID, name, dept_name
from instructor;
```

## Materialized Views

Definition:
Certain database systems allow view relations to be stored, but they make sure that, if the actual relations used in the view definition change, the view is kept up-to-date. Such views are called `materialized views`.

SQL does not define a standard way of specifying that a view is materialized, but many database systems provide their own SQL extensions for this task. Some database systems always keep materialized views up-to-date when the underlying relations change, while others permit them to become out of date and periodically recompute them.

## Update of a View

In general, an SQL view is said to be updatable (i.e., inserts, updates, or deletes can be applied on the view) if the following conditions are all satisfied by the query defining the view:
* The from clause has only one database relation.
* The select clause contains only attribute names of the relation and does not have any expressions, aggregates, or distinct specification.
* Any attribute not listed in the select clause can be set to null; that is, it does not have a not null constraint and is not part of a primary key.
* The query does not have a group by or having clause.

# 3. Transactions

A transaction consists of a sequence of query and/or update statements. The SQL standard specifies that a transaction begins implicitly when an SQL statement is executed. One of the following SQL statements must end the transaction:
* `Commit work` commits the current transaction; that is, it makes the updates performed by the transaction become permanent in the database. After the transaction is committed, a new transaction is automatically started.
* `Rollback work` causes the current transaction to be rolled back; that is, it undoes all the updates performed by the SQL statements in the transaction. Thus, the database state is restored to what it was before the first statement of the transaction was executed.

Once a transaction has executed commit work, its effects can no longer be undone by rollback work. The database system guarantees that in the event of some failure, such as an error in one of the SQL statements, a power outage, or a system crash, a transaction’s effects will be rolled back if it has not yet executed commit work.
In the case of power outage or other system crash, the rollback occurs when the system restarts.

`Atomic`:
By either committing the actions of a transaction after all its steps are completed, or rolling back all its actions in case the transaction could not complete all its actions successfully, the database provides an abstraction of a transaction as being atomic, that is, indivisible. Either all the effects of the transaction are reflected in the database or none are (after rollback).

# 4. Integrity Constraints

`Integrity constraints` ensure that changes made to the database by authorized users
do not result in a loss of data consistency.

Integrity constraints are usually identified as part of the database schema design process and declared as part of the `create table` command used to create relations.

However, integrity constraints can also be added to an existing relation by using the
command `alter table` *table-name* `add` *constraint*, where constraint can be any constraint on the relation. When such a command is executed, the system first ensures that the relation satisfies the specified constraint. If it does, the constraint is added to the relation; if not, the command is rejected.

## Constraints on a Single Relation

* not null
* unique
* check(<predicate>)

### Not Null Constraint

```sql
name varchar(20) not null
budget numeric(12,2) not null
```

The `not null` constraint prohibits the insertion of a null value for the attribute, and is an example of a `domain constraint`. Any database modification that would cause a null to be inserted in an attribute declared to be not null generates an error diagnostic.

Primary key is automaticly considered not null.

### Unique Constraint

```sql
unique (Aj1, Aj2, … , Ajm)
```

The `unique` specification says that attributes Aj1, Aj2, … , Ajm form a superkey; that is, no
two tuples in the relation can be equal on all the listed attributes.

Special case:
However, attributes declared as unique are permitted to be null unless they have explicitly been declared to be not null. 

### The Check Clause

When applied to a relation declaration, the clause `check(P)` specifies a predicate `P` that must be satisfied by every tuple in a relation.

A common use of the `check` clause is to ensure that attribute values satisfy specified conditions, in effect creating a powerful type system. For instance, a clause check (budget > 0) in the `create table` command for relation department would ensure that the value of budget is nonnegative.

Another example of use of `check` clause:

```sql
create table section
    (course_id varchar (8),
    sec_id varchar (8),
    semester varchar (6),
    year numeric (4,0),
    building varchar (15),
    room_number varchar (7),
    time_slot_id varchar (4),
    primary key (course_id, sec_id, semester, year),
    check (semester in ('Fall', 'Winter', 'Spring', 'Summer')));
```
We use the check clause to simulate an enumerated type by specifying that semester must be one of 'Fall', 'Winter', 'Spring', or 'Summer'. Thus, the check clause permits attribute domains to be restricted in powerful ways that most programming-language type systems do not permit.

Special case:
Null values present an interesting special case in the evaluation of a check clause. A check clause is satisfied if it is not false, so clauses that evaluate to unknown are not violations. If null values are not desired, a separate not null constraint (see Section 4.4.2) must be specified.

Where to place the `check` clause:
A `check` clause may appear on its own, as shown above, or as part of the declaration of an attribute.

Example (`check` clause as part of declaration of an attribute):
```sql
create table department
    (dept_name varchar (20),
    building varchar (15),
    budget numeric (12,2) check (budget > 0),
    primary key (dept_name));
```

Typically, constraints on the value of a single attribute are listed with that attribute, while more complex check clauses are listed separately at the end of a create table statement.


## Referential Integrity (Page 149)

Foreign keys can be specified as part of the SQL `create table` statement by using the `foreign key` clause.

```sql
foreign key (dept_name) references department
```

How to specify foreign key:
By default, in SQL a foreign key references the primary-key attributes of the referenced table. SQL also supports a version of the references clause where a list of attributes of the referenced relation can be specified explicitly.

Example (specify attribute of the referenced relation):
```sql
foreign key (dept_name) references department(dept_name)
```

Requirements for being referenced as foreign key:
The specified list of attributes must, however, be declared as a `superkey` of the referenced relation, using either a `primary key` constraint or a `unique` constraint.


### What happens when referential constaints is violated:

When a referential-integrity constraint is violated, the normal procedure is to reject
the action that caused the violation.However, a foreign key clause can specify that if a delete or update action on the referenced relation violates the constraint, then, instead of rejecting the action, the system must take steps to change the tuple in the referencing relation to restore the
constraint. 

`cascades`:
```sql
create table course
    ( ...
    foreign key (dept_name) references department
                on delete cascade
                on update cascade,
    ...);
```
Because of the clause on delete cascade associated with the foreign-key declaration, if a delete of a tuple in department results in this referential-integrity constraint being violated, the system does not reject the delete. Instead, the delete "`cascades`" to the course relation, deleting the tuple that refers to the department that was deleted. 

Special case (further violation):
If a cascading update or delete causes a constraint violation that cannot be handled by a further cascading operation, the system aborts the transaction.

## Assigning Names to Constraints

To name a constraint, we precede the constraint with the keyword `constraint` and the name we wish to assign it.

```sql
salary numeric(8,2), constraint minsalary check (salary > 29000),
```

Later, if we decide we no longer want this constraint, we can write:

```sql
alter table instructor drop constraint minsalary;
```

## Integrity Constraint Violation During a Transaction (Page 151)

What's the problem:

Transactions may consist of several steps, and integrity constraints may be violated temporarily after one step, but a later step may remove the violation.

How to solve it:

To handle such situations, the SQL standard allows a clause initially deferred to be added to a constraint specification; the constraint would then be checked at the end of a transaction and not at intermediate steps. A constraint can alternatively be specified as deferrable, which means it is checked immediately by default but can be deferred when desired. 

For constraints declared as deferrable, executing a statement set constraints constraint-list deferred as part of a transaction causes the checking of the specified constraints to be deferred to the end of that transaction. 

## Complex Check Conditions and Assertions (Page 152)

Complex check conditions can be useful when we want to ensure the integrity of data, but they may be costly to test.
```sql
check (time_slot_id in (select time_slot_id from time_slot))
```


An `assertion` is a predicate expressing a condition that we wish the database always to satisfy. Consider the following constraints, which can be expressed using assertions.

