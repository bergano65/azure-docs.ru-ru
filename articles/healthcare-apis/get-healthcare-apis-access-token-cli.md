---
title: Получение маркера доступа с помощью Azure CLI Azure API для FHIR
description: В этой статье объясняется, как получить маркер доступа для Azure API для FHIR с помощью Azure CLI.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: 4d1c4cfcb15d97a2c54a04344f0bd098f65c1392
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660378"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Получение маркера доступа для API Azure для FHIR с помощью Azure CLI

Из этой статьи вы узнаете, как получить маркер доступа для API Azure для FHIR с помощью Azure CLI. При [подготовке API Azure для FHIR](fhir-paas-portal-quickstart.md)настраивается набор пользователей или субъектов-служб, имеющих доступ к службе. Если идентификатор объекта пользователя находится в списке разрешенных идентификаторов объектов, доступ к службе можно получить с помощью маркера, полученного с помощью Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>Получение токена

API Azure для FHIR использует `resource`  или `Audience` с URI, равным универсальному коду ресурса (URI) сервера FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . Вы можете получить маркер и сохранить его в переменной (с именем `$token` ) с помощью следующей команды:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Использование с API Azure для FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как получить маркер доступа для API Azure для FHIR с помощью Azure CLI. Чтобы узнать, как получить доступ к API FHIR с помощью Postman, перейдите к руководству по Postman.

>[!div class="nextstepaction"]
>[Получение доступа к API FHIR с помощью Postman](access-fhir-postman-tutorial.md)
