### Delete duplicate rows from Oracle tables

The most effective way to detect duplicate rows is to join the table against itself as shown below.
```
select 
   book_unique_id, 
   page_seq_nbr, 
   image_key 
from 
   page_image a 
where 
   rowid > 
     (select min(rowid) from page_image b 
      where 
         b.key1 = a.key1 
      and 
         b.key2 = a.key2 
      and 
         b.key3 = a.key3 
      );
```
