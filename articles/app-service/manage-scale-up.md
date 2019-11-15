---
title: Функции добавления емкости и расширения функций в Службе приложений Azure| Документация Майкрософт
description: Узнайте, как увеличить масштаб приложения в службе приложений Azure для добавления емкости и расширения функций.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/19/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 91a6681c2ef4a6d0ac01889e6e92e752975e90a5
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74089462"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Увеличение масштаба приложения в службе приложений Azure

В этой статье показано, как масштабировать приложение в службе приложений Azure. Существует два рабочих процесса масштабирования (увеличение масштаба и развертывание), и в этой статье рассматривается первый из них.

* [Увеличение масштаба](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) — получение дополнительных ресурсов, в частности ЦП, памяти и места на диске, и дополнительных возможностей, таких как выделенные виртуальные машины, пользовательские домены и сертификаты, промежуточные слоты, автоматическое масштабирование и т. д. Чтобы увеличить масштаб приложения, следует изменить ценовую категорию плана службы приложений, к которому относится ваше приложение.
* [Развертывание](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) — увеличение количества экземпляров виртуальных машин, на которых работает приложение.
  В зависимости от ценовой категории можно масштабировать до 30 экземпляров. Использование [сред службы приложений](environment/intro.md) с ценовой категорией **Изолированная** позволит увеличить количество экземпляров до 100. Дополнительные сведения см. в статье [Масштабирование числа экземпляров вручную или автоматически](../monitoring-and-diagnostics/insights-how-to-scale.md). Из нее вы узнаете, как использовать автомасштабирование, которое позволяет масштабировать число экземпляров автоматически на основе предварительно определенных правил и расписаний.

Применение этих параметров масштаба занимает всего несколько секунд, но они влияют на все приложения в вашем [плане службы приложений](../app-service/overview-hosting-plans.md).
Для этого не требуется вносить изменения в код или повторно развертывать приложение.

Сведения о ценах и функциях отдельных планов службы приложений см. [здесь](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Прежде чем сменить уровень **Бесплатный** для плана службы приложений, следует снять имеющиеся [ограничения на расходы](https://azure.microsoft.com/pricing/spending-limits/), установленные для вашей подписки Azure. Чтобы просмотреть или изменить параметры подписки на службу приложений Microsoft Azure, см. раздел [Microsoft Azure Subscriptions][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Увеличение масштаба ценовой категории

> [!NOTE]
> Для масштабирования до ценовой категории **PremiumV2** см. раздел [Настройка ценовой категории PremiumV2 для службы приложений](app-service-configure-premium-tier.md).
>

1. В браузере откройте [портал Azure][portal].

1. На странице приложения службы приложений в меню слева выберите **увеличить масштаб (план службы приложений)** .
   
3. Выберите уровень и нажмите кнопку **Применить**. Выберите разные категории (например, **Рабочая**), а также **Дополнительные параметры** для отображения дополнительных уровней.
   
    ![Переход для увеличения масштаба приложения Azure][ChooseWHP]

    После завершения операции появится всплывающее окно уведомления с зеленой галочкой "успешно".

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Масштабирование связанных ресурсов
Если приложение зависит от других служб, таких как база данных SQL Azure или служба хранилища Azure, их можно масштабировать отдельно. Эти ресурсы не находятся под управлением плана службы приложений.

1. На странице **Обзор** для приложения щелкните ссылку **Группа ресурсов** .
   
    ![Увеличение масштаба связанных ресурсов приложения Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. В разделе **Сводка** страницы **Группа ресурсов** выберите ресурс, который необходимо масштабировать. На следующем снимке экрана показан ресурс базы данных SQL.
   
    ![Переход на страницу группы ресурсов для увеличения масштаба приложения Azure](./media/web-sites-scale/ResourceGroup.png)

    Чтобы увеличить масштаб связанного ресурса, см. документацию по конкретному типу ресурсов. Например, чтобы увеличить масштаб отдельной базы данных SQL, см. статью [масштабирование отдельных ресурсов базы данных в базе данных SQL Azure](../sql-database/sql-database-single-database-scale.md). Сведения о масштабировании ресурсов базы данных Azure для MySQL см. в статье [масштабирование ресурсов MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Сравнение ценовых категорий

Дополнительные сведения, в том числе размеры виртуальных машин для различных ценовых категорий, см. в разделе [Сведения о ценах для службы приложений](https://azure.microsoft.com/pricing/details/app-service).

Таблицу лимитов, квот, ограничений и поддерживаемых функций для каждой ценовой категории см. в разделе [Ограничения службы приложений](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Дополнительные ресурсы

[Масштабирование числа экземпляров вручную или автоматически](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[Настройка уровня категории премиум v2 для службы приложений](app-service-configure-premium-tier.md)

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
