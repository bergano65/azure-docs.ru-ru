---
title: azcopy Logout | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy Logout.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 987f5de78edc4c0fe2814259a1461e58018ec270
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879026"
---
# <a name="azcopy-logout"></a>azcopy logout

Записывает пользователя в журнал и завершает доступ к ресурсам службы хранилища Azure.

## <a name="synopsis"></a>Краткий обзор

Эта команда удалит все кэшированные данные входа для текущего пользователя.

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>Связанные концептуальные статьи

- [Начало работы с AzCopy](storage-use-azcopy-v10.md)
- [Перенос данных с помощью AzCopy и хранилища BLOB-объектов](./storage-use-azcopy-v10.md#transfer-data)
- [Перенос данных с помощью AzCopy и хранилища файлов](storage-use-azcopy-files.md)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="options"></a>Параметры

|Параметр|Описание|
|--|--|
|-h, --help|Отображение содержимого справки для команды Logout.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--Cap-Мбит/с, с плавающей запятой|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|
|--Trusted-Microsoft-суффикс строка   |Указывает дополнительные суффиксы домена, в которых могут быть отправлены Azure Active Directory токены входа.  Значение по умолчанию — "*. Core.Windows.NET;*". core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net ". Все перечисленные здесь значения добавляются к значениям по умолчанию. В целях безопасности следует размещать только Microsoft Azureные домены. Несколько записей разделяются точкой с запятой.|

## <a name="see-also"></a>См. также

- [azcopy](storage-ref-azcopy.md)
