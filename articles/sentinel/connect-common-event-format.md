---
title: Подключение данных CEF к предварительной версии Azure Sentinel | Документация Майкрософт
description: Подключите внешнее решение, которое отправляет сообщения в формате распространенных событий (CEF) в Azure Sentinel с помощью компьютера Linux в качестве прокси-сервера.
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
ms.openlocfilehash: d63893ab219854a270652da38c474e3ccad83abc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630514"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Подключение внешнего решения с помощью общего формата событий

При подключении внешнего решения, отправляющего сообщения CEF, необходимо выполнить три шага подключения с помощью Azure Sentinel:

Шаг 1. [Подключение CEF путем развертывания syslog/CEF сервера пересылки](connect-cef-agent.md) шаг 2. [выполнение действий, связанных с решением](connect-cef-solution-config.md) шаг 3. [Проверка подключения](connect-cef-verify.md)

В этой статье описывается, как работает подключение, приводятся необходимые условия, а также приведены инструкции по развертыванию агента в решениях по обеспечению безопасности, которые отправляют сообщения в формате общего события (CEF) поверх системного журнала. 

> [!NOTE] 
> Данные хранятся в географическом расположении рабочей области, в которой выполняется Sentinel-метка Azure.

Чтобы установить это подключение, необходимо развернуть сервер сервера пересылки syslog для поддержки взаимодействия между устройством и Azure Sentinel.  Сервер состоит из выделенного компьютера Linux (виртуальной машины или локальной среды) с установленным агентом Log Analytics для Linux. 

Следующая схема описывает настройку в событии виртуальной машины Linux в Azure.

 ![CEF в Azure](./media/connect-cef/cef-syslog-azure.png)

Кроме того, эта установка будет существовать, если вы используете виртуальную машину в другом облаке или на локальном компьютере: 

 ![CEF в локальной среде](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Вопросы безопасности

Обязательно настройте безопасность компьютера в соответствии с политикой безопасности вашей организации. Например, можно настроить сеть для согласования с политикой безопасности корпоративной сети и изменить порты и протоколы в управляющей программе в соответствии с вашими требованиями. Для улучшения конфигурации безопасности компьютера можно использовать следующие инструкции:  [безопасная виртуальная машина в Azure](../virtual-machines/security-policy.md), рекомендации [по сетевой безопасности](../security/fundamentals/network-best-practices.md).

Чтобы использовать TLS-связь между источником syslog и сервером пересылки syslog, необходимо настроить управляющую программу syslog (rsyslog или syslog-ng) для взаимодействия в TLS: [шифрование трафика syslog с помощью TLS — rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [Шифрование сообщений журнала с помощью TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).
 
## <a name="prerequisites"></a>Предварительные требования

Убедитесь, что компьютер Linux, используемый в качестве прокси-сервера, работает под управлением одной из следующих операционных систем:

- 64-разрядная
  - CentOS 7 и подверсии и выше (не 6)
  - Amazon Linux 2017.09
  - Oracle Linux 6 и 7
  - Red Hat Enterprise Linux (RHEL) Server 7 и подверсии и выше (не 6)
  - Debian GNU/Linux 8 и 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS и 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32-битная
   - CentOS 7
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 7
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
    - Убедитесь, что на вашем компьютере установлен Python (2,7 или более поздней версии)

## <a name="next-steps"></a>Дальнейшие шаги

В этом документе вы узнали, как подключить устройства CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

