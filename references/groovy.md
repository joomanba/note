### matcher

```groovy
_version ==~ /[0-9]{1,4}[.][0-9]{1,4}[.][0-9]{1,4}$/
```


### console color

```
ANSI_RESET = "\u001B[0m"; 
ANSI_RED   = "\u001B[31m"; 
```

### stop process

```
error("The version is empty!")
currentBuild.result = 'ABORTED'
```

### get tag 

```
// refs/tags/0.0.7

def tagGitVersion = "${env.gitlabBranch}"			      
...
def infoTagGit= "${tagGitVersion}".split('/')

...
else{
    if(infoTagGit.size()==3){
        // Push with tag version
        tagVersion="${tagGitVersion}".split('/')[2]  
        buildType= matcherVersion("${tagVersion}")
     ...
}
```

### varify version
```
def matcherVersion (def _version){
    def formatVersion = (_version ==~ /[0-9]{1,4}[.][0-9]{1,4}[.][0-9]{1,4}$/)
     ...
    if ("${formatVersion}" == "true" || formatVersion==true){
        buildType = "valid"
        return buildType
     }
     ...
    return buildType
}
```

### checkout
```
else{
    if(infoTagGit.size()==3){
        // Push with tag version
        tagVersion="${tagGitVersion}".split('/')[2]  
        buildType= matcherVersion("${tagVersion}")
        checkout([$class: 'GitSCM',
            branches: [[name: "**/tags/"+"${tagVersion}"]],
            doGenerateSubmoduleConfigurations: false,
            extensions: [[$class: 'CleanBeforeCheckout'], [$class: 'WipeWorkspace']],
            submoduleCfg: [],
            userRemoteConfigs: [[
                credentialsId: 'GitlabCI',
                url: "${GitUrl}",
            ]]
        ])
    }else{
        // Push with no tag version
        println("\u001b[31mThe Tag Push event already create in Git Lab but it don't contain tag version. Please create this event with tag version \u001b[31m.\u001b[0m")
        error("The Tag Push event already create in Git Lab but it don't contain tag version. Please create this event with tag version")
        currentBuild.result = 'ABORTED'
    }     
}
```

### deploy
```
def deployServer(def _server, def _project){
    sh "scp -i /home/deploy/myshop-prod.pem "+"${env.WORKSPACE}"+"/target/"+"${_project}"+"-0.0.1.jar"+ " centos@"+"${_server}"+":/applications/tv-insight/"+"${_project}/"  
    sh "ssh -i /home/deploy/myshop-prod.pem centos@"+"${_server}"+" sudo service "+"${_project}"+" stop"
    sh "ssh -i /home/deploy/myshop-prod.pem centos@"+"${_server}"+" sudo service "+"${_project}"+" start"
}
```

### check service
```
def checkService(def _server, def _project){
    sleep 120
    // check service if return 200: success
    def serviceURL= "http://"+"${_server}"+":8090"
    def statusCode = sh(script: 'curl -w "%{http_code}" '+"${serviceURL}", returnStdout: true)
    if (statusCode =~ "200"){
        echo "${_project} service in server "+"${_server}"+" is working!"
    } else {
        echo "${_project}"+" service is NOT working!"
        error("${_project}"+" service in server "+"${_server}" +" is NOT working!")
        currentBuild.result = 'ABORTED'
    }
}
```

(참조: [curl로 호출하고 HTTP status code 확인하기](https://blog.leocat.kr/notes/2018/08/03/shell-fetch-http-status-code-from-curl-result))
