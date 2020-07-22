---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/05/2020
ms.author: alkohli
ms.openlocfilehash: 7b8ebd085edfdb130f44c477d7697807d349e4e5
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86208902"
---
В зависимости от выбранной учетной записи хранения Data Box создает указанные ниже ресурсы.

* До трех общих папок для каждой связанной учетной записи хранения (GPv1 и GPv2).
* Одна общая папка для хранилища класса Premium.
* Одна общая папка для учетной записи хранения BLOB-объектов.

В общих папках для блочных и страничных BLOB-объектов объектами первого уровня являются контейнеры, а второго — большие двоичные объекты. В общих папках для файлов Azure объекты первого уровня — общие папки, а объекты второго уровня — файлы.

В следующей таблице приведен UNC-путь к общим папкам в Data Box и URL-адрес службы хранилища Azure, куда отправляются данные. Конечный URL-адрес службы хранилища Azure может быть производным от UNC-пути к общей папке.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Блочные BLOB-объекты Azure | <li>UNC-путь к общим папкам: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL-адрес службы хранилища Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Страничные BLOB-объекты Azure  | <li>UNC-путь к общим папкам: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL-адрес службы хранилища Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Файлы Azure       |<li>UNC-путь к общим папкам: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL-адрес службы хранилища Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

