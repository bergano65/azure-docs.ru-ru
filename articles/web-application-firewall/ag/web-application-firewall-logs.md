---
title: Мониторинг журналов для брандмауэра веб-приложений Azure
description: Узнайте, как включить и управлять журналами и для Брандмауэра веб-приложений Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1b807908c9fb54ecf15de6d44a04760659196a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980971"
---
# <a name="diagnostic-logs-for-azure-web-application-firewall"></a>Диагностические журналы для брандмауэра веб-приложений Azure

Вы можете контролировать ресурсы веб-приложений Firewall с помощью журналов. Можно сохранить производительность, доступ и другие данные или использовать их с ресурса для целей мониторинга.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Журналы диагностики

В Azure можно использовать различные виды журналов для управления шлюзами приложений и устранения возникающих в них неполадок. Доступ к некоторым из этих журналов можно получить через портал. Также можно извлечь все журналы из хранилища BLOB-объектов Azure и просматривать их с помощью таких средств, как [журналы Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md), Excel и Power BI. В списке ниже приведены дополнительные сведения о разных типах журналов:

* **Журнал действий.** В [журналах действий Azure](../../azure-resource-manager/management/view-activity-logs.md) (прежнее название — операционные журналы и журналы аудита) можно просматривать все операции, отправляемые в вашу подписку Azure, и состояние этих операций. Записи этого журнала собираются по умолчанию, и их можно просмотреть на портале Azure.
* **Вход в журнал**: Вы можете использовать этот журнал для просмотра шаблонов доступа к воротам приложений и анализа важной информации. Это включает в себя IP вызываемого абонента, запрошенный URL, задержку ответа, код возврата и байты в и из. Каждые 300 секунд собирается журнал доступа. Этот журнал содержит одну запись для каждого экземпляра шлюза приложений. Экземпляр Шлюза приложений определяется свойством instanceId.
* **Журнал производительности.** С помощью этого журнала можно просматривать, как работают экземпляры шлюза приложений. В этот журнал записываются сведения о производительности каждого экземпляра, включая общее число обработанных запросов, пропускную способность в байтах, число неудачных запросов, а также число работоспособных и неработоспособных серверных экземпляров. Данные для журнала производительности собираются каждые 60 секунд. Журнал производительности доступен только для v1 SKU. Для v2 SKU используйте [metrics](../../application-gateway/application-gateway-metrics.md) для данных о производительности.
* **Журнал брандмауэра.** Этот журнал можно использовать для просмотра запросов, которые были зарегистрированы в режиме обнаружения или предотвращения в шлюзе приложений, в котором настроен брандмауэр веб-приложения.

> [!NOTE]
> Журналы доступны только для ресурсов, развернутых в модели развертывания с помощью Azure Resource Manager. Журналы нельзя использовать для ресурсов в классической модели развертывания. Чтобы получить более полное представление об этих двух моделях, см. статью [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](../../azure-resource-manager/management/deployment-models.md).

Существует три способа хранения журналов:

* **Учетная запись хранения** лучше всего подходит для длительного хранения журналов и их просмотра по мере необходимости.
* **Концентраторы событий**: Концентраторы событий являются отличным вариантом для интеграции с другими инструментами управления безопасностью и событиями (SIEM) для получения оповещений о ваших ресурсах.
* **Журналы Azure Monitor:** журналы Azure Monitor лучше всего использовать для общего мониторинга приложения в режиме реального времени или для просмотра тенденций.

### <a name="enable-logging-through-powershell"></a>Включение ведения журнала с помощью PowerShell

Ведение журнала действий автоматически включается для каждого ресурса Resource Manager. Нужно включить ведение журналов доступа и производительности, чтобы начать сбор связанных данных. Вот как можно включить ведение журнала:

