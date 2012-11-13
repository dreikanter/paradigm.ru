title: Расширяем возможности контекстного меню Explorer
link: http://paradigm.ru/explorer-context-menu
creator: admin
description: 
post_id: 290
post_date: 2008-04-29 02:23:51
post_date_gmt: 2008-04-29 09:23:51
comment_status: open
post_name: explorer-context-menu
status: publish
post_type: post

# Расширяем возможности контекстного меню Explorer

Возможность добавить в контекстное меню Windows Explorer пункт «Open console here» давно не была новостью. Но необходимость в ее использовании всерьёз возникла только недавно. Когда приходится по десять раз в день открывать консоль и нудную писать команду перевода текущей директории из этого ужасного «`C:\Documents and Settings\&#133;`» куда надо, поневоле задумаешься о том, как упростить процесс.

А упростить его не сложно. Если добавить в реестр перечисленные ниже ключи, в контекстном меню появится новый пункт для открытия консольного окна в выбранной директории:
    
    Windows Registry Editor Version 5.00
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\cmd_root]
    @="Open console here"
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\cmd_root\command]
    @="Cmd.exe /k pushd \"%L\""
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Drive\shell\cmd_root]
    @="Open console here"
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Drive\shell\cmd_root\command]
    @="Cmd.exe /k pushd \"%L\""

Альтернативный вариант для открытия консоли с правами администратора (может быть полезен, если ваш действующий пользовательский профиль этими правами обделен):
    
    Windows Registry Editor Version 5.00
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\cmd_root]
    @="Open root console here"
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\cmd_root\command]
    @="runas /user:**COMPUTER_NAME**\\**AdministratorName** \"Cmd.exe /k pushd %L\""
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Drive\shell\cmd_root]
    @="Open root console here"
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Drive\shell\cmd_root\command]
    @="runas /user:**COMPUTER_NAME**\\**AdministratorName** \"Cmd.exe /k pushd %L\""

COMPUTER_NAME и AdministratorName, как не сложно догадаться, заменяются на имя машины и администратора соответственно. Пароль будет запрашиваться при открытии административной консоли.

Если лень возиться с реестром, можно воспользоваться программой из комплекта [Microsoft PowerToys](http://b23.ru/pyq). Понадобится та, что называется «Open Command Window Here». Но на мой взгляд, скопировать ключи в .reg файл и импортировать в реестр проще, чем искать для этого специальную утилиту.

Пока я искал гуглом приведенные выше ключи реестра, пришла мысль об автоматизации еще одного рутинного действия. Время от времени, для сортировки файлов я часто использую директории с именами в формате `YYYYMMDD-desc` (`YYYYMMDD`  текущая дата, `desc`  описание). По такому принципу, например, организуются черновики постов для блога, архивы документов, скачанные файлы, которые нужно разобрать и много еще чего.

Для генерации таких директорий я добавил еще один пункт в контекстное меню. В отличии от первого примера, здесь запускается небольшой скрипт на python, который, собственно, директорию и создает (скрипт приведен ниже).
    
    Windows Registry Editor Version 5.00
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\cmd_tmp]
    @="Create temp directory"
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\cmd_tmp\command]
    @="d:\\serv\\python25\\pythonw.exe d:\\pro\\mkdir.py \"%L\""
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Drive\shell\cmd_tmp]
    @="Create temp directory"
    
    [HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Drive\shell\cmd_tmp\command]
    @="d:\\path\\python25\\pythonw.exe d:\\path\\mkdir.py \"%L\""

Перед импортированием в реестр, понадобится заменить путь к скрипту `mkdir.py` и интерпретатору `pythonw` на реальные значения.

Вполне возможно, что генерировать date-based folder names можно и без питона, ограничившись командами консольного интерпретатора. Но, во-первых, на момент написания скрипта, я не знал, как это делается, во-вторых, скрипт при каждом вызове может генерировать новую директорию, добавляя к дате дополнительный числовой индекс (при повторном вызове, индекс инкрементируется). В том, что последнюю деталь можно реализовать в bat-файле я сомневаюсь.
    
    from sys import argv, exit
    from os import mkdir, path
    from time import strftime
    
    try:
        dir_path = argv[1]
    except:
        dir_path = "."
    
    i = 0
    
    while i < 10000:
        dir_name = dir_path + "/%s-%03d" % (strftime("%Y%m%d"), i)
        if not path.isdir(dir_name):
            break
        i += 1
    else:
        exit()
    
    mkdir(dir_name)

На всякий случай в скрипте сделано ограничение на максимальное количество директорий. Если у кого-то есть конструктивные комментарии относительно улучшения кода  с интересом выслушаю, т.к. на питоне начал писать совсем недавно.

Да, и еще одна деталь. Все описанные выше модификации реестра проверены на Windows XP. Будет ли это работать на Висте мне не известно. Предполагаю, что будет.

Все примеры из поста можно скачать в одном архиве: [explorer-console-menu-ext.zip](http://things.paradigm.ru/explorer-console-menu-ext.zip).

## Comments

**[pk](#633 "2008-04-29 19:38:05"):** .reg файлы почему-то не сработали, но за ссылочку на Microsoft PowerToys спасибо - пригодилась.

