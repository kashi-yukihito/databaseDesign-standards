# Database/Table Design Standard (PostgreSQL Edition)

## 1. Introduction
A database is a collection of resources that forms the foundation of a business. A well-designed database becomes a superior business model. Therefore, database design is a critical task requiring advanced skills.

A superior database structure is resilient to changes in business rules and enhances software maintainability. Conversely, a poorly designed database guarantees the failure of the entire business.

To respond to the rapid pace of today's business changes, a Data-Oriented Approach (DOA) is essential for logical database design.

This standard aims to provide designers with consistent criteria for building databases, by codifying appropriate naming and design conventions.

## 2. Prerequisites
- The target DBMS is PostgreSQL v17.0 or later.
- This standard assumes a standalone service environment and is not optimized for managed services (Amazon RDS/Aurora, GCP Cloud SQL, Microsoft Azure Database for PostgreSQL, etc.). However, notes regarding differences in managed services are included where relevant.
- PostgreSQL is an RDBMS; designs must leverage its characteristics and constraints.
- This standard does not assume use of the DBMS as a substitute for a file system.
- This standard does not cover systems requiring broad future extensibility (e.g., generic ERP). It follows the YAGNI principle, restricting unnecessary data definitions. However, excluding that aspect, it can be adapted for such systems.
- Data-centricity is the foundational principle of this standard.

## Legend
- Rule Name: ☆☆☆ Rule Content
  Rule description.

The ☆ rating indicates the priority of each rule:
- ☆☆☆: Must. Violations result in review rejection.
- ☆☆: Should. Deviations are allowed with justification.
- ☆: May. Left to the designer's discretion.

---

## 3. Naming Standards

### 3-1. General

- N_GEN001:☆☆☆ Use English for all object names.
Use ASCII characters only. Romaji (Japanese words in Latin script) causes ambiguity due to homophones; use English words instead.

> **Example**
> | Name | Character Set | Language | Allowed |
> |---|---|---|---|
> | 受注 | Japanese | Japanese | NO |
> | Juchu | ASCII | Japanese (Romaji) | NO |
> | order | ASCII | English | YES |

- N_GEN002:☆☆☆ Treat identifiers as case-insensitive.
PostgreSQL normalizes unquoted identifiers to lowercase. Name all objects assuming case insensitivity.

- N_GEN003:☆☆☆ Use snake_case.
Connect multiple words with underscores in lowercase.

> **Example**
> * Order detail: `order_detail`

- N_GEN004:☆☆ Keep object names to 30 bytes or fewer.
To ensure readability on a 40-byte-wide console screen (where the text does not extend beyond the middle of the line) and to accommodate legacy projects (such as Db2 or Informix), we recommend that object names be 18 bytes or less. Names between 19 and 30 bytes may be used without documentation. If a name exceeds 30 bytes, the reason for its use must be documented.

- N_GEN005:☆☆☆ Use abbreviations only when necessary; follow the abbreviation dictionary.
Do not abbreviate if the full name fits within 30 bytes. Maintain consistency within the same database. If the project has its own abbreviation policy, use that instead. Do not mix policies — inconsistent hybrids reduce readability.

> **Bad Example**
> * `cust_cd`: The full name `customer_code` is only 13 bytes. Abbreviating here is unnecessary and violates this rule.

> **Abbreviation Dictionary**
> | Concept | Full Word | Abbreviation | Priority |
> |---|---|---|---|
> | Status | status | sts | ☆☆ |
> | Code | code | cd | ☆☆ |
> | Number | number | no | ☆☆☆ |
> | Work | work | wrk | ☆☆ |
> | History | history | hist | ☆☆ |
> | Temporary | temporary | tmp | ☆☆☆ |
> | Customer | customer | cust | ☆ |
> | Currency | currency | curr | ☆ |
> | Country | country | cntry | ☆ |
> | Expense | expense | exp | ☆ |
> | Experience | experience | xp | ☆☆ |
> | Source | source | src | ☆☆ |
> | Quantity | quantity | qty | ☆☆☆ |
> | Amount | amount | amt | ☆☆ |
> | Message | message | msg | ☆☆ |
> | Organization | organization | org | ☆☆☆ |
> | Abbreviation | abbreviation | abbr | ☆☆ |

### 3-2. Database

- N_DBS001:☆☆☆ Use a unique, meaningful name that clearly identifies the domain.

### 3-3. Tables

- N_TBL001:☆☆ Use concrete noun expressions for table names.
The name must clearly convey the table's content. Use underscores per N_GEN003 when multiple words are needed; the last word should be a noun. Singular or plural forms are both acceptable, but must be consistent within the same database. Plural is slightly preferred (e.g., `orders`).