1. Запишите или запомните ИД ресурса учетной записи хранения, где хранятся данные журнала, в формате: /subscriptions/\<идентификатор_подписки\>/resourceGroups/\<имя_группы_ресурсов\>/providers/Microsoft.Storage/storageAccounts/\<имя_учетной_записи_хранения\>. Можно использовать любую учетную запись хранения в подписке. Получить эти сведения можно на портале Azure.

    ![ИД ресурса для учетной записи хранения на портале](../media/web-application-firewall-logs/diagnostics1.png)

2. Запишите или запомните идентификатор ресурса шлюза приложений, для которого нужно включить ведение журнала. в формате: /subscriptions/\<идентификатор_подписки\>/resourceGroups/\<имя_группы_ресурсов\>/providers/Microsoft.Network/applicationGateways/\<имя_шлюза_приложений\>. Получить эти сведения можно на портале.

    ![ИД ресурса для шлюза приложений на портале](../media/web-application-firewall-logs/diagnostics2.png)

3. Включите ведение журнала диагностики с помощью следующего командлета PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Для журналов действий отдельная учетная запись хранения не требуется. За использование хранилища для журналов доступа и производительности взимается плата.

### <a name="enable-logging-through-the-azure-portal"></a>Включение ведения журнала на портале Azure

1. На портале Azure найдите ресурс и выберите **параметры диагностики.**

   Для шлюза приложений доступны три журнала:

   * журнал доступа;
   * журнал производительности;
   * журнал брандмауэра.

2. Чтобы начать сбор данных, выберите **Включить диагностику.**

   ![Включение диагностики][1]

3. На странице **Параметры диагностики** представлены параметры журналов диагностики. В этом примере для хранения журналов используется Log Analytics. хранения журналов диагностики можно также использовать концентраторы событий и учетную запись хранения.

   ![Начало процесса настройки][2]

5. Введите имя для настроек, подтвердите настройки и выберите **Сохранить.**

### <a name="activity-log"></a>Журнал действий

На портале Azure журнал действий создается по умолчанию. Журналы хранятся в течение 90 дней в хранилище журналов событий Azure. Дополнительные сведения об этих журналах см. в статье [Просмотр журналов действий для аудита действий с ресурсами](../../azure-resource-manager/management/view-activity-logs.md).

### <a name="access-log"></a>журнал доступа;

Журнал доступа создается, только если он включен для каждого экземпляра шлюза приложений, как описано выше. Данные хранятся в учетной записи хранения, указанной при включении ведения журнала. Каждый доступ шлюза приложения регистрируется в формате JSON, как показано в следующем примере для v1:

|Значение  |Описание  |
|---------|---------|
|instanceId     | Экземпляр шлюза приложений, который обрабатывает запрос.        |
|clientIP     | IP-адрес источника запроса.        |
|clientPort     | Порт источника запроса.       |
|httpMethod     | Метод HTTP, используемый для запроса.       |
|requestUri     | URI полученного запроса.        |
|RequestQuery     | **Server-Routed** — экземпляр внутреннего пула, из которого отправлен запрос.</br>**X-AzureApplicationGateway-LOG-ID** — идентификатор корреляции, используемый для запроса. Он может использоваться для устранения неполадок с трафиком на внутренних серверах. </br>**SERVER-STATUS** — код отклика HTTP, полученный шлюзом приложений из серверной части.       |
|UserAgent     | Агент пользователя из заголовка HTTP-запроса.        |
|httpStatus     | Код состояния HTTP, возвращаемый клиенту из шлюза приложений.       |
|httpVersion     | Версия HTTP для запроса.        |
|receivedBytes     | Размер полученного пакета (в байтах).        |
|sentBytes| Размер отправленного пакета (в байтах).|
|timeTaken| Время (в миллисекундах), необходимое для обработки запроса и отправки ответа. Вычисляется как промежуток времени от момента, когда шлюз приложений получает первый байт HTTP-запроса, до завершения отправки ответа. Важно отметить, что в поле Time-Taken обычно указывается время передачи пакетов запросов и ответов по сети. |
|sslEnabled| Определяет, используется ли SSL для обмена данными с внутренними пулами. Допустимые значения: on и off.|
|host| Имя хоста, с которым запрос был отправлен на сервер бэкэнда. Если имя хоста бэкэнда переопределяется, это имя будет отражать это.|
|originalHost| Хостимя, с которой запрос был получен шлюзом приложения от клиента.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Для приложения шлюз и WAF v2, журналы показывают немного больше информации:

