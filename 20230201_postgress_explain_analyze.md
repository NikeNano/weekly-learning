# Explain analyze

[Video](https://www.timescale.com/forum/t/a-beginners-guide-to-explain-analyze/77)
[Slide](https://www.pgmustard.com/timescale-day-slides)
[Glossary](https://www.pgmustard.com/docs/explain)

- Read inside out
- `Seq sca` fast if we want all or table is small
- Planing time vs execution time
	- Dont cover network - large chunks of data takes time to send, especially if network sucks! Filter in database if possible as much as posible
- Observer effect - cache, rerunning makes data move to cache.

Recomended process[slide9](https://www.pgmustard.com/timescale-day-slides): 
1) Realistic dataset = NORTHVOLT prod
	1) nbr of rows especially - effect algo for join, sort
2) Make use of the `EXPLAIN` parameters
	1) [docs](https://www.postgresql.org/docs/current/sql-explain.html)
	2) Turn of `timing` for example
	3) Possible to get JSON and build some bad boys applications! Simon will build small tool in go. 
	4) Less is not more add some stuff!
3) `Start at the end`  AND  `inside out`
4) Focuse on the most expensive parts
	1) Most stats inclusive of children, and per-loop. So inside out sums
	2) In slide example 14, se see a sort on disk, more info [here](https://www.pgmustard.com/docs/explain/sort-method)
5) Now focuse on how to make it faster

[Good blog](https://www.pgmustard.com/blog) about postgres. 
