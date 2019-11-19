# Docker Misc Documentation
## Note: 2019-08-12:
Docker and Cisco AnyConnect client fatally conflict with each other, and have for several years with no fix yet... If Docker fails to start properly, you may need to
1. Disable Cisco AnyConnect service (from TaskManager, etc)
2. Restart (possibly reinstall) Docker
3. Re-enable Cisco AnyConnect
Repeat as necessary...

## Installing Docker is Easy
1. Create a Docker Hub account if you don't already have one
2. Find DockerCE online from Docker Hub, download, and install.
	* It does currently require Windows Professional's Hyper-V (which work laptops have). This will change I believe with Windows Service for Linux updates.
	
## Usage
1. Write a dockerfile
	* Find a template online or use a team template. For Python checkout colin449/batchpython in Dockerhub 
2. Navigate to the directory in your command line <br/><br/>
	`cd ./Some_Folder/Container_Folder/` 
3. Run the following to build (compile, etc) your container. Where --tag= specifies your container name <br/><br/>
	`docker build --tag=testpython .` *don't forget the period, and you may need sudo*
4. Run the following to run the container <br/><br/>
	`docker run testpython`<br/><br/> 
	*potentially with lots of additional arguments*
	https://docs.docker.com/engine/reference/run/
	
#### More examples
Remove on stop, name, mount a directory (note the :/app/projects destination), run python, run the script in mounted directory
`docker run --rm --name=dailyForecast -v /home/USV_vm/vm-iaprod-ubuntu-1/Projects/Forecasting/DailyForecast:/app/projects batchpython1 python /app/projects/combinedInventoryDaily.py`









# Container Building and Compilation for Python Use
To build a container, you need Docker (or another software, but as of writing that's the global standard).
There's a lot to what you can do with docker, but often it will look something like this:
#### Make a new empty folder where you are planning to work
#### Write a dockerfile (note there is no extension (no .txt), just 'Dockerfile')
Here's an example. It ain't elegant but it's easy to follow: Note python:3.6-slim will eventually not be the cutting edge, you'll want to check what you want, that works with the latest packages as are installed.
```
FROM python:3.6.8-slim

WORKDIR /app

COPY . /app

RUN apt-get -y update  && apt-get install -y \
  python3-dev \
  apt-utils \
  python-dev \
  build-essential \
&& rm -rf /var/lib/apt/lists/*

# not a pretty as a requirements.txt, but quick to follow
RUN pip install --upgrade setuptools
RUN pip install cython
RUN pip install numpy
RUN pip install scipy
RUN pip install matplotlib
RUN pip install pystan
RUN pip install fbprophet
RUN pip install azure
RUN pip install scikit-learn
# add additional packages here with the same pip syntax as above, as needed

CMD ["python"]
```
When this container runs, it is designed to be told to .py script as part of the run command, as you'll see.
Optionally, use this instead if you just want to run just one .py script with the container: <br/><br/>
`CMD ["python", "your_script.py"]` <br/><br/>
and then you can drop the 'python your_script.py' argument from the `docker run ...` command below.

FYI: A docker file can run as large as 2 GB built in this way. Of course, 
some time and expertise will let you bring it down to 300 MB in some cases, or even less...
<br/><br/>

#### Place your .py scripts in the container's folder, if you haven't done so already
#### Build the Dockerfile...
1. Navigate to the directory in your command line <br/><br/>
`cd ./Some_Folder/Container_Folder/` 
2. Run the following to build (compile, etc) your container. Where --tag= specifies your container name <br/><br/>
	`sudo docker build --tag=testpython2 .`
3. Run the following to run the container <br/><br/>
	`sudo docker run testpython2 python your_script.py`<br/><br/>

### Scheduling with CRON
In the Linux command line of the VM:
```
sudo crontab -e
``` 
Do you need sudo (granting root/administration access)? 
If you can run 'docker run xxx' without sudo, then you can run crontab without sudo.
That is just `crontab -e`. If in doubt, probably include sudo to make sure it runs.
<br/><br/>
This will open up an old-fashioned command line text editor. At the top, or anywhere really, enter a line like this:
```
15 2 * * * docker run testpython2 python your_script.py
```
This command runs your_script out of the Docker container every day at 2:15 am. Google cron syntax if you don't know it. Basically it's two parts, the five numbers _ _ _ _ _ for minute, hour, day, month, day of week, then followed by your command. Use asterix for "everyday" or "every minute" etc.

You can also schedule the entire VM using, in Azure's case, Azure Automation, because if you don't need it running 24/7, why pay for it? 

