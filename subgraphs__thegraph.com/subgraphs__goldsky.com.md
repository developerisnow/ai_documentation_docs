# Subgraphs
https://docs.goldsky.com/mirror/sources/subgraphs
You can use subgraphs as a pipeline source, allowing you to combined the flexibility of subgraph indexing with the expressiveness of the database of your choice.

This enables a lot of powerful use-cases:

* Reuse all your existing subgraph entities.
* Increase querying speeds drastically compared to graphql-engines.
* Flexible aggregations that weren't possible with just GraphQL.
* Analytics on protocols through Clickhouse, and more.
* Plug into BI tools, train AI, and export data for your users

Full configuration details for Subgraph Entity source is available in the [reference](/reference/config-file/pipeline#subgraph-entity) page.

## Automatic Deduplication

Subgraphs natively support time travel queries. This means every historical version of every entity is stored. To do this, each row has an `id`, `vid`, and `block_range`.

When you update an entity in a subgraph mapping handler, a new row in the database is created with the same ID, but new VID and block\_range, and the old row's `block_range` is updated to have an end.

By default, pipelines **deduplicate** on `id`, to show only the latest row per `id`. In other words, historical entity state is not kept in the sink database. This saves a lot of database space and makes for easier querying, as additional deduplication logic is not needed for simple queries. In a postgres database for example, the pipeline will update existing rows with the values from the newest block.

This deduplication happens through setting the primary key in the data going through the pipeline. By default, the primary key is `id`.

If historical data is desired, you can set the primary key to `vid` through a transform.

<Tabs>
  <Tab title="v3">
    ```yaml  theme={null}
    name: qidao-optimism-subgraph-to-postgrse
    apiVersion: 3
    sources:
      subgraph_account:
          type: subgraph_entity
          name: account
          subgraphs:
          - name: qidao-optimism
            version: 1.1.0
    transforms:
      historical_accounts:
        sql: >-
          select * from subgraph_account
        primary_key: vid
    sinks:
      postgres_account:
        type: postgres
        table: historical_accounts
        schema: goldsky
        secret_name: A_POSTGRESQL_SECRET
        from: historical_accounts
    ```
  </Tab>

  <Tab title="v2 (deprecated)">
    ```yaml  theme={null}
    sources:
      - type: subgraphEntity
        # The deployment IDs you gathered above. If you put multiple,
        # they must have the same schema
        deployments:
          - id: QmPuXT3poo1T4rS6agZfT51ZZkiN3zQr6n5F2o1v9dRnnr
        # A name, referred to later in the `sourceStreamName` of a transformation or sink
        referenceName: account
        entity:
          # The name of the entities
          name: account

    transforms:
      - referenceName: historical_accounts
        type: sql
        # The `account` referenced here is the referenceName set in the source
        sql: >-
          select * from account
        primaryKey: vid


    sinks:
      - type: postgres
        table: historical_accounts
        schema: goldsky
        secretName: A_POSTGRESQL_SECRET
        # the `historical_accounts` is the referenceKey of the transformation made above
        sourceStreamName: historical_accounts
    ```
  </Tab>
</Tabs>

In this case, all historical versions of the entity will be retained in the pipeline sink. If there was no table, tables will be automatically created as well.

## Using the wizard

### Subgraphs from your project

Use any of your own subgraphs as a pipeline source. Use `goldsky pipeline create <pipeline-name>` and select `Project Subgraph`, and push subgraph data into any of our supported sinks.

### Community subgraphs

When you create a new pipeline with `goldsky pipeline create <your-pipeline-name>`, select **Community Subgraphs** as the source type. This will display a list of available subgraphs to choose from. Select the one you are interested in and follow the prompts to complete the pipeline creation.

This will get load the subgraph into your project and create a pipeline with that subgraph as the source.
