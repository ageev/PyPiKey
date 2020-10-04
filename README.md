# PyPiKey
Основная задача Пупикея - эмулировать клавиатуру и мышь на Raspberry Pi ZeroW, шевилить мышкой и что-нибудь печатать чтобы ноутбук не заснул и любой софт, отслеживающий работу ПК, видел как вы плодотворно работаете. К сожалению, любая софтварная имплементация подобного ни к чему хорошему не приведет, а Пи0 стоит копейки. 

## Подготовка
Вначале необходимо кое-что изменить в Pi0, чтобы плата стала определяться как USB клавиатура и мышь. 

```bash
pi@raspberrypi:~ $ echo "dtoverlay=dwc2" | sudo tee -a /boot/config.txt
pi@raspberrypi:~ $ echo "dwc2" | sudo tee -a /etc/modules
pi@raspberrypi:~ $ sudo echo "libcomposite" | sudo tee -a /etc/modules
pi@raspberrypi:~ $ sudo touch /usr/bin/pypikey_usb
pi@raspberrypi:~ $ sudo chmod +x /usr/bin/pypikey_usb
```

## автозапуск
```
pi@raspberrypi:~ $ sudo nano /etc/rc.local
```
добавляем *над* exit 0
```
/usr/bin/pypikey_usb # libcomposite configuration
```

## cоздаем гаджет
```
sudo nano /usr/bin/pypikey_usb
```

## шевелим мышкой

## печатаем клавиатурой