- N_TBL002:☆☆☆ Do not use SQL keywords as table names.
Both standard SQL and PostgreSQL reserved words are prohibited. Non-reserved keywords may be technically usable but must also be avoided to prevent future conflicts.
> **Reference**
> [SQL Keywords](https://www.postgresql.org/docs/17/sql-keywords-appendix.html)

- N_TBL003:☆☆☆ Do not use prefixes for regular tables.
Prefixes are reserved for views and temporary tables. Regular tables must not have prefixes.

- N_TBL004:☆☆☆ Use the prefix `tmp_` for temporary tables.
All temporary tables must use the `tmp_` prefix. Avoid using names starting with `tmp` for regular tables.

- N_TBL005:☆☆ Use the suffix `_hist` for backup history tables.
Use `_hist` for tables storing historical snapshots of transactions or master data changes.
> **Example**
> * History of orders: `order_hist`
> * History of product pricing: `product_sales_price_hist`

- N_TBL006:☆☆ Use the suffix `_log` for tables whose primary purpose is logging.
> **Example**
> * User operation history: `user_operation_log`

- N_TBL007:☆☆ Use prefix `wrk_` and suffix `_yymmdd` for one-time work tables.
Use `wrk_` for tables created for temporary data investigation or batch updates (when a temp table is insufficient). The `_yymmdd` suffix indicates the expiry date. Tables without a suffix may be deleted at any time.

- N_TBL008:☆☆ Use suffix `_p` + identifier for partitioned child tables.
Append `_p` followed by a range or value identifier to clarify the relationship to the parent (partitioned) table.
> **Example**
> * Range partitioning (monthly): `order_p202401`
> * List partitioning (by region): `customer_p_kanto`
> * Hash partitioning: `log_p0`, `log_p1`, ...
> * Default partition: `tablename_p_default`

### 3-4. Columns

- N_COL001:☆☆☆ Columns with the same meaning must use the same name across all tables.

- N_COL002:☆☆☆ Do not use SQL keywords as column names.
Same rule as N_TBL002.

- N_COL003:☆☆ Use singular forms for numeric data columns (ISO-11179).

- N_COL004:☆☆ Use the suffix `_flag` for boolean flag columns.
Clearly indicate that the column stores a true/false value.

- N_COL005:☆☆ Use the suffix `_sts` for status columns that cannot be expressed as a boolean.

- N_COL006:☆ Use the prefix `sk_` for surrogate key columns.
This makes it explicit that the business domain uses a different key system.

- N_COL007:☆☆☆ Never use `name` as a column name.
Although not reserved in PostgreSQL, `name` is reserved or candidate-reserved in many other RDBMSs. Use `table_name` or a descriptive alternative (e.g., `user_name`) for portability.

### 3-5. Indexes

- N_IDX001:☆☆☆ Name primary key indexes as `pk_` + table name.
- N_IDX002:☆☆☆ Name all other indexes as `idx_` + table name + [free description].

### 3-6. Constraints

- N_CON001:☆ Use system-generated names for constraints by default.
System-generated names are acceptable in most cases. However, explicit naming is recommended in the following situation:

> **When explicit naming is preferred**
> a) Foreign key constraints
> When restoring a table that has foreign key constraints, the constraints may prevent simple INSERT/UPDATE/DELETE operations. Explicit naming aids troubleshooting.
> See N_CON002 for foreign key naming convention.

- N_CON002:☆☆☆ Name foreign key constraints as `fk_` + source table + target table.
The `fk_` prefix followed by source and target table names clearly identifies the relationship. Names may exceed 30 bytes; this is acceptable for foreign keys.

> **Exception**
> a) For self-referencing constraints, use `fk_` + table name + `_self`

- N_CON003:☆☆ Name check constraints as `chk_` + table name + `_` + column name.

> **Exception**
> a) For multi-column check constraints, use `chk_` + table name + `_` + arbitrary identifier
> The identifier must clearly describe the constraint's purpose.
> **Example**
> * Single column: `chk_journals_payment_source_id`
> * Multi-column: `chk_journals_payment_source` (for exclusive checks on `payment_card_id` and `payment_type_code`)

- N_CON004:☆☆ Name unique constraints as `uq_` + table name + `_` + column name.

### 3-7. Rules

- N_RUL001:☆☆☆ Do not create Rules.
Rules rewrite queries internally, making execution plans unpredictable and introducing risk of unintended side effects.
> **Exception**
> a) Allowed only for making views updatable.
> When providing a view as an interface over a normalized table, Rules (e.g., INSTEAD OF) may be used under strict DBA control. However, INSTEAD OF TRIGGERs are preferred as they behave more predictably.

### 3-8. Views

