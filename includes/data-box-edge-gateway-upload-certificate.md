---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 216380cf7069468d13c4e533fc90b2596aa211c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116159"
---
Действительный сертификат SSL гарантирует, что вы отправляете зашифрованные сведения к требуемому серверу. Помимо шифрования этот сертификат позволяет также для проверки подлинности. Вы можете отправить свои собственные доверенный SSL-сертификат в интерфейсе PowerShell устройства.

1. [Подключение к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. Используйте `Set-HcsCertificate` командлет, чтобы отправить сертификат. При запросе укажите следующие параметры:

   - `CertificateFilePath` — Путь к общей папке, содержащей файл сертификата в *.pfx* формат.
   - `CertificatePassword` Пароль, используемый для защиты сертификата.
   - `Credentials` — Имя пользователя и пароль для доступа к папке, который содержит сертификат.

     В следующем примере показано использование этого командлета:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
     ```

