---
title: Microsoft Угроза Моделирование Релиз 4/9/2019
titleSuffix: Azure
description: Документирование заметок о выпуске средства моделирования угроз
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269730"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Угроза Моделирование Инструмент обновления релиз 7.1.60408.1 - 4/9/2019

Версия 7.1.60408.1 инструмента моделирования угроз Microsoft (TMT) была выпущена 9 апреля 2019 года и содержит следующие изменения:

- Новые трафареты для Azure Key Vault и менеджера трафика Azure
- Номер версии TMT теперь отображается на домашнем экране
- Обновлены ссылки на поддержку
- Исправления ошибок

## <a name="feature-changes"></a>Изменения функций

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Новые трафареты для Azure Key Vault и менеджера трафика Azure

![Лазурный ключ Хранилище Stencil](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Новые трафареты и угрозы для Azure Key Vault и Azure Traffic Manager были добавлены в набор трафаретов Azure. При открытии моделей на основе набора трафаретов Azure пользователям будет предложено обновить шаблон, связанный с моделью. Обновление модели на основе набора трафаретов Azure также может быть инициировано вручную с помощью команды "Apply Template" в меню "File" и повторного применения последнего файла Azure Cloud Services.tb7.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Номер версии TMT теперь отображается на домашнем экране

Клиентская версия инструмента моделирования угроз теперь отображается на домашнем экране приложения для облегчения доступа.

![Лазурный ключ Хранилище Stencil](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Обновлены ссылки на поддержку

Все ссылки поддержки в инструменте [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) были обновлены, чтобы направлять пользователей на форум MSDN.

## <a name="system-requirements"></a>Требования к системе

- Поддерживаемые операционные системы
  - [Microsoft Windows 10 Годовщина Обновление](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) или позже
- Требуемая версия .NET
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) или позже
- Дополнительные требования
  - Для получения обновлений средства и шаблонов требуется подключение к Интернету.

## <a name="documentation-and-feedback"></a>Документация и отзывы

- Документация по Threat Modeling Tool находится на сайте [docs.microsoft.com](threat-modeling-tool.md) и содержит информацию [об использовании средства](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Дальнейшие действия

Скачайте последнюю версию [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
