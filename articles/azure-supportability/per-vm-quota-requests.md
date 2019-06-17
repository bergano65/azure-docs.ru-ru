---
title: Azure на виртуальную Машину увеличение квоты виртуальных ЦП запросы | Документация Майкрософт
description: на виртуальной Машине квоту на виртуальные ЦП запросы на увеличение
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: f921b4a95c1b0cfb29d84c0bacc17d268af6e6c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082824"
---
# <a name="vm-series-vcpu-limit-increase"></a>Увеличение лимита ЦП серии виртуальных Машин

Теперь вы можете запросить увеличение через **Справка и поддержка** колонке или **использование + Квота** колонки на портале. 

## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Запрос на увеличение квоты виртуальных ЦП виртуальной Машины на уровне с помощью подписки **Справка и поддержка** колонке

Следуйте приведенным ниже инструкциям для создания запроса на обслуживание, доступных на портале Azure по колонке «Справка и поддержка» в Azure. 

1. Из https://portal.azure.com выберите **Справка и поддержка**.

![Справка и поддержка](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Выберите **Новый запрос в службу поддержки**. 

![Новый запрос на техническую поддержку](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. В раскрывающемся списке Тип проблемы, выберите **ограничения службы и подписки (квоты)** .

![Раскрывающийся список типов проблема](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Выберите подписку, которая требует увеличенную квоту.

![Выберите newSR подписки](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Выберите **вычислений лимита подписки - VM (ядер ЦП) увеличивает** в **тип квоты** раскрывающегося списка. 

![Выберите тип квоты](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. В **сведения о проблеме**, содержат дополнительные сведения о процесс ваш запрос, нажав кнопку **сведениями**.

![Укажите сведения](./media/resource-manager-core-quotas-request/provide-details.png)

7. В **сведения о квотах** панели, выберите модель развертывания и выберите расположение.

![Сведения о квотах DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Выберите **семейства SKU** , требуют увеличения квоты. 

![Семейство SKU](./media/resource-manager-core-quotas-request/sku-family.png)

9. Введите желаемые ограничения для подписки. Чтобы удалить строку, снимите флажок SKU в раскрывающемся списке семейства SKU или щелкните значок отмены "x". После ввода требуемой квоты для каждого семейства SKU, нажмите кнопку **сохранить и продолжить** на панели сведений квоты, чтобы продолжить создание запроса на поддержку.

![Новые ограничения](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Запрос на увеличение квоты виртуальных ЦП виртуальной Машины на уровне с помощью подписки **использование + Квота** колонке

Следуйте инструкциям ниже, используя для создания запроса на обслуживание с помощью Azure «использование + Квота» доступных на портале Azure. 

1. В https://portal.azure.com выберите **Подписки**.

![Подписки](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Выберите подписку, которая требует увеличенную квоту.

![Выберите подписку.](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Выберите **Использование и квоты**.

![Использование и квоты](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. В правом верхнем углу выберите **Запросить увеличение**.

![Запросить увеличение](./media/resource-manager-core-quotas-request/request-increase.png)

5. Выберите **предел подписки вычислительных виртуальных Машин (ядер ЦП) увеличивается** как тип кавычек. 

![Заполнение формы](./media/resource-manager-core-quotas-request/forms.png)
   
6. В **сведения о квотах** панели, выберите модель развертывания и выберите расположение.

![Колонка "Проблема" для квоты](./media/resource-manager-core-quotas-request/problemstep.png)

7. Выберите **семейства SKU** , требуют увеличения квоты.

![Выбранные семейства SKU](./media/resource-manager-core-quotas-request/sku-family.png)

8. Введите желаемые ограничения для подписки. Чтобы удалить строку, снимите флажок SKU в раскрывающемся списке семейства SKU или щелкните значок отмены "x". После ввода требуемой квоты для каждого семейства SKU, нажмите кнопку **сохранить и продолжить** на странице Шаг "проблема" Продолжить создание запроса на поддержку.

![Новый запрос квоты для SKU](./media/resource-manager-core-quotas-request/new-limits.png)

