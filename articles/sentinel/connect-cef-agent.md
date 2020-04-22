---
title: Развертывание форварда журнала для подключения данных CEF к Azure Sentinel Документы Майкрософт
description: Узнайте, как развернуть агент для подключения данных CEF к Azure Sentinel.
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
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731659"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Шаг 1: Развертывание переверного журнала


На этом этапе вы назначите и настраиваете машину Linux, которая будет перенаправлять журналы из решения безопасности в рабочее пространство Azure Sentinel. Эта машина может быть физической или виртуальной машиной в вашей предварительной среде, VM Azure или VM в другом облаке. Используя предоставленную ссылку, вы запустите скрипт на назначенной машине, выполняющий следующие задачи:
- Устанавливает агент Log Analytics для Linux (также известный как агент OMS) и настраивает его на следующие цели:
    - прослушивание сообщений CEF от встроенного Linux Syslog daemon на порте TCP 25226
    - безопасное отправка сообщений по TLS в рабочее пространство Azure Sentinel, где они разбираются и обогащаются

- Настраивает встроенный Linux Syslog daemon (rsyslog.d/syslog-ng) для следующих целей:
    - прослушивание сообщений Syslog из решений безопасности на порте TCP 514
    - пересылать только сообщения, которые он идентифицирует как CEF, агенту Log Analytics на localhost, используя порт TCP 25226
 
## <a name="prerequisites"></a>Предварительные требования

- Вы должны иметь повышенные разрешения (sudo) на назначенной машине Linux.
- Вы должны иметь питона, установленного на машине Linux.<br>Используйте `python -version` команду для проверки.
- Машина Linux не должна быть подключена к каким-либо рабочим пространствам Azure перед установкой агента Log Analytics.

## <a name="run-the-deployment-script"></a>Выполнение скрипта развертывания
 
1. Из меню навигации Azure Sentinel щелкните **разъемы данных.** Из списка разъемов щелкните плитку **Common Event Format (CEF),** а затем кнопку **"Открытая страница разъема"** в правом нижнем правом. 

1. В соответствии с **1.2 Установить коллектор CEF на машине Linux**, скопировать ссылку, предоставленную в соответствии **с Выполнить следующий сценарий для установки и применения коллектора CEF**, или из текста ниже:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Во время работы скрипта проверьте, чтобы не получить никаких ошибок или предупреждающих сообщений.

Продолжить [STEP 2: Нанастройка решения безопасности для переадресовки сообщений CEF.](connect-cef-solution-config.md)

## <a name="deployment-script-explained"></a>Сценарий развертывания объяснил

Ниже приводится командное описание действий сценария развертывания.

Выберите syslog daemon, чтобы увидеть соответствующее описание.

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. **Загрузка и установка агента Log Analytics:**

    - Загружает сценарий установки для агента Linux Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Устанавливает агент Log Analytics<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Настройка сайслога:**

    1. Открывает порт 514 для связи TCP с `/etc/rsyslog.conf`помощью файла конфигурации syslog.

    1. Настраивает daemon для того чтобы препровождать сообщения CEF к агенту аналитики журнала на `security-config-omsagent.conf` порте TCP 25226, путем вставлять специальный архив конфигурации в каталог `/etc/rsyslog.d/`syslog daemon.

        Содержимое `security-config-omsagent.conf` файла:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **Перезапуск Сайслог-димона**

    `service rsyslog restart`

1. **Настройка конфигурации агента log Analytics для прослушивания в порте 25226 и переадресации сообщений CEF в Azure Sentinel**

    1. Загрузка конфигурации из репозитория Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Перезагрузка агента Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[Syslog-нг-демон](#tab/syslogng)

1. **Загрузка и установка агента Log Analytics:**

    - Загружает сценарий установки для агента Linux Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Устанавливает агент Log Analytics<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Настройка сайслога:**

    1. Открывает порт 514 для связи TCP с `/etc/syslog-ng/syslog-ng.conf`помощью файла конфигурации syslog.

    1. Настраивает daemon для того чтобы препровождать сообщения CEF к агенту аналитики журнала на `security-config-omsagent.conf` порте TCP 25226, путем вставлять специальный архив конфигурации в каталог `/etc/syslog-ng/conf.d/`syslog daemon.

        Содержимое `security-config-omsagent.conf` файла:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **Перезапуск Сайслог-димона**

    `service syslog-ng restart`

1. **Настройка конфигурации агента log Analytics для прослушивания в порте 25226 и переадресации сообщений CEF в Azure Sentinel**

    1. Загрузка конфигурации из репозитория Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Перезагрузка агента Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как развернуть агент Log Analytics для подключения приборов CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

