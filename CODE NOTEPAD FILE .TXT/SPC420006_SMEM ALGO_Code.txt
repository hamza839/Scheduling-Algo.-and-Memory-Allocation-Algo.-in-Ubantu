from  tkinter import * 
from tkinter import ttk
from tkinter import messagebox

root = Tk()
root.title("Scheduling Algorithm")
root.geometry("400x300")

process = []
burst_time = []
arrival_time = []
index=0
selection=0
################
memselection = 0
blockSize = []
processSize = []
memindex=0
############
global disvar1
global disvar2
global disvar3

disvar1=""
disvar2=""
disvar3=""

pqt = ""
           
uivalue = ""
#_______________________________________Schedule.PY________________________________
###########################
class FCFS:
    def processData(self, no_of_processes):
        process_data = []
        for i in range(no_of_processes):
            temporary = []
            process_id = int(idbox.get(i))
            arrival_time = int(atbox.get(i))
            burst_time = int (btbox.get(i))

            temporary.extend([process_id, arrival_time, burst_time])
            process_data.append(temporary)
        FCFS.schedulingProcess(self, process_data)

    def schedulingProcess(self, process_data):
        process_data.sort(key=lambda x: x[1])

        '''

        Sort according to Arrival Time 
        '''

        start_time = []
        exit_time = []
        s_time = 0
        for i in range(len(process_data)):
            if s_time < process_data[i][1]:
                s_time = process_data[i][1]
            start_time.append(s_time)
            s_time = s_time + process_data[i][2]
            e_time = s_time
            exit_time.append(e_time)
            process_data[i].append(e_time)
        t_time = FCFS.calculateTurnaroundTime(self, process_data)
        w_time = FCFS.calculateWaitingTime(self, process_data)
        FCFS.printData(self, process_data, t_time, w_time)

    def calculateTurnaroundTime(self, process_data):
        total_turnaround_time = 0
        for i in range(len(process_data)):
            turnaround_time = process_data[i][3] - process_data[i][1]
            '''
            turnaround_time = completion_time - arrival_time
            '''
            total_turnaround_time = total_turnaround_time + turnaround_time
            process_data[i].append(turnaround_time)
        average_turnaround_time = total_turnaround_time / len(process_data)
        '''
        average_turnaround_time = total_turnaround_time / no_of_processes
        '''
        return average_turnaround_time

    def calculateWaitingTime(self, process_data):
        total_waiting_time = 0
        for i in range(len(process_data)):
            waiting_time = process_data[i][4] - process_data[i][2]
            '''
            waiting_time = turnaround_time - burst_time
            '''
            total_waiting_time = total_waiting_time + waiting_time
            process_data[i].append(waiting_time)
        average_waiting_time = total_waiting_time / len(process_data)
        '''
        average_waiting_time = total_waiting_time / no_of_processes
        '''
        return average_waiting_time

    def printData(self, process_data, average_turnaround_time, average_waiting_time):


      #  print("Process_ID  Arrival_Time  Burst_Time  Completion_Time  Turnaround_Time  Waiting_Time")
        for i in range(len(process_data)):
      ##       for j in range(len(process_data[i])):
       ##          print(process_data[i][j], end="\t\t\t\t")
             ctbox.insert(i,process_data[i][3]) 
             tatbox.insert(i,process_data[i][4]) 
             wtbox.insert(i,process_data[i][5]) 
     ##   print('Average Turnaround Time: ', average_turnaround_time)
      ##  print('Average Waiting Time: ', average_waiting_time)
        disvar1 = ("Average waiting time = %.5f "%(average_waiting_time)) 
        #dis1 = Label(top2,text=disvar1 ,font=(28),pady=10)
        #dis1.grid(column=2, row=11)
    
        disvar2= ("Average turn around time = ", average_turnaround_time)  
        #dis2 = Label(top2,text=disvar2 ,font=(28),pady=10)
        #dis2.grid(column=2, row=12)
        
        dis3 = ""
        
        printR(disvar1,disvar2,disvar3)

    
def display():
    idbox.delete(0,sizeid)
    btbox.delete(0,sizebt)
    atbox.delete(0,sizeat)
###########################

