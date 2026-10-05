# Ftssearch

PostgreSQL Full-Text Search engine — lightweight, zero-extra-dependency alternative to Elasticsearch.

Built with TypeScript. Production-ready.

## Features

- Ranked full-text search with PostgreSQL `tsvector`/`ts_rank`
- Weighted column mapping (A/B/C/D matching `ts_rank` weights)
- Automatic GIN index and trigger management
- ILIKE fallback for fuzzy/partial matching
- Supports array columns (tags, etc.)
- Configurable language parser (`simple` for Chinese, `english`, etc.)
- Zero additional processes — runs inside PostgreSQL

## Install

```bash
npm install ftssearch
```

## Usage

```typescript
import { Ftssearch } from 'ftssearch'

const search = new Ftssearch('postgresql://user:pass@localhost:5432/mydb')

// Step 1: Define which columns to index with weights
await search.install({
  table: 'videos',
  columns: [
    { column: 'title',       weight: 'A', isArray: false },
    { column: 'tags',        weight: 'B', isArray: true  },
    { column: 'category',    weight: 'B' },
    { column: 'author',      weight: 'C' },
    { column: 'description', weight: 'D' },
    { column: 'introduction',weight: 'D' },
  ],
  language: 'simple', // 'simple' for Chinese character-level, 'english' for English
})

// Step 2: Search
const result = await search.search('videos', [
  { column: 'title', weight: 'A' },
  { column: 'author', weight: 'C' },
  { column: 'description', weight: 'D' },
], '二次元 动漫', 'simple', {
  limit: 20,
  offset: 0,
})

console.log(result.total)
result.hits.forEach(hit => {
  console.log(hit.rank, hit.source.title)
})

// Step 3: Clean up
await search.close()
```

### With existing pg.Pool

```typescript
import pg from 'pg'
import { Ftssearch } from 'ftssearch'

const pool = new pg.Pool({ connectionString: '...' })
const search = new Ftssearch(pool)
```

## API

### `new Ftssearch(connectionString | Pool)`

### `install(config: IndexConfig): Promise<void>`
Creates `tsv` column, GIN index, and auto-update trigger. Reindexes existing rows.

### `uninstall(table: string): Promise<void>`
Removes trigger, function, index, and `tsv` column.

### `reindex(table, columns, language?): Promise<void>`
Rebuilds `tsv` for all rows.

### `search<T>(table, columns, query, language?, options?): Promise<SearchResult<T>>`
Full-text search with ranked results.

### `close(): Promise<void>`
Closes the connection pool (only if owned).

## License

MIT
