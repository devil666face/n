- https://community.n8n.io/t/n8n-with-sqlite-postgresql-or-supabase-three-installation-options-compared/118201
- https://github.com/coleam00/local-ai-packaged/blob/main/start_services.py#L57
- https://github.com/eleiton/ollama-intel-arc/blob/main/docker-compose.yml
- https://github.com/mattcurf/ollama-intel-gpu
- https://github.com/NikolasEnt/ollama-webui-intel
- https://github.com/supabase/supabase/blob/master/docker/docker-compose.yml

```ini
postgres: postgres://n8n:Qwerty123@postgres:5432/n8n
supabase: http://kong:8000
supabase-store: postgres://postgres:Qwerty123@store:5432/superbase
```

### [Supabase table for docs](https://supabase.com/docs/guides/ai/langchain?queryGroups=database-method&database-method=sql)

```sql
-- Enable the pgvector extension to work with embedding vectors
create extension vector;

-- Create a table to store your documents
create table documents (
  id bigserial primary key,
  content text, -- corresponds to Document.pageContent
  metadata jsonb, -- corresponds to Document.metadata
  embedding vector(1536) -- 1536 works for OpenAI embeddings, change if needed
);

-- Create a function to search for documents
create function match_documents (
  query_embedding vector(1536),
  match_count int default null,
  filter jsonb DEFAULT '{}'
) returns table (
  id bigint,
  content text,
  metadata jsonb,
  similarity float
)
language plpgsql
as $$
#variable_conflict use_column
begin
  return query
  select
    id,
    content,
    metadata,
    1 - (documents.embedding <=> query_embedding) as similarity
  from documents
  where metadata @> filter
  order by documents.embedding <=> query_embedding
  limit match_count;
end;
$$;
```