##################################################
class SJF:

    def processData(self, no_of_processes):
        process_data = []
        for i in range(no_of_processes):
            temporary = []
         #   process_id = int(input("Enter Process ID: "))
          ##  arrival_time = int(input(f"Enter Arrival Time for Process {process_id}: "))
          #  burst_time = int(input(f"Enter Burst Time for Process {process_id}: "))
            process_id = int(idbox.get(i))
            arrival_time = int(atbox.get(i))
            burst_time = int (btbox.get(i))
            temporary.extend([process_id, arrival_time, burst_time, 0, burst_time])
            '''
            '0' is the state of the process. 0 means not executed and 1 means execution complete
            '''
            process_data.append(temporary)
        SJF.schedulingProcess(self, process_data)

    def schedulingProcess(self, process_data):
        start_time = []
        exit_time = []
        s_time = 0
        sequence_of_process = []
        process_data.sort(key=lambda x: x[1])
        '''
        Sort processes according to the Arrival Time
        '''
        while 1:
            ready_queue = []
            normal_queue = []
            temp = []
            for i in range(len(process_data)):
                if process_data[i][1] <= s_time and process_data[i][3] == 0:
                    temp.extend([process_data[i][0], process_data[i][1], process_data[i][2], process_data[i][4]])
                    ready_queue.append(temp)
                    temp = []
                elif process_data[i][3] == 0:
                    temp.extend([process_data[i][0], process_data[i][1], process_data[i][2], process_data[i][4]])
                    normal_queue.append(temp)
                    temp = []
            if len(ready_queue) == 0 and len(normal_queue) == 0:
                break
            if len(ready_queue) != 0:
                ready_queue.sort(key=lambda x: x[2])
                '''
                Sort processes according to Burst Time
                '''
                start_time.append(s_time)
                s_time = s_time + 1
                e_time = s_time
                exit_time.append(e_time)
                sequence_of_process.append(ready_queue[0][0])
                for k in range(len(process_data)):
                    if process_data[k][0] == ready_queue[0][0]:
                        break
                process_data[k][2] = process_data[k][2] - 1
                if process_data[k][2] == 0:        #If Burst Time of a process is 0, it means the process is completed
                    process_data[k][3] = 1
                    process_data[k].append(e_time)
            if len(ready_queue) == 0:
                if s_time < normal_queue[0][1]:
                    s_time = normal_queue[0][1]
                start_time.append(s_time)
                s_time = s_time + 1
                e_time = s_time
                exit_time.append(e_time)
                sequence_of_process.append(normal_queue[0][0])
                for k in range(len(process_data)):
                    if process_data[k][0] == normal_queue[0][0]:
                        break
                process_data[k][2] = process_data[k][2] - 1
                if process_data[k][2] == 0:        #If Burst Time of a process is 0, it means the process is completed
                    process_data[k][3] = 1
                    process_data[k].append(e_time)
        t_time = SJF.calculateTurnaroundTime(self, process_data)
        w_time = SJF.calculateWaitingTime(self, process_data)
        SJF.printData(self, process_data, t_time, w_time, sequence_of_process)

    def calculateTurnaroundTime(self, process_data):
        total_turnaround_time = 0
        for i in range(len(process_data)):
            turnaround_time = process_data[i][5] - process_data[i][1]
            '''
            turnaround_time = completion_time - arrival_time
            '''
            total_turnaround_time = total_turnaround_time + turnaround_time
            process_data[i].append(turnaround_time)
        average_turnaround_time = total_turnaround_time / len(process_data)
        '''
        average_turnaround_time = total_turnaround_time / no_of_processes
        '''
        return average_turnaround_time

    def calculateWaitingTime(self, process_data):
        total_waiting_time = 0
        for i in range(len(process_data)):
            waiting_time = process_data[i][6] - process_data[i][4]
            '''
            waiting_time = turnaround_time - burst_time
            '''
            total_waiting_time = total_waiting_time + waiting_time
            process_data[i].append(waiting_time)
        average_waiting_time = total_waiting_time / len(process_data)
        '''
        average_waiting_time = total_waiting_time / no_of_processes
        '''
        return average_waiting_time

    def printData(self, process_data, average_turnaround_time, average_waiting_time, sequence_of_process):
        process_data.sort(key=lambda x: x[0])
        '''
        Sort processes according to the Process ID
        '''
       # print("Process_ID  Arrival_Time  Rem_Burst_Time      Completed  Orig_Burst_Time Completion_Time  Turnaround_Time  Waiting_Time")
        for i in range(len(process_data)):
      ##       for j in range(len(process_data[i])):
       ##          print(process_data[i][j], end="\t\t\t\t")
             ctbox.insert(i,process_data[i][5]) 
             tatbox.insert(i,process_data[i][6]) 
             wtbox.insert(i,process_data[i][7]) 
     ##   print('Average Turnaround Time: ', average_turnaround_time)
      ##  print('Average Waiting Time: ', average_waiting_time)
      ##  print('Sequence of Process: ', sequence_of_process)
        disvar1 = ("Average waiting time = %.5f "%(average_waiting_time)) 
        #dis1 = Label(top2,text=disvar1 ,font=(28),pady=10)
        #dis1.grid(column=2, row=11)
    
        disvar2= ("Average turn around time = ", average_turnaround_time)  
        #dis2 = Label(top2,text=disvar2 ,font=(28))#,pady=10)
        #dis2.grid(column=2, row=12)

        disvar3= ("Sequence OF Process: ", sequence_of_process)  
        #dis3 = Label(top2,text=disvar3 ,font=(28))#,pady=10)
        #dis3.grid(column=2, row=13)
        printR(disvar1,disvar2,disvar3)
################################################################

##************************************************
def printR(disvar1,disvar2,disvar3):
       
        dis1.configure(text=disvar1)
        dis1.grid(row=12) 
         
        dis2.configure(text=disvar2)
        dis2.grid(row=13)

        dis3.configure(text=disvar3)
        dis3.grid(row=14)

