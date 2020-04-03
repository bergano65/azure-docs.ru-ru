---
title: Помутнение Лазурного Бастиона (ru) Документы Майкрософт
description: В этой статье узнайте, как устранить неполадки ВАЗО "Бастион".
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 749d7125c013f419197ef8243d2475e612dc81b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619164"
---
# <a name="troubleshoot-azure-bastion"></a>Устранение неполадок в службе "Бастион Azure"

В этой статье показано, как устранить неполадки Azure Bastion.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Невозможно создать NSG на AzureBastionSubnet

**В.** Когда я пытаюсь создать NSG в подсети Azure Bastion, я получаю следующую ошибку: * <NSG name> «Группа сетевой безопасности не имеет необходимых правил для подсети Azure Bastion AzureBastionSubnet».*

**A:** Если вы создаете и применяете NSG в *AzureBastionSubnet,* убедитесь, что вы добавили следующие правила в свой NSG. Если вы не добавите эти правила, создание/обновление NSG завершится неудачей.

1. Управление подключением самолета - Входящий на 443 от GatewayManager
2. Регистрация диагностики и другие - Исходящие на 443 в AzureCloud (региональные теги в этом теге обслуживания еще не поддерживаются.)
3. Целевой VM - Исходящие для 3389 и 22 в VirtualNetwork

Пример правил NSG доступен для справки в [шаблоне quickstart.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion)
Для получения дополнительной [NSG guidance for Azure Bastion](bastion-nsg.md)информации см.

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Невозможно использовать клавишу SSH с Azure Bastion

**В.** Когда я пытаюсь просмотреть мой файл ключа SSH, я получаю следующую ошибку: *"SSH Частный ключ должен начаться с -----BEGIN RSA PRIVATE KEY----- и заканчивается -----END RSA PRIVATE KEY-----"*.

**A:** На данный момент Azure Bastion поддерживает только клавиши RSA SSH. Убедитесь, что вы просматриваете ключевой файл, который является конфиденциальным ключом RSA для SSH, с открытым ключом, подготовленным на целевом VM. 

Например, можно использовать следующую команду для создания нового ключа RSA SSH:

**ssh-keygen -t rsa -b 4096 -C "email@domain.com**

Выходные данные:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Невозможно войти в мою виртуальную машину, связанную с доменом Windows

**В.** Я не могу подключиться к моей виртуальной машине Windows, которая подключена к домену.

**A:** Azure Bastion поддерживает вступив в систему VM- интерфейс, в который можно вписаться только в доменана на имя пользователя. При указании учетных данных домена на портале Azure используйте формат UPN (username@domain) вместо формата *domain'username* для вхинга. Это поддерживается для доменов, соединенных или гибридных (как кондомета, так и связанных с Azure AD) виртуальных машин. Он не поддерживается для виртуальных машин, связанных только с Azure AD.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Проблемы передачи файлов

**В.** Поддерживается ли передача файлов Azure Bastion?

**A:** Передача файлов в настоящее время не поддерживается. Мы работаем над добавлением поддержки.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Черный экран на портале Azure

**В.** Когда я пытаюсь подключиться с помощью Azure Bastion, я получаю черный экран на портале Azure.

**A:** Это происходит, когда между веб-браузером и Azure Bastion возникает проблема с подключением к сети (ваш клиентский интернет-брандмауэр может блокировать трафик WebSockets или аналогичный), либо между Azure Bastion и вашим целевым VM. В большинстве случаев nsG применяется либо к AzureBastionSubnet, либо к вашей целевой подсети VM, которая блокирует трафик RDP/SSH в вашей виртуальной сети. Разрешить WebSockets трафик на вашем клиенте интернет-брандмауэр, и проверить NSGs на вашей целевой подсети VM.

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительной [информации](bastion-faq.md)см.