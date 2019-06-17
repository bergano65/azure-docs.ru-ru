---
title: Включите решение Azure для VMware CloudSimple службы
description: Описывается, как включить службу CloudSimple для подписки Azure, а затем зарегистрировать поставщик ресурсов CLoudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676943"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Регистрация поставщика ресурсов Microsoft.VMwareCloudSimple в вашей подписке Azure

Служба CloudSimple позволяет использовать решения Azure VMware с CloudSimple. Чтобы использовать службу CloudSimple, его сначала необходимо включить в вашей подписке Azure. Затем можно зарегистрировать Microsoft.VMwareCloudSimple службы как поставщик ресурсов.

## <a name="enable-the-cloudsimple-service"></a>Включите службу CloudSimple

Чтобы включить службу CloudSimple в вашей подписке Azure, откройте запрос в службу поддержки с [поддержки Майкрософт](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Выберите следующие параметры при отправке запроса.

* Тип проблемы. **Технические требования**
* Подписка: **Идентификатор подписки**
* Тип службы: **Решение VMware, CloudSimple**
* Тип проблемы: **Квота выделенных узлов**
* Подтип проблемы: **Увеличьте квоту выделенных узлов**
* Субъект: **Включение службы CloudSimple**

Можно также обратиться к своему представителю корпорации Майкрософт в [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com). Укажите идентификатор своей подписки Azure в сообщении электронной почты.  

После включения службы CloudSimple для вашей подписки, вы можете включить поставщик ресурсов в подписке.

## <a name="register-the-resource-provider"></a>Регистрация поставщика ресурсов

1. Войдите на [портале Azure](https://portal.azure.com).

2. Выбор пункта **Все службы**.

3. Найдите и выберите **подписок**.

    ![Выбор пункта "Подписки"](media/cloudsimple-service-select-subscriptions.png)

4. Выберите подписку, на котором вы хотите включить службу CloudSimple.

5. Нажмите кнопку **поставщиков ресурсов** для подписки.

6. Используйте **Microsoft.VMwareCloudSimple** для фильтрации поставщика ресурсов.

7. Выберите поставщика ресурсов и нажмите кнопку **зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [создать службу CloudSimple](create-cloudsimple-service.md)
* Узнайте, как [Настройка среды с частным облаком](quickstart-create-private-cloud.md)