#***************************************************
class RoundRobin:

    def processData(self, no_of_processes, quntum):
        process_data = []
        for i in range(no_of_processes):
            temporary = []
            process_id = int(idbox.get(i))
            arrival_time = int(atbox.get(i))
            burst_time = int (btbox.get(i))

            temporary.extend([process_id, arrival_time, burst_time, 0, burst_time])
            '''
            '0' is the state of the process. 0 means not executed and 1 means execution complete
            
            '''
            process_data.append(temporary)


        time_slice = quntum


        RoundRobin.schedulingProcess(self, process_data, time_slice)

    def schedulingProcess(self, process_data, time_slice):
        start_time = []
        exit_time = []
        executed_process = []
        ready_queue = []
        s_time = 0
        process_data.sort(key=lambda x: x[1])
        '''
        Sort processes according to the Arrival Time
        '''
        while 1:
            normal_queue = []
            temp = []
            for i in range(len(process_data)):
                if process_data[i][1] <= s_time and process_data[i][3] == 0:
                    present = 0
                    if len(ready_queue) != 0:
                        for k in range(len(ready_queue)):
                            if process_data[i][0] == ready_queue[k][0]:
                                present = 1
                    '''
                    The above if loop checks that the next process is not a part of ready_queue
                    '''
                    if present == 0:
                        temp.extend([process_data[i][0], process_data[i][1], process_data[i][2], process_data[i][4]])
                        ready_queue.append(temp)
                        temp = []
                    '''
                    The above if loop adds a process to the ready_queue only if it is not already present in it
                    '''
                    if len(ready_queue) != 0 and len(executed_process) != 0:
                        for k in range(len(ready_queue)):
                            if ready_queue[k][0] == executed_process[len(executed_process) - 1]:
                                ready_queue.insert((len(ready_queue) - 1), ready_queue.pop(k))
                    '''
                    The above if loop makes sure that the recently executed process is appended at the end of ready_queue
                    '''
                elif process_data[i][3] == 0:
                    temp.extend([process_data[i][0], process_data[i][1], process_data[i][2], process_data[i][4]])
                    normal_queue.append(temp)
                    temp = []
            if len(ready_queue) == 0 and len(normal_queue) == 0:
                break
            if len(ready_queue) != 0:
                if ready_queue[0][2] > time_slice:
                    '''
                    If process has remaining burst time greater than the time slice, it will execute for a time period equal to time slice and then switch
                    '''
                    start_time.append(s_time)
                    s_time = s_time + time_slice
                    e_time = s_time
                    exit_time.append(e_time)
                    executed_process.append(ready_queue[0][0])
                    for j in range(len(process_data)):
                        if process_data[j][0] == ready_queue[0][0]:
                            break
                    process_data[j][2] = process_data[j][2] - time_slice
                    ready_queue.pop(0)
                elif ready_queue[0][2] <= time_slice:
                    '''
                    If a process has a remaining burst time less than or equal to time slice, it will complete its execution
                    '''
                    start_time.append(s_time)
                    s_time = s_time + ready_queue[0][2]
                    e_time = s_time
                    exit_time.append(e_time)
                    executed_process.append(ready_queue[0][0])
                    for j in range(len(process_data)):
                        if process_data[j][0] == ready_queue[0][0]:
                            break
                    process_data[j][2] = 0
                    process_data[j][3] = 1
                    process_data[j].append(e_time)
                    ready_queue.pop(0)
            elif len(ready_queue) == 0:
                if s_time < normal_queue[0][1]:
                    s_time = normal_queue[0][1]
                if normal_queue[0][2] > time_slice:
                    '''
                    If process has remaining burst time greater than the time slice, it will execute for a time period equal to time slice and then switch
                    '''
                    start_time.append(s_time)
                    s_time = s_time + time_slice
                    e_time = s_time
                    exit_time.append(e_time)
                    executed_process.append(normal_queue[0][0])
                    for j in range(len(process_data)):
                        if process_data[j][0] == normal_queue[0][0]:
                            break
                    process_data[j][2] = process_data[j][2] - time_slice
                elif normal_queue[0][2] <= time_slice:
                    '''
                    If a process has a remaining burst time less than or equal to time slice, it will complete its execution
                    '''
                    start_time.append(s_time)
                    s_time = s_time + normal_queue[0][2]
                    e_time = s_time
                    exit_time.append(e_time)
                    executed_process.append(normal_queue[0][0])
                    for j in range(len(process_data)):
                        if process_data[j][0] == normal_queue[0][0]:
                            break
                    process_data[j][2] = 0
                    process_data[j][3] = 1
                    process_data[j].append(e_time)
        t_time = RoundRobin.calculateTurnaroundTime(self, process_data)
        w_time = RoundRobin.calculateWaitingTime(self, process_data)
        RoundRobin.printData(self, process_data, t_time, w_time, executed_process)

    def calculateTurnaroundTime(self, process_data):
        total_turnaround_time = 0
        for i in range(len(process_data)):
            turnaround_time = process_data[i][5] - process_data[i][1]
            '''
            turnaround_time = completion_time - arrival_time
            '''
            total_turnaround_time = total_turnaround_time + turnaround_time
            process_data[i].append(turnaround_time)
        average_turnaround_time = total_turnaround_time / len(process_data)
        '''
        average_turnaround_time = total_turnaround_time / no_of_processes
        '''
        return average_turnaround_time

    def calculateWaitingTime(self, process_data):

        total_waiting_time = 0
        for i in range(len(process_data)):
            waiting_time = process_data[i][6] - process_data[i][4]
            '''
            waiting_time = turnaround_time - burst_time
            '''
            total_waiting_time = total_waiting_time + waiting_time
            process_data[i].append(waiting_time)
        average_waiting_time = total_waiting_time / len(process_data)
        '''
        average_waiting_time = total_waiting_time / no_of_processes
        '''
        return average_waiting_time

    def printData(self, process_data, average_turnaround_time, average_waiting_time, executed_process):
        process_data.sort(key=lambda x: x[0])
        '''
        Sort processes according to the Process ID
        '''
       # print("Process_ID  Arrival_Time  Rem_Burst_Time   Completed  Original_Burst_Time  Completion_Time  Turnaround_Time  Waiting_Time")
        for i in range(len(process_data)):
      ##       for j in range(len(process_data[i])):
       ##          print(process_data[i][j], end="\t\t\t\t")
             ctbox.insert(i,process_data[i][5]) 
             tatbox.insert(i,process_data[i][6]) 
             wtbox.insert(i,process_data[i][7]) 
       # print('Average Turnaround Time: ', average_turnaround_time)
       # print('Average Waiting Time: ', average_waiting_time)
      #  print('Sequence of Process: ', executed_process)
        disvar1 = ("Average waiting time = %.5f "%(average_waiting_time)) 
        #dis1 = Label(top2,text=disvar1 ,font=(28),pady=10)
        #dis1.grid(column=2, row=11)
    
        disvar2= ("Average turn around time = ", average_turnaround_time)  
        #dis2 = Label(top2,text=disvar2 ,font=(28),pady=10)
        #dis2.grid(column=2, row=12)

        disvar3= ("Sequence OF Process: ", executed_process)  
        #dis3 = Label(top2,text=disvar3 ,font=(28),pady=10)
        #dis3.grid(column=2, row=13)
        
        printR(disvar1,disvar2,disvar3)

