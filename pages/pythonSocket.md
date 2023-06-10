---
layout: page-fullwidth
title: "Python Socket Programming"
meta_title: "Python Socket Programming"
subheadline: "A Large File Transmission Protocol"
teaser: "Large File synchronization in Python"
header:
   image_fullwidth: "header_unsplash_9.jpg"
permalink: "/pythonSocket/"
---
This is a report for file synchronization using python socket programming.

## 1. Abstract

File synchronization is widely applied in applications with the function of providing file downloading and file uploading. Softwares such as Dropbox, Baidu Netdisk, Ali Cloud, have the rudimentary function of file synchronization with breakpoint resume, partially update along with encryption methods to ensure security. To provide file synchronization between devices, network programming enables the interaction between different computers, which mainly containing file transferring protocols. This report will demonstrate the process of implementing a file synchronization application including several transferring protocols between three hosts using python network programming along with the testing methods and results. Also, further improvement plan with be introduced.

# 2. Introduction

## 2.1 Background

File sharing is a fundamental function used in every popular cloud applications, such as Baidu Netdisk, Dropbox, Google Drive. These platforms provide reliable, fast and flexible file transfer between devices and cloud. Users can download files from cloud or upload  files anywhere and anytime as along as network is connected.

## 2.2 Project Requirement

To simulate techniques provided in cloud softwares,  this project is required to implement a ***Large Efficient Flexible and Trusty*** File Sharing application through python network programming. ***Large*** requires that transferring size is up to one GB. ***Efficient*** requires automatic and fast file synchronization between peers along with partially changed file updating and compression. ***Flexible*** means that optional IP addresses of peers are arranged and break point resume transferring is required. More importantly, ***Trusty*** means that errors are prohibited in transferred files and data transmission security is also considered.  

## 2.3 Iiterature Review and  Project Scope

In similar applications, for example Dropbox, file synchronization and sharing are realized by listening changes in specified file directories and uploading the newest files to cloud. Furthermore, this app can access the cloud through any devices with internet connection and download files with reliability [1]. In this way, files can be accessed by multiple devices. These sorts of change listening method and flexible access ability can not only simplify users' daily work but also offers portable file storage method. However, it only provides limited storage capacity, those users with large storage demands cannot be fed by this app.

In this project, p2p transferring modal is applied to synchronize files among these three hosts, UDP socket protocol is used to download files with fast speed and it also plays crucial role in broadcasting file information news to peers. Also, compression is used before each file transmission phases to reduce package size.  To make the code structure concise, object oriented programming is used to distinguish server function and client function.

# 3. Methodology

## 3.1 Proposed Functions and Ideas

There are 2 classes and 7 methods in `main.py`, the detailed functions of each method is shown in the chart below:

1. **Main class**:

| Method                                   | Function                                                     |
| ---------------------------------------- | ------------------------------------------------------------ |
| _argparse()                              | Parse external parameter passing.                            |
| zip_new_file(file_dir, out_partial_name) | Find files added into the directory and partially changed files, compress them into a whole zip file. |
| get_file_mtime()                         | Find the modify time of each file and save them into a dictionary. |
| file_listener1()                         | Listen file information in peer 1.                           |
| file_listener2()                         | Listen file information in peer 2.                           |
| file_downloader()                        | If there are news received in file listener, retrieve new files in peer 1 and peer 2. |
| send_file_info()                         | Broadcast file information in share folder including file number, file modified time dictionary, the number of file add time to peer #1 and peer #2. |

<center>Table 1: Functions of class Main</center>

2. **Server Class**:

|                        Functions                        |                         Descriptions                         |
| :-----------------------------------------------------: | :----------------------------------------------------------: |
| ____init____( self, file_dir, block_size, server_Uport) |                  Initialize a server object                  |
|              get_file_size(self, filename)              |              Get the size of each required file              |
|       get_file_block(self, filename, block_index)       |                 Get each computed file block                 |
|   make_return_file_information_header(self, filename)   | Make the  information header of each requested file containing file name, file size and total block number |
|      make_file_block(self, filename, block_index)       | Pack each block with file block data and information header  |
|                  msg_parse(self, msg)                   | Parse the message client sends to return the file block data and |
|                        run(self)                        | Run this thread by calling run(self), and start to wait for download requests |

<center>Table 2: Functions of class Server</center>

3. **Client Class**: 

| Functions                                                    | Descriptions                                                 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ____init____(self, server_address, server_port, file_dir, filename) | Initialize a client object to download the requested files   |
| make_get_file_information_header(self, filename)             | Pack the file names and operation code                       |
| make_get_fil_block_header(self, filename, block_index)       | Get the file header of each block containing filename and bock index |
| parse_file_information(self, msg)                            | parse the information server sends and return file size, block size and total block number |
| parse_file_block(self, msg)                                  | If get right block, parse the block index, block length, and block data |
| get_file_size(self, filename)                                | Get the size of downloaded file                              |
| unzipFiles(self, addtimes)                                   | Uncompress downloaded files to download directory            |
| run(self)                                                    | Run this thread by calling run(self), and start to retrieve the requested file in the server side |

