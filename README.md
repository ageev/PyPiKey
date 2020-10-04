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
```
#!/bin/bash
cd /sys/kernel/config/usb_gadget/
mkdir -p pypikey
cd pypikey
echo 0x1d6b > idVendor # Linux Foundation
echo 0x0104 > idProduct # Multifunction Composite Gadget
echo 0x0100 > bcdDevice # v1.0.0
echo 0x0200 > bcdUSB # USB2
mkdir -p strings/0x409
echo "0123456789" > strings/0x409/serialnumber
echo "Artyom" > strings/0x409/manufacturer
echo "PyPiKey USB Device" > strings/0x409/product
mkdir -p configs/c.1/strings/0x409
echo "Config 1: ECM network" > configs/c.1/strings/0x409/configuration
echo 250 > configs/c.1/MaxPower

# keyboard
REPORT_DESC="\
\\x05\\x01\\x09\\x06\\xa1\\x01\\x05\\x07\\x19\\xe0\\x29\\xe7\\x15\\x00\\x25\\x01\
\\x75\\x01\\x95\\x08\\x81\\x02\\x95\\x01\\x75\\x08\\x81\\x03\\x95\\x05\\x75\\x01\
\\x05\\x08\\x19\\x01\\x29\\x05\\x91\\x02\\x95\\x01\\x75\\x03\\x91\\x03\\x95\\x06\
\\x75\\x08\\x15\\x00\\x25\\x65\\x05\\x07\\x19\\x00\\x29\\x65\\x81\\x00\\xc0"

mkdir -p functions/hid.usb0
echo 1 > functions/hid.usb0/protocol
echo 1 > functions/hid.usb0/subclass
echo 8 > functions/hid.usb0/report_length
echo -ne ${REPORT_DESC} > functions/hid.usb0/report_desc
ln -s functions/hid.usb0 configs/c.1/
# End keyboard

# mouse
MOUSE_COMBINED_DESC="\
\\x05\\x01\\x09\\x02\\xa1\\x01\\x09\\x01\\xa1\\x00\\x85\\x01\\x05\\x09\\x19\\x01\
\\x29\\x03\\x15\\x00\\x25\\x01\\x95\\x03\\x75\\x01\\x81\\x02\\x95\\x01\\x75\\x05\
\\x81\\x03\\x05\\x01\\x09\\x30\\x09\\x31\\x15\\x81\\x25\\x7f\\x75\\x08\\x95\\x02\
\\x81\\x06\\x95\\x02\\x75\\x08\\x81\\x01\\xc0\\xc0\\x05\\x01\\x09\\x02\\xa1\\x01\
\\x09\\x01\\xa1\\x00\\x85\\x02\\x05\\x09\\x19\\x01\\x29\\x03\\x15\\x00\\x25\\x01\
\\x95\\x03\\x75\\x01\\x81\\x02\\x95\\x01\\x75\\x05\\x81\\x01\\x05\\x01\\x09\\x30\
\\x09\\x31\\x15\\x00\\x26\\xff\\x7f\\x95\\x02\\x75\\x10\\x81\\x02\\xc0\\xc0"

mkdir -p functions/hid.usb1
echo 2 > functions/hid.usb1/protocol
echo 1 > functions/hid.usb1/subclass
echo 6 > functions/hid.usb1/report_length
echo -ne ${MOUSE_COMBINED_DESC} > functions/hid.usb1/report_desc
ln -s functions/hid.usb1 configs/c.1/
# End mouse

ls /sys/class/udc > UDC
```



## шевелим мышкой
```python
with open('/dev/hidg1', 'rb+') as hidg1:
     hidg1.write(b'\x01\x00\xff\x00\x00\x00') #move 1 pixel right
     hidg1.write(b'\x01\x00\x01\x00\x00\x00') #move 1 pixel left
```
## печатаем клавиатурой

## что ещё посмотреть
https://github.com/RoganDawes/P4wnP1_aloa - из pi0w можно сделать еще и сетевую карту, чтобы давать команды напрямую. 
