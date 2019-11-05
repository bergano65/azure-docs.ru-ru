---
title: Подключение данных Fortinet к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Fortinet к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 7a44d63b834a7b6b580909005a440637bf730918
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475787"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Подключение Fortinet к Azure Sentinel



В этой статье объясняется, как подключить устройство Fortinet к Azure Sentinel. Соединитель данных Fortinet позволяет легко подключать журналы Fortinet с помощью Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследование. Использование Fortinet в Azure Sentinel предоставит вам более подробные сведения об использовании Интернета в вашей организации и улучшит возможности ее работы. 


## <a name="how-it-works"></a>Принцип работы

Необходимо развернуть агент на выделенном компьютере Linux (виртуальную машину или локально) для поддержки обмена данными между Fortinet и Sentinel. На следующей схеме описывается настройка в событии виртуальной машины Linux в Azure.

 ![CEF в Azure](./media/connect-cef/cef-syslog-azure.png)

Кроме того, эта установка будет существовать, если вы используете виртуальную машину в другом облаке или на локальном компьютере. 

 ![CEF в локальной среде](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Вопросы безопасности

Обязательно настройте безопасность компьютера в соответствии с политикой безопасности вашей организации. Например, можно настроить сеть для согласования с политикой безопасности корпоративной сети и изменить порты и протоколы в управляющей программе в соответствии с вашими требованиями. Для улучшения конфигурации безопасности компьютера можно использовать следующие инструкции:  [безопасная виртуальная машина в Azure](../virtual-machines/linux/security-policy.md), рекомендации [по сетевой безопасности](../security/fundamentals/network-best-practices.md).

Чтобы использовать TLS-связь между решением безопасности и компьютером syslog, необходимо настроить управляющую программу syslog (rsyslog или syslog-ng) для взаимодействия в TLS: [шифрование трафика syslog с помощью TLS — rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [Шифрование сообщений журнала с помощью TLS — syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Технические условия
Убедитесь, что компьютер Linux, используемый в качестве прокси-сервера, работает под управлением одной из следующих операционных систем:

- 64-разрядная
  - CentOS 6 и 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 и 7
  - Red Hat Enterprise Linux Server 6 и 7
  - Debian GNU/Linux 8 и 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS и 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32-битная
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 и 9
   - Ubuntu Linux 14.04 LTS и 16.04 LTS
 
 - Версии управляющей программы
   - Syslog-ng: 2,1-3.22.1
   - Rsyslog: V8
  
 - Поддерживаемые документы RFC системного журнала
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Убедитесь, что компьютер соответствует следующим требованиям: 
- Разрешения
    - Необходимо иметь повышенные разрешения (sudo) на компьютере. 
- Требования к программному обеспечению
    - Убедитесь, что на вашем компьютере установлен Python.
## <a name="step-1-deploy-the-agent"></a>Шаг 1. Развертывание агента

На этом шаге необходимо выбрать компьютер Linux, который будет использоваться в качестве прокси-сервера между Sentinel Azure и решением безопасности. Вам потребуется запустить сценарий на прокси-компьютере, который:
- Устанавливает агент Log Analytics и настраивает его по мере необходимости для прослушивания сообщений Syslog через порт 514 через TCP и отправляет сообщения CEF в рабочую область "Sentinel" Azure.
- Настраивает управляющую программу syslog для пересылки CEF сообщений агенту Log Analytics с помощью порта 25226.
- Устанавливает агент системного журнала для получения данных и их безопасного отправку в Log Analytics, где они анализируются и расширяются.
 
 
1. На портале Sentinel Azure щелкните **Data Connectors (соединители данных** ), выберите **Fortinet** , а затем **откройте страницу соединителя**. 

1. В разделе **Установка и настройка агента syslog**выберите тип компьютера: Azure, другое облако или локально. 
   > [!NOTE]
   > Так как скрипт на следующем шаге устанавливает агент Log Analytics и подключает компьютер к рабочей области Sentinel Azure, убедитесь, что этот компьютер не подключен к какой-либо другой рабочей области.
1. Необходимо иметь повышенные разрешения (sudo) на компьютере. Убедитесь, что на компьютере установлен Python, выполнив следующую команду: `python –version`

1. Выполните следующий сценарий на прокси-компьютере.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Во время выполнения скрипта убедитесь, что не получены сообщения об ошибках и предупреждения.

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Шаг 2. Пересылка журналов Fortinet в агент системного журнала

Настройте Fortinet для пересылки сообщений системного журнала в формате CEF в рабочую область Azure с помощью агента системного журнала.

1. Откройте интерфейс командной строки на устройстве Fortinet и выполните следующие команды:

        config log syslogd setting
        set format cef
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Замените **IP-адрес** сервера IP-адресом агента.
    - Задайте для **порта системного журнала** значение **514** или порт, установленный на агенте.
    - Чтобы включить формат CEF в ранних версиях Фортиос, может потребоваться выполнить команду Set **CSV Disabled**.
 
   > [!NOTE] 
   > Для получения дополнительных сведений перейдите в [библиотеку документов Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Выберите свою **версию и воспользуйтесь** **справочной ссылкой и сообщением журнала**.

 Чтобы использовать соответствующую схему в Azure Monitor Log Analytics для событий Fortinet, выполните поиск по запросу `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Шаг 3. Проверка подключения

1. Откройте Log Analytics, чтобы убедиться, что журналы получены с помощью схемы CommonSecurityLog.<br> Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 

1. Перед запуском скрипта рекомендуется отправить сообщения из решения безопасности, чтобы убедиться, что они перенаправлены на настроенный вами прокси-сервер syslog. 
1. Необходимо иметь повышенные разрешения (sudo) на компьютере. Убедитесь, что на компьютере установлен Python, выполнив следующую команду: `python –version`
1. Выполните следующий сценарий, чтобы проверить подключение между агентом, Sentinel Azure и решением по обеспечению безопасности. Он проверяет правильность настройки перенаправления управляющей программы, прослушивает правильные порты и не блокирует обмен данными между управляющей программой и агентом Log Analytics. Сценарий также отправляет фиктивные сообщения "Тесткоммоневентформат" для проверки сквозного подключения. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`




## <a name="next-steps"></a>Дальнейшие действия
Из этой статьи вы узнали, как подключить устройства Fortinet к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

