---
title: Инструменты для работы со службой хранилища Azure | Документация Майкрософт
description: Список инструментов, позволяющих просматривать данные в службе хранилища Azure и взаимодействовать с ними.
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.subservice: common
ms.openlocfilehash: ae6ceee33aa6d96c360782c33232e0c7561cf209
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483117"
---
# <a name="azure-storage-client-tools"></a>Клиентские инструменты службы хранилища Azure
Пользователям службы хранилища Azure часто требуется просматривать свои данные или взаимодействовать с ними с помощью клиентского инструмента службы хранилища Azure. В приведенных ниже таблицах перечислен ряд инструментов, которые позволяют это сделать. Соответствующее поле содержит "X", если инструмент позволяет перечислить абстракцию данных и (или) предоставить к ней доступ. В таблице также показано, являются ли инструменты бесплатными. "Пробная версия" означает, что доступна бесплатная пробная версия, но полная версия продукта платная. "Да/нет" указывает, что есть как платная, так и бесплатная версии.

Мы предоставляем только набор клиентских инструментов службы хранилища Azure, доступных на данный момент. Развитие этих инструментов и расширение их функций может продолжаться. Если необходимо внести какие-либо исправления или были выпущены обновления, оставьте комментарий для нас. Кроме того, если вы знаете об инструментах, которые должны быть в этой статье, мы с радостью добавим их.

**Клиентские инструменты службы хранилища Microsoft Azure**

<table>
  <tr>
    <th rowspan="2">Клиентский инструмент службы хранилища Azure</th>
    <th rowspan="2">Блочный BLOB-объект</th>
    <th rowspan="2">Страничный BLOB-объект</th>
    <th rowspan="2">Добавить BLOB-объект</th>
    <th rowspan="2">Таблицы</th>
    <th rowspan="2">Очереди</th>
    <th rowspan="2">Файлы</th>
    <th rowspan="2">Бесплатно</th>
    <th colspan="4">платформа</th>
  </tr>
  <tr>
    <td>Интернет</td>
    <td> Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Портал Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Да</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://storageexplorer.com/">Обозреватель службы хранилища Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Да</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Обозреватель сервера Microsoft Visual Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Да</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Сторонние клиентские инструменты службы хранилища Azure**

Мы не проверяли функциональные возможности или качество, заявленные для следующих инструментов сторонних производителей, и их наличие в списке не означает поддержку корпорацией Майкрософт.

<table>
  <tr>
    <th rowspan="2">Клиентский инструмент службы хранилища Azure</th>
    <th rowspan="2">Блочный BLOB-объект</th>
    <th rowspan="2">Страничный BLOB-объект</th>
    <th rowspan="2">Добавить BLOB-объект</th>
    <th rowspan="2">Таблицы</th>
    <th rowspan="2">Очереди</th>
    <th rowspan="2">Файлы</th>
    <th rowspan="2">Бесплатно</th>
    <th colspan="4">платформа</th>
  </tr>
  <tr>
    <td>Интернет</td>
    <td> Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Пробная версия</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate: Azure Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>Да</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Да</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>Да/нет</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gapotchenko.com/cloudcombine">Cloud Combine</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Пробная версия</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer и TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Да</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Пробная версия</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