################################################################

def gen(idbox):
        
        global pqt 
        label3 = Label(top2,text="Scheduling Proccesses  " ,font=(28),pady=10)
        label3.grid(column=1, row=0)
        
        frame = Frame(top2,width=400, height=300,borderwidth = 3,background="gray")
        frame.grid(column=1, row=2 , sticky="nsew", pady= 10 )
        
        label5 = Label(frame,text="Enter Process ID : " ,font=(28),pady=10)
        label5.grid(column=1, row=2)
        
        pid=Entry(frame,width=30)
        pid.grid(column=1, row=3)
        
        label6 = Label(frame,text="Enter Process Burst Time: " ,font=(28),pady=10)
        label6.grid(column=2, row=2)
        
        pbt=Entry(frame,width=30)
        pbt.grid(column=2, row=3)
        
        label7 = Label(frame,text="Enter Process Arrival Time: " ,font=(28),pady=10)
        label7.grid(column=3, row=2)
        
        pat=Entry(frame,width=30)
        pat.grid(column=3, row=3)
        
        label8 = Label(frame,text="Enter Quantum Time: ",font=(28),pady=10)
        label8.grid(column=4, row=2)
        
        global pqt
        pqt=Entry(frame,width=30)
        pqt.config(state='disabled')
        pqt.grid(column=4, row=3)    #?????? pqt.get()  #??????
       
    ###################index error############
        addbtn = Button(frame, text = "insert", command = lambda : add(pid.get(), pbt.get() , pat.get(),pid,pbt,pat), pady=10,width=10,bg="Black",fg="White",font=(20))
        ###index = ret_index(index)
        addbtn.grid(column=5, row=3)
        
        
        
        
def add(idtxt,bttxt,attxt,pid,pbt,pat):
        global index
        #print("before" , index)
        if(idtxt=="" or bttxt=="" or attxt=="" or pid=="" or pbt=="" or pat=="" or idtxt==" " or bttxt==" " or attxt==" " or pid==" " or pbt==" " or pat==" "):
       	        messagebox.showinfo("Can't Insert", "Empty Values can't be inserted")
        else:
             idCheck=True
             for i, listbox_entry in enumerate(idbox.get(0, END)):
                if listbox_entry == idtxt:
                     idCheck=False
             if idCheck:
                 if bttxt.isdecimal() and attxt.isdecimal():
                     idadd(idtxt,index)
                     btadd(bttxt,index)
                     atadd(attxt,index)
                     pid.delete(0, 'end')
                     pbt.delete(0, 'end') 
                     pat.delete(0, 'end')
                     index = index+1
                 else:
                     messagebox.showinfo("Data Type", "Invalid Data Type")
             else:
                 messagebox.showinfo("Process ID", "ID already Existing")
        #print("after" , index)
    
def idadd(idtxt,index):
     idbox.insert(index,idtxt)

def btadd(bttxt,index):
     btbox.insert(index,bttxt)

def atadd(attxt,index):
     atbox.insert(index,attxt)

def insert():              #inserting in array from listbox
    wtbox.delete(0, END)
    tatbox.delete(0, END)
    ctbox.delete(0, END)
    global sizeid
    global sizebt
    global sizeat
    sizeid = int(idbox.size())
   # print(idbox.get(0,sizeid))
    
    for i in range(0,sizeid):
        temp = int(idbox.get(i))
        process.append(temp)
                                       
    sizebt = int(btbox.size())
    #print(btbox.get(0,sizebt))
    
    for i in range(0,sizebt):
        temp1 = int (btbox.get(i))
        burst_time.append(temp1)
    
    sizeat = int(atbox.size())
    #print(atbox.get(0,sizeat))
    
    for i in range(0,sizeat):
        temp2 = int(atbox.get(i))
        arrival_time.append(temp2)
    if sizeid==0:
        messagebox.showinfo("Empty list", "Nothing to calculate")
    else:
        cal(process,sizeid,burst_time,arrival_time)
        
