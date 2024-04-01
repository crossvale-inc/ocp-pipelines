<style>
  
  input[type=text], select, textarea {
    width: 100%;
    padding: 12px;
    border: 1px solid #ccc;
    border-radius: 4px;
    resize: vertical;
  }

  input[type=button] {
    background-color: #F50000;
    color: white;
    padding: 12px 20px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    
  }

  input[type=button]:hover {
    background-color: #751515;
  }
  
  .header {
    padding: 10px 16px;
    background: #ffffff;
  }

  .markdown-body .headerlink {
    color: #751515;
  }

  .sticky {
    position: fixed;
    top: 0;
    width: 100%;
  }

  .mr-2 {
    margin-right: var(--base-size-8, 8px) !important;
  }

  .octicon {
    display: inline-block;
    overflow: visible !important;
    vertical-align: text-bottom;
    fill: currentColor;
  }

  .markdown-alert {
    padding-left: 16px;
    margin-bottom: 16px;
    color: inherit;
  }

  .markdown-alert-title {
    display: flex;
    font-weight: var(--base-text-weight-medium, 500);
    align-items: center;
    line-height: 1;
  }

  .markdown-alert-note {
    border-left: .25em solid #1f6feb;
    border-left-color: #1f6feb;
    color: #1f6feb;
  }

  .markdown-alert-caution {
    border-left: .25em solid #da3633;
    border-left-color: #da3633;
    color: #da3633;
  }

  .markdown-alert-important {
    border-left: .25em solid #8957e5;
    border-left-color: #8957e5;
    color: #8957e5;
  }


</style>

<div class="header" id="xvHeader">
<a href="https://crossvale.com">
      <img width="234" height="45" src="https://crossvale.com/wp-content/uploads/2023/09/crossvale-main-logo_300px.png" class="attachment-medium_large size-medium_large wp-image-19157 entered lazyloaded" alt="" data-lazy-src="https://crossvale.com/wp-content/uploads/2023/09/crossvale-main-logo_300px.png" data-ll-status="loaded"><noscript><img width="234" height="45" src="https://crossvale.com/wp-content/uploads/2023/09/crossvale-main-logo_300px.png" class="attachment-medium_large size-medium_large wp-image-19157" alt="" /></noscript></a>
</div>

# XV OCP Pipelines Workshop

The purpose of this workshop is to provide an overview on how to build, deploy and monitor applications running in an Openshift cluster.

To build the applications, *Openshift Pipelines* will be used and the deployment will be done by *Openshift GitOps*.

The configuration management is based on `Helm` and `Kustomize` while the monitoring and alerting of application metrics is done using `Grafana` and `Prometheus`.

## Lab 0 Prepare OCP Cluster for Workshop

The purpose of this Lab is to prepare the Openshift cluster for the exercises.

<div class="markdown-alert markdown-alert-note" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-info mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 8a8 8 0 1 1 16 0A8 8 0 0 1 0 8Zm8-6.5a6.5 6.5 0 1 0 0 13 6.5 6.5 0 0 0 0-13ZM6.5 7.75A.75.75 0 0 1 7.25 7h1a.75.75 0 0 1 .75.75v2.75h.25a.75.75 0 0 1 0 1.5h-2a.75.75 0 0 1 0-1.5h.25v-2h-.25a.75.75 0 0 1-.75-.75ZM8 6a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"></path></svg>Note</p>
  <p dir="auto">This lab is optional and must only be done by the instructor.</p>
</div>

### Lab 0.1 Install Openshift Pipelines Operator

Install the pipelines operator from the OperatorHub:

* Name: Red Hat OpenShift Pipelines
* Version: 1.14.3 provided by Red Hat

![OpenshiftPipelinesOperatorInstall](images/pipelines-operator-install.png)

Click `Install`, select default options and wait until the operator is installed.

### Lab 0.2 Install Openshift GitOps Operator

Install the GitOps operator from the OperatorHub:

* Name: Red Hat OpenShift Pipelines
* Version: 1.12.0 provided by Red Hat Inc

![OpenshiftGitOpsInstall](images/gitops-operator-install.png)

Click `Install`, select default options and wait until the operator is installed.

Optionally, add `admin` role to all users logged to `ArgoCD` if needed for this workshop. Add the following line to the `ArgoCD` YAML located in the `openshift-gitops` namespace:

<div class="markdown-alert markdown-alert-caution" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-stop mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M4.47.22A.749.749 0 0 1 5 0h6c.199 0 .389.079.53.22l4.25 4.25c.141.14.22.331.22.53v6a.749.749 0 0 1-.22.53l-4.25 4.25A.749.749 0 0 1 11 16H5a.749.749 0 0 1-.53-.22L.22 11.53A.749.749 0 0 1 0 11V5c0-.199.079-.389.22-.53Zm.84 1.28L1.5 5.31v5.38l3.81 3.81h5.38l3.81-3.81V5.31L10.69 1.5ZM8 4a.75.75 0 0 1 .75.75v3.5a.75.75 0 0 1-1.5 0v-3.5A.75.75 0 0 1 8 4Zm0 8a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"></path></svg>Caution</p>
  <p dir="auto">Do this only for workshop purposes in a controlled environment.</p>
