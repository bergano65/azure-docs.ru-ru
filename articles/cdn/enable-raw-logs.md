---
title: Мониторинг метрик и необработанных журналов для Azure CDN от Майкрософт
description: В этой статье описывается Azure CDN из метрик мониторинга Майкрософт и необработанных журналов.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/25/2020
ms.author: allensu
ms.openlocfilehash: e73b4b2eefeb26dad4d028f617cbe453dbd1870d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342470"
---
# <a name="monitoring-metrics-and-raw-logs-for-azure-cdn-from-microsoft"></a>Мониторинг метрик и необработанных журналов для Azure CDN от Майкрософт
С Azure CDN Майкрософт вы можете отслеживать ресурсы следующими способами, чтобы помочь в устранении неполадок, отслеживании и отладке проблем. 

* Необработанные журналы содержат подробные сведения о каждом запросе, получаемом CDN. Необработанные журналы отличаются от журналов действий. Журналы действий позволяют анализировать операции, выполненные с ресурсами Azure.
* Метрики, отображающие четыре ключевых метрики в CDN, включая коэффициент попадания в байты, число запросов, размер ответа и общую задержку. Он также предоставляет различные измерения для разбиения метрик.
* Оповещение, которое позволяет клиенту настроить оповещение для ключевых метрик
* Дополнительные метрики, которые позволяют клиентам использовать Log Analytics Azure для включения дополнительных метрик ценности. Мы также предоставляем примеры запросов для нескольких других метрик в Azure Log Analytics.

> [!IMPORTANT]
> Компонент необработанных журналов HTTP доступен только в Azure CDN корпорации Майкрософт.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Настройка — портал Azure

Чтобы настроить необработанные журналы для Azure CDN из профиля Майкрософт, выполните следующие действия: 

1. В меню портал Azure выберите **все ресурсы**  >>  **\<your-CDN-profile>** .

2. В разделе **Мониторинг** выберите элемент **Параметры диагностики**.

3. Щелкните команду **Добавить параметр диагностики**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Добавьте параметр диагностики для профиля CDN." border="true":::
    
    > [!IMPORTANT]
    > Необработанные журналы доступны только на уровне профиля. Агрегированные журналы кодов состояний HTTP доступны на уровне конечной точки.

4. В разделе **Параметры диагностики** введите значение в поле **Имя параметра диагностики**.

5. Выберите **азурекднакцесслог** и укажите срок хранения в днях.

6. Выберите элемент **Сведения о назначении**. Доступны следующие варианты назначения:
    * **Отправить в Log Analytics.**
        * Выберите **подписку** и **рабочую область Log Analytics**.
    * **Архивировать в учетной записи хранения.**
        * Выберите **подписку** и **учетную запись хранения**.
    * **Передать в концентратор событий.**
        * Выберите **подписку**, **пространство имен концентратора событий**, **имя концентратора событий (необязательно)** и **имя политики концентратора событий**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Настройка назначения для параметров журнала." border="true":::

7. Щелкните **Сохранить**.

## <a name="configuration---azure-powershell"></a>Настройка — Azure PowerShell

Используйте [Set-аздиагностиксеттинг](/powershell/module/az.monitor/set-azdiagnosticsetting) , чтобы настроить параметр диагностики для необработанных журналов.

Данные хранения определяются параметром **-RetentionInDays** в команде.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Включение журналов диагностики в учетной записи хранения

1. Войдите в Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Чтобы включить журналы диагностики в учетной записи хранения, введите следующие команды. Замените переменные значениями:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Включение журналов диагностики для рабочей области Log Analytics

1. Войдите в Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Чтобы включить журналы диагностики для рабочей области Log Analytics, введите следующие команды. Замените переменные значениями:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Включение журналов диагностики для пространства имен концентратора событий

1. Войдите в Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Чтобы включить журналы диагностики для пространства имен концентратора событий, введите следующие команды. Замените переменные значениями:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Свойства необработанных журналов

Сейчас Azure CDN из службы Майкрософт предоставляет необработанные журналы. Необработанные журналы содержат сведения о каждом запросе к API со следующей схемой: 

