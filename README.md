# Healthcare Provider Master Data Management

This project builds a small provider Master Data Management (MDM) workflow using public healthcare provider data from NPPES.

The main problem is that the same provider can exist in multiple hospital systems with slightly different information. One system may use a full legal name, another may use an abbreviation, and another may have a different specialty or address format.

The goal is to identify records that belong to the same provider and combine them into one trusted master record.

## Problem

A provider could appear across systems like this:

```text
EHR
Samantha Lee
NPI 1234567890
Cardiology

HR
Samantha J Lee
Employee ID 84721
Heart Center

Credentialing
Sam Lee, MD
NPI 1234567890
Cardiovascular Medicine
```

These records describe the same person, but they are not identical.

This project works through how to:

* clean and standardize provider data
* identify duplicate provider records
* group records that belong to the same person
* resolve conflicting values
* create one golden provider record
* validate whether the matching process worked correctly

## Data

The source data comes from the **National Plan and Provider Enumeration System (NPPES)** maintained by CMS.

NPPES contains public information about healthcare providers and organizations, including:

* NPI
* provider name
* credentials
* addresses
* taxonomy codes
* provider identifiers

The full dataset is large, so this project uses a smaller subset of providers.

Raw NPPES files are stored locally under:

```text
data/raw/nppes/
```

Raw data is not committed to GitHub.

## Project Workflow

```text
NPPES Data
    ↓
Create Provider Subset
    ↓
Simulate 3 Hospital Systems
    ↓
Profile + Clean Data
    ↓
SQL Staging
    ↓
Pimcore MDM
    ↓
Provider Matching
    ↓
Duplicate Review
    ↓
Survivorship Rules
    ↓
Golden Records
    ↓
Provider Master
    ↓
Validation
```

## Main Steps

### 1. Create an NPPES subset

Select a manageable number of individual providers and keep only the fields needed for the project.

The original downloaded data remains unchanged.

### 2. Simulate hospital source systems

The NPPES subset will be used to create three simulated systems:

* EHR
* HR
* Credentialing

The datasets will intentionally contain differences such as:

* name variations
* missing values
* address formatting differences
* specialty variations
* source-specific IDs
* duplicate records

### 3. Create ground truth

Because the simulated records come from known providers, the original provider relationship can be saved separately.

This ground truth will later be used to check whether the matching process identified the correct records.

### 4. Profile and standardize the data

Python and pandas will be used to review:

* missing values
* duplicates
* formatting
* unexpected values
* field completeness

The data will then be standardized before matching.

### 5. Load SQL staging tables

Each source system will be loaded into its own SQL staging table.

For example:

```text
stg_ehr_provider
stg_hr_provider
stg_credentialing_provider
```

SQL will also be used for validation and reconciliation.

### 6. Build the provider model in Pimcore

Pimcore Community Edition is being used as the MDM platform.

The provider model will include fields such as:

* NPI
* name
* credentials
* specialty
* taxonomy
* address
* source system
* source record ID

### 7. Apply data quality rules

Rules will be added for important fields such as:

* valid 10-digit NPI
* valid state values
* required provider names
* valid ZIP format
* valid taxonomy values

### 8. Match provider records

Records will be compared using fields such as:

* NPI
* name
* address
* ZIP code
* specialty
* taxonomy

Strong matches can be automatically grouped, while uncertain matches can be reviewed manually.

### 9. Apply survivorship rules

When multiple records belong to the same provider, rules will determine which values should be kept.

For example:

* prefer a valid NPI
* prefer the most complete provider name
* prefer credentialing data for credentials
* prefer standardized specialty values

### 10. Create the provider master

The final result will be one golden record per provider.

```text
Source Records
      ↓
Matching
      ↓
Duplicate Groups
      ↓
Survivorship
      ↓
Golden Record
      ↓
Provider Master
```

### 11. Validate the results

The final provider matches will be compared against the ground truth.

Validation will look at:

* correct matches
* missed matches
* incorrect merges
* unmatched records
* duplicate reduction
* golden record completeness

## Technology

* Python
* pandas
* SQL
* Pimcore Community Edition
* Docker
* Jupyter Notebook
* Git / GitHub

## Repository Structure

```text
provider-mdm-pimcore/
│
├── data/
│   ├── raw/
│   │   └── nppes/
│   ├── subset/
│   └── ground_truth/
│
├── notebooks/
├── src/
├── config/
├── tests/
├── docker-compose.yaml
└── README.md
```

## Current Status

Pimcore Community Edition is installed and running locally.

The raw NPPES data has been downloaded and placed in:

```text
data/raw/nppes/
```

The next step is creating the provider subset and simulated hospital source datasets.
