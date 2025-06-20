# SAP for TDM
- [Description](#description)
- [Installation](#installation)
- [How to upgrade to a new TDM version](#how-to-upgrade-to-a-new-tdm-version)
- [How to use](#how-to-use)
    - [TDMLUInit](#tdmluinit)
    - [TDM Table Level](#tdm-table-level)
    - [Synthetic data](#synthetic-data)
    - [Sequences](#sequences)
        - [How to Use](#how-to-use-1)
        - [Verifying the Sequence Masking Creation](#verifying-the-sequence-masking-creation)

# 

# Description

The *SAP for TDM* library is designed to enable seamless
integration between K2view and SAP systems, by using TDM.

Installing the *SAP for TDM* library will **also** automatically install the *SAP
Connector* library, which includes five built-in actors that are used in the *SAP for TDM* library. Make sure to read the *SAP Connector*
README document as well, as it includes essential information regarding
these actors.

# Installation

## Requirements

Before starting, ensure the following versions are in place:

-   **Fabric version:** 8.1.0 or higher
-   **Studio version:** 8.1.0 or higher
        
    <img src="media/tdm/image1.png" width="370" height="250" />


### Step 1: Ensure TDM 9.1.3 is Installed

### Step 2: Install *SAP Connector* Extension
Click on the *extensions* icon (left-most activity bar) to open EXTENSIONS in the K2Exchange tab, from which you should install the *SAP Connector* extension. Ensure the version is compatible with your Fabric version (v1 for Fabric 8.1, v2 for Fabric 8.2).
> **Note:**
> In *SAP for TDM* v1.0.1 or older, if you don't have the *SAP Connector* extension installed, *SAP for TDM* will install it automatically. However, it will install the latest available version, which may not be compatible with the Fabric version that you are using. Due to the above, we have decided to remove this auto-install feature in the latest *SAP for TDM* extension versions.

### Step 3: Install *SAP for TDM* Extension
> **Note:**
> It is crucial to install the *SAP Connector* extension before *SAP for TDM* as the latter might overwrite a file from the *SAP Connector* extension.
1.  Install the *SAP for TDM* extension from the K2Exchange tab.
2.  If you get a conflict warning, similar to the one shown below, then choose 'Overwrite All'.
    <img src="media/tdm/image2.png" width="601" height="265" />
3.  Deploy the TDM LU.
4.  Create SAP interface and test it:
    1) Navigate to the Project Tree -\> Implementation -\> Shared Objects -\>
    Interfaces.
    2) Right-click on 'Interfaces' and select 'New Interface'.
    3) Select the 'SAP' interface type:
    <img src="media/tdm/image3.png" width="600" height="363" />

    4) Set the corresponding connection details (host, port, user,
    password, etc.).
    5) SAP Namespace - set to the namespace where the SAP code was
    installed (refer to the *SAP Connector* README document for more
    details) and test the connection:
    <img src="media/tdm/image4.png" width="600" height="373" />

### Step 4: Build a Catalog for the SAP Tables

> Note: The SAP Connector itself is built on top of the Catalog and therefore this step is necessary, otherwise you will not be able to see the tables in the Interface Explorer.

1. The tables listed in the plugins.discovery file, under the data_platforms section must include the schema name (Category). If the Category is unknown, use \"\*\" as a wildcard to represent the schema, as shown below:
<img src="media/tdm/image5.png" width="601" height="322" />

2. Run SAP Discovery: Navigate to Project Tree -\> Implementation -\> Shared Objects -\> Interfaces -\> Other. Right-click the SAP interface that you intend to run the Catalog on, and select 'Run Discovery Job':
<img src="media/tdm/image6.png" width="601" height="443" />

3. After the Discovery job has been completed, navigate to the Catalog application, click on Actions -\> Build Artifacts:
<img src="media/tdm/image7.png" width="532" height="308" />

4.  **Note:** The SAP field definitions are strict with their size; if you decide to mask some of the fields, make sure that the generated values do not exceed their size as defined in SAP (you can check the size in the 'Column Size' in the 'catalog_field_info' MTable located under Implementation -\> Shared Objects -\> Interfaces -\> Discovery -\> MTable).

### Step 5: Create and Test the SAP LU
1. Create a new LU:
    - Navigate to the Project Tree -\> Implementation.
    - Right-click on Logical Units / Data Products and select 'New Data Product (Logical Unit)...'.
    - Name the new Logical Unit and select the default type.
2. Add SAP tables to the LU Schema:
    - Open the schema of the newly created LU.
    - Navigate to the INTERFACE EXPLORER tab by clicking on the database icon (left-most activity bar).
    - Open the SAP interface and locate the SAP tables to be added.
    - Right-click a table(s) and select the ‘Add Tables to Schema’ option, or any one of the three marked options (shown below).
    <img src="media/tdm/image8.png" width="526" height="371" />

3.  Test your SAP LU with a sample entity.

### Step 6: Build the Complete LU with all the SAP Schemas and Connect between the Different Tables (from all your required categories).

### Step 7: Set TDM for your SAP LU

1.  Environment settings:
    1.  Create source and target environments: Navigate to the Project Tree -\> Implementation -\> Shared Objects -\> Environments. Open the Environments sub-folder, which would open a new tab.
    <img src="media/tdm/image9.png" width="601" height="306" />

    2. Set interfaces (POSTGRESQL_ADMIN, TDM, SAP) for those environments, and test all interfaces to validate that they are working properly.
    3. Press 'Deploy Environments'.
3.  Run TDMLUInit flow: Navigate to the Project Tree -\> Implementation -\> Shared Objects -\> Broadway -\> TDM -\> TDMLUInit. Open the TDMLUInit.flow and run it with the corresponding arguments. As a result, the following flows will be created, using SAP actors: Load flows, Delete flows, Generator flows (the last two flows are created only if specified in the arguments).
    <img src="media/tdm/image10.png" width="601" height="337" />
4.  Test your SAP LU with a sample entity.

# How to Upgrade to a New TDM Version

1. Navigate once again (via the *extensions* icon) to open EXTENSIONS in the K2Exchange tab, click on the *settings* icon (to the right of *Uninstall*) and choose
    'Install Another Version\...':
   
    <img src="media/tdm/image11.png" width="524" height="156" />

3. Install the corresponding version of *SAP for TDM* extension in the same way. Verify that the version of the *SAP for TDM* extension that you are installing is compatible with the new TDM version.

4. If you get a conflict warning, similar the the one shown below, then choose 'Overwrite All':
    <img src="media/tdm/image12.png" width="601" height="224" />

5. If you need to install another *SAP Connector* extension's version, you will need to downgrade it as below:
    - Navigate to EXTENSIONS in the K2Exchange tab, locate the installed *SAP Connector* extension, click on the *settings* icon (to the right of *Uninstall*) and choose 'Install Another Version...'
     
        <img src="media/tdm/2c740864c4726e15bb925a94e7b1cbf1b7b714e3.png" width="600" height="180" />
    - Choose the corresponding version (e.g., if you work with Fabric 8.1, the corresponding version should be 1.0.2)
    - If you get the below warning, select 'Keep Changes':
        <img src="media/tdm/4cf58df3f816eddb65e18e90a34289cbf311910b.png" width="600" height="167" />
    - And then, in the next warning select 'Keep Both':    
        <img src="media/tdm/21d4d5bf0de4817f08e7e831ce5ae644c4609862.png" width="600" height="168" />
    - Navigate to Implementation -\> Shared Objects -\> Java -\> src -\> com -\> k2view -\> cdbms -\> usercode -\> common -\> sap. Delete the file 'SapActorTableQuery.java.new'
    - Refresh the page

# How to use

### TDMLUInit

Running the TDMLUInit flow with an LU that uses the *SAP Connector* extension,
automatically creates all the new flows (Load flows, Delete
flows and Generator flows) as built-in within the *SAP Connector*
actors. No further action is required from your end.
> **Note**: If you would like to use sequences, create them before running the TDMLUInit flow (see details below).

### TDM Table Level

Inside the *SAP for TDM* extension, there are special flows for SAP in the
TDM Tables LU and everything should work automatically when executing TDM
Table Level tasks. The only thing you need to set is the SAP interface
(or the name you have chosen for the interface) in the TableLevelInterfaces
MTable (Navigate to Implementation -\> Logical Units -\> References -\>
MTable -\> TableLevelInterfaces.csv): Set the Supress_indicator to false
and the Truncate_indicator to false as well.

<img src="media/tdm/e2f5baede4c24e5106d0c65b907ba6aa1997449d.png" width="600" height="170" />

### Synthetic data

The *SAP for TDM* extension supports synthetic data generation (Rule-based generation TDM task). 
In order to get the Generator flows, before running the TDMLUInit flow, set its 'CREATE_GENERATE_FLOWS' value to true.
> **Note**: It is very important to set the Classification for every field according to the Column Size in the catalog_field_info MTable, otherwise the default flow would retrieve a random string of size 10-15 (which could cause size conflicts in SAP). 


### Sequences

The SAP masking sequences ensure consistent and traceable document numbering
by leveraging dynamic elements such as dates and incremental values.
This mechanism simplifies reporting, enhances traceability, and reduces the number of errors in complex SAP environments.

A built-in flow, called SAP_Get_Initial_Sequence_Value, is available for
handling sequences. Open this flow and populate its parameters as
required.

The SapSequence actor will call the API with the provided parameters and
return the next available sequence value in the range. This value will
serve as the initial value, and TDM will manage the sequences from this
point forward.

> **Note:** In TDM, only the numeric non-external number range objects are supported. 

#### How to Use

- Step 1: Create an initial Broadway flow, which includes only one stage with the SapSequence actor, and fill in its inputs (see the actor's description for more details on the inputs).
- Step 2: Open the TDMSeqList.actor and configure the following as needed in the table input:
    - SEQUENCE_NAME: Specify the name of your sequence.
    - SEQUENCE_REDIS_OR_DB: TDM.
    - INITIATE_VALUE_OR_FLOW: The initial flow you created in step 1.

- Step 3: Open the TDMSeqSrc2TrgMapping.actor and configure the following as needed in the table input:
    - Define the fields as needed by your Logical Unit (LU) relations.
    - Under SEQUENCE_NAME, provide the same sequence name as used in the TDMSeqList.actor.

For further details about TDMSeqList and TDMSeqSrc2TrgMapping, refer to the link below [TDM Implementation Using Generic Flows \| K2View Support](https://support.k2view.com/Academy/articles/TDM/tdm_implementation/11_tdm_implementation_using_generic_flows.html#step-2---create-sequences) (Step 2 - Create Sequences)

- Step 4: Run the TDMLUInit.flow to implement the new sequence in TDM.

#### Verifying the Sequence Masking Creation

After running the TDMLUInit.flow, go to the Sequences folder (navigate to Implementation -\> Shared Objects -\> Broadway -\> Sequences). You should see the created sequence. Tasks associated with your LU will now display the new sequence in the TargetID field (under *Task Execution - Detailed Statistics*).

## Changelog

### v1.0.7-tdm9.3
- Supports TDM 9.3.1.
- Enhanced support for sequences.

### v1.0.6
- Revert part of TDM Hotfix which messes up TAR tables creation.

### v1.0.5
- Support TAR tables creation with useFabric param.
- TDM Hotfix for TAR tables creation in useFabric mode (fix is planned for TDM 9.4)

### v1.0.4
- Fix fetching list of table columns in TDM.

### v1.0.3
- Change extension name from "Sap for TDM" to "SAP for TDM".

### v1.0.2

### Fixed
- Number of rows counter in Table Level load - now displays.
- Added the missing inner-flow used by Table Level load flow. 

#### Added
- Added Table Level Delete flow.

#### Changed
- Moved the README.md to a separate Git repository.
  
### v1.0.1
#### Changed
- The `SapTableQuery` actor - that comes with the *SAP Connector* extensions - has been overwritten in order to use the `TDMSourceDbQuery` actor instead of the `SourceDbQuery` actor - which is needed for proper synthetic data generation in TDM.
### v1.0.0
#### Changed
- Adapted the code and tested it on TDM 9.1.3
