---
title: Просмотр состояния задания инструмента импорта и экспорта Azure версии 1 | Документация Майкрософт
description: Узнайте, как использовать файлы журнала, созданные при запуске задания импорта или экспорта, для просмотра состояния задания импорта и экспорта.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306e3ccf19ba8db2de01e4b20a52707215a4a040
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470371"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Просмотр состояния задания импорта и экспорта Azure с помощью файлов журнала копирования
При обработке дисков, связанных с заданием импорта или экспорта, служба импорта и экспорта Microsoft Azure записывает файлы журнала копирования в учетную запись хранения, в которую импортируются большие двоичные объекты или из которой они экспортируются. Файл журнала содержит подробные сведения о состоянии каждого импортируемого или экспортируемого файла. При запросе состояния завершенного задания возвращается URL-адрес для каждого файла журнала копирования. Дополнительные сведения см. [здесь](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>Примеры URL-адресов

Ниже приведены примеры URL-адресов файлов журнала копирования для задания импорта с двумя дисками.  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Дополнительные сведения о формате журналов копирования и полный список кодов состояний см. в статье [Формат файла журнала службы импорта и экспорта Azure](../storage-import-export-file-format-log.md).  
  
## <a name="next-steps"></a>Дополнительная информация
 
 * [Настройка средства импорта и экспорта Azure](storage-import-export-tool-setup-v1.md)   
 * [Подготовка жестких дисков для задания импорта](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Подготовка задания импорта](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Подготовка задания экспорта](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Устранение неполадок со средством импорта и экспорта Azure](storage-import-export-tool-troubleshooting-v1.md)
