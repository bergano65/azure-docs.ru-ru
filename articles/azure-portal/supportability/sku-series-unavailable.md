---
title: Регион или Серия SKU недоступна - Azure
description: Некоторые серии SKU недоступны для выбранной подписки для этого региона, что может потребовать запроса поддержки управления подпиской.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
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

При создании запроса на поддержку для увеличения ключевой квоты регион или семейство SKU недоступны для выбора.

## <a name="solution"></a>Решение

Во-первых, рекомендуется рассмотреть альтернативный регион или номер SKU, который соответствует потребностям бизнеса.

Если вы не можете найти подходящий регион или SKU, создайте [запрос на поддержку управления](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) **подпиской** после следующих шагов:

1. Из меню [портала Azure](https://portal.azure.com) выберите **поддержку Справки .** Затем выберите **Новый запрос в службу поддержки**.

1. В **основах**, для **типа выпуска**, выберите управление **подпиской.**

1. Выберите **Подписку** и введите краткое описание в **Резюме**.

   ![Вкладка Основы запроса новой поддержки](./media/SKU-series-unavailable/support-request-basics.png)

1. Для **типа проблемы**выберите **тип проблемы.**

1. Для **типа проблемы Select**выберите опцию, например, не **удается получить доступ к моей подписке или ресурсу** > **Моя проблема не указана выше.** Нажмите кнопку **Сохранить**.

   ![Указать проблему для запроса](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Выберите **Далее: Решения** для изучения возможных решений. При необходимости выберите **Далее: Подробная информация** для продолжения.

1. Введите любую дополнительную информацию, которая вы можете предоставить, вместе с вашей контактной информацией.

1. Выберите **Review + create** (Просмотреть и создать). После проверки информации выберите **Создать** для создания запроса.

## <a name="send-us-your-suggestions"></a>Присылайте нам свои предложения

Мы всегда открыты для отзывов и предложений! Отправьте нам свои [предложения](https://feedback.azure.com/forums/266794-support-feedback). Кроме того, вы можете взаимодействовать с нами на [Twitter](https://twitter.com/azuresupport) или [msDN форумах](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Дополнительные сведения

[Часто задаваемые вопросы о поддержке Azure](https://azure.microsoft.com/support/faq)
