---
title: Серия регионов или номеров SKU недоступна
description: Некоторые серии номеров SKU недоступны для выбранной в регионе подписки, что может потребовать запроса на поддержку по управлению подписками.
author: stevendotwang
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: 2bbe1dff6bbf8fed481300db95482c46b0e8a7b7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745287"
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

При создании запроса на поддержку для увеличения квоты вычислительных ядер регион или семейство номеров SKU недоступны для выбора.

## <a name="solution"></a>Решение

Во-первых, рекомендуется рассмотреть альтернативный регион или номер SKU, который соответствует потребностям бизнеса.

Если вам не удалось найти подходящий регион или номер SKU, создайте **запрос на поддержку** типа [Управление подпиской](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), выполнив следующие действия.

1. В главном меню на [портале Azure](https://portal.azure.com) выберите **Справка + поддержка**. Затем выберите **Новый запрос в службу поддержки**.

1. На вкладке **Основы** в качестве **типа проблемы** выберите **Управление подпиской**.

1. Выберите **Подписку** и введите краткое описание в **Сводку**.

   ![Меню "Новый запрос в службу поддержки" — вкладка "Основы"](./media/SKU-series-unavailable/support-request-basics.png)

1. В разделе **Тип проблемы** выберите **Выбор типа проблемы**.

1. В разделе **Выбор типа проблемы** выберите вариант, например **Не удается получить доступ к подписке или ресурсу** > **Моя проблема не указана выше**. Щелкните **Сохранить**.

   ![Укажите проблему для запроса.](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. По завершении выберите **Next: решения** для просмотра возможных решений. При необходимости выберите **Далее: сведения**, чтобы продолжить.

1. Введите любые дополнительные сведения, которые вы можете предоставить, а также контактные данные.

1. Выберите **Review + create** (Просмотреть и создать). После проверки сведений выберите **Создать**, чтобы создать запрос.

## <a name="send-us-your-suggestions"></a>Отправляйте нам свои предложения

Мы всегда рады вашим отзывам и предложениям! Отправьте нам свои [предложения](https://feedback.azure.com/forums/266794-support-feedback). Кроме того, вы можете общаться с нами через [Twitter](https://twitter.com/azuresupport) или [Страницу вопросов в разделе вопросов и ответов на сайте Майкрософт](/answers/products/azure).

## <a name="learn-more"></a>Дополнительные сведения

[Часто задаваемые вопросы о поддержке Azure](https://azure.microsoft.com/support/faq)