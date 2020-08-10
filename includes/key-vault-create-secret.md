---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512822"
---
Мы создадим секрет с именем **mySecret** и значением **Success!** . Секретом может быть пароль, строка подключения SQL или другие сведения, которые должны быть защищены и при этом доступны приложению. 

Чтобы добавить секрет в созданное хранилище ключей, используйте команду Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set):

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```