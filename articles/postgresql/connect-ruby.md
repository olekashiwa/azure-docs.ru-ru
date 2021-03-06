---
title: Подключение к службе "База данных Azure для PostgreSQL — отдельный сервер" с помощью Ruby
description: В этом кратком руководстве представлен пример кода Ruby, который можно использовать для подключения к службе "База данных Azure для PostgreSQL — отдельный сервер" и запроса данных из нее.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 5dd4cfe9bee0db6f14f736c79fe91770641008fb
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766911"
---
# <a name="azure-database-for-postgresql---single-server-use-ruby-to-connect-and-query-data"></a>База данных Azure для PostgreSQL — отдельный сервер: подключение и запрос данных с помощью Ruby
В этом кратком руководстве описывается, как подключиться к базе данных Azure для PostgreSQL с помощью приложения [Ruby](https://www.ruby-lang.org). Здесь также показано, как использовать инструкции SQL для запроса, вставки, обновления и удаления данных в базе данных. В этой статье предполагается, что у вас уже есть опыт разработки на Ruby и вы только начали работу с базой данных Azure для PostgreSQL.

## <a name="prerequisites"></a>Предварительные требования
В качестве отправной точки в этом кратком руководстве используются ресурсы, созданные в соответствии со следующими материалами:
- [Создание базы данных с помощью портала](quickstart-create-server-database-portal.md)
- [Создание базы данных с помощью Azure CLI](quickstart-create-server-database-azure-cli.md)

Также необходимо установить:
- [Ruby](https://www.ruby-lang.org/en/downloads/)
- Ruby pg, модуль PostgreSQL для Ruby

## <a name="get-connection-information"></a>Получение сведений о подключении
Получите сведения, необходимые для подключения к базе данных Azure.для PostgreSQL. Вам потребуется полное имя сервера и учетные данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева на портале Azure щелкните **Все ресурсы** и выполните поиск по имени созданного сервера (например, **mydemoserver**).
3. Щелкните имя сервера.
4. Запишите **имя сервера** и **имя для входа администратора сервера** с панели сервера **Обзор**. Если вы забыли свой пароль, можно также сбросить пароль с помощью этой панели.
 ![Имя сервера службы "База данных Azure для PostgreSQL"](./media/connect-ruby/1-connection-string.png)

> [!NOTE]
> Символ `@` в имени пользователя Azure Postgres URL был закодирован как `%40` во всех строках соединения. 

## <a name="connect-and-create-a-table"></a>Подключение и создание таблицы
Используйте приведенный ниже код для подключения и создайте таблицу с помощью инструкции SQL **CREATE TABLE**. Добавьте строки в таблицу, применив инструкцию SQL **INSERT INTO**.

Код использует объект [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) с конструктором [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) для подключения к базе данных Azure для PostgreSQL. Затем он вызывает метод [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) для выполнения команд DROP, CREATE TABLE и INSERT INTO. Этот код проверяет наличие ошибок с помощью класса [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error). После этого вызывается метод [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method), чтобы разорвать подключение перед завершением работы.

Замените строки `host`, `database`, `user` и `password` собственными значениями. 


```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Чтение данных
Используйте указанный ниже код с инструкцией SQL **SELECT** для подключения и чтения данных. 

Код использует объект [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) с конструктором [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) для подключения к базе данных Azure для PostgreSQL. Затем он вызывает метод [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) для выполнения команды SELECT, сохраняя результаты в итоговом наборе. Для коллекции набора результатов выполняется итерация с применением цикла `resultSet.each do`. При этом данные строки текущего значения сохраняются в переменной `row`. Этот код проверяет наличие ошибок с помощью класса [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error). После этого вызывается метод [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method), чтобы разорвать подключение перед завершением работы.

Замените строки `host`, `database`, `user` и `password` собственными значениями. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Обновление данных
Используйте указанный ниже код с инструкцией SQL **UPDATE** для подключения и обновления данных.

Код использует объект [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) с конструктором [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) для подключения к базе данных Azure для PostgreSQL. Затем он вызывает метод [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) для выполнения команды UPDATE. Этот код проверяет наличие ошибок с помощью класса [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error). После этого вызывается метод [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method), чтобы разорвать подключение перед завершением работы.

Замените строки `host`, `database`, `user` и `password` собственными значениями. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Удаление данных
Используйте указанный ниже код с инструкцией SQL **DELETE** для подключения и чтения данных. 

Код использует объект [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) с конструктором [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) для подключения к базе данных Azure для PostgreSQL. Затем он вызывает метод [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) для выполнения команды UPDATE. Этот код проверяет наличие ошибок с помощью класса [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error). После этого вызывается метод [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method), чтобы разорвать подключение перед завершением работы.

Замените строки `host`, `database`, `user` и `password` собственными значениями. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Перенос базы данных с помощью экспорта и импорта](./howto-migrate-using-export-and-import.md)
