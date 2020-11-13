# Metadata Integrity Checks

___
[<< Contents](/procfwk/contents) 

___

For every new execution of the processing framework the database [stored procedure](/procfwk/storedprocedures) [procfwk].[CheckMetadataIntegrity] is called to inspect and check the content of the metadata [tables](/procfwk/tables).

This procedure is intended as a set pass/fail checks to prevent any runtime failures during a given framework execution run. The stored procedure is called near the very start of the parent [pipeline](/procfwk/pipelines) and includes the following metadata checks:

- Are there execution stages enabled in the metadata?
- Are there pipelines enabled in the metadata?
- Are there any service principals available to run the processing pipelines?
- Is there at least one TenantId available?
- Is there at least one SubscriptionId available?
- Is there a current OverideRestart property available?
- Are there any enabled pipelines configured without a service principal?
- Are any Data Factorys set to use the default subscription value?
- Are any Subscription set to use the default tenant value?
- Is there a current PipelineStatusCheckDuration property available?
- Is there a current UseFrameworkEmailAlerting property available?
- Is there a current EmailAlertBodyTemplate property available?
- Does the total size of the request body for the pipeline parameters added exceed the Azure Functions size limit when the Worker execute pipeline body is created?
- Is there a current FailureHandling property available?
- Does the FailureHandling property have a valid value?
- When using DependencyChain failure handling, are there any dependants in the same execution stage of the predecessor?
- Does the SPNHandlingMethod property have a valid value?
- Does the Service Principal table contain both types of SPN handling for a single credential?

In the event any check fails an exception will be raised stopping the parent pipeline and framework execution run from proceeding.

If an exception is thrown, the stored procedure can be used in debug mode, seen below, where details of the failed check(s) can be return as a result set via a SQL database client.

```sql
EXEC [procfwk].[CheckMetadataIntegrity] 
    @DebugMode = 1;
```

___

In addition to checking existing metadata integrity, the [stored procedure](/procfwk/storedprocedures) establishes if an execution run [clean up](/procfwk/prevruncleanup) is required. This secondary output is done here for convenience using a lookup activity within the parent [pipeline](/procfwk/pipelines) activity chain.