def cal(process,n,burst_time,arrival_time):
    global selection
    if selection == 1:
         fcfs = FCFS()
         fcfs.processData(sizeid)
    elif selection == 2:
        sjf = SJF()
        sjf.processData(sizeid)
    elif selection == 3:
          value = 0
          try: 
              value = int(pqt.get())
              if value > 0:
              	rr = RoundRobin()
              	rr.processData(sizeid,value)
              else:
                messagebox.showinfo("Wrong Quntum", "Should be greater than 0")
          except ValueError:
              messagebox.showinfo("Quntum Error", "Please, Enter correct data")
    else:
        messagebox.showinfo("Nothing Selected", "Please, select Algorithm to calculate")
        
def delete():
    index = idbox.curselection()
    if not index:
        messagebox.showinfo("Nothing Selected", "Select something to delete")
    else:
        idbox.delete(index)
        btbox.delete(index)
        atbox.delete(index)
    
def sel():
    global selection
    selection = var.get()
    resultclear()
    if selection == 3:
        pqt.config(state='normal')
    else:
        pqt.config(state='disabled')
   # print(selection)
    #label.config(text = selection)
def resultclear():
    disvar1=""
    disvar2=""
    disvar3=""
    
def EmptyRBox():
    disvar1 = ("Average waiting time = ") 
    disvar2= ("Average turn around time = ")  
    disvar3= ("Sequence OF Process: ") 
    printR(disvar1,disvar2,disvar3)
    
def Schclear():
    
    idbox.delete(0 , END)
    btbox.delete(0, END)
    atbox.delete(0, END)
    ctbox.delete(0, END)
    wtbox.delete(0, END)
    tatbox.delete(0, END)
    EmptyRBox()
    
def radio():
    
    global var
    var = IntVar()
    
    exlbl = Label(top2,text = "")  
    exlbl.grid(column=2, row=4)
    
    frame = Frame(top2,width=400, height=300,borderwidth = 3,background="black" )
    frame.grid(column=1, row=4 , sticky="nsew"  )
    
    rblbl = Label(frame,text = "Select Process",bg="Black",fg="White",font=(20))  
    rblbl.grid(column=0, row=4) 
    
    R1 = Radiobutton(frame, text="FCFS", variable=var, value=1,command=sel , anchor = W , width = 15)
    R1.grid(column=1, row=4 , padx=20)

    R2 = Radiobutton(frame, text="SJF", variable=var, value=2,command=sel, anchor = W, width = 15)
    R2.grid(column=2, row=4, padx=20)

    R3 = Radiobutton(frame, text="RR", variable=var, value=3,command=sel, anchor = W , width = 15 )
    R3.grid(column=3, row=4, padx=20)

##################################
   # for i in range(0,sizeid):
    #    print (process[i])
   # for i in range(0,sizebt):
    #    print (burst_time[i])
   # for i in range(0,sizeat):
    #    print (arrival_time[i])
#####################################
def maincomp():
    global idbox
    global btbox
    global atbox
    global tatbox
    global ctbox
    global wtbox
    
    exlbl = Label(top2,text = "")  
    exlbl.grid(column=0, row=6) 
    
    
    frame = Frame(top2, borderwidth=2, relief='ridge')
    frame.grid(column=1, row=7 , sticky="nsew"  )
        
    idlbl = Label(frame,text = "Proccess ID")  
    idlbl.grid(column=0, row=7) 

    idbox = Listbox(frame) 
    idbox.grid(column=0, row=8) 

    btlbl = Label(frame,text = "Burst Time")  
    btlbl.grid(column=1, row=7) 

    btbox = Listbox(frame) 
    btbox.grid(column=1, row=8) 
    btbox.config(selectmode=DISABLED) 

    atlbl = Label(frame,text = "Arrival Time")  
    atlbl.grid(column=2, row=7) 

    atbox = Listbox(frame) 
    atbox.grid(column=2, row=8) 

    wtlbl = Label(frame,text = "Waiting Time")  
    wtlbl.grid(column=3, row=7) 

    wtbox = Listbox(frame,background="Black", fg="white",selectbackground="Gray",highlightcolor="Gray") 
    wtbox.grid(column=3, row=8) 

    tatlbl = Label(frame,text = "TurnAround Time")  
    tatlbl.grid(column=4, row=7) 

    tatbox = Listbox(frame,background="Black", fg="white",selectbackground="Gray",highlightcolor="Gray") 
    tatbox.grid(column=4, row=8) 

    ctlbl = Label(frame,text = "Completion Time")  
    ctlbl.grid(column=5, row=7) 

    ctbox = Listbox(frame,background="Black", fg="white",selectbackground="Gray",highlightcolor="Gray") 
    ctbox.grid(column=5, row=8) 
    

    gen(idbox)

  
    #this button will delete the selected item from the list   
  
    btn = Button(frame, text = "delete", command = lambda : delete(),pady=10,width=10,bg="Black",fg="White",font=(20) )#listbox=idbox : listbox.delete(ANCHOR))    
    btn.grid(column=2, row=9,padx=5, pady=10)

    delbtn = Button(frame, text = "calculate", command = lambda : insert(),pady=10,width=10,bg="Black",fg="White",font=(20))    
    delbtn.grid(column=3, row=9, padx=5, pady=15)
    
    
    clrbtn = Button(frame, text = "clear", command = lambda : Schclear(),pady=10,width=10,bg="Black",fg="White",font=(20))    
    clrbtn.grid(column=4, row=9, padx=5, pady=15)
    #BackBtn=Button(top2,text="Back", pady=10,width=10,bg="Black",fg="White",font=(20))
    #BackBtn.grid(column=5, row=10, padx=5, pady=15)
    
    radio()
