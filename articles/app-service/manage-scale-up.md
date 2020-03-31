---
title: Масштабирование функций и емкостей
description: Узнайте, как масштабировать приложение в службе приложений Azure. Получите больше процессора, памяти, дискового пространства и дополнительных функций.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: dfd9297e80836978b8a185df3fc4659676383802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659905"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Масштабирование приложения в службе приложений Azure

В этой статье показано, как масштабировать приложение в службе приложений Azure. Существует два рабочих процесса масштабирования (увеличение масштаба и развертывание), и в этой статье рассматривается первый из них.

* [Увеличение масштаба](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) — получение дополнительных ресурсов, в частности ЦП, памяти и места на диске, и дополнительных возможностей, таких как выделенные виртуальные машины, пользовательские домены и сертификаты, промежуточные слоты, автоматическое масштабирование и т. д. Чтобы увеличить масштаб приложения, следует изменить ценовую категорию плана службы приложений, к которому относится ваше приложение.
* [Развертывание](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) — увеличение количества экземпляров виртуальных машин, на которых работает приложение.
  Вы можете масштабировать до 30 экземпляров, в зависимости от вашего уровня ценообразования. Использование [сред службы приложений](environment/intro.md) с ценовой категорией **Изолированная** позволит увеличить количество экземпляров до 100. Дополнительные сведения см. в статье [Масштабирование числа экземпляров вручную или автоматически](../monitoring-and-diagnostics/insights-how-to-scale.md). Из нее вы узнаете, как использовать автомасштабирование, которое позволяет масштабировать число экземпляров автоматически на основе предварительно определенных правил и расписаний.

Применение этих параметров масштаба занимает всего несколько секунд, но они влияют на все приложения в вашем [плане службы приложений](../app-service/overview-hosting-plans.md).
Для этого не требуется вносить изменения в код или повторно развертывать приложение.

Сведения о ценах и функциях отдельных планов службы приложений см. [здесь](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Прежде чем сменить уровень **Бесплатный** для плана службы приложений, следует снять имеющиеся [ограничения на расходы](https://azure.microsoft.com/pricing/spending-limits/), установленные для вашей подписки Azure. Просмотреть или изменить параметры подписки на службу приложений Microsoft Azure можно на странице [подписок Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Увеличение масштаба ценовой категории

> [!NOTE]
> Для масштабирования до ценовой категории **PremiumV2** см. раздел [Настройка ценовой категории PremiumV2 для службы приложений](app-service-configure-premium-tier.md).
>

1. В браузере откройте [портал Azure.][portal]

1. На странице приложения App Service из левого меню выберите **Scale Up (план службы приложений).**
   
3. Выберите свой уровень, а затем выберите **Apply**. Выберите различные категории (например, **Производство),** а также **см. дополнительные опции,** чтобы показать больше уровней.
   
    ![Переход для увеличения масштаба приложения Azure][ChooseWHP]

    Когда операция завершена, вы увидите всплывающее окно уведомления с зеленым знаком проверки успеха.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Масштабирование связанных ресурсов
Если приложение зависит от других служб, таких как база данных SQL Azure или служба хранилища Azure, их можно масштабировать отдельно. Эти ресурсы не находятся под управлением плана службы приложений.

1. На странице **«Обзор»** для приложения выберите ссылку **группы ресурсов.**
   
    ![Увеличение масштаба связанных ресурсов приложения Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. В **сводной** части страницы **группы ресурсов** выберите ресурс, который необходимо масштабировать. На следующем скриншоте показан ресурс базы данных S'L.
   
    ![Переход на страницу группы ресурсов для увеличения масштаба приложения Azure](./media/web-sites-scale/ResourceGroup.png)

    Для масштабирования связанного ресурса см. Например, для масштабирования единой базы данных S'L см. [Масштабируйте единую базу данных в базе данных Azure S'L.](../sql-database/sql-database-single-database-scale.md) Для масштабирования базы данных Azure для [Scale MySQL resources](../mysql/concepts-pricing-tiers.md#scale-resources)ресурса MyS'L см.

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Сравнение ценовых категорий

Дополнительные сведения, в том числе размеры виртуальных машин для различных ценовых категорий, см. в разделе [Сведения о ценах для службы приложений](https://azure.microsoft.com/pricing/details/app-service).

Таблицу лимитов, квот, ограничений и поддерживаемых функций для каждой ценовой категории см. в разделе [Ограничения службы приложений](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Дополнительные ресурсы

[Масштабирование числа экземпляров вручную или автоматически](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[Настройка уровня PremiumV2 для службы приложений](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
