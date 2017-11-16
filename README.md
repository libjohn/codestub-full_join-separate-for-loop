

### Simplified tidyverse approach with fulljoin (i.e. outer join)

```{r sidea}
side_a <- country_df %>% 
  filter(side_a == 0)

side_b <- country_df %>% 
  filter(side_a == 1)
  
full_join(side_b, side_a, by = "dispute") %>% 
  select(dispute, country.x, country.y)
```