#_______________________________________Schedule.PY________________________________
#***********************************************************************************
#____________________________________Memory.py______________________

def bestFit(blockSize, m, processSize, n): 
    bk = blockSize.copy()
    # Stores block id of the block  
    # allocated to a process  
    allocation = [-1] * n  
      
    # pick each process and find suitable  
    # blocks according to its size ad  
    # assign to it 
    for i in range(n): 
          
        # Find the best fit block for 
        # current process  
        bestIdx = -1
        for j in range(m): 
            if blockSize[j] >= processSize[i]: 
                if bestIdx == -1:  
                    bestIdx = j  
                elif blockSize[bestIdx] > blockSize[j]:  
                    bestIdx = j 
  
        # If we could find a block for  
        # current process  
        if bestIdx != -1: 
              
            # allocate block j to p[i] process  
            allocation[i] = bestIdx  
  
            # Reduce available memory in this block.  
            blockSize[bestIdx] -= processSize[i] 
  
    #print("Process No. Process Size  Block no.") 
    Memorydisplay()
    for i in range(n): 
        #print(i + 1, "          ", processSize[i], end = "           ")  
        idbox.insert(i,  i + 1)
        psbox.insert(i , processSize[i])
        
        if allocation[i] != -1:  
            bnbox.insert(i, str(allocation[i]+1)+") "+str(bk[allocation[i]])+"-&-"+str(blockSize[allocation[i]]))  
        else: 
            bnbox.insert(i , "Not Allocated")
####################################################################################################
####################################################################################################

# Python3 program for next fit  
# memory management algorithm  
  
# Function to allocate memory to 
# blocks as per Next fit algorithm  
def NextFit(blockSize, m, processSize, n): 
    bk = blockSize.copy()
    # Stores block id of the block  
    # allocated to a process  
  
    # Initially no block is assigned  
    # to any process  
    allocation = [-1] * n  
    j = 0
  
    # pick each process and find suitable blocks  
    # according to its size ad assign to it  
    for i in range(n): 
  
        c = m*n
        # Do not start from beginning  
        while c >= 0: 
  
            if blockSize[j] >= processSize[i]: 
  
                # allocate block j to p[i] process  
                allocation[i] = j  
  
                # Reduce available memory in this block.  
                blockSize[j] -= processSize[i]  
  
                break
  
            # mod m will help in traversing the  
            # blocks from starting block after  
            # we reach the end.  
            j = (j + 1) % m 
            c-= 1
  
   # print("Process No. Process Size Block no.")  
    for i in range(n):  
        idbox.insert(i,  i + 1)
        psbox.insert(i , processSize[i])
        if allocation[i] != -1: 
            bnbox.insert(i, str(allocation[i]+1)+") "+str(bk[allocation[i]])+"-&-"+str(blockSize[allocation[i]])) 
        else: 
            bnbox.insert(i , "Not Allocated") 

####################################################################################################
####################################################################################################

def firstFit(blockSize, m, processSize, n): 
    bk = blockSize.copy()
    # Stores block id of the  
    # block allocated to a process  
    allocation = [-1] * n  
  
    # Initially no block is assigned to any process 
  
    # pick each process and find suitable blocks  
    # according to its size ad assign to it  
    for i in range(n): 
        for j in range(m): 
            if blockSize[j] >= processSize[i]: 
                  
                # allocate block j to p[i] process  
                allocation[i] = j  
  
                # Reduce available memory in this block.  
                blockSize[j] -= processSize[i]  
  
                break
  
   # print(" Process No. Process Size      Block no.") 
    for i in range(n): 
        idbox.insert(i,  i + 1)
        psbox.insert(i , processSize[i])  
        if allocation[i] != -1:  
      #      print(allocation[i] + 1)  
             bnbox.insert(i, str(allocation[i]+1)+") "+str(bk[allocation[i]])+"-&-"+str(blockSize[allocation[i]]))
        else: 
      #      print("Not Allocated") 
             bnbox.insert(i , "Not Allocated") 

####################################################################################################

def Memorydisplay():
    idbox.delete(0,sizeid)
    psbox.delete(0,sizeps)
    bnbox.delete(0,sizebn)
###########################
def Memorygen(idbox):
    
        label3 = Label(top1,text="Memory Proccesses  " , font=(28),pady=10)
        label3.grid(column=2, row=1)
        
        frame = Frame(top1,width=400, height=300,borderwidth = 3,background="gray")
        frame.grid(column=2, row=3 , sticky="nsew"  )
    
        label6 = Label(frame,text="Enter Process Size: " ,font=(28),pady=10)
        label6.grid(column=1, row=2)
        pps=Entry(frame,width=30)
        pps.grid(column=1, row=3, padx=5)
        
        label7 = Label(frame,text="Enter Block Size: " ,font=(28),pady=10)
        label7.grid(column=2, row=2 , padx=5, pady=5)
        pbn=Entry(frame,width=30)
        pbn.grid(column=2, row=3, padx=5)
       
        addbtn = Button(frame, text = "insert", command = lambda :  Memoryadd(pps.get(),pbn.get(),pps,pbn),
                                                pady=10,width=10,bg="Black",fg="White",font=(20))
                                                                 
        ###memindex = ret_memindex(memindex)
        addbtn.grid(column=4, row=3 , padx= 15) 
    

