```markdown

## Dedicated Master Nodes

Availability
GA - Generally Available

Type
New Feature

Location in Docs Site
Under Global Apps

Applicable Releases/Backports
2.18, 2.20

Related Issues
14461, DB-3860

IDEA
IDEA-457

### Introduction
The Dedicated Master Nodes feature in YugabyteDB enhances database infrastructure by isolating master nodes from the worker nodes, providing improved performance and reliability. This feature is beneficial for large-scale deployments where master nodes require dedicated resources to ensure optimal functioning and reduced contention with data nodes. A typical use case involves enterprise-grade database deployments where high availability and performance are critical.

### Enable/Disable Feature for GA
To enable or disable the Dedicated Master Nodes feature, use the following GFlag:

Bash

```bash
# Enable dedicated master nodes
--enable_dedicated_master_nodes=true

# Disable dedicated master nodes
--enable_dedicated_master_nodes=false
```

### Unsupported Scenarios

Limitation 1: The Dedicated Master Nodes feature requires a minimum of three nodes for master configuration. Deployments with fewer nodes may not support this feature effectively.

Limitation 2 (2.18 only): In version 2.18, dedicated master nodes may experience delayed replication under heavy load, impacting real-time data consistency.

### Feature Details

**Prerequisites**

- Ensure you have Python 3.8 or higher installed.
- A running instance of the database, accessible via localhost:5433.

**Setup Environment**

1. Download the latest feature package from our releases page.
2. Extract the contents to your preferred directory.
3. Install necessary dependencies: `pip install -r requirements.txt`.

**Connect to the Database**

To connect to the database using ysqlsh:

Bash

```bash
ysqlsh -h <your_db_host> -p 5433 -U <your_username> -d <your_database>
```

If you're using ycqlsh:

Bash

```bash
ycqlsh <your_db_host> 9042
```

### Example

SQL

```sql
-- Example using ysqlsh: Create a table with dedicated master nodes enabled
CREATE TABLE master_node_data (
    id INT PRIMARY KEY,
    value TEXT
) WITH (master_nodes = 'dedicated');
```

### Code Samples

Python

```python
# Python Example: How to interact with dedicated master nodes via a client library
import yugabyte_client

client = yugabyte_client.connect("http://localhost:8080")
response = client.get_master_status()
print(f"Master node status: {response.status}")

client.enable_dedicated_masters()
print("Dedicated master nodes enabled successfully!")
```

Java

```java
// Java Example: Demonstrating API calls for dedicated master nodes
import com.example.yugabyte.ApiClient;
import com.example.yugabyte.models.MasterConfig;

public class MasterNodeUsage {
    public static void main(String[] args) {
        ApiClient client = new ApiClient("http://localhost:8080");
        MasterConfig config = new MasterConfig();
        config.setDedicated(true);
        client.updateMasterConfig(config);
        System.out.println("Master nodes configured for dedicated use.");
    }
}
```

### Additional Configuration

| GFlag                        | Details                                                                                            |
|------------------------------|----------------------------------------------------------------------------------------------------|
| example_ysql_max_connections | Defines the maximum number of concurrent YSQL connections allowed per database instance.           |
| --enable_dedicated_logging   | A boolean setting that, when enabled, activates enhanced logging for dedicated master nodes.       |

### Metrics

| Metric Name            | Details                                                                                                    |
|------------------------|------------------------------------------------------------------------------------------------------------|
| master_node_writes     | Cumulative count of write operations performed on dedicated master nodes.                                   |
| dedicated_usage_count  | A counter that increments each time dedicated master nodes are initialized or actively used within a session.|

### Architecture

The Dedicated Master Nodes feature introduces a separate processing module, MasterNodeProcessor, which operates independently to handle master-specific tasks. This architecture ensures no resource contention occurs between master and worker nodes, leveraging a custom queuing mechanism for efficient request handling and persistence via an optimized RocksDB layer.

### Best Practices

- **Optimal Resource Allocation:** Allocate at least 4GB of RAM per master node to ensure peak performance.
- **Monitoring Strategy:** Implement alerts for dedicated_usage_count spikes to detect unusual activity or potential bottlenecks.
- **Backward Compatibility:** Test upgrades in a staging environment to ensure compatibility with existing configurations.

### Related Articles

- [Azure with YugabyteDB: A Comprehensive Deployment Guide](https://docs.yugabyte.com/latest/deploy/azure/)
- [Blog Post: Diving Deep into Our New Feature's Performance](https://blog.yugabyte.com/new-feature-performance/)
- [YugabyteDB Fridays Tech Talk (YFTT): Understanding New Feature X](https://www.youtube.com/watch?v=xyz123)
```