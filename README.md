# Creating a Python REST API using sklearn and Docker

In this application, we use the Python machine learning library scikit-learn to make predictions of Iris species given measurements.

The app is presented as a REST API (using Flask-RESTful) that returns JSON data, and the whole thing is packaged up in a Docker container.

Created using the tutorial by [Data Dan](http://www.datadan.io/containerized-data-science-and-engineering-part-2-dockerized-data-science/).

---

### Running It

```bash
sudo docker build --force-rm=true -t pythoniris .
sudo docker run --net host -d --name myiris pythoniris
sudo docker stop myiris
sudo docker rm myiris
```

Head to http://localhost:5000/prediction?slength=0.1&swidth=0.1&plength=0.1&pwidth=0.1 and get a prediction!

Instead of always using sudo, can also create a docker group and run off of that. Run these, then log out and log back in, or run 'newgrp docker'.

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

---

### Pulling a Basic Python Image, Then Creating Your Own

Here's a simple example of getting a Docker image with python already installed, then creating your own image from it that has your code on it, and running that.

https://civisanalytics.com/blog/engineering/2014/08/14/Using-Docker-to-Run-Python/

Run the 'hello-world' Docker image, then take a look at all the images and processes.
```bash
docker run hello-world
docker images
docker ps -a
```

Get a Docker image with python installed and check the version of Python. The first command gets the image from Docker Hub, the second command runs "python --version" on the docker image called 'python', and the third command just prints the contents of the root directory of the Docker image.
```bash
docker pull python
docker run python python --version
docker run python ls
```

Now, let's create our own Docker image using the 'python' image as a starting point, and a small test script to run. First, let's create a new directory.

```bash
mkdir docker-python-test
cd docker-python-test
```

Now, a small test Python script that we'll want to run on our new Docker image.

~/docker-python-test/main.py
```python
#!/usr/bin/env python3

if __name__ == '__main__':
    print("Hello Docker!")
```

Now, we create the Dockerfile which lists how to build the Docker image. For more information on how these files work, check out the [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/) and [Best Practices for Writing Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/).

~/docker-python-test/Dockerfile
```
# DOCKER-VERSION 1.12.3

# use the 'python' image as the source
FROM python

# copy files from the current directory into '/src' on the Docker image
COPY . /src

# default command when running the Docker image
CMD ["python", "/src/main.py"]
```

Now, we build a Docker image called 'stevenrouk/docker-python-test' using that Dockerfile.

```bash
docker build -t stevenrouk/docker-python-test .
```

Let's check the version of Python again on this image, then run our script! We'll run it two different ways: the first way is using the default command in the Dockerfile that we set up, and the second way we'll explicitly tell the Docker image to run our script.

```bash
docker run stevenrouk/docker-python-test python --version
docker run stevenrouk/docker-python-test
docker run stevenrouk/docker-python-test python /src/main.py
```

---

### Creating an Anaconda Docker Image

```bash
docker search continuumio
docker pull continuumio/miniconda3
docker run continuumio/miniconda3 python --version
docerr run -it continuumio/miniconda3
```

Dockerfile
```
# DOCKER-VERSION 1.12.3

# Use the miniconda3 image
FROM continuumio/miniconda3

# Copy files from the current directory into '/src' on the Docker image
COPY . /src

# Install dependencies
RUN apt-get -y update --fix-missing
RUN conda install --name root --file /src/spec-file.txt

# Default command when running the Docker image
CMD ["python", "/src/main.py"]
```

```bash
docker build -t stevenrouk/sklearn-test .
```
