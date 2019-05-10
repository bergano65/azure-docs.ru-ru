---
title: Подключайте данные из брандмауэра Windows для предварительной версии Sentinel Azure | Документация Майкрософт
description: Узнайте, как подключиться к Azure Sentinel данных брандмауэра Windows.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 9388e267c52ef53b59aacad844e964d3cfeb13d7
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233823"
---
# <a name="connect-windows-firewall"></a>Подключение брандмауэра Windows

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Соединитель брандмауэр Windows позволяет легко подключать журналов брандмауэров Windows, если они подключены к рабочей области Azure Sentinel. Это подключение позволяет просматривать панели мониторинга, создать настраиваемые оповещения и улучшит исследование. Это дает больше возможностей регулировать сети вашей организации и улучшает возможности безопасности операции.  


> [!NOTE]
> Данные будут храниться в географическое расположение рабочей области, на котором выполняется Azure Sentinel.

## <a name="enable-the-connector"></a>Включение соединителя 

1. На портале Azure Sentinel выберите **соединители данных** и выберите команду **брандмауэра Windows** плитку. 
1. Выберите типы данных для потоковой передачи.
1. Нажмите кнопку **Установить**.
6. Чтобы использовать соответствующей схемы в Log Analytics для брандмауэра Windows, найдите **SecurityEvent**.

## <a name="validate-connectivity"></a>Проверка подключения

Может потребоваться до 20 минут, пока не журналов в Log Analytics. 



## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключиться к Azure Sentinel брандмауэра Windows. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).

