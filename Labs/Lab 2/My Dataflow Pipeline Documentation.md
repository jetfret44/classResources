\# My Dataflow Pipeline Documentation



\## Pipeline Overview



\*   \*\*Purpose:\*\* This pipeline processes retail sales data from a CSV file, performing data type conversions and schema standardization before loading it into BigQuery for analytics and reporting.

\*   \*\*Source:\*\* Google Cloud Storage bucket containing a superstore dataset CSV file (`gs://mgmt599-davidjetter-data-lake-2/superstore\_dataset.csv`)

\*   \*\*Transformations:\*\*

&nbsp;   \*   Parses CSV data into structured records.

&nbsp;   \*   Converts date strings from `MM/DD/YYYY` format to proper `DATE` objects.

&nbsp;   \*   Casts numeric fields to appropriate data types (e.g., `FLOAT` for monetary values, `INTEGER` for quantities).

&nbsp;   \*   Maps raw CSV columns to named dictionary fields for better data structure.

\*   \*\*Destination:\*\* BigQuery table in Google Cloud Platform (`mgmt599-davidjetter-lab2.lab2\_dataflow.processed\_sales`)



\## Pipeline Configuration



\*   \*\*Job Name:\*\* \[Generated automatically by Dataflow based on pipeline execution]

\*   \*\*Region:\*\* `us-central1`

\*   \*\*Machine Type:\*\* \[Default Dataflow worker machine type]

\*   \*\*Max Workers:\*\* \[Default Dataflow scaling configuration]

\*   \*\*Runner:\*\* `DataflowRunner` (Google Cloud Dataflow)

\*   \*\*Project:\*\* `mgmt599-davidjetter-lab2`



\## Data Flow



\*   \*\*Read from:\*\* `gs://mgmt599-davidjetter-data-lake-2/superstore\_dataset.csv` (skipping header row)

\*   \*\*Transform:\*\*

&nbsp;   \*   Parse each CSV line using Python's `csv.reader`.

&nbsp;   \*   Convert date fields (`order\_date`, `ship\_date`) from string to `DATE` format.

&nbsp;   \*   Cast numerical fields to proper types (`discount`, `profit`, `sales`, `profit\_margin` to `FLOAT`; `quantity` to `INTEGER`).

&nbsp;   \*   Structure data into a dictionary with meaningful field names.

\*   \*\*Write to:\*\* `mgmt599-davidjetter-lab2.lab2\_dataflow.processed\_sales` BigQuery table with `WRITE\_TRUNCATE` disposition.



\## Pipeline Architecture



```text

CSV File (GCS) --> Parse CSV --> Transform Data Types --> Load to BigQuery

&nbsp;   ↓                  ↓                  ↓                      ↓

Raw text           Structured         Type-converted         Analytics-ready

records            arrays             dictionaries           table







\## Data Schema



The pipeline transforms 19 fields, including:



\*   \*\*Identifiers:\*\* `order\_id`, customer information

\*   \*\*Dates:\*\* `order\_date`, `ship\_date` (converted to `DATE` type)

\*   \*\*Geographic:\*\* `region`, `zip`, `city`, `state`, `country`

\*   \*\*Product:\*\* `manufactory`, `product\_name`, `segment`, `category`, `subcategory`

\*   \*\*Metrics:\*\* `discount`, `profit`, `quantity`, `sales`, `profit\_margin` (properly typed)



\## Lessons Learned



\### What was challenging?



\*   Ensuring proper date format parsing (`MM/DD/YYYY`) to match the source data.

\*   Managing data type conversions for mixed field types in CSV data.

\*   Configuring appropriate GCS bucket permissions and BigQuery dataset access.



\### What would you do differently?



\*   Add error handling for malformed records or missing values.

\*   Implement data validation steps to catch parsing errors.

\*   Consider using a more robust schema definition approach.

\*   Add logging and monitoring for pipeline execution status.

\*   Implement incremental loading instead of `WRITE\_TRUNCATE` for production scenarios.





