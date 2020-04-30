---
title: Предоставление автономного шлюза в службе управления API Azure | Документация Майкрософт
description: Узнайте, как подготавливать самостоятельный шлюз в службе управления API Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: 2870a654faad4e760a9d022488cb2c4c406cbeab
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203137"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Предоставление автономного шлюза в службе управления API Azure

Подготовка ресурса шлюза в экземпляре службы управления API Azure является необходимым условием для развертывания самостоятельно размещенного шлюза. В этой статье рассматриваются действия по подготовке ресурса шлюза в оснастке управления API.

## <a name="prerequisites"></a>Предварительные условия

Выполните следующие действия в кратком руководстве по [созданию экземпляра службы управления API Azure](get-started-create-service-instance.md) .

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Предоставление независимого шлюза

1. Выберите **шлюзы** в разделе **Параметры**.
2. Щелкните **+ Добавить**.
3. Введите **имя** и **регион** шлюза.
> [!TIP]
> **Регион** указывает предполагаемое расположение узлов шлюза, которые будут связаны с этим ресурсом шлюза. Он семантически эквивалентен похожему свойству, связанному с любым ресурсом Azure, но ему может быть присвоено произвольное строковое значение.

4. При необходимости введите **Описание** ресурса шлюза.
5. При необходимости выберите **+** в разделе **интерфейсы API** , чтобы связать один или несколько API с этим ресурсом шлюза.
> [!IMPORTANT]
> По умолчанию ни один из существующих API не будет связан с новым ресурсом шлюза. Поэтому попытки вызвать их через новый шлюз приведут к `404 Resource Not Found` ответу.

6. Нажмите кнопку **Добавить**.

Теперь ресурс шлюза был подготовлен в экземпляре управления API. Вы можете перейти к развертыванию шлюза.

## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения о самостоятельно размещенном шлюзе см. в статье [Обзор самостоятельного размещения шлюза в службе управления API Azure](self-hosted-gateway-overview.md) .
* Дополнительные сведения о [развертывании автономного шлюза в Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
* Дополнительные сведения о [развертывании автономного шлюза в DOCKER](how-to-deploy-self-hosted-gateway-docker.md)
