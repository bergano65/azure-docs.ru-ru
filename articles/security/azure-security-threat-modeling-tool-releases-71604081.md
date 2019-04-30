---
title: Выпуски Microsoft Threat Modeling Tool в Azure | Документация Майкрософт
description: Документирование заметок о выпуске средства моделирования угроз
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 502c1e8a422eb9e74586e5a6820d5b12ec4ae2a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610621"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool выпуска обновлений 7.1.60408.1 - 4/9/2019 г.

Версия 7.1.60408.1 из Microsoft Threat Modeling средство (TMT) был выпущен 9 апреля 2019 г. и содержит следующие изменения:

- Новые наборы элементов для хранилища ключей Azure и диспетчер трафика Azure
- Номер версии TMT теперь отображается на начальном экране
- Обновлены ссылки для получения поддержки
- Исправления ошибок

## <a name="feature-changes"></a>Изменения функций

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Новые наборы элементов для хранилища ключей Azure и диспетчер трафика Azure

![Набор элементов хранилища ключей Azure](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Новые наборы элементов и угроз для Azure Key Vault и диспетчера трафика Azure были добавлены в набор Azure трафарета. При открытии модели на основе Azure набора элементов набора, пользователям будет предложено обновить шаблон, связанный с моделью. Обновление модели на основе набора Azure набора элементов можно также вручную инициироваться с помощью команды «Применение шаблона» в меню «Файл» и повторно примените самый последний файл Services.tb7 облака Azure.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Номер версии TMT теперь отображается на начальном экране

Клиентская версия средства моделирования угроз теперь отображается на начальном экране приложения для более удобного доступа.

![Набор элементов хранилища ключей Azure](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Обновлены ссылки для получения поддержки

Для перенаправления пользователей будут обновлены все ссылки для получения поддержки в средстве [ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com) вместо того, чтобы на форум MSDN.

## <a name="system-requirements"></a>Системные требования

- Поддерживаемые операционные системы
  - [Юбилейное обновление Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) или более поздней версии
- Требуемая версия .NET
  - [.NET 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) или более поздней версии
- Дополнительные требования
  - Для получения обновлений средства и шаблонов требуется подключение к Интернету.

## <a name="documentation-and-feedback"></a>Документация и отзывы

- Документация по Threat Modeling Tool находится на сайте [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) и содержит информацию [об использовании средства](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Дальнейшие действия

Скачайте последнюю версию [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).