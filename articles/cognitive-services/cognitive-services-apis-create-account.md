---
title: Создание учетной записи Cognitive Services на портале Azure
titlesuffix: Azure Cognitive Services
description: Как создать учетную запись API Cognitive служб Azure на портале Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: aahi
ms.openlocfilehash: b857ee0395c447c8699b8f6a812853528812a7bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445857"
---
# <a name="create-a-cognitive-services-account-using-the-azure-portal"></a>Создание учетной записи Cognitive Services с помощью портала Azure

В этом кратком руководстве вы узнаете, как зарегистрироваться в Azure Cognitive Services и создать учетную запись, имеющему подписку, одна служба или нескольких служб. Эти службы представляются [ресурсами](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure, которые позволяют подключиться к одному или нескольким API-интерфейсам Azure Cognitive Services.

## <a name="prerequisites"></a>Технические условия

* Действующая подписка Azure. [Создайте учетную запись](https://azure.microsoft.com/free/) бесплатно.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Создайте новый ресурс Azure Cognitive Services

Прежде чем создавать ресурс, необходимо иметь группу ресурсов Azure. Каждая учетная запись Cognitive Services (и его связанных ресурсов Azure) должны принадлежать к группе ресурсов Azure. При создании учетной записи, у вас есть параметр, чтобы создать новую группу ресурсов или использовать уже существующий. В этой статье показано, как создать группу ресурсов.

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните **+Создать ресурс**.

    ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Вы найдете доступных Cognitive Services с одним из следующих способов:
    * Используйте панель поиска и введите имя службы, которую вы хотите подписаться.
        * Чтобы создать ресурс для нескольких служб подписки, введите **Cognitive Services** при поиске панели и выберите **Cognitive Services** ресурсов.

        ![Поиск по запросу "Cognitive Services"](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Чтобы просмотреть все доступные службы cognitive services, выберите **искусственный Интеллект и машинное Обучение**в разделе **Azure Marketplace**. Если вы не видите службу, вас интересует, щелкнуть **см. в разделе, все** и прокрутите страницу до **Cognitive Services**. Нажмите кнопку **дополнительные** для просмотра всего каталога API-интерфейсов Cognitive Services.
    
        ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. На странице **Создание** введите приведенные ниже сведения.

    > [!IMPORTANT]
    > Не забывайте ваше расположение Azure, как вам может понадобиться при вызове Azure Cognitive Services.

    |    |    |
    |--|--|
    | **Имя** | Описательное имя для ресурса Cognitive Services. Рекомендуем использовать описательное имя, например *MyCognitiveServicesAccount*. |
    | **Подписка** | Выберите одну из доступных подписок Azure. |
    | **Location** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. |
    | **Ценовая категория** | Затраты на учетную запись Cognitive Services зависят от выбранных параметров и использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
    | **Группа ресурсов** | [Группа ресурсов Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group), которая будет содержать ресурс Cognitive Services. Вы можете создать новую группу или добавить к имеющейся группе. |

    ![Снимок экрана создания ресурса](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-subscription"></a>Получить ключи для вашей подписки

После создания ресурса вы можете получить к нему доступ на панели мониторинга Azure, если она закреплена. В противном случае его можно найти в **Группе ресурсов**. После выбора ресурса, ключи можно получить, выбрав **ключи** под **управления ресурсами**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, можно удалить ресурс или группу ресурсов. Удаление группы ресурсов также удаляет другие ресурсы, связанные с группой ресурсов.

Чтобы удалить группу ресурсов с помощью портала Azure, сделайте следующее:

1. На портале Azure разверните меню слева, чтобы открыть меню служб, и выберите **Resource Groups** (Группы ресурсов), чтобы просмотреть список групп ресурсов.
2. Найдите группу ресурсов, который нужно удалить и щелкните правой кнопкой мыши еще кнопку (...) справа от списка.
3. Выберите **Удалить группу ресурсов** и подтвердите выбор.

## <a name="see-also"></a>См. также

* [Проверка подлинности запросов к Azure Cognitive Services](authentication.md)
* [Что такое Azure Cognitive Services?](Welcome.md)
* [Поддержка естественного языка](language-support.md)
* [Поддержка контейнеров docker](cognitive-services-container-support.md)
