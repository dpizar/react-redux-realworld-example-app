# This repostitory contains:
1. React + Redux codebase that adheres to the RealWorld spec and API.
2. A Dockerfile to build the React + Redux codebase into a container and push it to the GCP container registry(gcr.io) by our workflow.
3. Kubernetes Deployment and Service manifests for deploying the React + Redux.
4. Helm chart for deploying the React + Redux into GKE. This is used by our workflow.
5. *Deploy realworld-app to GKE* workflow which packages the React + Redux application into a Dockerfile, pushes the image to the GCP container registry(gcr.io), and uses it to deploy the application into out infrastructure which already exists in our GCP account. Created workflows in this repo: [gke_infrastructure-terraform](https://github.com/dpizar/dpizar_gke-infrastructure-terraform).

# Instructions
## Prerequisites
The GKE infrastructure should already be deployed, please follow this instructions: [gke_infrastructure-terraform](https://github.com/dpizar/dpizar_gke-infrastructure-terraform).

Make sure your container registry host gcr.io is public, otherwise Helm won't be able to pull the React + Redux image. On your GCP account go to *Container Registry* -> *Settings* -> set Visibility to Public. You can set it back to Private once the applications has been deployed, or add the Service Account credentials to HELM(This is something TODO in the future).

## Enable the following Google Cloud APIs
```
gcloud services enable container.googleapis.com
gcloud services enable compute.googleapis.com 
gcloud services enable cloudresourcemanager.googleapis.com
```

## Secrets
1. Create a Service Account and follow the instructions [here](https://docs.github.com/en/actions/deployment/deploying-to-your-cloud-provider/deploying-to-google-kubernetes-engine) to convert credentials to base64 and store them in secret:
```
GKE_SA_KEY
```
2. Get your GCP project ID where your infrastructure was deployed and store it in secret:
```
GKE_PROJECT
```
## Deploy Application
Make a change to any part of the code or manually trigger the workflow *Deploy realworld-app to GKE*.
The React + Redux app should have been deployed to your GKE cluster.

## Manually Deploying Application
After the terraform infrastructure is up and running we can deploy our real world application.

Clone this repository to your GCP account.
Authenticate to cluster 
```
gcloud container clusters get-credentials gke-ycit-tformers-default-dev --region us-central1 
```

Navigate to folder react-redux-chart/
Create a namespace, switch to create namespace and deploy helm chart.
```
kubectl create ns dev
kubectl config set-context --current --namespace=dev

# install helm chart, make sure the name is lowercase
helm install react-redux-chart .
```

# ![React + Redux Example App](project-logo.png)
[![Netlify Status](https://api.netlify.com/api/v1/badges/aa569c8f-ebd5-413e-9fb2-e34facc71873/deploy-status)](https://app.netlify.com/sites/react-redux-realworld/deploys)
[![RealWorld Frontend](https://img.shields.io/badge/realworld-frontend-%23783578.svg)](http://realworld.io)

> ### React + Redux codebase containing real world examples (CRUD, auth, advanced patterns, etc) that adheres to the [RealWorld](https://github.com/gothinkster/realworld-example-apps) spec and API.

<a href="https://stackblitz.com/edit/react-redux-realworld" target="_blank"><img width="187" src="https://github.com/gothinkster/realworld/blob/master/media/edit_on_blitz.png?raw=true" /></a>&nbsp;&nbsp;<a href="https://thinkster.io/tutorials/build-a-real-world-react-redux-application" target="_blank"><img width="384" src="https://raw.githubusercontent.com/gothinkster/realworld/master/media/learn-btn-hr.png" /></a>

### [Demo](https://react-redux-realworld.netlify.app/)&nbsp;&nbsp;&nbsp;&nbsp;[RealWorld](https://github.com/gothinkster/realworld)

Originally created for this [GH issue](https://github.com/reactjs/redux/issues/1353). The codebase is now feature complete; please submit bug fixes via pull requests & feedback via issues.

We also have notes in [**our wiki**](https://github.com/khaledosman/react-redux-realworld-example-app/wiki) about how the various patterns used in this codebase and how they work (thanks [@thejmazz](https://github.com/thejmazz)!)


## Getting started

You can view a live demo over at https://react-redux-realworld.netlify.app/

To get the frontend running locally:

- Clone this repo
- `npm install` to install all req'd dependencies
- `npm start` to start the local server (this project uses create-react-app)

Local web server will use port 4100 instead of standard React's port 3000 to prevent conflicts with some backends like Node or Rails. You can configure port in scripts section of `package.json`: we use [cross-env](https://github.com/kentcdodds/cross-env) to set environment variable PORT for React scripts, this is Windows-compatible way of setting environment variables.
 
Alternatively, you can add `.env` file in the root folder of project to set environment variables (use PORT to change webserver's port). This file will be ignored by git, so it is suitable for API keys and other sensitive stuff. Refer to [dotenv](https://github.com/motdotla/dotenv) and [React](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-development-environment-variables-in-env) documentation for more details. Also, please remove setting variable via script section of `package.json` - `dotenv` never override variables if they are already set.  

### Making requests to the backend API

For convenience, we have a live API server running at https://api.realworld.io/api for the application to make requests against. You can view [the API spec here](https://github.com/GoThinkster/productionready/blob/master/api) which contains all routes & responses for the server.

The source code for the backend server (available for Node, Rails and Django) can be found in the [main RealWorld repo](https://github.com/gothinkster/realworld).

If you want to change the API URL to a local server, start/build the app with the REACT_APP_BACKEND_URL environment variable pointing to the local server's URL (i.e. `REACT_APP_BACKEND_URL="http://localhost:3000/api" npm run build`)


## Functionality overview

The example application is a social blogging site (i.e. a Medium.com clone) called "Conduit". It uses a custom API for all requests, including authentication. You can view a live demo over at https://redux.productionready.io/

**General functionality:**

- Authenticate users via JWT (login/signup pages + logout button on settings page)
- CRU* users (sign up & settings page - no deleting required)
- CRUD Articles
- CR*D Comments on articles (no updating required)
- GET and display paginated lists of articles
- Favorite articles
- Follow other users

**The general page breakdown looks like this:**

- Home page (URL: /#/ )
    - List of tags
    - List of articles pulled from either Feed, Global, or by Tag
    - Pagination for list of articles
- Sign in/Sign up pages (URL: /#/login, /#/register )
    - Use JWT (store the token in localStorage)
- Settings page (URL: /#/settings )
- Editor page to create/edit articles (URL: /#/editor, /#/editor/article-slug-here )
- Article page (URL: /#/article/article-slug-here )
    - Delete article button (only shown to article's author)
    - Render markdown from server client side
    - Comments section at bottom of page
    - Delete comment button (only shown to comment's author)
- Profile page (URL: /#/@username, /#/@username/favorites )
    - Show basic user info
    - List of articles populated from author's created articles or author's favorited articles

<br />

[![Brought to you by Thinkster](https://raw.githubusercontent.com/gothinkster/realworld/master/media/end.png)](https://thinkster.io)
