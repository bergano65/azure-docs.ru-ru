---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503636"
---
Приведенные ниже команды Azure CLI предназначены для подготовки ресурса "Детектор аномалий" ценовой категории "Бесплатный". Нажмите кнопку **Попробовать**, вставьте код и нажмите клавишу ВВОД, чтобы выполнить его в Azure Cloud Shell. Отобразятся ключи проверки подлинности для приложения. По завершении [создайте переменную среды](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) для любого из своих ключей с именем `ANOMALY_DETECTOR_KEY`.

> [!NOTE]
> * При необходимости можно:
>    * создать ресурс с помощью [портала Azure](../articles/cognitive-services/cognitive-services-apis-create-account.md) или [Azure CLI](../articles/cognitive-services/cognitive-services-apis-create-account.md) на локальном компьютере;
>    * получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней (после регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/));
>    * просмотреть этот ресурс на [портале Azure](https://portal.azure.com/). 

Ресурсами Azure, которые вы подготавливаете, будет представлено семейство служб Cognitive Services. Каждая учетная запись Cognitive Services (и связанные с ней ресурсы Azure) должна принадлежать к группе ресурсов Azure.

1. создание группы ресурсов Azure;

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. Создание ресурса "Детектор аномалий" ценовой категории "Бесплатный"

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. Вывод списка ключей для ресурса

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```