</div>

```
spec:
  rbac:
    defaultPolicy: 'role:admin'
```

### Lab 0.3 Install Grafana Operator

Create a namespace for the Grafana operator:

```
oc new-project grafana-operator
```

Install the Grafana operator the OperatorHub:

* Name: Grafana Operator
* Version: 5.8.0 provided by Grafana Labs

![GrafanaOperatorInstall](images/grafana-operator-install.png)

Install the grafana operator in the created project:

![GrafanaOperatorInstallMenu](images/grafana-operator-install-menu-edit.png)

Click `Install`, and wait until the operator is installed.

## Generate Your Own Script

Set your personal details for the script:

Image Registry: <input type="text" id="imageRegistry" name="imageRegistry" value="image-registry.openshift-image-registry.svc:5000"/> 

Git Repo: <input type="text" id="gitRepo" name="gitRepo" value="https://github.com/crossvale-inc/ocp-pipelines"/>

Default Git Branch: <input type="text" id="gitBranch" name="gitBranch" value="main"/>

UserName: <input type="text" id="username" name="name" value="user"/>

<input type="button" value="generate" onClick="window.location.reload()"/>

<script>

  var username= document.getElementById("username").value
  var gitRepo= document.getElementById("gitRepo").value
  var gitBranch= document.getElementById("gitBranch").value

  var gitConfigRepo= document.getElementById("gitRepo").value

  var imageRegistry= document.getElementById("imageRegistry").value

  var workNamespace = username + "-devspaces"
  var devNamespace = username + "-development"
  var testNamespace = username + "-test"

  // When the user scrolls the page, execute myFunction
  window.onscroll = function() {myFunction()};

  // Get the header
  var header = document.getElementById("xvHeader");

  // Get the offset position of the navbar
  var sticky = header.offsetTop;

  // Add the sticky class to the header when you reach its scroll position. Remove "sticky" when you leave the scroll position
  function myFunction() {
    if (window.pageYOffset > sticky) {
      header.classList.add("sticky");
    } else {
      header.classList.remove("sticky");
    }
  } 
</script>

## Student Project Preparation

<div class="markdown-alert markdown-alert-caution" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-stop mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M4.47.22A.749.749 0 0 1 5 0h6c.199 0 .389.079.53.22l4.25 4.25c.141.14.22.331.22.53v6a.749.749 0 0 1-.22.53l-4.25 4.25A.749.749 0 0 1 11 16H5a.749.749 0 0 1-.53-.22L.22 11.53A.749.749 0 0 1 0 11V5c0-.199.079-.389.22-.53Zm.84 1.28L1.5 5.31v5.38l3.81 3.81h5.38l3.81-3.81V5.31L10.69 1.5ZM8 4a.75.75 0 0 1 .75.75v3.5a.75.75 0 0 1-1.5 0v-3.5A.75.75 0 0 1 8 4Zm0 8a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"></path></svg>Caution</p>
  <p dir="auto">Confirm with your instructor if this needs to be done. This lab may not be required in your case.</p>
</div>

Create the devspace project:

<div class="highlight"><pre>oc new-project <script>document.write(workNamespace)</script></pre>
</div>

Create the development environment project:

<div class="highlight"><pre>oc new-project <script>document.write(devNamespace)</script></pre>
</div>

Create the test environment project:

<div class="highlight"><pre>oc new-project <script>document.write(testNamespace)</script></pre>
</div>

## Lab 1 - Build Pipeline

Clone the repository in your workspace:

<div class="highlight"><pre>git clone <script>document.write(gitRepo)</script></pre>
</div>

In order to build the application image it is necessary to creathe an Openshift Pipeline.

Before continuing, make sure you are in the work namespace in the CLI:

<div class="highlight"><pre>oc project <script>document.write(workNamespace)</script></pre>
</div>

And in the console, search for your `devspaces` namespace and click on its name:

![OpenshiftProjectSelectionLAb1](images/project-selection-lab1.png)

To create the pipeline, click the `+` in the upper right corner of the Openshift console:

![OpenshiftHeader](images/openshift-header-edit.png)

And paste the following YAML file:

<div class="highlight"><pre>apiVersion: tekton.dev/v1
kind: Pipeline
metadata:
  name: build-pipeline