# add(idtxt,ppstxt,pbntxt,pid,pps,pbn):               
def Memoryadd(ppstxt,pbntxt,pps,pbn):
        global memindex
        #print("before" , memindex)
        #idadd(idtxt,memindex)
	
        if not (ppstxt=="" or ppstxt==" "):
            if ppstxt.isdecimal():
                Memorypsadd(ppstxt,memindex)
                pps.delete(0, 'end') 
            else:
                 messagebox.showinfo("Invalid Input Process", "Insert only numbers")
        if not (pbntxt=="" or pbntxt==" "):
            if pbntxt.isdecimal():
                Memorybnadd(pbntxt,memindex)
                pbn.delete(0, 'end')
            else:
                 messagebox.showinfo("Invalid Input Block", "Insert only numbers")
        if (ppstxt=="" or ppstxt==" ") and (pbntxt=="" or pbntxt==" "):
             messagebox.showinfo("Empty Input", "Nothing to Insert")
        #pid.delete(0, 'end')
        memindex = memindex+1
        #print("after" , memindex)
        
def Memoryidadd(idtxt,memindex):
     idbox.insert(memindex,idtxt)
        
def Memorypsadd(pstxt,memindex):
     inppsbox.insert(memindex,pstxt)

def Memorybnadd(bntxt,memindex):
     inpbnbox.insert(memindex,bntxt)

#def psadd(pstxt,memindex):
 #    psbox.insert(memindex,pstxt)

#def bnadd(bntxt,memindex):
 #    bnbox.insert(memindex,bntxt)


def Memoryinsert():              #inserting in array from listbox
    global sizeid
    global sizeps
    global sizebn
    idbox.delete(0 , END)
    psbox.delete(0, END)
    bnbox.delete(0, END)
    blockSize.clear()
    processSize.clear()
    
    sizeid = int(idbox.size())
    #print(idbox.get(0,sizeid))
    
    #for i in range(0,sizeid):
        #temp = int(idbox.get(i))
        #process.append(temp)
                                       
    sizeps = int(inppsbox.size())
    #print(btbox.get(0,sizebt))
    
    for i in range(0,sizeps):
        temp1 = int (inppsbox.get(i))
        if temp1 != 0 :
            processSize.append(temp1)
    
    sizebn = int(inpbnbox.size())
    #print(atbox.get(0,sizeat))
    
    for i in range(0,sizebn):
        temp2 = int(inpbnbox.get(i))
        blockSize.append(temp2)
    
    m = len(blockSize)  
    n = len(processSize)
    if sizeps==0 or sizebn==0:
        messagebox.showinfo("Empty list", "Complete Data to calculate")
    else:
         Memorycal(blockSize, m, processSize, n)
        
    
def MemoryClrOnCal():
    idbox.delete(0 , sizeps)
    psbox.delete(0, sizeps)
    bnbox.delete(0, sizebn)

def Memorycal(blockSize, m, processSize, n):
    global memselection
    if memselection == 1:
        firstFit(blockSize, m, processSize, n) 
    elif memselection == 2:
        NextFit(blockSize, m, processSize, n) 
    elif memselection == 3:
        bestFit(blockSize, m, processSize, n) 
    else:
        messagebox.showinfo("Nothing Selected", "Please, select Algorithm to calculate")
        
        
def Memorypsdelete():
    memindex = inppsbox.curselection()
    if not memindex:
        messagebox.showinfo("Nothing Selected", "Select something to delete")
    else:
        inppsbox.delete(memindex)
    
def Memorybndelete():
    memindex = inpbnbox.curselection()
    if not memindex:
        messagebox.showinfo("Nothing Selected", "Select something to delete")
    else:
        inpbnbox.delete(memindex)
    

def Memoryclear():
    
    idbox.delete(0 , sizeps)
    psbox.delete(0, sizeps)
    bnbox.delete(0, sizebn)
    inppsbox.delete(0, sizeps)
    inpbnbox.delete(0,sizebn)
    blockSize.clear()
    processSize.clear()
    
def Memorysel():
    global memselection
    memselection = var.get()
    #print(memselection)
    
def Memoryradio():
    
    global var
    var = IntVar()
    
    exlbl = Label(top1,text = "" )  
    exlbl.grid(column=0, row=4)
    
    
    frame = Frame(top1,width=400, height=300,borderwidth = 3,background="black" )
    frame.grid(column=2, row=5 , sticky="nsew"  )
    
    rblbl = Label(frame,text = "Select Process",bg="Black",fg="White",font=(20))  
    rblbl.grid(column=0, row=4 )
    
    R1 = Radiobutton(frame, text="First Fit", variable=var, value=1,command= Memorysel , anchor = W , width= 10 )
    R1.grid(column=1, row=4, padx=20)

    R2 = Radiobutton(frame, text="Next Fit", variable=var, value=2,command= Memorysel, anchor = W, width= 10)
    R2.grid(column=2, row=4, padx=20)

    R3 = Radiobutton(frame, text="Best Fit", variable=var, value=3,command= Memorysel, anchor = W, width= 10)
    R3.grid(column=3, row=4, padx=20)

    
