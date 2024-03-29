<style>
.code-block {
    background-color: grey
  }
</style>

# XV OCP Pipelines Workshop

The purpose of this workshop is to provide an overview on how to build, deploy and monitor applications running in an Openshift cluster.

To build the applications, *Openshift Pipelines* will be used and the deployment will be done by *Openshift GitOps*.

The configuration management is based on `Helm` and `Kustomize` while the monitoring and alerting of application metrics is done using `Grafana` and `Prometheus`.

# Lab 0 Prepare OCP Cluster for Workshop

The purpose of this Lab is to prepare the Openshift cluster for the exercises.

> [!NOTE]
> This lab is optional and must only be done by the instructor.

## Lab 0.1 Install Openshift Pipelines Operator

## Lab 0.2 Install Openshift GitOps Operator

## Lab 0.3 Install Grafana Operator

## Lab 0.4 Configure Service Account Permissions

# Lab 1

Set input:

UserName <input type="text" id="name" name="name" onChange="window.location.reload()"/>

<script>
  var username= document.getElementById("name").value

  if (username == "") {
    username = "user"
  }

  var devNamespace = username + "_development"

</script>

Create your develop and test projects:

<div class="highlight"><pre>oc create project <script>document.write(devNamespace)</script></pre>
</div>

