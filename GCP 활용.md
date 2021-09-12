
# VM start script
apt-get update
apt-get install apache2 php php-mysql -y
service apache2 restart

# Cloud Shell 활용( 이미지 카피해서 공개로 변경)
export LOCATION=ASIA
gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID
gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png
gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png

# Kubenetes 실습
1. APIs & Services에서 enabled 확인 Kubernetes Engine API, Container Registry API

Cloud Shell에서
$ export MY_ZONE=us-central1-a
$ gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2

@cloudshell:~ (qwiklabs-gcp-03-7355db387d84)$ gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2
WARNING: Starting in January 2021, clusters will use the Regular release channel by default when `--cluster-version`, `--release-channel`, `--no-enable-autoupgrade`, and `--no-enable-autorepair` flags are not specified.
WARNING: Currently VPC-native is not the default mode during cluster creation. In the future, this will become the default mode and can be disabled using `--no-enable-ip-alias` flag. Use `--[no-]enable-ip-alias` flag to suppress this warning.
WARNING: Starting with version 1.18, clusters will have shielded GKE nodes by default.
WARNING: Your Pod address range (`--cluster-ipv4-cidr`) can accommodate at most 1008 node(s).
WARNING: Starting with version 1.19, newly created clusters and node-pools will have COS_CONTAINERD as the default node image when no image type is specified.
Creating cluster webfrontend in us-central1-a...done.
Created [https://container.googleapis.com/v1/projects/qwiklabs-gcp-03-7355db387d84/zones/us-central1-a/clusters/webfrontend].
To inspect the contents of your cluster, go to: https://console.cloud.google.com/kubernetes/workload_/gcloud/us-central1-a/webfrontend?project=qwiklabs-gcp-03-7355db387d84
kubeconfig entry generated for webfrontend.
NAME         LOCATION       MASTER_VERSION  MASTER_IP      MACHINE_TYPE  NODE_VERSION    NUM_NODES  STATUS
webfrontend  us-central1-a  1.20.8-gke.900  35.193.66.165  e2-medium     1.20.8-gke.900  2          RUNNING



# VPC 생성 command
gcloud compute networks create privatenet --subnet-mode=custom

# subnet 생성
gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24
gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20

# network 리스트 확인
gcloud compute networks list
gcloud compute networks subnets list --sort-by=NETWORK



# 방화벽 정책생성
gcloud compute --project=qwiklabs-gcp-00-91eacb6ae61d firewall-rules create management-allow-icmp-ssh-rdb --direction=INGRESS --priority=1000 --network=managementnet --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0

# 방화벽 정책확인
gcloud compute firewall-rules list --sort-by=NETWORK

# VM생성
gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=privatesubnet-us --image-family=debian-10 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=privatenet-us-vm

 gcloud compute instances list --sort-by=ZONE


# cloud shell에서 vm으로 접속
gcloud compute ssh vm-internal --zone us-central1-c --tunnel-through-iap



