## Create table that collect [type 2](https://en.wikipedia.org/wiki/Slowly_changing_dimension#Type_2:_add_new_row) history in the same table.
I want to create a table, and add a trigger and a function to handle updates.

When creating a new row, the defaults sould be set, and a constraint will prevent duplicate keys.
Notice that PostgreSQL has a (nice) concept about 'infinity'. Other database systems use something like 9999-12-31 or NULL in this situation.

I want only one table. Both the current and the historical rows should be in the same tabel.
No-one should have delete-rights to the table, since deletions is prohibitet. You can make a row passive, but you can't delete anything.

Let's create
```SQL
DROP TABLE if exists public.t_tabletest;
CREATE TABLE public.t_tabletest (
	id int4 NOT NULL GENERATED ALWAYS AS IDENTITY,
	product_id int2 NOT null,
	product varchar(40) NOT NULL,
	info1 varchar(40),
	info2 varchar(40),
	active_from timestamptz not NULL DEFAULT now(),
	active_to timestamptz not NULL DEFAULT 'infinity',
	active boolean not NULL DEFAULT true,
	modified_by varchar(63) not null default get_app_user(),
	modified_by_session_user varchar(63) not null default SESSION_USER,
	CONSTRAINT t_tabletest_product_id UNIQUE (product_id, active_to),
	CONSTRAINT t_tabletest_product UNIQUE (product, active_to)
);
```
The get_app_user() is a function the helps me getting the name of the logged in user. 
It is described [here](https://github.com/ThorkilG12/Frontend-Backend-Communication/tree/main/Get%20the%20logged%20in%20users%20id%20into%20the%20database)
```SQL
CREATE OR REPLACE FUNCTION public.t_tabletest_update_1()
  	RETURNS trigger LANGUAGE plpgsql AS $function$ BEGIN 
		INSERT INTO t_tabletest (product_id, product, info1, active_from, active_to, active)
	    	VALUES (OLD.product_id, old.product, old.info1, old.active_from, now(), false);
		UPDATE t_tabletest 
			SET modified_by = get_app_user(),
	      		modified_by_session_user = SESSION_USER,
	      		active_from = now() + '1 MICROSECONDS'::interval
	    WHERE id = NEW.id ;
	    RETURN NEW; 
  	RETURN null;
END; $function$;
```
The `NOW() function` returns same timestamp within the function above. It is the same transaction.

Notice that when updating a row, the current values are copied to a new row, and the new values updates the original row.
Since **between** comparison includes both from and to, it's important that we add one micro-second.
```SQL
create trigger t_tabletest_trigger_1 after
	update of 
  		product_id, product, info1
  	on public.t_tabletest 
  	for each row execute function t_tabletest_update_1();
```
Notice that the column info2 is not part of the trigger. It is just to demonstrate, that you **can have** columns the does **not** generates a history row.

Now let's insert a few new rows:
```SQL
INSERT INTO public.t_tabletest (product, info1, info2) VALUES
	('Beer', 'Like in Germany',null),
	('Wine', 'Like in France', 'Also in Italy')
;
INSERT INTO public.t_tabletest (product, info1, info2) VALUES
	('Carlsberg', 'Like in Copenhagen',null)
;
update t_tabletest
  set info1 = 'Like in Scandinavia'
  where product_id = 3 and active;
```
![image](https://user-images.githubusercontent.com/12120277/172167661-90cc627f-abce-4b5c-98bb-bb8393d7285b.png)
Let's **delete** a row. (In the table security no-one are able to delete.) You only can make a row passive.
```SQL
-- delete a product
update t_tabletest
  set active = false,
  active_to = now()
  where product_id = 1 and active
; 
-- update a product
update t_tabletest
  set product = 'RedWine'
  where product_id = 2 and active; 
```
![image](https://user-images.githubusercontent.com/12120277/172168495-297f0488-9625-4479-9f92-6fc7e9510c02.png)
Now let's see the current rows and the historic point in time at 15:11:
```SQL
select * from t_tabletest
where now() between active_from and active_to
-- alternative: where active
;
select * from t_tabletest
where '2022-06-06 15:11:00'::timestamptz between active_from and active_to
;
```
![image](https://user-images.githubusercontent.com/12120277/172169469-951842ae-a25f-4f93-9337-245279c9ce59.png)
![image](https://user-images.githubusercontent.com/12120277/172169734-06bfb130-d792-4c45-8221-489dbae98cdb.png)
Let's wrap up by creating a view to the users. A view that hides all this history-stuff...  😎
```SQL
drop view if exists tabletest;
create view tabletest AS
  select 
  	product as "Product",
  	info1 as "First information",
  	info2 as "Second information",
  	product_id as "Product Id"
  from t_tabletest
  where active
  order by product;
```  
