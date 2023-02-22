# Домашнее задание к занятию "Работа в терминале. Лекция 1"

### Цель задания

В результате выполнения этого задания вы:
1. Научитесь работать с базовым функционалом инструмента VirtualBox, который помогает с быстрой разверткой виртуальных машин.
2. Научитесь работать с документацией в формате man, чтобы ориентироваться в этом полезном и мощном инструменте документации.
3. Ознакомитесь с функциями Bash (PATH, HISTORY, batch/at), которые помогут комфортно работать с оболочкой командной строки (шеллом) и понять некоторые его ограничения.


### Инструкция к заданию

1. Установите средство виртуализации [Oracle VirtualBox](https://www.virtualbox.org/).
Установка VB на ПК:
![VB_Install-1](https://user-images.githubusercontent.com/123881243/219954954-30ec19d3-4679-4d88-b5cd-63ef4f047464.png)
![VB_Install-2](https://user-images.githubusercontent.com/123881243/219955022-16aa8fe1-4836-43ac-8a5a-bc7baf7ba65c.png)
![VB_Install-3](https://user-images.githubusercontent.com/123881243/219955030-2ace8c2e-58ae-477f-b6c5-c2e9c9081101.png)
![VB_Install-4](https://user-images.githubusercontent.com/123881243/219955037-ce4c9aae-bc41-41e0-af6a-179354650ecf.png)
![VB_Install-4_1](https://user-images.githubusercontent.com/123881243/219955042-eb746d1d-b9cc-4e57-88b0-4e23f0f05f5b.png)

1. Установите средство автоматизации [Hashicorp Vagrant](https://hashicorp-releases.yandexcloud.net/vagrant/).
Установка Vagrant на ПК:
![Vagrant_1](https://user-images.githubusercontent.com/123881243/219955764-f66352a9-e455-4133-a349-0f35e4cef4ab.png)
![Vagrant_2](https://user-images.githubusercontent.com/123881243/219955765-cb507f1f-0e57-4920-97fb-021185fcf0f0.png)
![Vagrant_3](https://user-images.githubusercontent.com/123881243/219955769-9c70dcb8-fb26-47c1-a597-1adf7e168bd4.png)

1. В вашем основном окружении подготовьте удобный для дальнейшей работы терминал. Можно предложить:

	* iTerm2 в Mac OS X
	* Windows Terminal в Windows
	* выбрать цветовую схему, размер окна, шрифтов и т.д.
	* почитать о кастомизации PS1/применить при желании.
Кастомизация терминала:
![Termina_settings](https://user-images.githubusercontent.com/123881243/220042190-a288eb48-9245-47c8-819b-8dcfa2f91510.png)

	Несколько популярных проблем:
	* Добавьте Vagrant в правила исключения перехватывающих трафик для анализа антивирусов, таких как Kaspersky, если у вас возникают связанные с SSL/TLS ошибки,
	* MobaXterm может конфликтовать с Vagrant в Windows,
	* Vagrant плохо работает с директориями с кириллицей (может быть вашей домашней директорией), тогда можно либо изменить [VAGRANT_HOME](https://www.vagrantup.com/docs/other/environmental-variables#vagrant_home), либо создать в системе профиль пользователя с английским именем,
	* VirtualBox конфликтует с Windows Hyper-V и его необходимо [отключить](https://www.vagrantup.com/docs/installation#windows-virtualbox-and-hyper-v):выключаем Hiper-V из PowerShell
	![Disable_Hyper-V](https://user-images.githubusercontent.com/123881243/220050047-f4960302-2fd1-4c35-8b62-b5ad15089d10.png)

	* [WSL2](https://docs.microsoft.com/ru-ru/windows/wsl/wsl2-faq#does-wsl-2-use-hyper-v-will-it-be-available-on-windows-10-home) использует Hyper-V, поэтому с ним VirtualBox также несовместим,
	* аппаратная виртуализация (Intel VT-x, AMD-V) должна быть активна в BIOS,
	* в Linux при установке [VirtualBox](https://www.virtualbox.org/wiki/Linux_Downloads) может дополнительно потребоваться пакет `linux-headers-generic` (debian-based) / `kernel-devel` (rhel-based).


### Инструменты/ дополнительные материалы, которые пригодятся для выполнения задания

1. [Конфигурация VirtualBox через Vagrant](https://www.vagrantup.com/docs/providers/virtualbox/configuration.html)
2. [Использование условий в Bash](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)

------

## Задание

1. С помощью базового файла конфигурации запустите Ubuntu 20.04 в VirtualBox посредством Vagrant:

	* Создайте директорию, в которой будут храниться конфигурационные файлы Vagrant. В ней выполните `vagrant init`. Замените содержимое Vagrantfile по умолчанию следующим:

		```bash
		Vagrant.configure("2") do |config|
			config.vm.box = "bento/ubuntu-20.04"
		end
		```
### Ответ:
Инициализация и мзменение конфигурационного файла:
![Init_Vag_create_def_conf](https://user-images.githubusercontent.com/123881243/220052899-c1c8e5e1-a3c8-40bc-824d-ee203dfa7b3d.png)
* Выполнение в этой директории `vagrant up` установит провайдер VirtualBox для Vagrant, скачает необходимый образ и запустит виртуальную машину.
### Ответ:
Разворачиваем VM с помощью Vagrant в дирректории C:\Users\00640\Documents\Vagrant>:
![Vagrant_install_Ubuntu](https://user-images.githubusercontent.com/123881243/220088773-95d075ea-6bc0-4e07-8fa4-ca492634e64d.png)

* `vagrant suspend` выключит виртуальную машину с сохранением ее состояния (т.е., при следующем `vagrant up` будут запущены все процессы внутри, которые работали на момент вызова suspend), `vagrant halt` выключит виртуальную машину штатным образом.

* Выполним команды:
	
	 `vagrant suspend`:	
![vagrant suspend](https://user-images.githubusercontent.com/123881243/220089888-f8ed9150-85a4-4771-9359-fc66935008cc.png)
	 Из suspend делаем shutdown VM командой `vagrant halt`:
![vagrant halt](https://user-images.githubusercontent.com/123881243/220091329-db68b212-64db-47e1-8473-0054f1fbbabb.png)

2. Ознакомьтесь с графическим интерфейсом VirtualBox, посмотрите как выглядит виртуальная машина, которую создал для вас Vagrant, какие аппаратные ресурсы ей выделены. Какие ресурсы выделены по-умолчанию?
### Ответ:
Рабочая VM Ubuntu 20.04 после зупуска:

![_Ubuntu_](https://user-images.githubusercontent.com/123881243/220095788-802b37a3-3304-4c34-b6d2-8ccf67dc831f.png)

Выделенные ресурсы по умолчанию VM:
* 1024MB RAM
* 2 CPU
* 4MB Graphics 
* 64GB VMDK(Virtual Machine Disk)
![systems_VM](https://user-images.githubusercontent.com/123881243/220100146-c46e101d-d789-4a56-8584-69b220956034.png)

3. Ознакомьтесь с возможностями конфигурации VirtualBox через Vagrantfile: [документация](https://www.vagrantup.com/docs/providers/virtualbox/configuration.html). Как добавить оперативной памяти или ресурсов процессора виртуальной машине?
### Ответ:
Команда `config.vm.provider` полностью блок с директивами выглядит так:
Увеличим дефолтное значение VM RAM с 1024 до 2048MB и поднимим количество CPU до 4-х, и заодно изменим имя на более читабельное:
```
 Vagrant.configure("2") do |config|
 	config.vm.box = "bento/ubuntu-20.04"
	config.vm.provider "virtualbox" do |v|
		v.name = "Netology"
		v.memory = 2048
  		v.cpus = 4
	end
end
```

4. Команда `vagrant ssh` из директории, в которой содержится Vagrantfile, позволит вам оказаться внутри виртуальной машины без каких-либо дополнительных настроек. Попрактикуйтесь в выполнении обсуждаемых команд в терминале Ubuntu.
### Ответ:
	* Подключимся по SSH к нашей VM:
	
![connect_to_ssh_VM](https://user-images.githubusercontent.com/123881243/220113951-6f5bcae3-be8b-4a19-ad2a-62357b1a48a6.png)

5. Ознакомьтесь с разделами `man bash`, почитайте о настройках самого bash:
    * какой переменной можно задать длину журнала `history`, и на какой строчке manual это описывается?
     Переменная с помощью которой задается длина журнала $HISTSIZE.
     Располагается блок описывающий в man с сроки 3064 по 3100
     ![HISTSIZE](https://user-images.githubusercontent.com/123881243/220125527-5441d60f-098d-4544-8860-9840280d98d0.png)

    * что делает директива `ignoreboth` в bash?
### Ответ:
Значение ignoreboth является сокращением для ignorespace и ignoredups. Т.е его использование приведёт к использованию сразу двух значений:

`ignorespace`, строки, начинающиеся с пробела, не сохраняются.
`erasedups` приводит к тому, что все предыдущие строки, совпадающие с текущей строкой, удаляются из списка истории до того, как эта строка будет сохранена

![HISTCONTROL](https://user-images.githubusercontent.com/123881243/220141274-ddbdef1d-dff8-4a2a-a5f5-3885867e9e35.png)

6. В каких сценариях использования применимы скобки `{}` и на какой строчке `man bash` это описано?
### Ответ:
BASH(1) 1-ая страница man
Между {} указывется список элементов.Пример на команде touch создать файлы с целочисленным значением от 1 до 10
touch{1..10}
Также {} нужны чтобы обратиться к элементу массива, описание на 701 строке.

7. С учётом ответа на предыдущий вопрос, как создать однократным вызовом `touch` 100000 файлов? Получится ли аналогичным образом создать 300000? Если нет, то почему?
### Ответ:
Создать однократным вызовом можно с помощью команды:
* `touch {1..100000}` 
* Аргумент не может в себя принимать такое значение. Имеется ограничение на создание файлов однократным вызовом.
```
vagrant@vagrant:~$ touch {1..300000}
-bash: /usr/bin/touch: Argument list too long
```


8. В man bash поищите по `/\[\[`. Что делает конструкция `[[ -d /tmp ]]`
### Ответ:
```
1343 строка man на 1 странице.
Расширенный вариант команды test, в приведённом примере проверяется, что true если /tmp == catalog
```
9. Сделайте так, чтобы в выводе команды `type -a bash` первым стояла запись с нестандартным путем, например bash is ... 
Используйте знания о просмотре существующих и создании новых переменных окружения, обратите внимание на переменную окружения PATH 

	```bash
	bash is /tmp/new_path_directory/bash
	bash is /usr/local/bin/bash
	bash is /bin/bash
	```

	(прочие строки могут отличаться содержимым и порядком)
    В качестве ответа приведите команды, которые позволили вам добиться указанного вывода или соответствующие скриншоты.
![bash](https://user-images.githubusercontent.com/123881243/220572677-daf053f2-050e-42c2-a3e6-42567ed3b13e.png)
Для начала посмотрим всt все места где встречается исполняемый файл bash по системе:
* `type -a bash `
После скопируем его бинарник в новую директорию:
* `cp /bin/bash /home`
Пропишем путь в PATH:
* `PATH=/home:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin`
 
 Убедимся в том, что результат по заданию был достигнут:
* `type -a bash`
10. Чем отличается планирование команд с помощью `batch` и `at`?
### Ответ:
* `batch` Выполняет команды позднее, когда позволяет уровень загрузки системы. 
* `at` Выполняет команду в определенное время
![Batch_and_at](https://user-images.githubusercontent.com/123881243/220455554-82dc3f6c-724d-42ab-9ffc-f360486514cb.png)

11. Завершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.

*В качестве решения дайте ответы на вопросы свободной форме* 

---

### Правила приема домашнего задания

- В личном кабинете отправлена ссылка на .md файл в вашем репозитории.

### Критерии оценки

Зачет - выполнены все задания, ответы даны в развернутой форме, приложены соответствующие скриншоты и файлы проекта, в выполненных заданиях нет противоречий и нарушения логики.

На доработку - задание выполнено частично или не выполнено, в логике выполнения заданий есть противоречия, существенные недостатки. 
