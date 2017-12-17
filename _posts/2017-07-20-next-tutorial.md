---
title: RaspMusic 
description: Raspmusic은 소형점포주에게 부담되지 않으며 점포 내 음악을 틀어놓을 수 있도록 설계된 MVP제품입니다.


- With Raspmusic, Be Happy.
---

> Raspmusic과 함께한다면, 당신의 가게는 항상 손님은 물론 행복과 활기가 가득찰 겁니다.

<!-- more -->

[Live Preview](http://simpleyyt.github.io/jekyll-theme-next/)
## Brand
* Brand Image
![Desktop Preview](https://postfiles.pstatic.net/MjAxNzEyMThfMTk3/MDAxNTEzNTI2MDk4NDg1.b6VJYFCJDoLTlFqBtGTNOi3CjpI7s-zYPiUntONhhyQg.QaUTrEtf963QaxynZtGQyU-5XxZynylZVjcmEKfYi5Qg.PNG.funclan0924/logos.png?type=w966)

* Device Image
![Desktop Preview](https://postfiles.pstatic.net/MjAxNzEyMThfNDkg/MDAxNTEzNTI2MTY5OTI3.V56l9rT3FRnHcZQKt6vr_YFEpDH1CV6C9pGGDl1C6V0g.CTqejVGbaI_KOF_9T-Um6_bLtX5SDnCk9ArE7PK7TQQg.PNG.funclan0924/design.png?type=w966)

## Design Process

* 1 Drawing
![Desktop Preview](https://postfiles.pstatic.net/MjAxNzEyMTdfOTAg/MDAxNTEzNTIxNzA3ODU3.N7a5X4hhKMP0L6tWmI2SPwwqiD3BAJRhCAF7-ZKQsO4g.3nLSkA5jTQU0XMhrNtSPxwl9hJ97MFJAPV-wrcG5Zdkg.JPEG.funclan0924/1.jpeg?type=w966)

* 2, 3D Modeling in Rhino

![Desktop Sidebar Preview](https://postfiles.pstatic.net/MjAxNzEyMTdfMjM3/MDAxNTEzNTIxNzA4MjQ4.0xrOhGY10X6pmFy0Dc5jHACXk1XSGNW26qmgTz1wKbUg.fX6A5m5lmkwGBgrGK2nzAvSR5NngxnTWSeRftT9fw8sg.JPEG.funclan0924/rhino.jpeg?type=w966)

* 3, 3D Printing	

![Desktop Sidebar Preview](https://postfiles.pstatic.net/MjAxNzEyMTdfMTY3/MDAxNTEzNTIxNzA4MTUw.MR34u48uLlFVLvae3c4buQrGHpc7AtmXBBfA1foZFBYg.alOSLjOJ5vMaVHY2uJP3D8z0AdbcB-5Lkx1q7uh54Gog.JPEG.funclan0924/print.jpeg?type=w966)


* 4, Coloring

![Mobile Preview](https://postfiles.pstatic.net/MjAxNzEyMTdfMjE2/MDAxNTEzNTIxNzExMDE0.iZnBL7_UfGCfI1--LXGi6E5muApuH0EvKc5-z_nmVJkg.-7_mE8_jvB2dK84lXrrdZ7YDV498oTy1szVsz0Bpqucg.JPEG.funclan0924/color.jpeg?type=w966)


## Code

#!/usr/bin/env python

	import smbus	
	import time	
	import RPi.GPIO as GPIO
	import vlc
	import os
	import ftplib
	import random


	#!/usr/bin/env python
	# Define some device parameters
	I2C_ADDR = 0x27  # I2C device address
	LCD_WIDTH = 16  # Maximum characters per line

	# Define some device constants
	LCD_CHR = 1  # Mode - Sending data
	LCD_CMD = 0  # Mode - Sending command

	LCD_LINE_1 = 0x80  # LCD RAM address for the 1st line
	LCD_LINE_2 = 0xC0  # LCD RAM address for the 2nd line
	LCD_LINE_3 = 0x94  # LCD RAM address for the 3rd line
	LCD_LINE_4 = 0xD4  # LCD RAM address for the 4th line

	LCD_BACKLIGHT = 0x08  # On


	ENABLE = 0b00000100  # Enable bit

	# Timing constants
	E_PULSE = 0.0005
	E_DELAY = 0.0005

	# Open I2C interface
	# bus = smbus.SMBus(0)  # Rev 1 Pi uses 0
	bus = smbus.SMBus(1)  # Rev 2 Pi uses 1

	print("display done")

	GPIO.setmode(GPIO.BCM)

	GPIO.setup(18, GPIO.IN)	
	GPIO.setup(23, GPIO.IN)
	GPIO.setup(24, GPIO.IN)
	GPIO.setup(25, GPIO.IN)
	GPIO.setup(12, GPIO.IN)

	global minsec
	minsec=""
	global s
	global m

	def lcd_init():
    # Initialise display
    lcd_byte(0x33, LCD_CMD)  # 110011 Initialise
    lcd_byte(0x32, LCD_CMD)  # 110010 Initialise
    lcd_byte(0x06, LCD_CMD)  # 000110 Cursor move direction
    lcd_byte(0x0C, LCD_CMD)  # 001100 Display On,Cursor Off, Blink Off
    lcd_byte(0x28, LCD_CMD)  # 101000 Data length, number of lines, font size
    lcd_byte(0x01, LCD_CMD)  # 000001 Clear display
    time.sleep(E_DELAY)


	def lcd_byte(bits, mode):
    bits_high = mode | (bits & 0xF0) | LCD_BACKLIGHT
    bits_low = mode | ((bits << 4) & 0xF0) | LCD_BACKLIGHT

    # High bits
    bus.write_byte(I2C_ADDR, bits_high)
    lcd_toggle_enable(bits_high)

    # Low bits
    bus.write_byte(I2C_ADDR, bits_low)
    lcd_toggle_enable(bits_low)


	def lcd_toggle_enable(bits):
    # Toggle enable
    time.sleep(E_DELAY)
    bus.write_byte(I2C_ADDR, (bits | ENABLE))
    time.sleep(E_PULSE)
    bus.write_byte(I2C_ADDR, (bits & ~ENABLE))
    time.sleep(E_DELAY)


	def lcd_string(message, line):
    # Send string to display

    message = message.ljust(LCD_WIDTH, " ")

    lcd_byte(line, LCD_CMD)

    for i in range(LCD_WIDTH):
        lcd_byte(ord(message[i]), LCD_CHR)

	def main():
    lcd_init()
    so = 0
    i = 0
    b = 0
    ranso_index = 0
    s = 0
    m=0
    minsec = ""
    asd = 0

    lcd_string("  STARTING ......  "      , LCD_LINE_1)
    lcd_string("   PUSH >>   ", LCD_LINE_2)
    file = "/home/pi/Desktop/startsong.mp3"
    instance = vlc.Instance()

    player=instance.media_player_new()
    media=instance.media_new(file)

    player.set_media(media)

    player.play()
    time.sleep(3)
    ap = player.get_state()
    while True:
        if GPIO.input(23)==0:
            break
    
        if GPIO.input(18)==0:
            base = "/home/pi/Downloads/"
        
            channel_pop = "pop/"
            channel_piano = "piano/"
            channel_hip = "hip/"
            channel_edm = "edm/"

            list_channel = [channel_pop, channel_piano, channel_hip, channel_edm]


            pop1= "Yellow.mp3"
            pop2= "Tell_Me_You_Love_Me.mp3"
            pop3= "Alesso_Heroes.mp3"
            pop4= "Girls_On_Boys.mp3"
            pop5= "Ariana_Grande_Greedy.mp3"
            pop6 = "angels.mp3"

            list_pop=[pop1, pop2, pop3, pop4, pop5, pop6]


            piano1= "night.mp3"
            piano2= "Daily_Life-duggy.mp3"
            piano3= "Sky-Duggy.mp3"
            piano4= "sacrifice.mp3"
            piano5= "TIDO.mp3"

            list_piano=[piano1, piano2, piano3, piano4, piano5]

            hip1 = "beautiful.mp3"
            hip2 = "noback.mp3"
            hip3 = "stereo.mp3"
            hip4 = "timegap.mp3"
            hip5 = "watcheye.mp3"

            list_hip=[hip1, hip2, hip3, hip4, hip5]

            edm1 = "all_falls_down.mp3"
            edm2 = "bedroom.mp3"
            edm3 = "slowly.mp3"
            edm4 = "space_jam.mp3"
            edm5 = "you_and_me.mp3"

            list_edm = [edm1, edm2, edm3, edm4, edm5]


            file ="/home/pi/Downloads/pop/Yellow.mp3"
            instance = vlc.Instance()

            #player=instance.media_player_new()
            media=instance.media_new(file)

            player.set_media(media)

            player.play()
            time.sleep(1)


            #i = 0

            path = base + list_channel[i]

            li_st = os.listdir(path)

            # Main program block
            # Initialise display
            lcd_init()
            so = 0
            #i = 0
            b = 0
            ranso_index = 0
            min_save = 0
            sec_save = 0
            ranso_index_save = 0
            i_save = 0
            so_save = 0
            
            while True:
                if GPIO.input(12)==0:
                    if b == 1:
                        print("Can't change. Exit random mode!")
                    else:
                        player.stop()
                        if i < len(list_channel)-1:
                            i+=1
                        else:
                            i = 0

                        path = base + list_channel[i]

                        if i == 0:
                            ranso_index = 0
                            so = 0
                            file = path + list_pop[so]
                            instance = vlc.Instance()

                            media=instance.media_new(file)

                            player.set_media(media)

                            player.play()
                            time.sleep(1)
                        elif i == 1:
                            so = 0
                            ranso_index = 0
                            file = path + list_piano[so]
                            instance = vlc.Instance()

                            media=instance.media_new(file)

                            player.set_media(media)

                            player.play()
                            time.sleep(1)
                        elif i == 2:
                            so = 0
                            ranso_index = 0
                            file = path + list_hip[so]
                            instance = vlc.Instance()

                            media=instance.media_new(file)

                            player.set_media(media)

                            player.play()
                            time.sleep(1)
                        elif i == 3:
                            so = 0
                            ranso_index = 0
                            file = path + list_edm[so]
                            instance = vlc.Instance()

                            media=instance.media_new(file)

                            player.set_media(media)

                            player.play()
                            time.sleep(1)

                        li_st = os.listdir(path)
                        time.sleep(0.7)
                    #get in main()

                if GPIO.input(18) == 0:
                    player.pause()
                    a = player.get_state()

                    time.sleep(0.7)

                if GPIO.input(23) == 0:
                    a = player.get_state()
                    if a == 4:
                        player.stop()
                        lcd_string("     Exiting......    ", LCD_LINE_1)
                        lcd_string("      Bye-!!       ", LCD_LINE_2)
                        file = "/home/pi/Desktop/endsong.mp3"
                        instance = vlc.Instance()

                        media=instance.media_new(file)

                        player.set_media(media)

                        player.play()
                        time.sleep(3)
                        break
                    else:
                        if b == 1:
                            b = 0
                            print("Exit random song")
                            time.sleep(0.7)
                        else:
                            b = 1
                            print("Start random song")
                            time.sleep(0.7)

                if GPIO.input(25)==0:
                    player.stop()
                    so = so- 1
                    time.sleep(0.7)
                    if i == 0:
                        if so<0:
                            so = len(list_pop)-1
                        some= base+list_channel[i]+list_pop[so]
                        if b == 1:
                            ranso = random.choice(list_pop)
                            some = base+list_channel[i]+ranso
                            ranso_index = list_pop.index(ranso)
                    elif i == 1:
                        if so<0:
                            so = len(list_piano)-1
                        some = base+list_channel[i]+list_piano[so]
                        if b == 1:
                            ranso = random.choice(list_piano)
                            some = base+list_channel[i]+ranso
                            ranso_index = list_piano.index(ranso)
                    elif i == 2:
                        if so<0:
                            so = len(list_hip)-1
                        some = base+list_channel[i]+list_hip[so]
                        if b == 1:
                            ranso = random.choice(list_hip)
                            some = base+list_channel[i]+ranso
                            ranso_index = list_hip.index(ranso)
                    elif i == 3:
                        if so<0:
                            so = len(list_edm)-1
                        some = base+list_channel[i]+list_edm[so]
                        if b == 1:
                            ranso = random.choice(list_edm)
                            some = base+list_channel[i]+ranso
                            ranso_index = list_edm.index(ranso)
                    instance = vlc.Instance()

                    media=instance.media_new(some)

                    player.set_media(media)

                    player.play()
                    time.sleep(1)



                if GPIO.input(24)==0:
                    player.stop()
                    so = so+ 1
                    time.sleep(0.7)
                    if i == 0:
                        if so>len(list_pop)-1:
                            so = 0
                        some1= base+list_channel[i]+list_pop[so]
                        if b == 1:
                            ranso = random.choice(list_pop)
                            some1 = base+list_channel[i]+ranso
                            ranso_index = list_pop.index(ranso)
                    elif i == 1:
                        if so>len(list_piano)-1:
                            so = 0
                        some1 = base+list_channel[i]+list_piano[so]
                        if b == 1:
                            ranso = random.choice(list_piano)
                            some1 = base+list_channel[i]+ranso
                            ranso_index = list_piano.index(ranso)
                    elif i == 2:
                        if so>len(list_hip)-1:
                            so = 0
                        some1 = base+list_channel[i]+list_hip[so]
                        if b == 1:
                            ranso = random.choice(list_hip)
                            some1 = base+list_channel[i]+ranso
                            ranso_index = list_hip.index(ranso)
                    elif i == 3:
                        if so>len(list_edm)-1:
                            so = 0
                        some1 = base+list_channel[i]+list_edm[so]
                        if b == 1:
                            ranso = random.choice(list_edm)
                            some1 = base+list_channel[i]+ranso
                            ranso_index = list_edm.index(ranso)
                    instance = vlc.Instance()


                    media=instance.media_new(some1)

                    player.set_media(media)

                    player.play()
                    time.sleep(1)
                a = player.get_state()
                if a == 6:
                    so = so+ 1
                    time.sleep(0.7)
                    if i == 0:
                        if so>len(list_pop)-1:
                            so = 0
                        some6= base+list_channel[i]+list_pop[so]
                        if b == 1:
                            ranso = random.choice(list_pop)
                            some6 = base+list_channel[i]+ranso
                            ranso_index = list_pop.index(ranso)
                    elif i == 1:
                        if so>len(list_piano)-1:
                            so = 0
                        some6 = base+list_channel[i]+list_piano[so]
                        if b == 1:
                            ranso = random.choice(list_piano)
                            some6 = base+list_channel[i]+ranso
                            ranso_index = list_piano.index(ranso)
                    elif i == 2:
                        if so>len(list_hip)-1:
                            so = 0
                        some6 = base+list_channel[i]+list_hip[so]
                        if b == 1:
                            ranso = random.choice(list_hip)
                            some6 = base+list_channel[i]+ranso
                            ranso_index = list_hip.index(ranso)
                    elif i == 3:
                        if so>len(list_edm)-1:
                            so = 0
                        some6 = base+list_channel[i]+list_edm[so]
                        if b == 1:
                            ranso = random.choice(list_edm)
                            some6 = base+list_channel[i]+ranso
                            ranso_index = list_edm.index(ranso)
                    instance = vlc.Instance()


                    media=instance.media_new(some6)

                    player.set_media(media)

                    player.play()
                    time.sleep(1)

                if GPIO.input(12)==0:
                    if b == 1:
                        print("Can't change. Exit random mode!")
                    else:
                        player.stop()
                        if i < len(list_channel)-1:
                            i+=1
                        else:
                            i = 0

                        path = base + list_channel[i]

                        if i == 0:
                            so = 0
                            ranso_index = 0
                            file = path + list_pop[so]
                            instance = vlc.Instance()

                            media=instance.media_new(file)

                            player.set_media(media)

                            player.play()
                            time.sleep(1)
                        elif i == 1:
                            so = 0
                            ranso_index = 0
                            file = path + list_piano[so]
                            instance = vlc.Instance()

                            media=instance.media_new(file)

                            player.set_media(media)

                            player.play()
                            time.sleep(1)
                        elif i == 2:
                            so = 0
                            ranso_index = 0
                            file = path + list_hip[so]
                            instance = vlc.Instance()

                            media=instance.media_new(file)

                            player.set_media(media)

                            player.play()
                            time.sleep(1)
                        elif i == 3:
                            so = 0
                            ranso_index = 0
                            file = path + list_edm[so]
                            instance = vlc.Instance()

                            media=instance.media_new(file)

                            player.set_media(media)

                            player.play()
                            time.sleep(1)

                        li_st = os.listdir(path)
                        time.sleep(0.7)
                        #get in main()

                a= player.get_state()
                if a==4:
                    if i == 0:
                        if b == 1:
                            lcd_string(      list_pop[ranso_index]      , LCD_LINE_1)
                            lcd_string("      Paused      ", LCD_LINE_2)
                        else:
                            lcd_string(      list_pop[so]      , LCD_LINE_1)
                            lcd_string("      Paused      ", LCD_LINE_2)
                    if i == 1:
                        if b == 1:
                            lcd_string(      list_piano[ranso_index]      , LCD_LINE_1)
                            lcd_string("      Paused      ", LCD_LINE_2)
                        else:
                            lcd_string(      list_piano[so]      , LCD_LINE_1)
                            lcd_string("      Paused      ", LCD_LINE_2)
                    if i == 2:
                        if b == 1:
                            lcd_string(      list_hip[ranso_index]      , LCD_LINE_1)
                            lcd_string("      Paused      ", LCD_LINE_2)
                        else:
                            lcd_string(      list_hip[so]      , LCD_LINE_1)
                            lcd_string("      Paused      ", LCD_LINE_2)
                    if i == 3:
                        if b == 1:
                            lcd_string(      list_edm[ranso_index]      , LCD_LINE_1)
                            lcd_string("      Paused      ", LCD_LINE_2)
                        else:
                            lcd_string(      list_edm[so]      , LCD_LINE_1)
                            lcd_string("      Paused      ", LCD_LINE_2)


                    if GPIO.input(25)==0:
                        player.stop()
                        so = so- 1
                        time.sleep(0.7)
                        if i == 0:
                            if so<0:
                                so = len(list_pop)-1
                            some2= base+list_channel[i]+list_pop[so]
                            if b == 1:
                                ranso = random.choice(list_pop)
                                some2 = base+list_channel[i]+ranso
                                ranso_index = list_pop.index(ranso)
                        elif i == 1:
                            if so<0:
                                so = len(list_piano)-1
                            some2 = base+list_channel[i]+list_piano[so]
                            if b == 1:
                                ranso = random.choice(list_piano)
                                some2 = base+list_channel[i]+ranso
                                ranso_index = list_piano.index(ranso)
                        elif i == 2:
                            if so<0:
                                so = len(list_hip)-1
                            some2 = base+list_channel[i]+list_hip[so]
                            if b == 1:
                                ranso = random.choice(list_hip)
                                some2 = base+list_channel[i]+ranso
                                ranso_index = list_hip.index(ranso)
                        elif i == 3:
                            if so<0:
                                so = len(list_edm)-1
                            some2 = base+list_channel[i]+list_edm[so]
                            if b == 1:
                                ranso = random.choice(list_edm)
                                some2 = base+list_channel[i]+ranso
                                ranso_index = list_edm.index(ranso)
                        instance = vlc.Instance()

                        media=instance.media_new(some2)

                        player.set_media(media)

                        player.play()
                        time.sleep(1)

                    if GPIO.input(24)==0:
                        player.stop()
                        so = so+ 1
                        time.sleep(0.7)
                        if i == 0:
                            if so>len(list_pop)-1:
                                so = 0
                            some3= base+list_channel[i]+list_pop[so]
                            if b == 1:
                                ranso = random.choice(list_pop)
                                some3 = base+list_channel[i]+ranso
                                ranso_index = list_pop.index(ranso)
                        elif i == 1:
                            if so>len(list_piano)-1:
                                so = 0
                            some3 = base+list_channel[i]+list_piano[so]
                            if b == 1:
                                ranso = random.choice(list_piano)
                                some3 = base+list_channel[i]+ranso
                                ranso_index = list_piano.index(ranso)
                        elif i == 2:
                            if so>len(list_hip)-1:
                                so = 0
                            some3 = base+list_channel[i]+list_hip[so]
                            if b == 1:
                                ranso = random.choice(list_hip)
                                some3 = base+list_channel[i]+ranso
                                ranso_index = list_hip.index(ranso)
                        elif i == 3:
                            if so>len(list_edm)-1:
                                so = 0
                            some3 = base+list_channel[i]+list_edm[so]
                            if b == 1:
                                ranso = random.choice(list_edm)
                                some3 = base+list_channel[i]+ranso
                                ranso_index = list_edm.index(ranso)
                        instance = vlc.Instance()


                        media=instance.media_new(some3)

                        player.set_media(media)

                        player.play()
                        time.sleep(1)

                    if GPIO.input(18) == 0:
                        player.pause()
                        time.sleep(0.7)
                        continue

                    a = player.get_state()
                    if a == 6:
                        so = so+ 1
                        time.sleep(0.7)
                        if i == 0:
                            if so>len(list_pop)-1:
                                so = 0
                            some6= base+list_channel[i]+list_pop[so]
                            if b == 1:
                                ranso = random.choice(list_pop)
                                some6 = base+list_channel[i]+ranso
                                ranso_index = list_pop.index(ranso)
                        elif i == 1:
                            if so>len(list_piano)-1:
                                so = 0
                            some6 = base+list_channel[i]+list_piano[so]
                            if b == 1:
                                ranso = random.choice(list_piano)
                                some6 = base+list_channel[i]+ranso
                                ranso_index = list_piano.index(ranso)
                        elif i == 2:
                            if so>len(list_hip)-1:
                                so = 0
                            some6 = base+list_channel[i]+list_hip[so]
                            if b == 1:
                                ranso = random.choice(list_hip)
                                some6 = base+list_channel[i]+ranso
                                ranso_index = list_hip.index(ranso)
                        elif i == 3:
                            if so>len(list_edm)-1:
                                so = 0
                            some6 = base+list_channel[i]+list_edm[so]
                            if b == 1:
                                ranso = random.choice(list_edm)
                                some6 = base+list_channel[i]+ranso
                                ranso_index = list_edm.index(ranso)

                        instance = vlc.Instance()


                        media=instance.media_new(some6)

                        player.set_media(media)

                        player.play()
                        time.sleep(1)

                    if GPIO.input(12)==0:
                        if b == 1:
                            print("Can't change. Exit random mode!")
                        else:
                            player.stop()
                            if i < len(list_channel)-1:
                                i+=1
                            else:
                                i = 0

                            path = base + list_channel[i]

                            if i == 0:
                                so = 0
                                ranso_index = 0
                                file = path + list_pop[so]
                                instance = vlc.Instance()

                                media=instance.media_new(file)

                                player.set_media(media)

                                player.play()
                                time.sleep(1)
                            elif i == 1:
                                so = 0
                                ranso_index = 0
                                file = path + list_piano[so]
                                instance = vlc.Instance()

                                media=instance.media_new(file)

                                player.set_media(media)

                                player.play()
                                time.sleep(1)
                            elif i == 2:
                                so = 0
                                ranso_index = 0
                                file = path + list_hip[so]
                                instance = vlc.Instance()

                                media=instance.media_new(file)

                                player.set_media(media)

                                player.play()
                                time.sleep(1)
                            elif i == 3:
                                so = 0
                                ranso_index = 0
                                file = path + list_edm[so]
                                instance = vlc.Instance()

                                media=instance.media_new(file)

                                player.set_media(media)

                                player.play()
                                time.sleep(1)

                            li_st = os.listdir(path)
                            time.sleep(0.7)
                        #get in main()





                else:
                    # Send some test
                    s=0
                    m=0

                    while True:
                        if ((so == so_save) and (i == i_save) and (ranso_index == ranso_index_save) and (asd == 1)):
                            m = min_save
                            s = sec_save
                            asd = 0
                        if (GPIO.input(24)==0) or (GPIO.input(25)==0) or (GPIO.input(12)==0):
                            break
                        time.sleep(0.85)
                        s=s+1
                        if s==60:
                            s=0
                            m=m+1
                        min = str(m)
                        sec = str(s)
                        minsec = min+':'+sec
                        #print(minsec)
                        if (GPIO.input(24)==0) or (GPIO.input(25)==0) or (GPIO.input(12)==0):
                            #time.sleep(0.5)
                            break
                        if GPIO.input(23)==0:
                            if b == 0:
                                print("Start random song")
                                b = 1
                            else:
                                print("Exit random song")
                                b = 0
                            time.sleep(0.5)
                        if GPIO.input(18)==0:
                            player.pause()
                            time.sleep(0.5)
                            min_save = m
                            sec_save = s
                            ranso_index_save = ranso_index
                            i_save = i
                            so_save = so
                            asd = 1
                            break



                        if i == 0:
                            if b == 1:
                                lcd_string(      list_pop[ranso_index]      , LCD_LINE_1)
                                lcd_string(list_channel[i]+"RANDOM"+" "+minsec , LCD_LINE_2)
                            else:
                                lcd_string(      list_pop[so]      , LCD_LINE_1)
                                lcd_string(list_channel[i]+minsec  , LCD_LINE_2)
                        if (GPIO.input(24)==0) or (GPIO.input(25)==0) or (GPIO.input(12)==0):
                            #time.sleep(0.5)
                            break
                        if i == 1:
                            if b == 1:
                                lcd_string(      list_piano[ranso_index]      , LCD_LINE_1)
                                lcd_string(list_channel[i]+"RANDOM"+minsec , LCD_LINE_2)
                            else:
                                lcd_string(      list_piano[so]      , LCD_LINE_1)
                                lcd_string(list_channel[i]+minsec  , LCD_LINE_2)
                        if (GPIO.input(24)==0) or (GPIO.input(25)==0) or (GPIO.input(12)==0):
                            #time.sleep(0.5)
                            break
                        if i == 2:
                            if b == 1:
                                lcd_string(      list_hip[ranso_index]      , LCD_LINE_1)
                                lcd_string(list_channel[i]+"RANDOM"+" "+minsec , LCD_LINE_2)
                            else:
                                lcd_string(      list_hip[so]      , LCD_LINE_1)
                                lcd_string(list_channel[i]+minsec  , LCD_LINE_2)
                        if (GPIO.input(24)==0) or (GPIO.input(25)==0) or (GPIO.input(12)==0):

                            #time.sleep(0.5)
                            break
                        if i == 3:
                            if b == 1:
                                lcd_string(      list_edm[ranso_index]      , LCD_LINE_1)
                                lcd_string(list_channel[i]+"RANDOM"+" "+minsec , LCD_LINE_2)
                            else:
                                lcd_string(      list_edm[so]      , LCD_LINE_1)
                                lcd_string(list_channel[i]+minsec  , LCD_LINE_2)
                        if (GPIO.input(24)==0) or (GPIO.input(25)==0) or (GPIO.input(12)==0):
                            #time.sleep(0.5)
                            break
                        add = player.get_state()
                        if add == 6:
                            break
                        

                    if GPIO.input(25)==0:
                        player.stop()
                        so = so- 1
                        time.sleep(0.7)
                        if i == 0:
                            if so<0:
                                so = len(list_pop)-1
                            some4= base+list_channel[i]+list_pop[so]
                            if b == 1:
                                ranso = random.choice(list_pop)
                                some4 = base+list_channel[i]+ranso
                                ranso_index = list_pop.index(ranso)
                        elif i == 1:
                            if so<0:
                                so = len(list_piano)-1
                            some4 = base+list_channel[i]+list_piano[so]
                            if b == 1:
                                ranso = random.choice(list_piano)
                                some4 = base+list_channel[i]+ranso
                                ranso_index = list_piano.index(ranso)
                        elif i == 2:
                            if so<0:
                                so = len(list_hip)-1
                            some4 = base+list_channel[i]+list_hip[so]
                            if b == 1:
                                ranso = random.choice(list_hip)
                                some4 = base+list_channel[i]+ranso
                                ranso_index = list_hip.index(ranso)
                        elif i == 3:
                            if so<0:
                                so = len(list_edm)-1
                            some4 = base+list_channel[i]+list_edm[so]
                            if b == 1:
                                ranso = random.choice(list_edm)
                                some4 = base+list_channel[i]+ranso
                                ranso_index = list_edm.index(ranso)
                        instance = vlc.Instance()

                        media=instance.media_new(some4)

                        player.set_media(media)

                        player.play()
                        time.sleep(1)

                    if GPIO.input(24)==0:
                        player.stop()
                        so = so+ 1
                        time.sleep(0.7)
                        if i == 0:
                            if so>len(list_pop)-1:
                                so = 0
                            some5= base+list_channel[i]+list_pop[so]
                            if b == 1:
                                ranso = random.choice(list_pop)
                                some5 = base+list_channel[i]+ranso
                                ranso_index = list_pop.index(ranso)
                        elif i == 1:
                            if so>len(list_piano)-1:
                                so = 0
                            some5 = base+list_channel[i]+list_piano[so]
                            if b == 1:
                                ranso = random.choice(list_piano)
                                some5 = base+list_channel[i]+ranso
                                ranso_index = list_piano.index(ranso)
                        elif i == 2:
                            if so>len(list_hip)-1:
                                so = 0
                            some5 = base+list_channel[i]+list_hip[so]
                            if b == 1:
                                ranso = random.choice(list_hip)
                                some5 = base+list_channel[i]+ranso
                                ranso_index = list_hip.index(ranso)
                        elif i == 3:
                            if so>len(list_edm)-1:
                                so = 0
                            some5 = base+list_channel[i]+list_edm[so]
                            if b == 1:
                                ranso = random.choice(list_edm)
                                some5 = base+list_channel[i]+ranso
                                ranso_index = list_edm.index(ranso)
                        instance = vlc.Instance()


                        media=instance.media_new(some5)

                        player.set_media(media)

                        player.play()
                        time.sleep(1)

                    if GPIO.input(18) == 0:
                        player.pause()
                        time.sleep(0.7)
                        continue

                    a = player.get_state()
                    if a == 6:
                        so = so+ 1
                        time.sleep(0.7)
                        if i == 0:
                            if so>len(list_pop)-1:
                                so = 0
                            some6= base+list_channel[i]+list_pop[so]
                            if b == 1:
                                ranso = random.choice(list_pop)
                                some6 = base+list_channel[i]+ranso
                                ranso_index = list_pop.index(ranso)
                        elif i == 1:
                            if so>len(list_piano)-1:
                                so = 0
                            some6 = base+list_channel[i]+list_piano[so]
                            if b == 1:
                                ranso = random.choice(list_piano)
                                some6 = base+list_channel[i]+ranso
                                ranso_index = list_piano.index(ranso)
                        elif i == 2:
                            if so>len(list_hip)-1:
                                so = 0
                            some6 = base+list_channel[i]+list_hip[so]
                            if b == 1:
                                ranso = random.choice(list_hip)
                                some6 = base+list_channel[i]+ranso
                                ranso_index = list_hip.index(ranso)
                        elif i == 3:
                            if so>len(list_edm)-1:
                                so = 0
                            some6 = base+list_channel[i]+list_edm[so]
                            if b == 1:
                                ranso = random.choice(list_edm)
                                some6 = base+list_channel[i]+ranso
                                ranso_index = list_edm.index(ranso)
                        instance = vlc.Instance()


                        media=instance.media_new(some6)

                        player.set_media(media)

                        player.play()
                        time.sleep(1)

                    if GPIO.input(12)==0:
                        if b == 1:
                            print("Can't change. Exit random mode!")
                        else:
                            player.stop()
                            if i < len(list_channel)-1:
                                i+=1
                            else:
                                i = 0

                            path = base + list_channel[i]
                            if i == 0:
                                so = 0
                                ranso_index = 0
                                file = path + list_pop[so]
                                instance = vlc.Instance()

                                media=instance.media_new(file)

                                player.set_media(media)

                                player.play()
                                time.sleep(1)
                            elif i == 1:
                                so = 0
                                ranso_index = 0
                                file = path + list_piano[so]
                                instance = vlc.Instance()

                                media=instance.media_new(file)

                                player.set_media(media)

                                player.play()
                                time.sleep(1)
                            elif i == 2:
                                so = 0
                                ranso_index = 0
                                file = path + list_hip[so]
                                instance = vlc.Instance()

                                media=instance.media_new(file)

                                player.set_media(media)

                                player.play()
                                time.sleep(1)
                            elif i == 3:
                                so = 0
                                ranso_index = 0
                                file = path + list_edm[so]
                                instance = vlc.Instance()

                                media=instance.media_new(file)

                                player.set_media(media)

                                player.play()
                                time.sleep(1)

                            li_st = os.listdir(path)
                            time.sleep(0.7)
                        #get in main()

            break






	if __name__ == '__main__':

   	 try:
   	     main()
   	 except KeyboardInterrupt:
    	    pass
   	 finally:
   	     lcd_byte(0x01, LCD_CMD)
	
