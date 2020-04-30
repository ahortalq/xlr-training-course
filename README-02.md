# XL Release Training Course

# Agenda

* Releasefile
* Jython and REST API. Jython and REST API extension points

# Releasefiles

## Introduction

* A Releasefile is a Groovy representation of a template that provides "release-as-code" functionality.
* View the Releasefile for an existing template by pressing the Releasefile button.
* Create one in a text editor and store it with the source code for the application it releases.

## Groovy and the Domain-Specific Language

The properties for the release are defined in an intuitive hierarchical fashion.

The language reference can be found here: https://docs.xebialabs.com/generated/xl-release/8.2.x/dsl-api/


## Usage

Call a Releasefile from a Groovy script task.

The result is a planned release derived from the Releasefile.

Run that planned release and view the output.

## Importing Releasefiles

You can import a template from an .xlr file (previously exported from XLR). You can also import a template from an exported .zip file which contains Releasefile (Releasefile.groovy).

## Releasefile documentation:

https://docs.xebialabs.com/xl-release/concept/release-as-code.html


# Jython and REST API

## Using the Jython API in scripts

XL Release exposes an API that can be used to manipulate releases and tasks. The Jython API can be accessed from both Script Tasks and plugin scripts. Example for a script task:

```
task = getCurrentTask()
comment = Comment()
comment.setComment("Hello World!")
taskApi.commentTask(task.id, comment)
```

## XL Release APIs - Additional Jython helper functions

In addition to the Jython API, the following helper functions are also available in Script tasks and Python scripts for plugin tasks.

* getCurrentTask() Returns the current task - a Task object
* getCurrentPhase() Returns the current phase - a Phase object
* getCurrentRelease() Returns the current release - a Release object
* getTasksByTitle(taskTitle, phaseTitle = None, releaseId = None) Finds tasks by title - an array of Task objects
* getPhasesByTitle(phaseTitle, releaseId = None) Finds phases by title - An array of Phase objects
* getReleasesByTitle(releaseTitle) Finds releases by title - An array of Release objects

## XL Release APIs - REST API

The REST API can be accessed via a URL of the form

```
http://[host]:[port]/[context-root]/[service-resource]
```

All IDs start with Applications/ for technical reasons

For example:

```
GET http://localhost:5516/api/v1/templates/Applications/Release84d86c528c2d43abb8627180588a369d
```

The following api objects are available:

* templates
* config
* phases
* dsl
* tasks
* users
* releases
* roles
* folders
* global-permissions

## XL Release APIs examples

Getting a template
```
$ curl 'http://localhost:5516/api/v1/templates/Applications/Release84d86c528c2d43abb8627180588a369d' -i -H 'Accept: application/json'
```
Getting a release
```
$ curl 'http://localhost:5516/api/v1/releases/Applications/Release84d86c528c2d43abb8627180588a369d' -i -H 'Accept: application/json'
```
Getting a phase
```
$ curl 'http://localhost:5516/api/v1/phases/Applications/Release1/Phase1' -i -H 'Accept: application/json'
```
Getting a task
```
$ curl 'http://localhost:5516/api/v1/tasks/Applications/Release1/Phase1/Task1' -i -H 'Accept: application/json'
```

## XL Release APIs - Custom REST endpoints in XL Release

You can extend XL Release by creating new endpoints backed by Jython scripts. This feature can be useful for example to integrate with other systems.

To declare new endpoints you need to put a file `xl-rest-endpoints.xml` in the classpath of your XL Release server: either in a `jar` file of your custom plugin or in `SERVER_INSTALLATION/ext` folder:

```
<?xml version="1.0" encoding="UTF-8"?>
<endpoints xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns="http://www.xebialabs.com/deployit/endpoints"
           xsi:schemaLocation="http://www.xebialabs.com/deployit/endpoints endpoints.xsd">
    <endpoint path="/test/demo" method="GET" script="demo/demo-endpoint.py" />
    <!-- ... more endpoints can be declared in the same way ... -->
</endpoints>
```

After processing this file, XL Release creates a new REST endpoint, which is accessible via `http(s)://{xl-release-hostname}:{port}/{[context-path]}/api/extension/test/demo`

Every endpoint should be represented with <endpoint> element which can contain following attributes:

* **path** Relative REST path which be exposed to run the Jython script
* **method** HTTP method type (GET, POST, DELETE, PUT)
* **script** Relative path to the Jython script in the classpath

In a script you have access to the following objects:

* **request**: https://docs.xebialabs.com/jython-docs/#!/xl-deploy/8.0.x/service/com.xebialabs.xlplatform.endpoints.JythonRequest
* **response**: https://docs.xebialabs.com/jython-docs/#!/xl-deploy/8.0.x/service/com.xebialabs.xlplatform.endpoints.JythonResponse
* **Jython api**: https://docs.xebialabs.com/jython-docs/#!/xl-release/8.2.x/


## XL Release APIs - Start a release

First, know the release template ID (find in the URL for the template).

There is a single API call to start a release from a template.

For the template example, the curl command to create the release would be:
```
curl -u 'admin:admin'  -v -H "Content-Type: application/json"
http://localhost:5516/api/v1/http://localhost:5516/#/templates/Folderf2cc43de20584ba4b4f6658216af0256-Release84d86c528c2d43abb8627180588a369d/start -i -X POST
-d '{"releaseTitle": "My Automated Release"}'
```