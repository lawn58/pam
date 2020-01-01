# pam

1. Запретить всем пользователям, кроме группы admin логин в выходные дни
1.1 pam_time
Создадим группы admin и myusers и добавим пользователей vasya, petya и kolya:

# groupadd myusers
# groupadd admin
# useradd -g myusers vasya
# useradd -g myusers petya
# useradd -g admin kolya
Добавим правило в /etc/security/time.conf, запрещающее вход в выходные дни всем пользователям, кроме тех, кто принадлежит группе admin:

sshd;*;!admin;!Wk0000-2400
Добавим правило в /etc/pam.d/sshd

account required pam_time.so
Пробуем зайти по ssh:

$ ssh petya@localhost

petya@localhost's password:
Authentication failed.
Наше правило сработало. Теперь все пользователи, кроме пользователей группы admin не могут зайти по ssh на хост.

*
1) Для того чтобы дать возможность использовать Docker обычным пользователям в системы, мы можем выставить suid-бит:

#chmod ug+s /usr/bin/docker

Больше не придется писать sudo перед использованием Docker.

2) Так же, мы можем добавить пользователя в группу Docker (которая создается по умолчанию при установке Docker из пакетного менеджера)

#gpasswd -a ${USER} docker

Эта команда добавляет текущего пользователя в группу Docker и дает ему право запускать Docker.

Для того чтобы дать пользователю право рестартить докер сервис, мы используем такую команду

# echo "user ALL=/usr/bin/systemctl restart docker" >> /etc/sudoers

Эта команда позволяет пользователю user, выполнять команду systemctl с определенными опциями, в нашем случае - перезапускать докер сервис.
