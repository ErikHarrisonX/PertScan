# PertScan
Team Port Scanner
import socket
import threading
from queue import Queue
import tkinter as tk
#import tkmessagebox as tmb
import pyttsx3
import wikipedia


#Put in your own IP adress here for target. Gets accurate info if using own IP
target = "127.0.0.1"
#Setting up the Queue and open ports as variables and method
queue = Queue()
open_ports = []

#Setting up the display
#app is a placeholder name will redo name once come up with better name
#TODO: Create better name than 'app'
##app = tk.Tk()
##app.title("Port Scanner")
##app.geometry('300x90')
##app.resizeable(False, False)
#Frame and field options
##frame = ttk.Frame(app)
##options = {'padx':5, 'pady':5}

#Speach AI portion for talking ai
converter = pyttsx3.init()
speaker_rate = 142 #Set rate of talking speed (0 = slower - 200 = faster)
converter.setProperty('rate', speaker_rate)
print("")
converter.setProperty('volume', 0.7) #Set volume from 0 to 1, can also be over 1
#Set voice of speech, can be gender, adult, accent, etc
voice_id = "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Speech\Voices\Tokens\TTS_MS_EN-US_ZIRA_11.0"
converter.setProperty('voice', voice_id)

#The brickbones of the program
#This tells what to look for which is the port(s)
def PortScanner(port):
    try:
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.connect((target, port))
        return True
    except:
        return False
#Scans for all possible ports and creates a list of all the open ones
def fill_QUEUE(port_list):
    for port in port_list:
        queue.put(port)

#Programs scans for open ports and tells you which are open
def Worker():
    while not queue.empty():
        port = queue.get()
        if PortScanner(port):
            print("Scanning, please wait\n")
            print("This might take a while")
            print("Port {} is open!".format(port))
            open_ports.append(port)

converter.say("Hello welcome to Port Scarn, working title, choose what you would like to do.")

port_list = range(1, 1024)
fill_QUEUE(port_list)
#Creates the thread list so it can write which ports are open
thread_list = []
#Increase the number to speed the process, but dont make it too large
for t in range(500):
    thread = threading.Thread(target=Worker)
    thread_list.append(thread)
    
#Thread starts
for thread in thread_list:
    thread.start()
    
#Thread join or adding to thread
for thread in thread_list:
    thread.join()
    
#Printing out result of open ports
converter.say("The open ports are: " , open_ports)
print("Open ports are:\n " , open_ports)


def PortScanButton():
    tmb.showinfo("Port Scan", "")
