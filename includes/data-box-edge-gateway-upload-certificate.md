---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64732658"
---
Действительный сертификат SSL гарантирует, что вы отправляете зашифрованные сведения к требуемому серверу. Помимо шифрования этот сертификат позволяет также для проверки подлинности. Вы можете отправить свои собственные доверенный SSL-сертификат в интерфейсе PowerShell устройства.

1. [Подключение к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. Используйте `Set-HcsCertificate` командлет, чтобы отправить сертификат. При запросе укажите следующие параметры:

   - `CertificateFilePath` — Путь к общей папке, содержащей файл сертификата в *.pfx* формат.
   - `CertificatePassword` Пароль, используемый для защиты сертификата.
   - `Credentials` — Имя пользователя и пароль для доступа к папке, который содержит сертификат.

     В следующем примере показано использование этого командлета:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

