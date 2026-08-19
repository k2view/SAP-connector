# SAP Connector Implementation Guide

## Technical Configuration & Component Reference

This guide walks through deploying partitioned, table-level TDM extraction and in-place masking for SAP source systems, covering all custom components, configuration tables, and task setup required.

## 1. Overview

This document provides a step-by-step implementation guide for deploying the K2View SAP Connector's TDM table-level partitioning support. It covers all custom components, configuration tables, and task setup required to enable partitioned data extraction and masking from SAP source systems.

## 2. Installation

Before proceeding with configuration, ensure the K2View SAP Connector is installed in your environment.

1. Install the K2View SAP Connector package from the K2View marketplace or your internal repository.
2. Verify the connector appears in the project's dependency list after installation.
3. Confirm connectivity to the SAP source system using the connector's built-in test utility.

## 3. Components

The SAP Connector implementation includes three custom flows and a dedicated Logical Unit (LU). Each component serves a specific role in the extraction and partitioning pipeline.

### 3.1 Globals

The partition size parameter controls how data is split across parallel extraction jobs. Adjust this value based on SAP system performance and the size of the target tables.

| Global Parameter | Default value |
|---|---|
| `SAP_PARTITION_SIZE` | 100,000 |

### 3.2 Flows

The following flows handle partition calculation, data fetching, and test environment preparation.

#### SapGetPartitionsNumber

Calculates the total number of partitions required for a given table based on the MIN value, MAX value, and the global `SAP_PARTITION_SIZE` parameter.

- Input parameters: `columnName`, `MIN`, `MAX`
- Output: Integer representing total partition count
- Used as the `partition_count_source` in `TableLevelDefinitions`

#### SapGetDataByPartition

Fetches data from the SAP source table for a specific partition range. This flow is called once per partition during the extraction process.

- Input parameters: `columnName`, `MIN`, `MAX`, `selectColumns`
- Output: Rows from the target SAP table for the given partition
- Used as the `partition_records_flow` in `TableLevelDefinitions`

#### preparePartitioningTestingTable

An internal utility flow used for demo and testing purposes. It copies the original SAP table (`BUT000`) to the demo table (`ZBUT000`) and generates sequential IDs for the `PARTNER` partitioning field.

> **Note:** This flow is for internal/demo use only and should not be included in production task configurations.

### 3.3 TDM Logical Unit (LU)

A dedicated TDM Logical Unit named `SAP` is created as part of this implementation. The LU is intentionally created without any custom business logic — all processing is handled by the flows and MTable configurations described in this guide.

## 4. MTable Configuration

Three MTables must be configured to enable the partitioned extraction pipeline: `TableLevelDefinitions`, `TableLevelPartitionFlow` and `RefList`.

### 4.1 TableLevelDefinitions

Add one entry per SAP table that will be included in the extraction. Each entry requires the following core fields, plus a set of flow parameter rows.

**Core Fields**

Set the following fields for each table entry:

| Field | Value / Description |
|---|---|
| `interface_name` | Name of the SAP interface |
| `schema_name` | Target schema name |
| `table_name` | SAP table name (e.g., `ZBUT000`) |
| `record_count_flow` | `SAPTableRecordCount` |
| `partition_count_source` | `SapGetPartitionsNumber` |
| `partition_records_flow` | `SapGetDataByPartition` |

### 4.2 TableLevelPartitionFlow

In addition to the core fields, add the following parameter rows. Each row maps a flow to a specific parameter name and value. The `columnName` and `MIN`/`MAX` values must be consistent across both flows for the same table.

| flow_name | param_name | value |
|---|---|---|
| `SapGetPartitionsNumber` | `columnName` | Column name on which partition ranges are decided |
| `SapGetDataByPartition` | `columnName` | Column name on which partition ranges are decided |
| `SapGetPartitionsNumber` | `MIN` | Minimum value of the partition column |
| `SapGetDataByPartition` | `MIN` | Minimum value of the partition column |
| `SapGetPartitionsNumber` | `MAX` | Maximum value of the partition column |
| `SapGetDataByPartition` | `MAX` | Maximum value of the partition column |
| `SapGetDataByPartition` | `selectColumns` | Pipe-separated list of columns required for masking, e.g. `CLIENT\|PARTNER\|NAME_FIRST\|NAME_LAST` |

### 4.3 RefList

Add a new entry in the `RefList` MTable for each SAP table being processed. Populate the entry with the same table details used in `TableLevelDefinitions` (interface name, schema name, table name).

As lu name should set `SAP` as defined in LU section [3.3](#33-tdm-logical-unit-lu).

## 5. Load Task Configuration

Create a new Load Task in the K2View TDM to orchestrate the full extract-and-load pipeline. The task is configured in four stages: Extract, Subset, Test Data Store, and Load.

### 5.1 Extract Stage

Configure the extraction source and scope:

1. Create a new task.
2. Set the task type to Entities & Referential Data.
3. Set the Business Entity to `SAP`.
4. Select the appropriate source environment.
5. Enable the Referential Tables checkbox.
6. Under Tables, select the SAP table(s) required.

### 5.2 Subset

1. Set the subsetting method to Entity List.
2. Enter `1` in the Entity IDs field.

### 5.3 Test Data Store

1. Set the Retention Period to Do Not Retain.

### 5.4 Load Stage

1. Set the Business Entity to `SAP`.
2. Select the same environment used in the Extract stage.
3. Enable the Load checkbox.

---
*End of Implementation Guide*