- N_VIW001:☆☆ Use the prefix `vw_` for view names.
The prefix distinguishes views from regular tables and prevents nested view creation.

### 3-9. Triggers

- N_TRG001:☆☆ Do not create Triggers.
Triggers are hidden from developers, creating risk of unintended data manipulation and difficult debugging.
> **Exception**
> a) Allowed only for auditing or supplementary processes that contain no business logic.

## 4. Logical Design Standards

### 4-1. Database Design

- L_DBS001:☆☆☆ Do not modify `template1`.

- L_DBS002:☆☆☆ Use UTF-8 server encoding.
Use UTF-8 unless there is a specific reason not to. Exceptions apply when the connecting system requires a different encoding.

- L_DBS003:☆☆☆ Create application-specific schemas; do not use the `public` schema.
The `public` schema is accessible to all users by default. Place all application objects in a project-specific schema. Do not create any objects in `public`.
Set `search_path` explicitly for the application DB user so objects can be referenced without schema qualifiers.

> **Setup**
> ```sql
> -- Create schema
> CREATE SCHEMA app_schema;
> -- Set search_path permanently for the user
> ALTER USER app_user SET search_path TO app_schema;
> ```

> **Note**
> * Since PostgreSQL 15, CREATE privilege on the `public` schema is revoked from general users by default. This policy is consistent with v17 behavior.
> * `search_path` can be overridden per session; be careful of application-side overrides.

- L_DBS004:☆☆☆ Decide the DB server timezone at project start and keep it consistent.
Do not change the timezone after the system goes live. The application-side TZ setting must match the DB server.

> **Selection Criteria**
> * Domestic systems: `Asia/Tokyo` (JST) is acceptable.
> * International systems: UTC is the standard.
> * RDS defaults to UTC. To use JST, explicitly configure the parameter group.

### 4-2. Table Design

- L_TBL001:☆☆☆ Design using DOA (Data-Oriented Approach).
DOA focuses on the fact that data changes less frequently than business processes. Define tables based on normalized business facts, not on UI layouts or report formats.

- L_TBL002:☆☆ One table, one purpose (One Fact in One Place).
Redundant table structures lead to excessive NULLs and poor readability. When complex joins are unavoidable, consider using views rather than merging tables. (See N_TBL001)

- L_TBL003:☆☆☆ Separate entities from events.
Clearly distinguish between resource tables (masters: customers, products, organizations) and event tables (transactions: orders, shipments, payments).

- L_TBL004:☆☆ Do not store calculated data in tables.
Values derivable from other columns (e.g., total amount, tax) must not be stored. Calculate them in views or the application layer.
If stored for performance reasons (batch aggregation, OLAP), document the redundancy explicitly and implement safeguards: recalculation via batch process and a recorded calculation timestamp for data snapshot clarity.

- L_TBL005:☆☆☆ Every table must have a primary key.
All tables must satisfy first normal form.

> **Exception**
> a) Tables guaranteed to have exactly one row.
> Uniqueness is inherently guaranteed, but setting a primary key is still recommended to avoid confusion.
> b) Work tables for externally sourced data.
> For tables with frequent full DELETE/INSERT cycles, omitting the primary key may be more efficient. However, if no DELETE occurs, apply a surrogate key with auto-increment.

- L_TBL006:☆ Limit columns per table to 25.
Use 25 as a reference threshold for reviewing table structure during initial design. This number has no strict logical basis.

- L_TBL007:☆☆ Define a data lifecycle for every table.
At the time of definition, clarify how data will be retained, purged, or archived, and under what criteria.

- L_TBL008:☆☆☆ Do not leave expired tables in place.
Tables created for migration or temporary backup must have their lifecycle documented. Tables without a documented lifecycle are considered unnecessary.
Conduct periodic audits to detect and remove forgotten tables. (See N_TBL007)

- L_TBL009:☆ Do not create unnecessary tables.
Apply YAGNI. Even when future extensibility is a concern, do not implement tables unless there is a reasonable and immediate need.

- L_TBL010:☆☆ Consider UNLOGGED tables for temporary bulk data processing.
UNLOGGED tables bypass WAL, offering 2–6x performance improvement for INSERT/UPDATE. Useful for test data preparation or bulk loading.

> **Exception (prohibited use)**
> a) Tables requiring persistence (data is lost on crash).
> b) Tables targeted for PITR (see P_CNF001).
> c) Tables targeted for replication (always empty on standby).

> **Note**
> UNLOGGED cannot be changed via ALTER TABLE; DROP and re-CREATE is required.
> Since PostgreSQL 15, conversion from UNLOGGED to LOGGED is possible via `ALTER TABLE ... SET LOGGED`.

### 4-3. Primary Key Design

