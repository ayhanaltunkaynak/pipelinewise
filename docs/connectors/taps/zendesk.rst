
.. _tap-zendesk:

Tap Zendesk
-----------


Configuring what to replicate
'''''''''''''''''''''''''''''

PipelineWise configures every tap with a common structured YAML file format.
A sample YAML for Zendesk replication can be generated into a project directory by
following the steps in the :ref:`generating_pipelines` section.

Example YAML for tap-zendesk:

.. code-block:: yaml

    ---

    # ------------------------------------------------------------------------------
    # General Properties
    # ------------------------------------------------------------------------------
    id: "zendesk"                          # Unique identifier of the tap
    name: "Sampe data on Zendesk"          # Name of the tap
    type: "tap-zendesk"                    # !! THIS SHOULD NOT CHANGE !!
    owner: "somebody@foo.com"              # Data owner to contact
    #send_alert: False                     # Optional: Disable all configured alerts on this tap


    # ------------------------------------------------------------------------------
    # Source (Tap) - Zendesk connection details
    # ------------------------------------------------------------------------------
    db_conn:
      access_token: "<ACCESS_TOKEN>"       # Plain string or vault encrypted
      subdomain: "zendesk_subdomain"       #
      start_date: "2000-01-01T00:00:00Z"   # Data before this date will be ignored
      #rate_limit: 1000                    # If you wish to avoid ever hitting the rate limit
      #max_workers: 10                     # Max concurrent threads when communicating to zendesk API
      #batch_size: 50                      # Number of tickets to query in one batch


    # ------------------------------------------------------------------------------
    # Destination (Target) - Target properties
    # Connection details should be in the relevant target YAML file
    # ------------------------------------------------------------------------------
    target: "snowflake"                       # ID of the target connector where the data will be loaded
    batch_size_rows: 20000                    # Batch size for the stream to optimise load performance
    stream_buffer_size: 0                     # In-memory buffer size (MB) between taps and targets for asynchronous data pipes
    default_target_schema: "zendesk"          # Target schema where the data will be loaded 
    default_target_schema_select_permission:  # Optional: Grant SELECT on schema and tables that created
      - grp_power
    #batch_wait_limit_seconds: 3600           # Optional: Maximum time to wait for `batch_size_rows`. Available only for snowflake target.

    # Options only for Snowflake target
    #archive_load_files: False                      # Optional: when enabled, the files loaded to Snowflake will also be stored in `archive_load_files_s3_bucket`
    #archive_load_files_s3_prefix: "archive"        # Optional: When `archive_load_files` is enabled, the archived files will be placed in the archive S3 bucket under this prefix.
    #archive_load_files_s3_bucket: "<BUCKET_NAME>"  # Optional: When `archive_load_files` is enabled, the archived files will be placed in this bucket. (Default: the value of `s3_bucket` in target snowflake YAML)


    # ------------------------------------------------------------------------------
    # Source to target Schema mapping
    # ------------------------------------------------------------------------------
    schemas:
      - source_schema: "zendesk"           # This is mandatory, but can be anything in this tap type
        target_schema: "zendesk"           # Target schema in the destination Data Warehouse

        # List of Zendesk tables to replicate into destination Data Warehouse
        # Tap-Zendesk will use the best incremental strategies automatically to replicate data
        tables:
          - table_name: "group_memberships"
          - table_name: "groups"
          - table_name: "macros"
          - table_name: "organizations"
          - table_name: "satisfaction_ratings"
          - table_name: "tags"
          - table_name: "tickets"
          - table_name: "ticket_audits"
          - table_name: "ticket_comments"
          - table_name: "ticket_fields"
          - table_name: "ticket_metrics"
          - table_name: "users"

            # OPTIONAL: Load time transformations
            #transformations:                    
            #  - column: "last_name"            # Column to transform
            #    type: "SET-NULL"               # Transformation type
