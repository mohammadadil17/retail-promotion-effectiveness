# Production mapping

This project runs locally with open-source tools. In a client engagement the same pipeline would map onto Azure services as follows. The design (raw -> clean -> analytics layers, tracked models, a governed semantic layer) stays the same; the tools change.

| Project stage | Built here (local) | Production equivalent | Why |
|---|---|---|---|
| Ingest raw data | Manual download into `data/raw/` (never edited) | **Azure Data Factory** pipelines copying from source systems on a schedule into the raw layer of a data lake | Repeatable, scheduled, monitored loads |
| Clean and transform | `01_cleaning` notebook, pandas, CSV/Parquet | **Azure Databricks** notebooks or jobs writing Delta tables (raw / clean / analytics layers) | Scales to the full 1M+ rows and beyond; versioned tables |
| Data governance | Folder convention, cleaning log | **Unity Catalog**: table ownership, access control, lineage, data-quality rules | Who can see what, and where each number came from |
| Model training and tracking | LightGBM, open-source MLflow (`mlflow.db`) | **Azure ML** (or Databricks MLflow): experiment tracking, model registry, scheduled retraining | Reproducible models with approval steps |
| Serving predictions | Batch scores saved to CSV | Azure ML batch or online endpoints; scores written back to the analytics layer | Fresh forecasts feed the dashboard automatically |
| Dashboard | Tableau Public over CSVs | **Tableau Cloud or Server** on Databricks/Synapse tables (live connection or scheduled extract), row-level security by region | One governed dataset for all users |
| Monitoring | Manual checks | Data-quality tests in the pipeline, model drift and forecast-bias monitoring, cost and usage dashboards | The 6% under-forecast seen here would be caught and tracked |

## Things that must change before this is "production"
- **Secrets:** API keys in a key vault, not in code or files.
- **Testing:** unit tests on cleaning rules and measure definitions; the checks that already run in the notebooks become pipeline tests.
- **Governance:** access to each table controlled in Unity Catalog, with lineage from raw data to the dashboard number.
