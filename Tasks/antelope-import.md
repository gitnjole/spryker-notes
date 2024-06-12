## Database

1. Create csv file in `data/import/local/common/`
- update the `full_EU.yml` with the new import

## Zed

1. Create a module directory for Goblins
2. Create a database schema in `Persistence/Propel/Schema/`
- `schema.xml`
- execute `console propel:install`
3. Create a DataImporter module
Create a class `GoblinDataImportConfig` that extends `DataImportConfig` and create a constant that uses the same name as in `full_EU.yml`

TBD
