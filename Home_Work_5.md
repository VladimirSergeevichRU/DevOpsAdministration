# Домашнее задание к занятию «Файловые системы»

------

## Задание

1. Узнайте о [sparse-файлах](https://ru.wikipedia.org/wiki/%D0%A0%D0%B0%D0%B7%D1%80%D0%B5%D0%B6%D1%91%D0%BD%D0%BD%D1%8B%D0%B9_%D1%84%D0%B0%D0%B9%D0%BB) (разряженных).

### Ответ:
Смысл в том, что вместо записи реального пустого места , записывается информация о количестве этого пустого места. Таким образом , если в файле действительно существует пустое место, то на диске может быть сэкономлено пространство. Информация о дырах храница в метаданных. Может быть так, что при копировании такого файла, дыры могут быть удалить и файл будет восстановлен в своём полном объёме.

2. Могут ли файлы, являющиеся жёсткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?

### Ответ:
* Нет, так как hard link ведёт на ту же inode, что и изначальный файл.

3. Сделайте `vagrant destroy` на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:

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


4. Используя `fdisk`, разбейте первый диск на два раздела: 2 Гб и оставшееся пространство.

### Ответ: 
Создано два раздела, разного размера:

![изображение](https://user-images.githubusercontent.com/123881243/230666021-d4e550eb-037a-4715-98ff-70915e301fb9.png)


5. Используя `sfdisk`, перенесите эту таблицу разделов на второй диск.

### Ответ:
Чтобы перенести таблицу разделов выполнил команду из-под root `sfdisk -d /dev/sdb –force | sfdisk /dev/sdc`
![изображение](https://user-images.githubusercontent.com/123881243/230794630-86873687-0ee8-4b32-98cf-b12a77e0ff7a.png)

6. Соберите `mdadm` RAID1 на паре разделов 2 Гб.

### Ответ: 
Командой `mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sd[b-c]1` был создан RAID 1.
![изображение](https://user-images.githubusercontent.com/123881243/230964254-7004e956-44d3-4ba2-9ecd-7d3e1c84e5fc.png)


7. Соберите `mdadm` RAID0 на второй паре маленьких разделов.

### Ответ: 
Командой `mdadm --create --verbose /dev/md1 --level=0 --raid-devices=2 /dev/sd[b-c]2` был создан RAID 0.
![изображение](https://user-images.githubusercontent.com/123881243/230964882-930eabc8-90dc-4313-be0a-bca810dc03a3.png)

8. Создайте два независимых PV на получившихся md-устройствах.

### Ответ: 
Командами `pvcreate /dev/md0` и `pvcreate /dev/md1` были созданы два физических тома.
![изображение](https://user-images.githubusercontent.com/123881243/230967260-76eb644d-12fd-4725-a1b1-0ab6c310398c.png)

9. Создайте общую volume-group на этих двух PV.

### Ответ: 
Командой `vgcreate vg0 /dev/md0 /dev/md1` была создана группа томов.
![изображение](https://user-images.githubusercontent.com/123881243/230968512-e7edd7cb-46b2-4f57-bafc-1c82b8f2a7c4.png)


10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.

### Ответ: 
Командой `lvcreate -L 100 -n lv0 vg0 /dev/md1` был создан логический том lv0.
![изображение](https://user-images.githubusercontent.com/123881243/230971126-edd87780-edd1-4edb-bea4-5c1462318508.png)


11. Создайте `mkfs.ext4` ФС на получившемся LV.

### Ответ: 
Командой `mkfs.ext4 /dev/vg0/lv0` была создана ФС на lv0
![изображение](https://user-images.githubusercontent.com/123881243/230971931-dc9207c2-0d3a-4737-82e1-4a4adc1d8b3d.png)


12. Смонтируйте этот раздел в любую директорию, например, `/tmp/new`.

### Ответ: 
Командой `mount /dev/vg0/lv0 /tmp/new_mount` логический том был смонтирован в директорию /tmp/new
![изображение](https://user-images.githubusercontent.com/123881243/230972706-c2a69087-9190-4781-b74d-6c35fa727227.png)

13. Поместите туда тестовый файл, например, `wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz`.

### Ответ: 
![изображение](https://user-images.githubusercontent.com/123881243/230973457-a8b1ec64-0d94-4797-8c33-5398861a118a.png)

14. Прикрепите вывод `lsblk`.

### Ответ:
![изображение](https://user-images.githubusercontent.com/123881243/230973540-94c38e8a-88f5-4258-b69d-d7f8569a5021.png)

15. Протестируйте целостность файла:

    ```bash
    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
    ```

### Ответ:
![изображение](https://user-images.githubusercontent.com/123881243/230973859-f5050620-c95a-4792-9631-16aacd52d52c.png)

16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.

### Ответ:
Командой `pvmove /dev/md1 /dev/md0` было перемещено содержимое с md1 на md0
![изображение](https://user-images.githubusercontent.com/123881243/230974839-04275cf3-0c53-43e2-96df-c0662b5fc500.png)

17. Сделайте `--fail` на устройство в вашем RAID1 md.

### Ответ:
Командой `mdadm /dev/md0 -f /dev/sdc1` в RAID1 было помечено сбойным устройство sdc1.
![изображение](https://user-images.githubusercontent.com/123881243/230976986-257471bc-ed35-424d-a6cd-6c3602435c06.png)

18. Подтвердите выводом `dmesg`, что RAID1 работает в деградированном состоянии.

### Ответ:
![изображение](https://user-images.githubusercontent.com/123881243/230978203-7a22dc50-1903-4fb4-b57f-bd9e832c6f11.png)

19. Протестируйте целостность файла — он должен быть доступен несмотря на «сбойный» диск:

    ```bash
    root@vagrant:~# gzip -t /tmp/new/test.gz
    root@vagrant:~# echo $?
    0
    ```

### Ответ:
Целостность файла подтверждена.
![изображение](https://user-images.githubusercontent.com/123881243/230978470-8c46cd11-76ac-42f5-8c18-6b06ef8d4bb1.png)

20. Погасите тестовый хост — `vagrant destroy`.
 
### Ответ:
![изображение](https://user-images.githubusercontent.com/123881243/230979035-27fed5e8-a10c-4cd5-86fd-a111dbc6e4c4.png)


