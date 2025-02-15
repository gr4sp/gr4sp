# gr4sp
This repository holds the source code of the Generic Recursive Simulation of Socio-technical Systems for Service Provision (GR4SP) model.
The simulation model uses data for the Victorian electricity system in Australia. But GR4SP's structure can guide the development of similar simulations in other contexts.

In addition to the source code of the main simulation engine, an Agent-based model in Java, this repository holds several Jupyter notebooks for analysing and visualising sets of experimental results.

## Set up Gr4sp

Clone repo:

```git clone https://github.com/angelara/gr4sp.git```

go to the root folder `gr4sp` and run

```
mkdir logs
mkdir csv
mkdir plots
```

To run the model, you need to install JAVA JRE and JDK.
## JAVA
### Ubuntu
```
sudo apt update
sudo apt install default-jre
sudo apt install default-jdk
```

See [this guide](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-18-04) more details on how to setup JAVA in ubuntu
### Windows

follow [these instructions](https://java.com/en/download/help/windows_manual_download.xml)

## Python

Install JPype, Pandas, ipyparallel, SALib, numpy, scipy, matplotlib:

```pip install JPype1 pandas ipyparallel SALib numpy scipy matplotlib```

## compile java files

You can use [InteliJ](https://www.jetbrains.com/idea/). Once installed, open the project clicking on the gr4sp.iml file. You can compile/build and run from InteliJ.

Alternatively, run this command from the root of the repo to make the bash script executable:

```
javac -d classes -cp src/:/mnt/data/gr4sp/experiments/../libraries/bsh-2.0b4.jar:/mnt/data/gr4sp/experiments/../libraries/itext-1.2.jar:/mnt/data/gr4sp/experiments/../libraries/j3dcore.jar:/mnt/data/gr4sp/experiments/../libraries/j3dutils.jar:/mnt/data/gr4sp/experiments/../libraries/jcommon-1.0.21.jar:/mnt/data/gr4sp/experiments/../libraries/jfreechart-1.0.17.jar:/mnt/data/gr4sp/experiments/../libraries/jmf.jar:/mnt/data/gr4sp/experiments/../libraries/mason.19.jar:/mnt/data/gr4sp/experiments/../libraries/portfolio.jar:/mnt/data/gr4sp/experiments/../libraries/vecmath.jar:/mnt/data/gr4sp/experiments/../libraries/postgresql-42.2.6.jar:/mnt/data/gr4sp/experiments/../libraries/opencsv-4.6.jar:/mnt/data/gr4sp/experiments/../libraries/yamlbeans-1.13.jar  $(find src -name *.java)
```

## Set simulation YAML file

The settings for the simulation are specified in YAML format. Which is a human readable file that can be edited to change the simulation settings.

To run the simulation settings stored in the YAML file, the folderOutput variable in the YAML file should point to the main folder containing gr4sp (root folder where the code was cloned).

Run the following command:
```
[src\core\settings\BAUVIC.yaml](src\core\settings\BAUVIC.yaml)
```

This command should have printed the full location path.
Make sure the folderOutput variable at the beginning of the YAML file is set to the correct full path printed by pwd.

For example, if you clone the repo in windows in this folder `c:\\Users\\MyUserName\\Documents\\GitHub\\gr4sp`, then set the first variable in the yaml file [simulationSettings/BAUVIC.yaml](simulationSettings/VIC.yaml) as

```
folderOutput: "C:\\Users\\MyUserName\\Documents\\GitHub\\gr4sp"
```

```

## Install PostGres and load database

Install postgres

```
sudo apt install postgresql postgresql-contrib
```

To create `postgres` username and database

```
sudo -u postgres -i

dropdb postgres
createdb postgres
sudo -u postgres psql
\q
```

To load data into the database in linux run
```
sudo -u postgres pg_restore -U postgres -d postgres < backupDB/databaseName.sql
```
If you are using windows run instead

```
pg_restore.exe -U postgres -d postgres -l backupDB/databaseName.sql
```

:warning: If you already loaded the database and you need to recreate it again using a new different backup file run:
```
sudo -u postgres dropdb postgres
sudo -u postgres createdb postgres
sudo -u postgres pg_restore -U postgres -d postgres < backupDB/databaseName.sql
```


Edit file pg_hba.conf. In ubuntu it can be found at `/etc/postgresql/XX/main/pg_hba.conf` where XX is the PostgreSQL version:

```
 sudo nano /etc/postgresql/XX/main/pg_hba.conf
```

See [this guide](https://linuxize.com/post/how-to-use-nano-text-editor/#opening-and-creating-files) about how to edit files with nano. Scroll down to the end of the file and make sure that all the lines below finish with the word `trust`. The file should be edited so it looks as shown below:

```local   all             postgres                                trust

# "local" is for Unix domain socket connections only
local   all             all                                     trust                                                                                                                                                                         # IPv4 local connections:
host    all             all             127.0.0.1/32            trust                                                                                                                                                                        # IPv6 local connections:
host    all             all             ::1/128                 trust   
```

and then run

```
sudo service postgresql restart
```
These changes make sure that gr4sp access the postgres database without any issues (e.g. username, passwords).

You can run from InteliJ.

Otherwise, make sure you added executable rights to `run_gr4sp.sh` by running

```
chmod +x *.sh
```

and then simply run:

```
./runGr4sp.sh
```
It will run the simulation using the default simulationSettings [simulationSettings/BAUVIC.yaml](simulationSettings/VIC.yaml). Once the simulation is over, the results should appear in the `csv` and `plots` folders.

if you want to run it with the Graphical interface to see the progress of the simulation with the live plots, then run

```
./runGr4spUI.sh

```

alternatively, the command being used is as follows:

```

java -classpath classes/production/gr4sp:libraries/bsh-2.0b4.jar:libraries/itext-1.2.jar:libraries/j3dcore.jar:libraries/j3dutils.jar:libraries/jcommon-1.0.21.jar:libraries/jfreechart-1.0.17.jar:libraries/jmf.jar:libraries/mason.19.jar:libraries/portfolio.jar:libraries/vecmath.jar:libraries/postgresql-42.2.6.jar:libraries/opencsv-4.6.jar:libraries/yamlbeans-1.13.jar core.Gr4spSim

```

## Run Experiments with EMA Workbench

install python3 and pip3 following this [instructions](https://raturi.in/blog/installing-python3-and-pip3-ubuntu-mac-and-windows/)
### Python dependencies
install PIP python package manager:

```
sudo apt update
sudo apt install python3-pip
```
Install JPype, Pandas, ipyparallel, SALib, numpy, scipy, matplotlib:

```
pip3 install JPype1 pandas ipyparallel SALib numpy scipy matplotlib
```

### Set JVM library

open [experiments/settingsExperiments.json](experiments/settingsExperiments.json) file, and edit the file setting the variable `jvmPath` accordingly to `jvmPathWindows` or `jvmPathUbuntu` , making sure that the folders contain the `jvm.dll` or `libjvm.so` library.

### Run EET or Variance-based SA

go to `experiments` folder, edit the experiment you want to run in [experiments/runExperiments.py:26](experiments/runExperiments.py:26) and execute:

```
python3 runExperiments.py
```
## Analyze results

see notebooks in [experiments/notebookGr4sp](experiments/notebookGr4sp)
