---
title: Поддержка диспетчера рендеринга — пакетная служба Azure
description: Использование интеграции диспетчера подготовки пакетов Azure. Узнайте о встроенной поддержке или надстройках для популярных диспетчеров рендеринга.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449680"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Использование пакетной службы Azure с диспетчерами фермы рендеринга

Если вы используете существующую локальную ферму рендеринга, скорее всего для управления ее емкостью и заданиями рендеринга настроен диспетчер рендеринга.

Azure предоставляет встроенную поддержку или поддержку через надстройки для многих популярных диспетчеров рендеринга. Это позволяет добавлять и удалять виртуальные машины Azure, включая содержащие лицензии приложений с оплатой по мере использования и (или) низкоприоритетные виртуальные машины.

Поддерживаются следующие диспетчеры рендеринга:

* [PipelineFX Qube!](https://www.pipelinefx.com/);
* [Royal Render](https://www.royalrender.de/);
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/).

## <a name="azure-render-hub"></a>Центр рендеринга Azure

Центр подготовки отчетов Azure упрощает создание и Управление фермами визуализации Azure.  В концентраторе отрисовки имеется собственная поддержка Пипелинефкс Кубе и крайнего срока 10.  Дополнительные сведения и подробные инструкции см. [в репозитории GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Использование Azure с PipelineFX Qube

Центр подготовки отчетов Azure поддерживает популярные диспетчеры рендеринга, включая крайний срок.  Инструкции по развертыванию и использованию концентратора визуализации см. [в репозитории GitHub](https://github.com/Azure/azure-render-hub).

Сценарии и инструкции по включению использования виртуальных машин пула пакетной службы Azure в качестве рабочих ролей Кубе также доступны в [репозитории GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Использование Azure с Royal Render

Для Royal Render в Azure и пакетной службе Azure реализована встроенная поддержка интеграции, что позволяет расширить ферму рендеринга за счет виртуальных машин на платформе Azure. Краткое описание этого варианта вы найдете в [файлах справки](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Пример клиента Royal Render с интеграцией в Azure можно изучить в статье [об истории клиента Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Использование Azure с Thinkbox Deadline

Центр подготовки отчетов Azure поддерживает популярные диспетчеры рендеринга, включая крайний срок.  Инструкции по развертыванию и использованию концентратора визуализации см. [в репозитории GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте настроить интеграцию пакетной службы Azure с используемым диспетчером рендеринга, применяя соответствующий подключаемый модуль и инструкции на сайте GitHub, если потребуется.
