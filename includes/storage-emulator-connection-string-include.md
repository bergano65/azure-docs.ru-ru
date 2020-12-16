---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582738"
---
Эмулятор поддерживает одну фиксированную учетную запись и известный ключ проверки подлинности для аутентификации с общим ключом. Эта учетная запись и ключ являются единственными учетными данными общего ключа, разрешенными для использования с эмулятором. К ним относятся:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Ключ проверки подлинности, поддерживаемый эмулятором, предназначен только для тестирования функциональности кода проверки подлинности клиента. Его использование не гарантирует защиту. Вы не можете использовать рабочую учетную запись хранения и ключ в эмуляторе. Не следует использовать учетную запись для разработки с рабочими данными.
> 
> Эмулятор поддерживает подключение только по протоколу HTTP. Тем не менее для доступа к ресурсам в рабочей учетной записи хранения Azure рекомендуется использовать протокол HTTPS.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Подключение к учетной записи эмулятора с помощью ярлыка
Самый простой способ подключиться к эмулятору из приложения — настроить строку подключения в файле конфигурации приложения, который ссылается на ярлык `UseDevelopmentStorage=true` . Ниже приведен пример строки подключения к эмулятору в файле *app.config* . 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Эквивалентно полному указанию имени учетной записи, ключа учетной записи и конечных точек для каждой службы эмулятора, которую вы хотите использовать в строке подключения. Это необходимо, чтобы строка подключения содержала ссылки на конечные точки эмулятора, которые отличаются от конечных точек рабочей учетной записи хранения. Например, значение строки подключения будет выглядеть следующим образом:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