- L_PKY001:☆☆ Use natural keys for core domain data.
Natural keys enforce data identity at the design level. Use them as the default for domain-representative data.

- L_PKY002:☆☆ Use composite primary keys for child tables in parent-child relationships.
Entities that cannot exist without a parent (e.g., order line: `order_id + line_no`) must use composite primary keys.

- L_PKY003:☆ Use surrogate keys when no natural key exists in the business domain.
Use surrogate keys for logs, append-only data, or any data whose uniqueness is not defined by the business domain.
Surrogate keys may also be used for core domain data when identifiers are likely to change due to business expansion, regulatory changes (e.g., ISBN), or M&A consolidation.

- L_PKY004:☆☆ Primary keys should express the meaning of the data.
A primary key is not merely a technical identifier — it should convey the identity of the data it represents. Even when a surrogate key is adopted, business-level uniqueness must always be enforced via a unique constraint. (See L_CON001)


### 4-4. Data Type Standards

- L_TYP001:☆☆☆ Use NUMERIC/DECIMAL for decimal values.
Floating-point types introduce rounding errors unacceptable for monetary or quantity data. Do not mix NUMERIC and DECIMAL in the same DDL.

> **Exception**
> Use `double precision` or `float` when computation speed is critical and rounding errors are acceptable.

- L_TYP002:☆☆ Default NUMERIC precision is (11,2); maximum is (20,4).
The default (11,2) supports values up to 999,999,999.99, sufficient for most quantities. Use larger precision only when required. Values beyond (20,4) are not prohibited but must be justified.

> **Note**
> `double precision` / `real` may also be considered when precision exceeds (20,4).

- L_TYP003:☆☆☆ Do not use the MONEY type.
MONEY is locale-dependent, limiting its use in internationalized systems. Use BIGINT for integer-only monetary values (L_TYP004) or NUMERIC for decimals (L_TYP001).

- L_TYP004:☆☆☆ Use INTEGER or BIGINT for whole numbers.
Do not use SMALLINT without specific justification. INTEGER provides sufficient precision and good performance for general use. Always use BIGINT for primary keys to avoid future key exhaustion.

- L_TYP005:☆☆ Use VARCHAR(n) for string columns.
Always specify a length `n`. For very long text where indexing is not required, use TEXT explicitly (see L_TYP007).

- L_TYP006:☆☆ Use CHAR for fixed-length strings.
CHAR is appropriate for structured codes with fixed length (e.g., phone numbers, ISBN). Note that CHAR pads values with trailing spaces, which may cause unexpected behavior in application-side comparisons.

- L_TYP007:☆☆ Use TEXT for long-form content.
Use TEXT when the column is expected to store 1,000 or more characters, or when individual indexing is not required.

- L_TYP008:☆☆☆ Use BOOLEAN for `_flag` columns.
Columns with the `_flag` suffix must store only true/false values. Always use the BOOLEAN type.

- L_TYP009:☆☆ Use VARCHAR for `_sts` (status) columns.
Status columns that cannot be expressed as a boolean must use VARCHAR, even if the values are numeric strings.

- L_TYP010:☆☆☆ Use the INET type for network addresses.
INET is preferred over CIDR for its broader applicability. CIDR is stricter about notation but less versatile.

- L_TYP011:☆☆☆ Use JSONB for JSON data.
JSONB supports indexing and avoids re-parsing on read, outweighing its INSERT overhead. Always use JSONB over JSON.

> **Exception (prohibited use)**
> a) As a substitute for normalizable data.
> b) For columns used frequently in search or join conditions.
> c) For primary business attributes (primary keys, foreign keys, status fields, etc.).

- L_TYP012:☆☆ Do not use array types.
Arrays within a relational table are counterintuitive and make it difficult to treat each element as an entity. Use a separate table or JSONB (per L_TYP011) instead.

- L_TYP013:☆☆☆ Use GENERATED AS IDENTITY for auto-increment columns.
Do not use `serial` or `bigserial`. Use SQL-standard `GENERATED AS IDENTITY` to prevent type mismatches between sequences and columns, and to clarify object ownership.

> **Exception / Note**
> - **Migration exception**: When inserting data with existing key values during migration, temporarily use `GENERATED BY DEFAULT AS IDENTITY`.
> - **Post-migration**: Promptly change to `ALTER TABLE ... SET GENERATED ALWAYS` and sync the sequence:
> `SELECT setval(pg_get_serial_sequence('table_name', 'column_name'), coalesce(max(column_name), 1))`

### 4-5. Data Design Standards

> **Note**
> This section covers items that are physical in nature but treated as logical design due to their role in column definition.

