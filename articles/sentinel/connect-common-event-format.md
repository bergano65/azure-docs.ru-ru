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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 51e6c74a8b80b94ca552645cfbb76bd4e162a62b
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650066"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Подключение внешнего решения с помощью общего формата событий


При подключении внешнего решения, отправляющего сообщения CEF, необходимо выполнить три шага подключения с помощью Azure Sentinel:

Шаг 1. [Подключение CEF путем развертывания агента](connect-cef-agent.md) шаг 2. [выполнение действий, связанных с решением](connect-cef-solution-config.md) шаг 3. [Проверка подключения](connect-cef-verify.md)

В этой статье описывается, как работает подключение, приводятся необходимые условия и предоставляются шаги по развертыванию агента в решениях по обеспечению безопасности, которые отправляют сообщения в формате общего события (CEF) поверх системного журнала. 

> [!NOTE] 
> Данные хранятся в географическом расположении рабочей области, в которой выполняется Sentinel-метка Azure.

Чтобы установить это подключение, необходимо развернуть агент на выделенном компьютере Linux (виртуальную машину или локальную) для поддержки взаимодействия между устройством и Sentinel-метками Azure. На следующей схеме описывается настройка в событии виртуальной машины Linux в Azure.

 ![CEF в Azure](./media/connect-cef/cef-syslog-azure.png)

Кроме того, эта установка будет существовать, если вы используете виртуальную машину в другом облаке или на локальном компьютере. 

 ![CEF в локальной среде](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Замечания по безопасности

Обязательно настройте безопасность компьютера в соответствии с политикой безопасности вашей организации. Например, можно настроить сеть для согласования с политикой безопасности корпоративной сети и изменить порты и протоколы в управляющей программе в соответствии с вашими требованиями. Для улучшения конфигурации безопасности компьютера можно использовать следующие инструкции:  [безопасная виртуальная машина в Azure](../virtual-machines/security-policy.md), рекомендации [по сетевой безопасности](../security/fundamentals/network-best-practices.md).

Чтобы использовать TLS-связь между решением безопасности и компьютером syslog, необходимо настроить управляющую программу syslog (rsyslog или syslog-ng) для обмена данными в TLS: [шифрование трафика syslog с помощью TLS — rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [Шифрование сообщений журнала с помощью TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Предварительные требования
Убедитесь, что компьютер Linux, используемый в качестве прокси-сервера, работает под управлением одной из следующих операционных систем:

- 64-разрядная
  - CentOS 6 и 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 и 7
  - Red Hat Enterprise Linux Server 6 и 7
  - Debian GNU/Linux 8 и 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS и 18.04 LTS
  - SUSE Linux Enterprise Server 12
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



## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить устройства CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

