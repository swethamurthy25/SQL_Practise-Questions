
Tesla is investigating production bottlenecks and they need your help to extract the relevant data. Write a query to determine which parts have begun the assembly process but are not yet finished.

Assumptions:

parts_assembly table contains all parts currently in production, each at varying stages of the assembly process.
An unfinished part is one that lacks a finish date.
This question is straightforward, so let's approach it with simplicity in both thinking and solution.

<img width="320" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/397827fb-d5e6-477a-b779-949994441201">

<img width="229" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/94a223b3-fe42-450d-99ad-6189b6a27242">

### Solution

```sql
SELECT part,assembly_step FROM parts_assembly
WHERE finish_date IS NULL;
```