- L_DAT001:☆☆ Follow ISO standards for internationally defined data values.
Use ISO-compliant code systems for country codes (ISO-3166), language codes (ISO-639), and currency codes (ISO-4217) unless a project-specific definition exists.

- L_DAT002:☆☆ Include standard audit columns in all persistent tables.
Add the following columns to ensure row-level traceability.

> **Columns**
> * `created_at`: Timestamp of INSERT. Type: `TIMESTAMPTZ NOT NULL DEFAULT now()`
> * `created_by`: ID of the user/system that performed the INSERT. Type: `VARCHAR(36)` *1
> * `updated_at`: Timestamp of last UPDATE (same as `created_at` on INSERT). Type: `TIMESTAMPTZ NOT NULL DEFAULT now()` *2
> * `updated_by`: ID of the user/system that performed the UPDATE. Type: `VARCHAR(36)` *1
> * *1) `VARCHAR(36)` is used to accommodate hyphenated UUIDs.
> * *2) `updated_at` must be set by the application layer. The DEFAULT is only for INSERT. Ensure the application uses the DB server clock to avoid TZ discrepancies.

> **Exception**
> * a) Temporary tables and work tables not intended for persistence may omit these columns.

- L_DAT003:☆☆ Add a `version` column to support optimistic locking.
Whether to use optimistic locking is an application-layer decision. Add the column as a standard preparation. The application increments the value by 1 on each update.

```sql
version BIGINT NOT NULL DEFAULT 0
```

> **Exception**
> * a) Temporary tables and work tables not intended for persistence may omit this column.

- L_DAT004:☆☆☆ Use soft delete only when required.
Soft delete pollutes queries by requiring constant exclusion of deleted rows. Do not add soft delete columns by default.
Use soft delete only when required by audit regulations or when frequent queries against deleted data are expected.

> **Columns**
> * `deleted_at`: Deletion timestamp. NULL = active, non-NULL = deleted.
>   Type: `TIMESTAMPTZ NULL DEFAULT NULL`
> * `deleted_by`: ID of the user/system that performed the deletion. Type: `VARCHAR(36)` (see L_DAT002 *1)

### 4-6. Constraint Design

- L_CON001:☆☆☆ Add a unique constraint on natural key columns when a surrogate key is used.
When a surrogate key is the primary key, the domain value (the actual business key) must still be enforced as unique.

- L_CON002:☆☆☆ Add check constraints for status values.
All columns matching L_TYP009 or any column accepting only specific values must have a check constraint.

- L_CON003:☆☆☆ Declare foreign key constraints explicitly using CONSTRAINT.
Do not use inline `REFERENCES`. Use the explicit `CONSTRAINT fk_... FOREIGN KEY (...)` form. This assigns a meaningful name to the constraint and aids operational troubleshooting.

### 4-7. View Design

- L_VIW001:☆☆☆ Do not create views based on other views.
Deeply nested view structures cause unpredictable behavior and make changes difficult. All views must be built directly from base tables. The `vw_` prefix exists to enforce this.

- L_VIW002:☆☆ Consider materialized views for frequently referenced aggregation views.
When an aggregation view is queried very frequently, consider converting it to a materialized view.
Not currently used in this standard's target scope. Define criteria when adapting for production use.

### 4-8. Trigger Design

- L_TRG001:☆☆ Do not create Triggers.
See N_TRG001 for rationale.

> **Exception**
> a) Allowed only for auditing or supplementary processes containing no business logic.

## 5. Physical Design Standards

### 5-1. postgresql.conf Settings

A reference configuration file `postgresql.conf.basic` is provided separately. This section covers settings with significant developer impact or those likely to require project-specific tuning.

> **Note**
> In `postgresql.conf`, a commented-out line such as `#autovacuum = on` is still active as the default value. Always uncomment the line explicitly when changing a setting.

- P_CNF001:☆☆☆ Enable WAL.
Enable WAL to support PITR (Point-in-Time Recovery). Use the default WAL level `replica` unless there is a specific reason to change it.

- P_CNF002:☆☆☆ Configure `log_min_duration_statement`.
Set this parameter to enable slow query detection during development and production monitoring. Queries exceeding the threshold are logged for analysis.
Default production environment setting: 30 seconds (30000 ms). This value should be adjusted based on the actual performance of the system.
`log_min_duration_statement = 30000`

> **Note**
> * The default value for `log_min_duration_statement` is 0 (log all queries). Setting it to 30000 (30 seconds) enables logging only for queries that take longer than 30 seconds.
> * This setting is particularly important for identifying performance bottlenecks in development and production.

- P_CNF003:☆☆ Use CSV as the standard log format.
CSV logs are easy to inspect visually and with spreadsheet tools. JSON format is acceptable if a dedicated log analysis platform (e.g., Elasticsearch) is in use. Always enable `logging_collector` regardless of format.

