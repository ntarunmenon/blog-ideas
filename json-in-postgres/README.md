Based on the talk by Andrew Dunstan [here](https://www.youtube.com/watch?v=tMT16q9pqp0)

#### Pre - Requesisites

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TABLE employee
(
    systemid uuid,
    employeeid text,
    first_name text,
    PRIMARY KEY (systemid)
)

CREATE TABLE employee_json
(
    systemid uuid,
    employee_json jsonb NOT NULL,
    PRIMARY KEY (systemid)
)

INSERT INTO public.employee(
	systemid, employeeid, first_name)
	VALUES (uuid_generate_v1(), 'emp1', 'Rajesh');
	
INSERT INTO public.employee(
	systemid, employeeid, first_name)
	VALUES (uuid_generate_v1(), 'emp2', 'Sujesh');
	
INSERT INTO public.employee(
	systemid, employeeid, first_name)
	VALUES (uuid_generate_v1(), 'emp3', 'Vignesh');

insert into employee_json(systemid,employee_json) 
    select uuid_generate_v1(), jsonrecord from 
        ((select to_json(r) jsonrecord from (select * From employee)r)) jsonrecords

```

#### to_json

```sql
select to_json(r) from 
	(select * From employee where employeeid = 'emp2' ) r
```

will return 

```json
{"systemid":"dfffbfac-6937-11e9-bc0d-144f8a4326cd","employeeid":"emp1","first_name":"Rajesh"}
```

### json_agg

```sql
select json_agg(r) from (select * from employee) r
```
will return json array

```json
[{"systemid":"dfffbfac-6937-11e9-bc0d-144f8a4326cd","employeeid":"emp1","first_name":"Rajesh"}, 
 {"systemid":"dfffbfad-6937-11e9-bc0d-144f8a4326cd","employeeid":"emp2","first_name":"Sujesh"}, 
 {"systemid":"dfffbfae-6937-11e9-bc0d-144f8a4326cd","employeeid":"emp3","first_name":"Vignesh"}]
 ```

 ### json_object_agg

 ```sql
 select json_object_agg(employeeid,firstname) from (select employeeid,firstname from employee) r
 ```
 will return 

 ```json
 { "emp1" : "Rajesh", "emp2" : "Sujesh", "emp3" : "Vignesh" }
 ```

 ### json_populate_record

```sql
insert into employee select * from json_populate_record(NULL::employee, '{"systemid":"c4ad0abc-693b-11e9-bc0d-144f8a4326cd","employeeid":"emp4","first_name":"Sumesh"}')

will insert a new record into employee table
```

### query specific values from json

```sql
 select employee_json->'employeeid' from employee_json
```

will return 3 rows as JSONB

```
"emp1"
"emp2"
"emp3"
```

when you use `>>` as in `employee_json->>'employeeid'` 

```sql
 select employee_json->>'employeeid' from employee_json
```

will return 3 rows as text

```
emp1
emp2
emp3
```

```sql
select json_agg(r) -> 0 from (select employee_json from employee_json) r
```
will return the first object from the resulting JSON array

```json
select json_agg(r) -> 0 from (select employee_json from employee_json) r
```

values 

```JSON
{"employee_json":{"systemid": "dfffbfac-6937-11e9-bc0d-144f8a4326cd", "employeeid": "emp1", "first_name": "Rajesh"}}
```

### other interesting JSON  functions

```sql
json_each
json_object_keys
json_array_elements
json_array_length
jsonb_pretty
|| -- union of JSONB
- --delete element from JSON 
```