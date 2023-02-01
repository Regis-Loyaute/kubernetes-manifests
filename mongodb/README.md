## test yaml

kubectl label nodes <node-name> node-role.k3s.io/worker=""
kubectl label nodes <node-name> <label-key>-
kubectl label nodes <node-name> node-role.k3s.io/worker= --overwrite

example:
kubectl label nodes k3s-worker01 node-role.k3s.io/worker-

apiVersion: v1
kind: Service
metadata:
  name: mongo-mongodb
  namespace: youtube
spec:
  ports:
  - port: 27017
    targetPort: 27017
  type: LoadBalancer
  loadBalancerIP: 10.100.10.34
  selector:
    app.kubernetes.io/component: mongodb
    app.kubernetes.io/instance: mongo
    app.kubernetes.io/name: mongodb

##  Install MongoDB with Bitnami

helm install mongo bitnami/mongodb --set architecture="replicaset" --set replicaCount=3 --set global.storageClass="longhorn" --set global.namespaceOverride="youtube" --set persistence.size=1Gi --set auth.rootPassword="bsEs7rvx5bdmJYp"


MongoDB&reg; can be accessed on the following DNS name(s) and ports from within your cluster:

    mongo-mongodb-0.mongo-mongodb-headless.youtube.svc.cluster.local:27017
    mongo-mongodb-1.mongo-mongodb-headless.youtube.svc.cluster.local:27017
    mongo-mongodb-2.mongo-mongodb-headless.youtube.svc.cluster.local:27017

To get the root password run:

    export MONGODB_ROOT_PASSWORD=$(kubectl get secret --namespace youtube mongo-mongodb -o jsonpath="{.data.mongodb-root-password}" | base64 -d)

To connect to your database, create a MongoDB&reg; client container:

    kubectl run --namespace youtube mongo-mongodb-client --rm --tty -i --restart='Never' --env="MONGODB_ROOT_PASSWORD=$MONGODB_ROOT_PASSWORD" --image docker.io/bitnami/mongodb:6.0.4-debian-11-r0 --command -- bash

Then, run the following command:
    mongosh admin --host "mongo-mongodb-0.mongo-mongodb-headless.youtube.svc.cluster.local:27017,mongo-mongodb-1.mongo-mongodb-headless.youtube.svc.cluster.local:27017,mongo-mongodb-2.mongo-mongodb-headless.youtube.svc.cluster.local:27017" --authenticationDatabase admin -u root -p $MONGODB_ROOT_PASSWORD

## mongosh command lines

db.createUser(
{
	user: "youtube-admin",

	pwd: "JEG9oJouat9$F$6",

	roles:[{role: "userAdmin" , db:"youtube"}]})


db.grantRolesToUser('youtube-admin', [{ role: 'readWrite', db: 'youtube' }]);

db.createCollection("MyCollection")

