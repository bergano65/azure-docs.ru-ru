---
title: Microsoft Threat Modeling Tool выпуск 07/29/2020 — Azure
description: Документирование заметок о выпуске для 7.3.00729.1 выпуска средства моделирования угроз.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: fc343f292fa32ed2db67dd74aba7a66dbc00d6ab
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317843"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool обновления выпуска 7.3.00729.1 — 07/29/2020

Версия 7.3.00729.1 Microsoft Threat Modeling Tool (ТМТ) была выпущена 29 2020 июля и содержит следующие изменения:

- Исправления ошибок
 
## <a name="known-issues"></a>Известные проблемы

### <a name="errors-related-to-tmt7application-file-deserialization"></a>Ошибки, связанные с десериализациюм файла TMT7. Application

#### <a name="issue"></a>Проблема

Некоторые клиенты сообщили о получении следующего сообщения об ошибке при скачивании Threat Modeling Tool:

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

Эта ошибка возникает из-за того, что некоторые браузеры изначально не поддерживают установку ClickOnce. В таких случаях файл приложения ClickOnce загружается на жесткий диск пользователя.

#### <a name="workaround"></a>Обходной путь

Эта ошибка будет отображаться, если Threat Modeling Tool запускается двойным щелчком по файлу TMT7. Application. Однако после обойти ошибку средство будет работать нормально. Вместо того чтобы запускать Threat Modeling Tool, дважды щелкнув файл TMT7. Application, пользователи должны использовать ярлыки, созданные в меню Windows во время установки, чтобы запустить Threat Modeling Tool.

## <a name="system-requirements"></a>Требования к системе

- Поддерживаемые операционные системы
  - [Юбилейное обновление Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) или более поздняя версия
- Требуемая версия .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) или более поздней версии
- Дополнительные требования
  - Для получения обновлений средства и шаблонов требуется подключение к Интернету.

## <a name="documentation-and-feedback"></a>Документация и отзывы

- Документация по Threat Modeling Tool находится на сайте [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) и содержит информацию [об использовании средства](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Дальнейшие действия

Скачайте последнюю версию [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
