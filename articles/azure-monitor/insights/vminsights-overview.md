---
title: Что такое Azure Monitor для виртуальных машин (предварительная версия)? | Документы Майкрософт
description: Общие сведения о Azure Monitor для виртуальных машин, которые отслеживают работоспособность и производительность виртуальных машин Azure в дополнение к автоматическому обнаружению и сопоставлению компонентов приложения и их зависимостей.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 17aa81c626c1bfa8a8a344552b7a0fe61dd85a7e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365790"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Что такое Azure Monitor для виртуальных машин (предварительная версия)?

Azure Monitor для виртуальных машин отслеживает виртуальные машины Azure и масштабируемые наборы виртуальных машин в нужном масштабе. Это решение анализирует производительность и работоспособность виртуальных машин Windows и Linux, отслеживает их процессы и зависимости на других ресурсах, а также внешние процессы. 

Она включает поддержку мониторинга производительности и зависимостей приложений для виртуальных машин, размещенных локально или в другом поставщике облачных служб. Следующие основные функции предоставляют подробные сведения:

- **Диаграммы с предварительно определенными**показателями производительности: отображают основные метрики производительности из операционной системы гостевой виртуальной машины.

- **Схема зависимостей**. отображает взаимосвязь компонентов с виртуальной машиной из различных групп ресурсов и подписок.  

>[!NOTE]
>Недавно мы [объявили об изменениях](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) , внесенных в функцию работоспособности, на основе отзывов наших клиентов, полученных от общедоступной предварительной версии. Учитывая количество изменений, которые мы будем вносить, мы перейдем к предложению функции работоспособности для новых клиентов. Существующие клиенты могут продолжать использовать функцию работоспособности. Дополнительные сведения см. в статье [часто задаваемые вопросы о доступности](vminsights-ga-release-faq.md).  

Интеграция с журналами Azure Monitor обеспечивает мощную агрегацию и фильтрацию и позволяет проводить анализ тенденций данных с течением времени. Такой комплексный мониторинг рабочей нагрузки не может осуществляться только c помощью Azure Monitor или Сопоставления служб.  

Вы можете просмотреть эти данные на одной ВИРТУАЛЬНОЙ машине непосредственно из виртуальной машины или использовать Azure Monitor для предоставления агрегированного представления виртуальных машин, в которых представление поддерживает контекст ресурсов Azure или контекста рабочей области. Дополнительные сведения см. в разделе [Общие сведения о режимах доступа](../platform/design-logs-deployment.md#access-mode).

![Перспектива полезных сведений о виртуальной машине на портале Azure](./media/vminsights-overview/vminsights-azmon-directvm.png)

Пакет Azure Monitor для виртуальных машин может обеспечить прогнозируемую производительность и доступность важнейших приложений. Он определяет узкие места производительности и проблемы с сетью. Azure Monitor для виртуальных машин может также помочь вам понять, связана ли проблема с другими зависимостями.  

## <a name="data-usage"></a>Использование данных

При развертывании Azure Monitor для виртуальных машин данные, собранные с виртуальных машин, принимаются и хранятся в Azure Monitor. Собранные данные о производительности и зависимостях хранятся в Log Analytics рабочей области. Плата в Azure Monitor для виртуальных машин выставляется за следующие элементы (на основе цен, опубликованных на [странице цен на Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)):

- данные, которые принимаются и хранятся;
- правила генерации оповещений, которые создаются;
- отправляемые уведомления. 

Размер журнала зависит от длины строк счетчиков производительности и может увеличиваться с учетом количества логических дисков и сетевых адаптеров, выделенных для виртуальной машины. Если у вас уже есть рабочее пространство и вы собираете эти счетчики, никаких дублирующих сборов не будет. Если вы уже используете Сопоставление служб, единственное изменение, которое вы увидите — это данные дополнительного подключения, отправляемые в Azure Monitor.

## <a name="next-steps"></a>Дальнейшие действия

Сведения о требованиях и методах, помогающих отслеживать виртуальные машины, см. в статье [Подключение Azure Monitor для виртуальных машин (предварительная версия)](vminsights-enable-overview.md).
