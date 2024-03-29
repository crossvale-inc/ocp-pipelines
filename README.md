# XV OCP Pipelines Workshop

The purpose of this workshop is to provide an overview on how to build, deploy and monitor applications running in an Openshift cluster.

To build the applications, *Openshift Pipelines* will be used and the deployment will be done by *Openshift GitOps*.

The configuration management is based on `Helm` and `Kustomize` while the monitoring and alerting of application metrics is done using `Grafana` and `Prometheus`.

# Lab 0 Prepare OCP Cluster for Workshop

The purpose of this Lab is to prepare the Openshift cluster for the exercises.

<div class="markdown-alert markdown-alert-important" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-report mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"></svg>Important</p><p dir="auto">This lab is optional and must only be done by the instructor.</p>
</div>

## Lab 0.1 Install Openshift Pipelines Operator

## Lab 0.2 Install Openshift GitOps Operator

## Lab 0.3 Install Grafana Operator

## Lab 0.4 Configure Service Account Permissions

# Lab 1

Set your personal details for the script:

Git Repo: <input type="text" id="gitRepo" name="gitRepo" value="https://github.com/crossvale-inc/ocp-pipelines"/>

UserName: <input type="text" id="username" name="name" />
<button type="button" onClick="window.location.reload()">generate</button>

<script>

  var username= document.getElementById("username").value
  var gitRepo= document.getElementById("gitRepo").value

  if (username == "") {
    username = "user"
  }

  var devNamespace = username + "_development"

</script>

Clone the repository in your workspace:

<div class="highlight"><pre>git clone <script>document.write(gitRepo)</script></pre>
</div>

Create your develop and test projects:

<div class="highlight"><pre>oc create project <script>document.write(devNamespace)</script></pre>
</div>

