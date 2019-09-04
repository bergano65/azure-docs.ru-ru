---
title: Создание Cognitive Services ресурса в портал Azure
titleSuffix: Azure Cognitive Services
description: Начните работу с Cognitive Services Azure, создав подписку на ресурс в портал Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274686"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Создание Cognitive Services ресурса с помощью портал Azure

Используйте это краткое руководство для создания ресурса Azure Cognitive Services с помощью портал Azure. После успешного создания Cognitive Services ресурса вы получите конечную точку и ключ, которые можно использовать для проверки подлинности приложений.

## <a name="prerequisites"></a>Предварительные требования

* Допустимая подписка Azure — [создайте ее бесплатно](https://azure.microsoft.com/free/).

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Создание нового ресурса Azure Cognitive Services

Перед созданием ресурса Cognitive Services необходимо иметь группу ресурсов Azure, в которой будет содержаться ресурс. При создании нового ресурса можно либо создать новую группу ресурсов, либо использовать существующую. В этой статье показано, как создать новую группу ресурсов.

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните **+Создать ресурс**.

    ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Как упоминалось ранее, можно создать ресурс Cognitive Services двумя способами — с помощью ресурса нескольких служб или ресурса одной службы.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Ресурс с несколькими службами](#tab/multiservice)

    Чтобы создать ресурс с несколькими службами, введите **Cognitive Services** в строке поиска.

    ![Поиск по запросу "Cognitive Services"](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    На странице Cognitive Services выберите **создать**.

    ![Создание учетной записи Cognitive Services](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[Ресурс с одной службой](#tab/singleservice)

    Чтобы просмотреть все доступные средства, выберите **AI + машинное обучение**в **Azure Marketplace**. Если вы не видите интересующую вас службу, щелкните **Показать все** и прокрутите до **Cognitive Services**. Щелкните **Подробнее** , чтобы просмотреть весь каталог Cognitive Services.

    Выбрав интересующую вас службу, нажмите кнопку **создать**.
    
    ![Выбор API-интерфейсов Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. На странице **Создание** введите приведенные ниже сведения.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Ресурс с несколькими службами](#tab/multiservice)

    |    |    |
    |--|--|
    | **Name** | Описательное имя для ресурса Cognitive Services. Например, *микогнитивесервицесресаурце*. |
    | **Подписка** | Выберите одну из доступных подписок Azure. |
    | **Location** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. Запомните расположение Azure, так как оно может потребоваться при вызове Cognitive Services Azure. |
    | **Ценовая категория** | Затраты на учетную запись Cognitive Services зависят от выбранных параметров и использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
    | **Группа ресурсов** | Группа ресурсов Azure, которая будет содержать ресурс Cognitive Services. Вы можете создать новую группу или добавить к имеющейся группе. |

    ![Снимок экрана создания ресурса](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Нажмите кнопку **Создать**.

    #### <a name="single-service-resourcetabsingleservice"></a>[Ресурс с одной службой](#tab/singleservice)

    |    |    |
    |--|--|
    | **Name** | Описательное имя для ресурса Cognitive Services. Например, *текстаналитиксресаурце*. |
    | **Подписка** | Выберите одну из доступных подписок Azure. |
    | **Location** | Расположение экземпляра Cognitive Service. Разные расположения могут увеличивать задержку, но не влияют на доступность среды выполнения ресурса. Запомните расположение Azure, так как оно может потребоваться при вызове Cognitive Services Azure. |
    | **Ценовая категория** | Затраты на учетную запись Cognitive Services зависят от выбранных параметров и использования. Дополнительные сведения см. на [странице с ценами](https://azure.microsoft.com/pricing/details/cognitive-services/) API-интерфейсов.
    | **Группа ресурсов** | Группа ресурсов Azure, которая будет содержать ресурс Cognitive Services. Вы можете создать новую группу или добавить к имеющейся группе. |

    ![Снимок экрана создания ресурса](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Нажмите кнопку **Создать**.

    ***

## <a name="get-the-keys-for-your-resource"></a>Получение ключей для ресурса

После успешного создания ресурса вы получите всплывающее уведомление в правом верхнем углу экрана. В уведомлении щелкните **Переход к ресурсу** , чтобы просмотреть созданный вами ресурс службы. 

![Переход к ресурсу службы "автообслуживание"](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

В открывшейся области быстрого запуска можно получить доступ к конечной точке и ключу.

![Получить ключ и конечную точку](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Ценовые категории и выставление счетов

Ценовые категории (и сумма, за которую взимается плата) основываются на количестве отправленных вами транзакций с использованием данных проверки подлинности. Каждая ценовая категория определяет:
* Максимальное число разрешенных транзакций в секунду (TPS).
* функции службы, включенные в ценовой категории.
* Стоимость предопределенного количества транзакций. При превышении этого объема взимается плата за дополнительную плату, указанную в [сведениях о ценах](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) для вашей службы.

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
