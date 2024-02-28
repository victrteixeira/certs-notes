- Há vários e diferentes tipos de bancos de dados gerenciáveis na AWS. Questões a cerca deles, e como escolhê-los corretamente são comuns:
	- Read-heavy, Write-reavy, or balanced workload
	- Is throughput need or will it change and also need to fluctuate during the day
	- How much data and how long will be stored
	- What's the average object size, and how are they accessed
	- Latency or Concurrent Users requirements
	- Data model
	- RDBMS or NoSQL
	- Data Durability and what's the source of truth for the data

### Database Types
1. RDBMS = SQL / OLTP
   - RDS, Aurora - Good for joins
2. NoSQL Database
   - DynamoDB (~ JSON), ElastiCache (Key/Value pairs), Nepture (Graphs), DocumentDB (for MongoDB), Keyspaces (for Apache Cassandra)
3. Object Store
   - S3 (for big objects) / Glacier (for backups and archives)
4. Data Warehouse = SQL Analytics / BI
   - Redshift (OLAP), Athena, EMR
5. Search
   - OpenSearch (JSON) - free text, unstructured searches
6. Graphs
   - Amazon Nepture - displays relationships between data
7. Ledger
   - Amazon Quantum Ledger Database
8. Time Series
   - Amazon Timestream

### 