|Значение  |Описание  |
|---------|---------|
|instanceId     | Экземпляр шлюза приложений, который обрабатывает запрос.        |
|clientIP     | IP-адрес источника запроса.        |
|clientPort     | Порт источника запроса.       |
|httpMethod     | Метод HTTP, используемый для запроса.       |
|requestUri     | URI полученного запроса.        |
|UserAgent     | Агент пользователя из заголовка HTTP-запроса.        |
|httpStatus     | Код состояния HTTP, возвращаемый клиенту из шлюза приложений.       |
|httpVersion     | Версия HTTP для запроса.        |
|receivedBytes     | Размер полученного пакета (в байтах).        |
|sentBytes| Размер отправленного пакета (в байтах).|
|timeTaken| Время (в миллисекундах), необходимое для обработки запроса и отправки ответа. Вычисляется как промежуток времени от момента, когда шлюз приложений получает первый байт HTTP-запроса, до завершения отправки ответа. Важно отметить, что в поле Time-Taken обычно указывается время передачи пакетов запросов и ответов по сети. |
|sslEnabled| Определяет, используется ли SSL для обмена данными с внутренними пулами. Допустимые значения: on и off.|
|sslCipher| Шифровка используется для sSL-коммуникаций (если включен SSL).|
|sslProtocol| Используется протокол SSL (если включен SSL).|
|серверRouted| Сервер бэкэнда, к которому направляет шлюз приложения, направляет запрос.|
|serverStatus| КОД состояния HTTP сервера бэкэнда.|
|serverResponseLatency| Задержка ответа от сервера бэкэнда.|
|host| Адрес, указанный в заголовке запроса.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>журнал производительности;

Журнал производительности создается, только если он включен для каждого экземпляра шлюза приложений, как описано выше. Данные хранятся в учетной записи хранения, указанной при включении ведения журнала. Данные журнала производительности формируются с интервалом в 1 минуту. Он доступен только для v1 SKU. Для v2 SKU используйте [metrics](../../application-gateway/application-gateway-metrics.md) для данных о производительности. В журнал записываются следующие данные:


|Значение  |Описание  |
|---------|---------|
|instanceId     |  Экземпляр шлюза приложений, для которого формируются данные о производительности. Если экземпляров шлюза приложений несколько, каждому экземпляру будет соответствовать определенная строка.        |
|healthyHostCount     | Число работоспособных узлов во внутреннем пуле.        |
|unHealthyHostCount     | Число неработоспособных узлов во внутреннем пуле.        |
|requestCount     | Число обрабатываемых запросов.        |
|latency | Средняя задержка (в миллисекундах) запросов от экземпляра к серверной части, обрабатывающей запросы. |
|failedRequestCount| Количество невыполненных запросов.|
|throughput| Средняя пропускная способность (в байтах в секунду) с момента создания последнего журнала.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Задержка вычисляется от момента получения первого байта HTTP-запроса до момента отправки последнего байта HTTP-отклика. Это сумма времени обработки шлюза приложений, времени передачи по сети к серверной части, а также времени, необходимого для обработки запроса в серверной части.

### <a name="firewall-log"></a>журнал брандмауэра.

Этот журнал создается, только если он включен для каждого шлюза приложений, как описано выше. Кроме того, в шлюзе приложений должен быть настроен брандмауэр веб-приложения. Данные хранятся в учетной записи хранения, указанной при включении ведения журнала. В журнал записываются следующие данные:


