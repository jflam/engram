# engram

Experiments in using `pgvector` enabled `supabase` as an embedding store. Here's 
an example of the schema:

I am using the `gtr-t5-large` embedding model to compute embeddings. It does quite
well on the [Huggingface leaderboard](https://huggingface.co/spaces/mteb/leaderboard) 
for the MTEB benchmark for embedding performance.

```sql
create table posts (
  id serial primary key,
  title text not null,
  body text not null,
  embedding vector(768)
)
```

