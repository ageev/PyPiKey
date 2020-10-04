#!/usr/bin/env python3

def shake_mouse():
        with open('/dev/hidg1', 'rb+') as hidg1:
                hidg1.write(b'\x01\x00\xff\x00\x00\x00') #move 1 pixel right
                hidg1.write(b'\x01\x00\x01\x00\x00\x00') #move 1 pixel left

shake_mouse()
