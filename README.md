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