spec:
  params:
    - default: <script>document.write(gitBranch)</script>
      name: targetRevision
      type: string
  tasks:
    - name: git-clone
      params:
        - name: url
          value: '<script>document.write(gitRepo)</script>'
        - name: revision
          value: $(params.targetRevision)
        - name: refspec
          value: ''
        - name: submodules
          value: 'true'
        - name: depth
          value: '1'
        - name: sslVerify
          value: 'true'
        - name: crtFileName
          value: ca-bundle.crt
        - name: subdirectory
          value: repo
        - name: sparseCheckoutDirectories
          value: ''
        - name: deleteExisting
          value: 'true'
        - name: httpProxy
          value: ''
        - name: httpsProxy
          value: ''
        - name: noProxy
          value: ''
        - name: verbose
          value: 'true'
        - name: gitInitImage
          value: 'registry.redhat.io/openshift-pipelines/pipelines-git-init-rhel8@sha256:5546aaa01993f7fe6829ac83dbe583dff48b2d33e92144af5e09ee004616117a'
        - name: userHome
          value: /home/git
      taskRef:
        kind: ClusterTask
        name: git-clone
      workspaces:
        - name: output
          workspace: shared-devops
    - name: build-application
      params:
        - name: MAVEN_IMAGE
          value: 'registry.redhat.io/ubi8/openjdk-17@sha256:a8165bc2cd5051a96d6937e25ed03155bbd4b731da6e58cebfe2ea83209c16d8'
        - name: GOALS
          value:
            - clean
            - package
            - '-Dmaven.repo.local=$(workspaces.maven-settings.path)'
        - name: MAVEN_MIRROR_URL
          value: ''
        - name: SERVER_USER
          value: ''
        - name: SERVER_PASSWORD
          value: ''
        - name: PROXY_USER
          value: ''
        - name: PROXY_PASSWORD
          value: ''
        - name: PROXY_PORT
          value: ''
        - name: PROXY_HOST
          value: ''
        - name: PROXY_NON_PROXY_HOSTS
          value: ''
        - name: PROXY_PROTOCOL
          value: http
        - name: CONTEXT_DIR
          value: repo/order-service
      runAfter:
        - git-clone
      taskRef:
        kind: ClusterTask
        name: maven
      workspaces:
        - name: source
          workspace: shared-devops
        - name: maven-settings
          workspace: maven-settings
    - name: build-image
      params:
        - name: IMAGE
          value: '<script>document.write(imageRegistry)</script>/$(context.pipelineRun.namespace)/order-service:latest'
        - name: BUILDER_IMAGE
          value: 'registry.redhat.io/rhel8/buildah@sha256:b48f410efa0ff8ab0db6ead420a5d8d866d64af846fece5efb185230d7ecf591'
        - name: STORAGE_DRIVER
          value: vfs
        - name: DOCKERFILE
          value: repo/order-service/src/main/docker/Dockerfile.jvm
        - name: CONTEXT
          value: repo/order-service
        - name: TLSVERIFY
          value: 'true'
        - name: FORMAT
          value: oci
        - name: BUILD_EXTRA_ARGS
          value: ''
        - name: PUSH_EXTRA_ARGS
          value: ''
        - name: SKIP_PUSH
          value: 'false'
      runAfter:
        - build-application
      taskRef:
        kind: ClusterTask
        name: buildah
      workspaces:
        - name: source
          workspace: shared-devops
  workspaces:
    - name: shared-devops
    - name: maven-settings
</pre>
</div>

<div class="markdown-alert markdown-alert-important" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-report mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 1.75C0 .784.784 0 1.75 0h12.5C15.216 0 16 .784 16 1.75v9.5A1.75 1.75 0 0 1 14.25 13H8.06l-2.573 2.573A1.458 1.458 0 0 1 3 14.543V13H1.75A1.75 1.75 0 0 1 0 11.25Zm1.75-.25a.25.25 0 0 0-.25.25v9.5c0 .138.112.25.25.25h2a.75.75 0 0 1 .75.75v2.19l2.72-2.72a.749.749 0 0 1 .53-.22h6.5a.25.25 0 0 0 .25-.25v-9.5a.25.25 0 0 0-.25-.25Zm7 2.25v2.5a.75.75 0 0 1-1.5 0v-2.5a.75.75 0 0 1 1.5 0ZM9 9a1 1 0 1 1-2 0 1 1 0 0 1 2 0Z"></path></svg>Important</p>
  <p dir="auto">Before importing the pipeline make sure that the selected project is your devspace project.</p>
</div>

Paste the pipeline:

![ImportBuildPipelineYAML](images/import-build-pipeline-yaml.png)

And click the `Create` button.

Since `Openshift Pipelines` is run on containers it is required to create two `PersistentVolumeClaims` to allow to share information between pipeline stages.

Create the following YAML in the `devspace` project:

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: maven-settings
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: shared-devops
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

When the resources are successfully create, go back to the `Pipelines` view in the left menu and select **Pipelines** option and click on **build-pipeline** to see the created pipeline.

To start the pipeline build, click on the **Start** option in the **Actions** section of the pipeline:

![StartPipeline](images/build-pipeline-start-edit.png)

Start the pipeline selecting the correct branch and persistent volumes:

