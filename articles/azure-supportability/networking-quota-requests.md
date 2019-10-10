---
title: Увеличение ограничения сети | Документация Майкрософт
description: Увеличение лимита сети
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 278e9ff68fa20a0a99a6447bb4cf7ac7fddbfb7b
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249111"
---
# <a name="networking-limit-increase"></a>Увеличение лимита сети

Чтобы просмотреть текущие сведения об использовании и квоте сети, можно посетить колонку **использование и квота** в портал Azure. Для просмотра использования и ограничений сети можно также использовать интерфейс [командной строки (CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) или [API использования сети](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) .

Вы можете запросить увеличение через колонку **Справка и поддержка** или колонку **использование и квота** на портале.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Запрос увеличения квоты сети на уровне подписки с помощью колонки " **Справка и поддержка** "

Следуйте приведенным ниже инструкциям, чтобы создать запрос на поддержку через колонку "Справка и поддержка" Azure, доступную в портал Azure. 

1. В https://portal.azure.com выберите **Справка и поддержка**.

    ![Справка и поддержка](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Выберите **Новый запрос в службу поддержки**. 

    ![Новый запрос на техническую поддержку](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. В раскрывающемся списке Тип проблемы выберите **пределы службы и подписки (квоты)** .

    ![Раскрывающийся список типов проблем](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Выберите подписку, которая требует увеличенную квоту.

    ![Выберите Новости подписки](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. В раскрывающемся списке **тип квоты** выберите **сеть** . 

    ![Выберите тип квоты](./media/networking-quota-request/select-quota-type-network.png)

6. В окне **сведения о проблеме**укажите дополнительные сведения, которые помогут обработать запрос, нажав кнопку **указать подробности**.

    ![Укажите сведения](./media/resource-manager-core-quotas-request/provide-details.png)

7. На панели **сведения о квоте** выберите модель развертывания, расположение и ресурсы, для которых необходимо запросить увеличение.

    ![Сведения о квоте DM](./media/networking-quota-request/quota-details-network.png)

8.  Введите желаемые ограничения для подписки. Чтобы удалить линию, снимите флажок с ресурса в раскрывающемся списке ресурсов или щелкните значок "отбросить" "x". После ввода требуемой квоты для каждого ресурса нажмите кнопку **сохранить и продолжить** на панели сведений о квотах, чтобы продолжить создание запроса на поддержку.

    ![Новые ограничения](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Запрос увеличения квоты сети на уровне подписки с использованием **использования + колонка квоты**

Следуйте приведенным ниже инструкциям по созданию запроса на поддержку через колонку "использование и квота" Azure, доступную в портал Azure. 

1. В https://portal.azure.com выберите **Подписки**.

    ![Подписки](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Выберите подписку, которая требует увеличенную квоту.

    ![Выберите подписку.](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Выберите **Использование и квоты**.

    ![Использование и квоты](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. В правом верхнем углу выберите **Запросить увеличение**.

    ![Запросить увеличение](./media/resource-manager-core-quotas-request/request-increase.png)

5. Выполните шаги, начиная с шага 3, из раздела *запрос увеличения квоты в сети на уровне подписки* в разделе **Справка и поддержка** .

## <a name="about-networking-limits"></a>Ограничения сети

Дополнительные сведения об ограничениях сети см. в [разделе "сети](../azure-subscription-service-limits.md#networking-limits) " страницы "ограничения" или "ограничения сети".