def Memorymaincomp():
    global idbox
    global psbox
    global bnbox
    global inppsbox
    global inpbnbox
   
    tbl = Label(top1 ,text = "" ) 
    tbl.grid(column=1,row=7   )
    
    frame1 = Frame(top1, borderwidth=2, relief='ridge')
    frame1.grid(column=2, row=8,sticky="nsew")
    
    idlbl = Label(frame1 ,text = "Proccess ID")  
    idlbl.grid(column=3, row=6) 

    idbox = Listbox(frame1 ,background="Black", fg="white",selectbackground="Gray",highlightcolor="Gray") 
    idbox.grid(column=3, row=7) 

    pslbl = Label(frame1,text = "Prcoess Size")  
    pslbl.grid(column=4, row=6) 

    psbox = Listbox(frame1 ,background="Black", fg="white",selectbackground="Gray",highlightcolor="Gray") 
    psbox.grid(column=4, row=7) 

    bnlbl = Label(frame1 ,text = "Block Allocated-&-Left")  
    bnlbl.grid(column=5, row=6) 

    bnbox = Listbox(frame1 ,background="Black", fg="white",selectbackground="Gray",highlightcolor="Gray") 
    bnbox.grid(column=5, row=7) 
    
    inppslbl = Label(frame1,text = "Prcoess Size")  
    inppslbl.grid(column=1, row=6) 

    inppsbox = Listbox(frame1 ) 
    inppsbox.grid(column=1, row=7) 

    inpbnlbl = Label(frame1 ,text = "Block Size")  
    inpbnlbl.grid(column=2, row=6) 

    inpbnbox = Listbox(frame1 ) 
    inpbnbox.grid(column=2, row=7) 

    Memorygen(idbox)

 
    #this button will delete the selected item from the list   
  
    psdelbtn = Button(frame1, text = "delete", command = lambda :  Memorypsdelete(),pady=10,width=10,bg="Black",fg="White",font=(20) )#listbox=idbox : listbox.delete(ANCHOR))    
    psdelbtn.grid(column=1, row=10,padx=10, pady=10)
    
    bndelbtn = Button(frame1, text = "delete", command = lambda :  Memorybndelete(),pady=10,width=10,bg="Black",fg="White",font=(20) )#listbox=idbox : listbox.delete(ANCHOR))    
    bndelbtn.grid(column=2, row=10, padx=10, pady=15)

    calbtn = Button(frame1, text = "calculate", command = lambda :  Memoryinsert(),pady=10,width=10,bg="Black",fg="White",font=(20))    
    calbtn.grid(column=4, row=10, padx=12, pady=5)
    
    #BackBtn=Button(top1,text="Back", pady=10,width=10,bg="Black",fg="White",font=(20))
    #BackBtn.grid(column=5, row=14, padx=12, pady=15)
    
    ClrBtn=Button(frame1,text="Clear", command = lambda :  Memoryclear(), pady=10,width=10,bg="Black",fg="White",font=(20))
    ClrBtn.grid(column=5, row=10, padx=12, pady=5)
    
    Memoryradio()
#____________________________________Memory.py______________________

#________________________________________MAIN.PY________________________________
def selected(event):
    global uivalue 
    uivalue  = clicked.get()


def openOption():
    if uivalue  == 'Scheduling Algorithms':
        open_Schedulewindow()         
    elif uivalue  == 'Memory Managment Algorithms':
        open_Memorywindow()
   
        
def open_Schedulewindow():
    global top2 
    global dis1
    global dis2
    global dis3

    top2 = Toplevel()
    top2.title("Scheduling")
    top2.geometry("1200x800")
    
    Rframe = Frame(top2,width=200, height=100,borderwidth = 1,background="black")
    Rframe.grid(column=1,row=10)
    
    dis1 =Label(Rframe,text=disvar1 ,font=(10))
    dis2= Label(Rframe,text=disvar2 ,font=(10))
    dis3= Label(Rframe,text=disvar3,font=(10))
    
    EmptyRBox()
    
    maincomp()
    Back(top2)
    

def open_Memorywindow():
    global top1
    top1 = Toplevel()
    top1.title("Memory")
    top1.geometry("1200x600")
    Memorymaincomp()
    Back(top1)
    
def Back(top): 
    BackBtn=Button(top,text="Back", pady=10,width=10,bg="Black",fg="White",font=(20) , command = top.destroy)
    BackBtn.grid(column=2, row=10, padx=50, pady=15)
    
def quit():
    root.destroy()
def combo():
    global clicked
    values = ['Select Algorithm Type',  
              'Scheduling Algorithms', 
              'Memory Managment Algorithms' ]

    clicked = StringVar()
    clicked.set(values[0])
    
    #label3 = Label(root,text="Select Algorithm Type" ,font=(28),pady=10)
    #label3.grid(column=6, row=2)

    drop = OptionMenu(root, clicked , *values, command=selected)
    drop.grid(column=6, row=2, pady=15)
    
    GoBtn=Button(root,text="Go", pady=10,width=20,bg="Black",fg="White",font=(20) , command =  openOption)
    GoBtn.grid(column=6, row=7, padx=50, pady=15)
	
    QuitBtn=Button(root,text="Quit", pady=10,width=20,bg="Black",fg="White",font=(20) , command = root.destroy)
    QuitBtn.grid(column=6, row=8,  pady=15)	
    
 
   
combo()
root.mainloop()