<div class="markdown-alert markdown-alert-note" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-info mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 8a8 8 0 1 1 16 0A8 8 0 0 1 0 8Zm8-6.5a6.5 6.5 0 1 0 0 13 6.5 6.5 0 0 0 0-13ZM6.5 7.75A.75.75 0 0 1 7.25 7h1a.75.75 0 0 1 .75.75v2.75h.25a.75.75 0 0 1 0 1.5h-2a.75.75 0 0 1 0-1.5h.25v-2h-.25a.75.75 0 0 1-.75-.75ZM8 6a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"></path></svg>Note</p>
  <p dir="auto">Mind that the git targetRevision may be different in your case.</p>
</div>

![StartPipelineMenu](images/build-pipeline-start-menu.png)

Click the `Start` button and wait until the pipeline is finished, it may take some time while downloading all the maven dependencies.

It is possible to check the Pipeline logs in the **Pipeline Runs** tab on the pipeline, then select the run in progress and click **Logs**.

When the pipeline is finished, all steps should be green and the image is pushed to the specified image registry:

![BuildPipelineResult](images/build-pipeline-result-edit.png)

It should be possible to check the `ImageStreams` in the console:

![BuildPipelineImageStream](images/build-pipeline-image-stream.png)

It is also possible to check the image stream in the CLI:

<div class="highlight"><pre>oc get is -n <script>document.write(workNamespace)</script></pre>
</div>

## Lab 2 - Deployment to Dev with Helm Charts

Clone the git config repository in the local workspace:

<div class="highlight"><pre>git clone <script>document.write(gitConfigRepo)</script></pre>
</div>

Create a branch to store your local changes:

<div class="highlight"><pre>git checkout -b lab2/<script>document.write(username)</script></pre>
</div>

And push the changes to git

<div class="highlight"><pre>git push --set-upstream origin lab2/<script>document.write(username)</script></pre>
</div>

Create the deployment pipeline by following the same steps as in the build pipeline:

<div class="markdown-alert markdown-alert-important" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-report mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 1.75C0 .784.784 0 1.75 0h12.5C15.216 0 16 .784 16 1.75v9.5A1.75 1.75 0 0 1 14.25 13H8.06l-2.573 2.573A1.458 1.458 0 0 1 3 14.543V13H1.75A1.75 1.75 0 0 1 0 11.25Zm1.75-.25a.25.25 0 0 0-.25.25v9.5c0 .138.112.25.25.25h2a.75.75 0 0 1 .75.75v2.19l2.72-2.72a.749.749 0 0 1 .53-.22h6.5a.25.25 0 0 0 .25-.25v-9.5a.25.25 0 0 0-.25-.25Zm7 2.25v2.5a.75.75 0 0 1-1.5 0v-2.5a.75.75 0 0 1 1.5 0ZM9 9a1 1 0 1 1-2 0 1 1 0 0 1 2 0Z"></path></svg>Important</p>
  <p dir="auto">Before importing the pipeline make sure that the selected project is your devspace project.</p>
</div>

<div class="highlight"><pre>apiVersion: tekton.dev/v1
kind: Pipeline
metadata:
  name: deploy-to-dev
