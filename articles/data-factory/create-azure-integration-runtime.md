---
title: Создание времени выполнения интеграции Azure в фабрике данных Azure
description: Узнайте, как создать среду выполнения интеграции Azure в фабрике данных Azure, используемую для копирования данных и подготовки к отправке действий преобразования.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/13/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: e32530ece3626807b199850a2b4af5461ff51cde
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414076"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Создание и настройка среды выполнения интеграции Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Среда выполнения интеграции (IR) — это инфраструктура вычислений, которую фабрика данных Azure использует для обеспечения интеграции данных в разных сетевых средах. Дополнительные сведения о среде выполнения интеграции см. в статье [Integration runtime in Azure Data Factory](concepts-integration-runtime.md) (Среда выполнения интеграции в фабрике данных Azure).

Среда выполнения интеграции Azure обеспечивает полностью управляемый вычислительный процесс для выполнения операций перемещения данных и подготовки действий преобразования данных в службах вычислений, например HDInsight. Она размещается в среде Azure и поддерживает подключение к ресурсам в общей сетевой среде с общедоступными конечными точками.

В этом документе вы узнаете, как можно создать и настроить среду выполнения интеграции Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Среда выполнения интеграции Azure по умолчанию
По умолчанию в каждой фабрике данных на сервере есть среда выполнения интеграции Azure, которая поддерживает операции в облачном хранилище данных и вычислительные службы в общедоступной сети. Расположение среды выполнения интеграции Azure разрешается автоматически. Если свойство **connectVia** не задано в определении связанной службы, используется среда выполнения интеграции Azure по умолчанию. Среду выполнения интеграции Azure необходимо явно создавать, если нужно явно определить ее расположение или если нужно виртуально группировать выполнения действий в разных средах выполнения интеграции для управления. 

## <a name="create-azure-ir"></a>Создание среды выполнения интеграции Azure

Для создания и настройки ИК Azure можно использовать следующие процедуры.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Создание ИК Azure через Azure PowerShell
Интеграция Runtime может быть создана с помощью **Set-AzDataFactoryV2IntegrationRuntime** PowerShell cmdlet. Чтобы создать ИК Azure, вы укажете имя, местоположение и тип команды. Ниже приведен пример команды для создания среды выполнения интеграции Azure с заданным расположением "Западная Европа":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Для типа среды выполнения интеграции Azure необходимо задать значение **Управляемый**. Указывать детальные сведения вычисления не нужно, так как служба эластично полностью управляется в облаке. Если нужно создать среду выполнения интеграции Azure SSIS, укажите такие сведения о вычислении, как размер и число узлов. Дополнительные сведения см. в статье [Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure](create-azure-ssis-integration-runtime.md).

Можно настроить существующую ИК Azure, чтобы изменить его местоположение с помощью cmdlet Set-AzDataFactoryV2IntegrationRuntime PowerShell. Дополнительные сведения о расположении среды выполнения интеграции Azure см. в статье [Integration runtime in Azure Data Factory](concepts-integration-runtime.md) (Среда выполнения интеграции в фабрике данных Azure).

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Создание ИК-иК Azure через uI-иноматизм Azure Data Factory
Используйте следующие шаги для создания ИК-иК Azure с помощью ui-мотоисвязи Azure Data Factory.

1. На странице **«Давайте начнем»** в uI Azure Data Factory выберите вкладку **Автора** на левой панели.

   ![Кнопка "Автор домашней страницы"](media/doc-common-process/get-started-page-author-button.png)

1. Выберите **соединения** в нижней части левой панели и выберите **время выполнения интеграции** в окне **соединения.** Выберите **новый .**

   ![Создание среды выполнения интеграции](media/create-azure-integration-runtime/new-integration-runtime.png)

1. На странице **настройки времени запуска интеграции** выберите **Azure, Self-Hosted,** а затем выберите **Продолжить**. 

1. На следующей странице выберите **Azure** для создания ИК Azure, а затем выберите **Продолжить**—
   ![Создание среды выполнения интеграции](media/create-azure-integration-runtime/new-azure-ir.png)

1. Введите имя для ИК Azure и выберите **«Создать».**
   ![Создание ИК Azure](media/create-azure-integration-runtime/create-azure-ir.png)

1. По завершении создания появится всплывающее уведомление. На странице **«Время выполнения интеграции»** убедитесь, что вы видите вновь созданный ИК в списке.

## <a name="use-azure-ir"></a>Использование среды выполнения интеграции Azure

После создания среды выполнения интеграции Azure ее можно использовать для определения связанной службы. Ниже представлен пример использования ссылки на среду выполнения интеграции Azure, созданную выше, из связанной службы хранилища Azure.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
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

- [Создание автономного времени выполнения интеграции](create-self-hosted-integration-runtime.md)
- [Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure](create-azure-ssis-integration-runtime.md)
 
