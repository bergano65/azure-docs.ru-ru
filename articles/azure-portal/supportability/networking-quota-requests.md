---
title: Увеличение лимита сети Документы Майкрософт
description: Увеличение лимита сети
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547811"
---
# <a name="networking-limit-increase"></a>Увеличение лимита сети

Используйте [портал Azure,](https://portal.azure.com) чтобы увеличить квоту сетей.

Чтобы просмотреть текущее использование сети и квоту на портале Azure, откройте подписку, а затем выберите **квоты Usages и** Вы также можете использовать следующие параметры для просмотра использования сети и ограничений.

* [Использование CLI](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [API использования сети](/rest/api/virtualnetwork/virtualnetworks/listusage)

Вы можете запросить увеличение, используя **справку и поддержку** или в **использовании квот на** портале.

> [!Note]
> Чтобы изменить размер **общедоступных префиксов IP**по умолчанию, выберите **длину префикса Min Public IP InterNetwork** из списка выпадающих.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>Запрос сетевой квоты увеличение на уровне подписки с помощью справки и поддержки

Следуйте инструкциям ниже, чтобы создать запрос на поддержку, используя **поддержку Справки и на** портале Azure.

1. Вопийте на [портале Azure,](https://portal.azure.com)а затем выберите **поддержку справки** из меню портала Azure или ищите и выберите **поддержку Справки.**

    ![Справка и поддержка](./media/networking-quota-request/help-plus-support.png)

1. Выберите **новый запрос на поддержку**.

    ![Новый запрос на техническую поддержку](./media/networking-quota-request/new-support-request.png)

1. Для **типа выпуска**выберите ограничения **службы и подписки (квоты).**

    ![Выберите ограничения подписки из отбрасываемого типа проблемы](./media/networking-quota-request/select-quota-issue-type.png)

1. Выберите подписку, которая требует увеличенную квоту.

    ![Выберите подписку newSR](./media/networking-quota-request/select-subscription-support-request.png)

1. В **типе квоты**выберите **Сеть**. Выберите **далее: Решения**.

    ![Выберите тип квоты](./media/networking-quota-request/select-quota-type-network.png)

1. В **PROBLEM DETAILS**выберите **предоставить подробную информацию** и заполнить дополнительную информацию, чтобы помочь обработать ваш запрос.

    ![Предоставить подробную информацию](./media/networking-quota-request/provide-details-link.png)

1. В панели **деталей квоты** выберите модель развертывания, местоположение и ресурсы для включения в запрос.

    ![Квота Подробная информация DM](./media/networking-quota-request/quota-details-network.png)

1. Введите желаемые ограничения для подписки. Чтобы удалить строку, отображайте ресурс из меню **Ресурсов** или выберите значок отбрасываемого "x". После ввода квоты для каждого ресурса выберите **Сохранить и продолжить** работу с созданием запроса поддержки.

    ![Новые границы](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>Запрос сетевой квоты увеличение на уровне подписки с использованием Usages и квоты

Следуйте этим инструкциям для создания запроса на поддержку, используя **квоту «Использование»** на портале Azure.

1. От, https://portal.azure.comпоиск и выберите **Подписки**.

    ![Подписки](./media/networking-quota-request/search-for-suscriptions.png)

1. Выберите подписку, которая требует увеличенную квоту.

    ![Выбор подписки](./media/networking-quota-request/select-subscription-change-quota.png)

1. Выберите **Квоты использования**

    ![Использование и квоты](./media/networking-quota-request/select-usage-plus-quotas.png)

1. В правом верхнем углу выберите **увеличение запроса.**

    ![Запросить увеличение](./media/networking-quota-request/request-increase-from-subscription.png)

1. Выполните шаги, начиная с шага 3 в [увеличении квоты Сети Запроса на уровне подписки.](#request-networking-quota-increase-at-subscription-level-using-help--support)

## <a name="about-networking-limits"></a>О пределах сетей

Чтобы узнать больше о сетевых ограничениях, смотрите [раздел Сети](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) страницы limits или наши задаваемые вопросы для сетевых ограничений.