spec:
  params:
    - description: Name of the image to be promoted
      name: sourceImage
      type: string
      default: order-service
    - description: Tag of the image to be promoted
      name: sourceImageTag
      type: string
      default: latest
    - description: Name of the ArgoCD application to deploy
      name: applicationToDeploy
      type: string
      default: dev-argocd-helm-<script>document.write(username)</script>
    - description: Build Namespace
      name: buildNamespace
      type: string
      default: <script>document.write(workNamespace)</script>
    - description: Target Namespace
      name: targetNamespace
      type: string
      default: <script>document.write(devNamespace)</script>
  tasks:
    - name: promote-image
      params:
        - name: SCRIPT
          value: 'oc tag $(params.buildNamespace)/$(params.sourceImage):$(params.sourceImageTag) $(params.targetNamespace)/$(params.sourceImage):dev --reference-policy=local'
        - name: VERSION
          value: latest
      taskRef:
        kind: ClusterTask
        name: openshift-client
    - name: deploy-application
      params:
        - name: SCRIPT
          value: |-
            #!/bin/sh

            ARGO_APP=$(params.applicationToDeploy)
            SOURCE_IMAGE=$(params.sourceImage)
            TARGET_NAMESPACE=$(params.targetNamespace)
            SOURCE_IMAGE_TAG=$(params.sourceImageTag)
            SOURCE_IMAGE_NS=$(params.buildNamespace)

            IMAGE_SHA=$(oc get imagestreamtag $SOURCE_IMAGE:$SOURCE_IMAGE_TAG -o jsonpath='{.image.metadata.name}' -n $SOURCE_IMAGE_NS) 

            echo "Deploying application based on image $SOURCE_IMAGE with tag $SOURCE_IMAGE_TAG that points to latest SHA $IMAGE_SHA using $ARGO_APP"

            oc get application $ARGO_APP -n openshift-gitops | grep helm
              
            IS_HELM=$?

            oc get application $ARGO_APP -n openshift-gitops | grep kustomize

            IS_KUSTOMIZE=$?

            if [[ $IS_HELM -eq 0 ]]; then
              
              echo "Applying helm patch"

              oc patch application $ARGO_APP -p '{"operation": {"initiatedBy": {"username": "pipeline"},"sync": {"syncStrategy": null}},"spec":{ "source": {"helm": {"parameters": [{"name": "image.sha","value": "'$IMAGE_SHA'"},{"name": "image.repository","value": "'<script>document.write(imageRegistry)</script>'/'$TARGET_NAMESPACE'/'$SOURCE_IMAGE'"}]}}}}' -n openshift-gitops --type=merge

            elif [[ $IS_KUSTOMIZE -eq 0 ]]; then
              
              echo "Applying kustomize patch"

              oc patch application $ARGO_APP --type='json' -p='[{"op":"replace","path":"/spec/source/kustomize/images/0","value":"quarkus-container-image=<script>document.write(imageRegistry)</script>/'$TARGET_NAMESPACE'/'$SOURCE_IMAGE'@'$IMAGE_SHA'"}]' -n openshift-gitops

              oc patch application $ARGO_APP -p '{"operation": {"initiatedBy": {"username": "pipeline"},"sync": {"syncStrategy": null}}}' -n openshift-gitops --type=merge

            else

              echo "Applying default patch"

              oc patch application $ARGO_APP -p '{"operation": {"initiatedBy": {"username": "pipeline"},"sync": {"syncStrategy": null}}}' -n openshift-gitops --type=merge

            fi
        - name: VERSION
          value: latest
      runAfter:
        - promote-image
      taskRef:
        kind: ClusterTask
        name: openshift-client
</pre>
</div>

The pipeline contains the steps to promthe the image to the development enviroment <script>document.write(devNamespace)</script> and to deploy the application by using  `ArgoCD`. To configure an application, it is necessary to login to `ArgoCD` through the web UI. The hostname is published in the `Route` created by the `Openshift GitOps` Operator:

<div class="markdown-alert markdown-alert-note" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-info mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 8a8 8 0 1 1 16 0A8 8 0 0 1 0 8Zm8-6.5a6.5 6.5 0 1 0 0 13 6.5 6.5 0 0 0 0-13ZM6.5 7.75A.75.75 0 0 1 7.25 7h1a.75.75 0 0 1 .75.75v2.75h.25a.75.75 0 0 1 0 1.5h-2a.75.75 0 0 1 0-1.5h.25v-2h-.25a.75.75 0 0 1-.75-.75ZM8 6a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"></path></svg>Note</p>
  <p dir="auto">If the below command fails, ask your instructor for the ArgoCD URL</p>
</div>

```
oc get route openshift-gitops-server -n openshift-gitops
```

To create the `ArgoCD` application, click on the `Create Application` option in the web UI:

![ArgoCDCreateApplication](images/argo-cd-create-application.png)

And then click on **EDIT AS YAML** option on the upper right corner:

![ArgoCDCreateApplicationMenu](images/argo-cd-create-application-menu-edit.png)

And paste the following YAML:

<div class="highlight"><pre>apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: dev-argocd-helm-<script>document.write(username)</script>
spec:
  destination:
    namespace: <script>document.write(devNamespace)</script>
    server: 'https://kubernetes.default.svc'
  project: default
  source:
    path: helm/quarkus-helm
    repoURL: <script>document.write(gitConfigRepo)</script>
    targetRevision: lab2/<script>document.write(username)</script>
    helm:
      releaseName: order-service-helm-<script>document.write(username)</script>
      valueFiles:
      - /helm/dev-order-service-values.yaml</pre>
</div>

Then click **SAVE** and finally **CREATE**.

Validate that the `ArgoCD` application is created. Mind that `ArgoCD` will set the application status to `Missing` and `OutOfSync`, this makes sense since the application was not deployed yet.

To deploy the application, run the `deploy-to-dev` pipeline. Go to the namespace <script>document.write(workNamespace)</script> and run the `deploy-to-dev` pipeline. Validate that the values are correct:

![DeployToDevRunPipeline](images/deploy-to-dev-pipeline-start.png)

<div class="markdown-alert markdown-alert-note" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-info mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 8a8 8 0 1 1 16 0A8 8 0 0 1 0 8Zm8-6.5a6.5 6.5 0 1 0 0 13 6.5 6.5 0 0 0 0-13ZM6.5 7.75A.75.75 0 0 1 7.25 7h1a.75.75 0 0 1 .75.75v2.75h.25a.75.75 0 0 1 0 1.5h-2a.75.75 0 0 1 0-1.5h.25v-2h-.25a.75.75 0 0 1-.75-.75ZM8 6a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"></path></svg>Note</p>
  <p dir="auto">If the pipeline fails due to permission problems
    <br>Be sure that the permissions of the involved service accounts are correct.
    <br>Execute the commands: <pre>oc adm policy add-role-to-user edit system:serviceaccount:<script>document.write(workNamespace)</script>:pipeline -n <script>document.write(devNamespace)</script>
