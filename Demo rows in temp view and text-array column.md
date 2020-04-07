
##Create a temporary wiev with data


´´´
	drop view if exists categories;
	create temp view categories as ( 
		  select 1 as id, array['VW','BMW'] 			  as cats  
	union select 2 as id, array['Volvo','Kia'] 			  as cats
	union select 3 as id, array['Kia','VW','Volvo','BMW'] as cats
	union select 4 as id, array['Kia'] 					  as cats
	union select 5 as id, array[]::text[] 				  as cats
	union select 6 as id, NULL 							  as cats
	);
	select * from categories order by id;
	select distinct unnest(cats) as all_cats from categories order by 1; 
	select * from categories where 'BMW' = any(cats) order by 1; -- 1,3
	select * from categories where cats @> array['Kia','Volvo'] = true order by id; -- 2,3
	select *, 'BMW' = any(cats) 		   as result from categories order by id; -- 1,3
	select *, cats && array['Kia','Volvo'] as result from categories order by id; -- 2,3,4
	select *, cats @> array['Kia','Volvo'] as result from categories order by id; -- 2,3
	select *, cats <@ array['Kia','Volvo'] as result from categories order by id; -- 2,4,5
´´´


http://sqlfiddle.com/#!17/f5de26/4
