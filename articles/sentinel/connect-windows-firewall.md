---
title: Сбор данных брандмауэра Windows в предварительной версии Sentinel Azure | Документация Майкрософт
description: Дополнительные сведения о сборе данных брандмауэра Windows в Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2356a7e5426037ffe9fc8b304ac113f4a3fe2a17
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103355"
---
# <a name="connect-windows-firewall"></a>Подключение брандмауэра Windows

> [!IMPORTANT]
> Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Соединитель брандмауэр Windows позволяет легко подключать журналов брандмауэров Windows, если они подключены к рабочей области Azure Sentinel. Это подключение позволяет просматривать панели мониторинга, создать настраиваемые оповещения и улучшит исследование. Это дает больше возможностей регулировать сети вашей организации и улучшает возможности безопасности операции.  


> [!NOTE]
> 
> - Данные будут храниться в географическое расположение рабочей области, на котором выполняется Azure Sentinel.

## <a name="enable-the-connector"></a>Включение соединителя 

1. На портале Azure Sentinel выберите **сбора данных** и выберите команду **брандмауэра Windows** плитку. 
1. Выберите типы данных для потоковой передачи.
1. Щелкните **Install**(Установить).

## <a name="validate-connectivity"></a>Проверка подключения

Может потребоваться до 20 минут, пока не журналов в Log Analytics. 



## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключиться к Azure Sentinel брандмауэра Windows. Дополнительные сведения о Azure Sentinel, см. в разделе со следующими статьями:
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

