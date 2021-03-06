---
title: Часто задаваемые вопросы Azure Monitor для виртуальных машин (общедоступные) | Документация Майкрософт
description: Azure Monitor для виртуальных машин — это решение в Azure, которое отслеживает работоспособность и производительность ОС виртуальных машин Azure, автоматически обнаруживает компоненты приложений и их зависимости от других ресурсов, а также строит схему каналов связи между ними. В этой статье содержатся ответы на часто задаваемые вопросы о общедоступном выпуске.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/05/2019
ms.openlocfilehash: 4833b8a1835bd5da3327c73058f170fb0a5738a8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450700"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Часто задаваемые вопросы о Azure Monitor для виртуальных машин общедоступной версии

В этой общей статье часто задаваемые вопросы рассматриваются изменения, происходящие в Azure Monitor для виртуальных машин, как подготовка к нашему общедоступному выпуску. 

## <a name="updates-for-azure-monitor-for-vms"></a>Обновления для Azure Monitor для виртуальных машин

Мы планируем выпустить новую версию Azure Monitor для виртуальных машин в январе 2020. Клиенты, которые раззапускают мониторы Azure для виртуальных машин после этого выпуска, автоматически получат новую версию, но уже существующие клиенты Azure Monitor для виртуальных машин будут получать запрос на обновление. Это часто задаваемые вопросы и документация, в которой содержатся рекомендации по обновлению в масштабе при наличии крупных развертываний в нескольких рабочих областях.

При этом обновлении Azure Monitor для виртуальных машин данные о производительности хранятся в той же `InsightsMetrics` таблице, что и [Azure Monitor для контейнеров](container-insights-overview.md), и упрощает выполнение запросов к этим двум наборам данных. Кроме того, вы можете хранить более разнообразные наборы данных, которые не могли бы храниться в ранее использованной таблице. а представления данных о производительности будут обновлены для использования новой таблицы.

Мы переходим к новым типам данных для наших наборов данных подключения. Это изменение будет происходить в декабре 2019 и будет объявлено в блоге по обновлению Azure. Данные, которые в настоящее время хранятся в `ServiceMapComputer_CL` и `ServiceMapProcess_CL`, которые являются пользовательскими таблицами журналов, будут перемещаться на выделенные типы данных с именами `VMComputer` и `VMProcess`. Переходя к выделенным типам данных, они получают приоритет для приема данных, и схема таблицы будет стандартизована для всех клиентов.

Мы понимаем, что при обновлении существующих заказчиков происходит прерывание рабочего процесса, поэтому мы решили сделать это сейчас в общедоступной предварительной версии, а не позднее после бесплатного использования.

## <a name="what-is-changing"></a>Что изменяется?

Сейчас, когда вы завершите процесс адаптации для Azure Monitor для виртуальных машин, включите решение Сопоставление служб в рабочей области, выбранной для хранения данных мониторинга, а затем настройте счетчики производительности для данных, собранных из виртуальных машин. Мы выпустим новое решение с именем **вминсигхтс**, которое включает дополнительные возможности для сбора данных, а также новое расположение для хранения этих данных в рабочей области log Analytics.

Текущий процесс использования счетчиков производительности в рабочей области Log Analytics отправляет данные в `Perf` таблицу. Это новое решение отправляет данные в таблицу с именем `InsightsMetrics`, которая также используется Azure Monitor для контейнеров. Эта схема таблицы позволяет нам хранить дополнительные метрики и наборы данных служб, несовместимые с форматом таблицы производительности.

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Что делать со счетчиками производительности в моей рабочей области, если я устанавливаю решение Вминсигхтс?

Текущий метод включения Azure Monitor для виртуальных машин использует счетчики производительности в рабочей области. Новый метод сохраняет эти данные в новой таблице с именем `InsightsMetrics`.

