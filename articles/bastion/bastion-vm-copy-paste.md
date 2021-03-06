---
title: 'Копирование и вставка в виртуальную машину и из нее: Azure бастиона'
description: Из этой статьи вы узнаете, как копировать и вставлять в виртуальную машину Azure и из нее с помощью бастиона.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 0aaf816cdfe7d42fd345eb4f010cf47b1615f462
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989541"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Копирование и вставка на виртуальную машину: Azure бастиона

Эта статья поможет вам скопировать и вставить текст в виртуальные машины и из них при использовании Azure бастиона. Перед началом работы с виртуальной машиной убедитесь, что выполнены действия по [созданию узла бастиона](bastion-create-host-portal.md). Затем подключитесь к виртуальной машине, которую вы хотите использовать, с помощью [RDP](bastion-connect-vm-rdp.md) или [SSH](bastion-connect-vm-ssh.md).

Для браузеров, поддерживающих расширенный доступ к API буфера обмена, можно копировать и вставлять текст между локальным устройством и удаленным сеансом так же, как и при копировании и вставке между приложениями на локальном устройстве. Для других браузеров можно использовать палитру средства доступа к буферу обмена бастиона.

   ![Разрешить буфер обмена](./media/bastion-vm-manage/allow.png)

Поддерживается только текст для копирования и вставки. Для прямого копирования и вставки в браузере может появиться запрос на доступ к буферу обмена при инициализации сеанса бастиона. **Разрешить** веб-странице доступ к буферу обмена.

## <a name="to"></a>Копирование в удаленный сеанс

После подключения к виртуальной машине с помощью [портал Azure ](https://portal.azure.com)выполните следующие действия.

1. Копировать текст или содержимое с локального устройства в локальный буфер обмена.
1. Во время удаленного сеанса откройте палитру средств доступа к буферу обмена бастиона, выбрав две стрелки. Стрелки расположены в левом центре сеанса.

   ![Палитра инструментов](./media/bastion-vm-manage/left.png)

   ![буфер обмена](./media/bastion-vm-manage/clipboard.png)
1. Как правило, скопированный текст автоматически отображается в палитре «бастиона Copy Вклеить». Если текст отсутствует, вставьте текст в текстовое поле палитры.
1. После того как текст появится в текстовой области, его можно вставить в удаленный сеанс.

   ![авить](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Копирование из удаленного сеанса

После подключения к виртуальной машине с помощью [портал Azure ](https://portal.azure.com)выполните следующие действия.

1. Копировать текст или содержимое из удаленного сеанса в удаленный буфер обмена (с помощью клавиш CTRL + C).

   ![Палитра инструментов](./media/bastion-vm-manage/remote.png)
1. Во время удаленного сеанса откройте палитру средств доступа к буферу обмена бастиона, выбрав две стрелки. Стрелки расположены в левом центре сеанса.

   ![буфер обмена](./media/bastion-vm-manage/clipboard2.png)
1. Как правило, скопированный текст автоматически отображается в палитре «бастиона Copy Вклеить». Если текст отсутствует, вставьте текст в текстовое поле палитры.
1. После того как текст появится в текстовой области, его можно вставить на локальное устройство.

   ![авить](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [часто задаваемыми вопросами о бастиона](bastion-faq.md).