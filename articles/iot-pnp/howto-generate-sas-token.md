---
title: Создание маркера безопасности для доступа к Интернету вещей Plug and Play Preview | Документация Майкрософт
description: Создайте маркер подписанного URL-доступа для использования при программном доступе к репозиторию модели предварительной версии Интернета вещей Plug and Play.
author: Philmea
ms.author: philmea
ms.date: 08/06/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f6c4f5b9784eeff9d03b6e93953674736fb78c6c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976032"
---
# <a name="generate-sas-token"></a>Создание маркера SAS

В этом пошаговом руководство показано, как программным образом создать маркер подписанного URL-адрес (SAS) для использования с API репозитория модели предварительной версии для Интернета вещей Plug and Play.

## <a name="python"></a>Python

В следующем фрагменте кода показано, как создать маркер SAS с помощью Python:

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

В следующем фрагменте кода показано, как создать маркер SAS с помощью C\#:

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Использование маркера SAS

После создания маркера SAS его можно использовать для создания HTTP-запроса POST. Пример.

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Если вы выдаете клиенту маркер SAS, он не имеет первичного ключа ресурса и не может изменить хэш, чтобы получить его. Маркер SAS позволяет контролировать доступ клиента и срок его действия. При изменении первичного ключа в политике все созданные на его основе маркеры SAS становятся недействительными.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали о создании маркеров безопасности, которые будут использоваться для доступа к Plug and Play репозиториям модели предварительной версии Интернета вещей, предлагаемый следующий шаг — дополнительные сведения в разделе [центра Интернета вещей Plug and Play предварительной версии для разработчиков моделей](concepts-developer-guide.md).