|Значение  |Описание  |
|---------|---------|
|instanceId     | Экземпляр шлюза приложений, для которого формируются данные о брандмауэре. Если экземпляров шлюза приложений несколько, каждому экземпляру будет соответствовать определенная строка.         |
|clientIp     |   IP-адрес источника запроса.      |
|clientPort     |  Порт источника запроса.       |
|requestUri     | URL-адрес полученного запроса.       |
|ruleSetType     | Тип набора правил. Доступное значение — OWASP.        |
|ruleSetVersion     | Используемая версия набора правил. Возможные значения: 2.2.9 и 3.0.     |
|ruleId     | Идентификатор правила события-триггера.        |
|message     | Понятное сообщение для события-триггера. Дополнительные сведения приведены в разделе details.        |
|action     |  Действие, выполняемое с запросом. Возможные значения: Blocked и Allowed.      |
|site     | Сайт, для которого создан журнал. В нашем случае возможно только значение Global, так как применяются глобальные правила.|
|подробности     | Сведения о событии-триггере.        |
|details.message     | Описание правила.        |
|details.data     | Определенные данные из запроса, которые соответствуют правилу.         |
|details.file     | Файл конфигурации, содержащий правило.        |
|details.line     | Номер строки в файле конфигурации, активировавшей событие.       |
|hostname   | Хостимя или IP-адрес шлюза приложения.    |
|transactionId  | Уникальный идентификатор для данной транзакции, который помогает группировать несколько нарушений правил, которые произошли в рамках одного и того же запроса.   |
|policyId   | Уникальный идентификатор политики брандмауэра, связанный с шлюзом приложения, слушателем или Пути.   |
|policyScope    | Расположение политики - значения могут быть "Глобальный", "Слушатель", или "Местоположение".   |
|policyScopeName   | Название объекта, на котором применяется политика.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Просмотр и анализ журнала действий

Данные журнала действий можно просматривать и анализировать с помощью любого из следующих методов:

* **Средства Azure.** Информацию из журналов действий можно получать с помощью Azure PowerShell, Azure CLI, REST API Azure или портала Azure. Пошаговые инструкции для каждого метода подробно описаны в статье [Activity operations with Resource Manager](../../azure-resource-manager/management/view-activity-logs.md) (Выполнение операций в журналах действий с помощью Resource Manager).
* **Мощность BI**: Если у вас еще нет учетной записи [Power BI,](https://powerbi.microsoft.com/pricing) вы можете попробовать его бесплатно. С помощью [приложений шаблона Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)вы можете анализировать свои данные.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Просмотр и анализ журналов доступа, производительности и брандмауэра

[Журналы Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md) позволяют собирать файлы журналов счетчиков и событий из вашей учетной записи хранилища BLOB-объектов. Эта служба предоставляет средства визуализации и эффективные возможности поиска для анализа журналов.

Вы также можете подключиться к учетной записи хранения и извлечь записи журнала JSON для журналов доступа и производительности. После скачивания JSON-файлов их можно преобразовать в формат CSV и просматривать в Excel, Power BI или другом средстве визуализации данных.

> [!TIP]
> Если вы знакомы с Visual Studio и основными понятиями изменения значений констант и переменных в C#, можно использовать [инструменты преобразования журналов](https://github.com/Azure-Samples/networking-dotnet-log-converter), доступные на сайте GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Анализ журналов доступа через GoAccess

Мы опубликовали шаблон Resource Manager, который устанавливает и запускает популярный анализатор журналов [GoAccess](https://goaccess.io/) для журналов доступа шлюза приложений. GoAccess предоставляет ценную статистику трафика HTTP, такую как уникальные посетители, запрошенные файлы, узлы, операционные системы, браузеры, коды состояния HTTP и многое другое. Дополнительные сведения см. в [файле сведений в папке шаблона Resource Manager на GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Дальнейшие действия

* См. сведения о визуализации журналов счетчиков и событий с помощью [журналов Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).
* [Визуализируйте свой журнал активности Azure с](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) помощью блога Power BI.
* Прочтите запись блога [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Просмотр и анализ журналов аудита Azure с помощью Power BI и других средств).

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
