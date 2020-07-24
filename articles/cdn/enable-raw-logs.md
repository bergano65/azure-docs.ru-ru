---
title: Необработанные журналы HTTP (Azure CDN)
description: В этой статье собраны сведения о необработанных журналах HTTP в службе Azure CDN.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/22/2020
ms.author: allensu
ms.openlocfilehash: 3b36e528a013403a2ed664d3011338d92f37a3db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040162"
---
# <a name="azure-cdn-http-raw-logs"></a>Необработанные журналы HTTP (Azure CDN)
Необработанные журналы предоставляют широкие сведения об операциях и ошибках, полезные для аудита и (или) устранения неполадок. Необработанные журналы отличаются от журналов действий. Журналы действий позволяют анализировать операции, выполненные с ресурсами Azure. Необработанные журналы содержат записи действий самого ресурса. Журнал необработанных данных содержит подробные сведения о каждом запросе, получаемом CDN. 

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

    ![Параметр диагностики CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Необработанные журналы доступны только на уровне профиля. Агрегированные журналы кодов состояний HTTP доступны на уровне конечной точки.

4. В разделе **Параметры диагностики** введите значение в поле **Имя параметра диагностики**.

5. Выберите вариант **Журнал** и задайте срок хранения в днях.

6. Выберите элемент **Сведения о назначении**. Доступны следующие варианты назначения:
    * **Отправить в Log Analytics.**
        * Выберите **подписку** и **рабочую область Log Analytics**.
    * **Архивировать в учетной записи хранения.**
        * Выберите **подписку** и **учетную запись хранения**.
    * **Передать в концентратор событий.**
        * Выберите **подписку**, **пространство имен концентратора событий**, **имя концентратора событий (необязательно)** и **имя политики концентратора событий**.

    ![Параметр диагностики CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Щелкните **Сохранить**.

## <a name="configuration---azure-powershell"></a>Настройка — Azure PowerShell

Используйте [Set-аздиагностиксеттинг](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest) , чтобы настроить параметр диагностики для необработанных журналов.

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
> Журналы будут доступны для запросов в профиле Log Analytics. Пример запроса: AzureDiagnostics | where Category == "AzureCdnAccessLog"


### <a name="sent-to-origin-shield-deprecation"></a>Нерекомендуемая отправка на исходную панель
Необработанное свойство журнала **иссенттуригиншиелд** является устаревшим и заменено новым полем **исрецеиведфромклиент**. Используйте новое поле, если вы уже используете устаревшее поле. 

Необработанные журналы включают журналы, созданные из границы CDN (дочернего окна) и точки источника. Точка происхождения ссылается на родительские узлы, которые стратегически расположены по всему миру. Эти узлы обмениваются данными с исходными серверами и снижают нагрузку на источник данных. 

Для каждого запроса, который перемещается в точку происхождения, существуют две записи журнала:

* Один для граничных узлов
* Один для источника щита. 

Чтобы отличить исходящие или отклики от пограничных узлов против экранирования, можно использовать поле Исрецеиведфромклиент для получения правильных данных. 

Если значение равно false, то это означает, что запрос ответил от точки защиты источника к граничным узлам. Этот подход эффективен для сравнения необработанных журналов с данными о выставлении счетов. Плата не взимается за исходящий трафик с точки пограничной защиты на граничные узлы. Плата взимается за исходящий трафик от граничных узлов к клиентам. 

**Пример запроса Kusto для исключения журналов, созданных на исходном щита в Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> Функциональность необработанных журналов HTTP автоматически становится доступной для всех профилей, созданных или обновленных после **25 февраля 2020 года**. Для ранее созданных профилей CDN следует обновить конечную точку CDN после настройки ведения журнала. Например, можно открыть раздел в географической фильтрации для конечных точек CDN и заблокировать любую страну или регион, которая не важна для вашей работы, а затем нажать кнопку "Сохранить". 

## <a name="next-steps"></a>Next Steps
В рамках этой статьи вы включили поддержку необработанных журналов HTTP в службе Microsoft CDN.

Дополнительные сведения о Azure CDN и других службах Azure, упомянутых в этой статье, см. в следующих статьях:

* [Изучите](cdn-log-analysis.md) шаблоны использования Azure CDN

* Узнайте подробнее о службе [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Настройте [Log Analytics в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
