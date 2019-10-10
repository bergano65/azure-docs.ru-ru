---
title: Серия SKU недоступна | Документация Майкрософт
description: Некоторые серии SKU могут быть недоступны для выбранной подписки в определенном регионе.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: e317ae1ad88cf162f1d55a06d19e7b3b0b88ce60
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249049"
---
# <a name="region-or-sku-unavailable"></a>Регион или номер SKU недоступен
В этой статье описывается, как устранить проблему с подпиской Azure, если для нее недоступен какой-либо регион или номер SKU виртуальной машины.

## <a name="symptoms"></a>Проблемы

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>При развертывании виртуальной машины появляется одно из приведенных ниже сообщений об ошибке.
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>При покупке зарезервированных экземпляров виртуальных машин появляется одно из приведенных ниже сообщений об ошибке.

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>При создании запроса в службу поддержки для увеличения квоты вычислительных ядер регион или семейство номеров SKU недоступны для выбора.

## <a name="solution"></a>Решение
Во-первых, рекомендуется рассмотреть альтернативный регион или номер SKU, который соответствует потребностям бизнеса. Если вам не удалось найти подходящий регион или номер SKU, создайте [запрос в службу поддержки](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) типа "Управление подпиской", выполнив следующие действия.


- На странице "Основные данные" выберите тип проблемы "Управление подпиской", выберите подписку и нажмите кнопку "Далее".

![Колонка «Основные»](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   На странице "Проблема" выберите тип проблемы "Other General Questions" (Другие общие вопросы).
- В разделе "Сведения" сделайте следующее.
  - Укажите, если хотите ли вы развернуть виртуальные машины или приобрести зарезервированные экземпляры виртуальных машин.
  - Укажите регион, номер SKU и количество экземпляров виртуальной машины, которые вы планируете развернуть или купить.


![Проблема](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Введите контактные сведения и нажмите кнопку "Создать".

![Контактные данные](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Отзыв
Мы всегда рады вашим отзывам и предложениям! Отправьте нам свои [предложения](https://feedback.azure.com/forums/266794-support-feedback). Кроме того, с нами можно связаться через [Twitter](https://twitter.com/azuresupport) или [форумы MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Подробнее
[Часто задаваемые вопросы о поддержке Azure](https://azure.microsoft.com/support/faq)

