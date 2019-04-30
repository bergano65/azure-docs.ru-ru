---
title: Подключение к Azure Sentinel Preview данных центра безопасности Azure | Документация Майкрософт
description: Узнайте, как подключиться к Azure Sentinel данных центра безопасности Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: bd6fe027da8c16157c7fce6c348e49c0a6b73ea3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596617"
---
# <a name="connect-data-from-azure-security-center"></a>Подключайте данные из центра безопасности Azure

> [!IMPORTANT]
> Сейчас Azure Sentinel предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Azure Sentinel позволяет подключать оповещения из [центра безопасности](../security-center/security-center-intro.md) и передает их в Azure Sentinel. 

## <a name="prerequisites"></a>Технические условия

- Если вы хотите экспортировать оповещения из центра безопасности Azure, необходимо быть участником в подписке, журналы которых можно потоковую передачу.

- Необходимо иметь [стандартного центра безопасности Azure уровня](../security-center/security-center-pricing.md) на подписку. В противном случае [обновить подписку до standard](https://azure.microsoft.com/pricing/details/security-center/).

- Необходимо войти в учетную запись пользователя с глобального администратора или администратора разрешения безопасности для каждой подписки, которую вы хотите подключиться.


## <a name="connect-to-azure-security-center"></a>Подключение к центру безопасности Azure

1. В Azure Sentinel, выберите **соединители данных** и нажмите кнопку **центра безопасности** плитку.
1. В правой части щелкните **Connect** рядом с каждой подпиской, оповещения которого вы хотите выполнять потоковую передачу в Azure Sentinel. Убедитесь в том, что обновления каждой подписки до стандартного центра безопасности Azure уровня на поток оповещения для Azure Sentinel.

3. Щелкните **Подключить**.

4. Чтобы использовать соответствующей схемы в Log Analytics для оповещений центра безопасности Azure, поиск **SecurityEvent**.

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как соединиться с центра безопасности Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальные угрозы](quickstart-get-visibility.md).
- Начало работы [обнаружение угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
