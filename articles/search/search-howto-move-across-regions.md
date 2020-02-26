---
title: Как переместить ресурс службы в регионы
titleSuffix: Azure Cognitive Search
description: В этой статье показано, как переместить ресурсы Azure Когнитивный поиск из одного региона в другой в облаке Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599305"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Перемещение службы Когнитивный поиск Azure в другой регион Azure

Чтобы переместить учетную запись службы для работы с приложением Azure из одного региона в другой, создайте шаблон экспорта, который будет перемещать ваши подписки. После перемещения подписки необходимо переместить данные и повторно создать службу.

В этой статье вы узнаете, как выполнять следующие задачи.

> [!div class="checklist"]
> * Экспорт шаблона.
> * Изменение шаблона: Добавление целевых регионов, поиска и имен учетных записей хранения.
> * Разверните шаблон, чтобы создать новый поиск и учетные записи хранения.
> * Проверка состояния службы в новом регионе
> * Очистка ресурсов в исходном регионе.

## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что службы и функции, используемые вашей учетной записью, поддерживаются в целевом регионе.

- Для функций предварительной версии убедитесь, что ваша подписка список разрешений для целевого региона. Дополнительные сведения о функциях предварительной версии см. в [статье хранилища знаний](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [добавочное](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)дополнение и [Частная конечная точка](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## <a name="assessment-and-planning"></a>Оценка и планирование

При перемещении службы поиска в новый регион необходимо [переместить данные в новую службу хранилища](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) , а затем перестроить индексы, навыков и хранилища знаний. Следует записывать текущие параметры и копировать JSON файлы, чтобы упростить и ускорить перестроение службы.

## <a name="moving-your-search-services-resources"></a>Перемещение ресурсов службы поиска

Для начала будет экспортирован и затем изменен шаблон диспетчер ресурсов.

### <a name="export-a-template"></a>Экспорт шаблона

1. Войдите на [портал Azure](https://portal.azure.com).

2. Перейдите на страницу группы ресурсов.

> [!div class="mx-imgBorder"]
> Пример страницы ![группы ресурсов](./media/search-move-resource/export-template-sample.png)

3. Щелкните **Все ресурсы**.

3. В меню навигации слева выберите **Экспорт шаблона**.

4. На странице **Экспорт шаблона** нажмите кнопку **загрузить** .

5. Выберите ZIP-файл, скачанный с портала, и распакуйте его в выбранную папку.

ZIP-файл содержит JSON-файлы, составляющие шаблон и скрипты для развертывания шаблона.

### <a name="modify-the-template"></a>Изменение шаблона

Шаблон будет изменен путем изменения имен и регионов для поиска и учетной записи хранения. Имена должны соответствовать правилам для каждого соглашения об именовании служб и регионов. 

Чтобы получить коды расположения регионов, см. раздел [расположения Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Код для региона — это имя региона без пробелов, **Центральная американская** = **centralus**.

1. На портале Azure выберите **Создать ресурс**.

2. В строке **Поиск в Marketplace** введите **развертывание шаблона** и нажмите клавишу **ВВОД**.

3. Выберите **Развертывание шаблона**.

4. Выберите **Создать**.

5. Выберите **Создать собственный шаблон в редакторе**.

6. Выберите **загрузить файл**и следуйте инструкциям по загрузке файла **template. JSON** , скачанного и распакованного в предыдущем разделе.

7. В файле **template. JSON** назовите целевой поиск и учетные записи хранения, задав значения по умолчанию для имен учетных записей хранения и поиска. 

8. Измените свойство **Location** в файле **template. JSON** на целевой регион для служб поиска и хранения. В этом примере целевому региону присваивается значение `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
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

### <a name="deploy-the-template"></a>Развертывание шаблона

1. Сохраните файл **template. JSON** .

2. Введите или выберите значения свойств:

- **Подписка**— выберите подписку Azure.

- **Группа ресурсов**: щелкните **Создать** и укажите имя группы ресурсов.

- **Расположение**. Выберите расположение Azure.

3. Установите флажок **я принимаю указанные выше условия** и нажмите кнопку **выбрать покупку** .

## <a name="verifying-your-services-status-in-new-region"></a>Проверка состояния служб в новом регионе

Чтобы проверить перемещение, откройте новую группу ресурсов, и ваши службы будут перечислены в новом регионе.

Чтобы переместить данные из исходного региона в целевой, ознакомьтесь с рекомендациями по [перемещению данных в новую учетную запись хранения](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account)в этой статье.

## <a name="clean-up-resources-in-your-original-region"></a>Очистка ресурсов в исходном регионе

Чтобы сохранить изменения и завершить перемещение учетной записи службы, удалите учетную запись исходной службы.

## <a name="next-steps"></a>Следующие шаги

[Создание индекса](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Создание набора навыков](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Создание хранилища знаний](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

