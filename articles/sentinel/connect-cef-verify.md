---
title: Проверка подключения к Azure Sentinel Документы Майкрософт
description: Проверка подключения решения безопасности, чтобы убедиться, что сообщения CEF перенаправляются в Azure Sentinel.
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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731815"
---
# <a name="step-3-validate-connectivity"></a>ШАГ 3: Проверка подключения

После того, как вы развернули форварджурнала журнала (в шаге 1) и настроили решение безопасности для отправки сообщений CEF (в шаге 2), следуйте этим инструкциям для проверки связи между решением безопасности и Azure Sentinel. 

## <a name="prerequisites"></a>Предварительные требования

- Вы должны иметь повышенные разрешения (sudo) на вашем журнале перегонов машины.

- Вы должны иметь Python установлен на вашем журнале перегоняющего машины.<br>
Используйте `python –version` команду для проверки.

## <a name="how-to-validate-connectivity"></a>Как проверить подключение

1. Из меню навигации Azure Sentinel, **открытые журналы**. Запустите запрос с помощью схемы **CommonSecurityLog,** чтобы узнать, получают ли журналы из решения безопасности.<br>
Имейте в виду, что это может занять около 20 минут, пока ваши журналы начинают появляться в **журнале Analytics**. 

1. Если вы не видите каких-либо результатов из запроса, убедитесь, что события генерируются из решения безопасности, или попробуйте создать некоторые из них, и убедитесь, что они направляются в назначенную вами машину syslog. 

1. Запустите следующий скрипт на форварде журнала, чтобы проверить связь между решением безопасности, форвардом журнала и Azure Sentinel. Этот скрипт проверяет, что daemon слушает на правильных портах, что пересылка правильно настроена, и что ничто не блокирует связь между агентом daemon и Log Analytics. Он также отправляет макет сообщения 'TestCommonEventFormat', чтобы проверить сквозное подключение. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Сценарий проверки объяснил

Скрипт проверки выполняет следующие проверки:

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. Проверяет, что файл<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    существует и является действительным.

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

1. Проверка, есть ли какие-либо улучшения безопасности на машине, которые могут блокировать сетевой трафик (например, брандмауэр host).

1. Проверяет, что syslog daemon (rsyslog) правильно настроен для отправки сообщений, которые он идентифицирует как CEF (с помощью regex) агенту Log Analytics на порте TCP 25226:

    - Файл конфигурации:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Проверяет, что syslog daemon получает данные о порту 514

1. Проверяет, что необходимые соединения установлены: tcp 514 для получения данных, tcp 25226 для внутренней связи между syslog daemon и агентом Log Analytics

1. Отправляет данные MOCK в порт 514 на localhost. Эти данные должны быть запозваны в рабочей области Azure Sentinel при запуске следующего запроса:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[Syslog-нг-демон](#tab/syslogng)

1. Проверяет, что файл<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    существует и является действительным.

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

1. Проверка, есть ли какие-либо улучшения безопасности на машине, которые могут блокировать сетевой трафик (например, брандмауэр host).

1. Проверяет, что syslog daemon (syslog-ng) правильно настроен для отправки сообщений, которые он идентифицирует как CEF (с помощью regex) агенту Log Analytics на порте TCP 25226:

    - Файл конфигурации:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Проверяет, что syslog daemon получает данные о порту 514

1. Проверяет, что необходимые соединения установлены: tcp 514 для получения данных, tcp 25226 для внутренней связи между syslog daemon и агентом Log Analytics

1. Отправляет данные MOCK в порт 514 на localhost. Эти данные должны быть запозваны в рабочей области Azure Sentinel при запуске следующего запроса:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить приборы CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.

