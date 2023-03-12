# engram

Experiments in using `pgvector` enabled `supabase` as an embedding store. Here's 
an example of the schema:

I am using the `gtr-t5-large` embedding model to compute embeddings. It does quite
well on the [Huggingface leaderboard](https://huggingface.co/spaces/mteb/leaderboard) 
for the MTEB benchmark for embedding performance.

```sql
create table engrams (
  id serial primary key,
  uri text not null,
  content text not null,
  embedding vector(768)
)
```

I created the `query_engrams` stored procedure to retrieve the `match_count` most 
similar embeddings from the `engrams` table.

```sql
create or replace function query_engrams (
  query_embedding vector(768),
  similarity_threshold float,
  match_count int
)
returns table (
  id bigint,
  content text,
  similarity float
)
language plpgsql
as $$
begin
  return query
  select
    id,
    content,
    1 - (engrams.embedding <=> query_embedding) as similarity
  from engrams
  where 1 - (engrams.embedding <=> query_embedding) > similarity_threshold
  order by engrams.embedding <=> query_embedding
  limit match_count;
end;
$$;
```

