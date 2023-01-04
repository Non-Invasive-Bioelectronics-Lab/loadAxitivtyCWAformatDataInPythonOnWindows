# Load Axitivty CWA format Data In Python On Windows

## Aim
Load accelerometry data stored in CWA format as used by the Axitivty sensor used by the UK Biobank. This version runs in Python (Anaconda) on Windows. It is based upon https://github.com/activityMonitoring/biobankAccelerometerAnalysis and 
https://github.com/CASSON-LAB/BiobankActivityCSF. There are very few changes, but the previous code only works on Unix.

## Dependencies:
This code was made with:
- Python 3.7.3
- Java 15.0.1
- Windows 10 Enterprise 20H2
- Spyder 4.1.5

It likely runs with other versions, but this hasn't been checked. 

## Install
The main code is in Python and the intention is to provide a Python interface to .cwa files on Windows. However the code uses a Java back-end to actually load and process the data. The Java used needs compiling for your OS and processor before the Python can be run. 

Sync the git folder to your computer. For illustration I assume this is put in C:\Users\Alex\Deskop\load_cwa
Install the Java Development Kit (JDK) from https://www.oracle.com/java/technologies/javase-downloads.html. For development I used the "Windows X64 installer" at https://www.oracle.com/java/technologies/javase-jdk15-downloads.html.

Open PowerShell in the Windows start menu and use this to compile the Java. This is done in the base folder (C:\Users\Alex\Desktop\load_cwa here) not the java folder.
  > cd C:\Users\Alex\Desktop\load_cwa
  
  > javac.exe -cp "./java;./java/JTransforms-3.1-with-dependencies.jar" ./java/*.java

## Run
An example CWA file called example.cwa can be downloaded from https://github.com/digitalinteraction/openmovement/wiki/AX3-GUI. 

In Spyder run:
  > runfile('C:/Users/Alex/Desktop/load_cwa/accProcess.py', wdir='C:/Users/Alex/Desktop/load_cwa', args='./example.cwa') 

This will produce a number of temporary files, including a numpy file example.npy. (If the input file is example.cwa.) You can load this numpy file into Python with the command:
  > data, time = import_npy("example.npy")

This will leave arrays of data and time in your workspace for you to work with. I image most people re-saving these arrays in an easier to use format than .cwa so this code becomes a 'one time conversion' only. Unless working with a very large number of files this is probably more effective than loading the .cwa file every time. 

## Limitations
The base code at https://github.com/activityMonitoring/biobankAccelerometerAnalysis and 
https://github.com/CASSON-LAB/BiobankActivityCSF has lots of functionality for processing and analyzing accelerometer data. I've not tested any of that. The code here is only for loading the data into an arrays called data and time, for you to run your own analyses on. The other analyses may work, but you should check them. 

The second step of import_npy("example.npy") should be automatic, i.e. you run the code and are left with data and time arrays in your Spyder workspace. You can then change the default "deleteIntermediateFiles" argument on line 169 to be False and there won't be lots of temporary files left hanging around. The line for import_npy("example.npy") is in accProcess.py on line 276 but doesn't seem to have an effect for some reason. I'll debug this as some point, but it's not a high priority - unless working on lots of files you probably want to run the conversion once and then just keep and operate the .npy files which will be much faster to load. Maybe I should add a line to save these as hdf5 files? 

For speed and ease this is set up as its own repo. It's not a fork or a branch or https://github.com/activityMonitoring/biobankAccelerometerAnalysis and 
https://github.com/CASSON-LAB/BiobankActivityCSF which is based on, or using them as a sub-module. There's no way to auto-detect/incoperate changes in those repos. We should fix this at some point. 
  
## Changes from base libraries
This code is based upon the existing libraries at https://github.com/activityMonitoring/biobankAccelerometerAnalysis and 
https://github.com/CASSON-LAB/BiobankActivityCSF. The main changes are in acelerometer/devices.py, modifying the java calls to use Windows compatible syntax. 

On Unix the call put together was:
  > java -classpath java:java/JTransforms-3.1-with-dependencies.jar -XX:ParallelGCThreads=1 -XX:ParallelCMSThreads=1 -Xmx4G AxivityAx3Epochs ./example.cwa outputFile:./test-stationaryPoints.csv verbose:False filter:true getStationaryBouts:true epochPeriod:10 stationaryStd:0.013

For Windows this is now: 
  > java -Xmx4G -classpath "java;java/JTransforms-3.1-with-dependencies.jar" AxivityAx3Epochs ./example.cwa outputFile:./test-stationaryPoints.csv verbose:False filter:true getStationaryBouts:true epochPeriod:10 stationaryStd:0.013
(Assuming the input file is called example.cwa. The code builds up this line automatically.)

Some of the flags for garbage collection have been removed. This is fine for a single user Windows environment. If running on a shared Windows server or HPC you might need to look into re-enabling these or it will swamp the system with multiple treads and whoever else is on the server may not be happy!

## How to cite
If you use this code please do cite it. To do this please use DOI: 10.48420/13567541.
