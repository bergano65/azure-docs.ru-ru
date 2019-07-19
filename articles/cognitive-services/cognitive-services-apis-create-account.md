---
title: Создание Cognitive Services ресурса в портал Azure
titlesuffix: Azure Cognitive Services
description: Начните работу с Cognitive Services Azure, создав подписку на ресурс в портал Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: e9616f3014288e4b2580f474c49c646928db5a08
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334252"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Создание Cognitive Services ресурса с помощью портал Azure

Используйте это краткое руководство, чтобы приступить к работе с Azure Cognitive Services с помощью портал Azure. Cognitive Services представлены [ресурсами](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure, созданными в подписке Azure. После создания ресурса используйте ключи и конечную точку, созданные для проверки подлинности приложений. 

## <a name="prerequisites"></a>Предварительные требования

* Допустимая подписка Azure. [создайте ее бесплатно](https://azure.microsoft.com/free/) .

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Создание нового ресурса Azure Cognitive Services

Перед созданием ресурса Cognitive Services необходимо иметь группу ресурсов Azure, в которой будет содержаться ресурс. При создании нового ресурса можно либо создать новую группу ресурсов, либо использовать существующую. В этой статье показано, как создать новую группу ресурсов.

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните **+Создать ресурс**.

    ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Доступные Cognitive Services можно найти следующими способами.
    * Используйте панель поиска и введите имя службы, на которую вы хотите подписываться.
        * Чтобы создать ресурс с несколькими службами, введите **Cognitive Services** в строке поиска и выберите ресурс **Cognitive Services** .

        ![Поиск по запросу "Cognitive Services"](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Чтобы просмотреть все доступные средства, выберите **AI + машинное обучение**в **Azure Marketplace**. Если вы не видите интересующую вас службу, щелкните **Показать все** и прокрутите до **Cognitive Services**. Щелкните **Дополнительно** , чтобы просмотреть весь каталог API-интерфейсы Cognitive Services.
    
        ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. На странице **Создание** введите приведенные ниже сведения.

    > [!IMPORTANT]
    > Запомните расположение Azure, так как оно может потребоваться при вызове Cognitive Services Azure.

    |    |    |
    |--|--|
    | **Name** | Описательное имя для ресурса Cognitive Services. Например, *микогнитивесервицесаккаунт*. |
    | **Подписка** | Выберите одну из доступных подписок Azure. |
    | **Location** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. |
    | **Ценовая категория** | Затраты на учетную запись Cognitive Services зависят от выбранных параметров и использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
    | **Группа ресурсов** | [Группа ресурсов Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group), которая будет содержать ресурс Cognitive Services. Вы можете создать новую группу или добавить к имеющейся группе. |

    ![Снимок экрана создания ресурса](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-resource"></a>Получение ключей для ресурса

После создания ресурса вы можете получить к нему доступ на панели мониторинга Azure, если она закреплена. В противном случае его можно найти в **Группе ресурсов**. Выбрав ресурс, можно получить ключи, выбрав **ключи** в разделе **Управление ресурсами**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При удалении группы ресурсов также удаляются все другие ресурсы, содержащиеся в этой группе.

Чтобы удалить группу ресурсов с помощью портала Azure, сделайте следующее:

1. На портале Azure разверните меню слева, чтобы открыть меню служб, и выберите **Resource Groups** (Группы ресурсов), чтобы просмотреть список групп ресурсов.
2. Выберите группу ресурсов для удаления и щелкните правой кнопкой мыши кнопку Дополнительно (...) в правой части списка.
3. Выберите **Удалить группу ресурсов** и подтвердите выбор.

## <a name="see-also"></a>См. также

* [Проверка подлинности запросов к Azure Cognitive Services](authentication.md)
* [Что такое Azure Cognitive Services?](Welcome.md)
* [Поддержка естественного языка](language-support.md)
* [Поддержка контейнеров DOCKER](cognitive-services-container-support.md)
