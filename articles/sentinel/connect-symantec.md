---
title: Подключайте данные из Symantec ICDX предварительную версию Sentinel Azure | Документация Майкрософт
description: Узнайте, как подключиться к Azure Sentinel данных Symantec ICDX.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1a6843fb1668307aa442011233999c648901d404
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793478"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Подключение устройства Symantec ICDX 

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Соединитель Symantec ICDX позволяет легко подключить все журналы решений безопасности Symantec с вашей Azure Sentinel, для просмотра панелей мониторинга, создать настраиваемые оповещения и улучшит исследование. Это дает больше возможностей регулировать сети вашей организации и улучшает возможности безопасности операции. Интеграция между Symantec ICDX и Azure Sentinel использует REST API.


> [!NOTE]
> Данные будут храниться в географическое расположение рабочей области, на котором выполняется Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Настройка и подключение Symantec ICDX 

Symantec ICDX можно интегрировать и экспортировать журналы непосредственно в Azure Sentinel.

1. Откройте консоль управления ICDX.
2. В меню навигации слева, выберите **конфигурации** и затем **серверов пересылки** вкладки.
3. В строке Microsoft Azure Log Analytics, щелкните **дополнительные**, за которым следует **изменить**. 
4. В **пересылки службы Microsoft Azure Log Analytics** окна, задайте следующее:
    - Оставьте значение по умолчанию, SymantecICDX имя пользовательского журнала.
    - Скопируйте идентификатор рабочей области и вставьте его в **идентификатор клиента** поля. Копировать **первичный ключ** и вставьте его в поле общего ключа. Вы можете скопировать эти значения с портала Azure Sentinel, выбрав **соединители данных** и затем **Symantec ICDX**.
6. Для соответствующей схемы в Log Analytics можно использовать для событий Symantec ICDX, поиск **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Проверка подключения

Может потребоваться до 20 минут, пока не журналов в Log Analytics. 



## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключиться к Azure Sentinel Symantec ICDX. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

