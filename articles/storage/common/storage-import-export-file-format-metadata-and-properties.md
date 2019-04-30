---
title: Формат файла свойств и файла метаданных импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как указать метаданные и свойства для одного или нескольких больших двоичных объектов, входящих в задание импорта или экспорта.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 2066d4a2ed6db97285d92d15e14dbd21629dbdfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478561"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Формат файла свойств и файла метаданных службы импорта и экспорта Azure
В рамках задания импорта или экспорта можно указать метаданные и свойства для одного или нескольких больших двоичных объектов. Чтобы задать метаданные и свойства для больших двоичных объектов, созданных в рамках задания импорта, на жестком диске необходимо создать файл метаданных или файл свойств, содержащий импортируемые данные. Для задания экспорта метаданные и свойства записываются в файл метаданных или файл свойств, содержащийся на жестком диске, возвращаемом пользователю.  
  
## <a name="metadata-file-format"></a>Формат файла метаданных  
Файл метаданных имеет следующий формат:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML-элемент|type|Описание|  
|-----------------|----------|-----------------|  
|`Metadata`|Корневой элемент|Корневой элемент файла метаданных.|  
|`metadata-name`|String|Необязательный элемент. XML-элемент указывает имя метаданных большого двоичного объекта, а его значение определяет значение параметра метаданных.|  
  
## <a name="properties-file-format"></a>Формат файла свойств  
Файл свойств имеет следующий формат:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML-элемент|type|Описание|  
|-----------------|----------|-----------------|  
|`Properties`|Корневой элемент|Корневой элемент файла свойств.|  
|`Last-Modified`|String|Необязательный элемент. Время последнего изменения большого двоичного объекта. Только для заданий экспорта.|  
|`Etag`|String|Необязательный элемент. Значение ETag большого двоичного объекта. Только для заданий экспорта.|  
|`Content-Length`|String|Необязательный элемент. Размер большого двоичного объекта в байтах. Только для заданий экспорта.|  
|`Content-Type`|String|Необязательный элемент. Тип содержимого большого двоичного объекта|  
|`Content-MD5`|String|Необязательный элемент. Хэш MD5 большого двоичного объекта.|  
|`Content-Encoding`|String|Необязательный элемент. Кодирование содержимого большого двоичного объекта.|  
|`Content-Language`|String|Необязательный элемент. Язык содержимого большого двоичного объекта.|  
|`Cache-Control`|String|Необязательный элемент. Строка управления кэшем для большого двоичного объекта.|  

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные правила настройки свойств и метаданных больших двоичных объектов см. в статьях [Set Blob Properties](/rest/api/storageservices/set-blob-properties) (Задание свойств большого двоичного объекта), [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) (Задание метаданных большого двоичного объекта) и [Setting and Retrieving Properties and Metadata for Blob Resources](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) (Задание и получение свойств и метаданных для ресурсов больших двоичных объектов).
