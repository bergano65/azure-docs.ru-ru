---
title: Сетевые подключения увеличение лимита | Документация Майкрософт
description: Увеличение лимита сети
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297334"
---
# <a name="networking-limit-increase"></a>Увеличение лимита сети

Чтобы просмотреть текущее использование сети и квоты, вы можете посетить **использование + Квота** колонка на портале Azure. Можно также использовать использования [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) или [использование API сети](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) Чтобы просмотреть использование сети и ограничения.

Можно попросить увеличить ее через **Справка и поддержка** колонке или **использование + Квота** колонки на портале.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Запросить увеличение квоты сети на уровне с помощью подписки **Справка и поддержка** колонке

Следуйте приведенным ниже инструкциям для создания запроса на обслуживание, доступных на портале Azure по колонке «Справка и поддержка» в Azure. 

1. Из https://portal.azure.com выберите **Справка и поддержка**.

    ![Справка и поддержка](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Выберите **Новый запрос в службу поддержки**. 

    ![Новый запрос на техническую поддержку](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. В раскрывающемся списке Тип проблемы, выберите **ограничения службы и подписки (квоты)** .

    ![Раскрывающийся список типов проблема](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Выберите подписку, которая требует увеличенную квоту.

    ![Выберите newSR подписки](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Выберите **сети** в **тип квоты** раскрывающегося списка. 

    ![Выберите тип квоты](./media/networking-quota-request/select-quota-type-network.png)

6. В **сведения о проблеме**, содержат дополнительные сведения о процесс ваш запрос, нажав кнопку **сведениями**.

    ![Укажите сведения](./media/resource-manager-core-quotas-request/provide-details.png)

7. В **сведения о квотах** панели, выберите модель, расположение и ресурсы, которые вы хотите запросить увеличение для развертывания.

    ![Сведения о квотах DM](./media/networking-quota-request/quota-details-network.png)

8.  Введите желаемые ограничения для подписки. Чтобы удалить строку, снимите флажок ресурсов из раскрывающегося списка ресурсов или щелкните значок отмены «x». После ввода требуемой квоты для каждого ресурса, нажмите кнопку **сохранить и продолжить** на панели сведений квоты, чтобы продолжить создание запроса на поддержку.

    ![Новые ограничения](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Запросить увеличение квоты сети на уровне с помощью подписки **использование + Квота** колонке

Следуйте инструкциям ниже, используя для создания запроса на обслуживание с помощью Azure «использование + Квота» доступных на портале Azure. 

1. В https://portal.azure.com выберите **Подписки**.

    ![Подписки](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Выберите подписку, которая требует увеличенную квоту.

    ![Выберите подписку.](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Выберите **Использование и квоты**.

    ![Использование и квоты](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. В правом верхнем углу выберите **Запросить увеличение**.

    ![Запросить увеличение](./media/resource-manager-core-quotas-request/request-increase.png)

5. Выполните действия, начиная с шага 3 из *запроса сети увеличение квоты на уровне подписки* разделе с помощью **Справка и поддержка** колонке раздел

## <a name="about-networking-limits"></a>О ограничения сети

Дополнительные сведения о ограничения сети, см. в разделе [раздел "Сеть"](../azure-subscription-service-limits.md#networking-limits) страницы ограничения или вопросы и ответы ограничения сети