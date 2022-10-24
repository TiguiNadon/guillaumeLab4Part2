# guillaumeLab4Part2

#!/usr/bin/env python
import RPi.GPIO as GPIO
GPIO.setwarnings(False)
import ADC0832
import time
import requests
import dweepy

#dweetIO = "https://dweet.io/dweet/for/"
myThing = "guillaume"

def init():
    ADC0832.setup()
    GPIO.setmode(GPIO.BOARD)
    GPIO.setup(16,GPIO.OUT, initial=GPIO.LOW) #Setup the distance sensor
    GPIO.setup(18, GPIO.IN)


def checkdist():
    GPIO.output(16, GPIO.HIGH)
    time.sleep(0.000015)
    GPIO.output(16,GPIO.LOW)
    while not GPIO.input(18):
            pass
    t1 = time.time()
    while GPIO.input(18):
            pass
    t2 = time.time()
    return (t2-t1)*340/2

def loop():
    while True:
        res = ADC0832.getResult()
        vol = 5.0/255 * res
        print ('analog value: %03d  ||  voltage: %.2fV' %(res, vol))
        print(checkdist())
        
        dweepy.dweet_for(myThing,{key1: str('{:.2f}'.format(vol)), key2: str('{:.2f}'.format(checkdist()))})
        print(rqsString) #url for post
        rqs = requests.post(rqsString) #posting
        print (rqs.status_code) #printing response status
        print (rqs.headers) #print response headers
        print (rqs.content) #print response content
        time.sleep(5) #5 seconds pause

if __name__ == '__main__':
    init()
    try:
        loop()
    except KeyboardInterrupt: 
        ADC0832.destroy()
        print ('The end !')



