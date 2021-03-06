---
title: Создание и шифрование виртуальной машины Linux на портале Azure
description: Из этого краткого руководства вы узнаете, как с помощью портала Azure создать и зашифровать виртуальную машину Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 20802cf9f9934a85e00bce41b6bbe21a6ff95579
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772301"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Краткое руководство. Создание и шифрование виртуальной машины на портале Azure

Виртуальные машины Azure можно создать на портале Azure. Портал Azure — это пользовательский интерфейс на основе веб-браузера, который позволяет создавать виртуальные машины и связанные с ними ресурсы. В этом кратком руководстве вы будете использовать портал Azure для развертывания виртуальной машины Linux под управлением Ubuntu 18.04 LTS, создания хранилища ключей для хранения ключей шифрования, а также для шифрования этой виртуальной машины.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.
1. На странице "Создать" в разделе "Популярные" выберите **Ubuntu Server 18.04 LTS**.
1. На вкладке **Basics** (Основные) в разделе **Project Details** (Сведения о проекте) убедитесь, что выбрана правильная подписка.
1. Для **Группы ресурсов** выберите группу ресурсов, созданную при создании хранилища ключей выше (например, **myResourceGroup**)
1. В поле **Имя виртуальной машины** введите *MyVM*.
1. Для параметра **регион** выберите тот же регион, который использовался при создании хранилища ключей выше (например, **Восточная часть США**).
1. Убедитесь, что для параметра **Размер** выбрано значение *Standard D2s v3*.
1. В разделе **Учетная запись администратора** выберите **Пароль**. Введите имя пользователя и пароль.
    ![Экран создания группы ресурсов](./media/disk-encryption/portal-qs-vm-creation.png)
1. Выберите вкладку "Управление" и убедитесь, что у вас есть учетная запись хранения для диагностики. Если у вас нет учетных записей хранения, выберите "Создать", укажите имя новой учетной записи и нажмите кнопку "ОК". ![Экран создания группы ресурсов](./media/disk-encryption/portal-qs-vm-creation-storage.png)
1. Щелкните "Проверить и создать".
1. На странице **Создание виртуальной машины** отображаются сведения о создаваемой виртуальной машине. Когда вы будете готовы, нажмите **Создать**.

Развертывание виртуальной машины может занять несколько минут. После завершения развертывания перейдите к следующему разделу.

## <a name="encrypt-the-virtual-machine"></a>Шифрование виртуальной машины

1. Когда развертывание ВМ будет завершено, выберите **Перейти к ресурсу**.
1. На боковой панели слева выберите **Диски**.
1. На экране "Диски" выберите **Шифрование**. 

    ![Выбор дисков и шифрования](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. На экране шифрования в разделе **Disks to Encrypt** (Шифруемые диски) выберите **OS and data disks** (ОС и диски данных).
1. В разделе **Настройки шифрования** щелкните **Выберите хранилище ключей и ключ для шифрования**.
1. На экране **Select key from Azure Key Vault** (Выбор ключа из Azure Key Vault) нажмите кнопку **Создать**.

    ![Выбор дисков и шифрования](../media/disk-encryption/portal-qs-keyvault-create.png)

1. На экране **Создать хранилище ключей** убедитесь, что группа ресурсов совпадает с той, которая использовалась для создания виртуальной машины.
1. Присвойте своему хранилищу ключей имя.  Каждое хранилище ключей в Azure должно иметь уникальное имя.
1. На вкладке **Политики доступа** установите флажок **Шифрование дисков Azure для шифрования томов**.

    ![Выбор дисков и шифрования](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. Выберите **Review + create** (Просмотреть и создать).  
1. После того как хранилище ключей пройдет проверку, нажмите кнопку **Создать**. Вы вернетесь на экран **Select key from Azure Key Vault** (Выбор ключа из Azure Key Vault).
1. Оставьте поле **Ключ** пустым и нажмите кнопку **Выбрать**.
1. В верхней части экрана шифрования нажмите кнопку **Сохранить**. После этого откроется всплывающее окно с предупреждением о том, что виртуальная машина будет перезагружена. Нажмите кнопку **Да**.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов, виртуальная машина и все связанные с ними ресурсы вам больше не требуются, их можно удалить. Для этого выберите группу ресурсов для виртуальной машины, выберите действие "Удалить" и подтвердите имя удаляемой группы ресурсов.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали хранилище ключей для ключей шифрования, создали виртуальную машину и включили шифрование для этой виртуальной машины.  

> [!div class="nextstepaction"]
> [Общие сведения о шифровании дисков Azure](disk-encryption-overview.md)
