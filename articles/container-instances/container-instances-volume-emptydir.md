---
title: Гора пустойDir объем контейнерной группы
description: Узнайте, как подключить том emptyDir для обмена данными между контейнерами в группе контейнеров службы "Экземпляры контейнеров Azure"
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117737"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Подключение тома emptyDir в службе "Экземпляры контейнеров Azure"

Узнайте, как подключить том *emptyDir* для обмена данными между контейнерами в группе контейнеров службы "Экземпляры контейнеров Azure". Используйте *объемы emptyDir* в качестве эфемерных кэшов для контейнерных рабочих нагрузок.

> [!NOTE]
> Сейчас подключение тома *emptyDir* поддерживается только для контейнеров Linux. В то время как мы работаем над тем, чтобы привести все функции в контейнеры Windows, вы можете найти текущие различия платформы в [обзоре.](container-instances-overview.md#linux-and-windows-containers)

## <a name="emptydir-volume"></a>Том emptyDir

Том *emptyDir* предоставляет доступный для записи каталог каждому контейнеру в группе контейнеров. Контейнеры в группе могут считывать и записывать одни и те же файлы в томе. Также их можно подключать с использованием одинаковых или разных путей в каждом контейнере.

Некоторые примеры использования тома *emptyDir*:

* область временных файлов;
* контрольные точки во время длительных задач;
* хранение данных, полученных сопроводительным контейнером и обслуживаемых контейнером приложения.

В случае сбоев контейнеров данные в томе *emptyDir* сохраняются. Но при перезапуске контейнеров сохранность данных в томе *emptyDir* не гарантируется. Если вы останавливаете группу контейнеров, объем *emptyDir* не сохраняется.

Максимальный размер объема Linux *emptyDir* составляет 50 ГБ.

## <a name="mount-an-emptydir-volume"></a>Подключение тома emptyDir

Чтобы установить объем emptyDir в экземпляре контейнера, можно развернуть с помощью [шаблона менеджера ресурсов Azure,](/azure/templates/microsoft.containerinstance/containergroups) [файла YAML](container-instances-reference-yaml.md)или других программных методов для развертывания группы контейнеров.

Во-первых, `volumes` заселите массив `properties` в разделе группы контейнеров файла. Затем для каждого контейнера в группе контейнеров, в которой нужно подключить том *emptyDir*, заполните массив `volumeMounts` в разделе `properties` определения контейнера.

Например, при помощи следующего шаблона Resource Manager создается группа из двух контейнеров, в каждом из которых подключается том *emptyDir*:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Чтобы увидеть примеры развертывания контейнерных групп, [см. Развертывание многоконтейнерной группы с помощью шаблона «Менеджер ресурсов»](container-instances-multi-container-group.md) и [развертывание группы с несколькими контейнерами с помощью файла YAML.](container-instances-multi-container-yaml.md)

## <a name="next-steps"></a>Дальнейшие действия

Сведения о подключении других типов томов в службе "Экземпляры контейнеров Azure" см. в следующих статьях:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Подключение файлового ресурса Azure с помощью службы "Экземпляры контейнеров Azure")
* [Подключение тома gitRepo в службе "Экземпляры контейнеров Azure"](container-instances-volume-gitrepo.md)
* [Подключение тома secret в службе "Экземпляры контейнеров Azure"](container-instances-volume-secret.md)