- P_CNF004:☆☆ Keep `work_mem` at its default; set dynamically per session when needed.
`work_mem` controls the memory available for sort and hash operations before spilling to disk. Default is 4MB (v17). Raising it globally risks consuming up to `max_connections × work_mem` of memory. Keep the default in `postgresql.conf` and set it dynamically only for sessions requiring large sorts.

> **Example**
> ```sql
> -- Temporarily increase for the current session
> SET work_mem TO '64MB';
> -- Execute query with large sort
> SELECT ... ORDER BY ...;
> -- Reset at end of session or explicitly
> RESET work_mem;
> ```

> **Note**
> * If large sorts occur consistently, consider raising the default in `postgresql.conf`. Especially effective for batch and OLAP workloads.
> * `work_mem` is allocated per sort/hash node in the execution plan, not per query. Total memory usage may exceed the configured value.

### 5-2. Index Design Standards

- P_IDX001:☆☆☆ Use B-Tree indexes by default.
B-Tree is sufficient for most use cases. Use GIN for JSONB columns. GiST may be considered for complex geometric data, but array types are prohibited per L_TYP012.

- P_IDX002:☆☆☆ Add a unique index on domain values when a surrogate key is used.
Same requirement as L_CON001.

- P_IDX003:☆☆ Document the intent of every non-unique index.
Record why each index was created and what query pattern it supports. This enables informed decisions when the data structure or access patterns change.

- P_IDX004:☆☆ Consider partial indexes for columns with frequent condition-based searches.
A `CREATE INDEX ... WHERE` clause limits the index to matching rows, reducing index size and improving planner accuracy.

> **Example**
> ```sql
> CREATE INDEX idx_orders_active ON orders (customer_id)
> WHERE deleted_at IS NULL;
> ```

> **Note**
> Especially effective when soft delete (L_DAT004) is adopted.
> Always document the intent per P_IDX003 when creating a partial index.

- P_IDX005:☆☆ Consider column order carefully when creating composite indexes.
Use composite indexes for queries combining multiple conditions or requiring specific sort order (ORDER BY). Follow these principles for column ordering:

> **Principles**
> a) Place equality columns (`=`) first; prioritize higher cardinality.
> b) Place range columns (`BETWEEN`, `>`, `<`, `LIKE` prefix, etc.) last.
> c) Place ORDER BY columns after filter columns.

### 5-3. Vacuum Parameter Standards

- P_VCM001:☆☆ Use default autovacuum scale factors as a baseline.
The defaults (vacuum: 20%, analyze: 10%) are sufficient for most workloads. For tables with tens of thousands of updates per day, lower the scale factors to prevent query performance degradation. Review settings during periodic audits.

- P_VCM002:☆☆☆ Set `fillfactor` for frequently updated tables.
INSERT-only tables (e.g., logs) can use the default (100). For tables with frequent UPDATEs, set `fillfactor` to 70–90 to enable HOT (Heap Only Tuple) updates and reduce VACUUM cost.

> **Note**
> Review `fillfactor` values based on bloat findings during periodic audits (see Section 7-1).
> On RDS, set via `ALTER TABLE` rather than the parameter group.
> ```sql
> ALTER TABLE orders SET (fillfactor = 80);
> ```

### 5-4. Storage Sizing Standards

- P_SIZ001:☆☆☆ Always include index storage in size estimates.
Indexes can be equal to or larger than the base table. Never estimate table size alone. Note that index size varies significantly by column count, composite key structure, and duplication rate — do not assume a simple ratio.

- P_SIZ002:☆☆☆ Account for 8KB page granularity in storage estimates.
PostgreSQL manages data in 8KB pages. Even small datasets consume storage in 8KB increments. The default file size limit is 1GB; files are split beyond that. Include TOAST storage when rows may exceed 8KB.

- P_SIZ003:☆☆☆ Include MVCC bloat in storage estimates.
Dead tuples from updates and deletes remain until VACUUMed. Given the default autovacuum threshold of 20%, use at least 1.2× the calculated size as the minimum estimate. For high-update tables, consider 1.5× or more.

- P_SIZ004:☆☆ Account for `fillfactor` overhead in storage estimates.
Tables and indexes configured with `fillfactor` reserve empty space per the configured value. Even without explicit configuration, the default index `fillfactor` is 90, meaning indexes will always be larger than the raw data. (See P_VCM002)

- P_SIZ005:☆☆ Size the WAL storage area (`pg_wal`) separately.
WAL storage must be estimated independently from data storage. In v17, WAL is managed as a soft limit via `max_wal_size` (default: 1GB); there is no fixed formula — actual usage depends on write frequency. Account for peak spikes from large transactions.

