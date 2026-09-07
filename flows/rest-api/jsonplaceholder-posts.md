# JSONPlaceholder Posts Flow

## Purpose

Fetches posts from the [JSONPlaceholder](https://jsonplaceholder.typicode.com/posts) REST API on a schedule and writes each post as a JSON record into a Snowflake table.

## Components

- **ScheduleTrigger** (GenerateFlowFile): Fires on a configurable schedule to initiate each fetch cycle
- **FetchPosts** (InvokeHTTP): GETs the REST API endpoint; outputs the JSON array response
- **SplitJsonArray** (SplitJson): Splits the JSON array into individual post objects
- **PutToSnowflake** (PutSnowflake): Writes each JSON record to the configured Snowflake table

## Required NARs

- `org.apache.nifi:nifi-standard-nar:2.8.0`
- `com.snowflake.openflow:nifi-snowflake-nar:2.8.0`

## Parameters

| Parameter | Default | Description |
|---|---|---|
| `API URL` | `https://jsonplaceholder.typicode.com/posts` | REST endpoint to fetch |
| `Destination Database` | — | Snowflake database for the target table |
| `Destination Schema` | — | Snowflake schema for the target table |
| `Destination Table` | `POSTS` | Target table name |
| `Snowflake Warehouse` | — | Warehouse used for ingestion |

The polling schedule is hardcoded to 60 minutes in the ScheduleTrigger processor (NiFi scheduling fields do not support parameter references).

## Network Rules

The runtime needs an egress rule allowing `jsonplaceholder.typicode.com:443`.

## Expected Behavior

Each run fetches the full list of posts (100 records), splits them into individual JSON objects, and inserts them into the Snowflake target table. The API returns objects with fields: `userId`, `id`, `title`, `body`.
