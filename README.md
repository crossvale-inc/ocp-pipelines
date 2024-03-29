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
    color: #1f6feb;
  }

  .markdown-alert-note {
    border-left: .25em solid #1f6feb;
    border-left-color: #1f6feb;
    color: #1f6feb;
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

# Lab 0 Prepare OCP Cluster for Workshop

The purpose of this Lab is to prepare the Openshift cluster for the exercises.

<div class="markdown-alert markdown-alert-note" dir="auto"><p class="markdown-alert-title" dir="auto"><svg class="octicon octicon-info mr-2" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path d="M0 8a8 8 0 1 1 16 0A8 8 0 0 1 0 8Zm8-6.5a6.5 6.5 0 1 0 0 13 6.5 6.5 0 0 0 0-13ZM6.5 7.75A.75.75 0 0 1 7.25 7h1a.75.75 0 0 1 .75.75v2.75h.25a.75.75 0 0 1 0 1.5h-2a.75.75 0 0 1 0-1.5h.25v-2h-.25a.75.75 0 0 1-.75-.75ZM8 6a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"></path></svg>Note</p>
  <p dir="auto">This lab is optional and must only be done by the instructor.</p>
</div>

## Lab 0.1 Install Openshift Pipelines Operator

Install the pipelines operator from the OperatorHub:

* Name: 
* Version:

## Lab 0.2 Install Openshift GitOps Operator

## Lab 0.3 Install Grafana Operator

## Lab 0.4 Configure Service Account Permissions

# Lab 1

Set your personal details for the script:

Git Repo: <input type="text" id="gitRepo" name="gitRepo" value="https://github.com/crossvale-inc/ocp-pipelines"/>

UserName: <input type="text" id="username" name="name" />

<input type="button" value="generate" onClick="window.location.reload()"/>

<script>

  var username= document.getElementById("username").value
  var gitRepo= document.getElementById("gitRepo").value

  if (username == "") {
    username = "user"
  }

  var devNamespace = username + "_development"
  var testNamespace = username + "_test"

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

Clone the repository in your workspace:

<div class="highlight"><pre>git clone <script>document.write(gitRepo)</script></pre>
</div>

Create the develop project:

<div class="highlight"><pre>oc create project <script>document.write(devNamespace)</script></pre>
</div>

Create the test project:

<div class="highlight"><pre>oc create project <script>document.write(testNamespace)</script></pre>
</div>


![GrafanaAlertResponse](images/grafana-alert-response-time.png)