| Свойство              | Описание                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Это уникальная эталонная строка, которая идентифицирует обслуженный Front Door запрос. Она же отправляется клиенту в заголовке X-Azure-Ref. Эта строка нужна для поиска в журналах доступа сведений о конкретном запросе. |
| HttpMethod            | Метод HTTP, используемый для запроса.                                                                                                                                                                     |
| HttpVersion           | Тип запроса или подключения.                                                                                                                                                                   |
| RequestUri            | URI полученного запроса.                                                                                                                                                                         |
| RequestBytes          | Размер сообщения с HTTP-запросом в байтах, включая заголовки и текст запроса.                                                                                                   |
| ResponseBytes         | Количество байтов, отправленных внутренним сервером в качестве ответа.                                                                                                                                                    |
| UserAgent             | Тип браузера, используемый клиентом.                                                                                                                                                               |
| ClientIp              | IP-адрес отправившего запрос клиента.                                                                                                                                                  |
| TimeTaken             | Количество времени, затраченное на выполнение действия (в миллисекундах).                                                                                                                                            |
| SecurityProtocol      | Версия протокола TLS/SSL, которая использовалась в запросе. Содержит значение NULL, если шифрование не использовалось.                                                                                                                           |
| Конечная точка              | Узел конечной точки CDN, который настроен в родительском профиле CDN.                                                                                                                                   |
| Имя внутреннего узла     | Имя узла внутреннего сервера или источника, куда отправляются запросы.                                                                                                                                |
| Отправлено на защиту источника </br> (не рекомендуется) * **см. раздел Примечание об устаревании ниже.** | Если установлено значение true, значит, ответ на запрос был получен из кэша защиты источника, а не из граничной точки присутствия. Защитой источника называется родительский кэш, который предназначен для повышения коэффициента попаданий в кэш.                                       |
| исрецеиведфромклиент | Если значение — true, это означает, что запрос поступил от клиента. Если значение равно false, запрос является пропуском ребра (дочернего окна) и ответ получен от экранирования источника (родительский элемент POP). 
| HttpStatusCode        | Код состояния HTTP, полученный от прокси-сервера.                                                                                                                                                        |
| HttpStatusDetails     | Итоговое состояние запроса. Пояснения к этому строковому значению есть в ссылочной таблице "Состояние".                                                                                              |
| Pop                   | Граничная точка присутствия, которая ответила на запрос пользователя. Точки присутствия кодируются аббревиатурами аэропортов в соответствующих городах.                                                                                   |
| Состояние кэша          | Указывает, получен ли объект из кэша или напрямую из источника.                                                                                                             |
> [!NOTE]
> Журналы будут доступны для запросов в профиле Log Analytics. Пример запроса будет выглядеть следующим образом:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Нерекомендуемая отправка на исходную панель
Необработанное свойство журнала **иссенттуригиншиелд** является устаревшим и заменено новым полем **исрецеиведфромклиент**. Используйте новое поле, если вы уже используете устаревшее поле. 

Необработанные журналы включают журналы, созданные из границы CDN (дочернего окна) и точки источника. Точка происхождения ссылается на родительские узлы, которые стратегически расположены по всему миру. Эти узлы обмениваются данными с исходными серверами и снижают нагрузку на источник данных. 

Для каждого запроса, который перемещается в точку происхождения, существуют две записи журнала:

* Один для граничных узлов
* Один для источника щита. 

Чтобы отличить исходящие или отклики от пограничных узлов против экранирования, можно использовать поле **исрецеиведфромклиент** для получения правильных данных. 

Если значение равно false, то это означает, что запрос ответил от точки защиты источника к граничным узлам. Этот подход эффективен для сравнения необработанных журналов с данными о выставлении счетов. Плата не взимается за исходящий трафик с точки пограничной защиты на граничные узлы. Плата взимается за исходящий трафик от граничных узлов к клиентам. 

**Пример запроса Kusto для исключения журналов, созданных на исходном щита в Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> Функциональность необработанных журналов HTTP автоматически становится доступной для всех профилей, созданных или обновленных после **25 февраля 2020 года**. Для ранее созданных профилей CDN следует обновить конечную точку CDN после настройки ведения журнала. Например, можно открыть раздел в географической фильтрации для конечных точек CDN и заблокировать любую страну или регион, которая не важна для вашей работы, а затем нажать кнопку "Сохранить".


## <a name="metrics"></a>Метрики
Azure CDN от Майкрософт интегрируется с Azure Monitor и публикует четыре метрики CDN для помощи в отслеживании, устранении неполадок и отладке проблем. 

Метрики отображаются на диаграммах и доступны через PowerShell, CLI и API. Метрики CDN бесплатно взимается.

Azure CDN от мер Майкрософт и отправляет свои метрики через 60 секунд. Для отображения на портале метрики может потребоваться до 3 минут. 

