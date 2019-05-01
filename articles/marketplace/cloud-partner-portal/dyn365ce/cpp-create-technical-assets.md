---
title: Создание Dynamics 365 для взаимодействия с клиентами технических средств | Azure Marketplace
description: Создание технических ресурсов для предложения приложения Dynamics 365 для взаимодействия с клиентами.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/29/2018
ms.author: pabutler
ms.openlocfilehash: eff175264677d6b8ffb885229b5e68b306424335
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943110"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Создание технических ресурсов для предложения приложения Azure

Обычно вы разрабатываете решения с помощью [пакета SDK для приложений Dynamics 365 для взаимодействия с клиентами](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk).  Решения могут принимать различные формы, как описано в [моделях программирования приложений Dynamics 365 для взаимодействия с клиентами ](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models).  Выберите форму, которая наилучшим образом соответствует требованиям вашего решения.  При разработке решения необходимо учитывать ряд проблем, таких как выбор расширяемости, компоненты решения и совместимость версий.  Дополнительную информацию см. в статье [Введение в решения](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions).

Большинство решений Dynamics 365, опубликованных в AppSource, представляют собой управляемые приложения, которые распространяются в виде файлов пакетов.


## <a name="creating-and-storing-the-package"></a>Создание и хранение пакета

Параллельная документация по созданию предложений Dynamics 365 для взаимодействия с клиентами находится в разделе [Публикация приложения в AppSource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource).  Cnfysckfd GjksoerНиже содержатся подробные сведения о создании файла пакета решения и его отправки в службу хранилища Azure.

- [Шаг 4. Создание пакета AppSource для вашего приложения](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource). На этом шаге вы узнаете, как создать ZIP-файл, который представляет приложение и содержит: папку с ресурсами решения, настаиваемый код DLL, файл сведений о типе MIME, значок пакета AppSource, файл условий лицензии (HTML) и содержимое файла (XML).
- [Шаг 5. Хранение пакета AppSource в службе хранилища Azure и создание URL-адреса с помощью ключа SAS](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage). На этом шаге объясняется, как сохранить файл пакета AppSource в учетную запись хранения больших двоичных объектов Microsoft Azure, а также как использовать ключ SAS для совместного использования файла пакета. Файл пакета будет извлечен из расположения службы хранилища Azure для сертификации и будет использоваться в пробных версиях и публикациях AppSource.


## <a name="next-steps"></a>Дальнейшие действия

Если вы еще не создали предложение для Dynamics 365 for Customer Engagement, изучите [эту статью](./cpp-create-offer.md).  См. [здесь](./cpp-publish-offer.md), чтобы опубликовать предложение.
