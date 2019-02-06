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
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: 55c781d0c6284de1382c9d3e614621c40963194b
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302959"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, выпуск обновления 7.1.60126.1 от 29.01.2019

29 января 2019 г. была выпущена версия 7.1.60126.1 средства Microsoft Threat Modeling Tool со следующими изменениями.

- Минимальная требуемая версия .NET повышена до [.Net 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262).
- Минимальная требуемая версия Windows повышена до [юбилейного обновления Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) из-за зависимости от .NET.
- В меню параметров средства добавлена функция переключения для проверки моделей.
- Обновлено несколько ссылок в свойствах угроз.
- В интерфейс начального экрана внесено несколько незначительных изменений.
- Threat Modeling Tool теперь наследует параметры TLS операционной системы сервера виртуальных машин и поддерживается в средах, требующих использования TLS 1.2 или более поздней версии.

## <a name="feature-changes"></a>Изменения функций

### <a name="model-validation-option"></a>Параметр проверки модели

На основе отзывов клиентов в средство был добавлен параметр для включения или отключения проверки модели. Ранее, если ваш шаблон использовал один однонаправленный поток данных между двумя объектами, возможно, вы могли получать следующее сообщение об ошибке в кадре сообщений. ObjectsName требует по крайней мере одного any. Отключение проверки модели предотвратит отображение этих предупреждений в представлении.

Параметр включения и выключения проверки модели можно найти в меню "Файл->Настройки->Параметры". Значением по умолчанию для этого параметра является "Отключено".

![Параметр проверки модели](./media/azure-security-threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Требования к системе

- Поддерживаемые операционные системы
  - [Юбилейное обновление Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) или более поздней версии
- Требуемая версия .NET
  - [.NET 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) или более поздней версии
- Дополнительные требования
  - Для получения обновлений средства и шаблонов требуется подключение к Интернету.

## <a name="known-issues"></a>Известные проблемы

### <a name="unsupported-systems"></a>Неподдерживаемые системы

#### <a name="issue"></a>Проблема

Пользователи систем Windows 10, которые не могут установить .NET 4.7.1 или более поздней версии, например Windows 10 Enterprise LTSB (версии 1507), не смогут открыть средство после обновления. Эти старые версии Windows больше не поддерживаются платформами для Threat Modeling Tool и не должны устанавливать последнее обновление.

#### <a name="workaround"></a>Возможное решение

Пользователи Windows 10 Enterprise LTSB (версии 1507), которые установили последнее обновление, могут вернуться к предыдущей версии Threat Modeling Tool через диалоговое окно удаления в "Приложениях и возможностях".

## <a name="documentation-and-feedback"></a>Документация и отзывы

- Документация по Threat Modeling Tool находится на сайте [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) и содержит информацию [об использовании средства](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Дополнительная информация

Скачайте последнюю версию [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).