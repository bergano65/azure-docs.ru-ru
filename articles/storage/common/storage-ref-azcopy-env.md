---
title: azcopy env | Документация Майкрософт
description: В этой статье содержатся справочные сведения о команде azcopy env.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 197d8ee1ea1d389a26548fa65adee83c6a92cf96
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87275978"
---
# <a name="azcopy-env"></a>azcopy env

Показывает переменные среды, которые могут настраивать поведение AzCopy.

## <a name="synopsis"></a>Краткий обзор

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Если задать переменную среды с помощью командной строки, эта переменная будет доступна для чтения в журнале командной строки. Рассмотрите возможность очистки переменных, содержащих учетные данные из журнала командной строки. Чтобы переменные отображались в журнале, можно использовать сценарий, чтобы запросить у пользователя учетные данные и задать переменную среды.

## <a name="related-conceptual-articles"></a>Связанные концептуальные статьи

- [Начало работы с AzCopy](storage-use-azcopy-v10.md)
- [Перенос данных с помощью AzCopy и хранилища BLOB-объектов](storage-use-azcopy-blobs.md)
- [Перенос данных с помощью AzCopy и хранилища файлов](storage-use-azcopy-files.md)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md) (Настройка, оптимизация и устранение неполадок с AzCopy)

## <a name="options"></a>Параметры

|Параметр|Описание|
|--|--|
|-h, --help|Отображает содержимое справки для команды env. |
|--показывать с учетом|Отображает конфиденциальные и секретные переменные среды.|

## <a name="options-inherited-from-parent-commands"></a>Параметры, унаследованные от родительских команд

|Параметр|Описание|
|---|---|
|--Cap-Мбит/с, с плавающей запятой|Скорость передачи с прописными буквами в мегабит в секунду. Посекундная пропускная способность может немного отличаться от ограничения. Если этот параметр имеет значение 0 или пропущен, пропускная способность не ограничена.|
|--строка выходного типа|Формат вывода команды. Среди вариантов: Text, JSON. Значение по умолчанию — "Text".|
|--Trusted-Microsoft-суффикс строка  | Указывает дополнительные суффиксы домена, в которых могут быть отправлены Azure Active Directory токены входа.  Значение по умолчанию — "*. Core.Windows.NET;*". core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net ". Все перечисленные здесь значения добавляются к значениям по умолчанию. В целях безопасности следует размещать только Microsoft Azureные домены. Несколько записей разделяются точкой с запятой.|

## <a name="see-also"></a>См. также раздел

- [azcopy](storage-ref-azcopy.md)
