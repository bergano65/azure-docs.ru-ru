---
title: Обзор Соединитель управления ИТ-услугами
description: В этой статье представлен обзор Соединитель управления ИТ-услугами (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93759cf239a2e7ef79c719c83299740ea3722130
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614563"
---
# <a name="it-service-management-connector-overview"></a>Обзор Соединитель управления ИТ-услугами

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Соединитель управления ИТ-услугами (ITSMC) позволяет подключать Azure к поддерживаемому продукту или службе управления ИТ-службами (ITSM).

Службы Azure, такие как Azure Log Analytics и Azure Monitor, предоставляют средства для обнаружения, анализа и устранения неполадок в ресурсах Azure и не являющихся ресурсами Azure. Но рабочие элементы, связанные с проблемой, обычно находятся в ITSM продукте или службе. ITSMC обеспечивает двунаправленное подключение между инструментами Azure и ITSM, которые позволяют быстрее устранять проблемы.

## <a name="configuration-steps"></a>Этапы настройки

Соединитель управления ИТ-услугами поддерживает подключения к следующим инструментам ITSM:

-   ServiceNow
-   System Center Service Manager;
-   Provance;
-   Cherwell

   >[!NOTE]
> Мы предлагаем нашим клиентам Cherwell и Provance использовать [действие веб-перехватчика](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) для Cherwell и Provance конечной точки в качестве другого решения интеграции.

С помощью ITSMC можно:

-  Создание рабочих элементов в средстве ITSM на основе оповещений Azure (оповещения метрик, оповещения журнала действий и Log Analytics оповещения).
-  При необходимости синхронизировать данные инцидентов и запросов на изменение из инструмента ITSM с рабочей областью Azure Log Analytics.

Сведения о юридических условиях и политике конфиденциальности см. в [заявлении о конфиденциальности Майкрософт](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Вы можете начать использовать ITSMC, выполнив следующие действия.

1. [Подключение продуктов и служб ITSM с помощью Соединитель управления ИТ-услугами.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
2. [Добавьте ITSMC.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#add-it-service-management-connector)
3. [Создайте подключение ITSM.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-an-itsm-connection)
4. [Используйте соединение.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#use-itsmc)

## <a name="next-steps"></a>Дальнейшие действия

[Добавление продуктов и служб ITSM в Соединитель управления ИТ-услугами](./itsmc-connections.md)
