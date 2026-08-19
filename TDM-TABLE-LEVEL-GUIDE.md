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

## 4. MTable Configuration

Two MTables must be configured to enable the partitioned extraction pipeline: `TableLevelDefinitions`, `TableLevelPartitionFlow`.

### 4.1 TableLevelDefinitions

Add one entry per SAP table that will be included in the extraction. Each entry requires the following core fields, plus a set of flow parameter rows.

**Core Fields**

Set the following fields for each table entry:

| Field | Value / Description |
|---|---|
| `interface_name` | Name of the SAP interface |
| `schema_name` | Target schema name |
| `table_name` | SAP table name (e.g., `BUT000`) |
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
