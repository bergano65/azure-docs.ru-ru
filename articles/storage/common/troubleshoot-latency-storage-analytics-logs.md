---
title: Устранение неполадок с помощью журналов Аналитики Службы хранилища
description: Выявление и устранение проблем с задержкой с использованием аналитических журналов Azure Storage Analytic и оптимизация клиентского приложения.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196509"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Устранение неполадок с помощью журналов Аналитики Службы хранилища

Диагностика и устранение неполадок — ключевой навык для создания и поддержки клиентских приложений с помощью Azure Storage.

Из-за распределенного характера приложения Azure диагностика и устранение ошибок и устранения неполадок могут быть более сложными, чем в традиционных средах.

Следующие шаги демонстрируют, как выявлять и устранять проблемы с задержкой с помощью аналитических журналов Azure Storage Analytic и оптимизировать клиентское приложение.

## <a name="recommended-steps"></a>Рекомендуемые действия

1. Скачать [журналы Analytics хранения](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. Используйте следующий скрипт PowerShell для преобразования журналов необработанных форматов в табликовый формат:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Скрипт запустит окно ГРАФИЧЕСКОго интерфейса, где можно отфильтровать информацию столбцов, как показано ниже.

   ![Окно аналитического журнала Parser](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Сузите записи в журнале на основе "эксплуатационно-типа" и ищите запись журнала, созданную в течение периода времени проблемы.

   ![Записи журналов типа операции](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Во время возникновения проблемы важны следующие значения:

   * Эксплуатационная тип - GetBlob
   * статус запроса - SASNetworkError
   * Конец-к-End-Задержка-В-МС 8453
   * Сервер-Задержка-в-МС No 391

   Сквозная задержка рассчитывается с использованием следующего уравнения:

   * Задержка между конца миной и задержкой в работе сервера и задержки клиента

   Рассчитайте задержку клиента с помощью входа в журнал:

   * Задержка клиента - задержка с конечным иэнд-эндом - Задержка сервера

          * Example: 8453 – 391 = 8062ms

   В следующей таблице приводится информация о результатах операции OperationType и RequestStatus:

   |   |ЗапросСтатус<br>Успешно|ЗапросСтатус<br>(SAS) Сетеваяошибка|Рекомендация|
   |---|---|---|---|
   |GetBlob|Да|нет|[**Операция GetBlob:** ЗапросСтатус - Успех](#getblob-operation-requeststatus--success)|
   |GetBlob|нет|Да|[**Операция GetBlob:** ЗапросСтатус (SAS)Сетевая ошибка](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Да|нет|[**Операция «Пут»:** ЗапросСтатус - Успех](#put-operation-requeststatus--success)|
   |PutBlob|нет|Да|[**Операция «Пут»:** ЗапросСтатус (SAS)Сетевая ошибка](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Результаты состояния

### <a name="getblob-operation-requeststatus--success"></a>Операция GetBlob: ЗапросСтатус - Успех

Проверьте следующие значения, упомянутые в шаге 5 раздела "Рекомендуемые шаги":

* Сквозная задержка
* Задержка сервера
* Клиент-Задержка

В **операции GetBlob** с **requestStatus и Успех**, если **Время Max** тратится в **клиент-задержка**, это означает, что Azure Storage тратит большой объем времени на написание данных для клиента. Эта задержка указывает на проблему клиента-стороны.

**Рекомендации:**

* Исследуйте код в вашем клиенте.
* Используйте Wireshark, Microsoft Message Analyzer или Tcping для изучения проблем сетевого подключения клиента. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Операция GetBlob: Requeststatus (SAS)NetworkError

Проверьте следующие значения, упомянутые в шаге 5 раздела "Рекомендуемые шаги":

* Сквозная задержка
* Задержка сервера
* Клиент-Задержка

В **операции GetBlob** с **RequestStatus (SAS)NetworkError**, если **Max Time** тратится в **клиентской задержке,** наиболее распространенной проблемой является то, что клиент отключается до истечения тайм-аута в службе хранения.

**Рекомендации:**

* Изучите код клиента, чтобы понять, когда и почему клиент отключается от службы хранилища.
* Используйте Wireshark, Microsoft Message Analyzer или Tcping для изучения проблем сетевого подключения клиента. 

### <a name="put-operation-requeststatus--success"></a>Операция «Пут»: ЗапросСтатус и успех

Проверьте следующие значения, упомянутые в шаге 5 раздела "Рекомендуемые шаги":

* Сквозная задержка
* Задержка сервера
* Клиент-Задержка

В **операции Put** с **RequestStatus и Успехом,** если **время Max** тратится на **задержку клиента,** это означает, что клиенту требуется больше времени для отправки данных в хранилище Azure. Эта задержка указывает на проблему клиента-стороны.

**Рекомендации:**

* Исследуйте код в вашем клиенте.
* Используйте Wireshark, Microsoft Message Analyzer или Tcping для изучения проблем сетевого подключения клиента. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Операция по ставить: RequestStatus (SAS)NetworkError

Проверьте следующие значения, упомянутые в шаге 5 раздела "Рекомендуемые шаги":

* Сквозная задержка
* Задержка сервера
* Клиент-Задержка

В **операции PutBlob** с **RequestStatus (SAS)NetworkError**, если **Max Time** тратится в **клиентской задержке,** наиболее распространенной проблемой является то, что клиент отключается до истечения тайм-аута в службе хранения.

**Рекомендации:**

* Изучите код клиента, чтобы понять, когда и почему клиент отключается от службы хранилища.
* Используйте Wireshark, Microsoft Message Analyzer или Tcping для изучения проблем сетевого подключения клиента.