> **Note**
> Plan for 2–3× `max_wal_size` as a buffer, considering:
> a) WAL accumulation from archive failures and retries.
> b) WAL retained for standby servers via `wal_keep_size`.
> c) Temporary WAL spikes from bulk INSERT/COPY operations.
>
> Avoid setting hard quotas on `pg_wal`. A quota breach causes a PANIC and service outage. (See P_CNF001)

## 6. SQL Writing Standards

### 6-1. SQL Style

- S_001:☆☆☆ Do not use column numbers in ORDER BY clauses.
`ORDER BY 2, 1` is concise but was flagged for removal in SQL92 and reduces maintainability when columns are added or reordered. Do not use in embedded or persistent code. Allowed in one-off psql console queries.

- S_002:☆☆☆ Do not use column numbers in GROUP BY clauses.
Same rationale as S_001.

- S_003:☆☆ Write SQL keywords in uppercase; write table and column names in lowercase.

- S_004:☆☆ Place the data value on the left side of comparison operators.

- S_005:☆☆☆ Always specify column names explicitly in INSERT statements.
Do not rely on column order. Always list target column names to prevent silent failures when the table structure changes.

- S_006:☆☆☆ Window functions are permitted.
Window functions are approved for OLAP use. They are not prohibited for OLTP use, but separate coding guidelines should be established if used in that context.

- S_007:☆☆☆ Use the JOIN clause for joins.
Do not join tables in the WHERE clause. Explicitly separating join conditions from filter conditions improves readability. Furthermore, joining in the WHERE clause (e.g., the old Oracle syntax) can lead to accidental Cartesian products (CROSS JOINs) if a single join condition is omitted, causing performance degradation and bugs.

- S_008:☆ Prefer EXISTS over IN for existence checks.
While the current PostgreSQL optimizer rewrites IN to EXISTS, making performance differences negligible, IN implies "matches any value in the list" while EXISTS implies "the subquery result exists." Using EXISTS for existence checks improves clarity and readability.

- S_009:☆☆☆ Do not rely on result order.
Tables and query results are unordered sets. Do not write code that depends on the order of rows unless an ORDER BY clause is explicitly specified. Similarly, when using LIMIT or OFFSET, always accompany them with ORDER BY to ensure deterministic results.
Row ordering may change silently after VACUUM, plan changes, or version upgrades, making order-dependent bugs difficult to reproduce.

## 7. Operations Design Standards

### 7-1. Periodic Audits

The DBA must conduct audits at intervals defined per database. Set KPIs based on the SLA/SLO of each application, incorporating the following monitoring areas.

#### Monitoring and Evaluation Items:
* **Resource utilization**: Trends in CPU, memory, I/O, and disk usage.
* **Query performance**: Detection and resolution of chronic or sudden slow queries.
* **Index optimization**: Removal of unused indexes and addition of missing indexes. (See O_IDX003)
* **Storage fragmentation (Bloat)**: Assessment of the need for table/index rebuilding (REINDEX / VACUUM FULL).
* **Backup verification**: Regular recovery tests to confirm restorability.
* **Security logs**: Review for suspicious login attempts, operations by privileged users (e.g., `postgres`), and unauthorized bulk data exports (e.g., dumps).

### 7-2. Vacuum Strategy

- O_VCM001:☆☆☆ Always use autovacuum.
Properly configured autovacuum is more stable than manual execution. Disabling autovacuum is not an option.
After large-scale bulk DELETEs or bulk INSERTs, manually running `VACUUM ANALYZE` is recommended to immediately refresh statistics.

- O_VCM002:☆☆☆ Schedule VACUUM FULL based on audit findings.
VACUUM FULL causes significant I/O and locks many objects. Avoid it where possible. When audit results indicate substantial bloat reduction benefit, plan and execute within a maintenance window.

- O_VCM003:☆☆ Set per-table autovacuum parameters for high-volume update tables.
Changing scale factors in `postgresql.conf` affects all tables globally. For targeted tuning, use `ALTER TABLE` to set per-table parameters.

> **Example**
> ```sql
> ALTER TABLE orders SET (
>   autovacuum_vacuum_scale_factor = 0.01,
>   autovacuum_analyze_scale_factor = 0.005
> );
> ```

> **Note**
> Review settings during periodic audits (see Section 7-1).
> Per-table settings via `ALTER TABLE` are also effective on RDS.

### 7-3. Index Creation Timing and Method

- O_IDX001:☆☆☆ Always use the CONCURRENTLY option when adding indexes to live systems.
Index creation acquires an exclusive lock on the target table, which can cause service disruption on live systems. Unless a full maintenance window is available, always use the `CONCURRENTLY` option during off-peak hours to create indexes without locking.

