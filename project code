from tkinter import *
from tkinter import ttk
from tkinter import messagebox as mess
import tkinter.simpledialog as tsd
import cv2
import glob
import time
import face_recognition
import numpy as np
from datetime import datetime
from datetime import date
import pymysql
import sys
import os

conn = pymysql.connect(host= '192.168.220.243', user ='root', password = '', db = 'vizion')
cur = conn.cursor()

def encoding1(images):
    encode=[]

    for img in images:
        unk_encoding = face_recognition.face_encodings(img)[0]
        encode.append(unk_encoding)
    return encode   

def mysql_add_data(names):
    query = f"INSERT INTO attendance(USERNAME) VALUES('{names}')"
    cur.execute(query)
    print(f"{cur.rowcount} details inserted")
    conn.commit()

dateString = '%Y-%m-%d %H-%M-%S'

def add_attendance(atten_id, atten_name, atten_date, atten_time):
    table.insert("", 'end', text=atten_id,values=(atten_name, atten_date, atten_time))
    
    
def cap_image():
    cam = cv2.VideoCapture(0)

    while True:
        ret, image = cam.read()
        cv2.imshow('Imagetest',image)
        k = cv2.waitKey(1)
        print(k)
        if k != -1:
            break
    var = Entrybox.get()
    path = f"/home/ampi/VIZION/TrainingImages/{var}.jpg"
    Entrybox.delete(0,'end')
    cv2.imwrite(path, image)
    cam.release()
    cv2.destroyAllWindows()
    

def run():
    q =1
    FONT=cv2.FONT_HERSHEY_COMPLEX
    images=[]
    names=[]
    today = date.today()
    path = "/home/ampi/VIZION/TrainingImages/."
    for file in glob.glob(path):
        image = cv2.imread(file)
        a=os.path.basename(file)
        b=os.path.splitext(a)[0]
        names.append(b)
        images.append(image)

    encodelist=encoding1(images)
    cap =cv2.VideoCapture(0)
    name1 = "name"
    while True:
        ret,frame=cap.read()
        frame1=cv2.resize(frame,(0,0),None,0.25,0.25)
        face_locations = face_recognition.face_locations(frame1)
        curframe_encoding = face_recognition.face_encodings(frame1,face_locations)
        for encodeface,facelocation in zip(curframe_encoding,face_locations):
            results = face_recognition.compare_faces(encodelist, encodeface)
            distance= face_recognition.face_distance(encodelist, encodeface)
            match_index=np.argmin(distance)
            name=names[match_index]
            if(name1 != name):
                now = time.strftime("%H:%M:%S")
                mysql_add_data(name)
                add_attendance(q, name, today, now)
                q = q+1
                
                table.update()
            name1 = name
            
            x1,y1,x2,y2=facelocation
            x1,y1,x2,y2=x1*4,y1*4,x2*4,y2*4
            cv2.rectangle(frame,(y1,x1),(y2,x2),(0,0,255),3)
            cv2.putText(frame,name,(y2+6,x2-6),cv2.FONT_HERSHEY_COMPLEX,1,(255,0,255),2)
            
        cv2.imshow("FRAME",frame)
        k = cv2.waitKey(1)
        
        print(k)
        if k != -1:
            break
       
    cap.release()
    cv2.destroyAllWindows()

window = Tk()
window.geometry("1280x720")
window.title("Attendance System")
window.configure(background='indigo')


frame1 = Frame(window, bg="light pink")
frame1.place(relx=0.08, rely=0.10, relwidth=0.40, relheight=0.70)

frame2 = Frame(window, bg="deep sky blue")
frame2.place(relx=0.53, rely=0.10, relwidth=0.40, relheight=0.70)

title1 = Label(window, text="Face Recognition Based Attendance System" ,fg="white",bg="black" ,width=40,height=1,font=('algerian',28,'bold'))
title1.place(x=0, y=10)

heading1 = Label(frame1, text="                 For Already Registered                       ", fg="black",bg="pale green" ,font=('times', 17, ' bold ') )
heading1.place(x=0,y=0)

heading2 = Label(frame2, text="                  For New Student                       ", fg="black",bg="light green" ,font=('times', 17, ' bold ') )
heading2.grid(row=0,column=0)

Label1 = Label(frame2, text="Enter Name",width=20  ,fg="black"  ,bg="blue" ,font=('times', 17, ' bold '))
Label1.place(x=88, y=130)

Entrybox = Entry(frame2,width=32 ,fg="black",font=('times', 17, ' bold ')  )
Entrybox.place(x=30, y=180)

Message = Label(frame2, text="1)Enter Name >>> 2)Take Photo >>> 3)Save Details" ,bg="red" ,fg="white"  ,width=48 ,height=1,font=('times', 12, ' bold '))
Message.place(x=11, y=70)

Label2 = Label(frame1, text="Attendance Record",width=20  ,fg="yellow"  ,bg="blue"  ,height=1 ,font=('algerian', 16, ' bold '))
Label2.place(x=50, y=110)


## MENUBAR 

Menubar = Menu(window,relief='ridge')
Menu = Menu(Menubar,tearoff=0)

Menu.add_command(label='Exit',command = window.destroy)

#ATTENDANCE TABLE 

table= ttk.Treeview(frame1,height =13,columns = ('name','date','time'))
table.column('#0',width=40)
table.column('name',width=150)
table.column('date',width=80)
table.column('time',width=80)
table.grid(row=2,column=0,padx=(20,0),pady=(145,0),columnspan=4)
table.heading('#0',text ='SNo.')
table.heading('name',text ='NAME')
table.heading('date',text ='DATE')
table.heading('time',text ='TIME')







#SCROLLBAR 

scroll=ttk.Scrollbar(frame1,orient='vertical',command=table.yview)
scroll.grid(row=2,column=4,padx=(0,100),pady=(150,0),sticky='ns')
table.configure(yscrollcommand=scroll.set)

#BUTTONS 

takeImg = Button(frame2, text="Take Photo" ,fg="black" ,command=cap_image ,bg="yellow"  ,width=35  ,height=1,font=('times', 16, ' bold '))
takeImg.place(x=0, y=250)
trainImg = Button(frame2, text="Save Details" ,fg="black"  ,bg="yellow"  ,width=35  ,height=1 ,font=('times', 16, ' bold '))
trainImg.place(x=0, y=350)
trackImg = Button(frame1, text="Take Attendance             " , command=run, fg="red"  ,bg="blue"  ,width=40  ,height=1 ,font=('times', 18, ' bold '))
trackImg.place(x=0,y=50)
quitWindow = Button(frame1, text="Exit         ", command=window.destroy  ,fg="black"  ,bg="red"  ,width=40 ,height=1,font=('times', 16, ' bold '))
quitWindow.place(x=0, y=450)
