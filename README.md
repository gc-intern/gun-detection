# Gun Detection Using Tensorflow
Utilizing Tensorflow and multiple trained models, these Python-based scripts can access a video feed and detect either pistols (handguns, pistols, etc.) or "long guns" (rifles, etc.). To acheive this, two Tensorflow sessions are ran seperately. The first session detects people and the second session detects guns within the images of detected persons. This method reduces the number of false positives while also maintainig optimal performance times that allow the sessions to be ran with any real-time video feed. The two sessions are diesgined to be ran concurrently with each other.

## Prerequisites
A Minio server and bucket is required to store certain data. The public Minio server 'play.minio.io:9000' can be used with the access_key 'Q3AM3UQ867SPQQA43P2F' and secret_key 'zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG'. More information about Minio Client can be found here: https://docs.minio.io/docs/minio-client-complete-guide.

The scripts can be ran in a docker container, but they can also be ran in an Anaconda Environment as an alternative. One must download Anaconda (https://www.anaconda.com/download/) and set up a new Anaconda environment. This Anaconda environment must have Tensorflow GPU, OpenCV, and the Minio Python Client SDK installed on it. Instructions for setting up all of these can be found here:

  Tensorflow: https://www.tensorflow.org/install/; https://pythonprogramming.net/introduction-use-tensorflow-object-detection-api-tutorial/
  
  OpenCV: https://pythonprogramming.net/loading-images-python-opencv-tutorial/
  
  Minio Pyhton Client SDK: https://docs.minio.io/docs/python-client-quickstart-guide

## Installation
### Docker
It is recommened that these scripts are ran within a docker container. The following commands should be ran to set up the container:

```
git clone https://github.com/sofwerx/assault-rifle-detection.git $HOME/Documents/pistol-detection
```
```
cd $HOME/Documents/pistol-detection
```

```
docker build -t gpu_tf .
```

```
xhost +local:docker
```

```
nvidia-docker run --rm --network host --privileged -it -v ~/.Xauthority:/root/.Xauthority -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$DISPLAY --env="QT_X11_NO_MITSHM=1" -v /dev/video0:/dev/video0  -v $HOME/Documents/pistol-detection/tf_files:/tf_files  --device /dev/snd gpu_tf bash
```

```
cd object_detection
```

For optimization, the object detection code has been split into two seperate scripts that can be run simulatenously, but should be run in seperate instances (on seperate machines). Depending on which instance will be running (which on you want to run), run the following commands:

```
cp /detect_pistol/person-camera-session-one.py .
```
OR
```
cp /detect_pistol/person-camera-session-two.py .
```
then, after either command:
```
wget http://download.tensorflow.org/models/object_detection/faster_rcnn_resnet101_coco_2017_11_08.tar.gz
```

```
tar -xvf faster_rcnn_resnet101_coco_2017_11_08.tar.gz
```


The following commands will require one to enter with the bash command which gun type will be detected. The current options are as follows: 

----------------------------------------------------------------------------------------------------------------------------------------

Select Gun Type:

PISTOL

LONGGUN

----------------------------------------------------------------------------------------------------------------------------------------

The following commands also require one to input the name of their Minio Client, Minio access key, and Minio secret key.

Run session one in its own instance (before session two). (It's not entirely neccesary to run session one before session two on subsequent runs, but it should be done if one wants to use a live video feed and track objects in real time.)

```
python person-camera-session-one.py <absolute path to video feed or rtsp link here> <Minio Client> <Minio access key> <Minio secret key>
```

Session two can be ran simultaneously with session one in a seperate instance.
Choose what gun type is being detected.

```
python person-camera-session-two.py <Gun type> <Minio Client> <Minio access key> <Minio secret key>
```
Here's an example of running the scripts:

```
python person-camera-session-one.py rtsp://184.72.239.149/vod/mp4:BigBuckBunny_175k.mov play.minio.io:9000 Q3AM3UQ867SPQQA43P2F zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG
```

```
python person-camera-session-two.py PISTOL play.minio.io:9000 Q3AM3UQ867SPQQA43P2F zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG
```

### Anaconda Environment
Clone this repo and store it on your machine.

You'll need to make some direct changes to the code in person-camera-session-one.py and person-camera-session-two.py in your clone.

All lines with the comment '#!!!' on it will need to be changed. On those lines there will be path names that point to certain files and locations. You should changes those paths to the absolute paths that point to those same locations but on your own machine.

Also, delete the read_me files in the directories 'Frame' and 'Data'.

Open the Anaconda cmd, activate your environment, change your directory to where the clone is stored, change your directory again to pistol-detection/detect_pistol like this:

```
cd /pistol-detection/detect_pistol
```

and run the scripts. Make sure to run them concurrently but seperately to detect objects in live video feed in real time.

```
python person-camera-session-one.py <absolute path to video feed or rtsp link here> <Minio Client> <Minio access key> <Minio secret key>
```

```
python person-camera-session-two.py <Gun type> <Minio Client> <Minio access key> <Minio secret key>
```

Here's an example:

```
python person-camera-session-one.py rtsp://184.72.239.149/vod/mp4:BigBuckBunny_175k.mov play.minio.io:9000 Q3AM3UQ867SPQQA43P2F zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG
```

```
python person-camera-session-two.py PISTOL play.minio.io:9000 Q3AM3UQ867SPQQA43P2F zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG
```
