---
title: Обзор Соединитель управления ИТ-услугами
description: В этой статье представлен обзор Соединитель управления ИТ-услугами (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: aaaeb23ef24f0d8a0fa4b38139fed57cda6fa63d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657045"
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

1. [Подключение продуктов и служб ITSM с помощью Соединитель управления ИТ-услугами.](./itsmc-connections.md)
1. [Добавьте ITSMC.](/.itsmc-definition.md#add-it-service-management-connector)
1. [Создайте подключение ITSM.](./itsmc-definition.md#create-an-itsm-connection)
1. [Используйте соединение.](./itsmc-definition.md#use-itsmc)

## <a name="next-steps"></a>Дальнейшие действия

[Добавление продуктов и служб ITSM в соединитель управления ИТ-услугами](./itsmc-connections.md) 
 [Добавить соединитель ITSM](./itsmc-definition.md) 
 [Устранение неполадок в соединитель ITSM](./itsmc-resync-servicenow.md)