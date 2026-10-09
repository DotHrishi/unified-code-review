# Database Review Principles

When reviewing database-related code, schema designs, and SQL queries, focus on structural integrity, performance, and best practices. Assume connections and infrastructure are handled via cloud hosting, so do NOT review connection strings, pool sizes, or network-level configurations.

## Scope of Review

- Schema Design (Tables, columns, types)
- Keys (Primary keys, foreign keys, constraints)
- Indexing (Missing indexes, over-indexing)
- Query Structure (Joins, subqueries, N+1 issues)
- Best Practices (Normalization, data types, naming conventions)

## 1. Schema Design
- Ensure appropriate data types are used (e.g., using `INT` vs `BIGINT`, `VARCHAR` vs `TEXT`).
- Check for proper normalization (at least 3NF) unless denormalization is explicitly justified for performance.
- Avoid wide tables with too many nullable columns (consider extracting to a related table).

## 2. Keys & Constraints
- Every table MUST have a well-defined Primary Key.
- Foreign Keys should be explicitly defined to enforce referential integrity.
- Use `UNIQUE` constraints where duplicate values are not permitted logically.
- Avoid using floating-point types for primary keys or exact matching.

## 3. Query Best Practices
- Avoid `SELECT *`; explicitly select only the necessary columns.
- Ensure efficient use of `JOIN`s; be wary of implicit cross joins or Cartesian products.
- Identify and warn about potential N+1 query problems in ORM usage.
- Review for SQL injection vulnerabilities (ensure parameterized queries/prepared statements are used).

## 4. Indexing Strategy
- Check for indexes on columns frequently used in `WHERE`, `JOIN`, `ORDER BY`, and `GROUP BY` clauses.
- Warn against redundant or overlapping indexes.
- Remember that too many indexes can degrade `INSERT`/`UPDATE` performance.

## 5. Exclusions
- Do NOT review database connection pooling, connection strings, timeouts, or infrastructure-level configurations.