<center>Table 3: Functions of Client class</center>

4. **Global variables**:

|   Variable name    |                         Description                          |
| :----------------: | :----------------------------------------------------------: |
|     file_names     | A global dictionary to record the modify time and related file name |
|     add_times      | A global counter to record the number of time files are added every time files are added, add this counter by one |
|       finish       |   A boolean to record the finish state of a client thread    |
| modified_file_name | A string to record the partially updated file found in the share folder |

<center>Table 4: Description of global variables</center>

In this application, five threads are initialized to accomplish the whole functions, which are respectively `newFile_detector`, `file_info_sender`, `file_downloader`, `server` and `client`. 

+ `newFile_detector` calls `zip_new_file()` which is a infinite loop to detect new files added into share folder and compress them into a whole zip file to *zip* folder, and it can also detect newly updated file,  compress it to *zip* folder and broadcast the updated file names to peers.

+ `file_info_sender` calls for `send_file_info()` which is a infinite loop to send the file information including `add_times`, `file_names` and `modified_file_name` in local share folder to peers. 

+ `file_downloader` calls `file_downloader()` which is also a infinite loop to listen to the online of the two peers. It is in charge of download the newly added files and updated files in peers' `./share` folder.

+ `server` is a infinite loop in charge of listening file download requests incoming from the other two peers. And make file block to return the download request as fleeting as possible

+ `client` is a thread in charge of downloading each requested file. 

  And it is required to cater for different download demands with file dividing download.

## 3.2 Proposed Protocols

In this application, five threads interact with each other through a communicative protocol. As the finite state machine describes below, the above four threads start simultaneously, with file information interchangeable with each other. Meanwhile, `clilent` thread is used to retrieve the files when ` file_downloader` detect new file added in other peer or updated file is found.

Once a peer is online, `file_info_sender` on this peer will set a connection with other peers, and start to send file number, `add_times`, and `file_names` to peer side. If files are added into `./share` folder of local machine, `new_file_detector` will check them out by comparing the file information with other peers and compress them into `./zip` folder. Also, it increase `add_times` by one, to inform the peers that there are newly added zip file in `./zip` folder. Then, `file_info_sender` will inform other peers the news, so that other peers get the updated file list and the `file_downloader` on them will create a `client` thread to retrieve the newest zip file in `./zip` folder on the local machine. Finally `client` thread will  uncompress the zip files into local `./share` folder. 

UDP socket protocol is used in both file transferring and file information sharing among peers due to its relatively fast transmission speed and connectionless technique. Two ports are used in  this application, which are respectively in charge of sending file information and transferring file.

