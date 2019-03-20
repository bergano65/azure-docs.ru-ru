---
title: Имя издателя и ключ издателя в службах BizTalk | Документация Майкрософт
description: Узнайте, как извлечь имя и ключ издателя для Service Bus или управления доступом (ACS) в службах BizTalk. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 5eac98ec88b960956c9a0931673e67f530aef8da
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542188"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>Службы BizTalk. Имя издателя и ключ издателя

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

В службах BizTalk в Azure используются имя и ключ издателя Service Bus, а также имя и ключ издателя службы Access Control. В частности:

| Задача | Используемые имя и ключ издателя |
| --- | --- |
| Развертывание приложения из Visual Studio |Имя и ключ издателя для службы Access Control |
| Настройка портала служб BizTalk в Azure |Имя и ключ издателя для службы Access Control |
| Создание бизнес-ретрансляций со службами адаптера BizTalk в Visual Studio |Имя и ключ издателя шины обслуживания |

В этом разделе перечислены шаги для извлечения имени и ключа издателя. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Имя и ключ издателя для службы Access Control
Имя и ключ издателя для службы Access Control используются в следующих областях:

* Приложение службы BizTalk Azure, созданные в Visual Studio: Для успешного развертывания приложения служба BizTalk в Visual Studio в Azure, введите имя издателя контроля доступа и ключ издателя. 
* На портале служб Azure BizTalk: При создании службы BizTalk и откройте на портале служб BizTalk, имя издателя контроля доступа и ключ издателя автоматически регистрируются для развертываний с теми же значениями контроля доступа.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Имя и ключ издателя для службы контроля доступа

Чтобы использовать ACS для аутентификации и получить значения имени и ключа издателя, сделайте следующее:

1. Установите [командлеты Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Добавьте учетную запись Azure: `Add-AzureAccount`.
3. Получите имя подписки: `get-azuresubscription`.
4. Выберите свою подписку: `select-azuresubscription <name of your subscription>`. 
5. Создайте пространство имен: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`.

    Пример:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. При создании пространства имен ACS (занимает несколько минут) значения имени и ключа издателя указываются в строке подключения: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Итог:  
имя издателя — SharedSecretIssuer;  
ключ издателя — SharedSecretKey.

Дополнительные сведения о командлете [New-AzureSBNamespace](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace). 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Имя и ключ издателя шины обслуживания
Имя и ключ издателя шины обслуживания используются службами адаптера BizTalk. В проекте служб BizTalk в Visual Studio можно использовать службы адаптера BizTalk для подключения к локальной бизнес-системе. Для подключения создайте бизнес-ретранслятор и введите сведения о бизнес-системе. При этом также вводится имя и ключ издателя шины обслуживания.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Чтобы извлечь имя и ключ издателя Service Bus
1. Войдите на [портале Azure](https://portal.azure.com).
2. Выполните поиск по фразе **Service Bus** и выберите свое пространство имен. 
3. Откройте вкладку свойств **Политики общего доступа**, выберите политику и просмотрите **строку подключения** для этих значений ключей и имени.  

## <a name="next"></a>Далее
Дополнительная информация о службах BizTalk в Azure

* [Установка пакета SDK для служб BizTalk Azure](https://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Учебники: Службы Azure BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Как приступить к работе с пакетом SDK для служб BizTalk Azure](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Службы BizTalk Azure](https://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>См. также
* [How to: Использованию службы управления ACS для настройки удостоверения службы](https://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [Службы BizTalk. Developer, Basic, Standard и Premium диаграмма выпусков](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Службы BizTalk. Подготовка](https://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Службы BizTalk. Схема состояний подготовки](https://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Службы BizTalk. Вкладки панели мониторинга, мониторинг и масштабирование](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Службы BizTalk. Резервное копирование и восстановление](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Службы BizTalk. Регулирование](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

