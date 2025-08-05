# Step-by-Step Guide: Integrating DBeaver Community Edition with Azure Databricks using OAuth Browser Authentication

This guide will walk you through configuring OAuth user-to-machine (U2M) authentication with Azure Databricks and DBeaver Community Edition, enabling automatic browser-based authentication through Microsoft Entra ID.

https://github.com/user-attachments/assets/3518628e-7950-4caa-bbf7-7c1cea804c97

## Prerequisites

Before starting, ensure you have:

- **DBeaver Community Edition** installed on your local machine
- An **Azure Databricks workspace** accessible via your organization
- **Microsoft Entra ID** access with appropriate permissions
- A running **Azure Databricks cluster or SQL warehouse**
- **Databricks JDBC Driver** (version 2.6.36 or higher for OAuth U2M support)


## Step 1: Download and Install DBeaver Community Edition

1. **Download DBeaver CE** from the [official website](https://dbeaver.io/download/)
2. **Install** using your preferred method:
    - **Windows**: Run the installer or use `choco install dbeaver`
    - **macOS**: Use Homebrew with `brew install --cask dbeaver-community`
    - **Linux**: Download and run the appropriate Linux installer

## Step 2: Download the Databricks JDBC Driver

1. **Download** the latest Databricks JDBC Driver from the [Databricks documentation](https://docs.databricks.com/integrations/jdbc-oss/)
2. **Extract** the `DatabricksJDBC42.jar` file from the downloaded ZIP archive
3. **Note the location** where you saved the JAR file for the next step

## Step 3: Configure the Databricks JDBC Driver in DBeaver

1. **Start DBeaver** and dismiss any initial database connection prompts
2. **Navigate** to **Database > Driver Manager**
3. **Click "New"** to create a new driver
4. **On the Libraries tab**:
    - Click **"Add File"**
    - Navigate to and select the `DatabricksJDBC42.jar` file
    - Click **"Open"**
5. **On the Settings tab**:
    - **Driver Name**: Enter `Databricks`
    - **Class Name**: Enter `com.databricks.client.jdbc.Driver`
6. **Click "OK"** to save the driver configuration
7. **Click "Close"** to exit the Driver Manager

## Step 4: Gather Azure Databricks Connection Information

1. **Log into your Azure Databricks workspace**
2. **Navigate** to your cluster or SQL warehouse
3. **Collect the following information**:
    - **Server hostname**: Found in your cluster/warehouse connection details
    - **HTTP path**: Also found in the connection details
    - **Port**: Typically 443 for Azure Databricks

## Step 5: Create a New Database Connection in DBeaver

1. **In DBeaver**, click **Database > New Database Connection**
2. **On the "All" tab**, select **"Databricks"** and click **"Next"**
3. **On the "Main" tab**, configure the connection:

### Connection URL Configuration

**For OAuth User-to-Machine (U2M) authentication**, use this JDBC URL format[^1]:

```
jdbc:databricks://<server-hostname>:443;httpPath=<http-path>;AuthMech=11;Auth_Flow=2;TokenCachePassPhrase=<your-passphrase>;EnableTokenCache=1
```

**Replace the placeholders**:

- `<server-hostname>`: Your Databricks workspace hostname
- `<http-path>`: The HTTP path from your cluster/warehouse
- `<your-passphrase>`: A secure passphrase of your choice for token encryption


### Example Connection URL:

```
jdbc:databricks://adb-1234567890123456.7.azuredatabricks.net:443;httpPath=/sql/1.0/warehouses/abc123def456;AuthMech=11;Auth_Flow=2;TokenCachePassPhrase=MySecurePassphrase123;EnableTokenCache=1
```


## Step 6: Test and Complete the Connection

1. **Click "Test Connection"** in DBeaver
2. **Browser Authentication Process**:
    - A browser window will **automatically open**
    - You'll be redirected to **Microsoft Entra ID login page**
    - **Sign in** with your organizational credentials
    - **Grant permissions** when prompted for Databricks access
    - The browser will show a success message once authentication is complete[^1]
3. **Back in DBeaver**:
    - The connection test should show **"Connected"**
    - Click **"OK"** if the test succeeds
    - Click **"Finish"** to save the connection

## Step 7: Verify and Customize Your Connection

1. **In the Database Navigator**, you should see your **Databricks connection**
2. **To rename the connection**:
    - Right-click on **"Databricks"**
    - Select **"Edit Connection"**
    - Go to the **"General"** tab
    - Change the **"Connection name"** to something descriptive
    - Click **"OK"**

## Key Configuration Parameters Explained

- **`AuthMech=11`**: Enables OAuth 2.0 authentication[^1]
- **`Auth_Flow=2`**: Specifies user-to-machine (browser-based) authentication flow[^1]
- **`TokenCachePassPhrase`**: Encrypts cached refresh tokens to avoid repeated browser authentication[^1]
- **`EnableTokenCache=1`**: Enables token caching for seamless reconnections[^1]


## Troubleshooting Tips

1. **Browser doesn't open**: Ensure DBeaver has permission to launch your default browser
2. **Authentication timeout**: The browser authentication should complete within a few minutes[^3]
3. **Connection fails**: Verify your cluster/warehouse is running before testing the connection
4. **Token cache issues**: Try setting `EnableTokenCache=0` to disable caching if you encounter authentication problems

## Security Benefits

This OAuth U2M setup provides several security advantages:

- **No stored passwords**: Authentication uses temporary tokens rather than static credentials
- **Automatic token refresh**: Expired tokens are refreshed automatically without user intervention[^1]
- **Microsoft Entra ID integration**: Leverages your organization's existing identity and access management
- **Token encryption**: Cached tokens are encrypted using your specified passphrase[^1]

Once configured, DBeaver will automatically handle the OAuth authentication flow, opening your browser when needed and managing token refresh in the background, providing a seamless and secure connection to your Azure Databricks workspace.

<div style="text-align: center">⁂</div>

[^1]: https://learn.microsoft.com/en-us/azure/databricks/integrations/jdbc/authentication

: https://learn.microsoft.com/en-us/azure/databricks/dev-tools/dbeaver

[^3]: https://community.databricks.com/t5/data-engineering/unable-to-connect-with-databricks-serverless-sql-using-dbeaver/td-p/64392

[^4]: https://docs.databricks.com/aws/en/dev-tools/dbeaver

[^5]: https://dbeaver.com/docs/dbeaver/Database-driver-Azure-Databricks/

[^6]: https://docs.databricks.com/gcp/en/integrations/odbc/authentication

[^7]: https://docs.databricks.com/aws/en/integrations/jdbc-oss/configure

[^8]: https://www2.microstrategy.com/producthelp/Current/Gateway_Connections/WebHelp/Lang_1033/Content/databricks_oidc_azure.htm

[^9]: https://www.alation.com/docs/en/latest/OpenConnectorFramework/DataSourceConnectors/DatabricksUnityCatalog/DatabricksonAWSBuiltInOAuth.html

[^10]: https://learn.microsoft.com/en-us/azure/databricks/integrations/jdbc-oss/configure

[^11]: https://docs.azure.cn/en-us/databricks/integrations/odbc/authentication

[^12]: https://dbeaver.com/docs/dbeaver/Database-driver-Azure-Databricks/24.3

[^13]: https://docs.databricks.com/gcp/en/integrations/jdbc/authentication

[^14]: https://community.databricks.com/t5/warehousing-analytics/issue-when-using-m2m-authentication-with-azure-databricks-jdbc/td-p/114796

[^15]: https://learn.microsoft.com/en-us/azure/databricks/integrations/jdbc-oss/

[^16]: https://docs.databricks.com/aws/en/integrations/jdbc/authentication

[^17]: https://dbeaver.com/docs/dbeaver/Authentication-Microsoft-Entra-ID/

[^18]: https://www.cdata.com/kb/tech/databricks-odbc-dbeaver.rst

[^19]: https://github.com/dbeaver/dbeaver/issues/22475

[^20]: https://github.com/dbeaver/dbeaver/issues/23470