![](https://img-blog.csdnimg.cn/20210127163644501.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70)

# 4. implementation

## 4.1 Steps of  Implementation

1. To implement the new file detector with continuous inspection, one thread with infinite loop is applied. 

   + As the FSM (Finite State Machine) demonstrates below, firstly, `zip_new_file` with check the local file number with peer file number. If local file number is more than peer file number which indicate new files are added into `./share` folder, then, newly added files will be compressed into a whole zip file, and `add_times` is increased by one. 
   + If file number is equal to peer side and no `modified_file_name` found, it will enter the next phase. To filter updated file, it checks continuous file modify time with a time interval of 1 second. If the two modify time of one file is not the same which indicates changes have occurred in this file, it will update `modified_file_name`, send it to peers and compress the updated file, finally plus `add_times` by one to wait for other peers retrieve the file.

   <img src="https://img-blog.csdnimg.cn/20210127163814810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70" style="zoom:67%;" />


   <center>Image 1: FSM of new_file_detector</center>

2. To complete the file information sharing part, a infinite loop is rendered to share the information of  local machine. 

   + Once the application is started, it will pack the file number, `file_names` dictionary, `add_times` and send them to peers if them are online.
   + Also, to update `modified_file_name` if other peer find updated file, it can receive the modified_file_name sent by other peers.

   <img src="https://img-blog.csdnimg.cn/20210127163849181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70" style="zoom:;" />

   <center>Image 2: FSM of file_info_sender</center>

3. To download the files in other peers, three situations are considered:

   + If peer is online,  `filenames` is not zero and there are new files added in their share folder, then start a `client` thread to retrieve them.
   + If the number of files `filenames` and that in peer's folder along with a larger add_times value which indicates that there finds partially updated file, then start a `client` thread to retrieve the updated file. 
   + If the number of file in `filenames` is zero and `add_times` in peer side is larger than local `add_times`, which indicates this machine has been restarted, then compare the number of files in peer side and that in the local side, if there are more files in peer side, then start a `client` thread to download them.

   <img src="https://img-blog.csdnimg.cn/20210127163924887.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70" alt="subdiagram1 Activity Diagram1" style="zoom:150%;" />

   <center>Image 3: FSM of file_downloader</center>

4. To answer for file download requests from other peers, `server` thread is always there to listen to requesting messages from `client` side. 

   +  It firstly parse the message coming from the requesting peers, then according to the file download request, it divides the whole requested file into blocks.
   +  It then transmit the file block to the requiring peer.

   <img src="https://img-blog.csdnimg.cn/20210127163956695.png"  />

   <center>Image 4: FSM of server thread</center>

5. To download the new files from peer side, `client` thread is initialized to retrieve zipped files in peer's `./zip` into its own `./download` folder, and then unzip the zip package to its `./share` folder. 

   ![image-20201221173032909](https://img-blog.csdnimg.cn/20210127164026208.png)

   <center>Image 5: FSM of client thread</center>


## 4.2 Programming Techniques Used and Difficulties Encountered

### 4.2.1 Main techniques:

1. ***OOP*** (Object Oriented Programming)

   In the application, `server` and `client` are two seperated classes with constructors and data encapsulations. `server` class requires `file_dir`, `block_size`, `server_port` parameters to initialize a `server` instance. Similarly, to obtain different files, `client` class requires  `server_address`, `server_port`, `file_dir`, `filename` parameters to initialize a `client` object. Take the advantage of adaptation of different download requests, `client` thread can be utilized to download disparate files according to the demand.

2. ***Multiple Threading***

   As it stated above, each function of this application is a separated thread, every thread has its own mission to accomplish. To make these threads communicative, global variables are used to hold the property of the file information, and these global variables are shared among threads, which means that if one thread changes a global variable, other threads will get the updated one. To be more concise, multiple threading make the application has multiple functions and burgeoning the utilization of CPU.

### 4.2.2 Difficulties Encountered

1. ***Duplicated File Transmission***

   When designing  the `new_file_detector`, the partially updated file can be indistinguishable with the extracting file, which is triggerred by successive file modify time when uncompressing a zip file. As a consequence, the newly-added files can be misapprehended as a partially-updated file, and can be duplicatedly retransmitted between peers. 

   To solve this dilemma, a process lock is introduced. The global boolean value `finish` is a recorder to record the finish state of a `client` thread. Once a `client` thread is in progress, `finish` becomes *False*, when a unzip process is finished, the value of which becomes *True*. Therefore, the extracting files are not checked when comparing the successive modify time of a file. 

# 5. Testing and Results

## 5.1 Testing Steps

To test the system with reliability, it is required to run the application on a Linux virtual machine (Tiny Core) with python 3.6. To simulate the running environment, 3 VMs are applied which are respectively AS1peer1, AS1peer2 and AS1peer3.

 To prepare for the running environment, the following shell command is used on each VM:

```shell
cd /home/tc
mkdir ./workplace
sudo mount /mnt/sda1 ~/workplace
sudo chown tc /home/tc/workplace
mkdir -p /home/tc/workplace/cw1
```

To remotely run the code, Xftp is applied to transfer `main.py` to `./workplace`. And Xftp is applied to remotely control the VMs. After the preparing of running environment, the working directory shown in Xshell is the image shown below:

<img src="https://img-blog.csdnimg.cn/20210127164100438.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70" alt="working_directory" style="zoom: 67%;" />

<center>Image  : Working directory shown in Xshell</center>

To make use of the disk storage, `./sda` is mounted to `./workplace` by the following command:

```shell
sudo mount /mnt/sda1 ~/workplace
sudo chown tc /home/tc/workplace
```

After preparing the working environment, code is tested step by step.

1. Run the code seperately on three machine by:

   ```shell
   python3 main.py --ip <ip1>,<ip2>
   ```

2. Use soft link command to link a test file with around 10 MB with `./share` folder on VM1. After the operation, the file is compressed into `./zip` folder.

   <img src="https://img-blog.csdnimg.cn/20210127164126332.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70" style="zoom: 67%;" />

<center>Image  : Link a file to ./share folder on VM1</center>

3. Start VM2, it will get this test file.

   <img src="https://img-blog.csdnimg.cn/20210127164146655.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70" style="zoom: 67%;" />

<center>Image  :Start VM2 and retreive the file</center>

4. Start VM3, testfile is also sent to peer3.

   <img src="https://img-blog.csdnimg.cn/20210127164205540.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70" style="zoom: 67%;" />

   <center>Image  : Start VM3 and get the file</center>

5. Add one Videos and folder with 50 small files of size 650 MB to the `./share` folder of VM2. And VM3 will get the test video. After 2 seconds, kill the process in VM1. 

   <img src="https://img-blog.csdnimg.cn/2021012716424718.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70" alt="VM3getVideo" style="zoom:67%;" />

   <center>Image  : VM3 get the folder and </center>

6. Restart VM1, and it will get the files since last offline.

   <img src="https://img-blog.csdnimg.cn/20210127164322912.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70" alt="restartVM1getVideo" style="zoom: 67%;" />

<center>Image  : Restart VM1 and get files</center>

7. Add a test `.txt` file in `./share` folder of VM3, and it will be synchronized to VM1 and VM2. After the Partially update, peer1 and peer2 will get the newest version of the file and replace it with the old one.

   <img src="https://img-blog.csdnimg.cn/2021012716434461.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hoaGhoYW9oYW8=,size_16,color_FFFFFF,t_70" alt="VM2recieveUpdatedfile" style="zoom:67%;" />

   <center>Image  :Peers get the partially modified file</center>

## 5.2 Testing Results and Defects to Optimize

As the testing results shows above, for the efficiency of the application, it takes 20 seconds to transmit the 650 MB files and 12 seconds to extract and compress the files. For the flexibility, no matter which peer the file is added, it can synchronize them into other peers, also, it can detect partial update among the added files and broadcast it to peers to download the newest version. 

However, when referring to defects of the application, it is indicated that when the file is not easy to compress, the speed of file transferring with be constraint, conversely, when the files are easy to compress, it is fast to synchronize the files. Thus, the efficiency of this application is decided by the format of the added files. Further more, network stability is another limitation of transmission speed. With faster network bandwidth, the transferring time will be shorter, in contrast, although UDP protocol leaves out the time of setting up connection with three hand shaking with hosts compared to TCP, precarious network connetion will result in slow transferring speed even package loss. 

# 6. Conclusion

To study deep into the working pattern of file sharing system such as Dropbox, Baidu Netdisk and Google Drive, a python application with network programming is introduced to simulate functions including file synchronization, partially update, and breakpoint resume. The test results is also demonstrated in the above statements, where both advantages and disadvantages are discovered. 

To carry on further study, it is required to  improve the transferring performance of different formats of file without the constraint of compressing. Also, the stability of transferring needs further improvement. An RDT (Reliable Data Transferring) method including ACK and NCK mechanism can be rendered in to the server and client. If package loss happens, client send a NCK to server, so that server can retransmit the losing package.

# References

[1]. What is dropbox [Online]. Available: http://www.dropboxchina.com/what-is-dropbox.html

## The codes are demonstrated below:

```python
# -*- coding = utf-8 -*-
# @Time : 2020-11-11 15:33
# @Author : Danny
# @File : main.py
# @Software: PyCharm
import argparse
import math
import struct
import threading
import time
import zipfile
from os.path import join
from tqdm import tqdm
import os
from socket import *

file_names = {}
add_times = 0
finish = True
modified_file_name = ''
# Two UDP sockets to listen to the other two peers
clientInfo_Socket1 = socket(AF_INET, SOCK_DGRAM)
clientInfo_Socket1.settimeout(1)
clientInfo_Socket2 = socket(AF_INET, SOCK_DGRAM)
clientInfo_Socket2.settimeout(1)
serverInfo_socket = socket(AF_INET, SOCK_DGRAM)
# serverInfo_socket.settimeout(1)


def _argparse():
    print('parsing args...')
    parser = argparse.ArgumentParser()
    parser.add_argument("--ip", "-ip_addr", default='', help="ip addr of peers")
    arg = parser.parse_args()
    return arg


# Find the file added into the directory and compress them
def zip_new_file(file_dir, out_partial_name):
    global add_times
    global file_names
    global modified_file_name
    global finish

    while True:
        file_mtime_dict = get_file_mtime(file_dir)
        morefiles = (len(get_file_mtime(file_dir)) > file_listener1()[0]) and \
                    (len(get_file_mtime(file_dir)) > file_listener2()[0])
        equalfiles = (len(get_file_mtime(file_dir)) == file_listener1()[0]) and \
                     (len(get_file_mtime(file_dir)) == file_listener2()[0])

        # if new files are added into folder, and the current file number is bigger than peer side, add them
        if len(get_file_mtime(file_dir)) > len(file_names) and morefiles:
            finish = False
            print('Detect newly added files...')
            new_file_dict = {}
            server_addtimes = add_times + 1
            time.sleep(1)

            for new_file in list(get_file_mtime(file_dir).keys()):
                find = False
                for file in file_names:
                    if new_file == file:
                        find = True
                        break
                if not find:
                    new_file_dict.update({new_file: os.path.getmtime(new_file)})  # add new added files to newfileList

            file_names.update(new_file_dict)  # add new added files to the original filelist
            print('start compressing newly added files')
            # start to compress files
            zip_file = zipfile.ZipFile(out_partial_name + str(server_addtimes) + '.zip', 'w', zipfile.ZIP_DEFLATED)
            zip_start = time.time()

            # Compress newly added files and leave out root name
            for new_file in list(new_file_dict.keys()):
                print(new_file[8:])
                zip_file.write(new_file, new_file[8:])
            zip_file.close()
            zip_end = time.time()
            add_times += 1  # Update addtimes
            print('Finish compression, compression time: ' + str(zip_end - zip_start))
            finish = True

        # find modified file and compress it to zip directory
        # If modified file name is empty and file number is equal to peers and other processes are not writing files
        # in share directory
        if modified_file_name == '' and finish and equalfiles:
            time.sleep(1)
            file_mtime_dict1 = get_file_mtime(file_dir)
            time.sleep(1)
            file_mtime_dict2 = get_file_mtime(file_dir)
            find = False
            modified_file = ''

            # iterate through the file dictionary and new file dictionary to find the file with changed mtime
            # If the file mtime is not continuously changed which indicate it is not a extracting file,
            # then broadcast to peers, compress it, and add addtimes by 1.
            for file in file_mtime_dict1.keys():
                if file_mtime_dict1[file] != file_mtime_dict2[file]:
                    find = True
                    modified_file = str(file)
                    # update modify time of the partially changed file
                    file_mtime_dict1[file] = file_mtime_dict2[file]
            if find:
                server_addtimes = add_times + 1
                print(modified_file + " has been modified, start to zip it...")
                modified_file_name = modified_file
                # send peers news that the updated file has been found
                clientInfo_Socket1.sendto(modified_file.encode(), (peer_addr1, peer_info_port1))
                clientInfo_Socket2.sendto(modified_file.encode(), (peer_addr2, peer_info_port2))

                zip_file = zipfile.ZipFile(out_partial_name + str(server_addtimes) + '.zip', 'w', zipfile.ZIP_DEFLATED)
                zip_start = time.time()
                # Compress modified file and leave out root name
                zip_file.write(modified_file, modified_file[8:])
                zip_file.close()
                zip_end = time.time()
                add_times += 1  # update addtimes
                print('Finish compressing modified file, compression time: ' + str(zip_end - zip_start))


def get_file_mtime(file_dir):
    file_mtime = {}
    for root, dirs, files in os.walk(file_dir):
        for file in files:
            file_mtime.update({os.path.join(root, file): os.path.getmtime(os.path.join(root, file))})
    return file_mtime


# file_listener1() and file_listener2() are used to check the file information in peer folders...
# if the folder already have the file in the server folder, do not compress them
# apply UDP to broadcast and obtain the fileList along with the file numbers and addtimes in server side
def file_listener1():  # File listener of machine1
    global add_times
    global file_names
    try:
        # Try to connect peer #1
        clientInfo_Socket1.sendto(''.encode(), (peer_addr1, peer_info_port1))
        return_msg, _ = clientInfo_Socket1.recvfrom(20480)
    except:
        time.sleep(1)
        online = False
        # print("Device 1 is not connected...")
        return -1, [], -1, online
    else:
        # parse information
        # print('Connect to device 1...')
        online = True
        file_num_b = return_msg[:4]
        addtimes_b = return_msg[4:8]
        file_mtime_dict_b = return_msg[8:]
        file_num = struct.unpack('!I', file_num_b)[0]
        # print('file number is: ' + str(file_num))
        peer_addtimes = struct.unpack('!I', addtimes_b)[0]
        # print("peer addtimes is: " + str(addtimes))
        file_mtime_dict = eval(file_mtime_dict_b.decode('utf-8'))  # reform the fileList information
        # print('file_list dict is: ' + str(file_mtime_dict))
        return file_num, file_mtime_dict, peer_addtimes, online


# File listener of peer2
def file_listener2():
    global add_times
    global file_names
    try:
        # Try to connect peer #2
        clientInfo_Socket2.sendto(''.encode(), (peer_addr2, peer_info_port2))
        return_msg, _ = clientInfo_Socket2.recvfrom(20480)
    except:
        time.sleep(1)
        online = False
        # print("Device 2 is not connected...")
        return -1, [], -1, online
    else:
        # parse information
        # print('Connect to device 2...')
        online = True
        file_num_b = return_msg[:4]
        addtimes_b = return_msg[4:8]
        file_mtime_dict_b = return_msg[8:]
        file_num = struct.unpack('!I', file_num_b)[0]
        # print('file number is: ' + str(file_num))
        peer_addtimes = struct.unpack('!I', addtimes_b)[0]
        # print("peer addtimes is: " + str(addtimes))
        file_mtime_dict = eval(file_mtime_dict_b.decode('utf-8'))  # reform the fileList information
        return file_num, file_mtime_dict, peer_addtimes, online


def file_downloader():
    global add_times
    global file_names
    while True:
        # If peer 1 is online
        if file_listener1()[3]:
            peer_addtimes = file_listener1()[2]
            peer_file_num = file_listener1()[0]
            peer_file_list = dict(file_listener1()[1])
            # print(peer_file_list)
            # If the number of files in peer1 side is more than that in this machine
            if peer_addtimes > add_times and peer_file_num > len(get_file_mtime(source_dir)) and len(file_names) != 0:
                print('Detect files added in peer 1...')
                # update addtimes and filenames
                file_names = peer_file_list
                add_times = peer_addtimes
                # Start a client thread to download the zip file
                cli = Client(peer_addr1, peer_port1, download_dir, 'zip' + str(add_times) + '.zip')
                cli.start()

            # If peer addtimes is larger than this side and file list length is equal, then retrieve the updated file
            if peer_addtimes > add_times and peer_file_num == len(get_file_mtime(source_dir)) and len(file_names) != 0:
                print('Detect file update in peer 1...')
                # update addtimes
                add_times = peer_addtimes
                # start a client to retrieve the updated file
                cli = Client(peer_addr1, peer_port1, download_dir, 'zip' + str(add_times) + '.zip')
                cli.start()

            # If this app has been killed and restart again, synchronize the file list and addtimes from peer1
            if peer_addtimes > add_times and len(file_names) == 0:
                print("Restart app and reconnected to peer 1...")
                # Update addtimes and filenames to synchronize with peer1
                add_times = peer_addtimes
                file_names = peer_file_list
                print("Files in peer2 are: " + str(peer_file_list.keys()) + " addtimes is: " + str(peer_addtimes))

                # If the number of files is smaller than that in the peer1 side, than retrieve the latest zip file
                if len(get_file_mtime(source_dir)) < len(file_names):
                    print('Start retrieving the latest added files in peer1 since last offline...')
                    cli = Client(peer_addr1, peer_port1, download_dir, 'zip' + str(add_times) + '.zip')
                    cli.start()
                else:
                    print('Files are up to date comparing to peer1...')

        # If peer2 is online, then ask for file download requests
        if file_listener2()[3]:
            peer2_addtimes = file_listener2()[2]
            peer2_file_num = file_listener2()[0]
            peer2_file_list = dict(file_listener2()[1])

            # If the number of files in peer2 side is more than that in this machine
            if peer2_addtimes > add_times and peer2_file_num > len(get_file_mtime(source_dir)) and len(file_names) != 0:
                print('Detect files added in peer 2...')
                # update addtimes and filenames
                file_names = peer2_file_list
                add_times = peer2_addtimes
                # Start a client thread to download the zip file
                cli = Client(peer_addr2, peer_port2, download_dir, 'zip' + str(add_times) + '.zip')
                cli.start()

            # If peer addtimes is larger than this side and file list length is equal, then retrieve the updated file
            if peer2_addtimes > add_times and peer2_file_num == len(get_file_mtime(source_dir)) and len(
                    file_names) != 0:
                print('Detect file update in peer 2...')
                # update addtimes
                add_times = peer2_addtimes
                # start a client to retrieve the updated file
                cli = Client(peer_addr2, peer_port2, download_dir, 'zip' + str(add_times) + '.zip')
                cli.start()

            # If this app has been killed and restart again, synchronize the file list and addtimes from peer1
            if peer2_addtimes > add_times and len(file_names) == 0:
                print("Restart app and reconnected to peer 2...")
                # Update addtimes and filenames to synchronize with peer1
                add_times = peer2_addtimes
                file_names = peer2_file_list
                print("Files in peer2 are: " + str(peer2_file_list.keys()) + " addtimes is: " + str(peer2_addtimes))

                # If the number of files is smaller than that in the peer1 side, than retrieve the latest zip file
                if len(get_file_mtime(source_dir)) < len(file_names):
                    print('Start retrieving the latest added files in peer2 since last offline...')
                    cli = Client(peer_addr2, peer_port2, download_dir, 'zip' + str(add_times) + '.zip')
                    cli.start()
                else:
                    print('Files are up to date comparing to peer2...')


# send file information to client side
def send_file_info():
    global modified_file_name
    print('start sending file information...')
    serverInfo_socket.bind(('', serverInfo_port))
    while True:
        bin_file_mtime = str(get_file_mtime(source_dir)).encode('utf-8')
        try:
            modified_file_name_b, peer_address = serverInfo_socket.recvfrom(102400)
            if modified_file_name_b == b'':
                serverInfo_socket.sendto(struct.pack('!II', len(get_file_mtime(source_dir)), add_times)
                                     + bin_file_mtime, peer_address)

            # if serverInfo_socket receives modified file information, update it
            else:
                modified_file_name = modified_file_name_b.decode()
                print('receive modified file name ' + str(modified_file_name))
        except:
            print('One peer is off line')


class Client(threading.Thread):

    def __init__(self, server_address, server_port, file_dir, filename):
        threading.Thread.__init__(self)
        self.client_socket = socket(AF_INET, SOCK_DGRAM)
        self.file_dir = file_dir
        self.server_address = server_address
        self.server_port = server_port
        self.filename = filename

    def make_get_file_information_header(self, filename):
        operation_code = 0
        header = struct.pack('!I', operation_code)
        header_length = len(header + filename.encode())
        return struct.pack('!I', header_length) + header + filename.encode()

    def make_get_fil_block_header(self, filename, block_index):
        operation_code = 1
        header = struct.pack('!IQ', operation_code, block_index)
        header_length = len(header + filename.encode())
        return struct.pack('!I', header_length) + header + filename.encode()

    def parse_file_information(self, msg):
        header_length_b = msg[:4]
        header_length = struct.unpack('!I', header_length_b)[0]
        header_b = msg[4:4 + header_length]
        client_operation_code = struct.unpack('!I', header_b[:4])[0]
        server_operation_code = struct.unpack('!I', header_b[4:8])[0]
        if server_operation_code == 0:  # get right operation code
            file_size, block_size, total_block_number = struct.unpack('!QQQ', header_b[8:32])
            # md5 = header_b[32:].decode()
        else:
            file_size, block_size, total_block_number = -1, -1, -1

        return file_size, block_size, total_block_number

    def parse_file_block(self, msg):
        header_length_b = msg[:4]
        header_length = struct.unpack('!I', header_length_b)[0]
        header_b = msg[4:4 + header_length]
        client_operation_code = struct.unpack('!I', header_b[:4])[0]
        server_operation_code = struct.unpack('!I', header_b[4:8])[0]

        if server_operation_code == 0:  # get right block
            block_index, block_length = struct.unpack('!QQ', header_b[8:24])
            file_block = msg[4 + header_length:]
        elif server_operation_code == 1:
            block_index, block_length, file_block = -1, -1, None
        elif server_operation_code == 2:
            block_index, block_length, file_block = -2, -2, None
        else:
            block_index, block_length, file_block = -3, -3, None

        return block_index, block_length, file_block

    def get_file_size(self, filename):
        return os.path.getsize(join(self.file_dir, filename))

    def unzipFiles(self, addtimes):
        global add_times
        global file_names
        global finish
        print('Start extracting files...')
        startTime = time.time()
        file_path = './download/zip'
        des_dir = './share'

        # If there exits the required file unzip it
        if os.path.exists(file_path + str(addtimes) + '.zip'):
            zipFile = zipfile.ZipFile(file_path + str(addtimes) + '.zip', 'r')
            for file in zipFile.namelist():  # extract files in the download folder
                zipFile.extract(file, des_dir)
            zipFile.close()

            endTime = time.time()
            print('Extracting time is: ' + str(endTime - startTime))
            print('Client end. ')
            finish = True
        else:
            print('Requested file is in another peer...')
            # Refresh add_times and file_names
            add_times = add_times - 1
            file_names = ''

    def run(self):
        global finish
        print('Download start!')
        finish = False
        startTime = time.time()

        # Get file information
        print('start to download the ' + str(self.filename[-5]) + ' times added file...')

        self.client_socket.sendto(self.make_get_file_information_header(self.filename),
                                  (self.server_address, self.server_port))
        msg, _ = self.client_socket.recvfrom(102400)
        file_size, block_size, total_block_number = self.parse_file_information(msg)

        if file_size > 0:
            print('Filename:', self.filename)
            print('File size:', file_size)
            print('Block size:', block_size)
            print('Total block:', total_block_number)
            # print('MD5:', md5)

            # Creat a file
            f = open(join(self.file_dir, self.filename), 'wb')

            # Start to get file blocks
            for block_index in tqdm(range(total_block_number)):
                self.client_socket.sendto(self.make_get_fil_block_header(self.filename, block_index),
                                          (self.server_address, self.server_port))
                msg, _ = self.client_socket.recvfrom(block_size + 100)
                block_index_from_server, block_length, file_block = self.parse_file_block(msg)
                f.write(file_block)
            f.close()

            # Check the MD5
            file_size_download = self.get_file_size(self.filename)
            if file_size_download == file_size:
                print('Downloaded file is completed.')
            else:
                print('Downloaded file is broken.')
        else:
            print('No such file.')
        endTime = time.time()
        print('Transfer time: ' + str(endTime - startTime))
        if self.filename[-4:] == '.zip':
            self.unzipFiles(self.filename[-5:-4])


class Server(threading.Thread):

    def __init__(self, file_dir, block_size, server_Uport):
        threading.Thread.__init__(self)
        self.server_socket = socket(AF_INET, SOCK_DGRAM)
        self.server_Uport = server_Uport
        self.file_dir = file_dir
        self.block_size = block_size

    def get_file_size(self, filename):
        return os.path.getsize(join(self.file_dir, filename))

    def get_file_block(self, filename, block_index):
        # global block_size
        f = open(join(self.file_dir, filename), 'rb')
        f.seek(block_index * self.block_size)
        file_block = f.read(self.block_size)
        f.close()
        return file_block

    def make_return_file_information_header(self, filename):
        # global block_size
        if os.path.exists(join(self.file_dir, filename)):  # find file and return information
            client_operation_code = 0
            server_operation_code = 0
            file_size = self.get_file_size(filename)
            total_block_number = math.ceil(file_size / self.block_size)
            # md5 = self.get_file_md5(filename)
            header = struct.pack('!IIQQQ', client_operation_code, server_operation_code,
                                 file_size, self.block_size, total_block_number)
            header_length = len(header)
            print(filename, file_size, total_block_number)
            return struct.pack('!I', header_length) + header

        else:  # no such file
            client_operation_code = 0
            server_operation_code = 1
            header = struct.pack('!II', client_operation_code, server_operation_code)
            header_length = len(header)
            return struct.pack('!I', header_length) + header

    def make_file_block(self, filename, block_index):
        file_size = self.get_file_size(filename)
        total_block_number = math.ceil(file_size / self.block_size)

        if os.path.exists(join(self.file_dir, filename)) is False:  # Check the file existence
            client_operation_code = 1
            server_operation_code = 1
            header = struct.pack('!II', client_operation_code, server_operation_code)
            header_length = len(header)
            return struct.pack('!I', header_length) + header

        if block_index < total_block_number:
            file_block = self.get_file_block(filename, block_index)
            client_operation_code = 1
            server_operation_code = 0
            header = struct.pack('!IIQQ', client_operation_code, server_operation_code, block_index, len(file_block))
            header_length = len(header)
            # print(filename, block_index, len(file_block))
            return struct.pack('!I', header_length) + header + file_block
        else:
            client_operation_code = 1
            server_operation_code = 2
            header = struct.pack('!II', client_operation_code, server_operation_code)
            header_length = len(header)
            return struct.pack('!I', header_length) + header

    def msg_parse(self, msg):
        header_length_b = msg[:4]
        header_length = struct.unpack('!I', header_length_b)[0]
        header_b = msg[4:4 + header_length]
        client_operation_code = struct.unpack('!I', header_b[:4])[0]

        if client_operation_code == 0:  # get file information
            filename = header_b[4:].decode()
            return self.make_return_file_information_header(filename)

        if client_operation_code == 1:
            block_index_from_client = struct.unpack('!Q', header_b[4:12])[0]
            filename = header_b[12:].decode()
            return self.make_file_block(filename, block_index_from_client)

        # Error code
        server_operation_code = 400
        header = struct.pack('!II', client_operation_code, server_operation_code)
        header_length = len(header)
        return struct.pack('!I', header_length) + header

    def run(self):
        print('Service start!')
        self.server_socket.bind(('', self.server_Uport))
        while True:
            # self.get_fileList_info()
            msg, client_address = self.server_socket.recvfrom(10240)  # Set buffer size as 10kB
            return_msg = self.msg_parse(msg)
            self.server_socket.sendto(return_msg, client_address)


if __name__ == '__main__':
    start = time.time()
    args = _argparse()
    peer_addr1 = args.ip.split(',')[0]
    print(str(peer_addr1))
    peer_port1 = 22001
    peer_info_port1 = 22002
    peer_addr2 = args.ip.split(',')[1]
    print(str(peer_addr2))
    peer_port2 = 22001
    peer_info_port2 = 22002
    download_dir = 'download'
    server_file_dir = 'zip'
    block_size = 20480
    server_port = 22001
    serverInfo_port = 22002
    source_dir = './share'
    zip_dir = './zip/zip'

    if not os.path.exists('share'):
        os.makedirs('share')
    if not os.path.exists('download'):
        os.makedirs('download')
    if not os.path.exists('zip'):
        os.makedirs('zip')

    # initialize a server obj
    server = Server(server_file_dir, block_size, server_port)
    server.start()

    # Initialize a file detector
    newFile_detector = threading.Thread(target=zip_new_file, args=(source_dir, zip_dir,))
    newFile_detector.start()
    # Initialize a file information sender
    file_info_sender = threading.Thread(target=send_file_info)
    file_info_sender.start()
    # Initialize a file downloader to listen to peer1
    file_downloader = threading.Thread(target=file_downloader())
    file_downloader.start()
```