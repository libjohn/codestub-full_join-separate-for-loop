

### Simplified tidyverse approach with full_join (i.e. outer join)

```{r sidea}
side_a <- country_df %>% 
  filter(side_a == 0)

side_b <- country_df %>% 
  filter(side_a == 1)
  
full_join(side_b, side_a, by = "dispute") %>% 
  select(dispute, country.x, country.y)
```

## Details:

Part of a project Mara brought to Ryan and me.  Goal is to transform 


Country |	Dispute	| Side A	| Side B
--- | --- | --- | ---
A	| 1	| 0 | 1
B |	1 |	0 |	1
C |	1 |	1 |	0
D |	1 |	1 |	0
A |	2 |	0 |	1
E |	2 |	1 |	0
F |	2 |	1 |	0

to 


Country.x	Country.y	Dispute
A	C	1
B	C	1
A	D	1
B	D	1
A	E	2
A	F	2