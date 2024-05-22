# Reference
[colorapp demo](examples/apps/colorapp/README.md)

```bash

# Configure AWS profile
aws configure --profile xyz
export AWS_PROFILE=xyz
export AWS_DEFAULT_REGION=us-east-1
export DOCKER_DEFAULT_PLATFORM=linux/amd64
export AWS_ACCOUNT_ID=767397749712

# Configure app environment
export ENVIRONMENT_NAME=jfapp
export MESH_NAME="jfapp"
export KEY_PAIR_NAME=jfapp
export ENVOY_IMAGE="public.ecr.aws/appmesh/aws-appmesh-envoy:v1.27.3.0-prod"
export CLUSTER_SIZE=2
export CLUSTER_INSTANCE_TYPE="t3.medium"
export SERVICES_DOMAIN=jfapp.local
export COLOR_GATEWAY_IMAGE="${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/gateway:latest"
export COLOR_TELLER_IMAGE="${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/colorteller:latest"

# change to examples directory
cd examples

# Setup infrastructure
./infrastructure/vpc.sh

# Setup App Mesh
./infrastructure/appmesh-mesh.sh

# ECS Cluster
./infrastructure/ecs-cluster.sh

# Deploy the apps
cd apps/colorapp/src/colorteller/
./deploy.sh

cd -

cd apps/colorapp/src/gateway
./deploy.sh

cd -

cd apps/colorapp/servicemesh 
./appmesh-colorapp.sh

cd -
./create-task-defs.sh
# Introducing Cloud Map support for AWS App Mesh
# https://github.com/aws/aws-app-mesh-examples/tree/main/walkthroughs/howto-servicediscovery-cloudmap

# export RESOURCE_PREFIX=$ENVIRONMENT_NAME

# Deploy task definition and services
# cd apps/colorapp/ecs
# cd ../walkthroughs/howto-servicediscovery-cloudmap

# useful
ws ssm get-parameter --name "/aws/service/ecs/optimized-ami/amazon-linux-2/recommended/image_id"
aws cloudformation delete-stack --stack-name jfapp-ecs-cluster --profile xyz 

# modified ./deploy.sh
docker build --platform=linux/amd64 --build-arg GO_PROXY=$GO_PROXY -t $COLOR_TELLER_IMAGE ${DIR}
