---
title: Квота на место | Документация Майкрософт
description: Запросы к квотам на место
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850573"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>Квота на место: максимальное увеличение для всей серии виртуальных машин

Плашечные виртуальные машины предоставляют другую модель использования Azure, что позволяет снизить затраты на удаление виртуальной машины Azure по мере необходимости для развертывания экземпляров виртуальных машин с оплатой по мере использования или зарезервированного экземпляра виртуальной машины. Дополнительные сведения о плашечных виртуальных машинах см. [здесь](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot).

Диспетчер ресурсов поддерживает два типа квот виртуальных ЦП для виртуальных машин. **Виртуальные машины с оплатой по мере использования и зарезервированные экземпляры виртуальных машин** используют стандартную квоту. В **плашечных виртуальных машинах** используется Раздельная квота. 

Для типа **дисковой квоты** диспетчер ресурсов квоты виртуальных ЦП применяются ко всем доступным сериям виртуальных машин как единое Региональное ограничение.

В любой момент, когда будет развернута новая виртуальная машина, сумма новых и существующих виртуальных ЦП использования всех экземпляров виртуальных машин не должна превышать допустимый объем виртуальных цпной квоты. Если превышена квота, Плашечное развертывание виртуальной машины не будет разрешено. Вы можете запросить увеличение виртуальных ЦП квоты для точки с портал Azure. 

Дополнительные сведения о стандартных квотах виртуальных ЦП см. на странице квоты виртуальной машины виртуальных ЦП, а также на странице подписки Azure и ограничения службы. Узнайте больше о том, как увеличить значение региональных виртуальных ЦП для стандартной квоты на этой [странице](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests).

Теперь вы можете запросить увеличение **квоты для всех рядов виртуальных машин** , используя колонку **Справка и поддержка** или колонку **использование и квота** на портале.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>Запрос на увеличение квоты на количество запросов для всех рядов виртуальных машин на подписку с помощью колонки "Справка и поддержка"

Следуйте приведенным ниже инструкциям, чтобы создать запрос на поддержку через колонку "Справка и поддержка" Azure, доступную в портал Azure.

Вы также можете **запросить квоту для нескольких регионов** , используя один вариант поддержки. Дополнительные сведения см. на шаге 10 ниже. 


1. В https://portal.azure.com выберите **Справка и поддержка**.

   ![Справка и поддержка](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Выберите **Новый запрос в службу поддержки**. 

     ![Новый запрос на техническую поддержку](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. В раскрывающемся списке Тип проблемы выберите **пределы службы и подписки (квоты)** .

   ![Раскрывающийся список типов проблем](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Выберите подписку, которая требует увеличенную квоту.

   ![Выберите Новости подписки](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. В раскрывающемся списке " **тип квоты** **" выберите вычисление — виртуальная машина (ядра — виртуальных ЦП)** . 

   ![Выберите тип квоты](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. В окне **сведения о проблеме**укажите дополнительные сведения, которые помогут обработать запрос, нажав кнопку **указать подробности**.

   ![Укажите сведения](./media/resource-manager-core-quotas-request/provide-details.png)
   
7.  На панели **сведения о квоте*** выберите **модель развертывания** и укажите **Расположение**.

![Укажите сведения](./media/resource-manager-core-quotas-request/3-7.png)

8. Для выбранного расположения выберите **тип** значение **"плашка"** . Вы можете запросить как стандартные, так и типы квот из одного варианта поддержки, используя поддержку множественного выбора в поле **типа** . Дополнительные сведения об **увеличении стандартной квоты на серию виртуальных машин** см. на этой [странице](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

![Укажите сведения](./media/resource-manager-core-quotas-request/3-8.png)

9.  Введите новое ограничение для подписки. 
 
 ![Укажите сведения](./media/resource-manager-core-quotas-request/3-9.png)

10. Чтобы запросить квоту для нескольких расположений, можно проверить расположение в раскрывающемся списке и выбрать соответствующий тип виртуальной машины. Затем можно ввести новые ограничения.

![Укажите сведения](./media/resource-manager-core-quotas-request/3-10.png)

11. После ввода нужной квоты нажмите кнопку **сохранить и продолжить** на панели сведений о квотах, чтобы продолжить создание запроса на поддержку.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>Запросить увеличение квоты для разряда виртуальных машин на подписку с помощью колонки "использование" и "квота"

Следуйте приведенным ниже инструкциям по созданию запроса на поддержку через колонку "использование и квота" Azure, доступную в портал Azure.

Вы также можете **запросить квоту для нескольких регионов** , используя один вариант поддержки. Дополнительные сведения см. в шаге 9 ниже. 

1.  В https://portal.azure.com выберите **Подписки**.

 ![Подписки](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  Выберите подписку, которая требует увеличенную квоту.

 ![Выберите подписку.](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  Выберите **Использование и квоты**.

 ![Использование и квоты](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  В правом верхнем углу выберите **запросить увеличение.**

   ![Запросить увеличение](./media/resource-manager-core-quotas-request/request-increase.png)

5.  Выберите **Вычисление — ВМ (ядра-виртуальных ЦП). ограничение подписки увеличивается** как тип предложения.

  ![Заполнение формы](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  На панели **сведения о квоте** выберите модель развертывания и укажите расположение.

  ![Заполнение формы](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  Для выбранного расположения выберите **тип** значение **"плашка".** Вы можете запросить как стандартные, так и типы квот из одного варианта поддержки, используя поддержку множественного выбора в поле **типа** . Дополнительные сведения об **увеличении стандартной квоты на серию виртуальных машин** см. на этой [странице](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

  ![Заполнение формы](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  Введите новое ограничение для подписки.

  ![Заполнение формы](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  Чтобы запросить квоту для нескольких расположений, можно проверить **Расположение** в раскрывающемся списке и выбрать соответствующий тип виртуальной машины. Затем можно ввести новые ограничения.

  ![Заполнение формы](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. После ввода нужной квоты нажмите кнопку **сохранить и продолжить** на панели сведений о квотах, чтобы продолжить создание запроса на поддержку.