- O_IDX002:☆☆☆ Use REINDEX CONCURRENTLY for periodic index rebuilding.
When index bloat is confirmed during audits, use `REINDEX CONCURRENTLY` to avoid locking.
`REINDEX CONCURRENTLY` cannot run inside a transaction; verify the result after execution as failures may leave an invalid index behind.
Execute during off-peak hours to minimize I/O and WAL impact. (See O_IDX001)

> ```sql
> REINDEX INDEX CONCURRENTLY idx_orders_customer_id;
> -- Rebuild all indexes on a table
> REINDEX TABLE CONCURRENTLY orders;
> ```

> **Note**
> `REINDEX CONCURRENTLY` is available from PostgreSQL 12.
> Must be executed outside a transaction.

- O_IDX003:☆☆☆ Detect and remove unused indexes during periodic audits.
Every index adds overhead to INSERT/UPDATE/DELETE operations. Unused indexes contribute to bloat and degrade write performance.
During periodic audits (see Section 7-1), identify low-usage indexes using the query below, cross-reference against the intent documented in P_IDX003, and remove those confirmed as unnecessary.
Note that low scan frequency alone is not sufficient justification for removal — some indexes (e.g., those supporting monthly batch queries) are critical despite infrequent use.

> **Detection Query**
> ```sql
> SELECT
>     psui.indexrelid,
>     psui.schemaname,
>     psui.relname,
>     psui.indexrelname,
>     psui.idx_scan,
>     (psut.seq_scan + psut.idx_scan) AS total_scan,
>     round(
>         COALESCE(psui.idx_scan, 0)
>         / NULLIF((psut.seq_scan + psut.idx_scan), 0)::numeric * 100, 2
>     ) AS idx_scan_ratio,
>     pg_size_pretty(pg_relation_size(psui.indexrelid)) AS index_size
> FROM pg_stat_user_indexes AS psui
> INNER JOIN pg_stat_user_tables AS psut
>     ON psui.relid = psut.relid
> WHERE psut.seq_scan + psut.idx_scan > 0
> ORDER BY idx_scan_ratio;
> ```

> **Note**
> * A very low `idx_scan_ratio` indicates a deletion candidate, but evaluate in context of other indexes on the same table.
> * A small `index_size` means deletion has limited impact; use it as a tiebreaker, not the primary criterion.
> * `pg_stat_*` values are cumulative since the last server start. Treat values collected immediately after a restart or statistics reset as reference only.
> * Base audit decisions on statistics collected after at least one week of normal operation.

## 8. Security Standards

### Zero Trust Architecture

This standard's security rules are based on the Zero Trust principle: *"Verify every access continuously, regardless of network location or origin."*

### 8-1. Authentication and Access Control

- S_SEC001:☆☆☆ Apply the Principle of Least Privilege.
Do not grant superuser privileges to application DB users. Grant only the minimum required permissions: SELECT, INSERT, UPDATE, and DELETE on the necessary tables.

- S_SEC002:☆☆☆ Separate developer access from production environments.
Direct access to production data must be restricted to DBAs only. When developers require access for investigation, provide a masked copy of the data or issue a time-limited account on request.
Not currently in scope for this standard's target systems. Define procedures when adapting for production use.

- S_SEC003:☆ Consider Row Level Security (RLS) when needed.
PostgreSQL's RLS feature allows row-level access control at the database level.
Not currently used in this standard's target scope. Evaluate when multi-tenant architecture or per-user data isolation is required.

### 8-2. Data Protection

- S_SEC004:☆☆☆ Encrypt sensitive data.
Passwords must be salted and hashed at the application layer (e.g., Argon2, bcrypt) before storage. Never store passwords in plain text.

- S_SEC005:☆☆ Mark PII columns using COMMENT ON COLUMN.
Columns containing personally identifiable information must be explicitly marked to support data governance and audit requirements.

> **Example**
> ```sql
> COMMENT ON COLUMN users.email IS 'PII: Email address';
> ```

- S_SEC006:☆☆☆ Do not handle personal data in development environments.
PII-marked columns must not be accessible to developers with production data. If loading production data into a development environment is necessary, apply masking or randomization to prevent individual identification.

> **Exception**
> a) Systems that contain no personal data are exempt.
> b) When the developer is the sole user and the personal data belongs only to themselves, the DBA may allow it with documented acknowledgment of the risk.

### 8-3. SQL Injection Prevention

- S_SEC007:☆☆☆ Always use prepared statements with bind variables.
Dynamic query generation is prohibited. Always use placeholders (bind variables) to prevent SQL injection. This rule is shared with the application coding standards.
