---
title: Проверка подключения к Azure Sentinel | Документация Майкрософт
description: Проверьте подключение решения безопасности, чтобы убедиться, что CEF сообщения перенаправляются в Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731815"
---
# <a name="step-3-validate-connectivity"></a>Шаг 3. Проверка подключения

После развертывания сервера пересылки журналов (на шаге 1) и настройки решения безопасности для отправки сообщений CEF (на шаге 2) выполните следующие инструкции, чтобы проверить подключение между решением безопасности и Sentinel. 

## <a name="prerequisites"></a>Предварительные условия

- Необходимо иметь повышенные разрешения (sudo) на компьютере сервера пересылки журналов.

- На компьютере сервера пересылки журналов должна быть установлена Python.<br>
Используйте `python –version` команду для проверки.

## <a name="how-to-validate-connectivity"></a>Проверка подключения

1. В меню навигации меток Azure откройте **журналы**. Выполните запрос с помощью схемы **CommonSecurityLog** , чтобы узнать, поступают ли журналы из решения по обеспечению безопасности.<br>
Имейте в виду, что журналы в **log Analytics**могут занять около 20 минут. 

1. Если результаты запроса не отображаются, убедитесь, что события формируются из решения безопасности, или попытайтесь создать некоторые из них и убедитесь, что они перенаправлены на указанный компьютер сервера пересылки системного журнала. 

1. Выполните следующий скрипт на стороне сервера пересылки журналов, чтобы проверить подключение между решением безопасности, перенаправителем журналов и Sentinel Azure. Этот сценарий проверяет, что управляющая программа прослушивает правильные порты, что пересылка настроена правильно и что ничто не блокирует обмен данными между управляющей программой и агентом Log Analytics. Он также отправляет фиктивные сообщения "Тесткоммоневентформат" для проверки сквозного подключения. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Описание скрипта проверки

Скрипт проверки выполняет следующие проверки:

# <a name="rsyslog-daemon"></a>[Управляющая программа rsyslog](#tab/rsyslog)

1. Проверяет, что файл<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    существует и является допустимым.

1. Проверяет, что файл содержит следующий текст:

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. Проверяет наличие каких-либо улучшений безопасности на компьютере, которые могут блокировать сетевой трафик (например, брандмауэр узла).

1. Проверяет, что управляющая программа системного журнала (rsyslog) правильно настроена для отправки сообщений, которые она идентифицирует как CEF (с помощью регулярного выражения) агенту Log Analytics через TCP-порт 25226:

    - Файл конфигурации:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Проверяет, что управляющая программа системного журнала получает данные через порт 514

1. Проверяет, что установлены необходимые подключения: TCP 514 для получения данных, TCP 25226 для внутреннего взаимодействия между управляющей программой syslog и агентом Log Analytics

1. Отправляет ФИКТИВные данные на порт 514 на localhost. Эти данные должны быть наблюдаемыми в рабочей области Sentinel Azure, выполнив следующий запрос:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[Демон syslog-ng](#tab/syslogng)

1. Проверяет, что файл<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    существует и является допустимым.

1. Проверяет, что файл содержит следующий текст:

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. Проверяет наличие каких-либо улучшений безопасности на компьютере, которые могут блокировать сетевой трафик (например, брандмауэр узла).

1. Проверяет, правильно ли настроена управляющая программа syslog (syslog-ng) для отправки сообщений, идентифицированных как CEF (с помощью регулярного выражения), в агент Log Analytics через TCP-порт 25226:

    - Файл конфигурации:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Проверяет, что управляющая программа системного журнала получает данные через порт 514

1. Проверяет, что установлены необходимые подключения: TCP 514 для получения данных, TCP 25226 для внутреннего взаимодействия между управляющей программой syslog и агентом Log Analytics

1. Отправляет ФИКТИВные данные на порт 514 на localhost. Эти данные должны быть наблюдаемыми в рабочей области Sentinel Azure, выполнив следующий запрос:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Дальнейшие шаги
В этом документе вы узнали, как подключить устройства CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте книги](tutorial-monitor-your-data.md) для отслеживания данных.

