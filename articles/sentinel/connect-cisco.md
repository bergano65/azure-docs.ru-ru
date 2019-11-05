---
title: Подключение данных Cisco к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Cisco к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 4985593c6fc86f7d80243990c1cce10ce25a2998
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498729"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Подключение Cisco ASA к Azure Sentinel



В этой статье объясняется, как подключить устройство Cisco ASA к Azure Sentinel. Соединитель данных Cisco ASA позволяет легко подключать журналы Cisco ASA с помощью Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследование. С помощью Cisco ASA в Azure Sentinel вы получите более подробные сведения об использовании Интернета в вашей организации и улучшите возможности его работы по обеспечению безопасности. 


## <a name="how-it-works"></a>Принцип работы

Необходимо развернуть агент на выделенном компьютере Linux (виртуальную машину или локально) для поддержки взаимодействия между Cisco ASA и Azure Sentinel. На следующей схеме описывается настройка в событии виртуальной машины Linux в Azure.

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
 
 
1. На портале Sentinel Azure щелкните **Data Connectors (соединители данных** ) и выберите **Cisco ASA** , а затем **откройте страницу соединителя**. 

1. В разделе **Установка и настройка агента syslog**выберите тип компьютера: Azure, другое облако или локально. 
   > [!NOTE]
   > Так как скрипт на следующем шаге устанавливает агент Log Analytics и подключает компьютер к рабочей области Sentinel Azure, убедитесь, что этот компьютер не подключен к какой-либо другой рабочей области.
1. Необходимо иметь повышенные разрешения (sudo) на компьютере. Убедитесь, что на компьютере установлен Python, выполнив следующую команду: `python –version`

1. Выполните следующий сценарий на прокси-компьютере.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Во время выполнения скрипта убедитесь, что не получены сообщения об ошибках и предупреждения.

 
## <a name="step-2-forward-cisco-asa-logs-to-the-syslog-agent"></a>Шаг 2. Пересылка журналов Cisco ASA в агент системного журнала

Cisco ASA не поддерживает CEF, поэтому журналы отправляются в виде системного журнала, и агент Sentinel Azure знает, как их анализировать, как если бы они были журналами CEF. Настройте Cisco ASA для пересылки сообщений Syslog в рабочую область Azure с помощью агента системного журнала:

Перейдите к разделу [Отправка сообщений системного журнала на внешний сервер syslog](https://aka.ms/asi-syslog-cisco-forwarding)и следуйте инструкциям по настройке подключения. При появлении запроса используйте следующие параметры:
- Задайте для параметра **порт** значение 514 или порт, заданный в агенте.
- Задайте для **syslog_ip** IP-адрес агента.

Чтобы использовать соответствующую схему в Log Analytics для событий Cisco, выполните поиск по запросу `CommonSecurityLog`.

## <a name="step-3-validate-connectivity"></a>Шаг 3. Проверка подключения

1. Откройте Log Analytics, чтобы убедиться, что журналы получены с помощью схемы CommonSecurityLog.<br> Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 

1. Перед запуском скрипта рекомендуется отправить сообщения из решения безопасности, чтобы убедиться, что они перенаправлены на настроенный вами прокси-сервер syslog. 
1. Необходимо иметь повышенные разрешения (sudo) на компьютере. Убедитесь, что на компьютере установлен Python, выполнив следующую команду: `python –version`
1. Выполните следующий сценарий, чтобы проверить подключение между агентом, Sentinel Azure и решением по обеспечению безопасности. Он проверяет правильность настройки перенаправления управляющей программы, прослушивает правильные порты и не блокирует обмен данными между управляющей программой и агентом Log Analytics. Сценарий также отправляет фиктивные сообщения "Тесткоммоневентформат" для проверки сквозного подключения. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`





## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить устройства Cisco ASA к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