Дополнительные сведения см. в разделе [метрики Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

**Метрики, поддерживаемые Azure CDN от Майкрософт**

| metrics         | Описание                                                                                                      | Измерение                                                                                   |
|-----------------|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| Коэффициент обращений к байтам * | Процент исходящего трафика из кэша CDN, вычисленный относительно общего исходящего трафика.                                      | Конечная точка                                                                                    |
| RequestCount    | Число клиентских запросов, обслуживаемых CDN.                                                                     | Конечная точка </br> Страна клиента. </br> Регион клиента. </br> Состояние HTTP. </br> Группа состояния HTTP. |
| ResponseSize    | Число байтов, отправленных в качестве ответов от стороны сети CDN клиентам.                                                  |Конечная точка </br> Страна клиента. </br> Регион клиента. </br> Состояние HTTP. </br> Группа состояния HTTP.                                                                                          |
| TotalLatency    | Общее время от запроса клиента, полученного CDN **до отправки последнего ответа из сети CDN клиенту**. |Конечная точка </br> Страна клиента. </br> Регион клиента. </br> Состояние HTTP. </br> Группа состояния HTTP.                                                                                             |

**_Байт Ъявлению = (исходящий трафик от ребра — исходящий трафик из источника)/егресс из ребра_*

Сценарии, исключенные при вычислении коэффициента попаданий в байтах:

* Вы явно не настраиваете кэш с помощью обработчика правил или поведения кэширования строки запроса.
* Директиву Cache-Control явно настраивается без сохранения или закрытого кэша.

### <a name="metrics-configuration"></a>Конфигурация метрик

1. В меню портал Azure выберите **все ресурсы**  >>  **\<your-CDN-profile>** .

2. В разделе **мониторинг** выберите **метрики**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="Метрики для профиля CDN." border="true":::

3. Выберите **Добавить метрику**, выберите метрику для добавления:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Добавьте и выберите метрику для профиля CDN." border="true":::

4. Выберите **Добавить фильтр** , чтобы добавить фильтр:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Примените фильтр к метрике." border="true":::

5. Выберите **Применить** разделение, чтобы увидеть тенденцию по различным измерениям:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="Примените разделение к метрике." border="true":::

6. Выберите " **создать диаграмму** ", чтобы добавить новую диаграмму:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Добавьте новую диаграмму в представление метрики." border="true":::

### <a name="alerts"></a>видны узлы

Вы можете настроить оповещения в Microsoft CDN, выбрав **Контрольные**  >>  **оповещения**.

Выберите **новое правило генерации оповещений** для метрик, перечисленных в разделе метрики.

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="Настройка оповещений для конечной точки CDN." border="true":::

За использование оповещений будет выплачиваться Azure Monitor. Дополнительные сведения об оповещениях см. в разделе [Azure Monitor Alerts](../azure-monitor/platform/alerts-overview.md).

### <a name="additional-metrics"></a>Дополнительные метрики
Дополнительные метрики можно включить с помощью Log Analytics Azure и журналов необработанных ресурсов, чтобы получить дополнительную стоимость.

1. Выполните действия, описанные выше, в разделе Включение диагностики для отправки необработанного журнала в log Analytics.

2. Выберите созданную рабочую область Log Analytics:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Выбор рабочей области log Analytics" border="true":::   

3. В рабочей области log Analytics в разделе **Общие** выберите **журналы** .  Затем выберите **начать работу**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Рабочая область ресурсов log Analytics." border="true":::   
 
4. Выберите **Профили CDN**.  Выберите пример запроса для запуска или закройте пример экрана, чтобы ввести пользовательский запрос:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Образец экрана запроса." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Выполнение запроса." border="true":::   

4. Чтобы просмотреть данные по диаграмме, выберите **Диаграмма**.  Выберите **закрепить на панели мониторинга** , чтобы закрепить диаграмму на панели мониторинга Azure:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Закрепите диаграмму на панели мониторинга." border="true"::: 

## <a name="next-steps"></a>Next Steps
В рамках этой статьи вы включили поддержку необработанных журналов HTTP в службе Microsoft CDN.

Дополнительные сведения о Azure CDN и других службах Azure, упомянутых в этой статье, см. в следующих статьях:

* [Изучите](cdn-log-analysis.md) шаблоны использования Azure CDN

* Узнайте подробнее о службе [Azure Monitor](../azure-monitor/overview.md).

* Настройте [Log Analytics в Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md).