---
title: Устранение неполадок при работе с System Center Data Protection Manager
description: В этой статье описаны решения проблем, которые могут возникнуть при использовании System Center Data Protection Manager.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: bcb30fa7eb3e05099761fc751b09a9fb16134e34
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2020
ms.locfileid: "75664749"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Устранение неполадок при работе с System Center Data Protection Manager

В этой статье описывается устранение проблем, которые могут возникнуть при использовании Data Protection Manager.

Последние заметки о выпуске System Center Data Protection Manager приведены в [документации по System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Узнать больше о поддержке Data Protection Manager можно из [этой матрицы](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).

## <a name="error-replica-is-inconsistent"></a>Ошибка: "Реплика не согласована"

Реплика может быть несогласованной по следующим причинам.

- Ошибка задания создания реплики.
- Проблемы с журналом изменений.
- Битовая карта фильтра уровня тома содержит ошибки.
- Исходный компьютер неожиданно завершает работу.
- Журнал синхронизации переполнен.
- Реплика действительно несогласованная.

Чтобы устранить эту проблему, выполните описанные ниже действия.

- Чтобы устранить несогласованное состояние, выполните проверку согласованности вручную или настройте ежедневную проверку согласованности по расписанию.
- Убедитесь, что вы используете последнюю версию Microsoft Azure Backup Server и Data Protection Manager.
- Убедитесь, что параметр **Automatic Consistency** (Автоматическая согласованность) включен.
- Попытайтесь перезапустить службы из командной строки. Последовательно выполните команды `net stop dpmra` и `net start dpmra`.
- Убедитесь, что соблюдаются требования к сетевым подключениям и пропускной способности.
- Проверьте, не был ли исходный компьютер неожиданно выключен.
- Убедитесь, что диск работоспособен и содержит достаточно места для реплики.
- Проверьте, нет ли выполняющихся параллельно аналогичных заданий резервного копирования.

## <a name="error-online-recovery-point-creation-failed"></a>Ошибка: "Сбой при создании точки восстановления в сети"

Чтобы устранить эту проблему, выполните описанные ниже действия.

- Убедитесь, что вы используете самую последнюю версию агента Azure Backup.
- Попробуйте вручную создать точку восстановления в области задач защиты.
- Обязательно проверьте согласованность источника данных.
- Убедитесь, что соблюдаются требования к сетевым подключениям и пропускной способности.
- Когда данные реплики находятся в несогласованном состоянии, создайте точку восстановления диска для этого источника данных.
- Убедитесь, что реплика существует.
- Убедитесь, что реплика содержит достаточно места для создания журнала номеров последовательных обновлений (USN).

## <a name="error-unable-to-configure-protection"></a>Ошибка: "Не удалось настроить защиту"

Эта ошибка возникает, когда серверу Data Protection Manager не удается связаться с защищенным сервером.

Чтобы устранить эту проблему, выполните описанные ниже действия.

- Убедитесь, что вы используете самую последнюю версию агента Azure Backup.
- Убедитесь в наличии подключения (по сети, через брандмауэр или прокси-сервер) между сервером Data Protection Manager и защищенным сервером.
- Если вы защищаете сервер SQL Server, убедитесь, что свойство **Свойства имени для входа** > **NT AUTHORITY\SYSTEM** содержит включенный параметр **sysadmin**.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Ошибка: "Server not registered as specified in vault credential file" (Сервер не зарегистрирован так, как указано в файле учетных данных хранилища)

Эта ошибка возникает во время восстановления данных сервера Data Protection Manager или Azure Backup Server. Файл учетных данных хранилища, который используется в процессе восстановления, не принадлежит хранилищу служб восстановления для Data Protection Manager или Azure Backup Server.

Проблему можно устранить следующим способом.

1. Скачайте файл учетных данных из хранилища служб восстановления, в котором зарегистрирован сервер Data Protection Manager или Azure Backup Server.
2. Попробуйте зарегистрировать сервер в хранилище с помощью самого последнего скачанного файла учетных данных хранилища.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Ошибка: " No recoverable data or selected server not a Data Protection Manager server" (Восстанавливаемые данные отсутствуют или выбранный сервер не является сервером Data Protection Manager)

Эта ошибка возникает последующим причинам.

- В хранилище служб восстановления не зарегистрированы другие серверы Data Protection Manager или Azure Backup Server.
- Серверы еще не передали метаданные.
- Выбранный сервер не является сервером Data Protection Manager или Azure Backup Server.

Если в хранилище служб восстановления зарегистрированы другие серверы Data Protection Manager или Azure Backup Server, устраните данную проблему следующим способом.

1. Убедитесь, что установлена самая последняя версия агента Azure Backup.
2. После этого подождите один день, прежде чем начать процесс восстановления. Задания резервного копирования, запланированные на ночь, передадут метаданные для всех защищенных резервных копий в облако. После этого данные резервных копий станут доступны для восстановления.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Ошибка: "Provided encryption passphrase doesn't match passphrase for server" (Введенная парольная фраза для шифрования не совпадает с парольной фразой для сервера)

Эта ошибка возникает во время шифрования при восстановлении данных сервера Data Protection Manager или Azure Backup Server. Парольная фраза для шифрования, используемая в процессе восстановления, не совпадает с парольной фразой для шифрования сервера. В результате агенту не удается расшифровать данные и происходит сбой восстановления.

> [!IMPORTANT]
> Если вы забыли или потеряли парольную фразу для шифрования, другие методы восстановления данных отсутствуют. Единственной возможностью является повторное создание парольной фразы. Используйте новую парольную фразу для шифрования будущих данных резервных копий.
>
> При восстановлении данных всегда указывайте парольную фразу для шифрования, связанную с сервером Data Protection Manager или Azure Backup Server.
>
