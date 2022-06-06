## The plan
I want to create a table, and add a trigger and a function to handle update.
When creating a new row, the defaults sould be set, and a constraint will prevent dublicate keys.
Notice that PostgreSQL has a (nice) concept about 'infinity'. Other Database systems use something like 9999-12-31.
```SQL
CREATE TABLE public.t_tabletest (
	id int4 NOT NULL GENERATED ALWAYS AS IDENTITY,
	person_id int2 NOT null,
	city varchar(40) NOT NULL,
	info1 varchar(40),
	info2 varchar(40),
	active_from timestamptz not NULL DEFAULT now(),
	active_to timestamptz not NULL DEFAULT 'infinity',
	active boolean not NULL DEFAULT true,
	modified_by varchar(63) not null default get_app_user(),
	modified_by_session_user varchar(63) not null default SESSION_USER,
	CONSTRAINT t_tabletest_unique UNIQUE (pilot_id, active_to)
);
```
The get_app_user() is a function the helps me getting the name of the logged in user. 
It is described [here](https://github.com/ThorkilG12/Frontend-Backend-Communication/tree/main/Get%20the%20logged%20in%20users%20id%20into%20the%20database)
