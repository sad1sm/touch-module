# Домашнее задание к занятию 08.04

Наша цель - написать собственный module, который мы можем использовать в своей role, через playbook. Всё это должно быть собрано в виде collection и отправлено в наш репозиторий.

1. В виртуальном окружении создать новый `my_own_module.py` файл.
2. Наполнить его содержимым из [статьи](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html#creating-a-module).
3. Заполните файл в соответствии с требованиями ansible так, чтобы он выполнял основную задачу: module должен создавать текстовый файл на удалённом хосте по пути, определённом в параметре `path`, с содержимым, определённым в параметре `content`.
4. Проверьте module на исполняемость локально.
```js
(venv) $ python -m ansible.modules.touch_module test.json

{"changed": true, "original_message": "All your base are belong to us.", "message": "File created.", "invocation": {"module_args": {"path": "/tmp/file.conf", "content": "All your base are belong to us."}}}

(venv) $ cat /tmp/file.conf 
All your base are belong to us.

(venv) $ python -m ansible.modules.touch_module test.json

{"changed": false, "original_message": "All your base are belong to us.", "message": "File exists.", "invocation": {"module_args": {"path": "/tmp/file.conf", "content": "All your base are belong to us."}}}
```
5. Напишите single task playbook и используйте module в нём.
6. Проверьте через playbook на идемпотентность.
```js
(venv) $ ansible-playbook playbook.yml 

PLAY [Create file with touch module.] ******************************************************************************

TASK [Gathering Facts] ******************************************************************************
ok: [localhost]

TASK [Create file.] ******************************************************************************
changed: [localhost]

TASK [debug] ******************************************************************************
ok: [localhost] => {
    "msg": "File created."
}

PLAY RECAP ******************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) $ ansible-playbook playbook.yml 

PLAY [Create file with touch module.] ******************************************************************************

TASK [Gathering Facts] ******************************************************************************
ok: [localhost]

TASK [Create file.] ******************************************************************************
ok: [localhost]

TASK [debug] ******************************************************************************
ok: [localhost] => {
    "msg": "File exists."
}

PLAY RECAP ******************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```
7. Выйдите из виртуального окружения.
8. Инициализируйте новую collection: `ansible-galaxy collection init my_own_namespace.yandex_cloud_elk`
```
$ ansible-galaxy collection init netology.touch_module
- Collection netology.touch_module was created successfully
```
9. В данную collection перенесите свой module в соответствующую директорию.
10. Single task playbook преобразуйте в single task role и перенесите в collection. У role должны быть default всех параметров module
11. Создайте playbook для использования этой role.
12. Заполните всю документацию по collection, выложите в свой репозиторий, поставьте тег `1.0.0` на этот коммит.
13. Создайте .tar.gz этой collection: `ansible-galaxy collection build` в корневой директории collection.
```
$ ansible-galaxy collection build
Created collection for netology.touch_module at ~/netology/touch_module/netology-touch_module-1.0.0.tar.gz
```
14. Создайте ещё одну директорию любого наименования, перенесите туда single task playbook и архив c collection.
15. Установите collection из локального архива: `ansible-galaxy collection install <archivename>.tar.gz`
```cd
$ ansible-galaxy collection install netology-touch_module-1.0.0.tar.gz 
Starting galaxy collection install process
Process install dependency map
Starting collection install process
Installing 'netology.touch_module:1.0.0' to '~/.ansible/collections/ansible_collections/netology/touch_module'
netology.touch_module:1.0.0 was installed successfully
```
16. Запустите playbook, убедитесь, что он работает.
```js
$ ansible-playbook playbook.yml 

PLAY [Testing touch module.] ******************************************************************************
g
TASK [Gathering Facts] ******************************************************************************
ok: [localhost]

TASK [role-touch-file : Create file.] ******************************************************************************
changed: [localhost]

TASK [role-touch-file : debug] ******************************************************************************
ok: [localhost] => {
    "msg": "File created."
}

PLAY RECAP ******************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
17. В ответ необходимо прислать ссылку на репозиторий с collection
