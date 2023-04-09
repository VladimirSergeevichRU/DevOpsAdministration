# Домашнее задание к занятию «Файловые системы»

------

## Задание

1. Узнайте о [sparse-файлах](https://ru.wikipedia.org/wiki/%D0%A0%D0%B0%D0%B7%D1%80%D0%B5%D0%B6%D1%91%D0%BD%D0%BD%D1%8B%D0%B9_%D1%84%D0%B0%D0%B9%D0%BB) (разряженных).
### Ответ:
Смысл в том, что вместо записи реального пустого места , записывается информация о количестве этого пустого места. Таким образом , если в файле действительно существует пустое место, то на диске может быть сэкономлено пространство. Информация о дырах храница в метаданных. Может быть так, что при копировании такого файла, дыры могут быть удалить и файл будет восстановлен в своём полном объёме.

1. Могут ли файлы, являющиеся жёсткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?
### Ответ:
* Нет, так как hard link ведёт на ту же inode, что и изначальный файл.

1. Сделайте `vagrant destroy` на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:

    ```ruby
    path_to_disk_folder = './disks'

    host_params = {
        'disk_size' => 2560,
        'disks'=>[1, 2],
        'cpus'=>2,
        'memory'=>2048,
        'hostname'=>'sysadm-fs',
        'vm_name'=>'sysadm-fs'
    }
    Vagrant.configure("2") do |config|
        config.vm.box = "bento/ubuntu-20.04"
        config.vm.hostname=host_params['hostname']
        config.vm.provider :virtualbox do |v|

            v.name=host_params['vm_name']
            v.cpus=host_params['cpus']
            v.memory=host_params['memory']

            host_params['disks'].each do |disk|
                file_to_disk=path_to_disk_folder+'/disk'+disk.to_s+'.vdi'
                unless File.exist?(file_to_disk)
                    v.customize ['createmedium', '--filename', file_to_disk, '--size', host_params['disk_size']]
                    v.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', disk.to_s, '--device', 0, '--type', 'hdd', '--medium', file_to_disk]
                end
            end
        end
        config.vm.network "private_network", type: "dhcp"
    end
    ```

    Эта конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2,5 Гб.
### Ответ: 
Машина была пересоздана, с необходимыми атрибутами и доп scsi накопителями.

![изображение](https://user-images.githubusercontent.com/123881243/230664749-914e49db-a25e-4a02-8593-6ad0689f65be.png)


1. Используя `fdisk`, разбейте первый диск на два раздела: 2 Гб и оставшееся пространство.
### Ответ: 
Создано два раздела, разного размера:

![изображение](https://user-images.githubusercontent.com/123881243/230666021-d4e550eb-037a-4715-98ff-70915e301fb9.png)


2. Используя `sfdisk`, перенесите эту таблицу разделов на второй диск.

### Ответ:
Чтобы перенести таблицу разделов выполнил команду из-под root `sfdisk -d /dev/sdb –force | sfdisk /dev/sdc`
![изображение](https://user-images.githubusercontent.com/123881243/230794630-86873687-0ee8-4b32-98cf-b12a77e0ff7a.png)

3. Соберите `mdadm` RAID1 на паре разделов 2 Гб.

4. Соберите `mdadm` RAID0 на второй паре маленьких разделов.

5. Создайте два независимых PV на получившихся md-устройствах.

6. Создайте общую volume-group на этих двух PV.

7. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.

8. Создайте `mkfs.ext4` ФС на получившемся LV.

9. Смонтируйте этот раздел в любую директорию, например, `/tmp/new`.

10. Поместите туда тестовый файл, например, `wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz`.

11. Прикрепите вывод `lsblk`.

12. Протестируйте целостность файла:

    ```bash
    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
    ```

13. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.

14. Сделайте `--fail` на устройство в вашем RAID1 md.

15. Подтвердите выводом `dmesg`, что RAID1 работает в деградированном состоянии.

16. Протестируйте целостность файла — он должен быть доступен несмотря на «сбойный» диск:

    ```bash
    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
    ```

17. Погасите тестовый хост — `vagrant destroy`.
 
*В качестве решения пришлите ответы на вопросы и опишите, как они были получены.*

----

### Правила приёма домашнего задания

В личном кабинете отправлена ссылка на .md-файл в вашем репозитории.


### Критерии оценки

Зачёт:

* выполнены все задания;
* ответы даны в развёрнутой форме;
* приложены соответствующие скриншоты и файлы проекта;
* в выполненных заданиях нет противоречий и нарушения логики.

На доработку:

* задание выполнено частично или не выполнено вообще;
* в логике выполнения заданий есть противоречия и существенные недостатки. 