---
title: Настройка квот и ограничений в Azure Data Lake Analytics
description: Узнайте, как настроить и увеличить квоты в учетных записях Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c1d7c8cc4b50682a74ac88b2113f0d44ebc55199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644721"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Настройка квот и ограничений в Azure Data Lake Analytics

Узнайте, как настроить и увеличить квоту в учетных записях Azure Data Lake Analytics (ADLA). Знание этих ограничений поможет вам понять ваше поведение работы u-S'L. Все квоты являются мягкими, то есть максимальное ограничение можно увеличить, обратившись в службу поддержки Azure.

## <a name="azure-subscriptions-limits"></a>Ограничения подписок Azure

**Максимальное количество учетных записей Azure Data Lake Analytics в одной подписке:** 5

Если вы попытаетесь создать шестую учетную запись Azure Data Lake Analytics, то увидите сообщение о том, что достигнуто максимальное количество учетных записей Data Lake Analytics (5) для текущего региона и подписки.

Если вы хотите выйти за рамки этого ограничения, можно воспользоваться этими вариантами:
* Выберите другой подходящий регион.
* [Отправьте запрос в службу поддержки Azure](#increase-maximum-quota-limits) с запросом на увеличение квоты.

## <a name="default-adla-account-limits"></a>Ограничения учетной записи Azure Data Lake Analytics по умолчанию

**Максимальное количество единиц аналитики (AUs) на счет:** 250, по умолчанию 32

Это максимальное число единиц аналитики, которые могут выполняться параллельно в вашей учетной записи. Если общее количество единиц аналитики по всем выполняемым заданиям превысит это ограничение, новые задания будут автоматически помещаться в очередь. Пример:

* Если выполняется только одно задание на 32 единицы использования аналитики, то при отправке второго задания оно будет помещено в очередь, пока не завершится первое задание.
* Если выполняются четыре задания, каждое из которых использует по 8 единиц аналитики, то при отправке пятого задания, на которое нужно 8 единиц использования аналитики, оно будет ожидать в очереди заданий, пока не освободятся эти 8 единиц использования аналитики.

    ![Ограничения и страница квот Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Максимальное количество единиц аналитики (AUs) на работу:** 250, по умолчанию 32

Это максимальное количество AUs, которое может быть назначено в вашей учетной записи. Задания, для которых назначено больше этого предела, будут отклонены, если на отправителя не влияет политика расчета (лимит отправки задания), которая дает им больше единиц использования аналитики для каждого задания. Верхняя граница этого значения — это предел единиц аналитики для учетной записи.

**Максимальное число одновременно выполняемых заданий U-SQL на учетную запись:** 20.

Это максимальное число заданий, которые могут выполняться параллельно в вашей учетной записи. При достижении этого значения новые задания автоматически помещаются в очередь.

## <a name="adjust-adla-account-limits"></a>Регулировка ограничений учетной записи Azure Data Lake Analytics

1. Подпишитесь на [портал Azure](https://portal.azure.com).
2. Выберите существующую учетную запись ADLA.
3. Нажмите кнопку **Свойства**.
4. Отрегулируйте значения для **Максимум AU**, **Maximum number of running jobs** (Максимальное число выполняемых заданий) и **Пороговые значения для отправки заданий** по необходимости.

## <a name="increase-maximum-quota-limits"></a>Увеличение максимальной квоты

Дополнительные сведения об ограничениях Azure можно найти в разделе [Ограничения определенных служб](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Создайте запрос на поддержку на портале Azure.

    ![Azure Data Lake Analytics: страница на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics: страница на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Для типа проблемы укажите **Квота**.
3. Выберите **подписку** (убедитесь, что она не является "пробной" подпиской).
4. Выберите тип квоты **Data Lake Analytics**.

    ![Azure Data Lake Analytics: страница на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. На странице проблемы опишите необходимое увеличение квоты, а в поле **Сведения** укажите причины для такого увеличения.

    ![Azure Data Lake Analytics: страница на портале](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Проверьте введенные контактные данные и создайте запрос в службу поддержки.

Корпорация Майкрософт проверит запрос и удовлетворит потребности вашего бизнеса как можно скорее.

## <a name="next-steps"></a>Дальнейшие действия

* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Управление аналитикой озера данных Azure с помощью Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Мониторинг и устранение неполадок задания Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
