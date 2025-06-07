# ADF-project-SCD-type-1-employee-data-update

This project demonstrates how to implement Slowly Changing Dimension Type 1 (SCD Type 1) logic using Azure Data Factory (ADF) to manage employee data updates. The solution reads data from a CSV file stored in Azure Blob Storage and performs upserts into an Azure SQL Database using Mapping Data Flows and Pipelines.

This implementation covers key concepts in data engineering, such as data movement, transformation, and maintaining the most recent state of dimension data without tracking historical changes.

## Components Used

### 1. Pipeline: pipeline1
- Orchestrates the end-to-end workflow.
- Invokes the Mapping Data Flow (dataflow1) as its main activity.
- Can be triggered manually or on a schedule.

### 2. Data Flow: dataflow1
Handles transformation and SCD Type 1 logic.

- Source:
  - DelimitedText1 – CSV dataset from Azure Blob Storage
- Transformation:
  - alterRow1 – uses the expression upsertIf(1==1) to apply unconditional upserts
- Sink:
  - AzureSqlTable1 – maps data into a target SQL table with upsert settings

## Sink Table: dbo.Employee (Azure SQL Database)

The target table is defined as follows:

| Column      | Data Type     | Description                  |
|-------------|---------------|------------------------------|
| EmployeeID  | INT           | Unique identifier, Primary Key |
| FirstName   | VARCHAR       | First name of the employee   |
| LastName    | VARCHAR       | Last name of the employee    |
| Department  | VARCHAR       | Department name              |
| Salary      | INT           | Current salary               |
| LastUpdated | DATE          | Date of the last update      |

The table is designed for SCD Type 1, which means:
- New records are inserted
- Existing records are updated based on EmployeeID
- No historical data is retained

## Concepts Applied

- Slowly Changing Dimension Type 1 (SCD Type 1): Overwrites existing data to reflect the most recent changes.
- Mapping Data Flows: Used for performing ETL operations in a low-code/no-code environment.
- Upsert Logic: Achieved using the alterRow transformation with upsertable set to true and keys set to EmployeeID.
- Schema Drift: Enabled to allow flexible schema mapping.

## Sample Source File Format

The CSV file used as source (DelimitedText1) should be structured as follows:

EmployeeID,FirstName,LastName,Department,Salary,LastUpdated
1001,John,Doe,IT,60000,2025-01-10
1002,Jane,Smith,HR,55000,2025-02-15
1003,Michael,Brown,Finance,70000,2025-03-20

markdown
Copy
Edit

## Business Use Case

This project suits scenarios where only the latest data needs to be retained. For example:
- Maintaining employee records
- Customer or product data where changes overwrite previous values
- Master data synchronization tasks

## Execution Flow

1. The user triggers pipeline1.
2. pipeline1 runs dataflow1.
3. dataflow1:
   - Reads the CSV file from blob storage
   - Applies upsert logic to all incoming records
   - Inserts or updates records in the SQL table using EmployeeID as the key

## Notes

- This implementation does not include audit columns or change tracking history.
- It can be extended to SCD Type 2 by adding attributes like IsActive, EffectiveDate, and surrogate keys.
- Parameterization can be added to make the pipeline reusable for different datasets and tables.
