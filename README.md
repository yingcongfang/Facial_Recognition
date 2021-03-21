# Distributed Image Processing in Cloud Dataproc
This is IDS721 project3. I learned how to use Apache Spark on Cloud Dataproc to distribute a computationally intensive image processing task onto a cluster of machines in this project.
This repository is the content of the storage bucket. 

Reference Lab:[Distributed Image Processing in Cloud Dataproc](https://www.qwiklabs.com/focuses/5834?catalog_rank=%7B%22rank%22%3A7%2C%22num_filters%22%3A0%2C%22has_search%22%3Atrue%7D&parent=catalog&search_id=4914974)

## Workflow

1. Creat VM in Compute Engine
* go to Compute Engine > VM Instances > Create.
* Configure the following fields
![image](https://user-images.githubusercontent.com/61890131/111893073-733af500-89bd-11eb-879a-6aae4d4c388b.png)
* creat VM

2. Install Software on VM
* SSH into the instance
* run the following commands to install Scala and sbt
```
sudo apt-get install -y dirmngr unzip
sudo apt-get update
sudo apt-get install -y apt-transport-https
echo "deb https://dl.bintray.com/sbt/debian /" | \
sudo tee -a /etc/apt/sources.list.d/sbt.list
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 642AC823
sudo apt-get update
sudo apt-get install -y bc scala sbt
```
* Set up the Feature Detector Files
```
sudo apt-get update
gsutil cp gs://spls/gsp124/cloud-dataproc.zip .
unzip cloud-dataproc.zip
cd cloud-dataproc/codelabs/opencv-haarcascade
```
* Launch build
```
sbt assembly
```
3. Create a Cloud Storage bucket and download images
4. Create a Cloud Dataproc cluster
```
MYCLUSTER="${USER/_/-}-qwiklab"
gcloud config set dataproc/region us-central1
gcloud dataproc clusters create ${MYCLUSTER} --bucket=${MYBUCKET} --worker-machine-type=n1-standard-2 --master-machine-type=n1-standard-2 --initialization-actions=gs://spls/gsp010/install-libgtk.sh --image-version=2.0  
```
5. Submit the job to Cloud Dataproc
```
curl https://raw.githubusercontent.com/opencv/opencv/master/data/haarcascades/haarcascade_frontalface_default.xml | gsutil cp - gs://${MYBUCKET}/haarcascade_frontalface_default.xml
cd ~/cloud-dataproc/codelabs/opencv-haarcascade
gcloud dataproc jobs submit spark \
--cluster ${MYCLUSTER} \
--jar target/scala-2.12/feature_detector-assembly-1.0.jar -- \
gs://${MYBUCKET}/haarcascade_frontalface_default.xml \
gs://${MYBUCKET}/imgs/ \
gs://${MYBUCKET}/out/
```

## Sample Output
Input:
![image](https://user-images.githubusercontent.com/61890131/111893040-3838c180-89bd-11eb-8aed-2cb3de84f733.png)

Output:
![image](https://user-images.githubusercontent.com/61890131/111893022-20f9d400-89bd-11eb-9cac-eb439767ed39.png)



