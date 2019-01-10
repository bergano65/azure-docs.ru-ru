---
title: Эталонные архитектуры рендеринга в Azure — пакетная служба Azure
description: Архитектуры для применения пакетной службы Azure и других служб Azure для расширения локальных ферм рендеринга в облако
services: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 08/13/2018
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: d5102ba94e2b7808a457df00a87b35ef7022c454
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543501"
---
# <a name="reference-architectures-for-azure-rendering"></a>Эталонные архитектуры для рендеринга в Azure

В этой статье представлены обобщенные схемы нескольких архитектур, позволяющих расширить локальную ферму рендеринга в Azure. В этих примерах описаны несколько комбинаций служб Azure для вычислений, сетевого взаимодействия и хранения.

## <a name="hybrid-with-nfs-or-cfs"></a>Гибридное развертывание с NFS или CFS

На следующей схеме показан гибридный сценария, который включает в себя следующие службы Azure:

* **Служба вычислений** — пул пакетной службы Azure или масштабируемый набор виртуальных машин.

* **Сеть** — локальная сеть: Azure ExpressRoute или VPN. Azure Виртуальная сеть Azure.

* **Хранилище** — входные и выходные файлы NFS или CFS на базе виртуальных машин Azure, синхронизируемые с локальным хранилищем через службу синхронизации файлов Azure или RSync.

  ![Выход в облако — гибридное развертывание с NFS или CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Гибридное развертывание с Blobfuse

На следующей схеме показан гибридный сценария, который включает в себя следующие службы Azure:

* **Служба вычислений** — пул пакетной службы Azure или масштабируемый набор виртуальных машин.

* **Сеть** — локальная сеть: Azure ExpressRoute или VPN. Azure Виртуальная сеть Azure.

* **Хранилище** — входные и выходные файлы в хранилище BLOB-объектов, подключенном к вычислительным ресурсам через Azure Blobfuse.

  ![Выход в облако — гибридное развертывание с Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Гибридное развертывание служб вычислений и хранилища

На следующей схеме представлен полностью подключенный гибридный сценарий для служб вычислений и хранилища, в который входят следующие службы Azure:

* **Служба вычислений** — пул пакетной службы Azure или масштабируемый набор виртуальных машин.

* **Сеть** — локальная сеть: Azure ExpressRoute или VPN. Azure Виртуальная сеть Azure.

* **Хранилище** — между локальными средами: Avere vFXT. Опционально можно архивировать локальные файлы через Azure Data Box в хранилище BLOB-объектов.

  ![Выход в облако — гибридное развертывание служб вычислений и хранения](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>Дополнительная информация

* Изучите сведения об использовании [диспетчеров рендеринга](batch-rendering-render-managers.md) с пакетной службой Azure.

* Изучите сведения о возможностях для [рендеринга в Azure](batch-rendering-service.md).