---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070543"
---
Азурите поддерживает одну фиксированную учетную запись и известный ключ проверки подлинности для аутентификации с общим ключом. Эта учетная запись и ключ являются единственными учетными данными общего ключа, разрешенными для использования с Азурите. К ним относятся:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Ключ проверки подлинности, поддерживаемый Азурите, предназначен только для тестирования функциональности кода проверки подлинности клиента. Его использование не гарантирует защиту. Нельзя использовать рабочую учетную запись хранения и ключ с Азурите. Не следует использовать учетную запись для разработки с рабочими данными.
> 
> Азурите поддерживает только подключение по протоколу HTTP. Тем не менее для доступа к ресурсам в рабочей учетной записи хранения Azure рекомендуется использовать протокол HTTPS.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Подключение к учетной записи эмулятора с помощью ярлыка
Самый простой способ подключиться к Азурите из приложения — настроить строку подключения в файле конфигурации приложения, который ссылается на ярлык `UseDevelopmentStorage=true` . Ниже приведен пример строки подключения для Азурите в файле *app.config* . 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Подключение к учетной записи эмулятора с помощью известного имени и ключа учетной записи
Для создания строки подключения, которая содержит ссылку на имя учетной записи и ключ эмулятора, в строке подключения следует указать конечные точки для каждой службы, которую нужно использовать из эмулятора. Это необходимо, чтобы строка подключения содержала ссылки на конечные точки эмулятора, которые отличаются от конечных точек рабочей учетной записи хранения. Например, значение строки подключения будет выглядеть следующим образом:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Это значение идентично показанному выше ярлыку ( `UseDevelopmentStorage=true`).
