---
title: Развертывание сервера пересылки журналов для подключения данных CEF к Azure Sentinel | Документация Майкрософт
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: a54dfa0f2b072d30cac605937a1b623ef9d4051d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631500"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Шаг 1. Развертывание сервера пересылки журналов


На этом шаге вы назначите и настроите компьютер Linux, который будет пересылать журналы из решения безопасности в рабочую область Azure Sentinel. Это может быть физический компьютер или виртуальная машина в локальной среде, ВИРТУАЛЬная машина Azure или ВИРТУАЛЬная машина в другом облаке. Используя указанную ссылку, вы запустите сценарий на указанном компьютере, который выполняет следующие задачи:
- Устанавливает агент Log Analytics для Linux (также известный как агент OMS) и настраивает его для следующих целей:
    - Прослушивание сообщений CEF из встроенной управляющей программы Linux syslog на TCP-порте 25226
    - Безопасная отправка сообщений по протоколу TLS в рабочую область "Sentinel" Azure, где они анализируются и дополнены

- Настраивает встроенную управляющую программу Linux syslog (rsyslog. d/syslog-ng) в следующих целях:
    - Прослушивание сообщений системного журнала в решениях безопасности через TCP-порт 514
    - Пересылка только тех сообщений, которые он идентифицирует как CEF агенту Log Analytics на localhost с помощью TCP-порта 25226
 
## <a name="prerequisites"></a>Предварительные требования

- Необходимо иметь повышенные разрешения (sudo) на назначенном компьютере Linux.
- На компьютере Linux должна быть установлена Python.<br>Используйте `python -version` команду для проверки.
- Перед установкой агента Log Analytics компьютер Linux не должен быть подключен к рабочим областям Azure.

## <a name="run-the-deployment-script"></a>Выполнение скрипта развертывания
 
1. В меню навигации меток Azure щелкните **соединители данных**. В списке соединителей щелкните плитку **общий формат событий (CEF)** , а затем нажмите кнопку **Открыть соединительную страницу** в правом нижнем углу. 

1. В разделе **1,2 Установка СБОРЩИКА CEF на компьютере Linux**скопируйте ссылку, указанную в разделе **выполните следующий сценарий, чтобы установить и применить сборщик CEF**, или из следующего текста:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Во время выполнения скрипта убедитесь, что не получены сообщения об ошибках и предупреждения.

> [!NOTE]
> **Использование одного компьютера для пересылки обычных системных syslog *и* сообщений CEF**
>
> Если вы планируете использовать этот компьютер сервера пересылки журналов для пересылки [сообщений Syslog](connect-syslog.md) , а также CEF, то во избежание дублирования событий в таблицы syslog и CommonSecurityLog:
>
> 1. На каждом исходном компьютере, который отправляет журналы серверу пересылки в формате CEF, необходимо изменить файл конфигурации syslog, чтобы удалить средства, которые используются для отправки сообщений CEF. Таким образом, средства, отправляемые в CEF, не будут отправляться в syslog. Подробные инструкции по выполнению этой задачи см. в статье [Настройка системного журнала в агенте Linux](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. На этих компьютерах необходимо выполнить следующую команду, чтобы отключить синхронизацию агента с конфигурацией syslog в Azure Sentinel. Это гарантирует, что изменение конфигурации, сделанное на предыдущем шаге, не перезаписывается.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

Перейдите к [шагу 2. Настройка решения безопасности для пересылки сообщений CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Описание скрипта развертывания

Ниже приведено описание действий сценария развертывания с помощью команды.

Выберите управляющую программу системного журнала, чтобы просмотреть соответствующее описание.

# <a name="rsyslog-daemon"></a>[Управляющая программа rsyslog](#tab/rsyslog)

1. **Загрузка и установка агента Log Analytics:**

    - Скачивает скрипт установки для агента Log Analytics (OMS) Linux.

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Устанавливает агент Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Настройка конфигурации агента Log Analytics для прослушивания порта 25226 и пересылка сообщений CEF в Azure Sentinel:**

    - Скачивает конфигурацию из репозитория GitHub агента Log Analytics.

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Настройка управляющей программы syslog:**

    - Открывает порт 514 для связи TCP с помощью файла конфигурации системного журнала `/etc/rsyslog.conf` .

    - Настраивает управляющую программу на пересылку сообщений CEF агенту Log Analytics через TCP-порт 25226, вставляя специальный файл конфигурации `security-config-omsagent.conf` в каталог управляющей программы системного журнала `/etc/rsyslog.d/` .

        Содержимое `security-config-omsagent.conf` файла:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Перезапуск управляющей программы системного журнала и агента Log Analytics:**

    - Перезапускает управляющую программу rsyslog.
    
        ```bash
        service rsyslog restart
        ```

    - Перезапускает агент Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Проверка соответствия поля *компьютера* ожидаемым образом:**

    - Гарантирует, что поле " *компьютер* " в источнике системного журнала правильно сопоставлено в агенте log Analytics, выполнив эту команду и перезапустив агент.

        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
            -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
            filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[Демон syslog-ng](#tab/syslogng)

1. **Загрузка и установка агента Log Analytics:**

    - Скачивает скрипт установки для агента Log Analytics (OMS) Linux.

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Устанавливает агент Log Analytics.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Настройка конфигурации агента Log Analytics для прослушивания порта 25226 и пересылка сообщений CEF в Azure Sentinel:**

    - Скачивает конфигурацию из репозитория GitHub агента Log Analytics.

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Настройка управляющей программы syslog:**

    - Открывает порт 514 для связи TCP с помощью файла конфигурации системного журнала `/etc/syslog-ng/syslog-ng.conf` .

    - Настраивает управляющую программу на пересылку сообщений CEF агенту Log Analytics через TCP-порт 25226, вставляя специальный файл конфигурации `security-config-omsagent.conf` в каталог управляющей программы системного журнала `/etc/syslog-ng/conf.d/` .

        Содержимое `security-config-omsagent.conf` файла:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Перезапуск управляющей программы системного журнала и агента Log Analytics:**

    - Перезапускает управляющую программу syslog-ng.
    
        ```bash
        service syslog-ng restart
        ```

    - Перезапускает агент Log Analytics.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Проверка соответствия поля *компьютера* ожидаемым образом:**

    - Гарантирует, что поле " *компьютер* " в источнике системного журнала правильно сопоставлено в агенте log Analytics, выполнив эту команду и перезапустив агент.

        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
            -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
            filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```



## <a name="next-steps"></a>Дальнейшие шаги
В этом документе вы узнали, как развернуть агент Log Analytics, чтобы подключить устройства CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

