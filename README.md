# -*- coding: utf-8 -*-
"""
Created on Fri Oct 18 17:05:25 2019

@author: ragha
"""

import speech_recognition as sr
import smtplib 
import pyttsx3
import winsound
import sys
import os
import cv2
#import time
import numpy as np
from os import listdir
from os.path import isfile, join
flag1=5

"""Voice Assisstant"""

def voice_assisst():
    """ RATE"""
    rate = engine.getProperty('rate')   
    print (rate)                        
    engine.setProperty('rate', 100)     
    
    
    """VOLUME"""
    volume = engine.getProperty('volume')   
    print (volume)                          
    engine.setProperty('volume',1.0)    
    
    """VOICE"""
    voices = engine.getProperty('voices')       
    engine.setProperty('voice', voices[0].id)
  
def speak(message):
    engine.say(message)
    engine.runAndWait()
    engine.stop() 

def beep():
      duration = 1700  # milliseconds
      freq = 2500  # Hz
      winsound.Beep(freq, duration) 

"""Termination"""

def cont():
    speak("Do You want to start again say yes or no")
    answer=recognize(recognizer,mic)
    l=answer.split(" ")
    
    if 'yes' in l:
        get_details()
    else:
        speak('thank you')
        sys.exit()
        
def cancel_op(input_string):
    l=input_string.split(" ")
    if 'cancel' in l:
        speak('Operation Cancelled')
        cont()
        
        
def file(f,email,pas):
    f.write(repr(email)+" "+repr(pas))
    f.close()
    
def file1(f1,m1,m2,m3):
    f1.write(repr(m1)+" "+repr(m2)+" "+repr(m3))
    f1.close()    
       
    
"""Recognize form Microphone"""

def recognize(recognizer, microphone):
  
    with microphone as source:
        recognizer.adjust_for_ambient_noise(source) 
        try:    
            audio = recognizer.listen(source,timeout=5)
        except sr.WaitTimeoutError:
            speak("listening timed out while waiting to start, please speak again")
            audio = recognizer.listen(source,timeout=5)
        

    try:
        speech = recognizer.recognize_google(audio)
    except sr.RequestError:
        speak("API unavailable/unresponsive")
        cont()
    except sr.UnknownValueError:
        speak("Unable to recognize speech")
        cont()
    except UnboundLocalError:
        speak("Audio not detected")
        cont()

    try:
        return speech
    except UnboundLocalError:
        speak("Audio not detected")
        cont()


def folder(inp):
       os.chdir('F:/Python/New folder (2)')
       os.mkdir(inp)

"""Get User Details"""
    
def get_details():
        

    speak("Please Speak Your Name after 2 seconds of beep")
    beep()
    name = recognize(recognizer, mic)
    cancel_op(name)
    speak("Welcome"+str(name))
    folder(name)
    f= open(str(name)+".txt","w+")
               
    try:
        run(name)
    except:
        speak('User already exists')
        sys.exit()

    speak("Please Speak Your mail id after 2 seconds of beep")
    beep()
    mail = recognize(recognizer, mic)
    cancel_op(mail)
    mail=mail.lower()
    mail=mail.replace(" ","")
    speak("Your mail is"+str(mail))
    

    speak("Please Speak password after 2 seconds of beep")
    beep()
    password = recognize(recognizer, mic)
    cancel_op(password)
    password=password.lower()
    password=password.replace(" ","")
    speak("Your password is"+str(password))
    
    s=smtplib.SMTP('smtp.gmail.com',587)
    s.starttls()
    file(f,mail,password)
    
    
        
def send_mail(mail,name):
    s=smtplib.SMTP('smtp.gmail.com',587)
    s.starttls()
    speak("Please Speak receiver's mail id after 2 seconds of beep")
    beep()
    mail_receivers = recognize(recognizer, mic)
    mail_receivers=mail_receivers.lower()
    mail_receivers=mail_receivers.replace(" ","")

    speak("Please Speak Your message after 2 seconds of beep")
    beep()
    message=recognize(recognizer, mic)
    os.chdir('R:/Python/')
    f1= open(str(name)+"1.txt","w+")
    file1(f1,mail,mail_receivers,message)
    f1 = open(str(name)+'.1txt','r')
    l=f1.readlines()
    r="".join(l)
    a=r.split(" ")
    n1=a[0].split('\'')
    mail=n1[1]
    n2=a[1].split('\'')
    mail_receivers=n2[1]
    n3=a[2].split('\'')
    message=n3[1]

    try:
        s.sendmail(mail,mail_receivers,message)
        speak("mail sent successfully")
        s.quit()
    except:
        speak("mail not sent")
        s.quit()
        sys.exit()
"""Creating Location"""

   
        
        
"""Face Sign IN"""

