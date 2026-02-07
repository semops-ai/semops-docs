[https://www.getdbt.com/blog/modular-data-modeling-techniques](https://www.getdbt.com/blog/modular-data-modeling-techniques)

So when I think about data modeling techniques, I don’t really think in terms of style ([Kimball](https://docs.getdbt.com/terms/dimensional-modeling), Data Vault, star schema, etc), although plenty of people find those techniques to be useful.

Instead, I generally follow our internal modeling conventions at dbt Labs, which focuses on finding the shortest path between raw source data and the data products that would actually solve a problem for them.