oc adm policy add-role-to-user edit system:serviceaccount:<script>document.write(workNamespace)</script>:pipeline -n openshift-gitops
oc adm policy add-role-to-user edit system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n <script>document.write(devNamespace)</script></pre> To add the required permissions</p>
</div>

Once the pipeline is finished, `ArgoCD` will change the application status to `Healthy` and `Sync`:


![ArgoCDApplicationHealthy](images/argo-cd-app-sync-healthy.png)

Although `ArgoCD` is reporting the application as `Healthy` and that means that the pod is running correctly, it is possible to validate that the application is running manually. Execute a curl, from any pod within the cluster to the service endpoint and expect to recevied a *HTTP 200* and an empty array as response:

<div class="highlight"><pre>curl -v http://order-service-helm-<script>document.write(username)</script>-quarkus-helm.<script>document.write(devNamespace)</script>:8080/entity/orders</pre>
</div>

<div class="markdown-alert markdown-alert-note" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-info mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 8a8 8 0 1 1 16 0A8 8 0 0 1 0 8Zm8-6.5a6.5 6.5 0 1 0 0 13 6.5 6.5 0 0 0 0-13ZM6.5 7.75A.75.75 0 0 1 7.25 7h1a.75.75 0 0 1 .75.75v2.75h.25a.75.75 0 0 1 0 1.5h-2a.75.75 0 0 1 0-1.5h.25v-2h-.25a.75.75 0 0 1-.75-.75ZM8 6a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"></path></svg>Note</p>
  <p dir="auto">Since <strong>devspaces</strong> is running inside the cluster, it is possible to execute the curl from the IDE terminal.</p>
</div>

Now, try to add a `Route` object to the provided helm chart. 

To do that, add the following yaml inside the folder `helm/quarkus-helm/templates` folder of your git repository branch and name it **route.yaml**:

```
kind: Route
apiVersion: route.openshift.io/v1
metadata:
  name: {{ include "quarkus-helm.fullname" . }}
  labels:
    {{- include "quarkus-helm.labels" . | nindent 4 }}
spec:
  to:
    kind: Service
    name: {{ include "quarkus-helm.fullname" . }}
    weight: 100
  port:
    targetPort: http
  wildcardPolicy: None
```

Commit and push your change and wait until `ArgoCD` detects the application as `OutOfSync`:

![ArgoCDApplicationHealthy](images/argo-cd-app-outofsync-healthy.png)

Click the **SYNC** and then **SYNCRHONIZE** button of the application and wait until `ArgoCD` reports the applications as `Synced` again.

Validate that a route has been created in the dev namespace:

<div class="highlight"><pre>oc get routes -n <script>document.write(devNamespace)</script></pre>
</div>

Try to execute a `curl` to the route endpoint to retrieve the `/entity/orders` and validate that it returns a *HTTP 200* and an empty array.

## Lab 3 - Deployment to Test with Helm Charts

The deployment to test is done following the same principles as the deployment on dev.

Create a `deploy-to-test` pipeline by importing the following yaml in your namespace <script>document.write(workNamespace)</script>:

<div class="markdown-alert markdown-alert-important" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-report mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 1.75C0 .784.784 0 1.75 0h12.5C15.216 0 16 .784 16 1.75v9.5A1.75 1.75 0 0 1 14.25 13H8.06l-2.573 2.573A1.458 1.458 0 0 1 3 14.543V13H1.75A1.75 1.75 0 0 1 0 11.25Zm1.75-.25a.25.25 0 0 0-.25.25v9.5c0 .138.112.25.25.25h2a.75.75 0 0 1 .75.75v2.19l2.72-2.72a.749.749 0 0 1 .53-.22h6.5a.25.25 0 0 0 .25-.25v-9.5a.25.25 0 0 0-.25-.25Zm7 2.25v2.5a.75.75 0 0 1-1.5 0v-2.5a.75.75 0 0 1 1.5 0ZM9 9a1 1 0 1 1-2 0 1 1 0 0 1 2 0Z"></path></svg>Important</p>
  <p dir="auto">Before importing the pipeline make sure that the selected project is your devspace project.</p>
</div>

<div class="highlight"><pre>apiVersion: tekton.dev/v1
kind: Pipeline
metadata:
  name: deploy-to-test