def run(inp):
    face_classifier = cv2.CascadeClassifier('C:/Users/91870/AppData/Local/Programs/Python/Python36/Lib/site-packages/cv2/data/haarcascade_frontalface_default.xml')
    flag=0
    def face_extractor(img):
        
        gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
        faces = face_classifier.detectMultiScale(gray,1.3,5)

        if faces is():
            return None

        for(x,y,w,h) in faces:
            cropped_face = img[y:y+h, x:x+w]

        return cropped_face
        

    cap = cv2.VideoCapture(0)

    count=0

    while True:
        ret, frame = cap.read()
        frame=cv2.flip(frame,1)
        if face_extractor(frame) is not None:
            count+=1
            face = cv2.resize(face_extractor(frame),(200,200))
            face = cv2.cvtColor(face, cv2.COLOR_BGR2GRAY)

            file_name_path = 'F:/Python/New folder (2)/'+inp+'/'+str(count)+'.jpg'
            cv2.imwrite(file_name_path,face)

            cv2.putText(face,str(count),(50,50),cv2.FONT_HERSHEY_COMPLEX,1,(0,255,0),2)
            cv2.imshow('Face Cropper',face)
        else:
            if(flag!=1):
                print("Searching Face...Please adjust the camera")
                flag=1
                pass

        if cv2.waitKey(1)==13 or count==100:
            break

    cap.release()
    cv2.destroyAllWindows()
    print('Collecting Samples Complete')    


"""Face Detection"""

def face_detector(img, size = 0.5):
    gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
    face_classifier = cv2.CascadeClassifier('C:/Users/91870/AppData/Local/Programs/Python/Python36/Lib/site-packages/cv2/data/haarcascade_frontalface_default.xml')
    faces = face_classifier.detectMultiScale(gray,1.3,5)

    if faces is():
        return img,[]

    for(x,y,w,h) in faces:
        cv2.rectangle(img, (x,y),(x+w,y+h),(0,255,255),2)
        roi = img[y:y+h, x:x+w]
        roi = cv2.resize(roi, (200,200))

    return img,roi

"""Validating User"""

def checking_face():
    cap = cv2.VideoCapture(0)
    speak("Please Speak Your Name after 2 seconds of beep")
    beep()
    name = recognize(recognizer, mic)
    cancel_op(name)
    data_path = 'F:/Python/New folder (2)/'+name+'/'


    onlyfiles = [f for f in listdir(data_path) if isfile(join(data_path,f))]

    model = cv2.face.LBPHFaceRecognizer_create()
    Training_Data, Labels = [], []

    for i, files in enumerate(onlyfiles):
        image_path = data_path + onlyfiles[i]
        images = cv2.imread(image_path, cv2.IMREAD_GRAYSCALE)
        Training_Data.append(np.asarray(images, dtype=np.uint8))
        Labels.append(i)

    Labels = np.asarray(Labels, dtype=np.int32)
    model.train(np.asarray(Training_Data), np.asarray(Labels))

    while True:
        ret, frame = cap.read()
        frame=cv2.flip(frame,1)
        image, face = face_detector(frame)
        
        try:
            face1 = cv2.cvtColor(face, cv2.COLOR_BGR2GRAY)
            result = model.predict(face1)
    
            if result[1] < 500:
                confidence = int(100*(1-(result[1])/300))
                display_string = str(confidence)+'% Confidence it is user'
                cv2.putText(image,display_string,(100,120), cv2.FONT_HERSHEY_COMPLEX,1,(250,120,255),2)
    
            if confidence > 80:
                cv2.putText(image, "Unlocked",(250,450), cv2.FONT_HERSHEY_COMPLEX,1,(0,255,0),2)
                cv2.imshow('Face Cropper', image)
                User_matched=1
                break
                
            else:
                cv2.putText(image, "Locked",(250,450), cv2.FONT_HERSHEY_COMPLEX,1,(0,0,255),2)
                cv2.imshow('Face Cropper', image)
                          
        except:
            cv2.putText(image, "Face Not Found",(250,450), cv2.FONT_HERSHEY_COMPLEX,1,(255,0,0),2)
            cv2.imshow('Face Cropper', image)
            pass
        if cv2.waitKey(1)==13 :
            break
    
    cap.release()
    cv2.destroyAllWindows()

    if User_matched==1:
        speak("Welcome"+str(name))
        os.chdir('F:/Python/New folder (2)/')
        f = open(str(name)+'.txt','r')
        l=f.readlines()
        r="".join(l)
        a=r.split(" ")
        n1=a[0].split('\'')
        mail=n1[1]
        n2=a[1].split('\'')
        password=n2[1]
        
        
        s=smtplib.SMTP('smtp.gmail.com',587)
        s.starttls()
        try:
            
            s.login(mail,password)
            speak("login successfull")
            try:
                send_mail(mail,name)
            except:
                speak("Somethong went wrong please try again")
                sys.exit()
        except:
            speak("Mail and Password not accepted")
            
            
        
            
        
        
"""Setting Recognizer Class Instance"""

engine = pyttsx3.init()
cancelled=0 
recognizer = sr.Recognizer()
recognizer.dynamic_energy_threshold=500
recognizer.pause_threshold=3
mic = sr.Microphone(device_index=1)


"""Start"""
def start():
    speak("Please Speak Login for login and Signin for signin mail after 2 seconds of beep")
    beep()
    g = recognize(recognizer, mic)
    cancel_op(g)
    g=g.lower()
    g=g.replace(" ","")
    if(str(g)=='signin'):
        get_details()
        speak("User Successfully Registered")
        speak('You further want to login or exit, Please Speak after 2 seconds of beep')
        beep()
        r = recognize(recognizer, mic)
        cancel_op(g)
        r=r.lower()
        
        if(str(r)=='log in'):
            checking_face()
    
    if(str(g)=='login'):
        checking_face()
    else:
        speak('Unable to understand what you want to do')
start()
