# PostgreSQL Database Exports

This folder contains PostgreSQL-compatible SQL dumps for the Countries States Cities database.

## 📦 Files Available

| File | Size | Description |
|------|------|-------------|
| `world.sql.gz` | ~22 MB | Complete database (all tables compressed) |
| `cities.sql.gz` | ~21 MB | Cities table only (compressed) |
| `states.sql` | ~3.5 MB | States/provinces table |
| `countries.sql` | ~265 KB | Countries table |
| `subregions.sql` | ~14 KB | Subregions table |
| `regions.sql` | ~5 KB | Regions table |
| `schema.sql` | ~10 KB | Database schema only |

**Total folder size:** ~46 MB

## 🚀 Quick Download (Without Full Repo Clone)

### Method 1: Using SVN (Recommended)
```bash
# Install SVN if needed: apt-get install subversion
svn export https://github.com/dr5hn/countries-states-cities-database/trunk/psql
```

### Method 2: Using degit
```bash
# Install: npm install -g degit
degit dr5hn/countries-states-cities-database/psql
```

### Method 3: Using GitHub CLI
```bash
# Install: https://cli.github.com/
gh repo clone dr5hn/countries-states-cities-database
cd countries-states-cities-database/psql
# Then delete parent repo if only psql folder is needed
```

### Method 4: Direct File Download
Download individual files directly from GitHub:
```bash
# Complete database
wget https://raw.githubusercontent.com/dr5hn/countries-states-cities-database/master/psql/world.sql.gz

# Or individual tables
wget https://raw.githubusercontent.com/dr5hn/countries-states-cities-database/master/psql/cities.sql.gz
wget https://raw.githubusercontent.com/dr5hn/countries-states-cities-database/master/psql/states.sql
wget https://raw.githubusercontent.com/dr5hn/countries-states-cities-database/master/psql/countries.sql
```

### Method 5: Using curl
```bash
curl -o world.sql.gz https://raw.githubusercontent.com/dr5hn/countries-states-cities-database/master/psql/world.sql.gz
```

## 📥 Import to PostgreSQL

### Step 1: Extract Compressed Files
```bash
# Extract the complete database
gunzip world.sql.gz

# Or extract cities only
gunzip cities.sql.gz
```

### Step 2: Create Database
```bash
# Using psql command line
psql -U postgres -c "CREATE DATABASE world;"

# Or using createdb utility
createdb -U postgres world
```

### Step 3: Import Data

#### Import Complete Database
```bash
psql -U postgres -d world -f world.sql
```

#### Import Individual Tables
```bash
# First import schema
psql -U postgres -d world -f schema.sql

# Then import tables in order (respecting foreign keys)
psql -U postgres -d world -f regions.sql
psql -U postgres -d world -f subregions.sql
psql -U postgres -d world -f countries.sql
psql -U postgres -d world -f states.sql

# Extract and import cities (large file)
gunzip cities.sql.gz
psql -U postgres -d world -f cities.sql
```

#### Import with Custom Options
```bash
# With verbose output
psql -U postgres -d world -f world.sql -v ON_ERROR_STOP=1

# With specific host and port
psql -h localhost -p 5432 -U postgres -d world -f world.sql

# Show progress for large files
pv world.sql | psql -U postgres -d world
```

## 🗄️ Database Schema

The database includes the following tables:

- **regions** - Geographic regions (6 records)
- **subregions** - Geographic subregions (22 records)
- **countries** - Countries with details (250 records)
- **states** - States/provinces/regions (5,299 records)
- **cities** - Cities/towns/districts (153,765 records)

### Key Features
- ✅ Full UTF-8 support
- ✅ Foreign key constraints
- ✅ Indexed for performance
- ✅ Timestamps for all records
- ✅ Coordinates (latitude/longitude) for all locations
- ✅ 100% timezone coverage (IANA format)
- ✅ Multilingual support (19 languages)

## 🔍 Verify Import

After importing, verify the data:

```sql
-- Check record counts
SELECT 'regions' as table, COUNT(*) as count FROM regions
UNION ALL SELECT 'subregions', COUNT(*) FROM subregions
UNION ALL SELECT 'countries', COUNT(*) FROM countries
UNION ALL SELECT 'states', COUNT(*) FROM states
UNION ALL SELECT 'cities', COUNT(*) FROM cities;

-- Expected results:
-- regions: 6
-- subregions: 22
-- countries: 250
-- states: 5,299
-- cities: 153,765
```

## 📊 Sample Queries

```sql
-- Get all US states
SELECT * FROM states WHERE country_code = 'US';

-- Get all cities in California
SELECT * FROM cities WHERE state_code = 'CA' AND country_code = 'US';

-- Get countries by region
SELECT c.name, r.name as region 
FROM countries c 
JOIN regions r ON c.region_id = r.id;

-- Get cities with timezone
SELECT name, timezone FROM cities 
WHERE country_code = 'US' 
LIMIT 10;
```

## 🔄 Update Frequency

This database is updated monthly with the latest changes from the main repository.

**Last Updated:** December 13, 2025

## 📚 Additional Resources

- **Full Repository:** https://github.com/dr5hn/countries-states-cities-database
- **API Documentation:** https://docs.countrystatecity.in/
- **Demo Database:** https://demo.countrystatecity.in/
- **Export Tool:** https://export.countrystatecity.in/

## 🆘 Troubleshooting

### Permission Denied
```bash
# Switch to postgres user
sudo -u postgres psql
```

### Large File Import Issues
```bash
# Increase max allowed packet size
# Edit postgresql.conf and increase max_wal_size

# Or import in chunks using split
split -l 10000 cities.sql cities_part_
for file in cities_part_*; do
  psql -U postgres -d world -f $file
done
```

### Character Encoding Issues
```bash
# Create database with UTF-8 encoding
createdb -U postgres -E UTF8 world

# Or specify encoding during import
psql -U postgres -d world -f world.sql -v client_encoding=UTF8
```

## 📄 License

This data is licensed under the [Open Database License (ODbL)](../LICENSE).

## 🤝 Contributing

Found an issue or want to add data? Visit the [main repository](https://github.com/dr5hn/countries-states-cities-database) to contribute.

---

💡 **Tip:** For other formats (JSON, MySQL, SQLite, CSV, XML, YAML, MongoDB), visit the [repository root](https://github.com/dr5hn/countries-states-cities-database).