spec:
  params:
    - description: Name of the image to be promoted
      name: sourceImage
      type: string
      default: order-service
    - description: Tag of the image to be promoted
      name: sourceImageTag
      type: string
      default: dev
    - description: Name of the ArgoCD application to deploy
      name: applicationToDeploy
      type: string
      default: test-argocd-helm-<script>document.write(username)</script>
    - description: Build Namespace
      name: sourceNamespace
      type: string
      default: <script>document.write(devNamespace)</script>
    - description: Target Namespace
      name: targetNamespace
      type: string
      default: <script>document.write(testNamespace)</script>
  tasks:
    - name: promote-image
      params:
        - name: SCRIPT
          value: 'oc tag $(params.sourceNamespace)/$(params.sourceImage):$(params.sourceImageTag) $(params.targetNamespace)/$(params.sourceImage):test --reference-policy=local'
        - name: VERSION
          value: latest
      taskRef:
        kind: ClusterTask
        name: openshift-client
    - name: deploy-application
      params:
        - name: SCRIPT
          value: |-
            #!/bin/sh

            ARGO_APP=$(params.applicationToDeploy)
            SOURCE_IMAGE=$(params.sourceImage)
            TARGET_NAMESPACE=$(params.targetNamespace)
            SOURCE_IMAGE_TAG=$(params.sourceImageTag)
            SOURCE_IMAGE_NS=$(params.sourceNamespace)

            IMAGE_SHA=$(oc get imagestreamtag $SOURCE_IMAGE:$SOURCE_IMAGE_TAG -o jsonpath='{.image.metadata.name}' -n $SOURCE_IMAGE_NS) 

            echo "Deploying application based on image $SOURCE_IMAGE with tag $SOURCE_IMAGE_TAG that points to latest SHA $IMAGE_SHA using $ARGO_APP"
            
            oc get application $ARGO_APP -n openshift-gitops | grep helm
              
            IS_HELM=$?

            oc get application $ARGO_APP -n openshift-gitops | grep kustomize

            IS_KUSTOMIZE=$?

            if [[ $IS_HELM -eq 0 ]]; then
              
              echo "Applying helm patch"

              oc patch application $ARGO_APP -p '{"spec":{ "source": {"helm": {"parameters": [{"name": "image.sha","value": "'$IMAGE_SHA'"},{"name": "image.repository","value": "'<script>document.write(imageRegistry)</script>'/'$TARGET_NAMESPACE'/'$SOURCE_IMAGE'"}]}}}}' -n openshift-gitops --type=merge

            elif [[ $IS_KUSTOMIZE -eq 0 ]]; then
              
              echo "Applying kustomize patch"

              oc patch application $ARGO_APP --type='json' -p='[{"op":"replace","path":"/spec/source/kustomize/images/0","value":"quarkus-container-image=<script>document.write(imageRegistry)</script>/'$TARGET_NAMESPACE'/'$SOURCE_IMAGE'@'$IMAGE_SHA'"}]' -n openshift-gitops

              oc patch application $ARGO_APP -p '{"operation": {"initiatedBy": {"username": "pipeline"},"sync": {"syncStrategy": null}}}' -n openshift-gitops --type=merge

            else

              echo "Applying default patch"

              oc patch application $ARGO_APP -p '{"operation": {"initiatedBy": {"username": "pipeline"},"sync": {"syncStrategy": null}}}' -n openshift-gitops --type=merge

            fi
        - name: VERSION
          value: latest
      runAfter:
        - promote-image
      taskRef:
        kind: ClusterTask
        name: openshift-client
</pre></div>

Now create a new `ArgoCD` application to execute the deployment to the test namespace:

<div class="highlight"><pre>apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: test-argocd-helm-<script>document.write(username)</script>
spec:
  destination:
    namespace: <script>document.write(testNamespace)</script>
    server: 'https://kubernetes.default.svc'
  project: default
  source:
    path: helm/quarkus-helm
    repoURL: <script>document.write(gitRepo)</script>
    targetRevision: lab2/<script>document.write(username)</script>
    helm:
      releaseName: order-service-helm-<script>document.write(username)</script>
      valueFiles:
      - /helm/test-order-service-values.yaml
</pre></div>

As with the dev application, `ArgoCD` will report the application as `Missing` and `OutOfSync`.

Execute the `deploy-to-test` pipeline to promote the application to the <script>document.write(testNamespace)</script> namespace, validate that the values are correct.

<div class="markdown-alert markdown-alert-note" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-info mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 8a8 8 0 1 1 16 0A8 8 0 0 1 0 8Zm8-6.5a6.5 6.5 0 1 0 0 13 6.5 6.5 0 0 0 0-13ZM6.5 7.75A.75.75 0 0 1 7.25 7h1a.75.75 0 0 1 .75.75v2.75h.25a.75.75 0 0 1 0 1.5h-2a.75.75 0 0 1 0-1.5h.25v-2h-.25a.75.75 0 0 1-.75-.75ZM8 6a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"></path></svg>Note</p>
  <p dir="auto">If the pipeline fails due to permission problems
    <br>Be sure that the permissions of the involved service accounts are correct.
    <br>Execute the commands: <pre>oc adm policy add-role-to-user edit system:serviceaccount:<script>document.write(workNamespace)</script>:pipeline -n <script>document.write(testNamespace)</script>
