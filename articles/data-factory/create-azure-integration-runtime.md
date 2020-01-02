---
title: Создание среды выполнения интеграции Azure в фабрике данных Azure
description: Узнайте, как создать среду выполнения интеграции Azure в фабрике данных Azure, используемую для копирования данных и подготовки к отправке действий преобразования.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 1a9a88f975ca10a63741818dcb88874a8f1bf436
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926385"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Создание и настройка среды выполнения интеграции Azure
Среда выполнения интеграции (IR) — это инфраструктура вычислений, которую фабрика данных Azure использует для обеспечения интеграции данных в разных сетевых средах. Дополнительные сведения о среде выполнения интеграции см. в статье [Integration runtime in Azure Data Factory](concepts-integration-runtime.md) (Среда выполнения интеграции в фабрике данных Azure).

Среда выполнения интеграции Azure обеспечивает полностью управляемый вычислительный процесс для выполнения операций перемещения данных и подготовки действий преобразования данных в службах вычислений, например HDInsight. Она размещается в среде Azure и поддерживает подключение к ресурсам в общей сетевой среде с общедоступными конечными точками.

В этом документе вы узнаете, как можно создать и настроить среду выполнения интеграции Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Среда выполнения интеграции Azure по умолчанию
По умолчанию в каждой фабрике данных на сервере есть среда выполнения интеграции Azure, которая поддерживает операции в облачном хранилище данных и вычислительные службы в общедоступной сети. Расположение среды выполнения интеграции Azure разрешается автоматически. Если свойство **connectVia** не задано в определении связанной службы, используется среда выполнения интеграции Azure по умолчанию. Среду выполнения интеграции Azure необходимо явно создавать, если нужно явно определить ее расположение или если нужно виртуально группировать выполнения действий в разных средах выполнения интеграции для управления. 

## <a name="create-azure-ir"></a>Создание среды выполнения интеграции Azure
Integration Runtime можно создать с помощью командлета PowerShell **Set-AzDataFactoryV2IntegrationRuntime** . Чтобы создать среду выполнения интеграции Azure, необходимо указать имя, расположение и тип команды. Ниже приведен пример команды для создания среды выполнения интеграции Azure с заданным расположением "Западная Европа":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Для типа среды выполнения интеграции Azure необходимо задать значение **Управляемый**. Указывать детальные сведения вычисления не нужно, так как служба эластично полностью управляется в облаке. Если нужно создать среду выполнения интеграции Azure SSIS, укажите такие сведения о вычислении, как размер и число узлов. Дополнительные сведения см. в статье [Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure](create-azure-ssis-integration-runtime.md).

Вы можете настроить существующую Azure IR, чтобы изменить ее расположение с помощью командлета PowerShell Set-AzDataFactoryV2IntegrationRuntime. Дополнительные сведения о расположении среды выполнения интеграции Azure см. в статье [Integration runtime in Azure Data Factory](concepts-integration-runtime.md) (Среда выполнения интеграции в фабрике данных Azure).

## <a name="use-azure-ir"></a>Использование среды выполнения интеграции Azure

После создания среды выполнения интеграции Azure ее можно использовать для определения связанной службы. Ниже представлен пример использования ссылки на среду выполнения интеграции Azure, созданную выше, из связанной службы хранилища Azure.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о создании других типов среды выполнения интеграции см. в следующих статьях:

- [Создание и настройка локальной среды выполнения интеграции](create-self-hosted-integration-runtime.md)
- [Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure](create-azure-ssis-integration-runtime.md)
 
