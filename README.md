# Deploying a Flask API and MySQL server on with NGINX Ingress in Kubernetes

This is a modified version of (https://github.com/RikKraanVantage/kubernetes-flask-mysql) with Ingress

1. Deploys a Flask API to perform CRUD operations in the MySQL database
2. Deploys a MySQL server on a Kubernetes cluster
3. Deploys an ingress that acts as kind of a reverse proxy to the Flask Webserver


## Getting started
1. Clone the repository
2. Configure `Docker` to use the `Docker daemon` in your kubernetes cluster via your terminal: `eval $(minikube docker-env)`
3. Build a flask-api image with the Dockerfile in this repo: `docker build . -t flask-api`
4. `helm repo add <repo-name> https://raw.githubusercontent.com/Nibamot/mysql-flask-ingress/master`
5. `helm install <custom-chart-name> <repo/chart-name>` to install the helm chart. Once this is done you can check the status of all the resources we introduced, including deployments, services,config-maps, secrets,HPAs etc.
6. This chart can be removed simply by doing a `helm uninstall <custom-chart-name>`
1. Connect to your `MySQL database` by setting up a temporary pod as a `mysql-client`: 
   `kubectl run -it --rm --image=mysql --restart=Never mysql-client -- mysql --host mysql --password=<super-secret-password>`
   make sure to enter the (decoded) password specified in the `flaskapi-secrets.yml`
2. Create the database and table
   1. `CREATE DATABASE flaskapi;`
    2. `USE flaskapi;`
    3. `CREATE TABLE users(user_id INT PRIMARY KEY AUTO_INCREMENT, user_name VARCHAR(255), user_email VARCHAR(255), user_password VARCHAR(255));`
    
## Expose the API
The API can be accessed by exposing it using minikube: `minikube service flask-service`. This will return a `URL`. If you paste this to your browser you will see the `hello world` message. You can use this `service_URL` to make requests to the `API`

## Start making requests
Now you can use the `API` to `CRUD` your database
1. add a user: `curl -H "Content-Type: application/json" -d '{"name": "<user_name>", "email": "<user_email>", "pwd": "<user_password>"}' <service_URL>/create`
2. get all users: `curl <service_URL>/users`
3. get information of a specific user: `curl <service_URL>/user/<user_id>`
4. delete a user by user_id: `curl -H "Content-Type: application/json" <service_URL>/delete/<user_id>`
5. update a user's information: `curl -H "Content-Type: application/json" -d {"name": "<user_name>", "email": "<user_email>", "pwd": "<user_password>", "user_id": <user_id>} <service_URL>/update`