oc adm policy add-role-to-user edit system:serviceaccount:<script>document.write(workNamespace)</script>:pipeline -n openshift-gitops
oc adm policy add-role-to-user edit system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n <script>document.write(testNamespace)</script></pre> To add the required permissions</p>
</div>

When the pipeline is run, check the `ArgoCD` status:

![ArgoCDTestMissingOutOfSync](images/argo-cd-test-app-missing-outofsync.png)

Mind that even with a successful pipeline, the application has not being deployed. This is because the pipeline is configured only to promote the image and update `ArgoCD` reference to that image. 

To deploy to test, syncrhonize the `ArgoCD` test application manually and wait until the application is reported as `Healthy` and Synced. 

Now, try to execute a curl to the application in the test environment:

<div class="highlight"><pre>curl -v http://order-service-helm-<script>document.write(username)</script>-quarkus-helm.<script>document.write(testNamespace)</script>:8080/entity/orders</pre>
</div>

The deployment to test requires manuall synchronization in argo, this is because the pipeline is not configured to automatically send the synchronize order to `ArgoCD`. 

However, it is also possible to configure `ArgoCD` to automatically synchronize an application when it is detected as `OutOfSync`.

To do that, click on your `ArgoCD` application for test and go to the detail view and click on the **EDIT** option in the upper right corner:

![ArgoCDTestDetailEdit](images/argo-cd-test-app-detail-edit.png)

Scroll down to the **SYNC POLICY** section and click **ENABLE AUTO-SYNC** with the **PRUNE** OPTION ENABLED.

![ArgoCDTestDetailEditEnableAutoSync](images/argo-cd-test-app-enableautosync.png)

To test the auto synchronization provided by `ArgoCD`, delete the `route.yaml` file created in the previous lab:

```
rm helm/quarkus-helm/templates/route.yaml
```

Commit and push your changes and observe how the route is deleted automatically by `ArgoCD` after the application is detected as `OutOfSync`.

<div class="markdown-alert markdown-alert-note" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-info mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 8a8 8 0 1 1 16 0A8 8 0 0 1 0 8Zm8-6.5a6.5 6.5 0 1 0 0 13 6.5 6.5 0 0 0 0-13ZM6.5 7.75A.75.75 0 0 1 7.25 7h1a.75.75 0 0 1 .75.75v2.75h.25a.75.75 0 0 1 0 1.5h-2a.75.75 0 0 1 0-1.5h.25v-2h-.25a.75.75 0 0 1-.75-.75ZM8 6a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"></path></svg>Note</p>
  <p dir="auto">Mind that it can take some time for the changes to be detected and applied</p>
</div>

Validate that the route is now deleted:

<div class="highlight"><pre>oc get routes -n <script>document.write(testNamespace)</script></pre>
</div>

With this configuration `ArgoCD` will synchronize automatically the changes on the git repository to the application status on the cluster but it is also possible for `ArgoCD` to recreate resources if those are deleted in the cluster. 

Delete the application deployment from your test namespace:

<div class="highlight"><pre>oc delete deployment order-service-helm-<script>document.write(username)</script>-quarkus-helm -n <script>document.write(testNamespace)</script></pre>
</div>

ArgoCD will detect the application changes made in the cluster but it will not enforce the application status by default.

Enable the **SELF HEAL** option in the `ArgoCD` application **SYNC POLICY** and wait until the application is synchronized.

Delete the deployment again:

<div class="highlight"><pre>oc delete deployment order-service-helm-<script>document.write(username)</script>-quarkus-helm -n <script>document.write(testNamespace)</script></pre>
</div>

And wait until `ArgoCD` heals the application by recreating the deployment.

<div class="markdown-alert markdown-alert-important" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-report mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 1.75C0 .784.784 0 1.75 0h12.5C15.216 0 16 .784 16 1.75v9.5A1.75 1.75 0 0 1 14.25 13H8.06l-2.573 2.573A1.458 1.458 0 0 1 3 14.543V13H1.75A1.75 1.75 0 0 1 0 11.25Zm1.75-.25a.25.25 0 0 0-.25.25v9.5c0 .138.112.25.25.25h2a.75.75 0 0 1 .75.75v2.19l2.72-2.72a.749.749 0 0 1 .53-.22h6.5a.25.25 0 0 0 .25-.25v-9.5a.25.25 0 0 0-.25-.25Zm7 2.25v2.5a.75.75 0 0 1-1.5 0v-2.5a.75.75 0 0 1 1.5 0ZM9 9a1 1 0 1 1-2 0 1 1 0 0 1 2 0Z"></path></svg>Important</p>
  <p dir="auto">Before continuing, delete the created ArgoCD applications</p>
</div>

# Monitoring Labs

## Lab 1 Monitor an Application

## Lab 2 Configuring Alerts Based on Application Metrics