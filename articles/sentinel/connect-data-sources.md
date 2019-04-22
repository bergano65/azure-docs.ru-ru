---
title: Подключение источников данных к предварительной версии Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить источники данных к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: ba0f584e8026fe3828ec79c4b6c0ff5a0bb89f5a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492368"
---
# <a name="connect-data-sources"></a>Подключение источников данных

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Чтобы подключить Azure Sentinel, необходимо сначала подключиться к источникам данных. Azure Sentinel поставляется с несколькими соединителями для решений Майкрософт, которые доступны по умолчанию и обеспечивают интеграцию в режиме реального времени, включая решения Microsoft Threat Protection, источники Microsoft 365, Office 365, Azure AD, Azure ATP, Microsoft Cloud App Security и многое другое. Кроме того, доступны встроенные соединители для более широкой экосистемы безопасности для решений сторонних разработчиков. Чтобы подключить источники данных к Azure Sentinel, вы также можете использовать общий формат событий, Syslog или REST API.  

![Сборщики данных](./media/collect-data/collect-data-page.png)

## <a name="data-connection-methods"></a>Методы подключения к данным

Azure Sentinel поддерживает следующие методы подключения к данным:

- **Службы Майкрософт**.<br> Службы Майкрософт подключены в собственном коде с помощью платформы Azure для готовой интеграции. Следующие решения можно подключить несколькими щелчками мыши.
    - [Office 365](connect-office-365.md)
    - [Данные для входа и журналы аудита Azure AD](connect-azure-active-directory.md)
    - [Действия Azure](connect-azure-activity.md)
    - [защиту идентификации Azure AD](connect-azure-ad-Identity-protection.md)
    - [Центр безопасности Azure](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [События безопасности Windows](connect-windows-security-events.md) 
    - [Брандмауэр Windows](connect-windows-firewall.md)

- **Внешние решения через API**. Некоторые источники данных подключены с помощью интерфейсов API, предоставляемых подключенным источником данных. Как правило, большинство технологий безопасности предоставляют набор API, с помощью которых можно получить журналы событий. API подключаются к Azure Sentinel и собирают определенные типы данных, а затем отправляют их в Azure Log Analytics. Устройства, подключенные с помощью API:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Внешние решения через агента**. Azure Sentinel может подключаться ко всем источникам данных, которые могут выполнять потоковую передачу журнала в режиме реального времени с помощью протокола Syslog, через агента. <br>Большинство устройств использует протокол Syslog для отправки сообщений о событиях, которые включают сам журнал и данные о нем. Журналы имеют разный формат, но большинство устройств поддерживает стандарт общего формата событий (CEF). <br>Агент Azure Sentinel, основанный на агенте OMS, преобразует журналы в формате CEF в формат, который принимается службой Log Analytics. В зависимости от типа устройства агент устанавливается или непосредственно на устройстве, или на выделенном сервере Linux. Агент для Linux получает события из управляющей программы Syslog по протоколу UDP. В случаях, когда машина Linux должна собирать большое количество событий Syslog, они отправляются по протоколу TCP из управляющей программы Syslog в агент, а затем — в Log Analytics.
    - Брандмауэры, прокси-серверы и конечные точки.
        - [F5](connect-f5.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Пало-Альто](connect-paloalto.md)
        - [Другие устройства CEF](connect-common-event-format.md)
        - [Другие устройства Syslog](connect-syslog.md)
    - Решения DLP
    - [Поставщики Threat Intelligence](connect-threat-intelligence.md)
    - [Компьютеры DNS](connect-dns.md) — агент установлен непосредственно на компьютере DNS
    - Серверы Linux
    - Другие облака
    
## Параметры подключения агента<a name="agent-options"></a>

Чтобы подключить внешнее устройство к Azure Sentinel, агент должен быть развернут на выделенном компьютере (виртуальной машине или локальном компьютере) для поддержки связи между устройством и Azure Sentinel. Вы можете развернуть агент автоматически или вручную. Автоматическое развертывание доступно только в том случае, если выделенный компьютер является новой созданной в Azure виртуальной машиной. 


![CEF в Azure](./media/connect-cef/cef-syslog-azure.png)

Вместо этого вы можете развернуть агент вручную на существующей виртуальной машине Azure, на виртуальной машине в другом облаке или на локальном компьютере.

![CEF в локальной среде](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>Дополнительная информация

- Чтобы начать работу с Azure Sentinel, вам нужна подписка на Microsoft Azure. Если у вас нет подписки, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/free/).
- Узнайте, как [подключить данные к Azure Sentinel](quickstart-onboard.md), и [отслеживайте свои данные и потенциальные угрозы](quickstart-get-visibility.md).
