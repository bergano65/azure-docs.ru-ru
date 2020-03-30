---
title: Как переместить ресурс службы в разных регионах
titleSuffix: Azure Cognitive Search
description: В этой статье будет уотена способы перемещения ресурсов Azure Cognitive Search из одного региона в другой в облаке Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/24/2020
ms.openlocfilehash: 00f16d11f7a9cd276772eda5e91d6e117ada8c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246309"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Переместите службу когнитивного поиска Azure в другой регион Azure

Иногда клиенты спрашивают о перемещении поисковой службы в другой регион. В настоящее время нет встроенного механизма или инструмента, чтобы помочь с этой задачей, но эта статья может помочь вам понять ручные шаги для достижения того же результата.

> [!NOTE]
> На портале Azure все службы имеют команду **шаблонов Экспорта.** В случае Azure Cognitive Search эта команда производит базовое определение службы (имя, местоположение, уровень, реплика цимбистая и количество разделов), но не распознает содержимое службы и не переносит клавиши, роли или журналы. Несмотря на существование команды, мы не рекомендуем использовать ее для перемещения поисковой службы.

## <a name="guidance-for-moving-a-service"></a>Руководство по перемещению службы

1. Определите зависимости и связанные с ними службы, чтобы понять все последствия перемещения службы, в случае необходимости перемещения не только azure Cognitive Search.

   Azure Storage используется для ведения журнала, создания хранилища знаний и является широко используемым внешним источником данных для обогащения и индексирования ИИ. Когнитивные услуги зависят от обогащения ИИ. Как Cognitive Services, так и служба поиска должны находиться в одном регионе, если вы используете обогащение ИИ.

1. Создайте инвентаризацию всех объектов службы, чтобы вы знали, что двигаться: индексы, синонимы карты, индексы, источники данных, навыки. Если вы включили журнал, создайте и архивируйте любые отчеты, которые могут понадобиться для исторической записи.

1. Проверьте цены и доступность в новом регионе, чтобы обеспечить доступность Azure Cognitive Search плюс любые сопутствующие услуги в новом регионе. Большинство функций доступны во всех регионах, но некоторые функции предварительного просмотра имеют ограниченную доступность.

1. Создайте службу в новом регионе и переиздайте из исходного кода любые существующие индексы, синонимы карты, индексы, источники данных и навыки. Помните, что имена служб должны быть уникальными, поэтому вы не можете повторно использовать существующее имя. Проверьте каждый набор навыков, чтобы увидеть, если подключения к когнитивным службам по-прежнему действительны с точки зрения требований одного региона. Кроме того, если создаются хранилища знаний, проверьте строки подключения для Azure Storage, если вы используете другую службу.

1. Перезагрузить индексы и хранилища знаний, если это применимо. Вы будете использовать код приложения для втягивания данных JSON в индекс, либо перезапускать индексы для вытягивания документов из внешних источников. 

1. Включите журнал, и если вы используете их, воссоздайте роли безопасности.

1. Обновление клиентских приложений и наборов тестов для использования нового имени службы и ключей API, а также тестирование всех приложений.

1. Удалите старую службу после полного тестирования и работы новой службы.

## <a name="next-steps"></a>Дальнейшие действия

Следующие ссылки могут помочь вам найти более подробную информацию при выполнении шагов, изложенных выше.

+ [Цены на когнитивный поиск Azure и регионы](https://azure.microsoft.com/pricing/details/search/)
+ [Выбор уровня](search-sku-tier.md)
+ [Создание поисковой службы](search-create-service-portal.md)
+ [Документы поиска загрузки](search-what-is-data-import.md)
+ [Включение ведения журналов](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->