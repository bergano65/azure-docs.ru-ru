---
title: Создание маркера безопасности для доступа к репозиторию IoT Plug и воспроизведения предварительного просмотра (ru) Документы Майкрософт
description: Создайте маркер общей подписи доступа для использования при программном доступе к репозиторию модели IoT Plug и Play Preview.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159206"
---
# <a name="generate-sas-token"></a>Создание маркера SAS

Это руководство показывает, как программно генерировать маркер общей подписи доступа (SAS) для использования с помощью APpository модулей модули IoT Plug и Play Preview.

## <a name="python"></a>Python

Следующий фрагмент показывает, как создать токен SAS с помощью Python:

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

Следующий фрагмент показывает, как создать токен SAS\#с помощью C:

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

## <a name="use-the-sas-token"></a>Используйте токен SAS

После создания маркера SAS его можно использовать для запроса HTTP POST. Пример:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Если вы даете клиенту токен SAS, клиент не имеет основного ключа ресурса и не может обратить хэш, чтобы получить его. Токен SAS дает вам контроль над тем, к чему клиент может получить доступ и как долго. При изменении основного ключа в политике любые токены SAS, созданные из него, аннулируются.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали о создании маркеров безопасности для доступа к репозиториям моделей IoT Plug и Play Preview, следующим шагом является получение дополнительной информации в [руководстве разработчика моделирования IoT Plug и Play Preview.](concepts-developer-guide.md)
