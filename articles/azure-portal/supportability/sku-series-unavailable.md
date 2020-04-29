---
title: Серия регионов или номеров SKU недоступна — Azure
description: Некоторые серии номеров SKU недоступны для выбранной подписки в этом регионе, что может потребовать запроса на поддержку по управлению подписками.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76843650"
---
# <a name="region-or-sku-unavailable"></a>Регион или номер SKU недоступен

В этой статье описывается, как устранить проблему с подпиской Azure, если для нее недоступен какой-либо регион или номер SKU виртуальной машины.

## <a name="symptoms"></a>Симптомы

При развертывании виртуальной машины появляется одно из приведенных ниже сообщений об ошибке.

```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

При покупке зарезервированных экземпляров виртуальных машин появляется одно из приведенных ниже сообщений об ошибке.

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

При создании запроса в службу поддержки для увеличения квоты вычислений для базовой среды регион или семейство SKU недоступны для выбора.

## <a name="solution"></a>Решение

Во-первых, рекомендуется рассмотреть альтернативный регион или номер SKU, который соответствует потребностям бизнеса.

Если вы не можете найти подходящий регион или номер SKU, создайте [запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) по **управлению подписками** , выполнив следующие действия.

1. В меню [портал Azure](https://portal.azure.com) выберите пункт **Справка и поддержка**. Затем выберите **Новый запрос в службу поддержки**.

1. В статье **основные**сведения для **типа проблемы**выберите **Управление подписками**.

1. Выберите **подписку** и введите краткое описание в **сводке**.

   ![Вкладка "Основные сведения" нового запроса на поддержку](./media/SKU-series-unavailable/support-request-basics.png)

1. В качестве **типа проблемы**выберите **выбрать тип проблемы**.

1. Для параметра **выбрать тип проблемы**выберите вариант, например, **не удается получить доступ к подписке или ресурсу** > **Моя проблема не указана выше**. Нажмите кнопку **Сохранить**.

   ![Укажите проблему для запроса](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Выберите **Далее: решения** для просмотра возможных решений. При необходимости нажмите кнопку **Далее: подробности** , чтобы продолжить.

1. Введите любые дополнительные сведения, которые вы можете предоставить, а также контактные данные.

1. Выберите **Review + create** (Просмотреть и создать). После проверки сведений выберите **создать** , чтобы создать запрос.

## <a name="send-us-your-suggestions"></a>Отправьте нам свои предложения

Мы всегда будем открывать Отзывы и предложения! Отправьте нам свои [предложения](https://feedback.azure.com/forums/266794-support-feedback). Кроме того, вы можете привлекать нас к нам на [форумах](https://social.msdn.microsoft.com/Forums/azure) [Twitter](https://twitter.com/azuresupport) или MSDN.

## <a name="learn-more"></a>Дополнительные сведения

[Часто задаваемые вопросы о поддержке Azure](https://azure.microsoft.com/support/faq)
