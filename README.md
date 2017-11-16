

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


Country.x |	Country.y |	Dispute
--- | --- | ---
A |	C |	1
B |	C |	1
A |	D |	1
B |	D |	1
A |	E |	2
A |	F |	2

First Example took me down an long (and incorrect) road because my logic branched into the gutter.  I found myself using a for loop with also requried the use of `separate` to transform multi-valed cells into rows.  

While that was an interesting approach.  Ryan's outer join approach was **MUCH** more elegant and consice.  In tidyverse, outer join is `full_join()`.  Once back on track, it became three simple setps.

## The For Loop and the `separate function`

Well, I got tripped up on this originally.  My [first script](https://github.com/libjohn/codestub-full_join-separate-for-loop/blob/master/transform_and_merge_01.Rmd) goes round and round in a big old for-loop circle.  But all was not for naught since I forced myself to learn some techniques which may come in handy later.

Below are the highlights using for and separate

```{r}
# declare Tibble (data frame)
df_total = tibble()

for (i in 1:max(country_df$dispute)) {
  # Build vector of disputants (multi-valued cell: country_y) 
  country_y <- country_df %>% 
    filter(dispute == i) %>% 
    select(-dispute) %>% 
    mutate(side_b = if_else(side_b == 0, country, NA_character_)) %>% 
    select(side_b) %>% 
    drop_na() %>% 
    unique() %>% 
    as_vector() %>% 
    paste0(collapse = "|") 
  
  # Build a data frame that adds the disputants vector as a column-variable; 
  # drops blank rows ; separate the multi-valued cells (disputants/country_y)
  # into rows
  country_df_temp <- country_df %>% 
    filter(dispute == i) %>% 
    mutate(country_disputes = if_else(side_b == 1, country_y, NA_character_)) %>% 
    drop_na() %>% 
    separate_rows(country_disputes, convert = TRUE) 
  
  # Append big Dataframe from from the iterated dataframes
  df_total <- bind_rows(df_total, country_df_temp)
  
}

df_total %>%
  arrange(country_disputes) %>% 
  rename(country_x = country,
         counrty_y = country_disputes) %>% 
  select(dispute, counrty_y, country_x)
```