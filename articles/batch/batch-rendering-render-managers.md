---
title: Поддержка диспетчера рендеринга — пакетная служба Azure
description: Использование интеграции диспетчеров диспетчеров Azure Batch. Узнайте о встроенной поддержке или дополнениях для популярных менеджеров визуализации.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449680"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Использование пакетной службы Azure с диспетчерами фермы рендеринга

Если вы используете существующую локальную ферму рендеринга, скорее всего для управления ее емкостью и заданиями рендеринга настроен диспетчер рендеринга.

Azure предоставляет встроенную поддержку или поддержку через надстройки для многих популярных диспетчеров рендеринга. Это позволяет добавлять и удалять виртуальные машины Azure, включая содержащие лицензии приложений с оплатой по мере использования и (или) низкоприоритетные виртуальные машины.

Поддерживаются следующие диспетчеры рендеринга:

* [PipelineFX Qube!](https://www.pipelinefx.com/);
* [Royal Render](https://www.royalrender.de/);
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/).

## <a name="azure-render-hub"></a>Концентратор рендера Azure

Концентратор Azure Render упрощает создание и управление фермами визуализации Azure.  Render Hub имеет родную поддержку для PipelineFx Зубе и Deadline 10.  Для получения дополнительной информации и подробных инструкций смотрите [репозиторий GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Использование Azure с PipelineFX Qube

Концентратор Отобрагии Отлик Azure поддерживает популярных менеджеров визуализации, включая Deadline.  Для инструкций по развертыванию [the GitHub repository](https://github.com/Azure/azure-render-hub)и использованию render Hub см.

Скрипты и инструкции для включения в пул m Azure Batch для использования в качестве работников Зубе также доступны в [репозитории GitHub.](https://github.com/Azure/azure-qube)

## <a name="using-azure-with-royal-render"></a>Использование Azure с Royal Render

Для Royal Render в Azure и пакетной службе Azure реализована встроенная поддержка интеграции, что позволяет расширить ферму рендеринга за счет виртуальных машин на платформе Azure. Краткое описание этого варианта вы найдете в [файлах справки](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Пример клиента Royal Render с интеграцией в Azure можно изучить в статье [об истории клиента Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Использование Azure с Thinkbox Deadline

Концентратор Отобрагии Отлик Azure поддерживает популярных менеджеров визуализации, включая Deadline.  Для инструкций по развертыванию [the GitHub repository](https://github.com/Azure/azure-render-hub)и использованию render Hub см.

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте настроить интеграцию пакетной службы Azure с используемым диспетчером рендеринга, применяя соответствующий подключаемый модуль и инструкции на сайте GitHub, если потребуется.
