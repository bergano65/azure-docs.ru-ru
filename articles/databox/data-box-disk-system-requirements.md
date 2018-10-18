---
title: Системные требования для диска Microsoft Azure Data Box | Документация Майкрософт
description: Дополнительные сведения о требованиях к программному обеспечению и сети для диска Microsoft Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/06/2018
ms.author: alkohli
ms.openlocfilehash: d10ca5d704892ae0a1494d729b46abf0fc06aa64
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092258"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Системные требования для диска Azure Data Box (предварительная версия)

В этой статье описываются важные требования к системе и рекомендации для решения на основе диска Microsoft Azure Data Box и клиентов, подключающихся к нему. Прежде чем развертывать диск Data Box, внимательно ознакомьтесь с приведенной ниже информацией и по мере необходимости возвращайтесь к ней во время развертывания и последующих действий.

> [!IMPORTANT]
> Диск Data Box предоставляется в режиме предварительной версии. Изучите [условия использования для предварительной версии](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), прежде чем развертывать это решение. 

В системных требованиях перечислены поддерживаемые платформы для клиентов, подключающихся к дискам, поддерживаемые типы учетных записей хранения и хранилищ.


## <a name="supported-operating-systems-for-clients"></a>Поддерживаемые версии операционных систем для клиентов

Ниже приведен список поддерживаемых операционных систем для операций разблокировки диска и копирования данных при помощи клиентов, подключенных к диску Data Box.

| **Операционная система** | **Проверенные версии** |
| --- | --- |
| Windows Server |2008 R2 с пакетом обновления 1 (SP1) <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Другое необходимое программное обеспечение для клиентов Windows

Для клиента Windows нужно также установить следующие компоненты.

| **Программное обеспечение**| **Версия** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Другое необходимое программное обеспечение для клиентов Linux

Набор инструментов для диска Data Box позволяет установить следующее необходимое программное обеспечение для клиента Linux:

- DisLocker;
- OpenSSL.

## <a name="supported-storage-accounts"></a>Учетные записи хранилища BLOB-объектов

Ниже приведен список поддерживаемых типов хранилищ для диска Data Box.

| **Учетная запись хранения** | **Примечания** |
| --- | --- |
| Классический | Стандартная |
| Общего назначения  |"Стандартный" (поддерживаются версии V1 и V2). Поддерживаются "горячий" и "холодный" уровни. |


## <a name="supported-storage-types"></a>Поддерживаемые типы хранилищ

Ниже приведен список поддерживаемых типов хранилищ для диска Data Box.

| **Формат файлов** | **Примечания** |
| --- | --- |
| Блочный BLOB-объект Azure | |
| Страничный BLOB-объект Azure  | |


## <a name="next-step"></a>Дальнейшие действия

* [Deploy your Azure Data Box Disk](data-box-disk-deploy-ordered.md) (Развертывание диска Azure Data Box)

