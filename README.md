# python_plot_from_serial
# Using Python to plot the data transferred via serial (via an Arduino)

import serial
import matplotlib.pyplot as plt
from drawnow import *
import atexit

values = []

plt.ion()
cnt=0

serialArduino = serial.Serial('COM5', 9600) #'COM5'only refers to the port of my computer. this will be different for you (especially for Mac)

def plotValues():
    plt.title('Serial value from Arduino')
    plt.grid(True)
    plt.ylabel('Values')
    plt.plot(values, 'rx-', label='values')
    plt.legend(loc='upper right')

def doAtExit():
    serialArduino.close()
    print("Close serial")
    print("serialArduino.isOpen() = " + str(serialArduino.isOpen()))

atexit.register(doAtExit)

print("serialArduino.isOpen() = " + str(serialArduino.isOpen()))

#pre-load dummy data
for i in range(0,26):
    values.append(0)
    clr
while True:
    while (serialArduino.inWaiting()==0):
        pass
    print("readline()")
    valueRead = serialArduino.readline(500)

    #check if valid value can be casted
    try:
        valueInInt = int(valueRead)
        print(valueInInt)
        if valueInInt <= 1024:
            if valueInInt >= 0:
                values.append(valueInInt)
                values.pop(0)
                drawnow(plotValues)
            else:
                print("Invalid! negative number")
        else:
            print("Invalid! too large")
    except ValueError:
        print("Invalid! cannot cast")
