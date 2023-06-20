# Запретить всем пользователям, кроме группы admin логин в выходные (суббота и воскресенье), без учета праздников

Для выполнения посмотрим как можно управлять доступом для групп.
PAM имеет модуль управления группами пользователей: pam_group: http://www.linux-pam.org/Linux-PAM-html/sag-pam_group.html

Проверяем наличие группы admin:

```
root@Lab10 ~# less /etc/group |grep admin
```

Группа admin отсутствует. Создадим пользователей user1, user2 и добавам в группу admin.

```
root@Lab10 ~# useradd -m -s /bin/bash user1
root@Lab10 ~# useradd -m -s /bin/bash user2
root@Lab10 ~# echo "Otus2020" | sudo passwd --stdin user1 &&\
> echo "Otus2020" | sudo passwd --stdin user2
Changing password for user user1.
passwd: all authentication tokens updated successfully.
Changing password for user user2.
passwd: all authentication tokens updated successfully.
root@Lab10 ~# groupadd admin &&\
> gpasswd -a user1 admin && \
> gpasswd -a user2 admin
Adding user user1 to group admin
Adding user user2 to group admin
root@Lab10 ~# id u iCloud Keychainser1
uid=1004(user1) gid=1004(user1) groups=1004(user1),1006(admin)
root@Lab10 ~# id user2
uid=1005(user2) gid=1005(user2) groups=1005(user2),1006(admin)
```

Пользователи созданы и добвалены в гуппу admin.

Настроим PAM-модуль управления группами в файле /etc/security/group.conf
Используем следующую форму записи: сервис;терминал;пользователь;время доступа;группа
```
*;*;*;Wd0900-2000;*
*;*;*;Al;admin
```

В данном примере мы разрешили аутотенфикацию всем пользователям и всем группам в рабочие дни с 9-00 до 20-00. И доступ группе admin во все дни в любое время.

Добавим данный модуль в PAM /etc/pam.d/sshd
