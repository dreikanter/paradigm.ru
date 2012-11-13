title: Благоразумный C#
link: http://paradigm.ru/smart-csh
creator: admin
description: 
post_id: 259
created: 2008/04/02 12:02:58
created_gmt: 2008/04/02 19:02:58
comment_status: open
post_name: smart-csh
status: publish
post_type: post

# Благоразумный C#

Делаю так:
    
    String query = "SELECT COUNT(*) FROM ...";
    MySqlDataReader r;
    
    try
    {
        r = sqlQuery(query);
        // ...
    }
    catch (Exception ex)
    {
        // ...
    }
    finally
    {
        r.Close();
    }

Компилятор говорит, ошибка в строке `r.Close();`. Дескать, обращение к непроинициализированной переменной. Заменяю `MySqlDataReader r;` на `MySqlDataReader r = null;`. Прокатывает. Умный и заботливый C# (:

## Comments

**[Eldar](#527 "2008/04/11 19:13:52"):** А ты попробуй using ( MySqlDataReader r = sqlQuery(query) ) { }

