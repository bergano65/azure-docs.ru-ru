---
title: Защита учетных данных для доступа с помощью связанных служб в Apache Spark для Azure Synapse Analytics
description: В этой статье приведены основные сведения о безопасной интеграции Apache Spark для Synapse Analytics с другими службами с помощью связанных служб и библиотеки маркеров.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 90e7297236994650e0820e883c94a98b29c49fb7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249422"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>Защита учетных данных с помощью связанных служб и TokenLibrary
Доступ к данным из внешних источников является распространенным шаблоном. Если внешний источник данных разрешает анонимный доступ, то, вероятно, потребуется защитить подключение с помощью учетных данных, секрета или строки подключения.  

Azure Synapse Analytics предоставляет связанные службы для упрощения процесса интеграции, сохраняя сведения о подключении в связанной службе или Azure Key Vault. После создания связанной службы Apache Spark может на нее ссылаться, чтобы применить сведения о подключении в вашем коде. 

Дополнительные сведения см. в статье о [связанных службах](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
> [!NOTE]
> При доступе к файлам из Azure Data Lake Storage в рамках рабочей области выполняется сквозная проверка подлинности AAD, поэтому использовать TokenLibrary не нужно. 


## <a name="prerequisite"></a>Предварительное требование
* Связанная служба. Необходимо создать связанную службу для внешнего источника данных и ссылаться на нее из библиотеки маркеров. Дополнительные сведения о [связанных службах](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Подключение к ADLS 2-го поколения за пределами рабочей области Synapse

Synapse предоставляет интегрированную среду связанных служб для Azure Data Lake Storage 2-го поколения.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>Использование библиотеки маркеров

Чтобы подключиться к другим связанным службам, можно выполнить прямой вызов TokenLibrary.

### <a name="getconnectionstring"></a>GetConnectionString
 Чтобы получить строку подключения, используйте функцию <b>getConnectionString</b> и передайте <b>имя связанной службы</b>.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
Для анализа конкретных значений пары <i>ключ — значение</i> в строке подключения, например 

<i>DefaultEndpointsProtocol=https;AccountName=\<AccountName>;AccountKey=\<AccountKey></i>,

используйте функцию <b>getConnectionStringAsMap</b> и передайте ключ, чтобы вернуть значение.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>Дальнейшие действия

- [Запись в пуле SQL](./synapse-spark-sql-pool-import-export.md)

