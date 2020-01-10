---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779962"
---
### <a name="event-system-properties-mapping"></a>Сопоставление свойств системы событий

Если вы выбрали **Свойства системы событий** в разделе **источник данных** в приведенной выше таблице, перейдите к [веб-интерфейсу](https://dataexplorer.azure.com/) , чтобы выполнить соответствующую команду ККЛ для правильного создания сопоставления.

   **Для сопоставления CSV:**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **Для сопоставления JSON:**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * В сопоставление необходимо включить все выбранные свойства. 
   > * Порядок свойств важен в сопоставлении CSV. Системные свойства должны быть перечислены до всех остальных свойств и в том же порядке, в котором они отображаются в раскрывающемся списке **Свойства системы событий** .