После обновления пользовательского интерфейса для использования данных в таблице `InsightsMetrics` мы будем обновлять нашу документацию и сообщать об этом объявлении по нескольким каналам, включая отображение баннера в портал Azure. На этом этапе вы можете отключить эти [счетчики производительности](vminsights-enable-overview.md#performance-counters-enabled) в рабочей области, если их больше не нужно использовать. 

>[!NOTE]
>При наличии правил генерации оповещений, ссылающихся на эти счетчики в `Perf` таблице, необходимо обновить их, чтобы они ссылались на новые данные, хранящиеся в таблице `InsightsMetrics`. См. документацию по запросам к журналу, которые можно использовать для обращения к этой таблице.
>

Если вы решили сохранить счетчики производительности, вам будет выставлен счет за полученные и хранящиеся в `Perf` таблице данные, основанные на [ценах на Log Analytics [(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Как это изменение повлияет на мои правила генерации оповещений?

Если вы создали [оповещения журнала](../platform/alerts-unified-log.md) , которые запрашивают в `Perf` таблице счетчики производительности, которые были включены в рабочей области, следует обновить эти правила, чтобы они ссылались на `InsightsMetrics` таблицу. Это руководство также применяется к любым правилам поиска журналов, использующим `ServiceMapComputer_CL` и `ServiceMapProcess_CL`, так как эти наборы данных перемещаются в `VMComputer` и `VMProcess` таблицы.

Мы будем обновлять эту часто задаваемые вопросы и документацию, включив в нее примеры правил генерации оповещений поиска по журналам для наборов данных, которые мы соберем.

## <a name="how-will-this-affect-my-bill"></a>Как это повлияет на счет?

Выставление счетов по-прежнему основано на данных, принятых и хранящихся в рабочей области Log Analytics.

Собранные данные о производительности на уровне компьютера одинаковы, имеют тот же размер, что и данные, хранящиеся в таблице `Perf`, и стоимость будет составлять примерно один и тот же объем.

## <a name="what-if-i-only-want-to-use-service-map"></a>Что делать, если я хотел бы использовать только Сопоставление служб?

Это нормально. Вы увидите запросы в портал Azure при просмотре Azure Monitor для виртуальных машин о предстоящем обновлении. После выпуска вы получите запрос на обновление до новой версии. Если вы предпочитаете использовать только функции [карт](vminsights-maps.md) , вы можете не обновлять и продолжать использовать функцию карт в Azure Monitor для виртуальных машин и сопоставление служб решение, доступ к которому осуществляется из рабочей области или плитки панели мониторинга.

Если вы решили включить счетчики производительности вручную в рабочей области, вы можете увидеть данные на некоторых из наших диаграмм производительности, просмотрев их Azure Monitor. После выпуска нового решения мы будем обновлять наши диаграммы производительности для запроса данных, хранящихся в таблице `InsightsMetrics`. Если вы хотите просмотреть данные из этой таблицы на этих диаграммах, необходимо выполнить обновление до новой версии Azure Monitor для виртуальных машин.

Изменения в перемещении данных из `ServiceMapComputer_CL` и `ServiceMapProcess_CL` будут влиять как на Сопоставление служб, так и на Azure Monitor для виртуальных машин, поэтому вам все равно нужно спланировать это обновление.

Если вы решили не выполнять обновление до решения **вминсигхтс** , мы будем продолжать предоставлять устаревшие версии книг по производительности, которые ссылаются на данные в таблице `Perf`.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Будут ли Сопоставление служб наборы данных также сохранены в Инсигхтсметрикс?

Если используются оба решения, наборы данных не будут дублироваться. Оба предложения совместно используют наборы данных, которые будут храниться в `VMComputer` (ранее ServiceMapComputer_CL), `VMProcess` (ранее ServiceMapProcess_CL), `VMConnection`и `VMBoundPort` таблиц для хранения собранных наборов данных карт.  

В таблице `InsightsMetrics` будут храниться собранные наборы данных виртуальных машин, процессов и служб, которые будут заполнены, только если вы используете Azure Monitor для виртуальных машин и решение VM Insights. Сопоставление служб решение не будет выполнять накопление и хранение данных в таблице `InsightsMetrics`.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Будет ли я вдвое заряжена, если у меня есть решения Сопоставление служб и Вминсигхтс в моей рабочей области?

Нет, два решения совместно используют наборы данных карт, которые хранятся в `VMComputer` (ранее ServiceMapComputer_CL), `VMProcess` (ранее ServiceMapProcess_CL), `VMConnection`и `VMBoundPort`. Если в вашей рабочей области есть оба решения, вы не будете использовать двойную оплату за эти данные.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Если удалить решение Сопоставление служб или Вминсигхтс, будут ли удалены мои данные?

Нет, два решения совместно используют наборы данных карт, которые хранятся в `VMComputer` (ранее ServiceMapComputer_CL), `VMProcess` (ранее ServiceMapProcess_CL), `VMConnection`и `VMBoundPort`. Если вы удалите одно из решений, эти наборы данных заметят, что по-прежнему существует решение, которое использует данные и остается в рабочей области Log Analytics. Для удаления данных из рабочей области необходимо удалить оба решения.

## <a name="when-will-this-update-be-released"></a>Когда будет выпущено это обновление?

Мы планируем выпустить обновление для Azure Monitor для виртуальных машин в начале 2020 января. Как мы получаем ближе к дате выпуска в январе, мы будем публиковать обновления здесь и Показывать уведомления в портал Azure при открытии Azure Monitor.

## <a name="health-feature-is-in-limited-public-preview"></a>Функция работоспособности ограничена общедоступной предварительной версией

Мы получили огромное мнение от клиентов о наборе функций работоспособности виртуальной машины. Такие функции вызывают большой интерес благодаря возможностям поддержки рабочих процессов мониторинга. В соответствии с полученными отзывами мы планируем реализовать некоторые изменения и расширить функциональность. 

Чтобы снизить влияние этих изменений на новых клиентов, мы перенесли эту функцию в **ограниченную общедоступную предварительную версию**. Это обновление произошло в 2019 октября.

Мы планируем повторно запустить эту функцию работоспособности в 2020, после того, как Azure Monitor для виртуальных машин находится в общедоступной версии.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Как существующие клиенты получают доступ к функции работоспособности?

Существующие клиенты, использующие функцию работоспособности, будут по прежнему иметь доступ к ней, но не будут предлагаться новым клиентам.  

Чтобы получить доступ к функции, можно добавить в [https://portal.azure.com](https://portal.azure.com)URL-адрес портал Azure следующий флаг компонента `feature.vmhealth=true`. Пример `https://portal.azure.com/?feature.vmhealth=true`.

Можно также использовать этот короткий URL-адрес, который автоматически устанавливает флаг компонента: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

В качестве существующего клиента можно продолжать использовать функцию работоспособности на виртуальных машинах, подключенных к существующей настройке рабочей области, с функцией работоспособности.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Я использую работоспособность виртуальной машины сейчас с одной средой и хочу развернуть ее в новом

Если вы являетесь клиентом, который использует функцию работоспособности и хотите использовать его для нового развертывания, свяжитесь с нами по адресу vminsights@microsoft.com, чтобы запросить инструкции.

## <a name="next-steps"></a>Дальнейшие действия

Сведения о требованиях и методах, помогающих отслеживать виртуальные машины, см. в статье [Подключение Azure Monitor для виртуальных машин (предварительная версия)](vminsights-enable-overview.md).
