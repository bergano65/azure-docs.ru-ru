---
title: Подключение данных CEF к preview Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные CEF к Azure Sentinel.
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
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588354"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Подключите внешнее решение с помощью общего формата событий


При подключении внешнего решения, отправляющего сообщения CEF, существует три шага для подключения к Azure Sentinel:

Шаг 1: [Подключите CEF, развернув агент](connect-cef-agent.md) STEP 2: [Выполните конкретные решения ШАГ](connect-cef-solution-config.md) 3: Проверка [подключения](connect-cef-verify.md)

В этой статье описывается, как работает соединение, обеспечивается предпосылки и приведены шаги для развертывания агента в решениях безопасности, которые отправляют сообщения Common Event Format (CEF) поверх Syslog. 

> [!NOTE] 
> Данные хранятся в географическом местоположении рабочего пространства, на котором работает Azure Sentinel.

Для того, чтобы сделать это соединение, необходимо развернуть агента на специальном аппарате Linux (VM или в помещениях) для поддержки связи между прибором и Azure Sentinel. Следующая диаграмма описывает настройку в случае Linux VM в Azure.

 ![CEF в Azure](./media/connect-cef/cef-syslog-azure.png)

Кроме того, эта настройка будет существовать, если вы используете VM в другом облаке или в предварительном компьютере. 

 ![CEF в локальной среде](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Замечания по безопасности

Убедитесь в том, чтобы настроить безопасность машины в соответствии с политикой безопасности вашей организации. Например, можно настроить сеть в соответствии с корпоративной политикой безопасности сети и изменить порты и протоколы в daemon в соответствии с вашими требованиями. Вы можете использовать следующие инструкции для улучшения конфигурации безопасности машины:  [Secure VM in Azure,](../virtual-machines/linux/security-policy.md) [рекомендации по безопасности сети.](../security/fundamentals/network-best-practices.md)

Для использования TLS связи между решением безопасности и syslog машины, вам нужно будет настроить Syslog daemon (rsyslog или syslog-ng) для общения в TLS: [Шифрование Syslog движения с TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [шифрование журналов сообщений с TLS -syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Предварительные требования
Убедитесь, что машина Linux, которую вы используете в качестве прокси-сервера, работает с одной из следующих операционных систем:

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
 
 - Версии Daemon
   - Сыслог-нг: 2.1 - 3.22.1
   - Rsyslog: v8
  
 - Syslog RFCs поддерживается
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Убедитесь, что ваша машина также отвечает следующим требованиям: 
- Разрешения
    - Вы должны иметь повышенные разрешения (судо) на вашей машине. 
- Требования к программному обеспечению
    - Убедитесь, что у вас есть Python работает на вашей машине



## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить приборы CEF к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

