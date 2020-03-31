---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67448633"
---
Надлежащий SSL-сертификат гарантирует отправку зашифрованной информации на нужный сервер. Помимо шифрования, сертификат также позволяет проверить подлинность. Вы можете загрузить свой собственный доверенный сертификат SSL через интерфейс PowerShell устройства.

1. [Подключение к интерфейсу PowerShell.](#connect-to-the-powershell-interface)
2. Используйте `Set-HcsCertificate` cmdlet для загрузки сертификата. При запросе укажите следующие параметры:

   - `CertificateFilePath`- Путь к акции, содержащей файл сертификата в *формате .pfx.*
   - `CertificatePassword`- Пароль, используемый для защиты сертификата.
   - `Credentials`- Имя пользователя для доступа к акции, содержащей сертификат. Предоставьте пароль к сети доля, когда предложено.

     Следующий пример показывает использование этого cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

